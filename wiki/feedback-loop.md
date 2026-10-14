---
title: Feedback Loop
category: pattern
tags: [feedback-loop, agentic-engineering, self-correction, screenshots, tests, adb, playwright, verification]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-06-ai-medical-diagnostics]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-10-07-agentic-reconversion-devoxx]]"]
created: 2026-06-23
updated: 2026-10-13
---

A feedback loop is any mechanism that lets the agent *observe the result of its own work* and iterate without a human in the middle — the single most important thing to set up before handing off a task.

## The core rule

**Find a feedback loop for every task you hand off.** An agent that can only emit code and wait for you to report the outcome is stuck in the [[chop-ai-waiter|CHOP / AI-waiter]] anti-pattern — *"we are not the servants of AI."* An agent wired to a loop closes its own correction cycle: act, observe, adjust, repeat until the signal is green.

<span style="color:red">The same rule, as the sessions phrased it: **"Humans should not check the work of AI. Tools should"** [[2026-07-03-ai-bank]]. *"Craft a feedback loop that lets the agent prove it did the job — the most important concern in engineering from now on"* [[2026-07-23-ai-software-product]]. *"Any hard problem is solved once you build the agent a feedback loop around it"* [[2026-10-07-agentic-reconversion-devoxx]]. Anthropic's [best practices](https://code.claude.com/docs/en/best-practices#give-claude-a-way-to-verify-its-work) say the same: give Claude a way to verify its work [[2026-08-27-ai-it-services]].</span>

## <span style="color:red">"You shot blind"</span>

<span style="color:red">*"Every time you give the AI a task without an automated verification loop, you shot blind."* A `pg_trgm` index was requested without any check that it worked; the extension wasn't enabled on the test DB, the index silently wasn't created, a warning was ignored — slop swept under the rug, because AI doesn't feel the pain [[2026-05-11-ai-software-vendor]]. Likewise, a paginated grid passed its tests but still had the scrollbar the spec forbade, a blank gap, and columns that jumped widths on sort: *"it didn't validate in the browser; it shot blind"* [[2026-04-16-ai-safety-critical]].</span>

- <span style="color:red">**A requirement without automated verification is a wish.** If the spec says "must retry", the next question is *"how do I test automatically that retry works?"* — if you can't answer, don't write the requirement. Fault-injection tools: [Toxiproxy](https://github.com/Shopify/toxiproxy) (latency, resets, packet loss, timeouts), [WireMock](https://wiremock.org/) (HTTP faults), [Resilience4j](https://resilience4j.readme.io/) [[2026-05-11-ai-software-vendor]].</span>
- <span style="color:red">**"All tasks done" ≠ works.** The spec agent ticked every task and tests passed, yet the screen showed an empty grid with no console errors: the backend returned a paginated envelope, the frontend expected a flat array, and the spec's "on error return empty list" swallowed it. Standing rule since: for features spanning backend and frontend, **at least one end-to-end Playwright test that clicks through the UI** and verifies the rendered output [[2026-05-06-ai-ecommerce]].</span>
- <span style="color:red">**"A green build is not proof"** — add a visual loop: start the app and click through it [[2026-07-30-ai-safety-critical]].</span>
- <span style="color:red">**"If a test doesn't run on the build, it doesn't exist"** — E2E suites launched only at night are non-existent tests [[2026-08-03-ai-telecom-ops-qa]]; and *"a test is green or it doesn't exist"* — catch the agent when it comments tests out ("boss, they passed before") [[2026-08-06-ai-bank]]. See [[reward-hacking]].</span>

## What counts as a loop

- **Build / compiler warnings** — *"build the project so the deprecation warning surfaces,"* then loop until it's gone.
- **Tests** — run [[acceptance-test-bdd|acceptance tests]] until green; write a stress test with 20 parallel threads until the race bug reproduces, *then* fix it. <span style="color:red">*"Do red-green"* are three magic words ([[red-green-tdd]]); quoted lifts of ≈50% quality and 25–30% accuracy are anecdotal, but the mechanism — expectations as an executable check first — is sound [[2026-05-06-ai-ecommerce]] [[2026-05-13-ai-it-services]].</span>
- **Screenshots** — *"stay in a loop taking screenshots until the SVG is pixel-aligned"* (a pixel-perfect render reached <0.5% diff after ≈40 minutes unattended). Use a [[browser-mcp|Playwright]] accessibility-tree read or vision diff.
- **[[adb-automation]]** — on mobile, the agent screenshots/taps/drags a running emulator to reproduce a bug, fix it, and confirm a green test, hands-free.
- **Reflected diagrams** ([[archunit-drift-control]], [[field-reality-diagram]]) — a structural oracle the agent can check against. <span style="color:red">AI draws architecture diagrams badly but proves each line well, so iterate the diagram until it agrees with evidence — callers proven by JWT/service-account permissions, [OpenTelemetry traces](https://opentelemetry.io/docs/concepts/signals/traces/) or parsed draw.io XML, C4 container lines diffed per PR ([[deterministic-diagrams]]) [[2026-09-24-ai-bank]].</span>
- <span style="color:red">**Performance numbers** — pin a load test in the acceptance criteria (e.g. Gatling: p95 < 500 ms at 30 rps with 10k rows) so "fast enough" is executable [[2026-05-06-ai-ecommerce]]; or **"prove me wrong"**: *"write a load test on 10,000 owners with Testcontainers + Postgres; fail if > 10 ms"* [[2026-04-27-ai-healthcare-software]]. On one machine DB calls are unrealistically fast — add artificial latency so the test exposes query volume and N+1 patterns [[2026-05-11-ai-software-vendor]].</span>
- <span style="color:red">**Query plans** — optimise a query, then run it until the plan is actually faster [[2026-07-03-ai-bank]]. In closed loops against anonymised prod data, a cheap model found 2 optimisations in 15-year-old three-page queries, and in another session beat two DBAs — "knowing how to write that query is no longer job security" [[2026-09-07-ai-bank]] [[2026-09-24-ai-bank]].</span>
- <span style="color:red">**Coverage** — extract (un)covered lines from the report with a **script**, not a markdown instruction; without tests the agent can't close its loop at all [[2026-06-08-ai-security-tools]].</span>
- <span style="color:red">**Acceptance criteria as a self-grading rubric**, even when not scripted: *"Go through each criterion and explain why it is or isn't fulfilled."* For DevOps, a pre-existing end state (infra already in the cloud — "deploy and diff") is a built-in oracle [[2026-05-06-ai-ecommerce]].</span>

## <span style="color:red">Build the instrument first</span>

<span style="color:red">**Have the agent build its verification instrument first, then iterate on the numbers.** Winning prompt: *"produce the coverage report, find the uncovered line yourself, fix the test, don't touch production code"* — and *"don't rush to fix and say it's done; the validation scaffolding comes first."* A vector-clock SVG was redrawn in ≈40 attempts against a diff tool the agent wrote itself, marking differences in red. **The agent is done not when it says so, but when the instrument it built shows green** [[2026-09-10-testing-ai-bank]].</span>

- <span style="color:red">Give a **measurable criterion, not an aesthetic goal**: diff old vs new screenshot, programmatically redden the differing pixels, *"minimise the red pixels"* [[2026-07-09-ai-live-casino]] [[2026-08-27-ai-it-services]]. Loops can be wild: an old JSP app checked pixel-perfect side by side against its Angular rewrite [[2026-10-07-agentic-reconversion-devoxx]].</span>
- <span style="color:red">Not "start the app" but *"stay in a loop until you see 28 owners at `localhost:4200`"*. Watch it for 3 minutes, then leave; stop and steer if it thinks nonsense [[2026-08-06-ai-bank]].</span>
- <span style="color:red">**LLM-as-judge regeneration**: delete an impeccable human-written artifact, have a sub-agent regenerate it from the skill alone, and iterate until close — e.g. release notes learned from the last 4 hand-written releases, tested by regenerating an already-shipped one ([[judge-llm]]) [[2026-07-30-ai-safety-critical]].</span>
- <span style="color:red">**Ask for proof, not status**: never *"are you done?"* but *"a screenshot of the feature working / the passing test / the diff"* [[2026-04-27-ai-healthcare-software]]. *"You've become agentic the day you ask the agent 'demonstrate it to me' instead of dictating the steps"* [[2026-09-10-testing-ai-bank]].</span>

## <span style="color:red">The bug-fix loop</span>

<span style="color:red">The standard prompt — never just "fix it" [[2026-07-09-ai-live-casino]] [[2026-07-01-ai-java-community]] [[2026-08-12-ai-bank]] [[2026-08-31-ai-software-vendor]] [[2026-09-10-testing-ai-bank]] [[2026-09-24-ai-bank]]:</span>

1. <span style="color:red">`fix bug #40` — let the agent find the ticket in the tracker itself;</span>
2. <span style="color:red">start backend + frontend and **reproduce the bug in a real browser** (Playwright CLI);</span>
3. <span style="color:red">write a **failing e2e test** (Gherkin + Playwright works) — and guard the backend API too;</span>
4. <span style="color:red">fix until green;</span>
5. <span style="color:red">hand back a **before/after video with subtitles** (Playwright records, TTS narrates) — a short "TikTok-style" demonstration.</span>

<span style="color:red">You review only the patch **and the demonstration**; the Jira → PR fix happens without opening the IDE [[2026-09-18-ai-agentic-how]]. One session cited OpenAI's [Harness engineering](https://openai.com/index/harness-engineering) post for PRs approved from the video [[2026-08-31-ai-software-vendor]]. Runs take 5–12 min unattended [[2026-08-27-ai-it-services]]. For a new constraint, go **backend first** (test red → green), only then the frontend [[2026-07-09-ai-live-casino]]. QA teams use the same loop to open tickets with screenshots, expected vs actual and a video of the steps — *"you've never seen tickets this good"* [[2026-08-03-ai-telecom-ops-qa]].</span>

<span style="color:red">Playwright's browser profile is separate (no cookies) but can attach to your logged-in Chrome — then it can do anything you can, including HR systems and timesheets. If an MCP exists for a site, the agent prefers it over UI navigation; check what it actually used [[2026-08-27-ai-it-services]]. See [[browser-mcp]].</span>

## <span style="color:red">UI loops</span>

- <span style="color:red">End every UI task with *"open the browser, take screenshots, compare visually with the spec"* — better, *"stay in a loop taking screenshots until you're happy with the result"* [[2026-04-16-ai-safety-critical]] [[2026-04-27-ai-healthcare-software]]. A standing memory rule — *"every time you change the frontend, take a screenshot and verify"* — caught a white-on-white header [[2026-07-03-ai-bank]].</span>
- <span style="color:red">**Annotated screenshots as UI spec**: draw red marks (or move cut-out regions) on a screenshot, talk over it, and paste it — the agent does the CSS [[2026-04-27-ai-healthcare-software]] [[2026-05-04-ai-telecom]] [[2026-07-09-ai-live-casino]]. If it's still wrong, paste a screenshot back; don't fix it yourself [[2026-07-03-ai-bank]] ([[review-by-images]]).</span>
- <span style="color:red">Review the running UI **as a product owner, not a coder** — affordance, paginator position, alignment — describe defects in plain words and re-fire [[2026-07-23-ai-software-product]]. Watching the browser during a run caught a test that filled the *time* but never the *date* [[2026-07-06-ai-medical-diagnostics]].</span>
- <span style="color:red">**"I want one like that"**: another app's URL or a pen-drawn screen → a prototype in 10 minutes, polished live with the business beside you. Cloning is trivial now; the value stays in ideation [[2026-08-13-ai-insurer]].</span>

## <span style="color:red">Speed up the loop</span>

<span style="color:red">**Tier the tests by speed**: a TDD-ing agent runs only the fast, relevant unit tests; anything over ≈5 s leaves the inner loop for a harness `Stop` hook, a git pre-push hook, or CI watched by the agent ([[ci-green-loop]], [[agent-lifecycle-hooks]]). Run only fast linters inline — a post-edit formatter hook invalidates the agent's file cache — and slow ones (Sonar, ≈3 min) at pre-push [[2026-07-22-ai-agentic-how]]. Past ≈15 minutes of build, "the film breaks, the human is already on TikTok" [[2026-08-03-ai-telecom-ops-qa]].</span>

<span style="color:red">*"Vibe your developer tooling; it's the safest place to be reckless."* Integration-test hacks to hand to an agent: pre-seeded schema and dummy data at DB boot; heavy parallelisation with a single Spring context enforced by a failing build; predictive test selection from the change's call graph; bytecode hot-swap over the debug port; `@Lazy`; [Spring Modulith](https://docs.spring.io/spring-modulith/reference/testing.html) single-module boot; [Azul CRaC](https://docs.azul.com/core/crac/crac-introduction). ≈20 s of Spring boot is the floor. One speaker's verdict: *"Java is the worst language for agentic coding"* — slow startup plus verbosity burn tokens and loop time [[2026-07-22-ai-agentic-how]].</span>

<span style="color:red">**Make the app runnable locally just to give the agent a loop**: one team adapted a large Spring MVC app that only started inside a heavyweight application server to also boot with Spring Boot locally [[2026-07-30-ai-safety-critical]].</span>

## Why it's the whole game

The deeper thesis: the new engineering skill is **seeing, testing, and reproducing the problem**, not fixing it — *"move your focus from typing to feeling the problems"* (races, deadlocks, load, stress). The agent can fix almost anything *once you point it at a loop that makes the problem observable*. Expensive loops (start-the-app, pixel-compare) should be gated so they don't auto-fire every turn — set `disable-model-invocation: true` on the [[agent-skill]] that wraps them.

<span style="color:red">**Anything with a mechanical feedback loop is already solved**: load tests, JFR profiling, heap dumps (17 analysed in one session), build-time optimisation — hand it the artefact and let it loop for days [[2026-08-10-ai-consultancy]]. Flaky tests and slow builds are golden ground: a 1-hour build → 20 minutes with caches and parallelisation [[2026-08-03-ai-telecom-ops-qa]]; −30% after 3 days, with the agent mounting Java Flight Recorder on the build itself — which also **tests your observability before the production incident** [[2026-08-27-ai-it-services]]. Autonomous hours come from **slow tool calls** (e2e builds, hour-long suites), not from thinking [[2026-08-27-ai-it-services]].</span>

<span style="color:red">**A sandbox teaches better than a manual.** To teach an agent a proprietary vendor language without staging, a sandbox where it tries and sees the result beats the PDF: **examples > specification, sandbox > examples**. Give it real prod queries as samples, then let it write its own skill ([[example-driven-prompting]]) [[2026-09-07-ai-bank]]. Given a sandbox, frontier models brute-force hard low-level problems: Opus reverse-engineered an air conditioner's wire protocol in ≈4 hours of trial bytes; Anthropic reports that Mythos ([Project Glasswing](https://www.anthropic.com/glasswing)) found zero-days in every major OS and browser [[2026-09-24-ai-bank]].</span>

<span style="color:red">**Criterion for a good automation idea: it has a feedback loop** — otherwise you remain the evaluator of every output [[2026-07-30-ai-safety-critical]]. The proof-loop test for where to trust AI at all: *"AI doesn't help when failure is expensive and not automatically verifiable"* — use it where mistakes are cheap **or** verification is automatic; keep a human expert where both are false (a chatbot under-sized a home boiler that a plumber would have got right — ≈£2,000 wasted; tax and legal advice fall in the same bucket) [[2026-05-06-ai-ecommerce]] [[2026-05-11-ai-software-vendor]].</span>

## <span style="color:red">Feed it context and tools</span>

<span style="color:red">When the agent slops, it usually **lacks your context or your tools** (MCPs or CLIs) — "agent parenting" [[2026-10-07-agentic-reconversion-devoxx]]. **"Teach it how to fetch, don't fetch for it"**: don't ask a model to recall CLI flags — let it run `--help` or fetch the docs, then bake the result into a skill [[2026-07-23-ai-software-product]]; turn whatever you explained for half an hour into a skill, scripted where possible [[2026-10-07-agentic-reconversion-devoxx]].</span>

<span style="color:red">**Every time you feel like copy-pasting into it, connect that source instead**: code, staging DB, production *metrics* (RPS and table sizes are derivable from exposed metrics alone) [[2026-08-10-ai-consultancy]]; the infra repo ("maybe you have Kafka and don't know it"), draw.io diagrams (the PNG carries the XML), runbooks and post-mortems, **sanitised meeting transcripts committed as markdown** — "pure gold"; a local Whisper model needs no corporate MCP approval [[2026-08-13-ai-insurer]] [[2026-08-10-ai-consultancy]]. The north-star prompt shape: problem + context + a loop it can run (*"…as you can see in the production metrics via your MCP"*) [[2026-04-16-ai-safety-critical]]. An E2E lab prompt can even capture the **distributed trace in Grafana** across apps started on separate ports ([[observability-mcp]]) [[2026-08-03-ai-telecom-ops-qa]].</span>

<span style="color:red">The agent has a common-sense limit — "I've worked on this too long, are you sure?" — and forcing it with "go on" produced work that only the tests revealed as wrong [[2026-08-06-ai-bank]]. When a refactor breaks unit tests, let the AI *explain* the failure, then **you** decide whether the test or the refactor is wrong — never let it silently make the test green [[2026-04-27-ai-healthcare-software]].</span>

## See also
- [[chop-ai-waiter]]
- [[acceptance-test-bdd]]
- [[browser-mcp]]
- [[adb-automation]]
- [[archunit-drift-control]]
- [[ci-green-loop]]
- [[agentic-engineering]]
- <span style="color:red">[[red-green-tdd]]</span>
- <span style="color:red">[[reward-hacking]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[judge-llm]]</span>
- <span style="color:red">[[observability-mcp]]</span>
- <span style="color:red">[[review-by-images]]</span>
- <span style="color:red">[[deterministic-diagrams]]</span>
- [[2026-06-23-ai-device-maker]]
