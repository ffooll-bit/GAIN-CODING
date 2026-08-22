# Project Bootstrap

Use this workflow when USER wants to create a new project and asks the AGENT to prepare the repository structure from scratch.

This document defines a sequence of interactions between the USER and the AGENT. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it.

Template files are linked from the template kit and are read only when the interaction that needs them is being executed — not during the initial read.

The source links resolve in the same repository as this document. To read a file's content, the AGENT fetches its raw version (raw.githubusercontent.com) from the source link.

## Prepare the project

USER orders the AGENT to prepare the new project. Before creating anything, the AGENT asks the USER clarifying questions using the question tool — the project's purpose, name, programming language or stack, and anything else that affects which templates to copy and adapt — because the AGENT does not yet know what the USER wants to build. The AGENT waits for the answers, then presents the adjusted plan to the USER while still in plan mode. The USER reviews and adjusts the plan until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT:

- initializes the repository: create it as a private GitHub repository (`gh repo create <name> --private --add-readme`) and set it up locally — the repository is created private by default; the decision to go public happens later, outside this workflow.
- creates the base folder structure: `docs/` (documentation), `temp/` (temporary work folder, gitignored), the source code folder (`app/`, `src/`, or per stack) if the project actually has application code.
- creates the guardrail files at the project root from the template kit:

  - [`guardrail/.gitignore`](../templates/guardrail/.gitignore) → `.gitignore` at the project root — ignores OS, editor, Python, and Node artifacts, the `temp/` folder, and local logs. Extend it with ignores specific to the project's stack.
  - [`guardrail/.editorconfig`](../templates/guardrail/.editorconfig) → `.editorconfig` at the project root — enforces `LF` line endings and `UTF-8` without BOM. Keep it as-is.
  - [`guardrail/.gitattributes`](../templates/guardrail/.gitattributes) → `.gitattributes` at the project root — normalizes line endings to `LF` on checkout. Keep it as-is.

- creates the standard documentation at the project root from the template kit — all of it lands at the root, and free-form files are written soft-wrapped: never break a line mid-paragraph:

  - [`docs/README.md`](../templates/docs/README.md) → `README.md` at the project root — the project front page: badges, quick start, screenshot when present. Adapt the placeholders.
  - [`docs/CHANGELOG.md`](../templates/docs/CHANGELOG.md) → `CHANGELOG.md` at the project root — release history, `Unreleased` on top. Keep it as-is.
  - `LICENSE` — MIT or another license per need; follows the standard license format, hardwrapping allowed. No template: vendored verbatim from its official source.
  - [`docs/CONTRIBUTING.md`](../templates/docs/CONTRIBUTING.md) → `CONTRIBUTING.md` at the project root — contribution rules and the workflow that applies in this project. Adapt the placeholders and stack commands.
  - [`docs/CODE_OF_CONDUCT.md`](../templates/docs/CODE_OF_CONDUCT.md) → `CODE_OF_CONDUCT.md` at the project root — the Contributor Covenant. Replace only the contact placeholder.
  - [`docs/SECURITY.md`](../templates/docs/SECURITY.md) → `SECURITY.md` at the project root — how to report security vulnerabilities and the response policy. Replace the placeholders.

  `ARCHITECTURE.md` and `STRUCTURE.md` are not created here — Magic Context generates them during the first dream session; the AGENT does not hand-write them, and updates them only when the architecture or structure changes significantly.
- prepares the `.github` folder from the template kit:

  - [`.github/workflows/ci.yml`](../templates/.github/workflows/ci.yml) → `.github/workflows/ci.yml` — a CI `build` job that fails on `CRLF` line endings or a UTF-8 BOM in Markdown files. Copy it as-is.
  - [`.github/ISSUE_TEMPLATE/bug_report.md`](../templates/.github/ISSUE_TEMPLATE/bug_report.md), [`.github/ISSUE_TEMPLATE/feature_request.md`](../templates/.github/ISSUE_TEMPLATE/feature_request.md), and [`.github/ISSUE_TEMPLATE/config.yml`](../templates/.github/ISSUE_TEMPLATE/config.yml) → `.github/ISSUE_TEMPLATE/` — the issue templates with their labels (`bug`, `enhancement`) and a security contact link. Replace `<Project Name>` and `<owner>/<repo>` placeholders.
  - [`.github/PULL_REQUEST_TEMPLATE.md`](../templates/.github/PULL_REQUEST_TEMPLATE.md) → `.github/PULL_REQUEST_TEMPLATE.md` — the PR checklist for the project's standard verification (lint, code-style fixer on changed files, tests, build, route check). Fill in the placeholder commands for the project's stack.
  - [`.github/RELEASE_NOTES_TEMPLATE.md`](../templates/.github/RELEASE_NOTES_TEMPLATE.md) → `.github/RELEASE_NOTES_TEMPLATE.md` — the release notes structure per Release Notes Template. Replace `<Project Name>`, the version, and `<owner>/<repo>` placeholders.
  - [`.github/dependabot.yml`](../templates/.github/dependabot.yml) → `.github/dependabot.yml` — weekly updates for `composer`, `npm`, and `github-actions`. Keep only the ecosystems the project actually uses.

- creates `docs/IMPROVEMENTS.md` copied from the [`docs/IMPROVEMENTS.md`](../templates/docs/IMPROVEMENTS.md) template — the tracker for features, bugs, and optimization plans, whose items follow the lifecycle `recorded → verified → Issue → implemented → archived` and IDs `<LABEL_CODE>-<NNN>` (from the default GitHub labels). Each item is recorded under `## Items` using the `### <ID> — <Title>` skeleton from the template.
- applies the repository settings through `gh repo edit`, after getting USER approval first (non-file changes are never executed directly): set `delete_branch_on_merge=true`, enable all three merge methods (`--enable-merge-commit`, `--enable-squash-merge`, `--enable-rebase-merge`) — which one is used for a given PR is decided per case — and ensure Issues are enabled (`--enable-issues`).
- leaves Branch Protection on `main` out of the bootstrap — it becomes applicable once the repository is public and is applied at that point. Until then, the workflow itself keeps `main` safe: commits reach `main` only through reviewed pull requests, merged with explicit approval.

Before presenting, the AGENT scans every written Markdown file for mid-paragraph line breaks and unwraps any hardwrapped text. The AGENT **presents the prepared repository to the USER and stops**. No commit yet — this is the review gate.

## Commit and merge the first PR

USER approves the prepared repository and orders the AGENT to commit. The AGENT commits all files on the `chore/initial-setup` working branch, pushes it, and opens a pull request into `main` — no direct pushes to `main`. The AGENT waits for a green CI (if it fails, it fixes the code locally and pushes a new commit to the same branch), then merges the PR with `gh pr merge --squash --delete-branch --admin` (squash joins the entire setup into one clean commit on `main`), and verifies no sensitive data (tokens, `.env`, internal endpoints) is present in the history or tracked files.

The AGENT **presents the result to the USER and stops**.