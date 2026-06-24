---
title: Policeman Agent
category: pattern
tags: [orchestration, parallel-agents, pr-conflict, worktree, sub-agents]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-24
updated: 2026-06-24
new_in: 2026-06-24
---

<span style="color:red">🆕 New in Garmin workshop Day 2 (2026-06-24)</span>

A policeman agent is a dedicated orchestrator that blocks new parallel tasks from starting if their file-overlap with an open PR would cause merge conflicts.

## The problem

When many agent-driven PRs run in parallel overnight, two agents editing the same file in different branches creates merge conflicts that no agent can resolve deterministically. Without coordination, the morning brings a pile of broken PRs.

## How it works

Before any agent starts a new task, the policeman agent checks the set of files the task is expected to touch (from the `tasks.md` or task description) against the files already touched by open PRs. If the sets overlap, the new task is queued until the existing PR is merged. If they don't overlap, the new task starts immediately.

This is a lightweight orchestration primitive that scales better than sequential execution (most PRs don't overlap) without the conflict cost of fully parallel execution.

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
- [[2026-06-23-ai-garmin]]
