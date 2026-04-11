---
name: pr-stats-engineering
description: Compute PR metrics for the engineering team—time to review (ready→closed), PR size, and ready vs merged by repo. Outputs p50/p90 per repo and overall. Week-over-week trends (+/-). Personal stats column (You as author/reviewer). Use when asked for PR stats, mean/median PR size, time to review, personal PR stats, or reviewer stats.
---

# Engineering PR Stats

## When to use

- User asks for **mean or median PR size**, **p50/p90 PR size**, or **PR size by repo**
- User asks for **time to review**, **review time**, **ready to closed**, or **p50/p90 time to review**
- User asks for **PRs ready vs merged per week** (or by date)
- User asks for **engineering team** or **per-repository** PR metrics
- User asks for **personal PR stats**, **my stats vs team**, or **as author / as reviewer**
- User asks for **trends**, **week-over-week**, or **compare to last week**

## What it does

1. **Time to review (ready → closed)**  
   Hours from when the PR became “ready for review” (or opened) to merged/closed. Uses GitHub GraphQL `ReadyForReviewEvent` or `createdAt`. Reports **p50** and **p90** per repo and overall.

2. **PR size**  
   Additions + deletions per merged PR. Reports **p50** and **p90** per repo and overall.

3. **Ready vs merged**  
   Count of PRs that became ready and that were merged in the **past week** (7 days) and **past month** (30 days), per repo and overall. One table, one row per repo.

4. **Week-over-week trends (+/-)**  
   Compares each metric to the previous run. Shows deltas as `(+X)` or `(-X)` for time-to-review, size, and ready/merged counts. Persists current stats to `$DATA_DIR/previous_stats.json` for next run.

5. **Personal stats column**  
   The main script includes a **You (author)** row for time-to-review and PR size, plus **You (author)** and **You (reviewer)** rows in the ready vs merged table. Uses `gh api user` for the current login.

6. **Personal summary (optional, full)**  
   Run `pr-stats-personal.sh` after the main script for a detailed comparison: your stats vs team with % of team for counts.

## How to run

**Prerequisites:** `gh` (GitHub CLI) authenticated, `jq` installed.

Run the script (from repo root or where the script lives):

```bash
bash scripts/pr-stats.sh
```

Optional: pass a directory for cached data (default `/tmp/pr-stats-data`):

```bash
bash scripts/pr-stats.sh /tmp/my-pr-stats
```

Script config (edit if needed): `REPOS="backend employer ui admin worker-web"`, `PER_REPO=200`, org `workwhile`. Increase `PER_REPO` for more PRs per repo (slower).

**Personal summary (you vs team):** Run after the main script (so team baseline exists):

```bash
bash scripts/pr-stats-personal.sh
```

Uses `gh api user` for the current login. Fetches your merged PRs as **author** (search `org:workwhile author:USER is:pr is:merged`) and as **reviewer** (search `org:workwhile reviewed-by:USER is:pr is:merged`), then computes the same metrics and compares to team overall.

## Output format

Summarize results in this shape:

**Time to review (ready → closed)**  
| Repo / Overall | n | p50 (hrs) | p90 (hrs) | trends (+/-) |
Include **You (author)** row. Show week-over-week deltas as `(+X)` or `(-X)` when previous run exists.

**PR size (additions + deletions)**  
| Repo / Overall | n | p50 (lines) | p90 (lines) | trends (+/-) |
Include **You (author)** row.

**Ready vs merged**  
One table by repo with counts for past week and past month. Include **You (author)** and **You (reviewer)** rows. Show trends (+/-) vs previous run.

| repo           | ready_past_week | merged_past_week | ready_past_month | merged_past_month |
| -------------- | --------------- | ---------------- | ---------------- | ----------------- |
| backend        | 12              | 10               | 45               | 42                |
| …              | …               | …                | …                | …                 |
| overall        | 35              | 32               | 120              | 115               |
| You (author)   | 6               | 7                | 26               | 26                |
| You (reviewer) | —               | 18               | —                | 44                |

**Personal summary (You vs team)**  
When the user asks for personal stats or “how do I compare,” run `pr-stats-personal.sh` after the main script and present:

- **As PR author:** table or lines: Your n (and % of team), time-to-review p50/p90 vs team, size p50/p90 vs team, ready/merged past_week and past_month vs team **with your % of team** for counts.
- **As PR reviewer:** Your n (merged PRs you reviewed), time to your first review (p50/p90 hrs), and merged PRs you reviewed in past_week / past_month **as % of team merged**.

**Example (megrbui):**

| Role     | Metric                          | You     | Team (overall) | % of team     |
| -------- | ------------------------------- | ------- | -------------- | ------------- |
| Author   | n (merged PRs)                  | 391     | 1765           | 22.2%         |
| Author   | Time to review p50              | 6.4 h   | 0.84 h         | —             |
| Author   | PR size p50                     | 32      | 22             | —             |
| Author   | Ready past_week / past_month    | 6 / 26  | 67 / 314       | 9.0% / 8.3%   |
| Author   | Merged past_week / past_month   | 7 / 26  | 84 / 323       | 8.3% / 8.0%   |
| Reviewer | n (PRs you reviewed)            | 363     | —              | —             |
| Reviewer | Time to your review p50         | 1.7 h   | —              | —             |
| Reviewer | Reviewed past_week / past_month | 18 / 44 | 84 / 323       | 21.4% / 13.6% |

## Script location

- **Team stats:** `scratch/scripts/pr-stats.sh` — fetches team data, computes stats with trends (+/-), calls personal script with `--json` for You rows, persists to `$DATA_DIR/previous_stats.json`
- **Personal summary:** `scratch/scripts/pr-stats-personal.sh` — run standalone for full You vs team; or `--json` for machine output (used by main script)

Use the skill’s script when the user is not in the WorkWhile repo; otherwise either path is fine.
