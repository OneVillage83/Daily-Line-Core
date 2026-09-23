# Daily-Line-Core Documentation Index

This directory contains the governing product/architecture documentation for Daily-Line-Core (DLC).

## Read order

1. `../README.md` — mission and system placement.
2. `../AGENTS.md` — repository constitution and hard boundaries.
3. `../CODEX_START_HERE.md` — agent entry point.
4. `CURRENT_RESUME_POINT.md` — exact continuation point.
5. `ARCHITECTURE_CERTIFICATION_LOG.md` — authority/status record.
6. `ARCHITECTURE.md` — system architecture.
7. `OWNERSHIP_BOUNDARIES.md` — repository responsibility split.
8. `INTEGRATION_CONTRACTS.md` — cross-repository contract intent.
9. `EDGESTACK_PARLAY_OPTIMIZER_V1.md` — EdgeStack / All Bets product architecture.
10. `PREGAME_SIGNAL_HEALTH_INTEGRATION_V1.md` — how DLC consumes sport-owned current-state/regime summaries without recalculating sport probabilities.
11. `POST_GAME_FEEDBACK_LOOP_V1.md` — post-game forensic attribution, calibration, PIT counterfactual and research-handoff architecture.
12. `POSTGAME_IMPROVEMENT_RECONCILIATION_20260920.md` — maps the seven NFL/MLB post-game refinements to existing owners to prevent duplicate systems.
13. `THE_DAILY_LINE_LIVE_ARCHITECTURE_V1.md` — cross-system architecture for Daily Data Live Core, sport-live engines, Daily-Line-Live-Core, contamination firewall, live modeling, synchronization, validation and release.
14. `IMPLEMENTATION_ROADMAP.md` — DLC-0 through DLC-12 sequence.
15. `CHANGE_JOURNAL.md` — durable chronological memory.
16. `EXTRACTION_PROVENANCE_20260917.md` — origin/extraction history from DDC staging.

## Current status

**DLC-0: DOCUMENTED — REVIEW PENDING.**

No production implementation or Bridge authority is granted by the repository seed.

**TDL-LIVE-0: DOCUMENTED — REVIEW PENDING.** The live architecture is supplemental cross-system design only and grants no repository creation, provider, implementation, recommendation, or production authority.

## Product boundary summary

DLC consumes sealed sport decision packages plus point-in-time DDC market evidence, assembles the All Bets Prediction Scanner, optimizes EdgeStack 2–5 leg combinations, builds cross-sport product rankings, and seals one immutable `DailyLinePublicationPackage` for downstream consumers.

Sport-owned pregame signal-health/regime output may be carried in a sealed `SportDecisionPackage` as optional publication-safe metadata. DLC may use certified uncertainty/regime metadata for All Bets/EdgeStack ranking and explanation, but it does not recalculate sport state or fair probability.

Report/infographic/website/TDLA render or distribute sealed DLC truth; they do not recompute it.
