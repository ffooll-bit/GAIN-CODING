# Release Process

Use this workflow when USER wants to release a version following Semantic Versioning (SemVer), with version format `vMAJOR.MINOR.PATCH`.

This document defines a sequence of interactions between the USER and the AGENT. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it.

Template files are linked from the template kit and are read only when the interaction that needs them is being executed — not during the initial read.

The source links resolve in the same repository as this document. To read a file's content, the AGENT fetches its raw version (raw.githubusercontent.com) from the source link.

## Prepare the changelog

USER orders the release of the next version. While still in plan mode, the AGENT syncs local `main` first (`git switch main`; `git pull origin main`), reads `CHANGELOG.md`, and assesses the `[Unreleased]` section before planning any edit:

- **Release readiness** — checking every entry under `[Unreleased]`: is there anything worth releasing? An empty or insignificant section means the AGENT **stops and presents that verdict** instead of proceeding.
- **Version classification** — deciding which part of `vMAJOR.MINOR.PATCH` grows, per Semantic Versioning: breaking changes grow MAJOR, backward-compatible new functionality grows MINOR, backward-compatible fixes grow PATCH.

The AGENT presents its assessment with a recommended version, and the USER confirms the release and its version — or cancels. The AGENT then presents its edit plan for the confirmed version:

- every change listed under `[Unreleased]` moves down beneath a new version title `## [X.Y.Z] - YYYY-MM-DD`, dated today;
- a fresh empty `[Unreleased]` block returns to the very top;
- the comparison links at the bottom of the file gain an entry for the new version.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT edits `CHANGELOG.md` exactly as planned — the format follows the [CHANGELOG template](../templates/docs/CHANGELOG.md).

The AGENT **presents the edited changelog to the USER and stops**.

## Release the changelog through a pull request

USER approves the edited changelog. While still in plan mode, the AGENT presents its delivery plan: one `chore/release-vX.Y.Z` branch created from `main`, carrying a single atomic commit that touches only `CHANGELOG.md`, returned through the standard pull request path — never a direct push.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT:

- creates the branch from `main`, carrying the pending changelog edit — `git switch -c chore/release-vX.Y.Z`; `git add CHANGELOG.md`; `git commit -m "chore: release vX.Y.Z"`;
- pushes the branch (`git push -u origin chore/release-vX.Y.Z`) and opens a pull request — the body written to a temporary file in `temp/` and applied with `--body-file`;
- waits for a green CI (a failing check follows the CI and Git Rescue workflow) and merges with `gh pr merge --squash --delete-branch --admin`.

The AGENT **presents the merged release PR to the USER and stops**.

## Tag and publish the release

USER asks the AGENT to publish the released version. While still in plan mode, the AGENT confirms local `main` carries the merged release PR (`git log -1`) and that the tag name matches the merged version, then drafts the release notes following the [`.github/RELEASE_NOTES_TEMPLATE.md`](../templates/.github/RELEASE_NOTES_TEMPLATE.md) template, written to `temp/release_notes.md`. The USER reviews the draft until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT tags the freshly merged `main` and creates the official GitHub release:

```
git switch main
git pull origin main
git tag vX.Y.Z
git push origin vX.Y.Z
gh release create vX.Y.Z --title "vX.Y.Z" --notes-file temp/release_notes.md
```

When the release is live, the AGENT deletes `temp/release_notes.md` and any other temporary files this workflow produced.

The AGENT **presents the published release to the USER and stops**.
