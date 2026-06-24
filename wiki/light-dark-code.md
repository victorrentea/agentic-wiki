---
title: Light vs Dark Code
category: concept
tags: [agents-md, reference-architecture, code-quality, exemplar]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-24
updated: 2026-06-24
new_in: 2026-06-24
---

<span style="color:red">🆕 New in Garmin workshop Day 2 (2026-06-24)</span>

"Light code" is the reference-architecture exemplar module that sets the quality bar; "dark code" is the rest of the legacy codebase the agent must not imitate.

## The problem

An agent learning from a legacy codebase learns its patterns — including the bad ones. If 80% of the codebase uses anti-patterns (RxJava callbacks, God classes, ignored exceptions), the agent will replicate them even when AGENTS.md says not to, because the surrounding code is thousands of examples screaming the opposite.

## Pointing the agent at the light

The solution is to designate one module as the **reference-architecture exemplar** — *"if you want to see state-of-the-art quality like we want you to write, look at **that** module — not the other 80%."* This can be done:
- In `AGENTS.md`: *"Follow patterns from `/app/core/trail/` — it is the reference implementation."*
- By naming the exemplar module explicitly in the [[spec-driven-development|spec's]] design document.
- As a build-flavor pointer: since build flavors share folders you can't have separate AGENTS.md per flavor, but you can reference the best variant by name.

## Relation to anti-pattern guards

Naming the light module is complementary to listing explicit anti-pattern bans in [[agents-md-discipline|AGENTS.md]]: *"never use RxJava, use coroutines"* stops the agent from reaching for the dark patterns; pointing at the light module shows it the positive exemplar.

## See also
- [[agents-md-discipline]]
- [[legacy-migration-stages]]
- [[spec-driven-development]]
- [[code-graph]]
- [[cognitive-debt]]
- [[2026-06-23-ai-garmin]]
