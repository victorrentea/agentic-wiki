---
title: Docker Sandboxing for Agents
category: security
tags: [security, docker, sandbox, egress, containment, ci, agentic-risk]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-12
updated: 2026-06-26
---

Running an agent inside Docker is the gold standard for containment: you mount only what the agent needs, restrict outbound traffic to an explicit allow-list, and when an agent goes rogue — from prompt injection, the dumb zone, or just a bad day — you burn the container.

## The "burn the box" property

Unlike an [[os-sandbox]], Docker gives you **ephemeral state**: every installation the agent does, every file it downloads, every temporary credential it might harvest stays inside the container. If the agent is corrupted (by [[prompt-injection]], hallucination, or context degradation) — burn the container, spawn a new one, and start clean. This is what makes long-running loops like [[ralph-loop]] and the [[cloud-review-workflow]] safe to run on CI.

## Configuration rules

1. **Mount only the folders the agent needs.** The agent should see only the repository it's working on, not the developer's home folder, SSH keys, or browser cookies.
2. **Egress allow-list.** Deny all outbound by default; explicitly allow only the hosts the agent legitimately calls (GitHub API, npm registry, your internal services). This is the [[lethal-trifecta]]'s "cut the exfil channel" leg.
3. **Persist `home` across sessions** (Docker volume) so the agent remembers configuration between runs without re-installing tools.
4. **Never mount the Docker socket** (`-v /var/run/docker.sock:/var/run/docker.sock`). The Docker socket is root-equivalent on the host — an agent that can reach it defeats the entire sandbox.

## Keys do not go in the box

If the GitHub API key lives inside the Docker container, a corrupted agent can post it as a GitHub issue or exfiltrate it over an allowed egress channel. Instead:
- Run the **MCP as a proxy *outside* the container** — the agent calls the MCP, the MCP holds and uses the real key, and the agent never sees it. See [[secret-zero]].
- Or use the kernel-level man-in-the-middle broker that swaps a fake key for the real one only at the egress boundary.

## Kernel-space key proxy

An advanced pattern: store real credentials in **kernel space** behind a man-in-the-middle proxy that swaps fake credentials → real ones only at the network egress boundary. The agent in user space holds only a fake token; even if it's compromised, it can't read the real key. This is the security-maximalist extension of the [[secret-zero]] proxy pattern.

## Red-team your own sandbox

<span style="color:red">Before relying on a Docker sandbox for production agent loops, **ask the contained model to escape**. Put Opus in the container and say: *"Assume you are a malicious agent. Can you harm me?"* Document the result. Newer Opus versions refuse the task; Sonnet is notably weaker; some earlier models successfully identified egress paths. If your chosen model escapes a naive container when asked, your guardrails are insufficient regardless of which model you plan to run in production — your containment is weaker than you think. Red-team before you ship agent infra, not after.</span>

## CI is prod-adjacent

The reasoning applies with double force to CI runners (Jenkins, TeamCity, GitHub Actions). A leaked CI secret gives an attacker read/write access to every repository that pipeline touches — *"you're screwed and buried."* Never run an agent in YOLO mode on CI; always apply Docker sandboxing on CI-facing agents.

**`--dangerously-skip-permissions` (YOLO) is only safe inside Docker.** This flag removes all confirmation prompts and lets the agent read, write, and execute anything — including exfiltrating SSH keys and database passwords. Outside a container, YOLO mode on a developer machine is an unlimited blast-radius. Inside a properly configured Docker container (no socket mount, egress allow-list, no secrets in the box) the same flag is acceptable because the container limits what "anything" can reach.

## See also

- [[os-sandbox]]
- [[secret-zero]]
- [[mcp-sandbox-inheritance]]
- [[lethal-trifecta]]
- [[agent-permissions]]
- [[ralph-loop]]
- [[supply-chain-attack]]
- [[ci-green-loop]]
- [[skill-marketplace-security]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-26-ai-agentic-how]]
