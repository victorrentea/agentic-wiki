---
title: AGENTS.md (Cross-Tool Standard)
category: concept
tags: [claude-md, cross-tool, standards, harness, codex, interoperability, copilot]
sources: ["[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-22
updated: 2026-06-24
---

`AGENTS.md` is the emerging cross-harness standard for project memory, readable by both Claude Code and Codex — you can symlink `AGENTS.md → CLAUDE.md` to serve both harnesses from one file.

## Why it matters

Different coding agents use different filenames for project memory: Claude Code reads `CLAUDE.md`, Codex reads `AGENTS.md`. The [agents.md](https://agents.md) standard proposes a common filename that both harnesses recognise. Symlinking makes a single source of truth serve both:

```bash
ln -s CLAUDE.md AGENTS.md
```

## The catch: harness-specific features don't port

Switching between Claude Code and Codex still breaks on features that are not in the standard:
- **Hooks** — Claude Code's `PreToolUse`/`PostToolUse` bash hooks have no Codex equivalent.
- **Skills** — Claude Code's front-matter activation mechanism differs from Codex's.
- **Permissions** — `allow`/`deny` permission blocks are Claude Code-specific.

So `AGENTS.md` provides a common project-description layer, but any harness-specific automation (hooks, skills, permission rules) must live in harness-specific config alongside it.

## `copilot-instructions.md` is dead

The older `.github/copilot-instructions.md` convention has been superseded by the cross-vendor `AGENTS.md` standard at repo root — use `AGENTS.md`. *"Stick with `AGENTS.md`; rename to `CLAUDE.md` the day Claude wins in your company."* `AGENTS.md` is the file that matters now; how to *curate* it (anti-dumping, the Socratic enrichment loop, sub-folder injection) is its own discipline — see [[agents-md-discipline]] — and it sits in the project tier of the four [[memory-layers]].

## See also
- [[claude-md]]
- [[agents-md-discipline]]
- [[memory-layers]]
- [[agent-skill]]
- [[harness]]
- [[progressive-disclosure]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-23-ai-garmin]]
