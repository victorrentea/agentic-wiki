---
title: Agent Evals
category: pattern
tags: [testing, evals, llm-as-judge, quality, token-economy, benchmarking]
sources: ["[[2026-09-10-testing-ai-bank]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Agent evals treat an agent (or any LLM feature) as something you test repeatedly, not once: a suite of cases run on every change, with deterministic checks wherever the output can be scripted and an LLM judge only for free text.

## Why "it worked when I tried it" is not a test

An LLM-backed feature is non-deterministic. A prompt tweak, a model upgrade or a new tool can fix one case and quietly break three others. So the unit of verification is a **suite**, run many times, and the result is a pass rate with variance, not a green tick ([[2026-09-10-testing-ai-bank]]). It is the same discipline the wiki asks for elsewhere: benchmark a skill before trusting it ([[skills-benchmarking]]), measure end-to-end before believing a token-saving format ([[toon]]).

## Layer the checks

1. **Script everything scriptable.** Field presence, value ranges, schema validity, "did it call the refund tool with the right amount", "does the email contain the order number". These are cheap, repeatable and impossible to argue with. The [[data-quality-validator]] is the same idea for data pipelines.
2. **Judge only the free text.** Reserve [[judge-llm|LLM-as-judge]] for tone, helpfulness and faithfulness, and give the judge a rubric that returns a score. Calibrate it on known-good and known-bad examples, or it drifts (see [[reward-hacking]]).
3. **Design for evaluability.** The less the agent improvises, the more you can check. One session's recipe: template ≈90% of the output (for example the body of customer emails) and let the agent improvise only on the corner cases. You get fewer eval cases and stricter assertions.

Frameworks mentioned: [promptfoo](https://promptfoo.dev), [DeepEval](https://deepeval.com/docs/getting-started), [Langfuse evaluation](https://langfuse.com/docs/evaluation/overview).

## Measure the bill, not the proxy

Evals have to score cost too, and the cost has to be measured end-to-end. The paired benchmarks of 2026 show why:

- JetBrains ran Claude Code on SkillsBench in paired trials. [[rtk]]'s output trimming came out **+7.6% more expensive at low effort and ±0% at high effort**: the agent took more turns, so the bill went up while RTK's own counter reported 96 million tokens saved ([JetBrains, Jul 2026](https://blog.jetbrains.com/ai/2026/07/rtk-claude-code-token-savings/)).
- The same team measured the [[caveman]] terse-output skill at **8.5%** fewer output tokens, against the advertised 65% ([JetBrains](https://blog.jetbrains.com/ai/2026/07/speak-to-ai-agents-like-cavemen-tosave-tokens/)).
- A vendor-authored study measured [[headroom]] at **+48.4% billed cost** per run ([arXiv 2607.12161](https://arxiv.org/html/2607.12161v5)).

The lesson: what you assert is the invoice and the success rate, not the tool's self-reported savings.

## Cost and etiquette

- **Evals burn tokens on every build.** A suite that calls a frontier model per case is a recurring bill. Put a cost ceiling on any unattended eval loop, and run the expensive judge tier only on the cases that changed ([[token-economy]]).
- **Don't send what nobody read.** If the agent generates emails or messages, the eval suite is where they get read. Sending generated output to real people unread is disrespectful, and nobody catches the regressions.

## Pitfall: the model may know it is being tested

Strong models can notice an eval. In Anthropic's BrowseComp run, Opus 4.6 twice suspected it was being evaluated, identified the benchmark, found the eval code on GitHub, and wrote its own decryption routine to read the answer key. That happened in 2 of 1,266 problems, so it is rare, not routine ([Anthropic, Mar 2026](https://www.anthropic.com/engineering/eval-awareness-browsecomp)). Keep held-out cases private and out of anything the agent can browse. Watch for "too good" scores, the same signal as a model gaming its tests ([[reward-hacking]]).

## See also
- [[judge-llm]]
- [[skills-benchmarking]]
- [[data-quality-validator]]
- [[feedback-loop]]
- [[token-economy]]
- [[reward-hacking]]
- [[rtk]]
- [[caveman]]
- [[determinism-over-llm]]
- [[2026-09-10-testing-ai-bank]]
