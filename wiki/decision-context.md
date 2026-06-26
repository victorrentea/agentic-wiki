---
title: Decision Context
category: pattern
tags: [agents-md, claude-md, knowledge-engineering, adr, decisions]
sources: ["[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-26
updated: 2026-06-26
new_in: 2026-06-26
---

<span style="color:red">🆕 New in AI@agentic.how (2026-06-26)</span>

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

## Relation to [[progressive-disclosure]]

Decision Context is a dense distillation for always-loaded context. When the full rationale matters, keep the ADR linked from a [[progressive-disclosure]] lazy-load. The rule of thumb: if Opus already knows the shape of the solution, give it only the constraint that makes your case different.

## See also
- [[agents-md-discipline]]
- [[claude-md]]
- [[progressive-disclosure]]
- [[context-window]]
- [[dumb-zone]]
- [[self-improving-knowledge]]
- [[2026-06-26-ai-agentic-how]]
