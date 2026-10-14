# Testing cu AI @ o bancă — 10–11 septembrie 2026

*Rezumat generat automat din transcrierea sesiunii.*

Prima zi: de ce scriem teste și ce se schimbă când testele nu le mai scrii tu de mână. Am mers de la motivele reale din spatele testării, prin Gherkin, mock-uri și piramida testării, până la mutation testing, test data builders și felul în care un agent poate reproduce un bug și scrie testul care îl prinde.

A doua zi: agenții intră efectiv în munca de zi cu zi a unui QA — citesc din bază, manevrează browserul, sapă prin build-uri, iar lecțiile învățate se împachetează în skill-uri.

# 📅 Ziua 1 — 10 septembrie 2026

## De ce scriem teste

**Testele există ca să țipe ele mâine, nu userul — siguranța de mâine e motivul #1.**

- Poll de deschidere: cât cod mai scrieți **cu mâna**? Răspunsuri între 0% și 30%.
- ❌ Anti-pattern: **[80% coverage](https://martinfowler.com/bliki/TestCoverage.html)** pe CI ⇒ click-dreapta > generate tests, pe care nu le citește nimeni.
- **Fast feedback**, ca să-ți ții flow-ul: validezi azi, repede, fără partea repetitivă.
- Ca să nu mai faci **manual testing**: „este degradant pentru ființa umană să verifice cu mouse-ul că ceea ce mergea ieri merge și azi".
- Ca să **[clarifici cerințele](https://github.com/victorrentea/petclinic/issues/25)** cu businessul — dacă nu poți testa o cerință, n-ai înțeles-o.
- Ca **feedback de design**: codul prost e greu de unit-testat, iar testul îți spune asta primul.
- Pentru **audit**: auditorul verifică testele, nu codul care rulează în prod.
- Pentru **corner case-uri** pe care mâna nu le încearcă: nume de 1GB, vârstă negativă, `\t` în câmp.

## Gherkin și BDD

**Gherkin e doar limbajul; BDD înseamnă că testul e scris și confirmat cu BA-ul înaintea codului.**

- **[Given/When/Then](https://martinfowler.com/bliki/GivenWhenThen.html) = Arrange/Act/Assert**; sub Gherkin rulează orice: Selenium, Java, Playwright ([exemplu .feature](https://github.com/victorrentea/unit-testing/blob/master/src/test/resources/features/record-search.feature)).
- [Gherkin](https://cucumber.io/docs/gherkin/reference/) **adaugă complexitate și risc**: [glue code](https://cucumber.io/docs/cucumber/step-definitions/)-ul care traduce textul în cod poate avea el buguri.
- „Businessul scrie feature files" e **visul eșuat al [BDD](https://dannorth.net/blog/introducing-bdd/)** — cerințele vin pe Word și pe Slack.
- Un [testing DSL](https://github.com/victorrentea/petclinic/blob/main/petclinic-test/src/add-visit.dsl.ts) se citește la fel — **glue-ul nu dispare, el sunt funcțiile din DSL**, exact ce ai fi scris sub Given/When/Then.

## Cod legacy

**Pe cod vechi scrii characterization tests care îngheață comportamentul actual, ca să-l poți atinge.**

- „Dacă un cod stă destul în producție, **nu mai are buguri**" — îl iei de bun și scrii testul peste el.
  - Excepția: bugul devenit feature — aprobarea de credit fără al doilea semnatar.
  - Termenul vine de la Michael Feathers, [*Working Effectively with Legacy Code*](https://www.informit.com/store/working-effectively-with-legacy-code-9780131177055).
- Testul funcțional e mai ușor de citit decât **codul over-engineered** pe care îl descrie.

## Feedback de design

**Șapte mock-uri în jurul unei metode înseamnă șapte dependențe pe care codul ți le ascundea.**

- Behaviorul e un **arbore**: fiecare frunză cere alt setup ca s-o atingi ([CreateCustomerShould.java](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/nested/CreateCustomerShould.java)).

## Teste la API, nu cu mock-uri

**Testerii testează la nivel de API, ca seniorii — nu cu `@Mock`, ca junii.**

- Cele mai valoroase teste sunt **cele lente, de integrare** ([ProductApiEpicITest.java](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/spring/rest/ProductApiEpicITest.java)) — ele reflectă cerințele reale.
- O metodă fără logică nu merită test propriu: o acoperi **trecând prin ea** din testul de API ([ProductService.java](https://github.com/victorrentea/unit-testing/blob/master/src/main/java/victor/testing/spring/service/ProductService.java)).
- Înainte să retestezi, urmează **[call hierarchy](https://www.jetbrains.com/help/idea/viewing-structure-and-hierarchy-of-the-source-code.html)** până la [`@PostMapping`](https://github.com/victorrentea/unit-testing/blob/master/src/main/java/victor/testing/spring/rest/ProductApi.java) — acolo sunt ecranele de retestat.
  - Graful nu-ți spune însă cine **citește gunoiul persistat** în bază după flow-ul tău.

![Testing on the Toilet — Prefer Testing Public APIs Over Implementation-Detail Classes](https://lh3.googleusercontent.com/d/1OYYspplIHoe3F8GmOpp9X_dHIWUBnXlu)

- `UserInfoValidator` n-are nevoie de teste proprii: e **implementation detail**, se acoperă prin `UserInfoService`.
- Arhiva completă [Testing on the Toilet](https://testing.googleblog.com/search/label/TotT) — episoadele lipite pe ușa de la Google.

## Viața unui QA + 🤖 = ❤️

**Citești test planul cu voce tare, dai MP3-ul agentului, iar el dă click în browser cu Playwright.**

- Explici agentului ca unui junior nou-venit **ce apeși și de ce**, zece minute de audio.
- [Playwright test agents](https://playwright.dev/docs/test-agents) — pentru Selenium, ceri agentului să le **porteze**.
- Devul devine **gatekeeper**: semnezi ce intră în prod, chiar dacă nu tu ai scris codul.
- Nu retraversa flow-ul de la zero: salvezi un **dump de bază** în punctul stabil și-l restaurezi.
  - Netrivial: mai multe dump-uri, câte unul per punct din flux, nu unul singur.

## Ce model, la ce cost

**Abonamentele AI sunt masiv subvenționate: 100 € plătiți, 8000 $ arși în 30 de zile.**

- Subvenția de 50-70x de pe [planurile pro/max](https://claude.com/pricing) cere **sub ~150 de angajați** în firmă.
- Costul inferenței scade **2-10x pe an** — ce e scump azi e ieftin la anul.
- [Copilot în IntelliJ](https://plugins.jetbrains.com/plugin/17718-github-copilot) e **„copilul nimănui"**: IntelliJ e JetBrains, Copilot e Microsoft, nimeni n-o repară.
- [Copilot CLI](https://github.com/github/copilot-cli) în terminal e **cel mai bun harness accesibil**: [`npm install -g @github/copilot`](https://docs.github.com/copilot/how-tos/set-up/install-copilot-cli).
- [Model după task](https://platform.claude.com/docs/en/about-claude/models/choosing-a-model), **niciodată sub Sonnet** la scris cod: Haiku la prostii banale, Opus la arhitectură, plan și review.
  - Opus pe testul generat live a fost risipă curată.

## Bug reprodus cu Playwright

**Test-driven bug fixing: reproduci, scrii testul roșu, repari, verde — și agentul poate face tot lanțul.**

- Promptul real: du-te la [issue-ul #40](https://github.com/victorrentea/petclinic/issues/40), reprodu-l în browser cu **Playwright**, scrie [testul](https://github.com/victorrentea/petclinic/blob/main/petclinic-test/src/visit-date-range.feature), repară, nu comite nimic.
- Dovada cerută: **video stil TikTok** cu subtitrări și voce sintetică, bugul reprodus și apoi fixat.

## Coverage

**Coverage măsoară linii executate, nu comportament verificat — pus ca țintă, produce exact testele pe care nu le vrei.**

![Slide 75 — Testing for Coverage](https://lh3.googleusercontent.com/d/1YTKGd9TEWjvof7pEc209Tjy4liwd0aa4)

- Galben în raport = **branch parțial acoperit**: o ramură a `if`-ului e executată, cealaltă nu.
- God Service, 1200 de linii, 87% coverage, 200 de `when/then/return` și **zero assert**.
- **[Branch coverage](https://www.jacoco.org/jacoco/trunk/doc/counters.html)** cere ambele ramuri: o condiție `a && b && c` are nevoie de minim 4 teste.
- Profesionistul citește raportul ca să **vâneze ce nu e acoperit**, nu ca să se bucure de verde.
- Un test poate fi verde fără să atingă linia testată: **altă linie arunca** excepția așteptată.

Demo live: cu `if`-urile reordonate, verificarea `city.length() < 3` ajunge după `streetName == null` — toate testele rămân verzi, dar ultima linie nu se execută niciodată (gutter roșu).

![Coverage: linia rămasă neatinsă după reordonarea if-urilor](https://lh3.googleusercontent.com/d/1B_HOMjBrSTfH2J-2l4_uYmcv8VKFXGj0)
- Se măsoară instrumentând codul de producție: un **[Java agent](https://www.jacoco.org/jacoco/trunk/doc/agent.html)** se mufează în JVM, ca un debugger pe fiecare linie.
- „Pe viitor nu mai testa o clasă fără coverage" → regula intră în **[`AGENTS.md`](https://agents.md/)**.

## AssertJ

**Înlocuirea tuturor `assertEquals` cu [AssertJ](https://assertj.github.io/doc/) în tot proiectul l-a costat un dolar de credite.**

- Ghost text-ul din IDE citește **doar tab-ul curent**: primele rânduri le corectezi tu, restul se aliniază după ele.
- `assertTrue(list.contains(400))` pică cu „expected true but was false"; `assertThat(list).contains(400)` **afișează lista reală** ([AssertJ.java](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/assertThat/AssertJ.java)).

## OpenRewrite

**Migrarea în masă se face cu o rețetă [OpenRewrite](https://docs.openrewrite.org/) peste AST, nu cu agentul rescriind fișier cu fișier.**

- Agentul doar rulează [`rewrite.yml`](https://github.com/victorrentea/unit-testing/blob/master/rewrite.yml), nu atinge nicio linie: **zero tokeni** pe schimbarea propriu-zisă.
- AST: în `1 + 2 * 3` înmulțirea e **copilul plusului**, iar rețeta umblă programatic pe arbore.

## Mock-urile mint

**Mock-urile sunt fragile și mint: testezi A fără B, iar lipiciul dintre ele se rupe nevăzut.**

![Slide 134 — Solitary vs Sociable](https://lh3.googleusercontent.com/d/1e9bsyx9_PbP_vKTROretXAvKRfE_Al2_)

- Între două microservicii **nu pui [wiremock](https://wiremock.org/)**: dacă spargi B în două, testul solitary rămâne verde mincinos ([SafetyApiClient.java](https://github.com/victorrentea/unit-testing/blob/master/src/main/java/victor/testing/spring/infra/SafetyApiClient.java)).
- Ștergi o regulă din codul de producție și testele rămân verzi → regula aia **nu era acoperită**.
- JUnit creează o **instanță nouă** a clasei de test pentru fiecare test, iar ordinea e amestecată intenționat.

## Piramida testării

**[E2E-urile](https://martinfowler.com/articles/practical-test-pyramid.html) își merită locul pe happy path: îți spun că se trece din click în click.**

- Lanțuri de **17–45 de pași** — un pas picat aruncă încrederea în tot ce urmează ([ProductApiEpicITest.java](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/spring/rest/ProductApiEpicITest.java)).
- E greu de gândit mental despre un lanț atât de lung, iar când pică **îl reiei de la zero**.

## Mutation testing cu PIT

**Mutation testing schimbă bytecode-ul și re-rulează testele: mutantul supraviețuitor arată exact ce nu verifici.**

- Ce mutează [PIT](https://pitest.org/): inversează `if`-ul, `<=` devine `<`, șterge apeluri de metodă.
- **Mutant supraviețuitor** = testul trece atunci când tu așteptai să pice. ([CustomerValidatorTest.java](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/mutation/CustomerValidatorTest.java))
- Build-ul urcă la ore, deci se rulează **targetat pe pachete** din [`pom.xml`](https://github.com/victorrentea/unit-testing/blob/master/pom.xml) sau peste noapte.
- [Uncle Bob](https://www.youtube.com/live/zcLPGC-tvgk): mutation testing revine fiindcă **nu tu mai scrii codul** — dă-i agentului raportul.

## Sample by risk

**Testarea exhaustivă e imposibilă: eșantionezi după risc, iar verdele nu dovedește absența bugurilor.**

![Slide 4 — Terms](https://lh3.googleusercontent.com/d/1U0ze3ljPe93LhqNJMTRccHkekZ8FTdP6)

- **[Pesticide paradox](https://astqb.org/istqb-foundation-level-seven-testing-principles/)**: aceleași teste nu mai prind buguri noi — schimbă unghiul.
- **Defect clustering**: 80% din buguri stau în 20% din cod, deci testezi tot colțul ăla.
- 100% mutation coverage merită doar dacă o eroare costă **o misiune spațială** (vezi ❌ de mai jos).
- **Right build ≠ right product**: produs impecabil testat, idee de business moartă.

## Test data builder

**Datele de test construite cinstit o dată, într-un singur loc, ajung refolosite de toată echipa.**

- Fișierul [`TestData`](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/mutation/TestData.java) care „construiește universul" a ajuns la **2500 de linii** la un client.
- Fixture-uri canonice pe disc în loc de `set/set/set`: **YAML bate JSON**, fără ghilimele și acolade. ([customer.json](https://github.com/victorrentea/unit-testing/blob/master/src/test/resources/canonical/customer.json))
- Dă persona-elor nume și devin limbaj comun — echipa zice „**John**", nu „base customer".
- [`lombok.accessors.chain=true`](https://projectlombok.org/features/experimental/Accessors) face fiecare setter să întoarcă **`this`**, deci se înlănțuie fără builder. ([lombok.config](https://github.com/victorrentea/unit-testing/blob/master/lombok.config))
- Pattern-ul se cheamă [Object Mother](https://martinfowler.com/bliki/ObjectMother.html).

## Teste parametrizate

**Pune agentul să-și construiască întâi instrumentul de verificare, apoi lasă-l să itereze pe cifre.**

- Promptul câștigător: scoate raportul de coverage, **vezi tu singur linia neacoperită**, repară testul, fără să atingi codul de producție. ([CustomerValidatorParameterizedTest.java](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/mutation/CustomerValidatorParameterizedTest.java))
- „Nu te arunca să repari și să zici că e gata" — **schelăria de validare** vine prima.
- Ceas vectorial redesenat în **~40 de încercări**, cu un tool de diff scris de el care marca roșu diferențele.

## Buget și unelte

**Cât merită să coste o sesiune depinde de cât cod legacy are agentul de vânturat.**

- Sesiuni de **zeci de dolari** sunt normale când rup o problemă; una a ajuns la 20 $.
- Pragul: un run de **1M tokeni**.
- **[Agents View](https://agentsview.io/)** din VS Code: editor construit în jurul agenților, cu control pe skill-uri și MCP-uri.

## Teste `@Nested`

**Clasele `@Nested` produc un arbore de test citibil de business, cu fixture moștenit automat.**

- `@BeforeEach` din clasa nested rulează **după** cel din clasa-gazdă — [fixture ierarhic](https://docs.junit.org/current/writing-tests/nested-tests.html), fără base class.
- Un **[DisplayNameGenerator](https://docs.junit.org/current/writing-tests/display-names.html)** propriu sparge camelCase-ul: `createCustomerShould` → „for a valid customer, send a welcome email". ([CreateCustomerShould.java](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/nested/CreateCustomerShould.java))

## Dictare și second brain

**Dictarea nativă e gratis pe orice laptop și pornește tot fluxul de testare cu voce.**

- Mac: System Settings → **[Dictation](https://support.apple.com/guide/mac-help/dictate-messages-and-documents-mh40584/mac)**, dublu-tap pe globe; Windows: [`Win+H`](https://support.microsoft.com/en-us/windows/use-voice-typing-to-talk-instead-of-type-on-your-pc-fec94565-c4bd-329d-e59a-af033fa5689f).

## Agentul verificabil

**Agentul nu e gata când zice el, ci când instrumentul pe care l-a construit arată verde.**

- Orice text pe care agentul îl citește **[devine instrucțiune](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)** — tichet, clipboard, pagină web.
- Nu [aproba](https://code.claude.com/docs/en/security) niciodată „șterg ceva de pe disc?".

## Stil imitat

**Agentul imită ce vede: un test-exemplu bine structurat bate orice prompt lung de reguli.**

- Arată-i structura pe care s-o copieze: [Page Object](https://playwright.dev/docs/pom) peste design system, DSL citibil de business, [nume cu underscore](https://docs.junit.org/current/writing-tests/display-names.html).

## Playwright vs Selenium

**Playwright [așteaptă singur](https://playwright.dev/docs/actionability) elementul vizibil, clickabil și neacoperit; în Selenium scrii tu [fiecare wait](https://www.selenium.dev/documentation/webdriver/waits/).**

- Feedback-ul pe E2E se cumpără cu **infrastructură**, nu cu răbdare.
  - suită de 100 de minute spartă în [joburi paralele](https://playwright.dev/docs/test-sharding) pe 120 de VM-uri

## 🔗 Links

**De ce testăm**

- [Uncle Bob on TDD](https://www.youtube.com/watch?v=GvAzrC6-spQ) — clasicul despre de ce testul vine înaintea codului.
- [Venkat Subramaniam, live](https://www.youtube.com/watch?v=0hQvWIdwnw4) — speaker-ul de la care vine citatul cu manual testing degradant.
- [Issue #25 — pagination pe Owners](https://github.com/victorrentea/petclinic/issues/25) — cerința cu titlu și corp, arătată ca „jira bună".
- [Issue #40 — visit date fără validare](https://github.com/victorrentea/petclinic/issues/40) — bugul reprodus live în browser.

**Agenți, modele, costuri**

- [Is AI Profitable Yet?](https://isaiprofitable.com/) — cine cât arde din subvenția pe care rulăm azi.
- [Claude — planuri și prețuri](https://claude.com/pricing) — pragurile pro/max de unde vine multiplicatorul.
- [GitHub Copilot — planuri și prețuri](https://github.com/features/copilot/plans) — comparația de la care a pornit discuția de buget.
- [Installing GitHub Copilot CLI](https://docs.github.com/copilot/how-tos/set-up/install-copilot-cli) — pașii încercați în sală, blocați de certificat.

**Unelte de migrare și verificare**

- [AssertJ recipe (OpenRewrite)](https://docs.openrewrite.org/recipes/java/testing/assertj) — migrarea automată `assert*` → AssertJ, fără tokeni.
- [PIT Mutation Testing](https://pitest.org/) — framework-ul din raportul de mutanți supraviețuitori.
- [Uncle Bob — Software Fundamentals in the Age of AI](https://www.youtube.com/live/zcLPGC-tvgk) — interviul cu „mutation testing revine".
- [/human-review — snapshots publicate](https://victorrentea.github.io/human-review) — pagina de review demonstrată live.

**Comunitate**

- [European Software Crafters](https://victorrentea.ro/community) — comunitatea lunară, gratuită.
- [Gray Factory: Code Review for Agentic Engineers (16 sep)](https://www.meetup.com/european-software-crafters/events/316388730) — următoarea întâlnire.
- [Agentic Engineering — masterclass](https://agentic.how/) — cursul de agentic la care s-a tot referit.

## 🎯 Main takeaways

- **Testele există ca să țipe ele mâine, nu userul — siguranța de mâine e motivul #1.**
- **Coverage măsoară linii executate, nu comportament verificat — pus ca țintă, produce exact testele pe care nu le vrei.**
- **Mock-urile sunt fragile și mint: testezi A fără B, iar lipiciul dintre ele se rupe nevăzut.**
- **Mutation testing schimbă bytecode-ul și re-rulează testele: mutantul supraviețuitor arată exact ce nu verifici.**
- **Testarea exhaustivă e imposibilă: eșantionezi după risc, iar verdele nu dovedește absența bugurilor.**
- **Pune agentul să-și construiască întâi instrumentul de verificare, apoi lasă-l să itereze pe cifre.**

## Corecturi ❌

- Notă: „~45x mai eficient pe tokeni" n-are sursă proprie, dar [NVIDIA anunță până la 50x throughput/MW](https://developer.nvidia.com/blog/scaling-token-factory-revenue-and-ai-efficiency-by-maximizing-performance-per-watt/) Blackwell vs Hopper.
- ❌ Modul „auto" din Copilot **nu** dă ~30% reducere: selecția automată se facturează la [0.9x](https://docs.github.com/en/copilot/reference/copilot-billing/request-based-billing-legacy/model-multipliers-for-annual-plans), adică ~10%.
- ❌ [`assertThrows`](https://docs.junit.org/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Assertions.html) nu e „interzis prin lege" — e idiomatic în JUnit 5; ținta reală sunt `assertTrue`/`assertEquals` din `org.junit.Assert`.
- ❌ Object Mother nu e pattern-ul lui Fowler: e descris de [Schuh & Punke](https://martinfowler.com/bliki/ObjectMother.html) (ThoughtWorks, XP2001); Fowler are doar bliki-ul.
- ❌ CERN operează [acceleratoare](https://home.cern/about), nu reactoare — anecdota clasică de coverage extrem e cea a [shuttle-ului NASA](http://www1.udel.edu/CIS/106h/16F/resources/theyWriteTheRightStuffNasa.html): 420.000 de linii, o eroare.

# 📅 Ziua 2 — 11 septembrie 2026

## Agentul ca tester de date

**Cere agentului să verifice ieșirile față de formulele din ticket, nu față de codul care le-a produs.**

- Story-urile din Jira și documentația `.docx` se dau **ca fișiere în folder** — le parsează fără probleme.
- Dă-i URL-ul repo-ului să-l cloneze în temp **doar ca să vadă unde aterizează datele de ieșire**.
- Începe cu **un singur data record** intrare→ieșire, cu explicația pașilor, ca să-i poți ajusta flow-ul.
- Conectează-l la bază cu un **user read-only**, niciodată cu admin-ul de prod.

## Pași mici

**Prima încercare eșuează garantat — cere pași mici și dă-i un al doilea prompt, nu un restart.**

- Când [Copilotul din VS Code](https://code.visualstudio.com/docs/copilot/overview) îngheață, **stop și repromptare**; sub 10 secunde nu e blocaj.
- SSH-ul e blocat în bancă: clonezi pe HTTPS cu **[PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)**, sau descarci zip-ul.

## Browserul potrivit

**Playwright pornește un profil de browser nou și nelogat, deci pe intranet trebuie legat de profilul tău principal.**

- Fără MCP aprobat în organizație, browserul se manevrează doar prin **[Playwright CLI](https://playwright.dev/docs/test-cli) din terminal**.
- Legat de [Edge-ul tău](https://playwright.dev/docs/browsers#google-chrome--microsoft-edge), agentul **moștenește toate sesiunile tale logate**, inclusiv mailul personal.
- Profilul efemer al lui Playwright devine persistent cu **[`--user-data-dir`](https://github.com/microsoft/playwright-mcp)**, deci te loghezi o singură dată.
- SSO-ul cere browserul **închis complet**, altfel profilul nou nu preia login-ul.

## Lecțiile devin skill-uri

**Orice chin al agentului cu mediul devine un skill împins în marketplace-ul intern de plugin-uri.**

- După ce s-a chinuit cu profilul Edge: **extrage lecțiile** în skill-ul de browser și dă push pe git.
- [`description`](https://code.claude.com/docs/en/skills)-ul e **reclama skill-ului** către agent — el decide singur când să-l activeze.
  - dacă nu se auto-activează, cheamă-l manual cu `/nume-skill`
- Cunoștințele comune merg în skill-ul proiectului, restul într-un **skill global în home**.

## Marketplace de plugin-uri

**Playwright livrează agenți gata făcuți — planner, generator, healer — care scot test planul dintr-un tichet.**

- Pui agentul să-i ia din [docs Playwright](https://playwright.dev/docs/test-agents) și să-i împingă în **[marketplace-ul intern](https://code.claude.com/docs/en/plugin-marketplaces)**.

## Opus sau Sonnet

**Opus e arhitectul cu 40 de ani de experiență; Sonnet, colegul de șapte ani care are nevoie de ghidaj.**

- Pentru o zi de lab, **[Sonnet](https://code.claude.com/docs/en/costs#choose-the-right-model) cu ~200k de context** e suficient.

## Plan mode

**În [plan mode](https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode) îi poți da voie la orice: propune, nu execută.**

- **Rotița galbenă** = [context aproape plin](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents); de acolo agentul o ia razna, semn că taskul era prea mare.
- Modelul [îți dă ce vrei să auzi](https://arxiv.org/abs/2310.13548) și te închide într-un **echo chamber**, ca short-urile de YouTube.

## Secrete și acces

**Agentul ajunge la baza de date printr-un skill dedicat, niciodată cu userul tău personal.**

- Skill-ul de **DB CLI** din marketplace primește URL, bază, user, parolă și explorează schema singur.
- Pe termen lung, cere DBA-ului un **[user tehnic separat](https://code.claude.com/docs/en/mcp)** pentru agent, nu credențialele tale de dev.

## Test manual din Jira

**Dai agentului tichetul, URL-ul aplicației și un test similar ca exemplu, apoi îl lași să dea click.**

- Cere-i ca model **ultimul feature scris de tester**: în el sunt adunate toate lecțiile de până acum.
- Reproducând manual pașii din Jira în browser, agentul **a găsit un bug real**, confirmat pe două request-uri.
- Prompt de închidere: comentează bugul în [Jira](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-comments/), continuă automatizarea **chiar dacă testul pică**, apoi deschide-mi tichetul.
- Trei agenți în paralel, fiecare pe altă pistă: **skill-uri, click-uri în browser, baza de date**.

## Arheologie pe build-uri

**Fără MCP de Jenkins, împachetezi [REST API-ul](https://www.jenkins.io/doc/book/using/remote-access-api/) într-un [skill](https://code.claude.com/docs/en/skills) și lași agentul să repare pipeline-ul în buclă.**

- „Ce pică des în ultima lună?" peste istoria de build-uri — primul prompt pe orice **CI necunoscut**.
- „Repară testele din pipe, nu te opri până nu e verde" — agentul are **bucla de feedback** completă: loguri, timpi, retry.
- [Jenkins CLI](https://www.jenkins.io/doc/book/managing/cli/) e un jar; mai simplu `curl` + `jq` + basic auth într-un script.

## Unelte condiționate

**Controlezi ce unelte poate folosi agentul la fiecare pas, în funcție de starea la care a ajuns.**

- [Descrierea](https://platform.claude.com/docs/en/agents-and-tools/tool-use/define-tools) fiecărui tool spune și **când NU se folosește**, altfel agentul le încurcă între ele ([PetClinicMcp.java](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/main/java/victor/training/petclinic/mcp/PetClinicMcp.java)).
  - scrisă ca pentru tine peste 3-4 luni
- Întâi îi dai două unelte; după ce a ales ciocanul, îi expui **o singură opțiune**.

## Cere dovada

**Nu crede agentul pe cuvânt că s-a integrat — pune-l să demonstreze cu date reale, înainte de submit.**

- „Ia ultimele 20 de build status-uri, o bucată de log și ultimele 10 teste picate" — **înainte** de publicat plugin-ul.
- [`gh auth status`](https://cli.github.com/manual/gh_auth_status) — dovada ieftină că e logat.
- Trecând prin context ca să demonstreze, agentul **se corectează singur** pe drum.

## DSL de test

**Un test scris în limbajul aplicației se citește de sus în jos, și de către business.**

- Componentele de bază se definesc după **cum interacționezi** cu ele (clickable, selectable), nu după ce sunt.
  - traits = interfețe Java cu [metode default](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html), combinate pe componentă
  - Lombok `@Getter` + [`fluent=true`](https://projectlombok.org/features/experimental/Accessors) în `lombok.config` scot prefixul `get` din DSL
- Jumătate din framework scris de mână, restul **generat** de Copilot citind DOM-ul aplicației.
- [Locatori Playwright](https://playwright.dev/docs/locators) pe **reguli de accesibilitate**, nu XPath — mai stabili, dar trebuie să fie unici pe pagină.
  - două input-uri `password` pe aceeași pagină = [locator ambiguu](https://playwright.dev/docs/locators#strictness)
  - în Playwright: `getByRole` / `getByLabel`, nu `page.locator('//…')`

## Trace viewer-ul din Playwright

**Trace-ul Playwright dă film, network și consolă pe fiecare pas — de aia nu te mai întorci la Selenium.**

- Componente UI **standardizate** în aplicație: [un locator scris o dată](https://playwright.dev/docs/pom), nu repetat în fiecare test.
- Raportul reține doar testele care pică de **3 ori la rând** — întâi screenshot, [trace-ul mare](https://playwright.dev/docs/trace-viewer) abia după.

## Teste pe bază de date

**Un dump de bază înlocuiește 40 de click-uri de setup — dar numai dacă știi ce compari.**

- [Paralelizezi](https://playwright.dev/docs/test-parallel) pe **date disjuncte**: stivă de useri partajată între thread-uri, câte un browser per aplicație.
  - optimul de thread-uri se află empiric, nu dintr-o formulă
- Ca să compari baza nouă cu cea salvată, **mascezi ce se schimbă la fiecare rulare**: timestamp-uri, UUID-uri.
  - reset de sequence = adio paralelism pe schema aia
- [Profilerul din IntelliJ](https://www.jetbrains.com/help/idea/profiler-intro.html) îți arată **care pas de configurare mănâncă timpul** — acolo merită dump-ul.
- [Predictive Test Selection](https://docs.gradle.com/develocity/predictive-test-selection/) învață cu ML **ce teste să ruleze primele**, ca suita să crape devreme.
- Smoke test pe fluxul core, ca **poartă**: dacă pică, regresia nu mai are rost.

## QA, dev, ops

**Te-ai agentificat în ziua în care îi ceri agentului „demonstrează-mi", în loc să-i dictezi pașii.**

- Un **proiect demo** cu componentele și infra de bază, trimis pe mail — agentul colegului scrie restul citind DOM-ul.
- Cere-i agentului **un handover doc** peste proiectul demo: deduce singur bunele practici din cod.
- Magia e când **QA devine dev** și devul devine QA — agentul tău pornește agentul colegului prin terminal.

## Diff pe DOM

**Când se schimbă UI-ul, pune agentul să facă diff pe DOM, nu să caute cu ochiul.**

- Diff pe **[accessibility tree](https://playwright.dev/docs/aria-snapshots)** între cele două aplicații pornite simultan.
- Proactiv: **la orice element nou** pe ecran, agentul generează locatorul lipsă.

## Token maxxing

**Bugetul de tokeni se arde în luni, nu în ani — învață pe banii tăi.**

- Nu există 10x: măsurat, e **[1.3x productivitate](https://www.microsoft.com/en-us/research/publication/the-effects-of-generative-ai-on-high-skilled-work-evidence-from-three-field-experiments-with-software-developers/)**, dar cu zero defecte noi.

## Stub-uri externe

**Sistemul extern se stubuiește: imagine reală, trafic înregistrat, sau reimplementare cu stare.**

- [Testcontainers](https://testcontainers.com/) rulează **exact imaginea de prod**, cu patch-urile și flag-urile ei.
  - merge și peste [Rancher sau Colima](https://java.testcontainers.org/supported_docker_environment/), nu doar Docker
- [WireMock Recorder](https://wiremock.org/docs/record-playback/) proxiază o dată sistemul real, apoi testele dau **replay** ([safetyApi-safe.json](https://github.com/victorrentea/unit-testing/blob/master/src/test/resources/mappings/safetyApi-safe.json)).
  - o cerere reală pe săptămână, ca să prinzi schimbările de contract
- Interacțiunile **stateful** cer stub propriu: hash map plus un endpoint `__reset` între teste.

## Trusa QA

**Agentul scrie singur DSL-uri de API și de load; tu îi dai mediul și sandbox-ul.**

- Alege frameworkul de API după **lock-in**, nu după popularitate: [RestAssured](https://rest-assured.io/), [Karate](https://github.com/karatelabs/karate), [Bruno](https://www.usebruno.com/), sau [`.http`](https://www.jetbrains.com/help/idea/http-client-in-product-code-editor.html) din IntelliJ.
- Load: [JMeter](https://jmeter.apache.org/), [Gatling](https://gatling.io/), [K6](https://k6.io/), [Locust](https://locust.io/).
- Nu te crampona de limbaj: **Kotlin sau TypeScript au mai multă valoare pe caracter**.

## Trace ID

**Un trace ID plimbat din click până în ultimul microserviciu face din test o radiografie.**

- Testul generează **sequence diagram** din trace-ul real: browser, backend, bază ([AddVisitSequenceTest.java](https://github.com/victorrentea/petclinic/blob/test-pr/petclinic-backend/src/test/java/victor/training/petclinic/rest/AddVisitSequenceTest.java)).
- Dacă lanțul se rupe, cineva nu pasează [header-ul](https://www.w3.org/TR/trace-context/) — **testarea observabilității**, direct tichet.

## Fagure, nu piramidă

**Piramida a devenit [fagure](https://engineering.atspotify.com/2018/01/testing-of-microservices): integration-urile în mijloc, unit-urile doar unde logica e prea grea.**

- E2E puține, fiindcă-s scumpe: **142 de teste rulau o oră**.

## Chaos engineering

**Strici tu producția, controlat, înainte s-o strice realitatea.**

- Booking.com injectează erori cu un sidecar: **0.5% din erorile de prod sunt ale lor**.
  - [Chaos Monkey](https://netflix.github.io/chaosmonkey/); [ToxiProxy](https://github.com/Shopify/toxiproxy) — proxy în Docker care taie pachete

## Evaluri pe agenți

**Un agent nu-l testezi o dată: îl rulezi repetat, scriptezi ce poți, restul îl judecă alt LLM.**

- Ce se poate verifica **programatic** verifici scriptat — LLM-judge rămâne doar pentru textul liber.
  - framework-uri de evals: [promptfoo](https://promptfoo.dev), [DeepEval](https://deepeval.com/docs/getting-started), [Langfuse](https://langfuse.com/docs/evaluation/overview)
- Scriptează **90% din mailuri ca template-uri** și lasă agentul liber doar pe corner case-uri.
- Evalurile **ard tokeni la fiecare build** — pune un plafon de cost pe orice buclă lăsată nesupravegheată.
- Mailuri generate și trimise necitite = **lipsă de respect**.

## Memorie centrală

**Un [memory](https://code.claude.com/docs/en/memory) menținut central bate zece agenți care redescoperă Jira și Confluence la fiecare task.**

- O firmă a raportat **scădere dramatică de tokeni** și calitate mai bună citind memoria, nu Confluence.
- Un job face update la memorie din Confluence, cod și **diff-uri** — proactiv, nu la fiecare task.

## Proiect toxic

**Într-un proiect toxic raportezi, pistonezi trei-șase luni, apoi pleci — nu te reformezi tu după el.**

- Waterfall care mănâncă 3 luni din 4: **betonezi suita de teste în avans**, cu agenți.
  - cere acces la mediul de demo din timpul dezvoltării, nu la final
- Sabotajul e real: un coleg frâna proiectul de **frica restructurării**.

## 🔗 Links

**Agenți gata făcuți**

- [Playwright test agents](https://playwright.dev/docs/test-agents) — planner, generator, healer; de portat pe Selenium.
- [Jenkins CLI](https://www.jenkins.io/doc/book/managing/cli/) — alternativa „oficială" la scriptul cu `curl` + `jq`.

**Infrastructură de suită**

- [Predictive Test Selection](https://docs.gradle.com/develocity/predictive-test-selection/) — ordonarea testelor cu ML, ca suita să crape devreme.

**Skill-uri și marketplace**

- [Agent Skills](https://code.claude.com/docs/en/skills) — `description`-ul e ce citește agentul ca să se auto-activeze; skill personal în `~/.claude/skills`.
- [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) — cum distribui skill-uri și agenți echipei, dintr-un repo git.
- [MCP în Claude Code](https://code.claude.com/docs/en/mcp) — exemplu de conectare la Postgres cu DSN și user read-only.

## 🎯 Main takeaways

- **Cere agentului să verifice ieșirile față de formulele din ticket, nu față de codul care le-a produs.**
- **Prima încercare eșuează garantat — cere pași mici și dă-i un al doilea prompt, nu un restart.**
- **Orice chin al agentului cu mediul devine un skill împins în marketplace-ul intern de plugin-uri.**
- **Nu crede agentul pe cuvânt că s-a integrat — pune-l să demonstreze cu date reale, înainte de submit.**
- **Un test scris în limbajul aplicației se citește de sus în jos, și de către business.**
- **Un dump de bază înlocuiește 40 de click-uri de setup — dar numai dacă știi ce compari.**
- **Te-ai agentificat în ziua în care îi ceri agentului „demonstrează-mi", în loc să-i dictezi pașii.**
- **Piramida a devenit fagure: integration-urile în mijloc, unit-urile doar unde logica e prea grea.**

## Corecturi ❌

- Notă: Postman are în continuare un plan **[Free](https://www.postman.com/pricing/)** (cu limite de colaborare/rulări) — nu a devenit plătit integral.
- Notă: portul în Rust al Claude Code („CRAFT”, în 3 ore) nu e verificabil; documentată e doar expunerea sursei prin [sourcemaps](https://www.theregister.com/2026/03/31/anthropic_claude_code_source_code/).
- Notă: raportul output = 5× input e valabil la [modelele Anthropic](https://claude.com/pricing), nu e o regulă universală între furnizori.
- Notă: overhead-ul de pornire al unui CLI agentic e de regulă ~10–25k tokeni; 40–80k doar cu multe [MCP-uri](https://code.claude.com/docs/en/costs) și skill-uri.
- Notă: „Predictive Test Selection" nu e în Gradle open-source, ci în [Develocity](https://docs.gradle.com/develocity/predictive-test-selection/) — produs comercial, cu licență.
- Notă: povestea e experimentul [bixonimania](https://www.smithsonianmag.com/smart-news/scientists-invented-a-disease-to-test-whether-ai-knew-it-was-fake-then-chatbots-started-saying-it-was-real-180988924/) — boală inventată, descrisă ca reală de Copilot, Gemini și ChatGPT. Cifra de ~50.000 nu apare în surse.
- Notă: [Playwright MCP](https://github.com/microsoft/playwright-mcp) nu pornește headless implicit, dar [`playwright test`](https://playwright.dev/docs/test-cli) da — comportamentul văzut în sală venea din scriptul skill-ului, nu din tool-ul upstream.

## 📚 Wiki-ul sesiunii

Materialele sesiunii includ `wiki.zip` — un vault [Obsidian](https://obsidian.md) mic, cu notițe scurte, cross-linkate, câte una per idee discutată la curs. Descarcă-l în `~/Downloads`, apoi lipește asta într-un Terminal pe macOS:

```bash
ls -d /Applications/Obsidian.app >/dev/null 2>&1 || brew install --cask obsidian
Z=$(ls -t ~/Downloads/wiki.zip ./wiki.zip 2>/dev/null | head -1); [ -n "$Z" ] || { echo "pune întâi wiki.zip în ~/Downloads"; exit 1; }
rm -rf ~/Documents/testing-wiki && mkdir -p ~/Documents/testing-wiki && unzip -qo "$Z" -d ~/Documents/testing-wiki
V=~/Documents/testing-wiki/wiki
python3 -c 'import json,os,sys,time,pathlib; v=os.path.abspath(sys.argv[1]); p=pathlib.Path.home()/"Library/Application Support/obsidian/obsidian.json"; d=json.loads(p.read_text()) if p.exists() else {}; vs=d.setdefault("vaults",{}); [x.pop("open",None) for x in vs.values()]; k=next((kk for kk,x in vs.items() if os.path.abspath(x.get("path",""))==v),None) or format(int(time.time()*1000),"016x"); vs.setdefault(k,{"path":v,"ts":int(time.time()*1000)})["open"]=True; p.parent.mkdir(parents=True,exist_ok=True); p.write_text(json.dumps(d))' "$V"
open "obsidian://open?path=$(python3 -c 'import urllib.parse,sys;print(urllib.parse.quote(sys.argv[1],safe=""))' "$V/Home.md")"
```

Pornește din **Home** și urmărește `[[link]]`-urile. Pe Windows sau Linux: dezarhivează `wiki.zip` oriunde și deschide folderul `wiki` ca vault în Obsidian („Open folder as vault").
