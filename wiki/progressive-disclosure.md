---
title: Progressive Disclosure
category: pattern
tags: [context-window, skill, claude-md, hook, dumb-zone]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-10-01-ai-bank]]"]
created: 2026-06-11
updated: 2026-10-13
---

Progressive disclosure is the practice of loading knowledge into the agent **only when it becomes relevant**, so the [[context-window]] stays lean and the model never drifts into the [[dumb-zone]].

## Why it matters

The goal is *not* primarily cost — it is keeping the agent out of the dumb zone by not flooding it with knowledge it doesn't currently need. <span style="color:red">The first step is subtractive: clean out the AI-generated memory file before adding any mechanism ([[2026-06-02-ai-open-workshop]]). Then the techniques, from most discretionary to most enforced:</span>

1. **[[agent-skill]]** — front-matter is eager-loaded; the agent pulls the body *if it decides* it's relevant. <span style="color:red">Skill activation is not deterministic, so nothing that *must* happen belongs only in a skill. A skill's own `paths:` globs only narrow when Claude may auto-load it; they don't force it in ([code.claude.com/docs/en/skills](https://code.claude.com/docs/en/skills); [[2026-07-23-ai-software-product]]).</span>
2. <span style="color:red">**Referenced files, by mention** — split a large markdown and point to `AUTH.md` *without* `@`; a smart model opens it only when needed, like a table of contents. Strong models refrain from reading linked files they don't need; weaker ones over-read — Haiku "swallows everything and gets dumber" ([[2026-06-05-ai-consultancy]], [[2026-09-14-ai-industrial]], [[2026-08-03-ai-telecom-ops-qa]]).</span>
3. **Sub-folder `CLAUDE.md`** — <span style="color:red">loaded lazily, the first time Claude reads, writes or edits a file in that sub-folder (including `cat`/`head` via Bash); a sub-folder `AGENTS.md` behaves the same when there is no CLAUDE.md beside it ([docs](https://code.claude.com/docs/en/memory#how-claude-md-files-load); [[agents-md]]).</span>
4. <span style="color:red">**[[tripwire]]** — just-in-time: when the agent does X, tell it *then* to also do Y ([[2026-08-03-ai-telecom-ops-qa]]).</span>
5. <span style="color:red">**Path-scoped rules** — forced injection by the harness, covered below.</span>

<span style="color:red">**`@path` imports are not progressive.** Earlier versions of this page listed `@include` as a disclosure technique. The `@file` syntax **inlines the content immediately** at load, so it is a forced *eager* load: reserve it for what must always be present, or as a fallback for weak harnesses that don't auto-load nested files (`@backend/CLAUDE.md`) ([[2026-05-27-ai-open-workshop]], [[2026-06-05-ai-consultancy]], [[2026-09-14-ai-industrial]]).</span>

<span style="color:red">**Push-down ladder.** When a [[claude-md]] section passes ≈20–30 lines, push it down: sub-folder CLAUDE.md (on demand) → skill (may be skipped) → path-bound rule (forcibly injected) → referenced markdown files. Recurring cross-feature concerns ("how we build grids") belong in a skill, not CLAUDE.md ([[2026-08-10-ai-consultancy]]).</span>

## A hook beats discretion

The key insight: **a programmatic hook beats a skill's discretion.** A skill relies on the agent *choosing* to load it — it might pick the wrong one or not know it exists. A sub-folder `CLAUDE.md` plus a hook that force-loads it whenever the agent touches a Java file in that folder is **enforcement**, not a polite suggestion. This is the same enforcement gradient as a [[tripwire]] (a kind requirement the agent may ignore) versus a tool that *makes* the workflow happen, as in [[openspec]].

## <span style="color:red">Path-scoped rules: the hook, built in</span>

<span style="color:red">Harnesses now ship the "hook beats discretion" idea natively. In Claude Code, a `.claude/rules/*.md` file with `paths:` frontmatter loads when Claude reads, edits or writes a matching file (Bash `cat`/`head` included); a rule *without* `paths:` loads unconditionally at launch ([code.claude.com/docs/en/memory#path-specific-rules](https://code.claude.com/docs/en/memory#path-specific-rules)). A rule is its own file type, not a skill — one session described it as "a skill with a path glob", which is a useful mental model but not how the docs define it ([[2026-08-10-ai-consultancy]], [[2026-07-22-ai-agentic-how]]). Use it for anything that must be guaranteed, e.g. a Java style rule scoped to `**/*.java` ([[2026-09-14-ai-industrial]]); petclinic's [`pom-rule.md`](https://github.com/victorrentea/petclinic/blob/main/.claude/rules/pom-rule.md) is a small example.</span>

<span style="color:red">GitHub Copilot's equivalent is **path-scoped custom instructions**: `.github/instructions/java.instructions.md` with `applyTo: "**/*.java"` ([Copilot docs](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions#creating-path-specific-custom-instructions)), injected whenever the agent touches a matching file — on disk, no semantic search. *"Programmatic activation, not begging"* ([[2026-09-07-ai-bank]], [[2026-08-06-ai-bank]]). Example: petclinic's [pom.instructions.md](https://github.com/victorrentea/petclinic/blob/main/.github/instructions/pom.instructions.md) ([[2026-09-25-ai-bank-migration]]); one team shares a Java style across three modules this way ([[2026-10-01-ai-bank]]). Converting is a one-liner: *"Turn this skill into a Copilot custom instruction file mapped to `**/*.java`."*</span>

## <span style="color:red">Prove that it loads</span>

<span style="color:red">**Test a new rule in a fresh chat** — the chat that wrote it already knows it. Prove an instruction file is actually loaded with a **loading canary** ("ring a bell when you read this file"), plus VS Code's [Chat Debug View](https://code.visualstudio.com/docs/agents/agent-troubleshooting/chat-debug-view), and repeat a few times ([[2026-10-01-ai-bank]]). This is distinct from the review canary on [[prompt-injection-canary]].</span>

## See also
- [[agent-skill]]
- [[claude-md]]
- [[context-window]]
- [[dumb-zone]]
- [[tripwire]]
- [[token-economy]]
- <span style="color:red">[[agents-md]]</span>
- <span style="color:red">[[agents-md-discipline]]</span>
- <span style="color:red">[[enforcement-ladder]]</span>
- <span style="color:red">[[prompt-injection-canary]]</span>
- [[2026-06-11-ai-mobile-gaming]]
