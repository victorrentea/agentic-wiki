---
title: Knowledge Cutoff
category: concept
tags: [hallucination, training-data, context7, web-fetch, reliability]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-24
---


The knowledge cutoff is the date past which a model has no training data — ask it about anything newer (a library release, a language version, an API change) and it will **invent an answer confidently** rather than admit ignorance.

## Why it causes hallucination

A model is a fixed file of weights frozen at training time; it has no awareness of the present. Newer-than-cutoff questions are one of the recurring [[hallucination]] causes: the model fills the gap from the nearest pattern it knows, producing a plausible-but-wrong API surface or a deprecated method. The defining example: it confidently answers a language-version question that post-dates its training, getting it subtly wrong.

## Fixing it — pull, don't recall

Never trust recall for post-cutoff facts; bring the fact into the [[context-window]] instead:

- **[[context7]] MCP** — a vetted feed of up-to-date, version-specific official docs. The safer default for fast-moving library APIs because the source corpus is controlled.
- **Web-fetch** — works for one-off facts, but carries [[prompt-injection]] risk: a poisoned page (e.g. a malicious Reddit/forum post) can hijack the agent. Use a quarantine sub-agent that extracts only structured facts.
- **Runnable prototype** — for "learning a new API," have the agent build something that actually compiles and runs against the real library, so its claims are grounded in execution rather than memory (see [[chop-ai-waiter]]).

The knowledge cutoff is distinct from the [[dumb-zone]]: the cutoff is *missing* knowledge (fixable by retrieval), the dumb zone is *present but un-attendable* knowledge (fixable by context hygiene). Both, plus [[sycophancy]], are the three real causes of confident wrongness — randomness is minor.

## See also
- [[hallucination]]
- [[context7]]
- [[dumb-zone]]
- [[sycophancy]]
- [[prompt-injection]]
- [[chop-ai-waiter]]
- [[2026-06-23-ai-garmin]]
