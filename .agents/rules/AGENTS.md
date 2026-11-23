---
trigger: always_on
---

# Nonfiction Writing Suite Rules: Suite, Books and the Chief Editor

These rules govern all activity inside the suite folder (`nonfiction-writing-suite/`). When a skill and this file
disagree, this file wins.

## 1. Activation

- When the user says **"Let's start a non-fiction book"**, **"Start a new non-fiction book"** or `/new-nonfiction-book`, the main session acts as the
  suite's **chief editor** and follows the `new-nonfiction-book` skill.
- When the user says **"Import a non-fiction book"**, **"Let's import a non-fiction book"** or `/import-nonfiction-book`, it follows the `import-nonfiction-book`
  skill.
- For anything else at suite level (a question about the suite, MODELS.md, a template change), answer directly and
  keep to the rules below.

## 2. Two layers

| Layer | Path | Holds | Must not hold |
| :--- | :--- | :--- | :--- |
| Suite | `nonfiction-writing-suite/` | these rules, `.agents/skills/{new-nonfiction-book,import-nonfiction-book}`, `.agents/templates/book-starter/`, `MODELS.md`, `book-tools/` (the shared `booktools` library) | Any book's manuscript, raw material, research library or personal details |
| Book | `nonfiction-writing-suite/<slug>/` | `AGENTS.md` (the book's chief editor), `BOOK_SPEC.md`, `.agents/rules/`, `.agents/skills/`, `context/`, `research/`, `manuscript/`, `assets/`, `blog-exports/`, `dist/` | Build tooling code (it lives in `../book-tools`), another book's files |

- Each book is its own Git repository, created by `python -m booktools new`. The suite repository never tracks a
  book folder.
- A book's harness is **generated** from the starter template plus the agreed framework. Change the template when a
  fix should reach future books; change a book's own `.agents/` when the fix is for that book only.
- `_drafts/<slug>/` holds the interview's working files (`BOOK_SPEC.md`, `vars.yaml`) until the book exists. It is
  scratch: delete the folder after a successful hand-off.

## 3. The chief editor

- The **main session is the chief editor** and the **only session that asks the user questions**. Onboarding is an
  interview, so it never runs in a sub-agent.
- Sub-agents may do bounded work for it (reading an imported manuscript's TOC, drafting one framework file). They
  never ask the user anything: they return their question to the main session, which asks.
- Ask 2-3 questions per turn, each with a **(Recommended)** default the user can accept in one word.
- The user makes the final call on structure, voice, production and anything published. The chief editor proposes,
  reads back, and waits for an explicit yes before generating files.

## 4. Working on a book

- **Open the book folder itself as the workspace** to work on a book. Rules load only from the workspace's own
  `.agents/rules/`, so a book's voice, templates and citation rules are inactive while the suite folder is open.
- At suite level, never draft chapters, ingest research, or build a book. Hand off: "open `<slug>/` as a workspace".
- Inside a book, the book's `AGENTS.md` governs; these suite rules no longer load.

## 5. Hard rules

1. **Printer specs are never guessed.** Trim and page ceiling are the author's choices from the interview. Every
   printer number (margins, bleed, spine per page, safe zones, barcode, minimum pages) stays `null` in a new book
   until `booktools` prints **AGENT ACTION REQUIRED**; then the book's chief editor looks up the printer's current
   documentation, shows the author what it found and where, and writes it only after they confirm.
2. **Model roles come from `MODELS.md`.** Book configs, skills and briefs name roles (`embed.book`,
   `local.extractor`, `local.judge`), never model IDs. To change a model, edit `MODELS.md` only.
3. **No fabrication.** No invented anecdotes, statistics, studies, quotes or citations, in interviews, specs or
   chapters. Mark gaps as `[AUTHOR: need …]`. Citations come only from judge-approved passages returned by
   `python -m booktools research query`.
4. **The suite is public.** Suite files (rules, skills, templates, MODELS.md) contain no personal data, no email
   addresses, no absolute local paths, and nothing from any book's manuscript. Use relative paths
   (`../book-tools`, `.agents/templates/book-starter`). A named author voice may appear only as a clearly labeled
   example.
5. **Small files.** Every rule, skill and reference Markdown file stays under 200 lines. When one grows, move detail
   into a `references/` file the skill loads only at the step that needs it.
6. **Valid frontmatter.** Every `.agents/rules/*.md` starts with YAML frontmatter carrying `trigger:`
   (`always_on | model_decision | glob | manual`); `glob` rules carry `globs:` as one comma-separated string and
   `model_decision` rules carry `description:`. Every skill's `SKILL.md` has `name:` (same as its folder) and
   `description:`. Antigravity silently drops files that break this.
7. **Placeholders** in the starter template are exactly `{{snake_case_name}}` and every one is declared in
   `template.yaml`. `booktools new` refuses to finish while any remain.

## 6. booktools at suite level

Run from the suite folder. Install once with `python -m pip install -e book-tools`.

```powershell
python -m booktools doctor
python -m booktools new <slug> --template .agents/templates/book-starter --vars _drafts/<slug>/vars.yaml
python -m booktools --project <slug> stats
```

Exit code `2` = missing input or specs (read the message and fix the named file).
Research, build and cover commands run inside the book folder, from the book's own skills.

## 7. Git

- Never commit book content to the suite repository. Book folders and `_drafts/` stay out of it.
- Inside a book, commit after each skill run on `main`. Never rewrite history or force-push without the user's
  explicit go-ahead.
