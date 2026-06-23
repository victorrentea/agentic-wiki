---
title: Feedback Loop
category: pattern
tags: [feedback-loop, agentic-engineering, self-correction, screenshots, tests, adb]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-23
new_in: 2026-06-23
---

<span style="color:red">🆕 New in Garmin 2026-06-23</span>

A feedback loop is any mechanism that lets the agent *observe the result of its own work* and iterate without a human in the middle — the single most important thing to set up before handing off a task.

## The core rule

**Find a feedback loop for every task you hand off.** An agent that can only emit code and wait for you to report the outcome is stuck in the [[chop-ai-waiter|CHOP / AI-waiter]] anti-pattern — *"we are not the servants of AI."* An agent wired to a loop closes its own correction cycle: act, observe, adjust, repeat until the signal is green.

## What counts as a loop

- **Build / compiler warnings** — *"build the project so the deprecation warning surfaces,"* then loop until it's gone.
- **Tests** — run [[acceptance-test-bdd|acceptance tests]] until green; write a stress test with 20 parallel threads until the race bug reproduces, *then* fix it.
- **Screenshots** — *"stay in a loop taking screenshots until the SVG is pixel-aligned"* (a pixel-perfect render reached <0.5% diff after ≈40 minutes unattended). Use a [[browser-mcp|Playwright]] accessibility-tree read or vision diff.
- **[[adb-automation]]** — on mobile, the agent screenshots/taps/drags a running emulator to reproduce a bug, fix it, and confirm a green test, hands-free.
- **Reflected diagrams** ([[archunit-drift-control]], [[field-reality-diagram]]) — a structural oracle the agent can check against.

## Why it's the whole game

The deeper thesis: the new engineering skill is **seeing, testing, and reproducing the problem**, not fixing it — *"move your focus from typing to feeling the problems"* (races, deadlocks, load, stress). The agent can fix almost anything *once you point it at a loop that makes the problem observable*. Expensive loops (start-the-app, pixel-compare) should be gated so they don't auto-fire every turn — set `disable-model-invocation: true` on the [[agent-skill]] that wraps them.

## See also
- [[chop-ai-waiter]]
- [[acceptance-test-bdd]]
- [[browser-mcp]]
- [[adb-automation]]
- [[archunit-drift-control]]
- [[ci-green-loop]]
- [[agentic-engineering]]
- [[2026-06-23-ai-garmin]]
