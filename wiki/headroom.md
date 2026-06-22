---
title: Headroom (Token Proxy)
category: tool
tags: [token-economy, cli, output-tokens, cost-optimization, hook]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-22
updated: 2026-06-22
new_in: 2026-06-22
---

<span style="color:red">🆕 New in AI@Kambi (2026-06-22)</span>

Headroom is a more evolved CLI-output compression proxy in the same family as [[rtk]] — both intercept dev tool output (git, npm, test runners) and strip noise before the model reads it, reducing the tool-result tokens that dominate agent cost.

## Relationship to RTK

[[rtk]] (Rust Token Killer) and headroom address the same problem: most tokens in an agentic session live in **tool results**, not the developer's messages, and raw CLI output is noisy (ANSI codes, progress bars, redundant paths, verbose XML). Both work by hooking `bash` invocations and rewriting the output.

Headroom is described as the more evolved sibling — it may offer richer filtering, configurable rules, or broader command coverage. The philosophical lineage is the same: *teach the agent once, then script the compaction — no mercy.* (The JaCoCo coverage-report example from [[token-economy]]: one hour of teaching, one script, zero repeated parsing.)

## Realistic savings

The advertised headline numbers (80–90% savings) are marketing. In practice, realistic savings are **≈20–30%** on a high-volume agentic workflow. That is still material when compounded with [[prompt-caching]] discipline and pruning unused MCP tools.

The biggest single wins are:
- `git status` (97 tokens → 19 with [[rtk]])
- Maven/Gradle build output (verbose, redundant classpath lines)
- pom.xml / package.json reads (≈3,000 tokens of angle-bracket noise — pre-process instead of dumping raw)

## See also
- [[rtk]]
- [[token-economy]]
- [[prompt-caching]]
- [[agent-skill]]
- [[toon]]
- [[2026-06-22-ai-kambi]]
