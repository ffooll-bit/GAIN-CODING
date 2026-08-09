# Repository Protection

The repository must be configured as follows:

- Enable **Branch Protection** on `main`.
- Enable the CI status check **`build`** as a required check (require status checks to pass) before merge.
- Set **`enforce_admins=false`** so the AGENT can merge PRs on behalf of the USER using the `--admin` bypass. Note: `enforce_admins=false` technically lets an admin push directly to `main` (protection is not applied to admins). Therefore No Direct Push remains a binding SOP rule for whoever executes git commands (including the AGENT acting on behalf of an admin): direct push to `main` is forbidden, and the only technical bypass is `gh pr merge --admin`.
- Enable **`delete_branch_on_merge=true`** to delete the branch automatically after merge.
- Enable all three merge methods (`allow_merge_commit=true`, `allow_squash_merge=true`, `allow_rebase_merge=true`). Which method is used for a given PR is decided per case, not as one fixed method for all PRs.
- Enable **Dependabot** to check updates periodically (weekly) on relevant ecosystems (`composer`, `npm`, `github-actions`).