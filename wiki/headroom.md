---
title: Headroom (Token Proxy)
category: tool
tags: [token-economy, cli, output-tokens, cost-optimization, hook]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-10-ai-consultancy]]"]
created: 2026-06-22
updated: 2026-10-13
---

Headroom is a more evolved CLI-output compression proxy in the same family as [[rtk]] — both intercept dev tool output (git, npm, test runners) and strip noise before the model reads it, reducing the tool-result tokens that dominate agent cost. <span style="color:red">Canonical repo: [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) (the older `chopratejas/headroom` link seen in some sessions redirects there).</span>

## Relationship to RTK

[[rtk]] (Rust Token Killer) and headroom address the same problem: most tokens in an agentic session live in **tool results**, not the developer's messages, and raw CLI output is noisy (ANSI codes, progress bars, redundant paths, verbose XML). Both work by hooking `bash` invocations and rewriting the output.

<span style="color:red">Headroom goes wider than [[rtk]]: it compresses **content, not just shell output** — tool outputs, logs, files and RAG chunks — and runs as a library, a proxy or an MCP server ([repo](https://github.com/headroomlabs-ai/headroom)). Practitioners describe it as already including RTK-style rewriting (one setup instead of two) and as flattening JSON more aggressively ([[2026-07-09-ai-live-casino]], [[2026-07-20-ai-telecom]]).</span> The philosophical lineage is the same: *teach the agent once, then script the compaction — no mercy.* (The JaCoCo coverage-report example from [[token-economy]]: one hour of teaching, one script, zero repeated parsing.)

## Realistic savings — measured as a net loss

<span style="color:red">Vendor claim: ≈20% fewer tokens for coding agents and 60–95% fewer for JSON. Practitioners in the room claimed ≈50% of tokens saved "with no impact on experience" ([[2026-07-09-ai-live-casino]]).</span>

<span style="color:red">The only controlled measurement found says the opposite on the bill. **PointFive** (Weinberger & Hozez, [arXiv 2607.12161](https://arxiv.org/html/2607.12161v5), Jul 2026 — vendor-authored, as the company discloses) measured Headroom v0.27.0 at **+48.4% billed cost** [+42, +55] and **×1.46 cost per successful task**, with success rate unchanged; [[rtk]] v0.44.1 in the same study came out ≈break-even (−2.7%). Earlier sessions attributed this result to JetBrains — JetBrains benchmarked only RTK, not Headroom ([JetBrains, Jul 2026](https://blog.jetbrains.com/ai/2026/07/rtk-claude-code-token-savings/)). JetBrains traced RTK's extra cost to more turns and more cache reads; whatever the exact mechanism for Headroom, the lesson is the same — **tokens removed ≠ money saved**.</span>

<span style="color:red">An earlier version of this page put realistic savings at ≈20–30%; by autumn 2026 the evidence says measure on your own bill (e.g. with `ccusage`) before trusting any compression proxy — see [[optimize-then-measure-subset]].</span>

## Where compression still helps

The biggest single wins are:
- `git status` (97 tokens → 19 with [[rtk]])
- Maven/Gradle build output (verbose, redundant classpath lines)
- pom.xml / package.json reads (≈3,000 tokens of angle-bracket noise — pre-process instead of dumping raw)

## Operational risk

<span style="color:red">Headroom is **invasive**: in one live session it broke the presenter's remote-desktop connection ([[2026-08-03-ai-telecom-ops-qa]]). Anything that sits as a proxy in front of the model or the shell is part of your critical path — roll it out to a few people first.</span>

## Rolling it out to a team

<span style="color:red">If you do adopt it, distribute it through an internal [[plugin-marketplace]] with a start-up hook ([[agent-lifecycle-hooks]]) that detects whether it is installed and installs it if missing — developers need not know it exists ([[2026-07-09-ai-live-casino]]).</span>

## See also
- [[rtk]]
- [[token-economy]]
- [[prompt-caching]]
- [[agent-skill]]
- [[toon]]
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[optimize-then-measure-subset]]</span>
- <span style="color:red">[[caveman]]</span>
- [[2026-06-22-ai-sports-betting]]
- <span style="color:red">[[2026-07-09-ai-live-casino]]</span>
- <span style="color:red">[[2026-07-20-ai-telecom]]</span>
- <span style="color:red">[[2026-08-03-ai-telecom-ops-qa]]</span>
- <span style="color:red">[[2026-08-10-ai-consultancy]]</span>
