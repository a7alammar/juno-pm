# Juno PM — Lovable Prototype

## Lovable share link
https://juno-pm-prototype.lovable.app

## GitHub source code
https://github.com/a7alammar/juno-pm-prototype

## What this prototype demonstrates
- The three-column dashboard: Raw User Transcripts → Structured Insights → Draft PRD
- The "Process Transcript" interaction triggering a ~1.5s loading state before the middle and right columns populate
- The Sarah / Data-Analyst transcript landing in the Raw Input column (153 words), then synthesised into ranked insight clusters (Priority + Sentiment tags) and a rendered, evidence-linked Opportunity Brief (v0.1)

## What it doesn't yet do (the "Beautiful Liar" problem)
The UI looks production-grade, but the AI summary has no real product brain. Fed Sarah's transcript, Juno ignores what she actually said — the **bright-blue nav bar that "hurts my eyes,"** the **'Export to CSV' crash that costs her hours,** and her **dark-mode request** — and instead returns the same generic Dana/Marco insight set. The real signal gets buried; a scripted output masquerades as synthesis. **Module 1 Section 04 (Prompting as Product Configuration) and the System Prompt Configurator tool fix this.**

## Screenshots
- `junodashboardempty.png` — awaiting-input state (three columns, persistent Process Transcript button)
- `junodashboardprocessed.png` — post-synthesis state (4 insight clusters + Opportunity Brief, Draft Ready)

## Design refinement path used
- **Lovable's built-in Themes only**  — the Option A "Build Your Own" prompt already specified dark-mode + a single accent colour + three fixed columns, so the base generation met the spec. Skipped a separate Path A/B pass to conserve free-tier credits.

## Open questions for M2
- **What this proves:** the UX shell and the synthesis *workflow* (messy multi-source input → ranked insights → evidence-linked brief) is coherent and desirable.
- **What it doesn't prove yet:** that an AI can produce *trustworthy* synthesis — as the Sarah run shows, the output is scripted, not model-generated, and misses the real signal. Whether "AI synthesis of messy PM inputs" is a genuine AI-fit bet (vs. dressed-up templating) is exactly what the M2 Decision Matrix pressure-tests.
