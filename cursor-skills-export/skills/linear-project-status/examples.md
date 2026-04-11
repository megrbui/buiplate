# Linear Project Status — Examples

## Sample Status Report

```markdown
# Project Status: Remove Redux from app_scratch

## Context

- **Summary**: Phased migration removing Redux, one reducer domain at a time, moving to React Query.
- **Documents**: (none)
- **Milestones**: Phase 5 (Low-impact/dead slices), Phase 6 (Redux removal finalization)

## Complete

- Phase 1–4 reducer migrations — ENG-36xx (Done)
- Test harness prep — ENG-36xx (Done)

## Remaining Work

**Blocking release**

- Phase 6A: Final Redux import inventory, remove provider + store wiring (ENG-3676, ENG-3677)
- Phase 6B: Test harness migration, dependency removal (ENG-3678, ENG-3679)

**Fast follows**

- Phase 5: Remove orphan actions/constants/imports (ENG-3675)

**Nice to haves**

- (none)
```

## Sample Slack Update

```
*Remove Redux from app_scratch — Project Update*

We're in the final phases of the Redux migration. Phases 1–4 are done; we're cleaning up orphan code and removing the last wiring.

*Done*
• Phases 1–4: All reducer domains migrated to React Query
• Test harness prepared for Redux-free runs

*Blocking release*
• Phase 6A: Final import inventory + remove provider/store wiring
• Phase 6B: Test harness migration + dependency removal

*Fast follows*
• Phase 5: Remove orphan actions/constants/imports

*Next*
Targeting Phase 6 completion by [date]. No blockers.
```
