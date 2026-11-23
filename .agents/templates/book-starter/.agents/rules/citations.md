---
trigger: glob
globs: "manuscript/*.md"
description: "How to cite sources in the manuscript: only judge-approved passages from booktools research query, as [@key, p. N]. Never cite from memory."
---

# Rule: Citations

Style: `context/book-index.yaml` → `research.citation_style` (default Chicago notes-bibliography). Pandoc turns
each citation into a footnote and builds the Bibliography at the end of the back matter.

## The only legal source of a citation

1. Run `python -m booktools research query "<question>" --chapter NN` (see `/research-query`).
2. Cite only passages it lists as approved: verified quote, judge score ≥ `research.judge_min_score`.
   The query logs each approved passage to `research/citations.jsonl`; `research bibliography` checks every
   citation in the manuscript against that log.
3. Copy the citation exactly as printed: `[@key, p. N]`. Use the page the tool gives, not a page you remember.

**Never cite from memory**, from a model's general knowledge, or from a source that is not in the library. If no
passage passes the judge, write the claim without a citation only if it is common knowledge; otherwise mark it:
`[AUTHOR: need source for <claim>]`.

## Syntax (pandoc)

| Use | Write |
|---|---|
| One source | `… the effect disappeared [@smith2024habits, p. 12].` |
| Page range | `[@smith2024habits, pp. 12-14]` |
| Two sources | `[@smith2024habits, p. 12; @lee2023focus, p. 7]` |
| Author in the sentence | `Smith argues … [-@smith2024habits, p. 12].` |

- The citation goes before the sentence's final punctuation.
- Web sources without pages: `[@key]` only if the query returned the passage with no page.
- Never write footnotes by hand for sources; pandoc generates them. Short explanatory footnotes (`^[…]`) are
  allowed, one sentence each.

## Quotes

- A direct quote must match the approved passage's `quote` **word for word**. Shorten only with `…`, and never
  change meaning.
- Quotes over ~40 words become a block quote (`>`), with the citation after the closing punctuation.
- Paraphrase by default; quote only when the exact wording matters.

## Notes are not sources

Files in `context/raw-material/` (ingested with `--notes`) are the author's notes. They organize the book; they are
never cited. The author's own experience is told in the author's voice without a citation.

## Before handing off a chapter

- Every `[@key, …]` in the file came from a `research query` run for this chapter.
- No claim with a number, date, study, or quotation is left without a citation or an `[AUTHOR: need source …]`.
- `python -m booktools research bibliography` reports no MISSING KEY or NOT JUDGE-APPROVED line for this file.
