# CI and Git Rescue

Use this workflow when CI fails on a pull request opened by the Code Implementation workflow or by another contributor, a commit accidentally lands on `main`, or a push is rejected.

This document defines a sequence of interactions between the USER and the AGENT. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it.

## Fix a failing CI check

USER reports a CI failure on a pull request. While still in plan mode, the AGENT inspects the failing check (`gh pr checks <#N>` shows the statuses, `gh run view <run-id> --log-failed` shows the failed logs), identifies the root cause, and presents its fix plan. The USER reviews and adjusts the plan until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT applies the fix locally and creates one new commit on the same branch — never amends an existing commit — then pushes it normally. GitHub reruns CI automatically, and the AGENT waits for the checks to pass (`gh pr checks <#N>`).

The AGENT **presents the passing checks to the USER and stops**.

## Salvage a commit landed on main

USER reports a commit that landed directly on `main`. While still in plan mode, the AGENT confirms the stray commit (`git log -1`) and presents its salvage plan: secure the work on a salvage branch first, then drop the commit from local `main`, then let the work return through a proper pull request.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT:

- secures the work before touching anything — `git branch chore/salvaged-work` while `main` still points at the stray commit;
- drops the commit from local `main` — `git reset --hard HEAD~1` (a higher number when more than one commit landed);
- pushes the salvage branch (`git push -u origin chore/salvaged-work`) and opens a pull request from it — the body written to a temporary file in `temp/` and applied with `--body-file`;
- waits for a green CI and merges with `gh pr merge --squash --delete-branch --admin`.

If the stray commit is already missing from local history, the AGENT finds its hash with `git reflog` and recovers it onto the salvage branch with `git cherry-pick <hash>` before pushing.

The AGENT **presents the salvaged result to the USER and stops**.

## Recover a rejected push

USER reports a `git push` that was rejected (non-fast-forward). While still in plan mode, the AGENT reads the rejection message and fetches the remote state (`git fetch --prune`) to diagnose the cause, then presents exactly one of two recovery plans:

- **Protection rejected a direct push to `main`** — direct pushes are forbidden anyway, so the AGENT does not retry. The commits move onto a working branch named per the Branching Model policy, are pushed there, and return through the normal pull request path — open the PR, wait for a green CI, and merge.
- **Local history diverged from `origin/main`** — the local branch carries commits the remote does not have. Anything valuable is salvaged first, as described in the Salvage a commit landed on main interaction; then the local branch is discarded and synced:

```
git reset --hard origin/main
```

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT executes the chosen recovery and verifies the branch state matches the plan (`git status`; `git log --oneline -5`).

The AGENT **presents the recovered repository to the USER and stops**.
