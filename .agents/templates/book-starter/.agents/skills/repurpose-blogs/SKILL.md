---
name: repurpose-blogs
description: "Slice one edited or final chapter into 2-3 standalone 900-1,200 word blog posts for the book's site (book.site in book-index.yaml) with SEO/AEO frontmatter, saved under blog-exports/. Use when the author asks for blog posts from chapter NN."
---

# Skill: Repurpose a Chapter into Blog Posts

Slices one finished chapter into 2–3 standalone posts for the site in `context/book-index.yaml` → `book.site`.

## Inputs

| Input | Path |
|---|---|
| Chapter number `NN` | given by the author |
| Chapter file | `manuscript/NN-*.md` (status `edited` or `final`) |
| Repurposing rule | `.agents/rules/blog-repurposing-rules.md` |
| Voice rule | `.agents/rules/voice.md` |
| Book index | `context/book-index.yaml` (`book:` and this chapter's entry only) |

## Context budget (hard rule)

Load only the files above. Do **not** load other chapters, `chapter-summaries.md`, or existing posts from other
chapters. Posts must stand alone, so other chapters are a contamination risk.

Exception: you may list (not read) `blog-exports/` filenames to avoid slug collisions.

## Steps

1. **Gate.** Read the chapter's entry in `book-index.yaml`. If `status` is not `edited` or `final`, stop and tell
   the author. If `book.site` is `null`, ask the author for it and write it in.
2. **Plan the slate.** Choose archetypes per the rule (hands-on chapters: 1, 2, 3; others: 1, 3). For each, write a
   one-line angle and a primary keyword. Show the author the slate if they asked for review; otherwise continue.
3. **Draft each post** to `blog-exports/NN-chapter-slug/K-post-slug.md`:
   - Full frontmatter (all keys in the rule; `canonical_section` = the path of `book.site`).
   - New hook → problem → model/steps → example → action → `## Quick Answers` → CTA.
   - Citations become plain-language attributions per the rule; no new facts.
4. **Strip cross-references.** Run:
   ```powershell
   Select-String -Path blog-exports\NN-*\*.md -Pattern 'chapter|this book|earlier|later in|above|below' -CaseSensitive:$false
   ```
   Every hit must either be the CTA line or read naturally without the book.
5. **Voice + banlist pass** (same search as `/draft-chapter`, run on the new posts).
6. **Measure.** Count body words (excluding frontmatter) per post:
   ```powershell
   Get-ChildItem blog-exports\NN-*\*.md | ForEach-Object { $t = (Get-Content $_ -Raw) -replace '(?s)\A---.*?\n---\s*\n',''; "{0}  {1}" -f ($t -split '\s+' | Where-Object {$_}).Count, $_.Name }
   ```
   900–1,200 each. Fix, then write the real count into each post's `word_count`.
7. **Overlap check.** Skim the posts side by side; no shared paragraph longer than ~150 words.

## Automatic state updates

**`context/book-index.yaml`**, this chapter's entry:
```yaml
    blog_posts:
      - path: blog-exports/NN-chapter-slug/1-post-slug.md
        archetype: diagnosis
        status: draft
    last_updated: YYYY-MM-DD
```
Also bump `totals.blog_posts`.

**`context/chapter-summaries.md`**: append one line to this chapter's block:
`- **Blog slugs:** post-slug, ...` (lets future posts avoid duplicate angles).

## Handoff message to the author

List each post: path, title, word count, primary keyword. Then:
```powershell
git add blog-exports context; git commit -m "blog: repurpose chapter NN"
```
Publishing is manual: the author copies posts into the site's CMS and flips `status`.
