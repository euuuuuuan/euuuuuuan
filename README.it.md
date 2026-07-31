## Orchestrazione di agenti · gate deterministici · sviluppo potenziato dall'AI

[English](README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [繁體中文](README.zh-TW.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Русский](README.ru.md) · **Italiano** · [Bahasa Indonesia](README.id.md) · [Türkçe](README.tr.md)

Automazione basata su codice e sistemi orchestrati da agenti: una piattaforma di agenti con un
layer di permessi fail-closed, pipeline di retrieval locali con gate di valutazione e simulazioni
deterministiche il cui replay è identico bit per bit.

Ogni numero in questa pagina è una misurazione, non una stima. Il comando che l'ha prodotto vive nel
repo che descrive, e il repo lo riverifica prima di ogni pubblicazione.

---

## Lavori di punta

| Progetto | Che cos'è | Il dato misurato | Live |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | Piattaforma di orchestrazione local-first per lavoro agentico delimitato: un daemon di controllo macOS in Swift dietro gate di permessi fail-closed, pilotato da un orchestratore TypeScript via MCP. *(piattaforma)* | **63 tool MCP documentati** in `docs/TOOL_SURFACE.json`, **234 file di test/spec**, **15 ADR** e un broker di approvazioni remoto i cui grant sono vincolati al fingerprint e monouso (ADR-003). | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | RAG in coreano progettato per ambienti a rete segregata (망분리): retrieval ibrido in pura standard library, mascheramento dei PII e un gate di rifiuto per le domande fuori ambito. *(strumento)* | **0 dipendenze di terze parti**, e la build è vincolata a un **golden set di 50 domande**: hit@3 **100 %** (39/39), precisione dei rifiuti **100 %** / recall **81,8 %** — e accuratezza delle citazioni **36,75 %**, la metrica debole, pubblicata anziché nascosta. | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | Il lavoro SQL di un marketer lifecycle/CRM svolto da un agente via MCP, dietro un kernel di governance che decide fin dove l'agente può spingersi e che cosa viene messo per iscritto. *(strumento)* | **108 test superati** (con l'SDK MCP installato), inclusi **33 attacchi red-team sui PII** e **12 red-team di governance** contro il kernel. I dati sono **100 % sintetici** e riproducibili byte per byte: seed 42 → digest SQLite identico, 5.000 clienti / 99.922 eventi. | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | Pianificatore tattico di irruzione e sim di squadra in tempo reale nel browser; un raid completato si condivide come URL che si ri-simula nel browser di qualcun altro. *(build giocabile, non una release commerciale)* | **Simulazione a tick fisso a 30 Hz** con un gate di purezza che bandisce **11 API non deterministiche** dai package di sim e contenuti — e il fatto che passi è ciò che rende identico il replay di seed + log di input. **55 file di test/spec** e 40 script in `tools/`, di cui 35 sono harness headless (gli altri 5 sono fetcher di asset e codegen). | **[gioca](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | Un dispositivo di ragionamento deterministico: 8 tool MCP che impongono un lock dello scope, un piano basato sul passo disconfermante più economico, uno sweep dei punti ciechi e un verdict gate che respinge le affermazioni prive di prova di esecuzione. *(strumento)* | **0 dipendenze a runtime**, **33/33 test superati** — e uno di quei test è la garanzia stessa: l'intera superficie non effettua scritture su filesystem, non lancia processi e non fa chiamate di rete. | — |

---

## Come sono costruiti

La parte interessante non è «costruito con l'AI». È ciò che sta *tra* il modello e il repository.

- **Due coding agent, un arbitro deterministico.** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  è un driver esterno che fa girare due CLI di agenti l'una contro l'altra finché un gate non passa. È il gate
  a decidere quando è «fatto» — mai l'opinione di un agente sul proprio lavoro. 12 unit test, superati con il
  `PATH` ridotto alle sole directory di sistema, così i rifiuti del driver sono dimostrabili senza che nessuno
  dei due agenti sia installato.
- **I gate deterministici girano prima ancora che il modello venga chiamato.** Verificatori di purezza, non
  sensazioni: il gate di sim di fatal-funnel bandisce 11 API non deterministiche; il test read-only di
  reasonforge impone 6 pattern vietati. Anche la qualità del retrieval è un gate di build — un golden set più
  soglie che fanno fallire la run quando regrediscono ([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)).
- **Le affermazioni sulla UI vengono da probe e screenshot, non dalla memoria.** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  include gli harness `shot`, `relic-qa` e `mobile-qa`; fatal-funnel ha 35 harness headless tra i 40 script in `tools/`
  e 4 camere fisse per gli screenshot. Lezione imparata nel modo più costoso: un tooltip che «funziona» può essere
  morto dietro un singolo flag di input-filter; una probe di input sintetico lo scopre, leggere il codice no.
- **Gli asset passano un gate di licenza prima ancora di essere generati.** [assetforge](https://github.com/euuuuuuan/assetforge-public)
  si rifiuta di generare da qualsiasi modello assente dal suo registro. A valle, **tutti i 27 snapshot pubblicati
  portano un `CREDITS.md` con una tabella di ridistribuzione a 4 livelli** — così chi fa un fork sa esattamente
  che cosa deve rimuovere.
- **Etichette oneste, imposte da uno schema.** In [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  ogni metrica pubblicata è tipizzata `kind: z.enum(['measured', 'estimated'])`. Un numero non può essere
  pubblicato senza dichiarare quale dei due è. È questa regola il motivo per cui in questa pagina non ci sono
  cifre di marketing arrotondate.
- **La pubblicazione è una pipeline, non un copia-incolla.** Ogni repo pubblico qui presente è prodotto da una
  publish di sanitizzazione ripetibile, il cui file dei claim ri-esegue ogni quantità del README e si rifiuta di
  creare un commit se una di esse deriva. Sui 27 snapshot fanno **629 file di test/spec**.

---

## Lavori selezionati

Non tutti i 27 repo — quelli che mostrano una capacità distinta.

**Giochi browser (apri il link e gioca)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — sim tattica deterministica, 40 armi, 12 missioni scritte a mano · [live](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — action RPG compatto: parry, reliquie, due zone. *Vertical slice alla v0.5.1* · [live](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — piccolo idle game completo sui rapporti di organico, 18 file di test · [live](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — slice di ARPG isometrico. **Condivide il core di simulazione deterministico con hollowmere**; regole di combattimento, encounter e contenuti sono suoi. · [live](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — arena isometrica a tre ondate: leggi il telegraph, concatena il mooncut, schiva col dash il colpo ormai partito. **43 file di test** e il ledger di asset più rigido del gruppo — 100 righe SHA-256, una per file, tutto CC0. · [live](https://moonshard-warden.vercel.app)

**Giochi su engine (Godot / Unity — vertical slice e prototipi, nessuno rilasciato commercialmente)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — artiglieria di navi capitali nello spazio: tempo di volo dei proiettili, anticipo inerziale, penetrazione in funzione dell'angolo di corazza, su una sim deterministica a 30 Hz (27 file di test)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — ARPG boss-raid in solitaria, dieci boss a tre fasi; l'affermazione «il raid non ti porta mai in spalla» è una statistica post-combattimento misurata, non un invariante imposto — se un alleato mette a segno il colpo di grazia, il report della run lo dice (33 file di test)
- **[todak](https://github.com/euuuuuuan/todak-public)** — pet pixel da desktop che vivono lungo il bordo inferiore dello schermo (36 file di test)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)** (30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)** (29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + il suo **[port Unity](https://github.com/euuuuuuan/driftfolk-unity-public)** — il senso del port è la parità numerica con l'originale · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — battle royale ad autonomia diretta, Unity. Elencato a parte perché questo snapshot è uno **scheletro di verifica, non una slice giocabile**: la sim deterministica e il suo harness golden-file sono la parte consegnata, e 11 delle 15 asserzioni soak numerate sono ancora in sospeso.

**Tooling AI e per agenti**
- **[baton](https://github.com/euuuuuuan/baton-public)** — la piattaforma di agenti: superficie MCP da 63 tool, broker di approvazioni, 16 package dell'orchestratore, 90 sorgenti Swift
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — harness di ragionamento read-only, 8 tool MCP, 0 dipendenze
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — loop tra due coding agent terminato da un gate
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — generazione di asset locale a costo zero dietro un gate di licenza fail-closed
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — generate → judge → compose → publish, dove «non sono sicuro» parcheggia la run invece di spedirla

**Dati, backend e tooling di dominio**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — amministrazione di asset multi-tenant in cui il confine tra tenant vive in PostgreSQL: **82 policy row-level-security** distribuite su 11 file di migrazione, con 6 file di test pgTAP puntati contro di esse — niente filtri a livello applicativo
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — retrieval in cui il punto è il gate di valutazione
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — agente + kernel di governance su un CDP interamente sintetico
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — linter di prima passata per copy pubblicitario finanziario/assicurativo coreano. Tutti i 5 ruleset consegnati sono marcati `"verified": false` nei dati stessi e il report non può essere stampato senza il suo disclaimer: segnala il copy per revisione umana, non certifica la conformità
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — suona gli strumenti o scrivi i pattern; la stessa canzone, modificata senza perdite da entrambi i lati (27 file di test)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — sito statico bilingue in cui ogni numero dichiara se è misurato o stimato · [live](https://euuuuuuan.pages.dev)

---

## A proposito di questi repo

Tutti i 27 repo sono snapshot sanitizzati: codice sotto Apache-2.0, asset regolati repo per repo da un
`CREDITS.md` a 4 livelli. I giochi sono build personali e vertical slice — giocabili, protetti da gate e
etichettati con onestà, non release commerciali.

**Contatti:** apri una issue o una discussion su uno qualsiasi dei repo qui presenti.
