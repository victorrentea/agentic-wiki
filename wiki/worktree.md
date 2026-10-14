---
title: Git Worktree
category: tool
tags: [git, parallel-agents, isolation, context-management, race-condition]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-09-30-ai-enterprise]]"]
created: 2026-06-11
updated: 2026-10-13
---

A git worktree is a separate working folder of the same repository, letting two agents each work on their own files and see each other's changes only on commit+push / pull.

## Why agents need them

Without worktrees, **two agents in one folder hit a race condition**: one writes a file, the other's cached view goes stale, it detects the change, panics, and **re-reads the file — trashing its context** (the same cache-invalidation harm as a human hand-editing a file mid-session). Worktrees give each agent an isolated folder; synchronization happens only through git (fast-forward on push/pull). `git worktree add ../feature-x branch` is the primitive, and it is how Claude Code isolates parallel/background agents safely.

<span style="color:red">The race, concretely: agent 1 holds lines 14–50 in memory; agent 2 inserts a line at 5; agent 1 writes "fix at line 17" to the wrong place → compile errors → tokens burned chasing ghost bugs. With worktrees, conflicts surface only at `git pull` — where AI is *excellent* at resolving them. Even the trainer caught himself running a backend conversation and a frontend-polish conversation in one folder ([[2026-04-27-ai-healthcare-software]]).</span>

## Constraints

You **cannot check out the same branch in two worktrees** (`fatal: already checked out`), so an agent makes a new branch, commits, and merges back. <span style="color:red">Ask the agent to create the worktrees when you parallelise and to delete them afterwards.</span> A typical expensive-skill workflow: do the work in a separate worktree, push to main, then delete the worktree (see the `disable-model-invocation` example in [[agent-skill]]).

<span style="color:red">When parallel tasks overlap — a backend agent and a frontend agent both touching an API contract — expect ≈5% drift that the orchestrator fixes after the merge; still net-positive on wall time ([[2026-05-27-ai-open-workshop]], [[2026-06-05-ai-consultancy]]). Keeping the contract in one place helps — see [[openapi-contract-sync]].</span>

Worktrees are the safe substrate for the kind of parallelism that, used carelessly, becomes the [[ai-addiction]] dopamine trap — isolation prevents the *technical* collisions, but you still have to guard your own attention.

## Limits: ports and running stacks

<span style="color:red">Worktrees isolate *files*, not *runtime*. For end-to-end work Victor gave up on them because **agents fight over ports** — two copies of the app can't both bind 8080. Fixes: run the whole stack in Docker Compose with an **isolated network per copy** ([Compose networking](https://docs.docker.com/compose/how-tos/networking/)), or simply keep **two colour-coded clones — they beat five worktrees** ([[2026-09-30-ai-enterprise]]). See also [[docker-sandboxing]].</span>

## Branching and burnout

**Shared object store, isolated branches.** Worktrees share one git object store (no disk duplication) but each agent commits to its own branch — agents pull before pushing and fast-forward cleanly. Two agents on the *same* branch will need a rebase, not a fast-forward, so set branch policies accordingly.

**Multi-agent burnout is real.** Managing four parallel agents is a real cognitive load — technically isolated, but emotionally draining. Daily heavy parallelism is unsustainable; use worktrees for burst parallelism, not as a permanent operating mode.

## See also
- [[agent-skill]]
- [[sub-agents]]
- [[ai-addiction]]
- [[token-economy]]
- <span style="color:red">[[docker-sandboxing]]</span>
- <span style="color:red">[[openapi-contract-sync]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
