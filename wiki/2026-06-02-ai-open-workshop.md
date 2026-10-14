---
title: "Source: AI-Assisted Coding @ an open workshop (2026-06-02)"
category: source
tags: [source, open-workshop, workshop, one-day, english]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

One-day English workshop for mid-senior developers, using PetClinic issue #25 (sorting + pagination on the owners screen) as the running example from grill to merged code. The morning catalogued how LLMs break — hallucination triggers, sycophancy, premature fixation, answer injection, spiraling — and how to keep context lean (pain-fed CLAUDE.md, nested files, skills, sub-agents, explicit compaction). The afternoon ran the feature through grill-me and OpenSpec with parallel sub-agent waves, introduced tracer bullets and wide acceptance tests, surveyed MCPs and CLIs, then laid out the converged multi-agent workflow with its quality and architecture guardrails (hooks taxonomy, git hooks, post-push watcher, diagrams kept honest by tests) and a security block on permissions, red-teaming and sandbox layers. It closed with an unplanned live proof: an 8-hour transcript choked Sonnet and needed Opus 1M.

## Concept inventory

*How LLMs break* — [[hallucination]] (SpecRot, >85 rules, too-close-to-standard; demand proof links), [[sycophancy]], [[red-green-tdd]] (premature fixation), [[answer-injection]], [[ai-spiraling]], [[dumb-zone]], [[handover]] (explicit compaction, rewind, one-shot restart).

*Context engineering* — [[claude-md]] / [[agents-md-discipline]] (arXiv study on generated context files), [[progressive-disclosure]], [[agent-skill]], [[cross-repo-knowledge]], [[token-economy]], [[rtk]], [[caveman]], [[code-graph]].

*Specs* — [[grill-me]] (composite-name trap), [[openspec]], [[spec-driven-development]] (when to go SDD), [[tracer-bullets]], [[acceptance-test-bdd]] (test wide, example tables), [[non-goals]].

*Workflow & review* — [[agentic-review-pipeline]], [[multi-model-review]] (1 writes, 2 review, then human), [[code-review]] (risk-ordered review), [[agentic-corporation]] (1-pizza AI pods), [[chatops-agent]].

*Guardrails* — [[agent-lifecycle-hooks]] (full taxonomy), [[enforcement-ladder]] (agent hooks → git hooks → CI), [[ci-green-loop]] (same-context watcher), [[gitleaks]], [[openapi-contract-sync]], [[archunit-drift-control]], [[codeowners-elders]], [[code-city]].

*MCP & tools* — [[model-context-protocol]], [[elicitation]], [[cli-vs-mcp-tradeoff]] (gh ≪ GitHub MCP), [[browser-mcp]], [[observability-mcp]] (runaway queries on a shared DB).

*Security* — [[agent-permissions]] (auto-mode miss rate, red-teaming), [[os-sandbox]], [[docker-sandboxing]], [[secret-zero]], [[supply-chain-attack]], [[slopsquatting]], [[skill-marketplace-security]].

## See also
- [[2026-05-27-ai-open-workshop]]
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-05-ai-consultancy]]
