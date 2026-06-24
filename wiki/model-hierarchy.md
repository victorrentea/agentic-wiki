---
title: Model Hierarchy
category: concept
tags: [models, opus, sonnet, haiku, gpt, orchestration, cost]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-24
---


The model hierarchy is the discipline of matching the model tier to the task's difficulty — read like an "experienced-hire" analogy — instead of always reaching for the strongest (expensive) or cheapest (unreliable) model.

## The experienced-hire analogy

- **Opus = senior architect (10–15 yrs).** Mandatory for legacy code and the best orchestrator in multi-agent flows — the one you put in charge of farming work out to cheaper models. Strongest on long horizons (see [[dumb-zone]]).
- **Sonnet = mid-level dev (2–5 yrs).** Excellent for greenfield day-to-day work — but **don't point Sonnet at a big legacy codebase**; it lacks the architect's reach.
- **Haiku = graduate hire (<1 yr).** Trivial, well-scoped sub-tasks only. Quotably: *"you can do seppuku with Haiku"* — never trust it with judgement calls, guard decisions, or autonomous review (see the [[ralph-loop]] warning about Haiku as a judge model).
- **GPT = the second opinion.** Reach for it when the primary model has a hiccup — *"only when Claude feels sick"* — and as an independent reviewer family in a [[multi-model-review]] quorum.

## Why it matters

Picking the tier is a [[token-economy]] decision as much as a quality one: the architect-orchestrator (Opus) plans and delegates, while the cheap models execute the parallel grunt-work in [[sub-agents]] and the [[ralph-loop]]. A [[custom-agent]] can pin its own model (`haiku` for trivial steps, `opus` for long-horizon ones) so the tier choice is encoded, not improvised each run.

The tier is independent of the [[harness]]: the same Sonnet runs better inside a stronger harness, so "which model" and "which harness" are two separate knobs. Model strength also governs [[agent-skill]] activation — strong models wait to load a skill, weak ones greedily trash their own context.

## See also
- [[harness]]
- [[multi-model-review]]
- [[sub-agents]]
- [[ralph-loop]]
- [[custom-agent]]
- [[token-economy]]
- [[dumb-zone]]
- [[2026-06-23-ai-garmin]]
