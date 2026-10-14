---
title: Enterprise Architect Agent
category: pattern
tags: [architecture, microservices, contracts, drift, background-agent, cross-repo]
sources: ["[[2026-04-16-ai-safety-critical]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

An enterprise architect agent is a background agent — typically a long-context Opus — that periodically reads every service's published contracts and architecture notes from one central repository and flags cross-service incoherence and [[drift]] before it reaches production.

## The setup

1. **Each microservice keeps its contracts in git** — `swagger.yaml` / OpenAPI, Avro schemas, event definitions, plus whatever architecture notes it has (see [[openapi-contract-sync]], [[c4-model]]).
2. **CI pushes them into one central repository.** A GitHub Actions job (or equivalent) in every repo copies its contracts into the shared "big repo" on merge — the same mechanism described in [[cross-repo-knowledge]].
3. **The meta-agent cross-checks what each repo believes about its neighbours.** Its output is a human-facing finding, not a fix: *"You (A) think you talk to B, but B thinks it talks to C — you're out of sync, have a meeting and align."*

The pattern was reported as already running at some companies ([[2026-04-16-ai-safety-critical]]).

## The developer side

The same central repo serves the agents doing feature work. When working on one service, the developer only says *"Boss, see that big repo? Everything's there"* — the coding agent clones it and scans for the neighbour contracts it needs, instead of guessing or asking another team ([[2026-04-16-ai-safety-critical]]). It is a lightweight, file-based alternative to a [[code-graph]] across repos.

## Why it works

It gives a **gravitational pull toward consistency** while every repo stays autonomous: no central team approves changes, but divergence becomes visible quickly. Pair it with:
- **Consumer-driven contract tests** — the deterministic check for the incompatibilities the agent can only suspect (see [[determinism-over-llm]]).
- **Rule promotion** — a finding that recurs becomes a rule in the relevant repo's memory file, moving up the [[memory-layers]] from one team to all.
- **Diagrams of what actually runs** — [[field-reality-diagram]] for comparing the intended architecture with the observed one.

At company scale this is one of the standing agents of an [[agentic-corporation]]: like a [[policeman-agent]] or [[doc-gardener]], it runs on a schedule rather than on request.

## See also
- [[cross-repo-knowledge]]
- [[drift]]
- [[c4-model]]
- [[field-reality-diagram]]
- [[openapi-contract-sync]]
- [[memory-layers]]
- [[agentic-corporation]]
- [[doc-gardener]]
- [[policeman-agent]]
