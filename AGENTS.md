# AGENTS.md — Daily-Line-Core Operating Constitution

This document governs all human, ChatGPT, Codex, agent, CI, and automation changes to Daily-Line-Core (DLC).

## 1. Mission

DLC is The Daily Line's cross-sport decision aggregation and product-assembly layer. It consumes sealed sport decision packages and point-in-time DDC market evidence, assembles the All Bets Prediction Scanner, optimizes EdgeStack combinations, and emits sealed product publication packages.

DLC must not become a sport-model repository, raw-provider acquisition layer, website renderer, or wagering execution system.

## 2. Required reading before changes

Read, in order:

1. `README.md`
2. `AGENTS.md`
3. `CODEX_START_HERE.md`
4. `docs/CURRENT_RESUME_POINT.md`
5. `docs/ARCHITECTURE_CERTIFICATION_LOG.md`
6. `docs/ARCHITECTURE.md`
7. `docs/OWNERSHIP_BOUNDARIES.md`
8. `docs/INTEGRATION_CONTRACTS.md`
9. `docs/EDGESTACK_PARLAY_OPTIMIZER_V1.md`
10. `docs/PREGAME_SIGNAL_HEALTH_INTEGRATION_V1.md`
11. `docs/FACT_ENGINE_GAME_INTELLIGENCE_V1.md`
12. `docs/POST_GAME_FEEDBACK_LOOP_V1.md`
13. relevant change/validation records.

## 3. Non-negotiable ownership boundaries

### DLC may own

- admission/validation of immutable sport decision packages;
- admission/reference of DDC market evidence;
- product-layer supported-market catalog;
- All Bets cross-sport assembly;
- 2–5 leg EdgeStack candidate generation;
- combination-level joint probability from approved upstream probabilities/joint evidence;
- cross-game/cross-sport combination optimization;
- actual provider combo/parlay quote comparison;
- Core / Value / Upside classifications;
- cross-sport product ranking/indexing;
- immutable `DailyLinePublicationPackage` sealing;
- product-level provenance and downstream consumer contracts;
- publication-safe fact admission, product ranking/diversity, and sealing for Game Intel surfaces.

### DLC must not own

- raw provider acquisition already owned by DDC;
- permanent sport team/player/game ontology;
- sport-native state/features;
- sport model training/inference;
- sport-specific TDL Unified Line production;
- individual-market sport decision models;
- sport Recommendation Gate semantics;
- sport-specific settlement interpretation;
- sport-specific predictive fact weighting or fact-to-probability adjustments;
- website/report/infographic rendering logic;
- social/video/marketing generation;
- bankroll/stake management in V1;
- automated wagering/order placement in V1.

When authority is ambiguous, stop at the boundary and record the unresolved decision. Do not silently duplicate another repository's logic.

## 4. Sealed-input rule

DLC consumes versioned immutable upstream artifacts. It may not reach around a `SportDecisionPackage` and re-read raw sport databases to reconstruct sport truth, and it may not bypass DDC provenance to scrape/reacquire market truth independently.

A later upstream revision creates a new package/revision. Historical input packages are never overwritten.

## 5. All Bets before EdgeStack

EdgeStack must never run from a hand-picked list.

The canonical order is:

1. admit sport decision packages;
2. admit/reference DDC market evidence;
3. build deterministic `AllBetsSnapshot` for every supported/modelable market;
4. apply leg eligibility from authoritative sport Recommendation Gates and DLC product policy;
5. generate valid 2–5 leg candidates;
6. calculate joint/dependency-aware probabilities;
7. compare actual provider combo/parlay quotes;
8. rank/publish qualifying EdgeStacks;
9. seal one immutable `DailyLinePublicationPackage`.

## 6. EdgeStack rules

- V1 EdgeStacks contain **2–5 legs only**.
- Cross-game and cross-sport combinations are first-class candidates.
- Same-event combinations require certified sport-authoritative joint/dependency evidence.
- Unsupported correlation fails closed.
- No leg may be added solely to increase payout.
- Every leg independently satisfies the sport's eligibility/Recommendation Gate requirements.
- A real provider Combo/parlay quote overrides synthetic multiplication as pricing authority.
- Hit probability and value are separate dimensions; neither alone determines ranking.

## 7. Point-in-time and provenance rules

Every product decision must preserve:

- prediction timestamp;
- sport data cutoff;
- market quote timestamp / available-at semantics;
- sport package ID/version/digest;
- DDC evidence/bundle refs;
- model/config/release provenance supplied upstream;
- quote/provider identity;
- dependency/joint-probability method/version;
- DLC policy/config version;
- publication package identity and supersession lineage.

Post-start, closing, or result information must never leak into an earlier pregame product snapshot.

## 8. Publication immutability

A sealed `DailyLinePublicationPackage` is immutable.

Late lineups, injury information, market movement, sport reruns, or quote changes produce a new package/revision linked through supersession. Downstream consumers must be able to prove which exact publication package they rendered.

## 9. Downstream consumer rule

Report, infographic, website/app, and The-Daily-Line-Automation consume sealed DLC product truth.

They may transform presentation but may not recalculate fair probabilities, alter sport gates, rerank EdgeStacks, or create new recommendation truth unless a future versioned contract explicitly grants that authority.

## 10. V1 exclusions

Do not silently implement:

- bankroll manager;
- Kelly/stake sizing;
- stop-loss/daily-loss rules;
- chase-prevention logic;
- wallet/account execution;
- automated wagering/order placement;
- personalized wagering amounts.

These are separate future capabilities.

## 11. Architecture-first / manual-first rule

Major capability architecture and contracts are reviewed before implementation.

DLC recommendation/publication behavior must be validated manually and historically before it becomes production-authoritative or automated through TDLA/Bridge workflows.

## 12. Pregame signal-health boundary

Sport-owned signal-health/regime detection may be carried into DLC only through versioned sealed sport contracts. DLC may expose certified uncertainty/regime metadata and use it in product ranking where policy is validated, but it may not reconstruct sport state, apply sport-specific percentage-point penalties, or override sport fair probabilities/Recommendation Gates.

## 13. Post-game feedback / no self-modification

Post-game results may generate immutable evaluation records, calibration evidence, miss-attribution findings, feature-watch signals, and research proposals.

A single game or EdgeStack result must never directly alter production sport-model weights, sport features, Recommendation Gate thresholds, DLC ranking weights, or provider policies.

Any proposed improvement must pass the owning repository's PIT backtest/champion-challenger/promotion process. DLC may aggregate and route evidence; it does not silently self-train from realized outcomes.

## 14. Change documentation

Every material change must leave durable human-readable memory.

Required locations:

- `docs/CHANGE_JOURNAL.md` — chronological material change record;
- `docs/CURRENT_RESUME_POINT.md` — exact continuation point;
- `docs/ARCHITECTURE_CERTIFICATION_LOG.md` — architecture/milestone authority status;
- architecture/ADR documents for durable authority/tradeoff changes.

A material record should capture timestamp, summary, reason, affected files/contracts, authority impact, validation evidence, risks/open questions, rollback/recovery where relevant, and next exact step.

## 15. Bridge rule

The GrokBot-OpenAI-Bridge currently uses one repository per Codex turn. Preserve that boundary when DLC is eventually registered.

Cross-repository work must be split into explicit repository-specific turns with immutable versioned handoffs. GrokBot must not silently broaden repository access.

DLC is **not yet authorized** for Bridge execution merely because the repository now exists. Bridge registration follows successful TDLA proving-ground acceptance and explicit owner authorization.

## 16. Quality direction

Initial implementation baseline is expected to use Python 3.12 with pytest, Ruff, strict mypy, deterministic contracts, immutable fixtures/artifacts, and reproducible dependencies. Exact runtime/package policy is finalized during DLC-0 review before implementation authority.

## 17. Definition of done

A material DLC change is not done until applicable architecture/contracts, tests/validation, provenance, change journal, certification status, and current resume point all agree.
