---
title: Agentic Engineering
category: concept
tags: [agentic-engineering, discipline, autonomy, spec-driven, overview]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

Agentic engineering is the disciplined practice of building software *through* autonomous agents — investing human effort in specs, knowledge, guardrails, and review rather than in typing code — the deliberate opposite of [[vibe-coding]].

## The defining principle

**Autonomy scales with the engineer:** "the stronger the agentic engineer, the longer the flows you can run." A weak operator can supervise a single short prompt; a strong one front-loads enough thinking that an agent can run for hours unattended (the longest single autonomous run cited was **6 hours** — the agent fixed a CI pipeline, waited 40 minutes for the build, saw it fail, fixed, and pushed). Weekend-scale runs are not one loop but the [[ralph-loop]].

## What the discipline consists of

The day's whole arc is the toolkit:

- **Front-load thinking** — [[grill-me|grill]] the requirement, write a [[spec-driven-development|spec]], leave a [[handover]].
- **Manage context** — respect the [[token-economy]] and stay out of the [[dumb-zone]] (via [[sub-agents]], [[handover]], [[plan-mode]]).
- **Engineer the knowledge** — curate [[claude-md]], grow [[tripwire|tripwires]], share via [[cross-repo-knowledge]].
- **Constrain the output** — [[acceptance-test-bdd|acceptance tests]] from one side, design decisions from the other, then [[multi-model-review]].
- **Guard the human** — beware [[ai-addiction]] and [[skill-erosion]].

The engineer becomes a "manager of agents", not a viper — see [[vibe-coding]] for the contrast.

## See also
- [[vibe-coding]]
- [[spec-driven-development]]
- [[ralph-loop]]
- [[token-economy]]
- [[multi-model-review]]
- [[skill-erosion]]
- [[2026-06-11-ai-playtika]]
