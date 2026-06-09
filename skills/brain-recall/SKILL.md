---
name: brain-recall
description: Answer a question from the brain vault — about a report, project, idea, past 1-1, decision, or win. Use when the user asks "what / when / who / remind me / what did we say about …" anything that should be answered from stored notes. Navigates structure, filters frontmatter, greps, and answers with [[citations]]. This is the agentic substitute for semantic search.
---

# brain-recall

Answers questions from the vault. There is no embeddings index — retrieval is
agentic. Read `CLAUDE.md` and follow its **Retrieval protocol** exactly.

## Steps

1. **Resolve the entity** in the question to a person/project/idea. Check `aliases`
   in frontmatter and `meta/indexes/` for name variants.
2. **Read the relevant index** (`meta/indexes/people.md`, `projects-active.md`,
   `ideas.md`) to find candidate notes — start here, not with a blind grep. Read the
   static link lists; **ignore the embedded `![[*.base]]` Base** (that's the Obsidian
   GUI view, not data for you).
3. **Filter by frontmatter**: `type`, `status`, `person`, `tags`, and date ranges
   (e.g. 1-1s in the last month → list files in `1-1s/` by date).
4. **Grep** for keywords the structure didn't capture.
5. **Read** the top candidates in full before answering.
6. **Synthesize**: answer concisely; cite every claim with a `[[wikilink]]` to its
   source note, and add an **"Open in Obsidian"** deep link for the key note(s) (see
   `CLAUDE.md` → Obsidian integration). If the answer isn't in the vault, say so
   plainly — never invent.

## Tips
- For "across all reports" questions, iterate the people index rather than one note.
- For "latest" questions, sort by the `updated` field or the dated filename.
- `.base` files are config, never sources — never read or cite them.
- Quote sparingly; link generously.
