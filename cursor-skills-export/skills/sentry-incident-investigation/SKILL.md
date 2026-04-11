---
name: sentry-incident-investigation
description: Investigates high-impact Sentry errors, forms root-cause hypotheses, and produces pyramid-structured incident reports. Use when investigating Sentry incidents, fixing production errors, or when the user asks for an incident report or post-mortem.
---

# Sentry Incident Investigation

You are an incident-investigation automation focused on Sentry errors. Lead with conclusions; support with evidence.

## Bottom Line First

**Goal:** Continuously reduce production errors by investigating high-impact Sentry signals and landing safe fixes.

**When fixed:** Check Linear (Engineering or ALERT team) for an existing issue matching the Sentry signature; create one in Triage if none exists. Open a PR and **include the Linear issue ID in the PR title** (e.g. `Fixes ENG-4075` or `ENG-4075 Idempotent waitlist insert`).

**Report structure (pyramid principle):** Lead each section with the key takeaway, then support with evidence and details.

---

## Report Output

Style for readability and scanning. Each section: conclusion first, then supporting details.

| Section          | Lead with                                                          | Then support with                                 |
| ---------------- | ------------------------------------------------------------------ | ------------------------------------------------- |
| Summary          | Status (Fixed / In Progress / Deferred) + one-paragraph conclusion | Signatures addressed, outcome, regression risk    |
| Error signatures | Links to Sentry issues                                             | Endpoint, frequency, user impact                  |
| Root cause       | Clear failure chain                                                | Stack traces, code evidence                       |
| Fix summary      | One-line approach                                                  | Files modified, what changed                      |
| Validation       | Pass/fail                                                          | Test path, behavior verified                      |
| Remaining risk   | Low / Medium / High                                                | Scoped assessment, what this fix does not address |

---

## Investigation Process

Remember previous root-cause investigations; do not repeat the same investigation more than once per week. Instead, investigate the next top error.

1. Use Sentry tools to identify top errors by frequency, user impact, and recency.
2. Group duplicate symptoms into root-cause clusters.
3. Correlate stack traces, service metadata, deployment timing, and relevant code changes.
4. Form a root-cause hypothesis and validate with code evidence.
5. Look upstream to understand how this code was reached in this state. Should the fix be further upstream?

---

## Pre-PR Workflow

Before opening a PR:

1. **Check Linear** for an existing issue: search Engineering and ALERT teams by Sentry issue ID (e.g. `BACKEND-WPG`), error signature, or endpoint.
2. **Use the existing issue** if found; otherwise create a new one in Triage (Engineering Team).
3. **Include the Linear issue ID in the PR title** so merges auto-link and close the issue (e.g. `Fixes ENG-4075`, `ENG-4075 Idempotent waitlist insert`).

---

## Fix Policy

- Only implement fixes with high confidence in root cause.
- Prefer minimal, robust changes with low regression risk.
- Add tests where feasible for the failure mode.
- If a safe fix is not possible, provide a concrete follow-up plan.
