---
title: Ralph Loop
category: pattern
tags: [agent-workflow, async-agents, spec-driven, context-management]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

A workflow where you spend a focused hour writing precise markdown specs, then hand them to an agent loop that runs unattended overnight — each batch entering with a clean, low-context "smart zone."

## How it works

Coined and documented at [ghuntley.com/ralph](https://ghuntley.com/ralph/), the loop exploits a simple asymmetry: human spec-writing is the bottleneck, not execution time. The ratio Victor Rentea quotes is **"1 hour of spec → 6 hours of work"** — you write the what, the agent does the how while you sleep.

The critical design constraint is **context hygiene**. Each task batch is launched into a fresh conversation with minimal accumulated context — the "smart zone" where a capable model like Opus operates at peak reasoning. Long-running conversations accumulate noise, tool definitions, and stale intermediate state; they push the model toward the [[context-window]]'s dangerous middle ground, where attention degrades ("lost in the middle"). Batching resets this.

The human's role shifts radically: stop typing code, **dictate everything**. Victor's self-imposed rule: no hand-written code for a month; even commit and push go through the agent. The spec itself becomes the deliverable — a markdown document precise enough to be executable.

Specs pair well with [[token-economy]] discipline: strip the agent to only the tools it needs, watch its first tool call, and [[profiling-agents|profile]] iteratively. [[llm-wiki]] pages make good durable spec anchors across loops.

## See also

- [[token-economy]]
- [[context-window]]
- [[profiling-agents]]
- [[llm-wiki]]
- [[2026-06-10-spring-ai-itkonekt]]
