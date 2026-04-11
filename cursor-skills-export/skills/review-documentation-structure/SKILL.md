---
name: review-documentation-structure
description: Audits feature documentation against create-documentation structure rules. Focus: leading sentences (value/meaning/impact, not summaries), UX Context (feature flow not code flow), data model (meaningful attributes), Code flow steps (request/response code blocks in repo format), scannability. Use when reviewing docs for clarity, redundancy, or structural consistency.
---

# Review Documentation Structure

Audits a feature doc in `backend/docs/` against `create-documentation` rules. Outputs a structured review. Focus: leading sentences (value/meaning/impact), UX Context (feature flow not code flow), data model (meaningful attributes), Code flow (request/response code blocks), scannability.

## When to Use

- After creating or updating feature documentation
- When a doc feels dense, repetitive, or hard to scan
- When asked to audit docs for clarity, redundancy, or structural consistency
- Before merging documentation changes

## Input

- **Doc path:** Path to the feature document (e.g. `backend/docs/assessments-classet-interview.md`). User provides this when invoking the skill.

## Reference

Read the `create-documentation` skill first to understand the target structure and rules. This skill compares the given doc against those rules.

## Workflow

### Phase 1: Load and parse

1. Read the full feature document from the path provided (e.g. `backend/docs/<feature>.md`).
2. Read the `create-documentation` skill to extract structure rules and pyramid principle.
3. Identify required sections: At a glance, UX Context, Terminology, State machine (if applicable), Data model, How UX maps to data model, Code flow, Reference, Advanced (if applicable), Troubleshooting, Citations.

### Phase 2: Structure compliance

- [ ] **Required sections present:** All required sections exist per create-documentation template.
- [ ] **Section order:** Matches the template order.
- [ ] **Reading time:** Estimated reading time added to major `##` sections.
- [ ] **Citations:** All `[[N]](#ref-N)` in body link to valid citations; explicit anchors exist (`<a id="ref-N"></a>**[N]**` before each citation).
- [ ] **Citations formatting:** No blank lines between citations; each on its own line.

### Phase 3: Leading-sentence philosophy

Leading sentences must extract **value, meaning, or impact** — not summarize steps.

- **In brief:** Lead with value first; definition follows. Good: "Product and backend teams define forms via JSON without writing new React components; SchemaRenderer renders them at runtime. FormSchema is the JSON structure that describes fields, components, and validation." Bad: "FormSchema is a JSON structure that describes form fields... This enables teams to define forms without new components." (Definition first; value at end.)
- **UX Context / Code flow:** Good: "Employers rely on this flow to verify worker fit before shift start; workers complete interviews via SMS link, with no in-app experience." Bad: "The flow runs outside the app: worker accepts → receives SMS → completes interview → webhook notifies backend." (Step summary; belongs in diagram.)

### Phase 4: UX Context — feature flow vs code flow

UX Context shows **feature flow** (user actions, outcomes), not **code flow** (tasks, webhooks, DB ops). If it duplicates the Code flow sequence diagram, it fails.

**Outcome branches** in flowcharts must be user-facing (e.g. "PDF signature saved", "Work experience submitted")—not implementation details (e.g. "POST /v1/me/template", "submit handler").

### Phase 5: Data model — meaningful attributes

ERD must include **meaningful attributes** (e.g. `status`, `notified_at`, `recording_url`, `evaluator_type`, `context`) — not just PKs/FKs. Avoid bare entities like `CompanyPosition { int id PK }` alone.

### Phase 6: Code flow — request/response code blocks

API/webhook steps include **code blocks** for request/response bodies. Format: Python Pydantic (backend), TypeScript/Zod (admin/employer). For GET requests, verify query params are documented (e.g. `companyId`, `filename`). Related supporting endpoints (e.g. options for dropdowns) should be documented.

### Phase 7: Scannability and formatting

- [ ] **Bullets:** Discrete points use bullets when appropriate.
- [ ] **Emphasis:** Key terms or actions use bold when appropriate.
- [ ] **Indentation:** Nested content uses appropriate indentation.
- [ ] **Tables:** State tables, reference tables use consistent formatting.
- [ ] **Mermaid diagrams:** Diagrams are present for flows, relationships, sequences.
- [ ] **Links:** In-doc links use `[[N]](#ref-N)`; external links use markdown.

### Phase 8: Consistency and redundancy

- [ ] **Terminology:** Terms used consistently; no invented or alternate terms for the same concept.
- [ ] **Redundancy:** Content not repeated across sections without justification (e.g. "In brief" vs "How UX maps to data model" should not repeat the same flow verbatim).
- [ ] **In brief vs Code flow:** "In brief" focuses on _why_ and business value; Code flow steps describe _what_ happens. Avoid redundancy.
- [ ] **Step table of contents:** Matches actual step headings and anchors.
- [ ] **Reference / Advanced:** Review content above; compare to implementation; add new, specific details. Serves engineers needing deep understanding. No repetition. Reference: config shapes, validation order, error behavior. Advanced: API signatures, filter criteria, constraints, management paths.

### Phase 9: Leading-sentence quality checklist

| Question                     | Pass | Fail |
| ---------------------------- | ---- | ---- |
| Value, meaning, or impact?   | ✓    | ✗    |
| Distinct from section title? | ✓    | ✗    |
| Not a step/content summary?  | ✓    | ✗    |

### Phase 10: Output report

Produce a structured report:

```markdown
# Documentation Structure Review: <doc name>

## Summary

- **Verdict:** Compliant / Minor issues / Major gaps
- **Focus:** Leading-sentence philosophy, UX Context feature vs code flow, data model attributes, request/response code blocks, scannability

## Structure compliance

- [List of pass/fail for required sections, order, reading time, citations]

## UX Context: feature flow vs code flow

- **Diagram:** Does it show user-facing flow (worker/admin actions, outcomes) or implementation steps (tasks, webhooks, DB ops)? Pass/Fail.
- **Outcome branches:** Are branches user-facing (e.g. "PDF signature saved") or implementation details (e.g. "POST /v1/me/template")? Pass/Fail.
- **Leading sentence:** Does it state value/meaning/impact, or is it a step summary? Pass/Fail.
- [Specific issues and suggested fixes]

## Leading-sentence philosophy violations

- [Section]: Leading sentence "[exact quote]" — [why it fails: e.g. "step summary; should state value/meaning/impact"]
- [Suggested replacement if applicable]

## Data model — meaningful attributes

- [ ] ERD includes meaningful attributes beyond PKs/FKs
- [List of entities with insufficient attributes, if any]

## Code flow — request/response code blocks

- [ ] Steps with API requests/webhooks include code blocks for request/response bodies
- [ ] Format matches repo: Python Pydantic (backend), TypeScript/Zod (admin/employer)
- [List of steps missing code blocks, if any]

## Scannability issues

- [List of formatting, bullet, or diagram issues]

## Redundancy / consistency

- [List of redundant content or inconsistent terminology]

## Reference / Advanced — pyramid rule

- [ ] Content above reviewed; implementation compared; new details added.
- [ ] No repetition. Subsections add implementation-specific detail.
- [List of violations]

## Leading-sentence quality

| Section   | Leading sentence | Pass/Fail | Notes (value/meaning/impact vs summary) |
| --------- | ---------------- | --------- | --------------------------------------- |
| In brief  | [Quote]          | [P/F]     | Lead with value; definition follows     |
| [Section] | [Quote]          | [P/F]     | [Brief reason]                          |

## Recommendations

- [Prioritized list of edits: UX Context diagram, leading sentences, data model attributes, request/response code blocks for API steps, etc.]
```

### Phase 11: Present and solicit feedback

- Present the review summary to the user.
- Ask: "Does this review look accurate? Any gaps or corrections before we plan edits?"
- Wait for approval before proposing edits.

## Anti-patterns

- Leading sentences: value/meaning/impact, not step summaries.
- "In brief" vs "How UX maps": different purposes (why vs how); don't flag as redundant.
- UX Context ≠ Code flow; user requirements may override template.

## Output location

- Review report: `backend/docs/<feature>-structure-review.md` (or user-specified)
- Edits: applied only after user approval (optional follow-up)
