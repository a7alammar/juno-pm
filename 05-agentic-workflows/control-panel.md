# Juno PM — Agent Control Panel

_Companion to `awspec.md`. Module 5 deliverable._

> The PM-facing dashboard for an agent: what to watch, what to throttle, what to roll back. Defines the on-call surface for Juno in production.

---

## Four levers

### 1. Stop conditions
`max_steps: 8` tool calls per escalation · max 3 reasoning iterations · at most 1 re-retrieval (only if the previous step produced a new, specific sub-question). Abort if the same tool fails 2× in a row. Hard timeout: **90s wall clock**. On any abort, hand to the PM with partial state — never silently retry.

### 2. Tool outputs
| Tool | Returns |
|---|---|
| `corpus.retrieve` | `{chunks:[{text, source_id, score}], summary, confidence}` |
| `strategy.get_doc` | `{pillars:[{id, clause}], not_doing:[...], version, confidence}` |
| `ranker.score` | `{rank, priority:P0-P3, cited_clause, confidence}` |
| `slack.read_thread` | `{messages:[{idx, author, text}], thread_len}` |
| `jira.draft_issue` | `{draft_id, status:"pending_pm_approval"}` |

Every retrieval-flavour tool returns a numeric `confidence` so the agent can reason about weak evidence instead of asserting it.

### 3. Confidence thresholds
- **≥ 80%** → post the ranked backlog to the PM review pane (`#pm-daily`) as ready-to-approve.
- **70–79%** → post to `#pm-juno-review` flagged "needs a second look," @on-call-pm.
- **< 70% on any P0** → block the item, mark `NEEDS CLARIFICATION`, require explicit PM approval.

Posting is never publishing — no threshold auto-writes to the roadmap.

### 4. North Star (re-read every loop)
> You are Juno. Your single goal is to surface the top-3 strategic risks from #escalations every weekday morning, ranked by strategic alignment and impact. Always cite the exact strategy clause and source ID behind every rank. Never invent customer names, ARR figures, or quotes. Escalate ambiguity to the PM. You propose; the PM disposes.

---

## Rules of engagement · the contract

**Agency permission** — Agent CAN draft a P0 risk summary, a ranked backlog, and a Jira stub in "pending approval" state. Agent **CANNOT** publish to the roadmap, auto-close or reassign threads, edit live Jira priorities, DM customers, or send any external comms.

**Access control** — READ: Slack `#escalations` (P0/P1), Strategy KB / corpus, Jira (ROCKET). WRITE: PM review pane + Jira draft stubs only. **CANNOT** write to customer records, contracts, or any external system. Every write is gated on explicit PM approval.

**Fallback protocols** — After 3 failed retrievals → degrade to cautious mode (no priorities, just thread links + "insufficient evidence"). After 2 tool errors → abort, post partial state and the error to `#pm-juno-review`. Strategy doc missing or stale → visibly-flagged Quality Mode, confidence N/A. **Never fabricate a rationale to fill a gap.**

**Checkpoints** — Any thread mentioning churn, legal, contracts, or security → PM approval before ranking. Any P0 with confidence < 70% → PM review. Any run where the PM overrides 2+ items → pause auto-posting and page the PM; the strategy doc, not the model, is likely the problem.

---

## Observability — what we trace

| Field | Notes |
|---|---|
| Trace ID | One per escalation run; links Slack thread → retrieval → ranking → PM decision |
| Trigger payload | Channel, thread ID, tag (P0/P1), reply count, timestamp |
| Retrieved chunks | Source IDs + similarity scores for all K=8; flag any below threshold |
| Tool calls | Tool, args, latency, success/failure, retry count |
| Outputs | Ranked top-3 with priority, cited clause, source IDs |
| Confidence per risk | Numeric per item + run-level mean; bucketed high/mid/low |
| Latency (p95) | Split retrieval vs. generation — the budget is generation-bound |
| Tokens + cost | Per run and per day; alert on 2× week-over-week drift |
| **PM decision** | approve / edit / reject per item — the override log that feeds `06-evals/eval-stack.md` |

## Throttles

| Lever | Default | When to change |
|---|---|---|
| Concurrency | 3 escalations in flight | Raise only after 2 clean weeks at p95 < 3s; drop to 1 during an incident |
| Max tokens / run | 30k | Lower if cost drifts >2× baseline; raise only with PM sign-off |
| Tool-call budget / run | 8 | Never raise without re-running the eval stack — more calls means longer, less reviewable runs |
| Per-day spend cap | Soft alert at 80%, hard stop at 100% | Recalculate monthly against actual escalation volume |
| Top-K retrieval | 8 | Raise only if golden-set accuracy is retrieval-limited, not generation-limited |

## Kill switches

- **`juno.autopost.enabled`** → off when: the override rate on P0 items exceeds 20% in a week, or any fabricated citation is confirmed. Juno keeps drafting, but nothing reaches the review pane automatically.
- **`juno.ranking.enabled`** → off when: a hard gate fires (PII leakage, citation-check failure). Juno degrades to thread links only — no priorities, no rationale.
- **Rollback path:** revert to the last green golden-set build (tagged in `06-evals/golden-set/`), disable auto-post, and re-run the weekly human-eval batch on the last 50 runs before re-enabling. PM owns the re-enable decision.

## On-call playbook

1. **Symptom:** PM reports a priority that "makes no sense," or the override rate alert fires.
2. **First check:** pull the Trace ID. Did retrieval return sources above threshold, and does the cited clause actually support the rank? Distinguish *bad retrieval* (fix the corpus) from *bad reasoning* (fix the prompt/scoring) from *stale strategy* (fix the doc).
3. **Remediation:** if evidence is hollow → flip `juno.autopost.enabled` off and re-run in cautious mode. If the strategy doc is stale → re-index and re-run the affected escalations. If the golden set regressed → roll back to the last green build.
4. **Escalation path:** on-call PM → product lead if a customer-visible decision was made on a bad ranking; loop in Eng if it's a tool/format failure, since Eng owns format and citation checks per the eval stack.
