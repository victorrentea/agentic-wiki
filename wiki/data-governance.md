---
title: Data Governance (ZDR)
category: security
tags: [security, privacy, zdr, compliance, data-residency, policy, gdpr, shadow-ai, copyright]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-10-01-ai-bank]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-23
updated: 2026-10-13
---

Data governance for agents is the policy layer that decides *which* model endpoints may legally see your source code and prompts — most concretely whether a vendor has signed a **Zero-Data-Retention (ZDR)** agreement.

## Zero-Data-Retention

A ZDR agreement means a vendor does not store your prompts and code and does not train on them. It is a contractual, not a technical, control — and it is endpoint-specific. A team can be cleared to use one provider's models and *forbidden* from another's, even for the identical task, purely on the basis of which ZDR contracts exist.

<span style="color:red">ZDR is mandatory in safety-critical work. The same model family can be reachable both with and without it depending on the channel (direct vendor API, a cloud provider such as AWS Bedrock, an enterprise Copilot licence), so verify the path you actually use. Anthropic documents its ZDR terms for Claude Code [here](https://code.claude.com/docs/en/zero-data-retention) ([[2026-04-16-ai-safety-critical]], [[2026-09-18-ai-agentic-how]]).</span> <span style="color:red">Even enterprise contracts differ in the details. Some Copilot Enterprise contracts let the vendor send prompt+response pairs for safety evaluation, so have legal read the contract ([[2026-04-27-ai-healthcare-software]]).</span>

<span style="color:red">**"Everything we send goes into training tomorrow" is false for commercial terms.** Anthropic's commercial terms state that it "may not train models on Customer Content from Services" ([commercial terms](https://www.anthropic.com/legal/commercial-terms)). Only **consumer accounts** have a training setting ([consumer terms update](https://www.anthropic.com/news/updates-to-our-consumer-terms)) ([[2026-08-27-ai-it-services]], [[2026-10-01-ai-bank]]). That is also the core of the next section.</span>

## <span style="color:red">Personal subscriptions and "shadow AI"</span>

<span style="color:red">A **personal** subscription gives no contractual guarantee: there is an opt-out toggle, but no signed agreement, and your code may be used for training. The team/enterprise tier is the floor for company code. Never use a personal account on production, government or police code ([[2026-06-08-ai-security-tools]], [[2026-07-01-ai-java-community]], [[2026-10-01-ai-bank]]).</span>

- <span style="color:red">**Using a personal subscription on corporate code is "shadow AI"**: a breach of internal policy and of your employment contract. Sessions called it "illegal" or even "prison". The corrected wording is a **firing offence, not criminal liability** ([[2026-08-06-ai-bank]], [[2026-09-07-ai-bank]], [[2026-09-18-ai-agentic-how]]). Where traffic is scanned and ZDR is signed only for the corporate licence (often Copilot via Microsoft), a personal Claude subscription on company code is simply not covered ([[2026-09-16-ai-safety-critical]]). One cautionary tale involved tunnelling into the work laptop over [Tailscale](https://tailscale.com/) to use a personal agent.</span>
- <span style="color:red">**Generating employer code on your own cheap subscription is the same violation** as pasting company code into a personal chatbot ([[2026-09-04-ai-software-company]]).</span>
- <span style="color:red">**The legitimate path for tooling:** build *generic* tools at home on your personal subscription, publish them on public GitHub, and clone them into the company environment. That is legal because it is tooling, not feature work ([[2026-09-07-ai-bank]], [[2026-08-27-ai-it-services]]). The other legitimate path is proving smart usage so the company raises your quota ([[2026-09-18-ai-agentic-how]]).</span>
- <span style="color:red">**"Token maxxing" belongs on your personal subscription and personal projects, not the company budget.** One public story told of a company burning its annual AI budget in 4 months ([Fortune, May 2026](https://fortune.com/2026/05/26/uber-coo-ai-spending-tokens-claude-code/)) ([[2026-10-01-ai-bank]]). A sub-$25/month personal subscription for learning is still a must-have for the 2026 engineer ([[2026-09-07-ai-bank]]).</span>

## The practical consequences

- **Cleared endpoint** (e.g. a vendor you hold ZDR with) — code and prompts may flow there.
- **Uncleared endpoints** (any provider without ZDR) — using them with company code is a **policy violation**, not a grey area.
- The "screenshot the code into a public chatbot when I'm out of quota" workaround is **explicitly off-limits** — it exfiltrates source to an uncleared endpoint. <span style="color:red">("Stop photographing code to ask your ChatGPT" — [[2026-07-30-ai-safety-critical]].)</span>
- **Disable IDE AI plugins that route to uncleared providers** org-wide — an in-editor assistant can silently ship your `AGENTS.md` and code over the wire to a non-ZDR endpoint.
- <span style="color:red">**Voice dictation is data egress too.** Cloud dictation tools such as [Wispr Flow](https://wisprflow.ai) send your voice, and whatever code or customer names you dictate, outside the company, so they need the same ZDR approval. Local alternatives: [Whisper](https://github.com/openai/whisper), [Handy](https://handy.computer) ([[2026-08-31-ai-software-vendor]]). See [[wispr-flow]].</span>
- <span style="color:red">**Search queries cross the perimeter.** A hosted web-search MCP, suggested to fill a harness's missing web search, sends every query outside the company. Your search history describes what you are building ([[2026-10-13-ai-bank]]).</span>
- <span style="color:red">**DLP doesn't stop a determined insider.** Stories from the room: a phone on a tripod filming the laptop while a script scrolls the code, then OCR frame by frame; next level, exfiltration via QR codes shown on screen ([[2026-07-30-ai-safety-critical]], [[2026-08-03-ai-telecom-ops-qa]]). Governance is a matter of culture and contract as much as tooling.</span>

## <span style="color:red">Contractual risks to list before signing</span>

<span style="color:red">The office risks of an agent are **contractual, not technical** ([[2026-08-27-ai-it-services]]):</span>

- <span style="color:red">**Data sovereignty.** "European" vendors often still run on GCP/AWS/Azure.</span>
- <span style="color:red">**Zero data retention** and **IP rights on training data**.</span>
- <span style="color:red">**Authenticity** of what you hand the client.</span>
- <span style="color:red">**Crawling from the company IP.** An agent scraping the web can get the company domain flagged as spam.</span>
- <span style="color:red">**Training-data bias** in outputs.</span>
- <span style="color:red">**Transfer law, stated correctly.** **Schrems II** is a CJEU ruling (C-311/18, 2020), not a law. It invalidated Privacy Shield; it did not ban transfers outside the EU. Since 10 July 2023 the **EU–US Data Privacy Framework** adequacy decision applies.</span>

<span style="color:red">**Copyright cuts both ways.** Generated code can [reproduce copyrighted material](https://docs.github.com/en/copilot/concepts/completions/code-referencing) from training data; in one case Copilot ghost-texted another company's copyright header ([[2026-09-04-ai-software-company]]). An autocomplete that continues a "secret" text is evidence of training on that text ([[2026-08-31-ai-software-vendor]]).</span>

## <span style="color:red">Personal data and in-house bots</span>

- <span style="color:red">**Don't feed agents production personal data** (GDPR). Use synthetic, anonymised or masked data ([[2026-06-05-ai-consultancy]]).</span>
- <span style="color:red">**In-house chatbots never get unrestricted read on the production DB.** Give them targeted MCPs with 2–3 precise actions. Authenticate the human first (login, [[jwt-identity|JWT bearer]]) and have each tool answer **only with that user's rows** (see [[mcp-tenant-isolation]]).</span>
- <span style="color:red">**Treat LLM output like user input.** Validate and escape it, and moderate in both directions ([[guardrails]]).</span>
- <span style="color:red">**Regulation may force a weaker model.** Some defence organisations are limited to local or EU models such as Mistral. Compensate by cutting the agent's rights further ([[2026-06-05-ai-consultancy]]).</span>

## Relationship to on-prem and sovereignty

ZDR governs *hosted* endpoints. The stronger guarantee is keeping inference inside your perimeter entirely: open-weights models like [DeepSeek](https://huggingface.co/deepseek-ai) run on [[ollama]] or via [[claude-code-router]] against an on-prem box — no data leaves at all. Note that routing to a *hosted* open-model API still leaves your perimeter; only the on-prem deployment buys true sovereignty. Frontier proprietary models can never be downloaded — they always run in a vendor datacenter, so ZDR is the only available control for them. <span style="color:red">The extreme is **air-gapped**: one national tax authority runs its own LLM instance with no internet at all. Local stacks (Ollama + Llama + Continue.dev) are the privacy/capability compromise ([[2026-04-16-ai-safety-critical]]).</span> <span style="color:red">Remember that **open weights ≠ open source**: Chinese and other open models publish weights, not training data, so sovereignty over *where* inference runs is not transparency about *what* the model learned ([[2026-08-31-ai-software-vendor]], [[uncensored-models]]).</span>

<span style="color:red">For auditability, mark AI-generated work: `Co-Authored-By: Claude <noreply@anthropic.com>` trailers and tags on AI-generated commits ([[2026-04-16-ai-safety-critical]]).</span>

This is a different concern from runtime [[prompt-injection]] / exfiltration: data governance is about *where the data legitimately goes by policy*, while the [[lethal-trifecta]] and [[docker-sandboxing]] are about *what a compromised agent can leak*.

## See also
- [[ollama]]
- [[claude-code-router]]
- [[prompt-injection]]
- [[lethal-trifecta]]
- [[docker-sandboxing]]
- [[secret-zero]]
- <span style="color:red">[[uncensored-models]]</span>
- <span style="color:red">[[mcp-tenant-isolation]]</span>
- <span style="color:red">[[wispr-flow]]</span>
- <span style="color:red">[[telemetry-by-default]]</span>
- <span style="color:red">[[ai-adoption]]</span>
- [[2026-06-23-ai-device-maker]]
- <span style="color:red">[[2026-08-27-ai-it-services]]</span>
- <span style="color:red">[[2026-09-18-ai-agentic-how]]</span>
