---
name: brain-import
description: Import a Google Doc into the brain vault using the Google Workspace CLI (gws). Use when the user gives a Google Doc link/ID to file, or references an ongoing doc (e.g. a running 1-1 doc with date headings) and wants the latest entry pulled in. Exports to markdown, optionally extracts only the newest dated entry, and returns it for filing.
---

# brain-import

Fetches a Google Doc as markdown via `gws` (Google Workspace CLI) and prepares it
for filing. **No content is sent out** — this only *pulls in*. Read `CLAUDE.md` for
where things get filed.

## Prerequisite
One-time: `gws auth login` (credentials stored encrypted in `~/.config/gws/`).
If `gws` is missing or unauthenticated, tell the user and offer the **paste
fallback**: they paste/export the doc text and you treat it as normal input.

## Get the file ID
From a URL like `https://docs.google.com/document/d/<FILE_ID>/edit`, extract `<FILE_ID>`.

## Export to markdown
`gws files export` writes the document to a **file** and prints only a JSON status
line to stdout. Pass the request as a JSON `--params` string and capture the bytes
with `-o`. The output path **must be inside the vault** — `gws` rejects paths outside
the current directory (so no `$TMPDIR`); use a dot-prefixed temp file in the vault
root and delete it after filing:
```
gws drive files export --params '{"fileId":"<FILE_ID>","mimeType":"text/markdown"}' -o .brain-import.tmp.md
# read .brain-import.tmp.md (NOT stdout), file it, then: rm -f .brain-import.tmp.md
```
- The markdown is in the `-o` file. stdout is a status object
  (`{"bytes":…,"mimeType":"text/markdown","saved_file":"…","status":"success"}`),
  not the document. A `Using keyring backend: keyring` line on stderr is harmless.
- Headings (`#`, `##`) are preserved (latest-entry mode still works), but Google's
  export escapes some characters (`\<`, `R\&D`, `\+`, `\#`, trailing `\` line-breaks)
  — normalize/tolerate these when filing.

## Modes

### Whole-doc
File under `resources/gdrive/<kebab-title>.md` using `meta/templates/resource.md`:
set `gdrive:` to the source URL, write a 2-4 line `## Summary`, cross-link related
people/projects, append the imported content below the divider.

### Latest-entry (ongoing docs, e.g. running 1-1 notes)
For a doc that accumulates dated sections under date headings (e.g. `## 2026-06-09`):
1. Parse the markdown headings that look like dates (`YYYY-MM-DD`, or month-name dates — normalize to `YYYY-MM-DD`).
2. Select the **latest** by default, or the specific date the user named.
3. Return only that section's content.
4. Hand off to **brain-capture** to file it as the matching note (e.g. a `one-on-one`).
5. Record the imported date in the person hub's `last_imported` frontmatter, so a
   re-run can pull only entries newer than `last_imported` ("what's new since last time").

## Output
Report: source doc, mode used, the date(s) extracted, and where it was filed.
Never duplicate an entry already imported (check `last_imported` / existing dated notes).
