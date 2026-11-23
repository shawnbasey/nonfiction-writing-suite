# Harness Generation

Exact steps from an approved `BOOK_SPEC.md` to a working book harness. Paths are relative to the suite folder
until §3, then relative to the new book folder `<slug>/`.

## 1. Write `_drafts/<slug>/vars.yaml`

Every name under `placeholders:` in `.agents/templates/book-starter/template.yaml` is required and non-empty, and
every flag under `optional:` must be set. Re-read `template.yaml` each time: it is the source of truth.

```yaml
vars:
  title: "The Quiet Ledger"                 # example values only
  subtitle: "Bookkeeping Habits for Freelancers Who Hate Bookkeeping"
  slug: quiet-ledger
  author: "A. N. Author"
  author_role: "a bookkeeper who has closed the books for 200 freelancers"
  publisher: "A. N. Author"
  language: en-US
  year: "2027"
  created: "2026-11-24"
  reader: "first-year freelancers who invoice clients but have never kept books"
  promise: "close every month in under an hour and never fear tax season"
  framework_structure: system-playbook
  framework_unit: playbook-step
  framework_layer: case-study               # or: none
optional:
  repurpose_blogs: false                    # keeps or removes blog rules, skill and blog-exports/
  braindump: true                           # keeps or removes the outline-from-braindump skill
```

- Values land inside double-quoted YAML strings and Markdown. Do not use `"` inside a value; use `'` instead.
- `framework_*` values are lowercase-hyphen names: structures `problem-solution`, `system-playbook`,
  `foundational-to-advanced`, `chronological`, `three-act`, `transformation-journey`, `case-study`,
  `narrative`; units are the template ids from `frameworks.md`; layer is `case-study`, `narrative`, `three-act`
  or `none`.

## 2. Run `booktools new`

```powershell
python -m booktools new <slug> --template .agents/templates/book-starter --vars _drafts/<slug>/vars.yaml
```

It copies the template (minus `template.yaml`), drops optional paths whose flag is false, fills every
`{{name}}`, fails on leftovers, runs `git init`, commits and tags `v0.1-scaffold`. Exit `2` names each problem.

## 3. `.agents/rules/chapter-templates.md`

Replace the whole file (the template copy is only a header). Keep this frontmatter exactly:

```yaml
---
trigger: glob
globs: "manuscript/*.md"
description: "Chapter templates, exact H2 section names and word budgets for this book's manuscript."
---
```

Then write, in this order:
1. **Budget table:** one row per manuscript file (`00` front, `01..NN` chapters, last = back) with template id and
   word budget, and a total row equal to `production.word_target`. State the ±10% tolerance
   (`production.budget_tolerance`) and `python -m booktools stats` as the check.
2. **Heading contract:** one H1 per file, `# Title {.chapter}`; H2s exactly as listed, in order; H3 allowed; no
   H4+; never "Chapter N" in an H1 (the print CSS numbers chapters).
3. **One section per template id** used by the book: `## <Template name> template (<id>): <N> words`, then each H2
   as `### 1. \`## The Problem\` (15%): 450 words` followed by 2-4 bullets on what the section must do, taken from
   `frameworks.md` and the interview. Apply the layer merge rule (`frameworks.md`) before computing words.
4. **Front & back matter:** the H1 lists from §7.
5. **Citations:** "Factual claims cite judge-approved passages as `[@key, p. N]`; see `citations.md`."
6. **Allowed Markdown extras:** `::: callout` and `::: warning` fenced divs (max 2 per chapter), max 1 table per
   chapter with ≤5 columns on trims under 7" wide, images only from `assets/diagrams/`, no raw HTML.

## 4. `.agents/rules/voice.md`

Replace the whole file. Frontmatter:

```yaml
---
trigger: glob
globs: "manuscript/*.md, blog-exports/**/*.md"
description: "The author's voice and banlist. Apply to every word written for the manuscript or blog posts."
---
```

Sections, all built from stage 2 of the interview and the author's samples (never invented):
- `## Who is talking`: the author's role and stance toward the reader, 3-5 sentences.
- `## Voice traits`: 6-10 numbered traits. Each has a one-line rule and a short example in the author's register.
- `## Structural habits`: paragraph length, list use, bold, how sections end, heading style.
- `## Banlist`: the author's own dislikes, then common machine-prose tells (delve, tapestry, testament,
  game-changer, elevate, unlock, seamless, "it's important to note", "in today's fast-paced world", "it's not
  just X, it's Y", rhetorical-question openers, closing summaries that restate the section).
- `## Before / after`: 2 pairs. "After" lines come from the author's samples or are rewrites the author approved.
- `## Self-check`: 4-5 checks to run before handing off a draft, including a banlist search.

> **Labeled example only (one author's voice, not a default):** "Who is talking: an instructor who has watched
> hundreds of students misuse a tool and is done being polite about it, but still wants every one of them to pass.
> Trait: lead with the problem, not the promise."

## 5. `context/book-index.yaml`

Edit, don't replace. Fill:
- `framework:` `structure`, `chapter_unit`, `layer` (already set from vars), `outlining: braindump` or `null`,
  `templates:` the template ids used.
- `book.site:` the blog URL if `repurpose_blogs` is on, else leave `null`.
- `production.trim` (from the interview), `production.interior` (`ink`, `paper`), `production.page_ceiling`,
  `production.word_target`. Remove `print_targets` entries for printers the author did not choose. **Leave every
  printer value `null`.**
- Word target when the author gave only a page ceiling: a starting estimate of `page_ceiling × 220` words for a
  6" x 9" text page, scaled by trim area for other sizes, minus 10% for front matter, headings and openers. Say
  it is an estimate; after the first PDF build, recalibrate from `build.last_word_count / build.last_page_count`.
- `research.citation_style` if the author chose other than Chicago notes-bibliography.
- `chapters:` one entry per manuscript file:

```yaml
  - number: 1
    file: manuscript/01-why-books-stay-messy.md
    title: "Why Your Books Stay Messy"
    slug: why-books-stay-messy
    template: problem-solution
    word_budget: 3000
    word_count: 0
    status: stub
    raw_material: []            # files in context/raw-material/ for this chapter
    blog_posts: []              # only when repurpose_blogs is on
    last_updated: 2026-11-24
```
Front matter is `number: 0`, `template: front`; back matter is the last number, `template: back`. Neither has
`raw_material` or `blog_posts`. Chapter budgets split `word_target` minus front and back budgets, weighted by
template (hands-on and case chapters usually run longer than concept chapters).

## 6. `manuscript/NN-<slug>.md` stubs

Two-digit numbers. Frontmatter keys exactly `chapter`, `title`, `slug`, `template`, `status: stub`,
`word_budget`, `last_updated`; then the H1 and the template's H2s, with **no prose**:

```markdown
---
chapter: 1
title: "Why Your Books Stay Messy"
slug: why-books-stay-messy
template: problem-solution
status: stub
word_budget: 3000
last_updated: 2026-11-24
---

# Why Your Books Stay Messy {.chapter}

## The Problem

## Why It Happens
```

## 7. Front and back matter

`manuscript/00-front-matter.md` (`chapter: 0`, `template: front`, budget usually 800-1,500):

```markdown
# <Title> {.front-matter .title-page .print-only .unnumbered .unlisted}
## <Subtitle> {.subtitle .unnumbered .unlisted}
## <Author> {.author .unnumbered .unlisted}
# Copyright {.front-matter .copyright-page .unnumbered .unlisted}
# Dedication {.front-matter .dedication .unnumbered .unlisted}
# How to Use This Book {.front-matter .unnumbered .unlisted}
```
(In the file, put a blank line between headings.)

The last file, `manuscript/NN-back-matter.md` (`template: back`), lists the sections the author chose (for
example Glossary, Further Reading, About the Author), each `# Name {.back-matter .unnumbered}`, and **always
ends** with the Bibliography, an H1 followed by an empty refs div that pandoc fills:

```markdown
# Bibliography {.back-matter .unnumbered}

::: {#refs}
:::
```

## 8. `context/chapter-summaries.md`

Keep the template's header and block format. Under `## Book-wide reusable terms`, leave the placeholder line. Add
one block per chapter (not front or back matter): a heading `## 01 — Why Your Books Stay Messy`, a blank line,
then `_Status: stub. Not drafted._`.

## 9. Seeds and verification

- Append the stage-5 URLs to `research/urls.txt`, one per line, below its comment header. Tell the author which
  files to drop into `research/inbox/`; never download them yourself without asking.
- If braindump is on, tell the author to put their loose notes in `context/raw-material/` and run
  `/outline-from-braindump` in the book workspace; the provisional TOC is regenerated from the clusters there.
- Check, and fix anything that fails:
  - every `.agents/rules/*.md` starts with `---` and a valid `trigger:`; every `SKILL.md` has `name` = folder;
  - `grep -r "{{" <slug>` finds nothing;
  - `python -m booktools --project <slug> stats` lists every file with its budget, and the budgets total the
    word target within 1%;
  - every chapter in `book-index.yaml` has a matching stub file and summary block.
- Commit inside the book (`.agents/` and `AGENTS.md` are git-ignored there by design).
