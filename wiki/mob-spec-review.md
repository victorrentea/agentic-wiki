---
title: Mob Spec Review
category: pattern
tags: [spec-driven, code-review, pair-programming, fatigue, decision-making]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-25
updated: 2026-06-25
---

A practice of reviewing architecture or spec documents with two or more humans present simultaneously, rather than asynchronously, to prevent fatigue-driven false approvals and anchor shared decisions.

## Overview

No architecture markdown or [[spec-driven-development|spec]] is reviewed in isolation — always 2+ humans together (mob). A 30-minute joint session condenses decisions that would otherwise scatter across a week of async back-and-forth.

The pattern defuses the deadliest phrase in AI review: *"looks good to me"* (fatigue-LGTM) followed later by *"wait, who decided this?"*. When reviewers work sequentially and asynchronously, it is easy for each person to assume someone else validated the hard parts.

## Mechanics

- Reviewers alternate reading in ≈10-minute chunks. When one person's brain fatigues, the other takes over — stop when both are done.
- If a concept won't click after re-reading, have the agent explain it visually: a diagram, a small runnable experiment, or an animation.
- Pair with [[multi-model-review]]: run automated agents first (see [[agentic-review-pipeline]]), then mob the residual issues together.
- The [[three-amigos]] ritual (BA + QA + Dev) is the closest classical analogue.

## When to invoke

- Any spec, ADR, or architecture markdown that will shape more than one sprint's worth of work.
- Decisions that involve [[skill-erosion]] risk — cases where rubber-stamping by an AI-fatigued human could quietly lower the quality bar.
- Any document longer than can be held in one person's working memory.

## Pitfalls

- Scheduling mob reviews too late (after the agent has already written code against the spec) removes leverage.
- Mob reviews of raw AI output instead of distilled specs waste the session on implementation noise.

## See also

- [[spec-driven-development]]
- [[code-review]]
- [[multi-model-review]]
- [[skill-erosion]]
- [[three-amigos]]
- [[agentic-review-pipeline]]
