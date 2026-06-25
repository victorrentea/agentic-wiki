---
title: Skills Benchmarking
category: pattern
tags: [skills, evaluation, statistics, llm-judge, quality-assurance]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-24
updated: 2026-06-24
---

Skills benchmarking is the practice of running the same task repeatedly with and without a skill, scoring the results statistically, to prove whether the skill improves outcomes before adopting it.

## Why statistics matter

LLMs are stochastic — a single run proves nothing. A skill that looks helpful in one run may hurt in the next. The only valid claim that a skill "works" comes from running the task ≈10 times with the skill and ≈10 times without it, then comparing the distribution of quality scores and token costs. *"Have you been to school? Do you remember statistics?"*

## LLM-as-judge

Hand-reviewing 20 outputs is expensive. The practical shortcut: use a third model as a judge — score each output against a written rubric, then validate the rubric itself once. This turns the evaluation into a scripted pipeline rather than a human review cycle. See [[judge-llm]] for the underlying pattern.

## Token cost as a second axis

A skill that improves quality by 5% but triples token burn is usually not worth it. Track both axes: quality score and token burn per run. A skill that improves quality *and* reduces burn (by focusing context) is the ideal case.

## When a skill earns its place

A skill earns adoption only when:
1. You have hand-held the agent through the same task several times and expect to repeat it.
2. Statistical benchmarking shows a reliable quality improvement.
3. The trigger `description` is precise enough that the skill fires when intended and not otherwise (see [[agent-skill]]).

## See also
- [[agent-skill]]
- [[judge-llm]]
- [[skills-governance]]
- [[token-economy]]
- [[2026-06-23-ai-garmin]]
