---
name: research-ingest
description: "Add sources from research/inbox/ and research/urls.txt (or the author's loose notes from context/raw-material/) to the book's local research library with booktools research ingest, then get the drafted citation metadata checked. Use when the author adds sources, asks to ingest or index research, or before the first chapter is drafted."
---

# Skill: Ingest Research

Builds and extends the book's local research library (`research/library.sqlite`, not in Git) using the models in
`research.models` (roles from the suite's `MODELS.md`), served by LM Studio at `research.endpoint`.

## Inputs

| Input | Path |
|---|---|
| Source files (PDF, EPUB, HTML, DOCX, Markdown, text) | `research/inbox/` (any subfolder) |
| Web pages | `research/urls.txt`, one URL per line |
| Author notes (uncitable) | `context/raw-material/` (only with `--notes`) |
| Config | `context/book-index.yaml` → `research:` |

Do not open the source files yourself. booktools reads them.

## Steps

1. **Pre-flight.** LM Studio running at `research.endpoint` with the embedding, extractor and judge models
   available (the role → model mapping is in the suite's `MODELS.md`). Then:
   ```powershell
   python -m booktools research status
   ```
   An **AGENT ACTION REQUIRED** block about `research.models` means a role is missing: fill it with a role name
   from `MODELS.md`, never a guessed model ID.
2. **Ingest sources.**
   ```powershell
   python -m booktools research ingest
   ```
   New URLs are fetched into `research/inbox/web/`. Each new source gets a citation key and a draft CSL entry in
   `research/sources.yaml` with `verified: false`. Already-ingested files are skipped. Use `--no-graph` to save
   time on a large batch (run `python -m booktools research graph` later).
3. **Ingest notes (brain-dump books only).**
   ```powershell
   python -m booktools research ingest --notes
   ```
   Notes are embedded for clustering and are **never citable**.
4. **Get the metadata checked.** Read only the new entries in `research/sources.yaml` and show the author a table:
   key, title, author(s), year, type, and anything that looks wrong (missing year, a site name as author, a
   generic title). The author checks each against the source (title page, copyright page, article header). Fix
   the `csl:` fields with them, then set `verified: true` on the entries they confirmed. Never mark an entry
   verified yourself.
5. **Report.** `python -m booktools research status` again: sources, chunks, notes, unverified count.

## Automatic state updates

None in `book-index.yaml`. `research/sources.yaml` and `research/urls.txt` are tracked in Git; the library and
`research/inbox/` contents follow the book's `.gitignore` (add `research/inbox/*` there if sources must not be
committed, e.g. copyrighted PDFs in a public repo).

## Handoff message to the author

New sources (key + title), how many are still unverified, failures (unreadable files, fetch errors), then:
```powershell
git add research/sources.yaml research/urls.txt; git commit -m "research: ingest N sources"
```
