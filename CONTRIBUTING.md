# Contributing to GAIN-CODING

This is a **personal-use** repository designed for a specific environment. By contributing you acknowledge that — see the [Disclaimer](README.md#disclaimer) before proceeding.

## How to Contribute

1. **Open an issue first.** No change happens without a supporting GitHub Issue. Use the standard issue templates for bug reports and feature requests.
2. **Branch from `main`.** Never push directly to `main`:
   ```
   git switch main
   git pull origin main
   git checkout -b <prefix>/<description>
   ```
3. **Make atomic commits.** Each commit is one logical change, following the [commit convention](playbook/conventions/commit-messages.md). No force-push.
4. **Open a pull request** that references the issue (`Fixes #N`).
5. **Await review and approval** before merge.

## Documentation Standards

- All documentation is written in International English, no hardwrapping, `UTF-8` without BOM, `LF` line endings. See the [documentation convention](playbook/conventions/documentation.md).
- Keep the decision matrices and selection tables consistent with the policies they explain.