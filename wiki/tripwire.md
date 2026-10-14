---
title: Tripwire
category: pattern
tags: [guardrails, claude-md, hook, soft-rule, onboarding, git-hooks, ci]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-07-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]", "[[2026-07-09-ai-live-casino]]"]
created: 2026-06-11
updated: 2026-10-13
---

A tripwire is a scripted "kind requirement" — a booby-trap that *gently reminds* the agent when it does something you dislike, but lets the agent decide whether to comply — a softer, often better alternative to a [[claude-md]] hard rule.

## Just-in-time correction

The governing idea: tell the AI its code is bad *just in time* for it to fix it — not too early, not too late. When you find a root cause, you can add a rule to `CLAUDE.md`, **or better, script a tripwire**. Example: instead of a CLAUDE.md rule against fully-qualified class names, a script that, on seeing `a.b.c.Foo`, nudges the agent to use the simple name plus an import. Crucially **a tripwire is not a hard rule** — the agent decides, and it has false positives (e.g. a literal string `"A.B.C"`).

<span style="color:red">The sharper contrast drawn later: a **guardrail** is a wall the AI can't break — hard, but brittle against a strong model that looks for a way round; a **tripwire** feeds corrective context the *instant* the agent misbehaves ([[2026-07-23-ai-software-product]]). The agent learns the rule only when it steps on it — *Indiana Jones' tile* ([[2026-08-31-ai-software-vendor]]). That is why tripwires stay **out of CLAUDE.md**: a rule there costs tokens on every turn, a tripwire costs nothing until it springs. *"Catch, don't forbid"* — instead of an always-loaded rule, a hook reports **"push failed: line >120"** only when violated ([[2026-10-09-ai-jax-london]]).</span>

<span style="color:red">Not every trap is a tripwire, though: a regex deny rule the agent can simply ask permission to jump is a weak guardrail; the strong rungs are the sandbox, MCP-only access and credentials kept on the host ([[2026-07-09-ai-live-casino]]; see [[enforcement-ladder]]).</span>

## Script the objective, leave the subjective in prose

<span style="color:red">**What a program can check, you don't write in AGENTS.md — you script it in the build** ([[2026-09-30-ai-enterprise]], [[2026-09-28-ai-gaming-tech]]):</span>
- <span style="color:red">**Line length ≤120** — a grep over the diff in a pre-push hook costs milliseconds; see the [pre-push hook in PetClinic](https://github.com/victorrentea/petclinic/blob/main/.githooks/pre-push) ([[2026-09-07-ai-bank]]).</span>
- <span style="color:red">**A CI grep on `@Autowired`** that fails the build with an **honest message addressed to the agent** — it reads the failure and fixes itself ([[2026-08-31-ai-software-vendor]]).</span>
- <span style="color:red">**Delete rules the linter or build already covers** ("max 120 chars"): a green build *is* the agent's definition of done.</span>
- <span style="color:red">**Don't script the vague ones.** "Methods under 30 lines" is too fuzzy to check honestly, and coding models already lean towards clean code; leave it as prose, or as a language-specific [[agent-skill|skill]] ([[2026-09-07-ai-bank]], [[2026-09-28-ai-gaming-tech]]).</span>

<span style="color:red">This is the [[determinism-over-llm]] principle applied to coding rules: whatever can be checked mechanically should be, so the prompt carries only judgement.</span>

## Don't mention the enemy

<span style="color:red">Naming an anti-pattern in a written rule can *"awaken the heresy"* — the agent starts reasoning, and justifying itself, around the very thing you forbade ([[2026-08-31-ai-software-vendor]]; see [[vibe-heresy]]). A tripwire never mentions it until it is actually there. And remember **GIGO**: the agent imitates the code around it — in an EJB-era codebase it happily generated SQL injection — so the strongest tripwire is cleaner surrounding code ([[light-dark-code]]).</span>

## Where tripwires run

<span style="color:red">Tripwires live in **git hooks** (pre-commit, pre-push) and are mirrored in **CI**, because hooks are skippable (`--no-verify`) or simply never installed ([[2026-10-09-ai-jax-london]]). PetClinic's [GUARDRAILS.md](https://github.com/victorrentea/petclinic/blob/main/GUARDRAILS.md) states the rule as *"no hook without a CI backstop"*. Two practical details ([[2026-10-01-ai-bank]], [[2026-09-30-ai-enterprise]]):</span>
- <span style="color:red">Git hooks must be **activated explicitly after clone**: `git config core.hooksPath .githooks` — or let [Husky](https://typicode.github.io/husky/) install them for the whole team on `npm install`.</span>
- <span style="color:red">**The agent doesn't request review until tests and hooks pass.**</span>

<span style="color:red">Order the checks by cost and by when the agent can still act on them ([[2026-09-28-ai-gaming-tech]], [[2026-07-23-ai-software-product]]): an **LLM review first**, while the agent is still in its smart zone (it beats Sonar on design issues); **cheap linters on the diff, locally** — in Docker, before push — so CI doesn't burn on every push; slow static analysis (a 40-minute Sonar run, Fortify) in CI only. The scanner line-up itself — Sonar, CodeQL, Snyk, Wiz — is covered in [[static-analysis-gauntlet]].</span>

<span style="color:red">Tripwires are easy for an agent to disarm — comment out the test, add `--no-verify` — so the hook scripts and guardrail tests themselves belong under [[codeowners-elders]].</span>

## Where it sits on the enforcement gradient

Tripwires occupy the middle of the spectrum between a polite [[claude-md]] rule (pure discretion) and a tool that *makes* a workflow happen ([[openspec]]'s backing CLI, or a [[progressive-disclosure]] hook). They are how you **grow a CLAUDE.md** responsibly: each new tripwire fixes the reason the AI last went off the rails — onboarding the brilliant-but-junior new colleague — and good ones get promoted to the [[cross-repo-knowledge|central repo]] by PR. <span style="color:red">The earlier framing was a gentle nudge the agent may overrule; by autumn 2026 most tripwires shown in sessions are build checks that **fail the push** — still just-in-time and free until triggered, but no longer optional. The soft variant remains useful where false positives are common.</span>

## See also
- [[claude-md]]
- [[progressive-disclosure]]
- [[openspec]]
- [[cross-repo-knowledge]]
- [[guardrails]]
- [[static-analysis-gauntlet]]
- <span style="color:red">[[enforcement-ladder]]</span>
- <span style="color:red">[[determinism-over-llm]]</span>
- <span style="color:red">[[codeowners-elders]]</span>
- <span style="color:red">[[light-dark-code]]</span>
- <span style="color:red">[[vibe-heresy]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- <span style="color:red">[[2026-07-23-ai-software-product]]</span>
- <span style="color:red">[[2026-08-31-ai-software-vendor]]</span>
- <span style="color:red">[[2026-09-07-ai-bank]]</span>
- <span style="color:red">[[2026-09-28-ai-gaming-tech]]</span>
- <span style="color:red">[[2026-09-30-ai-enterprise]]</span>
- <span style="color:red">[[2026-10-01-ai-bank]]</span>
- <span style="color:red">[[2026-10-09-ai-jax-london]]</span>
- <span style="color:red">[[2026-07-09-ai-live-casino]]</span>
