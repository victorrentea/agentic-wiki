---
title: OS Sandbox
category: security
tags: [security, sandbox, seatbelt, seccomp, landlock, bubblewrap, macos, linux, microvm]
sources: ["[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-08-13-ai-insurer]]", "[[2026-09-24-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-12
updated: 2026-10-13
---

An OS sandbox uses an **operating-system primitive** to restrict every process an agent spawns — including child processes and MCPs — to a declared file allow/deny list and a network allow-list, without the agent being able to change its own restrictions.

## Why OS-level matters

Unlike application-level tripwires or permission prompts, an OS sandbox is enforced by the kernel. The agent **cannot edit its own settings** — it cannot modify `/etc/hosts` to fake an allowed host if the real host wasn't on the allow-list, and it cannot escalate privileges to read restricted paths. This is the property that makes it meaningful: the restrictions are outside the agent's control.

<span style="color:red">**Why harness permissions aren't enough:** permissions live in the harness, not the OS. An agent can start a background bash script — not itself — that edits `settings.json` ([[2026-05-13-ai-it-services]]); and Bash permission rules match command *text*, so the docs themselves say a deny rule "isn't a security boundary around the program" and point to the sandbox for that ([permissions docs](https://code.claude.com/docs/en/permissions#compound-commands); see [[agent-permissions]]). An OS sandbox wraps the process **and all its sub-processes**, so even a forked helper can't escape.</span>

<span style="color:red">**Sandbox at the OS, not at the prompt.** With the harness's sandbox on, a `postinstall` script trying to read `~/.aws/credentials` or reach an unknown domain gets *permission denied* from the OS, because the restriction covers the agent and every child (npm, a helper script). This is the defence-in-depth that makes reduced-approval modes survivable ([[2026-05-18-secure-coding-payments]]; see [[supply-chain-attack]], [[approval-fatigue]]).</span>

## Platform implementations

| Platform | Primitive |
|---|---|
| macOS | **Seatbelt** (`sandbox-exec`) <span style="color:red">— e.g. [agent-seatbelt-sandbox](https://github.com/michaelneale/agent-seatbelt-sandbox)</span> |
| Linux | **seccomp** (syscall filtering) + **Landlock** (filesystem) + **[bubblewrap](https://github.com/containers/bubblewrap)** (the "BubbleRub" shorthand) |
| Linux egress proxy | `socat` as a transparent TCP proxy to enforce egress allow-lists |
| Windows | WSL2 (run Linux primitives inside) |
| <span style="color:red">Linux / Windows, practical equivalent</span> | <span style="color:red">a [Dev Container](https://containers.dev/) with only specific directories mounted ([[2026-05-13-ai-it-services]])</span> |

## Claude Code `/sandbox`

<span style="color:red">Claude Code's [`/sandbox`](https://code.claude.com/docs/en/sandboxing) is the harness-provided OS sandbox: kernel-level filesystem and network restrictions on **every Bash command and its children** — `cat secrets.env` fails at the kernel, and a helper script the agent writes and runs is fenced too (demo: hiding an API-key folder) ([[2026-05-27-ai-open-workshop]], [[2026-09-24-ai-bank]]). Recommended as the **default**: it keeps SSH keys out of reach; escalate to Docker or a microVM only for system-wide changes ([[2026-10-06-gray-factory-devoxx]]).</span>

<span style="color:red">A concrete config in `.claude/settings.json` shown in one session: `autoAllowBashIfSandboxed`, `denyRead` on `.env`, `denyWrite` on `.claude`, and network denied by default with an allow-list of domains ([[2026-08-13-ai-insurer]]) — check exact key names against the [sandboxing docs](https://code.claude.com/docs/en/sandboxing).</span>

<span style="color:red">**Credential masking**: against `.env` exfiltration, the sandbox can swap fake keys for real ones through a proxy — in headers only, toward the expected host only ([docs](https://code.claude.com/docs/en/sandboxing#mask-credentials)). The [[secret-zero]] swap has become a harness feature ([[2026-10-09-ai-jax-london]]).</span>

<span style="color:red">Catches ([[2026-05-27-ai-open-workshop]], [[2026-07-09-ai-live-casino]], [[2026-09-24-ai-bank]]):</span>

- <span style="color:red">**The escape hatch** — Claude can ask to run a command *outside* the sandbox; disable that in config if you want the fence to hold.</span>
- <span style="color:red">**Docker launched inside it escapes** — containers share the host kernel; it also can't easily start Docker for you (no Testcontainers).</span>
- <span style="color:red">**Shared filesystem** — writes land on the host; a globally installed dependency still ends up in your global setup. "Like Docker without Docker" ([[2026-07-01-ai-java-community]]).</span>
- <span style="color:red">Residual risks stay human: a "yes" to an install prompt, or an infected global dependency you later run yourself.</span>

## What it blocks — and what it doesn't

The OS sandbox blocks config tampering and restricts network egress. However: if an allowed outbound channel exists (e.g. GitHub API), the agent can still **read disk contents and exfiltrate them over that channel** (base64-encode a file, send it as a URL parameter). The sandbox is **necessary but not sufficient** — pair it with minimal-permission keys ([[secret-zero]]) and [[docker-sandboxing]] for full containment.

<span style="color:red">**Unscopable legacy keys** (Jenkins, Jira, Confluence can't be scoped like GitHub fine-grained tokens) stay on the host, behind an MCP that exposes **2 tools, not 45** ([[2026-10-09-ai-jax-london]]; see [[mcp-sandbox-inheritance]]).</span>

## Inheritance by child processes

Every bash subprocess and locally-spawned MCP (stdio) runs inside the same sandbox — this is the [[mcp-sandbox-inheritance]] property. Remote MCPs are unaffected.

## Beyond the OS sandbox: microVMs

<span style="color:red">The isolation spectrum, from lightest to heaviest: harness permissions → OS sandbox (Seatbelt / Landlock / seccomp) → agent in Docker → **microVM** ([Firecracker](https://firecracker-microvm.github.io/), [Kata Containers](https://katacontainers.io/), Apple Container) ([[2026-05-06-ai-ecommerce]], [[2026-07-01-ai-java-community]]).</span>

<span style="color:red">**Why a microVM beats plain Docker for agents** ([[2026-05-18-secure-coding-payments]], [[2026-08-13-ai-insurer]], [[2026-10-09-ai-jax-london]]):</span>

- <span style="color:red">a Docker-wrapped agent can't start its own containers (Testcontainers) without privileged mode, which defeats the sandbox — a microVM runs nested Docker safely;</span>
- <span style="color:red">containers share the host kernel, a microVM doesn't (see [Simon Willison on OpenAI's accidental attack on Hugging Face](https://simonwillison.net/2026/Jul/22/openai-cyberattack) for boxed agents digging through a shared kernel);</span>
- <span style="color:red">an agent in plain Docker *still has your keys inside*, so it can exfiltrate them; the microVM setup gives it **fake keys** and a host proxy swaps in the real ones only for allowed destinations.</span>

<span style="color:red">[Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) is the ready-made option: the standalone `sbx` CLI, each sandbox a microVM with its own kernel and Docker daemon, plus credential swapping; it runs on Apple-silicon macOS, Windows 11 and Ubuntu 24.04+ ([Docker docs](https://docs.docker.com/ai/sandboxes/install/)). For CI runners executing agent sessions: a fresh microVM per task, destroyed afterwards ([[2026-05-06-ai-ecommerce]]). Details in [[docker-sandboxing]].</span>

## Recommended ladder

**Auto-mode by default → OS sandbox for sensitive data → [[docker-sandboxing|Docker]] for remote/cloud runners → [[secret-zero|Bromure]] (microVM locker) for remote/other-machine runs → Bromure + remote for CI/prod-adjacent tasks.**

<span style="color:red">The same ladder as taught in later sessions, as four "leash levels" ([[2026-08-13-ai-insurer]], [[2026-07-01-ai-java-community]]): (1) auto mode — what you get if you do nothing else; (2) OS sandbox (`/sandbox`); (3) agent in Docker; (4) a microVM (Docker Sandboxes, devcontainer-in-VM, Bromure). By autumn 2026 the advice moved the OS sandbox from "for sensitive data" to **the default for everyday work** ([[2026-10-06-gray-factory-devoxx]]).</span>

## See also

- [[docker-sandboxing]]
- [[mcp-sandbox-inheritance]]
- [[agent-permissions]]
- [[secret-zero]]
- [[lethal-trifecta]]
- <span style="color:red">[[dont-run-in-home-folder]]</span>
- <span style="color:red">[[supply-chain-attack]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[production-safety]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- <span style="color:red">[[2026-05-18-secure-coding-payments]]</span>
- <span style="color:red">[[2026-08-13-ai-insurer]]</span>
- <span style="color:red">[[2026-10-09-ai-jax-london]]</span>
