# Release Process

> Trigger: The application version is ready to release following Semantic Versioning (SemVer) with version format `vMAJOR.MINOR.PATCH`.

1. USER asks for release `vX.Y.Z`. The AGENT makes sure the local `main` branch has been pulled and is exactly the same as the remote server, then edits `CHANGELOG.md` (format follows CHANGELOG): move all change lists under the `[Unreleased]` category down to the new version title with format `## [0.X.0] - YYYY-MM-DD`, create a new empty `[Unreleased]` block at the very top, then update the git comparison links at the bottom of the file. The AGENT **presents the edited changelog to the USER and stops**.
2. USER approves the changelog. The AGENT commits the change through a new branch and PR path:

```
git checkout -b chore/release-v0.X.0
git add CHANGELOG.md
git commit -m "chore: release v0.X.0"
```

Push the branch, create a PR, wait for a green CI, then merge via `--admin` with the method per Merge Strategy.
3. USER asks the AGENT to create and push the new version tag. The AGENT returns to the `main` branch, pulls, then creates and pushes the new version tag:

```
git switch main
git pull origin main
git tag v0.X.0
git push origin v0.X.0
```

4. USER asks the AGENT to create the official release. The AGENT creates the official release on the GitHub page using GitHub CLI, filling the release notes following the `.github/RELEASE_NOTES_TEMPLATE.md` template (see Release Notes Template):

```
gh release create v0.X.0 --title "v0.X.0" --notes-file release_notes.md
```

5. USER asks the AGENT to clean up temporary files. The AGENT deletes all temporary files or release notes no longer needed from the project workspace.