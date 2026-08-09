# Template Kit

This folder contains the reusable scaffolding for a new project following the GAIN-CODING workflow. Copy the relevant files into a new repository and adapt the placeholders (project name, stack, owner, commands) to that project.

## What to copy

| Source (here) | Destination (new project) |
|---------------|---------------------------|
| `guardrail/.gitignore` | project root (extend per stack) |
| `guardrail/.editorconfig` | project root |
| `guardrail/.gitattributes` | project root |
| `.github/dependabot.yml` | project root `.github/dependabot.yml` (keep only ecosystems used) |
| `.github/ISSUE_TEMPLATE/*` | project root `.github/ISSUE_TEMPLATE/` |
| `.github/PULL_REQUEST_TEMPLATE.md` | project root `.github/PULL_REQUEST_TEMPLATE.md` (adjust checklist to stack) |
| `.github/RELEASE_NOTES_TEMPLATE.md` | project root `.github/RELEASE_NOTES_TEMPLATE.md` |
| `.github/workflows/ci.yml` | project root `.github/workflows/ci.yml` (adjust jobs to stack) |
| `docs/IMPROVEMENTS.md` | project root `docs/IMPROVEMENTS.md` |

The workflow documents that explain how these files are used live in [Workflow Instructions](../README.md). The lifecycle for these artifacts is defined in Project Bootstrap (steps 1-3).

## Placeholders to adapt

- `<Project Name>` in the issue templates and release notes template.
- `<owner>/<repo>` in the dependabot `config.yml` and CHANGELOG links.
- Stack-specific commands (syntax check, lint, build, tests) in the PR template checklist and CI workflow.

Keep the file content consistent with the conventions in `../conventions/` — especially English language, no hardwrapping, and `UTF-8` without BOM.