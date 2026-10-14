---
title: OpenAPI Contract Sync
category: pattern
tags: [openapi, api-contract, drift, codegen, git-hooks, frontend]
sources: ["[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-23-ai-software-product]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

OpenAPI contract sync derives the API contract from the backend code on every build or push, regenerates the frontend types from it, and lets the compiler fail on any mismatch — so drift between back end and front end becomes impossible rather than something a reviewer has to spot.

*"The contract isn't a separate document; it's derived and verified."* ([[2026-06-08-ai-security-tools]])

## Why it matters with agents

An agent renaming a field in a Java DTO has no reason to remember the TypeScript interface three folders away — and a human reviewer skimming a large diff won't either. Hand-maintained contracts are exactly the kind of parallel truth that [[drift|drifts]]. Turning the contract into a build artefact moves the check from discretion to mechanism, the hard end of the [[enforcement-ladder]].

## The mechanism

1. **Extract** the contract from the code. In Java/Spring: start the app or a test and dump `/v3/api-docs` via [springdoc-openapi](https://springdoc.org/) ([[2026-05-25-ai-government]]), or let a Maven plugin turn the REST annotations into OpenAPI YAML ([[2026-07-23-ai-software-product]]).
2. **Compare** with the committed `openapi.yaml`. Any contract change shows up as a diff in the PR and demands a justification — route it to the API owners ([[codeowners-elders]]) ([[2026-05-25-ai-government]]).
3. **Lint** the generated spec (e.g. Spectral) before generating clients ([[2026-06-08-ai-security-tools]]).
4. **Generate** the TypeScript interfaces from it and **build** the front end (`ng build`). Rename a Java field and the front end no longer compiles.
5. **Wire it into a `pre-push` git hook** (and CI again): extract → generate → build. If the front end doesn't compile, you can't push ([[2026-06-02-ai-open-workshop]], [[2026-06-08-ai-security-tools]]).

## The contract as agent context

Reference the committed `openapi.yaml` from [[agents-md]] instead of listing endpoints by hand; a test regenerates it, so the file the agent reads can't drift from the code ([[2026-07-20-ai-telecom]]). It is the API equivalent of reflected [[deterministic-diagrams]]: generated from the source of truth, never edited.

The same spec can also seed test data — see [[openapi-test-fixtures]].

## See also

- [[openapi-test-fixtures]]
- [[archunit-drift-control]]
- [[drift]]
- [[deterministic-diagrams]]
- [[enforcement-ladder]]
- [[static-analysis-gauntlet]]
- [[codeowners-elders]]
- [[2026-05-25-ai-government]]
