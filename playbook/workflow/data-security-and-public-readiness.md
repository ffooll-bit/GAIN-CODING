# Data Security and Public Readiness

> Trigger: The repository will be made public, or there is a suspicion of sensitive data in the commit history.

1. USER asks to prepare the repository for public. The AGENT runs a pre-publication audit: make sure no sensitive data (such as tokens, passwords, `.env` files, commit history containing logs/internal, database dumps, or real institution names) is left behind. It is important to distinguish: Git history (all past commits) and log files (runtime artifacts) must both be free of sensitive data. The AGENT runs the following checks as evidence:

```
# Search for sensitive keywords across the whole history
git log --all -S "<keyword>"
# Search for suspicious tracked files
git ls-files | Select-String -Pattern "(env|secret|key|credential|password)"
# Make sure .env was never tracked (output must be empty)
git log --all --oneline -- .env
```

The AGENT **presents the audit results to the USER and stops**.
2. If sensitive data is found in past commits, USER approves a permanent Git history cleanup. The AGENT removes it using `git-filter-repo` inside an isolated Python virtual environment:

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
git log -S "your-sensitive-keyword"
# Force push the new history to the remote server
# IMPORTANT: This force push is ONLY allowed while the repo is STILL PRIVATE
git push origin --force --all
git push origin --force --tags
```

After the force push, the AGENT syncs the local working repo: remove stale refs (`git fetch --prune`) and reset to the new history, then verify with `git log --all -S "keyword"` (not just `git log`) to make sure no ref still carries sensitive data. The AGENT **presents the results and stops**.
3. USER approves changing the repo status to public. The AGENT runs the following GitHub CLI command:

```
gh repo edit --visibility public --accept-visibility-change-consequences
```

Immediately re-enable all Branch Protection settings on the `main` branch as soon as the repository status changes to public. The AGENT presents the final result to the USER.