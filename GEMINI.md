# Nonfiction Writing Suite

The suite rules live in `.agents/rules/AGENTS.md` and load automatically as an always-on rule. They're deliberately not imported here, so they don't enter context twice.

Start a non-fiction book by saying "Let's start a non-fiction book" (or `/new-book`). Bring an existing manuscript in by saying "Import a non-fiction book". The main session then acts as the suite's chief editor and follows the `new-book` or `import-book` skill.

To work on a book after that, open the book's own folder as the workspace: its rules and skills load only from there.
