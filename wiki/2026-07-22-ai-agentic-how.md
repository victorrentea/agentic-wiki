---
title: "Source: AI-Assisted Coding @ agentic.how (2026-07-22)"
category: source
tags: [source, agentic-how, workshop, one-day, remote]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Open one-day remote workshop for senior developers from several companies — 70% of the room said AI already writes most of their code. The morning covered tool hygiene and token economics (CLI over MCP, the cache window, handing over at ≈50% context instead of compacting); after lunch the group grilled a vague pagination ticket, walked through the three spec documents, and learned why every "stupid question" from the agent is a missing line in CLAUDE.md. The afternoon turned to the vibe horizon (≈2–3 months, then vibe heresy), the vibe-fixer's devibing moves, speeding up the agent's own test loop, adversarial reviewers that the coder must not invoke itself, and why the agentic transformation has to start bottom-up.

## Concept inventory

*Foundations & economics* — [[harness]], [[model-hierarchy]], [[effort-setting]] (xhigh to plan, lower to spit code), [[claude-code-router]], [[dumb-zone]], [[handover]] (hand over at ≈50%), [[sub-agents]] (warn, don't micromanage), [[token-economy]], [[prompt-caching]], [[caveman]], [[cli-vs-mcp-tradeoff]], [[rtk]], [[headroom]], [[context7]], [[knowledge-cutoff]].

*Knowledge engineering* — [[claude-md]], [[agents-md-discipline]] (stupid questions are missing context), [[self-improving-knowledge]] (mining PR comments, Sonnet as sanitizer), [[agent-skill]] (path-scoped loading), [[progressive-disclosure]], [[skill-marketplace-security]] (Snyk ToxicSkills ≈37%), [[determinism-over-llm]], [[openapi-contract-sync]].

*Feedback loops* — [[feedback-loop]] (test tiers, formatter-hook cache gotcha, speeding up the loop), [[agent-lifecycle-hooks]], [[ci-green-loop]], [[ai-use-cases-beyond-coding]].

*Requirements & specs* — [[grill-me]], [[answer-injection]], [[spec-driven-development]], [[openspec]], [[non-goals]], [[three-amigos]], [[mob-spec-review]], [[plan-mode]].

*Vibe coding* — [[vibe-coding]] (an act of faith, the vibe horizon), [[vibe-heresy]], [[vibe-fixer]] (devibing moves, clean up don't regenerate), [[tracer-bullets]], [[code-city]], [[cognitive-debt]].

*Review* — [[multi-model-review]] (coder never invokes its reviewers), [[code-review]] (the killer guided-review prompt), [[assumptions-ledger]], [[static-analysis-gauntlet]], [[review-by-images]], [[archunit-drift-control]], [[field-reality-diagram]], [[codeowners-elders]], [[agentic-corporation]].

*Connecting & safety* — [[chop-ai-waiter]] (four ways to connect), [[ai-calisthenics]], [[observability-mcp]] (local model emitting data patterns), [[sycophancy]], [[skill-erosion]] (scaffold-with-tests over explanations, TIL habit), [[agentic-engineering]] and [[ai-adoption]] (bottom-up transformation).

## See also
- [[2026-06-26-ai-agentic-how]]
- [[2026-07-01-ai-java-community]]
- [[2026-07-20-ai-telecom]]
