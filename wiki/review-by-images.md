---
title: Review by Images
category: pattern
tags: [code-review, diagrams, plantuml, er-diagram, pre-push-hook, visual-review]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-06-25
updated: 2026-10-13
---

A review practice that prioritizes visual artifacts (diagrams, ER diagrams, rendered diffs) over raw text diffs, optimizing for the limits of human attention and pattern recognition.

## The principle

Review diagrams first — "what's red?" — before opening the diff. A picture surfaces structural changes (new tables, removed relations, dependency cycles) at a glance; a text diff buries them in noise.

The human brain processes spatial structure faster than linear text. Spending the first 2 minutes of a review on an image is more informative than spending the first 10 minutes on raw line-by-line changes.

<span style="color:red">**Generate the pictures with scripts, never with an LLM.** A diagram an LLM draws from code is hallucinated the moment it is drawn; one a script derives costs zero tokens, cannot hallucinate, and comes out identical when re-run tomorrow ([[2026-09-14-ai-industrial]], [[2026-08-31-ai-software-vendor]]). The LLM comes in only at the end — to explain obscure code and to judge things a script can't (e.g. GDPR on log statements).</span> <span style="color:red">The same generated artifacts double as agent context — see [[deterministic-diagrams]]; bundled into one PR report they become the [[human-review-dashboard]] ([human-review](https://github.com/victorrentea/human-review), [[2026-09-18-ai-agentic-how]]).</span>

## Automation pattern

A pre-push hook can:
1. Parse a SQL schema change or migration file.
2. Regenerate an ER diagram with new/changed entities **highlighted in red**.
3. Commit the rendered image beside the SQL in the PR.

The reviewer's first action is to scan the picture for red, not to read the SQL. This is a concrete application of [[agentic-review-pipeline]]'s "sorted review" step — the human sees the highest-signal view first.

<span style="color:red">Rather than parsing SQL, the robust variant **runs the Flyway migrations in Testcontainers/Docker, dumps the schema and renders `db.puml`** with the delta in red and only the "layers of friends" around the changed tables — it stays readable even on an 800-table schema ([[2026-08-31-ai-software-vendor]], [[2026-09-28-ai-gaming-tech]]).</span> <span style="color:red">Same for the domain model: reflect the entities into PlantUML and show **only the entities neighbouring the change** (not all 70), as a target-branch-vs-PR diff — green = added, struck through = deleted ([[2026-09-14-ai-industrial]]).</span>

## <span style="color:red">The catalogue of generated review views</span>

- <span style="color:red">**API compatibility, proven not eyeballed.** Diff the OpenAPI of main vs the PR with [oasdiff](https://github.com/oasdiff/oasdiff) or [OpenAPI Diff](https://github.com/OpenAPITools/openapi-diff) — it *proves* backward compatibility (a new optional request field is not breaking) ([[2026-08-31-ai-software-vendor]], [[2026-09-28-ai-gaming-tech]]). Message contracts get the same treatment: [AsyncAPI](https://www.asyncapi.com/) for RabbitMQ, Avro schemas for Kafka, and the MCP tool contract ([[2026-09-16-ai-safety-critical]]).</span>
- <span style="color:red">**Narrated demo video.** A TikTok-style [Playwright video](https://playwright.dev/docs/videos) of the feature on a pre-deployed environment with the PR merged; the LLM writes the Playwright script and the captions once, re-recording afterwards is tokenless ([[2026-08-31-ai-software-vendor]], [[2026-09-28-ai-gaming-tech]]).</span>
- <span style="color:red">**Visual regression.** Screenshots of every screen before/after ([Playwright snapshots](https://playwright.dev/docs/test-snapshots)) — in one demo it caught a plain `<select>` that had replaced the brand's dropdown component ([[2026-09-28-ai-gaming-tech]]).</span>
- <span style="color:red">**Runtime sequence from traces.** OpenTelemetry traces of annotated e2e tests rendered as a diagram; add [`@WithSpan`](https://opentelemetry.io/docs/zero-code/java/agent/annotations/) on the methods you want to appear. The generated diagram exposed a **duplicate search call** next to the N+1 the reviewer was hunting ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">**Fan-in / fan-out** of each changed class ([[2026-08-31-ai-software-vendor]]).</span>

## <span style="color:red">Protect the reader's visual memory</span>

<span style="color:red">Auto-layout destroys your mental map: one new line and every re-rendered PlantUML box moves. Keep the **conceptual** diagram hand-arranged in draw.io — *"get emotionally attached to the diagram"* — let tooling add only the red delta, and have a unit test parse the draw.io XML and **fail the build if the diagram drifts from the code** ([ConceptualModelDiagramTest](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/test/java/victor/training/petclinic/guardrail/ConceptualModelDiagramTest.java)) ([[2026-09-02-ai-insurer]], [[2026-09-14-ai-industrial]], [[2026-09-28-ai-gaming-tech]]).</span>

## Extensions

The same principle applies to:
- **Class diagrams** — highlight new/changed classes and their relations in red.
- **Dependency graphs** — surface new circular dependencies or architecture violations (see [[archunit-drift-control]]).
- **Architecture diagrams** — show [[drift]] from the intended design visually before the diff.
- **PR narration video** — an AI-narrated walkthrough of the diff, combining visual and audio explanation. This is particularly useful for large diffs that exceed human attention span.
- <span style="color:red">**Every other review question** — don't add a section for every curiosity: excess signal shuts down reasoning. Send parallel **scout agents** for one-off questions ("do we have an index on this?") and keep your own attention on the problem ([[2026-08-31-ai-software-vendor]]).</span>

## Relation to [[field-reality-diagram]]

The field-reality diagram captures the current production state; review-by-images captures the *delta* — what is changing. Both are visual tools for different review moments.

## Pitfalls

- Generating diagrams but not highlighting the delta (new/changed entities) defeats the purpose — the human still has to find the change manually.
- Committing rendered images into source control may bloat the repo; use a dedicated artifacts path or CI artifact storage.
- Over-relying on pictures for logic-level bugs — visuals catch structural issues; [[code-review]] and [[multi-model-review]] catch behavioral ones.

## See also

- [[archunit-drift-control]]
- [[code-review]]
- [[field-reality-diagram]]
- [[drift]]
- [[agentic-review-pipeline]]
- <span style="color:red">[[deterministic-diagrams]]</span>
- <span style="color:red">[[human-review-dashboard]]</span>
- <span style="color:red">[[openapi-contract-sync]]</span>
- <span style="color:red">[[determinism-over-llm]]</span>
- <span style="color:red">[[2026-09-28-ai-gaming-tech]]</span>
