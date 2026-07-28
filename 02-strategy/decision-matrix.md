# Juno PM — AI Solution Decision Matrix

## Three-Layer Model + Autonomy — Juno Automated Prioritization

### Layer 1 — User Workflow
Roadmap discussions are driven by the loudest voice in Slack rather than customer evidence. The PM can't defend reasoning when leadership pushes back, leading to constant priority reversals and stakeholder mistrust.

### Layer 2 — Technical AI Solution + Autonomy
**Capability:** Hybrid (RAG + bounded Agentic)  **Autonomy dial:** Copilot

RAG over the RocketShip corpus (Slack threads, support tickets, interview notes, Notion docs) so every priority cites source IDs. Bounded Agentic orchestration cross-checks Jira priority vs. Slack escalations and flags conflicts.

_Phasing (minimum-first):_ v1 ships RAG-ranking only — enough to solve the Layer 1 friction with a defensible, evidence-cited backlog. The agentic conflict-check is a scoped fast-follow (v1.1) and stays read-only (it flags, never acts).

_Autonomy:_ Juno drafts a ranked backlog with reasoning and citations; the PM approves before publish. Avoiding Agent autonomy — moving sprint priorities without human approval is a one-way trust-erosion door.

### Layer 3 — Business Outcome
Reduce average weekly roadmap prioritization time from 2 hours to 30 minutes (75% reduction). Cut the rate of decisions reversed within 1 week to under 10%. Track evidence-coverage % — target 90% of prioritised items carry ≥2 cited sources.

---

## Decision Matrix — 6-Lens Score

| Lens | Score (1–5) | Notes |
|---|---|---|
| Value | 5 | Attacks the #1 Signal-Collapse pain; saves ~1 day/sprint per PM and restores defensible, evidence-linked decisions. |
| Feasibility | 4 | RAG over existing Slack/Notion/Jira corpus is well-trodden and the data already exists; citation reliability is the main build effort. |
| Cost | 4 | Reuses the existing corpus, no new headcount (fits "innovation budget, zero headcount"); RAG inference is moderate, predictable cost. |
| Risk | 4 | Copilot + human-approve-before-publish + citation coverage keeps a wrong ranking from acting on its own; residual risk is hallucinated citations. |
| Speed-to-impact | 4 | v1 (RAG-only) is shippable fast and the outcome is measurable in a 30-day pilot. |
| Defensibility | 3 | The RAG capability itself is replicable; the real moat is the proprietary RocketShip corpus + earned PM trust, not the tech. |

**Total: 24/30**

## Strongest lens
**Value (5)** — it hits the exact Signal-Collapse bottleneck and converts unaccountable, loudest-voice triage into a defensible, evidence-cited backlog. Everything else is in service of that.

## Weakest lens
**Defensibility (3)** — the core RAG-ranking is easy for a competitor to copy. What would change it: build a feedback loop where Juno learns from each PM approve/reject, so the ranking model becomes uniquely tuned to RocketShip's data and decisions over time — turning the proprietary corpus into a compounding moat.
