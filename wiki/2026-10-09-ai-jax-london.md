---
title: "Source: Agentic Engineering @ JAX London (2026-10-09)"
category: source
tags: [source, jax-london, conference, workshop, one-day]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

One-day hands-on conference workshop at JAX London with an international group of Java, TypeScript and Elixir developers, on the petclinic repo. The arc: package team knowledge as custom agents, skills, plugin marketplaces and hooks (extend a skill you don't own with a PostToolUse hook); make the agent safe with layered guardrails, `/sandbox` credential masking and microVMs; then take a real feature from grill-me through OpenSpec to a UI polished live with screenshots. Distinctive material: seat-vs-subscription economics and "over $50 is a red flag", Opus-orchestrated sub-agents over the bash Ralph loop, compaction as a code smell (re-inject rules with a SessionStart hook), MCP's three callbacks (elicitation, sampling, browser OAuth), cloud agents coordinating through commit messages, finding API consumers before a breaking change, and one LLM wiki per bounded context.

## Concept inventory

*Cost & models* — [[token-economy]] / [[prompt-caching]] (subsidised subscriptions, $5 cold "hi"), [[model-hierarchy]] / [[effort-setting]] (Opus high by default, guru models only to plan/critique).

*Packaging knowledge* — [[custom-agent]] (`claude --agent`), [[agent-skill]] (scripts call the LLM, goals not roadmap), [[plugin-marketplace]] / [[skill-marketplace-security]] (feedback skill first), [[agents-md-discipline]] / [[self-improving-knowledge]] (Sunday compaction cron), [[sycophancy]] / [[two-layer-memory]] ("challenge my prompts", "teach me Y"), [[llm-wiki]] (one per bounded context), [[elder-wisdom-mining]].

*Guardrails & safety* — [[codeowners-elders]] (needs branch protection), [[tripwire]] ("catch, don't forbid"), [[agent-permissions]] / [[approval-fatigue]] (prose deny rules, Autopilot ≠ auto mode), [[os-sandbox]] / [[secret-zero]] / [[docker-sandboxing]] (credential masking, microVM), [[production-safety]] (one key per week).

*Context & orchestration* — [[dumb-zone]] / [[handover]] / [[agent-lifecycle-hooks]] (compaction is a smell), [[ralph-loop]] / [[sub-agents]], [[cloud-review-workflow]] / [[worktree]] (cloud sessions, commit-message coordination), [[headless-claude]].

*Requirements, specs & review* — [[grill-me]] (first question, real data, collation), [[spec-driven-development]] / [[openspec]] / [[three-amigos]] (task triage, QA brings the attacks), [[acceptance-test-bdd]] / [[feedback-loop]] (before/after videos), [[field-reality-diagram]] / [[deterministic-diagrams]] (find consumers, shadow deployment), [[gray-factory]], [[vibe-fixer]] (skills in git for business).

*MCP* — [[model-context-protocol]] / [[cli-vs-mcp-tradeoff]] (three callbacks, stdio copies, tool-level scopes, headless-MCP debate).

## See also
- [[2026-10-06-gray-factory-devoxx]]
- [[2026-10-01-ai-bank]]
- [[2026-10-13-ai-bank]]
- [[plugin-marketplace]]
