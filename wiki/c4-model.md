---
title: C4 Model for Agent-Era Architecture
category: pattern
tags: [architecture, diagrams, c4, structurizr, plantuml, drift, archunit]
sources: ["[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-24-ai-bank]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Simon Brown's [C4 model](https://c4model.com) (Context → Container → Component → Code) is the zoom ladder used to keep a picture of what agents built — with the rule that only the levels derivable from code or traces can stay true.

## The four levels, and how each stays true

| Level | Shows | How to keep it in sync |
|---|---|---|
| **C1 Context** | The system as one box, its personas and external systems | Static, rarely changes — hand-written is fine. |
| **C2 Container** | SPA, API, database, queues, microservices | Hardest to keep in sync with what's deployed; best **derived from OpenTelemetry traces** ([[field-reality-diagram]]). |
| **C3 Component** | Packages/modules, controllers/services/repositories | **Derivable from code** — extract it, don't draw it. |
| **C4 Code** | Class diagrams | Auto-generated for 20 years; trivial. |

When a big model turns into a hairball, cut **views**: projections onto a subset ("just the quiz flow").

## Don't let the LLM draw it

**LLM-drawn C3 diagrams hallucinate.** A generated diagram showed `OwnerRestController → InvoiceService`; a second Claude instance checked the code and found no such call — *"the 2nd AI accused the 1st AI of hallucinations."* The fix is a program, not a better prompt — "I want a program, not a hallucination":

- **Extract the model programmatically** — e.g. Structurizr for Java from annotations plus Spring wiring; DomainModel `.puml` by reflection; sequence diagrams scripted from traces (which also expose N+1 queries), then C2 boxes-and-lines from the same traces. See [[deterministic-diagrams]].
- **Write dependencies once in text** with the [Structurizr](https://structurizr.com) DSL and cut views per zone ([example DSL in petclinic](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/c4model.c1%2Bc2.dsl)).
- **Give the agent a feedback loop** when it does write diagram code: validate PlantUML with its CLI before claiming done (an unchecked C3 once crashed the IDE plugin; tooling: PlantUML plugin + `graphviz`). See [[feedback-loop]].

## Enforce, don't just draw

Declare the allowed package dependencies in `architecture.puml`, let ArchUnit import it and **fail the build** when the agent crosses a boundary, and put the `.puml` under [[codeowners-elders]] — "so AI can *observe* the design but not change it alone." For Python/JS, a small import-parsing linter does the same job. See [[archunit-drift-control]] and [[drift]].

## Making it reviewable

- **Regenerate on every build and redden only the impacted elements**, so a reviewer sees at a glance what a PR touched ([[review-by-images]]).
- **Filtered views** are the payoff on huge systems: "show this repository and its nearest neighbours", or pick 3 packages out of 4000 and regenerate who depends on them — navigable where no single image could fit. A C4 model generated from a 700-table schema was one of the "show skeptics what's possible" demos.
- At scale, [Structurizr's enterprise workspaces](https://docs.structurizr.com/workspaces/enterprise) let each team maintain its part of the model and aggregate it upward — useful with hundreds of developers.

## What to share across repos

In a central knowledge repository share **C1 + C2** of every service, **not C3** — components are internal to their repo ([[cross-repo-knowledge]]). Victor's bet (summer 2026): C4 and Gherkin acceptance tests ([[acceptance-test-bdd]]) "explode next year" as the human-readable layer over agent-written code. C4 diagrams are one of the [[observation-points]] an engineer watches instead of reading every line.

## See also
- [[deterministic-diagrams]]
- [[archunit-drift-control]]
- [[field-reality-diagram]]
- [[drift]]
- [[observation-points]]
- [[review-by-images]]
- [[cross-repo-knowledge]]
- [[codeowners-elders]]
- [[acceptance-test-bdd]]
- [[2026-04-07-ai-product-team]]
- [[2026-09-16-ai-safety-critical]]
