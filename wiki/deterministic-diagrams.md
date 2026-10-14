---
title: Deterministic Diagrams as Agent Context
category: pattern
tags: [diagrams, drift, plantuml, c4, openapi, ddl, draw-io, context, architecture]
sources: ["[[2026-09-25-ai-bank-migration]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-07-03-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Deterministic diagrams are architecture views that a script generates, or a test verifies, from the real code. They are never drawn by an LLM. You point the agent at these files (from [[agents-md|AGENTS.md]]) so it reads a dense, true map instead of reconstructing one, or trusting a stale one.

## Why not let the LLM draw them

An LLM-drawn diagram is confidently wrong the moment it is written, and it goes on [[drift|drifting]] silently. A generated one costs nothing to keep true: re-run the script and it reflects today's code, identical tomorrow ([[2026-09-25-ai-bank-migration]]). Even a C4 model edited by the agent needs checking. In one demo the agent "updated" a [Structurizr](https://structurizr.com) DSL to add a Redis queue, and only a screenshot of the rendered result plus the question *"did you actually connect Redis to the notification service?"* showed the missing edge. Check box by box ([[2026-07-03-ai-bank]]).

## One diagram = one test

The pipeline that keeps each artifact honest ([[2026-05-06-ai-ecommerce]], [[2026-05-11-ai-software-vendor]], [[2026-05-13-ai-it-services]]):

| Artifact | Generated from | Guarded by |
|---|---|---|
| `openapi.yaml` | springdoc: boot the app in memory, dump `/v3/api-docs` ([example](https://github.com/victorrentea/petclinic/blob/main/openapi.yaml)) | `OpenApiInSyncTest`, see [[openapi-contract-sync]] |
| TypeScript API types | `openapi.yaml` via [openapi-typescript](https://www.npmjs.com/package/openapi-typescript) | the TS compiler: a renamed DTO field breaks the frontend build |
| `final.ddl` / `db-schema.md` | all Flyway migrations applied to a test Postgres, then `pg_dump --schema-only` | `DdlInSyncTest` |
| `packages.puml` | Java packages | [[archunit-drift-control|ArchUnit]] |
| `domain.puml` | JPA entities via reflection | regenerated in the build |
| Maven module graph | [`dependency:tree`](https://maven.apache.org/plugins/maven-dependency-plugin/tree-mojo.html) | regenerated in the build |
| C4 views | [Structurizr DSL](https://docs.structurizr.com/dsl): hand-written descriptions, generated edges ([[c4-model]]) | rendering plus review |
| `sequence.puml` | OpenTelemetry traces of a staging E2E run | living docs, not a test |

The schema dump is the big win for agents. They read one dense DDL instead of fifty migration files. Run the in-sync tests in a **pre-commit hook** (`.githooks/` + `git config core.hooksPath`), so the agent sees red and regenerates. Complying becomes the path of least resistance. A working set of these files lives in [petclinic's docs folder](https://github.com/victorrentea/petclinic/tree/main/petclinic-backend/docs), referenced from its [AGENTS.md](https://github.com/victorrentea/petclinic/blob/main/AGENTS.md) ([[2026-09-25-ai-bank-migration]]).

## Hand-laid diagrams, machine-checked

Auto-layout destroys visual memory: add one line and every box moves ([[2026-09-30-ai-enterprise]]). So the **conceptual** model stays a draw.io diagram arranged by a human, and a unit test asserts its edges against the code, for example that Owner really has a Pet collection ([ConceptualModelDiagramTest](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/test/java/victor/training/petclinic/guardrail/ConceptualModelDiagramTest.java)). The slogan is *"the AI draws the line, you move the boxes"* ([[2026-10-01-ai-bank]]).

**Feed metadata, not pixels.** A draw.io PNG export embeds its XML ([FAQ](https://www.drawio.com/doc/faq/export-to-png)), so the agent parses the exact graph instead of squinting at an image. For Gliffy, export the JSON source ([[2026-09-25-ai-bank-migration]]).

## Hand-written diagrams are for *why*

Don't hand-draw what the agent can mine from code (package dependencies, call graphs). Do hand-write **edge labels that explain intent**. In a ten-year-old monolith of 200+ modules, that saves the agent minutes per edge. The state of the art for legacy is [[c4-model]] docs plus PlantUML plus a symbol-level code MCP ([[code-graph]]), all kept current. *"The last word is the hardest"* ([[2026-05-27-ai-open-workshop]]).

## Small, scoped views

- **Many small diagrams, not one giant.** A 600-table schema is useless as one picture ([[2026-10-01-ai-bank]]).
- **Show only the neighbours of what you care about.** Generate a C3 view with just one component's direct neighbours, or a PlantUML diff against origin that keeps only the impacted entities ([puml_diff.py](https://github.com/victorrentea/human-review/blob/main/skills/human-review/puml-diff/puml_diff.py)). You get many detail levels from one source, and each is cheap in tokens ([[2026-07-03-ai-bank]], [[2026-10-01-ai-bank]]).
- **Call hierarchies by script.** One query per util method reveals which entry points use it. [CodeGraphContext](https://github.com/CodeGraphContext/CodeGraphContext) is one tool for this ([[2026-09-25-ai-bank-migration]]).

## Cross-check against runtime truth

A drawn integration map is a claim; check it against the systems that actually route traffic.

- **The API gateway knows who calls whom.** Compare the diagram with the gateway's authorised callers, for example a Kong ACL plugin config ([[2026-09-25-ai-bank-migration]]).
- **Find consumers before a breaking change.** In one demo the agent parsed a draw.io PNG's embedded XML and found that an API consumer (an MCP chatbot) was **missing from the diagram**. Validate diagrams against e2e or production traces ([[2026-09-30-ai-enterprise]]).

## Beyond one service

A far-future idea: each microservice publishes its own C4 view to a central repo, and an architect agent reconciles the diverging perceptions and pushes back on outliers, like a Backstage catalog with opinions. The technical part is easy; the hard part is political: whose model is truth ([[2026-05-06-ai-ecommerce]])? See [[enterprise-architect-agent]].

The same generated views, diffed between main and a PR, are what a human reviewer should see first. See [[review-by-images]] and [[human-review-dashboard]].

## See also
- [[review-by-images]]
- [[archunit-drift-control]]
- [[drift]]
- [[c4-model]]
- [[field-reality-diagram]]
- [[openapi-contract-sync]]
- [[code-graph]]
- [[agents-md-discipline]]
- [[determinism-over-llm]]
- [[human-review-dashboard]]
- [[2026-09-25-ai-bank-migration]]
