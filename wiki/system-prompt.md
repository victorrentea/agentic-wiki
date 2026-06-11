---
title: System Prompt
category: concept
tags: [system-prompt, security, guardrails, prompt-injection, llm]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]"]
created: 2026-06-10
updated: 2026-06-11
---

The system prompt is the `System`-role message that establishes the model's persona and behavioral constraints — and, unlike user/assistant turns, it is **never evicted** from the [[context-window]].

## The four message types

LLMs reason over exactly four message roles:

| Role | Purpose |
|---|---|
| **System** | Persona, rules, "pretty-please" guardrails |
| **User** | Human input each turn |
| **Assistant** | The model's own prior replies |
| **Tool** | [[tool-calling]] invocations and their results |

<span style="color:red">## What goes into the LLM on turn 1

In an agent [[harness]], far more than your text reaches the model before you finish your first sentence — run `/context` to see it (a fresh chat can already burn ≈20k tokens). The turn-1 payload:

- **Harness / system prompt** — "you shall not do harm", baked in by the harness.
- **Context** — your message plus the files it references.
- **Tools** — the harness's built-in `edit`/`write`/`glob` *plus* every MCP tool's [[tool-calling|schema]].
- **Memory** — `memory.md` in your home + the project [[claude-md|CLAUDE.md]] in the working directory.
- **Skill front-matter** — the *description* of every installed [[agent-skill|skill]] is eager-loaded so the agent knows what it could pull on demand ([[progressive-disclosure]]).

A bloated baseline (too many always-on MCP servers) silently shrinks your usable window and pushes you toward the [[dumb-zone]] faster — a core [[token-economy]] concern.</span>

## "Pretty-please" guardrails

The honest label for system-prompt constraints: you are *politely asking* the model to behave. The system prompt is not a security boundary — it is a strong nudge. It can be overridden by:

- **Classic [[prompt-injection]]:** "Ignore previous instructions…"
- **Emotional manipulation + sycophancy:** convince the model in small steps over multiple turns until "drift from purpose" feels insignificant and it complies.
- Uncensored models (HuggingFace, no safety RLHF) break far more easily than frontier models.

## Why it never leaves the window

Only `User` and `Assistant` turns slide out as the [[chat-memory]] window advances. The `System` message is pinned, so "your purpose in life" (the persona, the rules, the injected owner identity) is always visible to the model regardless of conversation length.

## Identity injection and the JWT risk

The demo injects `ownerId / name / email` extracted from a [[jwt-identity]] token into the system prompt. If the token signature is not validated, an attacker controls text that lands directly in the system prompt — a [[prompt-injection]] vector. Always verify the JWT signature before concatenating any of its claims.

## Layered defenses beyond the system prompt

The system prompt alone is insufficient. Layer it with [[guardrails]] (regex pre-filter via `SafeGuardAdvisor`) and a [[judge-llm]] (`JudgeGuard`) that screens both the incoming prompt and the outgoing response. See [[advisor]] for how these compose.

Source: [[2026-06-10-spring-ai-itkonekt]]

## See also

- [[guardrails]]
- [[prompt-injection]]
- [[advisor]]
- [[judge-llm]]
- [[chat-memory]]
- [[context-window]]
- [[jwt-identity]]
- [[harness]]
- [[claude-md]]
- [[agent-skill]]
- [[2026-06-11-ai-playtika]]
