---
title: Secret-Zero / Broker Pattern
category: security
tags: [security, secrets, credentials, broker, mcp, least-privilege]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
new_in: 2026-06-12
---

<span style="color:red">🆕 New in AI@Playtika Day 2 (2026-06-12)</span>

The secret-zero pattern ensures the agent **never holds a long-lived credential** — a sidecar or proxy injects the real key only at the outbound boundary, keeping it permanently outside the agent's reach.

## The problem

If an API key lives inside the agent's [[docker-sandboxing|Docker container]] or in the conversation context, a corrupted or [[prompt-injection|prompt-injected]] agent can:
- Post the key as a GitHub issue.
- Encode it base64 and fetch a URL with it as a query parameter.
- Write it to a file in a mounted volume.

This is the hardest exfiltration vector to defend against with filters alone.

## The broker solutions

### MCP-as-proxy (practical today)

Run the MCP server **outside the agent's container**. The agent calls `git_push` on the MCP; the MCP authenticates using the real GitHub API key it holds; the agent never sees the key. The agent can "unleash hell on Git" but cannot exfiltrate the credential because it never possessed it.

### Kernel-level key swap (advanced)

A kernel-level man-in-the-middle intercepts outbound requests, strips a **fake API key** the agent was given, and replaces it with the real one before the packet leaves the host. The real key is **stored outside user space** — even a root process inside the container cannot read it without kernel cooperation.

This is the vendor-neutral form of what platforms like HashiCorp Vault and cloud IAM role-injection do for traditional workloads — the "workload identity" pattern applied to agents.

### Bromure (microVM locker)

<span style="color:red">**[Bromure](https://bromure.io/en/agentic-coding)** boots a Linux microVM (Apple Virtualization.framework), mounts only the folders you choose, and ships the agent **fake credential stubs** — fake tokens, API keys, kubeconfig — that look real to `git`, `gh`, `kubectl`, `aws`, and `npm`. At egress, it swaps in the real secret only after a human-in-the-loop step per sensitive credential. Even local malware on the host machine cannot see the real key. This is the fullest realisation of the secret-zero pattern: the workload never holds any real credential, not even transiently. Recommended level on the containment ladder: **microVM for remote/cloud/other-machine runs**.</span>

## Practical minimum

The practical minimum for most teams:
1. Never paste secrets into the conversation or the CLAUDE.md.
2. Never set environment variables with long-lived keys inside a Docker container that the agent controls.
3. Use the MCP-as-proxy approach for any external service the agent calls.

## See also

- [[docker-sandboxing]]
- [[os-sandbox]]
- [[lethal-trifecta]]
- [[agent-permissions]]
- [[mcp-sandbox-inheritance]]
- [[2026-06-11-ai-playtika]]
