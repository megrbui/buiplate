---
name: weekly-triage-digest
description: Creates a concise weekly engineering digest for Triage summarizing issues in Triage or triagedAt in the last week, ENG/ALERT triage themes, bug fixes, incidents, and repetitive patterns. Use when asked for a weekly triage digest, triage summary, or Triage handoff.
---

# Weekly Triage Digest

Produce a concise, high-signal summary of issues in Triage or triaged in the last week. Include: ENG triage items and themes; ALERT items only if started or completed; bug fixes, incidents, data requests, cross-functional notes (Customer Support, Product, Account Management), kudos, and hand-off items. **Aggregate all resolved issues** (completed in the last 7 days) **and takeaways in the Hand Off section** — do not duplicate full resolution write-ups inside Themes; themes may link to issues only.

## Hardcoded values (WorkWhile)

**Linear** `list_issues`: Query **ENG and ALERT teams only**. Handoff `{ query: "This Week In Triage - Official Handoff", state: "Triage", team: "Engineering" }` | Last 4 handoffs `{ query: "This Week In Triage - Official Handoff", team: "Engineering", limit: 4, orderBy: "updatedAt" }` — omit `state` so completed handoffs are included; use 2nd for theme/trend, 4th for volume | Triage: run twice — `{ state: "Triage", limit: 50, team: "Engineering" }` and `{ state: "Triage", limit: 50, team: "Alerts" }` | On-call: run twice — `{ project: "Engineering On-Call", updatedAt: "-P7D", team: "Engineering" }` and `{ project: "Engineering On-Call", updatedAt: "-P7D", team: "Alerts" }` | Resolutions: `{ state: "Done", updatedAt: "-P7D", limit: 50, team: "Engineering" }` and `{ state: "Done", updatedAt: "-P7D", limit: 50, team: "Alerts" }` (or equivalent for completed issues).

**Sentry** `search_issues`: `organizationSlug: "workwhile"` | Queries: `"unresolved issues with high user count"`, `"InternalError last 7 days"`. No "OR"/"AND"; use separate queries.

## Pyramid principle

**Lead with conclusions.** The first block after the title is readable in under a minute: what changed, what matters, what to do next. Supporting detail and audit trail come later.

Each `##` and `###` section: **lead sentence** (takeaway from content, not the section title), then bullets. Empty sections: omit.

**Do:** "Frequent ask to urgently annotate PDFs." **Not:** "Data requests are straightforward."

## Progressive disclosure (layers)

Structure the comment so **skimmers** get signal without scrolling through every resolved ticket; **reviewers** can expand for takeaways and inventory.

| Layer              | Purpose          | Typical content                                                                                                                                                                                                                                   |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **L0 — Executive** | Default read     | **Bottom line** (2–4 sentences): volume vs last week, theme rotation vs prior digest, pointer to prior handoff comment for WoW. Optional **Do this week** (2–4 bullets: links + verbs).                                                           |
| **L1 — Now**       | On-call / triage | **Volume & comparison** (short). **Now** = P0 handled this window, **In progress**, **Watchlist** — one line per item, each on its own list line.                                                                                                 |
| **L2 — Themes**    | Broader eng      | Named clusters; **one takeaway line per subsection**; **cap ~2–4 issue links per cluster** unless the week is genuinely narrow. Tag (extending) / (new) vs prior week.                                                                            |
| **L3 — Evidence**  | Audit trail      | **Resolved** with full summaries + takeaways inside **`<details>`** collapsible blocks. Optional second `<details>` for **other closes** (hygiene, routine features) if they would bloat L2. Optional `<details>` for **Sentry / tooling notes**. |

**Linear Markdown:** Use `<details>` and `<summary>` for L3 if the renderer shows them (test in Linear). If HTML details are stripped, use normal `##` / `###` headings for the same content — still keep L0–L2 short so the comment does not balloon.

**Skim target:** ~5 minutes for L0–L2; L3 is optional depth.

## List formatting (required)

- **One list item per line.** Every bullet (`-` or `*`) and every ordered item (`1.`, `2.`, …) must start its **own** line in the output.
- **Never** concatenate multiple bullets or numbered steps on one line (e.g. avoid `* a * b * c` on a single line).
- **Nested lists:** each nested bullet also on its own line, indented consistently.

## Rules

- **Teams**: Only query and include issues from **Engineering** (ENG) and **Alerts** (ALERT) teams. Exclude PRO, MKT, MKTPRD, Product, Marketplace, and other teams.
- **ALERT items**: Include only started or completed; exclude Triage/backlog **from Themes** (L2). You may still reference critical ALERT Triage items in **Now** / **Watchlist** (L1).
- **Links**: Every reference ID as link — `[ENG-2445 Title](https://linear.app/workwhile/issue/ENG-2445/slug)` or `[BACKEND-TSK](https://workwhile.sentry.io/issues/7348073226/)`
- **Date range**: 7-day window (e.g. "March 12–19, 2026")
- **Evidence**: Cite commits, PRs, Linear, Slack; no invented details
- **Takeaway** (1–2 sentences per resolved item in L3): consider **theme** (recurring pattern), **resolution** (what was fixed), **impact** (users, noise, follow-up)

## Output

1. Find handoff: `list_issues` with Hardcoded values.
2. Gather: Linear (Triage + on-call + resolutions) — run separate queries for Engineering and Alerts teams per Hardcoded values; include resolved/completed issues from last 7 days; Sentry (high-impact + InternalError); attached links; issue comments; attached PRs.
3. Fetch last 4 "This Week In Triage - Official Handoff" issues via `list_issues`, then `list_comments` on each. Use for comparison: **previous week** (2nd issue) for theme/trend; **previous month** (4th issue) for volume/count.
4. Post digest as comment on handoff issue via `save_comment`.

**Structure (pyramid + progressive disclosure).** Order:

1. Title + **Bottom line** (L0) + optional **Do this week**
2. **Volume & comparison** (L1)
3. **Now** — P0/Urgent this window, **In progress**, **Watchlist** (L1); each item its own bullet line
4. **Themes** with subsections ### Data Requests, ### [Domain], ### Other (L2); lean bullets, one item per line
5. **Cross-functional Notes** → ## Customer Support, ## Product (and Account Management if needed)
6. **Kudos**
7. **Hand Off** — short intro sentence, then **## Resolved** as **`<details>`** (L3) with every resolution + takeaway; then **## In Progress**, **## Unstarted**, **## Watchlist** as short lists (or fold Unstarted if huge — prefer pointer to board + one line)

All sections describe triage issues from the gathered data (Linear, Sentry, Slack); not additional queries.

**Resolutions and takeaways.** Aggregate resolved issues in **Hand Off → Resolved** (L3). For each resolution: issue link, brief summary, and takeaway (1–2 sentences). Do not repeat that full block inside Themes; themes stay thematic.

| Section                 | Definition                                                                                                                  |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Bottom line**         | 2–4 sentences: volume trend, theme shift vs previous week digest, optional vs prior-month handoff count.                    |
| **Do this week**        | Optional 2–4 actionable bullets (owner implied or linked issue).                                                            |
| **Volume & comparison** | WoW and vs prior month; 1–3 sentences.                                                                                      |
| **Now**                 | P0 handled, active work, watch items — minimal prose.                                                                       |
| **Themes**              | Recurring patterns (ALERT: started or completed only in theme bullets). Subsections: Data Requests, domain-specific, Other. |
| **Cross-functional**    | Support, Product, Account Management takeaways.                                                                             |
| **Kudos**               | People + reason; one kudo per line.                                                                                         |
| **Hand Off**            | Intro + Resolved (details) + In Progress + Unstarted + Watchlist                                                            |

```
# Weekly Triage Digest — [start date] to [end date]

**Bottom line:** [2–4 sentences. WoW themes + volume vs prior month.]

**Do this week:** (optional; omit section if nothing actionable)

1. [Verb + link]
2. [Verb + link]

## Volume & comparison

[1–3 sentences. Trends vs previous week digest; counts vs previous month.]

## Now

**P0 / urgent this window**

- [One item per line — link + few words]

**In progress**

- [One item per line]

**Watchlist**

- [One item per line]

## Themes

[One takeaway sentence for the whole section.]

### Data Requests

[Takeaway sentence.]

- [ENG-XXXX](url) — [few words]
- [next item on its own line]

### [Domain, e.g. W2 Onboarding Errors]

[Takeaway sentence.]

- [one item per line]

### Other

[Takeaway sentence.]

- [one item per line]

# Cross-functional Notes

## Customer Support

[Takeaway.]

- [one item per line]

## Product

[Takeaway.]

- [one item per line]

# Kudos

[Takeaway.]

- [Person] — [reason]
- [next on its own line]

# Hand Off

[One sentence: what is left in Triage / queue shape.]

## Resolved

<details>
<summary><strong>Resolved this period (expand for summaries + takeaways)</strong></summary>

- [ENG-XXXX](url) — [brief summary]. **Takeaway:** [1–2 sentences]
- [next resolution on its own line]

</details>

## In Progress

[Takeaway.]

- [ENG-XXXX](url) — [status]
- [one item per line]

## Unstarted

[Takeaway.]

- [ENG-XXXX](url)
- [one item per line]

## Watchlist

[Takeaway.]

- [one item per line]
```

If `<details>` is unavailable, use plain `## Resolved` with the same bullet list (still one item per line); keep **Themes** and **Now** tight so total length stays manageable.

## Example (abbreviated — pyramid + disclosure)

```
# Weekly Triage Digest — March 12–19, 2026

**Bottom line:** Volume is down vs last month. Themes that extended from prior week: Cloud Run and cron (now P0). W2/Persona stay noisy; data requests are lighter than the Hypertrack/Speedex spike.

**Do this week:**

1. Track Cloud Run deploy and cron job health after mitigations.
2. Keep PII-in-logs audit visible until verification passes.

## Volume & comparison

Cloud Run and cron moved from watchlist to P0. Issue count is below the prior-month handoff baseline; ALERT Bugsnag backlog unchanged in shape.

## Now

**P0 / urgent this window**

- [Cloud Run background-backend performance](https://github.com/workwhile/backend/issues/7312)
- `/v1/cron/shifts` failures — [Slack thread](url)

**In progress**

- [ENG-3778](https://linear.app/workwhile/issue/ENG-3778/name-is-wrong-actually-email) — pending Brittany

**Watchlist**

- PII in logs — ongoing audit
- Shift Supervisor — product decision

## Themes

Sensitive data in logs when user-passed data reaches print/logger/slogger paths.

### Data Requests

Frequent ask to urgently annotate PDFs.

- [ENG-3770 Hypertrack data for IndeedFlex](https://linear.app/workwhile/issue/ENG-3770/hypertrack-data-for-indeedflex)
- [ENG-3765 Bonus data for Speedex](https://linear.app/workwhile/issue/ENG-3765/bonus-data-for-speedex)

### W2 Onboarding Errors

FE package upgrades and I-9 flow blocking workers.

- W2 Onboarding Issues project — Alex and Zurab
- FE package upgrades affecting biz portal

### Other

Notifications cache and SMS routing are hard to debug.

- [ENG-3777 Bad Update SMS route](https://linear.app/workwhile/issue/ENG-3777/bad-update-sms-route)

# Cross-functional Notes

## Customer Support

PII in Slack and info gaps when prompting support to dig deeper.

- Still seeing PII in Slack messages
- Threads where we lack good info and must prompt support to dig deeper

## Product

Shift Supervisor concept and data-to-feature requests in flight.

- Shift Supervisor — SMS from workers who add personal contact
- Data requests → features: Clock In/Out in Biz Portal, more detail in Pulse

# Kudos

Cross-team collaboration on product vs eng support boundaries.

- Alejandro — product vs eng support lines
- Arjun — Biz Portal issues
- Danny and Tan — Cloud Run brainstorming
- Julian — PII in logs

# Hand Off

Most open tickets wait on external input.

## Resolved

<details>
<summary><strong>Resolved this period (expand for summaries + takeaways)</strong></summary>

- [ENG-3579](https://linear.app/workwhile/issue/ENG-3579/should-be-validationerror) — ValidationError instead of InternalError for shift-lead check. **Takeaway:** Better error taxonomy cuts Sentry noise and clarifies expected failures.
- [ENG-3543](https://linear.app/workwhile/issue/ENG-3543/emailundeliverableerror-the-domain-name-hellfreshcom-does-not) — EmailUndeliverableError for hellfresh.com. **Takeaway:** Invalid domains surface late; earlier validation would reduce support load.

</details>

## In Progress

Several items blocked on Brittany or reporter follow-up.

- [ENG-3778](https://linear.app/workwhile/issue/ENG-3778/name-is-wrong-actually-email) — pending Brittany
- [ENG-3818](https://linear.app/workwhile/issue/ENG-3818/ticket-isnt-helpful) — pending reporter info

## Unstarted

Long-running task timeouts need investigation.

- [ENG-38](https://linear.app/workwhile/issue/ENG-38/27503s-for-long-running-tasks)

## Watchlist

Cloud Run and PII in logs need ongoing attention.

- Cloud Run — monitor after changes
- PII in logs — ongoing audit
```
