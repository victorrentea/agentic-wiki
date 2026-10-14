---
title: Cognitive Debt (Vibe-Coding Collapse)
category: concept
tags: [vibe-coding, anti-pattern, review, tests, human-factors, technical-debt]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-06-23
updated: 2026-10-13
---

Cognitive debt is the accumulated *not-knowing* that builds up when code is generated faster than any human reads it — within a few months nobody on the team understands what is in the codebase, and every bug fix spawns new bugs.

## How the collapse happens

The cautionary arc: an Opus orchestrator plans, parallel Sonnet [[sub-agents]] execute, and almost nothing is reviewed. Velocity feels euphoric for **2–3 months**, then it collapses — nobody knows what the code does, fixing one bug breaks two others, and forward progress grinds to a halt. This is [[vibe-coding]] without review at scale; the debt is not in the code (technical debt) but in the team's *understanding* of it.

<span style="color:red">Measured in features rather than months, a vibe-grown solution tends to collapse around the **4th–5th feature increment**. The antidote is to stay owner of the *solution* rather than of every line of code — know where to look when it misbehaves ([[observation-points]], [[2026-09-07-ai-bank]]).</span>

<span style="color:red">It can happen to one person in under two weeks: a developer shipped a feature ≈90% with AI, left for 1.5 weeks to help another team, and came back not knowing what the feature does — his two teammates had pushed so much AI code that he didn't recognise the repo ([[2026-05-27-ai-open-workshop]]).</span>

## The fake safety net

The trap that makes it worse: the team instructs the AI to use TDD and proudly hits 85%+ coverage — but **never reviews the tests**, and the AI rewrites the tests alongside the code each iteration. The "safety net" asserts only what the implementation already does ([[sycophancy]] applied to tests). High coverage with unreviewed tests is *negative* information: it manufactures false confidence.

<span style="color:red">The extreme case seen in one codebase: a 1,200-line God Service at **87% coverage, with 200 `when/then/return` stubs and zero asserts**. Coverage measures executed lines, not verified behaviour; set as a target, it produces exactly the tests you don't want. A test can even be green without reaching the line it targets (a different line throws the expected exception). Read the coverage report to **hunt what isn't covered**, not to enjoy the green — and remember branch coverage of `a && b && c` needs ≥4 tests. [[mutation-testing]] and [[semantic-coverage]] measure what coverage can't ([[2026-09-10-testing-ai-bank]]).</span>

## The real safety layer

The pragmatic antidote is **not more unit tests** — it is **business-reviewed `.feature` files** (Gherkin given/when/then the business can actually read). See [[acceptance-test-bdd]]. A human reviews the *test contracts* — the signatures and the asserts — not necessarily every value. A useful `AGENTS.md` rule: *"after writing tests, summarise in plain English what each test asserts and wait for human approval before implementing."* See [[agents-md-discipline]].

Cognitive debt is the human cost of [[vibe-coding]]; the standing countermeasure is [[skill-erosion]] discipline (mob-review, hands-only quarter) plus [[multi-model-review]] and honest [[code-review]].

## Brain burn and the "yeah okay Claude" moment

**The "yeah okay Claude" moment.** The debt accrues one rubber-stamp at a time. Parallel agent terminals compound the problem: running two deep-thinking sessions simultaneously — one on concurrency, one on caching — means your brain is in neither. The result is [[dumb-zone]]-level review quality from a human who is technically awake. Remedy: never approve in that state; walk, sleep, or take a break first. See also [[skill-erosion]] for countermeasures.

<span style="color:red">The root cause is compression: what took a week of thinking in 2022 now fits a two-hour brainstorm or "5 days compressed into 6 hours" of markdown, and an overloaded brain approves slop. While the agent works, don't open a second deep terminal (one cautionary story involved 11 terminals) — see Steve Yegge's [The AI Vampire](https://steve-yegge.medium.com/the-ai-vampire-eda6e4f07163) and [[ai-addiction]]. "Noticing your brain died decides who keeps the job." ([[2026-09-07-ai-bank]], [[2026-09-28-ai-gaming-tech]])</span>

<span style="color:red">Multiplexing 4 parallel terminals is legitimate only as a *drill* ([[ai-calisthenics]]); deep work stays single-threaded ([[plan-mode]]).</span>

## Countermeasures that work

- <span style="color:red">**Use the [Hawthorne effect](https://en.wikipedia.org/wiki/Hawthorne_effect) against [[approval-fatigue]]:** pair or mob *with the agent* — reviewers who are watched approve less slop ([[2026-09-28-ai-gaming-tech]]).</span>
- <span style="color:red">**Personal prototype lab:** write a ≤100-line prototype for every unknown technique and collect them in your own Baeldung-style repo (cf. [eugenp/tutorials](https://github.com/eugenp/tutorials)).</span>
- <span style="color:red">Some teams take **one AI-free week per quarter** ([[skill-erosion]]).</span>

## See also
- [[vibe-coding]]
- [[acceptance-test-bdd]]
- [[sycophancy]]
- [[skill-erosion]]
- [[code-review]]
- [[agents-md-discipline]]
- [[dumb-zone]]
- [[grilling-in-reverse]]
- <span style="color:red">[[ai-addiction]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[observation-points]]</span>
- <span style="color:red">[[mutation-testing]]</span>
- <span style="color:red">[[ai-calisthenics]]</span>
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
