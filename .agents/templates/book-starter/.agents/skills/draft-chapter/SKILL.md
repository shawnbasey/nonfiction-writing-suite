---
name: draft-chapter
description: "Draft one book chapter from the author's raw material in context/raw-material/, using the book's voice and chapter template, with every factual claim checked against the research library via booktools research query first; then update book-index.yaml and chapter-summaries.md. Use when the author asks to draft, write, or rewrite chapter NN."
---

# Skill: Draft a Chapter

Turns the author's raw material for one chapter into one chapter file. One chapter per run.

## Inputs

| Input | Path | Required |
|---|---|---|
| Chapter number `NN` | given by the author (e.g. `04`) | yes |
| Raw material | the chapter's `raw_material:` files in `book-index.yaml`, else `context/raw-material/NN-*.md` (or `.txt`) | yes |
| Book index | `context/book-index.yaml` | yes |
| Running summaries | `context/chapter-summaries.md` | yes |
| Themes (brain-dump books) | `context/themes.md`, this chapter's cluster only | if it exists |
| Voice rule | `.agents/rules/voice.md` | yes |
| Template rule | `.agents/rules/chapter-templates.md` | yes |
| Citation rule | `.agents/rules/citations.md` | yes |
| Target stub | `manuscript/NN-*.md` | yes (overwrite body only) |

## Context budget (hard rule)

Load **only** the files above. Never open other `manuscript/*.md` files, `blog-exports/`, `dist/`, or files in
`research/inbox/`. Cross-chapter knowledge comes from `chapter-summaries.md`; evidence comes from
`booktools research query`. If a summary is missing detail you need, ask the author.

Audio files are never read. The author supplies text transcripts.

## Steps

1. **Locate.** Find the chapter in `book-index.yaml` → `file`, `template`, `word_budget`, `status`. Refuse if
   `status` is `edited` or `final` unless the author explicitly says to overwrite in this conversation.
   If `voice.md` or `chapter-templates.md` still shows its "Generated file" header, stop and tell the author.
2. **Mine the raw material.** List (in scratch reasoning, not the file): the core claim, every concrete story and
   example, every opinion, every factual claim that will need a source. Keep the author's examples; they are the
   gold.
3. **Map to template.** Assign each mined item to the template's H2 sections. Note gaps.
4. **Research before drafting factual claims.** For each claim that needs support (numbers, dates, studies,
   history, quotations, anything a reader could check), and for each gap from step 3 that evidence could fill:
   ```powershell
   python -m booktools research query "<the claim, phrased as a question>" --chapter NN
   ```
   Keep the approved passages (citation, quote, page). Batch related claims into one question where you can;
   aim for under ~15 queries per chapter. No approved passage → the claim is cut, softened to what the author's
   own material supports, or marked `[AUTHOR: need source for …]`. If LM Studio isn't reachable, stop and tell the
   author rather than drafting factual claims without evidence.
5. **Check continuity.** From `chapter-summaries.md`, note terms and models already defined (reuse their exact
   names) and any promises earlier chapters made about this one.
6. **Draft.** Write the body under the existing H1/H2s in `manuscript/NN-*.md`.
   - Keep the YAML frontmatter; set `status: drafted` and `last_updated`.
   - Hit each section's budget within the tolerance in `chapter-templates.md`.
   - Cite per `citations.md`: `[@key, p. N]` exactly as the query printed it.
   - Gaps: `[AUTHOR: need a real example of X]`. Never fabricate statistics, studies, quotes or stories.
7. **Voice pass.** Run the self-check in `voice.md`. Search the file for every banlist term, e.g.:
   ```powershell
   Select-String -Path manuscript\NN-*.md -Pattern 'delve|tapestry|testament|game-changer|in conclusion' -CaseSensitive:$false
   ```
   Extend the pattern with the book's own banlist. Zero hits required.
8. **Check citations and count.**
   ```powershell
   python -m booktools research bibliography
   python -m booktools stats
   ```
   No MISSING KEY or NOT JUDGE-APPROVED line for this chapter. The chapter is within tolerance of its budget and the
   book total is still within `production.word_target`.

## Automatic state updates (every run)

**`context/book-index.yaml`**, this chapter only:
```yaml
    status: drafted
    word_count: 3087          # from `python -m booktools stats`
    raw_material: [context/raw-material/04-....md]
    last_updated: YYYY-MM-DD
```
Also update `totals.drafted_words` and `totals.chapters_drafted`.

**`context/chapter-summaries.md`**: replace this chapter's block with the block format at the top of that file
(core claim, key model or idea, terms, sources cited, promises, author TODOs). Max 120 words.

## Handoff message to the author

Report: word count vs budget, number of citations and distinct sources, number of `[AUTHOR:]` notes, any
continuity conflicts, unverified sources the bibliography check listed, and the next command:
```powershell
git add manuscript context research; git commit -m "draft: chapter NN"
```

When the author finishes editing, they set `status: edited` in the chapter frontmatter and `book-index.yaml`.
