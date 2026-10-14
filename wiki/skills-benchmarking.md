---
title: Skills Benchmarking
category: pattern
tags: [skills, evaluation, statistics, llm-judge, quality-assurance]
sources: ["[[2026-05-11-ai-software-vendor]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-23-ai-device-maker]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-10-testing-ai-bank]]"]
created: 2026-06-24
updated: 2026-10-13
---

Skills benchmarking is the practice of running the same task repeatedly with and without a skill, scoring the results statistically, to prove whether the skill improves outcomes before adopting it.

## Why statistics matter

LLMs are stochastic — a single run proves nothing. A skill that looks helpful in one run may hurt in the next. The only valid claim that a skill "works" comes from running the task ≈10 times with the skill and ≈10 times without it, then comparing the distribution of quality scores and token costs. *"Have you been to school? Do you remember statistics?"* <span style="color:red">Or: *"the only way to prove something in a random world is repeated study until the Gauss curve sharpens"* ([[2026-05-11-ai-software-vendor]]).</span>

<span style="color:red">**A new team role.** The prediction is that by ≈2027 AI teams will have a **statistics function** — an "AI researcher" who knows Gaussians and standard deviations, "because we work with random things" — A/B-testing CLAUDE.md and skill variants: same task ×10 on the same model, measuring usage and outcome ([[2026-05-11-ai-software-vendor]], [[2026-06-05-ai-consultancy]]).</span>

## LLM-as-judge

Hand-reviewing 20 outputs is expensive. The practical shortcut: use a third model as a judge — score each output against a written rubric, then validate the rubric itself once. This turns the evaluation into a scripted pipeline rather than a human review cycle. See [[judge-llm]] for the underlying pattern.

<span style="color:red">The general form is [[agent-evals]]: run the agent repeatedly, verify programmatically whatever can be scripted, and keep an LLM judge only for free text ([[2026-09-10-testing-ai-bank]]).</span>

## Token cost as a second axis

A skill that improves quality by 5% but triples token burn is usually not worth it. Track both axes: quality score and token burn per run. A skill that improves quality *and* reduces burn (by focusing context) is the ideal case.

## What an evaluation costs

<span style="color:red">A proper evaluation of one skill: ≈5 task types, each run with and without the skill, measuring token burn plus a quality score you judge yourself — about 1–2 hours and ≈1M tokens ([[2026-06-05-ai-consultancy]]). Sorting a marketplace by likes is only shortlisting, not evidence.</span>

<span style="color:red">Treat `.md` changes like code changes — **run evals on them the way you run tests on code** — but a good eval run can cost hundreds of dollars, so gate it with trigger logic (run only when the skill or CLAUDE.md actually changed) ([[2026-08-31-ai-software-vendor]]).</span>

## When a skill earns its place

A skill earns adoption only when:
1. You have hand-held the agent through the same task several times and expect to repeat it.
2. Statistical benchmarking shows a reliable quality improvement.
3. The trigger `description` is precise enough that the skill fires when intended and not otherwise (see [[agent-skill]]).

<span style="color:red">**A famous skill you don't understand is a learning source, not an install.** Take a vendor's best-practices skill (e.g. one for Next.js), review your code against it, mob on it like a course — then write your own "in your own image" ([[2026-06-05-ai-consultancy]]; see [[skills-governance]]).</span>

## See also
- [[agent-skill]]
- [[judge-llm]]
- [[skills-governance]]
- [[token-economy]]
- <span style="color:red">[[agent-evals]]</span>
- <span style="color:red">[[skill-marketplace-security]]</span>
- [[2026-06-23-ai-device-maker]]
- <span style="color:red">[[2026-05-11-ai-software-vendor]]</span>
- <span style="color:red">[[2026-06-05-ai-consultancy]]</span>
- <span style="color:red">[[2026-08-31-ai-software-vendor]]</span>
- <span style="color:red">[[2026-09-10-testing-ai-bank]]</span>
