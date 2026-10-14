---
title: "Source: Agentic Engineering @ a safety-critical systems vendor (2026-09-16..17)"
category: source
tags: [source, workshop, two-day, copilot, safety-critical]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop (Romanian-language summary) with ≈23 engineers of a safety-critical systems vendor, on restricted corporate laptops where third-party MCP servers were disabled by policy and GitHub Copilot was the base tool. Day 1 set the foundations — Copilot CLI with a live-cost status line, model and effort choice, AGENTS.md content and nesting, script-don't-write guardrails, skills, hooks, the 27k-token system prompt, malicious skills, grilling a three-line ticket, OpenSpec's four audiences, sycophancy and key hygiene. Day 2 was hard practice: multi-repo work and "who calls whom" from traces and C4, logs over read-only SSH, orchestrator kill thresholds, shift-left, the vibe-fixer and agent-as-teacher, guided and adversarial review, stacked PRs, safety-critical reliability testing, supply chain (including protestware aimed at LLMs) and chatbot guardrails and the lethal trifecta. Notable for the "deaf, not autonomous" autopilot failure and a fact-check of the $20 cache-miss anecdote.

## Concept inventory

*Harness & context* — [[harness]] (plugins only in Claude Code and Copilot CLI, steering queue), [[agent-permissions]] (global autopilot = deaf), [[prompt-caching]] ($20 "hi" restated at current prices; golden tokens), [[dumb-zone]] (Opus ≈600k / Sonnet ≈150–180k kill thresholds, smart zone), [[sub-agents]], [[ralph-loop]] (exit at 150k), [[model-hierarchy]].

*Knowledge engineering* — [[agents-md-discipline]] (content checklist, refactor legacy instead of writing a rule, conversation retros, non-English precision cost), [[agent-skill]] (test activation, skill-extension override, PowerShell twins), [[cross-repo-knowledge]] (multi-repo, who-calls-whom sources), [[c4-model]].

*Workflow & review* — [[spec-driven-development]] (narrow API, plan mode writes no files, runbook.md), [[plan-mode]], [[code-review]] (review ladder, stacked PRs, one-shot perfection, merge stays human), [[assumptions-ledger]], [[multi-model-review]], [[review-by-images]] / [[deterministic-diagrams]] (oasdiff, AsyncAPI), [[acceptance-test-bdd]] (Toxiproxy nightly reliability), [[ci-green-loop]] (Sonar-fix skill).

*Ops & observability* — [[observability-mcp]] (read-only SSH to logs, the $5–10k 1 GB log), [[firefighter-agent]], [[profiling-agents]] (17 leaks from heap dumps).

*Security* — [[skill-marketplace-security]] (skillspector, canary marker), [[supply-chain-attack]] (four local-execution vectors, jqwik protestware), [[lethal-trifecta]] (anonymous vs authenticated agent instances), [[guardrails]] (input and output guards, semantic-chaos filter), [[jailbreak-ladder]] (split attack), [[dont-run-in-home-folder]], [[production-safety]].

*Human factors* — [[vibe-coding]] ("committed trade-offs you didn't understand"), [[vibe-fixer]] (don't cut business's git access), [[skill-erosion]] (agent-as-teacher, risk-proportional understanding), [[agentic-engineering]] (live UX polish with business), [[context7]].

## See also
- [[2026-09-14-ai-industrial]]
- [[2026-09-18-ai-agentic-how]]
- [[2026-04-16-ai-safety-critical]]
