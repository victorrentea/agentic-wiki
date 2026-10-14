---
title: Policeman Agent
category: pattern
tags: [orchestration, parallel-agents, pr-conflict, worktree, sub-agents]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-07-23-ai-software-product]]", "[[2026-09-07-ai-bank]]"]
created: 2026-06-24
updated: 2026-10-13
---

A policeman agent is a dedicated orchestrator that blocks new parallel tasks from starting if their file-overlap with an open PR would cause merge conflicts.

## The problem

When many agent-driven PRs run in parallel overnight, two agents editing the same file in different branches creates merge conflicts that no agent can resolve deterministically. Without coordination, the morning brings a pile of broken PRs.

## How it works

Before any agent starts a new task, the policeman agent checks the set of files the task is expected to touch (from the `tasks.md` or task description) against the files already touched by open PRs. If the sets overlap, the new task is queued until the existing PR is merged. If they don't overlap, the new task starts immediately.

<span style="color:red">A variant waits on the *plan*, not the merge: the policeman refuses to start until the overlapping PR has reached its plan stage, because the plan lists every file in scope — only then is the locked set known ([[2026-07-23-ai-software-product]]).</span>

This is a lightweight orchestration primitive that scales better than sequential execution (most PRs don't overlap) without the conflict cost of fully parallel execution.

<span style="color:red">The gate is not exotic: it shows up in the reference agentic workflow that "10+ companies converge on", where the agent won't start work while an unapproved PR exists on the same files ([[2026-09-07-ai-bank]]; see [[agentic-review-pipeline]]).</span>

## Implementation hints

- The orchestrator reads open PR diffs via `gh pr diff` to build the current "locked file set."
- The new task's file set can be estimated from the [[spec-driven-development|tasks.md]] or from the grill-derived analysis.
- A **selective second reviewer** strategy pairs well: only route genuinely risky file types (Gradle/build files, swagger, shared utilities) through an extra reviewer gate.

## See also
- [[worktree]]
- [[sub-agents]]
- [[cloud-review-workflow]]
- [[spec-driven-development]]
- [[multi-model-review]]
- <span style="color:red">[[agentic-review-pipeline]]</span>
- <span style="color:red">[[stacked-prs]]</span>
- [[2026-06-23-ai-device-maker]]
