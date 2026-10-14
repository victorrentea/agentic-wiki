---
title: "Source: Agentic Engineering @ an insurer (2026-09-02..03)"
category: source
tags: [source, insurer, workshop, two-day, onsite]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day onsite workshop (in Romanian) with ≈26 developers of an insurer's tech hub who had already been through an internal Claude bootcamp. Day 1 ran from CLAUDE.md hygiene and guardrails on the petclinic demo, through skills, hooks, the system prompt and the agentic loop, to the real cost of context (prompt-cache TTL on subscription vs API key, quota, compaction), then real tickets: a bug fixed without opening an editor (Playwright + Gherkin + before/after video) and a grilled pagination ticket whose decisions landed in a `QA.md`. Day 2 turned the grill output into an OpenSpec change, argued tracer bullets over layer-by-layer big-bangs, inventoried internal tools wired to agents (including a service knowledge-base MCP), reviewed large diffs with script-generated artifacts, defended architecture with ArchUnit + CODEOWNERS, and closed with security: prompt injection's accidental entry points, the lethal trifecta, why `deny Bash(...)` is not security, and microVM sandboxes with placeholder keys. Motto: *you're no longer the smartest in the room, even when you're alone.*

## Concept inventory

*Models & cost* — [[model-hierarchy]] (Fable tier, advisor pattern, Fable-orchestrates-Opus), [[effort-setting]] (medium for code), [[prompt-caching]] (1h subscription / 5 min API key, model/effort switch invalidates cache, channels), [[token-economy]] (≈700k of 900k are tool results, RTK/caveman measured), [[caveman]], [[rtk]], [[dumb-zone]] (needle-in-a-haystack after ≈200k, Context Rot), [[sub-agents]], [[harness]] (a harness that uploads your repo).

*Knowledge engineering* — [[claude-md]] (delete half per model generation, cover-your-ass comments, `volumetrie.md`), [[agents-md]], [[agent-skill]] (≥60-token descriptions, 30–40 skills, path rules), [[skill-marketplace-security]] (ToxicSkills, Cisco Skill Scanner), [[plugin-marketplace]], [[skills-governance]], [[agent-lifecycle-hooks]] (hook-extending a plugin skill), [[memory-layers]], [[self-improving-knowledge]] (a `feedback` skill), [[elder-wisdom-mining]], [[legacy-migration-stages]] (mining requirements from undocumented legacy), [[llm-wiki]] (service knowledge-base MCP).

*Requirements & specs* — [[plan-mode]], [[grill-me]], [[hallucination]] (context, not prompt), [[keyset-pagination]] (collation), [[spec-driven-development]] (framework comparison, spec by day / apply by night, scaffolding deleted after ≈3 months), [[openspec]], [[superpowers]], [[tracer-bullets]], [[decision-context]].

*Review & testing* — [[code-review]] (new conversation, `/simplify`, "no green build, no review", dead four-eyes), [[multi-model-review]], [[human-review-dashboard]], [[semantic-coverage]], [[review-by-images]], [[field-reality-diagram]], [[archunit-drift-control]], [[codeowners-elders]] (needs branch protection), [[ci-green-loop]], [[acceptance-test-bdd]] (test tree, PACT), [[feedback-loop]], [[chop-ai-waiter]] (Playwright turns GUIs into CLIs).

*Integration* — [[model-context-protocol]] (CLI wins tokens, MCP wins authorization, gateway), [[sampling]] (client support caveat), [[observability-mcp]] (read-only Grafana, salted-hash pseudonymization), [[vibe-coding]] (definition, SaaS apocalypse, agentification to the left), [[vibe-fixer]], [[code-city]].

*Security* — [[agent-permissions]] (auto mode, Bash deny parsing), [[prompt-injection]] (accidental entry points), [[tool-poisoning]], [[slopsquatting]], [[lethal-trifecta]], [[judge-llm]] / [[guardrails]] (row-level security on JWT `sub`), [[jwt-identity]], [[jailbreak-ladder]] (Heretic, refusal direction), [[data-governance]], [[docker-sandboxing]].

*Human factors* — [[skill-erosion]] (Anthropic RCT −17%, METR, Hawthorne, Learning output style), [[ai-calisthenics]], [[sycophancy]].

## See also
- [[2026-08-31-ai-software-vendor]]
- [[2026-08-13-ai-insurer]]
- [[2026-06-26-ai-agentic-how]]
