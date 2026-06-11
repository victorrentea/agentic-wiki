---
title: Spec-Driven Development
category: pattern
tags: [spec-driven, planning, agent-workflow, handover, acceptance-tests]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

Spec-driven development (SDD) is the practice of **aligning your mental model with what the AI will build** by anticipating every decision point in a written spec *now*, then letting the agent execute later.

## Why a spec, not a prompt

SDD is the longest and most complex of the ways to develop with an agent — a day to a sprint of work — and it earns that cost on big, ambiguous changes. The metaphor: writing the spec is like aiming a rifle — **one degree off and you shoot wild** across three days of development. The spec also keeps each execution batch inside the model's comfort zone (it is the durable [[handover]] that lets the agent "die and be reborn" with clean context, the same engine behind the [[ralph-loop]]). The input to a spec is a [[grill-me|grill]]: turn a vague four-line ticket into ≈10 answered questions first.

## The three artifacts

[[openspec]] (and similar scaffolds) produce three files, each owned by a different role:

- **`proposal.md`** — the functional "what". Belongs in **Jira**, not the repo — "update the issue with the proposal findings." Reviewed by **Product**.
- **`design.md`** — pure technical decisions (REST controller, sort mapper, repository signature, response envelope, page-size cap). Reviewed by **Developers in pairs**, *no business present*. A **Risks & trade-offs** section is gold — it surfaces hidden assumptions ("single known consumer") so a human can interject. **Burn it when done** — it drifts; the code is the truth.
- **`tasks.md`** — a checklist *for the AI* to track its own progress. "You get very little of my attention."

## Two disciplines

- **Tell the AI what NOT to do.** It over-delivers to burn tokens — so forbid scope creep explicitly (no fuzzy search, only two sort states, don't paginate other screens). Otherwise you find every screen paginated in the morning.
- **Don't type the fix — ask.** A correction may need to propagate to `tasks.md`, and *maybe you're the one who's wrong* — let the agent challenge you and update the spec itself.

Then constrain the agent from the other side with [[acceptance-test-bdd]] tests that must go green.

## See also
- [[openspec]]
- [[handover]]
- [[grill-me]]
- [[acceptance-test-bdd]]
- [[ralph-loop]]
- [[multi-model-review]]
- [[2026-06-11-ai-playtika]]
