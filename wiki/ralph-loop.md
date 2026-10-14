---
title: Ralph Loop
category: pattern
tags: [agent-workflow, async-agents, spec-driven, context-management]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-10
updated: 2026-10-13
---

A workflow where you spend a focused hour writing precise markdown specs, then hand them to an agent loop that runs unattended overnight — each batch entering with a clean, low-context "smart zone."

## How it works

Coined and documented at [ghuntley.com/ralph](https://ghuntley.com/ralph/), the loop exploits a simple asymmetry: human spec-writing is the bottleneck, not execution time. A commonly quoted ratio is **"1 hour of spec → 6 hours of work"** — you write the what, the agent does the how while you sleep.

The critical design constraint is **context hygiene**. Each task batch is launched into a fresh conversation with minimal accumulated context — the "smart zone" where a capable model like Opus operates at peak reasoning. Long-running conversations accumulate noise, tool definitions, and stale intermediate state; they push the model toward the [[context-window]]'s dangerous middle ground, where attention degrades ("lost in the middle"). Batching resets this.

The human's role shifts radically: stop typing code, **dictate everything**. The self-imposed rule: no hand-written code for a month; even commit and push go through the agent. The spec itself becomes the deliverable — a markdown document precise enough to be executable.

**Mechanically, the loop is a bash `while` that re-feeds the prompt** — distinct from a single long 6-hour autonomous run. <span style="color:red">The canonical one-liner is `while :; do cat PROMPT.md | claude-code; done`; Anthropic ships an official [ralph-wiggum plugin](https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum) for Claude Code, and the how-to lives at [ghuntley/how-to-ralph-wiggum](https://github.com/ghuntley/how-to-ralph-wiggum). It runs on a subscription via `claude -p` — no API key needed ([[headless-claude]], [[2026-05-04-ai-telecom]], [[2026-09-30-ai-enterprise]]).</span> Because it implies `--dangerously-skip-permissions`, it is genuinely dangerous: **lock the agent in a Docker sandbox** (and don't hand it the Docker socket). "If there's a bomb, it's contained in the door." <span style="color:red">At least a few people per company must know how to dockerize an agent ([[docker-sandboxing]], [[2026-07-01-ai-java-community]]).</span> The clean-context engine of each batch is really a [[handover]]: the spec is the distilled state the agent is reborn into, keeping it out of the [[dumb-zone]]. Autonomy length scales with the operator's skill — see [[agentic-engineering]].

Specs pair well with [[token-economy]] discipline: strip the agent to only the tools it needs, watch its first tool call, and [[profiling-agents|profile]] iteratively. [[llm-wiki]] pages make good durable spec anchors across loops.

**A fresh agent per batch** is the load-bearing idea (sometimes spelled "RALF"): write the work as a long list of markdown checkboxes (≈150 is fine), and the bash loop spawns a *new* agent for each batch — it ticks its boxes and exits, so no single agent ever enters the [[dumb-zone]]. *"It's like doing `/clear` between every bunch of tasks, automatically."* Because each agent starts clean, this works beautifully with cheap models from the lower [[model-hierarchy]] tiers — Sonnet or even Haiku for trivial batches. <span style="color:red">"A fresh Sonnet per task chapter" keeps every step in the smart zone ([[2026-10-06-gray-factory-devoxx]]).</span>

## `tasks.md` is the new ticket

<span style="color:red">The checkbox list is what makes the loop resumable. `tasks.md` checkboxes have three uses: handing the task to another agent or colleague; resuming after burning the context or the credit limit; and driving a Ralph loop — a bash script that starts a fresh agent per chapter ([[2026-08-06-ai-bank]]). With [[openspec]], a long `tasks.md` (≈200 lines / 28+ tasks) that won't fit one context is run by a `while not done` shell loop that launches one agent per chapter and runs all the tests after each ([[2026-06-08-ai-security-tools]]). Review `tasks.md` with the engineers at the end of design — above **≈$50 of tokens without a `tasks.md` or an orchestrator, the agent derails** ([[2026-09-30-ai-enterprise]]).</span>

## Keeping it honest: the Stop hook

<span style="color:red">The enforcement mechanism is a `Stop` hook that refuses to let the agent stop until it has proof the tests pass — "proof the tests pass? No? Back to work." It is "a programmatic guarantee, not a plea in CLAUDE.md" ([[agent-lifecycle-hooks]], [[2026-06-02-ai-open-workshop]], [[2026-06-05-ai-consultancy]]). See also [[ci-green-loop]].</span>

## "Spec the day, code the night"

<span style="color:red">The lifestyle that grew around it, reported from several companies:</span>

- <span style="color:red">Mornings chasing the business, specs in markdown all day, refine the technical approach in the afternoon; at 17:00–17:30 throw the specs at the agents and go home. Next morning, review ≈5–6 fresh PRs before standup — and the standup becomes *"which rules do we add to the agents today?"* ([[2026-04-16-ai-safety-critical]], [[2026-05-13-ai-it-services]], [[2026-08-10-ai-consultancy]]). One team fires "loops on 7 open specs" at 5pm and checks their "Ralphs" at 9pm ([[2026-07-01-ai-java-community]]).</span>
- <span style="color:red">**Night-shift recipe:** 6 specs ready at 17:00, a script that runs the agents one after another, and a **hook that sleeps when quota drops under 5%** → in the morning, a chain of [[stacked-prs]] ([[2026-08-13-ai-insurer]]).</span>
- <span style="color:red">Never go to lunch with no agent running ([[2026-07-23-ai-software-product]]).</span>
- <span style="color:red">Spend weekend and idle quota on build speed, flaky tests and more acceptance tests — **never on risky refactors**. **Multi-day runs pay off only with a measurable goal** (weekend build speed-up, flaky-test hunt) ([[2026-08-10-ai-consultancy]], [[2026-10-09-ai-jax-london]]).</span>
- <span style="color:red">Quota trick: set the weekly quota to reset Sunday night; or hand your `plan.md` to a colleague with quota left and let the loop run overnight on *theirs* ([[2026-07-09-ai-live-casino]]).</span>

<span style="color:red">Maturity matters: it was positioned as the 4th tier of adoption, only after **> 6 months** of AI maturity — specs, guardrails and feedback loops already in place. Room reactions: "dangerous for the brain — everything becomes deep thinking"; "you do the work of two people"; "you need social capacity to chase the business for hours" ([[2026-05-04-ai-telecom]], [[ai-addiction]]). One (unverified) estimate: a solo overnight agent realistically delivers ≈4–5 hours of background work, with ≈80% success on sub-1-hour tasks; the rest stall on environment errors (port in use, invalidated build cache) and resume next morning ([[2026-05-13-ai-it-services]]).</span>

## Is it obsolete? Orchestrators vs loops

<span style="color:red">Earlier advice here was enthusiastic about the bash loop; by autumn 2026 the recommendation is to **send long task lists to an Opus orchestrator of [[sub-agents]], not to a Ralph script**. The loop exists **to survive a small context window**: it re-reads everything every chapter, which costs tokens (each iteration re-pays the file reads and the system prompt from zero) and makes it **die on the final integration chapter**, where everything must be re-read at once. New Opus models are native orchestrators ("use subagents liberally" is in Boris Cherny's CLAUDE.md): one Opus boss sending minions to independent, parallelizable batches does the same job and keeps the integration view ([[2026-07-01-ai-java-community]], [[2026-07-23-ai-software-product]], [[2026-08-13-ai-insurer]], [[2026-09-30-ai-enterprise]], [[2026-10-09-ai-jax-london]]).</span>

<span style="color:red">Reasons that still justify a loop: quota, budget, or a context window too small for the job. Lightweight alternative: tell the agent in the prompt to **exit at ≈150k tokens** and let the next one pick up `tasks.md` ([[2026-09-16-ai-safety-critical]]).</span>

## `/loop` — the tamer cousin

**`/loop` — the tamer cousin.** Claude Code's built-in `/loop` command re-runs until CI is green, retries flaky tests, and can be given a **token budget** that emails when exceeded. Unlike the bash `while` RALPH loop, `/loop` does not require `--dangerously-skip-permissions` — but it still needs a [[docker-sandboxing|Docker sandbox]] for any sensitive work. Avoid letting it fall back to Haiku as the judge model; Haiku's reliability is insufficient for autonomous guard decisions. <span style="color:red">Parallel agents with no budget can burn ≈$100/hour on Opus — set daily usage alerting ([[2026-04-16-ai-safety-critical]]).</span>

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
- <span style="color:red">[[sub-agents]]</span>
- <span style="color:red">[[openspec]]</span>
- <span style="color:red">[[stacked-prs]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[headless-claude]]</span>
- <span style="color:red">[[docker-sandboxing]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
