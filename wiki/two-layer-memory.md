---
title: Two-Layer Memory
category: concept
tags: [claude-md, memory, agents-md, communication-style, context-engineering]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-06-25
updated: 2026-10-13
---

The deliberate split of agent memory into a personal home scope (communication style and challenge preferences) and a project scope (domain rules and NFRs), so neither layer bleeds into the other.

## The two layers

| Layer | Location | Owned by | Content |
|---|---|---|---|
| **Home memory** | <span style="color:red">`\~/.claude/CLAUDE.md` (user scope, loaded in every project)</span> | Individual | Who you are, tone, "challenge me when I'm vague," language preference, anti-sycophancy rules |
| **Project memory** | `CLAUDE.md` (project scope) | Team | Domain rules, NFRs, architecture constraints, naming conventions |

<span style="color:red">Earlier versions of this page placed home memory at `\~/.claude/memory.md`; the user-scope instruction file is `\~/.claude/CLAUDE.md` ([[2026-07-23-ai-software-product]]).</span>

See [[claude-md]] for the full CLAUDE.md spec and [[agents-md-discipline]] for the operational discipline around it.

## The third, hidden layer: auto memory

<span style="color:red">Claude Code also keeps an **agent-written** [auto memory](https://code.claude.com/docs/en/memory#auto-memory): a `MEMORY.md` index plus topic files under `\~/.claude/projects/<project>/memory/`, where the agent stores what it learns about you and the project. `<project>` is derived from the git repository, so all worktrees and subfolders share one directory. Only the **first 200 lines or 25KB of `MEMORY.md`, whichever comes first,** load at session start; topic files are read on demand. Audit it with `/memory` (*"let's review what you know about me"*), or [disable it](https://code.claude.com/docs/en/memory#enable-or-disable-auto-memory) ([[2026-09-02-ai-insurer]]).</span>

<span style="color:red">Note the trap: auto memory is *about* the project but lives in **your home folder**, unversioned. Victor wants a higher retention rate from it — have the agent reflect over the session JSONL: *"what do I keep telling you that you don't retain?"*; the hardcore variant is [Hermes Agent](https://github.com/NousResearch/hermes-agent), which reflects overnight and escalates memories ([[self-improving-knowledge]], [[memory-layers]]).</span>

## Why the split matters

Without an explicit home memory, the agent inherits default communication style — often verbose, over-apologetic, and prone to [[sycophancy]]. Defining personal style once in home memory means every new session starts calibrated without re-prompting.

<span style="color:red">The **"digital twin"** framing: an agent that pours sheets of markdown on you, or bores you with basics, isn't dumb — it's a **memory problem**: it doesn't know your level. Put your profile — competences, preferences, aversions, the areas where you're clueless — in user memory ([[2026-08-27-ai-it-services]]). The "tell it you have ADHD, answer in 7 words" hack works, but it is a lie in the profile; say what you actually want instead ("senior dev, skip the basics").</span>

The project `CLAUDE.md` carries team-owned rules that every contributor (human or agent) must follow. Mixing personal taste into `CLAUDE.md` causes friction when teammates have different preferences.

## What belongs where

**Home memory** is deliberately selfish:
- "Always challenge me when my requirement is vague."
- "Be concise — no preamble, no summary of what you're about to do."
- "Match my language (English or French per session)."
- "Never start a response with 'Certainly!' or 'Great question!'"

**Project CLAUDE.md** is team-owned:
- Bounded-context naming rules.
- Which tests must stay green before pushing.
- Approved libraries and banned patterns.
- NFRs (latency, security, data-retention policy).
- <span style="color:red">What the agent *learned* about the project — e.g. production volumetry — goes into the versioned [[agents-md|AGENTS.md]], not its home file ([[2026-08-06-ai-bank]]).</span>

## Output styles: tune how it talks

<span style="color:red">Claude Code ships [output styles](https://code.claude.com/docs/en/output-styles) that raise your reading throughput without touching either memory file ([[2026-09-28-ai-gaming-tech]]): **Concise** when it rambles, **Explanatory** when it talks in riddles, and **Learning**, where it writes ≈90% and leaves you the core logic (*"Victor, your turn"*) — which doubles as a [[skill-erosion]] countermeasure. See also [[caveman]].</span>

## Pitfalls

- Putting personal tone rules in `CLAUDE.md` forces every team member to read and accept them.
- <span style="color:red">The reverse is worse: putting **project knowledge** in your home memory means a colleague who clones the repo starts from zero. *What the project knows is versioned in the project* ([[2026-08-03-ai-telecom-ops-qa]]). Letting the agent write hard-won lessons into your home folder is **selfish** — move durable lessons into project git or a shared [[plugin-marketplace]] repo ([[2026-08-12-ai-bank]], [[2026-08-10-ai-consultancy]]).</span>
- Leaving home memory empty lands the agent in the [[dumb-zone]] — it produces technically correct but communicatively misaligned output.
- Home memory is not a [[memory-layers|persistent chat history]]; it is a standing instruction file, not a log.

## See also

- [[claude-md]]
- [[memory-layers]]
- [[agents-md-discipline]]
- [[sycophancy]]
- [[dumb-zone]]
- <span style="color:red">[[self-improving-knowledge]]</span>
- <span style="color:red">[[skill-erosion]]</span>
- <span style="color:red">[[cross-repo-knowledge]]</span>
