---
title: Plugin Marketplace (team skill distribution)
category: tool
tags: [plugins, skills, distribution, claude-code, copilot, governance, supply-chain]
sources: ["[[2026-09-07-ai-bank]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-12-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

A [plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) is a git repo wired into Claude Code (or Copilot) so that one engineer's push reaches every teammate at their next session start [[2026-09-07-ai-bank]].

## What a plugin carries

A marketplace is just a git repo listing plugins. A plugin ships **[[agent-skill|skills]], [[sub-agents]], [[agent-lifecycle-hooks|hooks]], slash commands and MCP servers** together as one versioned unit — plus LSP servers, background monitors and a `bin/` folder put on the PATH ([plugin structure](https://code.claude.com/docs/en/plugins#plugin-structure-overview)) [[2026-09-04-ai-software-company]] [[2026-08-27-ai-it-services]]. It is the natural home for an [[internal-tool-cli-skill]] (CLI + `SKILL.md`) [[2026-07-30-ai-safety-critical]], or for ready-made agents such as Playwright's planner / generator / healer [test agents](https://playwright.dev/docs/test-agents) [[2026-09-10-testing-ai-bank]].

It is the last rung of the skill **distribution maturity ladder**: same git repo → shared repo → plugin marketplace, auto-pulled on every change [[2026-08-12-ai-bank]] — *"push once and the knowledge falls on everyone"* [[2026-07-03-ai-bank]].

## Setting one up

- **Start from a folder.** A marketplace is a `.claude-plugin/marketplace.json` listing plugins; petclinic's [plugin-marketplace-demo](https://github.com/victorrentea/petclinic/tree/main/plugin-marketplace-demo) is a plain folder with one plugin carrying one skill (`grill-me`), installable in Claude Code, Copilot CLI and Codex.
- **Host it on internal git.** The agent can do the plumbing: *"put grill-me in my marketplace"* — it clones the repo to a temp folder and pushes [[2026-10-01-ai-bank]].
- **Pin it in the repo**: add it to `.claude/settings.json` via [`extraKnownMarketplaces`](https://code.claude.com/docs/en/discover-plugins#configure-team-marketplaces), so it is offered the moment a colleague trusts the folder [[2026-09-04-ai-software-company]].
- **Make one marketplace mandatory** for every developer to auto-install the team's skills, guards and hooks on every machine — but keep it minimal, so you don't bloat everyone's context [[2026-07-09-ai-live-casino]].
- **Auto-update.** Plugins update at harness start [[2026-08-27-ai-it-services]]; first-party plugins auto-update by default, while for your own marketplace auto-update is opt-in [[2026-09-07-ai-bank]].
- **Restart after updates.** Skill descriptions sit in the system prompt, so an update invalidates the [[prompt-caching|prompt cache]]; start a new session to pick it up [[2026-09-07-ai-bank]].

## Bottom-up, per team

The strongest consensus across sessions: grow it **in your team's garden**, not as a company-wide commons too big for you to have a say in [[2026-09-07-ai-bank]].

- *"Megalomania fails"*: one Java style for 1,000 developers, when five never agree [[2026-09-18-ai-agentic-how]]. A company plugin for 4,000 people ends up used by 20; build it per team of ≈7 who agree [[2026-09-02-ai-insurer]]. Global plugins end up generic and unused; let knowledge escalate bottom-up, team → company [[2026-09-04-ai-software-company]].
- **Tiers**: company (infrastructure — Jira CLI, MCP servers), team (your way of working), private [[2026-09-28-ai-gaming-tech]] [[2026-10-09-ai-jax-london]]. Separate marketplaces per role (QA, backend, frontend) or per client also work [[2026-08-27-ai-it-services]].
- **Team rules belong here**, not in a home `CLAUDE.md`: project [[claude-md|CLAUDE.md]] for the project, home CLAUDE.md for strictly personal preferences, `MEMORY.md` for auto-learned notes, and team conventions as skills in a plugin [[2026-08-31-ai-software-vendor]].
- **Extend a skill you don't own with a hook, not a rival skill.** Skill activation is a tool call, so a `PostToolUse` hook on it can append your extension markdown — "class X extends A" (demo: "blank line after every signature") [[2026-10-09-ai-jax-london]] [[2026-09-28-ai-gaming-tech]] [[2026-09-24-ai-bank]].
- **Victor's challenge**: push one line of markdown to your team marketplace every week [[2026-09-04-ai-software-company]].

## The first skill: `feedback`

The first skill to put in the marketplace is a **feedback skill** that teaches the agent where and how to send a correction PR back to the marketplace repo — *"with no way to PR back, I won't install it"* [[2026-10-09-ai-jax-london]] [[2026-09-07-ai-bank]] [[2026-09-16-ai-safety-critical]]. A platform team or the owners approve the PRs periodically [[2026-08-27-ai-it-services]]. The cross-team variant opens your repo to PRs from colleagues' agents — the cheapest learning loop; see [[self-improving-knowledge]] and [[cross-repo-knowledge]] [[2026-09-14-ai-industrial]].

## Never a stranger's marketplace

**Plugin auto-update is a supply chain**: if the author's git is compromised, you pull the malware at your next start [[2026-09-02-ai-insurer]]. The update button is the live demonstration of why you don't install from strangers [[2026-10-01-ai-bank]]. Rules:

- **Internal git only**; the one commonly cited exception is Anthropic's vetted [claude-plugins-official](https://github.com/anthropics/claude-plugins-official) [[2026-09-30-ai-enterprise]].
- Treat public hubs and other teams' "AI champion" repos as **inspiration, not bulk install** [[2026-10-01-ai-bank]]: read the skill, then copy what you need into your own marketplace. Scan with [Snyk Skill Scan](https://labs.snyk.io/experiments/skill-scan) [[2026-09-30-ai-enterprise]].
- Public hubs do contain malware: [Snyk ToxicSkills](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/) scanned 3,984 skills from ClawHub and skills.sh and found 76 confirmed malicious payloads (≈1.9%) and 36.8% with at least one security flaw. (A figure of "≈2% of ≈1.25M skills" quoted in one session extrapolates that sample to the whole hub.) See [[skill-marketplace-security]].

Discovery, for inspiration: [agent-plugins.org](https://agent-plugins.org/), [skills.sh](https://www.skills.sh/) (with [find-skills](https://www.skills.sh/vercel-labs/skills/find-skills) to search for the task at hand), and a public example marketplace at [victorrentea/skills](https://github.com/victorrentea/skills).

## See also

- [[skills-governance]]
- [[skill-marketplace-security]]
- [[agent-skill]]
- [[cross-repo-knowledge]]
- [[self-improving-knowledge]]
- [[memory-layers]]
- [[internal-tool-cli-skill]]
- [[supply-chain-attack]]
- [[claude-md]]
