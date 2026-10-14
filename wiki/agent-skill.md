---
title: Agent Skill
category: concept
tags: [skill, progressive-disclosure, tool-calling, front-matter, claude-code]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-11
updated: 2026-10-13
---

An agent skill is a markdown file with YAML front-matter whose *description* is eager-loaded into context while its *body* is pulled in on demand — a skill is itself a [[tool-calling|tool]] the agent invokes when it decides the skill is relevant.

## How it loads

The [agent-skills standard](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) is the cross-harness format; Claude Code currently exposes many more front-matter properties than the bare standard. <span style="color:red">Anthropic launched Agent Skills in October 2025 as an open standard; other harnesses copied it within 3–4 months, so the same skill folder now works in Claude Code, Copilot, Codex, Cursor and [[kiro]] ([[2026-06-05-ai-consultancy]], [[2026-08-12-ai-bank]]).</span> The mechanism is pure [[progressive-disclosure]], <span style="color:red">in three levels ([Anthropic](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)):</span>

1. Only the **description** is eager-loaded so the agent knows what it *could* load.
2. When a prompt matches, the agent calls the `skill` tool, which **pastes everything below the front-matter into context**. ("I know Kung Fu" — Neo loads the skill right before the fight.)
3. <span style="color:red">Files the body references — extra `.md` knowledge, scripts, templates — load **only if the body asks for them**; even plain markdown links are read on demand ([[2026-08-13-ai-insurer]], [[2026-10-09-ai-jax-london]]).</span>

<span style="color:red">Official sizing ([overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview), [Claude Code skills](https://code.claude.com/docs/en/skills)): ≈100 tokens of metadata per skill, a body under 5k tokens, and "keep `SKILL.md` under 500 lines" — push detail into sibling files. The ≈50–300-line ranges quoted in sessions are trainer heuristics, not limits.</span>

<span style="color:red">Why a skill rather than an `@include`d markdown: **visibility** (activations show up in the transcript, so you can retrospect which fired and whether they helped), **context isolation** (a skill can fork into its own sub-agent or pin a different model), and **versioned distribution** as a plugin ([[2026-05-11-ai-software-vendor]]). Ask Claude to **review your CLAUDE.md and move task-specific rules into skills** — memory loads every time, skills on demand ([[2026-04-07-ai-product-team]], [[claude-md]]).</span>

## Writing the description

The **front-matter `description` is the whole self-loading mechanism** — it's the only thing the agent reads at startup to decide whether to fire. <span style="color:red">The name is irrelevant ([[2026-08-06-ai-bank]]).</span> A vague description means the skill *never activates*; write it like search keywords with explicit "Use when…" triggers, not a summary.

<span style="color:red">**Length — the facts vs the folklore.** Earlier versions of this page said "keep it to ≈60 tokens"; sessions since then quoted floors of 30 tokens, 60 tokens, 20–40 words, 60–100 words and 60–300 words. There is **no documented minimum**. The hard limits are on the maximum: **1,024 characters** for `description` in the Agent Skills spec, and in Claude Code `description` + `when_to_use` are **truncated at 1,536 characters** in the skill listing ([frontmatter reference](https://code.claude.com/docs/en/skills#frontmatter-reference)). What *is* documented is the failure mode: a thin description and the agent [forgets the skill exists](https://code.claude.com/docs/en/skills#troubleshooting), and Claude tends to **under-activate** skills. So the practical rule across sessions is: treat ≈60 tokens as a **floor**, not a ceiling; write two sentences that **state the trigger twice in different words**; say what it does and when to fire, in the third person; one goal per description; be insistent ("Before touching any Java…") ([[2026-07-09-ai-live-casino]], [[2026-08-27-ai-it-services]], [[2026-09-02-ai-insurer]], [[2026-09-04-ai-software-company]], [[2026-09-28-ai-gaming-tech]], [[2026-10-13-ai-bank]]).</span>

<span style="color:red">**Phrase rules deterministically** — "always / will", not "can / maybe" ("whenever you touch a `.java` file, always read X — no luck involved") ([[2026-07-01-ai-java-community]]).</span>

<span style="color:red">**Test activation immediately.** Open a new conversation with an *indirect* prompt ("review `PetClinicMcp`") and look for `Activating skill (…)` in the transcript; if it's absent, the description is the problem — enrich it rather than shouting. Run 5–6 instances on the same prompt to see how reliably it fires; audit real activations by grepping `Skill` tool calls in `~/.claude/**/*.jsonl`; force a shy skill with `/skill-name` ([[2026-08-06-ai-bank]], [[2026-08-31-ai-software-vendor]], [[2026-09-07-ai-bank]], [[2026-09-16-ai-safety-critical]]). Watch the agent's first ≈10 seconds to see which skills and tools it picks.</span>

**Skill activation depends on model strength.** Strong models (Opus/Sonnet) exhibit "abstinence" — they wait to load a skill until they genuinely need it. Weak local models (Qwen, Llama) greedily load every referenced file and trash their own context. This is why local models are a poor fit for skill-heavy workflows. Write the description with concrete "Use when…" triggers — too thin a description and a capable model may not activate the skill at all. Activation is driven by semantic match between the trigger conditions and the current task, not raw word count.

## How many skills

<span style="color:red">Earlier versions of this page capped skills at ≈20 per agent. Sessions give a range, and two mechanisms explain it ([[2026-04-16-ai-safety-critical]], [[2026-05-11-ai-software-vendor]], [[2026-08-06-ai-bank]], [[2026-09-02-ai-insurer]], [[2026-09-18-ai-agentic-how]]):</span>

- <span style="color:red">**Collisions** — overlapping descriptions make the wrong skill fire or the agent freeze; ≈20 is comfortable, and things go haywire around ≈50 simultaneous front-matters. Keep them **disjoint**; ask the agent "which descriptions overlap? help me refine them".</span>
- <span style="color:red">**Token cost** — every description rides on every request: measured at 60 skills ≈ 3k tokens, 49 ≈ 7k, 102 ≈ 9k. Claude Code gives the whole listing a budget of **1% of the context window**, and the least-used skills lose their descriptions first ([docs](https://code.claude.com/docs/en/skills)). Hence "under 30–50" on token grounds.</span>

<span style="color:red">Victor himself runs only **4** workflow skills — `Grill Me`, `Validate`, `Architect`, `Verify` (the last one starting a separate agent) — because cost grows with the number and granularity of skills ([[2026-08-27-ai-it-services]]). `/skill-doctor` (v2.1.252+) reports what each skill costs and how often it fires ([find unused skills](https://code.claude.com/docs/en/skills#find-unused-skills)); `skillOverrides` (`on` / `name-only` / `off`) hides skills per setting, and a custom sub-agent's [`skills:`](https://code.claude.com/docs/en/sub-agents#preload-skills-into-subagents) list preloads a chosen set. One cleanup halved descriptions and memory — yet "hi" still cost 50k, because the untouched lever was MCP connectors never invoked ([[2026-09-04-ai-software-company]], [[context-window]]).</span>

## Skill, rule or command?

<span style="color:red">**A skill is a polite request; a glob-scoped rule is force-injection.** A skill fires "as the mood takes it", off its description. A [`.claude/rules/*.md`](https://code.claude.com/docs/en/memory#path-specific-rules) file with `paths:` is loaded by the harness whenever Claude reads, edits or writes a matching file — deterministic, "like a hormone"; Copilot's equivalent is `.github/instructions/<name>.instructions.md` with `applyTo:` ([docs](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions)). A `paths:` glob on a *skill* restricts automatic loading to matching files, but activation is still description-driven ([[2026-07-23-ai-software-product]], [[2026-08-12-ai-bank]], [[2026-08-13-ai-insurer]], [[2026-09-18-ai-agentic-how]]). Rule of thumb: **file-path knowledge (language style, `pom.xml` expertise) → rule; task/workflow knowledge → skill** ([[2026-10-13-ai-bank]]). One example keeps Java style out of Angular folders with a path-triggered [java-code-style](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/java-code-style/SKILL.md) skill ([[2026-09-28-ai-gaming-tech]]). Keep force-injected files tiny — they land per matching file, every turn.</span>

<span style="color:red">**Commands are now skills.** A `.claude/commands/*.md` file is a saved prompt that never auto-activates; a skill with `disable-model-invocation: true` is human-only `/name` invocation — "that is what a command is now", so delete duplicate command files. With that flag **even the description stays out of context** until you type `/name` — use it for expensive procedures (one review run costs ≈$20) ([[2026-08-31-ai-software-vendor]], [[2026-09-04-ai-software-company]], [[2026-09-07-ai-bank]]). Slash commands as a separate mechanism are "kind of the past": you must remember they exist and they aren't portable ([[2026-04-27-ai-healthcare-software]]).</span>

<span style="color:red">**Promote to a sub-agent** when the skill calls many tools, runs heavy bash/MCP or very long prompts — for context isolation ([[sub-agents]], [[custom-agent]], [[2026-04-27-ai-healthcare-software]]).</span>

## Where skills live

<span style="color:red">Locations vary by harness and change monthly — check the docs rather than memorising: `.claude/skills/` (project) and `~/.claude/skills/` (global) in Claude Code; `.github/skills/<name>/SKILL.md` in Copilot, which [also discovers `.claude/skills`](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills); `.agents/skills/` in some harnesses; `.kiro/skills/` in Kiro ([[2026-05-13-ai-it-services]], [[2026-07-03-ai-bank]], [[2026-09-02-ai-insurer]]).</span> Install one by pasting its URL and let the agent drop it in (redirect it to the project folder if it defaults to global). <span style="color:red">Shared knowledge goes in a project skill, personal knowledge in a global one; team rules parked in your home folder are selfish — ship them through a [[plugin-marketplace]] ([[2026-09-10-testing-ai-bank]], [[2026-09-18-ai-agentic-how]]).</span> Canonical examples: a **pre-push** skill (run all tests, watch CI go green) and the [grilling skill](https://github.com/mattpocock/skills/tree/main/skills/productivity/grilling).

## When to write one, and how

<span style="color:red">**Write one when** you expect to repeat a task: standard front-end components (one skill per major component), heavy integrations with internal systems (OpenTelemetry, auth, event bus), refactor recipes ([[openrewrite]], mass migrations), and above all **teaching the agent your clunky in-house tool**. **Don't** for one-off tasks, trivial rules (they go in [[agents-md]]) or things the AI already knows ([[2026-04-16-ai-safety-critical]], [[2026-07-23-ai-software-product]]).</span>

<span style="color:red">**Extract, don't author.** Babysit the agent by hand for ≈1 hour ("do it like this, not like that"), then *"look at everything I taught you and extract a skill"* — or after any hard-won success, *"remember all we did in a new skill"* ([[2026-06-08-ai-security-tools]], [[2026-10-13-ai-bank]]). **Every struggle the agent has with the environment becomes a skill** pushed to the internal marketplace ([[2026-09-10-testing-ai-bank]]). Write skills with Opus, not Haiku — "a junior micromanaging a senior" ([[2026-09-18-ai-agentic-how]]). Anthropic's [skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator) generates skills with evals and description tuning, but its output is verbose and needs trimming.</span>

<span style="color:red">**Ask for proof before publishing:** "fetch the last 20 build statuses, a log excerpt and the last 10 failed tests" with real data; `gh auth status` as cheap proof of login. Demonstrating forces the agent through the real path, and it self-corrects on the way ([[2026-09-10-testing-ai-bank]], [[2026-09-16-ai-safety-critical]]).</span>

## Goals, not instructions

<span style="color:red">The strongest lesson of 2026: **describe the goal, the environment and its pitfalls — not steps 1-2-3.** Skills full of orchestration exist only because we don't trust the model: "you're telling the smarter model you'll use tomorrow a plan built by a more stupid model today" ([[2026-07-01-ai-java-community]], [[2026-08-03-ai-telecom-ops-qa]]). Measured rewrites from step-by-step to objectives ([[goals-over-instructions]]):</span>

- <span style="color:red">5,000 → 300 lines, ≈3× faster on half the tokens ([[2026-09-28-ai-gaming-tech]]);</span>
- <span style="color:red">3,000 → 200 lines, half the time, ⅔ the tokens ([[2026-10-09-ai-jax-london]]);</span>
- <span style="color:red">1,000 → ≈200 lines, faster and more correct — "it was a dump of ideas, not a skill"; it invented its own 8 sub-agents ([[2026-09-07-ai-bank]]);</span>
- <span style="color:red">a 1,500-line transcript fan-out skill that still disappointed, rewritten in one go as objectives ([[2026-08-27-ai-it-services]]); 900 → 60–90 lines and 100 → 7 lines "because the agent knows what to do" ([[2026-07-03-ai-bank]]).</span>

<span style="color:red">**Skills rot and bloat.** Agents keep *appending* out of fear of losing something — one `SKILL.md` reached 2,000 lines / 37k tokens; a 900-line skill looped on for days developed contradictions. Periodically have an adversarial agent shrink it, or ask the model what it would *ignore* in the skill. Split into a **fixed part** (environment, goals) and a **variable part** (lessons learned — model-specific, flushed on a model upgrade). Superpowers' [writing-skills](https://github.com/obra/superpowers/blob/main/skills/writing-skills/SKILL.md) budgets < ≈200 words for a frequently loaded skill ([[2026-07-03-ai-bank]], [[2026-09-04-ai-software-company]]).</span>

<span style="color:red">Exception: a skill shared across vendors must be **proven on the weakest model that will run it** — when the reader model is unknown, over-specifying beats under-specifying ([degrees of freedom](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices#set-appropriate-degrees-of-freedom); [[2026-09-28-ai-gaming-tech]]).</span>

## What a skill can bundle

A skill is more than prose — it can carry **extra knowledge files** (a `testing.md` loaded only when writing tests), **scripts**, and **templates** (an example output file). <span style="color:red">Prefer **thin** skills — a small body pointing to files loaded progressively — over **fat** ones with everything in `SKILL.md` ([[2026-05-04-ai-telecom]]).</span> The key rule: when you are attacking a tool's fixed, structured output, **babysit the agent once, then script it — no mercy**. A worked example: rather than keeping a skill that re-parses a JaCoCo coverage file every time (slow, burns Opus, hallucinates), script it once to emit only the uncovered lines in compact `path: lines` form and put the script *inside* the skill. *Use intelligence only where you need intelligence.* This is the same move as [[rtk]].

<span style="color:red">*"Anywhere you can replace LLM tokens with deterministic code, do it"* — a `jq`/`awk` one-liner is tens of tokens and milliseconds vs hundreds of tokens, tens of seconds and non-determinism ([[determinism-over-llm]], [[2026-04-27-ai-healthcare-software]]). Live demo: the agent didn't know Azure DevOps' `az`, read `--help` "like a human" and groped through ≈20 tool calls to fetch PR review comments; "note what you learned in a skill and write a shell script" produced a ≈200-line script, and the next session answered "what are my tasks?" in ≈3 s ([[2026-07-03-ai-bank]]).</span>

- <span style="color:red">**Script = brain, `SKILL.md` = its man page** — e.g. [jira.py](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/jira-cli/jira.py) + [SKILL.md](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/jira-cli/SKILL.md). Wrap any REST API as a script + thin `SKILL.md` with usage examples; on every failure **fix the script, never add steps to the markdown**. Script work is invisible to the agent — only the result comes back ([[2026-09-04-ai-software-company]], [[2026-09-18-ai-agentic-how]], [[2026-09-28-ai-gaming-tech]]).</span>
- <span style="color:red">**Credentials** (a Jira PAT) live in the **home folder — outside git and outside the context**. Copilot refused *"I'll give you a personal access token"*; rephrase it as a concrete task ([[2026-10-01-ai-bank]]).</span>
- <span style="color:red">**MCPs blocked at work? CLIs wrapped in skills** do the job locally — DB, Jira, Confluence, Jenkins, browser ([petclinic skills](https://github.com/victorrentea/petclinic/tree/main/.claude/skills); [[cli-vs-mcp-tradeoff]], [[internal-tool-cli-skill]]). On Windows ask for a **PowerShell twin** of each `.sh`, or you pay the tokens again tomorrow ([[2026-09-16-ai-safety-critical]]).</span>
- <span style="color:red">**Let scripts call the LLM, not an LLM wrap your scripts** — pipelines derail less ([[2026-10-09-ai-jax-london]]).</span>
- <span style="color:red">Browser automation: *"click first, click twice, then script it, and prove it works — including the bot detection"*; save the discovered selectors as Playwright snippets inside the skill. Scraping third-party sites stays a personal toy — it breaks on the next layout change ([[2026-04-27-ai-healthcare-software]]).</span>
- <span style="color:red">Expensive manual skills that pay off: `regen-user-manual` drives headless Playwright through the app, screenshots and describes each screen, and writes a user manual — regenerating only the screens touched by this release's PRs — ideal for ISO/security documentation nobody wants to write ([[2026-07-01-ai-java-community]], [[2026-07-23-ai-software-product]], [[2026-09-24-ai-bank]]). A YouTube-transcript skill that deep-links the seconds worth watching beats watching ([[2026-10-09-ai-jax-london]]).</span>

## Front-matter controls

- `disable-model-invocation: true` keeps an expensive skill (e.g. one that starts the app and pixel-compares Playwright screenshots) from ever auto-firing — it becomes a manual `/`-command only.
- <span style="color:red">`paths:` (globs that restrict where it may auto-load), `context: fork` (run as an isolated sub-agent), a pinned **model** (Opus for review), and tool lists. One session reported that **`allowed-tools` only skips the permission prompt** for those tools while **`disallowed-tools` is the real restriction**; restricting tools per skill is the main defence against injection inside a downloaded body. These fields are Claude-Code-only — Copilot lacks them ([[2026-05-27-ai-open-workshop]], [[2026-07-23-ai-software-product]], [[agent-permissions]]).</span>
- <span style="color:red">**Extend a team skill without forking it:** a `PreToolUse`/`PostToolUse` [hook](https://code.claude.com/docs/en/hooks) on the Skill tool pours a local `x-extension.md` over the shared skill when it activates. Double-edged: your additions can contradict the master skill and "produce tension in the model" ([[2026-07-01-ai-java-community]], [[2026-08-27-ai-it-services]], [[2026-09-16-ai-safety-critical]], [[agent-lifecycle-hooks]], [[cross-repo-knowledge]]).</span>
- A skill is *your opinionated way* of doing something, not a magic quality upgrade. **Don't blindly adopt a downloaded skill** — use it to learn the delta ("compare my way with this skill and teach me the differences") and build your own. <span style="color:red">Run foreign skills as a **critique** of your own code. Karpathy (Jan 2026) "stopped teaching people, started giving skill.md": hunt skills published by domain experts, and read them the way you used to read CS books ([[2026-07-01-ai-java-community]], [[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]]). Catalogues to crib structure and wording from: [agentskills.io](https://agentskills.io/home), [skills.sh](https://www.skills.sh/).</span>

## Skill marketplace security

Community skills are a [[supply-chain-attack|supply-chain]] surface. A skill can embed arbitrary scripts that the agent executes. <span style="color:red">Snyk's ToxicSkills scan of 3,984 skills from ClawHub and skills.sh (Feb 2026) found **36.8%** with at least one security flaw, **13.4%** with a critical issue and **76 confirmed malicious payloads** ([Snyk](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/)).</span> The skill marketplace is at *"npm circa 2015"* — treat any community skill like an unknown npm package: read it before installing, cap installs from any marketplace at ≈10, prefer Anthropic-published skills, and <span style="color:red">vet others with a scanner such as NVIDIA's open-source [SkillSpector](https://github.com/NVIDIA/SkillSpector) or [Snyk skill-scan](https://labs.snyk.io/experiments/skill-scan/) — then read them yourself.</span> <span style="color:red">**Marketplaces outside your company auto-update** — a rug-pull path ([[tool-poisoning]], [[2026-08-13-ai-insurer]]). And the bigger risk of a downloaded skill may be **cognitive depth**, not security: a skill you never read is driving your agent and you're "offside" — read it line by line, **in mob** ([[2026-07-23-ai-software-product]]). "Volume of markdown or Python doesn't impress — the author didn't read it" ([[2026-10-01-ai-bank]]).</span> See [[skill-marketplace-security]] for the full threat model.

## Prove the skill helps before shipping it

A skill is only worth keeping if it demonstrably improves outcomes. The rigorous approach: run the same task ≈10 times with and without the skill, score quality and token burn on each run, and look at the spread. One run proves nothing (LLMs are stochastic). See [[skills-benchmarking]] for the full methodology including LLM-as-judge scoring. <span style="color:red">Claude Code documents an [evaluate-and-iterate loop](https://code.claude.com/docs/en/skills#evaluate-and-iterate-on-a-skill) and [skill-creator evals](https://code.claude.com/docs/en/skills#run-evals-with-skill-creator); teams gate marketplace entries on a minimum of **3–5 runs** with and without, and publish only **general-purpose, not opinionated** skills — otherwise endless bike-shedding. It isn't free: one statistical skill eval cost ≈$300 in an hour ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-27-ai-it-services]], [[2026-09-24-ai-bank]], [[skills-governance]]).</span>

## See also
- [[progressive-disclosure]]
- [[claude-md]]
- [[agents-md]]
- [[harness]]
- [[tool-calling]]
- [[rtk]]
- [[tripwire]]
- [[grill-me]]
- [[skills-governance]]
- [[custom-agent]]
- [[skills-benchmarking]]
- [[skill-marketplace-security]]
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[goals-over-instructions]]</span>
- <span style="color:red">[[determinism-over-llm]]</span>
- <span style="color:red">[[internal-tool-cli-skill]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[tool-poisoning]]</span>
- <span style="color:red">[[superpowers]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
