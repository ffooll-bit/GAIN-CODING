# Code Implementation

Use this workflow when there are `verified` items in `docs/IMPROVEMENTS.md` ready to be worked on, or code changes approved by USER.

This document defines a sequence of interactions between the USER and the AGENT. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it.

Template files are linked from the template kit and are read only when the interaction that needs them is being executed — not during the initial read.

The source links resolve in the same repository as this document. To read a file's content, the AGENT fetches its raw version (raw.githubusercontent.com) from the source link.

## Plan the implementation

USER orders the AGENT to start working on the `verified` items in `docs/IMPROVEMENTS.md`. The AGENT works from the tracker items — their `Problem`, their `Actual Fix`, and their recorded Issue numbers (`#N`) — not directly from the open Issues on GitHub: another contributor may be working on the same Issue at the same time and may close or merge it first, so the AGENT checks each Issue's current status later, right before committing.

While still in plan mode, the AGENT:
- reads every item with `verified` status and briefly crosschecks it against the related code and documentation;
- assigns each item its own working branch `<prefix>/<kebab-case-description>` created from `main`, with the prefix chosen from `feature/`, `fix/`, `chore/`, `docs/`, `refactor/` — strictly one Issue per branch, per the Branching Model policy;
- determines the processing order, so an item that depends on another starts only after that item's pull request has been merged.

If the work has no tracker item, the AGENT plans its branch directly.

The AGENT presents the plan to the USER. The USER reviews and adjusts the plan until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to record it. The AGENT writes the approved plan to `temp/implementation-plan.md` so the following interactions can act on it:
- every planned item with its Issue number (`#N`) and its own working branch;
- the dependency notes — which items depend on which, so independent items can be implemented, merged, and released in any order while dependent ones follow the plan order;
- the PR and merge consequences — independent items each get their own PR handled freely; dependent items are opened and merged strictly in plan order, because stacked branches never exist: every branch always starts from `main`.

The AGENT **presents the recorded plan to the USER and stops**, ready to be moved to the Implement a branch interaction.

## Implement a branch

USER moves the AGENT back to plan mode and orders it to implement the next planned item. While still in plan mode, the AGENT reads the plan from `temp/implementation-plan.md`, takes the next unprocessed item, and verifies it with:
- **Code verification** — reading the current code in the editor to confirm where and how the problem lives.
- **Online verification** — checking the planned solution against official documentation or other online sources so it is not done the wrong way.
- **CI awareness** — reading the repository's CI configuration to learn every check a pull request must pass, so the fix plan satisfies all of them.

The AGENT then presents its fix plan for the item, and the USER reviews and adjusts until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT syncs local `main` (`git switch main`; `git pull origin main`), creates the item's branch from `main`, and implements the fix. The AGENT then runs locally the same checks the CI enforces — formatting, linting, tests, and the build — so the pull request passes CI. After the checks pass, the AGENT updates the tracker and the changelog so they are committed together with the code:
- the tracker item becomes `implemented` — the `Implemented` field records the current date and time, the `Actual Implemented` and `Changes` fields are filled, and the `Issue` field keeps the Issue number, not the PR number;
- a release note is added under the `[Unreleased]` category in `CHANGELOG.md` (format follows the [CHANGELOG template](../templates/docs/CHANGELOG.md)). If the work has no linked tracker item, only the release note applies.

No commit yet — this is the review gate. Before presenting anything, if the work has a linked Issue, the AGENT checks its current status with `gh issue view <#N>`:

- **Issue closed by someone else** → the AGENT **stops and presents the fact that the Issue was closed by another contributor to the USER for a decision** instead of proceeding.
- **Issue still open** → the AGENT **presents the behaviour preview and the proposed commit message to the USER and stops**. A behaviour preview describes the effect from the user's or the application's point of view (example: "login now shows a message when the token expires"), not file locations or code line details. Once the USER approves, the AGENT commits everything as one atomic commit — the code change together with the tracker update and the release note — then presents the result and stops. The next item starts only when USER orders it, and this interaction begins again in plan mode.

## Open the pull request

USER orders the AGENT to open the pull request for the finished branch. While still in plan mode, the AGENT checks the current state (the active branch and its sync with the remote), determines which branches need a pull request, and composes each PR:
- the title and the body following the project's Pull Request Template, including its checklist;
- the body linking the Issue (`Fixes #N`);
- the body written to a temporary file in `temp/` and applied with `--body-file` — always, never inline.

The AGENT presents the PR plan — how many PRs will be opened, each title, body, and checklist — and the USER reviews and adjusts until it is fixed. Nothing is committed in this interaction; the approval of this plan is what moves the work forward.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT pushes each finished branch (`git push -u origin <branch>`) and opens its pull request:

```
gh pr create --body-file <pr-body-file>
```

The AGENT **presents the opened pull requests to the USER and stops**.

## Merge the pull requests

USER orders the AGENT to merge the open pull requests. Open pull requests may come from this workflow or from other contributors; pull requests opened by bots, such as Dependabot's dependency updates, are not handled in this interaction. This workflow assumes every pull request it opens passes CI — the implementation interaction runs the same checks locally before committing. While still in plan mode, the AGENT lists every open PR it may merge and classifies each one by its mergeability (`gh pr view <#N> --json mergeable,mergeStateStatus`):
- **Ready to merge** — the CI checks are green and there are no conflicts.
- **Conflicted** — the resolution goes into the plan: the AGENT merges `main` into the feature branch, resolves the conflict locally, and pushes normally — never force-pushes and never rewrites history.

If a pull request still fails CI despite the local checks, fixing it belongs to another workflow — the AGENT presents this to the USER instead of proceeding.

The AGENT also determines the merge order that minimizes conflicts, and chooses the merge method according to the Merge Strategy policy, based on each PR's source and commit count — a PR opened by this workflow normally carries exactly 1 atomic commit, so it merges with squash; every other case follows the full decision matrix. If a USER instruction violates the agreed policies, the AGENT does not proceed: it explains the violation and recommends another action — the admin bypass selects the merge method, it never overrides a failed required check.

The AGENT presents the merge plan with its recommendations, and the USER reviews it, picks the order, and adjusts until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT executes the plan in the approved order — applying the planned fixes and conflict resolutions first, then merging each PR:

```
gh pr merge --squash --delete-branch --admin   # non-collaborator PR, or a PR with exactly 1 commit
gh pr merge --rebase --delete-branch --admin   # collaborator PR, branch authored by 1 person
gh pr merge --merge  --delete-branch --admin   # branch shared by 2+ collaborators
```

After merging, the AGENT cleans up: returns to `main` and syncs (`git switch main`; `git pull origin main`), deletes leftover local branches (`git branch -d`), and prunes stale remote references (`git fetch --prune`) — the remote branch is deleted automatically by the repository settings. When every planned item is processed, the AGENT also deletes `temp/implementation-plan.md`.

The AGENT **presents the merged results and the cleanup to the USER and stops**.
