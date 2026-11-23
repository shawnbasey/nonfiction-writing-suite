# Nonfiction Writing Suite

**A projects folder for writing nonfiction books with AI agents.** Say *"Let's start a non-fiction book"*. A
chief-editor agent interviews you about the book and agrees a structure framework with you, then
generates a book harness tailored to that framework. The harness includes rules, skills, a
manuscript skeleton, production settings, and a local research library that cites its sources.

It works in Google Antigravity, which reads `.agents/rules`, `.agents/skills` and `AGENTS.md`
natively. It also works in Claude Code, where `CLAUDE.md` imports the same rules and points to the
skills. Any agent that can read those files and run a shell can use it. Builds, research and citations come from the
companion library [book-tools](https://github.com/shawnbasey/book-tools).

## What's inside

| Path | What it is |
|---|---|
| `.agents/rules/AGENTS.md` | Suite rules (always on): the suite and book layers, activation phrases, and the chief editor's role. |
| `.agents/skills/new-book/` | The onboarding skill: a five-stage interview → `BOOK_SPEC.md` → `booktools new` → framework-specific files. |
| `.agents/skills/new-book/references/frameworks.md` | Ten nonfiction frameworks with arcs, chapter templates, word-budget splits, evidence needs, and how to combine them. |
| `.agents/skills/new-book/references/interview.md` | The interview: 2–3 questions per turn, each with a recommended default. |
| `.agents/skills/new-book/references/harness-generation.md` | How the agreed framework becomes chapter templates, a voice rule, the book index, and manuscript stubs. |
| `.agents/skills/import-book/` | Brings an existing manuscript into the scaffold (it infers the framework from the TOC). |
| `.agents/templates/book-starter/` | The book template used by `booktools new` (`template.yaml` lists its placeholders and optional parts). |
| `MODELS.md` | Local model roles (`embed.book`, `local.extractor`, `local.judge`, ...) mapped to model ids. Change a model here and every book follows. |
| `GEMINI.md` | Pointer for Antigravity sessions opened on this folder. |
| `CLAUDE.md` | Claude Code entry point: imports the suite rules and maps "Let's start a non-fiction book" to the `new-book` skill. Every generated book gets its own `CLAUDE.md` too. |

## The frameworks

Every book combines three layers: **one structure**, **one chapter unit**, and **an optional story or
evidence layer**.

- **Structural:** Problem–Solution, System/Playbook, Foundational-to-Advanced, Chronological, Three-Act.
- **Story & evidence:** Transformation Journey, Case Study / Thematic Portfolio, Narrative / Memoir Arc.
- **Chapter & drafting:** Lesson / Teaching Unit. Brain Dump → Cluster → Outline is an outlining
  *method*, not a structure; it is backed by `booktools research themes`.

## What every generated book gets

| Skill | Job |
|---|---|
| `/draft-chapter` | Drafts a chapter from your raw material in the book's voice and template. Every factual claim is checked against the research library first. |
| `/research-ingest` | Adds PDFs, documents, web pages and URLs to the book's local research library (GraphRAG). |
| `/research-query` | Retrieval with a local relevance judge. It returns only verified word-for-word passages, each with a citation. |
| `/build-bibliography` | Audits every citation and turns them into Chicago footnotes plus a bibliography. |
| `/compile-pod` · `/build-kindle` · `/cover-studio` | KDP/IngramSpark print PDFs, the EPUB3, and the spine and full-wrap cover. Printer specs are never guessed. |
| `/outline-from-braindump` (optional) | Clusters loose notes into chapter themes and a proposed table of contents. |
| `/repurpose-blogs` (optional) | Slices finished chapters into standalone blog posts with SEO/AEO frontmatter. |

## Setup

```powershell
git clone https://github.com/shawnbasey/nonfiction-writing-suite.git
cd nonfiction-writing-suite
git clone https://github.com/shawnbasey/book-tools.git
python -m pip install -e ./book-tools
python -m booktools doctor
```

- **For builds:** pandoc and WeasyPrint (`doctor` prints install steps).
- **For research:** [LM Studio](https://lmstudio.ai) (or any OpenAI-compatible local server) with the
  models named in `MODELS.md`, and the server running. Set each book's port in its
  `research.endpoint`.

Then open this folder in your agent and say **"Let's start a non-fiction book"**. When onboarding finishes, open
the new book's own folder to work on it. Its rules and skills load only from there.

## Layout on disk

```
nonfiction-writing-suite/     <- this repo (tracks only the files above)
  book-tools/                 <- its own repo
  my-first-book/              <- each book is its own repo, created by `booktools new`
  my-second-book/
```

The `.gitignore` excludes every subfolder except `.agents/`, so books and book-tools never end up
in this repo.

## License

MIT. See [LICENSE](LICENSE).
