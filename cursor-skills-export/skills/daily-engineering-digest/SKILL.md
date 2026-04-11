---
name: daily-engineering-digest
description: Creates a concise daily engineering digest summarizing merged PRs, in-progress projects, bug fixes, and PRs needing review. Use when asked for a daily digest, change log, or summary of recent repo activity.
---

# Daily Engineering Digest

Create a daily engineering digest for this repository.

## Goal

Produce a concise, high-signal summary of what changed in the last 24 hours.

## What to include

- Major merged PRs and their user or system impact.
- The issue associated with the PR.
- In-progress projects on the ENG team and its sub-teams.
- Notable bug fixes, incidents, and risky areas touched.
- Security or dependency-related changes.
- Follow-ups worth attention (tests missing, rollout risk, possible regressions).
- **PRs that need review** — open PRs awaiting review, grouped by age or priority.

## Pyramid principle (required)

Every section must follow this structure:

1. **Lead sentence** — One sentence stating the main conclusion or takeaway. A reader who stops here should know the gist.
2. **Supporting details** — Bullets or sub-points that provide evidence. Never lead with bullets; always lead with the takeaway.

Apply to:

- **Section level** — Each `##` section starts with a lead sentence before any bullets.
- **Subsection level** — Each `###` subsection starts with a lead sentence before any bullets.
- **Empty sections** — Use a single declarative sentence (e.g., "None." or "No progress.").

## Quality bar

- Prioritize signal over completeness.
- Group PRs by Linear project and cluster related changes into themes.
- Do not invent details; cite concrete evidence from commits/PRs.
- Keep the digest easy to skim.
- Note how these changes relate to recent changes from the past week, if so. Otherwise no comment.
- Overall output should be readable in under 5 minutes.

## Output format

Post one Slack message in the format of the following example. Include the repo name in the title (e.g., `worker-web`, `employer`, `backend`). Every section demonstrates the pyramid: lead sentence first, then evidence.

```
# Daily Change Log: [repo-name] — March 10, 2026

## Breaking Changes
None.

## New Features

### Design Refresh
The design refresh project is now 80% complete, with a few tickets left on the profile page.
* **Jobs tab blocked state** — New screen when the Jobs tab is blocked (#1481).

### Tech Debt
No progress on this project.

### No Project
Two UX and compliance updates shipped outside of tracked projects.
* **Shift location → map** — Tapping a shift's address opens the native map (#1480).
* **Prop 22 mileage rate** — App now uses current backend mileage rate; requires backend PR #7368 (#1471).

## Bug Fixes
None in this window.

## Other Improvements
UI consistency and version bumps across Payments, Jobs, and dashboard.
* **Button migrations** — Payments, Jobs, and dashboard shift screens now use native-ui Button (#1473, #1472, #1470).
* **OTA** — Production 2.63.0 and beta 2.63.1 version bumps (#1482, #1484).

## Shoutouts
Three contributors drove today's merges.
* **huygo88** — Jobs blocked state + native map on address tap.
* **Christine Yi (hizegi)** — Payments, Jobs, and dashboard button migrations.
* **Mia (Rilakoya)** — Prop 22 mileage rate update.

## Deployment Note
Prop 22 mileage rate (#1471) depends on backend PR workwhile/backend#7368; coordinate deploy if needed.

## Fun Fact
Tapping an address and opening Maps is one of those small UX wins that makes the app feel more native.

## PRs Needing Review
2 PRs awaiting review, oldest open 4 days.
* **[PR title]** — [repo]#[number], open [X] days, author: [name]
* **[PR title]** — [repo]#[number], open [X] days, author: [name]
```
