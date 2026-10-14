---
title: "Source: AI-Assisted Coding @ an industrial-equipment company (2026-09-14..15)"
category: source
tags: [source, workshop, two-day, claude-code, industrial]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop (Romanian-language summary) at an industrial-equipment company, run on Claude Code with Sonnet. Day 1 covered agent fundamentals — harness vs model, AGENTS.md as "only what it can't deduce and you can't script", skills and glob-scoped rules, the system-prompt bill (a bare "hi" at 49k tokens), monthly `/context` hygiene, prompt-cache economics, sub-agent forking — plus what agents mean for QA and for organisational adoption (champions, agentifying testers and POs). Day 2 went through spec-driven development on a vague pagination ticket (when to stop the grill, "why are we doing this ticket?"), the prompt-vs-complexity ladder, guided human review of a 1,000-line diff in one 45-minute session, team chat bots, and security: rogue-agent behaviours, slopsquatting, the axios compromise, jailbreaking a public chatbot and prompt injection. It also introduced elder-wisdom mining.

## Concept inventory

*Fundamentals & cost* — [[harness]], [[model-hierarchy]], [[effort-setting]], [[token-economy]] (49k-token "hi", MCP cleanup 80k→46k, weekly budget, estimates in hours and dollars), [[prompt-caching]] (cache tied to model and effort), [[sub-agents]] (fork mode), [[caveman]] / output styles.

*Knowledge engineering* — [[agents-md]], [[agents-md-discipline]], [[progressive-disclosure]] (`.claude/rules` with globs, mention vs `@include`), [[drift]] (agent trusts the markdown; pre-push blocks uncommitted generated artifacts), [[hallucination]] (glossary file), [[cross-repo-knowledge]] (team skills repo symlinked everywhere), [[self-improving-knowledge]], [[elder-wisdom-mining]].

*Workflow* — [[plan-mode]] (five-rung complexity ladder), [[grill-me]] (when to stop, "why this ticket?"), [[spec-driven-development]], [[sprint-design-quiz]], [[chop-ai-waiter]] (zero copy-paste), [[cli-vs-mcp-tradeoff]], [[observability-mcp]] (semantic log search), [[chatops-agent]], [[vibe-fixer]] (PO prototypes).

*Review* — [[code-review]] (1,000 lines in 45 minutes, outside-in, OpenAPI diff, two human brains), [[review-by-images]], [[multi-model-review]] (one round, `/code-review` and `/security-review`), [[assumptions-ledger]], [[acceptance-test-bdd]] (StrongDM's hidden tests, Scenario Outline from spec).

*Security* — [[sycophancy]] ("I will verify you at the end"), [[docker-sandboxing]] (agent edits its own policy), [[supply-chain-attack]], [[slopsquatting]], [[skill-marketplace-security]], [[jailbreak-ladder]] (adversarial poetry), [[prompt-injection]] (grey-on-white job ad, Grok wallet), [[keyset-pagination]].

*Human factors* — [[skill-erosion]] (warm-up, AI-on-the-table interviews), [[ai-addiction]] (ride the agent), [[agentic-engineering]] (champions, agentify testers and POs).

## See also
- [[2026-09-07-ai-bank]]
- [[2026-09-16-ai-safety-critical]]
- [[elder-wisdom-mining]]
