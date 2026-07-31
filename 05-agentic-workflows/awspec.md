# Juno PM, Agent Workflow Spec (AWSpec)
_Module 5 final-project deliverable._

---

## 0. Carry-over from prior modules
- **Bet** _(from `02-strategy/strategy-one-pager.md`)_: A RAG-grounded prioritisation copilot — ground every ranking in RocketShip strategy so priorities cite evidence; Copilot autonomy (propose, PM disposes).
- **AI PRD** _(from `03-rag-prd/prd.md`)_: Hybrid RAG over the strategy corpus + last 90 days, Top-K = 8, p95 < 3s (generation-bound); fail-safe = insufficient-evidence flag → cautious mode.
- **User flow** _(from `04-ai-ux/user-flow.md`)_: Iceberg — trigger on new escalation, handshake breadcrumbs, inline cited P0–P3 cards, kill switch + no-fabrication fail-safe.

---

## 1. Pillar 1 — Actors
**Goal**
Turn each new P0/P1 escalation into a ranked, evidence-cited priority the PM can approve in minutes — a risk/decision value frame, scoped to #escalations triage only.

**Trigger**
A new message posts to #escalations tagged P0 or P1 — or an existing thread crosses ≥5 replies within 10 minutes.

**Primary actor:** Agent + Human-in-the-loop

**Humans in the loop**
The PM approves any ranking before it reaches the roadmap. Juno escalates immediately when confidence < 70% on a P0, or when a request touches contracts, legal, or external comms.

---

## 2. Pillar 2 — Pattern Plan
**Pattern:** ReAct (single-agent reason-act-observe loop)

**Sequential steps**
1. Detect and read the new escalation thread.
2. Retrieve strategy corpus + related history (hybrid RAG, Top-K = 8).
3. Map to strategy clause and score P0–P3 (alignment × impact).
4. Draft a ranked, cited backlog entry.
5. Surface to the PM for approval — never auto-publish.

**Stop conditions**
- Success: PM approves/edits the ranked item.
- Failure: retrieval below threshold → flag "insufficient evidence," hand to PM.
- Escalation: confidence < 70% on a P0, or contracts/legal/external comms involved.
- Timeout: no grounding within the p95 budget → degrade to cautious mode.

---

## 3. Pillar 3 — Memory
**Episodic — sequence of actions in this run**
In-scope: the thread being triaged, retrieved chunks, tool results, scores. Lifetime: end of run — discarded once the item is approved/rejected.

**Semantic — persistent behaviours / preferences**
In-scope: the PM's preferred risk/ranking format + prior approve/reject corrections (strategic-alignment tuning), lifetime indefinite. Out of scope: never persist customer PII or contractual terms.

**Working / Contextual — live, in-flight data**
Current thread, escalation ID, retrieved KB chunks, active strategy-doc version.

**External tools — sources of truth via APIs**
Slack thread API (read), Strategy KB / corpus (read), Jira (write = draft/stub only, never live edit).

---

## 4. Pillar 4 — Tools
**Tool inventory** (one per line, with scope)
- `slack.read_thread(id)` · read-only
- `corpus.retrieve(query, k=8)` · read-only
- `strategy.get_doc(version)` · read-only
- `ranker.score(signal, clauses)` · compute
- `jira.draft_issue(payload)` · write: draft-only (no publish)

**Schemas — what each tool returns**
- `corpus.retrieve` → {chunks:[{text, source_id, score}]}
- `strategy.get_doc` → {pillars:[{id, clause}], not_doing:[...]}
- `ranker.score` → {rank, priority:P0–P3, cited_clause, confidence}
- `jira.draft_issue` → {draft_id, status:"pending_pm_approval"}

**Read / write boundaries**
Juno can READ Slack threads, the strategy corpus, and Jira. Juno can WRITE only a Jira draft in "pending approval" state and post its ranked backlog to the PM's review pane. Juno CANNOT publish to the roadmap, edit live Jira priorities, send Slack/external messages, or write customer records. Every write is gated on explicit PM approval.

---

## Self-review
- [x] Goal is one sentence and names the value frame.
- [x] Trigger is a precise, testable condition.
- [x] Pattern is chosen with a defensible reason.
- [x] At least 3 stop conditions, including escalation.
- [x] Each memory type named (in or out).
- [x] Every tool lists scope (read-only vs write) and a schema.
- [x] Read/write boundaries match the AI PRD (M3).
