---
title: Legacy Migration Stages
category: pattern
tags: [legacy, migration, testing, dead-code, kotlin, java, refactoring]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-24
updated: 2026-06-24
new_in: 2026-06-24
---

<span style="color:red">🆕 New in Garmin workshop Day 2 (2026-06-24)</span>

A staged recipe for safely migrating a large, untested legacy codebase: first build a test harness, then remove dead code, and only then migrate technology — skipping stages collapses.

## The three stages

1. **Tests first.** Have the agent write characterization tests until coverage reaches ≈85%. Do not migrate anything yet. Without this net, every change in the next stages is a gamble.

2. **Dead-code removal.** Ask the agent to find unreachable code. In typical legacy codebases, 10–100% of a class body may be dead. Removing it reduces scope and makes the migration target smaller and clearer. [[code-graph]] accelerates this: *"who calls this method?"* instantly reveals whether a symbol has any live callers.

3. **Migrate.** Only now rewrite technology — Java → Kotlin, callbacks → coroutines, RxJava → Flow. The test net from stage 1 catches regressions.

## Why order matters

The instinct *"if it works, don't touch it"* is exactly how years of untested "weird stuff" accumulate. Eventually the codebase is too fragile to migrate safely. The staged approach forces a decision: invest in the safety net first, or accept that migration without tests is a guess.

**Caution:** virtual-thread migration (Java 21) is genuinely risky in legacy code due to `synchronized` interaction with virtual threads — this step needs human review beyond what the agent can verify.

## Agentic leverage

The agent is particularly good at stage 1 (mechanical test generation) and stage 2 (dead-code detection via call-graph analysis with [[code-graph]]). Stage 3 benefits from the agent's knowledge of the target language/framework but needs the human to validate idiomatic correctness and performance implications.

## See also
- [[code-graph]]
- [[acceptance-test-bdd]]
- [[hardware-mock]]
- [[feedback-loop]]
- [[vibe-fixer]]
- [[agents-md-discipline]]
- [[2026-06-23-ai-garmin]]
