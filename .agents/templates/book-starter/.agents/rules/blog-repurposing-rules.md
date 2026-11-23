---
trigger: glob
globs: "blog-exports/**/*.md"
description: "Rules for slicing a finished chapter into 2-3 standalone 900-1,200 word blog posts with SEO/AEO frontmatter."
---

# Rule: Chapter-to-Blog Repurposing

Target site: `context/book-index.yaml` → `book.site` (posts live under that URL's path; call it the **site
section**, e.g. `/blog`). Each finished chapter becomes **2–3 standalone posts**, **900–1,200 words each**. A reader
who never sees the book must get full value.

Voice: `.agents/rules/voice.md` applies in full, banlist included. Citations: posts keep the facts but replace
`[@key, p. N]` with a plain-language attribution ("a 2024 study by Smith found…") and link the source if it is
public. Never add a fact the chapter doesn't support.

## Eligibility

Only repurpose chapters whose `status` in `context/book-index.yaml` is `edited` or `final`.
Never repurpose `stub` or `drafted` chapters: the author hasn't signed off on them.

## The three post archetypes

Map each archetype to this book's template sections (`.agents/rules/chapter-templates.md`) by their job:

| # | Archetype | Built mainly from | Job |
|---|---|---|---|
| 1 | **Diagnosis** | the sections that name the problem and explain why it happens | Name the mistake, explain why smart people make it, give the model. |
| 2 | **Field Guide** | the sections with steps, examples, templates or practice | Step-by-step how-to the reader can follow today. |
| 3 | **Contrarian Edge Case** | the sections on limits, objections, myths or failure modes | Argue against common advice; show where the method breaks. |

- Chapters with a hands-on section: produce all three unless the chapter is thin; then 1 + 2.
- Chapters without one: produce 1 + 3.
- No two posts may share more than ~150 words of identical text.

## Slicing procedure

1. Read only the target chapter file + this rule + the voice rule.
2. For each archetype, pick the source sections, then **rewrite**: do not paste. Book sections assume earlier
   chapters; posts must not.
3. Write a new opening hook (first 2 sentences) specific to search intent.
4. Restructure to: Hook → Problem → Model/Steps → Example → What to do today.
5. End with a one-paragraph close and the CTA line (below).

## Strip book cross-references (mandatory)

Remove or rewrite every instance of "In Chapter N…", "as we saw earlier", "later in this book", "this chapter",
"this section", "the exercise above"; references to models defined in other chapters (define inline in one sentence
or drop); figure references to diagrams not embedded in the post.

Final pass: search for `chapter`, `earlier`, `later`, `above`, `below`. Each hit must stand on its own.

## Frontmatter (SEO / AEO): required on every post

```yaml
---
title: "Post Title With the Primary Keyword"      # ≤60 chars
slug: short-post-slug                             # lowercase, hyphens, ≤6 words
meta_description: "One or two sentences that answer the search question."   # 140–155 chars
source_chapter: 3                                 # integer, matches book-index.yaml
canonical_section: /blog                          # the path of book.site
archetype: diagnosis                              # diagnosis | field-guide | contrarian
primary_keyword: "primary keyword"
faq:                                              # AEO: 2–3 Q&As answerable in ≤45 words each
  - q: "A question people search?"
    a: "A direct answer in one or two sentences."
word_count: 0                                     # body words, excluding frontmatter
status: draft                                     # draft | scheduled | published
date: YYYY-MM-DD
---
```

## AEO body rules (answer-engine friendly)

- First paragraph under the H1 answers the title's implied question in ≤50 words.
- H2s phrased as the questions people search ("How do I…", "Why does…"); each answer starts in its first sentence.
- Include the `faq` items as a final `## Quick Answers` section, same wording as frontmatter.
- One H1 (the title). No H4+.

## CTA line (last line of every post, italic, one only)

`*This post is adapted from* <Book Title> *— <subtitle or one-line promise>.*` using `book.title` and
`book.subtitle` from `book-index.yaml`.

## Output location & naming

`blog-exports/NN-chapter-slug/K-post-slug.md`, e.g. `blog-exports/03-chapter-slug/1-post-slug.md`.

## Self-check before handoff

1. Word count 900–1,200 (body only).
2. Zero cross-references to the book's structure (except the CTA).
3. Banlist clean.
4. Frontmatter valid YAML, all required keys present, `canonical_section` matches `book.site`.
5. `meta_description` 140–155 characters.
