## Agent-Orchestrierung · deterministische Gates · KI-gestützte Entwicklung

[English](README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [繁體中文](README.zh-TW.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · **Deutsch** · [Français](README.fr.md) · [Русский](README.ru.md) · [Italiano](README.it.md) · [Bahasa Indonesia](README.id.md) · [Türkçe](README.tr.md)

Codebasierte Automatisierung und agenten-orchestrierte Systeme: eine Agenten-Plattform mit
fail-closed Berechtigungsschicht, lokale Retrieval-Pipelines mit Evaluations-Gates und
deterministische Simulationen, deren Replays Bit für Bit identisch sind.

Jede Zahl auf dieser Seite ist eine Messung, keine Schätzung. Der Befehl, der sie erzeugt hat,
liegt in dem Repo, das sie beschreibt, und das Repo prüft sie vor jeder Veröffentlichung erneut.

---

## Flaggschiff-Projekte

| Projekt | Was es ist | Der gemessene Fakt | Live |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | Local-first-Orchestrierungsplattform für begrenzte Agentenarbeit: ein Swift-Kontroll-Daemon für macOS hinter fail-closed Berechtigungs-Gates, gesteuert von einem TypeScript-Orchestrator über MCP. *(Plattform)* | **63 dokumentierte MCP-Tools** in `docs/TOOL_SURFACE.json`, **234 Test-/Spec-Dateien**, **15 ADRs** und ein Remote-Approval-Broker, dessen Freigaben an einen Fingerprint gebunden und nur einmal verwendbar sind (ADR-003). | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | Koreanisches RAG für netzgetrennte (망분리) Umgebungen: hybrides Retrieval rein aus der Standardbibliothek, PII-Maskierung und ein Refusal-Gate für Fragen außerhalb des Geltungsbereichs. *(Tool)* | **0 Drittanbieter-Abhängigkeiten**, und der Build wird durch ein **Golden Set mit 50 Fragen** abgesichert: hit@3 **100 %** (39/39), Refusal-Precision **100 %** / Recall **81.8 %** — und Zitationsgenauigkeit **36.75 %**, die schwache Metrik, veröffentlicht statt versteckt. | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | Die SQL-Arbeit eines Lifecycle-/CRM-Marketers, erledigt von einem Agenten über MCP, hinter einem Governance-Kernel, der entscheidet, wie weit der Agent gehen darf und was protokolliert wird. *(Tool)* | **108 Tests bestehen** (mit installiertem MCP SDK), darunter **33 PII-Red-Team-** und **12 Governance-Red-Team-Angriffe** gegen den Kernel. Die Daten sind **100 % synthetisch** und byte-reproduzierbar: Seed 42 → identischer SQLite-Digest, 5.000 Kunden / 99.922 Events. | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | Taktischer Breach-Planer und Echtzeit-Squad-Simulation im Browser; ein abgeschlossener Raid lässt sich als URL teilen, die sich im Browser anderer erneut simuliert. *(spielbarer Build, keine kommerzielle Veröffentlichung)* | **30-Hz-Fixed-Tick-Simulation** mit einem Purity-Gate, das **11 nicht-deterministische APIs** aus den Sim- und Content-Paketen verbannt — es besteht, und genau deshalb replayen Seed + Input-Log identisch. **55 Test-/Spec-Dateien** und 40 Skripte in `tools/`, davon 35 Headless-Harnesses (die übrigen 5 sind Asset-Fetcher und Codegen). | **[spielen](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | Ein deterministisches Reasoning-Gerät: 8 MCP-Tools, die einen Scope-Lock, einen Plan mit dem günstigsten widerlegenden Schritt, einen Blind-Spot-Sweep und ein Verdict-Gate erzwingen, das jede Behauptung ohne Ausführungsnachweis zurückweist. *(Tool)* | **0 Laufzeit-Abhängigkeiten**, **33/33 Tests bestehen** — und einer dieser Tests ist die Garantie selbst: Die gesamte Oberfläche führt keine Dateisystem-Schreibzugriffe, keine Prozess-Spawns und keine Netzwerkaufrufe aus. | — |

---

## Wie diese Projekte entstehen

Das Interessante ist nicht „mit KI gebaut“. Es ist das, was *zwischen* dem Modell und dem Repository sitzt.

- **Zwei Coding-Agenten, ein deterministischer Schiedsrichter.** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  ist ein externer Treiber, der zwei Agent-CLIs so lange gegeneinander laufen lässt, bis ein Gate besteht.
  Das Gate entscheidet über „fertig“ — nie die Selbsteinschätzung eines Agenten. 12 Unit-Tests, die mit einem
  auf die Systemverzeichnisse reduzierten `PATH` bestehen, sodass die Verweigerungen des Treibers ohne
  installierte Agenten beweisbar sind.
- **Deterministische Gates laufen, bevor das Modell überhaupt aufgerufen wird.** Purity-Checker statt Bauchgefühl:
  Das Sim-Gate von fatal-funnel verbannt 11 nicht-deterministische APIs; der Read-only-Test von reasonforge
  erzwingt 6 verbotene Muster. Auch Retrieval-Qualität ist ein Build-Gate — ein Golden Set plus Schwellenwerte,
  die den Lauf fehlschlagen lassen, sobald sie regressieren ([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)).
- **UI-Behauptungen stammen aus Probes und Screenshots, nicht aus dem Gedächtnis.** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  liefert die Harnesses `shot`, `relic-qa` und `mobile-qa` mit; fatal-funnel hat 35 Headless-Harnesses unter den
  40 Skripten in `tools/` sowie 4 fest verdrahtete Screenshot-Kameras. Diese Lektion wurde auf die teure Art
  gelernt: Ein Tooltip, der „funktioniert“, kann hinter einem einzigen Input-Filter-Flag tot sein; eine
  synthetische Input-Probe fängt das ab, Code-Lesen nicht.
- **Assets sind lizenz-gegated, bevor sie generiert werden.** [assetforge](https://github.com/euuuuuuan/assetforge-public)
  verweigert die Generierung mit jedem Modell, das nicht in seiner Registry steht. Nachgelagert tragen
  **alle 27 veröffentlichten Snapshots eine `CREDITS.md` mit einer 4-stufigen Redistributions-Tabelle** —
  wer forkt, weiß also genau, was entfernt werden muss.
- **Ehrliche Labels, per Schema erzwungen.** In [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  ist jede veröffentlichte Metrik als `kind: z.enum(['measured', 'estimated'])` typisiert. Eine Zahl kann nicht
  veröffentlicht werden, ohne zu deklarieren, welches von beiden sie ist. Diese Regel ist der Grund, warum auf
  dieser Seite keine runden Marketing-Zahlen stehen.
- **Veröffentlichung ist eine Pipeline, kein Copy-Paste.** Jedes öffentliche Repo hier entsteht durch einen
  wiederholbaren, sanitisierenden Publish, dessen Claims-Datei jede Größe im README erneut ausführt und keinen
  Commit erzeugt, wenn eine davon abweicht. Über die 27 Snapshots hinweg sind das **629 Test-/Spec-Dateien**.

---

## Ausgewählte Arbeiten

Nicht alle 27 Repos — sondern die, die eine eigenständige Fähigkeit zeigen.

**Browser-Spiele (Link öffnen und spielen)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — deterministische Taktik-Sim, 40 Waffen, 12 handgefertigte Missionen · [live](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — kompaktes Action-RPG: Parieren, Relikte, zwei Zonen. *Vertical Slice bei v0.5.1* · [live](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — kleines, vollständiges Idle-Game über Personalschlüssel, 18 Test-Dateien · [live](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — isometrischer ARPG-Slice. **Teilt seinen deterministischen Simulationskern mit hollowmere**; Kampfregeln, Encounter und Inhalte sind eigenständig. · [live](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — isometrische Drei-Wellen-Arena: den Telegraph lesen, den Mooncut verketten, dem angesetzten Schlag per Dash ausweichen. **43 Test-Dateien** und das strengste Asset-Ledger des Sets — 100 SHA-256-Zeilen (eine pro Datei), alles CC0. · [live](https://moonshard-warden.vercel.app)

**Engine-Spiele (Godot / Unity — Vertical Slices und Prototypen, keines kommerziell veröffentlicht)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — Großkampfschiff-Artillerie im Weltraum: Geschossflugzeit, inertialer Vorhalt, Durchschlag nach Panzerungswinkel, auf einer deterministischen 30-Hz-Sim (27 Test-Dateien)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — Solo-Bossraid-ARPG, zehn Bosse mit je drei Phasen; die Behauptung „der Raid trägt dich nie“ ist eine nach dem Kampf gemessene Statistik, keine erzwungene Invariante — landet ein Verbündeter den finalen Schlag, steht das auch so im Run-Report (33 Test-Dateien)
- **[todak](https://github.com/euuuuuuan/todak-public)** — Desktop-Pixel-Haustiere, die am unteren Bildschirmrand leben (36 Test-Dateien)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)** (30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)** (29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + sein **[Unity-Port](https://github.com/euuuuuuan/driftfolk-unity-public)** — der Sinn des Ports ist numerische Parität mit dem Original · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — Battle Royale mit dirigierter Autonomie, Unity. Separat gelistet, weil dieser Snapshot ein **Verifikations-Skelett und kein spielbarer Slice** ist: Die deterministische Sim und ihr Golden-File-Harness sind der ausgelieferte Teil, und 11 von 15 nummerierten Soak-Assertions stehen noch aus.

**KI- und Agenten-Tooling**
- **[baton](https://github.com/euuuuuuan/baton-public)** — die Agenten-Plattform: MCP-Oberfläche mit 63 Tools, Approval-Broker, 16 Orchestrator-Pakete, 90 Swift-Quelldateien
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — Read-only-Reasoning-Harness, 8 MCP-Tools, 0 Abhängigkeiten
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — gate-terminierte Schleife zwischen zwei Coding-Agenten
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — lokale Asset-Generierung zum Nulltarif hinter einem fail-closed Lizenz-Gate
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — generieren → beurteilen → komponieren → veröffentlichen, wobei „nicht sicher“ den Lauf parkt, statt ihn auszuliefern

**Daten-, Backend- und Domänen-Tooling**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — mandantenfähige Asset-Verwaltung, bei der die Mandantengrenze in PostgreSQL liegt: **82 Row-Level-Security-Policies** über 11 Migrationsdateien, mit 6 genau darauf gerichteten pgTAP-Testdateien — kein Filtern in der Anwendungsschicht
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — Retrieval, bei dem das Evaluations-Gate der eigentliche Punkt ist
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — Agent + Governance-Kernel über einer vollständig synthetischen CDP
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — Erstprüfungs-Linter für koreanische Finanz-/Versicherungs-Werbetexte. Alle 5 ausgelieferten Regelsätze sind in den Daten selbst als `"verified": false` markiert, und der Report lässt sich nicht ohne seinen Disclaimer ausgeben: Er markiert Texte für die menschliche Prüfung, er zertifiziert keine Compliance
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — die Instrumente spielen oder die Patterns schreiben; derselbe Song, verlustfrei von beiden Seiten editierbar (27 Test-Dateien)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — zweisprachige statische Site, auf der jede Zahl measured vs. estimated deklariert · [live](https://euuuuuuan.pages.dev)

---

## Über diese Repos

Alle 27 Repos sind sanitisierte Snapshots: Code unter Apache-2.0, Assets pro Repo geregelt durch eine
4-stufige `CREDITS.md`. Die Spiele sind persönliche Builds und Vertical Slices — spielbar, mit Gates
abgesichert und ehrlich gelabelt, keine kommerziellen Veröffentlichungen.

**Kontakt:** einfach ein Issue oder eine Discussion in einem beliebigen Repo hier eröffnen.
