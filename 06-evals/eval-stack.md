# Juno PM — Eval Stack


---

## Layer 1 · User feedback (online · highest volume)

**Signals captured**
- Active: thumbs up/down on each Juno output; "regenerate" and "edit before send" buttons; free-text feedback when thumbs-down.
- Passive: dismiss/suppress, time-to-first-action, abandon rate (PM closes thread without acting).
- **Override capture:** every Manual Override (force-promote / downgrade / reject) is logged with the original rank, the PM's corrected rank, and the cited clause — this is the primary source of high-confidence silent failures.

**Cadence:** Per request (real-time) + weekly aggregate.

**Pass bar:** ≥80% thumbs-up; regenerate rate ≤15%; override rate on P0 items ≤20%.

**Owner:** PM reviews weekly; on-call PM triages ≥2 thumbs-down on the same intent within 24h.

---

## Layer 2 · Human evaluation (system-level · highest fidelity)

**What gets sampled (with stratification)**
- 30 P0 triage runs per week (capped), stratified across confidence buckets (high / mid / low).
- 100% of hand-off cases (insufficient-evidence, escalation, timeout).
- 100% of PM-override cases — these are graded first, since they are known disagreements between Juno and a human.

**Rubric reference:** `06-evals/human-rubric.md`

**Cadence:** Two batches per week (Tuesday + Friday), ~15 runs each. Split deliberately — a single 50-run Friday batch is ~7 hours of grading and degrades into skimming exactly in the weeks when volume spikes.

**Pass bar:** ≥4.0/5 mean across Accuracy + Safety; 0 fabricated citations; 0 critical safety fails.

**Who grades (+ disagreement protocol):** 2 graders + PM tiebreak per disagreement. Graders differing by ≥2 points on any dimension trigger PM adjudication, and that case joins the calibration set.

---

## Layer 3 · Automated assessment (component-level · highest scale)

**Golden set (versioned)**
- 200 anonymised P0 threads with PM-curated expected top-3 risks.
- Versioned in `06-evals/golden-set/`.
- Refresh quarterly, after every major incident, **and continuously from confirmed PM overrides** — override-derived cases are the long-tail failures a statically curated set cannot anticipate.

**Eval checks (LLM-judge + format/safety)**
- LLM-judge scores accuracy of top-3 (rubric-aligned).
- Format check: valid markdown table with required columns.
- Citation check: each risk cites a message index that exists.
- Refusal check: contracts/legal language triggers refusal.
- **Critical-safety pattern check:** automated detection of PII strings, contract/ARR terms, and external-comms drafting — moved here from the human layer so it can gate at release time.

**Cadence:** Every PR (CI gate) + nightly cron.

**Pass bar:** ≥90% golden-set accuracy; 100% format/citation/refusal pass; 0 critical-safety pattern hits.

**Owner:** CI fails the PR. Eng owns format/citation. PM owns the accuracy bar.

---

## Outcome tracking · does the ranking hold up?

The three layers measure output quality; this measures whether the decision was right.

- **Reversal rate:** % of P0 priority decisions reversed within 7 days — target **<10%** (the M2 success metric).
- **Evidence coverage:** % of ranked items carrying ≥2 cited sources — target **≥90%**.
- **Time-to-first-draft:** weekly prioritisation time per PM — target **≤30 min** (from ~2h baseline).
- **Cadence:** monthly rollup, reviewed against the M2 strategy one-pager.

---

## PM execution plan · hard vs. soft gates

**Hard gates (auto-block, evaluated at PR time)**
- 0% PII leakage.
- Citation check fail ⇒ block.
- Any critical-safety pattern hit (PII, contract/ARR terms, external-comms drafting) ⇒ block.

**Soft gates (PM sign-off)**
- Human-eval batch shows any critical safety fail ⇒ PM sign-off required before the next release. _(Demoted from a hard gate: human eval runs twice weekly and cannot gate a same-day release without either freezing shipping or being routinely waived.)_
- P99 latency > 5s requires PM justification.
- Off-brand tone flags > 2% require PM review (not auto-block).
- Weekly override rate on P0 items > 20% ⇒ PM reviews whether the strategy doc, not the model, is the problem.
