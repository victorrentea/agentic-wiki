# AI-Assisted Coding @ a medical-diagnostics company — Day 3 — 2026-07-08

_generated in ≈11m · condensed for a \~30-min read_

The deep hands-on day, elbows in the client's real legacy Java EE codebase (servlets, EJBs, Oracle, an Angular front-end, XML-on-the-wire, JAMA for compliance). Less slideware, more scars: a live mob-review of the giant DAO→EJB **"Savior-PR"** with [Fable](https://www.anthropic.com), a JDBC connection-leak hunt, the **transaction-interceptor rollback** gotcha, regenerating a decoupled module from specs, and a full **QA track** on turning Word test-case docs into automated [WebDriver](https://webdriver.io) + [Allure](https://allurereport.org) suites. The recurring theme: _"repaying the sins of the ancestors"_ — and building tools so you don't have to.

## Full AI adoption is the only mode

- You can't half-use it — do *everything* with the agent and forget writing by hand; **half-manual + half-AI doesn't work**.
- The real leap is **agentic automation**: wire the agent to [JAMA](https://www.jamasoftware.com/platform/jama-connect/), [Jenkins](https://www.jenkins.io/doc/book/managing/cli/) and [Bitbucket](https://marketplace.atlassian.com/apps/1211193/bitbucket-command-line-interface-cli) and the DB — "a super power-user clicking for you."
- Make it a habit/KPI: **report daily what you automated**, even something trivial (≈1 test-case per tester per week).
- Tear down the QA↔dev wall — devs run and improve the testers' e2e suites. *"You can't run with AI faster than the team can comprehend."*

## QA: turning test-case docs into automation

- Core loop: drop the *simplest* un-automated test-case **.doc** in the project → agent converts it to **WebDriver.io** + generates an **Allure** report → review in browser → tell it what's missing → it adjusts. (one .doc drove 3 reports: HH/ML/QL)
- **Spot a red in Allure → investigate by hand *why* → tell the agent how to fix the test.** That little cycle is the whole job.
- Custom **Allure HTML reporter** tuned to taste: hover an expected-result to see its code, ctrl-click to jump to the exact line, a column maps each .doc phrase → its assertion.
- **Bidirectional trace**: highlight in RED every .doc word the AI never used; goal = zero red. *"Every comma in the test case is meant to be there."*
- Selectors live in **[page objects](https://martinfowler.com/bliki/PageObject.html) / widgets** — never inline in test code.
- Built a **reading-word-document skill** to decode the doc's quirks ("D" = Disabled, repeated N/A rows, preconditions like pre-creating a patient with extra data).
- **Boy-scout rule**: stop hand-editing the test-case .doc — automate it, make your change in the *real* test, then **delete the .doc**.
- New feature? Let the agent explore the screen and auto-write the tests — you'll learn how the test case *should* have been written. Feed it:
  - JIRA acceptance criteria (via MCP/CLI)
  - the transcript of the meeting where the feature was discussed
  - browser control to see + click the screen, plus the JAMA specs
- *"It's degrading for humans to check that what worked yesterday still works today"* ([Venkat Subramaniam](https://agiledeveloper.com)) — automate regression, free testers' brains for exploratory/creative testing.
- Prefer real **e2e** (back-end + DB); demote trivia (max-length, validation messages) to fast **Unit-UI** tests, tagged in a report column.
- Parallelizing e2e races on shared data → give each suite its **own data partition** (new patient/doctor/device), cleaned up after.
- A disabled button is *not* a secured feature — a user who discovers **curl** bypasses it. 🦄 One backend source-of-truth for per-session authorization drives *both* the Java **[@Secured](https://docs.spring.io/spring-security/reference/servlet/authorization/method-security.html) annotations** and the hidden front-end buttons — zero drift.
- Allure enhancement: one shared table-generator emits **both HTML and PDF** (only the renderer differs) → no HTML-vs-PDF drift.
- Baby steps beat retyping: Victor caught a real test bug (it filled the *time* but never the *date*) just by watching the screen, then told the agent to fix it.

## Regenerating a decoupled module from specs

- **[Strangler](https://martinfowler.com/bliki/StranglerFigApplication.html) play**: point AI at a small, decoupled module (QC, ≈10–12 entities), have it write specs, then regenerate it with modern architecture — orthogonal to a developer's byte-for-byte DAO refactor.
- Legacy teams *always* over-engineer the rewrite — **[hexagonal architecture](https://alistair.cockburn.us/hexagonal-architecture/) is overkill** for 20 classes / 3-line services. Be pragmatic, slice by feature.
- Deploy a **Spring companion app** next to WildFly, sharing the same DB via a separate **schema + views** — avoids Kafka/Debezium eventual-consistency pain.
- Keep the old front-end surface unchanged so e2e still passes; share auth by sending one **[Keycloak](https://www.keycloak.org)/PKCE** token to both back-ends. *"The best deploy looks exactly the same."*
- **Opus** can read + rewrite ≈100k lines in-memory with a low error rate — but only under real e2e coverage (aim ≥20%).

## The "Savior-PR": reviewing a byte-for-byte DAO→EJB refactor

- Reviewed a developer's PR (God-DAOs gone, SQL moved *verbatim*, build green) against a **Fable-authored review .md** with click-to-line + git-history-proof links.
- **The winning instruction was "maintain the exact behavior — move, don't rewrite"** → code moved verbatim, structural change without re-reasoning the logic; keep only the EJB+repo pair in context so the move stays clean.
- Bug class hunted: PreparedStatements / ResultSets closed *outside* `finally` **leak cursors** on the thread-local JTA connection → eventual "max open cursors" / connection starvation.
- **Always separate a preexisting master bug from a PR-introduced regression** — the click-to-history proof showed the same leak in old code, so it's not the PR's fault.
- Strategy: run Fable on **master's impacted files**, fix small-and-fast there, then merge master → refactor branch (**backwards**) — never grow the 100k-line branch. Latent bug too big to fix inline → **file a JIRA ticket** ("the fair thing to do").
- The *one* genuine new defect across 40 new EJBs: a per-call `Result` (27 mutable fields) lifted into a **pooled `@Stateless` EJB** — pooled instances share state across calls. *"The first single mistake in 100,000."*
- The tell: **`initialize()` / `finalize()` lifecycle methods** on an EJB — the DAO pattern imitated where it makes no sense; fix is boring (inline the vars, try-with-resources).
- Don't fear migration bugs: AI bug-fixing cut a fix from a week to ≈4 hours deployed, and regression runs anyway. *"This stinks of fear."*

## The transaction-interceptor rollback bug ⭐

- **A behavior flip caused by one annotation.** The migration added `@Interceptors(TransactionInterceptor)` to a `@Stateless` request-EJB that had none — same code, opposite transaction outcome:
  - **BEFORE:** `throw AppException` → **COMMIT** (the historic EJB "checked-exception = commit" mistake)
  - **AFTER:** `throw AppException` → **ROLLBACK** (the interceptor calls **[`setRollbackOnly()`](https://jakarta.ee/specifications/platform/9/apidocs/jakarta/ejb/ejbcontext)**)
- **Interview-grade rule**: EJB container-managed tx default — `RuntimeException` → rollback; a *checked* exception → commit. *"One of the biggest mistakes in EE history."*
- How it surfaced: the REST migration moved logic *down* from the servlet into the EJB, dragging exceptions across the newly-intercepted boundary.
- **Script it, don't scan**: write a program that parses Java into an **[AST](https://github.com/javaparser/javaparser)** and finds every `catch(AppException)` whose body has a branch that never re-throws — don't let AI eyeball 100k lines and hallucinate. Unit-test the scanner on positive & negative examples; run it via a sub-agent.
- **Delicate tx semantics stall AI**: it took ≈7 people + AI to fix 1 of 6 cases. *"AI can't find its way here alone — it needs assistance."*
- **Convergence = confidence**: Fable independently proposed the *same* fix (extract a `boolean`-returning method, don't throw) that Victor + that developer designed a year ago but never trusted enough to commit — review the scary cases ≈10–20× until the AI self-suggests it, then trust it and capture a skill.
- Exceptions-for-flow-control (thrown two layers down, caught two up) is a textbook anti-pattern — and lethal here because the interceptor blows up the whole transaction.
- 🤖 Good-citizen touch: have Opus add `@Deprecated` to the dead paths with a *human-worded why* + what to call instead; methods reachable only from tests → kill them after the refactor.

## Guardrails from this day's bugs

- Every recurring bug → a **scriptable check on the pipeline**, not an eyeball hunt: agent greps *now* to inventory occurrences **and** a sub-agent builds the permanent rule. *"I want this as a test on my pipeline."*
- Flag any ResultSet/PreparedStatement/Connection created outside **[try-with-resources](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)** — via **[ArchUnit](https://www.archunit.org)/PMD/Checkstyle**.
- 🤖 Check whether the tool already ships the rule before hand-rolling: the leak check is **[Sonar S2095 "Resources should be closed"](https://rules.sonarsource.com/java/RSPEC-2095/)** — the rule he hunted for live on stage.
- 🤖 On "can you close it twice?" (left open at [10:38]): per the JDBC spec, `close()` on an already-closed Connection/Statement/ResultSet is a **safe no-op** — double-close is not the leak.

## Closing — adopt incrementally

- The group "hit the wall of the transaction interceptor" *again* — the recurring legacy blocker of the day.
- Roll out QA automation **incrementally**: one test each morning, not an aggressive all-at-once push (*"too aggressive for them"*); follow up weekly — *"show me the word, show me the Allure."*

## 🎯 Main takeaways

- **Move, don't rewrite.** "Maintain the exact behavior" moved 100k lines byte-for-byte — structural change without re-reasoning the logic.
- **Separate preexisting bugs from PR regressions** — fix them on master and merge *backwards* into the branch; never grow the giant refactor branch.
- **One annotation can flip transaction semantics** — `@Interceptors(TransactionInterceptor)` turned a committing `AppException` into a rollback. Know your EJB tx rules.
- **Script it, don't scan.** Turn every recurring bug into a pipeline check (AST parser, ArchUnit rule) — don't make AI eyeball 100k lines and hallucinate.
- **When AI stalls on delicate semantics**, review the scary cases ≈10–20× until it self-suggests the fix — *then* trust it and capture a skill.
- **Automate regression** — "it's degrading for humans to check that yesterday's work still works." Free testers for exploration.
- **A disabled button ≠ a secured feature** (curl bypasses it) — one backend source-of-truth drives both the Java annotations and the hidden UI.

## 📚 Session wiki

A browsable Obsidian wiki of the whole workshop (Days 1 & 3). Once the wiki is shared, one command on macOS installs Obsidian if needed, downloads this session's wiki, and opens it on Home — paste the shared `wiki.zip` link in place of `<PASTE-SHARED-URL>`:

```bash
brew list --cask obsidian >/dev/null 2>&1 || brew install --cask obsidian; \
curl -L "<PASTE-SHARED-URL>" -o /tmp/session-wiki.zip && \
unzip -o /tmp/session-wiki.zip -d "$HOME/session-ai-wiki" && \
open -a Obsidian "$HOME/session-ai-wiki/wiki"
```
