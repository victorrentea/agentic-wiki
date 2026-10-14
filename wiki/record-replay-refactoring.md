---
title: Record-Replay Refactoring
category: pattern
tags: [legacy, refactoring, testing, characterization-tests, golden-master, production-data]
sources: ["[[2026-05-06-ai-ecommerce]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Record-replay refactoring captures the real production inputs and outputs of a component, then replays them against the refactored (often AI-rewritten) version and diffs the results — an automatic characterization test for code nobody fully understands.

## Why it matters for agents

An agent rewriting legacy code needs an **oracle**: something that says "same behaviour" or "different". Where unit tests don't exist, recorded production traffic is that oracle — the agent iterates until replay shows no divergence. It is stage 1 of [[legacy-migration-stages]] and the strongest form of [[feedback-loop]] you can give a rewrite.

## The prompt

From [[2026-04-27-ai-healthcare-software]]:

> *"Generate code I can deploy in production next to my app that records every input request and every output (SQL, API calls, results) to a file; I'll bring it to dev, anonymize it, and you'll write the replay harness so I can run my refactored version against captured production behaviour. You will never run in production yourself."*

Only AI-*written* code runs in production; the agent never does. **Anonymization is itself an LLM task** — deterministic pseudonyms that preserve referential integrity — spot-checked by a human before the data leaves prod ([[data-governance]]).

Combine recordings with **fuzzy tests** (*"looking at the data in my staging DB and the Jira tickets for this API, generate 200 reasonable test cases"*) into a "monster harness" around the legacy code, then refactor — and check that the harness has teeth with [[mutation-testing]].

## War story

A 35,000-line invoice-generator class with 28-parameter methods was blocking a business. The team recorded a **year** of production inputs/outputs, rebuilt the class as 28 classes behind a feature flag, replayed the year against the new version, and cut over once **6 months of replay showed no divergence** ([[2026-05-13-ai-it-services]]).

## Tooling

- [BitDive](https://bitdive.io/) (paid, Java/Spring) records method-level production traces via bytecode instrumentation — which method called which, with what parameters — so AI can replay them in tests and diff-debug. Side benefit: methods never executed in months flag likely dead code ([[2026-05-11-ai-software-vendor]]).
- GitHub's [Scientist](https://github.com/github/scientist) is the classic library form ("scientific refactoring"): run old and new paths side by side in production and report mismatches.
- **Golden Master** is the in-repo cousin, for when recording prod isn't possible: before taking an axe to a 5,000-line class, copy the old code and write many tests comparing old vs new output on observable behaviour (ideal for HTML or final responses). 100% coverage isn't enough — add [[mutation-testing]] ([[2026-06-05-ai-consultancy]]).

## See also
- [[legacy-migration-stages]]
- [[acceptance-test-bdd]]
- [[feedback-loop]]
- [[mutation-testing]]
- [[field-reality-diagram]]
- [[profiling-agents]]
- [[openrewrite]]
- [[2026-05-13-ai-it-services]]
