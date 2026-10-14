---
title: Data Quality Validator
category: pattern
tags: [testing, data-quality, observability, validator, feedback-loop, kafka]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-09-10-testing-ai-bank]]"]
created: 2026-06-25
updated: 2026-10-13
---

A pattern that replaces subjective agent eyeballing with a programmatic, deterministic validator to audit data quality — replacing "does this look right?" with an executable assertion.

## The core principle

Any time you would ask the agent "does this look right?", replace that question with a programmatic assertion. Subjective agent judgment on data quality produces slop: inconsistent, unrepeatable, and silently wrong in edge cases.

## The pattern

1. **Feed a known structured input** (e.g., a Kafka message, a CSV row, a JSON payload with known values).
2. **Assert the output** against explicit data-quality rules: field presence, type correctness, value ranges, referential integrity, naming consistency.
3. The validator is deterministic and repeatable — run it on every deploy, not just once.

This gives a trustworthy oracle that the [[feedback-loop]] can rely on, rather than an agent that may answer differently each run.

## <span style="color:red">Keep the oracle independent of the code</span>

<span style="color:red">When an agent writes the checks for a data pipeline, ask it to verify outputs against the **formulas in the ticket**, not against the code that produced them — an oracle derived from the implementation just re-confirms its bugs ([[2026-09-10-testing-ai-bank]]).</span> <span style="color:red">A workflow that works:</span>

1. <span style="color:red">Put the Jira stories and `.docx` specs in the agent's folder as files — they are the source of truth for the expected values.</span>
2. <span style="color:red">Clone the pipeline repo into a temp folder only to learn *where* the output data lands, not how it is computed.</span>
3. <span style="color:red">Start with **one data record**: input → expected output, with every calculation step explained, so you can correct the agent's reasoning before scaling to a whole dataset.</span>

<span style="color:red">The same rule — script every check that can be scripted, keep an LLM judge for free text only — is the core of [[agent-evals]].</span>

## Latency metric companion

Pair the correctness validator with a **latency metric** that measures the time between a row's production timestamp and when it lands in the output table. This surfaces slow incremental merges before they become production incidents.

The discipline from [[optimize-then-measure-subset]] applies: measure the latency baseline first, then optimize. Do not optimize a pipeline whose latency you have not yet quantified.

## Relation to [[observability-mcp]]

[[observability-mcp]] instruments the running system; the data quality validator instruments the *data*. They are complementary: observability tells you the pipeline ran; the validator tells you the pipeline ran *correctly*.

## When subjective review is unavoidable

If the domain requires judgment (e.g., "is this product description accurate?"), use an LLM-as-judge with a rubric — a structured prompt with explicit criteria that produces a score, not a free-text opinion. Even then, validate the judge's output against known-good and known-bad examples to calibrate it. See [[reward-hacking]] for why uncalibrated LLM judges drift.

## Pitfalls

- Writing a validator that only checks field presence, not values — a field being non-null is not the same as being correct.
- Running the validator only on happy-path test data; inject known-bad rows to confirm the validator catches them ([[acceptance-test-bdd]] mutation analogy).
- Not versioning the validator alongside the schema — a schema change that doesn't update the validator silently breaks the quality gate.

## See also

- [[feedback-loop]]
- [[observability-mcp]]
- [[optimize-then-measure-subset]]
- [[reward-hacking]]
- [[acceptance-test-bdd]]
- <span style="color:red">[[agent-evals]]</span>
- <span style="color:red">[[judge-llm]]</span>
- <span style="color:red">[[2026-09-10-testing-ai-bank]]</span>
