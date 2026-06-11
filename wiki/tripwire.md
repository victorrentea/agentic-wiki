---
title: Tripwire
category: pattern
tags: [guardrails, claude-md, hook, soft-rule, onboarding]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

A tripwire is a scripted "kind requirement" — a booby-trap that *gently reminds* the agent when it does something you dislike, but lets the agent decide whether to comply — a softer, often better alternative to a [[claude-md]] hard rule.

## Just-in-time correction

The governing idea: tell the AI its code is bad *just in time* for it to fix it — not too early, not too late. When you find a root cause, you can add a rule to `CLAUDE.md`, **or better, script a tripwire**. Example: instead of a CLAUDE.md rule against fully-qualified class names, a script that, on seeing `a.b.c.Foo`, nudges the agent to use the simple name plus an import. Crucially **a tripwire is not a hard rule** — the agent decides, and it has false positives (e.g. a literal string `"A.B.C"`).

## Where it sits on the enforcement gradient

Tripwires occupy the middle of the spectrum between a polite [[claude-md]] rule (pure discretion) and a tool that *makes* a workflow happen ([[openspec]]'s backing CLI, or a [[progressive-disclosure]] hook). They are how you **grow a CLAUDE.md** responsibly: each new tripwire fixes the reason the AI last went off the rails — onboarding the brilliant-but-junior new colleague — and good ones get promoted to the [[cross-repo-knowledge|central repo]] by PR.

## See also
- [[claude-md]]
- [[progressive-disclosure]]
- [[openspec]]
- [[cross-repo-knowledge]]
- [[guardrails]]
- [[static-analysis-gauntlet]]
- [[2026-06-11-ai-playtika]]
