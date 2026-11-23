---
name: build-bibliography
description: "Audit every [@key, p. N] citation in the manuscript against research/sources.yaml and the judge-approval log, then write research/references.json for pandoc's Chicago notes-bibliography footnotes and Bibliography. Use before any PDF or EPUB build of a manuscript with citations, or when the author asks to check citations."
---

# Skill: Build the Bibliography

Checks citations and writes `research/references.json` (CSL-JSON). The PDF and EPUB builds then run pandoc
citeproc with `research.citation_style`: each citation becomes a footnote, and the full list appears where the
back matter has its Bibliography section.

## Inputs

- `manuscript/*.md` (booktools reads them; you don't)
- `research/sources.yaml` (keys, `verified`, `csl`)
- `research/citations.jsonl` (judge-approved passages from `research query`)

## Steps

1. **Audit:**
   ```powershell
   python -m booktools research bibliography
   ```
   It lists every problem, by file and line:
   - `MISSING KEY`: the key isn't in `sources.yaml`. A typo, or a citation written from memory. Fix the key or
     remove the citation and mark `[AUTHOR: need source for …]`. This one fails the command (exit 2).
   - `UNVERIFIED`: the source's metadata hasn't been checked. Show the author the entry; they verify it and set
     `verified: true`.
   - `NOT JUDGE-APPROVED`: no approved `research query` passage for that key and page. Re-run the query for the
     claim (`/research-query`); if it doesn't approve that page, change the citation to what was approved or cut
     the claim.
2. **Before a final build,** run the strict check; it fails on any problem above:
   ```powershell
   python -m booktools research bibliography --strict
   ```
   `--all` includes every verified source in the Bibliography, not only cited ones (only if the author wants a
   full reading list).
3. **Check the back matter.** The last manuscript file must end with:
   ```markdown
   # Bibliography {.back-matter .unnumbered}

   ::: {#refs}
   :::
   ```
   If it's missing, add it (it's a heading and an empty div, not prose).
4. **Build** with `/compile-pod` or `/build-kindle`, then spot-check three footnotes and the Bibliography page in the
   output against `sources.yaml`.

## Automatic state updates

`research/references.json` is written by the tool. No `book-index.yaml` change.

## Handoff message to the author

Counts (citations, distinct sources), every remaining problem line, and whether the strict check passes. Then:
```powershell
git add research; git commit -m "research: bibliography check"
```
