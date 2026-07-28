## RAG Architecture · AI PRD

### Data Requirements
**Knowledge base · sources + quantity**
Juno indexes RocketShip's strategy corpus: the current Strategy One-Pager (north star + 3 pillars + "what we're NOT doing"), plus the last 90 days of #escalations threads, support tickets, and interview notes. Scoped deliberately — the strategy doc is ground truth for WHY something matters; the 90-day window keeps signal fresh and the index small, precise, and cheap. Not "index everything."

**Sync frequency & refresh**
Strategy doc: re-index on every edit (event-driven) — a pillar change must flip priorities immediately. Slack/tickets: refresh daily or on any new P0/P1. Stale strategy is worse than none: it would let Juno cite a retired pillar.

### Model Requirements
**Retrieval strategy:** Hybrid

**Justification**
RAG indexes the corpus so every ranking cites a specific source chunk; long-context handles single-doc deep reads (e.g. reading the whole Strategy One-Pager to map a priority to the right pillar). Pure long-context can't scale across 90 days of threads; pure modular RAG loses the whole-doc coherence needed to attribute a priority to a pillar. Hybrid fits How (cite + reason), Where (Slack/Notion/Jira), Scale (90-day corpus).

### AI Costs & Latency
**Top-K + latency**
Top-K = 8 retrieved chunks per prioritisation; p95 < 3s for a ranked backlog. Cap K at 8 to control token cost and avoid diluting the ranking with weak matches.

Latency budget is generation-bound, not retrieval-bound: hybrid top-8 retrieval is sub-second; the LLM synthesis of the ranked, cited backlog is the p95 risk. Mitigation: stream results row-by-row so the PM sees the top item well under 3s while the rest render.

**Retrieval pattern:** Hybrid (Semantic + Keyword)

### AI User Experience
**Grounded trust requirement**
Every priority Juno outputs cites the exact strategy clause and source ID that justifies its rank (e.g. "P0 — cites Reliability First"). When retrieval returns nothing above the similarity threshold, Juno does NOT guess — it flags the item "insufficient evidence," ranks it NEEDS CLARIFICATION, and escalates to the PM rather than inventing a rationale.

**Fail-safe behaviour**
When retrieval returns nothing above the similarity threshold, Juno never fabricates a rationale. It: (1) labels the item "insufficient evidence — not ranked"; (2) shows the empty-retrieval state explicitly ("no strategy clause matched") instead of a confident guess; (3) routes the item to the PM as NEEDS CLARIFICATION with the specific question to resolve; and (4) still ranks the items it CAN ground, so one gap never blocks the whole backlog. If the strategy doc is missing entirely, Juno degrades to a visibly-flagged "quality-only" mode rather than silently inventing priorities.
