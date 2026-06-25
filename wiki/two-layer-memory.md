---
title: Two-Layer Memory
category: concept
tags: [claude-md, memory, agents-md, communication-style, context-engineering]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-25
updated: 2026-06-25
new_in: "Kambi Day 2 (2026-06-25)"
---

<span style="color:red">🆕 New in Kambi Day 2 (2026-06-25)</span>

The deliberate split of agent memory into a personal home scope (communication style and challenge preferences) and a project scope (domain rules and NFRs), so neither layer bleeds into the other.

## The two layers

| Layer | Location | Owned by | Content |
|---|---|---|---|
| **Home memory** | `\~/.claude/memory.md` (user scope) | Individual | Who you are, tone, "challenge me when I'm vague," language preference, anti-sycophancy rules |
| **Project memory** | `CLAUDE.md` (project scope) | Team | Domain rules, NFRs, architecture constraints, naming conventions |

See [[claude-md]] for the full CLAUDE.md spec and [[agents-md-discipline]] for the operational discipline around it.

## Why the split matters

Without an explicit home memory, the agent inherits default communication style — often verbose, over-apologetic, and prone to [[sycophancy]]. Defining personal style once in home memory means every new session starts calibrated without re-prompting.

The project `CLAUDE.md` carries team-owned rules that every contributor (human or agent) must follow. Mixing personal taste into `CLAUDE.md` causes friction when teammates have different preferences.

## What belongs where

**Home memory** is deliberately selfish:
- "Always challenge me when my requirement is vague."
- "Be concise — no preamble, no summary of what you're about to do."
- "Match my language (English or French per session)."
- "Never start a response with 'Certainly!' or 'Great question!'"

**Project CLAUDE.md** is team-owned:
- Bounded-context naming rules.
- Which tests must stay green before pushing.
- Approved libraries and banned patterns.
- NFRs (latency, security, data-retention policy).

## Pitfalls

- Putting personal tone rules in `CLAUDE.md` forces every team member to read and accept them.
- Leaving home memory empty lands the agent in the [[dumb-zone]] — it produces technically correct but communicatively misaligned output.
- Home memory is not a [[memory-layers|persistent chat history]]; it is a standing instruction file, not a log.

## See also

- [[claude-md]]
- [[memory-layers]]
- [[agents-md-discipline]]
- [[sycophancy]]
- [[dumb-zone]]
