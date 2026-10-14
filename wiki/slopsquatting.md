---
title: Slopsquatting
category: security
tags: [security, supply-chain, npm, pypi, hallucination, dependencies]
sources: ["[[2026-05-06-ai-ecommerce]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-09-ai-live-casino]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Slopsquatting is typosquatting for the AI era: attackers pre-register the package names that LLMs *hallucinate*, and wait for an agent to `npm install` or `pip install` them ([Wikipedia](https://en.wikipedia.org/wiki/Slopsquatting)).

## How it works

Classic typosquatting bets on human typos. Slopsquatting bets on the model's own biases — spelling, regional variants, plausible-sounding combinations. The classroom example: a model suggests `colour-convert` (British spelling) where the real package is `color-convert`; if an attacker has registered the hallucinated name, the agent installs malware with full confidence. (`colour-convert` is not on npm today — the example illustrates the shape, not a live package.)

It is industrialised: attackers ask models which names they **repeatedly** hallucinate, then register those. Repetition is what makes it work — a USENIX Security '25 study ([arXiv 2406.10279](https://arxiv.org/abs/2406.10279)) found ≈5.2% of package suggestions from commercial models and ≈21.7% from open-weight ones were hallucinated, and many recurred consistently across generations: predictable targets.

## Why agents make it worse

A human pausing over an unknown package name is a defence; an agent on auto-approve is not. The risk is amplified by agents running `npx …@latest` / `uvx` and by blanket Bash approval ([[approval-fatigue]]) — and the payload usually lands through a `postinstall` script, which runs at install time before anyone looks at the code. See [[supply-chain-attack]] for the install-script mechanics and the 2026 Axios incident.

## Defences

- **Prefer existing dependencies.** Instruct the agent ([[claude-md]]) not to add a new package without saying so; any new entry in `package.json`/`pom.xml` is a review item, ideally routed to [[codeowners-elders|CODEOWNERS]].
- **Verify before adding**: fetch the registry page (WebFetch) and check the name, download counts, publish date and repository link. A name that appeared last week with no history is the tell.
- **Don't auto-approve `npm install`** of a package that isn't already in the lockfile ([[agent-permissions]]).
- **Internal registry or mirror** that developer machines *and* agents are forced to pull from. Banks already do this for production builds; "the new battle is developer machines". Add a **minimum-release-age** policy so brand-new versions wait out the first days.
- **`--ignore-scripts`** on installs, plus behavioural scanners (e.g. Socket) that flag install-time network or file access.
- **Contain the blast radius** with an [[os-sandbox]] or [[docker-sandboxing|sandbox]]: a malicious postinstall that cannot read `~/.ssh` or reach the network has little to steal.

## The knowledge-base cousin

The same "plant bait where the model will reach" idea works on retrieval: a departing employee leaves a keyword-stuffed Confluence page that, once vectorised, ranks first in every search and carries a `DROP TABLE` or `rm -rf` snippet. Curate the corpus and treat retrieved snippets as untrusted input — see [[rag]] and [[memory-poisoning]]. Skill and plugin marketplaces are a third vector of the same family ([[skill-marketplace-security]]).

## See also
- [[supply-chain-attack]]
- [[hallucination]]
- [[skill-marketplace-security]]
- [[agent-permissions]]
- [[codeowners-elders]]
- [[os-sandbox]]
- [[rag]]
- [[memory-poisoning]]
- [[2026-05-06-ai-ecommerce]]
- [[2026-05-27-ai-open-workshop]]
- [[2026-08-31-ai-software-vendor]]
