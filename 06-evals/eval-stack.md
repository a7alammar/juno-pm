# Juno PM — Eval Stack


## Layer 1 · User feedback (online · highest volume)

**Signals captured:**
- Active: thumbs up/down on each Juno output; "regenerate" and "edit before send" buttons; free-text feedback when thumbs-down
- Passive: dismiss/suppress, time-to-first-action, abandon rate (PM closes thread without acting)

- **Cadence:** Per request (real-time) + weekly aggregate review
- **Pass bar:** >=80% thumbs-up; regenerate rate <=15%; abandon rate <=20% on non-trivial intents
- **Owner:** PM reviews weekly; on-call PM triages >=2 thumbs-down on same intent within 24h

## Layer 2 · Human evaluation (system-level · highest fidelity)

**What gets sampled:**
- 50 P0 triage runs / week
- Stratified across confidence buckets (high / mid / low)
- 100% of hand-off cases included

- **Rubric:** 06-evals/human-rubric.md
- **Cadence:** Weekly batch (Friday afternoon)
- **Pass bar:** >=4.0/5 mean across accuracy + safety; 0 critical safety fails
- **Who grades:** 2 graders + PM tiebreak per disagreement protocol

## Layer 3 · Automated assessment (component-level · highest scale)

**Golden set:**
- 200 anonymised P0 threads with PM-curated expected top-3 risks
- Versioned in 06-evals/golden-set/
- Refresh quarterly and after every major incident

**Eval checks:**
- LLM-judge scores accuracy of top-3 (rubric-aligned)
- Format check: valid markdown table with required columns
- Citation check: each risk cites a message index that exists
- Refusal check: contracts/legal language triggers refusal

- **Cadence:** Every PR (CI gate) + nightly cron
- **Pass bar:** >=90% golden-set accuracy; 100% format/citation/refusal pass
- **Owner:** CI fails the PR. Eng owns format/citation. PM owns the accuracy bar.

## PM execution plan · hard vs. soft gates

**Hard gates (auto-block):**
- 0% PII leakage (auto-block)
- 0 critical safety fails on the human-eval layer
- Citation check fail => block

**Soft gates (PM sign-off):**
- P99 latency >5s requires PM justification
- Off-brand tone flags >2% require PM review (not auto-block)
