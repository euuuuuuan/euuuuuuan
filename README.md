# Hi, I'm euuuuuuan 👋

### AX practitioner — code-built automation + agent orchestration with governance-as-code

I'm a solo automation & AI engineer (in-house automation at a mid-sized company). I build production AI systems where the hard parts aren't the models — they're the **orchestration, governance, and cost discipline** around them. I design agents that ship, get approved before they act, and stay inside a budget.

---

## What I build

- **Agent orchestration platforms** — MCP-based daemons that expose tools to LLM agents with approval gates, audit logs, and retrospective learning loops.
- **Token-efficient LLM routing** — deterministic difficulty classification that routes simple work to local models and reserves cloud calls for the genuinely hard problems, under a hard budget ceiling.
- **Governance-as-code** — human-in-the-loop approvals, policy explanations, and audit trails as first-class primitives, not afterthoughts.
- **Edge / local-first AI** — pushing as much capability as possible onto local hardware (Ollama, MLX) to cut cost and latency to near zero.
- **Desktop & macOS automation** — agents that drive real apps, files, mail, and system state safely.

---

## Tech I work with

**Languages:** Python · TypeScript · Swift
**Agent & LLM:** Model Context Protocol (MCP) · local LLMs (Ollama, MLX) · LangGraph-style orchestration · RAG · constrained decoding · evals
**Patterns:** governance-as-code · approval gates · structured handoffs · budget ledgers · prompt caching · retrospective/self-improving loops

---

## Selected projects

### Baton — MCP daemon + agent orchestrator
A persistent daemon that exposes **58 tools** to AI agents over the Model Context Protocol. Built for production reliability:
- **Approval gates & governance** — mutating actions can require human sign-off; every action is audited and every policy decision is explainable.
- **Retrospective learning** — the system reviews its own runs and improves routing/behavior over time.
- **Cross-host portability** — designed to run as a single always-on host, resilient to network and sleep conditions.

### ai-research-lab — token-efficient LLM router
A router that decides *where* a request should run before spending a single token:
- **Zero-token difficulty classifier** routes simple prompts to **local Ollama** and escalates only complex ones to the cloud.
- **Prompt caching + a budget ledger** enforce hard cost ceilings.
- **Failure escalation** — local-first, with controlled escalation when a local model can't deliver.

### macagent — macOS automation agent
An agent that drives real desktop applications, files, and system state with the same safety posture: scoped permissions, reversible actions, and audit logging.

> Also: a stock auto-trading bot and a set of in-house internal automations (private).

---

## Currently learning / shipping

🚀 Driving local-LLM capability utilization toward **100%** (no idle hardware): constrained tool-calls, local rerankers, and owner-gated MLX activation — keeping high-value work cheap and on-device.

---

## How to reach me

- **GitHub:** [@euuuuuuan](https://github.com/euuuuuuan)
- Open to: **AI Solutions Architect · AI Orchestration Engineer · Forward-Deployed Engineer**

---

*I care most about the gap between an impressive demo and a system you can trust in production. That gap is governance, cost, and reliability — that's what I build.*
