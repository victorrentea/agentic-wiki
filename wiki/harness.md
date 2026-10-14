---
title: Harness
category: concept
tags: [agent-harness, claude-code, tooling, knowledge, actions, cli, copilot, codex, ide]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-07-agentic-reconversion-devoxx]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-11
updated: 2026-10-13
---

The harness is everything wrapped around the raw model: a human interface plus a set of tools that give the LLM **knowledge** (CLAUDE.md, skills, wiki, code graph) and **actions** (run builds, deploy, git, restart prod).

## Harness ≠ model

The **model** is the LLM itself (Opus, GPT, DeepSeek) — literally a file of floating-point weights that turns input tokens into output tokens. The **harness** is the program that turns that model into an agent — managing the [[context-window]], injecting the [[system-prompt]], exposing [[tool-calling]], and orchestrating turns. When a colleague says "we use Claude," ask whether they mean the model or the harness: they are separable and independently swappable. <span style="color:red">The metaphors used in sessions: **the LLM is the brain** (floating on a distant server), **the harness is the body and hands** you put it into — or the JRE vs the JDK; the CLIs and MCPs it calls are **the tools** (hammer, chainsaw). *"Copilot = an Anthropic brain with Microsoft hands"*; and *"there is no LLM called Copilot"* — Copilot CLI is a model-agnostic harness proxying OpenAI and Anthropic models, switched with `/model` ([[2026-07-09-ai-live-casino]], [[2026-07-20-ai-telecom]], [[2026-08-06-ai-bank]], [[2026-07-03-ai-bank]]). Plan/manual/auto are **permission modes, not harnesses**; Claude Code itself ships as CLI, Desktop app and VS Code plugin ([[2026-09-30-ai-enterprise]]).</span>

Swapping one for the other is the whole point of [[claude-code-router]]: same harness, different model underneath. <span style="color:red">Cross-harness project memory is standardized as [[agents-md|AGENTS.md]] — the portable bridge for Claude Code is now a `CLAUDE.md` containing `@AGENTS.md` rather than a symlink, which breaks on Windows ([docs](https://code.claude.com/docs/en/memory#agents-md)).</span> Hooks, skills, and permissions remain harness-specific and don't port. <span style="color:red">Neither do plugins: only [Claude Code](https://code.claude.com/docs/en/plugins) and [Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-cli-plugins) support them (not Codex, not Gemini CLI); and Copilot reads `AGENTS.md` and its own `.github/` config, **not `.claude/commands`** ([[2026-09-16-ai-safety-critical]], [[2026-09-18-ai-agentic-how]]).</span>

Examples named in the field: **Claude Code**, **Cursor**, **Codex**, **OpenCode**, and **Glean** (an in-house "ChatGPT" you type *into* — you plug in codebases, Slack, and Jira, and it speaks [[model-context-protocol]]). <span style="color:red">The list keeps growing: Windsurf, [[kiro|Kiro]], Antigravity, Claude Desktop, [Pi](https://github.com/earendil-works/pi), and enterprise harnesses such as **IBM Bob** — whose system prompt is ≈9k tokens before your first message (vendor instructions, tool and MCP definitions, skills, AGENTS.md, per-mode `.bob` rules), with **custom modes scoped to a file type** acting as specialist personas; Bob spawns few sub-agents by default, so ask explicitly, and toggle unused MCP servers off ([[2026-10-13-ai-bank]]). Cursor's distinctive advantage is juggling models from several providers in one session; Claude Code is single-provider by design ([[2026-06-08-ai-security-tools]]).</span> Orchestration features are usually built around the model a harness ships with, so they don't always port.

## The harness matters as much as the model

**The harness matters as much as the model.** The same model in a better harness gives better results — e.g. Sonnet inside Claude Code beats Sonnet inside a weaker harness, largely because of the invisible harness prompt the CLI injects (part of the [[system-prompt]] payload). <span style="color:red">Two more reasons emerged: **models are trained inside their own harness** — Claude models on Claude Code — so the same Opus inside Copilot turns clumsy, with bad tool calls; and **the same model isn't even the same size** across harnesses: in spring 2026, Opus inside GitHub Copilot was capped at ≈160–200k context vs 1M in Claude Code — *"an Opus five times dumber"* ([[2026-04-16-ai-safety-critical]], [[2026-09-18-ai-agentic-how]], [[2026-09-28-ai-gaming-tech]]).</span>

<span style="color:red">What the harness actually adds, concretely:</span>
- <span style="color:red">**Hidden mini-prompts.** Claude Code's leaked source (April 2026) showed hundreds of them enriching every request; it wraps your prompt with git status and project info, so the same bare "hi" does far better than in Copilot CLI ([[2026-07-01-ai-java-community]]).</span>
- <span style="color:red">**A prompt that pushes forward.** An IDE chat's harness prompt assumes a human is watching and yields; a CLI's pushes on (e.g. [Codex's prompt.md](https://github.com/openai/codex/blob/ecc78e4cf5607ecf5f080d682eae0ecb650868ae/codex-rs/models-manager/prompt.md#L125)) — which is why a CLI is more autonomous. *"The model is the brain, the harness is the organism that finds the skills"* ([[2026-10-01-ai-bank]]).</span>
- <span style="color:red">**Efficient exploration.** Claude Code machine-guns grep/find/sed and loads only the relevant fragments, where Copilot's agent mode was verbose and wasteful with a small window ([[2026-04-07-ai-product-team]]).</span>
- <span style="color:red">**Sub-agent orchestration**, a CLI-only superpower: Claude Code and Copilot CLI spawn [[sub-agents]]; the Copilot IDE plugin cannot (a live attempt "succeeded" with nothing in the UI).</span>

The working ranking: **CLI > IDE plugin**, because CLIs are easier to unit-test and ship ≈3 months ahead of the plugins (the IDE plugin is the weakest harness). <span style="color:red">Innovation appears first in the CLI (Claude Code CLI before Desktop, Copilot insiders before the plugin) because a CLI is trivially testable — text in, text out — and Anthropic builds Claude Code *with* Claude Code (≈90% of it, per Boris Cherny, its creator), shipping features almost daily. "Every time there's a new model release, we delete a bunch of code" — but it is not rewritten from scratch every quarter ([Pragmatic Engineer interview](https://newsletter.pragmaticengineer.com/p/how-claude-code-is-built); [[2026-08-31-ai-software-vendor]]). Only a CLI can be put into a container or the cloud — see security below.</span> "Which model" (see [[model-hierarchy]]) and "which harness" are two independent knobs. The test: can you write an automated test for what the harness will do? If not, you can't iterate on it.

## <span style="color:red">Harness ranking (autumn 2026)</span>

<span style="color:red">Earlier advice (June 2026) put Claude Code, Codex and Cursor together as preferred. By autumn the ranking was: **Claude Code ≈ Codex, "the kings"**; then **Copilot CLI**, the best of the Copilot family and the best *accessible* harness for Copilot-licensed shops; Cursor, OpenCode and Pi were demoted to *"hype chasing attention"* ([[2026-09-24-ai-bank]]). A spring version for a mixed team read Claude Code > OpenCode > Cline > Copilot ([[2026-05-04-ai-telecom]]).</span>

<span style="color:red">Within Copilot, consistently from July to October:</span>

<span style="color:red">**Copilot CLI > VS Code Agents window > VS Code chat >> IntelliJ + Copilot plugin > any "Ask" mode.**</span>

- <span style="color:red">**The IntelliJ plugin is "nobody's child"** — a JetBrains IDE with a Microsoft plugin; JetBrains pushes [Junie](https://junie.jetbrains.com/) (and is itself moving Junie to delegate to a CLI), Microsoft pushes VS Code — so nobody fixes it. Observed faults: no parallel sessions, no message queue, no autopilot, terminals left lying around, hangs, gives up halfway; one sent ≈47,000 words of context for a one-line question without showing you what it sent. *"If you want software engineers to hate AI, give them Copilot in IntelliJ"* ([[2026-07-30-ai-safety-critical]], [[2026-08-06-ai-bank]], [[2026-09-07-ai-bank]]).</span>
- <span style="color:red">**Practical setup for IntelliJ fans**: VS Code full-screen "like a ChatGPT" on the main screen for agent work, IntelliJ on the side for reading and reviewing the same folder.</span>
- <span style="color:red">**Modes**: *Ask* = copy-paste "slave mode" — it turns you into the AI's waiter ([[chop-ai-waiter]]) and is fading out; *Plan* = debate a monster task without touching code ([[plan-mode]]); *Agent* = actually builds, runs tests and self-corrects. Always Agent, never Ask — Ask only near quota end.</span>
- <span style="color:red">**Ghost text** (inline completion) costs **zero premium credits** but is the weakest model with the narrowest view: in IntelliJ it sees only the current file (it generated Java 17 over a Java 8 pom), in VS Code all open tabs — keep `pom.xml` open. *Prompt-driven typing* when quota runs out: a descriptive comment + Enter, and a *longer* comment rescues it. Turn completions off while thinking deeply ([[2026-09-25-ai-bank-migration]], [[2026-10-01-ai-bank]]).</span>
- <span style="color:red">Many engineers had the Copilot CLI toggle disabled in their [GitHub Copilot settings](https://github.com/settings/copilot/features) and never asked; [Copilot CLI](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli) (`npm i -g @github/copilot`) rides a plain Copilot subscription — *a policy banning it "is worth rioting against"*. Its `/init` writes `.github/copilot-instructions.md` — rename it `AGENTS.md`.</span>
- <span style="color:red">**When the agent disappoints, change the harness first**, don't blame the AI.</span>

### <span style="color:red">Copilot billing (dated)</span>

<span style="color:red">In spring 2026 Copilot billed "premium requests" per prompt (0× = brainless models; 1× = Sonnet; 3× = Opus), so one short prompt running Opus for three hours cost one request; per-token billing was announced from June 2026, and Opus was quietly removed for non-Enterprise plans. One session estimated Copilot at ≈3× the cost of Claude Code on the same model (resale markup). Budget anchors quoted: ≈13,000 credits/month — *"a good coffee a day, not the sea"* — with a full day of demos including Playwright videos at $2–3. Copilot's PR review isn't free: it burns [13 premium requests](https://docs.github.com/en/copilot/concepts/billing/copilot-requests) per review. Approve *a specific harness with specific data access*, not "Claude" ([[2026-04-16-ai-safety-critical]], [[2026-07-30-ai-safety-critical]], [[2026-09-18-ai-agentic-how]], [[token-economy]]).</span>

## <span style="color:red">Don't build your own harness</span>

<span style="color:red">Models are trained on their own harness and change monthly, while your loop — skills, hooks, permissions — stays: *harness beats model*, and you should never write your own. Claude Code already resumes long tasks (`claude --resume`, Docker volumes to persist conversations), natively pauses agents near quota exhaustion, and [`/install-github-app`](https://code.claude.com/docs/en/github-actions) gives you an agentic pipeline on GitHub issues in an hour. Reach for LangChain/LangGraph only when you need determinism you can't get from the agent's "feeling": very large enterprises, high-cost-of-error domains (prod ops, security), custom embeddings, in-house open-weight deployment ([[2026-05-11-ai-software-vendor]], [[determinism-over-llm]]). Likewise **don't build orchestration tooling — the vendors ship it next** (e.g. [teleport](https://code.claude.com/docs/en/claude-code-on-the-web#from-cloud-to-terminal) from cloud session to terminal, [remote control](https://code.claude.com/docs/en/remote-control) from your phone) ([[2026-09-24-ai-bank]]). Boris Cherny re-evaluates CLAUDE.md and skills at every model release — the new model may already know them. Further reading: OpenAI's [*Harness engineering*](https://openai.com/index/harness-engineering) ([[doc-gardener]]).</span>

## <span style="color:red">Working the harness</span>

- <span style="color:red">**Steer in flight instead of interrupting**: a message typed while Claude Code works is [queued](https://code.claude.com/docs/en/interactive-mode#queue-messages-while-claude-works) and injected at the next tool call, not mid-thinking; Escape interrupts. IntelliJ Copilot doesn't support it ([[2026-09-28-ai-gaming-tech]]).</span>
- <span style="color:red">**Run `/compact` yourself before a break** — autocompaction cuts exactly the details you asked for ([[context-window]]).</span>
- <span style="color:red">**A status line** with model, effort, context and live cost (e.g. [victor-statusline](https://github.com/victorrentea/victor-statusline)) exists for both Claude Code and Copilot CLI.</span>
- <span style="color:red">**Hook points** to know: session start, prompt submit, pre/post tool, turn end ([[agent-lifecycle-hooks]]).</span>
- <span style="color:red">**Computer use**: Claude has guardrails on GUI control, so delegate desktop-driving to [Codex](https://developers.openai.com/codex/cli) (it brings its own cursor — "two mice on screen"); for web pages, a Playwright CLI reading the page burns far fewer tokens than screen-driving ([[browser-mcp]]).</span>
- <span style="color:red">**When MCP is disabled by policy**, nothing is lost: go through CLIs (`gh`, `az boards`, a Playwright `browser-cli` skill), and after the agent struggles once, *"build yourself a skill so next time it's cheaper and faster"* ([[cli-vs-mcp-tradeoff]]).</span>
- <span style="color:red">**The model suddenly "became dumb"?** First stop: [status.claude.com](https://status.claude.com/).</span>
- <span style="color:red">**Behind an NTLM corporate proxy**: run a local proxy facade (CNTLM on macOS, WinFoom on Windows) on `localhost:3128` that injects the corporate credentials; point the IDE at it and `export http_proxy=localhost:3128 https_proxy=localhost:3128` for CLI agents ([[2026-07-20-ai-telecom]]).</span>

## <span style="color:red">The harness is a security surface</span>

- <span style="color:red">**Judge a harness by what leaves on the wire**, not by its model: an independent network-traffic analysis (July 2026) reported the Grok Build CLI uploading the **entire repo** regardless of what the agent read, including unread files and secrets ([[2026-09-02-ai-insurer]], [[data-governance]]).</span>
- <span style="color:red">**Security paradox: the IDE plugin is the least secure harness** — only a CLI can be locked in a sandbox ([[docker-sandboxing]], [[os-sandbox]]).</span>
- <span style="color:red">**Conversation history is stored as unencrypted JSON in your home folder** — which is why it can be copied between machines and resumed with `/resume`, and why it's sensitive ([[chat-history]]).</span>
- <span style="color:red">Native harnesses (Claude Code, Codex CLI) usually need a company **zero-data-retention contract** first — a legal, not technical, blocker that can take ≈6 months ([[2026-07-03-ai-bank]]).</span>

## What lives in the harness

Everything that arrives in the model on turn 1 — see [[system-prompt]] for the full inventory: harness/system prompt, your message and its files, built-in plus MCP [[tool-calling]] schemas, [[claude-md]] memory, and the eager-loaded [[agent-skill]] front-matter. The harness is therefore where [[token-economy]], [[progressive-disclosure]], and [[tripwire]] enforcement all live.

## See also
- [[claude-code-router]]
- [[agents-md]]
- [[system-prompt]]
- [[model-context-protocol]]
- [[agent-skill]]
- [[token-economy]]
- [[model-hierarchy]]
- <span style="color:red">[[sub-agents]]</span>
- <span style="color:red">[[chop-ai-waiter]]</span>
- <span style="color:red">[[docker-sandboxing]]</span>
- <span style="color:red">[[data-governance]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[headless-claude]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
