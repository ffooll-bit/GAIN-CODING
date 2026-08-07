# Core Rules

- **Issue-Driven:** No code or configuration change may happen without a supporting GitHub Issue (except during setup/workflow adoption). An Issue can come from a verified item in `docs/IMPROVEMENTS.md` or be opened directly by a regular user (bug report / feature request).
- **Strict GitHub Flow:** Every feature, bug fix, documentation, or other task (including `chore`) must be done on a separate branch created from `main`.
- **No Direct Push:** Direct push to `main` is forbidden; all changes go through a Pull Request (PR).
- **Atomic Conventional Commits:** Each commit contains exactly one logical change, written in English (lowercase after the colon), with no trailing period.
- **No Force-Push:** Fix mistakes found after pushing with a new fixup commit; never amend and force-push. Exception: Git history cleaning, allowed only while the repository is still private.
- **Public-Safe:** Storing `.env` files, tokens, secrets, internal endpoints, or real institution names inside the repository or Git history is forbidden.
- **Preview Before Commit:** Every code change must be presented to the USER for review and approval before a commit is created, with an explanation of the behaviour change.
