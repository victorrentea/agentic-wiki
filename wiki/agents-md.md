---
title: AGENTS.md (Cross-Tool Standard)
category: concept
tags: [claude-md, cross-tool, standards, harness, codex, interoperability, copilot]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-13-ai-it-services]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-01-ai-bank]]"]
created: 2026-06-22
updated: 2026-10-13
---

<span style="color:red">`AGENTS.md` is the cross-harness standard for project memory — read natively by Codex, Copilot, Cline and (since September 2026, when no `CLAUDE.md` exists) Claude Code; the portable way to serve Claude Code too is a `CLAUDE.md` containing only `@AGENTS.md`.</span>

## Why it matters

<span style="color:red">Different coding agents historically used different filenames for project memory: Claude Code reads `CLAUDE.md`; Codex, Copilot and Cline read `AGENTS.md`. Claude Code is the only harness that insists on its own file.</span> The [agents.md](https://agents.md) standard proposes a common filename that every harness recognises. <span style="color:red">Adoption is still thin: in one autumn-2026 session only ≈4 of ≈22 participants' repos had any AGENTS.md at all ([[2026-09-24-ai-bank]]).</span>

## <span style="color:red">How Claude Code treats AGENTS.md (version-dependent)</span>

<span style="color:red">Since **v2.1.277 (18 Sep 2026)** Claude Code reads `AGENTS.md` natively through a built-in plugin — but by default **only when there is no `CLAUDE.md`, `.claude/CLAUDE.md` or `CLAUDE.local.md` in the working directory or above it** ([docs](https://code.claude.com/docs/en/memory#agents-md)). A nested `AGENTS.md` loads when Claude reads a file in that subdirectory, again only if that folder has no CLAUDE.md. A `claude-md-and-agents-md` setting reads both.</span>

<span style="color:red">Practical consequences:</span>
- <span style="color:red">A repo with **only** `AGENTS.md` now works in Claude Code out of the box.</span>
- <span style="color:red">The moment a `CLAUDE.md` exists, Claude reads only that — so it must import the other file with `@AGENTS.md`, or AGENTS.md is silently ignored.</span>
- <span style="color:red">Before September 2026 Claude Code didn't read AGENTS.md at all, and a `CLAUDE.md` saying "please read AGENTS.md" in prose was **not** reliably followed (observed May 2026) — never rely on a prose pointer; use the `@` import ([[2026-05-04-ai-telecom]], [[2026-09-24-ai-bank]]).</span>

## <span style="color:red">The bridge: `@AGENTS.md`, not a symlink</span>

<span style="color:red">Earlier advice (June 2026) was to symlink the two files (`ln -s CLAUDE.md AGENTS.md`). By autumn 2026 the recommended layout is the one in the [memory docs](https://code.claude.com/docs/en/memory#share-one-file-with-other-coding-tools): **`AGENTS.md` holds the content; `CLAUDE.md` is a one-liner:**</span>

```markdown
@AGENTS.md
```

<span style="color:red">Why not the symlink:</span>
- <span style="color:red">It **breaks on Windows**: creating one needs [Administrator or Developer Mode](https://gitforwindows.org/symbolic-links), and with `core.symlinks` off (the Git for Windows default) git [checks it out](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coresymlinks) as a small plain file containing the link text. The Claude Code docs prefer the import for Windows users.</span>
- <span style="color:red">Claude Code's Edit/Write refuse to write through a symlink.</span>
- <span style="color:red">Symlinks fail in odd ways: one 40-minute debugging session ended at a **31-byte circular symlink** ([[2026-08-06-ai-bank]]).</span>

<span style="color:red">Victor's own repos guard the convention with a [[tripwire]] script (`check-agents-md.sh`) that fails pre-push and CI if the symlink creeps back ([[2026-09-04-ai-software-company]]). When content gets scrambled while reconciling PRs, ask the agent to diff `main`'s git history to find what was lost.</span>

## <span style="color:red">Verify it's actually loaded — the "red Ferrari" test</span>

<span style="color:red">Put `I have a red Ferrari` at the end of the memory file, open a **new** conversation and ask *"what car do I have?"*. If the agent answers **without opening any file**, the file was pre-loaded; if it goes reading, it wasn't ([[2026-08-06-ai-bank]], [[2026-08-03-ai-telecom-ops-qa]]). Do the same for the folder hierarchy: ask the agent to check live whether Copilot CLI / Claude Code / Codex loads `backend/AGENTS.md` when touching backend files — don't assume ([[2026-05-13-ai-it-services]]). Always start the session from the repo root (don't `/init` in a sub-folder) so sub-folder files [auto-load](https://code.claude.com/docs/en/memory#how-claude-md-files-load) when files there are read or written.</span>

## The catch: harness-specific features don't port

Switching between Claude Code and Codex still breaks on features that are not in the standard:
- **Hooks** — Claude Code's `PreToolUse`/`PostToolUse` bash hooks have no Codex equivalent.
- **Skills** — Claude Code's front-matter activation mechanism differs from Codex's. <span style="color:red">The skill *format* itself is converging, though: Copilot CLI also reads `.claude/skills/`, making agent skills de-facto cross-harness ([agentskills.io](https://agentskills.io/home); see [[agent-skill]]).</span>
- **Permissions** — `allow`/`deny` permission blocks are Claude Code-specific.
- <span style="color:red">**Custom agents / sub-agents** — `.claude/agents/*.md` is **not** read by Copilot or Codex. Copilot CLI takes custom agents from [`.github/agents/*.agent.md`](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli) (plus `~/.copilot/agents`); Codex defines them as **TOML** in [`.codex/agents/`](https://developers.openai.com/codex/subagents) ([[custom-agent]], [[sub-agents]]).</span>

So `AGENTS.md` provides a common project-description layer, but any harness-specific automation (hooks, skills, permission rules) must live in harness-specific config alongside it.

## <span style="color:red">`copilot-instructions.md`: not dead, but delete it</span>

<span style="color:red">The June claim that `.github/copilot-instructions.md` is "dead" was too strong. Copilot still treats it as a first-class repo-wide location, and VS Code Copilot **reads and combines both** files ([docs](https://code.visualstudio.com/docs/agent-customization/custom-instructions#_resolve-conflicting-instructions); `chat.useAgentsMdFile` is on by default). Combining is the problem: stale content in the old file keeps leaking in, and a leftover file has been reported to make Copilot CLI stop looking for AGENTS.md ([copilot-cli#489](https://github.com/github/copilot-cli/issues/489)). One real 40-minute failure: an **empty** `copilot-instructions.md` won over AGENTS.md ([[2026-08-06-ai-bank]]).</span>

<span style="color:red">The recommendation stands: standardise on `AGENTS.md` at the repo root. Bootstrap with `/init`; if it writes `.github/copilot-instructions.md`, move the content into a root `AGENTS.md`, **delete the old file**, and commit.</span> *"Stick with `AGENTS.md`; rename to `CLAUDE.md` the day Claude wins in your company."*

## Curating it

`AGENTS.md` is the file that matters now; how to *curate* it (anti-dumping, the Socratic enrichment loop, sub-folder injection) is its own discipline — see [[agents-md-discipline]] — and it sits in the project tier of the four [[memory-layers]]. <span style="color:red">Headlines from the autumn sessions ([[2026-10-01-ai-bank]], [[2026-09-28-ai-gaming-tech]]):</span>
- <span style="color:red">**Code style goes in the repo; personal preferences in your home-level file** (e.g. "after 18:00, answer only in Romanian").</span>
- <span style="color:red">**Delete what the agent already knows from training — but keep the stack name**, so it doesn't go searching.</span>
- <span style="color:red">**"Update AGENTS.md on every change" is an anti-pattern**: it bloated one file to 2,000 lines (another session saw 8,000). Fix [[drift]] in a new chat with the offending line attached, review at every retro, clean weekly.</span>
- <span style="color:red">**Don't copy someone else's CLAUDE.md** (not even Boris Cherny's) — grow your own. Rules shared by several repos belong in a [[agent-skill|skill]] whose description says *"useful on any of repos X"*.</span>
- <span style="color:red">**Garbage in, garbage out:** the agent imitates the existing code style, however legacy — see [[light-dark-code]].</span>

## See also
- [[claude-md]]
- [[agents-md-discipline]]
- [[memory-layers]]
- [[agent-skill]]
- [[harness]]
- [[progressive-disclosure]]
- <span style="color:red">[[custom-agent]]</span>
- <span style="color:red">[[tripwire]]</span>
- <span style="color:red">[[drift]]</span>
- <span style="color:red">[[light-dark-code]]</span>
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
