---
title: OpenSpec
category: tool
tags: [spec-driven, skill, workflow-enforcement, scaffolding, handover]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-31-ai-software-vendor]]"]
created: 2026-06-11
updated: 2026-10-13
---

OpenSpec is a tool that scaffolds opinionated [[spec-driven-development]] markdown into `.claude/skills` and enforces the spec workflow in waves through a backing CLI.

## How it works

[OpenSpec](https://github.com/Fission-AI/OpenSpec) installs a set of [[agent-skill|skills]] whose markdown is **one opinionated take** ("50k likes, but one opinion") — so **tailor them**; commit the defaults first so you can adjust later. Setup is `openspec config profile` (enable **new** and **continue**, off by default), then `/openspec new change` and paste in the [[handover]] from your grill conclusions.

The crucial design point: the skill **calls a tool** (`bash openspec`) that **enforces** the workflow — the AI itself can only be "kindly required" (the [[tripwire]] gradient), while the tool does the actual enforcing. This is the same "a hook beats discretion" insight from [[progressive-disclosure]].

<span style="color:red">**Install and init** ([[2026-04-16-ai-safety-critical]], [[2026-07-01-ai-java-community]]): `npm install -g @fission-ai/openspec@latest`, then `openspec init` and tick the agents you use (Claude Code, Copilot, Cursor, Codex, Kiro, OpenCode, Windsurf…). `init` merely **drops files into the folders each tool already scans** (`.claude/`, `.github/prompts/`, `.windsurf/`) as slash commands or skills — no daemon, no service; *"no magic: saved bits of prompt on disk"*, which is all a skill is ([[2026-05-13-ai-it-services]], [[2026-06-08-ai-security-tools]]). **Open a new terminal** afterwards so the new skills (and the new `PATH`) load ([[2026-08-03-ai-telecom-ops-qa]]).</span>

<span style="color:red">**Portability caveat:** the markdown and config are portable and there is no vendor lock-in, but the `openspec` **CLI must be installed on every machine** — the slash commands call it. (An earlier "100% portable, copy and go" claim was corrected in the same session.) Enterprise friction to expect: admin rights for a global npm install on Windows, a proxy or firewall in front of the public registry, and a binary that doesn't land on `PATH` — run `npm config get prefix` and add that directory ([[2026-04-16-ai-safety-critical]], [[2026-05-13-ai-it-services]]).</span>

## Commands: prefer `new` + `continue` over `propose`

<span style="color:red">The workflow commands ([[2026-04-16-ai-safety-critical]], [[2026-05-11-ai-software-vendor]], [[2026-07-01-ai-java-community]]):</span>
- <span style="color:red">`propose` — writes proposal, design and tasks in one go.</span>
- <span style="color:red">`new` — proposal only; `continue` — the next artifact (design, then tasks).</span>
- <span style="color:red">`apply` — implement, ticking tasks; `archive` — fold the change into the baseline `specs/`.</span>
- <span style="color:red">`explore` — reverse-engineer current behaviour from the code before a large change (distinct from [[grill-me|grilling]] the human).</span>

<span style="color:red">**Prefer `new` + `continue` for real features.** `propose` burns tokens, risks inconsistency and gives no checkpoint to correct the proposal before it cascades into design: *"the agent panics when something contradicts what's already locked in earlier files — baby-step and catch contradictions while cheap"*. Rule of thumb: `propose` for quick spikes, `new` + `continue` for anything you will review. If the design changes late, `tasks.md` goes stale — regenerate it ([[2026-04-27-ai-healthcare-software]], [[2026-05-04-ai-telecom]], [[2026-06-05-ai-consultancy]]). Kick off with `/openspec new change` (or `/propose`) pointing at the grill's conclusions or the raw issue.</span>

## What it produces

The three SDD artifacts — `proposal.md`, `design.md`, `tasks.md` (see [[spec-driven-development]] for who owns each). The proposal captured a real grill: server-side pagination + sorting, cap page size at 20, avoid N+1 via batch-load, migrate the frontend display, and regenerate-and-commit the OpenAPI contract so a renamed Java field breaks the frontend build — [[drift]] made impossible. <span style="color:red">A later run of the same feature ticked 21/23 checkboxes and touched 25 files (+574/−190); each todo was a decision already made in markdown — defaults `page=0,size=10,sort=name,asc`, `@BatchSize(50)`, a Flyway migration with three indexes, `EXPLAIN` verification at ≈10k rows ([[2026-08-06-ai-bank]]).</span>

## Caveat: `npm i -g` runs code — sometimes

<span style="color:red">An earlier version of this page said a post-install script "silently registered the `openspec` command". That was wrong: the `openspec` command comes from npm's **`bin` linking**, which is not a script. What did exist, from 0.17.0 (Dec 2025) to 1.9.0, was a `postinstall` that **auto-installed shell completions** (writing e.g. to `~/.zsh/completions`) up to 1.2.0 and only printed a tip from 1.3.0; since 1.10.0 (Aug 2026) there is no install script at all ([npm registry metadata](https://registry.npmjs.org/@fission-ai/openspec)). The lesson stands: `npm install -g` *can* run arbitrary code on your machine, and older OpenSpec versions did — check `scripts` before a global install, and see [[supply-chain-attack]] and [[slopsquatting]].</span>

## The four artifacts and who reviews them

OpenSpec emits four reviewable markdown files that map to four stakeholder roles:
- **Proposal** — functional, non-technical → review with the **business / PO**. <span style="color:red">When it comes back full of DTOs, nullables and HTTP 400s, ask the agent to rewrite it in human language **and to modify the OpenSpec templates** so it doesn't recur ([[2026-08-06-ai-bank]]). Some teams keep the proposal in the issue tracker and start OpenSpec at the design ([[2026-05-11-ai-software-vendor]]).</span>
- **Design** — architecture and code decisions → review with **developers** (pair/mob). Include a `## Non-goals` section. <span style="color:red">Read it *after the business people leave* — *"this is where your brain dies; call for help if you faint"* ([[2026-08-06-ai-bank]]).</span>
- **Tasks** — the AI's execution checklist, git-tracked progress; minimal human review needed.
- **Spec** — acceptance criteria → review with **QA**, then convert to [[acceptance-test-bdd|Gherkin `.feature`]] files so the agent can verify its own work against an independent oracle. <span style="color:red">`specs/<capability>/spec.md` is *"the hidden gem"* most people miss ([[2026-04-27-ai-healthcare-software]]): Given/When/Then that is almost Gherkin, yet *"criteria, not tests"* — not runnable until the AI writes the cucumber glue. QA reviews it by trying to break it (*"what if I type 1M characters?"*) ([[2026-07-01-ai-java-community]]).</span>

The key guard: QA (or a human) must lock the `.feature` files *before* implementation starts. If the agent generates both the Gherkin and the implementation in the same pass, the test is no longer an independent oracle.

## Editing and tailoring the output

- <span style="color:red">**Never hand-edit the generated markdowns.** When someone did, the AI missed updating the correlated files. Ask the agent for the change and add the rule *"when we review OpenSpec docs, update all documents affected by the change"* ([[2026-04-16-ai-safety-critical]]).</span>
- <span style="color:red">**Steer the granularity.** On a first run OpenSpec split one screen into three capability specs (search / sort / paginate); force it to merge them into one "Owner page" spec. Pre-creating empty business-area folders in `openspec/specs/` steers it toward vertical slices ([[2026-05-04-ai-telecom]]).</span>
- <span style="color:red">**Tune the templates.** OpenSpec is pragmatic, minimal markdown — but *"if in 6 months you blindly follow other people's taste, you have no taste"* ([[2026-08-31-ai-software-vendor]]).</span>

## `tasks.md` is the new ticket

<span style="color:red">The checkboxes the agent ticks in `tasks.md` have three uses ([[2026-08-06-ai-bank]], [[2026-04-16-ai-safety-critical]], [[2026-05-27-ai-open-workshop]]):</span>
1. <span style="color:red">**Resume** after a compaction, a crash or an exhausted quota — they survived a mid-`apply` compaction and the agent picked up where it left off.</span>
2. <span style="color:red">**Hand over** a half-done change to another agent, or to a colleague who still has quota (see [[handover]]).</span>
3. <span style="color:red">**Drive a [[ralph-loop]]** — for a long `tasks.md` (≈200 lines, 28+ tasks) that won't fit one context, a `while not done` shell loop starts a fresh agent per chapter and runs all tests after each ([[2026-06-08-ai-security-tools]]). Some teams leave ≈6 tasks before going home and find ≈6 green PRs in the morning ([[2026-05-13-ai-it-services]]).</span>

<span style="color:red">The agent may fuse tasks to avoid re-reading files ([[2026-07-01-ai-java-community]]). One session's prediction: *"in 5 years this may replace Jira"* ([[2026-05-04-ai-telecom]]).</span>

## Archive: specs become living documentation

<span style="color:red">`openspec archive <change>` folds a completed change's spec deltas into the baseline `specs/`: the change folder retires, and each functional area's `spec.md` accumulates its When/Then criteria — future changes to the same screen merge into it. It is the middle path in the keep-vs-burn debate: the functional spec survives, the transient proposal/design/tasks retire ([[2026-05-11-ai-software-vendor]], [[2026-05-13-ai-it-services]], [[2026-07-01-ai-java-community]]). Even removing a feature becomes a change: `propose "remove feature X"` ([[2026-05-27-ai-open-workshop]]). Expired design decisions can be distilled into one-line [[decision-context]] entries.</span>

<span style="color:red">**Brownfield:** OpenSpec is designed for existing codebases and can *discover* requirements from code into an implicit backlog ([[2026-05-11-ai-software-vendor]], [[2026-08-31-ai-software-vendor]]). Honest caveat: reverse-engineering specs from a legacy codebase *"works better in theory"* — Jira and the tests remain the truth ([[2026-05-27-ai-open-workshop]]). Open scaling questions: rebucketing `specs/` as archives accumulate, and nested specs for a 15-module modulith; one session claimed archive can flag incompatible OpenAPI changes when merging — unverified ([[2026-04-07-ai-product-team]]).</span>

## Alternatives

<span style="color:red">OpenSpec is one of ≈7 phase-forcing frameworks ([[2026-08-03-ai-telecom-ops-qa]]): [GitHub Spec-Kit](https://github.com/github/spec-kit), [[kiro]] (which has it natively behind its "spec" button), BMad, [[superpowers]] and get-shit-done are the ones named in sessions ([[2026-05-13-ai-it-services]], [[2026-05-27-ai-open-workshop]]).</span>

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
- <span style="color:red">[[ralph-loop]]</span>
- <span style="color:red">[[kiro]]</span>
- <span style="color:red">[[superpowers]]</span>
- <span style="color:red">[[grill-me]]</span>
- <span style="color:red">[[decision-context]]</span>
- <span style="color:red">[[acceptance-test-bdd]]</span>
- <span style="color:red">[[slopsquatting]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
- <span style="color:red">[[2026-04-07-ai-product-team]]</span>
- <span style="color:red">[[2026-04-16-ai-safety-critical]]</span>
- <span style="color:red">[[2026-04-27-ai-healthcare-software]]</span>
- <span style="color:red">[[2026-05-04-ai-telecom]]</span>
- <span style="color:red">[[2026-05-11-ai-software-vendor]]</span>
- <span style="color:red">[[2026-05-13-ai-it-services]]</span>
- <span style="color:red">[[2026-05-27-ai-open-workshop]]</span>
- <span style="color:red">[[2026-06-02-ai-open-workshop]]</span>
- <span style="color:red">[[2026-06-05-ai-consultancy]]</span>
- <span style="color:red">[[2026-06-08-ai-security-tools]]</span>
- <span style="color:red">[[2026-07-01-ai-java-community]]</span>
- <span style="color:red">[[2026-08-03-ai-telecom-ops-qa]]</span>
- <span style="color:red">[[2026-08-06-ai-bank]]</span>
- <span style="color:red">[[2026-08-31-ai-software-vendor]]</span>
