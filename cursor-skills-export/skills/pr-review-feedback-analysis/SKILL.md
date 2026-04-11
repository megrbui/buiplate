---
name: pr-review-feedback-analysis
description: Analyzes PR review comments from the past week, extracts semantic themes, and recommends .rules edits. Use when asked to analyze PR feedback, review patterns, or create rules from recurring feedback.
---

# PR Review Feedback Analysis

**Output structure:** Follow the pyramid principle. Lead with the main conclusion or recommendation in each section, then support it with evidence and details. Never bury the key takeaway.

---

## Stats

1. Fetch all pull request comments from the past week across the repository, excluding comments by bots.
   - **Lead with:** One headline sentence (e.g., "Review activity was up/down X% vs last week; top reviewer: [name]")
   - **Then:** Compare trends vs previous week
   - **Then:** Breakdown by nit/non-blocking, blocking/changes requested, discussion/question, praise
   - **End with:** Shoutout to top reviewer with most valuable feedback

---

## Patterns

2. Create semantic themes from the comments.
   - **Lead with:** Top 1–2 recommended actions (e.g., "Add rule X" or "Address recurring feedback on Y")
   - **Then:** Semantic themes (code style, performance, security, testing, documentation) with bullet points
   - **Then:** High-frequency or recurring feedback
   - **Then:** Whether feedback aligns with or conflicts with existing `.rules`
   - **Then:** Recommended `.rules` edit with example code
   - **Then:** Value of the "approve" pattern vs risks/impact of the "rejected" pattern
   - **Remember:** These groups and patterns across runs

Use `#` and `##` headings, as well as bullet points for discrete points.

---

## Summary

6. Send a summary to Slack with:
   - **Lead with:** Overall recommendation (1–2 sentences)
   - **Then:** Semantic groups of feedback
   - **Then:** Identified repeating patterns
   - **End with:** Call to action (e.g., "Create PR for rule X")

---

## Pull Request

- Find existing pull request from previous run. If it is still open, amend it. If it is not open, create a new Pull Request.
- Add each rule as its own commit

---

**Format:** Be concise, precise, and scannable. Each section must start with its main takeaway. Output should be readable in under 5 minutes.
