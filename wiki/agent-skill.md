---
title: Agent Skill
category: concept
tags: [skill, progressive-disclosure, tool-calling, front-matter, claude-code]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---


An agent skill is a markdown file with YAML front-matter whose *description* is eager-loaded into context while its *body* is pulled in on demand — a skill is itself a [[tool-calling|tool]] the agent invokes when it decides the skill is relevant.

## How it loads

The [agent-skills standard](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) is the cross-harness format; Claude Code currently exposes many more front-matter properties than the bare standard. The mechanism is pure [[progressive-disclosure]]:

1. Only the **description** is eager-loaded so the agent knows what it *could* load. Keep it **short** — padding it so weaker models notice the skill is wasteful.
2. When a prompt matches (ask "test myService in Java" and the agent sees "Java"), the agent calls the `skill` tool, which **pastes everything below the front-matter into context**. ("I know Kung Fu" — Neo loads the skill right before the fight.)

## What a skill can bundle

A skill is more than prose — it can carry **extra knowledge files** (a `testing.md` loaded only when writing tests), **scripts**, and **templates** (an example output file). The key rule: when you are attacking a tool's fixed, structured output, **babysit the agent once, then script it — no mercy**. A worked example: rather than keeping a skill that re-parses a JaCoCo coverage file every time (slow, burns Opus, hallucinates), script it once to emit only the uncovered lines in compact `path: lines` form and put the script *inside* the skill. *Use intelligence only where you need intelligence.* This is the same move as [[rtk]].

## Front-matter controls

- `disable-model-invocation: true` keeps an expensive skill (e.g. one that starts the app and pixel-compares Playwright screenshots) from ever auto-firing — it becomes a manual `/`-command only.
- A skill is *your opinionated way* of doing something, not a magic quality upgrade. **Don't blindly adopt a downloaded skill** — use it to learn the delta ("compare my way with this skill and teach me the differences") and build your own.

## See also
- [[progressive-disclosure]]
- [[claude-md]]
- [[harness]]
- [[tool-calling]]
- [[rtk]]
- [[tripwire]]
- [[grill-me]]
- [[2026-06-11-ai-playtika]]
