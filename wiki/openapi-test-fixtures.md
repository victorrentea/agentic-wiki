---
title: OpenAPI Test Fixtures
category: pattern
tags: [openapi, testing, kotlin, dto-generation, contract-testing]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-24
updated: 2026-06-24
---

OpenAPI test fixtures is the practice of asking the backend to annotate their swagger with realistic `examples`, then using those examples to generate JSON test fixtures and Kotlin DTOs at build time.

## The workflow

1. Ask the backend team to add `examples` to their OpenAPI/swagger spec — realistic, representative payloads (not just `"string"` placeholders).
2. At build time, a code-generation step extracts those examples and produces:
   - **JSON fixture files** — ready to use in tests as realistic mock responses.
   - **Kotlin DTOs** — type-safe data classes matching the schema.
3. Commit the generated fixtures and DTOs.
4. Add a test that proves they regenerate identically — so if the OpenAPI spec changes, the test breaks and the team regenerates.

## Why it works

The generated fixtures are as realistic as the backend's own examples. The Kotlin DTOs are guaranteed to match the API contract (no manual sync required). A renamed Java field on the backend will break the generated DTO and fail the test — making [[drift]] impossible to ignore. This is the same "make drift a failing test" philosophy as [[archunit-drift-control]].

## Agentic relevance

An agent writing integration tests can use the generated fixtures directly instead of inventing plausible-but-wrong JSON. The fixture generation is mechanical and belongs in a skill or build step — the agent should not hand-write fixtures it could generate.

## See also
- [[drift]]
- [[archunit-drift-control]]
- [[spec-driven-development]]
- [[acceptance-test-bdd]]
- [[feedback-loop]]
- [[2026-06-23-ai-garmin]]
