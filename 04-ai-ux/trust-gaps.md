# Juno PM — AI-UX Trust Gap Audit

**Feature audited:** Juno PM — ranked P0/P1 priority cards (Insights column)
_Module 4 supplementary deliverable. Scored 1 = wide open · 5 = closed._

---

| Gap | Score | Mitigation (shipped / next) |
|---|---|---|
| **The Black-box Gap** | 4/5 | Inline citation chips + "why this rank" expand panel; next: expose alignment × impact sub-scores |
| **The Hallucination Gap** | 3/5 | ≥2-source grounding + NEEDS CLARIFICATION flag; next: blocking evidence strip + publish acknowledgement |
| **The Control Gap** | 3/5 | Manual Override on every card, PM approves before publish; next: mid-flight stop + per-signal suppression |
| **The Intelligence Tax** | 4/5 | Narrated breadcrumbs, p95 < 3s, streamed rows, no persisted PII; next: hover-to-verify citations |

**Average score:** 3.50 / 5 · **Lowest gap:** 3 / 5

## Verdict
**Hold.** Two gaps remain open (Hallucination and Control, both 3/5). Ship v1 down-scoped to a validator UI — Juno proposes, the PM validates every item — and close both gaps before widening autonomy. The blocking evidence strip is the single highest-leverage fix.

---

## Gap 1 — Black-box gap
_Users don't know why the AI did what it did._

- **Where it shows up in Juno:** the P0–P3 priority card in the Insights column — the rank is asserted, the reasoning is not.
- **Mitigation:** every card carries an inline citation chip (strategy clause + source ID: thread link / ticket / Jira key) and a "why this rank" expand panel showing the retrieved excerpt verbatim.
- **Remaining gap:** the PM sees the *evidence* but not the *arithmetic* — alignment × impact is opaque, so a P0-vs-P1 call can't be argued with. Next: surface both sub-scores on the expand panel.

## Gap 2 — Hallucination gap
_Users don't know when the AI is wrong._

- **Where it shows up in Juno:** a confidently-ranked, well-cited card whose citation doesn't actually support the claim — the "Beautiful Liar" failure documented in `01-prompting/lovable-prototype.md`.
- **Mitigation:** ≥2 retrievable sources required per ranked item; anything ungroundable is flagged `NEEDS CLARIFICATION` rather than ranked; retrieval below threshold degrades the whole run to a visibly-flagged cautious mode.
- **Remaining gap:** the flag is a badge sitting under a stack of authoritative-looking cards, so it gets out-shouted. Next: a blocking top strip ("3 items need evidence before this backlog is trustworthy"), a Draft watermark on sub-threshold items, and an explicit "Proceed with N ungrounded items?" gate on publish.

## Gap 3 — Control gap
_Users don't know what they can or cannot change._

- **Where it shows up in Juno:** during processing (no way to intervene) and at the publish step (approve-or-nothing).
- **Mitigation:** Manual Override on every card — force-promote, downgrade, or reject — and nothing reaches the roadmap without PM approval. Every override is logged as a strategic-alignment correction and feeds the eval loop.
- **Remaining gap:** control is post-hoc only. There's no mid-flight stop and no way to fix a bad input (wrong strategy-doc version) once a run starts. Next: a "Stop / fix input" control during processing, plus "don't rank this again" suppression per signal type.

## Reality check — the Intelligence Tax
_Is the latency / privacy / cognitive load worth the value?_

- **Latency:** narrated, not spun — four handshake breadcrumbs, p95 < 3s budget, results streamed row-by-row so the top item lands well under budget.
- **Privacy:** episodic memory discarded at end of run; PII and contract terms never persisted to semantic memory.
- **Cognitive load:** the remaining tax is real — verifying citations costs the PM reading time. Next: collapse citations to a hover-to-verify chip so the default scan stays fast.

---

## Cross-gap fail-state
_What happens if all three gaps fire at once?_

The worst case is a **silent, confident, unstoppable wrong ranking**: retrieval returns weakly-relevant chunks that clear the threshold (hallucination gap), the card cites them so the reasoning *looks* sound (black-box gap), and the PM can't intervene until the run completes (control gap). A $50k reliability blocker sits at P3 under a cosmetic request, the PM approves on a Friday scan, and leadership finds out a sprint later — exactly the trust collapse the whole bet was meant to prevent.

**The UI it goes into:** the fail-state must be loud and structural, not a badge. On any run where mean confidence is below threshold or ungrounded items exist, the Insights column renders **evidence-first**: a blocking strip at the top listing what couldn't be grounded, ranked cards greyed to draft state behind it, and the publish action disabled until the PM explicitly acknowledges. Ungrounded output should never be able to look finished.
