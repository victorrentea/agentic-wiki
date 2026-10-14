---
title: Handover
category: pattern
tags: [context-management, dumb-zone, plan-mode, spec-driven, handoff]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-06-ai-bank]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]"]
created: 2026-06-11
updated: 2026-10-13
---

A handover is a written summary of an agent's conclusions to a file, so the agent can "die and be reborn" reading only that file — escaping the [[dumb-zone]] without losing what it learned.

## The "die and be reborn" move

It is one of the three ways to dodge the dumb zone (alongside lossy compaction and spawning [[sub-agents]]), and the most surgical: rather than dragging a bloated [[context-window]] forward, you distill the decisions into a clean document and restart with only that as context. This is the essence of [[plan-mode]] and the seed of [[spec-driven-development]] — a [[grill-me|grill]] session ends with the instruction *"summarize the Q&A into a handover,"* and that handover is what you paste into [[openspec]] to start a change.

**Compaction is the inferior alternative.** Auto-compaction is a roulette — you don't know what survives. The "blue part" (system prompt + CLAUDE.md + skill front-matter + tools) is never compacted anyway; compaction only summarizes the conversation lossily. A manual handover — ask the agent to write `plan.md`, review it yourself to confirm nothing critical dropped, `/clear`, resume from the plan — is always preferable. <span style="color:red">"Good engineers are never auto-compacted." A ready-made version is Matt Pocock's [handoff skill](https://github.com/mattpocock/skills/blob/main/skills/productivity/handoff/SKILL.md).</span> See [[dumb-zone]] for why compaction can preserve bad reasoning made just before the context filled.

<span style="color:red">**When:** while the model is still lucid — sessions put it at ≈50% of the window, or ≈100–150k tokens in absolute terms. Needing a handover much earlier means you mis-scoped the task. If you know up front that a task is big, tell the agent so it **spawns [[sub-agents]]** — better than any `/compact`.</span>

<span style="color:red">**Why a file and not "its memory":** a session claimed thinking blocks are discarded at the end of every turn, so the written report is the agent's only surviving memory of its reasoning. That was true only for pre-Opus-4.5 models; current Claude models keep prior turns' thinking blocks in context and bill them as input ([docs](https://platform.claude.com/docs/en/build-with-claude/thinking#thinking-block-preservation-by-model)). Either way, nothing in the window survives a `/clear` — only the file does.</span>

The same principle powers the [[ralph-loop]]: each overnight batch enters with a clean context anchored by the spec. A handover is to a single task what a [[claude-md]] is to a whole project — durable, distilled knowledge that survives a context reset.

## <span style="color:red">Chaining tools, models and people</span>

<span style="color:red">Whenever you want to jump tools or models mid-task, **write the state down** — conversations are transient and private, markdown is shared and persistent. Example: conversation 1 runs a brainstorming skill (one question at a time) → *"synthesize the Q&A into `handover.md` at the project root"* → conversation 2: `/opsx:new` *"apply the answers in handover.md to the proposal"* ([[openspec]]) → delete `handover.md`.</span>

<span style="color:red">The same file hands work between colleagues: **markdown plans committed to git** let a lead plan and a junior with spare quota implement. Think of `architecture.md` as the map and plan markdowns as the GPS trail of one change; delete plan files after ≈3 months, folding the durable decisions into `architecture.md` (see [[drift]]).</span>

## <span style="color:red">Getting back into yesterday's work</span>

<span style="color:red">Three options: **resume** the conversation, **`/compact`** it tonight (fine when only polish remains), or start a fresh agent on a **handover markdown** — the cheapest. Ending a hard day by having the agent write its "markdown will" (decisions + learnings) and re-reading it with fresh eyes next morning works well.</span>

- <span style="color:red">**Resume goes stale.** A resumed conversation believes files it read 18 hours ago still have that content. If files changed meanwhile, start a new conversation. Note that the resume picker lists conversations **per folder, not per branch** ([docs](https://code.claude.com/docs/en/sessions#where-the-session-picker-looks)). Needing resume at all signals code that doesn't document itself.</span>
- <span style="color:red">**Rewind** (double-`Esc` in Claude Code, the fork icon in Copilot) rolls the conversation back to an earlier turn, including file state. Victor dislikes it as "too patched": after fixing the context, `/clear` and **re-run the exact same prompt** until it comes out impeccable in one shot — "human reinforcement learning; you need parenting skills." Sometimes the requirement was bad, not the AI; then fix the ticket with the business.</span>
- <span style="color:red">**Counterpoint for 1M windows:** with a 1M-context Opus, one session argued for keeping one conversation from spec to code, since fragmenting forces you to offload everything to files. Review still always runs in a fresh conversation.</span>

<span style="color:red">Bookkeeping can be automated with `/loop`: `/loop 30m /memory` → `.planning/MEMORY.md`, `/loop 1h /handoff` → `.planning/HANDOFF.md`. One session also showed `/loop 20m /compact`, which contradicts the "never auto-compact" stance above.</span>

## Handover before derailing

When an agent has clearly gone off the rails — not just filled context but actively lost the thread — don't silently `/clear` it. Ask it to write a **HANDOVER / post-mortem markdown** of what it learned before you end the session: Goal · What I tried · What worked · Dead ends · Current state · Next step · Open questions · Files touched. This ensures the next agent ingests it deterministically rather than re-discovering dead ends.

**Context budget discipline:** Keep an orchestrator's context to a few hundred K tokens. A background sub-agent that finishes after ≈50 minutes returns to an expired orchestrator context and "burns you." Sub-agents must report back concisely so they don't flood the boss. Set a context-budget alarm that auto-emits the handover template past ≈50–60% fill.

## See also
- [[dumb-zone]]
- [[plan-mode]]
- [[spec-driven-development]]
- [[grill-me]]
- [[ralph-loop]]
- [[sub-agents]]
- [[orchestrator-catches-lying-subagent]]
- [[openspec]]
- [[drift]]
- [[headless-claude]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
