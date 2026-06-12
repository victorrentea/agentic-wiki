---
title: OpenSpec
category: tool
tags: [spec-driven, skill, workflow-enforcement, scaffolding, handover]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---


OpenSpec is a tool that scaffolds opinionated [[spec-driven-development]] markdown into `.claude/skills` and enforces the spec workflow in waves through a backing CLI.

## How it works

[OpenSpec](https://github.com/Fission-AI/OpenSpec) installs a set of [[agent-skill|skills]] whose markdown is **one opinionated take** ("50k likes, but one opinion") — so **tailor them**; commit the defaults first so you can adjust later. Setup is `openspec config profile` (enable **new** and **continue**, off by default), then `/openspec new change` and paste in the [[handover]] from your grill conclusions.

The crucial design point: the skill **calls a tool** (`bash openspec`) that **enforces** the workflow — the AI itself can only be "kindly required" (the [[tripwire]] gradient), while the tool does the actual enforcing. This is the same "a hook beats discretion" insight from [[progressive-disclosure]].

## What it produces

The three SDD artifacts — `proposal.md`, `design.md`, `tasks.md` (see [[spec-driven-development]] for who owns each). The proposal captured a real grill: server-side pagination + sorting, cap page size at 20, avoid N+1 via batch-load, migrate the frontend display, and regenerate-and-commit the OpenAPI contract so a renamed Java field breaks the frontend build — [[drift]] made impossible.

## Caveat

OpenSpec ships via `npm install -g` — whose **post-install script** silently registered the `openspec` command, a live demonstration of the [[supply-chain-attack]] risk ("some library online just executed code on your machine").

## See also
- [[spec-driven-development]]
- [[handover]]
- [[agent-skill]]
- [[tripwire]]
- [[supply-chain-attack]]
- [[drift]]
- [[2026-06-11-ai-playtika]]
