# Juno PM — AI User Flow (the Iceberg)


---

## 0. Carry-over from prior modules
- **Bet** _(from `02-strategy/strategy-one-pager.md`)_: A RAG-grounded prioritisation copilot — ground Juno's ranking in RocketShip's real strategy so every priority cites evidence, turning loudest-voice triage into a defensible, cited backlog.
- **Autonomy level** (Assist / Copilot / Agent) _(from `02-strategy/decision-matrix.md`)_: **Copilot** — Juno drafts a ranked, cited backlog; the PM approves before publish.
- **Retrieval strategy** _(from `03-rag-prd/prd.md`)_: **Hybrid** — RAG over the corpus + long-context for single-doc reads; semantic + keyword, Top-K = 8.

---

## 1. Pillar 1 — The Trigger
**Signal type:** New message / transcript received

**Specific signal in Juno:** A new P0/P1 message lands in #escalations (or a ticket/interview note is added). Juno detects it the moment data enters the system — before the PM clicks "prioritise."

**Surface response (instant):** A subtle status badge appears on the escalation: "Juno is reading this…" — instant acknowledgement before any ranking exists, so the PM knows it's queued.

---

## 2. Pillar 2 — The Processing State
**Hidden logic, major underwater steps:**
1. Hybrid RAG retrieval over the RocketShip strategy corpus + last 90 days (Top-K = 8).
2. Map each signal to the strategy clause/pillar it supports (or flag "no match").
3. Score priority P0–P3 by strategic alignment × impact.
4. Draft a ranked backlog with per-item citations.

**Handshake breadcrumbs:** "Scanning Strategy One-Pager…" → "Cross-referencing 23 transcripts…" → "Matching to pillars…" → "Synthesising ranked priorities…"

**Router decision:** Strategy doc loaded → Strategy Mode (priorities cite clauses). No doc → Quality Mode with a visible "no strategy loaded" warning. Retrieval below threshold → route item to NEEDS CLARIFICATION.

---

## 3. Pillar 3 — The Presentation
**Placement maneuver:** Inline & Embedded

**Generated output:** P0–P3 priority cards inline in the Insights column — each shows the rank, the cited strategy clause + source ID, and the customer signal. The Opportunity Brief renders in the right pane.

**Why this maneuver (the value):** Augmentation, not automation: Juno drafts a ranked, cited backlog; the PM reviews/edits/approves. It scales the PM's judgment rather than replacing it — matching the Copilot autonomy committed in M2 (propose, human disposes).

---

## 4. Pillar 4 — The Feedback Loop
**Kill switch:** A Manual Override on every priority card lets the PM force-promote, downgrade, or reject a ranking before publish. Nothing reaches the roadmap without PM approval.

**Captured training signal:** Every approve/edit/reject is logged as a strategic-alignment correction (e.g. "PM demoted an item Juno ranked P0") → tightens future scoring and surfaces where the strategy doc is ambiguous.

**Fail-safe:** If retrieval is empty or the strategy doc is missing, Juno never fabricates — it degrades to a visibly-flagged "quality-only / cautious mode," drops priority confidence to N/A, and flags items NEEDS CLARIFICATION. No dead end: always a path back to a human PM.

---

## Diagram
See `user-flow-iceberg.svg` in this folder — the layered iceberg render of the flow above.
