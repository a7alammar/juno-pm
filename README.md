# Juno PM — AI Copilot for RocketShip's Product Org

> An AI Associate PM that turns Slack/Notion/Jira chaos into a prioritised top-3 risk list every morning.

_Abdulrahman Alammar · AI PM Cohort · July 2026_

Repo: https://github.com/a7alammar/juno-pm

This repo is my final project for the AI Product Management Certification — **Juno PM**. Each module's artefact lives in its own folder; this README is the dashboard and the pitch.

---

## Module artefacts

### M1 · Prompting
- **System prompt** — [`01-prompting/system-prompt.md`](01-prompting/system-prompt.md)
- **Lovable prototype** — https://juno-pm-prototype.lovable.app
- **Prototype write-up** — [`01-prompting/lovable-prototype.md`](01-prompting/lovable-prototype.md) · [source repo](https://github.com/a7alammar/juno-pm-prototype)

### M2 · Strategy
- **Decision matrix** — [`02-strategy/decision-matrix.md`](02-strategy/decision-matrix.md)
- **AI Strategy one-pager** — [`02-strategy/strategy-one-pager.md`](02-strategy/strategy-one-pager.md)

### M3 · RAG / AI PRD
- **AI PRD** — [`03-rag-prd/prd.md`](03-rag-prd/prd.md)

### M4 · AI-Native UX
- **AI user flow** — [`04-ai-ux/user-flow.md`](04-ai-ux/user-flow.md)
- **Iceberg diagram** — [`04-ai-ux/user-flow-iceberg.svg`](04-ai-ux/user-flow-iceberg.svg)
- **Trust-gap mitigations** — [`04-ai-ux/trust-gaps.md`](04-ai-ux/trust-gaps.md)

### M5 · Agentic Workflows
- **Agent Workflow Spec (AWSpec)** — [`05-agentic-workflows/awspec.md`](05-agentic-workflows/awspec.md)
- **Agent graph** — [`05-agentic-workflows/awspec-agent-graph.svg`](05-agentic-workflows/awspec-agent-graph.svg)
- **Agent Control Panel** — [`05-agentic-workflows/control-panel.md`](05-agentic-workflows/control-panel.md)

### M6 · Evals & Guardrails
- **Eval stack** — [`06-evals/eval-stack.md`](06-evals/eval-stack.md)
- **Human evaluation rubric** — [`06-evals/human-rubric.md`](06-evals/human-rubric.md)

---

## PM Execution Plan

### Where Juno is today
- M1–M6 specced and committed.
- Lovable prototype live: the three-column workspace validates the V1 flow end to end.
- RAG backend specced in `prd.md` but NOT wired — the live evidence engine is unbuilt.
- Eval stack designed (3 layers, cadences, pass bars, named owners); 200-thread golden set defined, not yet curated.
- Human rubric drafted with 1–5 anchors and a disagreement protocol; no calibration round yet.
- Trust audit: 3.5/5. Verdict Hold — hallucination and control gaps still open.

### What ships next (next 2 sprints)
- Sprint 1: wire the RAG backend (Lovable Cloud + edge function); curate the first 100 golden-set threads; ship the blocking evidence strip that closes the hallucination gap.
- Sprint 2: closed beta with 3 PMs; wire the eval harness into CI; calibrate 2 graders on 10 prototypical runs; instrument override logging into the weekly human-eval sample.

### What I watch (dashboards)
- Daily: thumbs-down rate, regenerate rate, hand-off rate, P0 override rate.
- Weekly: human-rubric mean per dimension (accuracy / citation / safety); evidence-coverage %; cost per run.
- Per release: golden-set accuracy; format/citation/refusal pass rate; p95 split retrieval vs generation.
- Monthly: % of P0 decisions reversed within 7 days — the M2 outcome metric.

### Red lines (what blocks shipping)
- Any confirmed fabricated citation in the last 30 days.
- <90% golden-set accuracy on the automated layer.
- Any critical-safety fail (a "1" on the safety dimension in human eval).
- P0 override rate >20% in a week → auto-posting pauses.
- P99 latency >5s on the triage flow.

### Governance
- Compliance: PII scrubber pre-LLM; PII and contract terms never persisted to semantic memory.
- Safety: refusal on legal / contract / external-comms content; prompt-injection row in the golden set.
- Reliability: p95 <3s with row-by-row streaming; cautious-mode fallback when retrieval fails.
- Reputation: Juno never publishes — PM approval gates every write; kill switches on auto-post and on ranking.

---

## Build Insights

- **Friction point.** Free-tier credits ran out mid-RAG-lab — the architecture is specced but the live evidence engine is unbuilt, and the repo says so rather than implying otherwise.
- **Key learning.** A system prompt is product config, not phrasing — treating it as a spec is how I caught a real contradiction between my guardrails and my refusal rules.
- **Aha moment.** The prototype looked production-grade and ignored everything the user actually said. Looking trustworthy and being trustworthy are two different products.

---

_Certification submission — AI Product Management Certification._
