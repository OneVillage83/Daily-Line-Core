# Daily-Line-Core Implementation Roadmap V1

**Status:** DOCUMENTED — IMPLEMENTATION AUTHORITY NOT YET GRANTED  
**Date:** 2026-09-17

This roadmap converts the DLC architecture into bounded milestones. Architecture review/certification comes before production implementation.

## DLC-0 — Repository bootstrap and governing contracts

Current phase.

Goals:

- seed repository constitution and architecture;
- preserve extraction provenance from DDC staging;
- review/freeze DDC / DMC / sport / DLC / website / TDLA ownership boundaries;
- define initial package/release baseline;
- define immutable contract/versioning rules;
- create exact continuation/status documentation.

Exit evidence:

- architecture review complete;
- ownership contradictions resolved;
- certification log updated;
- canonical contract work authorized.

## DLC-1 — Canonical cross-repository contracts

Implement schemas/fixtures for:

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

Acceptance:

- no sport-specific probability logic appears in DLC contracts;
- no bankroll/stake fields appear in V1;
- deterministic canonical identity/digest test vectors exist;
- valid/invalid compatibility fixtures exist.

## DLC-2 — Sport package admission

- validate schema/version/digest;
- validate PIT cutoffs;
- preserve sport authority and unsupported-market declarations;
- deterministic admission/rejection reason codes;
- support multiple sport package revisions without overwriting history.

## DLC-3 — DDC market-evidence admission

- ingest/reference DDC market snapshots and quote provenance;
- reconcile canonical sport market refs through approved mappings;
- preserve quote timestamps/freshness/provider identity;
- reject stale/incompatible market evidence;
- do not duplicate DDC provider acquisition logic.

## DLC-4 — All Bets Prediction Scanner

- enumerate every supported/modelable market across admitted sport packages;
- join sport fair probabilities/gates to DDC market prices;
- calculate product-layer comparison fields without altering sport probabilities;
- emit deterministic `AllBetsSnapshot`;
- expose filtering/ranking metadata for downstream report/website.

Required views include probability, market probability/price, edge, EV where valid, gate, reason, uncertainty, quote time and provenance.

## DLC-5 — EdgeStack candidate engine

- deterministic 2–5 leg enumeration;
- cross-game and cross-sport combinations;
- contradiction/duplicate exposure detection;
- bounded/prunable search;
- approved legs only;
- deterministic candidate IDs.

## DLC-6 — Joint probability / dependency engine

- independent-event product path;
- sport-authoritative simulation/joint-sample adapter;
- calibrated dependency/correlation adapter;
- uncertainty output;
- fail closed when same-event dependency is unsupported.

## DLC-7 — Provider Combo/parlay quote adapters

Priority:

1. Kalshi Combo/RFQ;
2. supported sportsbook parlay quote sources.

Requirements:

- normalize quote identity/multiplier/price;
- preserve timestamp/expiry;
- fee-aware break-even where supported;
- compare actual quote with model joint probability;
- retain standalone-price multiplication only as diagnostic.

Do not build live provider integration before DLC-0/1 contracts are frozen.

## DLC-8 — EdgeStack optimizer and classifications

Implement:

- `CORE`;
- `VALUE`;
- `UPSIDE`;
- `HIGHEST_HIT_RATE`;
- `BEST_VALUE`;
- `BEST_BALANCE`;
- `BEST_UPSIDE`.

Underlying metrics remain visible: hit probability, break-even, EV, uncertainty, correlation quality, freshness, leg count and multiplier.

## DLC-9 — Publication package sealing

Build immutable `DailyLinePublicationPackage` containing:

- admitted sport package refs;
- market evidence refs;
- All Bets snapshot;
- individual recommendation/top-pick index;
- EdgeStack catalog;
- report/infographic claims;
- website views/refs;
- automation-safe fact refs;
- provenance/degradation state;
- supersession lineage.

No renderer may become the authority for a value calculation.

## DLC-10 — Downstream consumer contracts

Repository-specific follow-up turns:

- report generator consumes sealed package;
- infographic renderer consumes sealed package;
- website ingests/displays sealed package;
- TDLA consumes approved facts for video/social/marketing automation.

Downstream consumers may transform presentation but may not recompute probabilities/gates/EdgeStacks.

## DLC-11 — PIT historical evaluation / certification

- replay exact historical cutoffs;
- evaluate individual-market calibration;
- evaluate Recommendation Gate performance;
- evaluate EdgeStack calibration by class/leg count;
- same-game vs cross-game;
- cross-sport vs same-sport;
- quote break-even vs realized results;
- gross/net EV where defensible;
- stale/availability audit;
- settlement correctness;
- promotion thresholds locked before production authority.

## DLC-12 — Bridge + operational onboarding

Only after the GrokBot-OpenAI-Bridge TDLA proving-ground acceptance and explicit owner authorization:

- register DLC in the Bridge authorized repository catalog;
- add DLC workstreams;
- prove one-repository-per-turn routing;
- define handoffs from sport repos/DDC to DLC and DLC to website/automation;
- later integrate operational orchestration only through certified versioned contracts.

## Explicit V1 exclusions

Do not implement during DLC-0 through DLC-12:

- bankroll manager;
- stake/Kelly sizing;
- stop-loss/chase controls;
- automated order placement;
- account/wallet execution.

## Exact current resume

See `docs/CURRENT_RESUME_POINT.md`.
