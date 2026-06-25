---
title: Plan Mode
category: pattern
tags: [planning, claude-code, deep-thinking, context-management, single-threaded]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-24
---


Plan mode (Shift-Tab in Claude Code) is a read-only thinking phase where the agent brainstorms and designs an approach *before* touching any code — single-threaded deep thinking that must not be parallelized.

## Single-threaded by design

The hard rule: **don't open a second terminal while in plan mode.** Humans cannot run two deep-thinking tasks in parallel — one terminal does the hard thinking; the others may do shallow polishing at most. (Napoleon could famously do three things at once on his horse — none of them deep thinking.) Violating this is the gateway to the [[ai-addiction]] dopamine trap of four idle terminals draining tokens and attention.

## Where it fits

Pick the approach by task size:

| Task size | Approach |
|---|---|
| Trivial change (you know the files) | Just prompt directly |
| Up to a day | **Plan mode** (Shift-Tab Shift-Tab in Claude Code) |
| Multi-day | [[spec-driven-development]] with a markdown spec |

Plan mode is the middle tier — Shift-Tab Shift-Tab activates it in Claude Code. Its output is a [[handover]]; the distilled conclusions you restart from.

The output of a plan-mode session is a [[handover]] — the distilled conclusions you restart from, keeping the agent out of the [[dumb-zone]]. Pair it with [[prompt-caching]] awareness: deep thinking is slow, so don't let an idle planning terminal blow past the 5-minute cache window.

This three-tier framing came from a concrete observation: plan mode is *"too ephemeral"* for multi-day work — you can't share it, hand it off, or review it asynchronously. [[spec-driven-development]] with [[openspec]] is the persistent, reviewable answer for the third tier. The mapping also clarifies when *not* to use plan mode: if a task fits in a few hours and you know the files, just prompt directly and save the planning overhead.

## See also
- [[grill-me]]
- [[handover]]
- [[spec-driven-development]]
- [[dumb-zone]]
- [[ai-addiction]]
- [[prompt-caching]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
