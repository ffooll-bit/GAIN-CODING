# Dependabot PRs

> Trigger: Dependabot opens a dependency update PR (`composer`, `npm`, `github-actions`).

1. USER asks the AGENT to inspect the newly opened Dependabot PR. The AGENT inspects the PRs, classifies their risk (low: minor/patch versions, dev dependencies, or GitHub Actions with a green CI; high: major versions that could break old code), then **presents the list with risk classification to the USER and stops**.
2. USER orders a merge for the approved PRs:
   - Low Risk: The AGENT merges directly using the squash command (Dependabot is a non-collaborator bot, per Merge Strategy):

```
gh pr merge --admin --squash
```

   - High Risk: The AGENT runs local testing first:

```
gh pr checkout <dependabot-pr-number>
```

Run the local build command (like `npm run build`) because Dependabot only changes the manifest file and does not run the build/compile process (e.g. producing assets to a public folder). Test application functionality by the AGENT using the available tool or test script. If code adjustments are needed, commit directly to that Dependabot branch, make sure CI on GitHub is green again, then merge via squash (`--admin`), per Merge Strategy.

Note: A Dependabot PR changing files in `.github/workflows/` can only be merged if the gh token has the `workflow` scope. If it fails, refresh the token with: `gh auth refresh -h github.com -s workflow`.