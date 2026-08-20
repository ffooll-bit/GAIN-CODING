# Data Security and Public Readiness

Use this workflow when the repository will be made public, or there is a suspicion of sensitive data in the commit history.

This document defines a sequence of interactions between the USER and the AGENT. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it.

## Audit and prepare the repository for public release

USER orders the AGENT to prepare the repository to be made public. First, while still in plan mode, the AGENT runs a pre-publication audit: it ensures no sensitive data (`.env` files, tokens, secrets, internal endpoints, or real institution names — plus passwords, internal logs, and database dumps as additional audit targets) remains. It is important to distinguish: Git history (all past commits) and log files (runtime artifacts) must both be free of sensitive data. The AGENT asks the USER which keywords or strings to search for — the USER knows what sensitive data is suspected — and runs the following checks as evidence:

```
# Search for sensitive keywords across the whole history
git log --all -S "<keyword>"
# Search for suspicious tracked files
git ls-files | Select-String -Pattern "(env|secret|key|credential|password)"
# Make sure .env was never tracked (output must be empty)
git log --all --oneline -- .env
```

The AGENT **presents the audit results to the USER and stops**. The USER reviews the results and chooses the next step:

- **Sensitive data found in past commits.** The plan is a permanent Git history cleanup. The USER can review and adjust the plan while staying in plan mode, then switches to build mode and orders the AGENT to work.
- **No sensitive data found.** There is nothing to purge. The USER switches to build mode and orders the AGENT to proceed with the Make the repository public interaction.

When sensitive data was found, the AGENT removes the sensitive data using `git-filter-repo` inside an isolated Python virtual environment. It creates the `temp/` folder when missing, clones the repository as an emergency mirror, writes the sensitive strings found in the audit into `rules.txt` (one per line), and runs the cleanup:

```
# Clone the repository as an emergency mirror
git clone --mirror https://github.com/<owner>/<repo>.git temp/purge.git
cd temp/purge.git
# Run the cleanup using a Python virtual environment
python -m venv .venv
& .\.venv\Scripts\Activate.ps1
pip install git-filter-repo
git-filter-repo --replace-text rules.txt
# Make sure the sensitive string is truly gone from the log
git log -S "<keyword>"
```

`git-filter-repo` removes the `origin` remote by default, so the AGENT re-adds it before pushing. This force push is ONLY allowed while the repository is STILL PRIVATE:

```
git remote add origin https://github.com/<owner>/<repo>.git
git push origin --force --all
git push origin --force --tags
```

After the force push, the AGENT syncs the local working repo: `git fetch --prune` to remove stale refs, then `git reset --hard origin/main` to reset to the new history, then verifies with `git log --all -S "<keyword>"` (not just `git log`) to make sure no ref still carries sensitive data. The AGENT **presents the purged repository to the USER and stops**. No visibility change yet — this is the review gate.

## Make the repository public

USER approves changing the repository status to public — a visibility change is a non-file change presented for USER approval first — and orders the AGENT to work. The AGENT runs the following GitHub CLI command:

```
gh repo edit --visibility public --accept-visibility-change-consequences
```

After the change, the AGENT verifies the protection settings are still intact (Branch Protection on `main`, the `build` required check, and the three merge methods). The AGENT **presents the result to the USER and stops**.