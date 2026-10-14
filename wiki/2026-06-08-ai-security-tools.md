---
title: "Source: AI-Assisted Coding @ a security-tools company (2026-06-08 – 2026-06-09)"
category: source
tags: [source, security-tools, workshop, two-day, romanian, security]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop (in Romanian) for the engineering team of a security-tools company standardising its AI usage on Claude Code + Codex, with exercises on PetClinic and, on day 2, group work on the team's own codebase. Day 1 covered model vs harness, what every prompt carries and costs, the dumb zone and compaction, sub-agents and Ralph loops, a push-watch hook as tripwire, what belongs in CLAUDE.md, Socratic anti-sycophancy prompting, skills, grilling issue #25, OpenSpec with TDD and Gherkin, and a testing-strategy debate (unit vs component vs e2e, characterization tests, WireMock). Day 2 moved to cloud coding, DB/custom/observability MCPs and elicitation, MCP vs CLI, team bots and token budgets, group exercises (build optimisation, mutation testing, an LLM phishing scanner, golden master), then supply chain, jailbreaks and prompt injection, permission modes and sandboxing, and hooks plus architecture surveillance as quality guardrails.

## Concept inventory

*Foundations* — [[harness]] (model ≠ harness; Cursor's multi-provider edge), [[chop-ai-waiter]], [[token-economy]] (status bar, tokens per outcome), [[prompt-caching]], [[dumb-zone]], [[handover]] (`/resume` staleness), [[sub-agents]], [[ralph-loop]], [[data-governance]] (personal vs company subscription).

*Knowledge & prompting* — [[claude-md]] (what to put, `chown` the untouchables), [[agents-md]] (only CLAUDE.md loads per sub-folder in Claude Code), [[agents-md-discipline]], [[caveman]], [[sycophancy]], [[agent-skill]] (framework skills, extract-a-skill), [[skill-marketplace-security]].

*Specs & testing* — [[grill-me]] (stale memory rule, don't grill alone), [[three-amigos]], [[keyset-pagination]] (pagination as a security control), [[openspec]], [[spec-driven-development]], [[red-green-tdd]], [[acceptance-test-bdd]] (component tests, WireMock), [[legacy-migration-stages]] (golden master, retroactive bug tests), [[mutation-testing]], [[feedback-loop]].

*Workflow & tools* — [[ci-green-loop]] (push-watch hook), [[tripwire]], [[cli-vs-mcp-tradeoff]] (Linear CLI), [[model-context-protocol]], [[elicitation]], [[observability-mcp]], [[browser-mcp]] (WebFetch can't see SPAs), [[chatops-agent]], [[claude-code-router]], [[agentic-engineering]].

*Security* — [[static-analysis-gauntlet]] (garbage in, SQL injection out), [[supply-chain-attack]], [[slopsquatting]], [[prompt-injection]], [[lethal-trifecta]], [[draft-only-email]] (cart, never buy), [[approval-fatigue]], [[agent-permissions]], [[os-sandbox]], [[docker-sandboxing]], [[secret-zero]], [[production-safety]] (disaster-planning prompt), [[judge-llm]] (LLM as aggregator, not authority).

*Guardrails & architecture* — [[agent-lifecycle-hooks]], [[enforcement-ladder]], [[gitleaks]], [[openapi-contract-sync]], [[archunit-drift-control]], [[field-reality-diagram]] / [[telemetry-by-default]], [[bug-heatmap]], [[codeowners-elders]].

## See also
- [[2026-06-05-ai-consultancy]]
- [[2026-05-25-ai-government]]
- [[2026-06-02-ai-open-workshop]]
