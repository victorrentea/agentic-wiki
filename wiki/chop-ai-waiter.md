---
title: CHOP / AI-Waiter Anti-Pattern
category: pattern
tags: [anti-pattern, feedback-loop, copy-paste, chat, agentic-engineering]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-23
new_in: 2026-06-23
---

<span style="color:red">🆕 New in Garmin 2026-06-23</span>

CHOP — **Ch**at-**O**riented **P**rogramming — is the anti-pattern of copy-pasting between a chat web UI and your editor, with *you* acting as the waiter: ferrying code and errors back and forth so the AI can never see the result of its own work.

## The failure

A developer chats in a web UI, gets back a method that turns out to be deprecated, and has to *manually* discover the deprecation and tell the AI it was wrong. The human is doing the legwork an agent should do for itself — checking the build, reading the warning, looping. *"We are not the servants of AI."*

## The fix: embed and give it a feedback loop

- **Embed the agent in the project**, not a detached chat — so it can read files, run the build, and run tests.
- **Give it a way to check its own work**: *"build the project so the deprecation warning surfaces,"* then let it loop until the warning is gone. This is a [[feedback-loop]].
- For **learning a new API**, don't chat about it — have the agent build a **runnable prototype** with a real feedback loop, so its claims are grounded in execution rather than recalled (and possibly [[knowledge-cutoff|out-of-date]]) memory.

## Why it persists

CHOP feels safe because the human stays "in the loop" — but it taxes the scarcest resource (your attention, see [[caveman]]) and removes the agent's ability to self-correct. The whole discipline of [[agentic-engineering]] is the opposite: integrate, don't copy-paste; let the agent close its own loops. The [[cli-vs-mcp-tradeoff]] "connect the agent to the issue tracker instead of retyping the ticket" rule is the same instinct applied to requirements.

## See also
- [[feedback-loop]]
- [[agentic-engineering]]
- [[caveman]]
- [[knowledge-cutoff]]
- [[cli-vs-mcp-tradeoff]]
- [[2026-06-23-ai-garmin]]
