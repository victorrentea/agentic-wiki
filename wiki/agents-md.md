---
title: AGENTS.md (Cross-Tool Standard)
category: concept
tags: [claude-md, cross-tool, standards, harness, codex, interoperability]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-22
updated: 2026-06-22
new_in: 2026-06-22
---

<span style="color:red">🆕 New in AI@Kambi (2026-06-22)</span>

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

## See also
- [[claude-md]]
- [[agent-skill]]
- [[harness]]
- [[progressive-disclosure]]
- [[2026-06-22-ai-kambi]]
