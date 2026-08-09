# Project Bootstrap

Use this workflow when USER wants to create a new project and asks the AGENT to prepare the repository structure from scratch.

This document defines a sequence of interactions between the USER and the AGENT. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it.

Template files are linked from the template kit and are read only when the interaction that needs them is being executed — not during the initial read.

The source links resolve in the same repository as this document. To read a file's content, the AGENT fetches its raw version (raw.githubusercontent.com) from the source link.

## Prepare the project

USER orders the AGENT to prepare the new project. Before creating anything, the AGENT asks the USER clarifying questions using the question tool — the project's purpose, name, programming language or stack, and anything else that affects which templates to copy and adapt — because the AGENT does not yet know what the USER wants to build. The AGENT waits for the answers, then presents the adjusted plan to the USER while still in plan mode. The USER reviews and adjusts the plan until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT:

- initializes the repository: create it on GitHub (`gh repo create`) and set it up locally
- creates the base folder structure: `docs/` (documentation), `temp/` (temporary work folder, gitignored), the source code folder (`app/`, `src/`, or per stack) if the project actually has application code
- creates the guardrail files at the root from the template kit:

  - [`.gitignore`](../templates/guardrail/.gitignore) → project root — ignores OS, editor, Python, and Node artifacts, the `temp/` folder, and local logs. Extend it with ignores specific to the project's stack.
  - [`.editorconfig`](../templates/guardrail/.editorconfig) → project root — enforces `LF` line endings and `UTF-8` without BOM. Keep it as-is.
  - [`.gitattributes`](../templates/guardrail/.gitattributes) → project root — normalizes line endings to `LF` on checkout. Keep it as-is.

- creates the standard documentation following Standard Docs and CHANGELOG: `README.md`, `CHANGELOG.md`, `LICENSE`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, and `SECURITY.md` — `ARCHITECTURE.md` and `STRUCTURE.md` are not created here (Magic Context generates them during the first dream session; the AGENT does not hand-write them)
- prepares the `.github` folder from the template kit:

  - [`.github/workflows/ci.yml`](../templates/.github/workflows/ci.yml) → `.github/workflows/ci.yml` — a CI `build` job that fails on `CRLF` line endings or a UTF-8 BOM in Markdown files. Copy it as-is.
  - [`.github/ISSUE_TEMPLATE/bug_report.md`](../templates/.github/ISSUE_TEMPLATE/bug_report.md), [`feature_request.md`](../templates/.github/ISSUE_TEMPLATE/feature_request.md), and [`config.yml`](../templates/.github/ISSUE_TEMPLATE/config.yml) → `.github/ISSUE_TEMPLATE/` — the issue templates with their labels (`bug`, `enhancement`) and a security contact link. Replace `<Project Name>` and `<owner>/<repo>` placeholders.
  - [`.github/PULL_REQUEST_TEMPLATE.md`](../templates/.github/PULL_REQUEST_TEMPLATE.md) → `.github/PULL_REQUEST_TEMPLATE.md` — the PR checklist aligned with the verification steps of Code Implementation. Fill in the placeholder commands for the project's stack.
  - [`.github/RELEASE_NOTES_TEMPLATE.md`](../templates/.github/RELEASE_NOTES_TEMPLATE.md) → `.github/RELEASE_NOTES_TEMPLATE.md` — the release notes structure per Release Notes Template. Replace `<Project Name>` and pointer placeholders.
  - [`.github/dependabot.yml`](../templates/.github/dependabot.yml) → `.github/dependabot.yml` — weekly updates for `composer`, `npm`, and `github-actions`. Keep only the ecosystems the project actually uses.

- creates `docs/IMPROVEMENTS.md` with the lifecycle skeleton and ID scheme (see IMPROVEMENTS Structure), using [docs/IMPROVEMENTS_ITEM_TEMPLATE.md](../templates/docs/IMPROVEMENTS_ITEM_TEMPLATE.md) as the item reference
- applies all GitHub protection settings from Repository Protection, after getting USER approval per the "Approval of Non-File Changes" boundary, including enabling all three merge methods per Merge Strategy, and ensures Issues are enabled

The AGENT **presents the prepared repository to the USER and stops**. No commit yet — this is the review gate.

## Commit and open the first PR

USER approves the prepared repository and orders the AGENT to commit. The AGENT commits all files, opens the first PR (not directly to `main`), waits for a green CI, and ensures the repository is ready to be published (see Data Security and Public Readiness).

The AGENT **presents the result to the USER and stops**.