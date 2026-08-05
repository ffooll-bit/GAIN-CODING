# CI and Git Rescue

> Trigger: CI fails on a PR, a commit accidentally lands on `main`, or a push is rejected (non-fast-forward).

1. USER reports CI failure on a PR. The AGENT fixes the code locally, then creates a new commit on that branch and pushes. CI on GitHub will detect and rerun the tests automatically. The AGENT waits for the result and **presents the result to the USER, then stops**.
2. USER reports a commit that accidentally landed on `main` (e.g. a commit without a separate branch). The AGENT immediately reverts and secures the work into a new branch:

```
git reset --hard HEAD~1
git checkout -b chore/salvaged-work
```

If the commit is missing from local history, recover it from the reflog:

```
git cherry-pick <commit-sha-from-reflog>
```

After that, the AGENT pushes that branch, creates a PR, waits for a green CI, and merges via `--admin` with the method per Merge Strategy. The AGENT **presents the result to the USER, then stops**.
3. USER reports a rejected push (non-fast-forward). The AGENT checks the cause. If it is due to branch protection rules, use the PR path. If it is due to a sync history difference with the remote server, reset the local branch to be exactly the same as `origin`:

```
git fetch origin
git reset --hard origin/main
```

The AGENT **presents the result to the USER, then stops**.