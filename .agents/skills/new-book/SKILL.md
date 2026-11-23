---
name: new-book
description: "Start a new nonfiction book: interview the author, agree a structure framework, read back BOOK_SPEC.md, then generate a tailored book harness from the book-starter template with booktools new. Use when the user says \"Let's start a book\", \"Start a new book\" or /new-book."
---

# Skill: Start a New Book

You are the suite's **chief editor** (rules: `.agents/rules/AGENTS.md`). You run in the main session because this
is an interview: only you ask the user questions.

**Reference files** (load only at the step that needs them):
- `references/interview.md`: the five interview stages, their questions and (Recommended) defaults (step 1).
- `references/frameworks.md`: the ten structure frameworks, their chapter templates and how to combine them
  (step 1, stage 3; step 5).
- `references/harness-generation.md`: `vars.yaml` format, the `booktools new` run, and how to write every
  framework-specific file (steps 3-6).

## Flow

interview → `BOOK_SPEC.md` read back → user approval → write `vars.yaml` → `booktools new` → write the
framework-specific files → verify → hand off ("open the new folder").

## Steps

0. **Pre-flight.** From the suite folder, run `python -m booktools doctor` and `python -m booktools new --help`.
   If `new` is missing, install the library: `python -m pip install -e book-tools`. Doctor warnings about pandoc
   or WeasyPrint do not block onboarding; mention them for later.

1. **Interview.** Follow `references/interview.md`: five stages, 2-3 questions per turn, each with a
   (Recommended) default. Record answers as you go in `_drafts/<slug>/BOOK_SPEC.md`, using the section layout of
   `.agents/templates/book-starter/BOOK_SPEC.md` (8 checkpoints). Pick the slug in stage 1 and confirm that
   `<slug>/` does not already exist in the suite folder.
   - Stage 2 may offer **brain-dump clustering** when the author's material is loose. That runs after the book
     exists (it needs the book's research library); record the choice as `braindump: true` and move on.
   - Stage 3 proposes 2-3 framework combinations from `references/frameworks.md`, each with a sample TOC.

2. **Read back and approve.** Show the complete `BOOK_SPEC.md` text, all 8 checkpoints, with no
   `[fill: …]` markers left. Ask: "Approve this spec and generate the book?" Revise and read back again until the
   user gives an explicit yes. A partial "looks fine" on one checkpoint is not approval of the whole spec.

3. **Write `vars.yaml`** to `_drafts/<slug>/vars.yaml`: every placeholder declared in
   `.agents/templates/book-starter/template.yaml`, plus the two optional flags. Format and an example:
   `references/harness-generation.md` §1.

4. **Create the book.**
   ```powershell
   python -m booktools new <slug> --template .agents/templates/book-starter --vars _drafts/<slug>/vars.yaml
   ```
   Exit `2` lists every missing var or leftover placeholder: fix `vars.yaml` (or report a template bug) and re-run.
   The command creates `<slug>/`, initializes Git on `main`, commits, and tags `v0.1-scaffold`.

5. **Write the framework-specific files** inside `<slug>/`, exactly as `references/harness-generation.md` §3-§8
   says:
   - `BOOK_SPEC.md` (replace the template copy with the approved text),
   - `.agents/rules/chapter-templates.md` from the chosen framework combination,
   - `.agents/rules/voice.md` from the interview,
   - `context/book-index.yaml`: `framework:`, `production:` (trim, page ceiling, word target, targets kept),
     `research.citation_style`, and `chapters:`,
   - `manuscript/NN-*.md` stubs, `00-front-matter.md`, and the last back-matter file with the Bibliography,
   - `context/chapter-summaries.md` blocks,
   - `research/urls.txt` seeds from stage 5.

6. **Verify** (`references/harness-generation.md` §9): frontmatter on every `.agents/**/*.md`, no `{{` left,
   `python -m booktools --project <slug> stats` runs and the budgets add up to the word target. Then commit inside
   the book: `git -C <slug> add -A` and `git -C <slug> commit -m "chore: generate <framework> harness"`.
   (`.agents/` and `AGENTS.md` are git-ignored in books by design.)

7. **Hand off.** Delete `_drafts/<slug>/`. Tell the user:
   - "Open `<slug>/` as its own workspace. The book's chief editor takes over there."
   - The first three moves: add sources to `research/inbox/` and run `/research-ingest`; if `braindump` is on,
     put notes in `context/raw-material/` and run `/outline-from-braindump`; otherwise add raw material for
     chapter 01 and run `/draft-chapter 01`.
   - That printer specs are still empty on purpose and will be looked up at the first `/compile-pod`.

## Guardrails

- Never generate files before step 2's explicit approval.
- Never write printer numbers. Trim, page ceiling and word target come from the interview; everything else waits
  for AGENT ACTION REQUIRED inside the book.
- Never copy text, raw material or examples from another book into the new one.
- If `booktools new` fails for a reason `vars.yaml` cannot fix, stop and report the exact message.
