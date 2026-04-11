# Linear Issue Breakdown — Examples

## Example: User request

**User:** "Break down LIN-456 for me"

**Agent actions:**

1. `get_issue` with id `LIN-456`
2. Extract `projectId`, `teamId` from response
3. `get_project` with that ID, `includeResources: true`
4. `list_documents` with `projectId`
5. `get_document` for each document
6. Write draft `LIN-456-add-oauth-login.md`
7. Ask refinement questions (e.g., "Should any subtasks be merged?")
8. When user confirms → `create_issue` for each subtask with `parentId: LIN-456`

## Example output (excerpt)

**Filename:** `LIN-456-add-oauth-login.md`

```markdown
# Add OAuth login

**Linear:** LIN-456 · **Project:** Auth Service

## Requirements summary

The Auth Service project requires OAuth 2.0 support for Google and GitHub. Per the project spec, we must support PKCE for public clients and store tokens securely. The existing JWT flow should remain unchanged.

## Parallel subtasks

- [ ] **OAuth provider config** — Add Google/GitHub client config, env vars, and provider discovery
- [ ] **OAuth callback handler** — Implement /auth/callback, token exchange, and session creation
- [ ] **PKCE flow** — Add code_verifier/code_challenge generation and validation
- [ ] **Token storage** — Secure storage layer for OAuth tokens (aligned with existing JWT storage)

## Unit tests (completion criteria)

- [ ] OAuth callback returns valid session for valid authorization code
- [ ] PKCE validation rejects mismatched code_verifier
- [ ] Token storage encrypts tokens at rest
```
