# Daily-Line-Core Ownership Boundaries

## Principle

DLC owns **cross-sport product assembly after sport intelligence is sealed**.

If a capability requires interpreting raw sport state, training/predicting a sport outcome, defining sport settlement, or deciding an individual sport market's Recommendation Gate, it belongs upstream in the sport/model layer.

If a capability requires shared raw/provider evidence acquisition or generic market math, it belongs in Daily-Data-Core.

If a capability combines already-authoritative sport decisions into one cross-sport product view, it belongs in DLC.

## Daily-Data-Core (DDC)

DDC owns:

- provider acquisition and transport;
- raw evidence and immutable provenance;
- generic odds/exchange/prediction-market observations;
- implied probability, no-vig, hold, freshness, consensus and other sport-neutral market math;
- sport-agnostic line-history and market-timeline evidence;
- shared weather/venue/travel/rest facts.

DDC does **not** own:

- sport fair probabilities;
- sport-specific EV/value decisions;
- sport Recommendation Gates;
- EdgeStack selection/ranking;
- final cross-sport publication packages.

## Daily-Model-Core (DMC)

DMC owns shared independent modeling/research governance through the calibrated fair-view boundary, as defined by DMC architecture.

DMC does **not** own:

- market-aware cross-sport product assembly;
- All Bets product indexing;
- EdgeStack optimization;
- downstream report/website truth.

## Sport repositories

Daily-MLB, Daily-NFL, Daily-NCAAF, and future Daily-* repositories own:

- canonical sport identity/state;
- sport features;
- sport model training/inference;
- sport simulations;
- TDL Unified Line outputs;
- individual-market fair probabilities/fair lines;
- sport-specific market-aware decision models;
- Line Timing Models where applicable;
- individual-market EV/value interpretation;
- Recommendation Gate semantics and outputs;
- sport-specific explanation claims;
- same-event simulation/joint evidence for correlated outcomes;
- sport settlement interpretation.

They export sealed `SportDecisionPackage` artifacts rather than exposing mutable internal state as DLC authority.

## Daily-Line-Core (DLC)

DLC owns:

- validating/admitting sealed sport decision packages;
- referencing/admitting sealed DDC market evidence;
- cross-sport supported-market product catalog;
- `AllBetsSnapshot` assembly;
- product-layer comparison fields that do not alter upstream sport probabilities;
- EdgeStack candidate eligibility from already-approved legs;
- deterministic 2–5 leg combination generation;
- combination-level joint probability using sport-authoritative dependency inputs;
- cross-game and cross-sport optimization;
- provider Combo/parlay quote comparison;
- Core / Value / Upside EdgeStack classification;
- highest-hit-rate / best-value / best-balance / best-upside product views;
- cross-sport top-pick/recommendation index derived from sealed sport decisions;
- `DailyLinePublicationPackage` sealing;
- product-level provenance, degradation and supersession state;
- versioned consumer handoff contracts.

DLC may **not**:

- acquire raw provider data already owned by DDC;
- infer missing sport probabilities;
- alter a sport's Recommendation Gate to improve product optics;
- reconstruct sport state from raw feeds;
- silently promote an unsupported market;
- allow a weak leg into EdgeStack merely because the payout is attractive.

## Downstream report / infographic

They own rendering/layout only.

They may:

- select approved layouts;
- summarize/rephrase approved claims within contract;
- render tables/charts/cards;
- produce PDFs/images/HTML.

They may not:

- recalculate probability or EV;
- rerun Recommendation Gates;
- rerank EdgeStacks;
- hide required audit/result information;
- invent new picks.

## Website / app

Owns customer-facing presentation, search/filtering, archive browsing, account/product UX and result display.

The website is not the authoritative decision engine. It consumes sealed DLC publication artifacts and records which package/version it displayed.

## The-Daily-Line-Automation (TDLA)

TDLA owns downstream operational/content automation: scheduling, social/video/content workflows, publication coordination and related operational infrastructure under its own architecture.

TDLA may consume sealed `DailyLinePublicationPackage` or derivative approved fact packages.

TDLA may not calculate:

- sport fair probabilities;
- sport Recommendation Gates;
- All Bets product truth;
- EdgeStack joint probabilities/rankings.

## Market identity boundary

DDC preserves provider market/event identities and evidence. Sport repos own canonical sport identity/reconciliation. DLC uses explicit versioned mappings/refs supplied through approved contracts. DLC must not become a hidden global sport identity database merely because it sees all sports.

## Same-event dependency boundary

DLC owns combination-level use of dependency evidence; sport repos own sport-native production of that evidence.

Examples:

- NFL may export joint samples for quarterback passing yards and receiver yards.
- MLB may export simulation/joint information for pitcher, run-line and total relationships.

DLC may consume those outputs to price a combination. It may not invent sport-specific dependency semantics when upstream evidence is absent.

## Publication boundary

DLC's authoritative external boundary is the sealed `DailyLinePublicationPackage`.

Every downstream product must be traceable to the exact package it consumed. A downstream renderer's database row, PDF, image, video or web page does not supersede DLC truth.

## V1 wagering boundary

DLC V1 ends at recommendation/product publication.

Out of scope:

- bankroll management;
- personalized sizing;
- Kelly sizing;
- stop-loss/chase logic;
- wallet/account connections;
- automated bet/order placement.
