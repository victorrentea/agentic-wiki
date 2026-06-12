---
title: Drift
category: concept
tags: [drift, claude-md, documentation, staleness, no-drift]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---


Drift is the silent divergence between a written artifact (memory file, spec, diagram, embedding) and the code it is supposed to describe — the central reason "the code is the holy truth."

## Where it strikes

- **[[claude-md]]** — bump Spring Boot or Angular and the memory file goes stale; a prompt that conflicts with a drifted CLAUDE.md is a [[hallucination]] cause (the model silently resolves the contradiction). Refresh ≈monthly with `/init`.
- **`design.md`** — drifts fast, so **burn it** once the code exists ("the spec is the path you walked; once you arrive, burn the path"). See [[spec-driven-development]].
- **Vector embeddings** — a stale embedding after its source row changes is the drift problem of [[rag]].
- **Hand-drawn diagrams** — rot the instant code changes.

## Designing drift out

The strongest move is to make drift *structurally impossible* rather than fighting it manually:

- A scripted **Java → OpenAPI → TypeScript** pipeline so renaming a Java field **breaks the frontend build** — the contract can't silently diverge (see [[openspec]]).
- A **[[field-reality-diagram]]** regenerated from real traces on every run — it can't drift because nobody hand-maintains it.

Drift is the machine analogue of the human risk of [[skill-erosion]]: both are entropy you must actively spend effort against.

## See also
- [[claude-md]]
- [[spec-driven-development]]
- [[field-reality-diagram]]
- [[rag]]
- [[hallucination]]
- [[skill-erosion]]
- [[2026-06-11-ai-playtika]]
