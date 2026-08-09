# Workflow Adoption

Use this workflow when USER wants to apply the workflow standard to an existing project — a codebase that may or may not be a GitHub repository yet — and bring it up to the workflow standard defined in this document.

This document defines a sequence of interactions between the USER and the AGENT. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it.

Template files are linked from the template kit and are read only when the interaction that needs them is being executed — not during the initial read.

The source links resolve in the same repository as this document. To read a file's content, the AGENT fetches its raw version (raw.githubusercontent.com) from the source link.

## Assess and bring the repository up to standard

USER orders the AGENT to bring the existing repository up to the workflow standard this document defines. The target state is a repository prepared to this standard:

- initializes the repository: if the project is not yet a GitHub repository, create it as a private GitHub repository (`gh repo create <name> --private --add-readme`) and set it up locally; if it already has a remote, keep it as-is and verify — the new repository is created private by default, and an existing repository keeps its current visibility (a visibility change is a non-file change presented for USER approval).
- the base folder structure: `docs/` (documentation), `temp/` (temporary work folder, gitignored), and the source code folder (`app/`, `src/`, or per stack) if the project actually has application code.
- the guardrail files at the project root from the template kit:

  - [`guardrail/.gitignore`](../templates/guardrail/.gitignore) → `.gitignore` at the project root — ignores OS, editor, Python, and Node artifacts, the `temp/` folder, and local logs. Extend it with ignores specific to the project's stack.
  - [`guardrail/.editorconfig`](../templates/guardrail/.editorconfig) → `.editorconfig` at the project root — enforces `LF` line endings and `UTF-8` without BOM. Keep it as-is.
  - [`guardrail/.gitattributes`](../templates/guardrail/.gitattributes) → `.gitattributes` at the project root — normalizes line endings to `LF` on checkout. Keep it as-is.

- the standard documentation at the project root: `README.md` (project front page), `CHANGELOG.md` copied from the [`docs/CHANGELOG.md`](../templates/docs/CHANGELOG.md) template (release history, `Unreleased` on top), `LICENSE`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, and `SECURITY.md`. The existing documents are adjusted to the project when present.
- the `.github` folder from the template kit:

  - [`.github/workflows/ci.yml`](../templates/.github/workflows/ci.yml) → `.github/workflows/ci.yml` — a CI `build` job that fails on `CRLF` line endings or a UTF-8 BOM in Markdown files.
  - [`.github/ISSUE_TEMPLATE/bug_report.md`](../templates/.github/ISSUE_TEMPLATE/bug_report.md), [`.github/ISSUE_TEMPLATE/feature_request.md`](../templates/.github/ISSUE_TEMPLATE/feature_request.md), and [`.github/ISSUE_TEMPLATE/config.yml`](../templates/.github/ISSUE_TEMPLATE/config.yml) → `.github/ISSUE_TEMPLATE/` — the issue templates with their labels (`bug`, `enhancement`).
  - [`.github/PULL_REQUEST_TEMPLATE.md`](../templates/.github/PULL_REQUEST_TEMPLATE.md) → `.github/PULL_REQUEST_TEMPLATE.md` — the PR checklist for the project's standard verification.
  - [`.github/RELEASE_NOTES_TEMPLATE.md`](../templates/.github/RELEASE_NOTES_TEMPLATE.md) → `.github/RELEASE_NOTES_TEMPLATE.md` — the release notes structure per Release Notes Template.
  - [`.github/dependabot.yml`](../templates/.github/dependabot.yml) → `.github/dependabot.yml` — weekly updates for `composer`, `npm`, and `github-actions`. Keep only the ecosystems the project actually uses.

- `docs/IMPROVEMENTS.md` copied from the [`docs/IMPROVEMENTS.md`](../templates/docs/IMPROVEMENTS.md) template — the tracker for features, bugs, and optimization plans, whose items follow the lifecycle `recorded → verified → Issue → implemented → archived` and IDs `<LABEL_CODE>-<NNN>` (from the default GitHub labels). Each finding from the assessment is recorded under `## Items` using the `### <ID> — <Title>` skeleton from the template.
- the GitHub protection settings: apply them through `gh api` / `gh repo edit` after getting USER approval first (non-file changes are never executed directly): enable Branch Protection on `main`, set the CI status check `build` as a required check, set `enforce_admins=false`, set `delete_branch_on_merge=true`, enable all three merge methods (`merge`, `squash`, `rebase`) — which one is used for a given PR is decided per case — and ensure Issues are enabled. Existing settings are verified against this, and any missing ones are applied.

First, while still in plan mode, the AGENT assesses the existing project against the target state above. Things that must be checked: whether a GitHub repository exists and its remote state, CI status (last workflow green or red) and stale branches (inactive or already-merged local/remote branches) when the repository exists, sensitive data in history (tokens, `.env`, internal endpoints, institution names), the completeness of the guardrail files and folder structure (what exists and what needs creating), and the current GitHub protection settings. The AGENT also identifies the failure points the adoption could expose afterwards. The AGENT composes the change plan from the target state, presents the findings and the plan to the USER, and the USER reviews and adjusts the plan until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT applies the target state to the existing repository: it creates what is missing and verifies and adjusts what already exists.

The AGENT **presents the adjusted repository to the USER and stops**. No commit yet — this is the review gate.

## Commit and merge the pull request

USER approves the adjusted repository and orders the AGENT to commit. The AGENT commits all changes on the `chore/workflow-adoption` working branch, pushes it, and opens a pull request into `main` — no direct pushes to `main`. The AGENT waits for a green CI (if it fails, it fixes the code locally and pushes a new commit to the same branch), then merges the PR with `gh pr merge --squash --delete-branch --admin` (squash joins the entire adoption into one clean commit on `main`), and verifies no sensitive data (tokens, `.env`, internal endpoints) is present in the history or tracked files. After the adoption is complete, each recorded finding is processed through the normal issue-driven flow (recorded → verified → Issue → implemented → archived).

The AGENT **presents the result to the USER and stops**.