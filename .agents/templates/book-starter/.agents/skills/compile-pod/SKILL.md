---
name: compile-pod
description: "Build the print-on-demand interior PDFs for each printer in production.print_targets with booktools, check page and word counts against the page ceiling, and record the build. Use when the author asks for a print build, page count, or POD PDF."
---

# Skill: Compile Print-on-Demand Interior PDFs

Builds the print interior for each configured printer. Trim, ink, paper, page ceiling and every printer measurement
come from `context/book-index.yaml` → `production:`. Nothing is assumed.

## Prerequisites (one-time)

| Tool | Install | Check |
|---|---|---|
| booktools | `python -m pip install -e ../book-tools` | `python -m booktools doctor` |
| Pandoc 3.x | Windows: `winget install JohnMacFarlane.Pandoc`; macOS: `brew install pandoc` | (doctor) |
| WeasyPrint + Pango | pip (with booktools) + Pango (see the booktools README for your OS) | (doctor) |
| Git | `winget install Git.Git` or your package manager | (doctor) |

`python -m booktools doctor` checks all of these; every command also checks up front and prints the exact fix.

## Specs gate

If a command prints **AGENT ACTION REQUIRED**, values are missing. A new book starts with every printer value
`null` on purpose.
1. Confirm trim, ink, paper and the printers with the author if `BOOK_SPEC.md` doesn't settle them.
2. Look up each missing value in the printer's **current** documentation for that exact trim + ink + paper
   (for example the KDP help pages on trim, bleed and margins; the IngramSpark File Creation Guide).
3. Show the author each value and the page it came from. Write them under `production.print_targets.<printer>`
   only after they confirm, with `specs_source` (the pages) and `specs_verified: YYYY-MM-DD`. Re-run.

A `VERIFY:` line means the values exist but are unconfirmed or older than 180 days: re-check before any upload.

## Inputs

- `manuscript/*.md` in numeric order (front matter first, back matter with the Bibliography last)
- Stylesheet, trim and margins from `production`; book metadata from `book:`
- `research/references.json` when the manuscript has citations

The agent does **not** read manuscript files for this skill. booktools does.

## Steps

1. **Pre-flight counts** (fast, no pandoc):
   ```powershell
   python -m booktools stats
   ```
   Per-file words vs budget and the book total vs `production.word_target`.
2. **Citations** (if any chapter has `[@…]`): run `/build-bibliography` first. A build with citations and no
   `research/references.json` stops with exit 2.
3. **Build each printer** in `production.print_targets`:
   ```powershell
   python -m booktools pdf --target <printer>
   ```
   → `dist/<slug>-<printer>.pdf`. For strict-black presses, `--pdf-variant pdf/x-1a` if WeasyPrint supports it.
4. **Read the report.** booktools prints the exact page count, word count and the ceiling check. Exit `3` = over
   `production.page_ceiling`.
5. **Visual QA** (open the PDF):
   - Chapter openers on right-hand (odd) pages; running heads correct on left and right.
   - No prompt/code box or table split across pages; images inside the margins.
   - Inside margin (gutter) is the wider margin on every page.
   - TOC page numbers match chapter starts; footnotes on the page of their citation; Bibliography present.
6. **Recalibrate** the word target if this is the first full build: words per page =
   `last_word_count / last_page_count`. Tell the author if `production.word_target` no longer fits the ceiling.

## Flags

| Flag | Effect |
|---|---|
| `--target NAME` | Required. Any key under `production.print_targets`. |
| `--keep-html` | Keeps `dist/_build/<target>.html` for CSS debugging. |
| `--no-snapshot` | Skip the Git snapshot commit. |
| `--pdf-variant X` | Passed to WeasyPrint (e.g. `pdf/x-1a`, `pdf/a-3b`). |

## Automatic state updates

1. **Git snapshot.** After a successful build booktools commits the working tree (plus `build-manifest.json`) to
   the `builds` branch with a temporary index. Your branch, staging area and files are untouched.
2. **`context/book-index.yaml`** (booktools prints the values):
   ```yaml
   build:
     last_target: <printer>
     last_page_count: 0
     last_word_count: 0
     last_build: YYYY-MM-DD
   ```

## If over the page ceiling

Do not shrink the font or margins to cheat: printers' reviewers and readers both notice. Run
`python -m booktools stats`, find chapters over budget, and propose cuts to the author (the longest hands-on or
example sections are usually the fat). Then re-run.

## Next

Run `/cover-studio`; it reads the page count from this PDF.
