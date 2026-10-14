---
title: Cross-Repo Knowledge
category: pattern
tags: [claude-md, skill, team, monorepo, knowledge-engineering, symlink, api-contracts]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-07-01-ai-java-community]]", "[[2026-08-06-ai-bank]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]"]
created: 2026-06-11
updated: 2026-10-13
---

Cross-repo knowledge is the pattern of keeping a team's shared agent configuration — [[claude-md]] rules plus [[agent-skill|skills]] — in one central git repo that every project repo references, so knowledge lives on the team rather than on one laptop.

## The central team repo

A studio may have 20–50 repos, only a handful of them yours. Putting agent knowledge in each repo's local `CLAUDE.md` fragments it and lets it drift independently. The pattern:

- A **team git repo** (e.g. `dark-horses-ai-central`) holds the shared `CLAUDE.md` and skills.
- Each cloned project **symlinks** to it (a real setup wired ≈100 symlinks), or each repo's `CLAUDE.md` does `@include $DARK_HORSES_CENTRAL/...` against an env-var path every dev sets.
- Add a central **OpenAPI registry** repo so your agent can see a colleague's service contract, and **skill-extensions** so teams tailor the standard workflow.

<span style="color:red">"Skills are opinions": share them within a bounded context of 1–2 teams rather than enforcing one company-wide style [[2026-05-06-ai-ecommerce]] (see [[skills-governance]]). At organisation level the same idea scales into a team [[plugin-marketplace]], grown bottom-up in the team's own garden rather than as a company-wide commons too big to influence [[2026-08-06-ai-bank]] [[2026-09-07-ai-bank]].</span>

## <span style="color:red">What goes in the central repo</span>

<span style="color:red">Prioritised [[2026-04-16-ai-safety-critical]]:</span>

1. <span style="color:red">**API contracts** — by far the most valuable: OpenAPI, AsyncAPI / Kafka topics, Avro, JSON Schema, Protobuf, GraphQL.</span>
2. <span style="color:red">**C1/C2 diagrams** (generated and ArchUnit-verified — see [[c4-model]], [[deterministic-diagrams]]), e.g. one `architecture.puml` per service.</span>
3. <span style="color:red">**AGENTS.md extracts** — an `agents-extract.md` per service with the cross-team-relevant part of its rules — plus a root `AGENTS.md` with ecosystem-wide rules.</span>
4. <span style="color:red">**Event schemas.**</span>

<span style="color:red">Also worth sharing: skills, slash commands, custom agents, MCP server configs, specs that span 2+ services, and a **reference microservice with impeccable tests** to clone and pattern-match ([[light-dark-code]]) [[2026-05-06-ai-ecommerce]] [[2026-04-27-ai-healthcare-software]]. **Keep out**: actual code, secrets, C3 diagrams, and — a "hard debate" — neighbours' DB schemas (one variant allows them "sometimes") [[2026-04-16-ai-safety-critical]] [[2026-04-27-ai-healthcare-software]]. Put there what the agent finds hard: team skills, PlantUML diagrams, the services' OpenAPI contracts [[2026-08-06-ai-bank]].</span>

<span style="color:red">**Contracts, not clones.** Have **CI push every service's contract on every commit** (GitHub Actions or submodules), so any agent can grep "who owns the birthday field?" without context-bombing itself with 2,000 Swaggers [[2026-04-07-ai-product-team]] [[2026-05-11-ai-software-vendor]] [[2026-05-27-ai-open-workshop]]. A cheaper interim rule in CLAUDE.md: *"if you need the schema, clone `<git-url>` to a temp folder and search for `serviceA.ddl`."* Alternatives are an API-management system (Apiary, [Pact](https://pact.io/)), or `/add-dir` / a parent-folder session, which floods the context with hundreds of repos. It is comparable to [Backstage](https://backstage.io), but for AI consumption; complement it with consumer-driven contract tests ([Pact](https://docs.pact.io)) and [[openapi-contract-sync]].</span>

<span style="color:red">**Keep DB migrations in the same repo as the code** (Flyway/Liquibase, same PR): *"AI works best when a human works best — nobody likes two repos"* [[2026-04-07-ai-product-team]]. Credentials never go in CLAUDE.md — MCP config JSON references `$ENV_VAR`.</span>

## <span style="color:red">Distribution mechanisms</span>

| <span style="color:red">Mechanism</span> | <span style="color:red">Trade-off</span> |
|---|---|
| <span style="color:red">A tool that copies central → local</span> | <span style="color:red">reinstall to update [[2026-05-06-ai-ecommerce]]</span> |
| <span style="color:red">**Symlinks** to a clone refreshed by cron `git pull`</span> | <span style="color:red">instant; fragile on Windows [[2026-05-06-ai-ecommerce]] [[2026-09-14-ai-industrial]]</span> |
| <span style="color:red">Git submodules</span> | <span style="color:red">robust, ergonomically heavy [[2026-05-06-ai-ecommerce]]</span> |
| <span style="color:red">`SessionStart` hook (or global CLAUDE.md) running `git pull` on central</span> | <span style="color:red">fresh on every boot [[2026-05-11-ai-software-vendor]] [[2026-04-07-ai-product-team]]</span> |
| <span style="color:red">[[plugin-marketplace]]</span> | <span style="color:red">auto-updates at start; bundles skills, hooks, MCPs [[2026-08-06-ai-bank]]</span> |

<span style="color:red">A concrete setup: one **team git repo of skills**, cloned by everyone and **symlinked into all 10 projects**, pulled daily at 8 am by cron (`schtasks` on Windows). When two harnesses need the same skills (e.g. Junie and Claude), keep the files in `.claude/skills` and make `.junie/skills` a symlink. Git stores symlinks with mode 120000; on Windows they need [`core.symlinks=true`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coresymlinks) plus Developer Mode or an admin shell (see [[agents-md]]) [[2026-09-14-ai-industrial]].</span>

<span style="color:red">Validate every shared skill's front-matter in CI — a typo in `description` silently kills auto-loading [[2026-05-06-ai-ecommerce]]. **Any skill change is approved by the whole team, after an eval**: one bad skill burned ≈$500 in a day [[2026-09-18-ai-agentic-how]].</span>

## Microservices: the parent-folder session trick

When one team owns ≈10 repos, opening a Claude Code session inside a single repo makes the agent blind to the others. Fix: clone all repos under one root directory and open the session *in that parent folder* — each repo gets its own small, proportional `CLAUDE.md`, and the root directory becomes an **orchestrator CLAUDE.md** that knows the full landscape. For production robustness (VPS, CI, Docker, Jenkins), this "clone them all" approach breaks because the repos may not be present. The durable solution is the **a central AI-knowledge repo** git repo: it holds shared skills, MCP configs, and agent personas, with the root CLAUDE.md a **symlink** into it ("it's a symlink — it IS versioned"). Every service repo symlinks to the central one; teams add skill-extensions for service-specific workflows. Someone argued agents are an incentive to merge microservices back into a monorepo — the symlink pattern is the alternative.

<span style="color:red">A sharper version of that argument: AI pulls microservices back toward **monorepos / modular monoliths**, because the agent can traverse, deploy, test and read logs faster locally without spinning up containers — "if you did microservices just for fun it was always a bad idea"; if you need them, keep several services in one git repo [[2026-07-01-ai-java-community]].</span>

<span style="color:red">**Per-feature workspaces**: either assemble a folder per feature with only the microservices involved (control), or leave all of `~/workspace` open to grep (bloated context). Ask the agent to generate the IDE project XML with linked modules instead of clicking. You still must know which services a feature touches — if the "who calls whom" map lives in the elders' heads, mine it from the dusty draw.io/Lucidchart in Confluence (≈80% right), API-management ACLs (Kong, Apiary), the [Pact](https://pact.io) broker and cloud service-account grants, into an [ARCHITECTURE.md](https://github.com/victorrentea/petclinic/blob/main/ARCHITECTURE.md). When a box has 10k lines, you care about its edges, not its code ([[elder-wisdom-mining]], [[field-reality-diagram]]) [[2026-09-16-ai-safety-critical]].</span>

## <span style="color:red">Rule hierarchy and promotion</span>

<span style="color:red">Three levels: **ecosystem** (central AGENTS.md) → **microservice** (repo AGENTS.md) → **module** (nested AGENTS.md); each repo's AGENTS.md imports the central folder, and the agent `git pull`s it before work [[2026-04-16-ai-safety-critical]]. *"7 microservices ⇒ 7 AGENTS.md"* — each service has its quirks; don't force a single file [[2026-08-06-ai-bank]]. Victor: "the art is giving the AI *enough* context — not too much, not too little."</span>

<span style="color:red">**Rule promotion**: each repo starts with local rules; periodically do a "radical extraction" — rules that appear in ≈5 teams get **promoted to the central AGENTS.md and deleted locally** (import, don't duplicate) — "suddenly everyone has it" [[2026-04-16-ai-safety-critical]]. One variant runs it quarterly, by "knowledge engineers", a role emerging now [[2026-04-27-ai-healthcare-software]].</span>

<span style="color:red">**Team overrides of a company skill**: a `PostToolUse` hook on the Skill tool appends your own markdown when the shared skill fires (an `x-extension.md`) [[2026-09-18-ai-agentic-how]].</span>

## Knowledge engineering

The loop that makes it compound: when an agent misbehaves it opens a **PR to central**, reviewed each sprint. That review *is* knowledge engineering — "training the minions" — and it is exactly how a [[tripwire]] or a corrected [[claude-md]] rule propagates from one engineer's frustration to the whole team. <span style="color:red">Note that an `@include` / [`@import`](https://code.claude.com/docs/en/memory#import-additional-files) is **expanded at launch and charged on every session** — eager loading, not [[progressive-disclosure]]; only a plain pointer ("if you need X, read file Y") is read lazily, when the agent judges it useful [[2026-09-04-ai-software-company]].</span> The OpenAPI registry feeds the [[field-reality-diagram]] and drift-check pipelines. A more automated version of this loop is [[self-improving-knowledge]] — a weekly agent that mines your own JSONL conversation logs and files PRs to the central repo automatically.

<span style="color:red">**Security**: give agents **read-only** access to the central repo; writes only through human-signed PRs — otherwise a [[prompt-injection]] in one service could write false rules into everyone's context ([[memory-poisoning]]). Mitigate approval bottlenecks with fine-grained CODEOWNERS ([[codeowners-elders]]) [[2026-04-16-ai-safety-critical]]. Never install an *internet* plugin marketplace in a company: plugins auto-update, pushing new scripts to every developer — see [[skill-marketplace-security]] [[2026-09-18-ai-agentic-how]].</span>

## See also
- [[claude-md]]
- [[agent-skill]]
- [[self-improving-knowledge]]
- [[progressive-disclosure]]
- [[tripwire]]
- [[drift]]
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[skills-governance]]</span>
- <span style="color:red">[[openapi-contract-sync]]</span>
- <span style="color:red">[[c4-model]]</span>
- <span style="color:red">[[agents-md]]</span>
- <span style="color:red">[[memory-poisoning]]</span>
- <span style="color:red">[[codeowners-elders]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-26-ai-agentic-how]]
