---
title: Decision Context
category: pattern
tags: [agents-md, claude-md, knowledge-engineering, adr, decisions]
sources: ["[[2026-06-26-ai-agentic-how]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-23-ai-software-product]]"]
created: 2026-06-26
updated: 2026-10-13
---

Decision Context is a compressed record of architectural and product decisions — one terse line per decision — that replaces verbose ADRs in an agent's memory file: *"an ADR for humans is one line for Opus."*

## Why not ADRs

A traditional Architecture Decision Record (ADR) describes problem, alternatives, and rationale across multiple paragraphs written for a human reviewer. That prose is expensive noise in the [[context-window]] — Opus already understands the alternatives; it needs only the decision and the constraint behind it. Pasting an ADR-heavy Confluence page into [[claude-md]] or [[agents-md-discipline|AGENTS.md]] burns tokens and accelerates the [[dumb-zone]] without adding knowledge the model didn't have.

## The format

One line per decision, enough for the model to expand from its own knowledge:

```
BullMQ — producer imports the worker directly; on failure retry N times with exponential backoff.
Sort owners server-side (1M rows); client-side is explicitly forbidden.
Users are millennials; latency >1s is unacceptable — no blocking synchronous calls to the analytics service.
```

The first clause names the decision; the second gives the *constraint* that explains it. The model knows what BullMQ is; it doesn't know that your team chose it, or why. One line = one correction to a false assumption.

## Where it lives

Decision Context belongs in [[claude-md]] (project tier) or a referenced `project-mission.md` for non-obvious context the model cannot infer from the codebase ("our users are millennials, no latency >1s"). Keep existing verbose ADRs; just don't paste them verbatim into the agent's memory file — point to them by path, and let the agent read them if it needs the full rationale.

The complement pattern is [[agents-md-discipline]]'s "seed-of-heresy Socratic loop": when the agent makes an architectural choice you disagree with, ask "why?" to uncover the false assumption, then encode that assumption as a one-line Decision Context entry — preventing an entire class of future mistakes.

## Lifecycle: where decisions come from

<span style="color:red">Decision lines are the residue of feature work. In one team's rhythm, roughly **1 PR in 20** appends a decision line; because it is an append to a table, concurrent PRs merge as fast-forwards rather than conflicts. **Product** decisions go to the issue tracker (Jira), not git. After merge, what stays in the repo is the feature tests (Gherkin, run on CI) and the decision lines — the architecture/plan markdowns die with the PR ([[2026-06-05-ai-consultancy]]).</span>

<span style="color:red">A variant for teams that commit their [[spec-driven-development|specs]]: commit every stage of the spec, then **expire it after ≈3 months** into a one-line ADR table in [[claude-md]] with three columns — *choice / why / what was rejected*. The reasoning: *"we'll never read these alone again, we ask the AI"* ([[2026-07-23-ai-software-product]]). The "rejected" column is what stops the agent from re-proposing an option the team already turned down. See [[openspec]] for the archive step that produces those specs.</span>

## Relation to [[progressive-disclosure]]

Decision Context is a dense distillation for always-loaded context. When the full rationale matters, keep the ADR linked from a [[progressive-disclosure]] lazy-load. The rule of thumb: if Opus already knows the shape of the solution, give it only the constraint that makes your case different.

## See also
- [[agents-md-discipline]]
- [[claude-md]]
- [[progressive-disclosure]]
- [[context-window]]
- [[dumb-zone]]
- [[self-improving-knowledge]]
- <span style="color:red">[[spec-driven-development]]</span>
- <span style="color:red">[[openspec]]</span>
- [[2026-06-26-ai-agentic-how]]
- <span style="color:red">[[2026-06-05-ai-consultancy]]</span>
- <span style="color:red">[[2026-07-23-ai-software-product]]</span>
