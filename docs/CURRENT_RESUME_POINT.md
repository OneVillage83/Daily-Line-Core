# Daily-Line-Core — Current Resume Point

Last updated: 2026-09-24 (America/Los_Angeles)  
Authority: this file is the single exact continuation point for unfinished DLC work. It does not by itself certify architecture or implementation.

## Current state

- `OneVillage83/Daily-Line-Core` now exists and is the canonical repository for DLC architecture.
- Initial architecture was staged first in `OneVillage83/Daily-Data-Core/docs/daily_line_core/` and extracted here on 2026-09-17.
- DLC mission/ownership, All Bets, EdgeStack, publication-package, pregame signal-health consumption, and post-game feedback architecture are documented.
- **DLC-0 is DOCUMENTED — REVIEW PENDING.**
- No production implementation is authoritative.
- No live Kalshi/provider Combo integration is authorized.
- No website/report/infographic/TDLA consumer integration is authorized from DLC yet.
- DLC is not yet registered in the GrokBot-OpenAI-Bridge authorized catalog; that remains intentionally deferred until TDLA proving-ground acceptance and explicit owner authorization.

## Locked product decisions

1. **Daily-Line-Core is the cross-sport decision aggregation/product-assembly layer.**
2. DDC remains shared evidence/acquisition/generic market infrastructure.
3. Daily-Model-Core remains shared independent modeling infrastructure through the calibrated fair-view boundary.
4. Sport repos own sport-native models, TDL Unified Lines, individual-market value/Recommendation Gate semantics, same-event joint evidence, explanations and settlement.
5. DLC consumes sealed sport decisions + DDC market evidence.
6. DLC builds the **All Bets Prediction Scanner** before combination optimization.
7. DLC owns the **EdgeStack Parlay Optimizer**.
8. EdgeStack V1 uses **2–5 legs**.
9. Cross-game and cross-sport combinations are first-class candidates.
10. Same-event combinations require explicit sport-authoritative dependency/joint evidence.
11. Product views include `HIGHEST_HIT_RATE`, `BEST_VALUE`, `BEST_BALANCE`, and `BEST_UPSIDE`; EdgeStack classes include `CORE`, `VALUE`, and `UPSIDE`.
12. DLC seals one immutable `DailyLinePublicationPackage` consumed by report, infographic, website/app and TDLA.
13. Downstream consumers render/transform sealed truth; they do not recompute probabilities, gates or EdgeStacks.
14. Product tagline: **“Stack the Edge. Not the Odds.”**
15. Bankroll management, stake sizing, stop-loss/chase controls and automated wagering are explicitly deferred from V1.
16. Sport repositories may publish optional PIT-bound signal-health/regime summaries; DLC may expose/use certified uncertainty metadata but may not recalculate sport state or fair probability.
17. DLC owns cross-sport/product post-game evaluation and feedback aggregation; single-game outcomes cannot directly mutate sport models or gates.
18. The **TDL Fact Engine / Game Intel** is split into publication and predictive-research lanes: sports own fact semantics/predictive promotion; DLC owns cross-sport admission, display ranking/diversity and publication sealing; website/TDLA render sealed facts.

## Exact next step

Do **not** jump directly to EdgeStack implementation, Kalshi RFQ calls, live provider integration, report UI, infographic rendering, website UI, or TDLA automation.

Resume at:

> **DLC-0 architecture/ownership conformance review.** Review the seeded architecture against current `Daily-Data-Core`, `Daily-Model-Core`, Daily-MLB, Daily-NFL, Daily-NCAAF, website and TDLA boundaries. Correct contradictions through versioned documentation. If clean, mark DLC-0 `ARCHITECTURE-CERTIFIED` and proceed to DLC-1 canonical contracts/fixtures.

## Supplemental architecture now included

- `docs/PREGAME_SIGNAL_HEALTH_INTEGRATION_V1.md` — DLC consumption boundary for sport-owned current-state/regime summaries.
- `docs/POST_GAME_FEEDBACK_LOOP_V1.md` — immutable outcome audit, miss attribution, PIT counterfactual and research-handoff loop.
- `docs/POSTGAME_IMPROVEMENT_RECONCILIATION_20260920.md` — anti-duplication mapping for the seven 2026-09-20 NFL/MLB post-game refinements.
- `docs/FACT_ENGINE_GAME_INTELLIGENCE_V1.md` — Game Intel structured-fact publication architecture plus a future zero-by-default sport-owned predictive research lane.

These do not change the exact next step below or authorize implementation.

## DLC-1 after certification

Freeze and implement versioned schemas/fixtures for:

- `SportDecisionPackage`;
- `MarketEvidenceBundleRef`;
- `AllBetsSnapshot`;
- `EdgeStackLeg`;
- `EdgeStackQuote`;
- `EdgeStackCandidate`;
- `EdgeStackRecommendation`;
- `EdgeStackCatalog`;
- `DailyLinePublicationPackage`;
- provenance/supersession envelopes.

No sport-specific model logic or bankroll/stake fields may enter these contracts.

## Bridge instruction

When the Bridge is eventually authorized for DLC, preserve one repository per Codex turn. Cross-repository prerequisites or consumer changes must become explicit handoffs rather than silent multi-repo edits.
