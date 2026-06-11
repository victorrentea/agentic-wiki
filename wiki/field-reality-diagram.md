---
title: Field-Reality Diagram
category: pattern
tags: [observability, traces, plantuml, diagrams, drift, no-drift]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

A field-reality diagram is a sequence diagram *generated from real execution traces* rather than hand-drawn — so it shows the actual flow of a system and **cannot [[drift]]**, because it is regenerated on every run.

## The pipeline

Hand-drawn architecture diagrams rot the moment the code changes. The alternative, already working in practice: run e2e tests on staging at **100% trace sampling** → collect the traces in an observability backend (e.g. Grafana) → a small script renders them as a [PlantUML](https://plantuml.com) sequence diagram (e.g. published into Jira). The result is *the reality of the flow*, regenerated every run, so it stays truthful for free.

## Why it beats both the model and the human

Because the diagram reflects what *actually happened*, an agent **can't cheat** against it: pointed at a trace and told "this PUT shouldn't trigger a websocket back", the AI couldn't bluff — it dug into the real behaviour and fixed it. This is the same anti-cheating value as reviewing [[acceptance-test-bdd|acceptance tests]] over mock tests, and the opposite philosophy to a `design.md` you **burn** because it drifts (see [[spec-driven-development]]). Inter-service sequence diagrams worth keeping in sync — for one-page incident triage — are exactly the diagrams to generate this way.

## See also
- [[drift]]
- [[acceptance-test-bdd]]
- [[spec-driven-development]]
- [[profiling-agents]]
- [[2026-06-11-ai-playtika]]
