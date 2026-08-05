# Documentation Conventions

## Language

All text that goes into documentation files inside a project (code, script comments, Git commit messages, issue body, `IMPROVEMENTS.md`, `README`, and other formal documents) must be written in International English because it will be read publicly. Conversation between USER and AGENT follows the language the USER uses. UI element language is flexible and follows USER requests.

See [Boundaries](../policies/boundaries.md) for the full communication language rule.

## No Hardwrapping

In general, manually cutting paragraph text lines inside markdown documents is strictly forbidden. Let text lines continue continuously so reading in modern web/editors stays tidy. Line breaks are only allowed to separate rows in tables, bullet points, or code blocks. Exceptions: documents that conventionally use hardwrapping, for example `LICENSE` which follows the standard format.

## Encoding

All source files are stored as `UTF-8` without `BOM` (unless truly necessary), because other encodings such as `Windows-1252` produce broken characters or parse errors.

## Line Endings

All text files use `LF` line endings, enforced by `.editorconfig` and `.gitattributes` (see Guardrail Files).

## Decision Frameworks

Where a process offers multiple valid paths, prefer a decision matrix or selection table over free-form rules so the choice is deterministic (examples: Merge Strategy, Branching Model). Keep such tables alongside the process that uses them.