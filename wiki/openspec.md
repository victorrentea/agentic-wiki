---
title: OpenSpec
category: tool
tags: [spec-driven, skill, workflow-enforcement, scaffolding, handover]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-24
---

OpenSpec is a tool that scaffolds opinionated [[spec-driven-development]] markdown into `.claude/skills` and enforces the spec workflow in waves through a backing CLI.

## How it works

[OpenSpec](https://github.com/Fission-AI/OpenSpec) installs a set of [[agent-skill|skills]] whose markdown is **one opinionated take** ("50k likes, but one opinion") — so **tailor them**; commit the defaults first so you can adjust later. Setup is `openspec config profile` (enable **new** and **continue**, off by default), then `/openspec new change` and paste in the [[handover]] from your grill conclusions.

The crucial design point: the skill **calls a tool** (`bash openspec`) that **enforces** the workflow — the AI itself can only be "kindly required" (the [[tripwire]] gradient), while the tool does the actual enforcing. This is the same "a hook beats discretion" insight from [[progressive-disclosure]].

## What it produces

The three SDD artifacts — `proposal.md`, `design.md`, `tasks.md` (see [[spec-driven-development]] for who owns each). The proposal captured a real grill: server-side pagination + sorting, cap page size at 20, avoid N+1 via batch-load, migrate the frontend display, and regenerate-and-commit the OpenAPI contract so a renamed Java field breaks the frontend build — [[drift]] made impossible.

## Caveat

OpenSpec ships via `npm install -g` — whose **post-install script** silently registered the `openspec` command, a live demonstration of the [[supply-chain-attack]] risk ("some library online just executed code on your machine").

## The four artifacts and who reviews them

OpenSpec emits four reviewable markdown files that map to four stakeholder roles:
- **Proposal** — functional, non-technical → review with the **business / PO**.
- **Design** — architecture and code decisions → review with **developers** (pair/mob). Include a `## Non-goals` section.
- **Tasks** — the AI's execution checklist, git-tracked progress; minimal human review needed.
- **Spec** — acceptance criteria → review with **QA**, then convert to [[acceptance-test-bdd|Gherkin `.feature`]] files so the agent can verify its own work against an independent oracle.

The key guard: QA (or a human) must lock the `.feature` files *before* implementation starts. If the agent generates both the Gherkin and the implementation in the same pass, the test is no longer an independent oracle.

## Human-in-the-loop discipline

The most common failure mode: the agent skips the spec review and proceeds directly to implementation. The reliable fix is to force [[plan-mode]] *before* invoking the OpenSpec skill, rather than trusting the skill's internal "don't continue" hint — harness-level plan-mode is the enforcer, not the skill text.

## See also
- [[spec-driven-development]]
- [[handover]]
- [[agent-skill]]
- [[tripwire]]
- [[supply-chain-attack]]
- [[drift]]
- [[plan-mode]]
- [[non-goals]]
- [[three-amigos]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
