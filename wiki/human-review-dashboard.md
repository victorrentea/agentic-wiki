---
title: Human Review Dashboard
category: tool
tags: [code-review, pull-request, diagrams, determinism, review-report, observability, semantic-coverage]
sources: ["[[2026-08-31-ai-software-vendor]]", "[[2026-08-13-ai-insurer]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

A human review dashboard is an HTML page per pull request that assembles script-generated, un-hallucinable artifacts — diagrams, contract diffs, schema diffs, a demo video, test-to-requirement mapping — so a human can review a 1,000–3,000-line agent PR in one ≈45-minute session, staying high-level and dropping into code only where it matters; Victor's implementation is [victorrentea/human-review](https://github.com/victorrentea/human-review).

## The principle: a program seasoned with AI

Every tab is produced by a **script, at zero tokens** — so it cannot hallucinate, and re-running it tomorrow gives the identical report. The **LLM enters only at the end**, for the few judgement calls a script can't make ([[determinism-over-llm]], [[deterministic-diagrams]]). That inversion is the point: [[code-review|review]] is the bottleneck, and the reviewer must be able to trust what they're shown without re-verifying it. In one guided-review run the report itself cost **0% AI**; a full guided review of the demo cost $3.79 ([[2026-09-14-ai-industrial]], [[2026-09-16-ai-safety-critical]]).

Public material: the [human-review demo](https://victorrentea.github.io/human-review/demo/review.html) and a [demo release](https://github.com/victorrentea/human-review/releases/tag/demo). Victor's own remark: it was vibe-coded and never read — *AI's best job is building your own tooling* ([[2026-09-25-ai-bank-migration]]).

## Tabs

One tab per question a reviewer must answer. The set evolved across sessions (Aug–Oct 2026):

| Tab | What it shows | How it's generated |
|---|---|---|
| **Behaviour** | A PlantUML sequence diagram per test, with SQL parameters; a C2 view aggregated from traces | e2e test traces (100% sampling in e2e, ≈1% in prod, correlated by trace ID). A gap in the diagram reveals missing [[observability-mcp|observability]] |
| **API contract** | Breaking vs additive changes, main vs PR (plus AsyncAPI / MCP contract) | [oasdiff](https://github.com/oasdiff/oasdiff), cross-checked with a second tool ([[openapi-contract-sync]]) |
| **Data model** | DB schema diff with neighbouring tables only; red = changed, struck = deleted | Flyway migrations applied in Testcontainers/Postgres → `db.puml`; domain diff by reflection |
| **Structure** | Maven modules and packages | computed from code ([[c4-model]]) |
| **Complexity / shape** | [Cognitive complexity](https://www.sonarsource.com/docs/CognitiveComplexity.pdf) before→after per endpoint; fan-in/fan-out of changed classes — *"those depend on me; if I change, they blow up"* | static analysis ([[bug-heatmap]]) |
| **Logging** | New log lines and which attributes flow into them — a GDPR check | [ast-grep](https://ast-grep.github.io), not regex |
| **UX** | Native controls used where a design-system component was required; visual regression | screenshot diff |
| **Demo** | A short narrated, TikTok-style Playwright recording of the feature | Playwright video + synthetic voice (cents); the LLM writes only captions |
| **Requirements** | Which test covers which sentence of the requirement; **red = no test** | the one LLM tab ([[semantic-coverage]]) |
| **Cost** | Dollars per phase — e.g. $20 implement / $6 review | harness telemetry |

Supporting pieces: the app pre-deployed **per branch** in Docker in seconds, with the DB reset to named states; links that open file+line in the IDE; and every PR file mapped to **the tests that traverse it** ([[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]]). Demos lead with BDD-level [[acceptance-test-bdd|acceptance tests]] — e.g. *visit has a vet*, *owners grid paged & sorted* ([[2026-10-09-ai-jax-london]]).

Anti-patterns the tabs catch in practice: one DTO shared between GET and request bodies (*"vandalism"*); a native control instead of the design-system one; a log line leaking personal data.

## Where the AI does enter

- **Review points** ordered by the AI by where you must be awake; auto-fixed issues listed at the end. One such review cost ≈$11; a 3,000-line PR was reviewed for ≈$42 ([[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]]).
- **The coder's assumptions and solo decisions with a confidence** (e.g. a 60% item), and reviewer findings it rejected, posted as PR comments — the [[assumptions-ledger]].
- **Judgement**: explaining obscure code, flagging hidden assumptions (*"if you have a first name you have a last name"*, concatenated without a guard), GDPR on logs.
- **Requirement coverage**: what stays red is the tester's territory; hand-marked critical zones (login, a 10k req/min flow) raise attention.

The idea grew from an earlier shape in [[2026-08-13-ai-insurer]]: an Opus sub-agent running `/code-review` + `/simplify`, a second review in another conversation, and an HTML report sorted by criticality with the most important test rendered as a sequence diagram — *"take me by the hand"*.

## Design rule: less is more

*"Excess signal shuts down reasoning."* Don't add a tab for every curiosity — a reviewer with ten panels reads none carefully. Side questions go to a scout agent kept open beside you ([[code-review]]), not into the report. It's scaffolding: a `review-points.md` next to the spec files holds what agents found, expected to be removed in a few months as trust grows ([[gray-factory]]).

## See also
- [[code-review]]
- [[assumptions-ledger]]
- [[review-by-images]]
- [[deterministic-diagrams]]
- [[semantic-coverage]]
- [[field-reality-diagram]]
- [[agentic-review-pipeline]]
- [[determinism-over-llm]]
- [[code-city]]
