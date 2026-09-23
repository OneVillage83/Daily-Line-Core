# Daily-Line-Core Change Journal

Purpose: durable human-readable memory for material DLC changes.

## Required record template

```markdown
## YYYY-MM-DDTHH:MM:SSZ — <title>

- **Change ID:**
- **Area:**
- **Summary:**
- **Reason:**
- **Files/components affected:**
- **Authority/contract impact:**
- **Data/migration impact:**
- **Operational impact:**
- **Validation/evidence:**
- **Risks/open questions:**
- **Rollback/recovery:**
- **Next exact step:**
```

---

## 2026-09-17T20:00:00Z — Daily-Line-Core repository seeded from staged architecture

- **Change ID:** initial DLC repository seed.
- **Area:** architecture / governance / documentation / repository bootstrap.
- **Summary:** Created the canonical Daily-Line-Core repository foundation from architecture first staged in `OneVillage83/Daily-Data-Core/docs/daily_line_core/`. Added repository mission, operating constitution, Codex entry point, architecture, ownership boundaries, integration contracts, canonical EdgeStack architecture, implementation roadmap, certification log, change journal, current resume point, extraction provenance, and minimal package skeleton metadata.
- **Reason:** DLC is a distinct cross-sport decision aggregation/product-assembly layer and should not live inside Daily-Data-Core or The-Daily-Line-Automation.
- **Files/components affected:** repository foundation and governing docs.
- **Authority/contract impact:** Establishes DLC as the intended canonical logical owner of All Bets cross-sport assembly, EdgeStack optimization, product ranking/indexing, and sealed `DailyLinePublicationPackage` assembly. Does not grant implementation or production authority. DDC remains evidence infrastructure; sport repos retain sport intelligence; TDLA remains downstream automation.
- **Data/migration impact:** None. No production database/schema/provider integration created.
- **Operational impact:** None. No live market access, report/website writes, automation workflows or wagering actions enabled.
- **Validation/evidence:** Seed derived from DDC staging docs created 2026-09-17 and the explicit owner decision to create `OneVillage83/Daily-Line-Core` as a peer repository.
- **Risks/open questions:** Exact schema fields, package runtime baseline, persistence model, provider Combo/RFQ interfaces, cross-repository compatibility matrices, and promotion thresholds remain for DLC-0/1 review. Bridge registration remains intentionally deferred until TDLA proving-ground acceptance.
- **Rollback/recovery:** Documentation-first seed; future corrections should be recorded as superseding architecture rather than erasing history.
- **Next exact step:** Perform DLC-0 architecture/ownership conformance review, then freeze canonical V1 contract schemas/fixtures before implementing All Bets or EdgeStack business logic.


---

## 2026-09-19T11:10:00Z — Pregame signal health / regime integration documented

- **Change ID:** SHARE cross-repository integration documentation.
- **Area:** architecture / sport-to-DLC contracts / EdgeStack uncertainty / post-game feedback.
- **Summary:** Added DLC's consumption boundary for sport-owned pregame signal-health/regime summaries; extended the conceptual SportDecisionPackage to carry optional publication-safe signal-health metadata; connected the post-game feedback loop back to sport-owned regime research; and documented how All Bets/EdgeStack may use certified uncertainty metadata without recalculating sport probability.
- **Reason:** The Toronto-Texas audit showed that season-long model quality can remain strong while current-state evidence materially changes confidence. Daily-MLB now has a dedicated SHARE architecture; DLC needs a strict downstream boundary for using that output.
- **Files/components affected:** docs/PREGAME_SIGNAL_HEALTH_INTEGRATION_V1.md; docs/INTEGRATION_CONTRACTS.md; docs/ARCHITECTURE.md; docs/POST_GAME_FEEDBACK_LOOP_V1.md; docs/README.md; docs/CURRENT_RESUME_POINT.md; docs/ARCHITECTURE_CERTIFICATION_LOG.md.
- **Authority/contract impact:** Signal detection and probability adjustment remain sport-owned. DLC may display and rank with sport-authored uncertainty/regime metadata after compatibility review, but cannot apply sport-specific probability penalties or override the sport Recommendation Gate.
- **Data/migration impact:** None; documentation only.
- **Operational impact:** None; no All Bets/EdgeStack runtime or live provider behavior changed.
- **Validation/evidence:** Cross-checked against Daily-MLB ModelFeatureSet and Unified Modeling architecture, DMC conditional-importance/ablation roadmap, and DLC ownership boundaries.
- **Risks/open questions:** Exact summary schema/ranges, compatibility versioning, and validated EdgeStack policy thresholds remain for DLC-1/DLC-8.
- **Rollback/recovery:** Supersede/version these supplemental docs; do not rewrite prior publication records.
- **Next exact step:** DLC still resumes at DLC-0 architecture/ownership conformance review. Signal-health implementation remains upstream in sport repos and later compatibility work in DLC-1.


---

## 2026-09-21T02:15:00Z — Seven post-game refinements reconciled with existing owners

- **Change ID:** 2026-09-20 cross-sport post-game research reconciliation.
- **Area:** post-game feedback / NFL current-state modeling / MLB SHARE / Recommendation Gate / DMC conditional trust.
- **Summary:** Audited the seven improvement ideas from the Sept. 20 NFL/MLB post-game review against existing repositories. No duplicate early-season, pressure, offensive-system, pitcher-health, bullpen, recommendation-gate, or feedback system was added. Existing owners were strengthened through sport-specific addenda, roadmap placement, SHARE/bullpen refinements, DMC sample-maturity context, and DLC layer-specific attribution.
- **Reason:** Several misses exposed real research questions, but the architecture already contains most required state and decision boundaries. New parallel engines would fragment authority and double count evidence.
- **Files/components affected:** `docs/POSTGAME_IMPROVEMENT_RECONCILIATION_20260920.md`; `docs/POST_GAME_FEEDBACK_LOOP_V1.md`; Daily-NFL post-game refinement addendum and roadmap; Daily-MLB post-game refinement addendum/SHARE; DMC conditional-trust supplement.
- **Authority/contract impact:** No production authority changed. Sport repositories remain authoritative for sport state/probability/gates; DLC remains post-game/product evaluator; DMC remains generic modeling governance.
- **Data/migration impact:** None.
- **Operational impact:** None.
- **Validation/evidence:** Repository architecture and machine feature registry were inspected before documentation changes. Daily-NFL already has active early-season-prior projections and reserved pressure/NGS/matchup features; advanced tracking features remain unavailable pending certified source/rights. Daily-MLB already has SHARE, bullpen features and gate confidence boundaries.
- **Risks/open questions:** Exact learned prior-decay policy, NGS licensing/PIT coverage, pressure feature activation, MLB reliever Statcast schema successor, and future gate thresholds require historical/prospective validation.
- **Rollback/recovery:** Supersede these research docs if future evidence changes the design; do not rewrite frozen production/certified artifacts.
- **Next exact step:** Preserve each repository's current implementation resume. Test these refinements only when their existing roadmap phase is explicitly authorized.


---

## 2026-09-23T20:07:00Z — TDL Shadow Audit Ledger V1 started

- **Change ID:** shadow-audit-ledger-v1.
- **Area:** research / manual audit evaluation / recommendation-gate evidence.
- **Summary:** Added a research-only immutable-style ledger for manual TDL Shadow Audits. The ledger defines prediction-vs-decision evaluation fields, retains RECOMMEND/LEAN/PASS/AVOID/WATCH states, adds a reconstructed 2026-09-20 through 2026-09-22 baseline, and seals the 2026-09-23 MLB pregame snapshot at 13:07 America/Los_Angeles. Already-started games are explicitly excluded from the pregame snapshot rather than backfilled.
- **Reason:** The Sunday-through-Tuesday audits showed that likely-winner accuracy and price/actionability are separate learning problems. Future audits need exact contemporaneous probability, price, Gate, revision and settlement evidence instead of chat-only reconstruction.
- **Files/components affected:** `research/shadow_audits/README.md`; `research/shadow_audits/2026-09-20_to_2026-09-22_reconstructed_baseline.md`; `research/shadow_audits/2026-09-23_mlb_pregame_1307PT.json`.
- **Authority/contract impact:** None. The ledger is explicitly research-only and cannot supersede sport model registries, certified Recommendation Gates, publication packages or production authority.
- **Data/migration impact:** None. No production schema or database migration.
- **Operational impact:** None. No provider execution, publishing or wagering action.
- **Validation/evidence:** Snapshot methodology follows the market-firewall, immutable-revision and prediction-vs-recommendation principles already documented across Daily MLB/DMC/DLC. The historical baseline is permanently labeled reconstructed because exact PIT artifacts did not yet exist.
- **Risks/open questions:** The shadow formulas are approximations, not certified models. Exact Brier/log-loss/CLV evaluation requires contemporaneous market closes and immutable settlement appendices. Gate thresholds remain research policy, not production policy.
- **Rollback/recovery:** Supersede/version research snapshots; never rewrite earlier research rows to match later information.
- **Next exact step:** Continue generating timestamped MLB Shadow Audit snapshots, append postgame settlement/evaluation, and compare Gate buckets without changing production authority.
