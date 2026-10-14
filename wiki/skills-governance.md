---
title: Skills Governance
category: pattern
tags: [skill, team, knowledge-engineering, marketplace, conflict, scaling]
sources: ["[[2026-04-27-ai-healthcare-software]]", "[[2026-06-23-ai-device-maker]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-23
updated: 2026-10-13
---

Skills governance is the organizational discipline of deciding *who owns which skill-set* — avoiding both the megalomania of one set for everyone and the dichotomy of every dev with their own.

## The two failure extremes

- **Megalomania** — one skill-set imposed on hundreds of company devs. Different teams, stacks, and conventions mean most rules don't fit; conflicting rules just confuse the agent. <span style="color:red">Not Java style imposed on 40 developers, and not a company plugin built for 4,000 of whom 20 use it ([[2026-09-28-ai-gaming-tech]], [[2026-09-02-ai-insurer]]).</span>
- **Dichotomy** — every developer maintaining their own personal skills. Knowledge fragments, nothing is shared, and the same lessons get re-learned in isolation.

## The sweet spot: one skill-set per team / per project

Layer a **team/project skill-set on top of a company base**, loaded by the harness in order (see [[memory-layers]]). The company layer carries universal conventions; the team layer carries what's specific to that family of repos. This keeps each set small enough to stay out of the [[dumb-zone]] and coherent enough that rules don't fight each other.

<span style="color:red">**Three tiers, split by owner and by kind of content** ([[2026-09-25-ai-bank-migration]], [[2026-09-28-ai-gaming-tech]], [[2026-10-13-ai-bank]]):</span>

| <span style="color:red">Tier</span> | <span style="color:red">Holds</span> | <span style="color:red">Example</span> |
|---|---|---|
| <span style="color:red">company</span> | <span style="color:red">**tool wrappers** and a few hard standards</span> | <span style="color:red">browser, DB CLI, Confluence and tracker CLIs; a security-review skill</span> |
| <span style="color:red">team</span> | <span style="color:red">**conventions** — code style, workflow</span> | <span style="color:red">a team skills repo, built bottom-up by a team of ≈7 who agree</span> |
| <span style="color:red">personal</span> | <span style="color:red">taste and working style</span> | <span style="color:red">home-folder skills, e.g. an output-style skill</span> |

<span style="color:red">One "AI champion" per tool wraps it and publishes to the skill repo; colleagues install it as an agent plugin from source ([[2026-09-25-ai-bank-migration]]). Distribution evolves from a team git repo **symlinked into every project's skills folder and pulled daily by cron** to an in-house marketplace ([[2026-10-13-ai-bank]]) — three git tiers, **never a stranger's marketplace** ([host your own marketplace](https://code.claude.com/docs/en/plugin-marketplaces#host-your-marketplace); see [[plugin-marketplace]], [[skill-marketplace-security]]).</span>

<span style="color:red">Promotion runs both ways: top-down standards, and **bottom-up "escalate shared"** — periodically, knowledge engineers inspect the repos, find rules and skills that converged, and promote them up a tier ([[2026-04-27-ai-healthcare-software]]).</span>

## Extending a corporate skill from a project

<span style="color:red">A project often needs to *extend* a company-wide skill rather than fork it. Conventions under debate ([[2026-04-27-ai-healthcare-software]], [[2026-09-28-ai-gaming-tech]]):</span>

- <span style="color:red">**(A) explicit extension point** — the corporate skill says "if you find a project-local skill named `<name>-extension`, also load it";</span>
- <span style="color:red">**(B) self-attaching extension** — the local skill's description says "load me whenever `<corporate-skill>` is loaded";</span>
- <span style="color:red">**(C) a hook on skill activation** that loads the extension deterministically ("class X extends A").</span>

<span style="color:red">The April session's account of which of A/B triggers more reliably is internally inconsistent, so test both in your harness; C trades model judgement for a mechanism, in line with [[determinism-over-llm]]. Vendors are designing different composition conventions — bet on the cross-harness agent-skills standard (see [[agent-skill]]) as the neutral baseline.</span>

## Keeping the marketplace alive

<span style="color:red">**Without a feedback skill, the marketplace dies.** A skill that turns agent frustration into a **PR back to the marketplace** keeps it current ([[2026-09-28-ai-gaming-tech]]; cf. [[self-improving-knowledge]]).</span>

<span style="color:red">Keep review-only rules out of the coder's always-loaded memory: put them in **`REVIEW.md`**, read by Claude Code's `/code-review` ([docs](https://code.claude.com/docs/en/code-review#review-md); [[2026-09-28-ai-gaming-tech]]).</span>

## Practical notes

- **Adapt, don't adopt blindly.** A common pattern: pull best-practice skills off the internet, rewrite them "the team way," and publish the result as a marketplace plugin for the team's harness (Copilot CLI / Claude Code). A downloaded [[agent-skill]] is someone's opinionated way of working — use it to learn the delta, then make your own. <span style="color:red">The stronger version: *"Don't download skills from the internet. Build your own — by correcting AI day after day the skill emerges naturally — then share it inside only."* A skill body is a prompt, and prompts can turn into actions: vet community skills like npm packages before promoting them into a shared bucket ([[2026-04-27-ai-healthcare-software]]).</span>
- <span style="color:red">**Don't adopt someone's 32-agent mega-framework** (e.g. [oh-my-claudecode](https://ohmyclaudecode.com/)) — build your own and stop where you stop seeing benefit, so you know what's running. Every "agentic framework" is a folder of markdown: other people's opinions. *If you haven't rewritten your workflow markdowns in six months, you're a user of the vendor's defaults, not a driver of your own.* Growing your workflow ≈2 h/week gets you there in 1–2 months ([[2026-08-12-ai-bank]], [[2026-08-10-ai-consultancy]]; see [[superpowers]]).</span>
- <span style="color:red">**One person per spike**, then share the scripts — parallel duplicate experiments across a team are burnt tokens ([[2026-08-12-ai-bank]]).</span>
- **Conflicting rules confuse the agent** — the same reason an over-stuffed `AGENTS.md` hurts (see [[agents-md-discipline]]).
- The team layer is operationalized as a **shared Git repo** of skills — the [[cross-repo-knowledge]] central-team-repo pattern.

## See also
- [[agent-skill]]
- [[memory-layers]]
- [[cross-repo-knowledge]]
- [[agents-md-discipline]]
- [[custom-agent]]
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[skill-marketplace-security]]</span>
- <span style="color:red">[[skills-benchmarking]]</span>
- <span style="color:red">[[self-improving-knowledge]]</span>
- <span style="color:red">[[superpowers]]</span>
- [[2026-06-23-ai-device-maker]]
- <span style="color:red">[[2026-09-28-ai-gaming-tech]]</span>
- <span style="color:red">[[2026-10-13-ai-bank]]</span>
