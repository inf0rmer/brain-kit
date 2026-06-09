---
name: brain-init
description: Scaffold a brand-new "brain" vault in its own separate repository from this brain-kit. Use when the user wants to create/initialize/bootstrap a new brain, second brain, or notes vault. Runs `brain init` to copy the vault skeleton + skills into a target directory and git-init it.
---

# brain-init

Creates a new brain vault (its own repo) from this kit. The kit holds the canonical
skills (`skills/`) and the vault skeleton (`template/`); this skill copies them into
a target directory so the new brain is self-contained and version-controlled on its own.

## Run it

```
brain init <target-dir> [--name "Brain Name"] [--seed] [--no-git]
```
(Falls back to `bin/brain init …` if `brain` isn't on PATH.)

- `--seed` includes the example notes (good for a first look / testing).
- `--no-git` skips the git repo + initial commit.
- Target must be empty or not exist (use `--force` to overlay).

Confirm the target path with the user first — this creates a directory and a git repo.

## What the new brain gets

- The full folder skeleton (`people/`, `projects/`, `ideas/`, `resources/`, `meta/`).
- `CLAUDE.md` operating manual (titled with `--name`).
- All five brain skills under `.claude/skills/` (capture, recall, review, import, upkeep).
- A local git repo with an initial commit (unless `--no-git`).

## After creating

Tell the user to:
1. `cd <target-dir>` and open Claude Code there.
2. One-time `gws auth login` to enable `brain-import` from Google Docs.
3. Optionally set up the weekly `brain-upkeep` cron (see the kit README).

This skill lives only in the kit — a brain doesn't initialize other brains.
