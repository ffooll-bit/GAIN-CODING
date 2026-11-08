# Boundaries

## Communication Language

Conversation, discussion, and work instructions between USER and AGENT follow the language the USER uses. However, all text that goes into project documentation files (code, script comments, Git commit messages, issue body, `docs/IMPROVEMENTS.md`, `README.md`, and other formal documents) must be written in International English because it will be read publicly. UI element language is flexible and follows USER requests. Content written into the agent's project memory (Magic Context) is also in International English.

## Line Wrapping Rule (No Hardwrapping)

In general, manually cutting paragraph text lines inside markdown documents is strictly forbidden. Let text lines continue continuously so reading in modern web/editors stays tidy. Line breaks are only allowed to separate rows in tables, bullet points, or code blocks. An exception is given to documents that conventionally use hardwrapping, for example `LICENSE` which follows the standard format; otherwise use softwrapping.

## Line Endings

All text files use `LF` line endings, enforced by the guardrail files (`.editorconfig` and `.gitattributes`) and re-verified by the CI `build` job.

## Auto-Stop Between Batches

Every time the AGENT finishes one round of code verification or feature implementation within a batch, the system must stop execution completely at the end of that batch. The AGENT is forbidden from continuing to the next work group without approval or a new command from the USER.

## Approval of Non-File Changes

GitHub settings changes that are not commits (branch protection, visibility, labels, repo settings) are executed via `gh api` / `gh repo edit` and must be presented for USER approval first — never executed directly without confirmation.

## Ask When Ambiguous

If an instruction is unclear, or there is a big choice with significant trade-offs, ask the USER before acting. Do not assume.

## Decision Frameworks

Where a process offers multiple valid paths, prefer a decision matrix or selection table over free-form rules so the choice is deterministic. Keep such tables alongside the process that uses them.

## Work Folder Isolation

External tools (CLI tools, linters, etc.) that need to be installed inside a project follow project convention (install location is determined by project type, e.g. virtual environment) and go into `.gitignore` — installing globally is forbidden. Temporary files (logs, notes, scratch) are written to a `temp/` folder inside the project that is in `.gitignore`, not scattered outside the project.

## Windows Environment

The AGENT runs on Windows, where the shell is PowerShell. Every command it executes — from a workflow step or from its own initiative — is written in PowerShell syntax for Windows, even when the documentation it follows, an upstream README, or a tool's official example uses bash; copying a Linux command and hoping it works is the failure this section exists to prevent. This applies to commands the AGENT runs, not to files whose target is another platform: a GitHub Actions job that declares `runs-on: ubuntu-latest` stays bash. Assume Windows PowerShell 5.1 unless the session confirms PowerShell 7, because `&&` and the `utf8NoBOM` encoding name do not exist in 5.1. Translate a command from an external source before running it — the common cases:

| Bash / Linux | PowerShell on Windows |
|--------------|----------------------|
| `export NAME=value` | `$env:NAME = "value"` |
| `command1 && command2` | `command1; if ($LASTEXITCODE -eq 0) { command2 }` — `&&` works only in PowerShell 7+, so never rely on it |
| Line continuation with a trailing `\` | Line continuation with a trailing backtick, which must be the last character on the line |
| `rm -rf <path>` | `Remove-Item -Recurse -Force <path>` |
| `cp -r <a> <b>` | `Copy-Item -Recurse <a> <b>` |
| `grep -r "<text>" .` | `Get-ChildItem -Recurse \| Select-String -Pattern "<text>"` |
| `sed -i` / `awk` in-place rewrite | the editor tool, not a shell one-liner |
| Heredoc `cat > <file> <<'EOF'` … `EOF` | Here-string in a variable: `@'` on its own line, the text, `'@` on its own line, then `\| [System.IO.File]::WriteAllText(<path>, <text>)` |
| `git commit -m "title\n\nbody"` | `git commit -m "title" -m "body"` |
| `chmod +x <file>` | Not needed; run the script through its interpreter, e.g. `python <file>.py` |

Multi-line text — a commit message with a body, an issue or pull request body, file content — is never written with `\n` inside a double-quoted string, because PowerShell stores a literal backslash and `n`; use a here-string or the editor tool. A script the AGENT writes for its own use is a PowerShell script (`.ps1`), never a `.sh` one. Running a `.ps1` file, including a virtual environment's `Activate.ps1`, can be blocked by the PowerShell execution policy; allow it for that session with `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` and let the permission expire with the session instead of changing the machine policy.

Read a command's result from `$LASTEXITCODE` and the command's own output, not from how the console paints it: in PowerShell 5.1, `2>&1` turns a successful command's stderr into an error record, so a successful command can report a non-zero exit while a quietly failing one can look harmless. When the result is ambiguous, check the effect itself — the remote state, the file, the branch — before retrying or repairing a state that was never broken.

All source files are stored as `UTF-8` without `BOM` (unless truly necessary) because other encodings such as `Windows-1252` produce broken characters or parse errors. Writing a file from a command is where a BOM comes back: `Set-Content -Encoding UTF8` and `Out-File` add one on Windows PowerShell 5.1, so write with `[System.IO.File]::WriteAllText(<path>, <text>)`, which defaults to UTF-8 without BOM, or with `Set-Content -Encoding utf8NoBOM` on PowerShell 7+, and prefer the editor tool for authoring files. Do not assume default commands (`cmd`/`PowerShell`) behave like `bash`, because much documentation is written for Linux; if a command fails in an unusual way, first check whether the shell/OS difference is the cause before changing code. `gh` arguments containing backticks or special characters often break when inserted directly in PowerShell; for long issue/PR bodies, write the body to a temporary file in `temp/` then use the `--body-file` option.