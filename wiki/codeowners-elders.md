---
title: CODEOWNERS Elders
category: pattern
tags: [security, governance, codeowners, github, agent-control, architecture-drift]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-01-ai-java-community]]", "[[2026-08-06-ai-bank]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-25
updated: 2026-10-13
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

The elder is summoned only for the exact files they guard — not for the entire PR. The [[agent-permissions]] and [[archunit-drift-control]] rules apply to everything else. <span style="color:red">In practice the architect sees only 1–2 PRs a week — exactly the ones that matter (a new dependency, an API contract change, CI configuration) — even when the AI produced an 800-line PR ([[2026-05-11-ai-software-vendor]]).</span>

<span style="color:red">**Name a group, not a person.** Keep the entry a generic "another human" group (`@team/elders`), never a hard-coded colleague ([[2026-07-01-ai-java-community]]). To the objection *"this is written proof you don't trust your colleagues"*, the answer: *"Trust the people. But have a night watchman — especially when AI is going yee-haw"* ([[2026-04-07-ai-product-team]]).</span>

<span style="color:red">**It only blocks with branch protection.** CODEOWNERS is a **code-host** feature (GitHub, GitLab, Bitbucket), not a CI one — pure CI tools don't honour it ([[2026-07-01-ai-java-community]]). On GitHub it blocks the merge only when branch protection has **"Require review from Code Owners"** turned on; otherwise it merely *requests* a review ([GitHub docs](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners); [[2026-09-02-ai-insurer]], [[2026-10-09-ai-jax-london]]). On GitLab the equivalent is Protected Branches + CODEOWNERS (a paid-tier feature, per [[2026-05-25-ai-government]]); the approval policy belongs on the merge request, not in the CI job.</span>

## Why these files specifically

AI agents rarely break contracts maliciously — they do so carelessly:
- Five different field names for the same concept across microservices.
- Inconsistent casing in enum values.
- A new CLAUDE.md instruction that contradicts an existing one.
- A `settings.json` change that widens agent permissions beyond the agreed boundary.

The CI pipeline ("the holy Jenkins") is the last line of defense. CODEOWNERS is the enforcement gate before that.

<span style="color:red">**The agent attacks the fence itself.** When an [ArchUnit](https://www.archunit.org/) test that keeps `packages.puml` in sync with the code fails, the agent takes the path of least resistance: it adds the wrong line to the diagram or **comments out the test** ([[2026-09-07-ai-bank]]). A guardrail the agent can delete is no guardrail ([[2026-09-18-ai-agentic-how]]) — so the guardrail tests, scripts, git hooks and linter configs must sit under CODEOWNERS too: *"either respect the architecture or call the elder"* ([[2026-09-02-ai-insurer]]). Likewise `specs/**` and `openapi.yaml`, or the agent co-evolves the spec to match its own buggy code ([[2026-04-27-ai-healthcare-software]]).</span>

<span style="color:red">**Why six eyes now.** Both the author's human and the reviewer's human work through agents, so a silent change to a guardrail file can pass two brains unnoticed ([[2026-09-04-ai-software-company]]). The third reviewer looks **only** at the ≈10 files covered by CODEOWNERS — the rest isn't their business ([[2026-08-31-ai-software-vendor]]). The second human is *"preparation for the inevitable rushed LGTM"* ([[2026-06-02-ai-open-workshop]]).</span>

## Relation to [[tripwire|tripwires]]

[[tripwire|Tripwires]] are advisory — they alert humans that something changed without blocking the merge. <span style="color:red">CODEOWNERS is an *enforcement* mechanism — provided branch protection requires Code Owner review: then the PR cannot be merged without the elder's approval.</span> Use tripwires for files worth watching; use CODEOWNERS for files that must not change without sign-off.

## Layered enforcement

<span style="color:red">Each layer catches what the previous one misses ([[2026-10-09-ai-jax-london]]): a **pre-push hook** (fast, but skippable with `--no-verify`) → **CI** re-runs the same checks server-side → **CODEOWNERS** forces an elder's review on any edit to the hooks or workflows that run those checks. A complementary triad ([[2026-05-25-ai-government]]): **CODEOWNERS** decides *who approves*, a [[gitleaks]] pre-commit decides *what may not leave*, ArchUnit decides *which design rules can't break*. Generated, zero-token surveillance artifacts — OpenAPI, DB schema, PlantUML, [[code-city]] — make the guarded changes visible in the diff ([[2026-09-16-ai-safety-critical]]; see [[observation-points]]). And in every retro, **review the markdown diffs first** — agent instructions are code.</span>

## Minimum CODEOWNERS set

<span style="color:red">Two tiers ([[2026-09-07-ai-bank]]): **medium** — the API contract and the DB schema; **critical** — the protections themselves (guardrail tests, hooks, CI, agent settings). Keep an inventory of what is guarded and why in a `GUARDRAILS.md` ([[2026-09-02-ai-insurer]], [[2026-09-04-ai-software-company]]; [PetClinic's example](https://github.com/victorrentea/petclinic/blob/main/GUARDRAILS.md)).</span>

| File | Risk |
|---|---|
| `CLAUDE.md` | Modifying agent instructions can silently change all future agent behavior |
| `.github/CODEOWNERS` | Protecting the guardian itself |
| `settings.json` / agent config | Widens or narrows agent permissions |
| Schema contracts / API contracts | Inconsistent naming causes data corruption across services |
| `.github/workflows/**` | CI workflow changes can exfiltrate secrets or disable safety checks |
| OpenAPI contract | Breaking API changes cause cross-service runtime failures |
| Domain model / `pom.xml` deps | Core model changes propagate everywhere; dependency additions are supply-chain events |
| Architecture diagrams | A stale diagram is confidently wrong; changes must be reviewed alongside the code |
| <span style="color:red">Guardrail tests (ArchUnit package), git hooks, linter configs</span> | <span style="color:red">The agent comments out or loosens the check that bothers it</span> |
| <span style="color:red">`.claude/settings*`, CI workflows (GitHub/Jenkins)</span> | <span style="color:red">Owner should be **adversarial, from another project** — "the old man with trauma in his eyes" ([[2026-08-31-ai-software-vendor]])</span> |
| <span style="color:red">DB migrations / the schema built from them</span> | <span style="color:red">Irreversible data changes; the generated schema SQL is a holy file in itself ([[2026-06-02-ai-open-workshop]], [[2026-07-01-ai-java-community]])</span> |
| <span style="color:red">`specs/**`, `api.md`, integration contracts</span> | <span style="color:red">The agent edits the spec to match its own code ([[2026-04-27-ai-healthcare-software]], [[2026-04-07-ai-product-team]])</span> |
| <span style="color:red">Generated docs (`docs/generated/*`)</span> | <span style="color:red">A change here means the code under it changed shape ([[2026-06-02-ai-open-workshop]])</span> |
| <span style="color:red">Domain model folder</span> | <span style="color:red">*"Touch the domain, and the old man comes back from retirement to review"* ([[2026-08-06-ai-bank]])</span> |

## Libraries, not versions

<span style="color:red">Guarding `pom.xml` summons the architect on every version bump. Instead, commit a generated projection that lists **just the dependency names** (e.g. `unversioned-dependencies.txt`) and guard *that*: *"I care about the set of libraries you have, not the version"* ([[2026-07-01-ai-java-community]]). A new library is a [[supply-chain-attack]] surface; a patch bump is not an architecture decision.</span>

## Beyond files: sensitive domains

<span style="color:red">Some changes are risky by topic, not by path: pricing, auth, payments, security, massive refactors. Route those to a selective second reviewer whose only question is *"explain why you touched this"* ([[2026-06-02-ai-open-workshop]]). The pattern assumes a second human exists — for a solo developer on a project, *"a second approver from whom — Saint Peter?"* — there the layered hooks + CI carry the load alone.</span>

## See also

- [[tripwire]]
- [[agent-permissions]]
- [[archunit-drift-control]]
- [[review-by-images]]
- [[production-safety]]
- [[prompt-injection-canary]]
- <span style="color:red">[[observation-points]]</span>
- <span style="color:red">[[gitleaks]]</span>
- <span style="color:red">[[code-city]]</span>
- <span style="color:red">[[supply-chain-attack]]</span>
- <span style="color:red">[[enforcement-ladder]]</span>
- <span style="color:red">[[static-analysis-gauntlet]]</span>
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-26-ai-agentic-how]]
- <span style="color:red">[[2026-04-07-ai-product-team]]</span>
- <span style="color:red">[[2026-04-16-ai-safety-critical]]</span>
- <span style="color:red">[[2026-04-27-ai-healthcare-software]]</span>
- <span style="color:red">[[2026-05-11-ai-software-vendor]]</span>
- <span style="color:red">[[2026-05-25-ai-government]]</span>
- <span style="color:red">[[2026-06-02-ai-open-workshop]]</span>
- <span style="color:red">[[2026-06-05-ai-consultancy]]</span>
- <span style="color:red">[[2026-07-01-ai-java-community]]</span>
- <span style="color:red">[[2026-08-06-ai-bank]]</span>
- <span style="color:red">[[2026-08-31-ai-software-vendor]]</span>
- <span style="color:red">[[2026-09-02-ai-insurer]]</span>
- <span style="color:red">[[2026-09-04-ai-software-company]]</span>
- <span style="color:red">[[2026-09-07-ai-bank]]</span>
- <span style="color:red">[[2026-09-16-ai-safety-critical]]</span>
- <span style="color:red">[[2026-09-18-ai-agentic-how]]</span>
- <span style="color:red">[[2026-10-09-ai-jax-london]]</span>
