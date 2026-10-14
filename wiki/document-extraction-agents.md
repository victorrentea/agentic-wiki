---
title: Multi-Agent Document Extraction
category: pattern
tags: [extraction, pdf, structured-output, multi-model, verification, golden-dataset]
sources: ["[[2026-08-06-ai-bank]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Extracting structured data from heterogeneous documents (e.g. onboarding PDFs for individuals vs. companies) with one specialised agent per document type, per-field confidence, and cross-model verification [[2026-08-06-ai-bank]].

## One agent per document type

Don't build one omniscient extractor. Split by document family — individuals, legal entities, an aggregator that combines them — each with its own [[agents-md|AGENTS.md]] and [[agent-skill|skills]]. Each [[sub-agents|sub-agent]] gets a small, focused context and its own rules, and can be evaluated and improved independently.

## Schema rules that prevent invented values

- **Ask for a confidence score per field** in the output JSON.
- **Make no field `required`.** A required field forces the model to produce *something*, so it invents a value to satisfy the schema — a structural source of [[hallucination]]. An absent field with low confidence is the honest answer.

## Cross-check with two different models

Run the extraction with **two different models**, not two runs of the same one — the same model tends to repeat its own mistakes. If the outputs differ, **escalate to a human**. This is [[multi-model-review]] applied to data; a [[judge-llm]] can arbitrate the easy disagreements, but the escalation path is a person.

## A free golden dataset

Every document humans processed manually in the past, together with what they keyed in, is a ready-made evaluation set: run the agents over it and measure field-level accuracy before going live. See [[agent-evals]] and [[data-quality-validator]].

## See also

- [[multi-model-review]]
- [[hallucination]]
- [[data-quality-validator]]
- [[sub-agents]]
- [[judge-llm]]
- [[agent-evals]]
- [[ai-use-cases-beyond-coding]]
