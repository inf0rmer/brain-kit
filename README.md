# brain-kit

The **meta framework** for a personal "second brain" backed by Obsidian and driven
by Claude Code. This repo is *not* a brain — it's the kit you use to **init** brains,
each living in its own separate repository.

## What's here

```
brain-kit/
  bin/brain             # the `brain` CLI dispatcher (put bin/ on your PATH)
  libexec/
    brain-init          #   `brain init` — scaffolds a new brain vault
    brain-open          #   `brain open` — opens a vault in Obsidian
  skills/               # canonical brain skills (copied into each new brain)
    brain-capture/      #   file new input (text or a Google Doc link)
    brain-recall/       #   answer questions from notes, with [[citations]]
    brain-review/       #   1-1 prep & status digests
    brain-import/       #   pull Google Docs via `gws` (incl. latest-dated-entry)
    brain-upkeep/       #   hygiene pass + index refresh (cron-friendly)
  template/             # the vault skeleton copied into a new brain
    CLAUDE.md           #   the brain's operating manual (retrieval/capture protocol)
    .obsidian/          #   baseline Obsidian config (Templates + Bases enabled)
    meta/templates/     #   note templates per type
    meta/indexes/       #   MOC dashboards: static lists (Claude) + embedded Base (you)
    meta/bases/         #   native Obsidian Bases (live dashboards over frontmatter)
    _seed/              #   optional example notes (brain init --seed)
  .claude/skills/
    brain-init/         # the only skill active in the kit (wraps `brain init`)
```

The `brain` CLI is a dispatcher: each subcommand is a `libexec/brain-<name>`
script, so future vault-management commands are added by dropping a new script in.

## Design in one paragraph

Retrieval is **agentic, not embeddings-based**: Claude navigates a well-structured
vault (consistent frontmatter, `[[wikilinks]]`, index notes) and reasons over grep
results. The conventions live in each brain's `CLAUDE.md`, which is the real "brain".
Brains are **local-only** (sensitive management data never leaves the machine); the
single permitted outbound call is `gws` to *import* a Google Doc you point at.

## Create a brain

```bash
brain init ~/Code/my-brain --name "My Brain"        # clean vault
brain init ~/tmp/demo-brain --name "Demo" --seed     # with example notes
```

Then:

```bash
cd ~/Code/my-brain && claude
# "log my 1-1 with Jane: ..."   /   "prep me for my 1-1 with Jane"
gws auth login        # one-time, enables brain-import from Google Docs
```

The `brain` command is available once `bin/` is on your `PATH` (run `brain` or
`brain help` to list subcommands).

## Obsidian

Each brain ships a baseline `.obsidian/` config, so it opens ready to use:

- **Core Templates** enabled, pointed at `meta/templates/`.
- **Bases** (Obsidian's native database, **requires Obsidian 1.9+**) enabled. The
  `meta/bases/*.base` files render live people / projects / ideas / 1-1 dashboards
  from note frontmatter. They're embedded at the top of each `meta/indexes/*.md`.
- New notes default to `00-inbox/`; `[[wikilinks]]` kept as the link format.

Open a vault in Obsidian:

```bash
cd ~/Code/my-brain && brain open      # or: brain open ~/Code/my-brain
brain init ~/Code/my-brain --open     # scaffold and open in one step
```

The **first** time, register the vault via Obsidian's *Open folder as vault* (the
command prints the path); after that `brain open` opens it directly.

**Dual layer, on purpose:** Bases are for *you* in the GUI. Claude can't render them,
so it reads the plain-markdown index lists + frontmatter instead. Both stay in sync
because they're driven by the same frontmatter; `brain-upkeep` maintains the static
lists, Bases self-update.

## Automating upkeep (per brain)

So `brain-upkeep` runs without you remembering:

- **Weekly cron** — in Claude Code, schedule a job that opens the brain and runs
  `/brain-upkeep` (e.g. Monday 09:00). It refreshes the indexes and writes
  `meta/upkeep-report.md` for you to review.
- **SessionStart nudge** (optional) — add to the brain's `.claude/settings.json`:

  ```json
  {
    "hooks": {
      "SessionStart": [
        { "hooks": [ { "type": "command",
          "command": "grep -rl 'status: active' . | head -1 >/dev/null && echo 'brain: run /brain-upkeep to refresh indexes & flag stale notes'" } ] }
      ]
    }
  }
  ```

## Updating brains after changing the kit

The kit is the source of truth. To push skill/template updates into an existing
brain, re-run `brain init <existing-brain> --force --no-git` (overlays files; review
the git diff in the brain before committing). Your notes are never touched by this —
only `template/` and `skills/` content is overlaid.
