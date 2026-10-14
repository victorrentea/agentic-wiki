---
title: "Source: AI-Assisted Coding @ a telecom operator — ops + QA (2026-08-03 – 2026-08-04)"
category: source
tags: [source, telecom, workshop, two-day, ops, qa, copilot]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop for a mixed group of ops engineers, testers and developers at a telecom, working on the petclinic demo repo. Day 1 climbed from "what changed in the industry" (a model, not a tool, was the inflection point; vibe coding defined by not reading the code) to the practical reconversion: moving knowledge from heads into the repo, fencing the agent with automatic guardrails (git hooks, a diagram guarded by ArchUnit, a CI-watching hook) and attacking a vague pagination ticket without burning the budget. Day 2 went hands-on for ops: MCP under the microscope and the `npx`/`uvx` supply-chain alarm, narrow tool sets for production agents, telemetry by default (OpenTelemetry + RED), chatbot latency tuning, bots that do work in team channels, spec-driven development with OpenSpec on the same feature, and a catalogue of the ways an agent gets prompt-injected.

## Concept inventory

*Industry & models* — [[vibe-coding]] (blast-radius line), [[model-hierarchy]] ("auto" mode = cheap), [[effort-setting]] (high, not xhigh), [[ollama]] open weights, [[multi-model-review]] (the 10⁻³ → 10⁻⁶ dice math).

*Knowledge in the repo* — [[agents-md-discipline]] ("the information is not on git"), [[agents-md]] red-Ferrari test, [[two-layer-memory]], [[progressive-disclosure]] (four mechanisms incl. [[tripwire]]), [[agent-skill]] (environment not steps; the temporal trap; internal marketplace).

*Token economy* — [[token-economy]] (thinking billed as output, usage-based Copilot billing, logs as "agent abuse"), [[prompt-caching]], [[dumb-zone]], [[rtk]] / [[headroom]] measured.

*Autonomy & guardrails* — [[agent-permissions]] (allow-all + autopilot, declarative ≠ real sandbox), [[ci-green-loop]], [[archunit-drift-control]], [[reward-hacking]] ("how did it make it green?"), [[feedback-loop]] (flaky tests, 15-minute build threshold), [[code-review]] (attack the backend, review windows, stop-the-line).

*Requirements & spec* — [[grill-me]], [[keyset-pagination]] (collation, tie-break), [[plan-mode]], [[spec-driven-development]], [[openspec]], [[three-amigos]], [[acceptance-test-bdd]], [[legacy-migration-stages]], [[light-dark-code]].

*Ops agents* — [[cli-vs-mcp-tradeoff]], [[mcp-transport]], [[custom-agent]], [[agent-auth]], [[firefighter-agent]], [[telemetry-by-default]], [[chatbot-latency-tuning]], [[chatops-agent]], [[observability-mcp]], [[browser-mcp]], [[context7]].

*Security* — [[supply-chain-attack]] (Shai-Hulud, pin + mirror + min release age), [[prompt-injection]] (any text in context is an instruction), [[tool-poisoning]], [[lethal-trifecta]], [[jailbreak-ladder]], [[production-safety]].

## See also
- [[2026-07-20-ai-telecom]]
- [[2026-08-06-ai-bank]]
- [[2026-08-10-ai-consultancy]]
- [[2026-06-26-ai-agentic-how]]
