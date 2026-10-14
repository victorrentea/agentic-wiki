---
title: "Source: Secure Coding @ a payments company (2026-05-18..22)"
category: source
tags: [source, payments, secure-coding, llm-security, supply-chain, sandbox, workshop]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

A classic secure-coding course (cryptography, OWASP injections and XSS, server resource abuse, AuthN/Z, OAuth/OIDC) for a payments company. Only one day, the "LLM Security" day, is in scope for this wiki, plus a few AI asides from the others. That day covered dependency attacks in the agent era (the Axios postinstall harvester, minimum release age, `npx`/`uvx`-launched MCP servers as the top supply-chain risk, slopsquatting with an internal registry as the defence), how LLMs learn to refuse and how that is bypassed (uncensored models, judge LLMs, a jailbreak taxonomy, the tax-office attack that switched between models), the lethal trifecta with MCP-enforced per-user JWT scoping as the structural defuser, and securing the CLI agent itself: allow/ask/deny permissions, approval fatigue, OS sandbox mode, Docker and microVMs, CLAUDE.md discipline and model selection. The OAuth day added consent-screen-scoped connector tokens.

## Concept inventory

*Supply chain* — [[supply-chain-attack]] (npx MCP entries, minimum release age, lockfile review), [[slopsquatting]], [[skill-marketplace-security]].

*LLM safety & injection* — [[jailbreak-ladder]] (uncensored models, poetry mode, emoji, adversarial suffixes, model-swap evasion), [[judge-llm]], [[prompt-injection]] (HTML-comment markdown-image exfiltration, screenshot steganography), [[lethal-trifecta]], [[openclaw]], [[mcp-tenant-isolation]] / [[jwt-identity]] (anonymous → authenticated conversation).

*Securing the agent* — [[agent-permissions]] / [[approval-fatigue]], [[os-sandbox]] (child processes covered), [[docker-sandboxing]] (microVM for nested Docker), [[draft-only-email]] (consent-scoped connectors), [[code-review]] / [[static-analysis-gauntlet]] (AI-written string-concatenated queries).

*Context & cost* — [[dumb-zone]] / [[context-window]] (compaction threshold, effective attention), [[claude-md]] (rule budget), [[model-hierarchy]], [[token-economy]] (team quotas), [[toon]].

## See also
- [[2026-05-13-ai-it-services]]
- [[2026-05-06-ai-ecommerce]]
- [[lethal-trifecta]]
- [[supply-chain-attack]]
