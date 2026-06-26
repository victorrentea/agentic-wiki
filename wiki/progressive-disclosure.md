---
title: Progressive Disclosure
category: pattern
tags: [context-window, skill, claude-md, hook, dumb-zone]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---

Progressive disclosure is the practice of loading knowledge into the agent **only when it becomes relevant**, so the [[context-window]] stays lean and the model never drifts into the [[dumb-zone]].

## Why it matters

The goal is *not* primarily cost — it is keeping the agent out of the dumb zone by not flooding it with knowledge it doesn't currently need. Three techniques, from most discretionary to most enforced:

1. **[[agent-skill]]** — front-matter is eager-loaded; the agent pulls the body *if it decides* it's relevant.
2. **Sub-folder `CLAUDE.md`** — loaded when the agent enters that folder.
3. **`@include` / file reference** — one [[claude-md]] forcibly pulls in another file (e.g. the full guardrail tests, living diagrams, CI drift checks). In Claude Code this is the `@path` import syntax.

## A hook beats discretion

The key insight: **a programmatic hook beats a skill's discretion.** A skill relies on the agent *choosing* to load it — it might pick the wrong one or not know it exists. A sub-folder `CLAUDE.md` plus a hook that force-loads it whenever the agent touches a Java file in that folder is **enforcement**, not a polite suggestion. This is the same enforcement gradient as a [[tripwire]] (a kind requirement the agent may ignore) versus a tool that *makes* the workflow happen, as in [[openspec]].

## See also
- [[agent-skill]]
- [[claude-md]]
- [[context-window]]
- [[dumb-zone]]
- [[tripwire]]
- [[token-economy]]
- [[2026-06-11-ai-playtika]]
