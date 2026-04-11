# Discrepancies and Plan: create-documentation Skill

**Purpose:** Lists discrepancies between `w2-onboarding-flow.md` and your requirements for clarification. Use this when working through an actual feature.

---

## Discrepancies to Clarify

_Where the example differs from your description, your explanation overrides—but please confirm these interpretations:_

### 1. Section order in At a glance

- **Your order:** UX Context → Terminology → Data model → How UX maps
- **Example order:** Terminology → UX context → W2 status transitions (table) → Data model → How UX maps

**Question:** Use your order (UX Context first)? --Or keep Terminology first so readers know terms before the flowchart?--
[Note: use my order]

---

### 2. Reference vs Advanced

- **Your structure:** Two sections—`# Reference` (summary topics, links to Advanced) and `# Advanced` (detailed explanations)
- **Example:** Only `# Advanced`—no separate Reference section

**Question:** Should we have both Reference and Advanced? If yes, Reference = digest/summary that links to Advanced for deeper detail?
[Note: Yes, keep both sections. If there is no need for an Advanced section, no need to include it.]

---

### 3. In brief content

- **Your requirement:** Business value/purpose and what problem it solves (e.g. for admins, employers, workers)
- **Example:** Describes the flow steps (1–6) and what happens

**Question:** In brief should focus on _why_ (business value, problem solved) rather than _what_ (flow steps)? Or combine both?
[Note: _why_; what will be explained below in the leading sentence for the ux flowchart. We want to avoid redundancy.]

---

### 4. Reading time granularity

- **Your requirement:** "At the end, go back to each #, ## and add an estimated reading time"
- **Example:** Reading time only on major sections (At a glance, Code flow, Advanced, Troubleshooting)

**Question:** Add reading time to every `##` and `###`? Or only major `##` sections like the example?
[Note: Let's do major ## sections like in the example]

---

### 5. Enum table vs terminology

- **Your requirement:** "Include a table of terms for primary enums, if applicable"
- **Example:** Enums (w2_status, i9_status, etc.) are in Terminology bullets. Separate "W2 status transitions" table shows state machine (From → To → Condition).

**Question:**

- Enum table = terminology-style table listing enum name, values, description?
- Status transitions table (From/To/Condition) = separate, include when feature has state machine?

[Note: for terminology, optimize for human readability here. this is an introduction to technical terminology; i think it should be styled like a glossary]
[Note: I think a state machine is best represented as a flowchart with a table of state enums and their description below it.]

---

### 6. Purpose and Requirements at top

- **Example has:** `**Purpose:**` and `**Requirements:**` lines at the top
- **Your structure:** Did not explicitly mention these

**Question:** Include Purpose and Requirements at the top? (Assumption: yes, they improve discoverability.)
[Note: yes, let's keep these. good callout]

---

### 7. Images

- **Example:** Uses `![alt](path)` for screenshots in UX Context and Code flow steps
- **Your structure:** Did not mention images

**Question:** Include screenshot placeholders/paths when documenting UI flows?
[Note: yes]

---

## Plan Template (for actual feature)

When working through an actual feature, follow this plan:

### Phase 1: Research

1. Identify frontend and backend code paths for the feature
2. Read models, API handlers, services, lib code, and comments
3. **Sentry research:** Search Sentry (search_issues, search_events) for common issues in this flow; add findings to research notes
4. Output notes to `docs/<feature>-research-notes.md`

### Phase 2: Plan

1. Organize findings into the document structure
2. List sections and key content for each
3. Output plan to `docs/<feature>-plan.md`
4. **Pause for user approval**

### Phase 3: Write

1. Create `backend/docs/<feature-name>.md`
2. Follow structure from SKILL.md
3. Add citations as you write
4. Add reading times at the end

### Phase 4: Citations pass

1. Review doc and ensure each `[[N]](#ref-N)` links to correct citation
2. Verify code paths are accurate

---

## Resolved (after clarification)

- [x] Section order — Use user order: UX Context → Terminology → Data model → How UX maps
- [x] Reference vs Advanced — Keep both; omit Advanced if no advanced/optional/edge-case content
- [x] In brief content — Focus on _why_ (business value, problem solved); _what_ goes in UX Context leading sentence
- [x] Reading time granularity — Major ## sections only (like example)
- [x] Enum/terminology — Terminology: glossary-style, human-readable. State machine: flowchart + table of state enums/descriptions
- [x] Purpose/Requirements — Keep at top
- [x] Images — Yes, include screenshots for UI flows
