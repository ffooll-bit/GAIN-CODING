# Code Implementation

> Trigger: There is an Issue with `verified` status ready to work on, or code changes approved by USER.

1. USER orders the AGENT to work on Issue `#N`. The AGENT makes sure the local repository is up to date with the server, then creates a new branch from `main` with the appropriate prefix:

```
git switch main
git pull origin main
git checkout -b <prefix>/<kebab-case-description>
```

Prefix choices: `feature/`, `fix/`, `chore/`, `docs/`, `refactor/`. Complete 1 Issue per 1 branch. Merging multiple issues is only allowed if the scope and/or changed files are exactly the same for PR efficiency.
2. USER asks the AGENT to verify changes locally before committing. The AGENT runs verification per the project stack being worked on:
   - Syntax check or lint for the language used (example PHP: `php -l`; JavaScript/TypeScript: `eslint` / `tsc`).
   - Run the code-style fixer only on changed files (not globally, to avoid Windows CRLF line noise). Example PHP: `vendor/bin/php-cs-fixer fix`.
   - Run unit tests per the project runner (example PHP: `vendor/bin/phpunit`; Node: `npm test`).
   - Run the build if there are changes to UI, CSS, or JavaScript (example: `npm run build`).
   - Check URL/routing paths if application routes changed.
   - Re-check code compliance per the project `CONTRIBUTING` file: all output is escaped (anti-XSS) per the framework API, there is a CSRF token for every form, code is typed and documented per the language convention, and no debug code remains.
   - The verification checklist above is aligned with the checklist in the Pull Request Template — both must be consistent.
3. USER asks the AGENT to show a behaviour preview. The AGENT shows a behaviour preview to the chat log and **stops waiting for USER approval**. "Behaviour preview" means a description from the user's or the application effect's point of view (example: "login now shows a message when the token expires"), not file locations or code line details.
4. USER approves the preview. The AGENT commits following the Commit Messages format.
5. USER orders the AGENT to push and create a PR. The AGENT pushes the branch to GitHub and creates the Pull Request automatically via GitHub CLI:

```
gh pr create --body "Fixes #N"
```

If the PR body is long and contains special characters, use `--body-file` (see Boundaries, Windows Environment). Then the AGENT waits for CI (GitHub Actions build) to finish. If it fails, fix the code locally then push as a new commit (fixup commit) to the same branch. Do not amend.
6. USER orders merge after the CI indicator is green. The AGENT merges administratively using the admin bypass rights, with the method chosen per Merge Strategy:

```
gh pr merge --squash --delete-branch --admin   # non-collaborator PR, or a PR with only 1 commit
gh pr merge --rebase --delete-branch --admin   # collaborator PR, branch authored by 1 person
gh pr merge --merge  --delete-branch --admin   # branch shared by 2+ collaborators (team feature)
```

7. USER asks the AGENT to do bookkeeping. The AGENT returns to `main` and syncs (`git switch main`; `git pull origin main`). Update the `docs/IMPROVEMENTS.md` file to `implemented` and add a new release note under the `[Unreleased]` category in `CHANGELOG.md` (format follows CHANGELOG). Remember, these document changes also must go through the branch and PR process (no direct commits to `main`).