---
title: AGENTS.md Discipline
category: pattern
tags: [agents-md, claude-md, knowledge-engineering, socratic, retrospective, sub-folder]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-23
new_in: 2026-06-23
---

<span style="color:red">🆕 New in Garmin 2026-06-23</span>

`AGENTS.md` discipline is the curation practice that keeps a project's agent-memory file lean and *earned* — the most important file in your repo right now, and the one most often ruined by dumping into it.

## The anti-dumping rule

The big anti-pattern is pasting your Confluence style guide into `AGENTS.md`. ≈90% of it is already obvious to the model (pure token waste → faster [[dumb-zone]] → higher cost), and most of the remaining ≈5% is your **personal OCD taste**, not a measurably better option. An [ETH Zürich finding](https://agents.md) noted that LLM-generated `AGENTS.md` can *hurt* effectiveness while costing more — so `/init` output is a **starting point, not the destination**. The honest confession behind the rule: *"no one builds me a statue for how polished the code is; it's my selfish desire to polish."*

**The bar for a style rule:** add it only when a **blind team vote (4–5 devs unanimous)** says A is clearly better than B *and* the AI doesn't already treat it as obvious.

## What actually belongs in it

*"What would you tell a brand-new hire on day one?"* — things **not** in the code:
- architecture layout and module semantics;
- target user and mission (*"trails have no GSM signal, so we need offline mode"*);
- **data volumetry** (*"a user can have up to 1,000 trails"* stops naive full-downloads);
- backend latency and caching constraints (*"you may not cache the bank balance"*).

These are the project NFRs the model cannot know from training data — see [[claude-md]] for the same "curate, don't generate" principle and the ≈100-line ceiling.

## The seed-of-heresy Socratic loop

**Trigger: whenever the agent makes you angry.** Don't scold (*"you're wrong, do X"*) — that triggers [[sycophancy]] and teaches nothing. Instead ask *"why?"* up to five times, calmly, to find the **"seed of heresy"** — the one missing assumption — then encode *that* in `AGENTS.md`. Correcting the agent's **output** is one-shot waste; correcting its **assumption** prevents an entire *class* of future mistakes — a multiplicative return on one Socratic session. (Worked example: the agent duplicated rules in root and sub-folder files; "how did you reason?" revealed it feared the nested file wouldn't be injected. A live rule born this way: *"dates sort as dates, never lexicographically."*) **Bonus the devs who enrich `AGENTS.md` the most.**

## Sub-folder injection (context scoped like imports)

Split `AGENTS.md` per sub-folder. The CLI injects a sub-folder's file **only when the agent touches files there** — so Compose/UI rules belong in `/app/AGENTS.md`, not root, or the networking module needlessly pays for "Composables are PascalCase." Mental model: root file = package-level import, sub-folder file = class-level; the harness resolves it by which paths the agent touches, at zero task-time effort. (Smart models like Opus even refuse to open `.md` files they don't need, protecting their own context.)

## Retrospect, and prove rules work

- **Retrospect `AGENTS.md` every sprint** — drop the now-obvious, resolve conflicts (a contradiction inside the file is a direct [[hallucination]] cause).
- **Hardcore proof a new rule works:** revert everything, `/clear`, and re-run the task from scratch to confirm the rule alone changes the outcome — *"don't tell your boss, it costs a fortune."*

`AGENTS.md` is the [[agents-md|cross-tool standard]] file; this page is how to *curate* it. Harness-specific automation (hooks, skills, permissions) still lives alongside it, not inside it.

## See also
- [[agents-md]]
- [[claude-md]]
- [[sycophancy]]
- [[hallucination]]
- [[memory-layers]]
- [[tripwire]]
- [[progressive-disclosure]]
- [[cross-repo-knowledge]]
- [[2026-06-23-ai-garmin]]
