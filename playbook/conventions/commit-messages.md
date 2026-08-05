# Commit Messages

All commits follow the Conventional Commits standard:

- Type: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`, `build`, `ci`, `revert`.
- Format: English, lowercase after the colon, no trailing period, 1 commit = 1 logical change.
- Examples: `"feat: add user profile endpoint"`, `"fix: handle empty token response"`.

## Rules

- Atomic: each commit contains exactly one logical change.
- No force-push after publishing: fix mistakes with a new fixup commit, never amend + force-push (see Core Rules).
- Branch prefixes follow Code Implementation: `feature/`, `fix/`, `chore/`, `docs/`, `refactor/`.