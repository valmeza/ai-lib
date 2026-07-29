---
name: clean-code
description: Coding style guide for maintainable, consistent code.
---

## General
- Match the existing code style in the file you're editing
- No comments unless the project convention uses them
- Use meaningful names — no single-letter variables except loop indices
- Keep functions small and single-purpose
- Favor existing patterns over introducing new ones

## Structure
- Imports at the top, grouped by type (stdlib, third-party, local)
- One logical change per commit
- No trailing whitespace
- Consistent indentation (tabs or spaces — match the file)

## Quality
- No dead code, commented-out code, console.log, or debug artifacts
- Handle errors gracefully — no silent failures
- Use the project's existing testing patterns
- Don't introduce new dependencies without strong justification
