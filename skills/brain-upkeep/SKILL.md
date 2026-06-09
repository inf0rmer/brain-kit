---
name: brain-upkeep
description: Run a hygiene pass over the brain vault and refresh the index notes. Use for "tidy/clean up the brain", "check for stale notes", "fix the indexes", or when invoked on a schedule. Finds orphans, stale active notes, frontmatter problems, and broken links; writes findings to meta/upkeep-report.md. Proposes only — never auto-deletes or auto-merges.
---

# brain-upkeep

Maintains vault health and keeps retrieval fast. Read `CLAUDE.md` first. **Never
delete or merge automatically** — write proposals to `meta/upkeep-report.md` for the
user to action.

## Checks

1. **Orphans** — notes no other note links to (and that link to nothing). Grep for
   each note's basename across the vault. **Scope: `.md` notes only** — `.base`,
   `.obsidian/`, and `meta/templates/` are config/scaffolding, never orphans.
2. **Stale active** — `status: active` projects/ideas whose `updated` is >14 days
   before today; `person` hubs with no 1-1 in >30 days.
3. **Frontmatter** — missing/invalid required fields per type (see `CLAUDE.md`
   schema, incl. `role`/`manager` on people and `owner` on projects, which feed the
   Bases); `updated` older than the file's last real edit.
4. **Broken links** — `[[wikilinks]]` with no matching note basename (the `![[*.base]]`
   embeds in index notes are expected — don't flag them).
5. **Index drift** — entities on disk missing from `meta/indexes/`, or index links
   pointing at notes that no longer exist. (Bases self-update from frontmatter — no
   maintenance needed; only the static link lists need refreshing.)

## Actions

- **Refresh indexes** (`meta/indexes/people.md`, `projects-active.md`, `ideas.md`):
  add missing entities, move done/parked items to their section, drop dead links.
  This is the one thing upkeep edits directly.
- **Write the report** to `meta/upkeep-report.md`: dated sections per check, each
  finding as a checkbox with the note `[[link]]` and a suggested fix. Overwrite the
  previous report.
- **Summarize** to the user: counts per category + the top few items needing action.

## Scheduling (run without remembering)

Set up once per brain so this runs automatically. Two complementary options:

- **Weekly cron** (recommended): in Claude Code, create a scheduled job that opens
  this vault and runs `/brain-upkeep` (e.g. Monday 09:00). It updates
  `meta/upkeep-report.md`; you review when convenient.
- **SessionStart nudge** (optional): a hook in `.claude/settings.json` that prints a
  one-line "N stale notes — run /brain-upkeep" when you open the vault. The brain-init
  README shows the snippet.
