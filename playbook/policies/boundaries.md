# Boundaries

## Communication Language

Conversation, discussion, and work instructions between USER and AGENT follow the language the USER uses. However, all text that goes into project documentation files (code, script comments, Git commit messages, issue body, `IMPROVEMENTS.md`, `README`, and other formal documents) must be written in International English because it will be read publicly. UI element language is flexible and follows USER requests. Content written into the agent's project memory (Magic Context) is also in International English.

## Line Wrapping Rule (No Hardwrapping)

In general, manually cutting paragraph text lines inside markdown documents is strictly forbidden. Let text lines continue continuously so reading in modern web/editors stays tidy. Line breaks are only allowed to separate rows in tables, bullet points, or code blocks. An exception is given to documents that conventionally use hardwrapping, for example `LICENSE` which follows the standard format; otherwise use softwrapping.

## Auto-Stop Between Batches

Every time the AGENT finishes one round of code verification or feature implementation within a batch, the system must stop execution completely at the end of that batch. The AGENT is forbidden from continuing to the next work group without approval or a new command from the USER.

## Approval of Non-File Changes

GitHub settings changes that are not commits (branch protection, visibility, labels, repo settings) are executed via `gh api` / `gh repo edit` and must be presented for USER approval first — never executed directly without confirmation.

## Ask When Ambiguous

If an instruction is unclear, or there is a big choice with significant trade-offs, ask the USER before acting. Do not assume.

## Work Folder Isolation

External tools (CLI tools, linters, etc.) that need to be installed inside a project follow project convention (install location is determined by project type, e.g. virtual environment) and go into `.gitignore` — installing globally is forbidden. Temporary files (logs, notes, scratch) are written to a `temp/` folder inside the project that is in `.gitignore`, not scattered outside the project.

## Windows Environment

All source files are stored as `UTF-8` without `BOM` (unless truly necessary) because other encodings such as `Windows-1252` produce broken characters or parse errors. Do not assume default commands (`cmd`/`PowerShell`) behave like `bash`, because much documentation is written for Linux; if a command fails in an unusual way, first check whether the shell/OS difference is the cause before changing code. `gh` arguments containing backticks or special characters often break when inserted directly in PowerShell; for long issue/PR bodies, write the body to a temporary file in `temp/` then use the `--body-file` option.