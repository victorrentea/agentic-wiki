# AI-Assisted Coding @ un operator telecom — Ziua 1 (2026-05-04)

> Workshop susținut de Victor Rentea pentru o echipă mixtă a unui operator telecom (sală + remote). O parte folosesc Copilot, o parte Cline, iar la curs aproape toată grupa s-a convertit la **Claude Code** prin LLM Proxy intern (CCR — Claude Code Router) cu 100$ buget. Stack proiect demo: PetClinic (Java backend, Angular frontend, Postgres embedded).

---

## 09:30 — 11:00 · Setup, fundamente LLM, economia tokenilor

Prima oră a fost dominată de bătălia de instalare a `claude-code` peste rețeaua internă. Lecția operațională: pe rețeaua internă a clientului nu poți face `npm install`, deci instalarea efectivă a CCR-ului se face de pe rețeaua externă, după care login-ul prin **LLM Proxy** funcționează doar pe rețeaua internă. Se face danț on/off pe Wi-Fi. **Recomandarea fermă a lui Victor: `claude-code` (terminal) > `opencode` (GUI, dar permite schimbarea modelului) > Cline (plugin de editor) > Copilot (în declin).**

**LLM vs Harness** — distincția crucială pe care nu o face nimeni clar la început: LLM-ul = modelul (Opus/Sonnet/Haiku), harness-ul = ce ecosistem rulezi în jur (Claude Code, OpenCode, Cline, Cursor). Poți combina modele cu harness-uri diferite.

### Fundamentele LLM-ului ([Karpathy — Intro to LLMs, 1h talk](https://www.youtube.com/watch?v=zjkBMFhNj_g))

- **LLM = API stateless**. Lucrează pe **token** (~ un cuvânt; `print(`, `AND`, `_ING` sunt token-i separați). Vizualizator: [tokenizer.openai.com](https://platform.openai.com/tokenizer)
- **Antrenarea Opus** se zice că a costat ~500M$. De-aia nu apar peste noapte modele noi.
- **Tarife (Sonnet 4.6)**: $3/M input, $15/M output. **Output-ul e ~5× mai scump** ca input-ul. La Opus: ~$5.25/M input — model "bătrân", profund, scump pentru reasoning complex.
- **🤖 Tip neînvins**: Claude API are [prompt caching](https://docs.claude.com/en/docs/build-with-claude/prompt-caching) cu TTL 5 min — token-ii deja procesați costă ~10% din normal. **Schimbarea de model invalidează cache-ul** — de-aia "planuiești cu Opus, execuți cu Sonnet" se face într-o **conversație nouă** după `/clear`, dându-i markdown-ul de design ca specificație fresh. Niciodată în aceeași conversație.

### Hallucinații — 3 cauze, 1 soluție

1. Promptul tău e prost (vag, contradictoriu, incomplet)
2. Modelul **n-are context despre proiect** (nu știe că folosești Java 21 sau Spring Boot 3.x)
3. Cunoștințele depășesc **knowledge cutoff** — Opus 4.7 e oprit la sfârșit ianuarie 2026

**Trucul unei participante** (memorabil): nu doar "dă-mi sursele", ci **"dă-mi link-uri către cărți pe Amazon și verifică prin web search că pointează către cărți reale"**. Forțează-l să-și demonstreze afirmațiile. Dacă o carte nu există pe Amazon, probabil e halucinată.

### Context window — economia atenției

- Sonnet: 200k token; Opus: 1M; GPT-5: 1M; Codex: 400k
- **Dump zone**: peste **65% context plin**, modelul începe să bată câmpii. La 130k din 200k → tragi frâna, faci handover prin markdown, dai `/clear`.
- Contextul include: system prompt (You shall not do harm), tool definitions, skills frontmatter, memorii (CLAUDE.md user + project), istoricul conversațional, output-urile de la tool-uri rulate. **Tool results și system prompt nu se compactează** — rămân la finalul conversației.
- **Compactare automată** (la 100%): pierzi detalii pe care nu le poți controla. De-aia faci handover manual via markdown — ai control pe ce rămâne.

🤖 **Detaliu nemenționat**: Claude Code afișează `/context` cu defalcare pe categorii. Dacă vezi tool definitions ocupând 30k+ token, e semn că ai prea multe MCP-uri instalate global — mută-le pe scope de proiect.

---

## 11:10 — 12:30 · Memorie, brainstorming înainte de cod, guardrail-uri

### CLAUDE.md / AGENTS.md — primul nivel de control

Două fișiere, două convenții:
- **CLAUDE.md** — citit automat de Claude Code
- **AGENTS.md** — citit automat de Copilot/Codex/Cline

Ambele pot trăi în două locuri: **home (`~/.claude/CLAUDE.md`)** = memorie user, persistă în orice proiect; **rădăcina proiectului** = memorie comisă în git pentru toată echipa.

Două reguli de aur care rezolvă boala lingușelii:
```
Keep explanation concise.
Challenge ambiguous wrong prompts.
```
Lingușeala = marketing. Vor să-ți placă să lucrezi cu el. Spune-i explicit să-ți zică când promptul tău e stupid.

⚠️ Atenție: dacă ai și `AGENTS.md` și `CLAUDE.md`, **Claude nu garantat citește AGENTS.md** chiar dacă i-ai scris în CLAUDE.md "te rog citește AGENTS.md". Recomandarea: consolidează totul în `CLAUDE.md` și șterge `AGENTS.md` (sau vice-versa, în funcție de uneltele echipei).

### Brainstorming înainte de orice feature

Demo: implementarea `InvoiceService.calculateTotal()`. **Înainte să te apuci de cod**, prompt:

```
Vreau să implementez calculate total, dar până te apuci să scrii orice cod,
vreau să mă întrebi întrebări despre decizii de design, despre funcționalitatea
dorită. Vreau să mă întrebi o întrebare pe rând, la care să-ți răspund.
Să facem un fel de brainstorming. La finalul acestui brainstorming, voi vrea să
scrii testele întâi, să le validezi, după care, abia ulterior, vei scrie implementarea.
```

Întrebările pe care le-a venit AI-ul singur (lucruri pe care **Victor le uitase**): discount = procent sau absolut? pe item sau pe linie? listă goală → 0 sau excepție? listă null → ce facem? valori negative după discount permise? unde validăm — în constructor de Item sau în service? câte zecimale rotunjim?

> 🤖 **Pattern-ul cheie**: AI-ul vede mult mai multe corner cases decât prinzi tu pentru că a văzut milioane de astfel de implementări. **Brainstorming-ul e free QA pe spec.**

Apoi TDD: scrie testele întâi, le validezi tu, abia apoi implementarea. Înainte de AI, TDD era frecvent abandonat din pricină de fricțiune; cu AI dispare costul scrierii testelor.

### Cele 4 etape când nu-ți place codul generat

| Etapă | Strategie | Slabe puncte |
|------|-----------|--------------|
| 0 ❌ | Modifici manual | Pierzi timp, AI nu învață |
| 1 — *steering* | Îi spui în chat: "liniile sunt prea lungi, sub 120" | Pe termen scurt funcționează, dar uită mâine |
| 2 — *project memory* | Pui regula în `CLAUDE.md` | E doar sugestie — uneori ignoră |
| 3 — *guardrail* | Script pre-commit (git hook, linter) care îl OPREȘTE programatic | Garantat, dar trebuie scris |

Demo: i-a cerut un git hook care fail-uie commit-ul dacă vreo linie depășește 120 caractere. Mai elegant: **chemi un linter (ESLint/SonarLint/Checkstyle) și ăla raportează erorile, care devin feedback-loop pentru ca Claude să corecteze singur**. Mai puțin cod custom, mai mult ecosistem.

🤖 **Anti-pattern de evitat**: nu pune git hook pe `commit` pentru blocaje grave — pune pe `push`. Commit local trebuie să fie ieftin; doar push-ul (care afectează echipa) merită blocat tare.

---

## 13:30 — 14:50 · AI Stories, skill-uri, context ierarhic

### Thinking — ce supraviețuiește între turn-uri

Discuție subtilă cu un participant: **thinking blocks NU se păstrează între turn-uri** (între user message și următorul user message). În cadrul aceluiași turn (cu tool calls multiple) se păstrează. Între turn-uri Claude trimite doar textul vizibil al răspunsului + tool use/results.

> Justificarea: dacă Opus extended thinking generează 30k token/turn, în 2-3 turn-uri ai prăjit context window-ul. **Tot istoricul textual e salvat pe disc** (în `~/.claude/projects/...`), dar la `/resume` nu se reîncarcă thinking-ul vechi.

🤖 Detaliu: poți seta `/effort` la `low | medium | high | max`. **Niciodată max** — îți arde bugetul pe o oră și uneori gândește mai prost decât pe high. Default = high.

### AI Stories de la participanți (cele mai memorabile)

😊 **Front-end design CSS** — "moarte CSS-ului". Ai cerut "ecran X, look-and-feel ca alea, behavior Y". A ieșit HTML+CSS impecabil. Mai mult: Victor a zis că au apărut tool-uri tip "Figma Make" și că UX designeri îi spun că AI-ul face design mai bun decât ei. **Truc memorabil**: alinează butoane făcând screenshot la Chrome și pasându-i poza — "uite cum arată acum, vreau să fie aliniate ca aici".

😊 **Documentație gârlă din cod** pentru cineva nou — generezi spec funcțional dintr-un repo opac. Util la onboarding și la migrări.

⚠️ **Refactor / cleanup** — dual-edged: AI-ul tinde să **repete cod în loc să-l reutilizeze** (e frică să nu strice ceva existent) și **îi e frică să șteargă cod mort**. **La review caută activ duplicări și cod neapelat** — sunt pattern-urile cele mai des introduse de AI gen.

🙁 **AI Spiraling**: a luat-o pe drum greșit, gândește 10-30 min, a divergat. **Reacția corectă**:

```
1. STOP — nu-l mai lăsa să continue
2. Reasoning root cause — îl întrebi calm "cum ai ajuns aici? explică firul logic"
   (NU îi spui "ai greșit" — devine sycophant și-ți dă dreptate fără să gândească)
3. Identifici presupunerea greșită din lanțul lui de raționament
4. Găsești ce knowledge îi lipsea
5. /clear și pornești o conversație nouă cu acel knowledge inclus
6. Adaugi regula în CLAUDE.md ca să nu se mai întâmple
```

🙁 **O participantă — refactor periculos** la auth middleware: AI-ul a propus să rescrie complet user directories din mai multe DB-uri în una singură. Truc de **slow-down**: "**cine v-a lucrat aici?**" — îl forțezi să citească `git blame` și să-și dea seama că se atinge de cod cu istorie complicată, ce a apărut din motive specifice.

### Vibe coding ≠ AI coding

> **Vibe coding = SĂ NU MAI CITEȘTI COD.** Doar testezi outputul. Pentru cod de producție e periculos azi; "peste 2 ani poate". Spike-uri / scaffolding / aplicații personale = OK.

### Context ierarhic + Skills — soluția când CLAUDE.md crește

CLAUDE.md root al PetClinic ajunsese la 149 linii pentru că amesteca reguli backend (Lombok, MapStruct), frontend (Angular), database. **Două tehnici de descongestionare**:

**1. CLAUDE.md ierarhice** (monorepo): pui `backend/CLAUDE.md`, `frontend/CLAUDE.md`. Claude Code **încarcă automat** CLAUDE.md din folderul în care lucrează în plus față de cel root. Cline / Codex respectă același pattern cu `AGENTS.md`. Garantat și determinist.

**2. Skills — progressive disclosure** (analogie Matrix: "I know Kung Fu" — neo învață skill-uri la nevoie). Anatomia:

```yaml
---
name: java-developer
description: Use when editing or writing Java files in PetClinic. Loads project conventions
  for constructor injection, transactional boundaries, exception handling.
---

[corpul cu reguli, încărcat DOAR la activare]
```

- **Frontmatter** (~75 token) = încărcat automat la pornirea conversației, în catalog
- **Body** (~600 token sau mai mult) = încărcat doar când AI-ul **decide că are nevoie** pe baza descripției
- 🤖 Vezi [repo-ul oficial Anthropic Skills](https://github.com/anthropics/skills) cu exemple, spec și template

**Atenție**: dacă pui >50 skills, AI-ul devine paralizat (Subway / Starbucks paradox). Industria rămâne pe "zeci, nu sute" de skills.

⚠️ **Contradicții între reguli** = tată-rău: AI-ul începe să halucineze pentru că nu știe ce să asculte. Periodic revizitează `CLAUDE.md` și skills, caută reguli care se bat cap în cap.

**Thin skills vs Fat skills**: thin = doar pointer-i către fișiere `.md` adiționale ce se încarcă progresiv (ex: skill-ul Victor de scraping LinkedIn cu antibot — un body mic + `antibot-deep-analysis.md` + scripturi `.js` de injectat în Chrome). Fat = tot conținutul direct în SKILL.md. Thin scalează mai bine.

🤖 **Linkuri utile**:
- [obra/superpowers](https://github.com/obra/superpowers) — colecția cea mai cunoscută de skills (Jesse Vincent)
- [Anthropic skills repo](https://github.com/anthropics/skills) — exemple oficiale și template-uri
- "Caveman" skill — forțează modelul să răspundă concis (economisești output token-i ~5×). Caută-l în comunitatea de skills; nu pun link că n-am găsit unul canonic în repo-uri oficiale

---

## 15:00 — 16:20 · Owner Search — exercițiu live cu Superpowers

Instalare: `/plugin install superpowers@superpowers-marketplace` (sau prin `/plugin marketplace add obra/superpowers-marketplace`). În funcție de rețea, una sau cealaltă merge.

### Cerința deschisă: "îmbunătățește owner search"

Brainstorming colectiv pentru a vedea câte cerințe ascunse sunt într-o cerere vagă:
- Search dinamic la fiecare literă (nu la enter)
- Cu **debounce ~200ms** (industria standard)
- Case-insensitive
- Search în toate coloanele vizibile (first, last, address, city, telephone, **pets** — uitat la prima iterație!)
- "Conține" nu doar "începe cu"

🤖 **Race condition pe care AI-ul l-a sugerat singur**: dacă apeși rapid `A` apoi `B`, două request-uri pleacă în paralel; dacă răspunsul pentru `A` ajunge **după** răspunsul pentru `AB`, vezi rezultatele greșite. **Soluția RxJS**: `switchMap` (anulează request-ul precedent). În Angular: `valueChanges.pipe(debounceTime(200), distinctUntilChanged(), switchMap(...))`.

### Spec → Design → Plan → Sub-agents

Workflow-ul `/brainstorming → /writing-plans → execuție` din Superpowers:
1. **Brainstorming** — întreabă pe rând, lămurește decizii
2. **Design doc** (markdown comis în repo) — citit și validat manual
3. **Implementation plan** — pași mici, granulari
4. **Execuție** — fie inline cu checkpoints, fie cu **sub-agenți paraleli**

**Sub-agents** = thread-uri noi cu context separat. Exemple de utilizare:
- **Research în cod** (cele mai bune use case): "minează 50 de fișiere și raportează-mi un sumar de 4 linii". Munca de research **bubuie contextul orchestratorului** dacă o face în-line; subagentul îți întoarce doar concluzia.
- **Backend + frontend în paralel** după ce ai dat planul piesă cu piesă.

🤖 **Detaliu nemenționat**: subagenții moștenesc CLAUDE.md global + skills, dar **nu** primesc istoricul conversației părinte — doar promptul explicit pe care îl dai. De-aia trebuie să le formulezi cereri auto-conținute. Vezi [Claude Code subagents docs](https://docs.claude.com/en/docs/claude-code/sub-agents).

🤖 **Modele pentru subagenți**: orchestratorul pe Opus poate spawna subagenți pe Sonnet (sau Haiku pentru task-uri mecanice). Setează `model` în frontmatter-ul agent-ului. Cost optimization principal când orchestrezi 5+ subagenți paraleli.

### Skills cheie din Superpowers (Victor le-a apreciat în mod special)

| Skill | De ce e love |
|-------|--------------|
| `brainstorming` | Auto-activează când cererea e netrivială; te interoghează în loc să-ți dea drumul orb |
| `writing-plans` / `executing-plans` | Spec + plan + checkpoints; baza pentru workflow disciplinat |
| `subagent-driven-development` | Orchestrare paralelă cu review între pași |
| `test-driven-development` | Forțează test-first; orice cod scris are test roșu→verde |
| **`systematic-debugging`** | "Iron law: no fixes without root cause". A găsit bug-uri de **ani de zile** în repo-uri unde nimeni nu se prinsese |
| `verification-before-completion` | Înainte să spună "gata", rulează verificări |

> Filozofia `systematic-debugging`: **dacă ai încercat 3 fix-uri și niciunul n-a mers, OPREȘTE-TE — ești pe drum greșit**. Nu se rezolvă prin "mai bag o fisă, poate merge". Găsește un working example de comparat, identifică diferențele, formulează ipoteză, test minimal pentru ipoteză, verify before continuing.

### Vina e mereu între scaun și tastatură

Spec-ul a omis explicit **petele** ca dimensiune de search. AI-ul l-a întrebat pe Victor "vrei și pets?", iar Victor a răspuns "doar owners". Apoi s-a supărat că nu caută după pets. **Lecție**: când AI-ul te întreabă ceva ce ți se pare ciudat, **nu răspunde reflex** — analizează dacă întrebarea relevă o presumpție pe care el o face și care contrazice cererea ta.

### Tracking buget — ccusage

```bash
npx ccusage@latest
```
Sau pentru status line live sub input: [Owloops/claude-powerline](https://github.com/Owloops/claude-powerline) (vim-style powerline cu token + cost tracking afișat sub input). Afișează tokens + cost current session + week.

🤖 **Important**: LLM Proxy-ul intern al clientului **nu expune endpoint de quota** către utilizator. Dacă vrei să vezi cât ai ars azi/luna asta, **trebuie să ceri admin-ului** sau să rulezi `ccusage` local (care citește log-urile cache-uite de Claude Code).

---

## 16:30 — 17:30 · MCP — Model Context Protocol

### Ce este MCP, ce face peste REST

[MCP](https://modelcontextprotocol.io/) e un protocol de comunicare între un agent (Claude Code) și un proces extern (MCP server, local sau remote) care expune **tool-uri**, **resources**, **prompts**.

Cinci feature-uri prin care diferă de REST clasic:
1. **Response streaming** (server-sent events / WebSockets) — răspunsul vine rând-cu-rând
2. **Elicitation** — server-ul poate cere agentului să întrebe userul (ex: "ești sigur că vrei să trimiți acest mail?")
3. **Prompt templates** — server-ul îi spune agentului cum să formuleze cererea către LLM
4. **Sampling** — server-ul poate folosi LLM-ul agentului (rar implementat, security-sensitive)
5. **Discovery dinamică** — agentul descoperă tool-urile la runtime, nu hardcodate

### Catalog & instalare

[github.com/mcp](https://github.com/mcp) — catalog oficial de servere MCP, inclusiv [chrome-devtools-mcp](https://github.com/ChromeDevTools/chrome-devtools-mcp), [bytebase/dbhub](https://github.com/bytebase/dbhub), [sooperset/mcp-atlassian](https://github.com/sooperset/mcp-atlassian) (Jira/Confluence).

Comandă tipică:
```
claude mcp add chrome-devtools --scope user npx chrome-devtools-mcp@latest
```

⚠️ **Securitate**: MCP servers rulează **cod de pe internet pe mașina ta**. Folosește **doar din catalogul oficial** sau de la furnizori de încredere. Multe servere comunitare au acces la file system / shell.

### Demo-uri rulate live

**Chrome DevTools MCP**: "Deschide site-ul unui operator telecom și extrage-mi ofertele de prepay". Claude a deschis Chrome în background, a făcut click-uri prin pagină, a comparat cu site-ul unui concurent. Use case puternic: **reproducerea bug-urilor din UI**:
1. Click prin aplicație până apare bug-ul (Claude controlează browser-ul)
2. "Acum scrie-mi un test Playwright care reproduce ce ai făcut"
3. "Acum fixează bug-ul până testul trece"

**DB MCP (PetClinic)**: "Cine are cele mai multe animale?" → Claude a generat SQL: `SELECT owner.first_name, owner.last_name, COUNT(*) FROM owner JOIN pet... GROUP BY ... ORDER BY count DESC`. Combinat cu **MCP de Postgres + teste pe proceduri PL/SQL = poți rescrie PL/SQL în Java cu Claude** (faimosul tweet Antroficului din februarie despre IBM care a căzut 15% la bursă).

🤖 **Punctul critic pe care l-a făcut Victor**: principala problemă a migrărilor PL/SQL → Java și COBOL → Java NU e capacitatea AI-ului, ci **lipsa testelor pe sistemele vechi**. Dacă ai test coverage pe procedurile PL/SQL, rescrierea cu AI devine fezabilă; fără teste, nu poți demonstra că nu ai stricat.

**Jira MCP**: în grupa anterioară un participant a făcut un MCP custom în 2-3 ore pentru Jira-ul intern al clientului, plecând de la [sooperset/mcp-atlassian](https://github.com/sooperset/mcp-atlassian). Use case: "ia-mi tichetul JIRA-1234 și implementează-l" — Claude citește din Jira, înțelege contextul, codează.

### Probleme de rețea la client

**npx prin rețeaua internă nu merge** — multe MCP servers se descarcă on-the-fly via `npx package@latest`, ceea ce eșuează pe rețeaua internă. Workaround: descarcă pe rețeaua externă, rulează apoi local. Acesta a fost frustrarea principală a după-amiezii.

---

## Citate / momente memorabile

> *"Vina e mereu între scaun și tastatură."* — refren al zilei

> *"Nu îi spui «ai greșit», că dacă îi spui ai greșit, zice «da stăpâne, am greșit». Tu vrei să-l întinzi pe canapea și să-l întrebi puiule, ce-a fost în capul tău?"* — pe analiza root cause a AI spiraling

> *"AI-ul iubește exemplele."* — pattern de prompting

> *"Să nu-l lași pe Opus niciodată."* — pe bugetul de 100$. (Opus arde 3000$/lună fără probleme)

> *"Asta e arta dezvoltării de acum: cum poți să faci ceva precis cu atâta random nesfârșit în jurul tău?"* — pe non-determinismul AI-ului

> *"E o etapă de transformare, de renaștere"* — Victor despre psihoza AI prin care trece oricine începe să folosească Claude Code intens. Apare după prima zi când pornești 2 terminale Claude simultan.

---

## Tools menționate

- [Wispr Flow](https://wisprflow.ai) — voice-to-text cu subscripție lunară (Victor menționează ~20$/lună), folosit pentru a vorbi cu Claude prin telefon din mall
- [Claude Code](https://docs.claude.com/en/docs/claude-code) — CLI principal recomandat
- [OpenCode](https://opencode.ai) — alternativă cu GUI și switch ușor între modele
- [Claude Code Router (CCR)](https://github.com/musistudio/claude-code-router) — permite folosirea Claude Code peste alte API-uri (LLM Proxy intern)
- [Superpowers marketplace](https://github.com/obra/superpowers-marketplace)
- [ccusage](https://github.com/ryoppippi/ccusage) — tracking spend Claude Code

---

## Ce urmează mâine (din ce a tachinat Victor)

- **Spec-driven development cu OpenSpec** — alternativă la Superpowers, folosește 3 markdown-uri (proposal + design + tasks) cu workflow disciplinat. [openspec.dev](https://github.com/Fission-AI/OpenSpec)
- Polish pe Owner Search (paginare cu sortare, pets în search)
- MCP de Jira custom pentru client
- Mai multă practică pe sub-agents paraleli cu Git Worktrees

---

# Ziua 2 (2026-05-05)

> Focus: **spec-driven development** cu OpenSpec și polish pe Owner Pagination/Sorting. **Conținutul planificat pentru după-amiază (Guardrails, Security, vs Legacy) nu apare în transcripție** — workshop-ul a continuat dar transcripția se oprește la pauza de prânz (12:35), urmată de o sesiune separată în engleză pentru o altă audiență, exclusă din acest summary.

## 09:33 — 11:00 · Cele 4 moduri de lucru cu AI + OpenSpec setup

### Patru paliere de complexitate

| Mod | Când îl folosești |
|-----|-------------------|
| **Direct prompt** | Schimbare mică, știi unde umbli. Exemplu: "alinează butonul X în Y". Mai bine polișezi promptul cu Dinuțu (ChatGPT free) ÎNTÂI ca să fie comprehensiv |
| **Plan mode** | Schimbare medie. AI-ul își face plan pas cu pas, fără să atingă codul |
| **Spec-Driven Development** | Feature mare (estimare > 1 zi). 3 markdown-uri: Proposal (ce), Design (cum), Tasks (microscopice) |
| **Ralph autonomous loop** | Maturitate > 6 luni de AI adoption. AI rulează 8h overnight cu spec + guardrails + feedback loops |

### Critica Copilot — o participantă și migrarea Java 14→17

O participantă a încercat o migrare Java 14→17 cu Opus în Copilot și AI-ul s-a turbat. Două motive grele:
- **Opus în Copilot are context limitat la 160k** (vs 1M real în Claude Code). E "un opus de 5 ori mai prost", capat artificial pentru economia de buget Microsoft.
- **Tarifare per-prompt** (până 1 iunie 2026) → fenomen pervers: dai 1 prompt scurt, Opus rulează 3 ore, te taxează ca 1 prompt.

🤖 **Hack pentru a șanteia Microsoft** (până în iunie când trec la per-token): faci în Dinuțul un prompt baban cu exemple, taci, după îl arunci în Opus dimineața. La 3 după-amiaza ai terminat. Copilot îți va factura ca 1 prompt unic. Începând **iunie 2026** Microsoft trece la per-token și paradisul moare.

### Open Rewrite — soluția corectă pentru migrări mari

[OpenRewrite](https://docs.openrewrite.org/) face syntax transformations programatic, **fără să consume tokens**. Strategie corectă:
1. Întâi rulezi rețetele OpenRewrite (acoperă ~90% din schimbări sintactice)
2. Pui AI-ul să polișeze restul (10%, partea unde rețeta nu acoperă)

🤖 **Alternativă încă mai ieftină**: AI-ul îți generează un script Python/AWK care modifică codul programatic. Dacă ai de schimbat 1000 de fișiere în același mod, **nu faci de mână și nu lași AI-ul să intre fișier cu fișier**. Mai precis decât AI hallucinând prin sute de locuri.

### Vibe Heresy — un participant și migrarea blocată

Un participant a vrut migrare Java și AI-ul a luat-o razna. **Vibe Heresy** (termen [Simon Willison](https://simonwillison.net/)): un moment de neatenție când îi spui "lasă așa" la ceva ce nu-ți place de fapt → AI-ul împrăștie acea greșeală în comentarii, markdown-uri, cod, **peste tot**.

> *"Dacă aveți la țară curte, încerci să smulgi pulpoiul sau iarba. Aia tot iese, mama mălții, tot iese. Dacă doar tai nu ne ajunge, trebuie să sapi, să răstorni pământul."*

**Soluția corectă**: revert la tot, `/clear`, repornești cu plan și CLAUDE.md actualizat. Dacă încerci să smulgi punctual fiecare apariție, mereu mai rămâne sămânța într-un comentariu sau markdown undeva.

### OpenSpec — anatomie

[OpenSpec](https://openspec.dev) ([github](https://github.com/Fission-AI/OpenSpec)) — framework spec-driven open-source, agnostic la harness (funcționează cu Claude Code, OpenCode, GitHub Copilot, Cline simultan).

```bash
npm install -g openspec      # global
cd <project-folder>
openspec init                # alegi tool-urile pe care le folosești
openspec config profile      # bifezi: new change, continue change
```

Două puncte de fricțiune pe rețeaua clientului (jumate de oră de debug colectiv):
- `npm config set proxy <proxy-ul intern>` ca să te tragă de pe internet din rețeaua internă
- Terminal cu admin pe Windows pentru `npm install -g`

### `/openspec:propose <prompt>` — ce generează

Într-un folder `openspec/changes/<change-name>/`:
- **proposal.md** — cerințe funcționale (user-facing); review cu business-ul
- **design.md** — design tehnic, contracte, entities, queries; review cu un coleg tech
- **tasks.md** — micro-task-uri cu `[ ]` brackets care devin `[x]` pe măsură ce sunt bifate
- **specs/** — sub-foldere cu **Given-When-Then** scenarios (acceptance criteria, NU teste executabile direct — sunt indicii pentru AI să-și scrie testele de acceptanță)

> 🤖 **De ce contează `[ ]` brackets**: permite **handover între developeri** (dai task.md la coleg cu progresul bifat când rămâi fără cotă) și **între sesiuni** (după compactare/clear). *"Peste 5 ani e posibil ca asta să înlocuiască Jira."*

⚠️ **Recomandare**: în loc de `/openspec:propose` (toate 3 deodată), folosește **`/openspec:new`** + **`/openspec:continue`** progresiv — review pe proposal cu business → continue → review pe design cu tech lead → continue → tasks (overview rapid). Dacă schimbi proposal după ce ai și design generat, OpenSpec mai uită să update și design și tasks → drift între artefacte.

### Granularitate prea fină

La primul run, OpenSpec a creat 3 sub-spec-uri pentru Owner Page (search, sort, paginate). Victor a forțat re-run: *"Unește specurile sub un singur folder Owner Page. Dacă mai vin modificări tot pe Owner Page, să cadă acolo."*

🤖 **Pattern**: pre-creează folderele goale în `openspec/specs/` pentru zonele de business pe care vrei vertical slicing. AI-ul citește specs/ existente când propune un change și își ghidează singur granularitatea.

### `/openspec:archive` — punctul magic

După implementare, conținutul din `openspec/changes/` se mută în `openspec/specs/` ca **behavior consolidat**. Pe măsură ce acumulezi changes, în `specs/` se acumulează reverse-engineered functionalitatea în format Given-When-Then.

**Brownfield-ready**: poți adopta OpenSpec pe orice proiect existent. După 6 luni de utilizare religioasă, ai un spec viu, în sync cu codul, fără drift față de Jira.

> *"Voi aveți proiecte în care Jira a făcut drift față de cod, a rămas în urmă cu luni? Tot timpul. Dacă joci religios pe OpenSpec, după 6 luni ai reverse-engineered behavior-ul în when/then, executabil."*

### Brainstorming colectiv: Owner Pagination + Sorting

Victor a forțat grupa să anticipeze toate punctele de design **înainte** să arunce în AI. Storci creierii întâi. Output (mai detaliat decât ieri):
- Pagini de **5/10/25** (default 10)
- Sortare în **BD** — Where → Order → Limit/Offset (nu invers; "AB-C-A vs ABC-A" pe registrari)
- Sortabil după **full name as displayed** (first + last concatenated, NU separat) și după city. NU după address ("11 Park Ave" în SUA începe cu numărul, nu literă)
- Iconiță sortare în header cu toggle ascending/descending
- Sortare default: nume ascending la prima intrare pe pagină
- Butoane: prev-2, prev-1, current, next-1, next-2, first, last (un participant: *"trebuie să ne aducă paginile pe care îți poți însări"*)
- "Înregistrările 11-20 din 43" jos (paginat și total)
- Search reset to page 1 on query change (cazul unei participante: ești pe page 30 căutând Ana, fără reset n-ai vedea cele 5 rezultate de pe page 1)
- Toate state-ul (search, page, sort) în URL ca query params, ca să poți da link la coleg

🤖 **Race condition**: dacă utilizatorul apasă rapid sort și apoi schimbă search-ul, două request-uri pleacă în paralel. Dacă răspunsul de la sort vine după răspunsul de la search, vezi rezultate greșite. **Soluție RxJS**: `switchMap` (anulează request-ul precedent). Apare și în output-ul OpenSpec ("Switch map to cancel stale requests").

---

## 11:00 — 12:30 · Review proposal/design, AI corrections, polish

### Last name vibe heresy — un detective story

În proposal-ul generat: `The owner list currently uses GET /happy-owners ...` ❌ — endpoint-ul nu există. Și undeva a apărut din nou `last_name` ca search criterion deși ieri trecuserăm pe `q` general. **Steering smart, fără sycophancy**:

> *"Cum ai ajuns la concluzia asta? Studiază ce informații ai avut, care te-au dus la această erezie de `last_name` în URL. Eliminăm această incorrectitudine din codul-sursă să nu mai presupui vreodată asta."*

Trei surse de halucinație identificate (subteran detective work cu AI-ul ca să le smulgi pe rând):
1. **Input-ul workshop-ului** din ieri menționa `Owner search improve` cu first/last
2. AI-ul a citit URL-ul actual `/owners?q=...` din cod și a confundat
3. **PetClinic original** (în training data al modelului) avea `last_name` search nativ

**Fix permanent în CLAUDE.md**: *"Înainte de orice artefact, verifică în T-code-ul real, nu presupune."* Pune-l ca skill sau direct în memorie de proiect — ca să nu mai facă drift.

🤖 **Pattern de steering**: NU spui "ai greșit". Devine sycophant și-ți dă dreptate fără să gândească. Spui *"Cum ai ajuns? Ce informații ai avut?"* — îl forțezi să refacă lanțul de raționament și să identifice presupunerea greșită singur.

### TOON — sfârșit de 2025, înlocuiește JSON

**Token Oriented Object Notation** ([toonformat.dev](https://toonformat.dev), [github](https://github.com/toon-format/toon)) — apărut decembrie 2025. Demo live pe [tokenizer.openai.com](https://platform.openai.com/tokenizer):

```
JSON same data: 85 tokens
TOON same data: 39 tokens   ← sub jumate
```

Format CSV-style cu header + linii: `users[3]{id,name,role}: 1,Alex,admin\n2,Maria,user\n...`. Avantaj: model nou (Sonnet 4.6+, lansat iulie 2025) îl recunoaște din training. Modele mai vechi îl deduc dar costă creier.

> *"Asta merită să aveți un slide. Asta e un hack-ăr de tokens."*

🤖 **Java vs Python token cost**: Java e ~3x mai scump decât Python pentru aceeași informație mentală — sintaxă verbose: `public static final`, type declarations, semicolons, getter-setter explicit. De-aia Victor a făcut training-assistant în Python deși preferă Java — *"oricum nu prea îl citesc, dă pentru AI."* Mai puțin tokens → mai mult buget pentru deep work.

### Ralph autonomous loop — la marginea SF

**Ralph Wiggum** by Geoffrey Huntley ([ghuntley.com/ralph](https://ghuntley.com/ralph/), [how-to repo](https://github.com/ghuntley/how-to-ralph-wiggum)). Există și **plugin-ul oficial Anthropic** [ralph-wiggum în claude-code](https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum). E o buclă autonomous esențial:

```bash
while :; do cat PROMPT.md | claude-code; done
```

Ralph rulează 6-8 ore peste noapte, execută teste E2E (Playwright/Selenium), load tests, auto-corectează regresii.

**Lifestyle Ralph** (anecdotă reală: omul care i-a mărturisit lui Victor în mall):
- **9-17**: alergi business-ul prin firmă cu laptopul sub-țioara, scrii spec-uri OpenSpec
- **17-21**: Ralph rulează implementarea autonom; tu mănânci/dormi
- **9 dimineața următoare**: review la cod și deploy-uri produse de Ralph

Reacții ale grupei (sentiment în sală): *"Periculos pentru creier — totul e deep thinking, partea boring (System 1) a dispărut."* / *"Faci munca a 2 oameni."* / *"Trebuie capacitate socială să alergi business-ul ore în șir."*

🤖 **Diagrama arhitectului veteran — System 1 vs System 2 thinking** (Kahneman's [Thinking, Fast and Slow](https://en.wikipedia.org/wiki/Thinking,_Fast_and_Slow)). Înainte: "băteam mapper la prost, fredonam muzică (System 1 - automatism), apoi găseai problema grea, te concentrai (System 2 - deep), apoi iar mecanic." AI-ul a **eliminat partea System 1** — totul e acum analiză și decizii. Concluzia ironică: 7 zile de Primilice... glumesc, dar **brain stamina se epuizează rapid** fără odihna mecanică. Strategy: 5 întrebări in-sphere + 2 în afara sferei pe zi, NU 50.

### Replace state vs push state pe URL

Pentru paginare/sort: **`router.navigate({...}, { replaceUrl: true })`** (NOT push). Push ar polua history-ul cu fiecare click pe sort/page → când dai back ai 50 de stări intermediare. Replace păstrează doar starea finală, history rămâne semantic util.

Un participant a prins asta primul (oracle al UX-ului grupei): *"Replace. Da, replace, push-urile ți-ar polua istoricul enorm."*

### Frontend UX polish — la AI-ul "spirit estetic"

Iterație live cu Cloud Code în IntelliJ via plugin (terminal command custom: un wrapper intern setat în Settings → Tools → Terminal):
- **"Ca românul scoate mânuța"** = `cursor: pointer` pe butoane dezactivate. Pune regula în `frontend/CLAUDE.md` ca să nu mai uite. Vorbește cu AI-ul în limbaj uman ("mânuță", nu "cursor: pointer") — *"e partener, nu rob, vorbești cu el ca cu un junior super-entuziast."*
- Alinieri: butoane page-size jos-stânga, paginare jos-dreapta, "10 din 43" pe același rând cu paginarea
- Toate butoanele dezactivate (first/prev pe pagina 1) → mai gri, **fără mânuță**
- În Java: **enums** pentru sort field și direction (vs `String` whitelist) — *"e mult mai simplu de scris și puțin cod."*

> 🤖 **Pattern UX**: nu polișa codul tu. Descrie comportamentul vrut. Sau și mai elegant: *"Făcea un coleg poze de ecran și muta el cu Ctrl+X bucata de imagine în alt loc, pe poză, ca să-i spună lui AI: vreau să arate așa."* Screenshot adnotat → AI face CSS-ul.

### LSP — find references pentru cod mort

OpenSpec a generat două variante de `search()` în repository (cu și fără pageable parametru) și a lăsat în urmă o metodă **never used**. **Soluția**: integrarea Cloud Code cu IntelliJ via [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) — agentul folosește IntelliJ inspections (`find references`) să detecteze cod neapelat. Fără LSP, AI-ul se teme să șteargă, lasă duplicări.

> *"Cloud-ul în IntelliJ are acces la inspecțiile inteligenței. E ca dinuțul tău cu poză la ecran — își dă seama ce se vede."*

🤖 **Setup LSP în Claude Code**: necesită Claude Code rulat **din terminalul IntelliJ** (nu standalone), iar plugin-ul Claude trebuie activ. Setting → `Tools → Terminal command` → wrapper-ul intern. Apoi când selectezi cod în editor, jos în Claude Code apare `1 line selected` (push-up via iconița în terminal panel).

### Glosar de termeni — domain-specific

Pentru domenii cu cuvinte multiple pentru același concept, faci un fișier `glossary.md` sau direct în CLAUDE.md. Exemplu din domeniul telecom:
- **Client** = persoana cu CNP (1:1)
- **Subscriber** = SIM ID (un client poate avea multe SIM-uri)
- **Customer** = cineva care a cumpărat o foto (ad-hoc, nelegat de CNP)

Fără glosar, AI-ul (și colegul nou) le confundă, scrie cod confuz. Glosar = anti-drift semantic.

### Cost reality check — spec-driven vs direct

Reacții la sfârșitul demo-ului OpenSpec:
- *"Consumator de tokens"* — DA, dar mai ieftin pe termen lung dacă eviți AI Spiraling (corecții drift, refacere). Unii din industrie au măsurat: spec-driven mediu < direct execution corectat
- *"Vreau să fac eu review la cod, nu să stau ca un televizor"* — fair, te ia ~1 lună să te obișnuiești
- *"Treci la coleg cu task-ul bifat"* — handover natural prin tasks.md cu `[x]`

> *"Specul e un markdown, gânduri scrise. Poți să-l muți la coleg care mai are token. Hai cu task-ul jumate făcut."*

### Ich Will — fii sigur pe ce vrei

**ich will** (germană: "Eu vreau") — atitudinea pe care trebuie să o ai cu AI. Nu îl rogi politicos. Tu vrei. Tu îl ghidezi cu **steering** ferm, palme până vezi că ce iese e decent de bun.

> *"Ești ca ciobanul. Cu palme, din steering, îl duci. Și până când vezi că ce iese e decent de bun."*

🤖 **Sfera ta de cunoștințe**: dacă AI-ul îți propune ceva 100% în afara sferei tale, te pune la shutdown. Învață **2 chestii noi pe zi** (5 in-sphere + 2 out-sphere). Nu te lăsa mitraliat cu acronyme; *"de fiecare dată să te întrebi o tâmpenie și să o înțelegi."*

---

## Citate / momente memorabile (Day 2)

> *"Erezia de Vibe: nu poți doar să tai. Sapi, răstorni pământul."* — pe AI drift din neatenție

> *"Brainstorming-ul e free QA pe spec. Storci creierii ÎNTÂI. Aruncă în AI doar după."*

> *"Asta cu apply-ul in flight (manual approval la fiecare modificare AI), mi se pare o greșeală. Te obligă să rămâi smirnă lângă el. Vreau să-mi văd altă treabă, să bag review pe codul final."*

> *"Specul e un markdown, gânduri scrise. Treci-l la coleg cu task-uri bifate când rămâi fără cotă."*

> *"Ich will. Tu vrei. Tu trebuie să vrei. Trebuie să știi pe ce vrei. Cu palme, din steering."*

> *"Mă, ce-ai făcut bobiță? Uite în ce hal arată de alinieri."* — Victor către Sonnet pe UX

> *"Diagrama arhitectului: înainte băteam mapper la prost, fredonam muzică, apoi găseam problema grea, ne concentram, apoi iar mecanic. AI-ul a eliminat partea boring."*

> *"Sentiment. Care e vibe-ul vostru cu analistul când vine cu requirements gol?"* — pe relația dev↔business

---

## Cum să combini OpenSpec cu Jira (vis declarat)

```
/brainstorm JIRA-1351
  → 3-5 întrebări să rușinezi/ajuți analistul (gol în requirements)

/openspec:propose JIRA-1351
  ├─ 10 min review pe proposal.md cu analistul/business
  └─ 10 min review pe design.md cu un coleg full-stack
```

🤖 **Setup necesar**: MCP de Jira ([sooperset/mcp-atlassian](https://github.com/sooperset/mcp-atlassian) sau unul custom intern) ca AI-ul să poată citi tichetul direct. Fără MCP, faci tu copy-paste varza din Jira.

---

## Tools / referințe noi în Day 2

- [OpenSpec](https://github.com/Fission-AI/OpenSpec) — spec-driven cu archive consolidation; brownfield-friendly
- [TOON format](https://toonformat.dev) ([repo](https://github.com/toon-format/toon)) — alternativa la JSON pentru tokens, decembrie 2025
- [OpenRewrite](https://docs.openrewrite.org/) — programatic Java migrations, gratis pe tokens
- [Ralph Wiggum loop](https://ghuntley.com/ralph/) — autonomous overnight cu bash one-liner ([how-to](https://github.com/ghuntley/how-to-ralph-wiggum), [oficial Anthropic plugin](https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum))
- [HumanLayer](https://www.humanlayer.dev/) — Dex Horthy ([github](https://github.com/dexhorthy)), context engineering thought leader
- [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) — Cloud Code în IntelliJ folosește pentru `find references` cod mort
- [Thinking, Fast and Slow](https://en.wikipedia.org/wiki/Thinking,_Fast_and_Slow) — Kahneman, dual-process theory aplicată pe AI-coding
- [Simon Willison](https://simonwillison.net/) — sursa termenilor "vibe coding" și "vibe heresy"

---

## Ce a rămas pentru altă dată

Conținutul planificat pentru după-amiază (din [agenda zilei 2](#)):

- **AI Guardrails** — code quality (linters, sonar, coverage), acceptance tests state-of-the-art, architecture (J Molecules, ArkUnit, C4 PlantUML extras din cod), layered guardrails (commit skill, Claude hooks, git hooks, CI)
- **AI Security** — Zero Data Retention, GDPR/SHREMS-2, prompt injection, supply chain (skill/MCP/slop-squatting), agent isolation (auto-mode, sandbox, Docker, microVM)
- **AI vs Legacy Code** — characterization tests, mutation tests, fuzzy tests, OpenRewrite refactor
- **Wrap-up** — Quiz cu AI, Personal Tutor ChatBot, surse cu signal/noise ridicat

Aceste teme sunt schițate doar parțial în notele lui Victor — pentru un overview complet, vezi cursul follow-up [`agentic.how`](https://agentic.how) sau ediția online IT Connect din 27 mai / 2 iunie.

<!-- SUMMARIZER: last_processed=2026-05-05T12:35 lines_through=1066 -->
