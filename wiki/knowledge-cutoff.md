---
title: Knowledge Cutoff
category: concept
tags: [hallucination, training-data, context7, web-fetch, reliability]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-05-04-ai-telecom]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]"]
created: 2026-06-23
updated: 2026-10-13
---

The knowledge cutoff is the date past which a model has no training data — ask it about anything newer (a library release, a language version, an API change) and it will **invent an answer confidently** rather than admit ignorance.

## Why it causes hallucination

A model is a fixed file of weights frozen at training time; it has no awareness of the present. Newer-than-cutoff questions are one of the recurring [[hallucination]] causes: the model fills the gap from the nearest pattern it knows, producing a plausible-but-wrong API surface or a deprecated method. The defining example: it confidently answers a language-version question that post-dates its training, getting it subtly wrong.

<span style="color:red">Concrete cutoffs quoted in the sessions: **Opus 4.6 ≈ May 2025** — so Java 25 and Spring Boot 4 APIs get hallucinated — and **Opus 4.7 ≈ end of January 2026** ([[2026-04-07-ai-product-team]], [[2026-05-04-ai-telecom]]). Check the model card of the model you actually run; the gap moves with every release.</span>

<span style="color:red">**The agent prefers its memory by default.** It answers from training data and avoids the web to save tokens, so you have to force retrieval explicitly ([[2026-09-04-ai-software-company]]).</span>

## Fixing it — pull, don't recall

Never trust recall for post-cutoff facts; bring the fact into the [[context-window]] instead:

- **[[context7]] MCP** — a vetted feed of up-to-date, version-specific official docs. The safer default for fast-moving library APIs because the source corpus is controlled. <span style="color:red">One session called Context7 "the most-installed Claude extension" ([[2026-04-07-ai-product-team]]).</span>
- <span style="color:red">**Force online research with proof** — *"research online first, **quote the sections with links** so I can check"*. Demanding citations raises quality everywhere, not just for post-cutoff facts ([[2026-09-04-ai-software-company]]).</span>
- **Web-fetch** — works for one-off facts, but carries [[prompt-injection]] risk: a poisoned page (e.g. a malicious Reddit/forum post) can hijack the agent. Use a quarantine sub-agent that extracts only structured facts.
- **Runnable prototype** — for "learning a new API," have the agent build something that actually compiles and runs against the real library, so its claims are grounded in execution rather than memory (see [[chop-ai-waiter]]).

The knowledge cutoff is distinct from the [[dumb-zone]]: the cutoff is *missing* knowledge (fixable by retrieval), the dumb zone is *present but un-attendable* knowledge (fixable by context hygiene). Both, plus [[sycophancy]], are the three real causes of confident wrongness — randomness is minor.

## <span style="color:red">The gap is shrinking</span>

<span style="color:red">The "Stack Overflow apocalypse": tutorial blogs die because LLM providers get framework release notes directly and models also learn from billions of user prompts — *"Opus 5 knows directly what you had to dictate to Opus 4"*. Agents are increasingly "born knowing" each mainstream release; the residual risk is the framework launched *tomorrow*, your internal libraries, and anything niche ([[2026-09-07-ai-bank]]).</span>

## See also
- [[hallucination]]
- [[context7]]
- [[dumb-zone]]
- [[sycophancy]]
- [[prompt-injection]]
- [[chop-ai-waiter]]
- <span style="color:red">[[uncensored-models]]</span>
- <span style="color:red">[[2026-09-04-ai-software-company]]</span>
- <span style="color:red">[[2026-09-07-ai-bank]]</span>
- [[2026-06-23-ai-device-maker]]
