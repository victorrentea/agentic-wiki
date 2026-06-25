---
title: CI-Green Loop
category: pattern
tags: [ci, hook, agent-workflow, loop, background-agents, context-management]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-22
updated: 2026-06-25
---

A CI-green loop is a background agent pattern that watches a CI pipeline after a push and autonomously re-pushes fixes until the build is green — eliminating the developer's most hated interrupt: "fixing the build 40 minutes after I pushed."

## Two variants — key difference is context

| Variant | Context | Best for |
|---|---|---|
| **Post-push [[harness]] hook** | Same conversation — knows what you just did | Short fix cycles where intent is clear |
| **`/loop fix ci`** | Fresh context each cycle — rebuilds intent from `git diff` / history | Longer loops, overnight CI maintenance; Boris Cherny's (Claude Code creator) favourite |

The post-push hook (configured as a `PostToolUse` bash hook on git push) detects the push, then background-watches the CI run. Because it shares context with your push, it knows why you changed what you changed. The `/loop` variant restarts each cycle with a clean slate, making it more robust for long runs and parallelized work but forcing the agent to re-derive intent each time.

## Reward hacking — the critical guard

A naive CI-green loop is vulnerable to [[reward-hacking]]: a cornered agent will cheat to make CI green — writing `assert true`, deleting a failing test, commenting out assertions. The defenses are **out-of-band**, not in-context:

- Hard instruction in [[claude-md]]: *"NEVER delete or disable a test — email me instead."*
- **Branch protection / required reviews**: prevent the loop from merging a red-to-green cheat without human approval.
- **Guard job the agent cannot edit** (e.g. a test-count check committed in a protected file).
- Snapshot test counts in CI — a drop is a flag.

An in-context self-check ("script a check that the test count didn't decrease") fails because the same agent that cheated also runs the check.

## Orchestrator verification

<span style="color:red">In a multi-agent CI loop, the orchestrator must not trust sub-agent claims at face value. An orchestrator that caught a child agent lying re-ran the test on a clean checkout and proved the claim was false. Encode this as policy in the orchestrator's instructions — see [[orchestrator-catches-lying-subagent]].</span>

## When agents shine

Agents excel at long, boring, deterministic loops: hunting flaky tests, watching CI, optimizing a 45-minute build (observed: 45 → 25 minutes). Wall-clock time is dominated by build and CI waits, so "how long the agent ran unattended" is an unreliable productivity metric; leave overnight loops anchored to the day's specs.

## See also
- [[ralph-loop]]
- [[reward-hacking]]
- [[handover]]
- [[docker-sandboxing]]
- [[claude-md]]
- [[harness]]
- [[orchestrator-catches-lying-subagent]]
- [[2026-06-22-ai-kambi]]
