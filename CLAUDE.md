# brain-kit — maintainer notes

This repo is the **kit** that scaffolds brain vaults. It is not itself a brain, so
the brain skills (capture/recall/review/import/upkeep) are intentionally **not**
active here — only `brain-init` is. See `README.md` for the user-facing overview.

## Where things live

- `skills/<name>/SKILL.md` — **canonical** brain skills. Edit here; they are copied
  verbatim into every new brain's `.claude/skills/`.
- `template/` — the vault skeleton copied into a new brain. `template/CLAUDE.md` is
  the **brain's** operating manual (frontmatter schema + retrieval/capture protocol).
- `template/_seed/` — optional example notes (`brain init --seed`); excluded from a
  normal init.
- `bin/brain` — the CLI dispatcher; finds and execs `libexec/brain-<subcommand>`.
- `libexec/brain-<cmd>` — one script per subcommand (`brain-init`, `brain-open`). Add
  a new vault-management command by dropping an executable `libexec/brain-<name>` in;
  `bin/brain` and its help list pick it up automatically.
- `template/.obsidian/` — baseline Obsidian config (Templates + Bases enabled).
- `template/meta/bases/*.base` — native Obsidian Bases (live dashboards), embedded in
  the index notes via `![[*.base]]`.
- `.claude/skills/brain-init/` — the only skill active in this kit (wraps `brain init`).

## Obsidian integration (gotchas)

- **Bases need Obsidian 1.9+.** `.base` files won't render on older versions.
- `core-plugins.json` is an **object map** (`{"templates": true, "bases": true}`) in
  current Obsidian; the format has changed across versions. If a brain's plugins don't
  enable, re-derive the file from a freshly-created vault's `.obsidian/` on the target
  machine.
- `brain init` injects the Obsidian vault name (the target dir basename) into the
  brain's `CLAUDE.md` by replacing the `<set-by-brain-init>` sentinel — keep that
  sentinel in `template/CLAUDE.md`.
- Frontmatter is the shared contract for the Bases too: `role`/`manager` (person) and
  `owner` (project) must stay defined in the manual, the templates, the seed notes,
  the `.base` columns, and the capture/upkeep skills together.

## Rules when editing the kit

- Keep `template/CLAUDE.md` (the schema/protocol) and the skills **in sync** — a
  skill must not reference a field, folder, or convention the manual doesn't define.
- Skill names, frontmatter `type` values, and folder paths are a shared contract;
  change them in all three places (manual, templates, skills) together.
- `brain init` copies `skills/*` into brains but never copies the `brain` CLI — brains
  don't init brains. Keep subcommands in `libexec/`, not in `skills/`.
- After changing `bin/brain` or `libexec/brain-init`, test it:
  `bin/brain init /tmp/test-brain --seed` then inspect the tree and `rm -rf /tmp/test-brain`.

## Pre-commit

Run a smoke test of the scaffolder (init into a temp dir, verify the tree, clean up)
before committing changes to `bin/`, `template/`, or `skills/`.
