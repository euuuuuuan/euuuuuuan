## Orquestração de agentes · gates determinísticos · desenvolvimento potencializado por IA

[English](README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [繁體中文](README.zh-TW.md) · [Español](README.es.md) · **Português (BR)** · [Deutsch](README.de.md) · [Français](README.fr.md) · [Русский](README.ru.md) · [Italiano](README.it.md) · [Bahasa Indonesia](README.id.md) · [Türkçe](README.tr.md)

Automação baseada em código e sistemas orquestrados por agentes: uma plataforma de agentes com uma
camada de permissões fail-closed, pipelines locais de recuperação com gates de avaliação e simulações
determinísticas que fazem replay bit a bit.

Cada número desta página é uma medição, não uma estimativa. O comando que o produziu vive no
repositório que ele descreve, e o repositório o reverifica antes de publicar.

---

## Trabalhos em destaque

| Projeto | O que é | O fato medido | Ao vivo |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | Plataforma de orquestração local-first para trabalho de agentes com escopo delimitado: um daemon de controle em Swift para macOS atrás de gates de permissão fail-closed, dirigido por um orquestrador em TypeScript via MCP. *(plataforma)* | **63 ferramentas MCP documentadas** em `docs/TOOL_SURFACE.json`, **234 arquivos de teste/spec**, **15 ADRs** e um broker de aprovação remota cujas concessões são vinculadas a fingerprint e de uso único (ADR-003). | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | RAG em coreano construído para ambientes com rede segregada (망분리): recuperação híbrida usando só a stdlib, mascaramento de PII e um gate de recusa para perguntas fora de escopo. *(ferramenta)* | **0 dependências de terceiros**, e o build é controlado por um **golden set de 50 perguntas**: hit@3 **100 %** (39/39), precisão de recusa **100 %** / recall **81.8 %** — e acurácia de citação **36.75 %**, a métrica fraca, publicada em vez de escondida. | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | O trabalho de SQL de um profissional de lifecycle/CRM feito por um agente via MCP, atrás de um kernel de governança que decide até onde o agente pode ir e o que fica registrado. *(ferramenta)* | **108 testes passam** (com o SDK do MCP instalado), incluindo **33 ataques de red team de PII** e **12 de red team de governança** contra o kernel. Os dados são **100 % sintéticos** e reproduzíveis byte a byte: seed 42 → digest SQLite idêntico, 5.000 clientes / 99.922 eventos. | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | Planejador tático de invasão e simulador de esquadrão em tempo real no navegador; um raid concluído é compartilhado como uma URL que se re-simula no navegador de outra pessoa. *(build jogável, não um lançamento comercial)* | **Simulação de tick fixo a 30 Hz** com um gate de pureza que bane **11 APIs não determinísticas** dos pacotes de simulação e de conteúdo — ele passa, e é isso que faz seed + log de entradas reproduzirem o replay de forma idêntica. **55 arquivos de teste/spec**, e 40 scripts em `tools/`, dos quais 35 são harnesses headless (os outros 5 são fetchers de assets e codegen). | **[jogar](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | Um dispositivo de raciocínio determinístico: 8 ferramentas MCP que forçam um travamento de escopo, um plano do passo desconfirmador mais barato, uma varredura de pontos cegos e um gate de veredito que rejeita qualquer alegação sem prova de execução. *(ferramenta)* | **0 dependências de runtime**, **33/33 testes passam** — e um desses testes é a garantia: toda a superfície não faz escritas no sistema de arquivos, não cria processos e não faz chamadas de rede. | — |

---

## Como isso é construído

A parte interessante não é "feito com IA". É o que fica *entre* o modelo e o repositório.

- **Dois agentes de código, um árbitro determinístico.** O [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  é um driver externo que roda duas CLIs de agente uma contra a outra até um gate passar. É o gate que decide
  o "pronto" — nunca a opinião de um agente sobre o próprio trabalho. 12 testes unitários, passando com o `PATH`
  reduzido aos diretórios do sistema, de modo que as recusas do driver são prováveis sem nenhum dos agentes instalados.
- **Gates determinísticos rodam antes de o modelo sequer ser chamado.** Verificadores de pureza, não intuição:
  o gate de simulação do fatal-funnel bane 11 APIs não determinísticas; o teste read-only do reasonforge impõe 6
  padrões proibidos. Qualidade de recuperação também é gate de build — um golden set mais limiares que derrubam a
  execução quando regridem ([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)).
- **Alegações sobre UI vêm de probes e screenshots, não da memória.** O [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  entrega os harnesses `shot`, `relic-qa` e `mobile-qa`; o fatal-funnel tem 35 harnesses headless entre os 40 scripts em `tools/`
  e 4 câmeras de screenshot fixas. Essa lição foi aprendida do jeito caro: um tooltip que "funciona" pode estar morto
  atrás de uma única flag de filtro de entrada; um probe de entrada sintética pega isso, ler o código não.
- **Assets passam por gate de licença antes de serem gerados.** O [assetforge](https://github.com/euuuuuuan/assetforge-public)
  se recusa a gerar a partir de qualquer modelo ausente do seu registro. Mais adiante no pipeline, **todos os 27 snapshots publicados
  trazem um `CREDITS.md` com uma tabela de redistribuição de 4 níveis** — assim, quem fizer fork sabe exatamente o que
  precisa remover.
- **Rótulos honestos, impostos por um schema.** No [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  toda métrica publicada é tipada como `kind: z.enum(['measured', 'estimated'])`. Um número não pode ser publicado
  sem declarar qual dos dois ele é. Essa regra é o motivo de esta página não ter nenhum número redondo de marketing.
- **Publicar é um pipeline, não um copia-e-cola.** Cada repositório público aqui é produzido por uma publicação
  sanitizada e repetível, cujo arquivo de claims re-executa cada quantidade do README e se recusa a criar um
  commit se alguma divergir. Somando os 27 snapshots, são **629 arquivos de teste/spec**.

---

## Trabalhos selecionados

Não são todos os 27 repositórios — apenas os que mostram uma capacidade distinta.

**Jogos de navegador (abra o link e jogue)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — simulação tática determinística, 40 armas, 12 missões criadas à mão · [ao vivo](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — action RPG compacto: parry, relíquias, duas zonas. *Vertical slice na v0.5.1* · [ao vivo](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — pequeno idle game completo sobre proporções de equipe, 18 arquivos de teste · [ao vivo](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — slice de ARPG isométrico. **Compartilha o núcleo de simulação determinística com o hollowmere**; regras de combate, encontros e conteúdo são próprios. · [ao vivo](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — arena isométrica de três ondas: leia o telegraph, encadeie o mooncut, esquive do golpe comprometido com dash. **43 arquivos de teste** e o ledger de assets mais rigoroso do conjunto — 100 linhas de SHA-256 por arquivo, tudo CC0. · [ao vivo](https://moonshard-warden.vercel.app)

**Jogos em engine (Godot / Unity — vertical slices e protótipos, nenhum lançado comercialmente)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — artilharia de naves capitais no espaço: tempo de voo do projétil, antecipação inercial, penetração por ângulo de blindagem, sobre uma simulação determinística a 30 Hz (27 arquivos de teste)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — ARPG solo de raid de chefes, dez chefes de três fases; a alegação de que "a raid nunca te carrega" é uma estatística medida pós-luta, não um invariante imposto — se um aliado dá o golpe final, o relatório da run diz isso (33 arquivos de teste)
- **[todak](https://github.com/euuuuuuan/todak-public)** — pets em pixel art de desktop que vivem na parte de baixo da sua tela (36 arquivos de teste)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)** (30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)** (29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + seu **[port para Unity](https://github.com/euuuuuuan/driftfolk-unity-public)** — o objetivo do port é a paridade numérica com o original · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — battle royale de autonomia dirigida, em Unity. Listado à parte porque este snapshot é um **esqueleto de verificação, não um slice jogável**: a simulação determinística e seu harness de golden files são a parte entregue, e 11 das 15 asserções de soak numeradas ainda estão pendentes.

**Ferramentas de IA e de agentes**
- **[baton](https://github.com/euuuuuuan/baton-public)** — a plataforma de agentes: superfície MCP de 63 ferramentas, broker de aprovação, 16 pacotes de orquestrador, 90 fontes Swift
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — harness de raciocínio read-only, 8 ferramentas MCP, 0 dependências
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — loop terminado por gate entre dois agentes de código
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — geração local de assets a custo zero atrás de um gate de licença fail-closed
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — gerar → julgar → compor → publicar, em que "não tenho certeza" estaciona a execução em vez de entregá-la

**Dados, backend e ferramentas de domínio**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — administração multi-tenant de ativos em que a fronteira entre tenants vive no PostgreSQL: **82 políticas de row-level security (RLS)** em 11 arquivos de migração, com 6 arquivos de teste pgTAP apontados para elas — não filtragem na camada de aplicação
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — recuperação em que o gate de avaliação é o ponto central
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — agente + kernel de governança sobre um CDP totalmente sintético
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — linter de primeira passada para textos publicitários coreanos do setor financeiro/de seguros. Todos os 5 conjuntos de regras entregues estão marcados como `"verified": false` nos próprios dados, e o relatório não pode ser impresso sem seu aviso: ele sinaliza o texto para revisão humana, não certifica conformidade
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — toque os instrumentos ou escreva os patterns; a mesma música, editada sem perdas pelos dois lados (27 arquivos de teste)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — site estático bilíngue em que cada número declara se é medido ou estimado · [ao vivo](https://euuuuuuan.pages.dev)

---

## Sobre estes repositórios

Todos os 27 repositórios são snapshots sanitizados: código sob Apache-2.0, assets regidos por repositório por um
`CREDITS.md` de 4 níveis. Os jogos são builds pessoais e vertical slices — jogáveis, com gates e rotulados
com honestidade, não lançamentos comerciais.

**Contato:** abra uma issue ou discussion em qualquer repositório daqui.
