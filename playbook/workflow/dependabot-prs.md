# Dependabot PRs

Use this workflow when Dependabot opens a dependency update pull request (`composer`, `npm`, `github-actions`).

This document defines a sequence of interactions between the USER and the AGENT. Each `##` heading in this document is one interaction, and the interactions run in the order the headings appear. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it. Working interactions enforce two review gates: the plan-to-build gate — the AGENT presents its plan and waits for approval before executing — and the pre-commit gate — the AGENT presents its result and waits for approval before committing; interactions that only read and present use the single plan-mode review gate. Standard flow branches from and merges to `main`; when `dev`/`develop` exists per the Branching Model policy, the USER directs the AGENT to adapt branch origins and merge targets accordingly.

## Classify the open Dependabot pull requests

USER asks the AGENT to inspect the newly opened Dependabot pull requests. While still in plan mode, the AGENT lists them (`gh pr list --author "app/dependabot"`), inspects each one (`gh pr view <#N>` shows the changed files and versions, `gh pr checks <#N>` shows the check states), and classifies its risk:

- **Low risk** — minor or patch version bumps, dev-dependency updates, or GitHub Actions updates whose checks are already green;
- **High risk** — major version bumps that could break existing code.

The AGENT **presents the classified list with a merge recommendation for each PR to the USER and stops**.

## Merge the approved pull requests

USER orders the merge for the approved pull requests. While still in plan mode, the AGENT presents its merge plan per PR:

- **Low risk merges directly** — Dependabot is a non-collaborator bot, so the Merge Strategy policy maps it to squash: `gh pr merge <#N> --squash --delete-branch --admin`;
- **High risk is tested locally first** — `gh pr checkout <#N>`, then run the project's build and tests, because Dependabot only edits the manifest files and never runs the build itself. If the code needs adjustments, the AGENT commits them onto the Dependabot branch and pushes, waits for a green CI (a failing check follows the CI and Git Rescue workflow), and only then merges with the same squash command.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT executes the plan PR by PR and verifies each merge result.

A pull request touching `.github/workflows/` merges only when the GitHub CLI token carries the `workflow` scope — if the merge fails for that reason, the AGENT refreshes it first: `gh auth refresh -h github.com -s workflow`.

The AGENT **presents the merge results to the USER and stops**.
