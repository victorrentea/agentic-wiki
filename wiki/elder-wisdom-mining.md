---
title: Elder Wisdom Mining
category: pattern
tags: [knowledge-management, memory, legacy, interviews, decision-context, human-factors]
sources: ["[[2026-09-14-ai-industrial]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Elder wisdom mining is the practice of having an agent interview a senior expert over weeks or months before they retire or leave, and mine their digital footprint, so the undocumented decisions they carry stay in the company's agent memory rather than walking out the door.

## How it works

- **Seed from the footprint.** The agent first scans what the expert left behind — commits, tickets, PR comments, mails, chat, docs — and uses it to **provoke memories** about decisions nobody wrote down ("why did you split this module in 2014?") ([[2026-09-14-ai-industrial]]).
- **Script the interviews.** The agent drafts the questions for each session; one variant runs a month of AI-scripted sessions and uses **old photos as memory cues** ([[2026-10-09-ai-jax-london]]). Others record hours of sessions over ≈three months before departure.
- **Distil into the shared memory layer**, not into a one-off document: an [[llm-wiki]], a glossary, [[decision-context]] / ADR entries the team's agents load on demand ([[memory-layers]], [[two-layer-memory]]).

Reported as practised at large banks ([[2026-08-31-ai-software-vendor]], [[2026-10-09-ai-jax-london]]), as part of a team "second brain" that tries to clone the expert's *reasoning*, not just their facts ([[2026-09-28-ai-gaming-tech]]). Research tools for corporate agent memory mentioned alongside it: [Mnemosyne](https://github.com/mnemosyne-oss/mnemosyne) and [Hindsight](https://github.com/vectorize-io/hindsight).

## Where it fits in knowledge recovery

Retirement interviews are one source among several when an old system has no docs or tests ([[2026-09-02-ai-insurer]]):

| Source | What it gives the agent |
|---|---|
| Jar sources from GitHub / internal Nexus, else a decompiler ([Vineflower](https://github.com/Vineflower/vineflower)) | the actual code |
| Playwright headless on a staging env where testers entered data for years | how the app behaves |
| Read-only DB on staging + `curl` on the API | real data shapes |
| Confluence/SharePoint, Jira, prod incidents, end-user tickets | intent and pain points |
| History of **PR comments** — what reviewers reproached newcomers for | house rules already written down |
| Business emails/chats, scanned **by the business with their own Claude** (privacy — not you in their inbox) | requirements in their words |
| ADRs | historical context, so you dare to change things |
| **Pre-retirement interviews** | the "why" that exists nowhere else |

## Relation to other patterns

It is [[grilling-in-reverse]] pointed at a person instead of code, and the human counterpart of [[self-improving-knowledge]]: there the agent captures lessons from its own sessions; here it captures them from a human before the window closes. The output feeds the same [[cross-repo-knowledge]] that onboarding and legacy work ([[legacy-migration-stages]]) depend on.

## See also
- [[llm-wiki]]
- [[decision-context]]
- [[grilling-in-reverse]]
- [[self-improving-knowledge]]
- [[memory-layers]]
- [[cross-repo-knowledge]]
- [[legacy-migration-stages]]
- [[2026-09-14-ai-industrial]]
