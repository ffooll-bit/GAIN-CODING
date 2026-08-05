# Workflow Instructions

This is the entry point for an AI coding agent operating on this repository or any project that follows it. Read this in full before taking any action.

## Before You Act

You operate under binding rules. Read these before modifying anything, creating a branch, or opening an issue/PR:

- [Core Rules](policies/core-rules.md) — the workflow principles that cannot be bypassed.
- [Repository Protection](policies/repository-protection.md) — GitHub settings that lock in the rules.
- [Merge Strategy](policies/merge-strategy.md) — how a PR enters the target branch, per case.
- [Branching Model](policies/branching-model.md) — branch arrangement and selection table.
- [Boundaries](policies/boundaries.md) — communication language, line wrapping, approvals, environment.

## Operating Procedures

Each flow is triggered by a specific situation. Follow the applicable one:

- [Project Bootstrap](workflow/project-bootstrap.md) — create a new repository from scratch.
- [Workflow Adoption](workflow/workflow-adoption.md) — bring an existing repository up to standard.
- [Findings and Planning](workflow/findings-and-planning.md) — record, verify, and track ideas/findings.
- [Code Implementation](workflow/code-implementation.md) — implement a verified Issue through branch and PR.
- [CI and Git Rescue](workflow/ci-and-git-rescue.md) — recover from CI failure, stray commits, rejected pushes.
- [Dependabot PRs](workflow/dependabot-prs.md) — classify and merge dependency update PRs.
- [Release Process](workflow/release-process.md) — cut a SemVer release end to end.
- [Data Security and Public Readiness](workflow/data-security-and-public-readiness.md) — audit and clean sensitive data before going public.

## Format Conventions

The documents and configuration you produce must follow these specs:

- [Documentation](conventions/documentation.md) — language, wrapping, encoding, line endings.
- [Commit Messages](conventions/commit-messages.md) — Conventional Commits rules.
- [CHANGELOG](conventions/changelog.md) — Keep a Changelog format.
- [Standard Docs](conventions/standard-docs.md) — README, LICENSE, CONTRIBUTING, and friends.
- [IMPROVEMENTS Structure](conventions/improvements-structure.md) — tracker lifecycle, ID scheme, item template.

## Scaffolding

Use [templates/](templates/README.md) to scaffold a new project, copying and adapting the guardrail files, `.github` configuration, and item templates following the linked steps above.

## Reference Environment

The workflow was validated against this environment: Windows OS, Visual Studio Code, OpenCode, Magic Context, GitHub CLI (`gh`), the DeepSeek V4 Flash Free model, and the ponytail skill. Fit is not guaranteed outside it — see the disclaimer in [README.md](../README.md#disclaimer).