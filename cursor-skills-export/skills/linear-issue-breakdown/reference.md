# Linear Issue Breakdown — Reference

## Markdown output template

```markdown
# [Issue Title]

**Linear:** [Issue ID] · **Project:** [Project Name]

## Requirements summary

[2–4 paragraphs synthesizing the task in context of the project. Draw from:

- Issue description and acceptance criteria
- Project goals and scope
- Relevant content from attached project documents
- Dependencies or constraints mentioned]

## Parallel subtasks

Each subtask should be implementable as a single Pull Request. Prefer independent, parallel work.

- [ ] **[Subtask 1 title]** — [Brief description of scope]
- [ ] **[Subtask 2 title]** — [Brief description of scope]
- [ ] **[Subtask 3 title]** — [Brief description of scope]
- [ ] ...

## Unit tests (completion criteria)

Tests that, when passing, indicate the task is done:

- [ ] [Specific test case or scenario]
- [ ] [Specific test case or scenario]
- [ ] [Specific test case or scenario]
```

## MCP tool reference

| Tool             | Purpose                                                                                   |
| ---------------- | ----------------------------------------------------------------------------------------- |
| `get_issue`      | Fetch issue by ID. Returns title, description, project, attachments.                      |
| `get_project`    | Fetch project by ID/name. Use `includeResources: true` for documents, links, attachments. |
| `list_documents` | List documents. Filter by `projectId` for project docs.                                   |
| `get_document`   | Fetch document content by ID or slug.                                                     |
| `list_projects`  | Fallback when issue has no project; search by `query` if needed.                          |
| `create_issue`   | Create subtask. Use `parentId` for parent issue, `team` from parent (required).           |

## Slug generation

Convert issue title to kebab-case for the filename:

- Lowercase
- Replace spaces with hyphens
- Remove or strip non-alphanumeric characters (except hyphens)
- Collapse multiple hyphens
- Trim leading/trailing hyphens

Example: `"Add User Authentication (OAuth)"` → `add-user-authentication-oauth`
