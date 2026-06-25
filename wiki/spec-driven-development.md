---
title: Spec-Driven Development
category: pattern
tags: [spec-driven, planning, agent-workflow, handover, acceptance-tests, three-amigos]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-25
---


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
- **Never touch the left side (the files) — only prompt.** Two reasons: (1) you may not see all the files that need changing, so a hand-edit desyncs the rest; (2) you want the AI's intelligence to counter you when you're wrong. The agent is a thinking partner, not a transcription service — find at least one thing you disagree with, fix it by prompting, and let the agent decide whether your correction is right.

Then constrain the agent from the other side with [[acceptance-test-bdd]] tests that must go green.

OpenSpec's full artifact set is **Proposal** (reviewed with business/PO), **Design** (reviewed with developers in a pair/mob), **Tasks** (the AI's git-tracked execution checklist, reviewed minimally), and **Spec** (acceptance criteria, reviewed with QA and then converted to Gherkin `.feature` files for agent self-testing). The reviewer mapping is the most durable lesson: each artifact belongs to a different audience, and mixing them dilutes both reviews.

## Framework interchangeability

<span style="color:red">Spec-driven frameworks (GSD, [Kiro](https://kiro.dev), [OpenSpec](https://github.com/Fission-AI/OpenSpec), Specky) are interchangeable — every team is unhappy with their flow after 3–6 months; their universal core is the same gates: interview → align business/QA/dev → architecture → tasks → code → review. Four companies starting from different frameworks converge on substantially the same structure.

**Never edit the framework markdown by hand** — prompt the agent to fix the flow. Change it only when the same failure recurs. The framework lives in committed skills, so you own it.</span>

## Spec alignment is precision work

Aligning your mental model with the agent's through the spec is like aiming at a distant target — one millimetre off at the barrel and you land somewhere completely wrong. Every word in the spec earns its place. The practical corollary: the spec should include a [[non-goals]] chapter that explicitly names what the feature will *not* do, since agents over-deliver by default ("agents build rockets when you asked for a bicycle").

**Cloud workflow integration:** In the [[cloud-review-workflow]], the SDD is built by a cloud bot (not the developer). The bot researches the codebase first (read-only phase), then generates proposal/design/tasks from the [[three-amigos]] acceptance tests. The `tasks.md` is explicitly broken down for a cheaper model (Sonnet). The developer's job shifts to reviewing and correcting the SDD from PR comments — not writing it.

## See also
- [[openspec]]
- [[handover]]
- [[grill-me]]
- [[acceptance-test-bdd]]
- [[ralph-loop]]
- [[multi-model-review]]
- [[cloud-review-workflow]]
- [[three-amigos]]
- [[non-goals]]
- [[openapi-test-fixtures]]
- [[keyset-pagination]]
- [[pg-trgm]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-23-ai-garmin]]
