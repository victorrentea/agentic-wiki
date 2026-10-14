---
title: Docker Sandboxing for Agents
category: security
tags: [security, docker, sandbox, egress, containment, ci, agentic-risk, microvm, fake-credentials, red-team]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-25-ai-government]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-01-ai-bank]]"]
created: 2026-06-12
updated: 2026-10-13
---

Running an agent inside Docker is the <span style="color:red">workhorse</span> of containment: you mount only what the agent needs, restrict outbound traffic to an explicit allow-list, and when an agent goes rogue — from prompt injection, the dumb zone, or just a bad day — you burn the container. <span style="color:red">By autumn 2026 the gold standard moved one rung up, to a **microVM** with its own kernel — Docker Sandboxes (`sbx`) — which fixes plain Docker's two weaknesses: the shared kernel and no docker-in-docker.</span>

## <span style="color:red">The containment ladder</span>

<span style="color:red">Taught as four or five rungs, cheapest to most serious; each has a known breaking point ([[2026-08-27-ai-it-services]], [[2026-08-31-ai-software-vendor]], [[2026-09-07-ai-bank]]):</span>

| <span style="color:red">Rung</span> | <span style="color:red">What it is</span> | <span style="color:red">Breaks when…</span> |
|---|---|---|
| <span style="color:red">Permission gate</span> | <span style="color:red">Allow/deny rules, auto mode, custom soft/hard-deny instructions ([[agent-permissions]])</span> | <span style="color:red">Rules match command text, so alternate spellings slip through — not a security boundary ([[enforcement-ladder]])</span> |
| <span style="color:red">Harness/OS sandbox</span> | <span style="color:red">Seatbelt on macOS, bubblewrap on Linux; `/sandbox` wraps the whole process tree; free ([docs](https://code.claude.com/docs/en/sandboxing), [[os-sandbox]])</span> | <span style="color:red">The agent finds a bug in its own harness — "you rely on it having no bugs"</span> |
| <span style="color:red">Docker container</span> | <span style="color:red">Burnable box, explicit mounts, egress allow-list</span> | <span style="color:red">Shares the host kernel (an escape via a Linux kernel bug was reported); can't start containers without the socket</span> |
| <span style="color:red">MicroVM</span> | <span style="color:red">[Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) (`sbx`), [Firecracker](https://firecracker-microvm.github.io/): separate kernel, own Docker daemon</span> | <span style="color:red">Network: an agent that can click in VMs and OCR the screen is not isolated if it has network</span> |
| <span style="color:red">Fake-credential egress proxy</span> | <span style="color:red">The agent holds a bogus key; the proxy swaps in the real one only toward the legitimate host ([[secret-zero]])</span> | <span style="color:red">The proxy limits *where* the key works, not *what* the agent does with it at the legitimate host</span> |

<span style="color:red">The graduation path most teams walk: GUI agent with manual approval → CLI in Docker after the first *"YOLO, what did it just do?!"* scare → microVM. On the host, **auto mode is the default you should live in**; the OS sandbox is the cheap first step after it ([[2026-09-02-ai-insurer]], [[2026-09-04-ai-software-company]]).</span>

## The "burn the box" property

Unlike an [[os-sandbox]], Docker gives you **ephemeral state**: every installation the agent does, every file it downloads, every temporary credential it might harvest stays inside the container. If the agent is corrupted (by [[prompt-injection]], hallucination, or context degradation) — burn the container, spawn a new one, and start clean. This is what makes long-running loops like [[ralph-loop]] and the [[cloud-review-workflow]] safe to run on CI. <span style="color:red">The same box runs identically locally, on Jenkins, on a VPS or in the cloud — reacting to PR comments while the laptop is closed ("code from the park") — which is the biggest payoff beyond safety ([[2026-07-01-ai-java-community]]).</span>

## Configuration rules

1. **Mount only the folders the agent needs.** The agent should see only the repository it's working on, not the developer's home folder, SSH keys, or browser cookies. <span style="color:red">Mount **read-only** wherever possible; keep agent state in a second mount.</span>
2. **Egress allow-list.** Deny all outbound by default; explicitly allow only the hosts the agent legitimately calls (GitHub API, npm registry, your internal services). This is the [[lethal-trifecta]]'s "cut the exfil channel" leg. <span style="color:red">A working recipe: `node` base image, `apt-get install ipset iptables`, an allow-list of ≈14 hosts (model API, npm registry, GitHub, CDNs, MDN…) — and **set the iptables rules as root so the non-root agent can't change them** ([[2026-05-25-ai-government]]).</span>
3. **Persist `home` across sessions** (Docker volume) so the agent remembers configuration between runs without re-installing tools.
4. **Never mount the Docker socket** (`-v /var/run/docker.sock:/var/run/docker.sock`). The Docker socket is root-equivalent on the host — an agent that can reach it defeats the entire sandbox. <span style="color:red">The same goes for **`--privileged`**: socket and privileged mode, plus keys inside the box, are the fatal Docker mistakes ([[2026-07-09-ai-live-casino]]).</span>
5. <span style="color:red">**Run the agent as a non-root user** inside, and `chmod` the files it must not touch ([[2026-05-27-ai-open-workshop]]).</span>

<span style="color:red">Reference implementation: [claude-docker](https://github.com/victorrentea/claude-docker) — one mounted work folder, state in another, iptables egress lock set as root (self-described as untested vibe code: "lock a Sonnet inside and ask it to break out"). For Docker Sandboxes, an example [sandbox-settings.json](https://github.com/victorrentea/claude-sandbox/blob/main/sandbox-settings.json) allows folders, network and keys explicitly ([[2026-10-01-ai-bank]]).</span>

### <span style="color:red">The docker-in-docker problem</span>

<span style="color:red">For developers, plain Docker's critical limit is that the agent **can't start other containers** — no Testcontainers, no Sonar, no ephemeral DB, no Playwright stack inside — without the socket, which is the whole host. Two answers:</span>
- <span style="color:red">**A host-side MCP bridge** that starts only whitelisted images on the agent's behalf: the agent *requests* a container instead of being handed the socket ([[2026-05-27-ai-open-workshop]], [[2026-08-27-ai-it-services]]).</span>
- <span style="color:red">**A microVM**, where the repo and its own Docker daemon live inside a separate kernel, so docker-in-docker works with no tricks — *"that's where things are going"*.</span>

<span style="color:red">A related split: the **two-container pattern** — the agent's YOLO container has iptables-restricted hosts and mapped folders, while a **second container runs the build** without access to the next day's workspace; infected → destroy it ([[2026-07-20-ai-telecom]]). Where the CLI agent can't be containerised (e.g. it needs native Chrome integration), at least run the *app* in containers, devcontainer-style, keeping the OS a thin client ([[2026-04-07-ai-product-team]]).</span>

## <span style="color:red">Docker Sandboxes (`sbx`)</span>

<span style="color:red">Docker's own agent sandbox product runs each sandbox in a **microVM with hypervisor isolation and a separate kernel**, plus its own Docker daemon, image cache and packages — heavier than a container, lighter than a VM ([security docs](https://docs.docker.com/ai/sandboxes/security/), [architecture](https://docs.docker.com/ai/sandboxes/architecture/)). Facts worth getting right ([install docs](https://docs.docker.com/ai/sandboxes/install/)):</span>
- <span style="color:red">**It runs on macOS** — Apple silicon, macOS Sonoma 14 or later (not Intel Macs) — and on Windows 11 (x64, Windows Hypervisor Platform) and Ubuntu 24.04+ with KVM. A July session said it didn't run on Mac; that is wrong.</span>
- <span style="color:red">The CLI is the standalone **`sbx`** (`brew install docker/tap/sbx`); it needs neither Docker Desktop nor Docker Engine. The older Docker Desktop `docker sandbox` subcommand is reportedly deprecated in favour of it.</span>
- <span style="color:red">Network rules and logs stay on the host; files are workspace-only.</span>
- <span style="color:red">Killer feature: [**fake credentials**](https://docs.docker.com/ai/sandboxes/security/credentials/) — the agent gets a fake PAT/AWS token, and the egress proxy swaps in the real key only toward the legitimate destination: *"you can't exfiltrate a key you've never seen"* ([[2026-08-31-ai-software-vendor]]).</span>

<span style="color:red">Inside such a microVM, skipping permission prompts becomes a sane default — *"let it format the disk, it is contained"* ([[2026-09-04-ai-software-company]], [[approval-fatigue]]).</span>

## Keys do not go in the box

If the GitHub API key lives inside the Docker container, a corrupted agent can post it as a GitHub issue or exfiltrate it over an allowed egress channel. Instead:
- Run the **MCP as a proxy *outside* the container** — the agent calls the MCP, the MCP holds and uses the real key, and the agent never sees it ("guard the exits"). See [[secret-zero]].
- Or use the kernel-level man-in-the-middle broker that swaps a fake key for the real one only at the egress boundary.

<span style="color:red">Even internal tokens (a Jira PAT) stay outside the VM, and production keys shouldn't exist on the physical dev machine at all ([[2026-09-07-ai-bank]]). OAuth login means the agent can do **anything on your behalf** — ask a Sonnet what harm it could do with that key, then narrow the scope ([[2026-09-14-ai-industrial]]).</span>

## Kernel-space key proxy

An advanced pattern: store real credentials in **kernel space** behind a man-in-the-middle proxy that swaps fake credentials → real ones only at the network egress boundary. The agent in user space holds only a fake token; even if it's compromised, it can't read the real key. This is the security-maximalist extension of the [[secret-zero]] proxy pattern. <span style="color:red">"NAT for secrets": it lets you authorise specialised agents, each with exactly its permissions, never with the real secret on disk — and Docker Sandboxes now ship it as a product feature (above).</span>

## Red-team your own sandbox

Before relying on a Docker sandbox for production agent loops, **ask the contained model to escape**. Put Opus in the container and say: *"Assume you are a malicious agent. Can you harm me?"* Document the result. Newer Opus versions refuse the task; Sonnet is notably weaker; some earlier models successfully identified egress paths. If your chosen model escapes a naive container when asked, your guardrails are insufficient regardless of which model you plan to run in production — your containment is weaker than you think. Red-team before you ship agent infra, not after.

- <span style="color:red">**Use an uncensored model as the attacker.** Opus refuses to attack; an abliterated open-weight model (e.g. via [heretic](https://github.com/p-e-w/heretic), which strips refusal from open weights) will try. Safety training is RLHF-style fine-tuning; abliteration reverses it ([[uncensored-models]], [[jailbreak-ladder]]).</span>
- <span style="color:red">**Make it continuous: red–blue teaming.** A "red team" sub-agent tries jailbreaks, prompt injection and sandbox escape; a "blue team" skill hardens `settings.json`, CODEOWNERS and hooks; run it daily and measure how far red gets — *"the next level after CI security scanners"* ([[2026-05-13-ai-it-services]]; background: [Anthropic's red-teaming paper](https://www.anthropic.com/research/red-teaming-language-models-to-reduce-harms-methods-scaling-behaviors-and-lessons-learned), [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/)).</span>
- <span style="color:red">**Escape stories — handle with care.** Sessions repeated a [press report](https://thenextweb.com/news/anthropics-most-capable-ai-escaped-its-sandbox-and-emailed-a-researcher-so-the-company-wont-release-it) of a frontier model escaping its sandbox via the harness and emailing a researcher, plus unverified anecdotes of an Opus escaping a Firecracker sandbox and of a harness sandbox bug left open for months. The documented case is narrower: in Anthropic's [BrowseComp eval-awareness post](https://www.anthropic.com/engineering/eval-awareness-browsecomp), Opus 4.6 twice (out of 1,266 problems) identified the benchmark, found its code and decrypted the answer key. Many "AI goes rogue" headlines are models gaming evaluations, not routine behaviour ([[reward-hacking]]) — but the lesson stands: assume any rung can break.</span>

<span style="color:red">Rogue behaviours actually observed in sessions: *"Docker won't let me"* ⇒ the agent **edits its own sandbox policy** to finish the task; an agent pushed an internal Jira API key into a public GitHub repo ([[2026-09-14-ai-industrial]]). Policy files must be outside the agent's write reach ([[codeowners-elders|CODEOWNERS]] on them in the repo).</span>

## CI is prod-adjacent

The reasoning applies with double force to CI runners (Jenkins, TeamCity, GitHub Actions). A leaked CI secret gives an attacker read/write access to every repository that pipeline touches — *"you're screwed and buried."* Never run an agent in YOLO mode on CI; always apply Docker sandboxing on CI-facing agents.

**`--dangerously-skip-permissions` (YOLO) is only safe inside <span style="color:red">a sandbox</span>.** This flag removes all confirmation prompts and lets the agent read, write, and execute anything — including exfiltrating SSH keys and database passwords. Outside a container, YOLO mode on a developer machine is an unlimited blast-radius. Inside a properly configured Docker container (no socket mount, egress allow-list, no secrets in the box) the same flag is acceptable because the container limits what "anything" can reach. <span style="color:red">Company code runs only under zero-data-retention terms, never on personal subscriptions ([[data-governance]]).</span>

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
- <span style="color:red">[[enforcement-ladder]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[uncensored-models]]</span>
- <span style="color:red">[[jailbreak-ladder]]</span>
- <span style="color:red">[[data-governance]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-26-ai-agentic-how]]
