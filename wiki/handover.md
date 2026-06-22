---
title: Handover
category: pattern
tags: [context-management, dumb-zone, plan-mode, spec-driven, handoff]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]"]
created: 2026-06-11
updated: 2026-06-22
---


A handover is a written summary of an agent's conclusions to a file, so the agent can "die and be reborn" reading only that file — escaping the [[dumb-zone]] without losing what it learned.

## The "die and be reborn" move

It is one of the three ways to dodge the dumb zone (alongside lossy compaction and spawning [[sub-agents]]), and the most surgical: rather than dragging a bloated [[context-window]] forward, you distill the decisions into a clean document and restart with only that as context. This is the essence of [[plan-mode]] and the seed of [[spec-driven-development]] — a [[grill-me|grill]] session ends with the instruction *"summarize the Q&A into a handover,"* and that handover is what you paste into [[openspec]] to start a change.

<span style="color:red">**Compaction is the inferior alternative.** Auto-compaction is a roulette — you don't know what survives. The "blue part" (system prompt + CLAUDE.md + skill front-matter + tools) is never compacted anyway; compaction only summarizes the conversation lossily. A manual handover — ask the agent to write `plan.md`, review it yourself to confirm nothing critical dropped, `/clear`, resume from the plan — is always preferable. See [[dumb-zone]] for why compaction can preserve bad reasoning made just before the context filled.</span>

The same principle powers the [[ralph-loop]]: each overnight batch enters with a clean context anchored by the spec. A handover is to a single task what a [[claude-md]] is to a whole project — durable, distilled knowledge that survives a context reset.

## See also
- [[dumb-zone]]
- [[plan-mode]]
- [[spec-driven-development]]
- [[grill-me]]
- [[ralph-loop]]
- [[sub-agents]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
