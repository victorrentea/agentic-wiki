---
title: "Source: AI-Assisted Coding @ an enterprise (2026-09-30, 2026-10-05)"
category: source
tags: [source, enterprise, workshop, two-day, romanian]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop held in Romanian on the public petclinic repo. Day 1: which harness and model for which job (a planner tier above Opus, Sonnet-high vs Opus-medium for coding, cheap tiers for docs/logs), how to build the agent's context (AGENTS.md under 100 lines, path-scoped skills and `.claude/rules`, hooks, MCP vs CLI-in-a-skill) and how to make it prove its own work — with the running theme that subsidised tokens should be burnt on hard historical debt, not on typing. Day 2: how to verify what the agent wrote before reading the code — a second adversarial agent, "who fixes" rules, deterministic gates, living diagrams, semantic coverage of requirements, Code City — plus agent identity (RFC 8693 on-behalf-of tokens), worktree pitfalls, brain fry, vibe addiction and the engineer's role as a curious generalist.

## Concept inventory

*Harness, models & cost* — [[harness]], [[model-hierarchy]] (planner tier, Claude vs Codex temperament), [[effort-setting]], [[token-economy]] (prompt in English, 100 skills = 6–10k tokens), [[prompt-caching]] (1h subscription vs 5-min API TTL, model switch breaks cache), [[dumb-zone]] (600k Opus / 150k Sonnet), [[handover]].

*Context & guardrails* — [[agents-md-discipline]] / [[claude-md]] / [[progressive-disclosure]] (`.claude/rules`, paths-skill gotcha), [[tripwire]] / [[static-analysis-gauntlet]] / [[openapi-contract-sync]], [[agent-lifecycle-hooks]] (Stop-hook tests, cache keep-alive), [[self-improving-knowledge]], [[plugin-marketplace]] / [[skill-marketplace-security]].

*Verification* — [[sub-agents]] / [[multi-model-review]] (adversarial agent, who fixes, `/simplify`), [[assumptions-ledger]], [[code-review]] (small PRs, 4-of-5 our fault), [[gray-factory]], [[semantic-coverage]], [[acceptance-test-bdd]] (fixture tree, component tests), [[deterministic-diagrams]] / [[review-by-images]], [[field-reality-diagram]] / [[c4-model]], [[code-city]], [[keyset-pagination]].

*Specs & requirements* — [[grill-me]] (glossary, cultural context), [[spec-driven-development]] / [[openspec]] / [[three-amigos]] (dose the process), [[ralph-loop]], [[sprint-design-quiz]].

*Tools & security* — [[model-context-protocol]] / [[cli-vs-mcp-tradeoff]] / [[sampling]], [[custom-agent]], [[agent-auth]] / [[tool-context]] (RFC 8693), [[prompt-injection]] / [[lethal-trifecta]], [[worktree]] (port fights), [[profiling-agents]] / [[feedback-loop]], [[legacy-migration-stages]].

*Human factors* — [[vibe-coding]] (redefined), [[ai-addiction]] (vibe addiction), [[agentic-engineering]] / [[skill-erosion]] (generalist, learning skills, brain fry), [[vibe-fixer]], [[agentic-corporation]] (holdout tests), [[draft-only-email]].

## See also
- [[2026-09-28-ai-gaming-tech]]
- [[2026-09-25-ai-bank-migration]]
- [[2026-10-01-ai-bank]]
- [[semantic-coverage]]
