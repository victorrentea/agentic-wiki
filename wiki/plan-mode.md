---
title: Plan Mode
category: pattern
tags: [planning, claude-code, deep-thinking, context-management, single-threaded]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---


Plan mode (Shift-Tab in Claude Code) is a read-only thinking phase where the agent brainstorms and designs an approach *before* touching any code — single-threaded deep thinking that must not be parallelized.

## Single-threaded by design

The hard rule: **don't open a second terminal while in plan mode.** Humans cannot run two deep-thinking tasks in parallel — one terminal does the hard thinking; the others may do shallow polishing at most. (Napoleon could famously do three things at once on his horse — none of them deep thinking.) Violating this is the gateway to the [[ai-addiction]] dopamine trap of four idle terminals draining tokens and attention.

## Where it fits

Plan mode is the middle tier of the ways to develop with an agent:

1. **Just prompt** — a few hours of work.
2. **Plan mode** — up to a day; brainstorm with [[grill-me]] or Superpowers.
3. **Jihad brainstorm** — a topic you've never touched or that is very vague.
4. **[[spec-driven-development]]** — the longest, a day to a sprint.

The output of a plan-mode session is a [[handover]] — the distilled conclusions you restart from, keeping the agent out of the [[dumb-zone]]. Pair it with [[prompt-caching]] awareness: deep thinking is slow, so don't let an idle planning terminal blow past the 5-minute cache window.

## See also
- [[grill-me]]
- [[handover]]
- [[spec-driven-development]]
- [[dumb-zone]]
- [[ai-addiction]]
- [[prompt-caching]]
- [[2026-06-11-ai-playtika]]
