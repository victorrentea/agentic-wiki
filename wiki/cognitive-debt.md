---
title: Cognitive Debt (Vibe-Coding Collapse)
category: concept
tags: [vibe-coding, anti-pattern, review, tests, human-factors, technical-debt]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-24
---


Cognitive debt is the accumulated *not-knowing* that builds up when code is generated faster than any human reads it — within a few months nobody on the team understands what is in the codebase, and every bug fix spawns new bugs.

## How the collapse happens

The cautionary arc: an Opus orchestrator plans, parallel Sonnet [[sub-agents]] execute, and almost nothing is reviewed. Velocity feels euphoric for **2–3 months**, then it collapses — nobody knows what the code does, fixing one bug breaks two others, and forward progress grinds to a halt. This is [[vibe-coding]] without review at scale; the debt is not in the code (technical debt) but in the team's *understanding* of it.

## The fake safety net

The trap that makes it worse: the team instructs the AI to use TDD and proudly hits 85%+ coverage — but **never reviews the tests**, and the AI rewrites the tests alongside the code each iteration. The "safety net" asserts only what the implementation already does ([[sycophancy]] applied to tests). High coverage with unreviewed tests is *negative* information: it manufactures false confidence.

## The real safety layer

The pragmatic antidote is **not more unit tests** — it is **business-reviewed `.feature` files** (Gherkin given/when/then the business can actually read). See [[acceptance-test-bdd]]. A human reviews the *test contracts* — the signatures and the asserts — not necessarily every value. A useful `AGENTS.md` rule: *"after writing tests, summarise in plain English what each test asserts and wait for human approval before implementing."* See [[agents-md-discipline]].

Cognitive debt is the human cost of [[vibe-coding]]; the standing countermeasure is [[skill-erosion]] discipline (mob-review, hands-only quarter) plus [[multi-model-review]] and honest [[code-review]].

## See also
- [[vibe-coding]]
- [[acceptance-test-bdd]]
- [[sycophancy]]
- [[skill-erosion]]
- [[code-review]]
- [[agents-md-discipline]]
- [[2026-06-23-ai-garmin]]
