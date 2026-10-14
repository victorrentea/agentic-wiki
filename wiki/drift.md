---
title: Drift
category: concept
tags: [drift, claude-md, documentation, staleness, no-drift, archunit]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-06-ai-medical-diagnostics]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]"]
created: 2026-06-11
updated: 2026-10-13
---

Drift is the silent divergence between a written artifact (memory file, spec, diagram, embedding) and the code it is supposed to describe — the central reason "the code is the holy truth."

## Where it strikes

- **[[claude-md]]** — bump Spring Boot or Angular and the memory file goes stale; a prompt that conflicts with a drifted CLAUDE.md is a [[hallucination]] cause (the model silently resolves the contradiction). Refresh ≈monthly with `/init`.
- **`design.md`** — drifts fast, so **burn it** once the code exists ("the spec is the path you walked; once you arrive, burn the path"). See [[spec-driven-development]].
- **Vector embeddings** — a stale embedding after its source row changes is the drift problem of [[rag]].
- **Hand-drawn diagrams** — rot the instant code changes.
- <span style="color:red">**Deep-research dumps** — materialising a deep code-research result into a markdown file is useful once, but it expires as soon as the code changes: keep such files short and scoped to the areas you know are complex. The same reason architecture/plan markdowns live on the PR and die after merge (see [[handover]]).</span>
- <span style="color:red">**Wikis** — Confluence is always behind; reality lives in code and tests.</span>

<span style="color:red">**Why it hurts more with agents:** when a document contradicts the code, the agent tends to believe the markdown over the code — and then blames it ("I trusted the markdown"). A human double-checks; an agent acts. So **generate whatever can be generated**, and keep hand-written prose to what can't.</span>

## Designing drift out

The strongest move is to make drift *structurally impossible* rather than fighting it manually:

- A scripted **Java → OpenAPI → TypeScript** pipeline so renaming a Java field **breaks the frontend build** — the contract can't silently diverge (see [[openspec]] and [[openapi-contract-sync]]). <span style="color:red">Back it with a **pre-push hook that blocks the push when regenerated artifacts** (`openapi.yaml`, generated TS types) are not committed.</span>
- A **[[field-reality-diagram]]** regenerated from real traces on every run — it can't drift because nobody hand-maintains it.
- **[[archunit-drift-control]]** for *static* structure — ArchUnit unit-tests package dependencies, and the class diagram is reflected out of the compiled code at test time, so a stale `.puml` fails the build. (AI-generated diagrams drift the moment they're written; see [[deterministic-diagrams]].)
- <span style="color:red">**One source of truth, many outputs.** A single backend authorization definition generates both the server-side `@Secured` annotations and which front-end buttons are hidden — zero drift between them (and remember a hidden button is not a secured feature: anyone with `curl` bypasses it). Likewise one shared table generator emitting **both HTML and PDF** reports, with only the renderer differing.</span>

## Detecting what can't be designed out

<span style="color:red">For prose that must stay hand-written, schedule a checker: a **[[doc-gardener]]** agent runs nightly and flags where `CLAUDE.md` and the docs no longer match what changed in the code.</span>

Drift is the machine analogue of the human risk of [[skill-erosion]]: both are entropy you must actively spend effort against.

## See also
- [[claude-md]]
- [[spec-driven-development]]
- [[field-reality-diagram]]
- [[rag]]
- [[hallucination]]
- [[skill-erosion]]
- [[archunit-drift-control]]
- [[doc-gardener]]
- [[openapi-contract-sync]]
- [[deterministic-diagrams]]
- [[handover]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
