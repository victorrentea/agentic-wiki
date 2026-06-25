---
title: Agent Lifecycle Hooks
category: concept
tags: [hooks, claude-code, agent-control, pre-tool-use, post-tool-use, tripwire]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-25
updated: 2026-06-25
new_in: "Kambi Day 2 (2026-06-25)"
---

<span style="color:red">🆕 New in Kambi Day 2 (2026-06-25)</span>

Programmable callbacks that tap into an AI agent's turn loop at defined points — before a tool runs, after it completes, when the agent stops, or when it is blocked — enabling inspection, enforcement, and side effects without modifying the agent's instructions.

## The hook points

| Hook | Fires | Use |
|---|---|---|
| `PreToolUse` | Before every tool call | Inspect and optionally **reject** the call |
| `PostToolUse` | After every tool call | Log, audit, trigger side effects |
| `Stop` | End of each agent turn | Notifications ("Captain Hook" — a beep on turn end) |
| `Notification` | Agent is blocked/waiting | External alerting, paging |

The canonical demo is a beep on `Stop` ("Captain Hook"). Immediately annoying, but it illustrates that hooks are real code executing on every turn — the same mechanism can do far more consequential things.

## `PreToolUse` as an enforcement layer

`PreToolUse` is the real guard hook. It can inspect the command *before* it runs and **reject** it:
- `git push --no-verify` → rejected.
- `rm -rf` on a protected path → rejected.
- Writes to `CLAUDE.md` or `CODEOWNERS` → rejected (force to [[codeowners-elders]] review flow instead).

This is stronger than git pre-push hooks, which are [[tripwire|tripwires]] — skippable with `--no-verify` or by chaining commands with `&&` to bypass the hook. `PreToolUse` rejection happens inside the harness, before the shell command is ever invoked.

## Relation to [[guardrails]] and [[reward-hacking]]

Hooks are the technical substrate for guardrails. Without hooks, the only enforcement is the agent's own instruction-following — which [[reward-hacking]] erodes over time. With `PreToolUse`, the enforcement is external to the agent's judgment and cannot be talked out of.

Combined with [[docker-sandboxing]], hooks form a layered defense: hooks intercept dangerous intent; the container limits blast radius if something slips through.

## [[ci-green-loop]] integration

A `PostToolUse` hook on test runs can push results to a dashboard or trigger a notification when tests go red — closing the [[feedback-loop]] without requiring the human to poll.

## See also

- [[tripwire]]
- [[agent-permissions]]
- [[ci-green-loop]]
- [[reward-hacking]]
- [[docker-sandboxing]]
- [[guardrails]]
- [[codeowners-elders]]
