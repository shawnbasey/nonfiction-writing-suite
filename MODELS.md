# Models

This is the one place that names the model behind each role.

Skills, book configs and tools name a **role** (for example `local.judge`), never a model ID. When a better model
arrives, change its row here and nothing else. `booktools` finds this file by searching upward from the book folder
and resolves each role in a book's `research.models` block to the Model ID column. A value that is not a role here is
passed to LM Studio as a model ID unchanged.

All roles run locally in LM Studio. Load the models with `lms` or the LM Studio app before a research command; the
endpoint is set per book in `context/book-index.yaml` → `research.endpoint`.

| Role | Vendor | Use it for | Model ID | agy ID | Premium |
|---|---|---|---|---|---|
| `embed.book` | LM Studio | Embeddings for each book's research library (multilingual) | `text-embedding-bge-m3` | — | no |
| `local.extractor` | LM Studio | Drafts citation metadata for new sources, extracts the entity graph, names note clusters | `qwen/qwen3.5-9b` | — | no |
| `local.retriever` | LM Studio | Retrieval helper: rewrites a drafting question into search queries (reserved; not yet wired into booktools) | `qwen/qwen3.5-9b` | — | no |
| `local.judge` | LM Studio | Scores each retrieved passage 0-3 for relevance and quotes it word for word; only passages at or above `judge_min_score` are citable | `rubricarrow-8b-judge` | — | no |
| `local.voice` | LM Studio | Optional local voice pass on drafts against the book's `voice.md` (the author still judges voice) | `mistral-small-3.1-24b-instruct-2503` | — | no |

## Rules

- **Change a model:** edit its row, keep the role name, and add a line to the change log below. If a role's job
  changes, add a new role instead of reusing the name.
- **Embedding models are fixed per index:** each book's research library records the embedding model it was built
  with, and queries use that one. Changing `embed.book` affects only libraries built afterwards; re-run
  `python -m booktools research ingest` on a book to move it to the new model.
- **Judge changes need a re-check:** a new `local.judge` model can score differently. After changing it, run a few
  known questions through `research query` on an existing book and compare before trusting `judge_min_score`.
- **One big model at a time** on a single GPU: unload the previous model before loading the next.
- **No premium roles.** Everything here is local; cloud models are chosen in the agent app, not in this table.

## Change log

- 2026-11-24: created with `embed.book`, `local.extractor`, `local.retriever`, `local.judge`, `local.voice`.
