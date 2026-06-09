---
name: brain-review
description: Produce a management or status digest from the brain vault. Use for "prep me for my 1-1 with <person>", "weekly review", "status of my projects", "what's overdue across my reports", or any roll-up across people/projects. Gathers and summarizes existing notes; does not invent.
---

# brain-review

Generates digests by gathering existing notes (uses the same retrieval protocol as
**brain-recall**). Read `CLAUDE.md` first. Two main modes:

## 1-1 prep — "prep me for my 1-1 with <person>"
Assemble:
- Person hub: role, **career plan**, **current WIP**, recent **wins**.
- The last 1-2 1-1 notes from `people/<name>/1-1s/` (newest by filename).
- **Open action items** from those 1-1s (unchecked `- [ ]`), flagged by owner.
- Linked projects' current state (follow `[[project]]` links).
Output: a short briefing + a suggested talking-points list. Cite notes with `[[links]]`.

## Weekly / status review
- Iterate `meta/indexes/projects-active.md`; for each active project summarize
  current state + next steps, and flag any not updated in 14+ days.
- Optionally roll up wins across reports for the week.

## Rules
- Summarize only what's in the vault; never fabricate progress or quotes.
- Keep it skimmable: headers, bullets, bold the items needing the user's action.
- End with concrete suggested actions, each linked to its source note.
