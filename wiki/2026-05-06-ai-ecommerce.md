---
title: "Source: AI-Assisted Coding @ an e-commerce company (2026-05-06, 2026-05-08)"
category: source
tags: [source, e-commerce, workshop, two-day, kiro, spec-driven-development, security, architecture-guardrails]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop for a product engineering team (developers plus DevOps/platform engineers) working in Kiro IDE and Kiro CLI. Day 1 covered LLM fundamentals (tokens, the context window and the dumb zone, prompt caching, model vs harness, thinking cost) and then a long hands-on spec-driven exercise on Spring Pet Clinic (a unified owner search) built to surface every friction point: invented personas, unrequested behaviour, race conditions, "all tasks done" while the screen stayed empty. Day 2 went into brainstorming skills and multi-agent review, hooks, what to commit, the supply-chain and prompt-injection threat model (lethal trifecta, Gandalf, the Railway story), architecture-as-code guardrails that make the cheapest path the correct one, and AI against legacy code and production incidents.

## Concept inventory

*Fundamentals* — [[context-window]], [[dumb-zone]], [[token-economy]], [[prompt-caching]] (stable prefix, vendor discounts), [[effort-setting]] (thinking billed as output), [[model-hierarchy]] (no Opus for CSS, silent auto-routing), [[harness]], [[toon]], [[hallucination]] (grounding by citation, model collapse), [[knowledge-cutoff]], [[sycophancy]].

*Spec-driven work* — [[kiro]] (EARS, steering inclusion modes, hooks), [[spec-driven-development]] (business need over mechanism, [[answer-injection]], invented personas, archive specs), [[plan-mode]] (vibe vs spec rule), [[grill-me]], [[superpowers]] (brainstorm → 2× review cadence), [[non-goals]], [[red-green-tdd]], [[feedback-loop]] (proof-loop test, "done ≠ works"), [[acceptance-test-bdd]], [[browser-mcp]] (Chrome DevTools design loop).

*Review & knowledge* — [[code-review]] (single owner per diff), [[multi-model-review]] (review the markdown too), [[sub-agents]], [[agent-lifecycle-hooks]] (silent on success, judge hook, recursion), [[determinism-over-llm]], [[agents-md-discipline]] (bias to add code), [[cross-repo-knowledge]], [[skills-governance]], [[light-dark-code]].

*Security* — [[supply-chain-attack]] (npx @latest, minimum release age, [[gitleaks]]), [[slopsquatting]], [[tool-poisoning]], [[skill-marketplace-security]], [[prompt-injection]], [[jailbreak-ladder]] (many-shot, adversarial suffixes, Gandalf, model-swap evasion), [[lethal-trifecta]], [[jwt-identity]] / [[mcp-tenant-isolation]], [[os-sandbox]], [[docker-sandboxing]], [[agent-permissions]] (guard the guards), [[elicitation]], [[production-safety]].

*Architecture guardrails & operations* — [[deterministic-diagrams]], [[openapi-contract-sync]], [[archunit-drift-control]] (path of least resistance), [[codeowners-elders]], [[c4-model]], [[reward-hacking]] (eval awareness), [[firefighter-agent]], [[observability-mcp]], [[legacy-migration-stages]], [[openrewrite]], [[record-replay-refactoring]], [[profiling-agents]], [[agentic-corporation]] (team-chat bot, ticket board as IDE), [[vibe-coding]], [[agentic-engineering]].

## See also
- [[2026-05-11-ai-software-vendor]]
- [[2026-05-13-ai-it-services]]
- [[2026-05-18-secure-coding-payments]]
- [[kiro]]
- [[spec-driven-development]]
