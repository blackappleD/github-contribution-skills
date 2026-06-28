# github-contribution-skills

[English](README.md) | [中文](README.zh-CN.md)

Codex skills for completing GitHub open source contributions from repository analysis to Pull Request submission.

## Overview

This repository contains a staged Codex skill workflow for open source contribution work:

1. Find realistic PR opportunities.
2. Design a technical plan for the selected contribution.
3. Implement the plan safely in a local fork.
4. Submit the contribution as a GitHub Pull Request.

The recommended entry point is:

```text
/github-contribution
```

Use the child skills directly when you already know which stage you want to run.

## Installation

Copy the following prompt into Codex, Claude Code, or another agent:

```text
Install this skill：https://github.com/blackappleD/github-contribution-skills
```

## Skills

| Skill | Purpose |
|---|---|
| `/github-contribution` | Orchestrates the full contribution workflow and routes to the stage-specific skills. |
| `/find-contribution-prs` | Analyzes a repository or fork and recommends 1-3 realistic PR directions. |
| `/design-pr-technical-plan` | Turns a selected PR direction into technical design and interview narrative documents. |
| `/implement-pr-technical-plan` | Implements the confirmed technical plan, runs validation, and produces a commit plan. |
| `/submit-contribution-pr` | Checks the target repository's PR requirements, prepares the PR body, and submits the Pull Request. |

## Workflow

```text
/github-contribution
  -> /find-contribution-prs
  -> /design-pr-technical-plan
  -> /implement-pr-technical-plan
  -> /submit-contribution-pr
```

The orchestrator can also start from the middle of the workflow:

- If you already have a PR analysis report, start from `/design-pr-technical-plan`.
- If you already have a technical plan, start from `/implement-pr-technical-plan`.
- If implementation is complete, start from `/submit-contribution-pr`.

## Expected Outputs

All Markdown documents are written to the target open source project root under `tmp/` by default. Chat responses should include only concise summaries and the absolute paths to the generated files.

| Stage | Output |
|---|---|
| Repository analysis | `tmp/contribution-analysis.md` with Top 3 PR recommendations. |
| Technical design | `tmp/technical-plan.md` and `tmp/interview-narrative.md`. |
| Implementation | Code changes, tests, validation results, and `tmp/implementation-report.md`. |
| PR submission | `tmp/pr-description.md`, PR title, branch/base details, test summary, and PR link. |

## Repository Structure

```text
.
+-- AGENTS.md
+-- README.md
+-- README.zh-CN.md
`-- skills/
    +-- github-contribution/
    +-- find-contribution-prs/
    +-- design-pr-technical-plan/
    +-- implement-pr-technical-plan/
    `-- submit-contribution-pr/
```

Each skill follows the standard layout:

```text
skills/<skill-name>/
+-- SKILL.md
+-- agents/
|   `-- openai.yaml
`-- references/
    `-- *.md
```

## Usage Examples

Run the full workflow:

```text
Use /github-contribution to analyze https://github.com/owner/repo and help me complete a contribution PR.
```

Only find contribution directions:

```text
Use /find-contribution-prs to analyze this fork and recommend 1-3 PR directions.
```

Design a plan from an existing analysis report:

```text
Use /design-pr-technical-plan to turn this PR analysis report into a technical plan and interview narrative.
```

Implement a confirmed plan:

```text
Use /implement-pr-technical-plan to implement this technical plan and run the validation strategy.
```

Submit a completed contribution:

```text
Use /submit-contribution-pr to prepare and submit the PR for this completed change.
```
