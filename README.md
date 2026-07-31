## Agent orchestration · deterministic gates · AI-augmented development

**English** · [한국어](README.ko.md)

Code-based automation and agent-orchestrated systems: an agent platform with a fail-closed
permission layer, local retrieval pipelines with evaluation gates, and deterministic simulations
that replay bit-for-bit.

Every number on this page is a measurement, not an estimate. The command that produced it lives in the
repo it describes, and the repo re-checks it before publishing.

---

## Flagship work

| Project | What it is | The measured fact | Live |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | Local-first orchestration platform for bounded agent work: a Swift macOS control daemon behind fail-closed permission gates, driven by a TypeScript orchestrator over MCP. *(platform)* | **63 documented MCP tools** in `docs/TOOL_SURFACE.json`, **234 test/spec files**, **15 ADRs**, and a remote approval broker whose grants are fingerprint-bound and single-use (ADR-003). | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | Korean RAG built for network-segregated (망분리) environments: pure-stdlib hybrid retrieval, PII masking, and a refusal gate for out-of-scope questions. *(tool)* | **0 third-party requirements**, and the build is gated by a **50-question golden set**: hit@3 **100 %** (39/39), refusal precision **100 %** / recall **81.8 %** — and citation accuracy **36.75 %**, the weak metric, published rather than hidden. | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | The SQL work of a lifecycle/CRM marketer done by an agent over MCP, behind a governance kernel that decides how far the agent may go and what gets written down. *(tool)* | **108 tests pass** (with the MCP SDK installed), including **33 PII red-team** and **12 governance red-team** attacks against the kernel. Data is **100 % synthetic** and byte-reproducible: seed 42 → identical SQLite digest, 5,000 customers / 99,922 events. | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | Tactical breach planner and real-time squad sim in the browser; a completed raid shares as a URL that re-simulates in someone else's browser. *(playable build, not a commercial release)* | **30 Hz fixed-tick simulation** with a purity gate that bans **11 non-deterministic APIs** from the sim and content packages — it passes, which is what makes seed + input log replay identically. **55 test/spec files**, and 40 scripts in `tools/` of which 35 are headless harnesses (the other 5 are asset fetchers and codegen). | **[play](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | A deterministic reasoning device: 8 MCP tools that force a scope lock, a cheapest-disconfirming-step plan, a blind-spot sweep, and a verdict gate that rejects a claim with no proof of execution. *(tool)* | **0 runtime dependencies**, **33/33 tests pass** — and one of those tests is the guarantee: the entire surface performs no filesystem writes, no process spawns, and no network calls. | — |

---

## How these are built

The interesting part is not "built with AI". It is what sits *between* the model and the repository.

- **Two coding agents, one deterministic referee.** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  is an external driver that runs two agent CLIs against each other until a gate passes. The gate decides
  "done" — never an agent's opinion of its own work. 12 unit tests, passing with `PATH` reduced to the
  system directories, so the driver's refusals are provable without either agent installed.
- **Deterministic gates run before the model is ever called.** Purity checkers, not vibes:
  fatal-funnel's sim gate bans 11 non-deterministic APIs; reasonforge's read-only test enforces 6
  forbidden patterns. Retrieval quality is a build gate too — a golden set plus thresholds that fail the
  run when they regress ([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)).
- **UI claims come from probes and screenshots, not from memory.** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  ships `shot`, `relic-qa` and `mobile-qa` harnesses; fatal-funnel has 35 headless harnesses among the 40 scripts in `tools/`
  and 4 fixed screenshot cameras. This lesson was learned the expensive way: a tooltip that "works" can be dead
  behind one input-filter flag; a synthetic input probe catches that, reading the code does not.
- **Assets are licence-gated before they are generated.** [assetforge](https://github.com/euuuuuuan/assetforge-public)
  refuses to generate from any model absent from its registry. Downstream, **all 27 published snapshots
  carry a `CREDITS.md` with a 4-tier redistribution table** — so anyone forking knows exactly what they
  must remove.
- **Honest labels, enforced by a schema.** In [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  every published metric is typed `kind: z.enum(['measured', 'estimated'])`. A number cannot be published
  without declaring which one it is. That rule is why this page has no round marketing figures on it.
- **Publication is a pipeline, not a copy-paste.** Each public repo here is produced by a repeatable
  sanitizing publish whose claims file re-executes every quantity in the README and refuses to create a
  commit if one drifts. Across the 27 snapshots that is **629 test/spec files**.

---

## Selected work

Not all 27 repos — the ones that show a distinct capability.

**Browser games (open the link and play)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — deterministic tactical sim, 40 weapons, 12 hand-authored missions · [live](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — compact action RPG: parry, relics, two zones. *Vertical slice at v0.5.1* · [live](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — small complete idle game about staffing ratios, 18 test files · [live](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — isometric ARPG slice. **Shares its deterministic simulation core with hollowmere**; combat rules, encounters and content are its own. · [live](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — three-wave isometric arena: read the telegraph, chain the mooncut, dash the committed strike. **43 test files** and the strictest asset ledger of the set — 100 per-file SHA-256 rows, all CC0. · [live](https://moonshard-warden.vercel.app)

**Engine games (Godot / Unity — vertical slices and prototypes, none commercially released)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — capital-ship artillery in space: shell travel time, inertial lead, armour-angle penetration, on a deterministic 30 Hz sim (27 test files)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — solo boss-raid ARPG, ten three-phase bosses; the "the raid never carries you" claim is a measured post-fight stat, not an enforced invariant — if an ally lands the killing blow, the run report says so (33 test files)
- **[todak](https://github.com/euuuuuuan/todak-public)** — desktop pixel pets that live along the bottom of your screen (36 test files)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)** (30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)** (29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + its **[Unity port](https://github.com/euuuuuuan/driftfolk-unity-public)** — the port's point is numeric parity with the original · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — directed-autonomy battle royale, Unity. Listed separately because this snapshot is a **verification skeleton, not a playable slice**: the deterministic sim and its golden-file harness are the shipped part, and 11 of 15 numbered soak assertions are still pending.

**AI and agent tooling**
- **[baton](https://github.com/euuuuuuan/baton-public)** — the agent platform: 63-tool MCP surface, approval broker, 16 orchestrator packages, 90 Swift sources
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — read-only reasoning harness, 8 MCP tools, 0 dependencies
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — gate-terminated loop between two coding agents
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — local, zero-cost asset generation behind a fail-closed licence gate
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — generate → judge → compose → publish, where "not sure" parks the run instead of shipping it

**Data, backend and domain tooling**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — multi-tenant asset administration where the tenant boundary lives in PostgreSQL: **82 row-level-security policies** across 11 migration files, with 6 pgTAP test files aimed at them — not application-layer filtering
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — retrieval with the evaluation gate as the point
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — agent + governance kernel over an all-synthetic CDP
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — first-pass linter for Korean financial/insurance ad copy. All 5 shipped rulesets are marked `"verified": false` in the data itself and the report cannot print without its disclaimer: it flags copy for human review, it does not certify compliance
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — play the instruments or write the patterns; the same song, edited losslessly from both sides (27 test files)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — bilingual static site where every number declares measured vs estimated · [live](https://euuuuuuan.pages.dev)

---

## About these repos

All 27 repos are sanitized snapshots: code under Apache-2.0, assets governed per-repo by a 4-tier
`CREDITS.md`. Games are personal builds and vertical slices — playable, gated, and honestly
labelled, not commercial releases.

**Contact:** open an issue or discussion on any repo here.
