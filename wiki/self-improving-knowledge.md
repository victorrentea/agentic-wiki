---
title: Self-Improving Knowledge
category: pattern
tags: [knowledge-engineering, claude-md, agents-md, jsonl, automation, retrospective]
sources: ["[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-26
updated: 2026-06-26
new_in: 2026-06-26
---

<span style="color:red">🆕 New in AI@agentic.how (2026-06-26)</span>

Self-improving knowledge is the practice of running an automated agent that mines your own conversation logs (Claude Code stores every session as JSONL) to find frustration moments, diagnose missing rules or context, and file pull requests to [[claude-md]] or [[agent-skill|skills]] — making the knowledge base improve itself from your own failures without manual retrospective effort.

## The mechanism

Claude Code writes every session to `~/.claude/**/*.jsonl`. An agent can scan these files and:

1. Identify moments where the human appeared frustrated (repeated corrections, explicit rephrasing, "no, not like that" signals).
2. Diagnose *why*: was a rule missing? was a context fact absent? was a [[decision-context]] entry needed?
3. File a PR to the relevant file (`CLAUDE.md`, a skill, a sub-folder `CLAUDE.md`) that would have prevented the failure.

A platform team at one bank runs this weekly — Saturday night, scheduled via background loop — and the team now reviews **hundreds of such PRs per week**. The engineer's job becomes accepting or rejecting AI-proposed self-improvements rather than writing them from scratch.

## Schedule it

Use a cron-style background loop (Boris Cherny's *chained-loop* feature in Claude Code) to trigger the JSONL-mining agent weekly without needing an open terminal. The output is a batch of draft PRs on the central AI-knowledge repo (see [[cross-repo-knowledge]]).

## Complementary source: git history

A parallel signal is the git history of code-review fixes — especially rejected MRs from newer team members: those commits pinpoint *exactly* the mistakes a fresh agent also makes. Scanning both signals (conversation logs + rejected MRs) gives a high-precision feed for [[agents-md-discipline|AGENTS.md]] improvements.

## Why it compounds

Every accepted PR converts a one-time frustration into a standing rule that benefits the entire team going forward. The [[decision-context]] pattern fits naturally: each frustration that traced to a false assumption becomes a one-line entry. Over months, the failure rate of the agent on recurrent task types measurably drops — the same quality-feedback loop that powers [[skills-benchmarking]], but applied to the knowledge base itself rather than individual skills.

## See also
- [[agents-md-discipline]]
- [[claude-md]]
- [[decision-context]]
- [[cross-repo-knowledge]]
- [[skills-benchmarking]]
- [[llm-wiki]]
- [[progressive-disclosure]]
- [[2026-06-26-ai-agentic-how]]
