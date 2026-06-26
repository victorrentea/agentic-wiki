---
title: Dumb Zone
category: concept
tags: [context-window, lost-in-the-middle, performance, compaction, recall]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-24
---

The dumb zone (also called "context rot") is the region of a [[context-window]] — roughly past **≈55–65% full** — where a model becomes "lost in the middle" and its reasoning quality visibly degrades, even on frontier models.

## Where it kicks in

The threshold is a fraction of the window, not an absolute token count, so it scales with the model:

- **≈130k tokens** for Sonnet-class models.
- **≈300–400k tokens** for Opus, which is far stronger on long horizons ("impeccable under 300k").

A **1M-token window does not mean 1M-token recall**: the ability to recall a specific token sitting around the half-million mark can drop to ≈50%. Paying for a bigger window buys *room you should mostly leave empty*, not reliable memory — the "lost in the middle" effect is documented (Liu et al., 2023; [arxiv 2307.03172](https://arxiv.org/abs/2307.03172)).

Treat the 65% figure as a **rule of thumb, not a published number** — actual degradation depends on the model, the task, and how *structured* the context is. A team that logs error-rate vs. context-fill % gets its own real curve instead of a guess. The operational shorthand for *why* it matters: this is *"where AI slop happens, AI forgets, AI drops prod databases."*

## Why it bites

A full window is one of the five root causes of [[hallucination]]: a model that can no longer attend to the middle of its context silently fills gaps — claiming all tests passed when they didn't, hallucinating that an API exists.

**Compaction is a failure mode, not a feature.** When the harness hits ≈90% and summarizes history into a blurry one-page CV, the decisions the model made *just before* compacting were already made inside the dumb zone, so the summary preserves bad reasoning. The "blue part" (system prompt + CLAUDE.md + skill front-matter + tools) is never compacted anyway — compaction only summarizes the conversation, lossily. Replace it with an explicit [[handover]]: have the agent write a `plan.md`, review it yourself to confirm nothing critical dropped, then `/clear` and resume from the plan. Good agentic engineers are never compacted.

## Staying out of it

Three escapes, in order of preference:

1. **[[handover]]** — write your conclusions to a file and "die and be reborn" reading only that file (the essence of [[plan-mode]] and [[spec-driven-development]]).
2. **[[sub-agents]]** — delegate research to fresh-context workers that report back a few compact paragraphs.
3. **Compact** — the lossy last resort.

This is the discipline behind [[token-economy]] and the [[ralph-loop]]: keep every task inside the model's comfort zone. Past ≈600k Opus even develops "context anxiety" — see [[vibe-coding]].

## See also
- [[context-window]]
- [[hallucination]]
- [[handover]]
- [[sub-agents]]
- [[token-economy]]
- [[ralph-loop]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
