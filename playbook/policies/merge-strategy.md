# Merge Strategy

The merge method choice is decided per PR, not per repository, with the following priority. Tie-breaker: the decision is made by who authored the PR first, then by the number of authors on the branch.

1. PR from a **non-collaborator** (external contributor, including bots such as Dependabot) → **squash merge** (`gh pr merge --squash --delete-branch`). The original commits on the branch are not controlled by this project, so the whole PR is joined into one clean commit on `main`.
2. PR from a **collaborator** with a branch authored by only 1 collaborator → **rebase merge** (`gh pr merge --rebase --delete-branch`). Atomic commits written with discipline are kept intact on top of `main`, and `main` history stays linear.
3. PR on a branch **shared by 2+ collaborators** (team feature worked on together) → **merge commit** (`gh pr merge --merge --delete-branch`). The integration trace of the collaboration is preserved as one unit; the accepted reasonable trade-off is that `main` is no longer purely linear in this scenario.
4. PR that **contains only 1 commit** → just **squash merge** (the result is identical to rebase for a single commit).

Note: the merge method and the branch model are two independent decisions. The merge method determines *how* one PR enters the target branch, while the branch model determines *from where to where* the PR flow goes (see Branching Model).

## Decision Matrix

| PR source | Branch authors | Method | Command |
|-----------|---------------|--------|---------|
| Non-collaborator (incl. bots) | any | squash | `gh pr merge --squash --delete-branch --admin` |
| Collaborator | 1 | rebase | `gh pr merge --rebase --delete-branch --admin` |
| Collaborator | 2+ | merge commit | `gh pr merge --merge --delete-branch --admin` |
| Any | 1 commit total | squash | `gh pr merge --squash --delete-branch --admin` |