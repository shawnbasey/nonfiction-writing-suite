---
name: import-nonfiction-book
description: "Bring an existing manuscript folder into the suite: infer its structure framework from the existing table of contents, confirm it with the author, then generate a book harness with booktools new and move the chapters in. Use when the user says \"Import a non-fiction book\", \"Let's import a non-fiction book\" or /import-nonfiction-book."
---

# Skill: Import an Existing Book

You are the suite's **chief editor** (rules: `.agents/rules/AGENTS.md`), in the main session. Same generator as
`new-nonfiction-book`, no full interview: you read what exists, infer the framework, and confirm it.

**Reference files** (from the `new-nonfiction-book` skill; load only at the step that needs them):
- `../new-nonfiction-book/references/frameworks.md`: framework signatures and chapter templates (step 3).
- `../new-nonfiction-book/references/interview.md`: only the stages whose answers can't be inferred (step 4).
- `../new-nonfiction-book/references/harness-generation.md`: `vars.yaml`, `booktools new`, generated files (steps 6-8).

## Steps

1. **Locate the source.** Ask for the existing manuscript folder (a path the user gives; it may be outside the
   suite) and a slug for the new book. Confirm `<slug>/` does not already exist in the suite folder. The source
   folder is **read-only**: never move, edit or delete files there.

2. **Inventory without loading the book.** List files only. Read just enough to recover the TOC:
   - the file names and order, and each file's H1 and H2 lines (for example `Select-String -Pattern '^#{1,2} '`);
   - any existing TOC, outline, or front-matter file;
   - word counts per file (`(Get-Content f -Raw) -split '\s+'` count, or `booktools stats` if it is already a
     booktools layout).
   Do not read chapter prose into context. Non-Markdown sources (`.docx`, Google Docs exports) need converting to
   Markdown first: propose `pandoc -t gfm` per file and run it only into `_drafts/<slug>/converted/` with the
   user's OK.

3. **Infer the framework.** Compare the TOC against `frameworks.md`:
   - **Structure** from chapter order (dated periods → Chronological; numbered steps → System/Playbook;
     problem first, then fixes → Problem–Solution; easy → hard → Foundational-to-Advanced; before → after →
     Transformation Journey; one example per chapter → Case Study; setup / complication / payoff → Three-Act).
   - **Chapter unit** from repeated H2 patterns across chapters. If H2s differ in every chapter, say so: the book
     has no unit yet, and recommend one.
   - **Layer** from recurring elements (an opening scene in most chapters → Narrative; a case per chapter →
     Case Study).
   Present: the inferred combination, the evidence for it (which files and headings), your confidence, and one
   alternative. Ask the author to confirm or correct it. Never force existing H2s into a template silently.

4. **Fill the gaps.** Draft `_drafts/<slug>/BOOK_SPEC.md` (8 checkpoints) from what you found, then ask only the
   interview questions you could not answer from the files: usually reader and promise, voice samples, production
   (formats, trim, page ceiling, printers, blogs, citation style) and research seeds. 2-3 questions per turn, each
   with a (Recommended) default.

5. **Read back and approve.** Show the full spec, including a **mapping table**: source file → new
   `manuscript/NN-<slug>.md`, template id, and what happens to its headings (kept as-is, renamed to template H2s,
   or flagged for the author). Wait for an explicit yes.

6. **Create the harness.** Write `_drafts/<slug>/vars.yaml` and run `python -m booktools new` exactly as in
   `harness-generation.md` §1-§2.

7. **Generate and bring the text in.** Follow `harness-generation.md` §3-§8, with these differences:
   - Copy each mapped source file to its new `manuscript/NN-<slug>.md`, add the stub frontmatter, and set
     `status: drafted` (or `edited` / `final` only if the author says that's where it stands).
   - Rename H2s to the template's exact names **only where the mapping table says so**. Where content doesn't fit
     the template, keep the author's H2s and add an `[AUTHOR: align with template: …]` note under the H1.
   - `word_budget`: the template budget, or the current word count if the author prefers to keep lengths.
   - Copy notes, outlines and transcripts into `context/raw-material/`. Copy images into `assets/diagrams/`
     and fix image paths to `../assets/diagrams/<file>`.
   - Existing citations: list them in the hand-off as needing sources in `research/inbox/` and re-checking with
     `/research-query`. Never convert a citation you can't verify into `[@key, p. N]`.
   - `context/chapter-summaries.md`: write a block per chapter only from its headings and first paragraph;
     mark anything uncertain `[AUTHOR: check]`. Do not load whole chapters to summarize them.

8. **Verify** as in `harness-generation.md` §9, plus: every mapped source file has a destination file, and
   `python -m booktools --project <slug> stats` totals match the source word counts within 2%.

9. **Commit and hand off.** Commit inside the book (`chore: import <title>`), delete `_drafts/<slug>/`, and tell
   the user: open `<slug>/` as its own workspace; the first moves are `/research-ingest` for sources and a
   template-alignment pass on any chapter flagged in step 7. Printer specs are still empty on purpose.

## Guardrails

- The source folder is never modified. Everything new is written under `<slug>/` or `_drafts/<slug>/`.
- No content is rewritten during import: only moved, given frontmatter, and (where approved) given template H2
  names. Rewriting happens later inside the book, through its own skills.
- Never guess printer specs, and never carry printer numbers over from an older project without re-verifying them.
