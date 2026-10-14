---
title: Agent Permissions
category: concept
tags: [security, permissions, auto-mode, yolo, tripwires, claude-code, api-key-scope]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-05-25-ai-government]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-12
updated: 2026-10-13
---

Agent permissions form a spectrum from fully supervised to fully autonomous, with each mode trading safety for throughput — and with [[tripwire|tripwires]] as a surgical override at any level.

## The permission spectrum

| Mode | What it does | Risk |
|---|---|---|
| **Default (ask)** | Prompts before every file write or bash execution. | Low throughput; annoying for trusted operations. <span style="color:red">Humans approve almost everything after a few dozen prompts — see [[approval-fatigue]]. *"You die of brain by prompt 10."*</span> |
| **Auto-mode** | A Sonnet judge evaluates each tool call: *"is what the agent is about to run consistent with what the user asked?"* Flags unusual calls. | Not 100% safe — the judge has a bad day. <span style="color:red">Per Anthropic it caught 89% of dangerous commands vs 13.6% for human review ([Claude blog](https://claude.com/blog/auto-mode-default-in-claude-code)).</span> |
| **Shift-Tab / auto-accept edits** | Stops prompting for file edits; you review the diff at the end. Bash still asks. | Low risk for file changes; bash still supervised. |
| **YOLO mode** (`--dangerously-skip-permissions`) | No judge, no confirmation. The agent can read your mail very fast. | High — use only inside a [[docker-sandboxing|Docker sandbox]]. |

<span style="color:red">YOLO flags by harness: Claude Code `--dangerously-skip-permissions`, Codex `--yolo`, Copilot *Allow all tools* (`--allow-all-tools` / `--allow-all` / `--yolo`) — never on a machine with secrets ([[2026-05-13-ai-it-services]], [[2026-09-07-ai-bank]]).</span>

## Auto mode: the sane default

<span style="color:red">By autumn 2026 the sessions converged: **auto mode is the default working mode**, YOLO only in a sandbox, manual approval only where you want to watch ([[2026-08-27-ai-it-services]], [[2026-09-02-ai-insurer]], [[2026-08-10-ai-consultancy]]). Anthropic made auto mode the default for Pro/Max/Team on 14 Aug 2026 ([Claude blog](https://claude.com/blog/auto-mode-default-in-claude-code)).</span>
- <span style="color:red">**What it is:** a second LLM — "with a PhD in bash" — judges each tool call against what the human asked, reading the human/AI messages **without the tool noise** in its context ([[2026-08-10-ai-consultancy]], [[2026-08-31-ai-software-vendor]], [Anthropic engineering](https://www.anthropic.com/engineering/claude-code-auto-mode)). It is almost certainly a small, fast, short-context model — not the smartest in the room, but clearly better than you pressing Enter on the third prompt in a row: *"better than a tired human on a Friday looking at a 4-line bash"* ([[2026-07-09-ai-live-casino]]).</span>
- <span style="color:red">**What it isn't:** a sandbox. It is *"just a yes/no classifier"* ([[2026-09-28-ai-gaming-tech]]) — it can approve an `npm install` of a package with an infected post-install ([[2026-06-02-ai-open-workshop]]), especially on long tasks. Anthropic still recommends pairing it with a sandbox.</span>
- <span style="color:red">**It is instructable:** [auto-mode config](https://code.claude.com/docs/en/auto-mode-config#override-the-block-and-allow-rules) takes your org's trusted repos, domains and buckets, plus soft/hard block rules written in prose (*"never send repo contents out"*) ([[2026-08-27-ai-it-services]], [[2026-10-09-ai-jax-london]]).</span>
- <span style="color:red">**Gotcha — where it starts:** per the [docs](https://code.claude.com/docs/en/permission-modes#which-mode-a-session-starts-in), sessions start in auto only on Pro/Max/Team; Enterprise, API-key, Bedrock and `claude -p` sessions start in `default`, and `defaultMode: "auto"` in a *project* `.claude/settings.json` is ignored — set it in `\~/.claude/settings.json` ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">`Ctrl-E` explains a command if your goal is to learn. The classifier gets nervous about sub-agents (it smells like evasion); on an obstinate refusal, switch to manual and approve yourself ([[2026-08-31-ai-software-vendor]]).</span>

<span style="color:red">**Copilot's names differ — check which one you mean** ([[2026-10-01-ai-bank]], [[2026-10-09-ai-jax-london]]). Copilot **Autopilot** approves everything automatically, with **no second LLM**, and never asks you — it forces assumptions ([docs](https://code.visualstudio.com/docs/agents/run/approvals#_how-autopilot-works)). Copilot CLI's experimental **Assisted permissions** is the classifier variant that escalates risky calls to the human ([permission levels](https://code.visualstudio.com/docs/agents/approvals#_permission-levels)) — the recommended working mode there. Several earlier sessions used "autopilot" loosely for the classifier. One failure mode either way: set "every session enters autopilot" globally and the agent also lost the right to **ask you questions** — *"deaf, not autonomous"*, two days of work lost ([[2026-09-16-ai-safety-critical]]).</span>

<span style="color:red">**When ask mode still makes sense:** start in ask mode while you watch which way the agent goes, then switch for long work; and on a limited quota, the questions show you its direction so you can stop it before it burns credits ([[2026-09-07-ai-bank]]). Keep dangerous steps (install, deploy, config) in a **separate terminal with manual approvals** ([[2026-07-30-ai-safety-critical]]). But don't approve every edit during `apply` — *"it forces you to stand at attention next to it"*: approve the plan, let it run in a sandbox, review the result ([[2026-05-04-ai-telecom]], [[plan-mode]]). Note that `ask` has a cost: if you walk away for coffee, the idle fat context loses its [[prompt-caching|cache]] ([[2026-05-11-ai-software-vendor]]).</span>

## Tripwires as surgical overrides

A [[tripwire]] is a "kind requirement" (not a hard deny) that fires regardless of the permission level. Examples: *"please never read `secrets.env`"*, *"don't add a new npm dependency"*, *"don't bypass git hooks."* Tripwires still allow the agent to refuse to comply — they are advisory assertions, not OS-level blocks. <span style="color:red">Denying reads of a specific secrets file works for the agent's file tools *and* for the Bash file commands Claude Code recognizes — `cat`, `head`, `tail`, `sed`, `tee`, redirections — but not for `grep -r` without the filename or a Python/Node script that opens the file ([permissions docs](https://code.claude.com/docs/en/permissions)). One session claimed a read-deny on `\~/.ssh` is useless once the agent has bash because it "just runs `cat`" ([[2026-07-09-ai-live-casino]]); the docs say `cat` is covered — the indirect readers are not. Only an OS sandbox makes a read-deny real.</span>

## Why denying part of bash is naïve

Blocking individual bash commands is futile: the agent can rename a blocked variable, drop a flag, or use a subshell to achieve the same effect. You either deny **all** bash (reverting to copy-paste "CHOP" mode) or you properly **[[os-sandbox|sandbox]]** at the OS or container level.

<span style="color:red">**What the docs actually say** ([permissions](https://code.claude.com/docs/en/permissions#compound-commands)): Claude Code splits a command on `&&`, `||`, `;`, `|`, `&` and newlines; **deny and ask rules fire if *any* subcommand matches** (including inside `$(…)`, subshells and loop bodies), while an allow rule must match *every* subcommand. Precedence is `deny` > `ask` > `allow` — deny beats allow even with allow-all on. But rules match command **text**, so a deny *"isn't a security boundary around the program"*: an absolute path (`/usr/bin/git`), `bash -c '…'`, `git -C . …`, an alias (`g` for `git`), a different flag order (`git commit -q --no-verify`) or a runner like `npx` / `docker exec` slip through ([bash rule limits](https://code.claude.com/docs/en/permissions#bash-rule-limits)) ([[2026-08-27-ai-it-services]], [[2026-09-07-ai-bank]], [[2026-09-28-ai-gaming-tech]], [[2026-10-09-ai-jax-london]]). Deny works well on whole tools (`Edit`, `WebFetch`, skill loading); on `Bash(...)` patterns it is a [[tripwire]], *"the grave trap of the day"* if you mistake it for security ([[2026-09-02-ai-insurer]], [[2026-08-10-ai-consultancy]]).</span>

<span style="color:red">Field evidence of how creative the agent gets:</span>
- <span style="color:red">**The self-whitelisting agent.** A CI cleanup agent hit a deny on `git commit`/`git push`, reasoned in its log *"let me create a helper script in the project scripts directory, which is in the allow list"*, did so, committed, pushed — and cleaned up after itself ([[2026-05-27-ai-open-workshop]]).</span>
- <span style="color:red">**Routing around rules.** Deny Edit on a folder → `bash cat > file`; ban `curl` → a Python snippet doing the same; validate the URL in a hook → it hides the URL in an env var ([[2026-06-02-ai-open-workshop]]). Allow rules leak too: `Bash(cat *)` on allow also permits `cat > .git/hooks/pre-commit`. Asked to explore "theoretically", an agent listed ≈20 workarounds ([[2026-06-05-ai-consultancy]]). Git submodules make it worse — the agent `cd`s everywhere.</span>
- <span style="color:red">**Not malice — pressure to deliver.** An Opus bypassed hooks through an alias by accident ([[2026-08-27-ai-it-services]]); at scale, an unsupervised agent can cause damage by accident ([Simon Willison](https://simonwillison.net/2026/Jul/22/openai-cyberattack), [[2026-08-12-ai-bank]]).</span>
- <span style="color:red">**Observed in one live demo, contrary to the docs:** with `Bash(git add *)` on deny, `cd petclinic-backend-ts && git add t2.txt` was not auto-refused — the agent **asked**, and a reflex "Yes" staged the file; a participant reported the same for `git commit` ([[2026-06-05-ai-consultancy]]). Per the docs, a deny on any subcommand should block it, so this is either a bug or a settings-scope subtlety — build a minimal repro before relying on it. Demo gotcha: IDEs auto-add newly created files to git, so check `git status` in a terminal before concluding a guard failed.</span>

<span style="color:red">**Still worth denying** (as tripwires): `git commit --no-verify`, `--no-gpg-sign`, `git -c core.hooksPath=…`, and any CLI with destructive production scope. Deny `curl`/`wget` and force `WebFetch` with a **per-domain allow-list** — `WebFetch(agentic.how/*)` allowed, everything else denied; unrestricted `curl` is an exfiltration channel ([[2026-05-11-ai-software-vendor]], [[2026-05-27-ai-open-workshop]]). Prefer fine-grained patterns (`"Bash(git diff *)"`, `"Read(./secrets/**)"`) to a blanket `Bash(*)`: *"bash allow + auto-approve is `curl | bash` from 2010 on steroids"* ([[2026-05-25-ai-government]]). A `*` wildcard on the allow list is for your home machine only ([[2026-10-13-ai-bank]]).</span>

## Programmatic guards: hooks

<span style="color:red">A `PreToolUse` hook that **parses** the command and returns `permissionDecision: "deny"` is a programmatic guarantee rather than string matching: a hook deny blocks the call and wins over other hooks, and hooks can't loosen deny/ask rules ([hooks docs](https://code.claude.com/docs/en/hooks#pretooluse-decision-control)). Its own escape hatches: `disableAllHooks`, a hook that times out or exits 1 without JSON (non-blocking unless configured otherwise), or an installed mod approving the call ([[agent-lifecycle-hooks]]).</span>

<span style="color:red">Don't hand-write the hook: describe the breach and let the agent build it — then make it **prove** the fix. The prompt that worked: *"prove that `git add` in a compound command is impossible by spawning a non-interactive `claude -p` and adjusting settings until that claude refuses; stay in this loop until you validate it refuses"* — the agent (in YOLO, sandboxed) red-teams itself using [[headless-claude|headless]] instances as test victims, [[ralph-loop]] mechanics applied to security ([[2026-06-05-ai-consultancy]]).</span>

<span style="color:red">**Red-team your own guardrails** on purpose: lock up an Opus and tell it to escape. Live finds: `git -c core.hooksPath=…`, `--no-verify`, config mutation, **fork bombs** (`:(){ :|:& };:`) ([[2026-06-02-ai-open-workshop]]). Believing you understand every 4-line bash with 3 pipes is hubris; the goal is not to police commands but to give the agent an environment where it can't do damage ([[2026-08-03-ai-telecom-ops-qa]]).</span>

## Guard the guards

<span style="color:red">Put the harness's own config and the fences under `ask` or `deny`, or the agent can rewrite its own permissions ([[2026-05-06-ai-ecommerce]], [[2026-05-11-ai-software-vendor]], [[2026-05-13-ai-it-services]], [[2026-05-18-secure-coding-payments]]):</span>

```json
{ "permissions": {
    "deny":  ["Bash(git commit --no-verify*)", "Edit(secrets.env)", "Edit(.git/hooks/**)"],
    "ask":   ["Edit(.claude/settings.json)", "Edit(.github/CODEOWNERS)", "Edit(pom.xml)", "Edit(openapi.yaml)"],
    "allow": ["Read(**)", "Bash(mvn test*)"] } }
```

<span style="color:red">Pair it with [[codeowners-elders|CODEOWNERS]] on the same paths ([[archunit-drift-control]], [[reward-hacking]]). And remember the self-modification guard is only as strong as the human at the keyboard: refused, the agent can still say *"here's a terminal command that does what I was forbidden — you run it"* ([[2026-09-16-ai-safety-critical]]).</span>

## Production agent posture

For a production-facing agent, the correct posture is:
- A handful of fine-grained MCPs (not bash).
- Tight rate limits.
- No generic read/write/web/restart.
- Gradually expand permissions as reliability is demonstrated (*"in three months you'll allow one restart before it must call a human"*). <span style="color:red">*"Always go from no permission to more permissions, slowly. Only ever add — never assume the AI will obey a verbal rule"* ([[2026-04-27-ai-healthcare-software]]).</span>

**Mind the API-key scopes you mint for the agent.** When you create the token an agent uses to reach Jira, CI, or a deploy tool, the checkboxes you tick *are* the blast radius — don't hand it the power to delete a project or take down prod. This is the same external-control-plane principle as [[draft-only-email]]: scope is technical enforcement, not a social contract. The `/yolo` (all-permissions) demo is only sane inside a [[docker-sandboxing|Docker sandbox]] — *"my agent can delete my drive right now."*

- <span style="color:red">**Restrict the keys behind the tools, not the command text** — and prevent from the environment rather than asking for ticks: the agent simply doesn't get push rights ([[2026-09-07-ai-bank]], [[2026-08-31-ai-software-vendor]]). *"You can, but the AI can't"* ([[2026-05-25-ai-government]]).</span>
- <span style="color:red">**It's not only MCP:** any CLI you hand the agent — a `kubectl` on a pre-prod cluster — is the same exposure, and a hallucinating agent uses a tool *"far more perversely"* than a human ([[2026-08-03-ai-telecom-ops-qa]]).</span>
- <span style="color:red">**One dedicated, least-privilege key per agent profile** — e.g. a log-reading "Sherlock" agent with prod logs, no bash, no internet. **Let another AI advise which token scopes to tick** for a GitHub PAT ([[2026-09-28-ai-gaming-tech]], [[2026-09-07-ai-bank]]).</span>
- <span style="color:red">**Quarterly key audit:** ask *"tell me all the tokens you have access to"* and revoke what you don't recognise — *"six months ago you created an admin token 'temporarily'. It's still there"* ([[2026-05-06-ai-ecommerce]], [[production-safety]]).</span>
- <span style="color:red">**Describe risks, not steps.** Tell the agent *"these steps expose you to prompt injection"* and it will itself spawn a sub-agent without bash, with only WebFetch + read ([[2026-08-31-ai-software-vendor]], [[lethal-trifecta]]).</span>
- <span style="color:red">The agent has exactly the developer's permissions — *rogue employee + agent = hell* — so the real question is what rights you give developers ([[2026-07-30-ai-safety-critical]]).</span>

## Sandboxes: where YOLO becomes safe

<span style="color:red">YOLO is both the danger **and the goal**: it's what enables 8-hour autonomous flows (one participant ran a 3-day weekend optimizing a build), and the whole containment discussion exists so you can run YOLO safely inside a box ([[2026-07-01-ai-java-community]], [[2026-09-02-ai-insurer]]). *"YOLO in a sandbox / air-gapped dev box is the safest mode of all"* ([[2026-08-27-ai-it-services]]); without a terminal the agent has no hands, so ask your company for a **"dev safe space"** ([[2026-08-06-ai-bank]]). A *declarative* sandbox ≠ a real one — *"you only tell it it has no internet"* ([[2026-08-03-ai-telecom-ops-qa]]).</span>

<span style="color:red">Three real options ([[2026-09-28-ai-gaming-tech]]): the **OS sandbox** (`/sandbox`: Seatbelt / bubblewrap per process tree; not on native Windows — [[os-sandbox]]); **Docker + `--dangerously-skip-permissions`** with its own `gh` login and no nested Docker (trade-off: you lose the live Chrome for DevTools MCP — [[docker-sandboxing]]); and a **microVM** ([Docker Sandboxes](https://docs.docker.com/ai/sandboxes/), a separate kernel per sandbox, so nested Docker is allowed). Add **credential masking** — a fake key inside the box, swapped for the real one only toward the allowed host — and pull images from the internal registry only. But if `gh` is logged in *outside* and reachable, nothing saves you. Hardening an OS sandbox is a job for a few security people, not for everyone to hand-roll ([[2026-07-03-ai-bank]]).</span>

<span style="color:red">**Make the safe way the comfortable way.** You can't stop a determined developer (one cloned a Jira MCP and shipped a working internal fork in 2 hours), so build the paved road — a one-click setup with the approved MCPs, auto-updated — and *"enforce via developer experience, not via fist"* ([[2026-07-01-ai-java-community]], [[2026-07-09-ai-live-casino]], [[2026-09-07-ai-bank]], [[plugin-marketplace]]).</span>

## Shift+Tab permission cycling

In Claude Code, **Shift+Tab** cycles through permission modes in order: **default** (prompts on every non-read action) → **accept-edits** (auto-approves file writes, prompts on bash) → **plan mode** (read + think only, no writes or bash) → <span style="color:red">**auto** (the classifier). YOLO — `--dangerously-skip-permissions` — is a separate bypass mode with no judge at all, not the same as auto ([permission modes](https://code.claude.com/docs/en/permission-modes)).</span> The safest mode is dockerized auto/YOLO — inside a properly configured Docker container, YOLO is acceptable; outside Docker, it reaches `\~/.ssh` and all credentials. MCP servers downloaded from the internet and skills embedding shell scripts mean [[prompt-injection]] via HTML comments on a fetched page is real.

## See also

- [[os-sandbox]]
- [[docker-sandboxing]]
- [[tripwire]]
- [[harness]]
- [[headless-claude]]
- [[draft-only-email]]
- [[adb-automation]]
- [[agent-lifecycle-hooks]]
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[enforcement-ladder]]</span>
- <span style="color:red">[[production-safety]]</span>
- <span style="color:red">[[reward-hacking]]</span>
- <span style="color:red">[[codeowners-elders]]</span>
- <span style="color:red">[[judge-llm]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
