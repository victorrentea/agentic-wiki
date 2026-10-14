---
title: CLAUDE.md (Project Memory)
category: concept
tags: [claude-md, memory, drift, context-window, onboarding]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]"]
created: 2026-06-11
updated: 2026-10-13
---

`CLAUDE.md` is the project memory file loaded into every agent session from the current working directory — and because it costs [[context-window]] on turn 1, its hygiene matters more than its completeness.

## <span style="color:red">How it loads</span>

<span style="color:red">Files are **concatenated, not overridden**: everything from the filesystem root down to the working directory is loaded at launch, the more specific file read *last*, and within a folder `CLAUDE.local.md` after `CLAUDE.md`. Contradictions are not resolved — *"Claude may pick one arbitrarily"*. CLAUDE.md files in **sub-folders load lazily**, the first time Claude reads, writes or edits a file there ([memory docs](https://code.claude.com/docs/en/memory#how-claude-md-files-load)). Hierarchical files therefore map naturally onto Maven modules or microservice repos ([[2026-04-07-ai-product-team]]).</span>

<span style="color:red">The tiers ([[memory-layers]]): the **project** `CLAUDE.md` goes on git — it is team infrastructure, a must-have, generated with `/init` so the agent doesn't rediscover the project every session. A gitignored **`CLAUDE.local.md`** holds personal, machine-specific rules for that repo. The **home** `~/.claude/CLAUDE.md` is strictly for preferences you would not give colleagues ("I dictate 90%, expect mangled words") and never goes on git. **`MEMORY.md`** is auto-learned and indexed; only its first 200 lines or 25 KB load at start ([docs](https://code.claude.com/docs/en/memory#auto-memory)). Team rules travel as **skills in a plugin** from an internal [[plugin-marketplace]] ([[2026-07-01-ai-java-community]], [[2026-08-31-ai-software-vendor]], [[2026-05-18-secure-coding-payments]]). Some harnesses add personality files too (e.g. `SOUL.md` in OpenClaw-style setups).</span>

## The hygiene ritual

The opening line of every sprint retrospective on your `CLAUDE.md`:

> "What in here is **absolutely obvious from your training data** that I can safely remove without risking you breaking the rule?"

Out go the platitudes the model already knows: "use constructor injection not `@Autowired`", "run `mvn test` / `npm test`", "validate `@RequestBody`", "keep line length". Then ask: *any internal conflicts? can we make it more concise?* — because a contradiction inside the file is a direct cause of [[hallucination]] (the model silently resolves the tension instead of flagging it).

<span style="color:red">**Exception — GIGO:** keep an "obvious" rule when the surrounding legacy code brutally violates it ("keep methods under 20 lines", lines ≤120 chars). The agent imitates the code it reads, whatever it "already knows" from training ([[2026-08-12-ai-bank]], [[2026-08-06-ai-bank]], [[2026-09-02-ai-insurer]]).</span>

<span style="color:red">**Re-audit at every model generation.** Boris Cherny's advice after Opus 5 was to **delete half** of your CLAUDE.md; Anthropic says it cut over 80% of Claude Code's own system prompt for the 5-generation models ([The new rules of context engineering](https://claude.com/blog/the-new-rules-of-context-engineering-for-claude-5-generation-models)). Beware that asking Opus "what can I safely delete?" invites a sycophantic answer — read the launch docs instead. The official test ([best practices](https://code.claude.com/docs/en/best-practices)): *"Would removing this cause Claude to make mistakes? If not, cut it."* [`/doctor`](https://code.claude.com/docs/en/memory#my-claude-md-is-too-large) now proposes trims for a checked-in file: it cuts what the agent can **derive from the codebase** (layouts, dependency lists, architecture overviews) and keeps **pitfalls, rationale and conventions**. A cheap alternative: ask your weaker day-to-day model "is this line needed, or obvious?" ([[2026-09-02-ai-insurer]], [[2026-09-04-ai-software-company]]).</span>

<span style="color:red">**The retro checklist** (every 2–4 weeks) hunts five things: **contradictions · ambiguities · repetition · the obvious · drift** from the code ([[2026-07-09-ai-live-casino]]). Conflicting rules "make the model schizophrenic, confused, unsure" ([[2026-06-05-ai-consultancy]]). Cadence that works: **everyone adds freely; the team prunes together every two weeks** ([[2026-08-10-ai-consultancy]]). Gamify it — a beer at the retro for a good rule, €1 per line deleted *by hand* (a hand edit is distinguishable from AI bloat); one team condensed 600 contradictory AI-generated lines to 60 ([[2026-07-01-ai-java-community]]). Review markdowns **more** attentively than code: CLAUDE.md and skills are the project's "utility functions", reused everywhere ([[2026-09-04-ai-software-company]]).</span>

## Curate, don't generate

The #1 mistake is dumping your knowledge "like writing your will" or pasting your conventions wiki verbatim. Instead: *"read that wiki, keep only what's special for us, write it concisely in your own words."* <span style="color:red">The evidence is ETH Zürich's *Evaluating AGENTS.md* ([arXiv 2602.11988](https://arxiv.org/abs/2602.11988)): context files do not generally improve task success but raise inference cost by over 20% on average. **LLM-generated** files changed success by −0.5 to −2 pp (not significant) at +20–23% cost; **developer-written** ones gained +2.4 pp on average (also not significant) at up to +19% cost. The paper sets no length target; its advice is to include only the non-standard instructions the agent can't get from the README, and to skip LLM-generated files for now ([[2026-05-11-ai-software-vendor]]; see [[agents-md-discipline]]).</span> The mechanism is sound: a generic memory file is pure [[dumb-zone]] fuel<span style="color:red">, and `/init` boilerplate doesn't just cost tokens, it **distracts** the agent off-task ([[2026-07-23-ai-software-product]])</span>.

<span style="color:red">**CLAUDE.md is for behaviour, README is for humans** — installation instructions belong in the README ([[2026-05-13-ai-it-services]]).</span>

## <span style="color:red">What earns a place</span>

<span style="color:red">Things the agent **cannot deduce from the code** ([[2026-06-05-ai-consultancy]], [[2026-06-08-ai-security-tools]], [[2026-07-01-ai-java-community]], [[2026-07-09-ai-live-casino]]):</span>

- <span style="color:red">**Volumetry and NFRs** — "100k owners in 5 years", SLAs, which APIs are rate-limited. When grilling surfaces such a fact, say **"remember this in CLAUDE.md"** at once so tomorrow it doesn't re-ask ([[2026-07-23-ai-software-product]], [[2026-08-31-ai-software-vendor]]). Volumetry changes often, so one team keeps it in a separate `volumetrie.md` referenced from AGENTS.md ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">**Untouchables and history** — files that must stay byte-identical because a regulator checks their hash in production; lessons from your **post-mortems** (feed them all in and let the agent extract the lessons); political history.</span>
- <span style="color:red">**Structure and versions** — the directory layout with the *semantics* of each folder, library/framework **versions** (so it doesn't invent APIs), and project-specific flows ("we test black-box end-to-end against tools in Docker").</span>
- <span style="color:red">**How to build incrementally** (agents struggle here), the linting/quality tools in play, and pointers to helper scripts (`start db,be,fe`) so the agent needn't rediscover them.</span>
- <span style="color:red">**A domain glossary** mapping business terms to exact code identifiers. When several generations of an algorithm coexist (the internet's classic v1, last year's tweak, this year's), the agent drifts back to the textbook version; baptise each with an uppercase **marker name** from the team's [ubiquitous language](https://martinfowler.com/bliki/UbiquitousLanguage.html) — WHITE / ELEPHANT / TIGER — and map them here; for a legacy monolith with overloaded words keep a `glossary.md` beside it ([[2026-09-04-ai-software-company]]; [[hallucination]] cause #4).</span>
- <span style="color:red">**Persona and reader profile** — the team ("experienced Java/Spring devs") and the users ("we target millennials → no endpoint slower than 0.5 s"; "older users → add a small delay so a too-fast search doesn't feel broken"). In the home file, your own profile: "20 years Java, weak in ops, guessing in frontend ⇒ explain frontend at length" ([[2026-07-01-ai-java-community]], [[2026-09-04-ai-software-company]]).</span>
- <span style="color:red">**Must-follow markers** — flag non-negotiable rules (e.g. `#musthave`) so the agent treats them as hard rules ([[2026-07-01-ai-java-community]]).</span>

<span style="color:red">What does **not** belong:</span>

- <span style="color:red">Training-data banalities (`npm start`, Angular's port 4200) and **task specifics** — those go in the [[spec-driven-development|SDD]] markdowns. Over-specific business logic rarely belongs.</span>
- <span style="color:red">**Anything a tool can check.** A mechanically checkable style rule (tabs vs spaces) is a linter/checkstyle in a hook or pre-push, not a context line; an "artistic" build is a `build.sh`; "route all requests through the proxy" is a test. A **script beats a markdown instruction** — `TESTING.md` just names the coverage-extraction script ([[2026-06-05-ai-consultancy]], [[2026-07-23-ai-software-product]], [[2026-09-04-ai-software-company]]; [[enforcement-ladder]]).</span>
- <span style="color:red">**"We removed X."** Naming the wrong thing plants the idea anyway ([[vibe-heresy]]); Steve Yegge describes the same failure as [agent dementia from conflicting markdown](https://steve-yegge.medium.com/introducing-beads-a-coding-agent-memory-system-637d7d92514a) — agents can't tell "we decided this yesterday" from "a brainstorm three weeks ago" ([[2026-07-23-ai-software-product]]).</span>
- <span style="color:red">**"Don't touch this code"** as a plea. In the dumb zone the agent will do anything to please you — including commenting out the test "just this once". Make it a spec requirement and check the diff at commit; extremist but effective: `chown root` + `chmod -w` the critical files and run the agent as a non-root user ([[2026-06-08-ai-security-tools]]).</span>

<span style="color:red">Write rules **ultra-compressed, "almost not English"** — e.g. `builder chains, one property per line unless only two properties total` ([[2026-04-07-ai-product-team]]).</span>

## Three moves to shrink a bloated file

When a `/init`-generated `CLAUDE.md` balloons to hundreds of lines, apply these in order:

1. **Strip the obvious** — don't tell a Java agent how to run `mvn test`; it knows.
2. **Split into per-folder files** — `frontend/CLAUDE.md` loads only when the agent touches that folder; the root file stays lean. <span style="color:red">Place each rule at the lowest folder that contains all its uses; when two sibling modules need it, escalate it to their common parent ([[2026-05-11-ai-software-vendor]]). Folder-scoped rules belong in a nested CLAUDE.md, not a skill — it loads programmatically on any read/write in that folder ([[2026-07-23-ai-software-product]]).</span>
3. **Extract into a [[agent-skill|skill]]** — durable, reusable knowledge lives in a skill whose body lazy-loads on demand. <span style="color:red">Cross-cutting conventions ("how we build grids", Java style) go here; if a rule must be *forced*, use a glob-bound rule or instructions file instead ([[2026-08-12-ai-bank]], [[progressive-disclosure]]).</span>

<span style="color:red">**Over one screen → split.** `###` sub-sections inside CLAUDE.md are an anti-pattern ("nostalgia for class + method"); the moment you need one, evacuate it into a referenced markdown or a skill ([[2026-05-11-ai-software-vendor]]). Reference by *mention*, not `@include`: an `@`-import is flushed into every context, while a plain link lets a strong model abstain until it needs the file — don't `@`-attach everything "to be sure it sees it" ([[2026-07-01-ai-java-community]], [[2026-05-13-ai-it-services]]). UX rulings made while polishing (fixed column widths, ellipsis + tooltip, sticky header) go into a referenced `ux-design.md` ([[2026-09-04-ai-software-company]]).</span>

<span style="color:red">**Size.** Earlier advice on this page was "under ≈100 lines". The sessions' targets range from ≈60 to ≈200: Boris Cherny's own file is ≈60 lines (54 by one count) of philosophy, mostly pointers to markdowns loaded on demand; Victor keeps his under 100; one team caps at ≈150; the memory docs' own guidance mentions ≈200 ([write effective instructions](https://code.claude.com/docs/en/memory#write-effective-instructions)) ([[2026-06-05-ai-consultancy]], [[2026-08-10-ai-consultancy]], [[2026-08-31-ai-software-vendor]], [[2026-09-04-ai-software-company]]). All of these are heuristics — the ETH study found no clear effect of length — but the direction is the same: **root at ≈60–100 lines, behavioural, mostly pointers.** One session cited degradation past ≈85 rules in total across CLAUDE.md and skills, another trainer heuristic ([[2026-05-18-secure-coding-payments]]).</span> Everything in the root `CLAUDE.md` loads in *every* conversation — it is a constant tax, not a free resource. The real value of a hand-curated file is project NFRs the agent cannot know from training data: expected throughput, row counts, resilience SLAs, which APIs are rate-limited.

The cross-harness equivalent is [[agents-md|AGENTS.md]] (symlink `AGENTS.md → CLAUDE.md` so Codex can read it too<span style="color:red">; Claude Code reads AGENTS.md natively since v2.1.277, but only when no CLAUDE.md exists — when both exist, the documented bridge is an `@AGENTS.md` import inside CLAUDE.md, which also works on Windows where symlinks don't ([docs](https://code.claude.com/docs/en/memory#agents-md))</span>); the full curation discipline — the anti-dumping bar, the seed-of-heresy Socratic enrichment loop, sub-folder injection, retrospect-every-sprint — lives in [[agents-md-discipline]], and this file is the *project* tier of the four [[memory-layers]] (user / project / team / company).

## Growth and drift

A good `CLAUDE.md` **grows like an onboarding doc** — each new rule fixes the reason the AI last went off the rails, the way you'd onboard a brilliant-but-junior colleague. <span style="color:red">**The game:** when Claude generates something you dislike, don't edit the code yourself — prompt Claude to change it; when the same correction repeats, it goes into CLAUDE.md for good ([[2026-04-07-ai-product-team]]). If you had a better idea than the agent, that is *your* failure to give context — add the missing line. After any AI failure, find the root cause and encode it: *"don't tolerate 'AI was being weird'"* ([[2026-08-10-ai-consultancy]], [[2026-05-18-secure-coding-payments]]).</span> Better still, replace hard rules with a [[tripwire]].

<span style="color:red">**Shortcut keywords** live here too: "if I say `SPARTA`, skip writing tests" (fast-experiment mode); a magic word *"remember"* that makes the agent write the rule into CLAUDE.md on the spot ([[2026-04-07-ai-product-team]]). A `/sparta` slash command in another session ran all tests and, if green, made a conventional commit and pushed ([[2026-04-27-ai-healthcare-software]]).</span>

<span style="color:red">**Greenfield vs brownfield:** on brownfield, `/init` reverse-engineers the existing layering — "Claude will imitate whatever architecture you already have"; on greenfield, hand-write one spike first ([[light-dark-code]]).</span>

And mind [[drift]]: bump Spring Boot or Angular and the file goes stale. <span style="color:red">Earlier advice was to refresh it ≈monthly with `/init`; sharper is to **refresh from the git diff** — have the agent analyse the diff since the last CLAUDE.md change and update its own knowledge ([[2026-07-01-ai-java-community]]). For a single drifted chapter one prompt does it: *"the Structure chapter is out of sync, update it with the rest of the modules"* ([[2026-08-12-ai-bank]], [[2026-08-31-ai-software-vendor]]). Doc gardening also deletes **cover-your-ass comments** — "moved X to Y" left in code by an agent terrified of disappointing you; "make no mistakes" in the file only deepens that phobia ([[2026-09-02-ai-insurer]], [[doc-gardener]]).</span> Loading deeper context is governed by [[progressive-disclosure]]; sharing it across repos is the [[cross-repo-knowledge]] pattern.

## See also
- [[progressive-disclosure]]
- [[agent-skill]]
- [[agents-md]]
- [[agents-md-discipline]]
- [[memory-layers]]
- [[tripwire]]
- [[drift]]
- [[hallucination]]
- [[cross-repo-knowledge]]
- [[dumb-zone]]
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[enforcement-ladder]]</span>
- <span style="color:red">[[vibe-heresy]]</span>
- <span style="color:red">[[doc-gardener]]</span>
- <span style="color:red">[[light-dark-code]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
