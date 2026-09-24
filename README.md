# Daily-Line-Core

Daily-Line-Core (DLC) is **The Daily Line's cross-sport decision aggregation and product-assembly layer**.

It consumes sealed prediction/decision packages from Daily-MLB, Daily-NFL, Daily-NCAAF, and future Daily-* sport engines plus point-in-time market evidence from Daily-Data-Core. DLC assembles the cross-sport **All Bets Prediction Scanner**, runs the **EdgeStack Parlay Optimizer**, builds cross-sport product recommendation views, and seals one immutable `DailyLinePublicationPackage` consumed by the report, infographic, website/app, and downstream automation systems.

> **EdgeStack — Stack the Edge. Not the Odds.**

## Current status

- Repository physically created and seeded on **2026-09-17**.
- Architecture was first staged in `OneVillage83/Daily-Data-Core/docs/daily_line_core/` while this repository did not yet exist.
- Canonical DLC architecture now lives here.
- **DLC-0 repository/architecture foundation: DOCUMENTED — REVIEW PENDING.**
- No production implementation is authoritative.
- No live provider/Kalshi integration is authorized yet.
- No automated wagering is part of V1.
- Bankroll/stake management is explicitly deferred.

The exact continuation point is `docs/CURRENT_RESUME_POINT.md`.

## Position in The Daily Line

```text
Daily-Data-Core -------------------------------+
market / odds / PIT evidence / provenance      |
                                               v
Daily-MLB ---------> sealed SportDecisionPackage \
Daily-NFL ---------> sealed SportDecisionPackage  +--> DAILY-LINE-CORE
Daily-NCAAF -------> sealed SportDecisionPackage /
Future Daily-* ----> sealed SportDecisionPackage/
                                                    |
                       +----------------------------+-------------------------+
                       |                            |                         |
                       v                            v                         v
                All Bets Scanner              EdgeStack                 Product Index
                                             Optimizer                  / Top Picks
                       \                            |                         /
                        +---------------------------+------------------------+
                                                    |
                                                    v
                                      DailyLinePublicationPackage
                                                    |
                    +-------------------------------+-------------------------------+
                    |                               |                               |
                    v                               v                               v
               Daily Report                    Infographic                    Website/App
                                                                                    |
                                                                                    v
                                                                    The-Daily-Line-Automation
                                                                    video/social/marketing
```

## Ownership summary

### Daily-Data-Core owns

Shared acquisition/evidence infrastructure, raw provenance, odds/exchange/prediction-market observations, generic market math, market timelines, weather, venue, travel/rest, and other sport-neutral facts.

### Daily-Model-Core owns

Shared independent-model research/governance infrastructure and generic modeling/calibration machinery through the independent calibrated fair-view boundary.

### Sport repositories own

Sport-native identity/state/features, models and simulations, TDL Unified Line outputs, individual-market fair probabilities/value interpretation, Recommendation Gate semantics, same-event joint evidence, explanations, and settlement interpretation.

### Daily-Line-Core owns

- sealed sport-package admission;
- DDC market-evidence admission/reference;
- cross-sport All Bets assembly;
- EdgeStack 2–5 leg candidate generation and optimization;
- cross-game/cross-sport combination ranking;
- provider combo/parlay quote comparison;
- cross-sport product ranking/indexing;
- immutable `DailyLinePublicationPackage` sealing;
- downstream consumer contracts and product-level provenance.

DLC may compute **combination-level** joint probability/value from approved upstream inputs. It may not overwrite a sport's individual fair probability or Recommendation Gate decision.

## Governing documents

Read in this order:

1. `AGENTS.md`
2. `CODEX_START_HERE.md`
3. `docs/ARCHITECTURE.md`
4. `docs/OWNERSHIP_BOUNDARIES.md`
5. `docs/INTEGRATION_CONTRACTS.md`
6. `docs/EDGESTACK_PARLAY_OPTIMIZER_V1.md`
7. `docs/FACT_ENGINE_GAME_INTELLIGENCE_V1.md` — Game Intel / Fact Engine cross-system architecture
8. `docs/IMPLEMENTATION_ROADMAP.md`
9. `docs/ARCHITECTURE_CERTIFICATION_LOG.md`
10. `docs/CHANGE_JOURNAL.md`
11. `docs/CURRENT_RESUME_POINT.md`

## Product surfaces

### All Bets Prediction Scanner

Every supported/modelable market across all admitted sport packages is published with its sport fair probability, market price/probability, edge/value fields where valid, Recommendation Gate, uncertainty, reason/provenance, quote timestamp, and support/degradation state.

### EdgeStack Parlay Optimizer

EdgeStack searches approved 2–5 leg combinations and compares correlation-adjusted model joint probability with real provider combo/parlay pricing. V1 supports Core, Value, and Upside classes plus ranking views for highest hit rate, best value, best balance, and best upside.

### Game Intel / Fact Engine

DLC also owns the cross-sport **product selection/sealing** layer for publication-safe facts emitted by sport repositories. Customer-facing surfaces use the label **Game Intel**.

The Fact Engine is deliberately split from predictive authority: sport repositories decide whether a structured fact is merely display context, a research candidate, or a validated model/gate feature. DLC may rank/select verified facts for presentation but may not give them sport prediction weight.

See `docs/FACT_ENGINE_GAME_INTELLIGENCE_V1.md`.

## Explicit V1 exclusions

DLC V1 does **not** include:

- bankroll management;
- stake/Kelly sizing;
- stop-loss or chase-prevention logic;
- personalized wagering amounts;
- wallet/account management;
- automatic wagering/order placement.

Those require separate future architecture and certification.

## Bridge note

The GrokBot-OpenAI-Bridge is intentionally being proven first against `The-Daily-Line-Automation`. DLC should be added to the Bridge's authorized repository catalog only after that proving-ground acceptance is complete and the owner explicitly authorizes expansion.
