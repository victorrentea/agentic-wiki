---
title: Semantic Coverage
category: pattern
tags: [testing, coverage, code-review, requirements, spec-driven, traceability]
sources: ["[[2026-09-04-ai-software-company]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Semantic coverage maps every test that executed a PR's changed code back to the requirement (spec sentence) it verifies, and grades how well each line is actually checked — so a requirement with no test, or code that runs but is never asserted, is visible at a glance without reading the code.

## Why line coverage isn't enough

Classic coverage answers "did this line run?". In a [[gray-factory]], where humans read only a sample of the AI's code, the reviewer needs a different answer: *"is every requirement proven by a test I can trust?"* Agents happily write tests that execute code without asserting anything meaningful — coverage goes green, nothing is verified (a cousin of [[reward-hacking]]).

## Three shades of coverage

Colour-code each statement as **fully / partially / not covered**, where **partially = executed but never asserted** ([[2026-09-04-ai-software-company]]). One step further, the AI **estimates how well each line is covered**, not just whether it ran ([[2026-10-01-ai-bank]]). Red gaps turn into a one-line review comment: *"one more test, please."*

Navigation is bidirectional: click a statement → the tests that touch it; click a test → every statement it covers ([[2026-09-04-ai-software-company]], [[2026-10-01-ai-bank]]). The evidence is inlined — the Gherkin scenario plus the glue code beneath — so the reviewer never opens the IDE.

## Requirement ↔ test mapping

- **Inputs**: the requirements in the change's [[openspec]] `spec.md`, plus per-test coverage of the PR's files, collected by coverage agents for Java and TypeScript ([[2026-09-30-ai-enterprise]]).
- **Output**: a requirement ↔ tests table in which the **AI flags uncovered spec sentences** — "map every test through changed code to the spec sentence it covers" ([[2026-09-28-ai-gaming-tech]]).
- **Prefer tests close to requirements** — Gherkin acceptance tests ([[acceptance-test-bdd]]), API and end-to-end tests. Implementation-coupled unit tests add little here; review "with your eyes on tests near the requirements" ([[2026-09-30-ai-enterprise]]).

## Companions in the review report

Semantic coverage is one panel of a reviewable PR report, rendered for example by [human-review](https://github.com/victorrentea/human-review) (see [[human-review-dashboard]]). Alongside it ([[2026-09-30-ai-enterprise]]):

- a Playwright Trace Viewer link per UI test — check the trace, not the code;
- a sequence diagram per test, drawn from OpenTelemetry traces (see [[field-reality-diagram]]);
- visual-regression proof of which screens changed ([[review-by-images]]);
- the decisions the agent took alone ([[assumptions-ledger]]).

## See also

- [[acceptance-test-bdd]]
- [[spec-driven-development]]
- [[human-review-dashboard]]
- [[review-by-images]]
- [[field-reality-diagram]]
- [[assumptions-ledger]]
- [[mutation-testing]]
- [[code-review]]
- [[gray-factory]]
- [[2026-09-30-ai-enterprise]]
