---
title: Skills Governance
category: pattern
tags: [skill, team, knowledge-engineering, marketplace, conflict, scaling]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-23
new_in: 2026-06-23
---

<span style="color:red">🆕 New in Garmin 2026-06-23</span>

Skills governance is the organizational discipline of deciding *who owns which skill-set* — avoiding both the megalomania of one set for everyone and the dichotomy of every dev with their own.

## The two failure extremes

- **Megalomania** — one skill-set imposed on hundreds of company devs. Different teams, stacks, and conventions mean most rules don't fit; conflicting rules just confuse the agent.
- **Dichotomy** — every developer maintaining their own personal skills. Knowledge fragments, nothing is shared, and the same lessons get re-learned in isolation.

## The sweet spot: one skill-set per team / per project

Layer a **team/project skill-set on top of a company base**, loaded by the harness in order (see [[memory-layers]]). The company layer carries universal conventions; the team layer carries what's specific to that family of repos. This keeps each set small enough to stay out of the [[dumb-zone]] and coherent enough that rules don't fight each other.

## Practical notes

- **Adapt, don't adopt blindly.** A common pattern: pull best-practice skills off the internet, rewrite them "the team way," and publish the result as a marketplace plugin for the team's harness (Copilot CLI / Claude Code). A downloaded [[agent-skill]] is someone's opinionated way of working — use it to learn the delta, then make your own.
- **Conflicting rules confuse the agent** — the same reason an over-stuffed `AGENTS.md` hurts (see [[agents-md-discipline]]).
- The team layer is operationalized as a **shared Git repo** of skills — the [[cross-repo-knowledge]] "Dark Horses" pattern.

## See also
- [[agent-skill]]
- [[memory-layers]]
- [[cross-repo-knowledge]]
- [[agents-md-discipline]]
- [[custom-agent]]
- [[2026-06-23-ai-garmin]]
