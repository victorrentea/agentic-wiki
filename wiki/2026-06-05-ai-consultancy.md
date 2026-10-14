---
title: "Source: AI-Assisted Coding @ a consultancy (2026-06-05)"
category: source
tags: [source, consultancy, workshop, one-day, romanian, remote]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

One-day online workshop (in Romanian, ≈16 participants) for one of the most advanced groups Victor had trained — heavy on debate, light on hands-on. The day ran fundamentals (what rides along with every prompt, token economics, the 5-minute cache that only expires while the agent waits for you) → context hygiene and Socratic correction → onboarding an agent on a brownfield codebase (pay the debt first) → skeptical skill evaluation → grilling the business and spec-driven development on PetClinic #25 → the full adversarial-review workflow seen at three companies → one-line ADRs, codemods and MCPs → security (supply chain, why deny rules leak, auto mode, OS sandbox, Docker) → three layers of quality guardrails. It closed with a live Q&A in which a compound `cd && git add` slipped past an explicit deny rule, and the agent then wrote — and self-validated with headless `claude -p` victims — a `PreToolUse` hook that really blocks it.

## Concept inventory

*Economics & context* — [[token-economy]] (fast mode, file "flushing"), [[prompt-caching]], [[model-hierarchy]] (Opus 4.8 as orchestrator, DeepSeek V4), [[dumb-zone]] (practical stop points), [[handover]] (one-shot restart), [[rtk]] (`rtk proxy`), [[caveman]].

*Knowledge* — [[claude-md]] (≈60-line benchmark, what earns a place), [[memory-layers]] (user-vs-project memory trap), [[progressive-disclosure]] (`@` inlines eagerly), [[agents-md-discipline]] (Socratic "cornfield" questioning), [[self-improving-knowledge]], [[drift]] (deep research expires), [[decision-context]] (one-line ADRs).

*Skills* — [[agent-skill]] (paths, context fork, model, allowed-tools), [[skills-benchmarking]], [[skill-marketplace-security]].

*Brownfield* — [[legacy-migration-stages]] (boy-scout 20%, retroactive bug tests), [[code-city]], [[openrewrite]] / [[determinism-over-llm]] (codemods, not AI rewrites).

*Specs & review* — [[grill-me]] (grilling the business at UX level), [[keyset-pagination]] (two-phase query, whitelisted sort keys), [[spec-driven-development]], [[mob-spec-review]] (Hawthorne effect), [[agentic-review-pipeline]] (tension zones, brain surgery on prompts, human-only merge), [[multi-model-review]], [[agentic-corporation]].

*MCP & bots* — [[elicitation]], [[cli-vs-mcp-tradeoff]], [[chatops-agent]], [[production-safety]] (Railway, monthly key audit), [[data-governance]] (in-house bots, GDPR).

*Security & guardrails* — [[supply-chain-attack]], [[secret-zero]] (fake-key proxy), [[agent-permissions]] (compound-command deny bypass), [[headless-claude]], [[os-sandbox]], [[docker-sandboxing]], [[prompt-injection]], [[lethal-trifecta]], [[agent-lifecycle-hooks]] (don't lint post-edit), [[enforcement-ladder]], [[gitleaks]], [[ci-green-loop]], [[ralph-loop]] (Stop-hook enforcement), [[archunit-drift-control]], [[codeowners-elders]].

## See also
- [[2026-06-02-ai-open-workshop]]
- [[2026-06-08-ai-security-tools]]
- [[2026-05-25-ai-government]]
