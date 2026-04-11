---
name: review-feature-documentation
description: Reviews feature documentation against independent deep research across all repos. Validates accuracy, completeness, and citation fidelity. Use when reviewing backend/docs feature docs, validating documentation before merge, or when asked to audit or verify feature documentation.
---

# Review Feature Documentation

Reviews a feature documentation file against an independent deep research pass across the codebase. Produces a structured review report identifying gaps, inaccuracies, and improvement opportunities.

## When to Use

- After creating or updating feature documentation in `backend/docs/`
- Before merging documentation changes
- When the user asks to audit, verify, or review feature documentation
- When validating that docs match current code

## Workflow

### Phase 1: Identify scope from the doc

1. Read the full documentation file.
2. Extract the feature domain, key terms, models, endpoints, and flow steps.
3. Build a checklist of claims to verify (endpoints, models, functions, flows, terminology).

### Phase 2: Independent deep research

Conduct research **without relying on the doc**. Search and read code across:

- **backend** — models, lib, api, services, tasks, webhooks, cron
- **admin** — API calls, pages, components that touch the feature
- **employer** — API calls, pages, components
- **app_scratch** — screens, components, actions
- **worker-web** — if the feature has worker-facing UI

**Research actions:**

- Grep for key terms (feature name, model names, endpoint paths, function names).
- Read models, lib modules, API handlers, and services referenced in the doc.
- Trace code paths: trigger → handler → side effects.
- Verify associations, enums, and data structures.
- Check for additional endpoints, tasks, or flows not mentioned in the doc.
- Search for slogger messages, error handling, and edge cases.
- Identify repos/consumers that touch the feature but use different patterns (negative scope).
- For GET requests: verify query params are documented.

### Phase 3: Compare and report

Compare the doc against research findings. Produce a review report with:

**Structure:**

```markdown
# Feature Documentation Review: <doc name>

## Summary

- **Verdict:** Accurate / Minor issues / Major gaps
- **Confidence:** High / Medium / Low (based on research depth)

## Verified

- [List of claims that match the code]

## Gaps

- [Missing content: flows, endpoints, models, edge cases, troubleshooting]

When identifying gaps, also check: **negative scope** (repos/consumers that do not use the feature); **related endpoints** (e.g. GET /form/options for dropdown components); **backend vs frontend schema differences**; **implementation quirks** (injected context, defaultValues); **framework constraints** (Rules of Hooks, conditional logic).

## Inaccuracies

- [Claims that contradict the code; include correct info]

## Citation issues

- [Broken links, missing citations, incorrect file paths or descriptions]

## Suggestions

- [Improvements: clarity, structure, additional troubleshooting, diagrams]

## Research notes

- [Key files read, paths traced, assumptions made]
```

### Phase 4: Output

- Write the review to `backend/docs/<feature>-review.md` or a user-specified path.

### Phase 5: Solicit feedback

- Present the review summary to the user.
- Ask: "Does this review look accurate? Any gaps or corrections before we plan changes?"
- Wait for user approval or feedback. Incorporate feedback and revise the review if needed.

### Phase 6: Plan changes (after approval)

- Create a change plan listing edits to the feature document (section by section).
- Include: what to add, remove, correct, or restructure.
- Write the plan to `backend/docs/<feature>-review-plan.md` or inline.

### Phase 7: Approve plan

- Present the plan to the user.
- Ask: "Approve this plan? I'll proceed with edits once you confirm."
- Wait for user approval.

### Phase 8: Apply edits

- After plan approval, apply edits to the feature document.
- Preserve structure and style. Update citations if paths changed.
- Summarize changes made.

## Review Checklist

For each major section of the doc, verify:

| Section                | Checks                                                                                                                             |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| At a glance / In brief | Business value matches feature scope; no conflated features                                                                        |
| UX Context             | Flowchart matches actual code paths; no missing branches                                                                           |
| Terminology            | Terms match model/API naming; no invented terms                                                                                    |
| State machine          | Enums and transitions match code                                                                                                   |
| Data model             | ERD matches models and associations; FKs correct                                                                                   |
| Code flow              | Steps exist; order correct; endpoints and handlers accurate                                                                        |
| Reference              | Config, APIs, formats match implementation                                                                                         |
| Advanced               | Edge cases and optional behavior match code                                                                                        |
| Troubleshooting        | Cases are real failure modes; fixes are valid; **conditions match code exactly** (e.g. `null` vs `[]` triggers different behavior) |
| Citations              | Every [[N]] links to a citation; paths exist; descriptions accurate                                                                |

## Repos to Search

- `backend/` — primary
- `admin/` — Admin Portal
- `employer/` — Employer app
- `app_scratch/` — Worker app (React Native)
- `worker-web/` — Worker web app

## Anti-patterns

- **Do not** trust the doc as source of truth; verify against code.
- **Do not** skip repos that might touch the feature.
- **Do not** assume citations are correct; verify file paths and content.
- **Do not** report "looks good" without listing what was verified.

## Output Location

- Review report: `backend/docs/<feature>-review.md` (or user-specified)
- Change plan: `backend/docs/<feature>-review-plan.md` (or user-specified)
- Edits: applied directly to the feature document

## Approval Gates

- **After Phase 4:** User reviews the report; may request revisions before proceeding.
- **After Phase 6:** User approves the change plan before any edits to the feature doc.
