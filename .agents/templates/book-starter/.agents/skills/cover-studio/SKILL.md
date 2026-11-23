---
name: cover-studio
description: "Calculate spine width and full-wrap paperback cover dimensions from the built interior PDF with booktools, write SVG cover templates, and handle missing or unverified printer specs. Use when the author asks about the cover, spine, or cover size."
---

# Skill: Cover Studio (Full-Wrap Paperback Cover)

Calculates spine width and full-wrap dimensions from the compiled interior, then writes SVG wrap templates to design
on. Every measurement comes from `context/book-index.yaml` → `production:`. Nothing is assumed.

## Inputs

- A compiled interior PDF in `dist/` (run `/compile-pod` first). The page count comes from it. Before the interior
  is final, use `--pages N` for an estimate only.
- `production.trim` and, for each printer under `production.print_targets`: `bleed_in`, `spine_in_per_page`,
  `safe_in`, `spine_safe_in`, `barcode_in`, `min_pages`, and optionally `spine_text_min_pages` /
  `spine_text_min_in`, `even_pages`.

## Formulas

```
spine        = pages × spine_in_per_page        (per printer, per paper + ink)
cover_width  = bleed + trim_width + spine + trim_width + bleed
cover_height = bleed + trim_height + bleed
```
With `even_pages: true` (the default), odd page counts are rounded up first.

## Steps

1. **Calculate:**
   ```powershell
   python -m booktools spine
   ```
   Reads `dist/<slug>-<target>.pdf`. Options: `--pdf FILE`, `--pages N` (estimate), `--target NAME`
   (repeatable; default = every configured printer), `--no-svg`.
2. **If it prints AGENT ACTION REQUIRED:** a spec is missing. Do not guess.
   - Confirm trim, ink, paper and printers with the author if unknown.
   - Look up current values for that exact combination in the printer's own tools and guides (for example the
     KDP cover calculator and trim/bleed help pages; the IngramSpark File Creation Guide and cover template
     generator).
   - Show the author each value and its source. After they confirm, write them under
     `production.print_targets.<printer>` with `specs_source` and `specs_verified: YYYY-MM-DD`, then re-run.
3. **If it prints VERIFY:** values exist but are unconfirmed or older than 180 days. Check them now and set
   `specs_verified`. Do not send a cover to print with this warning showing.
4. **Read the report:** spine width and full wrap in inches, mm and px at 300 dpi per printer, plus whether spine
   text is allowed.
5. **Templates written:** `assets/cover/cover-wrap-<target>.svg`. Each shows bleed (red), trim and folds (black),
   safe zones (blue dashed) and the barcode keep-out (orange). Guides live in the `<g id="guides">` layer: hide or
   delete it before export.
6. **Design** in Inkscape, Affinity or Illustrator on a layer under `guides`. Extend background art to the bleed.
7. **Export** per the printer's current cover spec (format, color space, ink limit, PDF/X).
8. **Cross-check** each wrap against the printer's official template for the same page count. If they differ, the
   printer is right: fix `spine_in_per_page` (official spine width ÷ pages) with the author's OK and re-run.

## E-book cover (separate)

Save it as `assets/cover/ebook-cover.jpg` (or set `production.epub.cover`). `python -m booktools epub` picks it
up. Crop it from the front panel only (no spine, no bleed), at the store's current recommended pixel size.

## Automatic state updates

Copy the `cover:` lines booktools prints into `context/book-index.yaml`, one key per printer:
```yaml
cover:
  last_calculated: YYYY-MM-DD
  <printer>: {pages: 0, spine_in: 0.0, wrap_in: [0.0, 0.0]}
```

Re-run every time the interior page count changes. A few pages move the spine enough to put spine text off-center.
