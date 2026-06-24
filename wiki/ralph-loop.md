---
title: Ralph Loop
category: pattern
tags: [agent-workflow, async-agents, spec-driven, context-management]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-10
updated: 2026-06-24
---

A workflow where you spend a focused hour writing precise markdown specs, then hand them to an agent loop that runs unattended overnight — each batch entering with a clean, low-context "smart zone."

## How it works

Coined and documented at [ghuntley.com/ralph](https://ghuntley.com/ralph/), the loop exploits a simple asymmetry: human spec-writing is the bottleneck, not execution time. A commonly quoted ratio is **"1 hour of spec → 6 hours of work"** — you write the what, the agent does the how while you sleep.

The critical design constraint is **context hygiene**. Each task batch is launched into a fresh conversation with minimal accumulated context — the "smart zone" where a capable model like Opus operates at peak reasoning. Long-running conversations accumulate noise, tool definitions, and stale intermediate state; they push the model toward the [[context-window]]'s dangerous middle ground, where attention degrades ("lost in the middle"). Batching resets this.

The human's role shifts radically: stop typing code, **dictate everything**. The self-imposed rule: no hand-written code for a month; even commit and push go through the agent. The spec itself becomes the deliverable — a markdown document precise enough to be executable.

**Mechanically, the loop is a bash `while` that re-feeds the prompt** — distinct from a single long 6-hour autonomous run. Because it implies `--dangerously-skip-permissions`, it is genuinely dangerous: **lock the agent in a Docker sandbox** (and don't hand it the Docker socket). "If there's a bomb, it's contained in the door." The clean-context engine of each batch is really a [[handover]]: the spec is the distilled state the agent is reborn into, keeping it out of the [[dumb-zone]]. Autonomy length scales with the operator's skill — see [[agentic-engineering]].

Specs pair well with [[token-economy]] discipline: strip the agent to only the tools it needs, watch its first tool call, and [[profiling-agents|profile]] iteratively. [[llm-wiki]] pages make good durable spec anchors across loops.

**`/loop` — the tamer cousin.** Claude Code's built-in `/loop` command re-runs until CI is green, retries flaky tests, and can be given a **token budget** that emails when exceeded. Unlike the bash `while` RALPH loop, `/loop` does not require `--dangerously-skip-permissions` — but it still needs a [[docker-sandboxing|Docker sandbox]] for any sensitive work. Avoid letting it fall back to Haiku as the judge model; Haiku's reliability is insufficient for autonomous guard decisions.

**A fresh agent per batch** is the load-bearing idea (sometimes spelled "RALF"): write the work as a long list of markdown checkboxes (≈150 is fine), and the bash loop spawns a *new* agent for each batch — it ticks its boxes and exits, so no single agent ever enters the [[dumb-zone]]. *"It's like doing `/clear` between every bunch of tasks, automatically."* Because each agent starts clean, this works beautifully with cheap models from the lower [[model-hierarchy]] tiers — Sonnet or even Haiku for trivial batches.

## See also

- [[token-economy]]
- [[context-window]]
- [[profiling-agents]]
- [[llm-wiki]]
- [[spec-driven-development]]
- [[handover]]
- [[dumb-zone]]
- [[agentic-engineering]]
- [[model-hierarchy]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
