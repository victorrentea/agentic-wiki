---
title: Vibe Fixer
category: concept
tags: [vibe-coding, engineering, judgement, leverage, role-shift]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-12
updated: 2026-10-13
---

A "vibe fixer" is an engineer who receives ≈10,000 lines of almost-working, vibe-coded output and turns it into production-grade code — not by rewriting it, but by applying engineering judgement to what survives.

## The role shift

In the [[cloud-review-workflow]], the business or a non-technical stakeholder "vibes" a rough UI draft using an LLM (or via [[vibe-coding]] tools), producing something that looks right but won't scale, duplicates existing components, or ignores performance/security constraints. The vibe fixer takes that output and:

The full production-readiness checklist the vibe fixer applies has a name: [[productionalize]]. Business or designers vibe-code a working prototype; the vibe fixer *"makes it real"* — stable, safe, handling the deadlocks, memory issues, and race conditions that exceed the skills of a non-engineer.

- Decides what to **keep** (already good enough).
- Decides what to **reuse** (existing components/services that should replace the vibe-generated version).
- Decides what to **throw away** (irredeemable design choices).
- Wires the remainder into the production codebase.

The leverage point is **judgement**, not typing. Some teams already do this ≈20% of the time — it is becoming a recognized job shape rather than an edge case. <span style="color:red">By mid-2026 "vibe fixer" was already a real job title ([[2026-07-22-ai-agentic-how]]); Victor calls it his own job description five years out — salvaging 10k lines nobody wrote or read (300 ifs, 50 dependencies) with "40 adversarial agents over unread code" ([[2026-07-23-ai-software-product]], [[2026-09-16-ai-safety-critical]], [[2026-10-06-gray-factory-devoxx]]).</span>

<span style="color:red">A related shape is the **forward-deployed engineer**: an engineer goes to the client with a laptop and vibe-codes whatever they need on top of internal services — "personal software for hundreds of thousands of clients", engineers as house-call professionals like plumbers ([[2026-05-27-ai-open-workshop]]).</span>

## <span style="color:red">Devibing moves</span>

<span style="color:red">**Clean up, don't regenerate.** Under ≈100k LOC a mess isn't worth an AI rewrite. Never "Opus, rewrite everything" — take the **thinnest vertical slice**, review it, repeat ([[tracer-bullets]]); otherwise it's three hours in the [[dumb-zone]] and an unreviewable diff. Typical moves: introduce DTOs (the vibed Python API passed raw dicts everywhere), break complex modules down, decouple — then **red-team the cleanup with AI** ("can you still return a dict?"). Hexagonal architecture pays only once integration junk creeps into business logic ([[2026-07-22-ai-agentic-how]]).</span>

<span style="color:red">**Agree the fix cost *before* the AI comes up.** Business says "it took me a week and it works, so you need two days" — but you are the one who signs off and gets woken at 3 am ([[2026-07-23-ai-software-product]]). "90% visible is not 90% done": an hour of vibing can be ≈5,000 bad lines that take developers ≈5× as long to rewrite ([[2026-09-14-ai-industrial]]).</span>

## <span style="color:red">The 10,000-line business PR</span>

<span style="color:red">Business that spent hours with your agent will eventually want it **directly**, without you, and will bring a 10,000-line PR: "put it in prod". With an empty Claude (no skills, no flow) that is garbage; armed with your dev pipeline it is salvageable. "I only accept tickets, not PRs from business" holds for 3–4 months at most ([[2026-09-07-ai-bank]]). The options the sessions list:</span>

- <span style="color:red">**Revoke their git rights** — seen as a policy (PO without write access; PO PRs accepted only for UI-only changes, never flow or database) ([[2026-09-14-ai-industrial]]). Later sessions agree it **doesn't hold**: whoever pays decides, shadow IT grows in a siloed enterprise, and "whoever locked knowledge away always lost" ([[2026-09-16-ai-safety-critical]], [[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]]).</span>
- <span style="color:red">**Treat it as a prototype** — *accept the idea, not the PR*; read it as a spec, then throw it away. It belongs on screen at the grilling, not in `main` ([[2026-09-28-ai-gaming-tech]], [[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]]).</span>
- <span style="color:red">**Merge it** when it is an idiomatic screen on an existing grid — tossing €500 of tokens is waste ([[2026-09-28-ai-gaming-tech]]).</span>
- <span style="color:red">**Vibe-fix it** — patch or redo; that call stays yours ([[2026-09-07-ai-bank]], [[2026-10-06-gray-factory-devoxx]]).</span>

<span style="color:red">The autumn-2026 consensus: **give business access *with* the pipeline and gate the merge.** Put your skills and sub-agents in git for everyone, business included, plus hard rules (e.g. no file over 500 lines); then most business PRs arrive good enough to accept ([[2026-09-16-ai-safety-critical]], [[2026-10-09-ai-jax-london]]). The questions you would fire at their PR belong **in a skill** they run first ([[2026-09-28-ai-gaming-tech]]). Target: a pipeline that breaks such a PR into small pieces on its way to prod ([[stacked-prs]], [[2026-10-01-ai-bank]]). Best defence: bring a new idea to every demo. Refusing outright risks the **Luddite** label ([Overton window of disruption](https://ghuntley.com/overton)).</span>

## <span style="color:red">Business shifts left</span>

<span style="color:red">Adoption runs in stages: a vague dream you do the lifting on → grilled, beautiful tickets → business builds the software and hands it over for cleanup ([[2026-07-23-ai-software-product]]). **Level 1:** business does the [[grill-me|grilling]] with the agent itself in functional language ("I don't know Jason"), so tickets arrive debated from every side — after ≈10 sessions they grill alone. **Level 2:** business runs the app locally and vibe-codes from a scribbled napkin ([[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]]). One team forced agentification upstream with a Jira hook rejecting tickets *not* written by AI ([[2026-10-09-ai-jax-london]]).</span>

<span style="color:red">**Prototype first, polish live.** When business can't agree on a UI, send a vibe-coded prototype within 30 minutes and critique it together — "no more misunderstandings"; PO prototypes cut debates at brainstorming by ≈90% ([[2026-09-28-ai-gaming-tech]], [[2026-09-14-ai-industrial]]). Polish UI live with the business, dictating what bothers them; prototype without mercy ("what if he had 7 pets?" → have it add them). Dictate with a screenshot and **circle** the spot instead of describing positions — sort arrows always visible, debounced search instead of a button ([[2026-09-30-ai-enterprise]], [[2026-10-09-ai-jax-london]]; [[review-by-images]]). AI can even write **user manuals from screenshots** — one settled a spec dispute live ([[2026-09-28-ai-gaming-tech]]).</span>

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
- <span style="color:red">[[tracer-bullets]]</span>
- <span style="color:red">[[grill-me]]</span>
- <span style="color:red">[[stacked-prs]]</span>
- <span style="color:red">[[gray-factory]]</span>
- <span style="color:red">[[review-by-images]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
