---
title: Secret-Zero / Broker Pattern
category: security
tags: [security, secrets, credentials, broker, mcp, least-privilege]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-03-ai-bank]]", "[[2026-09-25-ai-bank-migration]]"]
created: 2026-06-12
updated: 2026-10-13
---

The secret-zero pattern ensures the agent **never holds a long-lived credential** — a sidecar or proxy injects the real key only at the outbound boundary, keeping it permanently outside the agent's reach.

## The problem

If an API key lives inside the agent's [[docker-sandboxing|Docker container]] or in the conversation context, a corrupted or [[prompt-injection|prompt-injected]] agent can:
- Post the key as a GitHub issue.
- Encode it base64 and fetch a URL with it as a query parameter.
- Write it to a file in a mounted volume.

This is the hardest exfiltration vector to defend against with filters alone.

<span style="color:red">Two common ways the key ends up within reach without anyone noticing:</span>
- <span style="color:red">**Environment variables.** Any bash command — hence the agent — can read them (`env`, `printenv`, `/proc/self/environ`). Don't pass secrets to the agent's process that way ([[2026-07-03-ai-bank]]).</span>
- <span style="color:red">**Hard-coded credentials in `mcp.json`.** A plaintext DB password in an MCP config is a common slip; the agent reads its MCP config like any other file. Reference `${env:DB_PASSWORD}` or a secret manager instead, and keep that variable out of the agent's own shell ([[2026-05-13-ai-it-services]]).</span>

## The broker solutions

### MCP-as-proxy (practical today)

Run the MCP server **outside the agent's container**. The agent calls `git_push` on the MCP; the MCP authenticates using the real GitHub API key it holds; the agent never sees the key. The agent can "unleash hell on Git" but cannot exfiltrate the credential because it never possessed it.

<span style="color:red">If "MCP" is politically banned in your organisation, the same broker can be a **plain REST endpoint on the host** that holds the key, exposed by port into the container, plus a [[agent-skill|skill]] teaching the agent to `curl` it. The secret never enters the guest ([[2026-07-03-ai-bank]]).</span>

### Kernel-level key swap (advanced)

A kernel-level man-in-the-middle intercepts outbound requests, strips a **fake API key** the agent was given, and replaces it with the real one before the packet leaves the host. The real key is **stored outside user space** — even a root process inside the container cannot read it without kernel cooperation.

<span style="color:red">In a live demo of such a proxy, the agent worked with fake keys on the machine and the real ones were swapped in just before traffic was encrypted. Opus, challenged to find the real keys from inside, gave up: *"there was nothing I could do, it wasn't in user space."* A malicious post-install script running inside (such as the March 2026 axios RAT dropper — see [[supply-chain-attack]]) would likewise find only fakes ([[2026-06-05-ai-consultancy]]).</span>

This is the vendor-neutral form of what platforms like HashiCorp Vault and cloud IAM role-injection do for traditional workloads — the "workload identity" pattern applied to agents.

### Bromure (microVM locker)

**[Bromure](https://bromure.io/en/agentic-coding)** boots a Linux microVM (Apple Virtualization.framework), mounts only the folders you choose, and ships the agent **fake credential stubs** — fake tokens, API keys, kubeconfig — that look real to `git`, `gh`, `kubectl`, `aws`, and `npm`. At egress, it swaps in the real secret only after a human-in-the-loop step per sensitive credential. Even local malware on the host machine cannot see the real key. This is the fullest realisation of the secret-zero pattern: the workload never holds any real credential, not even transiently. Recommended level on the containment ladder: **microVM for remote/cloud/other-machine runs**.

## Practical minimum

The practical minimum for most teams:
1. Never paste secrets into the conversation or the CLAUDE.md.
2. Never set environment variables with long-lived keys inside a Docker container that the agent controls.
3. Use the MCP-as-proxy approach for any external service the agent calls.
4. <span style="color:red">**Let the agent do everything except the last step.** *You* place the secret in a file outside the working folder, so the agent can't `cat` it, and use a key scoped to dev/staging — never your personal user. Watch approved multi-line bash: a 5-line script whose last line quietly exfiltrates is easy to wave through ([[2026-07-03-ai-bank]], [[approval-fatigue]]).</span>
5. <span style="color:red">**Let the agent hide the password from itself.** On a test DB, have it use a vendor CLI's saved connection (e.g. Oracle SQLcl) by name, without ever reading the secret ([[2026-09-25-ai-bank-migration]]). The same idea scales to [[authenticated-cli|pre-authenticated CLIs]] in general.</span>

## See also

- [[docker-sandboxing]]
- [[os-sandbox]]
- [[lethal-trifecta]]
- [[agent-permissions]]
- [[mcp-sandbox-inheritance]]
- <span style="color:red">[[supply-chain-attack]]</span>
- <span style="color:red">[[authenticated-cli]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[internal-tool-cli-skill]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- <span style="color:red">[[2026-05-13-ai-it-services]]</span>
- <span style="color:red">[[2026-06-05-ai-consultancy]]</span>
- <span style="color:red">[[2026-07-03-ai-bank]]</span>
- <span style="color:red">[[2026-09-25-ai-bank-migration]]</span>
