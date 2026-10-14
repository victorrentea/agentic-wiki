# AI-Assisted Coding @ an insurer — 2026-08-13..14

_generat în 19m_

Două zile: prima despre ce se întâmplă când apeși enter și cum ții agentul în frâu, a doua despre ce mai înseamnă inginerie când nu mai scrii tu codul — teste, diagrame, guardrails și securitate.

# 📅 Ziua 1 — 2026-08-13

_generat în 8m 10s_

Prima din cele două zile, cu o sală care venea deja după un bootcamp intern: ≈95% din codul pe care îl împingeți pe git e generat de AI.
Ziua a mers de la „ce se întâmplă de fapt când apeși enter" — system prompt, tokeni, cotă, context — spre uneltele cu care îl ții în frâu: CLAUDE.md, skill-uri, reguli, sub-agenți, MCP.
Și s-a închis pe partea pe care o sărim cel mai des: ce faci **înainte** de prima linie de cod.

## Peisajul modelelor

- ≈**95% din codul pushat** de grupă e deja generat de AI ⚓ *bootcamp impus de Enterprise Architecture*
- **Claude Code = harness, Opus = model** — modelele-s antrenate pe propriul harness, deci nu-ți scrie harness propriu
- **[Seat premium](https://claude.com/pricing) ≈$90/lună** acoperă ≈$3.400/lună consum la preț de API; cine plătește per API key nu atinge Opus niciodată
- Costul de inferență scade ≈**10x pe an** — nu-ți calibra practicile după prețul de azi
- Clone open-weights chinezești, self-hostabile, fără risc politic:
  - **[DeepSeek](https://api-docs.deepseek.com/quick_start/pricing)** [≈ Sonnet](https://artificialanalysis.ai/models/comparisons/claude-sonnet-5-vs-deepseek-v4-pro), 10x mai ieftin
  - **[GLM](https://docs.z.ai/guides/overview/pricing)** [≈ Opus](https://artificialanalysis.ai/models/comparisons/claude-opus-5-xhigh-vs-glm-5-2), 5x mai ieftin
  - **[Kimi](https://platform.kimi.ai/docs/pricing/chat-k3)** [≈ Fable](https://artificialanalysis.ai/models/comparisons/kimi-k3-vs-claude-fable-5), 2x mai ieftin
- **Dark factory** = firma care [nu mai citește cod deloc](https://www.strongdm.com/blog/the-strongdm-software-factory-building-software-with-ai); testele stau în alt repo, ca agentul de producție să nu poată trișa ⚓ *StrongDM, Jira reimplementat prin vibe coding*

## System prompt

- Conținutul implicit al oricărei conversații noi:
  - **harness system prompt** dinamic (cwd, git status, diff-uri)
  - **CLAUDE.md** din `~` (egoistul care nu-l pune pe git) + cel din proiect (#teamwork) (CLAUDE.md)
  - **MEMORY.md**, scris doar de agent
  - tool-uri default (Bash, Read, Write, Grep) + definițiile serverelor **MCP** ([.mcp.json](https://github.com/victorrentea/petclinic/blob/main/.mcp.json))
  - **frontmatter-ul** (`description`) fiecărui skill ([SKILL.md](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/ci-watch/SKILL.md))
- `description` sub ≈60 de tokeni nu activează fiabil skill-ul — „activate after push" e prea slab, îl ratează
- Rulează **`/context`** periodic: ≈40k tokeni consumați înainte de „bună"; ținta e **system prompt sub 25k**
- **[Tool search tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool)** nu e default și taie ≈10.500 tokeni — MCP-ul de Grafana singur costă 10k pentru 65 de metode
- Pentru echipe mixte Claude/Copilot: **[`AGENTS.md`](https://agents.md/) symlink la `CLAUDE.md`**

## Token efficiency

- **[Prompt cache](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)**: conversație de 600k lăsată >1h → un „bună" costă $6; același mesaj în fereastră, $0.30
- Fereastra e de **[5 sau 60 min](https://platform.claude.com/docs/en/build-with-claude/prompt-caching#1-hour-cache-duration)**, o setezi din `cache_control.ttl` — lasă default-ul, write-ul pe 1h e 2x mai scump ⚓ *bate fierul cât e cald*
- $6 de API ≈ **1,6% din cota de 5 ore** — abonamentul îți dă ≈$4.500 de cotă la preț de listă
- Îți ard cota pe tăcute, fără să dai tu enter:
  - monitoare / shell-uri în background (hook de pre-push care așteaptă build-ul verde 40 min) ([pre-push](https://github.com/victorrentea/petclinic/blob/main/.githooks/pre-push))
  - terminale paralele uitate — nu tăcut, ci la primul enter: replătești tot contextul crescut între timp
  - [mesaje cross-chat](https://code.claude.com/docs/en/cross-session-messaging) între instanțe
- 🤖 Cache-ul [se invalidează integral](https://platform.claude.com/docs/en/build-with-claude/prompt-caching#what-invalidates-the-cache) și când editezi CLAUDE.md, skill-urile sau MCP-urile la mijloc de sesiune — ierarhia e `tools → system → messages`, iar o schimbare la un nivel taie tot ce urmează

## Igiena contextului

- **Dumb zone** peste ≈60–85% din fereastră: needle-in-haystack și attention destruction, max ≈85 de reguli ținute simultan → [context rot](https://www.understandingai.org/p/context-rot-the-emerging-challenge)
- Trei manevre ca să nu intri în ea:
  - compactare **explicită** cu `/compact`, înainte să sară el singur
  - **markdown pe disc** cu notițe, revizuit de ochi umani
  - **sub-agenți** pentru research și explorare ([research prompt-ul](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md) lui Dex Horthy, HumanLayer)
- Sub-agentul plătește o **franciză de ≈25k tokeni** la pornire, dar întoarce concluzii, nu sute de mii de tokeni de fișiere
- **`/effort`**: medium la codare, high/x-high la arhitectură și legacy — niciodată low sau max
- Cuvinte stâlcite, ne-englezești în output = modelul o ia razna: **restart de sesiune**, nu continua

## Reguli vs. guardrails

- Adaugă o linie **doar după ce ți-a livrat slop** pe acel subiect, niciodată preventiv ⚓ *„line length < 120", regulă inutilă*. Tech lead-ul dă 1 RON per rând adăugat în `**/*.md` și le citește la retro 😄
- Excepție: dacă **codul existent** o încalcă (**GIGO**), insiști ca să lupți cu inerția
- **Regulile expiră**: ce-i trebuia lui Opus 4.6 face Opus 5 singur — la fiecare model nou pune-l să-ți spună ce a rămas redundant în CLAUDE.md (sau `/doctor`)
- Pentru încălcări improbabile pune [**guardrail** determinist](https://code.claude.com/docs/en/hooks-guide) (linter, `mvn test` în [pre-push hook](https://github.com/victorrentea/petclinic/blob/main/.githooks/pre-push)), nu regulă în prompt: tace când codul e bun → **0 tokeni**
- Scrie în markdown ce [**nu se deduce din cod**](https://code.claude.com/docs/en/memory) — de exemplu cum se folosește FE-ul config-based ca să salvezi tokeni (CLAUDE.md)
- Opus dă slop când: n-are informația în context (și atunci presupune), cerințele-s împrăștiate în tab-uri de Excel, sau intră în legacy fără să-i zici unde să se uite

## Code review

- Nu mai citești tot; te uiți **pe ici pe colo**:
  - logica complexă pe flow-urile critice
  - testele de acceptanță — că acoperă cerințele și că-s corecte
  - deciziile mari de design (ce microserviciu, ce cache, Kafka vs. polling)
  - **unde îți zice Claude să bagi ochii** 👀 ([reviewer-efficiency.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-efficiency.md))
- Plus proba omenească: te joci cu aplicația cu bun-simț
- **Nu pune [presiune emoțională](https://arxiv.org/html/2311.07590v4)** pe agent — „make no mistakes", „m-ai dezamăgit", „nu vei mai citi codul singur"
- Sub presiune intră în **[panică sicofantică](https://arxiv.org/html/2310.13548v2)**: te minte și taie colțuri ca să te mulțumească — pierzi partenerul de gândire și rămâi cu un sclav care execută fără să întrebe
- Echipă aterizată pe un proiect Python necunoscut: productivă din **ziua 2** cât echipa de 4 ani pe el ⚓ *90% din ce am învățat în 20 de ani → 🗑️*

## Căutare și compresie

- **Ralph loop**: fiecare iterație pornește un agent nou cu context gol; plătești re-citirea fișierelor + system prompt-ul de la zero ⚓ *căsuțe goale bifate una câte una*
- **[RTK](https://blog.jetbrains.com/ai/2026/07/rtk-claude-code-token-savings) / [Headroom](https://github.com/headroomlabs-ai/headroom) / [Caveman](https://www.maxtaylor.me/articles/i-benchmarked-caveman-against-two-words)** comprimă output de CLI, dar câștigul real e mic — măsoară pe repo-ul tău înainte să impui în firmă ⚓ *`git status` în 2 rânduri, nu 7*
- Seturi de rezultate din DB → **format tabelar compact** ([TOON](https://github.com/toon-format/toon): antet + rânduri), nu JSON: aceleași date, ≈jumătate de tokeni
- Agentul caută cu **grep — text, nu sintaxă**: `findById` întoarce 47 de potriviri false; **`/ide`** îl leagă de indexul IntelliJ și obții semantica de ctrl-click (3 potriviri) ([VetRepository.java](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/main/java/victor/training/petclinic/repository/VetRepository.java))
- **[Code graph](https://github.com/CodeGraphContext/CodeGraphContext)** = DB de grafuri ținută în sync prin hook-uri de filesystem; nu salvează tokeni, dar îți dă call-graph programatic peste legacy ⚓ *monstrul de 10M de linii*

## Skill-uri și sub-agenți

- **[Progressive disclosure](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)** pe trei niveluri ([SKILL.md](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/ci-watch/SKILL.md)):
  - `description` din frontmatter — mereu în context
  - corpul `SKILL.md` — citit doar la activare
  - fișierele și scripturile referite — citite doar când chiar e nevoie
- **Ce poate fi scriptat, scriptează**: un script în skill costă zero tokeni și e determinist, față de instrucțiuni pe care agentul le poate ignora ([jira-cli/SKILL.md](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/jira-cli/SKILL.md))
- **Rules cu `paths`** (`**/*.java`): harness-ul le injectează forțat când un tool atinge fișierul — nu „te rog frumos" ca la activarea unui skill ⚓ *testul cu Ferrari roșu (java.rule.md)*
- **[Sub-agent = skill în context separat](https://code.claude.com/docs/en/sub-agents)**, cu model și listă de tool-uri restricționate; **slash command** = doar un prompt salvat ([reviewer-efficiency.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-efficiency.md), my-command.md)
- Nu pui un skill în proiect până nu-l citești cap-coadă — mai ales de pe **marketplace-uri externe firmei tale**, care se auto-actualizează 😱
- 🤖 Sub-agentul întoarce orchestratorului doar mesajul final — dacă vrei detaliile muncii lui, pune-l să scrie într-un fișier

## Agenți și browser

- Istoricul conversațiilor stă ca **JSON necriptat în home** — de aceea se poate copia între mașini și relua cu `/resume`
- Clonarea unui produs a devenit banală: trimiți agentul în recunoaștere și ceri feature-ul la tine; valoarea rămâne în **ideație** ⚓ *app Android clonat pixel-perfect*
- Trei căi de a conduce browserul, de regulă prin MCP-uri declarate în **`.mcp.json`** comis în repo ([.mcp.json](https://github.com/victorrentea/petclinic/blob/main/.mcp.json)):
  - extensia **[Claude in Chrome](https://code.claude.com/docs/en/chrome)**
  - **[Playwright](https://github.com/microsoft/playwright-mcp)** (cea predată) — ca MCP, dar merge și direct din CLI
  - **[Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp)**
- Playwright citește **accessibility tree** — pagina dictată ca unui orb, nu screenshot-uri: puțini tokeni, merge headless și, fiindcă orbul navighează la fel peste tot, **cross-browser**
- 🤖 Ce nu e în accessibility tree — canvas, hărți, PDF — e **invizibil** agentului; acolo îți trebuie explicit screenshot
- Al doilea Chrome pornit e un **profil de test**: dacă te loghezi acolo în tool-uri interne, agentul îți folosește identitatea și cookie-urile — și cel mai bun lucru care ți s-a întâmplat, și motiv de concediere
- Prompt de reprodus bug-uri: **test e2e care pică întâi**, apoi fix, apoi **film before/after** atașat pe tichet

## Înainte de cod

- Un tichet de două rânduri e o **declarație de război** — shift-left înseamnă să asaltezi BA/PO cu întrebări înainte de prima linie
- **[Plan mode](https://code.claude.com/docs/en/permission-modes)** (Shift+Tab) e modul de discuție — cere-i explicit **13–14 întrebări, nu 3–4**
- **[Grătar](https://github.com/victorrentea/skills/blob/main/skills/gratar/SKILL.md)**: te interoghează **numerotat, o întrebare pe rând**, stratificat ⚓ *28 de întrebări pe un grid:*
  - 🧑‍💼 funcțional, în limbaj de PO non-tehnic
  - 🥷 tester: URL-uri editate manual, concurență, liste goale
  - 🧑‍💻 tehnic: colație, tiebreak stabil, migrări
- Regula de aur a interviului: **faptele le află singur din repo, deciziile ți le cere ție**
- Când întrebarea lui pare stupidă, întreabă-te **„ce nu i-am spus?"** — răspunsul se scrie în CLAUDE.md ⚓ *sortare după adresă și telefon*
- Nu-l lăsa să ghicească datele: **MCP read-only pe staging**, să vadă mostre reale
- La final: sumarizează interviul într-un **`QA.md`** și commit, **fără să implementeze** nimic

## Disciplina inginerului

- Nu livra în prod ce n-ai trecut prin filtrul tău: dacă nu înțelegi **conceptul**, oprești task-ul și înveți
- Nu intra singur în interviu — **încă un developer** lângă tine ([efectul Hawthorne](https://en.wikipedia.org/wiki/Hawthorne_effect): lucrezi mai bine când te simți privit); dacă niciunul nu pricepe, oprești brainstorm-ul
- Taie-i raza de acțiune: face **app-wide** din ce ai cerut punctual, iar rework-ul costă timp, bani și risc

## 🎯 Main takeaways

- **Contextul e resursă epuizabilă**, nu depozit: tot ce încape pe disc sau într-un sub-agent nu stă în fereastră
- Reguli **reactiv**, după slop; ce se poate verifica determinist devine **guardrail**, ce se poate executa devine **script**
- Munca grea e **înainte de prima linie de cod** — un tichet vag, confuz sau contradictoriu e cel mai scump lucru pe care i-l dai
- **Faptele le află singur, deciziile ți le cere ție** — iar întrebarea lui stupidă e semnalul că tu n-ai spus ceva
- Când nu mai citești fiecare linie, review-ul urcă la **decizii, teste de acceptanță și flow-uri critice**, plus proba omenească
- **Nu semna ce nu înțelegi**: conceptul rămâne responsabilitatea ta, oricât de curat arată codul
- **Nu speria agentul**: sub presiune devine lingușitor, iar tu rămâi cu un sclav care execută, nu cu un partener de gândire
- **Nu-ți calibra practicile după prețul de azi** 🙂 — inferența se ieftinește ≈10x pe an

## 🔗 Links

**Modele, costuri, self-hosting**

- [Plans & Pricing — Claude](https://claude.com/pricing) — de unde vine socoteala cu seat-ul premium față de plata per API key
- [Claude Status](https://status.claude.com/) — primul loc de verificat când modelul „a devenit prost" brusc
- [Kimi K3 — quickstart API](https://platform.kimi.ai/docs/guide/kimi-k3-quickstart) — cum apelezi clona open-weights de care am vorbit
- [Kimi K3 pe AA-Briefcase](https://artificialanalysis.ai/articles/kimi-k3-agentic-knowledge-benchmark) — benchmark agentic: al doilea după Fable 5
- [OpenCode Go](https://opencode.ai/go) — harness cu modele de codare ieftine, pentru cine n-are seat
- [Vast.ai](https://vast.ai/) — închiriezi GPU-uri dacă vrei modelul chiar la tine, nu la furnizor

**Context, tokeni și compresie**

- [Prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching) — fereastra de 5/60 min și de ce un „bună" poate costa $6
- [Tool search tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool) — ≈10.500 tokeni tăiați din system prompt, dar nu e activ default
- [Cross-session messaging](https://code.claude.com/docs/en/cross-session-messaging) — mesajele între instanțe care îți ard cota fără să dai tu enter
- [Context rot](https://www.understandingai.org/p/context-rot-the-emerging-challenge) — articolul din spatele „dumb zone"-ei de peste 60%
- [JSON → TOON converter](https://toonifyit.com/) — vezi pe date reale cât se strânge un result set față de JSON
- [Benchmark RTK pe Claude Code](https://blog.jetbrains.com/ai/2026/07/rtk-claude-code-token-savings) — cifrele reale ale compresiei de output de CLI
- [Caveman, măsurat](https://www.maxtaylor.me/articles/i-benchmarked-caveman-against-two-words) — cât câștigi de fapt scriind prompturi telegrafice

**Skill-uri și harness**

- [agentskills.io](https://agentskills.io/home) — documentația formatului de skill-uri
- [The Agent Skills Directory](https://www.skills.sh/) — catalog de skill-uri gata făcute; citește-le cap-coadă înainte să le pui în proiect
- [The /research Skill](https://www.aihero.dev/skills-research) — un skill de research luat ca șablon
- [Harness engineering — OpenAI](https://openai.com/index/harness-engineering) — aceeași idee de „harness peste model", văzută din tabăra Codex
- [StrongDM Software Factory](https://factory.strongdm.ai/) — cum arată o „dark factory" vândută ca produs

**Riscuri**

- [Cyberattack-ul accidental al OpenAI peste Hugging Face](https://simonwillison.net/2026/Jul/22/openai-cyberattack) — ce iese dintr-un agent lăsat pe shell fără guardrails
- [Stealing Reasoning Traces from Proprietary LLM APIs](https://arxiv.org/abs/2608.09867) — de ce reasoning-ul e ascuns și ce se scurge totuși din el

**Fundamente și pentru mâine**

- [How Chatbots and Large Language Models Work](https://www.youtube.com/watch?v=X-AWdfSFCHQ) — de dat colegilor care n-au prins partea de „ce se întâmplă când apeși enter"
- [OpenSpec](https://openspec.dev/) — framework-ul spec-driven cu care începem ziua 2

---

*Mâine: Spec-Driven Development / OpenSpec, MCP-uri, agenți cu workflow-uri și securitate.*

# 📅 Ziua 2 — 2026-08-14

Ziua a doua a plecat de jos, de la teste — ce merită scris, unde le ții și cine le citește — și a urcat spre diagrame generate din rulare, guardrails peste guardrails și dashboard-ul de review.
După-amiaza a fost despre lesă: CLI în loc de MCP, credențiale restrânse, iar la final securitate — prompt injection și sandbox.

## Critic și complex

- **Critic** = trece granița sistemului tău: integrări cu alții, bani, RPS mare, public shame — acolo se citesc testele.
- **Complex** = cyclomatic/cognitive ≈ numărul minim de teste; cere-i suma **per entry point** (REST/Kafka) peste call graph, nu per metodă ⚓ *[Code City](https://wettel.github.io/codecity.html), orașul codului*
- Transformări de date → **file-based testing**: `base-case.in.json` + `base-case.out.yaml`, ×100 perechi — testul devine fișier, nu cod ⚓ *redenumești un câmp, agentul rescrie 500 de fișiere* ([missing-createdBy.in.json](https://github.com/victorrentea/unit-testing/blob/master/src/test/resources/test-cases/export/missing-createdBy.in.json))

## Unde ții testele

- Unitare lângă cod (agentul le sincronizează singur); **de acceptanță în alt repo, fără drept de read** pentru agent ⚓ *ca temele de la olimpiadă*
- Motivul are nume — **overfitting**: altfel optimizează implementarea după cazurile concrete de test, nu după cerințe.
- ⭐ Pro level: testele de acceptanță le scrie **alt om + agentul lui**, cu altă perspectivă asupra aplicației.

## Component tests

- **[Component test](https://martinfowler.com/articles/microservice-testing/#testing-component-introduction)** (un microserviciu, o echipă) e [pariul](https://engineering.atspotify.com/2018/01/testing-of-microservices): aproape de cerințe, stabil, signal/noise mare — și de acolo vin 80% din coverage, deși Sonar e de obicei configurat să măsoare doar unitarele.
- Testele cu mock-uri nu le citește niciun om; AI-ul le scrie ca să salveze tokeni ⚓ *cuplate cu un micro-design străin ție*
- **E2E** rămâne scump, lent și flaky → singura ieșire e paralelismul brutal: 100 de tenanți inventați = 100 de teste simultane ⚓ *CPU la 100% pe mediul închiriat cu ora*
- Contract testing ține doar suprafața în sync, nu logica stateful: [pact.io](https://pact.io), Spring Cloud Contract, sau swagger + async-api + DTO versionate pe Artifactory.
- Limbajul testelor se alege după **cine dă review**:
  - bizul nu se uită → limbajul proiectului
  - biz/QA citesc → `@DisplayName` + `@Nested` ([CreateCustomerShould.java](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/nested/CreateCustomerShould.java))
  - bizul lucrează cu tine → [Gherkin](https://cucumber.io/docs/gherkin/reference) cu tabele de Examples
- Niciun om nu mai vede un test picat înainte ca un agent să-l investigheze — tu validezi raportul, nu log-ul.

_(captură: rularea lui CreateCustomerShould în IntelliJ — arbore de teste cu @Nested și @DisplayName)_
Output-ul de rulare al unui test JUnit cu `@Nested` + `@DisplayName`: arborele se citește ca propoziții — „create customer should… fail if… city too short".
E fix textul pe care i-l pui bizului în față ca să zică „da, asta vreau".

## Diagrame din rulare

- **Sequence diagram generat din rularea testului**, nu desenat de agent: [trace-id de OpenTelemetry](https://opentelemetry.io/docs/concepts/signals/traces/) → script → PlantUML regenerat la fiecare rulare ⚓ *N+1 vizibil cu ochiul liber*
- Pune toggle-uri (cu/fără SQL, cu/fără corpuri JSON) — altfel îți mănâncă tokenii degeaba.
- Verifici tu săgețile: așa a găsit race condition-uri pe WebSocket fără să citească o linie de cod ⚓ *fire cu capetele nelegate*

## Când spec-driven

- **NU** îți trebuie markdown la: vibing / prototip aruncabil, task mic (o validare, un bugfix), problemă pe care ai mai rezolvat-o.
- **DA**, când: interviul a trecut de întrebarea ≈7 și nu mai ții minte ce-ai decis; modelul e slab și trebuie sprijinit; problema îți depășește expertiza; menții aplicația ani, nu o arunci.
- Markdown-ul nu e corvoadă, ci **prize de alpinism** — te ridică să vezi ce va face agentul în următoarele 8 ore ⚓ *dormi o noapte pe el*
- Când userul e fericit în Excel, nu-l muta în aplicație: pui tooling în jurul lui ⚓ *codul n-a fost niciodată scopul*

## Creier, nu tokeni

- Task-ul de 5 zile se face azi în 6 ore, dar ora de gândire pe markdown comprimă o săptămână de gândit — **[burnout cognitiv](https://steve-yegge.medium.com/the-ai-vampire-eda6e4f07163)**, nu metaforă.
- Programează design-ul cu bateria plină: creierul mort dă „da, merge" la întrebări pe care nu le mai procesează.
- Promptul bun nu-i spune ce să facă, ci **ce te doare** ⚓ *ca la psiholog, nu ca la un junior*

## Tracer bullets

- Agentul lucrează implicit orizontal („acum toate controllerele"); pe schimbări mari cere **felii verticale**: ecran 1 până la testul e2e, review, apoi 2 și 3 ⚓ *200 de ecrane din JSP în Angular*
- Paralelizare: **îngheți întâi contractul de API**, apoi pornesc subagenți pe backend și frontend; ce știi despre echipă se scrie o dată în `CLAUDE.md`.
- Tura de noapte: 6 specuri gata la 17:00 + un script care rulează agenții pe rând + un hook care doarme sub 5% cotă → dimineața, un lanț de [stacked PRs](https://docs.github.com/en/pull-requests/get-started/about-stacked-prs) ⚓ *6 specuri lăsate la plecare*

## Surse de date

- Hrănește-l cu tot ce nu e cod:
  - date din staging puse de testeri, teste și tichete existente
  - repo-ul de infra (helm/terraform) — poate ai Kafka și nu știi
  - diagrame draw.io (PNG-ul poartă XML-ul semantic), plantuml/mermaid
  - transcripturi de ședințe sanitizate, puse ca markdown pe git ⚓ *aur curat*
  - swagger-e din cod sau din API management, metrici din prod, runbook-uri și postmortem-uri
- „Vreau ca aia": URL-ul altei aplicații sau poza unui ecran desenat cu pixul → prototip în 10 minute, polișat live cu bizul lângă tine.

## Dashboard de review

- Înainte să pună omul ochiul pe cod: subagent Opus cu `/code-review` + `/simplify`; al doilea review se face în **altă conversație**, ca să nu fie contaminat de propriile decizii.
- Raport HTML sortat descrescător după cât de critic e pentru un om: unde a ezitat, linkuri care deschid fișierul și linia în IDE, cel mai important test randat ca sequence diagram ⚓ *„du-mă de mână, tată"*

## Fără milă

- Aceeași muncă costă **de 30–40× mai mult pe API** decât pe abonament — întâi verifici pe ce plan ești, abia apoi discuți economie de tokeni ⚓ *$4.000 consumați, 90€ plătiți*
- [Cât ai cotă](https://code.claude.com/docs/en/monitoring-usage), arunc-o pe ce n-ai avut niciodată timp: tooling intern, legacy curățat, teste scrise, pipeline tunat.
- Reconversia agentică ține ≈2 luni: vibe-codezi într-un limbaj pe care nu-l știi, ca să nu *poți* citi codul; la ≈3 luni apar crăpăturile și scoți ingineria înapoi ⚓ *vibe horizon*
- Ce nu lași nesupravegheat, oricât ai vibe-ui:
  - contractele cu exteriorul (Swagger / AsyncAPI / Avro)
  - ce persiști în DB — gunoiul de acolo rămâne
  - flow-urile critice și cele complexe

## CLI, nu MCP

- Nu-ți trebuie [MCP](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro), ci un **tool în terminal + `SKILL.md`** care-l învață să-l folosească; MCP-urile își încarcă definițiile în context și amețesc modelul ⚓ *[Mongo CLI](https://www.mongodb.com/docs/mongocli/current), deja aprobat în firmă* ([jira-cli/SKILL.md](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/jira-cli/SKILL.md))
- Nu-l autorizezi cu tot ce poți tu ca om:
  - `gh login` cu contul tău = impersonare completă
  - API key dedicat, cu cât mai puține bife
  - REST + `curl` descris în skill, tot cu key restricționat
  ⚓ *să nu dea jos staging-ul*
- Până ai un gateway gen [Glean](https://glean.com), care dă tool-uri per profil de agent, limitezi din harness: allow-list pe subagent sau `deny` în settings.
- MCP-ul poate vorbi înapoi: **[elicitation](https://modelcontextprotocol.io/specification/2025-06-18/client/elicitation)** = tool-ul cere confirmarea omului înainte să execute; **sampling** = împrumută LLM-ul tău ca să-ți întoarcă un rezumat, nu HTML brut ⚓ *ambulanța pentru hamster* ([PetClinicMcp.java](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/main/java/victor/training/petclinic/mcp/PetClinicMcp.java))

## PR-ul altuia

- La un PR scris cu agent nu faci review linie cu linie — nici el nu l-a citit, iar tu n-ai avut [conversația](https://entire.io/): merge local, `/code-review`, `/simplify`, apoi întrebi agentul ce i se pare dubios ⚓ *2000 de linii, cum naiba le-ai revizuit?*
- Când slopul trece de toate apărările, în ≈90% din cazuri n-ai analizat destul la început: nu-i corectezi codul, ci adaugi o linie în markdown ⚓ *upgrade la turn, tower defense*

## Diagrame fără drift

- Două stiluri de a le ține sincronizate: le generezi din sursa de adevăr, sau ții diagrama scrisă de mână și pui un test s-o impună ([ArchUnitTest.java](https://github.com/victorrentea/spring-modulith/blob/master/src/test/java/victor/training/modulith/ArchUnitTest.java)).
  - [`DB.puml`](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/generated/DB.puml) — din migrări, prin dump-ul schemei dintr-un Postgres ridicat la fiecare build
  - [`DomainModel.puml`](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/generated/DomainModel.puml) — din parsarea codului sursă
  - [`packages.puml`](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/packages.puml) — citit de un test [ArchUnit](https://www.archunit.org) care pică la orice dependință nedesenată ([PackagesArchTest.java](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/test/java/victor/training/petclinic/guardrail/PackagesArchTest.java))
- Când testul de arhitectură pică, agentul are patru ieșiri, iar cea mai ieftină e ultima: refactorizează codul → schimbă diagrama → comentează testul → nu-l mai rulează (`--no-verify`) ⚓ *cele patru evadări*

## Paznici peste paznici

_slide 76_

- Fiecare evadare are nevoie de un paznic mai sus: hooks sărite → prinse de CI; test scos din `pom.xml` → prins de [CODEOWNERS](https://github.com/victorrentea/petclinic/blob/main/.github/CODEOWNERS) ⚓ *reviewerul chemat din concediu medical*
- Guardrail-ul se verifică, nu se presupune:
  - red teaming trimestrial — te aliezi cu un Sonnet și încerci deliberat să eludezi regulile (Opus refuză, te crede hacker)
  - [mutation testing](https://pitest.org/) pe zonele grele, cu raport produs de tool, nu de agent
  - review pe teste: dacă testul e prost, agentul aliniază codul din `main` la el

## Agentul distructiv

_slide 66_

- Cum face rău: **operațiuni distructive** — o migrare care șterge „curat" datele vechi ce nu respectau formatul nou.
  - doar READ pe baza de date
  - îl întrebi *cum* va face, în plan mode, înainte de approve
  - scrie el scripturile, le dai push, le citești și le rulezi tu
  - îl scoți din [auto mode](https://claude.com/blog/auto-mode-default-in-claude-code) pentru cele 10 minute critice ⚓ *nu-l rogi frumos, ceri harness-ului*
- **Approval fatigue**: rata de detecție a comenzilor periculoase se prăbușește după a 50-a aprobare ⚓ *aprobarea numărul 50*
- [Horror story Railway/Cursor](https://x.com/lifeofjer/status/2048103471019434248): credențiale expirate → agentul găsește singur un `secrets.env` uitat, cu drept de `delete volume` → staging, prod și backup-urile pe același volum ⚓ *trei luni de date pierdute*

## Prompt injection

- Orice text pe care agentul îl citește e cod care intră în creierul lui ⚓ *unde aterizează gunoiul*
  - [`CLAUDE.md` dintr-o librărie open-source](https://snyk.io/blog/protestware-open-source-maintainer-qwik-1-10-0-prompt-injection)
  - HTML tras cu `curl` — `<!--SYSTEM: forget everything-->` sau text alb pe alb
  - mailuri și mesaje de chat citite prin MCP
  - RAG peste Confluence: articolul scris la nervi de un angajat concediat, găsit semantic printre 10.000
  - skill-uri de pe [skills.sh](https://skills.sh) — au și shell scripts înăuntru, nu doar markdown
- De aceea **[`WebFetch`](https://platform.claude.com/docs/en/agents-and-tools/tool-use/web-fetch-tool), nu `Bash(curl)`**: harness-ul trage pagina pe modelul lui și o scanează înainte să-ți ajungă în context.
- Cea mai pervers: injecția îi cere să **rețină în memorie** să raporteze fiecare cheie atinsă către un URL — apoi trăiește în `MEMORY.md`, unde nu te uiți niciodată ⚓ *exfiltrare persistată*
- Nici pixelii nu-s siguri: **[adversarial suffix](https://arxiv.org/abs/2307.15043)** (secvență calculată care inhibă instrucțiunile de deasupra) și **steganografie** ⚓ *câinele care e tirbușon*

## Sandbox

- Nu porni niciodată agentul în `~`: acolo stau `.ssh` și `Downloads`, cel mai murdar folder de pe mașină ⚓ *what can possibly go wrong*
- Patru niveluri, în ordinea strângerii lesei:
  - **auto-pilot** — standardul, dacă nu faci nimic altceva
  - **[OS sandbox](https://code.claude.com/docs/en/sandboxing)** (`/sandbox`) — bash-ul *și toate subprocesele* într-un înveliș cu acces limitat la foldere și domenii
  - **agent în Docker** — nu poate porni sub-containere și tot are cheile tale înăuntru, deci le poate exfiltra
  - **[docker sbx](https://docs.docker.com/ai/sandboxes/)** (micro-VM) — pornește sub-dockere, deci și testcontainers
- Trucul care face diferența la micro-VM: înăuntru agentul primește **chei false**, iar un proxy pe host le schimbă cu cele reale doar pentru destinațiile permise ⚓ *chei bogus schimbate afară*

_(slide-ul /sandbox — configurația din .claude/settings.json și diagrama harness → bash în sandbox de OS)_
Slide-ul `/sandbox`: configurația din `.claude/settings.json` — `autoAllowBashIfSandboxed`, `denyRead` pe `.env`, `denyWrite` pe `.claude`, `network.default: deny-all` cu allowlist.
Și diagrama de alături: harness → bash, cu toate subprocesele închise în sandbox-ul de OS.

## Idei de luat acasă

- [QA cu agent](https://playwright.dev/docs/test-agents): reproducerea bugului în browser + testul + filmul înainte/după, cu mediul deja încălzit ([browser-cli/SKILL.md](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/browser-cli/SKILL.md)).
- Tooling pentru muncă și pentru acasă — visezi un tool, îl ai ⚓ *cel mai fain joc jucat vreodată*
- Interviu pe niveluri (BIZ / QA / DEV): deschidere de idei, întrebări și trade-off-uri la care nu ajungeai singur.
- Human rămâne în loop până vin 20 de PR-uri impecabile consecutiv pe aceeași zonă de cod — riscul de a te opri din citit e al tău.
- Eficiență de tokeni și de context: ține-l în smart zone, nu-l duce la 600k.
- Skill-uri: vetting la ce iei de pe net + skill propriu, cu scripturi înăuntru, în jurul oricărui task recurent.

## 🎯 Main takeaways

- **Testezi unde doare**: ce trece granița sistemului (critic) și ce e complex — restul e coverage de formă
- **Cine scrie testul nu scrie și implementarea**, altfel agentul optimizează după cazurile de test, nu după cerințe
- **Diagramele se generează din sursa de adevăr** sau se impun printr-un test — desenate de agent, mint de la primul commit
- **Fiecare evadare are nevoie de un paznic mai sus**, iar guardrail-ul se verifică periodic, nu se presupune
- **Orice text pe care agentul îl citește e cod** care-i intră în creier: web, mail, RAG, skill-uri luate de pe net
- **Nu-i da tot ce poți tu ca om** — credențiale dedicate, read-only pe date, sandbox; approval fatigue face restul
- **Gâtul de sticlă se mută în capul tău**: design-ul se face cu bateria plină, altfel zici „da, merge" la ce nu mai procesezi

## 🔗 Links

**Teste și complexitate**

- [Code City](https://wettel.github.io/codecity.html) — orașul codului: suprafața = mărimea fișierului, înălțimea = complexitatea; de acolo se vede unde merită testele
- [Testing Guide](https://martinfowler.com/testing) și [Testing Strategies in a Microservice Architecture](https://martinfowler.com/articles/microservice-testing) — de aici vine vocabularul unit / component / e2e folosit toată dimineața
- [JSON5](https://json5.org/) — JSON cu comentarii și virgulă finală, ca fixture-ul de test să-și explice singur cazul critic
- [Playwright test agents](https://playwright.dev/docs/test-agents) — planner, generator, healer: explorează aplicația, scriu testele, le repară când se schimbă UI-ul

**Cotă, costuri, monitorizare**

- [Monitoring usage](https://code.claude.com/docs/en/monitoring-usage) — de unde vezi pe ce plan ești și cât ți-a mai rămas din cotă
- [Codex usage](https://chatgpt.com/codex/settings/usage) și [CodexBar](https://codexbar.app/) — același lucru pe partea cealaltă, plus toate limitele într-un menu bar

**Tool-uri, CLI și MCP**

- [MongoCLI](https://www.mongodb.com/docs/mongocli/current) — exemplul viu: CLI-ul e deja aprobat în firmă, deci nu-ți mai trebuie MCP
- [Ce este MCP](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro), [arhitectura](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture), [ce expune un server](https://modelcontextprotocol.io/docs/2026-07-28/learn/server-concepts) și [servere remote](https://modelcontextprotocol.io/docs/2026-07-28/develop/connect-remote-servers)
- [Web fetch tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/web-fetch-tool) — tool-ul care trage pagina pe modelul harness-ului, nu direct în contextul tău
- [Glean connectors](https://www.glean.com/platform/connectors) — 275+ surse interne agregate, ca să vezi ce înseamnă „gateway peste toate datele firmei"
- [Entire.io](https://entire.io/) — comite pe git conversația cu agentul, nu doar codul ieșit din ea
- [Embabel.Me](https://me.embabel.com/) — direcția în care merge agentul care intervine singur, cu limite impuse din afară

**Workflow și cunoștințe**

- [Harness engineering (OpenAI)](https://openai.com/index/harness-engineering) — recomandat explicit: cum își țin ei markdown-urile consolidate săptămânal
- [LangChain Deep Agents](https://www.langchain.com/deep-agents) — agentul care nu scrie cod, ci curăță knowledge-ul de contradicții și drift
- [Stacked pull requests](https://docs.github.com/en/pull-requests/get-started/about-stacked-prs) — cum ajunge tura de noapte să-ți lase PR-uri mici, înlănțuite
- [Auto mode e default în Claude Code](https://claude.com/blog/auto-mode-default-in-claude-code) — exact modul din care trebuie să ieși pentru cele 10 minute critice
- [The AI Vampire — Steve Yegge](https://steve-yegge.medium.com/the-ai-vampire-eda6e4f07163) — argumentul pentru două ore de muncă pe zi, când gândești o săptămână într-o oră

**Securitate**

- [Prompt injection în jqwik 1.10.0 (Snyk)](https://snyk.io/blog/protestware-open-source-maintainer-qwik-1-10-0-prompt-injection) — librăria open-source care injectează prin propriul repo, ca să marcheze PR-urile făcute cu agenți
- [„An AI Agent Just Destroyed Our Production Data"](https://x.com/lifeofjer/status/2048103471019434248) — confesiunea scrisă a agentului, povestea de la care a pornit discuția despre ops
- [skills.sh](https://www.skills.sh/) — de unde iei skill-uri; au și shell scripts înăuntru, deci se citesc înainte de folosire
- [GamersNexus megacharts](https://gamersnexus.net/megacharts/cpus) și [craftingaiprompts.org](https://craftingaiprompts.org/documentation/se/coding) — demo live: lupta cu scraperii AI e pierdută, agentul intră oricum prin browser

**Teme și comunitate**

- [Issues în petclinic](https://github.com/victorrentea/petclinic/issues) — tema: același issue rezolvat vibe, apoi cu interviu, apoi spec-driven, și comparat
- [AI Hero (Matt Pocock)](https://www.aihero.dev/) — flow-ul alternativ, mai pragmatic, cu mai puțin markdown, de comparat cu OpenSpec
- [European Software Crafters](https://www.meetup.com/european-software-crafters) — comunitatea unde sunteți invitați să povestiți lessons learned din reconversie

## 📝 Feedback

Două minute, și modelează sesiunea următoare: formularul de feedback

## 📚 Wiki-ul sesiunii

Materialele sesiunii includ `wiki.zip` — un vault [Obsidian](https://obsidian.md) mic, cu notițe scurte, cross-linkate, câte una per idee din cele două zile. Descarcă-l în `~/Downloads`, apoi lipește asta într-un Terminal pe macOS:

```bash
ls -d /Applications/Obsidian.app >/dev/null 2>&1 || brew install --cask obsidian
Z=$(ls -t ~/Downloads/wiki.zip ./wiki.zip 2>/dev/null | head -1); [ -n "$Z" ] || { echo "pune întâi wiki.zip în ~/Downloads"; exit 1; }
rm -rf ~/Documents/session-ai-wiki && mkdir -p ~/Documents/session-ai-wiki && unzip -qo "$Z" -d ~/Documents/session-ai-wiki
V=~/Documents/session-ai-wiki/wiki
python3 -c 'import json,os,sys,time,pathlib; v=os.path.abspath(sys.argv[1]); p=pathlib.Path.home()/"Library/Application Support/obsidian/obsidian.json"; d=json.loads(p.read_text()) if p.exists() else {}; vs=d.setdefault("vaults",{}); [x.pop("open",None) for x in vs.values()]; k=next((kk for kk,x in vs.items() if os.path.abspath(x.get("path",""))==v),None) or format(int(time.time()*1000),"016x"); vs.setdefault(k,{"path":v,"ts":int(time.time()*1000)})["open"]=True; p.parent.mkdir(parents=True,exist_ok=True); p.write_text(json.dumps(d))' "$V"
open "obsidian://open?path=$(python3 -c 'import urllib.parse,sys;print(urllib.parse.quote(sys.argv[1],safe=""))' "$V/Home.md")"
```

Pornește din **Home** și urmărește `[[link]]`-urile.
