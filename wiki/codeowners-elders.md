---
title: CODEOWNERS Elders
category: pattern
tags: [security, governance, codeowners, github, agent-control, architecture-drift]
sources: ["[[2026-06-22-ai-kambi]]", "[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-25
updated: 2026-06-26
---

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
| <span style="color:red">`.github/workflows/**`</span> | <span style="color:red">CI workflow changes can exfiltrate secrets or disable safety checks</span> |
| <span style="color:red">OpenAPI contract</span> | <span style="color:red">Breaking API changes cause cross-service runtime failures</span> |
| <span style="color:red">Domain model / `pom.xml` deps</span> | <span style="color:red">Core model changes propagate everywhere; dependency additions are supply-chain events</span> |
| <span style="color:red">Architecture diagrams</span> | <span style="color:red">A stale diagram is confidently wrong; changes must be reviewed alongside the code</span> |

## See also

- [[tripwire]]
- [[agent-permissions]]
- [[archunit-drift-control]]
- [[review-by-images]]
- [[production-safety]]
- [[prompt-injection-canary]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-26-ai-agentic-how]]
