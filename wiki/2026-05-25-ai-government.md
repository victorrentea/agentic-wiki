---
title: "Source: AI-Assisted Coding @ a government organization (2026-05-25 – 2026-05-26)"
category: source
tags: [source, workshop, two-day, romanian, government, in-house-model]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day, demand-driven workshop (in Romanian) for senior developers at a government organization whose only permitted model is a self-hosted open-weight coder (≈130k context, no vision) behind an IDE chat plugin with no bash, MCP or agent mode. Day 1 was about mindset — how a good human treats an agent, the dumb zone, sycophancy, sub-agents explained as delegating to a new hire, skills, and a live spec-driven exercise with OpenSpec and Gherkin on PetClinic. Day 2 went deep on containment (Docker sandbox, permissions, npm supply chain), a custom MCP, CODEOWNERS and architecture guardrails, plus several hours of non-agentic consulting (event sourcing, rate limiting, XSS, job schedulers) that the wiki doesn't track. A recurring theme: in-house models are *less* safe and *less* capable than frontier ones, so the process (SDD, sub-agents per task, guardrails) must compensate.

## Concept inventory

*Foundations* — [[context-window]], [[dumb-zone]] (`/clear` past ≈100k, not `/compact`), [[prompt-caching]] (the harness places cache breakpoints), [[knowledge-cutoff]] + [[context7]], [[model-hierarchy]] (Sonnet→Opus escalation; in-house models weaker on safety and vision), [[feedback-loop]] (no bash = no loop).

*Prompting & human factors* — [[sycophancy]] (point at the exact line; non-devs drive agents better), [[answer-injection]] (describe the problem, not the solution), [[hallucination]] (a smart human couldn't do it from this context either), [[skill-erosion]] (imagination, critical thinking, adaptability), [[wispr-flow]] (offline dictation).

*Delegation & specs* — [[sub-agents]] (the "Bianca" analogy, one sub-agent per task for weak models), [[agent-skill]], [[grill-me]], [[spec-driven-development]] / [[openspec]] (mandatory for weak models), [[red-green-tdd]], [[acceptance-test-bdd]].

*Tools* — [[browser-mcp]] (accessibility tree when there's no vision), [[cli-vs-mcp-tradeoff]] (SQLcl for Oracle; MCP when the agent serves end users), [[observability-mcp]] (DB column comments, session replay), [[model-context-protocol]] (where MCP secrets live).

*Security & guardrails* — [[docker-sandboxing]] (concrete egress-allow-list recipe), [[agent-permissions]], [[supply-chain-attack]] (npm hardening), [[prompt-injection]] (WebFetch vs curl, empathy jailbreaks), [[lethal-trifecta]], [[reward-hacking]] (agentic misalignment), [[codeowners-elders]], [[gitleaks]], [[archunit-drift-control]], [[openapi-contract-sync]].

## See also
- [[2026-05-27-ai-open-workshop]]
- [[2026-06-05-ai-consultancy]]
- [[2026-06-08-ai-security-tools]]
