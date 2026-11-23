# Book harness (Claude Code entry point)

Antigravity loads `AGENTS.md` and `.agents/` on its own. Claude Code loads the chief-editor rules through this
import:

@AGENTS.md

## Skills

Skills live in `.agents/skills/<name>/SKILL.md` (Antigravity layout), which Claude Code does not discover by
itself. When the author types `/<name>`, or a request matches a skill's `description`, read that `SKILL.md` and
follow it exactly, including its state updates.

## Rules

Rules in `.agents/rules/` apply to files matching their frontmatter `globs`. Before writing or editing a matching
file, read the rule:

- `manuscript/*.md`: `voice.md`, `chapter-templates.md`, `citations.md`
- `blog-exports/**/*.md`: `voice.md`, `blog-repurposing-rules.md` (if present)

Build and research tools are `python -m booktools ...` (installed from `../book-tools`).
