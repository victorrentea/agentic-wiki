---
title: Light vs Dark Code
category: concept
tags: [agents-md, reference-architecture, code-quality, exemplar]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-18-ai-agentic-how]]"]
created: 2026-06-24
updated: 2026-10-13
---

"Light code" is the reference-architecture exemplar module that sets the quality bar; "dark code" is the rest of the legacy codebase the agent must not imitate.

## The problem

An agent learning from a legacy codebase learns its patterns — including the bad ones. If 80% of the codebase uses anti-patterns (RxJava callbacks, God classes, ignored exceptions), the agent will replicate them even when AGENTS.md says not to, because the surrounding code is thousands of examples screaming the opposite.

<span style="color:red">**Garbage in, garbage out — including vulnerabilities.** Reported independently by several teams: an agent working in a 1990s-style EJB codebase generated **SQL-injection-vulnerable code**, because that is how the surrounding queries were written. Clean the context around the area you are about to touch before unleashing the agent, and add an explicit rule: *"don't follow legacy patterns without my approval."*</span>

<span style="color:red">**The agent imitates what it sees: one well-structured example beats any long prompt of rules.** This is why formatting rules in CLAUDE.md are near-useless — **reformat the repo once and push**, and every later edit inherits the style for free (see [[example-driven-prompting]]).</span>

## Pointing the agent at the light

The solution is to designate one module as the **reference-architecture exemplar** — *"if you want to see state-of-the-art quality like we want you to write, look at **that** module — not the other 80%."* This can be done:
- In `AGENTS.md`: *"Follow patterns from `/app/core/trail/` — it is the reference implementation."*
- By naming the exemplar module explicitly in the [[spec-driven-development|spec's]] design document.
- As a build-flavor pointer: since build flavors share folders you can't have separate AGENTS.md per flavor, but you can reference the best variant by name.

## <span style="color:red">Finish half-done migrations first</span>

<span style="color:red">Two coexisting styles paralyse the agent: half the code on the old framework, half on the new, and every task becomes a coin toss about which to copy. Legacy-first agentic work therefore starts with the boring prerequisites — fix flaky tests, speed up the build, **complete the migration so there is one style to imitate** — before any feature work (see [[legacy-migration-stages]]).</span>

## <span style="color:red">Greenfield: no light module yet</span>

<span style="color:red">With no code to imitate, the agent may go "brutally spiraling in completely wrong directions" ([[ai-spiraling]]). Two remedies:</span>
- <span style="color:red">**Hand-write one end-to-end spike flow** — controller → service → repository → entity → frontend component — or pair-prompt it with line-by-line review. That vertical slice becomes the exemplar for everything after it (cf. [[tracer-bullets]]).</span>
- <span style="color:red">**Point it at a git repo whose code you're proud of.** Without one it reinvents wheels; the same repo is also the target shape for a legacy migration plan. In a domain you have never shipped (e.g. mobile), borrow a specialist for the kickoff so the first exemplar is right.</span>

## <span style="color:red">Light modules beyond the backend</span>

- <span style="color:red">**Front end.** The morning after an overnight run is UX polish by eye — the agent misses contrast, alignment, inconsistent headers. When you repeat the same UI corrections, formalise them into a **design-system skill** (internal components, where buttons go, zero inline style) or hand the agent one existing screen as the reference.</span>
- <span style="color:red">**Tests.** One well-structured example test beats a page of testing rules: show the structure to copy — Page Objects over the design system, a business-readable DSL, underscore test names. When automating a manual test, give the agent **the tester's most recent feature** as the model: it carries every lesson learned so far. For Selenium, generate new page objects with an existing good one as the quality example ([[selenium-to-playwright-migration]]).</span>
- <span style="color:red">**Harvesting the light.** Over a small demo project, asking "write a handover doc" makes the agent deduce and spell out the good practices it sees in the code — a cheap way to turn an exemplar into written rules ([[handover]]).</span>

## <span style="color:red">Naming as leverage</span>

<span style="color:red">Names are part of the exemplar: `RegisterProduct` attracts exactly that behaviour, while `ProductService` attracts bloat. The same goes for over-engineered structure the agent will dutifully replicate — the classic hexagonal mistake is a Java interface in front of every use case, whereas in Cockburn's original "interface" meant the *contract* (the public methods); only driven ports need a separate interface.</span>

## Relation to anti-pattern guards

Naming the light module is complementary to listing explicit anti-pattern bans in [[agents-md-discipline|AGENTS.md]]: *"never use RxJava, use coroutines"* stops the agent from reaching for the dark patterns; pointing at the light module shows it the positive exemplar.

## See also
- [[agents-md-discipline]]
- [[legacy-migration-stages]]
- [[spec-driven-development]]
- [[code-graph]]
- [[cognitive-debt]]
- <span style="color:red">[[example-driven-prompting]]</span>
- <span style="color:red">[[ai-spiraling]]</span>
- <span style="color:red">[[tracer-bullets]]</span>
- <span style="color:red">[[selenium-to-playwright-migration]]</span>
- <span style="color:red">[[handover]]</span>
- [[2026-06-23-ai-device-maker]]
- <span style="color:red">[[2026-04-07-ai-product-team]]</span>
- <span style="color:red">[[2026-07-23-ai-software-product]]</span>
- <span style="color:red">[[2026-07-30-ai-safety-critical]]</span>
- <span style="color:red">[[2026-09-10-testing-ai-bank]]</span>
- <span style="color:red">[[2026-09-18-ai-agentic-how]]</span>
