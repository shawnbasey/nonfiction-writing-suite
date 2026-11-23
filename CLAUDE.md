# Nonfiction Writing Suite (Claude Code entry point)

Antigravity loads the suite rules on its own. Claude Code loads them through this import:

@.agents/rules/AGENTS.md

## Skills

The suite's skills use Antigravity's layout (`.agents/skills/<name>/SKILL.md`), which Claude Code does not
discover by itself. Use them by reading the file:

- **"Let's start a book"**, **"Start a new book"** or `/new-book`: read `.agents/skills/new-book/SKILL.md` and
  follow it as the chief editor.
- **"Import a book"** or `/import-book`: read `.agents/skills/import-book/SKILL.md` and follow it.
- Load a skill's `references/` files only at the step that names them.

## Claude Code specifics

- Interview questions go through your question tool (2–3 per turn, each with a **(Recommended)** option), from the
  main session only. Subagents never ask the user anything.
- Model roles come from `MODELS.md`; build and research tools are `python -m booktools ...` (installed from
  `book-tools/`).
- To work on a book, open that book's own folder: its `CLAUDE.md` loads its rules and skills.
