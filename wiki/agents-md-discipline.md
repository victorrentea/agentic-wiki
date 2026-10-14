---
title: AGENTS.md Discipline
category: pattern
tags: [agents-md, claude-md, knowledge-engineering, socratic, retrospective, sub-folder, doc-gardening]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-23
updated: 2026-10-13
---

`AGENTS.md` discipline is the curation practice that keeps a project's agent-memory file lean and *earned* — the most important file in your repo right now, and the one most often ruined by dumping into it.

<span style="color:red">Think of it as the **onboarding doc for an AI that has never seen your code** — "the unspoken elders' wisdom" of the repo ([[elder-wisdom-mining]]). It is team infrastructure: commit it without asking for approval, because what your agent knows, your colleagues' agents must know. Putting it in `.gitignore` is "selfishness — sharing is caring".</span>

## The anti-dumping rule

The big anti-pattern is pasting your Confluence style guide into `AGENTS.md`. ≈90% of it is already obvious to the model (pure token waste → faster [[dumb-zone]] → higher cost), and most of the remaining ≈5% is your **personal OCD taste**, not a measurably better option. <span style="color:red">ETH Zürich's *Evaluating AGENTS.md* ([arXiv 2602.11988](https://arxiv.org/abs/2602.11988)) found that context files do not generally improve task success but raise inference cost by over 20% on average; LLM-generated files nudged success down slightly (not significant) and developer-written ones up slightly (also not significant), and the authors advise including only non-standard instructions the agent can't get from the README and skipping LLM-generated files for now (details in [[claude-md]]).</span> <span style="color:red">So `/init` output is a **starting point, not the destination**.</span> The honest confession behind the rule: *"no one builds me a statue for how polished the code is; it's my selfish desire to polish."* <span style="color:red">Victor once wrote 400 lines of clean-code rules; Sonnet told him 90% were obvious.</span>

**The bar for a style rule:** add it only when a **blind team vote (4–5 devs unanimous)** says A is clearly better than B *and* the AI doesn't already treat it as obvious. <span style="color:red">Don't fight the AI on syntax taste (for-loop vs stream) — "you felt smart and emotionally attached to the polish; now no one cares." The exception is **shape consistency** that lowers cognitive load.</span>

<span style="color:red">**Exception — GIGO.** The agent imitates the code it reads: a 1990s EJB codebase made it generate SQL-injectable code. Keep an "obvious" rule (methods ≤30 lines, 120-char lines) **only if your codebase already breaks it**. Better still, refactor the legacy code to your standard, so the agent imitates good code instead of being told not to imitate bad code.</span>

## What actually belongs in it

*"What would you tell a brand-new hire on day one?"* — things **not** in the code:
- architecture layout and module semantics;
- target user and mission (*"trails have no GSM signal, so we need offline mode"*);
- **data volumetry** (*"a user can have up to 1,000 trails"* stops naive full-downloads);
- backend latency and caching constraints (*"you may not cache the bank balance"*).
- <span style="color:red">**the odd stack, named explicitly** (EJB2, Struts, XML config, a start-up cache) — otherwise every chat rediscovers it and burns tokens;</span>
- <span style="color:red">rules & conventions ("when you do X, don't forget Y"), project structure (what is where, who owns what), technologies and versions;</span>
- <span style="color:red">mostly **what NOT to do** — the part `/init` can't infer;</span>
- <span style="color:red">**the WHY** behind a convention — the agent broke an internal convention because nobody told it the rationale (pro move: send it to find the reason in the code and note it);</span>
- <span style="color:red">the **reader profile** (`senior engineers, with a pinch of ADHD`; "2–3 years of Spring") to control verbosity and tone, plus anti-[[sycophancy]] lines: *"challenge ambiguous prompts"*, *"tell me when I'm wrong, don't flatter me"*.</span>

<span style="color:red">These are the project NFRs the model cannot know from training data — see [[claude-md]] for the same "curate, don't generate" principle and the size heuristics.</span>

<span style="color:red">**Volumetry decides architecture.** A brainstorm asked "client-side or server-side pagination?" and the in-memory junior solution it proposed wasn't malice — *the AI had no idea about volumetry*. The right human answer is not "A" but *"I don't know, I'll ask the business how many owners there will be"* — then write it down. Once "10,000 owners in production" was in project memory, it never proposed the in-memory version again. If such facts pile up, keep them in a `volumetry.md` **linked** from AGENTS.md ([[progressive-disclosure]]).</span>

<span style="color:red">**What stays out:** one ticket's details or the fix for one specific bug (house rules only); hand-maintained lists that [[drift]] (*8 folders in the doc, 2 in reality*) — point at a generated artefact instead (`up-to-date APIs in openapi.yaml`, regenerated by a test such as [OpenApiExtractorTest](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/test/java/victor/training/petclinic/guardrail/OpenApiExtractorTest.java); see [[openapi-contract-sync]]); daily corporate-environment quirks ("PowerShell is blocked here"), which belong in your global home rules ([[memory-layers]]); and anything enforceable (below).</span>

**Prefer [[decision-context]] over verbose ADRs.** "An ADR for humans is one line for Opus." Replace pages of problem/alternatives/decision with one terse line per decision that names the choice and the constraint behind it (`"BullMQ — producer imports the worker directly; retry N times on failure"`). Point `AGENTS.md` at a `project-mission.md` for things the model can never infer ("our users are millennials, no latency >1s"). Keep existing ADRs; just don't paste their prose verbatim into the agent's memory — reference the file and let the model load it via [[progressive-disclosure]] when needed. <span style="color:red">The same goes for architecture and testing rules: a separate `architecture.md` and `testing.md` (later split per backend / frontend / e2e) referenced from AGENTS.md — an 800-line `testing.md` is unusable.</span>

<span style="color:red">**How to phrase rules.** Positively when you can ("reuse the standard components in `/components/**`" rather than "don't create new components") — LLMs, like children, get confused by negations; `don't` is fine when the wrong path is easier to name than the right one. **Pair rules across roles** or they're wishful thinking: a frontend "consistent error handling" rule needs a backend rule returning the same error shape; frontend adds `data-automation` attributes *for QA*, and the QA rule says "use only those stable locators". When corporate and team rules conflict, **say explicitly which one wins** — contradictions are a direct [[hallucination]] cause.</span>

<span style="color:red">**Lines that backfire:** *"make no mistakes"* makes the agent panic and fake "all tests pass"; anything that invites [sycophancy](https://arxiv.org/abs/2310.13548) — ask for "a thinking partner, not a yes man" instead. AI is also biased to *add* code, not delete it ("backwards-compatibility heresy", after Steve Yegge) and cowardly about APIs, duplicating code rather than changing an endpoint. Useful standing rules: *"if you add a new endpoint, remove the old one in the same commit unless told otherwise"* and *"never stay backwards-compatible with commits inside the same unmerged PR"* ([[ai-never-deletes]]). Delete the cover-your-ass comments agents leave to excuse deletions. Worth stealing from Boris Cherny: *"never mark a task complete without proof"*.</span>

## <span style="color:red">When to add a line</span>

<span style="color:red">**Only after you've seen it break — never preventively.** "Line length < 120" added in advance was useless: no model writes 120-char lines unprompted (unless your code does). Add a line *exactly when your cortisol rises*, when the agent catches you offside — "parenting-style lessons learned"; utility is proven, not decreed. One tension: some teams wait until the **third** repeat of a mistake, others react to the first annoyance. Too eager → bloat; too late → repeated pain.</span>

<span style="color:red">**Every failure is missing information.** *"Whenever the AI misses a task, it's because you didn't give it enough information"* — usually because that information **is not on git**: it lives in Confluence, SharePoint, Excel tabs, or a diagram on the architect's desk. Move it into the repo. Likewise **every "stupid question" the agent asks is a missing line** ("you think it's stupid because you know more than the agent"), and any failure ⇒ +1 row in AGENTS.md or a SKILL.md: correcting only in chat fixes today, and tomorrow's fresh chat derails identically. Before re-rolling a failed attempt, ask *"how do we prevent this next time?"*</span>

<span style="color:red">**Correct the agent, not the code.** Don't touch the file yourself — tell *it* to change it "…and remember this for the future": double effect, toward one-shot perfection. Live-born rules this way: no Spring bean if nothing from Spring is needed (it had invented a `@Component` with no injected field); no private constructor on utility classes. After a frustrating conversation, a retrospective prompt does it in bulk: *"look at this conversation and extract lessons learned so you don't annoy me again — persist them for the whole project so my colleagues aren't annoyed either"* ("why did it take you 10 minutes to start the app? what did you need in AGENTS.md?"). AI-written rules are fine as long as a human reviews them later — at the standup or the retro.</span>

## The seed-of-heresy Socratic loop

**Trigger: whenever the agent makes you angry.** Don't scold (*"you're wrong, do X"*) — that triggers [[sycophancy]] and teaches nothing. Instead ask *"why?"* up to five times, calmly, to find the **"seed of heresy"** — the one missing assumption — then encode *that* in `AGENTS.md`. Correcting the agent's **output** is one-shot waste; correcting its **assumption** prevents an entire *class* of future mistakes — a multiplicative return on one Socratic session. (Worked example: the agent duplicated rules in root and sub-folder files; "how did you reason?" revealed it feared the nested file wouldn't be injected. A live rule born this way: *"dates sort as dates, never lexicographically."*) <span style="color:red">Phrasings that work: *"Can you explain how you ended up in this cornfield? What was your reasoning?"*, *"what led you to believe that was right?"*, or the power prompt *"Explain how you reached this conclusion. Analyse the reasoning behind it. Why would you propose X directly? Stop flattering me!"* — "do not call their baby ugly". What you usually find is something it couldn't know: 10,000 req/s, a million rows, start-up under 3 s, another API client.</span>

**Mine your own conversation logs.** Claude Code stores every session as JSONL in `~/.claude/`. A weekly background agent can scan these logs, find moments where you were frustrated, diagnose what rule or context was missing, and file PRs to `AGENTS.md` or skills. See [[self-improving-knowledge]] for the full pattern. An equally rich signal: scan git history for past **code-review fixes**, especially rejected MRs from newer team members — those commits identify exactly the mistakes a fresh agent also makes. <span style="color:red">Old PR comments from the strictest reviewer are house rules already written; one bank records hours of sessions with retiring experts to clone their knowledge ([[elder-wisdom-mining]]). And Claude Code's **auto memory** is *selfish* — per user, in the home folder: when it captures a project fact ("100k owners"), move it into AGENTS.md so the team gets it.</span>

<span style="color:red">**Gamify it.** Bonus the devs who enrich `AGENTS.md` the most — one tech lead paid a token sum per line added (≈0.5 RON/line, from his own pocket), because moving expertise from heads into markdown is knowledge transfer, not bureaucracy. Pair it with a reward per line *deleted* at the retro (cf. [[claude-md]]).</span>

## Sub-folder injection (context scoped like imports)

<span style="color:red">Split `AGENTS.md` per sub-folder</span><span style="color:red">, so module-specific rules are paid only when the agent works in that module — one root `AGENTS.md` measured **2,518 tokens**, paid on every task even when you only touch the frontend</span>. <span style="color:red">Compose/UI rules belong in `/app/AGENTS.md`, not root, or the networking module needlessly pays for "Composables are PascalCase." Mental model: root file = package-level import, sub-folder file = class-level.</span> (Smart models like Opus even refuse to open `.md` files they don't need, protecting their own context.)

<span style="color:red">**Loading is harness-dependent — test yours.** Claude Code's native file is CLAUDE.md: root-level files load at launch, sub-folder CLAUDE.md files load lazily the first time the agent reads, writes or edits a file in that folder, and all of them are *concatenated*, not overridden ([docs](https://code.claude.com/docs/en/memory#how-claude-md-files-load)). Since v2.1.277 (Sept 2026) Claude Code also reads AGENTS.md natively — but by default only when no CLAUDE.md exists in the working directory or above it, and a nested `AGENTS.md` loads on Read only if that folder has no CLAUDE.md; when both kinds of file exist, bridge with an `@AGENTS.md` import inside CLAUDE.md ([docs](https://code.claude.com/docs/en/memory#agents-md); see [[agents-md]]). Sessions reported that GitHub Copilot CLI loads nested files while OpenCode and Cursor did not. The **"Ferrari test"** settles it in 5 seconds: write "I have a red Ferrari" in the file, open a new conversation and ask "what car do I have?".</span>

<span style="color:red">**The failure mode: cross-cutting facts hidden in a sub-folder.** A brainstorm run from the repo root asked "client-side or server-side pagination?" although the team had recorded "≈10,000 owners in production" — in `backend/AGENTS.md`, which the root session never loaded. Put **volumetry, NFR targets and compliance constraints** at the top level, or at least a one-hop index line (*"for DB volumetry and SQL-tuning rules see `backend/AGENTS.md`"*) — agents chase one-hop references well.</span>

<span style="color:red">**Path-scoped rules** are the finer-grained version: a DB-query rule (*"prefer `EXISTS` over `JOIN` for parent–child filters"* — avoids cardinality explosion and lying page counts) loaded only when the agent touches repository files. In Claude Code that is `.claude/rules/*.md` with `paths:` frontmatter, injected when Claude reads, writes or edits a matching file ([docs](https://code.claude.com/docs/en/memory#path-specific-rules); example [pom-rule.md](https://github.com/victorrentea/petclinic/blob/main/.claude/rules/pom-rule.md) on any `pom.xml`) — the equivalent of Copilot's path-scoped instructions and [[kiro]] inclusion modes. Skills take a `paths` glob too; one live demo observed a path-scoped skill not firing when the agent opened the file with bash `cat` instead of the Read tool — check your version.</span>

## <span style="color:red">Keep it small: the shrinking order</span>

<span style="color:red">Targets vary by trainer — Boris Cherny's own file is ≈50 general lines ([copy in petclinic](https://github.com/victorrentea/petclinic/blob/main/.claude/boris-CLAUDE.md): fix root causes, simplest change, one task per sub-agent); "under 100" is the common target, ≈200 the alarm. None of these numbers comes from a study ([[claude-md]] has the evidence). A ≈150-line AGENTS.md usually signals the team needs **more skills**, not more lines. When it grows past the alarm, or `###` sub-chapters appear:</span>

1. <span style="color:red">Remove contradictions and verbosity.</span>
2. <span style="color:red">Move module rules into nested files (`backend/AGENTS.md`).</span>
3. <span style="color:red">Extract any chapter over ≈20–50 lines into a **skill** — 36 Java clean-code rules loaded during a front-end tuning task make a dizzy agent. Prompt: *"Move the Java Code Style section out of AGENTS.md into a new skill **in this project**"* ("in this project" is mandatory, or it lands in your home folder) — and delete the mention from AGENTS.md: the skill loads by its description, so a pointer is redundant ([[agent-skill]]).</span>
4. <span style="color:red">**Script what can be scripted.** Of ≈15 Java rules only ≈3 were not scriptable; the rest went to Sonar, the formatter, Checkstyle, an import-grep and [[openrewrite|OpenRewrite]]/Refaster.</span>

<span style="color:red">**Never put an enforceable rule in markdown.** "Every commit message carries `[JIRA-1231]`" belongs in a local + remote **git hook** that fails the push — the agent sees the error and complies. *"I don't kindly invite it not to forget. We use the whip: you may not."* A deterministic guardrail (linter, `mvn test` in a pre-push hook) is **silent when the code is good → 0 tokens** ([[tripwire]], [[enforcement-ladder]]).</span>

<span style="color:red">**Write for your weakest model.** Write AGENTS.md for a Sonnet, not an Opus, and have a Sonnet (or "any Haiku") tell you what is redundant. **Rules expire:** one session relayed Boris Cherny's observation that moving from Opus 4.8 to Opus 5 made ≈half of his instructions useless — so at every new model, ask it what has become redundant. Old skills written for an older model **micromanage the newer one**; clean them out too.</span>

## Retrospect, and prove rules work

- **Retrospect `AGENTS.md` every sprint** — drop the now-obvious, resolve conflicts (a contradiction inside the file is a direct [[hallucination]] cause). <span style="color:red">Open the retro by projecting the **markdown diff** — "what did we teach the child this sprint", who added what — and mob-review it (every ≈2 weeks; every line is paid on every future task).</span>
- **Hardcore proof a new rule works:** revert everything, `/clear`, and re-run the task from scratch to confirm the rule alone changes the outcome — *"don't tell your boss, it costs a fortune."* <span style="color:red">The serious version is statistics, not anecdotes: ≈3 tasks of different complexity × 3 runs with the rule and 3 without, or ≈10 tasks with vs. without the line; for a skill, a fresh session with the same prompt ≈20 times on both Opus and Sonnet ([[skills-benchmarking]]). A too-short skill description means it **never fires** (seen live: a missing `@Validated` never reported).</span>

## <span style="color:red">Gardening, not accumulation</span>

<span style="color:red">Rules are **maintained, not accumulated**. "Update AGENTS.md on every change" auto-learning loops explode the file — sessions saw 2,000, ≈3,000 and once **8,000 lines** (≈700 tokens of them cover-your-ass apologies about deletions) within weeks. Stale markdown also lets agents **resurrect code you deleted** ("here lies the body"). The fix is a scheduled **[[doc-gardener]]**: a weekly or nightly agent (one team runs it at 23:30, another Sunday 10 PM) that reconciles rules across levels, dedupes, realigns docs with code and proposes *shrink* PRs — "an `/init` run continuously" ([OpenAI harness engineering](https://openai.com/index/harness-engineering/)). Run it on leftover weekly or monthly quota.</span>

<span style="color:red">For drift you spot yourself: **tell the agent to fix it** ("the structure in Project Overview is stale, update it") — never type in AGENTS.md yourself, and don't `/init` inside an old conversation (99k irrelevant tokens); `/clear` first. A drift-guard rule helps: refuse a commit that changes structure without updating AGENTS.md in the same PR (a "Project Overview" claimed 2 modules while 7 existed on disk) — Victor's version: before every commit, fix the drift.</span>

## <span style="color:red">Team distribution</span>

<span style="color:red">Team knowledge — skills, MCP configs, hooks, the team CLAUDE.md — belongs in a shared git repo or [[plugin-marketplace]], never in a personal `~/.claude` ([[cross-repo-knowledge]]). Commit the spec framework's markdowns and **tailor them**: a framework untouched after six months was never made yours; fork a published team plugin rather than adopting it as-is. Personal taste can still be layered on top, e.g. a `PostToolUse` hook on the Skill tool that appends your extra rule when a shared skill activates.</span>

<span style="color:red">**Language.** Sessions consistently found English prompts cheaper and more precise for architecture and hard debugging; one reported Romanian at ≈+10% tokens and ≈15% less precision (a session estimate, not a published benchmark) — see [[wispr-flow]].</span>

`AGENTS.md` is the [[agents-md|cross-tool standard]] file; this page is how to *curate* it. Harness-specific automation (hooks, skills, permissions) still lives alongside it, not inside it.

## See also
- [[agents-md]]
- [[claude-md]]
- [[decision-context]]
- [[self-improving-knowledge]]
- [[doc-gardener]]
- [[elder-wisdom-mining]]
- [[sycophancy]]
- [[hallucination]]
- [[memory-layers]]
- [[tripwire]]
- [[enforcement-ladder]]
- [[progressive-disclosure]]
- [[agent-skill]]
- [[skills-benchmarking]]
- [[cross-repo-knowledge]]
- [[drift]]
- [[observation-points]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
