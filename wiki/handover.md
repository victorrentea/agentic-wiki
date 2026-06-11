---
title: Handover
category: pattern
tags: [context-management, dumb-zone, plan-mode, spec-driven, handoff]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

A handover is a written summary of an agent's conclusions to a file, so the agent can "die and be reborn" reading only that file — escaping the [[dumb-zone]] without losing what it learned.

## The "die and be reborn" move

It is one of the three ways to dodge the dumb zone (alongside lossy compaction and spawning [[sub-agents]]), and the most surgical: rather than dragging a bloated [[context-window]] forward, you distill the decisions into a clean document and restart with only that as context. This is the essence of [[plan-mode]] and the seed of [[spec-driven-development]] — a [[grill-me|grill]] session ends with the instruction *"summarize the Q&A into a handover,"* and that handover is what you paste into [[openspec]] to start a change.

The same principle powers the [[ralph-loop]]: each overnight batch enters with a clean context anchored by the spec. A handover is to a single task what a [[claude-md]] is to a whole project — durable, distilled knowledge that survives a context reset.

## See also
- [[dumb-zone]]
- [[plan-mode]]
- [[spec-driven-development]]
- [[grill-me]]
- [[ralph-loop]]
- [[sub-agents]]
- [[2026-06-11-ai-playtika]]
