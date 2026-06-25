---
title: ArchUnit + Reflected-Diagram Drift Control
category: pattern
tags: [drift, archunit, plantuml, diagrams, tests, no-drift, architecture]
sources: ["[[2026-06-23-ai-garmin]]", "[[2026-06-22-ai-kambi]]"]
created: 2026-06-23
updated: 2026-06-25
---


A drift-control pattern that makes architecture diagrams and package rules *self-verifying*: unit-test package dependencies with ArchUnit, and reflect the class diagram out of the code at test time, so a stale diagram fails the build instead of quietly lying.

## The problem it fixes

AI-generated `.puml` (PlantUML) diagrams [[drift]] from the code **the moment they're written** — the AI draws what it intended, not what shipped, and nobody re-draws them after the next change. A hand-drawn diagram is worse than none because it is confidently wrong.

## Two complementary mechanisms

- **ArchUnit** — express allowed package dependencies as a unit test (e.g. "the networking layer must not depend on the UI layer"). The rule is code; violations fail CI. Reuse hunting and layering intent become executable.
- **Reflected class diagram** — generate the `.puml` *from the compiled code* at test time and assert it matches a checked-in expectation. Delete a line from the `.puml` and the test fails; remove a DTO field and the diagram regenerates without it. The diagram becomes a fixture that can't go stale.

## Why it matters for AI slop

A bird's-eye reflected diagram is a fast way to **hunt AI-generated slop** in a vibe-coded app — *"I found the shit!"* — because the structure the agent actually produced is laid bare, not the structure it claimed. This is the static-structure cousin of the [[field-reality-diagram]] (which reflects *runtime* behaviour from traces): both make drift structurally impossible by regenerating from the source of truth instead of maintaining a parallel artifact by hand.

It is also a [[feedback-loop]] for review: the diagram and the ArchUnit tests are an oracle the agent can be pointed at, and a guard against [[cognitive-debt]] in code humans no longer fully read.

## Review-by-images and the pre-push hook

<span style="color:red">The natural extension: a `pre-push` hook that parses a SQL schema change, regenerates an ER diagram with new/changed entities **highlighted in red**, and commits the rendered picture beside the SQL change — so a reviewer reads a picture, not a raw diff. The same pattern applies to class diagrams and dependency graphs. Human reviewers should look at images *first*: "how did my model change? My schema? What's red?" — this optimizes human brain bandwidth for large PRs.

See [[review-by-images]] for the broader visual-review pattern.</span>

## See also
- [[drift]]
- [[field-reality-diagram]]
- [[feedback-loop]]
- [[cognitive-debt]]
- [[static-analysis-gauntlet]]
- [[code-review]]
- [[review-by-images]]
- [[2026-06-23-ai-garmin]]
- [[2026-06-22-ai-kambi]]
