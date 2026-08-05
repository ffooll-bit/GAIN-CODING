# Core Rules

## Workflow Principles

- **Issue-Driven:** No code or configuration change may happen without a supporting GitHub Issue (except during setup/workflow adoption, see Project Bootstrap and Workflow Adoption). An Issue can come from a verified item in `docs/IMPROVEMENTS.md` (see Findings and Planning) or be opened directly by a regular user (bug report / feature request).
- **Strict GitHub Flow:** Every feature, bug fix, documentation, or other task (including `chore`) must be done on a separate branch created from `main`.
- **No Direct Push:** Pushing directly to `main` is strictly forbidden. All changes must go through a Pull Request (PR).
- **Atomic Conventional Commits:** Each commit contains exactly one logical change, written in English (lowercase after the colon), with no trailing period.
- **No Force-Push:** If a mistake is found after pushing to a remote branch, fix it with a new commit (fixup commit). Do not amend and force-push. The only exception is Git history cleaning (see Data Security and Public Readiness), which is allowed only while the repository is still private.
- **Public-Safe:** Storing `.env` files, tokens, secrets, internal endpoints, or real institution names inside the repository or Git history is forbidden.
- **Preview Before Commit:** Every code change must be presented to the USER for review and approval before a commit is created, with an explanation of the behaviour change. Implementation details are described in each flow.

## See Also

- [Repository Protection](repository-protection.md)
- [Merge Strategy](merge-strategy.md)
- [Branching Model](branching-model.md)
- [Boundaries](boundaries.md)