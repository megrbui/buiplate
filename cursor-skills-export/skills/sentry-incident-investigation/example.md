# Example: Pyramid-Structured Incident Report

Reference implementation of the report structure.

```markdown
# Sentry Incident Investigation: Waitlist Duplicate Insert

## Summary (Bottom Line Up Front)

**Status: Fixed.** Sentry signatures BACKEND-WPG and BACKEND-TSN for `/v1/me/listing/<int:listing_id>/waitlist` are resolved. The endpoint now handles duplicate and racing waitlist submissions idempotently via PostgreSQL upsert. First-time waitlist behavior is unchanged; regression risk is low.

---

## Error Signatures Addressed

| Signature                                                     | Endpoint                                        |
| ------------------------------------------------------------- | ----------------------------------------------- |
| [BACKEND-WPG](https://workwhile.sentry.io/issues/<issue-id>/) | `POST /v1/me/listing/<int:listing_id>/waitlist` |
| [BACKEND-TSN](https://workwhile.sentry.io/issues/<issue-id>/) | `POST /v1/me/listing/<int:listing_id>/waitlist` |

---

## Root Cause

The waitlist write path always attempted a new `listings_waitlist` insert. Duplicate or racing requests for the same `(listing_id, worker_id)` pair hit the unique constraint `ix_listings_waitlist_listing_id_worker_id`, triggering a rollback. Request logging then touched lazy-loaded fields on objects in rollback state, surfacing an `InvalidRequestError` to Sentry.

**Chain of failure:**

1. Duplicate POST → unique constraint violation → transaction rollback
2. Post-rollback logging accessed lazy-loaded attributes → `InvalidRequestError`
3. User saw 500; Sentry captured the secondary error

---

## Fix Summary

**Approach:** Replace insert-only semantics with PostgreSQL upsert.

**Change:** In `api/v1/me_listing.py`, the waitlist write path now uses `INSERT ... ON CONFLICT (listing_id, worker_id) DO NOTHING`. Duplicate or racing submissions for the same worker/listing pair are idempotent: the first succeeds, subsequent ones no-op without error.

**Files modified:**

- `api/v1/me_listing.py` — waitlist write logic

---

## Validation

- **Regression test:** Added `tests/unit/api/v1/me_listing_waitlist_test.py` that POSTs twice for the same `(listing_id, worker_id)` and asserts a single row remains.
- **Behavior:** First-time waitlist submissions unchanged; duplicates now succeed silently instead of 500.

---

## Remaining Risk

**Low.** The fix is scoped to the duplicate-insert failure mode. It does not address other non-duplicate causes that may appear under unrelated Sentry signatures.

---

## Assumptions & Limitations

- **Test/lint execution:** `uv` was unavailable in the automation runner. Recommend running locally before merge:
  - `uv run pytest tests/unit/api/v1/me_listing_waitlist_test.py`
  - `uv run pre-commit run --all-files`

---

## Follow-Up Actions

- [ ] Open Triage issue in Engineering Team
- [ ] Open PR with fix, using Triage issue ID in PR title
```
