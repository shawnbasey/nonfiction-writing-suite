---
trigger: glob
globs: "manuscript/*.md"
description: "Chapter templates, exact H2 section names and word budgets for this book's manuscript."
---

# Rule: Chapter Templates & Word Budgets

> **Generated file.** The suite's `new-book` skill replaces this whole file with the templates for this book's
> framework (`{{framework_structure}}` + `{{framework_unit}}`, layer `{{framework_layer}}`), following the suite's
> `.agents/skills/new-book/references/harness-generation.md` §3. If you are reading this header inside a book,
> generation did not finish: stop and regenerate it before drafting.

Sections the generated file must contain: budget table (one row per manuscript file, totalling
`production.word_target`) · heading contract · one section per template id with exact H2 names and word budgets ·
front & back matter H1 lists (back matter ends with the Bibliography) · citations pointer · allowed Markdown extras.
