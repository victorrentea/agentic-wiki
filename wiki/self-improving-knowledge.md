---
title: Self-Improving Knowledge
category: pattern
tags: [knowledge-engineering, claude-md, agents-md, jsonl, automation, retrospective]
sources: ["[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]"]
created: 2026-06-26
updated: 2026-10-13
---

Self-improving knowledge is the practice of running an automated agent that mines your own conversation logs (Claude Code stores every session as JSONL) to find frustration moments, diagnose missing rules or context, and file pull requests to [[claude-md]] or [[agent-skill|skills]] — making the knowledge base improve itself from your own failures without manual retrospective effort.

## The mechanism

Claude Code writes every session to `~/.claude/**/*.jsonl`. An agent can scan these files and:

1. Identify moments where the human appeared frustrated (repeated corrections, explicit rephrasing, "no, not like that" signals).
2. Diagnose *why*: was a rule missing? was a context fact absent? was a [[decision-context]] entry needed?
3. File a PR to the relevant file (`CLAUDE.md`, a skill, a sub-folder `CLAUDE.md`) that would have prevented the failure.

A platform team at one bank runs this weekly — Saturday night, scheduled via background loop — and the team now reviews **hundreds of such PRs per week**. The engineer's job becomes accepting or rejecting AI-proposed self-improvements rather than writing them from scratch. <span style="color:red">Another bank ran *"where did I get angry this week?"* **per employee, in cron** ([[2026-09-30-ai-enterprise]]).</span>

<span style="color:red">The JSONL also holds **summaries of the model's discarded reasoning**, so an auto-activating skill can introspect the *current* conversation when you're angry and find where it derailed ([[2026-07-01-ai-java-community]]). Privacy caveat: the same files hold personal chatter — mind what a mining agent publishes.</span>

## Trigger it in the moment: the feedback skill

- <span style="color:red">**The first skill in any internal [[plugin-marketplace]] should be `feedback`**: it activates when the user seems frustrated, investigates the conversation, and — with consent — opens an issue or PR on the skill owner's repo. *"Upset colleagues = gold mine"* ([[2026-09-02-ai-insurer]]). A `/feedback` command does the same on demand, against the framework repo ([[2026-08-10-ai-consultancy]]).</span>
- <span style="color:red">**SessionEnd hook** variant: retrospect the just-finished conversation and, if the user seemed annoyed, file a PR to the central skills repo — *"the coolest hook"* ([[2026-06-02-ai-open-workshop]], [[agent-lifecycle-hooks]]).</span>
- <span style="color:red">**Cross-team**: open your repo to PRs from colleagues' agents — their feedback skill sends suggestions as a markdown PR to *your* repo; the cheapest learning loop there is ([[2026-09-14-ai-industrial]]).</span>

## `/insights`: a retro on your own prompting

<span style="color:red">Claude Code's `/insights` (Copilot shipped its own in April 2026) scans your conversation history across projects and reports *impressive things*, *rough edges* (*"you ask me to do too many things at once — prefer minimal scope"*), and *next steps* — including where you burned tokens or derailed the agent with imprecise prompts. *"It does psychological profiling — and tells you where you're an AI laggard"* ([[2026-04-16-ai-safety-critical]], [[2026-04-07-ai-product-team]]). Victor's own catch: a Swift project whose CLAUDE.md still said "Python" had cost hours, surfaced only weeks later ([[2026-05-27-ai-open-workshop]]). Make it a **5-minute sprint-retro item**, or you can't tell whether your prompting improves. `/insights` and [AgentsView](https://www.agentsview.io/) also show **cost per session** — one participant found a $900 session ([[2026-09-30-ai-enterprise]]).</span>

<span style="color:red">Caveats: under zero-data-retention or strict privacy setups it may have nothing to read, and it raises the question whether your prompt history is company IP.</span>

## Schedule it

Use a cron-style background loop (Boris Cherny's *chained-loop* feature in Claude Code) to trigger the JSONL-mining agent weekly without needing an open terminal. The output is a batch of draft PRs on the central AI-knowledge repo (see [[cross-repo-knowledge]]).

<span style="color:red">**Mind the retention.** Claude Code deletes local conversations after ≈30 days by default — one participant lost a complex, undocumented investigation ([[2026-08-31-ai-software-vendor]]). Defences: raise [`cleanupPeriodDays`](https://code.claude.com/docs/en/data-usage) to 365; keep **sanitized JSONL** (no metadata, no tool calls) plus a weekly cron that distils conclusions into memory; the agent-managed `MEMORY.md` is *not* deleted ([[two-layer-memory]]); or an [[llm-wiki]] on git. Tools like [Entire](https://entire.io/) push conversations to git, but Victor prefers self-explaining code over storing agent conversations in the repo ([[2026-09-30-ai-enterprise]]).</span>

## Complementary source: git history

A parallel signal is the git history of code-review fixes — especially rejected MRs from newer team members: those commits pinpoint *exactly* the mistakes a fresh agent also makes. Scanning both signals (conversation logs + rejected MRs) gives a high-precision feed for [[agents-md-discipline|AGENTS.md]] improvements.

<span style="color:red">In practice ([[2026-07-22-ai-agentic-how]], [[2026-09-24-ai-bank]]): mine past PR comments via `gh` for the rules newcomers needed, then sanitize the candidates with **Sonnet — Opus over-classifies things as "obvious"** and drops too much. Expect ≈10–20 keepers out of hundreds of comments. To keep the markdown lean, one team "pays" per line (€0.5) and diffs it in the retro: *"100 lines, 4 real lessons."* Seniors' reasoning can be captured too — even **recorded interviews with departing seniors** ([[elder-wisdom-mining]], [[2026-09-28-ai-gaming-tech]]).</span>

## Which markdown let the slop through?

<span style="color:red">Every time slop reaches you, ask **which markdown let it through, then upgrade *that* one** ([[2026-08-10-ai-consultancy]]): CLAUDE.md or a skill for small local gaps; the adversarial reviewer agents (add perf, volumetry, pentest lenses — [[multi-model-review]]) when review missed it; the architecture or task markdowns when the failure was structural; back to Jira when the business missed a case. **Monthly**, feed human PR comments and spec decisions back into review-bot rules, linter/Sonar rules, CLAUDE.md and sub-agent definitions — the human reinforcement loop that gets a team to **half-reviewed code in ≈9 months** ([[gray-factory]]).</span>

## Doc gardening: fight drift, not just gaps

<span style="color:red">The same loop works in reverse — removing what went stale. **Doc gardening** (a term from OpenAI's [harness engineering](https://openai.com/index/harness-engineering/) post) is a weekly cron agent that folds the week's code changes back into the project docs, AGENTS.md and skills so they don't drift ([[doc-gardener]], [[drift]], [[2026-09-25-ai-bank-migration]]). LangChain [Deep Agents](https://www.langchain.com/deep-agents) was cited as an agent that writes no code but cleans a knowledge base of contradictions and drift ([[2026-08-13-ai-insurer]]).</span>

## Why it compounds

Every accepted PR converts a one-time frustration into a standing rule that benefits the entire team going forward. The [[decision-context]] pattern fits naturally: each frustration that traced to a false assumption becomes a one-line entry. Over months, the failure rate of the agent on recurrent task types measurably drops — the same quality-feedback loop that powers [[skills-benchmarking]], but applied to the knowledge base itself rather than individual skills.

<span style="color:red">Beyond weekly gardening, research tools escalate memory from facts → habits → principles: [Hermes Agent](https://github.com/NousResearch/hermes-agent) (Nous Research, "this on steroids" — turns annoyances into lessons automatically), [Mnemosyne](https://mnemosyne.site/) and [Hindsight](https://github.com/vectorize-io/hindsight) ([[2026-09-02-ai-insurer]], [[2026-09-28-ai-gaming-tech]]). Victor's advice is not to build a custom memory system yourself — wait for the vendor (a cynic in the room: a vendor paid per token has little interest in you not recomputing) ([[2026-08-31-ai-software-vendor]]).</span>

## See also
- [[agents-md-discipline]]
- [[claude-md]]
- [[decision-context]]
- [[cross-repo-knowledge]]
- [[skills-benchmarking]]
- [[llm-wiki]]
- [[progressive-disclosure]]
- <span style="color:red">[[two-layer-memory]]</span>
- <span style="color:red">[[doc-gardener]]</span>
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[elder-wisdom-mining]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- [[2026-06-26-ai-agentic-how]]
