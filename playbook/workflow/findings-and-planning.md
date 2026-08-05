# Findings and Planning

> Trigger: Every time there is a new feature idea, a found bug, or an optimization plan when USER is discussing with the AGENT.

1. USER tells the idea or finding to the AGENT. The AGENT records the idea or finding into the `docs/IMPROVEMENTS.md` file. The AGENT uses IDs in the format `<LABEL>-<NNN>` (Example: `ENH-001` for enhancement, `BUG-002` for bug, `DOC-003` for documentation — see IMPROVEMENTS Structure). The AGENT writes the Problem and Possible Fix details. The AGENT sets the initial status to `recorded`.
2. USER asks the AGENT to prepare a verification plan for all items in `docs/IMPROVEMENTS.md`. The AGENT composes a verification plan for all items with `recorded` status and shows the plan to verify ideas or findings, grouping ideas/findings that can be verified at once (because they share the same file and/or scope) into the same batch.
3. USER asks the AGENT to start batch verification. The AGENT verifies the items in that batch, one by one, in 3 stages:
   - Stage 1 (Code Verification): The AGENT checks the original code in the editor to confirm the problem is valid.
   - Stage 2 (Online Verification): The AGENT verifies the planned solution against official documentation or other online sources so it does not go the wrong way.
   - Stage 3 (Present Results): The AGENT stops and presents the results.
4. If the Result is Valid: USER asks the AGENT to create a new GitHub Issue using GitHub CLI (`gh issue create`). The AGENT applies the default GitHub label matching the ID code (e.g. `enhancement`, `documentation`, `bug`). The AGENT makes sure the item ID is written in the issue title, not used as a custom label. The AGENT fills the issue body with complete technical details, then updates the status in the markdown to `verified` along with the Issue number: `#N` and Actual Fix. The AGENT stops, then presents the results to the USER.

   If the Result is Invalid: The AGENT directly changes the status in the markdown to `rejected` and does not continue to the next tracking process. The AGENT stops, then explains why the item was rejected.
5. USER asks for `docs/IMPROVEMENTS.md` to be archived. The AGENT copies `docs/IMPROVEMENTS.md` to `docs/archived/IMPROVEMENT_YYYY-MM-DD.md`, then clears the `docs/IMPROVEMENTS.md` file again for the next batch. Note: to resolve items with `verified` status, see Code Implementation; after the issue is merged, the item status becomes `implemented` and the Issue column keeps recording the Issue number, not the PR number.