---
title: "Source: AI-Assisted Coding @ a software vendor (2026-05-11..12)"
category: source
tags: [source, software-vendor, workshop, two-day, openspec, guardrails, hooks, testing, claude-code]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day Claude Code workshop for a design-minded product engineering team (the transcript is in Romanian). Day 1 ran a full spec cycle on Pet Clinic's owner search: requirements gathering and feature-creep control, brainstorming skills versus plan mode, the OpenSpec propose → apply → archive loop, a JOIN-plus-pagination bug caught in design review, and the guardrail ladder from Stop hooks to CODEOWNERS, followed by knowledge engineering (a hand-written ≈60-line CLAUDE.md backed by the ETH Zürich study, compact ADRs, a central context repo pulled at session start) and MCP versus CLI. Day 2 toured every Claude Code hook, red-teamed a gitleaks pre-commit hook live, extracted a skill out of CLAUDE.md, and made the case for reviewing high-signal generated artifacts (dependencies, OpenAPI, schema dump, diagrams, trace-derived sequences) before code, for feature tests over AI-written unit tests, for authorization inside tools rather than the prompt, and for the minimal agent loop.

## Concept inventory

*Specs & requirements* — [[spec-driven-development]] (AI Unified Process, word-by-word review), [[openspec]] (archive accumulates acceptance criteria, brownfield discovery), [[superpowers]] (brainstorming vs plan mode, when to switch to OpenSpec), [[plan-mode]], [[grill-me]] (the race-condition question), [[non-goals]].

*Knowledge engineering* — [[claude-md]] (ETH Zürich numbers, ≈60 lines, split past one screen), [[agents-md-discipline]], [[decision-context]], [[agent-skill]] (≈50-skill ceiling, skill vs include vs slash command, skill-creator), [[skills-benchmarking]], [[cross-repo-knowledge]] (central repo pulled at `SessionStart`), [[memory-layers]].

*Guardrails & hooks* — [[agent-lifecycle-hooks]] (full catalogue, Stop-hook gate), [[enforcement-ladder]], [[agent-permissions]] (domain-scoped fetch), [[gitleaks]], [[reward-hacking]] (secret moved past the hook, eval awareness), [[determinism-over-llm]], [[codeowners-elders]], [[ci-green-loop]] (auto-poll CI skill).

*Testing & verification* — [[feedback-loop]] ("you shot blind", fault injection, a requirement without a test is a wish), [[acceptance-test-bdd]] (tests at the edge, unit tests dying), [[red-green-tdd]], [[record-replay-refactoring]], [[browser-mcp]] (auto-generated user manual, frontend-design loop).

*Architecture & review* — [[code-review]] (review order by signal), [[deterministic-diagrams]], [[openapi-contract-sync]], [[archunit-drift-control]], [[c4-model]], [[field-reality-diagram]] (sequence diagrams from traces, N+1), [[review-by-images]].

*Agents, MCP & security* — [[agent-loop]], [[harness]] (when LangGraph makes sense), [[firefighter-agent]] (Bug Sherlock vs firefighter, bounded state machine), [[cli-vs-mcp-tradeoff]], [[model-context-protocol]], [[elicitation]], [[observability-mcp]] (data democratization), [[code-graph]], [[jwt-identity]], [[tool-context]], [[skill-marketplace-security]], [[agentic-corporation]] (named dark factory, team-chat bot), [[agentic-engineering]].

## See also
- [[2026-05-06-ai-ecommerce]]
- [[2026-05-13-ai-it-services]]
- [[openspec]]
- [[claude-md]]
