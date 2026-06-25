---
title: CODEOWNERS Elders
category: pattern
tags: [security, governance, codeowners, github, agent-control, architecture-drift]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-25
updated: 2026-06-25
new_in: "Kambi Day 2 (2026-06-25)"
---

<span style="color:red">🆕 New in Kambi Day 2 (2026-06-25)</span>

A governance pattern that uses GitHub CODEOWNERS to auto-assign senior reviewers ("elders") to any PR that touches high-stakes files, enforcing human oversight over the files that control AI agent behavior.

## The pattern

Protect a curated set of high-stakes files with a CODEOWNERS entry that routes any PR touching them to a named senior group:

```
# .github/CODEOWNERS
CLAUDE.md                  @team/elders
.github/CODEOWNERS         @team/elders
settings.json              @team/elders
**/schema-contracts/**     @team/elders
```

The elder is summoned only for the exact files they guard — not for the entire PR. The [[agent-permissions]] and [[archunit-drift-control]] rules apply to everything else.

## Why these files specifically

AI agents rarely break contracts maliciously — they do so carelessly:
- Five different field names for the same concept across microservices.
- Inconsistent casing in enum values.
- A new CLAUDE.md instruction that contradicts an existing one.
- A `settings.json` change that widens agent permissions beyond the agreed boundary.

The CI pipeline ("the holy Jenkins") is the last line of defense. CODEOWNERS is the enforcement gate before that.

## Relation to [[tripwire|tripwires]]

[[tripwire|Tripwires]] are advisory — they alert humans that something changed without blocking the merge. CODEOWNERS is an *enforcement* mechanism: the PR cannot be merged without the elder's approval. Use tripwires for files worth watching; use CODEOWNERS for files that must not change without sign-off.

## Minimum CODEOWNERS set

| File | Risk |
|---|---|
| `CLAUDE.md` | Modifying agent instructions can silently change all future agent behavior |
| `.github/CODEOWNERS` | Protecting the guardian itself |
| `settings.json` / agent config | Widens or narrows agent permissions |
| Schema contracts / API contracts | Inconsistent naming causes data corruption across services |

## See also

- [[tripwire]]
- [[agent-permissions]]
- [[archunit-drift-control]]
- [[review-by-images]]
- [[production-safety]]
