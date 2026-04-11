---
name: create-documentation
description: Generates full-stack business feature documentation in backend/docs using pyramid principle, mermaid diagrams, and a standardized structure. Use when documenting a feature end-to-end, creating feature docs for admins/employers/workers, or when asked to create documentation based on codebase research.
---

# Create Feature Documentation

Generates full-stack business feature documentation in `backend/docs/` using the pyramid principle. Output is GitHub-flavored markdown.

## When to Use

- Documenting a new or existing feature end-to-end (frontend + backend)
- Creating docs for debugging, onboarding, or understanding feature flows
- User requests documentation based on codebase research

## Prerequisites

1. **Examples**: Read [examples.md](examples.md) to calibrate quality.
2. **Research phase**: Thoroughly read and understand each line of code, model, and comment on the frontend and backend related to the feature. Include repositories: backend, admin, employer, app_scratch
3. **Sentry research**: Search Sentry for common issues in this flow. Use Sentry MCP (`search_issues`, `search_events`) with queries targeting the feature (e.g. endpoint paths, module names, error signatures). Add findings to research notes; surface recurring issues in the plan's Troubleshooting section.
4. **Notes**: Output research notes in a separate `.md` file (e.g. `docs/<feature>-research-notes.md`).
5. **Plan**: After research, produce an implementation plan in a separate `.md` file (e.g. `docs/<feature>-plan.md`).
6. **Approval**: Wait for user approval of the plan before writing the document.

## Document Structure

Create the document in `backend/docs/<feature-name>.md` with this structure:

```markdown
---
# <Feature Title>

**~X min read** (total)

**Purpose:** [When to use this doc - debugging, adding/changing flow, understanding X]

**Requirements:** [App version, deprecated flows, etc. - if applicable]

---

## At a glance

**~X min read**

**In brief:** [Lead with value/meaning/impact first; then define the concept. Example: "Product and backend teams define forms via JSON without writing new React components; SchemaRenderer renders them at runtime. FormSchema is the JSON structure that describes fields, components, and validation." Avoid redundancy with flow steps, which appear in UX Context below.]

### UX Context

[Leading sentence: value, meaning, or impact—not a step summary.] Mermaid flowchart: **feature flow** only (user actions, outcomes). Outcome branches must be user-facing (e.g. "PDF signature saved")—not implementation details (e.g. "POST /v1/me/template"). No implementation steps (tasks, webhooks)—those go in Code flow. Screenshots: `![alt](path)`.

### Terminology

[Glossary-style introduction to technical terms. Optimize for human readability.]

- **Term** — Definition.
- [Introduce common terms used in the feature]

### State machine (if applicable)

Mermaid flowchart for state transitions. Below it, a table of state enums and their description.

### Data model

Mermaid ERD representing models used to power the UX from start to end, including how users relate to the flow. Include **meaningful attributes** (e.g. `status`, `notified_at`, `recording_url`, `evaluator_type`, `context`) — not just PKs and FKs. Avoid bare entities like `CompanyPosition { int id PK }` alone.

### How UX maps to data model

[Describe the data model using the terminology introduced above.]

---

## Code flow

**~X min read**

[Leading sentence: impact or key takeaway, not step summary. Link to Reference/Advanced for detail.]

### Step table of contents

1. [Step 1 name](#step-1--name)
2. [Step 2 name](#step-2--name)
   ...

### Mermaid sequence diagram

[Sequence diagram representing the steps of the flow]

### Step 1 — [Name]

[Content. Link to Reference/Advanced for detail.]

Include **code blocks** for request/response bodies: Python Pydantic (backend), TypeScript/Zod (admin/employer). For GET requests, document query params (e.g. `companyId`, `filename`). Document related endpoints that support the feature (e.g. options endpoints for dropdown components).

### Step 2 — [Name]

...

---

## Reference

**~X min read**

[Topics that summarize key concepts. Use pyramid principle. May use ### within topics. Link to Advanced for further detail.]

**Guidelines:** Review related content above; compare to implementation; add new, specific details. Serves engineers needing deep understanding of a facet. No repetition; add implementation details (config shapes, validation order, error behavior).

### Topic 1

### Topic 2

---

## Advanced

**~X min read** (omit this section if no advanced/optional/edge-case content)

[Detailed explanations, optional/advanced behavior, edge cases for any step/topic above.]

**Guidelines:** Same as Reference. Add API signatures, filter criteria, constraints, management paths, failure modes. When relevant: document **negative scope** (repos/consumers that do not use the feature); **backend vs frontend schema differences** (e.g. validation rules that exist in one but not the other); **implementation quirks** (injected context, defaultValues that affect consumers); **framework constraints** (e.g. Rules of Hooks) when they affect correctness.

---

## Troubleshooting

**~X min read**

Verify conditions in Issue/Fix match code exactly (e.g. `null` vs `[]` triggers different behavior—document the precise trigger).

### Case 1 — [Name]

- **Issue:** ...
- **Fix:** ...
- **Manual steps:** ...

### Case 2 — [Name]

...

---

## Citations and Code References

[Each reference: file path and key location. Doc body links use anchors e.g. [[1]](#ref-1). Use explicit anchors so links resolve: `<a id="ref-N"></a>**[N]**` before each citation. No blank lines between citations.]

<a id="ref-1"></a>**[1]** `path/to/file.py` — Description
<a id="ref-2"></a>**[2]** `path/to/component.tsx` — Description
```

## Pyramid Principle (Minto)

Barbara Minto's Pyramid Principle: answer-first structure.

1. **Answer first** — Lead with the conclusion, recommendation, or key message. Invert the usual build-up; state the point before the evidence.
2. **Group supporting arguments** — Middle level = key reasons, logically grouped (e.g. time, structure, importance). Each point summarizes the support below.
3. **Data at the base** — Details and evidence support each argument. Each level summarizes the level below.

Applied to docs: section leading sentences state the takeaway; body provides support. Reference/Advanced add detail without repeating; each subsection adds new information that supports or extends the level above.

**Formatting:** Mermaid for flows/relationships. Bullets, emphasis, indentation for scannability. In-doc citations `[[N]](#ref-N)`.

## Reading Time

Add estimated reading time to major `##` sections only (At a glance, Code flow, Reference, Advanced if present, Troubleshooting):

```markdown
## Section name

**~X min read**

...
```

## Citations

- Trace the full code path on frontend and backend.
- Add citations at the bottom in "Citations and Code References".
- Ensure each `[[N]](#ref-N)` in the body links to the corresponding citation.
- Use explicit anchors so links resolve: `<a id="ref-N"></a>**[N]**` before each citation (e.g. `<a id="ref-1"></a>**[1]**`).

## Output Location

- Documentation: `backend/docs/<feature-name>.md`
- Research notes: `docs/<feature-name>-research-notes.md` (or user-specified)
- Plan: `docs/<feature-name>-plan.md` (or user-specified)

## Examples

See [examples.md](examples.md) for annotated good/bad examples. Use to calibrate output quality with minimal rules.

## Reference Example

See `backend/docs/w2-onboarding-flow.md` for a complete example. User requirements override the example where they differ.

## Discrepancies and Clarifications

When the example differs from user requirements, check [discrepancies-and-plan.md](discrepancies-and-plan.md) for open questions. Resolve with user before finalizing structure.
