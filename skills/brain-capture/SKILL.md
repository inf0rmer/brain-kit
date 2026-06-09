---
name: brain-capture
description: File new input into the brain vault — a 1-1, a win, a project update, an idea, or a meeting note. Use when the user says "log/capture/note/add … to the brain", dictates a 1-1, records a win, or pastes content (including a Google Doc link) to be filed. Classifies the input, applies the right template + frontmatter, cross-links, and updates the index.
---

# brain-capture

Files raw input into the correct note in this vault. Read `CLAUDE.md` first for the
folder map, frontmatter schema, and the **Capture protocol** — this skill executes it.

## Steps

1. **If the input contains a Google Doc link**, invoke **brain-import** to fetch it
   first (use latest-dated-entry mode for ongoing 1-1 docs). Operate on the returned
   markdown. Otherwise operate on the text the user gave.

2. **Classify** into a `type` + target path:
   - 1-1 → `people/<name>/1-1s/<YYYY-MM-DD>.md` (`one-on-one`)
   - a win → add to the person hub `## Wins` (newest first); optionally a `win` note
   - project update → edit `projects/<name>.md` `## Current state` + decision log
   - new idea / concept dev → `ideas/<name>.md` (`idea`)
   - a person doesn't exist yet → create their hub from `meta/templates/person.md`
   - anything ambiguous → `00-inbox/<slug>.md` and tell the user it needs filing

3. **Resolve the entity.** Check `meta/indexes/people.md` (and aliases) before creating
   a new person/project — prefer editing an existing note over a duplicate.

4. **Apply the template** from `meta/templates/`, fill frontmatter: real fields +
   `created`/`updated` = today's date, `person`/`project` cross-links. **Set the
   fields that drive the Obsidian Bases**: `role` + `manager` on a person hub,
   `owner` on a project. Don't leave them blank when you know the value.

5. **Cross-link** related notes with `[[wikilinks]]` (kebab-case basenames).

6. **Update the matching index** (`meta/indexes/…`) if you created a new entity.

7. **Report** what you filed: the path, the type, links added, and an **"Open in
   Obsidian"** deep link (see `CLAUDE.md` → Obsidian integration for the
   `obsidian://open?vault=…&file=…` format). Do not commit unless asked.

## Notes
- Dates are `YYYY-MM-DD`. Use the real current date.
- Never overwrite an existing 1-1 for the same date without confirming.
