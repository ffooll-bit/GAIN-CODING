# Branching Model

The branch model is a policy about the arrangement of branches used by the project and how a change moves from one branch to another. The model chosen by a project is determined by its development needs and can change as development direction shifts; pick according to the selection table below.

## Branch Definitions

- **`main`** — the main and most stable branch. Its content matches the last released version. Releases are cut from `main`.
- **`dev` / `develop`** — the branch for working on the next release. Its content is a copy of `main` at creation time, then evolves as features are merged. This branch is created when the development direction or goal is clear and several features will be shipped in one release.
- **Working branches** — `feature/`, `release/`, `fix/`, `hotfix/`, and similar. Flexible branches, generally branched from `main`, but if `dev`/`develop` exists, working branches take from `dev`/`develop` first to work on one feature or one fix that will be part of the next release.

## Basic Flow

```
main  ──────────────────────────────────────────►  (matches last release)
  │
  └── dev ── feature / fix ──► dev ──► main        (next release work)
  │
  └── hotfix ──► main  ──► release (immediately)
       └──► dev  (merged back so dev carries the fix)
```

- Feature/fix changes are worked on in working branches branched from `dev`/`develop` when available, or from `main` when there is no `dev`.
- After finishing and passing review, the working branch is merged back into its source (`dev` or `main`).
- When `dev` is complete for the next release, `dev` is merged into `main` and the release is done from `main`.
- Critical bugs on an already-released version are handled through a separate **hotfix** path: branched from `main`, merged back to `main` (to be released immediately), then also merged into `dev` so the fix is carried into the next release.

## Model Selection Table

| Model | Branch arrangement | When to use |
|-------|--------------------|-------------|
| GitHub Flow (no `dev`) | `main` + working branches | Small / solo project, frequent deploys, development direction not yet clear, or unscheduled releases |
| main + dev | `main` + `dev` + working branches | Several features merged in one next release; development direction already clear |
| Full GitFlow | `main` + `dev` + `release/*` + `hotfix/*` | Scheduled releases needing parallel stability (big features, big team) |

Note: models with `dev` (rows two and three) use the same hotfix flow, only `release/*` is added in full GitFlow to stabilize one release without holding up the next feature work.