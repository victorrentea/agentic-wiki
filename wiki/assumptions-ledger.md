---
title: Assumptions Ledger & Guided Human Review
category: pattern
tags: [code-review, human-in-the-loop, confidence, review-report, attention, sycophancy]
sources: ["[[2026-08-10-ai-consultancy]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-23-ai-software-product]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

The assumptions ledger is a file the coding agent writes while it works — every assumption it took, every decision it made alone, each with a 0–1 confidence — so the human review starts where the agent hesitated instead of reading the diff top to bottom.

## Why

A diff shows *what* changed, never *where the author was guessing*. The most expensive agent failure is the silent one: a requirement quietly not met, an ambiguity resolved by a coin flip and presented with full confidence ([[hallucination]]). Asking a human colleague *"where did you hesitate?"* is normal; with an agent you can make it a contract — instruct it **upfront to log assumptions in a markdown as it goes**, not to reconstruct them at the end ([[2026-07-01-ai-java-community]], [[2026-09-24-ai-bank]]). The question *"what assumptions did you make?"* at the end of a run is the cheap version; the ledger is the systematic one.

## What goes in the report

The agent ends its run with a `review.md` (or a PR description, or PR comments) built from three streams:

1. **Assumptions and solo decisions, with confidence** — sorted **ascending** by confidence (lowest first). E.g. *"assumed sort is server-side because pagination is — 0.6"*.
2. **Reviewer findings the coder rejected**, each with its reason. Read these first: a [[sycophancy|sycophantic]] reviewer invents issues, so the rejections are exactly where a human's judgement is needed.
3. **Fixes that might overfit** — places where the agent made a test or a reviewer go green in a way that may not generalise ([[reward-hacking]]).

Auto-fixed findings go to the end (or straight into PR comments) — they're already handled. One session's shape for the finishing report: *fixed / declined-with-reason / assumptions*, and the human starts from the last two ([[2026-09-18-ai-agentic-how]]).

## How the human uses it

- **Skip assumptions rated >90%.** Spend the ≈45-minute focus window on low-confidence items and on the rejected findings ([[2026-09-28-ai-gaming-tech]]).
- **Before reviewing, ask** what the agent was least confident about, what it assumed, and what the reviewer objected to — **sorted by risk**.
- **The "AI-guided tour":** walk the diff in descending criticality — hot code first ([[bug-heatmap]]), then the coder's low-confidence spots, then findings it waved off as false positives — starting from visuals ([[review-by-images]]) ([[2026-07-23-ai-software-product]]).
- A multi-review skill can end simply by telling the human **which areas to review first** ([[2026-07-03-ai-bank]]).

This is *uncertainty-first* review; it complements the *disagreement-first* ordering in [[code-review]] and [[multi-model-review]]. It also differs from the committed verdict in [[agentic-review-pipeline]]: the verdict records the outcome, the ledger directs human attention *before* the verdict.

## Make the agent pre-chew its proof

The ledger is one piece of a broader move: make the agent hand over its work in the cheapest-to-verify form ([[2026-10-06-gray-factory-devoxx]]):

- a **before/after video of the bug** — approvable while walking the dog (borrowed from OpenAI's harness-engineering experiment);
- the **Playwright trace viewer** as inspectable proof that the scenario really ran;
- the list of **decisions it took alone, with confidence** — e.g. a 60% item in the [human-review demo](https://victorrentea.github.io/human-review/demo/review.html) ([repo](https://github.com/victorrentea/human-review)), a rendered review page that also maps PR files to the tests and spec sentences covering them, and posts the assumptions as PR comments ([[human-review-dashboard]]);
- a **running screen the business can criticise** — one session spent $17 of tokens instead of a two-day estimate; new feature ideas raised there get **parked, not built**.

It pairs naturally with spawning the reviewer from the implementer for one round only ([[agentic-review-pipeline]]), and with a human-review budget that shrinks over time on the way to a [[gray-factory]].

## See also
- [[code-review]]
- [[multi-model-review]]
- [[agentic-review-pipeline]]
- [[human-review-dashboard]]
- [[review-by-images]]
- [[sycophancy]]
- [[hallucination]]
- [[reward-hacking]]
- [[gray-factory]]
