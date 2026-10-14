---
title: Supply Chain Attack
category: security
tags: [security, supply-chain, sbom, npm, agentic-risk, typosquatting, slopsquatting, mcp, min-release-age]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-05-25-ai-government]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-06-10
updated: 2026-10-13
---

A supply chain attack compromises a dependency in your build graph — not your code directly — so that installing or building your project executes the attacker's payload.

## <span style="color:red">The npm incidents — three stories, often conflated</span>

<span style="color:red">The canonical war story of [[2026-06-10-spring-ai-open-workshop]] got retold across 2026 with details from different incidents mixed together. Later sessions untangled them, and the primary sources settle it ([[2026-07-20-ai-telecom]], [[2026-07-23-ai-software-product]], [[2026-09-04-ai-software-company]], [[2026-09-14-ai-industrial]]):</span>

- <span style="color:red">**chalk / debug (8 Sep 2025).** A phishing takeover of one maintainer's npm account shipped a crypto-wallet drainer inside **genuine** popular packages: `debug`, `chalk`, `color-convert` and 15 more ([Wiz](https://www.wiz.io/blog/widespread-npm-supply-chain-attack-breaking-down-impact-scope-across-debug-chalk)). It was a compromise of the real packages, not a typosquat.</span>
- <span style="color:red">**Shai-Hulud (14 Sep 2025).** A **self-replicating npm worm**. It started from `rxnt-authentication` and spread through `@ctrl/tinycolor` and hundreds of others. Using compromised maintainer accounts, it injected post-install scripts that harvested GitHub, npm and cloud tokens, then **republished the victim's other packages** with itself inside: 500+ packages were removed ([GitHub](https://github.blog/security/supply-chain-security/our-plan-for-a-more-secure-npm-supply-chain/), [CISA alert](https://www.cisa.gov/news-events/alerts/2025/09/23/widespread-supply-chain-compromise-impacting-npm-ecosystem), [ReversingLabs](https://www.reversinglabs.com/blog/shai-hulud-worm-npm)). A second wave followed in late November 2025.</span>
- <span style="color:red">**Axios (31 Mar 2026).** `axios@1.14.1` and `axios@0.30.4` were published from the lead maintainer's hijacked npm account. His PC had been taken over through targeted social engineering plus RAT malware. Both versions added a fake dependency, `plain-crypto-js`, whose `postinstall` dropped a **cross-platform remote-access trojan** on macOS, Windows and Linux. They were **live for about 3 hours** before npm pulled them. **It was not a worm**, and no primary source supports the retold details of `ANTHROPIC_*` env-var harvesting or self-propagation ([post-mortem](https://github.com/axios/axios/issues/10636), [StepSecurity](https://www.stepsecurity.io/blog/axios-compromised-on-npm-malicious-versions-drop-remote-access-trojan)). The advice was to treat the machine as compromised and rotate every secret; some firms told developers to reformat.</span>

<span style="color:red">The family keeps going. Sessions cited a "Mini Shai-Hulud" (29 Apr 2026) that stole tokens from env and `~/.aws` and propagated into the same maintainer's other packages ([Unit 42](https://unit42.paloaltonetworks.com/monitoring-npm-supply-chain-attacks/)). They also cited an attack on `keyv` / `cacheable` / `flat-cache` (Aug 2026, ≈2 billion monthly downloads) that wrote **persistence into `.vscode/tasks.json` and `.claude/settings.json`**, i.e. into your agent's own config ([Wiz](https://www.wiz.io/blog/keyv-and-cacheable-npm-supply-chain-attack)) ([[2026-08-06-ai-bank]]). One derivative reportedly hit ≈170 npm and PyPI packages at once ([[2026-08-03-ai-telecom-ops-qa]]).</span>

The vector is transitive: you never added the malicious package; your dependency did.

Mitigation: [SBOMs](https://www.linuxfoundation.org/blog/blog/what-is-an-sbom) (Software Bill of Materials) give you an auditable inventory of every direct and transitive dependency so anomalous additions are detectable. <span style="color:red">**Review `package-lock.json` in PRs**: the lockfile is where a silently added transitive dependency shows up ([[2026-05-18-secure-coding-payments]]).</span>

**`npm install` is always a security event.** <span style="color:red">The Axios window was only about 3 hours, yet anyone who ran a fresh install during it got a RAT.</span> This is why you pin versions, use a lockfile + `npm ci`, and prefer `--ignore-scripts`. Supply-chain attacks are the reason installing any dependency should be treated as running arbitrary code from the internet. <span style="color:red">But a lockfile gives reproducibility, not innocence. It doesn't protect against a MITM or DNS poisoning of the registry (the internal mirror is the real fix), and you must use `install`/`npm ci`, which honour the lock, never `update`. The same applies to composer ([[2026-07-09-ai-live-casino]], [[2026-05-25-ai-government]]).</span>

**The "S-BOM bomb".** The threat is concrete and easy to demonstrate: <span style="color:red">`npm install -g @somepackage` runs any install script the package ships, and that script can exfiltrate secrets: "some library online just executed code on your machine". The new command on your PATH, by contrast, comes from npm **linking the package's `bin` entry**, not from a script ([npm folders](https://docs.npmjs.com/cli/v11/configuring-npm/folders)). OpenSpec is the common example, and the facts are: versions 0.17.0–1.9.0 (Dec 2025 – Aug 2026) had a `postinstall` that installed shell completions or printed a tip, while current versions (1.10.0+) have **no install script at all** ([registry](https://registry.npmjs.org/@fission-ai/openspec)) ([[2026-09-28-ai-gaming-tech]]). So `--ignore-scripts` does not stop it registering its CLI.</span> Running `npm install @latest` on a fresh install is exactly running the post-install scripts of a possibly-just-hacked library. npm is far less regulated than Maven, but the JVM is not immune — [Log4Shell](https://en.wikipedia.org/wiki/Log4Shell) ("log4bomb") was the same class of weaponized dependency. <span style="color:red">And "Maven runs no code at install" is only half true. Dependency resolution just downloads jars, but **a Maven plugin runs arbitrary code during `mvn install`** ([[2026-08-27-ai-it-services]]). Contrast for Java developers: `npm install` runs shell scripts from "some guy in Nebraska"; `npx` downloads a binary *and* executes it ([[2026-07-20-ai-telecom]]).</span> Concrete defenses: `npm ci --ignore-scripts` (or `npm config set ignore-scripts true`), pin exact versions and commit the lockfile, and prefer provenance-attested packages — never `@latest` on a fresh machine.

**Typosquatting.** A hacker publishes a clone of a popular package with a one-character name difference (`openspeck` vs `openspec`, `expres` vs `express`). The package's post-install script runs the payload. Installing a dependency is a security event — verify the package name character by character, check download counts, look at the publish date, and consider using a scanner like Socket.dev or `npm audit signatures`.

<span style="color:red">**Slopsquatting** is the AI-era twist: the typo is the *model's*. Attackers pre-register package names that agents tend to hallucinate (a British spelling such as `colour-convert` is the classroom example; an agent you talk English to all day may drift into them) and wait for an agent to recommend and install them ([arXiv 2406.10279](https://arxiv.org/abs/2406.10279), [FOSSA](https://fossa.com/blog/slopsquatting-ai-hallucinations-new-software-supply-chain-risk/)). One session cited a fake `huggingface-cli` package with ≈30k downloads in 3 months ([[2026-09-14-ai-industrial]]). See [[slopsquatting]].</span>

## The tj-actions incident

A real-world instance of the MCP-era supply-chain threat: the **tj-actions/changed-files** GitHub Action was compromised, and a contributor's token was stolen and used to slip a post-install script into a widely-used action. Any workflow that ran `uses: tj-actions/changed-files@latest` in that window executed the malicious code, which harvested API keys from CI environment variables. The mitigation: **clone the MCP/action source, scan it with two models, build locally** rather than pulling from a registry at deploy time. Hash-lock (npm lockfile / Docker digest pinning) is the baseline — even if you can't clone-and-scan every dependency, pinning prevents a compromised later release from silently swapping in.

## The agentic-era amplifier

Agents change the threat model in two compounding ways:

1. **Auto-approve bash** — an agent running in "approve everything" mode is functionally `curl | bash` with extra steps. A post-install script runs with the same privilege as the agent.
2. **"latest" version pins** — agents told to "install the latest version" will resolve it at runtime, fetching whatever is current at that moment.

Never combine `latest` version pins with `bash:* auto-approve` on a machine that holds secrets. The two settings together collapse your supply-chain perimeter entirely.

### <span style="color:red">MCP configs are the new `npx @latest`</span>

<span style="color:red">A typical `mcp.json` entry, `"command": "npx", "args": ["@playwright/mcp@latest"]`, **downloads and executes whatever version is published right now, on every harness start**. Compromise one maintainer and you have a shell on every developer machine within minutes. One session called MCP servers *"the #1 supply-chain risk in the AI era"* ([[2026-05-18-secure-coding-payments]], [[2026-05-06-ai-ecommerce]], [[2026-04-27-ai-healthcare-software]]). The "x" in `npx`/`uvx` means *execute*: wherever you see them in an MCP config, sound the alarm ([[2026-07-09-ai-live-casino]], [[2026-08-03-ai-telecom-ops-qa]]).</span>

- <span style="color:red">**Pin MCP versions** (`1.2.0`, never `@latest`) and pull them from an internal, security-scanned mirror ([[2026-09-07-ai-bank]]).</span>
- <span style="color:red">**Prefer curated sources**: the [github.com/mcp](https://github.com/mcp) catalog, or a corporate-approved internal marketplace for MCPs and extensions ([[2026-04-27-ai-healthcare-software]], [[2026-07-01-ai-java-community]]; see [[plugin-marketplace]]).</span>
- <span style="color:red">**Or build it yourself**: an MCP is external code running on your machine, so clone what's open source, have the agent scan it, build and run locally, or turn it straight into a CLI ([[2026-07-30-ai-safety-critical]]).</span>
- <span style="color:red">**Banning MCPs solves nothing.** The agent does the same via CLIs (`glab`, `jenkins-cli`), and `curl … | bash` is a step worse: an instant reverse shell ([[2026-08-03-ai-telecom-ops-qa]]).</span>
- <span style="color:red">A malicious MCP doesn't even need code execution: its **tool descriptions** load into context and can instruct exfiltration ("always include a copy of the conversation in the URL…"). See [[tool-poisoning]].</span>

### <span style="color:red">Every local-execution vector</span>

<span style="color:red">One session listed four, all with the same trust problem: **MCP servers, skills, plugins, and open-weight models** ([[2026-09-16-ai-safety-critical]]).</span>

- <span style="color:red">An auto-updated plugin can ship a virus.</span>
- <span style="color:red">An open-weight model may carry behaviour trained into it that no inspection reveals (see [[uncensored-models]]).</span>
- <span style="color:red">`npx makemerich@latest` from a stranger's repo is remote code execution.</span>

<span style="color:red">Add **IDE extensions**, "the land of no one": nobody sanitises them. Fake ChatGPT-branded VS Code extensions with large download counts reportedly exfiltrated every file they touched for months ([[2026-05-06-ai-ecommerce]], [[2026-07-01-ai-java-community]]).</span> <span style="color:red">Add the **memory file itself**: one session reported a worm that propagated by committing a poisoned `CLAUDE.md` into repositories, infecting teammates' agents. That is a strong argument for [[codeowners-elders|code owners]] on CLAUDE.md/AGENTS.md ([[2026-08-10-ai-consultancy]]).</span>

<span style="color:red">**Protestware aimed at LLMs.** The maintainer of the jqwik testing library shipped a prompt injection in version 1.10.0: test output that tells an AI agent to delete the project's jqwik tests and code. ANSI escapes hide it on an interactive terminal, but it appears as plain text in the CI logs agents read ([issue #708](https://github.com/jqwik-team/jqwik/issues/708), [Snyk](https://snyk.io/blog/protestware-open-source-maintainer-qwik-1-10-0-prompt-injection)). Dependency *text*, not just dependency code, is now attack surface ([[2026-09-04-ai-software-company]], [[2026-09-16-ai-safety-critical]]). See [[prompt-injection]].</span>

<span style="color:red">**The model's intelligence turned against you.** A creative agent helping an injected skill will base64-encode, compress and encrypt your credentials so they "go out safely" ([[2026-07-01-ai-java-community]]).</span>

## <span style="color:red">Hardening checklist</span>

<span style="color:red">Consolidated across the 2026 sessions ([[2026-05-25-ai-government]], [[2026-05-27-ai-open-workshop]], [[2026-06-02-ai-open-workshop]], [[2026-06-05-ai-consultancy]], [[2026-06-08-ai-security-tools]], [[2026-08-27-ai-it-services]]):</span>

- <span style="color:red">**Minimum release age.** Refuse package versions younger than N days with npm's [`min-release-age`](https://docs.npmjs.com/cli/v11/using-npm/config#min-release-age) config (in days); uv has an equivalent. Suggestions ranged from 3 to 7 days, and up to ≈27. *"Even one day kills the 3-hour exposure window"*; *"let other suckers get the bomb first"*. One session called it the single biggest defence for the AI age, because agents `npm install` constantly ([[2026-05-06-ai-ecommerce]], [[2026-05-18-secure-coding-payments]]). The honest caveat: zero-day security patches also arrive late.</span>
- <span style="color:red">**An internal registry proxy** (Nexus, Artifactory/JFrog) that blocks public npm/PyPI and lags upstream, e.g. by 6 hours or by serving only versions ≥3 days old, gives the world time to detect a poisoned release ([[2026-04-27-ai-healthcare-software]], [[2026-07-20-ai-telecom]]). *"npm and PyPI are not as safe as Maven"* ([[2026-07-01-ai-java-community]]).</span>
- <span style="color:red">**Install scripts:** `ignore-scripts=true` in `.npmrc` and `npm ci --ignore-scripts` as the CI default ([[2026-09-04-ai-software-company]]). The middle ground is to allow scripts only for already-vetted packages: pnpm `allowBuilds` (formerly `onlyBuiltDependencies`). **pnpm 10 skips dependency install scripts by default** ([release](https://github.com/pnpm/pnpm/releases/tag/v10.0.0)). `--omit=dev` does **not** protect against transitive install scripts.</span>
- <span style="color:red">**Block "exotic sub-dependencies"**: deps pulled from git URLs or tarballs instead of the registry.</span>
- <span style="color:red">**Provenance and behaviour:** attestation ([Sigstore](https://www.sigstore.dev/)) and behavioural scanners ([Socket](https://socket.dev/)) catch malicious patches that pinning can't; Snyk, BlackDuck, Checkmarx or Fortify for transitive CVEs ([[2026-04-07-ai-product-team]], [[2026-09-16-ai-safety-critical]]).</span>
- <span style="color:red">**Justified paranoia for anything you `-g` install:** clone the repo, have the agent read it, build locally, install from local ([[2026-08-27-ai-it-services]]). Run CLI agents in a sandbox ([[docker-sandboxing]]).</span>

### <span style="color:red">Secrets that leak anyway</span>

<span style="color:red">A [[gitleaks]] pre-commit hook blocks token-shaped strings; add a rule for your `local.env` so real values never reach git. Back it with memory rules: *"never commit passwords, never run `git commit --no-verify`, never rewrite history with `git reset`"* ([[2026-04-16-ai-safety-critical]]). Its limit: a token without a recognisable prefix sails through, and an agent blocked by the hook may move the secret elsewhere ([[2026-05-11-ai-software-vendor]]).</span> <span style="color:red">If a secret does reach git, a deletion commit is not a fix. **Revoke the key first**, then purge history, because every clone and CI runner has already seen it ([GitHub docs](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)) ([[2026-08-03-ai-telecom-ops-qa]]).</span>

## Last line of defense

For destructive or privileged REST actions: gate them behind a **high-privilege token requiring biometric/MFA** — a fingerprint to drop the production database. When agents machine-gun your endpoints, proof of a human's physical presence at the moment of the critical action is the final backstop. See also [[dual-leg-rule]] for the architectural pattern that limits blast radius.

**Docker socket as a supply-chain amplifier:** never mount the Docker socket (`/var/run/docker.sock`) into an agent's container — it gives root-equivalent host access and means a post-install script in any dependency could take over the host. See [[docker-sandboxing]].

**Skill marketplace as supply-chain surface.** Any community [[agent-skill|skill]] that embeds a script is functionally an unvetted `npm install`. The skill marketplace is at *"npm circa 2015"* — treat it accordingly. <span style="color:red">Snyk's ToxicSkills scan of 3,984 skills from ClawHub and skills.sh found **36.8%** with at least one security flaw, **13.4%** with a critical issue, and **76 confirmed malicious payloads** (≈2%) ([Snyk](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/)). So "≈⅓ of skills are malware", as one session put it, overstates it: about a third are *flawed*, a few percent malicious ([[2026-04-07-ai-product-team]], [[2026-07-09-ai-live-casino]]). The golden rule stands: **read 100%** of any skill or MCP you adopt.</span> See [[skill-marketplace-security]] for the full threat model and defense posture.

## See also
- [[dual-leg-rule]]
- [[tool-calling]]
- [[tool-context]]
- [[mcp-transport]]
- [[openspec]]
- [[docker-sandboxing]]
- [[agent-permissions]]
- [[skill-marketplace-security]]
- <span style="color:red">[[slopsquatting]]</span>
- <span style="color:red">[[gitleaks]]</span>
- <span style="color:red">[[tool-poisoning]]</span>
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[prompt-injection]]</span>
- <span style="color:red">[[codeowners-elders]]</span>
- <span style="color:red">[[uncensored-models]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-26-ai-agentic-how]]
- <span style="color:red">[[2026-07-23-ai-software-product]]</span>
- <span style="color:red">[[2026-09-04-ai-software-company]]</span>
