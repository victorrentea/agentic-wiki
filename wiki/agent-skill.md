---
title: Agent Skill
category: concept
tags: [skill, progressive-disclosure, tool-calling, front-matter, claude-code]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-24
---


An agent skill is a markdown file with YAML front-matter whose *description* is eager-loaded into context while its *body* is pulled in on demand — a skill is itself a [[tool-calling|tool]] the agent invokes when it decides the skill is relevant.

## How it loads

The [agent-skills standard](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) is the cross-harness format; Claude Code currently exposes many more front-matter properties than the bare standard. The mechanism is pure [[progressive-disclosure]]:

1. Only the **description** is eager-loaded so the agent knows what it *could* load.
2. When a prompt matches, the agent calls the `skill` tool, which **pastes everything below the front-matter into context**. ("I know Kung Fu" — Neo loads the skill right before the fight.)

The **front-matter `description` is the whole self-loading mechanism** — it's the only thing the agent reads at startup to decide whether to fire. A vague description means the skill *never activates*; write it like search keywords with explicit "Use when…" triggers, not a summary. Skills live at `.agents/skills/<name>/SKILL.md` (project) or `~/.claude/` (global) — install one by pasting its URL and let the agent drop it in (redirect it to the project folder if it defaults to global). Canonical examples: a **pre-push** skill (run all tests, watch CI go green) and the [grilling skill](https://github.com/mattpocock/skills/tree/main/skills/productivity/grilling).

**Skill activation depends on model strength.** Strong models (Opus/Sonnet) exhibit "abstinence" — they wait to load a skill until they genuinely need it. Weak local models (Qwen, Llama) greedily load every referenced file and trash their own context. This is why local models are a poor fit for skill-heavy workflows. Write the description with concrete "Use when…" triggers — too thin a description and a capable model may not activate the skill at all. Activation is driven by semantic match between the trigger conditions and the current task, not raw word count.

## What a skill can bundle

A skill is more than prose — it can carry **extra knowledge files** (a `testing.md` loaded only when writing tests), **scripts**, and **templates** (an example output file). The key rule: when you are attacking a tool's fixed, structured output, **babysit the agent once, then script it — no mercy**. A worked example: rather than keeping a skill that re-parses a JaCoCo coverage file every time (slow, burns Opus, hallucinates), script it once to emit only the uncovered lines in compact `path: lines` form and put the script *inside* the skill. *Use intelligence only where you need intelligence.* This is the same move as [[rtk]].

## Front-matter controls

- `disable-model-invocation: true` keeps an expensive skill (e.g. one that starts the app and pixel-compares Playwright screenshots) from ever auto-firing — it becomes a manual `/`-command only.
- A skill is *your opinionated way* of doing something, not a magic quality upgrade. **Don't blindly adopt a downloaded skill** — use it to learn the delta ("compare my way with this skill and teach me the differences") and build your own.

## Prove the skill helps before shipping it

A skill is only worth keeping if it demonstrably improves outcomes. The rigorous approach: run the same task ≈10 times with and without the skill, score quality and token burn on each run, and look at the spread. One run proves nothing (LLMs are stochastic). See [[skills-benchmarking]] for the full methodology including LLM-as-judge scoring.

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
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-23-ai-garmin]]
