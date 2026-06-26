---
title: Data Quality Validator
category: pattern
tags: [testing, data-quality, observability, validator, feedback-loop, kafka]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-25
updated: 2026-06-25
---

A pattern that replaces subjective agent eyeballing with a programmatic, deterministic validator to audit data quality — replacing "does this look right?" with an executable assertion.

## The core principle

Any time you would ask the agent "does this look right?", replace that question with a programmatic assertion. Subjective agent judgment on data quality produces slop: inconsistent, unrepeatable, and silently wrong in edge cases.

## The pattern

1. **Feed a known structured input** (e.g., a Kafka message, a CSV row, a JSON payload with known values).
2. **Assert the output** against explicit data-quality rules: field presence, type correctness, value ranges, referential integrity, naming consistency.
3. The validator is deterministic and repeatable — run it on every deploy, not just once.

This gives a trustworthy oracle that the [[feedback-loop]] can rely on, rather than an agent that may answer differently each run.

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
