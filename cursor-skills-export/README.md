# Cursor custom skills export

This folder is a snapshot of personal Cursor Agent Skills copied from the machine that ran the export.

## Source paths

| Directory here | Typical install location |
|----------------|-------------------------|
| `skills/` | `~/.cursor/skills/` |
| `skills-cursor/` | `~/.cursor/skills-cursor/` |

## Restore on another machine

```sh
rsync -a skills/ ~/.cursor/skills/
rsync -a skills-cursor/ ~/.cursor/skills-cursor/
```

Restart Cursor (or reload the window) so it picks up new or changed skills.

## Workspace repos

A search under the multi-repo workspace did not find additional `SKILL.md` trees in application repositories (only unrelated matches inside Python virtualenvs). If you add repo-local skills later, copy them into a subfolder here and note the repo path in this README.
