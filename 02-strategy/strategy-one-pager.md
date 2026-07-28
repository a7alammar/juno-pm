# AI Strategy One-Pager - Juno Automated Prioritization

## 1. Problem & Workflow

The Problem: During Signal Collapse, PMs prioritise by whoever escalates loudest in Slack, not by cited customer evidence — so the roadmap flips with every new exec email and can't be defended to leadership.
Prevention: Juno explicitly prevents the "loudest-voice" prioritisation call — bumping an item up the roadmap on volume/recency instead of evidence.

## 2. Target Metrics

Cycle time: weekly roadmap prioritisation drops from ~2 hours to ≤30 minutes per PM (30-day pilot).
Leadership proof: <10% of P0 priority decisions reversed within 7 days, and ≥90% of ranked items carry ≥2 cited sources. Reversal rate is the "don't touch it" metric — it's what earns or erodes leadership trust.

## 3. Autonomy Level

Choice: Copilot — Juno drafts a ranked, cited backlog; the PM reviews and approves before publish.
Explicitly avoiding: Agent. Moving sprint priorities or live dates without human approval is a one-way trust-erosion door — one wrong autonomous call costs more trust than months of correct ones earn.

## 4. Data & Model Approach

Approach: Ground (RAG) over the RocketShip corpus (Slack, tickets, interviews, Notion) so every ranking cites source IDs.
Explicitly avoiding: the prompt-only shortcut (a raw LLM with no retrieval). Without grounding it can't cite sources and will fabricate evidence — destroying the exact trust this bet depends on. (Fine-tuning is also deferred until v1 proves demand.)

## 5. Risks & Mitigations

Risk: Juno confidently cites a source that doesn't actually support the claim — a "Beautiful Liar" ranking. A PM trusts it, leadership later finds the evidence was hollow, and that one incident poisons trust in the whole tool.
Mitigation: every ranked item must link to ≥2 retrievable source IDs; anything Juno can't ground is auto-flagged NEEDS CLARIFICATION instead of ranked, and the PM approves before publish — so no ungrounded claim reaches leadership.

## 6. V1 Scope

IN (v1): RAG-ranking of P0/P1 escalations into a cited, ranked top-5 backlog the PM approves.
OUT (v1):
- Juno autonomously moving Jira priorities or sprint dates (stays Copilot; the PM acts).
- The bounded agentic Jira-vs-Slack conflict-checker (deferred to v1.1).
- A fine-tuned/bespoke ranking model (RAG only for v1).
