<div align="center">

<h1>GAIN-CODING</h1>

<img src="docs/social-preview.png" alt="GAIN-CODING - Guided Agent In Native Coding, a personal workflow knowledge base">

**Guided Agent In Native Coding** — a personal workflow knowledge base for keeping agent-assisted development anchored in the principles of native (traditional) coding.

</div>

## Overview

GAIN-CODING is an **information repository**, not application code. It turns the working principles of traditional coding into a set of documents an AI agent can follow (and, with any luck, not reinterpret into something unrecognizable). You do not read this repository as code; you hand your agent the document that matches the task you want done.

GAIN is an attempt to combine the discipline of traditional coding with the speed of AI-assisted development. The sections below explain the trade-off: traditional coding brings the discipline, vibe coding brings the speed, and GAIN tries to keep both (in theory — the speed part still works better than the discipline part).

### Native (Traditional) Coding

Traditional coding is the discipline that professional software development has long depended on: issues before changes, clean branches, reviewed pull requests, atomic commits, tested builds, and disciplined releases.

It is deliberate and time-tested, but it is slow and human-driven (read: boring, but it works).

### Vibe Coding

"Vibe coding" describes an AI-assisted style of development where developers let an AI agent write most of the code and primarily ride the flow rather than follow a deliberately structured development process.

With sufficiently capable models, many of its weaknesses can be reduced. Better models can understand context, follow instructions, reason about changes, and produce reliable code with less guidance. But access to those models is not universal. Free models can be surprisingly capable, too, but their results can be much more sensitive to the quality, precision, and context of the prompt.

That can make vibe coding remarkably fast and effective when everything lines up. The trade-off becomes expensive when it does not: changes may be poorly tracked or reviewed, important checks may be skipped, secrets may end up in the repository, and code can become difficult to understand or safely modify.

**The results are _easy to love_ while the demo works, and _expensive to live with_ the moment something breaks.**

### GAIN Coding

GAIN stands for **Guided Agent In Native Coding**. The word **“native”** is deliberately a stretch — it is there to make the acronym work, while pointing to the conventional coding practices that came before agent-assisted development.

The idea is simple: one person wanted the agent to write their code, but still wanted to keep the habits that traditional coding had taught them.

This repository is not a solution, a movement, or a claim that vibe coding is wrong. It is a personal experiment, made for fun, for one environment, by one developer who likes having the agent do the work while still feeling like a proper developer.

At its core, it does three things:

- **Native principles stay first.** A strict GitHub-based workflow: issue-driven changes, protected branches, no direct pushes, atomic conventional commits, previews before merging (the parts nobody gets excited about, the parts that actually keep it together).
- **The agent does the work, not the deciding.** The AGENT executes the workflow — creating issues, branching, implementing, committing, pushing, and opening pull requests — while the USER stays the reviewer and approver at every gate.
- **Workflow is written down, not remembered.** Every policy, flow, and format specification lives in this repository as versioned documentation.

The honest summary: the agent does the heavy lifting, the rules keep things respectable, and nobody here is claiming to have solved software engineering (yet).

### Disclaimer

This repository was created for **personal use** and designed for a specific environment. It is primarily intended for people running a similar setup: Windows, [Visual Studio Code](https://github.com/microsoft/vscode), [OpenCode](https://github.com/anomalyco/opencode), [Magic Context](https://github.com/cortexkit/magic-context), GitHub CLI (`gh`, [cli/cli](https://github.com/cli/cli)), the [ponytail skill](https://github.com/dietrichgebert/ponytail), and DeepSeek V4 Flash Free as the primary agent model.

Anyone with a similar setup is welcome to adapt, extend, and contribute to this knowledge base, but there is no guarantee that it will work well outside that environment.

## How to Use

GAIN-CODING is used as a **workflow knowledge base for an AI agent**. You do not need to give the agent the entire repository. Give it the smallest set of documents that is sufficient for the task at hand.

The basic pattern is:

```text
Initialize the agent
        |
        v
Choose the workflow
        |
        v
Give the agent the workflow document
        |
        v
Agent works until an approval gate
        |
        v
You review and approve
        |
        v
Agent continues
```

### Quick Start

For a typical session:

1. **Initialize the agent** with the five core policy documents if they are not already available in the agent's context or memory.
2. **Choose the workflow** that matches what you want to do.
3. **Give the agent only that workflow document** and any templates or references it explicitly requires.
4. **Let the agent execute the workflow** until it reaches an approval gate.
5. **Review and approve** before the agent crosses the gate.

The agent may inspect, analyze, prepare, verify, and propose. It must not make decisions reserved for the user or cross an explicit approval gate without the user's approval.

> **Context principle:** Do not load the entire GAIN-CODING repository unless you explicitly need the full manual. Load the minimum context required for the current task.

### Step 1 — Initialize the Agent

At the beginning of a new session, make sure the agent knows the five core GAIN-CODING policies.

If the policies are already available in the agent's persistent memory or context, **do not load them again**. Otherwise, load the five documents below in order.

Feed them one at a time and wait for the agent to confirm each document before providing the next.

#### 1. Core Rules

**[Core Rules](playbook/policies/core-rules.md)** — the workflow principles that cannot be bypassed.

```text
Read this document fully: {URL}
It defines the workflow principles that cannot be bypassed in this project.

After reading:
1. Confirm that you have read the document.
2. Summarize the core rules in 1-2 sentences.
3. Do not take any action yet.
4. Wait for my next instruction.
```

#### 2. Repository Protection

**[Repository Protection](playbook/policies/repository-protection.md)** — the GitHub settings that enforce the workflow.

```text
Next, read this document fully: {URL}
It explains the GitHub protection settings that enforce the workflow, including branch protection, merge methods, and restrictions on direct pushes.

After reading:
1. Confirm that you have read the document.
2. Summarize which repository protections are enforced.
3. Do not change any repository settings.
4. Wait for my next instruction.
```

#### 3. Merge Strategy

**[Merge Strategy](playbook/policies/merge-strategy.md)** — how pull requests enter the target branch for each case.

```text
Next, read this document fully: {URL}
It defines how a pull request enters the target branch for each case, including when to use squash, rebase, or merge.

After reading:
1. Confirm that you have read the document.
2. Summarize which merge method applies to each case.
3. Do not merge or modify any pull request.
4. Wait for my next instruction.
```

#### 4. Branching Model

**[Branching Model](playbook/policies/branching-model.md)** — the branch layout and how to select the appropriate branch.

```text
Next, read this document fully: {URL}
It explains the branch layout and how to select the correct branch prefix for each type of change.

After reading:
1. Confirm that you have read the document.
2. Summarize the branching rules.
3. Do not create or switch branches yet.
4. Wait for my next instruction.
```

#### 5. Boundaries

**[Boundaries](playbook/policies/boundaries.md)** — the limits that apply to the agent's operation.

```text
Next, read this document fully: {URL}
It defines the boundaries you must respect, including communication language, line wrapping, approval requirements, and the operating environment.

After reading:
1. Confirm that you have read the document.
2. Summarize the boundaries you must respect.
3. Do not take any action yet.
4. Wait for my next instruction.
```

> **Magic Context:** If you use Magic Context, these five policies can be saved to memory once. Future sessions can start directly from Step 2 as long as the agent's stored context is still valid.

### Step 2 — Choose a Workflow

Choose the workflow that matches the task you want the agent to perform.

| What you want to do | Workflow |
|---------------------|----------|
| Start a new project from scratch | **Project Bootstrap** |
| Bring an existing project up to standard | **Workflow Adoption** |
| Record an idea, feature request, or bug | **Findings and Planning** |
| Implement a verified GitHub Issue | **Code Implementation** |
| Recover from a CI failure or Git accident | **CI and Git Rescue** |
| Handle dependency update PRs | **Dependabot PRs** |
| Create a new release | **Release Process** |
| Prepare a repository for public release | **Data Security and Public Readiness** |
| Need the complete workflow manual | **Workflow Instructions** |

Use the corresponding prompt below.

#### Start a New Project from Scratch

**[Project Bootstrap](playbook/workflow/project-bootstrap.md)** — bootstraps a repository from scratch, including structure, guardrails, documentation, `.github`, CI, and repository protection.

```text
Read this document fully: {URL}

It describes how to bootstrap a new project from scratch.

Follow the workflow step by step:
1. Initialize the repository.
2. Create the required folder structure and guardrail files.
3. Prepare the standard documentation.
4. Set up the .github templates and CI.
5. Apply the required repository protection settings.
6. Push the work through the first pull request.

Use the template kit at {TEMPLATE_URL} as the source for the guardrail files, issue/PR/release templates, CI workflow, and IMPROVEMENTS item template.

Stop at every approval checkpoint described by the workflow and wait for my approval before continuing.
```

#### Bring an Existing Project Up to Standard

**[Workflow Adoption](playbook/workflow/workflow-adoption.md)** — audits an existing repository and brings it up to the GAIN-CODING workflow standard.

```text
Read this document fully: {URL}

It describes how to bring an existing repository up to the GAIN-CODING workflow standard.

Start by auditing the current repository condition. Record the findings as improvement items and present the audit results to me.

Use the template kit at {TEMPLATE_URL} as the reference for the standard files when identifying gaps.

Do not modify the repository yet. Present the audit findings first and wait for my approval before applying any change.
```

#### Record and Track an Improvement Idea

**[Findings and Planning](playbook/workflow/findings-and-planning.md)** — records, verifies, and tracks feature ideas and bugs in `docs/IMPROVEMENTS.md`.

```text
Read this document fully: {URL}

It describes how to record, verify, and track improvement ideas.

Use the item template at {TEMPLATE_URL} as the format for each item in docs/IMPROVEMENTS.md.

Record the idea, prepare the required verification plan, and present the result to me.

Do not create a GitHub Issue yet. Wait for my explicit instruction before creating one.
```

#### Implement a Verified Issue

**[Code Implementation](playbook/workflow/code-implementation.md)** — implements a verified GitHub Issue through a branch and pull request.

```text
Read this document fully: {URL}

It describes how to implement a verified GitHub Issue through a branch and pull request.

Before making changes:
1. Confirm the current repository and branch.
2. Confirm the target GitHub Issue.
3. Confirm that the issue is verified and ready for implementation.
4. Summarize what you understand.
5. Wait for my approval before starting implementation.

Then follow the workflow step by step.

Verify the changes locally according to the project's stack. Show me a behaviour preview and wait for my approval before committing.

Keep the verification checklist consistent with the pull request template at {TEMPLATE_URL}.

Stop at every approval checkpoint described by the workflow.
```

#### Recover from CI Failure or Git Accidents

**[CI and Git Rescue](playbook/workflow/ci-and-git-rescue.md)** — recovers from CI failures, accidental commits to `main`, and rejected pushes.

```text
Read this document fully: {URL}

It describes how to recover from CI failures and Git accidents, including:
- a commit that landed on main by mistake;
- a rejected push;
- a failing CI workflow.

First inspect the current repository state and identify which recovery path applies.

Present your findings and the proposed recovery path to me before making corrective changes.

Follow the appropriate recovery procedure from the document and stop at every approval checkpoint it describes.
```

#### Handle a Dependabot PR

**[Dependabot PRs](playbook/workflow/dependabot-prs.md)** — classifies and merges dependency update pull requests.

```text
Read this document fully: {URL}

It describes how to handle Dependabot pull requests.

Inspect the open Dependabot PRs and classify each one as low or high risk.

Use the Dependabot configuration at {TEMPLATE_URL} to understand which ecosystems are enabled.

Present the classification and recommended action to me.

Do not merge anything. Wait for my explicit merge order before proceeding.
```

#### Cut a Release

**[Release Process](playbook/workflow/release-process.md)** — performs the complete SemVer release process.

```text
Read this document fully: {URL}

It describes how to cut a release end to end.

Follow the release workflow, but do not cross approval gates without my approval.

First prepare the required CHANGELOG.md changes and present them to me for approval.

After approval:
1. Commit the release changes through a branch and pull request.
2. Follow the required merge process.
3. Tag the appropriate SemVer version.
4. Create the official release.

Use the release notes template at {TEMPLATE_URL} when creating the release.
```

#### Prepare a Repository for Public Release

**[Data Security and Public Readiness](playbook/workflow/data-security-and-public-readiness.md)** — audits a repository for sensitive data and other public-release risks.

```text
Read this document fully: {URL}

It describes how to audit a repository before making it public.

Run the pre-publication audit and collect the required evidence.

Present the findings to me and stop.

Do not change the repository visibility, publish the repository, or remove potentially sensitive data without my explicit approval.
```

#### Get the Full Picture

**[Workflow Instructions](playbook/README.md)** — the complete GAIN-CODING manual containing policies, operating procedures, format conventions, and templates.

Use this only when you need to understand the complete system or troubleshoot how multiple workflows relate to one another.

```text
Read this document fully: {URL}

It is the complete GAIN-CODING workflow manual, covering policies, operating procedures, format conventions, and templates.

Read it in full and build an understanding of how the workflows relate to one another.

Do not modify the repository or execute any workflow yet.

After reading, summarize the workflow structure and wait for my instruction on which flow to run.
```

### Workflow Map

The workflows are designed to form a continuous development lifecycle rather than a collection of unrelated procedures:

```text
                +---------------------------+
                |      New Project          |
                |    Project Bootstrap      |
                +-------------+-------------+
                              |
                              v
                +---------------------------+
                |   Existing Repository     |
                |    Workflow Adoption      |
                +-------------+-------------+
                              |
                              v
                +---------------------------+
                |      Idea / Bug           |
                |   Findings & Planning     |
                +-------------+-------------+
                              |
                              v
                         GitHub Issue
                              |
                              v
                +---------------------------+
                |   Code Implementation     |
                +-------------+-------------+
                              |
                              v
                         Pull Request
                              |
               +--------------+--------------+
               |                             |
               v                             v
            CI passes                   CI fails
               |                             |
               |                             v
               |                    CI and Git Rescue
               |
               v
              Merge
                |
                v
          Release Process
```

Other workflows operate alongside this main path:

- **Dependabot PRs** handles dependency update pull requests.
- **Data Security and Public Readiness** prepares a repository for public release.
- **CI and Git Rescue** handles failures and unexpected Git states wherever they occur.

### Context Loading Rules

GAIN-CODING is intentionally designed around **progressive context loading**.

Follow these rules:

1. **Do not give the agent the entire repository by default.**
2. Load the five core policies only when they are not already available in the agent's memory or context.
3. Load **one workflow document** that matches the current task.
4. Load only the templates, specifications, or supporting documents explicitly required by that workflow.
5. Do not ask the agent to read unrelated workflows unless the current task actually depends on them.
6. Use **Workflow Instructions** when you need the complete picture, not as the default starting point.

The goal is simple:

> **Give the agent enough context to follow the rules, but not so much context that the rules become noise.**

### Approval Gates

GAIN-CODING separates **execution** from **decision-making**.

The agent is responsible for doing the work. The user remains responsible for deciding whether the work should proceed past an approval gate.

The agent may:

- inspect the repository;
- inspect Git history and GitHub state;
- analyze problems;
- prepare plans;
- propose changes;
- implement approved work;
- run verification;
- prepare commits and pull requests when the workflow allows it.

The agent must wait for the user's approval when the workflow requires a decision such as:

- applying a proposed change;
- committing after a review checkpoint;
- merging a pull request;
- changing repository visibility;
- publishing a release;
- taking a recovery action where multiple paths are possible.

**When in doubt, stop and ask.**

The workflow documents define the exact approval checkpoints for each operation.

## Contributing

This repository follows the same workflow it documents. Changes are welcome, especially from people running the same environment. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

This project is licensed under the [MIT License](LICENSE).