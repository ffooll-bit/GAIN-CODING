# Data Security and Public Readiness

Use this workflow when the repository will be made public, there is a suspicion of sensitive data in the commit history, or the repository's public presentation needs polishing.

This document defines a sequence of interactions between the USER and the AGENT. Each `##` heading in this document is one interaction, and the interactions run in the order the headings appear. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it. Working interactions enforce two review gates: the plan-to-build gate — the AGENT presents its plan and waits for approval before executing — and the pre-commit gate — the AGENT presents its result and waits for approval before committing; interactions that only read and present use the single plan-mode review gate. Standard flow branches from and merges to `main`; when `dev`/`develop` exists per the Branching Model policy, the USER directs the AGENT to adapt branch origins and merge targets accordingly.

Template files are linked from the template kit and are read only when the interaction that needs them is being executed — not during the initial read.

## Audit and prepare the repository for public release

USER orders the AGENT to prepare the repository to be made public. First, while still in plan mode, the AGENT runs a pre-publication audit: it ensures no sensitive data (`.env` files, tokens, secrets, internal endpoints, or real institution names — plus passwords, internal logs, and database dumps as additional audit targets) remains — default or placeholder credentials (e.g., `changeme`, `root`, `password`) in database schemas, migrations, or example configs, and institution names in mockup or test data, are excluded. It is important to distinguish: Git history (all past commits) and log files (runtime artifacts) must both be free of sensitive data. The AGENT asks the USER which keywords or strings to search for — the USER knows what sensitive data is suspected — and runs the following checks as evidence:

```
# Search for sensitive keywords across the whole history
git log --all -S "<keyword>"
# Search for suspicious tracked files
git ls-files | Select-String -Pattern "(env|secret|key|credential|password)"
# Make sure .env was never tracked (output must be empty)
git log --all --oneline -- .env
```

The AGENT **presents the audit results to the USER and stops**. The USER reviews the results and chooses the next step:

- **Sensitive data found in past commits.** The plan is a Git history cleanup: the exposed secret is revoked and rotated first, then the history is rewritten. The USER can review and adjust the plan while staying in plan mode, then switches to build mode and orders the AGENT to work.
- **No sensitive data found.** There is nothing to purge. The USER switches to build mode and orders the AGENT to proceed to the next interaction.

When sensitive data was found, the AGENT revokes and rotates the exposed secret first and does not start the rewrite until the USER confirms the old credential no longer works — a rewritten history does not undo a leak that already happened. The AGENT then removes the sensitive data using `git-filter-repo` inside an isolated Python virtual environment. It creates the `temp/` folder when missing, clones the repository as an emergency mirror, writes the sensitive strings found in the audit into `rules.txt` **inside `temp/purge.git`** (one per line), and runs the cleanup:

```
# Clone the repository as an emergency mirror
git clone --mirror https://github.com/<owner>/<repo>.git temp/purge.git
cd temp/purge.git
# Run the cleanup using a Python virtual environment
python -m venv .venv
& .\.venv\Scripts\Activate.ps1
pip install git-filter-repo
git-filter-repo --replace-text rules.txt
# Verify the rewrite: no commit in this clone still carries the string
git log -S "<keyword>"
```

`git-filter-repo` removes the `origin` remote by default, so the AGENT re-adds it before pushing. This force push is ONLY allowed while the repository is STILL PRIVATE:

```
git remote add origin https://github.com/<owner>/<repo>.git
git push origin --force --all
git push origin --force --tags
```

After the force push, the AGENT syncs the local working repo: `git fetch --prune` to remove stale refs, then `git reset --hard origin/main` to reset to the new history, then verifies with `git log --all -S "<keyword>"` (not just `git log`) so no branch or tag ref still carries sensitive data. That check covers branches and tags only, and the AGENT says so instead of declaring the data gone: the old commits stay readable by their SHA in cached views on GitHub, through the pull requests that reference them, and in forks or clones taken before the rewrite. A full purge on GitHub's side needs a request through the GitHub Support portal — with the affected pull request numbers and the first changed commit `git-filter-repo` reported — and forks have to be deleted or cleaned by their owners. Last, the AGENT deletes the emergency mirror (`Remove-Item -Recurse -Force temp/purge.git`) so the sensitive history does not stay on disk. The AGENT **presents the purged repository to the USER and stops**. No visibility change yet — this is the review gate.

## Polish the public presentation

USER orders the AGENT to make the repository presentable according to community standards. While still in plan mode, the AGENT reads the current `README.md`, inspects the repository metadata with `gh repo view`, and prepares a polish plan covering four items:

- **Repository description** — one clear sentence shown on the repository page and in search results.
- **Homepage URL** — only when the project has a website or a documentation page.
- **Topics** — lowercase, hyphenated keywords that make the repository discoverable.
- **Social preview image** — `docs/social-preview.html` copied from the [`docs/social-preview.html`](../templates/docs/social-preview.html) template and adapted to the name, tagline, description, chips, and colors the USER chooses, rendered to `docs/social-preview.png` at 1280×640 using the Playwright browser tooling.

The AGENT drafts each item, presents the full plan to the USER and stops. The USER adjusts the drafts while staying in plan mode, then switches to build mode and orders the AGENT to work.

In build mode, the AGENT first edits `README.md` and creates the social preview files exactly as approved. The AGENT **presents the README diff and the created files to the USER and stops**. No commit yet — this is the review gate.

Once the USER approves the files, the AGENT commits the README change on a branch and merges it through a pull request — the commit never goes directly to `main`. The repository settings changes are non-file changes presented for approval before execution:

```
gh repo edit --description "<description>"
gh repo edit --homepage "<url>"
gh repo edit --add-topic <topic>
```

Uploading the social preview cannot be done through the GitHub CLI; if wanted, the optional upload is handled in the Make the repository public interaction, where the option becomes available. After everything is applied, the AGENT verifies the result with `gh repo view` and **presents the polished repository to the USER and stops**.

## Make the repository public

USER approves changing the repository status to public — a visibility change is a non-file change presented for USER approval first — and orders the AGENT to work. The AGENT runs the following GitHub CLI command:

```
gh repo edit --visibility public --accept-visibility-change-consequences
```

After the change, the AGENT applies Branch Protection on `main` through `gh api` after USER approval (a non-file change is never executed directly): enable Branch Protection, set the CI status check `build` as a required check, and set `enforce_admins=false`. Now that the repository is public, this applies on every plan — the AGENT verifies the settings with `gh api repos/{owner}/{repo}/branches/main/protection`. Optionally, the USER can also upload `docs/social-preview.png` in Settings → General → Social preview — GitHub only offers that option for public repositories, or private ones that already carry an uploaded image. The AGENT **presents the result to the USER and stops**.