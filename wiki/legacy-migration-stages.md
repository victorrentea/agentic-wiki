---
title: Legacy Migration Stages
category: pattern
tags: [legacy, migration, testing, dead-code, kotlin, java, refactoring, openrewrite, strangler-fig]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-06-ai-medical-diagnostics]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-06-24
updated: 2026-10-13
---

A staged recipe for safely migrating a large, untested legacy codebase: first build a test harness, then remove dead code, and only then migrate technology — skipping stages collapses.

## <span style="color:red">Stage 0: pay the inherited debt</span>

<span style="color:red">*"Until the code is aligned, you can't accelerate with agentic."* Greenfield can accelerate immediately; legacy pays first — synonyms for the same concept, ancient libraries, missing tests ("debt: technical, cognitive, psychological"). The agent imitates what it sees, so it also copies every failed initiative it finds: **finish half-done and abandoned migrations first**, even when it looks like burning tokens for no business value — mixed paradigms wreck the agent's model of the code (see [[light-dark-code]]). The bottlenecks are often organisational too (processes, oversized teams, meetings, Conway) — "without data you can't optimise."</span>

## The three stages

1. **Tests first.** Have the agent write characterization tests until coverage reaches ≈85%. Do not migrate anything yet. Without this net, every change in the next stages is a gamble. <span style="color:red">Grow existing unit tests into **boundary-level functional tests** until the ≈85% comes from those alone, and run **[PIT](https://pitest.org/) mutation testing** on critical domain classes — 100% coverage is not enough without [[mutation-testing]]. Tests placed black-box on the API survive even a language change: one team moved 30k lines from Java to Rust with lower memory use and zero incidents that way.</span>

2. **Dead-code removal.** Ask the agent to find unreachable code. In typical legacy codebases, 10–100% of a class body may be dead. Removing it reduces scope and makes the migration target smaller and clearer. [[code-graph]] accelerates this: *"who calls this method?"* instantly reveals whether a symbol has any live callers. <span style="color:red">Good-citizen step: have the agent mark dead paths `@Deprecated` with a human-worded *why* and what to call instead; methods reachable only from tests get deleted after the refactor.</span>

3. **Migrate.** Only now rewrite technology — Java → Kotlin, callbacks → coroutines, RxJava → Flow. The test net from stage 1 catches regressions.

## <span style="color:red">Pouring concrete: ways to build the stage-1 net</span>

- <span style="color:red">**Every historical bug becomes a test.** An agent takes each bug from the tracker plus its fix commit and writes a test that fails without the fix and passes with it — "pour concrete retroactively wherever bugs were".</span>
- <span style="color:red">**Record the flows.** *"With the Playwright MCP, play with the app out of curiosity, exercise production-relevant flows and record them as Playwright tests so we see we don't break anything while refactoring"* ([[browser-mcp]]).</span>
- <span style="color:red">**Golden Master** before taking an axe to a 5,000-line class: keep a copy of the old code and compare old vs new output on observable behaviour (ideal for HTML or final responses).</span>
- <span style="color:red">**Production recordings.** Have the AI write a recorder you deploy next to the app — every input request and every output (SQL, API calls, results) to a file — then anonymise the capture (deterministic pseudonyms preserving referential integrity, spot-checked by a human before the data leaves prod) and replay it against the refactored version. *"You will never run in production yourself"* — only AI-*written* code runs there. Full pattern: [[record-replay-refactoring]].</span>
- <span style="color:red">**Fuzzy tests.** *"Looking at the data in my staging DB and the Jira tickets for this API, generate 200 reasonable test cases."* Combined with recordings, that is a "monster harness" around the legacy code.</span>

<span style="color:red">The real blocker for PL/SQL → Java or COBOL → Java rewrites is **missing tests on the old system**, not AI capability: with tests on the procedures (a DB MCP + tests), an AI rewrite becomes provable.</span>

## <span style="color:red">Mining requirements from an undocumented system</span>

<span style="color:red">For a 20–30-year-old system with no docs or tests, one room brainstormed every source the agent can mine:</span>
- <span style="color:red">**Code**: find the jar's sources on GitHub or the internal Nexus, else decompile ([Vineflower](https://github.com/Vineflower/vineflower)).</span>
- <span style="color:red">**Behaviour**: Playwright headless on staging, where testers have entered data for years — let it click around; a read-only DB on staging plus `curl` on the API.</span>
- <span style="color:red">**Paper trail**: Confluence/SharePoint, Jira, production incidents, end-user tickets, ADRs (historical context gives you the courage to change things); business emails and chats scanned **by the business with their own Claude** — not you in their inbox; pre-retirement interviews ([[elder-wisdom-mining]]).</span>

<span style="color:red">Deliverable: **functional tests on the old behaviour readable by non-technical people** (Gherkin or a test DSL — [[acceptance-test-bdd]]), validated with the business without a technical word. Everything mined goes into an [[llm-wiki]] (or RAG), then concisely back into Confluence. Before starting, study why any previous rewrite failed — and remember **the team's knowledge is the asset**: "we just have to validate" is why dark factories fail on legacy ([[gray-factory]]).</span>

## Why order matters

The instinct *"if it works, don't touch it"* is exactly how years of untested "weird stuff" accumulate. Eventually the codebase is too fragile to migrate safely. The staged approach forces a decision: invest in the safety net first, or accept that migration without tests is a guess.

<span style="color:red">**One migration at a time.** The disease of the agentic era is two simultaneous migrations — vertical slices on top of an unfinished layered → hexagonal move — guaranteed chaos: *"Everyone wants to start something. Finish something. Ship it."* Likewise **migrate first, build new features after** — two phases, never simultaneous. The one exception worth taking: migrate *and* slice vertically in a single pass per screen, so testers retest once, not twice.</span>

<span style="color:red">**Order for agent-driven bulk change:** test coverage first, then **one small victim**, verify, only then scale — delegating the run to a [[sub-agents|sub-agent]] told explicitly **what it may not do**.</span>

**Caution:** virtual-thread migration (Java 21) is genuinely risky in legacy code due to `synchronized` interaction with virtual threads — this step needs human review beyond what the agent can verify.

## <span style="color:red">Migration techniques that worked</span>

**<span style="color:red">Move, don't rewrite.</span>** <span style="color:red">The winning instruction for a huge structural refactor (God-DAOs → ≈40 EJBs, ≈100k lines) was *"maintain the exact behavior — move, don't rewrite"*: SQL and logic moved verbatim, structure changed without the model re-reasoning the logic. Keep only the source/target pair in context so each move stays clean. Result: one genuine new defect in ≈100k moved lines.</span>

**<span style="color:red">Freeze the risky layer physically.</span>** <span style="color:red">When queries and business rules must stay byte-identical, don't trust a prompt: keep the DAO interfaces visible but move the implementations into a **hidden Maven module** the agent never opens; split a 12k-line query XML per domain first. On a 100k-line migration PR the frozen files were *proven* unchanged — check "SQL stays byte-identical" adversarially, not by asking.</span>

**<span style="color:red">Show, don't tell.</span>** <span style="color:red">Hand-craft the first screen slowly (mini-prompts under a minute each) — screen two then takes **one prompt for ≈80%**. A **skeleton spike beats an architecture.md**. Polish one file by hand, then *"apply my diff everywhere"*; or hand-polish one slice as **≈100 small commits** and say "do the rest" — the commit history is the example. Review the first 3–4 copies to the blood. A variant from an hour of hand-refactoring with AI: ask it to read the final version *and* the conversation, then write a script that takes the remaining near-identical classes there.</span>

**<span style="color:red">Deterministic tools for mechanical bulk.</span>** <span style="color:red">Mass migration ≠ mass rewrite by Opus. Use [[openrewrite]] recipes that change syntax "without touching a token" — and use AI to *write* the recipes (e.g. [ListGetFirstTest](https://github.com/victorrentea/petclinic/blob/main/refactoring-tools/src/test/java/victor/training/petclinic/rewrite/ListGetFirstTest.java)), not to do the rewrite: public recipe → AI-written incremental recipes → test each on a sample → AI hand-finishes the bespoke ≈5% → parallel sub-agents per module for stubborn errors. Lombok via an IntelliJ inspection beats the agent rewriting 5,000 getters — exact, and no output tokens. For mass Java 8 → 21 upgrades there is [AWS Transform](https://aws.amazon.com/transform/custom) (formerly Amazon Q Code Transformation). For CVEs, let Renovate/Dependabot auto-upgrade first (zero tokens; a PR only if build and tests are green — the risk moves into your test quality) and keep agents for the hard upgrades. See [[determinism-over-llm]].</span>

**<span style="color:red">Prove equivalence by running both.</span>** <span style="color:red">Replacing a home-grown schema migrator with [Liquibase](https://www.liquibase.com): impact analysis first ("is this framework used anywhere but creating schemas?"), then a minimal experiment that converts one legacy migration class into a changeset while a test **runs both and proves an identical final schema** — iterate until **50 in a row match**; the *data* migration, not the `ALTER TABLE`, is the hard part. The same pattern compares a Jenkins and a GitLab pipeline stage by stage (same artefacts within tolerance), diffs generated **Terraform plans**, or has a QA agent click **the old and new app side by side** (e.g. a JSF → Angular migration). Mutation-test the migration itself: a test that fails if the data-migration logic changes at all.</span>

**<span style="color:red">Strangler regeneration from specs.</span>** <span style="color:red">Point the agent at a small, decoupled module (≈10–12 entities), have it write [[spec-driven-development|specs]] from the old code, then regenerate it with a modern architecture as a companion app next to the legacy server — sharing the same DB through a separate **schema + views** (avoids CDC/Kafka eventual-consistency pain), keeping the old front end so e2e still passes, and sharing one SSO token across both back ends. "The best deploy looks exactly the same." ([Strangler Fig](https://martinfowler.com/bliki/StranglerFigApplication.html)) Don't over-engineer it: hexagonal architecture is overkill for 20 classes with 3-line services — slice by feature.</span>

**<span style="color:red">Delete and reconstruct.</span>** <span style="color:red">Wipe one class from the approved codebase and have the agent rebuild it from the rest (no git peeking): identical and green means it truly understands the pattern — then let it loose.</span>

## Agentic leverage

The agent is particularly good at stage 1 (mechanical test generation) and stage 2 (dead-code detection via call-graph analysis with [[code-graph]]). Stage 3 benefits from the agent's knowledge of the target language/framework but needs the human to validate idiomatic correctness and performance implications.

<span style="color:red">Opus can read and rewrite ≈100k lines with a low error rate — **but only under real e2e coverage** (aim for ≥20% e2e). Use frontier models only on monster refactorings (high/xhigh effort, the top tier reviewing — see [[model-hierarchy]]). Burn leftover monthly quota on modularising the old system.</span>

<span style="color:red">**People and boundaries.** In a monolith you own 5% of, **carve module boundaries and stable APIs first**, then "unleash hell behind them"; cross-domain refactors need the tech leads of both domains sitting with the agent. Before a risky refactor, ask *"who worked here before?"* — it forces a `git blame` and history read. And keep a **boy-scout 20%**: spend ≈20% of every task cleaning up, without asking — *"you should never need to ask for a tech-debt budget."* For a project that dies tomorrow, *conscious* YOLO is acceptable — as a decision, not from ignorance.</span>

## Selenium → Playwright as a concrete migration pattern

A concrete instance of the three-stage recipe applied to end-to-end tests: dead Selenium-Java tests (not run on CI for ≈2 years, locators rotted) migrated to Playwright-TypeScript while **reusing the exact same Gherkin `.feature` files**.

- **Rewrite only the glue** (step definitions), never the `.feature` files — they are the acceptance oracle.
- **Verify the port with mutation testing**: break something in the browser so the old Selenium fails, then confirm the same breakage fails the new Playwright test.
- **Reality check**: if the Selenium tests are already broken, run a small experiment to gauge locator-repair cost before committing to the "fix Selenium first, then migrate" path.
- <span style="color:red">**Don't switch test stacks mid-migration.** During a framework migration that leans on working Selenium e2e (e.g. Vue 2 → 3), complement the existing Selenium tests rather than porting them to Playwright at the same time — one migration at a time.</span>

See [[selenium-to-playwright-migration]] for the full pattern.

## See also
- [[code-graph]]
- [[acceptance-test-bdd]]
- [[hardware-mock]]
- [[feedback-loop]]
- [[vibe-fixer]]
- [[agents-md-discipline]]
- [[selenium-to-playwright-migration]]
- <span style="color:red">[[light-dark-code]]</span>
- <span style="color:red">[[openrewrite]]</span>
- <span style="color:red">[[mutation-testing]]</span>
- <span style="color:red">[[record-replay-refactoring]]</span>
- <span style="color:red">[[elder-wisdom-mining]]</span>
- <span style="color:red">[[determinism-over-llm]]</span>
- <span style="color:red">[[llm-wiki]]</span>
- [[2026-06-23-ai-device-maker]]
- [[2026-06-22-ai-sports-betting]]
- <span style="color:red">[[2026-07-06-ai-medical-diagnostics]]</span>
- <span style="color:red">[[2026-07-23-ai-software-product]]</span>
- <span style="color:red">[[2026-09-25-ai-bank-migration]]</span>
- <span style="color:red">[[2026-10-06-gray-factory-devoxx]]</span>
