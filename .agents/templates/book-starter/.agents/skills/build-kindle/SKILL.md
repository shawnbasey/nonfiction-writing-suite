---
name: build-kindle
description: "Build the reflowable EPUB3 (Kindle, Apple Books, Kobo) with booktools and check it. Use when the author asks for the ebook, EPUB, or Kindle file."
---

# Skill: Build the Reflowable EPUB3 (Kindle / Apple / Kobo)

## Prerequisites

- Pandoc 3.x on PATH (`python -m booktools doctor` checks it).
- Optional: `epubcheck` on PATH (Java). booktools runs it automatically if found.
- Optional: an e-book cover at `assets/cover/ebook-cover.jpg` (or `.png`, or `production.epub.cover`), RGB, at
  the store's current recommended pixel size. Picked up automatically.

WeasyPrint is **not** needed for EPUB.

## Inputs

- `manuscript/*.md` in numeric order
- `kindle.css` (booktools default, or `production.epub.css`)
- `context/book-index.yaml` → `book:` metadata (title, subtitle, author, language, rights)
- `research/references.json` when the manuscript has citations

As with the PDF build, the agent never loads the manuscript; booktools does.

## Steps

1. **Citations** (if any chapter has `[@…]`): run `/build-bibliography` first.
2. **Build:**
   ```powershell
   python -m booktools epub
   ```
   → `dist/<slug>.epub`
3. **Read the report:** chapter count, word count, file size, epubcheck result.
4. **Preview** in Kindle Previewer (free from Amazon) or another EPUB reader and check:
   - The TOC lists the chapters and back matter (front matter is hidden from the TOC on purpose).
   - Prompt/code blocks wrap on a phone-width screen; no horizontal scrolling.
   - The print-only title page does not appear (pandoc generates the e-book title page).
   - Notes link to their text and back; the Bibliography is present.
   - Images keep their contrast on e-ink (grayscale-safe).
5. **Upload:** KDP and most stores accept the `.epub` directly. Check each store's current requirements before
   uploading.

## Flags

| Flag | Effect |
|---|---|
| `--cover PATH` | Override the cover image path. |
| `--no-epubcheck` | Skip epubcheck even if installed. |
| `--snapshot` | Also commit a snapshot to the `builds` branch (off by default). |

## What the CSS does (booktools `kindle.css`)

- Relative units only (`em`, `%`), so readers' font settings work.
- Code and prompt blocks use `white-space: pre-wrap` + `overflow-wrap: anywhere`, so long lines reflow.
- No fixed page geometry, no running heads, no colors beyond black/gray borders.
- Hides `.print-only` elements.

## Automatic state updates

**`context/book-index.yaml`**:
```yaml
build:
  last_epub: YYYY-MM-DD
  last_epub_size_kb: 0
```

## Common failures

| Symptom | Fix |
|---|---|
| `Unknown option --split-level` | Pandoc < 3.0. booktools falls back to `--epub-chapter-level`; upgrade pandoc anyway. |
| Image missing in EPUB | Image paths must be relative to `manuscript/` (e.g. `../assets/diagrams/x.svg`). |
| epubcheck errors on SVG | Export the diagram as PNG or simplify the SVG (no scripts, no external fonts). |
| Exit 2 about references | The manuscript cites sources but `research/references.json` is missing: run `/build-bibliography`. |
| Tiny code text on Kindle | Don't set `font-size` below `0.85em` in a custom `kindle.css`. |
