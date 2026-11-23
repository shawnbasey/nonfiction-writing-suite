# 5-Stage New-Book Interview

Ask at most 2-3 questions per turn, each with a **(Recommended)** default the user can accept with "yes" or "go".
Write each answer into `_drafts/<slug>/BOOK_SPEC.md` as soon as you have it. The stage list below names the
checkpoint each answer fills (`.agents/templates/book-starter/BOOK_SPEC.md` has the 8 checkpoints).

Before stage 1, ask one opening question: "Tell me the book in two or three sentences, however rough." Use the
answer to propose defaults for the questions that follow. Never invent facts about the author; if a default would
need one, ask instead.

## Stage 1: Topic, promise, reader, outcome (Checkpoints 1-2)

- **Working title, subtitle, slug.** Recommended: a title you propose from the opening answer, a subtitle that
  states the promise, and a lowercase-hyphen slug of 2-4 words.
- **Promise.** One sentence: "After this book, the reader can …". Recommended: the strongest concrete outcome in
  the opening answer.
- **Reader.** Who, what they already know, the situation they are in when they pick it up. Recommended: the
  narrowest reader the opening answer supports (a narrow reader makes every later choice easier).
- **Outcome evidence.** How the reader will know it worked (a skill, a decision, a finished artifact).

## Stage 2: Author voice and raw material (Checkpoint 3)

- **Author and role.** Name as it should appear on the cover, publisher name, and the author's standing to write
  this ("who is talking"). Recommended publisher: the author's own name or imprint.
- **Voice.** Ask for 1-3 samples (a past post, an email, a transcript) or five adjectives plus one sentence the
  author would never write. Recommended: build `voice.md` from samples, because adjectives alone produce generic
  prose. Ask for any words or constructions the author hates; they go on the banlist.
- **Raw material.** What exists already: notes, voice memos, transcripts, slides, a blog, a half-draft.
  Recommended per case:
  - **Organized by chapter already** → put one file per chapter in `context/raw-material/NN-*.md`.
  - **Loose pile of notes** → offer **brain-dump clustering**: after the book exists, the notes go in
    `context/raw-material/`, `python -m booktools research ingest --notes` embeds them as uncitable notes, and
    `python -m booktools research themes --chapters 8..12` clusters them into `context/themes.md` with an
    Unplaced ideas list. The outline is then built from the clusters (`/outline-from-braindump`). Record
    `braindump: true` (Recommended when there are more than ~30 loose notes or no chapter order yet).
  - **Nothing yet** → plan to record one voice rant or note file per chapter before drafting it.
- Audio is never read by agents. Transcripts only.

## Stage 3: Framework (Checkpoints 4-5)

Load `references/frameworks.md`. From stages 1-2, propose **2-3 combinations**, each as:

```
Option A (Recommended): <Structure> + <Chapter unit> [+ <Layer>]
Why: one sentence tied to the promise and reader.
Sample TOC: 8-12 chapter titles in order, plus front and back matter.
Chapter shape: the H2 list of the chapter template.
```

- Explain the three layers once: the **structure** orders the chapters; the **chapter unit** is the H2 template
  every chapter repeats; an optional **story/evidence layer** adds a required element to each chapter.
- If `braindump: true`, say the TOC is provisional until clustering runs; pick the structure now anyway.
- A book may use two chapter templates (for example a concept template and a hands-on template). Ask which
  chapters use which.
- Then ask the user to pick an option or adjust the TOC. Write the chosen combination to Checkpoint 4 and the
  agreed TOC to Checkpoint 5.

## Stage 4: Production (Checkpoint 6, and Checkpoint 8)

- **Formats.** Recommended: paperback (print-on-demand) plus reflowable EPUB.
- **Trim size** (author's choice, not a printer spec). Recommended: 6" x 9" for most nonfiction; 5.5" x 8.5" for
  short narrative; 7" x 10" or 8.5" x 11" for workbooks with tables.
- **Page ceiling and word target.** Recommended: ask for a page ceiling, then estimate the word target from it
  (`references/harness-generation.md` §5) and say it is an estimate that the first PDF build recalibrates.
- **Printers.** Recommended: Amazon KDP and IngramSpark. Interior ink and paper (black-and-white on white is the
  common default). Say plainly: printer measurements stay empty until the first build asks for them, then get
  looked up and confirmed with you.
- **Blog repurposing.** Ask if chapters will become posts, and the site URL. Recommended: no, unless the author
  already runs a blog. Record `repurpose_blogs: true|false` and the URL in Checkpoint 8.
- **Citation style.** Recommended: Chicago notes-bibliography (`chicago-notes-bibliography`), footnotes plus a
  back-matter Bibliography. Alternatives need a `.csl` file in the book.

## Stage 5: Research corpus seeds (Checkpoint 7)

- **Key sources.** Books, papers, reports and sites the author trusts for this topic. Files go in
  `research/inbox/` (PDF, EPUB, HTML, Markdown, text); URLs go one per line in `research/urls.txt`.
  Recommended: start with 5-15 sources the author already relies on; add more per chapter while drafting.
- **Gaps.** Claims the book must make that need evidence the author doesn't have yet. These become
  `[AUTHOR: need source for …]` targets, never invented citations.
- **Off-limits sources** (paywalled material the author has no copy of, sources they distrust).

## Finish

Read the complete spec back (all 8 checkpoints, no `[fill: …]` markers) and ask for explicit approval, as
`SKILL.md` step 2 says. Only then write `vars.yaml`.
