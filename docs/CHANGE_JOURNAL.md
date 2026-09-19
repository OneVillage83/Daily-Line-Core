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
