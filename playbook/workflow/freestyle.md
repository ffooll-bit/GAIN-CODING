# Freestyle

Use this workflow when the task does not fit any of the other workflows — ad-hoc research, experiments, exploratory changes, or any work the USER wants handled without a fixed interaction sequence. It is still bound by the core rules, including the Issue-Driven rule, the No Direct Push rule, the Atomic Conventional Commits rule, and the Preview Before Commit rule.

This document defines the ground rules for free-form work between the USER and the AGENT. The `##` heading in this document marks the working section. Freestyle has no fixed sequence of interactions: the USER drives the work freely, and after every step the AGENT presents the result and stops until the next instruction. Working steps enforce the two standard review gates — the plan-to-build gate, where the AGENT presents its plan and waits for approval before executing, and the pre-commit gate, where the AGENT presents its result and waits for approval before committing. Standard flow branches from and merges to `main`; when `dev`/`develop` exists per the Branching Model policy, the USER directs the AGENT to adapt branch origins and merge targets accordingly.

## Work a free-form task

USER gives the AGENT a task that does not fit another workflow. While still in plan mode, the AGENT re-anchors in the five core policies and this document, then analyzes the task and presents its plan — what it will do, which commands or files are involved, and where the review gates fall. If the task actually fits one of the other workflows, the AGENT reminds the USER to switch to that workflow instead. If a code change is involved, the plan includes a supporting GitHub Issue or a tracker item first, per the Issue-Driven rule; read-only work (research, reading, analysis) needs none. The USER reviews and adjusts the plan until it is fixed.

Once the plan is fixed, USER switches to build mode and orders the AGENT to work. The AGENT executes the approved plan, stopping at each pre-commit gate to present the result for approval before committing. Committed work returns through the standard pull request path into `main`, merged per the Merge Strategy policy — no direct push, no force-push.

The AGENT **presents the result to the USER and stops**.