## Orchestration d'agents · gates déterministes · développement augmenté par l'IA

[English](README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [繁體中文](README.zh-TW.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Deutsch](README.de.md) · **Français** · [Русский](README.ru.md) · [Italiano](README.it.md) · [Bahasa Indonesia](README.id.md) · [Türkçe](README.tr.md)

Automatisation par le code et systèmes orchestrés par agents : une plateforme d'agents dotée d'une
couche de permissions fail-closed, des pipelines de retrieval locaux avec gates d'évaluation, et des
simulations déterministes qui se rejouent bit à bit.

Chaque nombre sur cette page est une mesure, pas une estimation. La commande qui l'a produit vit dans
le dépôt qu'il décrit, et le dépôt le revérifie avant chaque publication.

---

## Projets phares

| Projet | Ce que c'est | Le fait mesuré | Live |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | Plateforme d'orchestration local-first pour du travail d'agents borné : un démon de contrôle macOS en Swift derrière des gates de permission fail-closed, piloté par un orchestrateur TypeScript via MCP. *(plateforme)* | **63 outils MCP documentés** dans `docs/TOOL_SURFACE.json`, **234 fichiers de test/spec**, **15 ADR**, et un courtier d'approbation distant dont les autorisations sont liées à une empreinte et à usage unique (ADR-003). | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | RAG coréen conçu pour les environnements à réseau cloisonné (망분리) : retrieval hybride en pure stdlib, masquage des PII et une gate de refus pour les questions hors périmètre. *(outil)* | **0 dépendance tierce**, et le build est gardé par un **golden set de 50 questions** : hit@3 **100 %** (39/39), précision de refus **100 %** / rappel **81.8 %** — et exactitude des citations **36.75 %**, la métrique faible, publiée plutôt que cachée. | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | Le travail SQL d'un marketeur CRM/cycle de vie effectué par un agent via MCP, derrière un noyau de gouvernance qui décide jusqu'où l'agent peut aller et ce qui est consigné. *(outil)* | **108 tests passent** (avec le SDK MCP installé), dont **33 attaques red-team PII** et **12 attaques red-team de gouvernance** contre le noyau. Les données sont **100 % synthétiques** et reproductibles à l'octet près : seed 42 → digest SQLite identique, 5000 clients / 99922 événements. | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | Planificateur tactique de breach et simulation d'escouade en temps réel dans le navigateur ; un raid terminé se partage sous forme d'URL qui se re-simule dans le navigateur de quelqu'un d'autre. *(build jouable, pas une sortie commerciale)* | **Simulation à tick fixe de 30 Hz** avec une gate de pureté qui bannit **11 API non déterministes** de la sim et des packages de contenu — elle passe, et c'est ce qui permet à seed + journal d'entrées de se rejouer à l'identique. **55 fichiers de test/spec**, et 40 scripts dans `tools/` dont 35 sont des harnais headless (les 5 autres sont des récupérateurs d'assets et du codegen). | **[jouer](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | Un dispositif de raisonnement déterministe : 8 outils MCP qui imposent un verrouillage du périmètre, un plan par l'étape infirmante la moins coûteuse, un balayage des angles morts, et une gate de verdict qui rejette toute affirmation sans preuve d'exécution. *(outil)* | **0 dépendance à l'exécution**, **33/33 tests passent** — et l'un de ces tests constitue la garantie : la surface entière n'effectue aucune écriture sur le système de fichiers, ne lance aucun processus et n'émet aucun appel réseau. | — |

---

## Comment ces projets sont construits

La partie intéressante n'est pas « construit avec l'IA ». C'est ce qui se trouve *entre* le modèle et le dépôt.

- **Deux agents de codage, un arbitre déterministe.** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  est un pilote externe qui fait tourner deux CLI d'agents l'un contre l'autre jusqu'à ce qu'une gate passe.
  C'est la gate qui décide « terminé » — jamais l'opinion d'un agent sur son propre travail. 12 tests
  unitaires, qui passent avec `PATH` réduit aux répertoires système, de sorte que les refus du pilote sont
  prouvables sans qu'aucun des deux agents ne soit installé.
- **Les gates déterministes s'exécutent avant même que le modèle soit appelé.** Des vérificateurs de
  pureté, pas du ressenti : la gate de sim de fatal-funnel bannit 11 API non déterministes ; le test
  read-only de reasonforge fait respecter 6 motifs interdits. La qualité du retrieval est elle aussi une
  gate de build — un golden set plus des seuils qui font échouer l'exécution en cas de régression
  ([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)).
- **Les affirmations sur l'UI viennent de sondes et de captures d'écran, pas de mémoire.** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  embarque des harnais `shot`, `relic-qa` et `mobile-qa` ; fatal-funnel compte 35 harnais headless parmi les 40 scripts de `tools/`
  et 4 caméras de capture d'écran fixes. Cette leçon a été apprise au prix fort : un tooltip qui « fonctionne »
  peut être mort derrière un seul flag de filtre d'entrée ; une sonde d'entrée synthétique le détecte, la
  lecture du code non.
- **Les assets passent une gate de licence avant d'être générés.** [assetforge](https://github.com/euuuuuuan/assetforge-public)
  refuse de générer à partir de tout modèle absent de son registre. En aval, **les 27 snapshots publiés
  embarquent tous un `CREDITS.md` avec une table de redistribution à 4 niveaux** — quiconque forke sait
  donc exactement ce qu'il doit retirer.
- **Des étiquettes honnêtes, imposées par un schéma.** Dans [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  chaque métrique publiée est typée `kind: z.enum(['measured', 'estimated'])`. Un nombre ne peut pas être
  publié sans déclarer duquel des deux il s'agit. C'est cette règle qui explique l'absence de chiffres
  marketing ronds sur cette page.
- **La publication est un pipeline, pas un copier-coller.** Chaque dépôt public ici est produit par une
  publication assainissante répétable dont le fichier de claims ré-exécute chaque quantité du README et
  refuse de créer un commit si l'une d'elles dérive. Sur les 27 snapshots, cela représente **629 fichiers
  de test/spec**.

---

## Sélection de projets

Pas les 27 dépôts — ceux qui montrent une capacité distincte.

**Jeux navigateur (ouvrez le lien et jouez)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — sim tactique déterministe, 40 armes, 12 missions écrites à la main · [live](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — action-RPG compact : parade, reliques, deux zones. *Vertical slice en v0.5.1* · [live](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — petit jeu idle complet sur les ratios d'effectifs, 18 fichiers de test · [live](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — slice d'ARPG isométrique. **Partage son cœur de simulation déterministe avec hollowmere** ; les règles de combat, les rencontres et le contenu lui sont propres. · [live](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — arène isométrique en trois vagues : lire le telegraph, enchaîner le mooncut, esquiver en dash la frappe engagée. **43 fichiers de test** et le registre d'assets le plus strict du lot — 100 lignes SHA-256 par fichier, le tout en CC0. · [live](https://moonshard-warden.vercel.app)

**Jeux moteur (Godot / Unity — vertical slices et prototypes, aucun sorti commercialement)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — artillerie de vaisseaux capitaux dans l'espace : temps de vol des obus, anticipation inertielle, pénétration selon l'angle de blindage, sur une sim déterministe à 30 Hz (27 fichiers de test)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — ARPG de raid de boss en solo, dix boss à trois phases ; l'affirmation « le raid ne vous porte jamais » est une statistique post-combat mesurée, pas un invariant imposé — si un allié porte le coup fatal, le rapport de run le dit (33 fichiers de test)
- **[todak](https://github.com/euuuuuuan/todak-public)** — animaux de compagnie en pixel art qui vivent le long du bord inférieur de votre écran (36 fichiers de test)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)** (30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)** (29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + son **[portage Unity](https://github.com/euuuuuuan/driftfolk-unity-public)** — tout l'intérêt du portage est la parité numérique avec l'original · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — battle royale à autonomie dirigée, sous Unity. Listé à part parce que ce snapshot est un **squelette de vérification, pas une slice jouable** : la sim déterministe et son harnais de golden files sont la partie livrée, et 11 des 15 assertions de soak numérotées restent en attente.

**Outillage IA et agents**
- **[baton](https://github.com/euuuuuuan/baton-public)** — la plateforme d'agents : surface MCP de 63 outils, courtier d'approbation, 16 packages d'orchestrateur, 90 sources Swift
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — harnais de raisonnement read-only, 8 outils MCP, 0 dépendance
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — boucle entre deux agents de codage, terminée par une gate
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — génération d'assets locale à coût nul derrière une gate de licence fail-closed
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — générer → juger → composer → publier, où « pas sûr » met le run en attente au lieu de le livrer

**Données, backend et outillage métier**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — administration d'actifs multi-tenant où la frontière entre tenants vit dans PostgreSQL : **82 politiques row-level security** réparties sur 11 fichiers de migration, avec 6 fichiers de test pgTAP qui les ciblent — pas du filtrage en couche applicative
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — du retrieval dont la gate d'évaluation est la raison d'être
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — agent + noyau de gouvernance sur un CDP entièrement synthétique
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — linter de première passe pour textes publicitaires coréens des secteurs finance/assurance. Les 5 jeux de règles livrés sont marqués `"verified": false` dans les données elles-mêmes et le rapport ne peut pas s'imprimer sans son avertissement : il signale des textes pour revue humaine, il ne certifie pas la conformité
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — jouez les instruments ou écrivez les patterns ; le même morceau, édité sans perte des deux côtés (27 fichiers de test)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — site statique bilingue où chaque nombre se déclare mesuré ou estimé · [live](https://euuuuuuan.pages.dev)

---

## À propos de ces dépôts

Les 27 dépôts sont des snapshots assainis : code sous Apache-2.0, assets régis par dépôt via un
`CREDITS.md` à 4 niveaux. Les jeux sont des builds personnels et des vertical slices — jouables, gardés
par des gates et étiquetés honnêtement, pas des sorties commerciales.

**Contact :** ouvrez une issue ou une discussion sur n'importe quel dépôt ici.
