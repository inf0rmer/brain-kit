---
name: brain-publish
description: Publish a single brain note to Google Docs to share with others. Use when the user says "publish / share / export <note> to Google Docs/Drive", or wants a shareable doc from something in the brain. Creates a clean (frontmatter-stripped) Google Doc in the user's own Drive via gws, links it back into the source note, and leaves sharing untouched.
---

# brain-publish

The **one sanctioned outbound path** for this vault. Everything else is import-only;
this skill sends content *out* — so treat it carefully. Read `CLAUDE.md` first.

Hard rules:
- **One named note per run.** Never publish a folder, multiple notes, or "everything".
- **Confirm before sending** (see step 4). Publishing creates a shareable artifact.
- **Never change sharing/permissions.** The Doc is created in the user's own Drive,
  visible only to them. They share it in Drive themselves.

## Steps

1. **Identify the single source note** the user named. If ambiguous, ask — don't guess.

2. **Sensitivity gate.** Read the note's `type` and `tags`. If `type` is
   `one-on-one`, `person`, or `win`, **or** `tags` includes any of
   `report`, `confidential`, `performance`, `career` → this is sensitive.
   **Refuse** and explain why, unless the user's request includes an explicit override
   (e.g. "publish anyway", "override the sensitivity gate", "yes, publish this
   sensitive note"). Even with an override, still do step 4.

3. **Make a clean copy** (a temp file in the vault root — `gws` won't accept paths
   outside cwd — named `.brain-publish.tmp.md`, deleted in step 6):
   - Strip the YAML frontmatter block entirely.
   - Convert internal `[[wikilinks]]` to their plain display text (external readers
     can't follow them); drop `![[*.base]]` embeds.
   - Remove internal-only lines: "Open in Obsidian" `obsidian://` links, the
     `<set-by-brain-init>` vault line if present, and any clearly private asides.
   - Keep the title, headings, and prose. Use the note's H1 (or filename) as the
     Doc title.

4. **Preview + confirm.** Show the user: the Doc **title**, the **destination** (their
   Drive root, or a folder ID if they named one), and a short **content preview**.
   Get an explicit go-ahead before the next step.

5. **Create the Doc** (Google converts the markdown to a real Doc):
   ```
   gws drive files create \
     --json '{"name":"<TITLE>","mimeType":"application/vnd.google-apps.document"}' \
     --upload .brain-publish.tmp.md --upload-content-type text/markdown
   ```
   To put it in a folder, add `"parents":["<FOLDER_ID>"]` to the `--json` body.
   The command returns JSON for the new file; read its `id`. The shareable link is
   `https://docs.google.com/document/d/<id>/edit`.
   (A `Using keyring backend: keyring` line on stderr is harmless.)

6. **Link back + clean up.** Add a short line near the top of the **source note**
   recording the publish, e.g.:
   `> 📤 Published to Google Docs (<YYYY-MM-DD>): <doc-url>`
   Then `rm -f .brain-publish.tmp.md`.

7. **Report**: the Doc URL, that it's **private to you** (not shared), and that you
   set sharing in Drive when ready. Do not commit unless asked.

## Prerequisite
`gws auth login` (same as `brain-import`). If `gws` is missing/unauthenticated, say so
and stop — there is no offline fallback for publishing.
