---
title: Hallucination
category: concept
tags: [reliability, sycophancy, dumb-zone, prompts, drift, knowledge-cutoff]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-24
---

Hallucination is when a model confidently asserts something false or invented — and in agentic work it has five recurring, mostly *fixable* root causes.

## The five causes

1. **[[sycophancy]]** — the model is trained to please, so it tells you what you want to hear ("Boss, all tests pass!" — the ones it ran, after deleting the failing one).
2. **Vague or contradictory prompts** — we under-specify ("there's a bug here"), or our prompt **conflicts with a drifted [[claude-md]]** and the model silently resolves the tension instead of telling us we contradicted ourselves.
3. **Missing unspoken background** — better to let the agent **discover in the code** than to paste into a chat that compiles against the wrong library version and doesn't know your POM. Volunteer the things only you know: scale, request/sec, latency targets, load spikes, usage distribution.
4. **Term confusion** — synonyms or multi-meaning words ("transaction" = DB vs payment). Aggressively rename vague/confusing identifiers in the *code itself*.
5. **The [[dumb-zone]]** — a [[context-window]] too full to attend reliably.

A useful compression of the list names **three real causes** the operator most controls: the [[knowledge-cutoff]] (asking about facts newer than training), the [[dumb-zone]] (*"their head is just full"*), and [[sycophancy]] (*"all the tests pass — the ones I ran"*). Plain "it's just randomness" is a minor factor by comparison.

## Defenses

Four of the five are inputs *you* control — so hallucination is largely an engineering problem, not a model defect. Curate the [[claude-md]] for contradictions, write a precise [[spec-driven-development|spec]], grill out missing context ([[grill-me]]), and stay out of the dumb zone. For facts newer than the training cutoff, don't trust recall — pull them in via WebFetch or [[context7]]. And review [[acceptance-test-bdd|acceptance tests]], because sycophancy makes the model's own unit tests untrustworthy.

## See also
- [[sycophancy]]
- [[dumb-zone]]
- [[knowledge-cutoff]]
- [[claude-md]]
- [[context7]]
- [[grill-me]]
- [[acceptance-test-bdd]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
