---
title: "Source: AI-Assisted Coding @ a consultancy (2026-08-10 – 2026-08-11)"
category: source
tags: [source, consultancy, workshop, two-day, claude-code]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day English-language workshop at a software consultancy. Day 1 covered the economics of running agents — what is really in the system prompt, tool definitions as a token tax, subscription vs API, measured (not advertised) token savers, context rot and permissions that don't contain an agent — then the "two hands" that squeeze hallucination out of the SDLC: specs on one side and requirement-level tests on the other, plus scripted architecture guardrails and code metrics that point at dangerous AI-written code. Day 2 went end to end through spec-driven development, adversarial review in a separate conversation, guided human review driven by the coder's own uncertainty, closing the loop back into the markdowns, and AI security: prompt-injection vectors, the ToxicSkills marketplace audit, credential isolation and real containment.

## Concept inventory

*Economics* — [[system-prompt]], [[context-window]] (tool calls ≈95% of context), [[token-economy]] (tool search, status-line cost), [[prompt-caching]], [[rtk]] / [[headroom]] / [[caveman]] (JetBrains measurements), [[model-hierarchy]] (model per phase), [[effort-setting]], [[claude-code-router]] (open-weights insurance).

*Context & permissions* — [[dumb-zone]] (kill past half the window), [[sub-agents]], [[handover]], [[agent-permissions]] (deny on Bash is bypassable; auto mode holds), [[progressive-disclosure]] (path-scoped rules).

*Requirements & spec* — [[grill-me]], [[spec-driven-development]] (spec lifecycle: split, then delete after ≈3 months), [[non-goals]], [[mob-spec-review]] (paragraphs read by different voices), [[claude-md]] (prune every two weeks, ≈150 lines).

*Tests & guardrails* — [[acceptance-test-bdd]] (BDD's false dream, glue code, black-box at the API boundary), [[archunit-drift-control]], [[field-reality-diagram]], [[codeowners-elders]], [[bug-heatmap]], [[code-city]], [[legacy-migration-stages]], [[mutation-testing]], [[openrewrite]].

*Review* — [[code-review]] (review is the bottleneck; ≈20 impeccable PRs before relaxing), [[multi-model-review]], [[assumptions-ledger]], [[stacked-prs]], [[self-improving-knowledge]] (which markdown let the slop through?), [[gray-factory]], [[agentic-corporation]].

*Security* — [[skill-marketplace-security]] (ToxicSkills numbers, SkillSpector), [[prompt-injection]], [[jailbreak-ladder]] (adversarial suffixes), [[production-safety]] (credential audits by a weaker model), [[docker-sandboxing]], [[dont-run-in-home-folder]], [[memory-poisoning]].

## See also
- [[2026-08-03-ai-telecom-ops-qa]]
- [[2026-08-13-ai-insurer]]
- [[2026-09-28-ai-gaming-tech]]
- [[2026-06-26-ai-agentic-how]]
