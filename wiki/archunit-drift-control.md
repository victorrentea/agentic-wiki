---
title: ArchUnit + Reflected-Diagram Drift Control
category: pattern
tags: [drift, archunit, plantuml, diagrams, tests, no-drift, architecture]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-25-ai-bank-migration]]"]
created: 2026-06-23
updated: 2026-10-13
---

A drift-control pattern that makes architecture diagrams and package rules *self-verifying*: unit-test package dependencies with ArchUnit, and reflect the class diagram out of the code at test time, so a stale diagram fails the build instead of quietly lying.

## The problem it fixes

AI-generated `.puml` (PlantUML) diagrams [[drift]] from the code **the moment they're written** — the AI draws what it intended, not what shipped, and nobody re-draws them after the next change. A hand-drawn diagram is worse than none because it is confidently wrong. <span style="color:red">Hence the rule: **"Never ask AI to draw a diagram"** — generate a scripted projection of the code that cannot go out of sync ([[2026-08-10-ai-consultancy]]) — and its twin, *"never review a diagram an AI drew"* ([[2026-09-04-ai-software-company]]).</span>

## Two complementary mechanisms

- **ArchUnit** — express allowed package dependencies as a unit test (e.g. "the networking layer must not depend on the UI layer"). The rule is code; violations fail CI. Reuse hunting and layering intent become executable.
- **Reflected class diagram** — generate the `.puml` *from the compiled code* at test time and assert it matches a checked-in expectation. Delete a line from the `.puml` and the test fails; remove a DTO field and the diagram regenerates without it. The diagram becomes a fixture that can't go stale. <span style="color:red">For a JPA domain model, reflect over the entity annotations on every test run and commit the output — add a field and it shows up in the PR diff (≈250 lines of "a test that isn't a test") ([[2026-07-01-ai-java-community]], [[2026-08-06-ai-bank]], [[2026-09-07-ai-bank]]).</span>

<span style="color:red">The two mechanisms reduce to **two ways to keep a diagram in sync** ([[2026-08-13-ai-insurer]]): **generate it from the source of truth** (domain model from code; `DB.puml` by running every migration on a clean Postgres in Docker/Testcontainers and dumping the schema), or **hand-write it and enforce it with a test** (the package diagram below). Nobody reads the ≈350 generated lines — what matters is that they are always true, and that the diff of two generated diagrams in a PR shows a new FK instantly: *zero tokens, zero hallucination* ([[2026-08-27-ai-it-services]]). The full table of generated artifacts lives in [[deterministic-diagrams]].</span>

## The diagram as the ruleset: `PlantUmlArchCondition`

<span style="color:red">ArchUnit can consume a hand-drawn PlantUML component diagram directly as the allowed-dependency ruleset — an official feature, [PlantUML component diagrams as rules](https://www.archunit.org/userguide/html/000_Index.html#_plantuml_component_diagrams_as_rules). Write the intended architecture declaratively in `packages.puml` (which packages may talk to which); every dependency not drawn as an arrow fails the build ([example `PackagesArchTest`](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/test/java/victor/training/petclinic/guardrail/PackagesArchTest.java)). To the agent it is *"a booby trap it isn't aware of until the test runs"* ([[2026-04-16-ai-safety-critical]], [[2026-07-01-ai-java-community]], [[2026-09-25-ai-bank-migration]]). The same works for a [[c4-model]] package diagram: remove a real dependency from the drawing and the push fails until restored — *"drift in the diagram must fail the build"* ([[2026-06-02-ai-open-workshop]]).</span>

<span style="color:red">Other stacks: [ArchUnitNET](https://github.com/TNG/ArchUnitNET) or [NDepend CQLinq](https://www.ndepend.com/features/cqlinq) for .NET; for Python/JS, a small custom linter that parses imports — Victor wrote one for a Python project, and non-Java teams can simply point the agent at the ArchUnit test and ask for a port. The lineage is fitness functions / architecture-as-code from [Building Evolutionary Architectures](https://www.thoughtworks.com/insights/books/building-evolutionary-architectures) ([[2026-05-13-ai-it-services]]).</span>

## When the test fails: code or diagram?

<span style="color:red">The sessions disagreed at first. One rule said the diagram is the architectural decision and the agent must **fix the code, not the diagram** — when an arrow was deleted, the agent tried to edit the diagram to match the code and had to be told *"no — the diagram is the truth"* ([[2026-05-13-ai-it-services]], [[2026-07-23-ai-software-product]]). Another saw it positively: **updating the diagram is cheaper for the agent than restructuring the code, so the diagram stays in sync** ([[2026-08-27-ai-it-services]]). The reconciliation, by autumn 2026: both outcomes are fine **as long as a diagram change cannot pass unseen** ([[2026-07-30-ai-safety-critical]], [[2026-09-25-ai-bank-migration]]):</span>
- <span style="color:red">Put the `.puml`, the guardrail test package, the hooks, CI workflows, `pom.xml` and DB schema under **CODEOWNERS** ([[codeowners-elders]]) — changing them "summons the elder".</span>
- <span style="color:red">A new arrow in the diagram then shows up in review as a visible architectural decision — **"architecture surveillance"**.</span>
- <span style="color:red">The agent is left one honest exit: fix the code, or report *"the diagram is off"* and ask permission ([[2026-08-31-ai-software-vendor]]).</span>

<span style="color:red">**The four escapes** when the architecture test fails: refactor the code → change the diagram → comment out the test → stop running it (`--no-verify`) ([[2026-08-13-ai-insurer]]). Hence **"guards over guards"** ([[reward-hacking]]), and the **three walls** ([[2026-05-13-ai-it-services]]): the failing test blocks the commit in pre-commit; `--no-verify` is caught by the pre-push hook; editing the `.puml` triggers CODEOWNERS review. *"Everything not enforced as a test, the AI will eventually violate."* And *"every escape earns one new line in a markdown"* — reinforcement learning for your repo ([[2026-09-18-ai-agentic-how]], [[self-improving-knowledge]]).</span>

<span style="color:red">Underneath is one design principle: **the path of least resistance**. When an `OpenApiInSyncTest` failed after a DTO field was added, the agent's cheapest move was to regenerate `openapi.yaml` — so it did. *"Build the fences so the easy path is the right path"*: if circumventing a test is cheaper than complying, the agent circumvents ([[2026-05-06-ai-ecommerce]], [[enforcement-ladder]]).</span>

<span style="color:red">**Hook gotcha:** a `.githooks/pre-push` only fires after `git config core.hooksPath .githooks` has been run in that clone — otherwise the guardrail silently never runs ([[2026-07-01-ai-java-community]]).</span>

## Why it matters for AI slop

A bird's-eye reflected diagram is a fast way to **hunt AI-generated slop** in a vibe-coded app — *"I found the shit!"* — because the structure the agent actually produced is laid bare, not the structure it claimed. This is the static-structure cousin of the [[field-reality-diagram]] (which reflects *runtime* behaviour from traces): both make drift structurally impossible by regenerating from the source of truth instead of maintaining a parallel artifact by hand. <span style="color:red">The `scripts/` folder that *generates* these diagrams can be 100% vibe-coded and unread — acceptable, because its output is checked against reality on every run ([[2026-07-30-ai-safety-critical]]). And the tests themselves concentrate years of edge cases: without them, the agent re-discovers 12 years of framework bugs at your expense ([[2026-08-27-ai-it-services]]).</span>

It is also a [[feedback-loop]] for review: the diagram and the ArchUnit tests are an oracle the agent can be pointed at, and a guard against [[cognitive-debt]] in code humans no longer fully read.

## Beyond PlantUML: draw.io and runtime diagrams

- <span style="color:red">**Keep a human-owned layout.** Auto-layout trashes visual memory, so keep the layout in draw.io, owned by a human, and unit-test only its *content* against the code (`ConceptualModelDiagramTest`) ([[2026-09-04-ai-software-company]]).</span>
- <span style="color:red">**Test the diagram on Confluence.** A draw.io diagram exported as PNG [carries its XML inside the file](https://www.drawio.com/blog/xml-in-png) — in a PNG `tEXt` metadata chunk ([source](https://github.com/jgraph/drawio/blob/744cb5420fdf126efd7a09b1d7082ca3e12c0841/src/main/webapp/js/diagramly/EditorUi.js#L6685)), not a "header". A test can parse the deployment diagram and assert it matches the code; doing so found a second real API consumer missing from the drawing ([[2026-09-07-ai-bank]], [[2026-09-18-ai-agentic-how]]).</span>
- <span style="color:red">**Observed sequence diagrams.** Tag a critical Gherkin scenario, collect 100% of OTel traces during the E2E run, render PlantUML — they prove runtime facts a review cannot (a cache hit, N repeated calls to one API, broken trace propagation); one race condition was fixed from such a diagram ([[2026-08-10-ai-consultancy]], [[field-reality-diagram]], [[observation-points]]).</span>

## The contract chain

<span style="color:red">The same idea guards API contracts end to end ([[2026-08-06-ai-bank]], [[2026-09-07-ai-bank]], [[2026-08-27-ai-it-services]]): a test boots the app and extracts `openapi.yaml` from the Java code → the npm build regenerates `api-types.ts` from it → renaming a backend field turns the frontend compilation red immediately. Commit `openapi.yaml` **deliberately**, so an agentic PR review sees the contract diff. Open question in one team: where the contract lives (a separate repo or a published package), and a CI job that pushes regenerated clients to every frontend. *"The only files I still type by hand are the markdowns."* See [[openapi-contract-sync]].</span>

## Review-by-images and the pre-push hook

The natural extension: a `pre-push` hook that parses a SQL schema change, regenerates an ER diagram with new/changed entities **highlighted in red**, and commits the rendered picture beside the SQL change — so a reviewer reads a picture, not a raw diff. The same pattern applies to class diagrams and dependency graphs. Human reviewers should look at images *first*: "how did my model change? My schema? What's red?" — this optimizes human brain bandwidth for large PRs. <span style="color:red">A PlantUML diff of `main` vs the branch turns 4,000 changed lines into *"3 red classes"* ([[2026-08-06-ai-bank]]).</span>

See [[review-by-images]] for the broader visual-review pattern.

## See also
- [[drift]]
- [[field-reality-diagram]]
- [[feedback-loop]]
- [[cognitive-debt]]
- [[static-analysis-gauntlet]]
- [[code-review]]
- [[review-by-images]]
- <span style="color:red">[[deterministic-diagrams]]</span>
- <span style="color:red">[[codeowners-elders]]</span>
- <span style="color:red">[[c4-model]]</span>
- <span style="color:red">[[reward-hacking]]</span>
- <span style="color:red">[[openapi-contract-sync]]</span>
- <span style="color:red">[[observation-points]]</span>
- <span style="color:red">[[code-city]]</span>
- [[2026-06-23-ai-device-maker]]
- [[2026-06-22-ai-sports-betting]]
