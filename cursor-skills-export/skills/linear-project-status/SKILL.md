---
name: linear-project-status
description: Gathers Linear project context (documents, issues, description), compares against issue statuses to determine completion, proposes remaining work and recommends additional tasks when scope has gaps, asks clarifying questions, and drafts a skimmable Slack project update. Use when given a Linear project name/ID or when the user asks for a project status update, project summary, or Slack update for a Linear project.
---

# Linear Project Status & Slack Update

Given a Linear project, gather context, assess completion vs remaining work, and produce a Slack-ready project update after clarification.

## Workflow

### 1. Gather project context

**Project details**

- `mcp_linear_get_project` with `query` = project name or ID, `includeResources: true`

**Documents**

- `mcp_linear_list_documents` with `projectId`
- For each document: `mcp_linear_get_document` to fetch content

**Issues**

- `mcp_linear_list_issues` with `project` = project name/ID, `includeArchived: false`
- Fetch all pages if `hasNextPage`; include subissues (issues with `parentId`)

**Statuses**

- `mcp_linear_list_issue_statuses` with `team` from the project's team (or first issue's team)

**Milestones** (optional)

- `mcp_linear_list_milestones` with `project` for phase/scope context

### 2. Define "complete"

Treat an issue as **complete** when its status `type` is `completed` or `canceled` (e.g., Done, Canceled, Icebox). Treat `started`, `backlog`, `unstarted`, `triage` as **incomplete**.

If the team uses custom statuses, infer from the status name (e.g., "Done", "Shipped", "Complete" → complete).

### 3. Compare requirements vs work

Build a requirements map from:

- Project `description` and `summary`
- Document content (specs, scope, acceptance criteria)
- Milestone names and descriptions

Map each requirement/scope item to:

- **Complete**: Covered by one or more issues in a complete status
- **Remaining**: No covering issue, or covering issues are incomplete
- **Gap**: Requirement in scope but no issue exists yet

### 3b. Categorize remaining work

Break remaining work into three buckets:

| Bucket               | Criteria                                                                |
| -------------------- | ----------------------------------------------------------------------- |
| **Blocking release** | Minimal next release that offers value — smallest shippable increment   |
| **Fast follows**     | Next minimal releases providing value — subsequent incremental releases |
| **Nice to haves**    | Polish, delight, and non-critical functionality                         |

Infer from issue descriptions, milestone ordering, and document language. When unclear, default to the lower-priority bucket.

### 3c. Recommend additional tasks for gaps

When requirements from the project description or documents have **no covering issue**, propose new tasks as bullet points. Include:

- Task title (actionable, scoped)
- Brief description of what it covers
- Suggested parent issue or milestone if inferrable

Example: "Project doc requires 'audit logging' but no issue exists → propose: _Add audit logging for admin actions_ — Implement logging per spec section 3.2."

### 4. Output: status report

Produce a markdown report:

```markdown
# Project Status: [Project Name]

## Context

- **Summary**: [project summary]
- **Documents**: [list with brief purpose]
- **Milestones**: [if any]

## Complete

- [Requirement/scope item] — [issue IDs]
- ...

## Remaining Work

**Blocking release**

- [Item]
- ...

**Fast follows**

- [Item]
- ...

**Nice to haves**

- [Item]
- ...

## Recommended Additional Tasks

(Only if gaps exist: requirements with no covering issue)

- [Proposed task] — [brief rationale]
- ...
```

### 5. Ask clarifying questions

Before drafting the Slack update, ask:

- "Does this accurately reflect what's done vs remaining?"
- "Should any items move between blocking release / fast follows / nice to haves?"
- "Should any requirements be added, removed, or re-scoped?"
- "Should I create the recommended additional tasks as Linear issues?"
- "Any milestones or phases to highlight?"
- "Target audience for the Slack update? (e.g., stakeholders, team, leadership)"
- "Tone: formal, casual, or technical?"

Iterate until the user confirms.

### 6. Draft Slack update (after approval)

Once the user approves, write a **concise, skimmable** Slack message:

**Structure**

- Short heading (project name or theme)
- 1–2 sentence summary
- **Done** (bullets)
- **Blocking release** (bullets)
- **Fast follows** (bullets)
- **Nice to haves** (bullets)
- Optional: **Next steps** or **Blockers**

**Style**

- Use headings (`*Heading*` or `**Heading**` for Slack)
- Short bullets; avoid long paragraphs
- Link to Linear project/issue when useful
- No jargon unless the audience expects it

**Template**

```
*[Project Name] — Project Update*

[1–2 sentence summary of current state.]

*Done*
• [Item]
• [Item]

*Blocking release*
• [Item]
• [Item]

*Fast follows*
• [Item]
• [Item]

*Nice to haves*
• [Item]
• [Item]

[Optional: *Next steps* or *Blockers*]
```

### 7. Deliver

Output the Slack update in a code block so the user can copy-paste. Optionally offer to post to Slack if an integration exists.

## MCP Tools Reference

| Tool                             | Purpose                                     |
| -------------------------------- | ------------------------------------------- |
| `mcp_linear_get_project`         | Project details, description, resources     |
| `mcp_linear_list_documents`      | Documents linked to project                 |
| `mcp_linear_get_document`        | Document content                            |
| `mcp_linear_list_issues`         | Issues (and subissues) in project           |
| `mcp_linear_list_issue_statuses` | Status definitions (complete vs incomplete) |
| `mcp_linear_list_milestones`     | Project milestones                          |

## Additional Resources

- For a sample status report and Slack output, see [examples.md](examples.md)
