---
title: Tracer Bullets
category: pattern
tags: [planning, vertical-slice, spec-driven-development, legacy-migration, workflow]
sources: ["[[2026-06-02-ai-open-workshop]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Tracer bullets means instructing the agent to build one thin vertical slice end-to-end (screen → backend → DB → test), get it reviewed, and only then widen — instead of its default of building whole horizontal layers and discovering integration breakage at the end.

## The agent's default: big-bang by layers

Left alone, an agent builds horizontally: all entities, then all services, then all controllers, then the UI — four screens at once ([[2026-06-02-ai-open-workshop]]). It does this because it is **cheapest in tokens**: it wants to open each file only once ([[2026-09-02-ai-insurer]]). The cost lands at the end — a painful integration stage, and one session observed that the final e2e step "eats 50%" of the effort when the plan is layered ([[2026-09-28-ai-gaming-tech]]). Worse, an agent that has written 500 lines before its first failure anchors on its solution like a human — **sunk cost** — and patches instead of rethinking ([[2026-09-02-ai-insurer]]).

## The magic words

Saying **"tracer bullets"** — the term from *The Pragmatic Programmer* ([tip #20](https://pragprog.com/tips/), [c2 wiki](https://wiki.c2.com/?TracerBullets)) — is enough for the model to switch to one full vertical flow first ([[2026-06-02-ai-open-workshop]]); one session credited the agent-era framing to Matt Pocock ([[2026-09-28-ai-gaming-tech]]). Small vertical slices, scope grown progressively, early feedback — even if that means passing through the controller three times: sorting first, prove it works, then pagination ([[2026-06-08-ai-security-tools]], [[2026-09-02-ai-insurer]]).

## Making it stick

- **Linearise `tasks.md` by slices, not layers.** Otherwise the agent resists, because slices burn more tokens ([[2026-09-02-ai-insurer]]; see [[spec-driven-development]], [[openspec]]).
- **Pour the principle into the planning agent.** Planning skills should carry tracer bullets into the *single* planning agent; separate sub-agents come only later ([[2026-09-02-ai-insurer]]).
- **Review each slice before the next.** Example: migrating 200 JSP screens to Angular — screen 1 all the way to its e2e test, review, then screens 2 and 3 ([[2026-08-13-ai-insurer]]). Each slice is a small PR; see [[stacked-prs]].
- **Parallelise only after freezing the contract.** Fix the API contract first, then start sub-agents on backend and frontend; shared team facts are written once in [[claude-md]] ([[2026-08-13-ai-insurer]], [[sub-agents]]).
- **One wide test per slice** — an acceptance test that goes red then green ([[red-green-tdd]], [[acceptance-test-bdd]]).

## In legacy work and cleanup

For a legacy migration, constrain scope hard: a **minimum viable vertical slice** plus an explicit "what NOT to do" chapter ([[2026-08-10-ai-consultancy]], [[legacy-migration-stages]]). Migrate and slice vertically in one pass rather than porting a layer at a time ([[2026-09-25-ai-bank-migration]]). The same rule devibes a vibe-coded codebase: never "Opus, rewrite everything" — take the **thinnest vertical slice, review it, repeat**; otherwise it is three hours in the [[dumb-zone]] and an unreviewable diff ([[2026-07-22-ai-agentic-how]], [[vibe-fixer]]). Overnight runs follow the same recipe slice by slice ([[ralph-loop]]).

## See also
- [[spec-driven-development]]
- [[openspec]]
- [[stacked-prs]]
- [[red-green-tdd]]
- [[legacy-migration-stages]]
- [[vibe-fixer]]
- [[ralph-loop]]
- [[feedback-loop]]
- [[token-economy]]
