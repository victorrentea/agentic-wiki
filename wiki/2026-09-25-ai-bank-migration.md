---
title: "Source: Agentic Coding @ a bank (2026-09-25)"
category: source
tags: [source, bank, workshop, one-day, hybrid, legacy-migration, copilot]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

One-day hybrid workshop (room + remote) for a banking team midway through migrating an EJB2 + Struts/JSP application onto Spring Boot Modulith + Thymeleaf, with GitHub Copilot as the only granted harness. The arc: spend AI credits wisely (model tiers, token-dollar estimates) → give the agent context, tools and guardrails (CLI-wrapped skills for the tracker, wiki and database, deterministic diagrams, CODEOWNERS) → make it grill and plan the migration with you before it codes (OpenSpec, hand-crafted first screen, frozen DAO layer, contract testing). Strong on legacy-migration tactics and on wiring a Copilot-only shop to its own CLIs.

## Concept inventory

*Economics & models* — [[model-hierarchy]] (top/mid/intern tiers, auto-routing on the first prompt), [[token-economy]] (estimate in hours + dollars, never paste logs), [[prompt-caching]] (subscription vs API ratio), [[harness]] (Copilot CLI vs IntelliJ plugin, prompt-driven typing).

*Context & knowledge* — [[agents-md-discipline]] (name the odd stack, house rules only), [[progressive-disclosure]] (path-scoped instructions), [[prompt-injection]] (never paste a web page), [[self-improving-knowledge]] (doc gardening), [[deterministic-diagrams]], [[archunit-drift-control]].

*Tools & skills* — [[openrewrite]] / [[determinism-over-llm]], [[cli-vs-mcp-tradeoff]] (tracker/wiki/DB CLIs), [[agent-skill]] (distill a session into a scripted skill), [[skills-governance]] (org-wide tool wrappers vs per-team conventions), [[skill-marketplace-security]] (auto-update), [[browser-mcp]] (Playwright planner/generator/healer).

*Review & guardrails* — [[multi-model-review]] (report-only, reviewer outranks author), [[sub-agents]] (falsifying sub-agent), [[hallucination]], [[agent-permissions]] / [[approval-fatigue]], [[secret-zero]], [[reward-hacking]], [[codeowners-elders]], [[code-review]] (shrink the diff), [[production-safety]] (fence the agent on real data).

*Requirements & specs* — [[grill-me]] (why first, how many before how fast, QA.md), [[spec-driven-development]] / [[openspec]] (one-week cap), [[three-amigos]], [[acceptance-test-bdd]] (narrate a test once, ticket → failing test → fix), [[feedback-loop]].

*Legacy migration* — [[legacy-migration-stages]] (first screen by hand, frozen layer, vertical slices), [[light-dark-code]], [[openapi-test-fixtures]] / [[openapi-contract-sync]] / [[drift]] (contract testing), [[agentic-corporation]] (why dark factories fail on legacy), [[dumb-zone]] (yellow bar, context anxiety).

## See also
- [[2026-09-28-ai-gaming-tech]]
- [[2026-06-26-ai-agentic-how]]
- [[legacy-migration-stages]]
- [[model-hierarchy]]
