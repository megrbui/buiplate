---
name: linear-issue-breakdown
description: Fetches a Linear issue with project context and documents, outputs a markdown breakdown with requirements summary and parallel PR-sized subtasks, asks questions to refine the plan, and creates nested subtasks in Linear when confirmed. Use when given a Linear issue ID (e.g., LIN-123) or when the user asks to break down a Linear task.
---

# Linear Issue Breakdown

Given a Linear issue ID, fetch the issue, project context, and project documents, then produce a markdown file with a requirements summary, parallel subtasks, and unit tests.

## Workflow

### 1. Fetch the issue

Call `get_issue` with the Linear issue ID (e.g., `LIN-123` or full ID):

```
call_mcp_tool(server: "plugin-linear-linear", toolName: "get_issue", arguments: { id: "<issue-id>", includeRelations: true })
```

Extract from the response: `title`, `description`, `project` (or `projectId`), and any `attachments`.

### 2. Fetch project context

Use the project ID from the issue. Call `get_project` with `includeResources: true`:

```
call_mcp_tool(server: "plugin-linear-linear", toolName: "get_project", arguments: { query: "<project-id>", includeResources: true })
```

### 3. Fetch project documents

Call `list_documents` filtered by project ID:

```
call_mcp_tool(server: "plugin-linear-linear", toolName: "list_documents", arguments: { projectId: "<project-id>" })
```

For each document returned, call `get_document` to fetch content:

```
call_mcp_tool(server: "plugin-linear-linear", toolName: "get_document", arguments: { id: "<document-id>" })
```

### 4. Generate filename

Format: `{linear-id}-{kebab-case-slug}.md`

- Linear ID: Use the issue identifier (e.g., `LIN-123`)
- Slug: Convert issue title to kebab-case (lowercase, spaces → hyphens, strip non-alphanumeric except hyphens)

Example: `LIN-123-add-user-authentication.md`

### 5. Write the draft markdown file

Use the template in [reference.md](reference.md). Fill in:

- **Requirements summary**: Synthesize the issue description, project context, and relevant document content into a concise summary of what must be done and why.
- **Parallel subtasks**: Bullet points where each subtask is independently implementable as a single PR. Order by dependency only when necessary; prefer parallelizable work.
- **Unit tests**: Specific tests that, when passing, indicate the task is complete.

### 6. Refinement — ask questions

Before finalizing, ask the user questions to refine the plan. Examples:

- "Does this requirements summary capture what you need?"
- "Should any subtasks be merged, split, or reordered?"
- "Are there additional unit tests or acceptance criteria to add?"
- "Any dependencies between subtasks I should reflect?"

Iterate until the user confirms the plan is ready.

### 7. Create subtasks in Linear (after confirmation)

When the user confirms (e.g., "looks good", "go ahead", "create them"):

1. For each subtask in the markdown, call `create_issue` with:
   - `title`: The subtask title
   - `description`: The subtask description (scope) as markdown
   - `parentId`: The parent issue ID
   - `team`: Team from the parent issue (required)
   - `project`: Project from the parent issue (if applicable)

2. Update the markdown file to link each subtask to its new Linear issue ID.

3. Summarize what was created (issue IDs and titles).

## Output location

Write the file to the project root or a `docs/` folder if it exists. Confirm with the user if unclear.

## Additional resources

- Markdown template: [reference.md](reference.md)
- Workflow example: [examples.md](examples.md)

## Notes

- If the issue has no project, use `list_projects` to infer context or proceed with issue-only data.
- If `list_documents` returns none, the project may not have linked documents; proceed with issue + project data.
- For slug generation: `re.sub(r'[^a-z0-9\s-]', '', title.lower()).strip().replace(' ', '-')` (Python) or equivalent.
- `create_issue` requires `team`; use the team from the parent issue. If missing, ask the user which team to use.
