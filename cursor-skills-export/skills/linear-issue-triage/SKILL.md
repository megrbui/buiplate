---
name: linear-issue-triage
description: Triage Linear issues in ENG or ALERT. Investigate bug reports, add background/related-issues/diagnosis comments, update issue plans, and open PRs for easy fixes. Use when triaging Linear bugs or when the user asks for issue triage.
---

# Linear Issue Triage Automation

You triage Linear issues in ENG or ALERT. Prioritize accuracy over thoroughness. Skip sections where you lack reliable information. Do not guess.

## Scope and Rules

- **React on start:** Add an eyes emoji reaction to the issue
- **PR linking:** Use the issue branch name when opening PRs so they auto-link
- **Only act on bug reports.** For other issue types, do nothing—no comments, no noise
- **De-duplicate:** If the same bug has a recent duplicate (within 7 days), skip

## For Bug Reports

1. **Investigate** — Understand the bug and report findings (refute or explain likely root cause). Also trace how the code with the bug was reached, and upstream causes leading to the code path.
2. **Use logs** when available and helpful, such as attached Sentry issues
3. **Understand context** — Implementation and business context across backend, app_scratch, admin, employer
4. **Plan** — Add a plan to the issue description; use Mermaid diagrams where helpful
5. **Fix if trivial** — Open stacked PRs for larger changes, or a single PR when the total change is under 76 lines

## Required Comment Structure

Post a concise comment with three sections. Skip sections where you lack confidence. Do not speculate.

### 1. Background

- Why does this matter? Business impact?
- Expected vs. actual behavior?
- Which project is it part of, and how does it fit the Happy Path (see labels)?

### 2. Related Issues

- What issues from the past 2 months are related and why?
- How often do similar issues appear in this area?
- Who are the topic owners or most frequent assignees?

### 3. Diagnosis

- **Immediate cause** — What is happening now?
- **Root cause** — What is the underlying problem?
- **Short-term mitigation** — How to reduce impact immediately
- **Long-term fix** — What would improve correctness and maintainability?
- **Plan** — Step-by-step approach (no code solutions)
