---
title: OS Sandbox
category: security
tags: [security, sandbox, seatbelt, seccomp, landlock, bubblewrap, macos, linux]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
new_in: 2026-06-12
---

<span style="color:red">🆕 New in AI@Playtika Day 2 (2026-06-12)</span>

An OS sandbox uses an **operating-system primitive** to restrict every process an agent spawns — including child processes and MCPs — to a declared file allow/deny list and a network allow-list, without the agent being able to change its own restrictions.

## Why OS-level matters

Unlike application-level tripwires or permission prompts, an OS sandbox is enforced by the kernel. The agent **cannot edit its own settings** — it cannot modify `/etc/hosts` to fake an allowed host if the real host wasn't on the allow-list, and it cannot escalate privileges to read restricted paths. This is the property that makes it meaningful: the restrictions are outside the agent's control.

## Platform implementations

| Platform | Primitive |
|---|---|
| macOS | **Seatbelt** (`sandbox-exec`) |
| Linux | **seccomp** (syscall filtering) + **Landlock** (filesystem) + **[bubblewrap](https://github.com/containers/bubblewrap)** (the "BubbleRub" shorthand) |
| Linux egress proxy | `socat` as a transparent TCP proxy to enforce egress allow-lists |
| Windows | WSL2 (run Linux primitives inside) |

## What it blocks — and what it doesn't

The OS sandbox blocks config tampering and restricts network egress. However: if an allowed outbound channel exists (e.g. GitHub API), the agent can still **read disk contents and exfiltrate them over that channel** (base64-encode a file, send it as a URL parameter). The sandbox is **necessary but not sufficient** — pair it with minimal-permission keys ([[secret-zero]]) and [[docker-sandboxing]] for full containment.

## Inheritance by child processes

Every bash subprocess and locally-spawned MCP (stdio) runs inside the same sandbox — this is the [[mcp-sandbox-inheritance]] property. Remote MCPs are unaffected.

## Recommended ladder

<span style="color:red">**Auto-mode by default → OS sandbox for sensitive data → [[docker-sandboxing|Docker]] for remote/cloud runners → [[secret-zero|Bromure]] (microVM locker) for remote/other-machine runs → Bromure + remote for CI/prod-adjacent tasks.**</span>

## See also

- [[docker-sandboxing]]
- [[mcp-sandbox-inheritance]]
- [[agent-permissions]]
- [[secret-zero]]
- [[lethal-trifecta]]
- [[2026-06-11-ai-playtika]]
