# Brain — Operating Manual

This repository is a personal "second brain": an Obsidian vault of markdown notes
plus a set of Claude Code skills. **You (the agent) are the primary interface.**
Read this file before answering any question or filing any note — it defines the
conventions that make retrieval and capture reliable.

There is **no embeddings index and no vector database** by design. Retrieval is
*agentic*: you navigate structure, read index notes, filter frontmatter, grep, and
synthesize. The conventions below are what make that fast and accurate, so honor
them exactly.

**Privacy:** This vault holds sensitive management data (reports' career plans,
performance, 1-1s). It is **local-only**. Do not send note contents to any external
service **except** via the explicit, user-initiated **brain-publish** skill, which
publishes a single named note to the user's own Google Docs with a sensitivity gate
and a confirm step. `gws` is otherwise import-only (brain-import). No other path —
and no bulk or automatic export — ever sends content out.

**Obsidian vault name:** `<set-by-brain-init>` (used to build `obsidian://` deep links).

---

## Folder map

| Path | Holds | Note `type` |
|------|-------|-------------|
| `00-inbox/` | Raw, unprocessed capture awaiting filing | (any) |
| `people/<name>/<name>.md` | Person **hub**: role, career plan, current WIP, wins log | `person` |
| `people/<name>/1-1s/<YYYY-MM-DD>.md` | One note per 1-1 | `one-on-one` |
| `projects/<name>.md` | A project: status, links, decision log | `project` |
| `ideas/<name>.md` | Ideation / concept development | `idea` |
| `resources/gdrive/<name>.md` | Imported/referenced Google Docs | `resource` |
| `meta/templates/` | Note templates (one per type) | — |
| `meta/indexes/` | MOC dashboards (entry points for retrieval) | — |
| `meta/upkeep-report.md` | Latest `brain-upkeep` findings | — |

`<name>` is **kebab-case** (`jane-doe`, `acme-migration`). Wikilinks resolve by
basename, so `[[jane-doe]]` points at `people/jane-doe/jane-doe.md`.

Projects are **flat** — keep separate concerns (e.g. work vs personal) in **separate
brains/vaults**, not subfolders. If you must mix them in one brain, distinguish with a
`tags:` entry (e.g. `tags: [side-project]`) and filter the Projects base on it.

---

## Frontmatter schema (the retrieval substrate)

Every note starts with YAML frontmatter. Filtering relies on it being consistent.

```yaml
---
type: person | one-on-one | project | idea | resource | win
status: active | parked | done        # projects, ideas
role: Senior Engineer                   # person — shown in the People base
manager: Bruno Abrantes                 # person
owner: "[[jane-doe]]"                   # project — shown in the Projects base
person: "[[jane-doe]]"                  # 1-1s, wins, mgmt notes
project: "[[acme-migration]]"           # when tied to a project
tags: [report, career, q3]
created: 2026-06-09
updated: 2026-06-09
gdrive: https://docs.google.com/...     # resources / imported notes
last_imported: 2026-06-09               # person hub: newest 1-1 entry pulled from a source doc
aliases: []
---
```

Required by type (extra fields populate the Obsidian Bases — keep them set):
- **person** — `type, role, manager, created, updated`; body has `## Career plan`, `## Current WIP`, `## Wins` sections.
- **one-on-one** — `type, person, created, updated`.
- **project** — `type, status, owner, created, updated`.
- **idea** — `type, status, created, updated`.
- **resource** — `type, gdrive, created, updated`.

Always set `updated` to today (`2026-06-09` is "today" at time of writing; use the
real current date) when you modify a note.

---

## Index notes (`meta/indexes/`)

`people.md`, `projects-active.md`, `ideas.md` link to every entity. **Start
retrieval here**, not with a blind grep — read the relevant index to find the right
note, then drill in. `brain-upkeep` keeps these current; if you add an entity,
add its link to the matching index in the same change.

Each index also embeds a live **Base** (`![[*.base]]`) for the human GUI. That embed
is for Obsidian only — **you read the static link lists below it**, not the Base.

---

## Obsidian integration

The vault is designed to work great in Obsidian (1.9+) without changing how you
retrieve. Three things to know:

- **Bases (`meta/bases/*.base`)** are live dashboards rendered by Obsidian from
  frontmatter — *dual layer*: humans get the Base, you get the markdown index +
  frontmatter. **`.base` files are config, not notes** — ignore them during
  retrieval and upkeep (never cite, link, or flag them as orphans).
- **Deep links.** When you file or cite a note, add a one-line "Open in Obsidian"
  link so the user can jump to it:
  `obsidian://open?vault=<VAULT>&file=<url-encoded-relative-path>`
  where `<VAULT>` is the vault name recorded near the top of this file and the path
  is the note's vault-relative path with spaces as `%20` (e.g.
  `obsidian://open?vault=my-brain&file=people%2Fjane-doe%2Fjane-doe.md`).
- **Templates.** Obsidian's core Templates plugin points at `meta/templates/` and
  only fills `{{date}}`/`{{title}}`; **you fill the rest** (`role`, `person`,
  `owner`, `gdrive`, …) during capture.

---

## Retrieval protocol (follow in order)

1. **Resolve the entity.** Map the query to a person / project / idea. Check
   `aliases` in frontmatter and the index notes for name variants.
2. **Read the index** (`meta/indexes/…`) to locate candidate notes.
3. **Filter by frontmatter** (`type`, `status`, `person`, `tags`, dates) to narrow.
4. **Grep** the vault for keywords the structure didn't capture.
5. **Read** the top candidate notes in full.
6. **Synthesize with citations.** Answer concisely and cite every claim with the
   source note as a `[[wikilink]]`. If the vault doesn't contain the answer, say so
   — never invent.

## Capture protocol

1. Classify the input into a `type` and target folder.
2. Apply the matching template from `meta/templates/`, fill frontmatter (incl.
   `created`/`updated` = today).
3. Cross-link: set `person`/`project`, and add `[[links]]` to related notes.
4. Update the relevant index note.
5. When the input is a Google Doc link, hand it to `brain-import` first (see below),
   then file the returned markdown.

---

## Skills

- **brain-capture** — file new input (text or a Google Doc link) into the right note.
- **brain-recall** — answer questions via the retrieval protocol, with citations.
- **brain-review** — management/status digests (e.g. "prep me for my 1-1 with Jane").
- **brain-import** — pull a Google Doc via `gws` → markdown; supports latest-dated-entry mode.
- **brain-publish** — publish one note to the user's Google Docs (clean copy, sensitivity-gated, confirm-first, links back). The only outbound path.
- **brain-upkeep** — hygiene + index refresh; runs on a weekly cron, writes `meta/upkeep-report.md`.

## Conventions

- Prefer editing an existing note over creating a duplicate; check the index first.
- Keep notes atomic and link liberally with `[[wikilinks]]`.
- Never auto-delete or auto-merge during upkeep — only propose, in the report.
- All dates are `YYYY-MM-DD`.
