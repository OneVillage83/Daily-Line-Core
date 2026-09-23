# The Daily Line Live — Cross-System Architecture V1

**Status:** DOCUMENTED — REVIEW PENDING  
**Date:** 2026-09-23  
**Canonical staging repository:** `OneVillage83/Daily-Line-Core`  
**Scope:** Cross-system live forecasting/product architecture  
**Implementation authority:** NONE — documentation only

---

# 1. Purpose

The Daily Line Live (TDL Live) is a proposed first-class sibling of the existing pregame Daily Line system.

Its purpose is to continuously ingest authoritative in-game state, update sport-specific fair probabilities and outcome distributions, compare those independent live forecasts against synchronized in-play market prices, and emit immutable live recommendation/product packages without contaminating the pregame prediction path.

The governing principle is:

> **The Daily Line Live is a separate point-in-time forecasting and decision system. It may consume sealed Pregame TDL predictions as immutable priors, but live state, live market evidence, live model outputs, and live decisions may never alter, contaminate, or retrospectively rewrite Pregame TDL evidence or predictions.**

TDL Live should eventually support MLB, NFL, NBA, NCAAF, NHL, soccer, tennis and future sports through shared live evidence infrastructure plus sport-owned live interpretation.

---

# 2. High-level topology

```text
                         THE DAILY LINE
                               |
              +----------------+----------------+
              |                                 |
              v                                 v
        PREGAME SYSTEM                      LIVE SYSTEM
              |                                 |
       Daily Data Core                 Daily Data Live Core
            (DDC)                            (DDLC)
              |                                 |
      Daily-[Sport]                    Daily-[Sport]-Live
              |                                 |
      Sport Predictions                Sport Live Predictions
              |                                 |
      Sport Decisions                  Sport Live Decisions
              |                                 |
      Daily-Line-Core                Daily-Line-Live-Core
           (DLC)                           (DLLC)
              |                                 |
              +----------------+----------------+
                               |
                               v
                    THE DAILY LINE PRODUCT
                  report / website / app /
                  alerts / archive / automation
```

The pregame and live systems are peers.

The only intentional predictive bridge is:

```text
SEALED PREGAME FORECAST
          |
          | immutable read-only prior
          v
    LIVE SPORT MODEL
```

There is no reverse predictive path.

---

# 3. Proposed repository ownership

The future repository layout is:

| Repository | Responsibility |
|---|---|
| `Daily-Data-Core` | Existing shared/pregame evidence and market infrastructure |
| `Daily-Data-Live-Core` | Shared real-time event/market evidence, clocks, sequencing, latency and synchronization |
| `Daily-MLB` | Existing pregame MLB intelligence and decisions |
| `Daily-MLB-Live` | MLB in-game state, live features, trajectory/simulation, live fair lines and live gate |
| `Daily-NFL` | Existing pregame NFL intelligence |
| `Daily-NFL-Live` | NFL play-state, live features, live fair lines and live gate |
| `Daily-NBA-Live` | Future NBA live engine |
| `Daily-NCAAF-Live` | Future NCAAF live engine |
| `Daily-NHL-Live` | Future NHL live engine |
| future `Daily-*-Live` | Sport-owned live interpretation |
| `Daily-Line-Core` | Existing cross-sport pregame product assembly |
| `Daily-Line-Live-Core` | Cross-sport live scanner, live ranking and later Live EdgeStack |
| `Daily-Model-Core` | Generic model/OOS/calibration/ensemble governance with strict PRE_GAME vs LIVE scopes |

A separate Live Model Core is not required initially. Daily-Model-Core should provide generic machinery under isolated live datasets, registries, scopes, artifacts and promotion evidence.

---

# 4. Contamination firewall

This is a non-negotiable architecture boundary.

## 4.1 Allowed direction

Pregame may expose a sealed immutable prior:

```text
PregamePriorReferenceV1

event_id
sport
pregame_prediction_id
prediction_created_at
model_version
ensemble_version
calibration_version

home_win_probability
away_win_probability

score_distribution_ref?
margin_distribution_ref?
total_distribution_ref?

prediction_checksum
feature_snapshot_checksum
```

Live may read this artifact.

Live may not mutate or supersede it.

## 4.2 Prohibited direction

Live state, live markets, live model output and final outcomes must never:

- overwrite pregame predictions;
- overwrite pregame feature snapshots;
- modify the pregame model registry;
- change a sealed pregame Recommendation Gate result;
- enter pregame predictive features during the same event;
- rewrite earlier market evidence;
- retroactively change the information cutoff;
- become training evidence for a pregame model except through a later governed historical/PIT training pipeline.

## 4.3 Explicit namespaces

Every prediction/model/artifact must declare scope:

```text
model_scope = PRE_GAME
```

or

```text
model_scope = LIVE
```

Example identifiers:

```text
tdl:pregame:mlb:<event_id>:<prediction_id>
tdl:live:mlb:<event_id>:<state_sequence>:<prediction_id>
```

The shared sport event identity may remain common, but evidence/prediction namespaces must never be ambiguous.

---

# 5. Daily Data Live Core (DDLC)

DDLC is the live equivalent of DDC.

Its job is to preserve and normalize real-time facts and market evidence. It is not a sports prediction engine.

## 5.1 DDLC owns

- real-time provider interfaces;
- polling and streaming/WebSocket support;
- reconnect/backoff/rate-limit handling;
- provider sequence handling;
- dropped-message detection;
- exact raw evidence;
- provider health;
- provider/event clocks;
- publication/observation/availability timestamps;
- latency measurements;
- generic event status;
- live market quotes;
- quote suspension/activation state;
- immutable market history;
- state-market temporal binding primitives.

DDLC does not interpret baseball outs, football downs, basketball possessions, hockey penalties or other sport-specific semantics.

## 5.2 Generic live evidence envelope

```text
LiveEvidenceEnvelopeV1

provider
dataset_key
external_event_id

provider_sequence?
provider_event_time?
published_at?
observed_at
available_at

payload_checksum
raw_evidence_ref

provider_latency_ms?
ingestion_latency_ms?
contract_version
```

Raw evidence is immutable.

A provider correction creates a new observation; it never rewrites prior evidence.

## 5.3 Generic live event status

DDLC may normalize neutral states such as:

```text
scheduled
pregame
live
paused
delayed
suspended
final
cancelled
unknown
```

Sport-specific interpretation stays downstream.

---

# 6. Live market evidence

DDLC should own a canonical in-play quote contract.

Conceptually:

```text
LiveMarketQuoteV1

event_id
provider
book_or_venue

market_type
selection
line?
price

quote_status
active_or_suspended

provider_market_updated_at?
provider_book_updated_at?
published_at?
observed_at
available_at

quote_age_ms
raw_evidence_ref
quote_checksum
```

Every quote is append-only.

Repeated identical quotes are valid observations because they preserve what the provider was still publishing at each time.

---

# 7. State-price synchronization

Live betting cannot compare a fair probability calculated from one game state with a price published for an earlier state.

This is a first-class subsystem.

Conceptual contract:

```text
LiveStateMarketBindingV1

event_id

state_snapshot_id
state_sequence_id
state_available_at
last_material_event_id?
last_material_event_at?

market_quote_id
market_available_at
market_provider_updated_at?

state_quote_delta_ms
last_material_event_quote_delta_ms?

sync_status:
    SYNCHRONIZED
    PROBABLY_SYNCHRONIZED
    STALE
    FUTURE_STATE
    UNKNOWN

binding_checksum
```

## Hard gate

> **No synchronized executable quote = no live recommendation.**

The sport model may still publish a fair probability while recommendation is blocked:

```text
PREDICTION: AVAILABLE
RECOMMENDATION: BLOCKED
REASON: UNSYNCHRONIZED_MARKET
```

Exact synchronization/freshness thresholds must be empirically validated from provider latency. They must not be invented before measurement.

---

# 8. Sport-owned live engines

Every `Daily-[Sport]-Live` repository consumes DDLC evidence and converts it into sport-native state, features, models and decisions.

Examples:

```text
Daily-MLB-Live
Daily-NFL-Live
Daily-NBA-Live
Daily-NCAAF-Live
Daily-NHL-Live
```

Each sport owns:

- sport event-state semantics;
- sport live feature engineering;
- live participant state;
- sport-specific simulation;
- direct live models;
- live calibration;
- live fair probabilities/distributions;
- sport-specific live Recommendation Gate;
- live settlement semantics;
- sport explanations.

DDLC supplies facts. The sport decides what those facts mean.

---

# 9. Daily-MLB-Live canonical state

The first proving sport should be MLB.

A future MLB state contract should include at minimum:

```text
MLBInGameStateV1

game_id
state_sequence_id

inning
half

home_score
away_score
outs

runner_on_first
runner_on_second
runner_on_third

current_batter_id
current_pitcher_id
batting_order_position

pitch_count
batters_faced
times_through_order

home_pitchers_used[]
away_pitchers_used[]

last_play_id
last_plate_appearance_id

observed_at
available_at
source_checksum
state_checksum
```

The authoritative source can build on MLB's live game feed already used by Daily-MLB, but the live contract must be a separate immutable state series rather than reuse pregame GameState semantics.

Initial model refresh triggers should be:

- completed plate appearance;
- pitching change;
- inning/half-inning transition;
- run-scoring event;
- material correction.

Pitch-by-pitch inference can be added later after latency and value are proven.

---

# 10. Live Statcast/player-state layer

Live models should not know only the player identity. They should estimate whether the player currently resembles the established baseline.

Example:

```text
PitcherLiveStateV1

baseline_fastball_velocity
game_fastball_velocity

baseline_whiff_rate
game_whiff_rate

baseline_chase_rate
game_chase_rate

baseline_strike_rate
game_strike_rate

release_point_delta
movement_delta
pitch_mix_delta

hard_hit_allowed
barrels_allowed
exit_velocity_allowed

pitches_thrown
batters_faced
times_through_order
rest_days

state_uncertainty
state_checksum
```

Comparable state contracts may later exist for hitters, bullpens, quarterbacks, offensive lines, NBA rotations, NHL goalies, etc.

Live evidence should update latent/current state through a Bayesian/state-space framework rather than hand-written immediate overrides.

Conceptually:

```text
SEASON / LONG-RUN TALENT PRIOR
             +
       RECENT STATE
             +
      TODAY'S EVIDENCE
             |
             v
      CURRENT POSTERIOR
```

Early in an event the prior dominates. As valid evidence accumulates, current-game evidence may receive more influence.

No universal manual rule such as "after 20 pitches use 50% live evidence" is authorized.

---

# 11. Live prediction model architecture

TDL Live should preserve multiple independent experts rather than depend on one formula.

```text
                   SEALED PREGAME PRIOR
                            |
        +-------------------+-------------------+
        |                   |                   |
        v                   v                   v
 Historical State      Direct Live ML      Trajectory /
 Win Expectancy        Model Family         Simulator
        |                   |                   |
        +-------------------+-------------------+
                            |
                    Specialist Models
                            |
                            v
                    LIVE META-ENSEMBLE
                            |
                            v
                       CALIBRATION
                            |
                            v
                   TDL LIVE FAIR STATE
```

## 11.1 Expert A — Pregame prior

The exact sealed pregame TDL probability/distribution.

It remains unchanged and acts only as prior context.

## 11.2 Expert B — Historical state model

For MLB, a baseline may estimate:

```text
P(win | inning, half, score, outs, bases)
```

This answers what historically happens from a neutral baseball game state.

It intentionally does not fully know the participating team/player quality.

## 11.3 Expert C — Direct live model

Candidate features may include:

- pregame TDL fair probability;
- score differential;
- inning/quarter/clock;
- base/out/down/possession state;
- home/away possession/batting;
- current participants;
- starter/quarterback/goalie state;
- bullpen/bench/rotation availability;
- fatigue/workload;
- park/weather;
- live tracking/Statcast/NGS state;
- remaining opportunities.

Candidate model families include:

- logistic/GLM/GAM baselines;
- XGBoost;
- LightGBM;
- CatBoost;
- hierarchical/state-space models.

## 11.4 Expert D — conditional trajectory simulator

The preferred long-term approach for state-rich markets is a coherent remaining-game simulator.

For MLB:

```text
CURRENT STATE
     |
     v
next PA outcome distribution
     |
     v
new base/out/score state
     |
     v
next PA
     |
     v
pitching change / fatigue?
     |
     v
bullpen transition
     |
     v
future innings
     |
     v
FINAL SCORE
```

Run many deterministic-seed simulation scenarios.

The same simulation should price multiple markets consistently:

- moneyline;
- run line;
- totals;
- team totals;
- winning margin;
- later supported sequence/inning markets.

This is conceptually aligned with the existing Daily-MLB `GAME_TRAJECTORY_STATE` architecture.

---

# 12. Correct conditional total formulation

Live totals must condition on current state rather than naively add current run expectancy to the original pregame mean.

The correct structure is:

```text
E[Final Total | State_t]
 =
Runs Already Scored
 +
E[Remaining Runs In Current Segment | State_t]
 +
Sum(E[Future Segment Runs | Updated State])
```

The current state replaces the pregame expectation for already-completed and partially completed game segments.

This correction is explicitly required because the first TDL live shadow audit exposed that a naive "pregame total + current run expectancy" approach can double-count scoring expectation.

---

# 13. TDL Live Unified State

A sport live engine should preserve every expert output before calibration.

Example:

```text
Historical state WP          63.1%
Direct live XGB              66.8%
Trajectory simulation        65.9%
Pregame-prior update         64.4%
Bullpen specialist           68.2%
Current-state specialist     67.1%

Ensemble mean                65.9%
Model range                  63.1–68.2%
Agreement                    HIGH

Calibrated Live Fair         65.3%
Uncertainty                  4.8 pp
```

The calibrated independent probability is the sport's live fair line.

Learned model influence must come from chronological forward/OOS evidence.

No permanent hand-written model weight table is authorized.

---

# 14. Live market firewall

The live independent forecast path remains market-blind.

```text
SPORT LIVE MODEL
       |
       v
TDL LIVE FAIR PROBABILITY / DISTRIBUTION
       |
      SEAL
====================================
          MARKET FIREWALL
====================================
       |
       v
SYNCHRONIZED LIVE MARKET EVIDENCE
       |
       v
LIVE VALUE ENGINE
       |
       v
LIVE RECOMMENDATION GATE
```

Sportsbook, exchange and prediction-market prices cannot influence the stored independent live fair probability.

A later explicitly labeled market-residual model may consume market evidence, but it must never overwrite the independent fair state.

---

# 15. Live value engine

Let:

```text
p_f = TDL live fair probability
p_m = no-vig/live market probability
d   = executable decimal odds
```

Then:

```text
raw_edge = p_f - p_m

EV = p_f * d - 1
```

Live decision-making also requires conservative probability.

Let `p_lower` represent an uncertainty-aware lower estimate from calibrated prediction intervals/model disagreement.

Then:

```text
conservative_edge = p_lower - p_m

conservative_EV = p_lower * d - 1
```

A recommendation should survive conservative probability, not only the ensemble mean.

This formalizes the behavior observed in the first live audit: a favorite may be most likely to win but still be a PASS if the price requires a probability near or above the lower confidence bound.

---

# 16. Live Recommendation Gate

Live should have its own gate rather than overload the pregame Recommendation Gate.

Conceptual output states:

```text
RECOMMEND
WATCH
PASS
AVOID
```

## 16.1 Structural gates

- prediction valid;
- live event identity valid;
- live-state lineage valid;
- feature/model lineage valid;
- market independence attested.

## 16.2 State gates

- live state complete;
- event sequence continuity valid;
- current participant/pitcher/possession state known where required;
- game not suspended/terminal;
- model familiarity adequate;
- current-state uncertainty acceptable.

## 16.3 Market gates

- quote active;
- quote executable;
- quote fresh;
- quote synchronized to state;
- two-way/no-vig evidence valid where required;
- minimum provider/book support.

## 16.4 Model/value gates

- model/calibration release eligible;
- disagreement acceptable;
- raw edge threshold;
- conservative edge threshold;
- raw EV threshold;
- conservative EV threshold.

No final numeric thresholds are frozen by this document.

They must be established through live historical replay/shadow operation and prospective validation.

## 16.5 WATCH semantics

WATCH is useful specifically for live systems.

Example:

```text
TDL fair probability: 61%
Nominal market edge: +7 pp
Quote age: unacceptable

WATCH
reason: QUOTE_STALE
```

A synchronized replacement quote may immediately produce RECOMMEND, PASS or AVOID.

WATCH is not a wager recommendation.

---

# 17. Daily-Line-Live-Core (DLLC)

DLLC is the live product-layer equivalent of DLC.

DLLC must not predict sports.

It consumes sealed sport-live decision packages.

```text
Daily-MLB-Live  -----+
Daily-NFL-Live  -----+
Daily-NBA-Live  -----+
Daily-NCAAF-Live ----+----> DAILY-LINE-LIVE-CORE
                     |
                     +--> All Live Bets
                     +--> Live Top Edges
                     +--> Live Product Index
                     +--> later Live EdgeStack
                     |
                     v
               LivePublicationPackage
```

## DLLC owns

- admission of immutable sport-live decision packages;
- reference/admission of DDLC market evidence;
- All Live Bets assembly;
- cross-sport live ranking/indexing;
- live alert candidates;
- later Live EdgeStack;
- immutable live publication packages;
- downstream live consumer contracts.

DLLC may not rewrite a sport's live fair probability or Live Recommendation Gate state.

---

# 18. All Live Bets scanner

The first live product should scan every supported active market emitted by sport-live engines.

For each row retain:

- sport/event;
- state sequence;
- state timestamp;
- market identity;
- TDL live fair probability;
- conservative probability;
- fair price;
- executable quote;
- no-vig probability;
- raw edge;
- conservative edge;
- raw EV;
- conservative EV;
- model agreement;
- uncertainty;
- data quality;
- quote freshness;
- state-price synchronization;
- gate;
- reason codes;
- provenance.

Ranking may use certified actionability dimensions but may not override sport gate truth.

---

# 19. Live EdgeStack

Live EdgeStack is a later capability, not V1.

Potential future flow:

```text
ALL LIVE BETS
     |
     v
eligible live legs
     |
     v
dependency/joint evidence
     |
     v
provider live combo quote
     |
     v
Live EdgeStack
```

Live combinations are harder than pregame because state and prices may change during candidate construction.

Therefore:

> **Single-market live calibration and execution must be proven before Live EdgeStack is authorized.**

Initial live release should not depend on live parlays/combos.

---

# 20. Sport inference cadence

Different sports require different material-event triggers.

## MLB

Initial:

- plate appearance complete;
- pitching change;
- scoring event;
- inning/half transition.

Later:

- pitch-by-pitch.

## NFL

- every play;
- possession change;
- timeout;
- quarter transition;
- meaningful injury/personnel event.

## NBA

Candidate:

- scoring/possession event;
- substitution;
- timeout;
- quarter transition.

## NHL

Candidate:

- shot/event stream;
- goal;
- penalty/power play;
- goalie change/pull;
- period transition.

DDLC carries events. The sport repository decides when inference must rerun.

---

# 21. Immutable live prediction history

Live does not mean mutable truth.

Every prediction snapshot is retained.

Example:

```text
18:12:14.120   SEA win = 61%
18:12:39.502   single      -> 57%
18:13:10.114   walk        -> 51%
18:13:55.919   3-run HR    -> 28%
```

This history supports:

- calibration by state;
- probability-move analysis;
- market response comparison;
- latency research;
- overreaction/underreaction detection;
- WATCH-to-RECOMMEND evaluation;
- historical replay;
- model-regime research.

No in-event prediction overwrites a prior live prediction.

---

# 22. Storage separation

Artifact/storage paths should make pregame/live contamination difficult.

Conceptually:

```text
artifacts/
    pregame/
        ...

    live/
        <date>/
            <sport>/
                <event_id>/
                    raw/
                    states/
                    features/
                    model_outputs/
                    predictions/
                    market_quotes/
                    state_market_bindings/
                    values/
                    decisions/
                    publications/
```

Database tables and artifact contracts should carry the same explicit scope.

---

# 23. Live observability and latency

Live operation requires stronger telemetry than pregame.

Track at minimum:

- provider event latency;
- ingestion latency;
- normalization/state-build latency;
- feature latency;
- model inference latency;
- quote latency;
- state-market synchronization delta;
- value/gate latency;
- publication/alert latency;
- full end-to-end latency;
- provider disconnect/reconnect;
- sequence gaps;
- stale-quote rejection rate;
- suspended-market rejection rate.

Example:

```text
MLB play source time      20:15:03.120
DDLC observed             20:15:03.410
sport state sealed        20:15:03.462
prediction completed      20:15:03.516
market observed           20:15:03.591
decision completed        20:15:03.620

end-to-end ~= 500 ms
```

The example is illustrative only. No production latency target is frozen here.

---

# 24. Live settlement and evaluation

Every live decision should eventually bind:

```text
event_id
state_sequence
state_snapshot_id

prediction_id
market_quote_id
state_market_binding_id

market
selection
line
price

fair_probability
conservative_probability
edge
EV
gate

decision_timestamp

final settlement
outcome
```

Evaluate:

- Brier score;
- log loss;
- calibration/reliability;
- market-family accuracy;
- raw/conservative EV;
- realized ROI;
- quote/state latency;
- synchronization quality;
- performance by inning/quarter/clock/state;
- performance by model agreement;
- performance by uncertainty;
- provider performance.

A winning wager does not prove the process was good. A losing wager does not prove the process was bad.

---

# 25. State-Matched Price Value

Classic pregame CLV has a clear closing-price concept. Live pricing changes because the game state changes.

TDL Live should therefore retain a state-matched price diagnostic.

Example:

```text
State:
bottom 7th
1 out
bases empty
home +1

TDL recommendation price:
+105

4 seconds later:
-108

No material play/state change occurred.
```

That provides evidence that the system captured a favorable state-matched price.

If a material event occurred between quotes, they are not directly comparable.

Possible future metric names include:

- State-Matched Price Value (SMPV);
- State-Constant Price Movement;
- Same-State Quote Improvement.

Final terminology requires review.

---

# 26. Learning and promotion loop

Live feedback must follow the same scientific discipline as pregame.

```text
live prediction
      |
      v
live recommendation
      |
      v
execution opportunity
      |
      v
settlement
      |
      v
calibration / proper scoring
      |
      v
state/regime attribution
      |
      v
research hypothesis
      |
      v
PIT/OOS experiment
      |
      v
promotion review
```

A single game/play must never directly alter production weights or thresholds.

All candidate model/feature/gate changes require versioned historical replay, forward/OOS evaluation and explicit promotion.

---

# 27. Initial live market scope

Live V1 should remain deliberately narrow.

## MLB Live V1

- full-game moneyline;
- full-game run line;
- full-game total.

## Later MLB Live V2

Possible candidates:

- team totals;
- remaining-game total;
- inning/period totals;
- supported F5/F7 continuation surfaces.

## Later V3+

Possible candidates:

- player props;
- next team to score;
- race-to-N;
- inning markets;
- sequence-dependent markets.

Expansion must follow demonstrated calibration and settlement reliability.

---

# 28. Customer-facing live product

The website/app should eventually present Pregame and Live as distinct product surfaces.

Example:

```text
LIVE — HOU @ SEA
Top 5th — SEA 3, HOU 2

TDL Live Fair:          SEA 64.7%
TDL Fair Odds:          -183
Market No-Vig:          57.9%
Best Executable:        -138

Raw Edge:               +6.8 pp
Conservative Edge:      +4.1 pp
Model Agreement:        HIGH
Data Quality:           97/100
State/Quote Sync:       0.8 sec

GATE: RECOMMEND
TIMING: BET NOW
```

A no-value case may show:

```text
TDL Live Fair: TEX 73.1%
Market No-Vig: 73.8%

PREDICTION:
Texas remains more likely to win.

GATE:
PASS — price has consumed the edge.
```

The product must preserve the distinction:

```text
MOST LIKELY OUTCOME != GOOD BET
```

---

# 29. Build program

The live program should be architecture-first and independently certified.

## TDL-LIVE-0 — Cross-system architecture freeze

Freeze:

- topology;
- ownership;
- contamination firewall;
- repository boundaries;
- ID namespaces;
- temporal semantics;
- market firewall;
- live decision states;
- cross-repository handoff contracts.

No prediction/runtime implementation.

## DDLC-1 — Live evidence foundation

Implement:

- provider interface;
- raw immutable live evidence;
- sequence handling;
- provider health;
- clocks;
- latency;
- append-only event storage.

## DDLC-2 — Live markets and synchronization

Implement:

- canonical live quote;
- active/suspended states;
- quote freshness;
- market timeline;
- state-price temporal binding;
- synchronization diagnostics.

## MLBL-1 — MLB in-game state

Implement:

- MLB live-feed adapter;
- score/inning/outs/base state;
- batter/pitcher identities;
- pitch count;
- play/PA sequence identity;
- immutable state snapshots.

## MLBL-2 — Historical state baseline

Build neutral historical state win/run expectancy with strict PIT/OOS evaluation.

## MLBL-3 — Pregame-prior integration

Consume sealed pregame TDL forecast as immutable prior.

## MLBL-4 — Conditional trajectory/run simulator

Build remaining-game state simulation and coherent moneyline/run-line/total projections.

## MLBL-5 — Live Statcast/current-state layer

Add validated current-game pitcher/hitter/bullpen state.

## MLBL-6 — Live unified ensemble/calibration

Fit conditional trust/stacking using chronological OOS predictions.

## MLBL-7 — Live value and gate

Add market firewall, synchronized market comparison, uncertainty-aware EV and Live Recommendation Gate.

## DLLC-1 — Live product core

Define/admit `SportLiveDecisionPackage`, assemble All Live Bets, rank live product views and seal publication packages.

## LIVE-VALIDATION

Operate silently/shadow-live.

Store every state/prediction/quote/decision.

No customer recommendation authority.

## LIVE-RELEASE

Require explicit certification based on:

- calibration;
- proper scoring;
- OOS performance;
- synchronization reliability;
- provider latency;
- operational resilience;
- gate behavior;
- settlement correctness;
- human review.

---

# 30. Historical replay strategy

Game-state history is often reconstructable from official play-by-play/Statcast.

Historical in-play market prices may be substantially harder to obtain.

Therefore two datasets should be developed separately:

## 30.1 Sport-state replay dataset

Supports:

- neutral state WP;
- direct live models;
- simulations;
- calibration;
- sport feature validation.

## 30.2 Live-market timeline dataset

Supports:

- value/gate evaluation;
- quote freshness;
- synchronization;
- execution realism;
- market-response/latency research;
- state-matched price evaluation.

If historical live odds are incomplete, sport-model validation may proceed independently while recommendation/EV claims remain blocked.

---

# 31. Fail-closed conditions

TDL Live should fail closed when:

- event identity cannot be proven;
- sequence gaps make current state uncertain;
- score/clock/inning state conflicts between authorities;
- current participant/pitcher/possession cannot be resolved for a model that requires it;
- market quote is suspended;
- quote freshness cannot be established;
- state/quote synchronization cannot be established;
- model artifact is not live-release eligible;
- prediction interval/uncertainty violates policy;
- settlement rules are unknown;
- provider clock anomalies are unresolved.

Failure to recommend must never imply failure to generate a prediction when prediction evidence itself remains valid.

---

# 32. Non-goals / not authorized by V1 architecture

This document does not authorize:

- creation of the proposed new repositories;
- provider purchases/contracts;
- production live odds ingestion;
- live customer recommendations;
- automated wagering/order placement;
- bankroll management;
- stake sizing/Kelly;
- personalized betting recommendations;
- Live EdgeStack;
- arbitrary manual model weights;
- arbitrary live freshness thresholds;
- pregame model modification;
- cross-repository implementation without repository-specific authorization.

---

# 33. Cross-repository handoff rule

Each repository remains independently governed.

No single implementation turn should silently modify DDLC, sport-live and DLLC together.

The intended flow is:

```text
architecture freeze
      |
      v
versioned handoff contract
      |
      +--> DDLC implementation
      |
      +--> sport-live implementation
      |
      +--> DLLC implementation
```

Every downstream repository must consume an explicit versioned upstream contract rather than depend on another repository's unsealed `main` behavior.

---

# 34. Relationship to existing architecture

This design intentionally reuses existing Daily Line principles:

- immutable point-in-time evidence;
- raw -> canonical -> feature -> model -> output separation;
- market firewall;
- sport-owned prediction authority;
- model disagreement;
- calibrated probabilities;
- Recommendation Gate;
- append-only predictions;
- settlement/performance loop;
- Daily-Model-Core OOS/ensemble/calibration governance;
- Daily-Line-Core product-layer separation.

It extends those principles to continuous in-event state.

For MLB specifically, it aligns with:

- MLB live game feed usage in existing GameState acquisition;
- existing `GAME_TRAJECTORY_STATE` concept;
- starter-to-bullpen transition modeling;
- SHARE/current-state concepts;
- independent fair-line architecture.

It does not change those existing pregame contracts.

---

# 35. Governing invariants

1. **Pregame truth is immutable once sealed.**
2. **Live is a separate predictive scope.**
3. **Live may read sealed Pregame truth only as an immutable prior.**
4. **Live evidence never rewrites Pregame evidence.**
5. **DDLC owns real-time facts and market evidence, not sport interpretation.**
6. **Sport-live repositories own live sport state, features, prediction, calibration and gate semantics.**
7. **DLLC owns cross-sport live product assembly, not sport probability.**
8. **Independent live fair probability is sealed before market comparison.**
9. **No synchronized executable quote means no recommendation.**
10. **Every live prediction revision is immutable and retained.**
11. **Uncertainty must influence actionability.**
12. **A likely winner is not automatically a good live bet.**
13. **Single-event results cannot directly self-modify production models.**
14. **Historical replay and forward/OOS evidence determine promotion.**
15. **No automatic wagering is authorized by this architecture.**

---

# 36. Exact next architecture step

Before any live implementation, perform **TDL-LIVE-0 review/freeze**.

The review should explicitly approve or revise:

1. names `Daily-Data-Live-Core`, `Daily-[Sport]-Live`, and `Daily-Line-Live-Core`;
2. the pregame-to-live prior contract;
3. contamination firewall;
4. DDLC live evidence and quote boundaries;
5. state-price synchronization contract;
6. live sport-state ownership;
7. PRE_GAME vs LIVE Daily-Model-Core scope rules;
8. Live Recommendation Gate vocabulary, including WATCH;
9. initial MLB supported markets;
10. artifact/ID namespaces;
11. shadow-live validation requirements;
12. repository creation/build order.

Only after that review should repository creation and implementation authority be considered.
