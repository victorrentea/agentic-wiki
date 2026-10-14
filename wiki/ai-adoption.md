---
title: Organizational AI Adoption
category: concept
tags: [adoption, organization, change-management, leadership, metrics]
sources: ["[[2026-07-23-ai-software-product]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-30-ai-safety-critical]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

How an engineering organization actually moves to agentic development: bottom-up, measured by usage, with skeptics converted by pairing rather than by mandate.

## Bottom-up beats top-down

Top-down programmes die the moment nobody will sign *"I accept the business risk"*; a separate "agentic silo" in which no developer is accountable for the production deployment is a sick setup ([[2026-07-22-ai-agentic-how]]). Start on yourself — [[openspec]], [[grill-me|grilling]], AI [[code-review]] — and expect that shipping ≈3× faster simply exposes the **next bottleneck**: fuzzy requirements, a slow build, missing tests, legacy code.

The usual ladder of harnesses is **IDE plugin → agentic CLI → agentic in production** ([[2026-07-01-ai-java-community]]). The personal step matters: a ≈€90/month personal subscription *"makes you a different developer in two months"* ([[2026-07-03-ai-bank]]).

## Find the laggers by data

Token burn on useful work is the leading adoption indicator ([[token-economy]]). One team looks for the ["flat curve"](https://steve-yegge.medium.com/the-flat-curve-society-36c8b01eb33b) (Steve Yegge's term) — people burning less than ≈4M tokens/day — and pairs with them rather than chasing them ([[2026-07-23-ai-software-product]]).

## Converting skeptics

What worked in the room ([[2026-07-23-ai-software-product]]):
- **Uninstall the IDE completion plugin** whose stupidity soured them, and give them a premium Claude Code seat with Opus quota instead.
- Have them **review a PR they are proud of with Opus** — the findings sell themselves.
- **Pair on a grilling session** on a real story.
- Reframe seniors as **AI coaches** who offload their intuition into skills and memory files ([[elder-wisdom-mining]], [[agent-skill]]).
- **Vibe-code internal tooling** to show what is newly possible: transaction-propagation graphs, a [[c4-model]] reverse-engineered from 700 tables, an [[openrewrite]] recipe deleting 10k lines.
- *"AI won't take your job — an AI engineer will"* works, but it is the **last resort**. Lead with curiosity and pair programming.

## Context, not magic

Colleagues who report they stopped reading code had invested **quarters** in [[agents-md]] files and skills, on clean greenfield code, with large credit budgets — *"context, not magic"* ([[2026-07-30-ai-safety-critical]]). Expect a similar ramp: after 6–9 months of consistent expertise extraction on a non-legacy project, humans review ≈50% of AI code ([[gray-factory]]).

## Make the good path easy

- An internal [[plugin-marketplace]] installed by policy, plus one-click setups ([[2026-07-09-ai-live-casino]]).
- Measure a vendor-built knowledge MCP by **adoption, not delivery** — it is dead weight until someone uses it in anger.
- Realistic gains reported by one organization: ≈1.3× developer speed, 2–3× faster idea→production lead time.

## See also
- [[agentic-engineering]]
- [[harness]]
- [[plugin-marketplace]]
- [[skill-erosion]]
- [[gray-factory]]
- [[token-economy]]
- [[ai-addiction]]
- [[agentic-reconversion]]
