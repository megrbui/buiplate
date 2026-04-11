---
name: typescript-jsdoc-annotation
description: >-
  Adds or refreshes JSDoc on TypeScript without @param/@returns; documented
  exports use @example with production-like mocks and genericized Thing entities.
  Module headers: leading sentence, optional Exports list, @see pointers—no @example.
  Per-export blocks: leading sentence plus short summary; deeper detail inline.
  Summarize purpose and intent, not step-by-step code flow. Friendly, readable tone.
  Use when the user asks for JSDoc, TSDoc-style comments, documented examples on
  functions/types, or API doc comments for TypeScript.
---

# TypeScript JSDoc annotation

## When to use

Apply when adding or updating block comments above functions, types, interfaces, classes, or exported constants in TypeScript (or TSX). Prefer this style over duplicating what types already express.

## Rules

### Do not duplicate types

- **Do not** use `@param` or `@returns` (or `@return`). TypeScript already documents parameters and return types.
- **Do** use a **summary line** (first line) for every documented export; see **Summary vs inline detail** below for how much belongs in the block versus next to the code.

### Summary vs inline detail (modules and functions)

- **Limit the JSDoc block** on an **exported** function, class, or constant to the **leading sentence** plus **at most one short follow-on sentence** (the summary). That pair states **purpose and intent**—why the export exists, what caller or product problem it serves, and the main boundary caveat if one is essential. (**Module** headers use the separate rule below—**Exports** / **`@see`** lines are not “extra summary sentences.”)
- **Purpose/intent, not code flow:** The block must **not** narrate implementation (“calls X, then Y, then in the catch…”). That belongs in **inline** comments next to the relevant lines. The summary answers **what this is for**, not **how the body executes**.
- **No implementation details in the block (“frontmatter”):** Do **not** name specific hooks (**`useEffect`**), framework versions (“v5 removed…”), or other wiring in the **leading summary**. Describe the **caller-visible contract** (what runs when, what state means) instead. Reasons for that wiring stay **inline** next to the code.
- **Move everything else** into **inline annotations**: `//` or `/** … */` placed **adjacent** to the branch, handler, nested function, option bag, or statement the note describes (e.g. `onError` arms, `try`/`catch`, feature flags, non-obvious invariants).
- **Purpose, not shape:** Inline comments should explain **why** the code is there or **what outcome** it achieves (contract, recovery, guard, product rule). Do **not** restate **what** the code is syntactically (“`if` branch for errors”, “loop over items”) or duplicate information obvious from types and names.
- **Document each symbol in isolation:** In block JSDoc and substantive inline comments, do **not** tie the description to another export with **same as**, **like X**, or **parity with Y**—even in the same file. State that symbol’s purpose and contract on its own. Use **`@see`** when a deliberate cross-link is warranted.
- **Affirmative purpose, not rejected alternatives:** The summary states **what the export does** and **why it matters** in positive terms. Do **not** lean on hypothetical code nobody wrote (**instead of repeating… in every hook**, **without having to duplicate X**, **so callers don’t need to Y**). That framing compares to an unimplemented baseline; prefer the actual contract alone.
- **Module / file header (above imports):** One **leading sentence** on what the file provides. Optionally list **every public export** in one line (**`Exports:`** + **`{@link …}`**). Then **`@see`** (and file paths) for **related resources** outside the file—design docs, conventions, sibling modules—without a second essay. **Do not** put **`@example`** on the module block; examples belong on individual exports. Deeper behavior belongs on each export or inline in the implementation.
- **`@example`, `@see`, `@deprecated`** on **exports:** `@example` belongs on documented exports, not on the module header. **`@see`** / **`@deprecated`** may stay in any block; keep examples focused.

### Tone (summaries and module docs)

- **Friendly and readable:** The **leading sentence and summary** (including **file- or module-level** blocks above imports) should read in plain language, with short sentences. Edge cases and recovery paths belong **inline**, not in a long second paragraph.
- **Imperative, verb-led summaries:** Open with imperative mood and strong verbs (**Configure…**, **Wrap…**, **Parse…**, **Return…**) instead of passive or nominal phrasing (**how X are logged…**, **Global configuration: …**). This stays concise and action-forward; it is not an invitation to use **you** or **we**.
- **No second person:** Avoid **you**, **your**, and **we** in summaries and explanation paragraphs.
- **Prefer prose over identifier stacks:** Do not string together heavy **`backtick` + bold** jargon; use enough technical terms to stay precise, and clarify _why_ something matters when the name alone is insufficient.
- **Still accurate:** A readable tone is not an excuse to hand-wave contracts, errors, or side effects—state them plainly.

### Always include `@example`

Every **documented export** should have at least one `@example` block unless the user explicitly opts out. **Module-level** file headers do **not** use **`@example`**—only the leading sentence, optional **Exports** list, and **`@see`** pointers.

### `@example` content by kind

| Kind                                            | Example content                                                                                                                                                                                                                                                        |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Generic functions / generic types**           | Use a stand-in entity name **`Thing`** (e.g. `Thing`, `ThingId`, `mockThing`) so examples stay readable and domain-agnostic.                                                                                                                                           |
| **Non-generic functions (concrete signatures)** | Show a **function call** with **production-like mock data**: realistic string formats (UUIDs, ISO dates, plausible enums), numeric ranges that match the domain, and nested objects that look like real API payloads—not `foo`, `bar`, or `1 / 2 / 3` without meaning. |
| **Plain `interface` / `type` (not generic)**    | Show **production-like mock data** as a value that satisfies the type (assigned to a `const` or passed to a helper)—same realism bar as function examples.                                                                                                             |

### Mock data quality

- Prefer values that could plausibly appear in staging or production (realistic lengths, formats, and optional fields populated where it clarifies usage).
- Match existing domain naming in the file when the code is not generic (use the project's entity names instead of `Thing`).

### Optional tags (no duplication)

Use when helpful and accurate: `@deprecated` (with migration hint in prose), `@see` (links or symbols), `@throws` (only when thrown errors are part of the contract), `@since`, `@internal` (if the codebase uses it). Do not add `@type` merely to repeat an obvious type.

## Templates

### Function (non-generic)

```ts
/**
 * Fetches shift details for admin review (workers and venue metadata).
 *
 * @example
 * const shift = await fetchShiftDetail({
 *   shiftId: 'a1b2c3d4-e5f6-7890-abcd-ef1234567890',
 *   includeWorkers: true,
 * })
 */
async function fetchShiftDetail(params: {
  shiftId: string;
  includeWorkers?: boolean;
}) {
  const response = await api.getShift(params.shiftId);
  // Worker list omitted when includeWorkers is false — smaller payloads on list views.
  return response;
}
```

### Module with inline detail

```ts
/**
 * Shift admin API helpers. Details sit on each export or in inline comments below.
 *
 * **Exports:** {@link listOpenShifts}
 *
 * @see `./AGENTS.md` — query conventions
 */

/** Lists open shifts; filters are applied inline where the query is built. */
export function listOpenShifts() {
  // …
}
```

### Function or type with type parameters

```ts
/**
 * Returns the first entity in `items` matching `id`, or null if none match.
 *
 * @example
 * const things = [
 *   { id: 'thing_01HXYZ', name: 'Alpha', status: 'active' as const },
 *   { id: 'thing_01HABC', name: 'Beta', status: 'draft' as const },
 * ]
 * const found = findThingById(things, 'thing_01HXYZ')
 */

/**
 * Container for a page-level query and initial row data.
 *
 * @example
 * const initialState: PageState<Thing> = {
 *   query: 'warehouse onboarding',
 *   rows: [
 *     { id: 'thing_01HXYZ', title: 'Northern DC intake', updatedAt: '2026-04-03T14:22:00.000Z' },
 *   ],
 * }
 */
```

### Interface / type alias (plain object)

```ts
/**
 * Row shown in the admin shifts table before expansion.
 *
 * @example
 * const mockShiftListRow: ShiftListRow = {
 *   id: 'shift_9K3mN2pQ',
 *   startsAt: '2026-04-05T13:00:00.000Z',
 *   endsAt: '2026-04-05T21:00:00.000Z',
 *   locationLabel: 'Oakland Fulfillment — Dock B',
 *   workersAssigned: 6,
 *   workersNeeded: 8,
 * }
 */
```

## Checklist

- [ ] Summary and explanations use a **friendly, readable** tone; **imperative, verb-led** openings where they fit; module/file docs follow the same standard
- [ ] **Module file header** is **leading sentence** + optional **Exports** list + **`@see`** pointers—**no** module-level **`@example`**
- [ ] **Per-export JSDoc** is only the **leading sentence + one summary sentence** (details **inline** next to the code)
- [ ] Descriptions stand on their own per symbol—no **same as / like / parity with** another export unless using **`@see`**
- [ ] Summary is **affirmative** (what this **is** / **does**), not framed as **instead of** or **avoids repeating** a hypothetical alternative
- [ ] Block summary has **no implementation trivia** (no **`useEffect`**, version churn, or “how it is wired”)—only **purpose** and **observable behavior**
- [ ] Summary states **purpose/intent** for callers, not a walkthrough of **code flow**; step-by-step behavior lives **inline** in the implementation
- [ ] Any extra “essay” prose moved to **inline** comments on the right branches or blocks (comments state **purpose / outcome**, not control-flow or type trivia)
- [ ] No `@param` / `@returns` / `@return`
- [ ] At least one `@example` per documented export
- [ ] Generics use **Thing**-style stand-ins; concrete APIs use realistic mocks
- [ ] Examples compile mentally (correct field names and shapes for the actual types)
