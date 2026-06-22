---
title: Lethal Trifecta
category: security
tags: [security, prompt-injection, exfiltration, agent-design, simon-willison]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
---

The lethal trifecta is [Simon Willison's](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/) name for the three agent capabilities that become catastrophic in combination: **private data + untrusted content + external communication**.

## The three legs

1. **Access to private data** — the agent holds secrets: card numbers, confidential reports, API keys, private messages.
2. **Exposure to untrusted content** — the agent reads web pages, emails, documents, or other user-controlled content (the [[prompt-injection]] vector).
3. **Ability to communicate externally** — the agent can send HTTP requests, emails, or other outbound messages.

Holding all three means a single injected instruction in an untrusted document can read the private data and exfiltrate it — no additional vulnerability needed. The capability combination *is* the vulnerability.

## The "no send tool" false comfort

You do not need an explicit "send email" tool to exfiltrate. A `WebFetch` call to any URL the attacker controls, with the secret base64-encoded in a query parameter, is sufficient exfiltration. The channel doesn't need to look like communication — any outbound request works.

## Mitigation: cut one leg

The structural fix is architectural — not filtering harder:
- **No untrusted content** in that agent session (quarantine pattern: a separate agent reads untrusted content and summarizes only structure, never raw text, back to the privileged agent).
- **No exfil channel** — [[os-sandbox]] or [[docker-sandboxing]] with an egress allow-list that permits only specific internal hosts.
- **No secret in context** — the [[secret-zero]] broker pattern: the agent never holds the long-lived key; a sidecar injects it only at the outbound boundary.

**There is no way today to guarantee an agent with all three legs will not exfiltrate.** All prompt-injection guardrails are probabilistic, not structural.

## See also

- [[dual-leg-rule]]
- [[prompt-injection]]
- [[geo-steganography]]
- [[openclaw]]
- [[docker-sandboxing]]
- [[os-sandbox]]
- [[secret-zero]]
- [[2026-06-11-ai-playtika]]
