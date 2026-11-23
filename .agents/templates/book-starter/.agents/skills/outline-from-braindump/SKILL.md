---
name: outline-from-braindump
description: "Turn the author's loose notes in context/raw-material/ into a chapter outline: ingest them as uncitable notes, cluster them into themes with booktools research themes, propose a TOC that fits the book's structure framework, and regenerate the chapter list and stubs after the author approves. Use when the author asks to cluster, organize or outline a brain dump."
---

# Skill: Outline from a Brain Dump

Brain Dump → Cluster → Outline is an **outlining method, not a structure**. Clustering shows what is in the notes;
the book's structure (`book-index.yaml` → `framework.structure`) decides order, and the chapter unit
(`.agents/rules/chapter-templates.md`) decides each chapter's sections.

## Inputs

| Input | Path |
|---|---|
| Notes (text, Markdown, transcripts) | `context/raw-material/` |
| Book index | `context/book-index.yaml` (`framework:`, `production.word_target`, `chapters:`) |
| Spec | `BOOK_SPEC.md` (Checkpoints 1, 4, 5) |
| Template rule | `.agents/rules/chapter-templates.md` |

Do not read the notes into context one by one. booktools embeds and clusters them.

## Steps

1. **Ingest the notes** (LM Studio running; see `/research-ingest` pre-flight):
   ```powershell
   python -m booktools research ingest --notes
   ```
   Notes are stored as uncitable. Re-running skips notes already ingested.
2. **Cluster:**
   ```powershell
   python -m booktools research themes --chapters 8..12
   ```
   Set the range around the chapter count in `BOOK_SPEC.md` Checkpoint 5 (±2). It writes `context/themes.md`:
   one `## Theme N: <name>` per cluster with suggested `###` sections and the notes in each, then
   `## Unplaced ideas` (notes that fit no theme).
3. **Read `context/themes.md`** (this file only). For each theme, note its likely chapter title, the promise it
   serves, and whether it is too thin (merge) or too broad (split).
4. **Propose the outline.** Order the themes using the structure's arc from `BOOK_SPEC.md` Checkpoint 4, e.g.
   problem before solution, foundations before advanced, earliest period first. Show the author a table:
   chapter number, title, source theme(s), template id, one-line purpose, word budget. Then list each
   **Unplaced idea** with a suggestion: fold into chapter N, a new chapter, front/back matter, or drop.
   Gaps the promise needs but no theme covers become chapters marked `[AUTHOR: no notes yet]`.
5. **Approve.** Revise until the author gives an explicit yes. Never regenerate chapters on a partial OK.
6. **Regenerate the chapter files.** Only chapters still at `status: stub` may be renamed, renumbered or removed.
   For a chapter already `drafted`, `edited` or `final`, ask before any change.
   - `context/book-index.yaml` → `chapters:`: one entry per chapter (format in the existing entries), budgets
     splitting `production.word_target` minus front and back matter; set `framework.outlining: braindump`.
   - `manuscript/NN-<slug>.md`: rename or create stubs with frontmatter (`chapter`, `title`, `slug`, `template`,
     `status: stub`, `word_budget`, `last_updated`), the H1 `# Title {.chapter}`, and the template H2s. No prose.
     Keep `00-front-matter.md`; renumber the back-matter file to the last number, keeping its Bibliography.
   - `context/raw-material/`: for each chapter, list the notes of its theme(s) in that chapter's
     `raw_material:` entry in `book-index.yaml` (paths only; don't copy or edit the notes).
   - `context/chapter-summaries.md`: one `_Status: stub. Not drafted._` block per chapter.
   - `.agents/rules/chapter-templates.md`: update the budget table to match.
   - `BOOK_SPEC.md` Checkpoint 5: replace the provisional TOC with the approved one, with the author's OK.
7. **Verify:** `python -m booktools stats` lists every file and the budgets total the word target within 1%;
   every chapter has a stub and a summary block.

## Automatic state updates

As in step 6. `context/themes.md` stays as the record of the clustering run.

## Handoff message to the author

The final TOC, how many notes went to each chapter, Unplaced ideas and what happened to each, then:
```powershell
git add context manuscript BOOK_SPEC.md; git commit -m "outline: chapters from brain dump"
```
Next: `/draft-chapter 01`.
