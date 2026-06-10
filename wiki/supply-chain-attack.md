---
title: Supply Chain Attack
category: security
tags: [security, supply-chain, sbom, npm, agentic-risk]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

A supply chain attack compromises a dependency in your build graph — not your code directly — so that installing or building your project executes the attacker's payload.

## The npm/Axios incident

The canonical war story from [[2026-06-10-spring-ai-itkonekt]]: attackers stole a contributor's GitHub token, published a poisoned patch version of a widely-used npm package (Axios ecosystem), sneaking in one extra transitive dependency carrying a malicious **post-install script**. A plain `npm install` — including on a CI server with no human present — **ran arbitrary code**, harvesting tokens and secrets from the machine. The vector is transitive: you never added the malicious package; your dependency did.

Mitigation: [SBOMs](https://www.linuxfoundation.org/blog/blog/what-is-an-sbom) (Software Bill of Materials) give you an auditable inventory of every direct and transitive dependency so anomalous additions are detectable.

## The agentic-era amplifier

Agents change the threat model in two compounding ways:

1. **Auto-approve bash** — an agent running in "approve everything" mode is functionally `curl | bash` with extra steps. A post-install script runs with the same privilege as the agent.
2. **"latest" version pins** — agents told to "install the latest version" will resolve it at runtime, fetching whatever is current at that moment.

Never combine `latest` version pins with `bash:* auto-approve` on a machine that holds secrets. The two settings together collapse your supply-chain perimeter entirely.

## Last line of defense

For destructive or privileged REST actions: gate them behind a **high-privilege token requiring biometric/MFA** — a fingerprint to drop the production database. When agents machine-gun your endpoints, proof of a human's physical presence at the moment of the critical action is the final backstop. See also [[dual-leg-rule]] for the architectural pattern that limits blast radius.

## See also
- [[dual-leg-rule]]
- [[tool-calling]]
- [[tool-context]]
- [[mcp-transport]]
- [[2026-06-10-spring-ai-itkonekt]]
