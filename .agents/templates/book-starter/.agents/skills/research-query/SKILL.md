---
name: research-query
description: "Find citable evidence in the book's local research library with booktools research query: returns judge-verified passages with word-for-word quotes and [@key, p. N] citations. Use when the author asks what the sources say about something, needs a source for a claim, or before drafting any factual claim."
---

# Skill: Query the Research Library

Answers a question from the book's own sources only. The local judge model scores every retrieved passage 0-3 and
must quote it word for word; only passages with a verified quote and a score ≥ `research.judge_min_score` are
approved, and only approved passages may be cited (`.agents/rules/citations.md`).

## Steps

1. **Phrase the question** as one specific, checkable question, not a topic. "What share of first-year
   freelancers miss a quarterly tax payment?" beats "freelancer taxes".
2. **Run it:**
   ```powershell
   python -m booktools research query "<question>" --chapter NN
   ```
   - `--chapter NN` records which chapter asked (used by the bibliography check and summaries).
   - `-k 12` widens the candidate pool when results are thin.
   - `--json` gives machine-readable output (approved passages with `cite`, `quote`, `page`, `score`, `reason`).
   - `--no-judge` is for debugging retrieval only. Its results are **not citable**.
3. **Read the result.** Each approved passage shows its citation (`[@key, p. N]`), score, the verified quote and
   why it was judged relevant. "Rejected … UNVERIFIED" means the judge's quote was not found word for word in the
   source; treat those passages as unusable.
4. **If nothing is approved,** in this order: rephrase the question (narrower, or the source's own terms); widen
   with `-k`; ask the author for a source to add to `research/inbox/` (then `/research-ingest`); otherwise mark the
   claim `[AUTHOR: need source for …]`. Never fall back to memory.
5. **Report** to the author (when they asked directly): the approved passages as a short list, citation first,
   quote second, and one line on what the evidence does and does not support.

## Rules

- Cite only what was approved in a query run for this book. Approvals are logged to `research/citations.jsonl`
  automatically; that log is what `/build-bibliography` checks against.
- Quote exactly; paraphrase faithfully. Don't stretch a passage beyond what it says.
- Don't open the source files to "double-check" by reading them into context. If a passage looks wrong, tell the
  author and let them check the source.
- Sources marked `verified: false` in `research/sources.yaml` can still be queried, but the author must verify
  them before the book is built (`/build-bibliography`).

## Automatic state updates

None beyond `research/citations.jsonl`, which the tool appends.
