---
title: Spec-Driven Development
category: pattern
tags: [spec-driven, planning, agent-workflow, handover, acceptance-tests, three-amigos]
sources: ["[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-11
updated: 2026-10-13
---

Spec-driven development (SDD) is the practice of **aligning your mental model with what the AI will build** by anticipating every decision point in a written spec *now*, then letting the agent execute later.

## Why a spec, not a prompt

SDD is the longest and most complex of the ways to develop with an agent — a day to a sprint of work — and it earns that cost on big, ambiguous changes. The metaphor: writing the spec is like aiming a rifle — **one degree off and you shoot wild** across three days of development. The spec also keeps each execution batch inside the model's comfort zone (it is the durable [[handover]] that lets the agent "die and be reborn" with clean context, the same engine behind the [[ralph-loop]]). The input to a spec is a [[grill-me|grill]]: turn a vague four-line ticket into ≈10 answered questions first.

<span style="color:red">**Context lives in git, not in chat history.** Your colleagues have no access to your conversation; go on holiday or switch Copilot → Claude and the context dies, and compaction loses details anyway. Self-test: *"If I close this conversation now and start a new one, am I afraid? If yes, you're doing it wrong"* ([[2026-04-16-ai-safety-critical]]; cf. [Martin Fowler's Exploring Gen AI series](https://martinfowler.com/articles/exploring-gen-ai.html)). The grill conversation's tokens are the "golden" ones — decisions, not tool calls — so **freeze them into markdown** you can review at your own pace ([[2026-10-06-gray-factory-devoxx]]). Markdown isn't a chore but **climbing holds** that lift you high enough to see what the agent will do in the next 8 hours ([[2026-08-13-ai-insurer]]).</span>

<span style="color:red">**It's cheaper, not dearer.** SDD spends tokens up front but is reportedly cheaper overall than direct prompting plus corrections: errors are fixed in the markdown before they're smeared across 200 lines in 20 files — the defence against [[ai-spiraling]] and [[vibe-heresy]] ([[2026-05-04-ai-telecom]], [[2026-04-27-ai-healthcare-software]]). It also makes long autonomous runs possible: without a spec the agent works 5 minutes then asks again; with one, ≈25 minutes AFK ([[2026-08-27-ai-it-services]]). Rifle math: a 3-day feature = ≈15 min writing the spec + 10 min review + ≈40 min of agent work ([[2026-06-05-ai-consultancy]]). It takes ≈1 month to get used to reviewing instead of typing ([[2026-05-04-ai-telecom]]).</span>

<span style="color:red">When code is written in hours, a detailed specification becomes a good investment again — the **AI Unified Process** ([Simon Martinelli](https://martinelli.ch/a-small-app-a-complete-process-demoing-ai-unified-process-with-a-feedback-app/)) revives RUP-style artifacts with very short cycles. The objection that this denies Agile's feedback lessons is answered by iterating the *spec* with the user quickly, not the code ([[2026-05-11-ai-software-vendor]]). A team that estimated "add a vet to visits" at two weeks saw it delivered via OpenSpec in ≈30 min — so knowing exactly what you want becomes the core skill: *"they no longer program — they manifest desire"* ([[2026-05-13-ai-it-services]]).</span>

## When to write a spec — and when not

<span style="color:red">Dose the process by **size and unknown-ness** ([[2026-09-30-ai-enterprise]], [[2026-09-04-ai-software-company]], [[2026-10-09-ai-jax-london]]; extends [[plan-mode]]):</span>

| <span style="color:red">Task</span> | <span style="color:red">Approach</span> |
|---|---|
| <span style="color:red">hours; move a button, add a field, change a validation</span> | <span style="color:red">direct prompt — or Jira → PR without opening the IDE</span> |
| <span style="color:red">medium, familiar ground</span> | <span style="color:red">[[plan-mode]] or a [[superpowers]] brainstorm</span> |
| <span style="color:red">new territory (Kafka, an outbox, Debezium)</span> | <span style="color:red">an interview ([[grill-me]]) — or a second terminal that *teaches* you with a 10-minute prototype</span> |
| <span style="color:red">over ≈1–2 days of hand work, or the domain model / a workflow</span> | <span style="color:red">full SDD</span> |
| <span style="color:red">stinking legacy</span> | <span style="color:red">code-research sub-agents first, then decide</span> |
| <span style="color:red">mature team, spec + guardrails + feedback loops</span> | <span style="color:red">[[ralph-loop]] overnight (only after >6 months of adoption)</span> |

<span style="color:red">Thresholds drift with the model: ">1 day" ([[2026-06-02-ai-open-workshop]]), "over 1–2 days" ([[2026-08-03-ai-telecom-ops-qa]], [[2026-09-30-ai-enterprise]]), "≈two days" ([[2026-09-18-ai-agentic-how]]). In practice **≈80% of tasks skip** the full grill → spec → review flow ([[2026-09-14-ai-industrial]], [[2026-09-30-ai-enterprise]]), and full spec markdowns go on ≤20% of issues ([[2026-10-06-gray-factory-devoxx]]). Ask the agent to **estimate effort in human days** first — useless as a deadline ("3–5 days", delivered in 20 minutes), but a free T-shirt size; above ≈3–4 days, split or negotiate a leaner MVP ([[2026-06-05-ai-consultancy]], [[2026-09-04-ai-software-company]]). Or let the agent judge the size and pick the technique itself ([[2026-08-10-ai-consultancy]]).</span>

<span style="color:red">**Write markdown when** the interview passed ≈question 7 and you no longer remember what you decided; the model is weak and needs support; the problem exceeds your expertise; you'll maintain the app for years. **Don't** for a throwaway prototype, a small fix, or a problem you've solved before ([[2026-08-13-ai-insurer]]).</span>

<span style="color:red">**Model strength changes the dose.** SDD is mandatory for weaker models — one big prompt to a small or in-house model is chaos, and the stepwise flow keeps each step out of the [[dumb-zone]] ([[2026-05-25-ai-government]]). On top models with lots of context it can be overkill: the framework's CLI exists to force phases programmatically because a weak model cheats and skips steps from plain markdown — **SDD is the bazooka for weak harnesses and stinking legacy** ([[2026-08-27-ai-it-services]]). Even with Opus everywhere you need fewer specs, but it still drifts on assumptions ([[2026-06-05-ai-consultancy]]).</span>

## The three artifacts

[[openspec]] (and similar scaffolds) produce three files, each owned by a different role:

- **`proposal.md`** — the functional "what". <span style="color:red">It opens with **WHY**, reverse-engineered even from a one-line ticket, and then: what changes, **impact / capabilities modified** ("what can I break?" — business loves it), **what it does NOT do**, intentionally deferred items, open questions, and what needs a human before it ships ([[2026-07-03-ai-bank]], [[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]]). Written at high-school level for the PO: zero JSON, zero component names — *"if the business asks who Jason is, you failed"* ([[2026-07-20-ai-telecom]]). During development it lives in git next to the code (the agent reads it ≈3× cheaper than a Jira call); when the change closes, its functional content goes **back into the Jira ticket** ([[2026-09-16-ai-safety-critical]]).</span> Reviewed by **Product**.
- **`design.md`** — pure technical decisions (REST controller, sort mapper, repository signature, response envelope, page-size cap). Reviewed by **Developers in pairs**, *no business present*. A **Risks & trade-offs** section is gold — it surfaces hidden assumptions ("single known consumer") so a human can interject. <span style="color:red">Decisions are explained one by one, like an [ADR](https://adr.github.io/). It is "the hardest file you'll ever read" — "LGTM here is the trap of the year" ([[2026-09-07-ai-bank]], [[2026-09-02-ai-insurer]]). Earlier advice was to burn it when done; by autumn 2026 it is **burn the file, keep the decisions** as one-line ADRs — see [[spec-afterlife]].</span>
- **`tasks.md`** — a checklist *for the AI* to track its own progress. "You get very little of my attention." <span style="color:red">It is sized for a junior, so implement it on the cheapest tier that copes ([[2026-10-01-ai-bank]]); it lets a session **resume after quota exhaustion or a handoff** ([[2026-09-02-ai-insurer]]) and counters context anxiety near the dumb zone ([[2026-09-28-ai-gaming-tech]]). Plan **vertical slices, not layers** — otherwise the final end-to-end step eats half the effort (see [[tracer-bullets]]).</span>

<span style="color:red">There are several files **because different eyes read them**, not for tidiness ([[2026-08-27-ai-it-services]]). Reviewer mappings vary by session — most give `design.md` to engineers, one gives it to QA for critical thinking ([[2026-07-22-ai-agentic-how]]) — but the principle is constant. How to actually read them is on [[spec-review]].</span>

## Two disciplines

- **Tell the AI what NOT to do.** It over-delivers to burn tokens — so forbid scope creep explicitly (no fuzzy search, only two sort states, don't paginate other screens). Otherwise you find every screen paginated in the morning.
- **Don't type the fix — ask.** A correction may need to propagate to `tasks.md`, and *maybe you're the one who's wrong* — let the agent challenge you and update the spec itself.
- **Never touch the left side (the files) — only prompt.** Two reasons: (1) you may not see all the files that need changing, so a hand-edit desyncs the rest; (2) you want the AI's intelligence to counter you when you're wrong. The agent is a thinking partner, not a transcription service — find at least one thing you disagree with, fix it by prompting, and let the agent decide whether your correction is right. <span style="color:red">The same fact usually lives in requirements, design *and* tasks — changing a debounce from 400 to 300 ms through the chat updates every reference at once ([[2026-05-06-ai-ecommerce]]). And don't whip: "maybe I'm the dumb one, check it yourself", otherwise it executes blindly ([[2026-09-16-ai-safety-critical]]).</span>
- <span style="color:red">**State the business need, never the mechanism.** Saying "regex" for a partial match risks the model fetching the whole table and filtering in Java. *"Tamper down your technical self, pull out your business self"* — implementation choices belong in the design phase ([[2026-05-06-ai-ecommerce]]; see [[answer-injection]]).</span>

Then constrain the agent from the other side with [[acceptance-test-bdd]] tests that must go green.

OpenSpec's full artifact set is **Proposal** (reviewed with business/PO), **Design** (reviewed with developers in a pair/mob), **Tasks** (the AI's git-tracked execution checklist, reviewed minimally), and **Spec** (acceptance criteria, reviewed with QA and then converted to Gherkin `.feature` files for agent self-testing). The reviewer mapping is the most durable lesson: each artifact belongs to a different audience, and mixing them dilutes both reviews.

## The full chain

<span style="color:red">A chain that crystallised live ([[2026-05-06-ai-ecommerce]]): issue tracker (read via MCP) → human brainstorm → brainstorming skill on Opus → `handover.md` → spec (requirements / design / tasks) → a fresh review on another model → implementation → reviewer agent → push any drift back to the ticket. The pipeline seen across companies by September ([[2026-09-02-ai-insurer]]): grill with business, connected to code, knowledge base and meeting transcripts → QA adds criteria and nasty inputs → architect agents → planning skills that pour principles ([[tracer-bullets]]) into a *single* agent → the coder spawns adversarial reviewers (clean code, performance, UX), fixes, pushes, loops on CI/Sonar, merges `main` into the branch → human review with artifacts ([[human-review-dashboard]]). Send agents on a read-only research phase before designing — architecture is where "your brain dies" compressing 1–2 weeks of design into an hour, so summon several models: "you are never the smartest technical person in the room — even if you're alone" ([[2026-07-01-ai-java-community]]).</span>

<span style="color:red">Every feature also needs a **metric** — money or traction — to measure its benefit ([[2026-07-23-ai-software-product]]). For large efforts, [Wayfinder](https://www.aihero.dev/skills-wayfinder) splits the work into **development vs. experiment chunks**, so the unknowns get prototyped first ([[2026-09-28-ai-gaming-tech]]). Gotcha: "remember this in AGENTS.md" said during plan mode was lost — **plan mode doesn't write files** ([[2026-09-16-ai-safety-critical]]).</span>

## Bugs found after implementation

<span style="color:red">Three options ([[2026-04-07-ai-product-team]]): (A) just prompt the fix for a minor tweak; (B) adjust the spec while it's warm, in the same conversation; (C) a new spec for a serious, architectural bug. "AI did it wrong" often means **nobody read the markdown carefully** — a two-column sort nobody asked for was in the spec ([[2026-10-09-ai-jax-london]]); seeded trap data (an owner with two last names) broke sorting, and the bug came from the spec: our fault, not the agent's ([[2026-10-01-ai-bank]]). Pointing `/propose` at the wrong GitHub issue built the wrong feature end-to-end ([[2026-07-01-ai-java-community]]).</span>

## Framework interchangeability

Spec-driven frameworks (GSD, [Kiro](https://kiro.dev), [OpenSpec](https://github.com/Fission-AI/OpenSpec), Specky) are interchangeable — every team is unhappy with their flow after 3–6 months; their universal core is the same gates: interview → align business/QA/dev → architecture → tasks → code → review. Four companies starting from different frameworks converge on substantially the same structure.

<span style="color:red">The menu as of autumn 2026 ([[2026-09-02-ai-insurer]], [[2026-09-24-ai-bank]], [[2026-09-28-ai-gaming-tech]]):</span>

- <span style="color:red">[[openspec]] — community-driven, a safety net for a weaker harness, [legacy-friendly](https://github.com/Fission-AI/OpenSpec/blob/main/docs/existing-projects.md); installs for Copilot too.</span>
- <span style="color:red">[[superpowers]] — "#1 for vibe coding": brainstorm with a browser mock-up, sub-agents at every step, adversarial review per stage, TDD; minus: an unreadable `plan.md` that eats context.</span>
- <span style="color:red">[Matt Pocock's skills](https://github.com/mattpocock/skills) (grill-me, [`to-spec`](https://github.com/mattpocock/skills/tree/main/skills/engineering/to-spec)) — fewest markdowns, most pragmatic.</span>
- <span style="color:red">GitHub's [Spec Kit](https://github.com/github/spec-kit) (specify → plan → tasks → implement), [[kiro]], [Get Shit Done](https://github.com/gsd-build/get-shit-done).</span>
- <span style="color:red">[BMAD](https://github.com/bmad-code-org/BMAD-METHOD) — huge markdown volume, fits formal companies (≈40 markdowns for one pagination problem — excessive) — but its [advanced elicitation](https://docs.bmad-method.org/explanation/advanced-elicitation/) list is worth mining for review personas, e.g. the pre-mortem *"assume the project failed, work backwards"* ([[2026-08-10-ai-consultancy]]).</span>

<span style="color:red">They all reimplement the same brainstorm (OpenSpec's *explore* ≈ grill-me), and two companies starting from OpenSpec and Spec Kit ended ≈80% identical ([[2026-07-23-ai-software-product]]). "After 3 months nobody remembers which framework they started from" — start anywhere, then tailor.</span>

**Never edit the framework markdown by hand** — prompt the agent to fix the flow. Change it only when the same failure recurs. The framework lives in committed skills, so you own it. <span style="color:red">When you fix a generated file, tell the agent to also edit the framework's own skill so the fix sticks — e.g. "rewrite the proposal for a PO who doesn't know JSON", "every UI change drafts an HTML mockup plus a screenshot", "require the standard design-system component in `design.md`" ([[2026-09-04-ai-software-company]], [[2026-09-24-ai-bank]], [[2026-10-01-ai-bank]]). *"If in 3–6 months you haven't changed a single markdown of the workflow you downloaded, you don't deserve a raise"* ([[2026-08-27-ai-it-services]], [[2026-09-16-ai-safety-critical]], [[2026-09-28-ai-gaming-tech]]). OpenSpec setup tip: delete its generated slash commands / `.github/prompts`, keep the skills — a skill is already `/`-invocable ([[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]]). See [[skills-governance]].</span>

## Rhythm: spec by day, apply by night

<span style="color:red">A spec plus its review (½ h business, 1 h colleagues, the tester) is about one working day, so teams **spec by day and `openspec apply` by night**, and wake up to several PRs; apply takes 30–45 min or more — "don't go on a break without an agent working behind you" ([[2026-09-02-ai-insurer]], [[2026-10-01-ai-bank]]). **Opus for thinking, Sonnet for execution**: brainstorm and markdown on Opus, apply on a cheaper model ([[2026-08-03-ai-telecom-ops-qa]]). Run `propose` in the **same conversation** that did the grilling (≈130k golden tokens of files read and decisions taken), or summarise the brainstorm into a committed `q&a.md` and pass it in so it doesn't re-ask decided questions ([[2026-09-04-ai-software-company]], [[2026-09-30-ai-enterprise]]). Rough cost gate from one session: propose ≈ a few dollars, apply ≥ $20 ([[2026-09-04-ai-software-company]]).</span>

<span style="color:red">`apply` with *"use a separate sub-agent per chapter and parallelize"* spawns waves (backend / frontend / docs) — faster but pricier, since each pays its own initial context ([[2026-06-02-ai-open-workshop]]); a huge `tasks.md` is a job for a [[ralph-loop]] or sub-agent waves ([[2026-09-25-ai-bank-migration]]). Risk: three or four fronts at once burn your review attention ([[2026-09-02-ai-insurer]]). After the push, the agent waits for the build and fixes it ([[ci-green-loop]]).</span>

## Legacy is where it breaks

<span style="color:red">On a small change in an untouched legacy app, brainstorming answers "let's rewrite everything" — AI always wants to do more work. Send Explore sub-agents to **imitate how it's already done there**, say explicitly **"minimal, focal change"**, and warn rather than command ("people died in there") so it chooses the terrain study itself ([[2026-09-04-ai-software-company]], [[2026-10-09-ai-jax-london]], [[2026-08-10-ai-consultancy]]). Plan mode and interviews "dream too much" in legacy. The real prerequisite is **test confidence** — spend leftover quota on acceptance tests first. Bugs go to a [systematic-debugging skill](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md), not a spec ([[2026-07-23-ai-software-product]]).</span>

## Beyond markdown: executable specs

<span style="color:red">When the LLM keeps failing on a complex protocol, make the spec executable ([[2026-08-31-ai-software-vendor]]): an executable [State pattern](https://refactoring.guru/design-patterns/state) from which you extract the state diagram and validate it visually; heavier, [Spring State Machine](https://github.com/spring-projects/spring-statemachine) or a BPM engine like [Camunda](https://camunda.com); hardest, a formal core (e.g. [Prolog](https://www.swi-prolog.org)) **run as-is**, with the LLM writing the core and the scaffold. A lighter variant: a PlantUML **activity diagram** as the business-flow source that the agent edits at the business's request ([example](https://github.com/victorrentea/clean-architecture/blob/main/adoc/activity.puml); [[2026-09-30-ai-enterprise]]; see [[deterministic-diagrams]]). For UI, give the agent HTML or at least a picture — better, a measurable objective and a loop (minimise the pixel diff) ([[2026-10-01-ai-bank]]). Non-functional requirements are proven with load tests, e.g. [Gatling](https://gatling.io/) ([[2026-08-31-ai-software-vendor]]).</span>

<span style="color:red">**Cross-repo changes** (one feature = 3 microservices = 3 PRs): a per-team spec repo linked bidirectionally from each service's CLAUDE.md, the "most impacted" service, the monolith if there is one, or a [monorepo](https://monorepo.tools) — which is making a comeback ([[2026-08-31-ai-software-vendor]], [[2026-10-01-ai-bank]]; see [[cross-repo-knowledge]]).</span>

## Spec alignment is precision work

Aligning your mental model with the agent's through the spec is like aiming at a distant target — one millimetre off at the barrel and you land somewhere completely wrong. Every word in the spec earns its place. The practical corollary: the spec should include a [[non-goals]] chapter that explicitly names what the feature will *not* do, since agents over-deliver by default ("agents build rockets when you asked for a bicycle"). <span style="color:red">*"If you didn't read the markdown before implementation, you did vibe coding"* ([[2026-08-27-ai-it-services]]) — the reading discipline is on [[spec-review]].</span>

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
- <span style="color:red">[[spec-review]]</span>
- <span style="color:red">[[spec-afterlife]]</span>
- <span style="color:red">[[plan-mode]]</span>
- <span style="color:red">[[superpowers]]</span>
- <span style="color:red">[[kiro]]</span>
- <span style="color:red">[[tracer-bullets]]</span>
- <span style="color:red">[[vibe-heresy]]</span>
- <span style="color:red">[[gray-factory]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
