---
name: document-and-review-feature
description: Orchestrates create-documentation, review-feature-documentation, and review-documentation-structure across three separate agents. Reviewer agents approve or reject; doc agent applies feedback and resubmits until both approve. No user approval gates.
---

# Document and Review Feature (Orchestrated)

Runs the create-documentation skill in one agent, then the review-feature-documentation and review-documentation-structure skills in two other agents. **Reviewer agents provide approval** — the doc agent applies feedback and resubmits until both reviewers approve. No user approval gates.

## When to Use

- User wants feature documentation created and iteratively refined until it passes independent accuracy and structure review
- Fully autonomous doc-and-review loop without user approval gates

## Output Directory

All planning and collaboration docs from the run go to:

```text
scratch/document-and-review/<feature-name>/
```

**Artifacts:**

- `<feature-name>.md` — main document
- `<feature-name>-research-notes.md` — research notes from create-documentation
- `<feature-name>-plan.md` — implementation plan from create-documentation
- `<feature-name>-review.md` — accuracy review report
- `<feature-name>-structure-review.md` — structure review report
- `<feature-name>-review-plan.md` — change plan from accuracy review (if created)

Use `OUTPUT_DIR = scratch/document-and-review/<feature-name>/` in all prompts. Path is relative to workspace root (scratch is a workspace root).

## Approval Loop

1. Doc agent creates initial document (skips user approval)
2. Launch both reviewer agents (accuracy + structure)
3. If either reviewer verdict is not "Approved": combine feedback, doc agent applies changes, repeat from step 2
4. Continue until both reviewers approve

**Approved verdicts:**

- Accuracy: "Accurate" (from review-feature-documentation)
- Structure: "Compliant" (from review-documentation-structure)

**Needs changes:** "Minor issues" or "Major gaps" — doc agent must apply feedback and resubmit.

## Workflow

### Step 1: Launch documentation agent (initial or revision)

Use `mcp_task` to spawn an agent that creates or revises the documentation. **Do not** use `run_in_background`.

**Prompt template for initial creation (iteration 1):**

```text
Create feature documentation for: [FEATURE_NAME / user's request].

Read and follow the create-documentation skill at ~/.cursor/skills/create-documentation/SKILL.md.

CRITICAL: Skip all user approval gates. Do NOT wait for user approval of the plan. Proceed directly: research → plan → write the doc. Reviewer agents will approve or request changes.

OUTPUT DIRECTORY: Write all artifacts to scratch/document-and-review/<feature-name>/
- Main doc: scratch/document-and-review/<feature-name>/<feature-name>.md
- Research notes: scratch/document-and-review/<feature-name>/<feature-name>-research-notes.md
- Plan: scratch/document-and-review/<feature-name>/<feature-name>-plan.md

When complete, return the path to the created document: scratch/document-and-review/<feature-name>/<feature-name>.md

User context: [Include any feature name, scope, or constraints from the user]
```

**Prompt template for revision (iteration 2+):**

```text
Apply reviewer feedback to the document at: [DOC_PATH].

FEEDBACK TO APPLY:

Accuracy reviewer feedback:
[Copy the full content of Gaps, Inaccuracies, Citation issues, and Suggestions from scratch/document-and-review/<feature-name>/<feature-name>-review.md]

Structure reviewer feedback:
[Copy the full content of Recommendations and any specific issues from scratch/document-and-review/<feature-name>/<feature-name>-structure-review.md]

Read the create-documentation skill for structure rules. Edit the document in place to address all feedback. Preserve structure and citations where unchanged.

When complete, return the path to the updated document: [DOC_PATH]
```

### Step 2: Launch both reviewer agents

Run **both** reviewers. They can run in parallel (two `mcp_task` calls in the same turn) since they only need the doc path.

**Prompt template for accuracy reviewer:**

```text
Review the feature documentation at: [DOC_PATH].

Read and follow the review-feature-documentation skill at ~/.cursor/skills/review-feature-documentation/SKILL.md.

CRITICAL: Do NOT wait for user approval. Do NOT solicit feedback. Output the review report to scratch/document-and-review/<feature-name>/<feature-name>-review.md (create the directory if needed).

At the end of your response, output exactly one of:
- APPROVED (if Verdict is "Accurate")
- NEEDS_CHANGES (if Verdict is "Minor issues" or "Major gaps") — and include the full feedback (Gaps, Inaccuracies, Citation issues, Suggestions) for the doc agent to apply

Do NOT trust the doc as source of truth — verify every claim against the codebase.
```

**Prompt template for structure reviewer:**

```text
Review the documentation structure of: [DOC_PATH].

Read and follow the review-documentation-structure skill at ~/.cursor/skills/review-documentation-structure/SKILL.md.

CRITICAL: Do NOT wait for user approval. Do NOT solicit feedback. Output the review report to scratch/document-and-review/<feature-name>/<feature-name>-structure-review.md (create the directory if needed).

At the end of your response, output exactly one of:
- APPROVED (if Verdict is "Compliant")
- NEEDS_CHANGES (if Verdict is "Minor issues" or "Major gaps") — and include the full Recommendations section for the doc agent to apply
```

### Step 3: Check verdicts and loop

- If both reviewers returned APPROVED: proceed to Step 4.
- If either returned NEEDS_CHANGES: combine feedback from both review reports (read from `scratch/document-and-review/<feature-name>/`), launch the doc agent with the revision prompt (Step 1 revision template), then repeat Step 2.
- Set a maximum iteration limit (e.g. 5) to avoid infinite loops. If exceeded, present current state and feedback to the user.
- If reviewers repeatedly flag the same categories (e.g. leading sentences, null conditions) after 3+ iterations, consider presenting current state and remaining feedback rather than continuing—minor issues may not converge.

### Step 4: Synthesize and present

- Summarize what was created and that both reviewers approved
- Point the user to the output directory: `scratch/document-and-review/<feature-name>/` (doc, research notes, plan, review reports)

## Important Notes

- **Output location**: All artifacts go to `scratch/document-and-review/<feature-name>/`. Create the directory if it does not exist. Path is relative to workspace (scratch is a workspace root).
- **No user approval**: All approval comes from reviewer agents. The doc agent and reviewers skip "wait for user" steps.
- **Sequential within loop**: Doc agent → both reviewers → check verdicts. If NEEDS_CHANGES, doc agent again → both reviewers again.
- **Parallel reviewers**: Steps 2a and 2b (accuracy + structure) can run in parallel.
- **Skill paths**: Use `~/.cursor/skills/` for personal skills. Adjust if skills live elsewhere.
- **Subagent context**: Subagents do not see prior steps. Each prompt must be self-contained. For revisions, pass the full feedback text in the prompt.

## Example Invocation

User: "Document and review the assessments-classet-interview feature"

1. Launch doc agent (initial) → doc at `scratch/document-and-review/assessments-classet-interview/assessments-classet-interview.md`
2. Launch accuracy reviewer + structure reviewer in parallel
3. Accuracy: NEEDS_CHANGES (Gaps: X, Y). Structure: NEEDS_CHANGES (Recommendations: A, B)
4. Launch doc agent (revision) with combined feedback
5. Launch both reviewers again
6. Both APPROVED → present `scratch/document-and-review/assessments-classet-interview/` with all artifacts
