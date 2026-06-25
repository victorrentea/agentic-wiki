---
title: Vibe Fixer
category: concept
tags: [vibe-coding, engineering, judgement, leverage, role-shift]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-12
updated: 2026-06-24
---

A "vibe fixer" is an engineer who receives ≈10,000 lines of almost-working, vibe-coded output and turns it into production-grade code — not by rewriting it, but by applying engineering judgement to what survives.

## The role shift

In the [[cloud-review-workflow]], the business or a non-technical stakeholder "vibes" a rough UI draft using an LLM (or via [[vibe-coding]] tools), producing something that looks right but won't scale, duplicates existing components, or ignores performance/security constraints. The vibe fixer takes that output and:

The full production-readiness checklist the vibe fixer applies has a name: [[productionalize]]. Business or designers vibe-code a working prototype; the vibe fixer *"makes it real"* — stable, safe, handling the deadlocks, memory issues, and race conditions that exceed the skills of a non-engineer.

- Decides what to **keep** (already good enough).
- Decides what to **reuse** (existing components/services that should replace the vibe-generated version).
- Decides what to **throw away** (irredeemable design choices).
- Wires the remainder into the production codebase.

The leverage point is **judgement**, not typing. Some teams already do this ≈20% of the time — it is becoming a recognized job shape rather than an edge case.

## The economic shift

When [[vibe-coding]] generates the first draft, the economic bottleneck moves from *writing* to *judgement*. The engineer's value is in knowing what won't scale, what violates a data contract, what the security posture requires — not in producing lines of code. This is the same shift as the "10× Learner, not 10× Engineer" framing: the agent does the "what", the engineer guides the "why".

## Relation to de-shitification

The vibe fixer role is closely related to what Victor calls "de-shitification" — the inevitable engineering cleanup after a pure [[vibe-coding]] sprint. The difference is that a vibe fixer receives the output as a *starting point*, not a disaster recovery job. The longer a codebase runs without engineering input, the harder the cleanup becomes (tested empirically at ≈3 months before collapse).

## See also

- [[vibe-coding]]
- [[cloud-review-workflow]]
- [[agentic-engineering]]
- [[three-amigos]]
- [[spec-driven-development]]
- [[productionalize]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
