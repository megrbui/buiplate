---
name: refactor-component-query-layer
description: >-
  Refactors admin components and screens to consume server
  state only through TanStack React Query hooks in queries/, never by importing
  api/ or calling utils/api, legacy actions, or raw promises. Also removes
  redundant useState that duplicates query data or loading flags. Follow
  admin/src/queries/AGENTS.md for keys, queryFn shape, QueryOptions/Query,
  options spread, types, and component patterns. Use when migrating data
  fetching, deduplicating local state with React Query, or when the user asks
  to align with the api → queries → UI layer pattern.
---

# Refactor components to the query layer

## Goal

**UI (`components/`, `pages/`) must not load or mutate server data except through `queries/<domain>` hooks** (`useQuery`, `useMutation`, and small helpers like invalidation). The **`api/<domain>`** layer remains the only place that talks to **`utils/api`**; **`queries/`** imports **`api/`** functions as `queryFn` / `mutationFn`.

**Normative rules for hooks and refactor patterns** — **`admin/src/queries/AGENTS.md`** (query keys, **`queryFn`** and API shaping, **`QueryOptions`** / **`Query`**, options spread, **`@example`** on types, mutations, redundant state, destructuring, anti-patterns, tests).

## When to apply this skill

- Grep shows `from 'api/` or `from "api/` inside **`components/`** or **`pages/`** (excluding rare type-only imports—prefer types from **`queries/`** or adjacent **`api/`** types).
- `useEffect` + manual `fetch` / `.then()` on API functions.
- Legacy **`actions/*`** thunks or **`listing_actions.js`**-style helpers called from UI for HTTP.
- Direct **`getUserStatus()`**, **`api.post`**, or **`utils/api`** from a component.
- **`useState`** holding **response payloads**, **loading booleans**, or **error strings** that **`useQuery`** / **`useMutation`** already expose.

## Layering (non-negotiable)

```
components/  pages/        →  queries/<domain>  →  api/<domain>  →  utils/api
        (React Query only)      (hooks + keys)      (HTTP + types)
```

| Layer                   | Allowed imports from                                                  |
| ----------------------- | --------------------------------------------------------------------- |
| **components/, pages/** | `queries/*`, path aliases; **not** `api/*` for runtime calls          |
| **queries/**            | `api/<domain>` (functions + types), `@tanstack/react-query`, `./keys` |
| **api/**                | `utils/api`, Zod, domain parsing                                      |

**Reads** → `useQuery` (or `useInfiniteQuery` where used). **Writes** → `useMutation` with **`onSuccess` → `invalidateQueries`** for affected keys—not `useQuery`.

## Refactor checklist

1. **Inventory** — List every server read/write in the target file (including nested hooks and callbacks).

2. **Prefer reuse** — Search `queries/<domain>/` and domain `index.ts` barrels for an existing `useXxx` / `useXxxMutation`. Use it if the `queryKey` and `queryFn` match.

3. **If no hook exists** — Add **`api/`** + **`queries/<domain>/keys.ts`** + **`useXxxQuery`** / **`useXxxMutation`** per **`admin/src/queries/AGENTS.md`** (including **Query function shape**, **Query keys**, **TypeScript types** with **`@example`**, **options spread order**, **barrel export**).

4. **Replace in the component** — Remove runtime **`api/`** imports; wire **`useQuery`** / **`useMutation`**; drop redundant **`useState`** per **Refactor patterns (components using hooks)** in **`admin/src/queries/AGENTS.md`**. Render loading/error/data per **`components/AGENTS.md`**.

5. **Cache** — After mutations, **`invalidateQueries`** (or **`setQueryData`** when justified) using the same key factories as in **`queries/AGENTS.md`**.

6. **Barrels** — Call sites import from **`queries/<domain>`** (domain root), not deep file paths, per root **`AGENTS.md`**.

## Tests and verification

- After refactor, **grep** the file for **`api/`** and **`utils/api`**; runtime imports should be gone from **`components/`** / **`pages/`**.

## Scope note (app_scratch)

- **Redux** coexists with React Query; **do not** expand Redux for new server state—prefer queries.
- **Type-only** imports from `api/` for Zod-inferred types are acceptable short-term; prefer types exported from **`queries/`** or shared **`api/<domain>/types`** as the codebase converges.

## Related docs

- **`admin/src/queries/AGENTS.md`** — **primary** reference for query hooks, keys, **`queryFn`** rules, **`queries/types.ts`**, and component refactor patterns.
- **`admin/src/api/AGENTS.md`** — adding **`api/`** functions and barrels.
- Root **`AGENTS.md`** — import aliases and **`queries/`** / **`api/`** barrel rules.
- **`admin/src/components/AGENTS.md`** — UI error/loading patterns for queries and mutations.
