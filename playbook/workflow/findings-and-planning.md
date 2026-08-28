# Findings and Planning

Use this workflow when USER wants to record a new feature idea, a bug, or an optimization plan during a discussion with the AGENT.

This document defines a sequence of interactions between the USER and the AGENT. Each `##` heading in this document is one interaction, and the interactions run in the order the headings appear. Each interaction ends with the AGENT presenting the result and stopping; the next starts only when the USER orders it. Working interactions enforce two review gates: the plan-to-build gate — the AGENT presents its plan and waits for approval before executing — and the pre-commit gate — the AGENT presents its result and waits for approval before committing; interactions that only read and present use the single plan-mode review gate.

The whole cycle works on a working branch `docs/improvements`. It is created from `main` when the first approved change is committed, and every review gate commits onto it. Its changes reach `main` only through the Deliver the updates through a pull request interaction — after each delivery, the branch is recreated from updated `main` when new changes need committing again.

Template files are linked from the template kit and are read only when the interaction that needs them is being executed — not during the initial read.

The source links resolve in the same repository as this document. To read a file's content, the AGENT fetches its raw version (raw.githubusercontent.com) from the source link.

## Record the idea or finding

USER tells the idea or finding to the AGENT. While still in plan mode, the AGENT responds with a brief analysis and rewrites the idea or finding in its own words, from its own perspective, to confirm it understood what USER meant. USER reads the rewrite and confirms the AGENT understood correctly; if anything is wrong or imprecise, USER clarifies it again, still in plan mode. The AGENT may use the question tool to ask USER to clarify. This repeats until USER is confident the AGENT understood the idea or finding.

Once USER is confident, USER switches to build mode and orders the AGENT to record the item. The AGENT records the item in `docs/IMPROVEMENTS.md` following the `### <ID> — <Title>` skeleton and the label code table in [`docs/IMPROVEMENTS.md`](../templates/docs/IMPROVEMENTS.md), and places it at the very bottom of the Items section. This interaction fills only the fields below; every other field stays `—`:
- **ID:** `<LABEL_CODE>-<NNN>` built from the default GitHub labels, with numbers counted per label code. This numbering is local to the tracker file: each label code counts its own sequence from `001`, and after the tracker is archived and recreated, the numbering restarts from `001` again. It is independent from GitHub's numbering — the AGENT never derives or guesses a GitHub issue or PR number from the ID.
- **Status:** the literal value `recorded`.
- **Recorded:** the date and time the item was recorded, formatted `YYYY-MM-DD HH:MM`.
- **Problem:** describes the finding as application behavior when possible — for non-application items (CI, settings, tooling), the impact instead.
- **Possible Fix:** the initial fix plan, written while still `recorded` so it is not guaranteed to work.

The AGENT **presents the recorded item to the USER and stops**. No commit yet — this is the review gate. Once the USER approves, the AGENT commits the recorded items once on the working branch (a single commit for the whole recording), then presents the result and stops.

## Verify the recorded items

USER orders the AGENT to verify the items with `recorded` status in `docs/IMPROVEMENTS.md`. The verification changes only those items — no other file. While still in plan mode, the AGENT verifies each item, one by one, with:
- **Code verification** — checking the original code in the editor to confirm the problem is valid.
- **Online verification** — checking the planned solution against official documentation or other online sources so it is not done the wrong way.

The AGENT then presents the verification results and what it will change in each item, and the USER reviews and adjusts until it is fixed. Each item then becomes one of the following:
- **`verified`** — the item is valid; its `Status` becomes `verified` and its `Actual Fix` field is filled with the final fix plan confirmed during deep review.
- **`rejected`** — the item is invalid; its `Status` becomes `rejected`, it is not continued, and the AGENT records why in its `Rejection Reason` field.

This interaction changes only `Status`, `Actual Fix` (for `verified` items), and `Rejection Reason` (for `rejected` items); every other field stays unchanged (`—`).

Once the plan is fixed, USER switches to build mode and orders the AGENT to update the tracker. The AGENT changes each item's status and fields in `docs/IMPROVEMENTS.md`.

The AGENT **presents the updated items to the USER and stops**. No commit yet — this is the review gate. Once the USER approves, the AGENT commits the updated items once on the working branch (a single commit for the whole verification), then presents the result and stops. If the USER wants adjustments, USER switches back to plan mode and the AGENT revises its plan first.

## Create GitHub Issues, close invalid ones, and keep the tracker in sync

USER orders the AGENT to open the GitHub Issues and keep `docs/IMPROVEMENTS.md` in sync. While still in plan mode, the AGENT composes its plan:
- **Issues to open** — one per item with `verified` status, using the default label matching the ID code (e.g. `enhancement`, `documentation`, `bug`), a plain descriptive title without any type prefix — the label is the only type marker on the issue — and the complete technical details in the body. The item ID is not referenced in the issue.
- **Open Issues to close** — one per item with `rejected` status that has an open Issue on GitHub. A `rejected` item has an open Issue only when that Issue was created directly on GitHub by a contributor, then synced into the tracker as a new `recorded` item in a previous Create GitHub Issues interaction and later verified as invalid in the Verify the recorded items interaction — the tracker item is now `rejected` while its Issue is still open, and the two conflict. Items that became `rejected` from freshly recorded ideas never had an Issue opened, so they are not in this list.
- **Tracker updates to match the open Issues on GitHub** — open Issues may have been created by other contributors, so:
  - every open Issue without a matching item gets recorded as a new `recorded` item — its `ID` is `<LABEL_CODE>-<NNN>` built from the issue's label code (for example, an issue labeled `bug` gets `BUG-<NNN>`), `Status` = `recorded`, `Recorded` = the sync timestamp (`YYYY-MM-DD HH:MM`), `Issue` = `#N`, `Problem` drawn from the issue title and body, `Possible Fix` left `—`, and every other field `—`;
  - every existing item with an open Issue records its Issue number (`#N`) — the number GitHub assigned globally (Issues and Pull Requests share one global, ever-increasing sequence), not the item's tracker ID.

The AGENT presents the plan to the USER. The USER reviews and adjusts the plan until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT:
- opens each new GitHub Issue with `gh issue create`, using a plain descriptive title without any prefix, and fills the issue body with complete technical details;
- closes each invalid open Issue with `gh issue close <#N> --comment "<rejection reason>"` — the comment explains why the issue is rejected, so anyone who later reads the closed issue understands. This closes the GitHub Issue but changes no tracker field.
- syncs `docs/IMPROVEMENTS.md` — for every existing item with a matching open Issue, records `#N` in its `Issue` field (the global GitHub number that Issues and Pull Requests share, not the tracker ID). For every open Issue without a matching item, adds a new `recorded` item: `ID` = `<LABEL_CODE>-<NNN>` built from the issue's label code (for example, an issue labeled `bug` gets `BUG-<NNN>`), `Status` = `recorded`, `Recorded` = the sync timestamp (`YYYY-MM-DD HH:MM`), `Issue` = `#N`, `Problem` from the issue title and body, `Possible Fix` = `—`, all other fields `—`. This interaction fills the `Issue` field of matched items and creates new `recorded` items with the fields listed; no other field of existing items changes.

The AGENT **presents the created and closed Issues and the synced tracker to the USER and stops**. If anything changed, no commit yet — this is the review gate. Once the USER approves, the AGENT commits the changed items once on the working branch (a single commit for the whole sync), then presents the result and stops. If the USER wants adjustments, USER switches back to plan mode and the AGENT revises its plan first.

## Archive the tracker

USER orders the AGENT to archive `docs/IMPROVEMENTS.md`. This is allowed only when every item in it is finished — status `rejected` or `implemented`; open GitHub Issues do not block archiving. While still in plan mode, the AGENT reads `docs/IMPROVEMENTS.md` and verifies briefly that every item is finished. If any item still has status `recorded` or `verified`, the AGENT **stops and reminds the USER that the tracker is not ready to be archived**. If every item is finished, the AGENT presents its archive plan:
- the target file `docs/archived/IMPROVEMENT_YYYY-MM-DD-HH-MM.md` — the archived copy of the finished tracker;
- the recreation of `docs/IMPROVEMENTS.md` from the [`docs/IMPROVEMENTS.md`](../templates/docs/IMPROVEMENTS.md) template for the next batch — the numbering restarts from `001` per label code.

The USER reviews and adjusts the plan until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT copies `docs/IMPROVEMENTS.md` to `docs/archived/IMPROVEMENT_YYYY-MM-DD-HH-MM.md`, then recreates `docs/IMPROVEMENTS.md` from the [`docs/IMPROVEMENTS.md`](../templates/docs/IMPROVEMENTS.md) template so its format stays unchanged. Note: items with `verified` status are not worked on in this workflow. This interaction does not fill any item fields — it only copies the finished tracker and recreates a fresh one from the template.

The AGENT **presents the archived result to the USER and stops**. No commit yet — this is the review gate. Once the USER approves, the AGENT commits the archived state once on the working branch (a single commit), then presents the result and stops. If the USER wants adjustments, USER switches back to plan mode and the AGENT revises its plan first.

## Deliver the updates through a pull request

USER orders the AGENT to deliver the tracker updates to `main`. This interaction is selectable at any point — it delivers whatever the working branch holds, no matter which interactions produced it. While still in plan mode, the AGENT inventories the unmerged commits (`git log main..HEAD` — an empty list means there is nothing to deliver, and the AGENT **stops with that verdict**) and composes the delivery plan:

- pushing the branch (`git push -u origin docs/improvements`);
- opening one pull request into `main` — title and body following the project's Pull Request Template, the body written to a temporary file in `temp/` and applied with `--body-file`;
- the merge method chosen per the Merge Strategy policy from the commit count — a single-commit pull request merges with squash, an accumulated multi-commit branch authored by one person merges with rebase.

The AGENT presents the delivery plan to the USER. The USER reviews and adjusts the plan until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT pushes the branch and opens the pull request, waits for a green CI (if a check fails, the AGENT fixes it locally and pushes one new commit to the same branch), merges with `gh pr merge --<method> --delete-branch --admin`, then cleans up: switches back to `main`, syncs it (`git pull origin main`), and deletes the leftover local branch. This interaction does not fill any item fields.

The AGENT **presents the merged result and the synced `main` to the USER and stops**.