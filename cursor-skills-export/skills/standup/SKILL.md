---
name: standup
description: >-
  Aggregates the user's open PRs and PRs merged in the past 24 hours across all
  workspace git repositories, groups open PRs into priority-ordered themes with
  markdown links (title + number) and Linear issues as links, fetches review
  comments for merge-readiness, merges Linear/project context into those themes
  (no separate alignment table), and appends a Slack-ready Yesterday/Today/Stretch
  TLDR (Blockers only if real). Use when the user invokes /standup, asks for a
  standup summary, what they shipped recently, or PR + Linear alignment.
---

# Standup (cross-repo PRs + Linear + Slack TLDR)

Produce a standup from:

1. **Open PRs** authored by the user across **every git root** in the workspace
2. **Merged PRs in the past 24 hours** (same repos), for “what shipped”
3. **Assigned Linear issues** (active), ranked by priority for **Today**
4. **Review state on open PRs** (comments / requested changes) **per theme**, so the user knows what to fix before merge

Tie **Linear** (teams **ENG**, **MKT**, **ALERT**) and **projects** into the **same themed sections as PRs**—do **not** use a standalone “Linear alignment” section.

After the main report, output a **Slack TLDR**: _Yesterday_ (comprehensive, discrete bullets) / _Today_ (1–3) / _Stretch_ (1). Include **Blockers** **only** if there is a real blocker; **omit the section entirely** when there are none.

## Formatting rules (main markdown report)

### Links (required wherever PRs or issues are referenced)

- **Pull requests:** Always use **markdown links** that include **title and PR number**, e.g. [`ApiResponse shapes should be frozen by default` #7578](https://github.com/org/repo/pull/7578). Same pattern for every PR mention.
- **Linear issues:** Always use **markdown links** with the issue id as link text, e.g. [`MKT-179`](https://linear.app/…/issue/MKT-179/…). Prefer the **canonical `url`** from Linear **`get_issue`** over guessing paths (slug-only URLs can 404).

### Structure

- Prefer **tables** or **nested bullet lists**—avoid long unstructured paragraphs.
- **Snapshot → Open PRs:** Group by **theme**, and order **themes by priority** (combine Linear issue priority, review urgency, draft vs ready, and business impact—state the ordering rule briefly in a footnote if non-obvious).

### Slack TLDR (separate rules)

- **Plain human language** in bullets—**do not** put Linear keys, PR numbers, or GitHub URLs in Slack (use them **only** internally to rank and pick).
- **Yesterday:** Can be **more comprehensive** than before: **one bullet per discrete shipped item** when merges span unrelated work—avoid cramming unrelated work into one bullet.
- **“So…” / outcome clauses:** Use **only** when they express **project progress** or a **milestone** (e.g. internal demo, MVP slice, release gate)—not every bullet needs a trailing “so that…”.
- **Today:** **1–3** bullets; **Stretch:** **exactly 1** bullet.
- **Blockers:** **Do not** include a `Blockers:` section when there are **no** blockers. **Only** add the section when there is at least one real blocker.

## When to run

- User says `/standup`, "standup", "what am I working on", or similar across repos.
- Before summarizing: **invoke this skill** and follow the workflow below.

## 1. Discover repositories

- Use workspace folder roots (multi-root) **or** run a one-time scan for git roots, e.g. find directories containing `.git` under the workspace roots the user has open.
- **Skip** bare submodules unless the user only cares about those; prefer top-level clones per repo.

## 2. Collect PRs per repo (GitHub CLI)

### 2a. Open PRs

In each repo directory:

```bash
gh pr list --author @me --state open --json number,title,url,headRefName,baseRefName,repository,isDraft
```

- Keep **`isDraft`** for **Stretch** selection. Prefer drafts tied to **High** / **Urgent** Linear issues when resolvable via **`get_issue`**.

### 2b. Merged in the past 24 hours

In each repo directory:

```bash
gh pr list --author @me --state merged --json number,title,url,headRefName,mergedAt,repository --limit 100
```

- Parse JSON and **keep only** items where `mergedAt` ≥ (now − 24h). **Deduplicate** by `url`.

If `@me` fails, use `gh api user` to get login and pass `--author <login>`.

If `gh` is missing or not authenticated: note that in the output and fall back to local-only context—**clearly label** limitations.

**Parallelize** across repos when safe.

### 2c. Review comments on open PRs (merge readiness)

For **each open PR** (or prioritize **non-draft** PRs and PRs with recent pushes first to stay within limits), fetch review feedback:

```bash
gh pr view <number> --repo <owner>/<name> --json url,title,number,reviews,comments
```

- Summarize **outstanding** reviewer asks: **approved with comments**, **changes requested**, or **commented** threads that need resolution before merge.
- If top-level `reviews` / `comments` are empty but you suspect inline threads, use **`gh api repos/{owner}/{repo}/pulls/{pull_number}/comments`** for review comments on lines.
- Roll this up **under the same theme** as the PR in **My assigned work & PR review status** (see §7)—not as a raw dump of every thread.

If there are many PRs, **sample** the highest-priority themes first and note “not exhaustively reviewed” if needed.

## 2d. Linear: issues assigned to you

Use Linear MCP **`list_issues`** with:

- `assignee`: `"me"`
- `includeArchived`: `false`
- `limit`: up to **100** (or paginate)

**Filter** to **active** work (exclude completed/canceled).

**Priority** (Linear numeric `priority`):

| Value    | Label       |
| -------- | ----------- |
| 1        | Urgent      |
| 2        | High        |
| 3        | Normal      |
| 4        | Low         |
| 0 / null | No priority |

## 3. Extract Linear issue keys

From each PR’s **title**, **head branch name**, and **body** (if fetched), collect `ENG-`, `MKT-`, `ALERT-`, etc. Deduplicate.

- For each ID, use Linear MCP **`get_issue`** with `id` set to the identifier. Use the returned **`url`** for markdown links in the report.

## 4. Linear: teams and projects (optional context)

- **`list_teams`** / **`list_projects`** as needed to **enrich theme narratives**—fold into **Active work** / **Snapshot** themes, not a separate alignment-only section.

## 5. Correlate PRs → Linear

- From **`get_issue`**, read **project** and **team**.
- Match PRs to issues and projects; **surface this inside the themed PR sections** (one cohesive story).

## 6. Derive themes (priority order)

Group **open** PRs into **3–8 themes** (product area, initiative, or tech area).

- **Order themes by priority** for the **Snapshot** list: consider Linear **priority** on linked issues, whether the PR is **blocking** others, **draft vs ready**, and **review state** (changes requested > silent open).
- Within each theme, list PRs as **markdown links** `[title #N](url)`.

**Merged last 24h:** For each merge, include at least **[title #N](url)** plus a short impact line.

## 7. Output format

Use this template (adjust if data is missing). **No** standalone “Linear alignment” section—Linear projects/issues appear **inside** the themed narrative.

```markdown
# Standup — [date]

[Optional one line: merge cutoff UTC, tool caveats.]

## Snapshot

| Metric                         | Value             |
| ------------------------------ | ----------------- |
| Repos with open PRs / activity | … (table or list) |
| Open PRs (mine)                | **N** total       |
| Merged last 24h (mine)         | **N** total       |

### Open PRs by theme (priority order)

1. **[Theme 1 — highest priority]**
   - [PR title #N](url) — draft/ready · linked [`ENG-1234`](url) if applicable
   - …
2. **[Theme 2]**
   - …

(Use a **table** instead of numbered themes if it reads better.)

## Shipped (merged last 24h)

| PR              | Notes                 |
| --------------- | --------------------- |
| [Title #N](url) | Impact / what changed |

Or bullet list with **every** PR as a link:

- [Title #N](url) — …

## Active work (themes + Linear + PRs)

For each **theme** (same priority order as Snapshot), write a **short cohesive paragraph or table row** that combines:

- **PRs** (linked as above)
- **Linear issues** / projects (**linked**)
- **Impact** (what moves for users, ops, or eng)

Do **not** duplicate a separate “alignment” table.

## My assigned work & PR review status

Group by **theme** (align with Snapshot). For each theme:

| Assigned Linear (active)      | Open PRs in this theme | Review / merge notes                                                                         |
| ----------------------------- | ---------------------- | -------------------------------------------------------------------------------------------- |
| [MKT-179](url) — title phrase | [PR #…](url), …        | Summarize **reviewer feedback** (requested changes, “approve but resolve threads”, nits, CI) |

Use bullets if the table is too wide.

- **Goal:** Make it obvious **what to change** to get merges **without** re-reading every thread.

## Gaps / follow-ups

**Recommendations**

- Bullet list of concrete suggestions (e.g. close stale branches, add issue keys to titles).

**Task checklist**

- [ ] …
- [ ] …
```

Keep the main report **skimmable** (expand only when the user asks).

## 8. Slack TLDR (after the report)

Append a **copy-paste** block for Slack.

### Sections

| Section       | Count              | Content                                                                                                                                                                                                                                                  |
| ------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Yesterday** | Many bullets OK    | **Discrete** bullets for **unrelated** merges—each bullet should stand alone. Use **“so…” / milestone** phrasing **only** when it reflects **project progress** (e.g. “completes MVP release milestone for internal demo”), not as filler on every line. |
| **Today**     | **1–3**            | Most important actions for **today**—human language only.                                                                                                                                                                                                |
| **Stretch**   | **1**              | Secondary open work if time—no overlap with Today.                                                                                                                                                                                                       |
| **Blockers**  | **Only if needed** | **Do not** output a `Blockers:` line when there are **none**.                                                                                                                                                                                            |

### Layout

1. Headers on their own line: `Yesterday:`, `Today:`, `Stretch:` — and **`Blockers:`** only when applicable.
2. **No blank line** between a header and its **first** bullet.
3. **One blank line** between sections.

### Ranking

- **Yesterday:** One bullet per **valuable** merge when they are **distinct**; merge related tiny PRs into one bullet if **same** theme.
- **Today:** Rank **assigned** Linear + **non-draft** PRs that need **your** action today.
- **Stretch:** Single best **if bandwidth** item from **open** work.

**Slack must not** include PR numbers, GitHub links, or Linear IDs.

## 9. Archive output to disk (daily file)

After the **full** standup is produced, **write** it to the **scratch** repo:

- **Path:** `scratch/daily-standup/YYYY-MM-DD.md`
- **Contents:** Same markdown as chat: cutoff, full report, fenced Slack block, footnotes.
- **Create** `daily-standup/` if missing.
- If **scratch** is absent, skip and note in the output.

### Example archived file

See `scratch/daily-standup/2026-03-26.md` for a worked example.

## Quality bar

- **Evidence over guesses**: tie the report to `gh` JSON, PR **`reviews`/`comments`**, and Linear **`get_issue`** / **`list_issues`**.
- **Links in the main report** are mandatory for PRs and Linear issues **where referenced**.
- **Slack:** no IDs, no Blockers section when empty.
- **Explicit assumptions** if auth, `gh`, or Linear MCP fails.
- **No fabrication** of PRs, merges, or review text.
