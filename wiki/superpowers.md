---
title: Superpowers
category: tool
tags: [skill, plugin, brainstorming, tdd, debugging, sub-agents, code-review, spec-driven]
sources: ["[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

[Superpowers](https://github.com/obra/superpowers) (by Jesse Vincent, "obra") is the most widely used community skill pack for coding agents — brainstorming, planning, sub-agent-driven execution, TDD, systematic debugging and code review, bundled as skills that auto-activate on non-trivial tasks.

## Install

- **Claude Code**: `/plugin marketplace add obra/superpowers-marketplace`, then `/plugin install superpowers@superpowers-marketplace`.
- **Copilot CLI**: `copilot plugin marketplace add obra/superpowers-marketplace` + `copilot plugin install superpowers@superpowers-marketplace` ([[plugin-marketplace]]).

The repo is `obra/superpowers` — one session linked `anthropics/superpowers`, which is wrong. One session cited ≈189k GitHub stars; another reminded the room that **GitHub stars are for sale** ([[2026-09-28-ai-gaming-tech]]).

## How it works

Superpowers is a **meta-skill**: *"a skill that tells the library of skills which one the task really needs"* ([[2026-07-03-ai-bank]]). Its native cadence for a non-trivial change is brainstorm → 2× plan review → design → 2× design review → implement → 2× code review — ≈8 fresh-context agent invocations, each with no investment in the previous step's choices ([[multi-model-review]]). End to end it automates the writer/reviewer loop "no hands" — smooth in Claude Code, with friction in Copilot CLI. One run was observed spawning Haiku implementers and then an Opus reviewer ([[sub-agents]], [[model-hierarchy]]).

## The skills people actually use

- **`brainstorming`** — intercepts a large or ambiguous prompt and interviews you **one targeted question at a time** before a line of code, until it says it has full context; for UI it can produce a quick HTML mock-up in the browser, and it shows the resulting design back in readable chunks. Live example: *"I'd like to rewrite from Angular to React because it's cooler"* → *"What do you actually want? DX? speed? easier hiring?"* A vague "pagination for the owners table" surfaced 2- vs 3-state sort and the initial sort state. It belongs to the same family as [[grill-me]] and OpenSpec's explore — the one-question-at-a-time interview — and is the recommended weapon for new territory (Kafka, an outbox) ([[2026-09-04-ai-software-company]], [[2026-09-30-ai-enterprise]]).
- **`systematic-debugging`** ([SKILL.md](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md)) — the recommended first install, *"a masterclass in critical thinking"*. Iron law: **no fixes without root cause**; *if you tried 3 fixes and none worked, STOP, you're on the wrong road*; find a working example, diff, hypothesise, minimal test, verify. It found years-old bugs in repos. See [[red-green-tdd]].
- **`writing-plans` / `executing-plans`** — spec and plan with checkpoints, a `plan.md` of `[ ]` tasks.
- **`subagent-driven-development`** — parallel orchestration with review between steps.
- **`test-driven-development`** — red→green enforced.
- **`verification-before-completion`** and **`requesting-code-review`** — ≈3 parallel reviewers (security, design, tests), aggregated.
- **`writing-skills`** ([SKILL.md](https://github.com/obra/superpowers/blob/main/skills/writing-skills/SKILL.md)) — guidance for authoring skills: keep a frequently loaded skill *under ≈200 words*, test it by running a sub-agent with and without it, and don't over-specify steps — describe goal, environment and pitfalls ([[agent-skill]], [[goals-over-instructions]]).

**Brainstorming vs plan mode:** brainstorming clarifies *requirements* by asking you questions (Opus recommended); [[plan-mode]] plans the *implementation* into a markdown it then follows.

## When to use it

Positioning has been stable since spring: Superpowers is interactive and suits **medium tasks under ≈1 day**; for new screens, features or workflow steps — 8+ classes, 1+ day, or work that should run overnight — go to [[openspec]] / [[spec-driven-development]]. Ladder: **SDD > Superpowers > direct prompting**, picked by task size ([[2026-05-11-ai-software-vendor]]). A recommended hand-off: broad prompt → `/superpowers:brainstorm` (3–5 questions) → *"extract the Q&A into `brainstorming.md`"* → `/clear` → feed it to OpenSpec (`/opsx:new`) as a [[handover]].

By autumn it was named *"number one for vibe coding"*: brainstorm with a browser mock-up, sub-agents at every step, adversarial review of spec, plan and code, TDD ([[2026-09-02-ai-insurer]]). For a career switcher's passion project in an unfamiliar language: Opus + Superpowers, *"until the project falls on you"* ([[2026-10-01-ai-bank]], [[agentic-reconversion]]).

## Costs and caveats

- **It reviews everything, and that's slow and expensive.** It "loses a lot, a lot of time" ([[2026-08-27-ai-it-services]]); the implicit review at every stage made one participant joke *"you pay 800 lei, you burn 6,000"* of subscription value ([[2026-10-01-ai-bank]], [[token-economy]]).
- **`plan.md` is hard to read and eats context** ([[2026-09-02-ai-insurer]]).
- **You learn nothing if you let it run.** A dev who let it auto-activate, brainstorm, TDD and self-review end-to-end produced good code but "learned nothing" and never reviewed it — a [[skill-erosion]] risk. Drill: implement the same feature 3× from a clean git — raw vibe, Superpowers, [[grill-me]] + [[openspec]] — and compare ([[2026-07-01-ai-java-community]]).
- **A framework is someone else's opinion.** Start from any of them — OpenSpec, [Spec Kit](https://github.com/github/spec-kit), [get-shit-done](https://github.com/gsd-build/get-shit-done), Superpowers, [BMAD](https://github.com/bmadcode/BMAD-METHOD), Matt Pocock's skills — but never stay on its defaults: *unchanged markdowns after six months mean you brought no opinion — no salary raise* ([[2026-09-18-ai-agentic-how]], [[2026-10-09-ai-jax-london]]).
- **Read before installing.** A skill pack is a pre-prompt plus scripts; a bad skill makes the agent do stupid things *consistently* — see [[skill-marketplace-security]].

## See also
- [[grill-me]]
- [[openspec]]
- [[spec-driven-development]]
- [[plan-mode]]
- [[agent-skill]]
- [[red-green-tdd]]
- [[sub-agents]]
- [[multi-model-review]]
- [[skill-erosion]]
- [[plugin-marketplace]]
- [[skill-marketplace-security]]
