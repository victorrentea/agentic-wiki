---
title: Git Worktree
category: tool
tags: [git, parallel-agents, isolation, context-management, race-condition]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

A git worktree is a separate working folder of the same repository, letting two agents each work on their own files and see each other's changes only on commit+push / pull.

## Why agents need them

Without worktrees, **two agents in one folder hit a race condition**: one writes a file, the other's cached view goes stale, it detects the change, panics, and **re-reads the file — trashing its context** (the same cache-invalidation harm as a human hand-editing a file mid-session). Worktrees give each agent an isolated folder; synchronization happens only through git (fast-forward on push/pull). `git worktree add ../feature-x branch` is the primitive, and it is how Claude Code isolates parallel/background agents safely.

## Constraints

You **cannot check out the same branch in two worktrees** (`fatal: already checked out`), so an agent makes a new branch, commits, and merges back. A typical expensive-skill workflow: do the work in a separate worktree, push to main, then delete the worktree (see the `disable-model-invocation` example in [[agent-skill]]).

Worktrees are the safe substrate for the kind of parallelism that, used carelessly, becomes the [[ai-addiction]] dopamine trap — isolation prevents the *technical* collisions, but you still have to guard your own attention.

## See also
- [[agent-skill]]
- [[sub-agents]]
- [[ai-addiction]]
- [[token-economy]]
- [[2026-06-11-ai-playtika]]
