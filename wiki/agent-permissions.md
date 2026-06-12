---
title: Agent Permissions
category: concept
tags: [security, permissions, auto-mode, yolo, tripwires, claude-code]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
new_in: 2026-06-12
---

<span style="color:red">🆕 New in AI@Playtika Day 2 (2026-06-12)</span>

Agent permissions form a spectrum from fully supervised to fully autonomous, with each mode trading safety for throughput — and with [[tripwire|tripwires]] as a surgical override at any level.

## The permission spectrum

| Mode | What it does | Risk |
|---|---|---|
| **Default (ask)** | Prompts before every file write or bash execution. | Low throughput; annoying for trusted operations. |
| **Auto-mode** | A Sonnet judge evaluates each tool call: *"is what the agent is about to run consistent with what the user asked?"* Flags unusual calls. | Not 100% safe — the judge has a bad day. |
| **Shift-Tab / auto-accept edits** | Stops prompting for file edits; you review the diff at the end. Bash still asks. | Low risk for file changes; bash still supervised. |
| **YOLO mode** (`--dangerously-skip-permissions`) | No judge, no confirmation. The agent can read your mail very fast. | High — use only inside a [[docker-sandboxing|Docker sandbox]]. |

## Tripwires as surgical overrides

A [[tripwire]] is a "kind requirement" (not a hard deny) that fires regardless of the permission level. Examples: *"please never read `secrets.env`"*, *"don't add a new npm dependency"*, *"don't bypass git hooks."* Tripwires still allow the agent to refuse to comply — they are advisory assertions, not OS-level blocks. Denying reads of a specific secrets file actually works well in practice.

## Why denying part of bash is naïve

Blocking individual bash commands is futile: the agent can rename a blocked variable, drop a flag, or use a subshell to achieve the same effect. You either deny **all** bash (reverting to copy-paste "CHOP" mode) or you properly **[[os-sandbox|sandbox]]** at the OS or container level.

## Production agent posture

For a production-facing agent, the correct posture is:
- A handful of fine-grained MCPs (not bash).
- Tight rate limits.
- No generic read/write/web/restart.
- Gradually expand permissions as reliability is demonstrated (*"in three months you'll allow one restart before it must call a human"*).

## See also

- [[os-sandbox]]
- [[docker-sandboxing]]
- [[tripwire]]
- [[harness]]
- [[headless-claude]]
- [[2026-06-11-ai-playtika]]
