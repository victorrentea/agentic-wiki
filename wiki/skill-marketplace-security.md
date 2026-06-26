---
title: Skill Marketplace Security
category: security
tags: [security, supply-chain, skill, marketplace, agent-skill, npm]
sources: ["[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-26
updated: 2026-06-26
new_in: 2026-06-26
---

<span style="color:red">🆕 New in AI@agentic.how (2026-06-26)</span>

Skill marketplace security is the recognition that community-published [[agent-skill|skills]] are a supply-chain surface equivalent to unaudited npm packages: they run arbitrary code inside your agent session and deserve the same vetting discipline.

## The threat model

An [[agent-skill|agent skill]] is a markdown file, but a well-written skill includes **embedded scripts** that the agent executes as part of the skill body. A malicious or compromised community skill can:

- Exfiltrate your API keys, SSH keys, or CLAUDE.md content.
- Install backdoored dependencies as a side effect.
- Override your CLAUDE.md rules to widen permissions.

Historically ≈30% of widely-downloaded community skills have had at least one security glitch — the skill marketplace is at *"npm circa 2015"* in terms of provenance and vetting maturity.

## Practical rules

1. **Don't install more than ≈10 skills from any marketplace.** Each adds a code-execution surface; the marginal benefit of the 11th community skill rarely justifies the risk.
2. **Prefer Anthropic-published skills.** First-party skills are reviewed by the team that owns the harness; third-party skills have no such guarantee.
3. **Use a security-scanning skill** (e.g. the NVIDIA-published scanner) to vet any community skill before activating it — "use the tool to vet the tool."
4. **Treat a skill install like `npm install <unknown-package>`** — read it, check what scripts it embeds, count the stars, check the publish date, look for signs of provenance.
5. **Prefer building your own** over downloading a stranger's. Use a community skill to *learn the delta* ("compare my way with this skill and teach me the differences") and then build your team's canonical version.

## Relation to [[supply-chain-attack]]

Skill marketplace security is a specialization of the [[supply-chain-attack]] threat for the agent-skills layer. The Docker boundary ([[docker-sandboxing]]) is the last-resort containment: a malicious skill that fires inside a properly configured container can't reach the host filesystem or egress beyond the allow-list.

## See also
- [[agent-skill]]
- [[supply-chain-attack]]
- [[docker-sandboxing]]
- [[agent-permissions]]
- [[skills-governance]]
- [[prompt-injection-canary]]
- [[2026-06-26-ai-agentic-how]]
