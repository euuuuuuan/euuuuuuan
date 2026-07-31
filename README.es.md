## Orquestación de agentes · gates deterministas · desarrollo aumentado por IA

[English](README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [繁體中文](README.zh-TW.md) · **Español** · [Português (BR)](README.pt-BR.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Русский](README.ru.md) · [Italiano](README.it.md) · [Bahasa Indonesia](README.id.md) · [Türkçe](README.tr.md)

Automatización basada en código y sistemas orquestados por agentes: una plataforma de agentes con
una capa de permisos fail-closed, pipelines locales de recuperación con gates de evaluación y
simulaciones deterministas que se reproducen bit a bit.

Cada número de esta página es una medición, no una estimación. El comando que lo produjo vive en el
repo que describe, y el repo lo vuelve a comprobar antes de publicar.

---

## Trabajo insignia

| Proyecto | Qué es | El dato medido | En vivo |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | Plataforma de orquestación local-first para trabajo de agentes acotado: un daemon de control para macOS escrito en Swift, detrás de gates de permisos fail-closed y dirigido por un orquestador en TypeScript sobre MCP. *(plataforma)* | **63 herramientas MCP documentadas** en `docs/TOOL_SURFACE.json`, **234 archivos de test/spec**, **15 ADRs** y un bróker de aprobación remota cuyas autorizaciones están ligadas a una huella y son de un solo uso (ADR-003). | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | RAG en coreano construido para entornos con red segregada (망분리): recuperación híbrida hecha solo con la stdlib, enmascaramiento de PII y un gate de rechazo para preguntas fuera de alcance. *(herramienta)* | **0 dependencias de terceros**, y el build está condicionado por un **golden set de 50 preguntas**: hit@3 **100 %** (39/39), precisión de rechazo **100 %** / recall **81.8 %** — y exactitud de citas **36.75 %**, la métrica débil, publicada en lugar de ocultada. | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | El trabajo SQL de un marketer de ciclo de vida/CRM hecho por un agente sobre MCP, detrás de un kernel de gobernanza que decide hasta dónde puede llegar el agente y qué queda registrado. *(herramienta)* | **108 tests pasan** (con el SDK de MCP instalado), incluidos **33 ataques red-team de PII** y **12 red-team de gobernanza** contra el kernel. Los datos son **100 % sintéticos** y reproducibles byte a byte: seed 42 → digest SQLite idéntico, 5,000 clientes / 99,922 eventos. | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | Planificador táctico de irrupciones y simulador de escuadra en tiempo real en el navegador; un asalto completado se comparte como una URL que se vuelve a simular en el navegador de otra persona. *(build jugable, no un lanzamiento comercial)* | **Simulación de tick fijo a 30 Hz** con un gate de pureza que prohíbe **11 APIs no deterministas** en los paquetes de simulación y de contenido — y pasa, que es lo que hace que seed + registro de inputs se reproduzcan de forma idéntica. **55 archivos de test/spec**, y 40 scripts en `tools/`, de los cuales 35 son harnesses headless (los otros 5 son descargadores de assets y codegen). | **[jugar](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | Un dispositivo de razonamiento determinista: 8 herramientas MCP que fuerzan un bloqueo de alcance, un plan del paso desconfirmatorio más barato, un barrido de puntos ciegos y un gate de veredicto que rechaza cualquier afirmación sin prueba de ejecución. *(herramienta)* | **0 dependencias en runtime**, **33/33 tests pasan** — y uno de esos tests es la garantía: toda la superficie no escribe en el sistema de archivos, no lanza procesos y no hace llamadas de red. | — |

---

## Cómo están construidos

La parte interesante no es «construido con IA». Es lo que hay *entre* el modelo y el repositorio.

- **Dos agentes de código, un árbitro determinista.** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  es un driver externo que enfrenta dos CLIs de agentes entre sí hasta que un gate pasa. El gate decide
  cuándo algo está «terminado» — nunca la opinión de un agente sobre su propio trabajo. 12 tests unitarios,
  que pasan con `PATH` reducido a los directorios del sistema, de modo que los rechazos del driver son
  demostrables sin ninguno de los dos agentes instalado.
- **Los gates deterministas corren antes de llamar siquiera al modelo.** Verificadores de pureza, no
  intuiciones: el gate de simulación de fatal-funnel prohíbe 11 APIs no deterministas; el test de solo
  lectura de reasonforge impone 6 patrones prohibidos. La calidad de la recuperación también es un gate
  del build — un golden set más umbrales que hacen fallar la ejecución cuando hay regresión
  ([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)).
- **Las afirmaciones sobre la UI salen de sondas y capturas de pantalla, no de la memoria.** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  incluye los harnesses `shot`, `relic-qa` y `mobile-qa`; fatal-funnel tiene 35 harnesses headless entre los 40 scripts de `tools/`
  y 4 cámaras de captura fijas. Esta lección se aprendió por la vía cara: un tooltip que «funciona» puede estar
  muerto detrás de un solo flag de filtrado de input; una sonda de input sintético lo detecta, leer el código no.
- **Los assets pasan un gate de licencia antes de generarse.** [assetforge](https://github.com/euuuuuuan/assetforge-public)
  se niega a generar a partir de cualquier modelo ausente de su registro. Aguas abajo, **los 27 snapshots
  publicados llevan un `CREDITS.md` con una tabla de redistribución de 4 niveles** — así, cualquiera que
  haga un fork sabe exactamente qué debe eliminar.
- **Etiquetas honestas, aplicadas por un esquema.** En [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  cada métrica publicada está tipada como `kind: z.enum(['measured', 'estimated'])`. Un número no puede
  publicarse sin declarar cuál de las dos es. Esa regla es la razón de que esta página no tenga cifras
  redondas de marketing.
- **La publicación es un pipeline, no un copiar y pegar.** Cada repo público de aquí se produce con una
  publicación sanitizada y repetible cuyo archivo de claims vuelve a ejecutar cada cantidad del README y
  se niega a crear un commit si alguna se desvía. A lo largo de los 27 snapshots, eso suma **629 archivos
  de test/spec**.

---

## Trabajo seleccionado

No los 27 repos — solo los que muestran una capacidad distinta.

**Juegos de navegador (abre el enlace y juega)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — sim táctica determinista, 40 armas, 12 misiones diseñadas a mano · [en vivo](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — action RPG compacto: parry, reliquias, dos zonas. *Vertical slice en v0.5.1* · [en vivo](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — pequeño juego idle completo sobre ratios de personal, 18 archivos de test · [en vivo](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — slice de ARPG isométrico. **Comparte su núcleo de simulación determinista con hollowmere**; las reglas de combate, los encuentros y el contenido son propios. · [en vivo](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — arena isométrica de tres oleadas: lee el telegraph, encadena el mooncut, esquiva con dash el golpe ya comprometido. **43 archivos de test** y el registro de assets más estricto del conjunto — 100 filas SHA-256 por archivo, todo CC0. · [en vivo](https://moonshard-warden.vercel.app)

**Juegos con motor (Godot / Unity — vertical slices y prototipos, ninguno lanzado comercialmente)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — artillería de naves capitales en el espacio: tiempo de vuelo del proyectil, adelanto inercial, penetración según el ángulo del blindaje, sobre una sim determinista a 30 Hz (27 archivos de test)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — ARPG de raid de jefes en solitario, diez jefes de tres fases; la afirmación de que «la raid nunca te lleva en brazos» es una estadística medida tras el combate, no un invariante impuesto — si un aliado asesta el golpe final, el informe de la partida lo dice (33 archivos de test)
- **[todak](https://github.com/euuuuuuan/todak-public)** — mascotas de píxeles de escritorio que viven a lo largo del borde inferior de tu pantalla (36 archivos de test)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)** (30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)** (29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + su **[port a Unity](https://github.com/euuuuuuan/driftfolk-unity-public)** — el sentido del port es la paridad numérica con el original · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — battle royale de autonomía dirigida, en Unity. Aparece listado aparte porque este snapshot es un **esqueleto de verificación, no un slice jugable**: la sim determinista y su harness de golden files son la parte entregada, y 11 de las 15 aserciones de soak numeradas siguen pendientes.

**Herramientas de IA y de agentes**
- **[baton](https://github.com/euuuuuuan/baton-public)** — la plataforma de agentes: superficie MCP de 63 herramientas, bróker de aprobación, 16 paquetes de orquestador, 90 fuentes Swift
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — harness de razonamiento de solo lectura, 8 herramientas MCP, 0 dependencias
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — bucle entre dos agentes de código que solo termina un gate
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — generación local de assets a costo cero detrás de un gate de licencias fail-closed
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — generar → juzgar → componer → publicar, donde «no estoy seguro» aparca la ejecución en lugar de entregarla

**Datos, backend y herramientas de dominio**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — administración de activos multitenant donde la frontera entre tenants vive en PostgreSQL: **82 políticas de row-level security (RLS)** repartidas en 11 archivos de migración, con 6 archivos de test pgTAP apuntándoles — no filtrado en la capa de aplicación
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — recuperación donde el gate de evaluación es el punto
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — agente + kernel de gobernanza sobre un CDP totalmente sintético
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — linter de primera pasada para textos publicitarios coreanos de finanzas/seguros. Los 5 conjuntos de reglas incluidos están marcados `"verified": false` en los propios datos y el informe no puede imprimirse sin su descargo de responsabilidad: señala textos para revisión humana, no certifica cumplimiento
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — toca los instrumentos o escribe los patrones; la misma canción, editada sin pérdidas desde ambos lados (27 archivos de test)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — sitio estático bilingüe donde cada número declara si es medido o estimado · [en vivo](https://euuuuuuan.pages.dev)

---

## Acerca de estos repos

Los 27 repos son snapshots sanitizados: código bajo Apache-2.0, assets regidos repo por repo mediante
un `CREDITS.md` de 4 niveles. Los juegos son builds personales y vertical slices — jugables, con gates
y etiquetados con honestidad, no lanzamientos comerciales.

**Contacto:** abre un issue o una discusión en cualquiera de estos repos.
