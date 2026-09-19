# Post-Game Feedback Loop V1

**Status:** DOCUMENTED — IMPLEMENTATION NOT YET AUTHORIZED  
**Date:** 2026-09-18  
**Canonical owner:** Daily-Line-Core (DLC) for cross-sport/product evaluation; sport repositories retain sport-native learning authority.

## 1. Purpose

The Post-Game Feedback Loop turns completed games and settled markets into structured evidence for improving The Daily Line without overreacting to individual wins or losses.

The loop answers:

- Was the pregame probability reasonably calibrated even though the outcome lost?
- Did the loss come primarily from irreducible variance, a missing/underweighted pregame signal, stale evidence, a matchup interaction, a Recommendation Gate issue, a correlation error, or a data/settlement failure?
- Did an EdgeStack fail because one leg was mis-modeled, because dependency was misestimated, or simply because a low-probability outcome occurred?
- Is a finding strong enough to justify monitoring, research, PIT backtesting, recalibration, or a sport-model change proposal?
- Which recurring failure modes should be handed back to the owning sport repository or Daily-Model-Core for research?

The loop must improve future decisions while preserving strict point-in-time integrity.

## 2. Core principle

A lost recommendation is **not automatically a bad model prediction**, and a winning recommendation is **not automatically a good model prediction**.

Every post-game review separates:

1. **Outcome quality** — what actually happened;
2. **Prediction quality** — whether the pregame probability was calibrated;
3. **Decision quality** — whether price/value/Recommendation Gate logic was sound;
4. **Combination quality** — whether EdgeStack joint probability/correlation/pricing was sound;
5. **Data/process quality** — whether the correct PIT evidence and settlement rules were used.

The system must never tune itself to make yesterday's result look predictable.

## 3. Authority boundaries

### 3.1 Daily-Data-Core

DDC may provide or reference:

- point-in-time opening/current/closing market evidence;
- provider timestamps, quote provenance and movement history;
- generic settlement/result evidence where shared and sport-neutral;
- immutable evidence needed to compare the published price with later market state.

DDC does not diagnose baseball/football mechanics or alter sport models.

### 3.2 Sport repositories

Daily-MLB, Daily-NFL, Daily-NCAAF and future sport repos own:

- final sport settlement interpretation;
- sport-native post-game statistics;
- sport-specific advanced-stat evidence;
- player/team/pitch/play-level diagnostics;
- feature residuals;
- rolling form/trend diagnostics;
- model residual decomposition;
- sport-specific matchup analysis;
- proposals to change sport features/models/Recommendation Gates;
- PIT backtests of any sport-specific improvement proposal.

Examples:
- Daily-MLB may analyze Statcast/Baseball Savant velocity, pitch shape, whiff, xwOBA, hard-hit, barrel and command trends.
- Daily-NFL may analyze EPA/play, success rate, pressure rate, coverage, route participation and personnel usage.

### 3.3 Daily-Line-Core

DLC owns the cross-sport/product evaluation layer:

- join the exact published `DailyLinePublicationPackage` to final outcomes;
- evaluate every published All Bets recommendation;
- evaluate every published EdgeStack;
- compare predicted probability with realized result and market break-even;
- aggregate recurring miss categories across sports/markets;
- track calibration/EV/Recommendation Gate performance;
- emit structured feedback findings and research handoffs;
- track recommendation-set concentration such as repeated anchor legs.

DLC does **not** directly change sport model weights/features or Recommendation Gate semantics.

### 3.4 Daily-Model-Core

DMC may consume generic model-quality findings, calibration evidence and reusable research questions. DMC does not override sport-native feature authority.

## 4. Canonical inputs

### 4.1 Published decision authority

The loop begins from the exact immutable `DailyLinePublicationPackage` that was actually published.

Required references include:

- publication package ID;
- sport decision package IDs;
- market evidence bundle IDs;
- All Bets snapshot ID;
- EdgeStack catalog/recommendation IDs;
- model/config/release versions;
- prediction/data/market cutoffs;
- provider quote IDs/timestamps;
- Recommendation Gate state/reason;
- joint probability/correlation method for EdgeStacks.

### 4.2 Final outcome / settlement authority

Each sport should provide a sealed `SportOutcomePackage` or equivalent containing:

- event identity;
- final result;
- final player/team statistics;
- canonical market settlements;
- settlement rule/version;
- result timestamp/provenance;
- corrections/supersession lineage.

### 4.3 Sport post-game evaluation package

Each sport may additionally emit a `SportPostGameEvaluationPackage` containing sport-native diagnostics, for example:

- expected vs actual performance residuals;
- rolling form signals;
- player usage/workload changes;
- velocity/command/pitch-shape trends;
- lineup/personnel surprises;
- injury/availability effects;
- matchup-specific interactions;
- advanced-stat residuals;
- model-family disagreement after the fact;
- candidate omitted/underweighted features;
- confidence and evidence references.

This package is diagnostic evidence, not an instruction for DLC to alter the sport model.

### 4.4 Post-event market evidence

Where available:

- closing price/line;
- closing no-vig probability;
- late line movement;
- liquidity/execution availability;
- final Combo/parlay quote state;
- provider settlement receipt.

Closing evidence is evaluation-only for the earlier prediction.

## 5. Canonical feedback outputs

### 5.1 `RecommendationOutcomeRecord`

One record per published individual recommendation.

Conceptual fields:

```text
recommendation_outcome_id
publication_package_id
all_bets_row_id
sport_decision_ref
market_evidence_ref
predicted_probability
published_market_probability
published_break_even_probability
published_edge
published_ev
recommendation_gate
final_settlement
result_binary_or_numeric
closing_market_probability?
closing_line_value?
probability_residual
brier_component
log_loss_component
attribution_refs[]
status
```

### 5.2 `EdgeStackOutcomeRecord`

One record per published EdgeStack.

```text
edgestack_outcome_id
edgestack_id
class
ranking_views[]
leg_outcome_refs[2..5]
predicted_joint_probability
published_break_even_probability
published_multiplier
provider_quote_ref
correlation_method
final_settlement
failed_leg_refs[]
shared_anchor_refs[]
joint_probability_residual
attribution_refs[]
```

### 5.3 `FeedbackFinding`

A structured explanation of a meaningful observation.

```text
feedback_finding_id
scope: EVENT | MARKET | PLAYER | TEAM | MODEL | GATE | EDGESTACK | PRODUCT
finding_type
severity
confidence
evidence_refs[]
pregame_signal_available: true|false
would_have_changed_pregame_decision: YES|NO|UNCERTAIN
research_action
owner_repository
status
```

### 5.4 `ImprovementProposal`

A proposal is a research handoff, never an automatic production mutation.

Possible destinations:

- Daily-MLB;
- Daily-NFL;
- Daily-NCAAF;
- Daily-Model-Core;
- DDC;
- DLC itself for EdgeStack/ranking/calibration behavior.

Every proposal must preserve evidence and specify the PIT backtest needed before promotion.

## 6. Miss-attribution taxonomy

A post-game record may contain multiple causes. Attribution is evidence-weighted, not forced into one exclusive label.

### 6.1 `OUTCOME_VARIANCE`

The pregame model may have been reasonable and a tail outcome occurred.

Examples:

- unusually high walk/error turnover rate;
- sequencing luck;
- bloop/error with runners on;
- improbable interception/fumble sequence;
- low-probability home-run cluster.

This label should **not** become a reason to change the model by itself.

### 6.2 `FORM_TREND_MISS`

A pregame-available rolling trend was missing or underweighted.

Examples:

- declining pitcher velocity;
- deteriorating command;
- workload/fatigue trend;
- changing route participation;
- rolling pressure/sack rate;
- recent pitch-shape movement.

### 6.3 `MATCHUP_INTERACTION_MISS`

The model captured overall talent but under-modeled how the specific opponent interacted with the player's/team's strengths and weaknesses.

Examples:

- lineup performance against a pitch type;
- batter/pitch-shape interaction;
- coverage/route interaction;
- run-defense personnel mismatch.

### 6.4 `FEATURE_GAP`

A relevant pregame feature did not exist in the production feature set or was unavailable to the model.

### 6.5 `FEATURE_WEIGHT_MISS`

The feature existed but production weighting/ensemble influence was inconsistent with forward evidence.

### 6.6 `CALIBRATION_MISS`

The model ranked the side correctly but the probability was systematically too high/low for its bucket.

### 6.7 `GATE_POLICY_MISS`

The sport probability may have been reasonable, but the Recommendation Gate should have reduced confidence, passed, or avoided due to uncertainty/price/context.

### 6.8 `PRICE_TIMING_MISS`

The underlying prediction remained reasonable but the published price had deteriorated or a better/worse quote timing materially changed value.

### 6.9 `CORRELATION_MISS`

EdgeStack dependency/joint probability was incorrect or insufficiently modeled.

### 6.10 `ANCHOR_CONCENTRATION`

A recommendation set reused the same leg across too many EdgeStacks, creating a universal or near-universal failure point.

This is a product-recommendation diagnostic only. It does not prescribe stake size.

### 6.11 `INPUT_FRESHNESS_OR_AUTHORITY`

The correct pregame information existed but was stale, conflicting, not admitted, or not authoritative.

### 6.12 `DATA_OR_SETTLEMENT_DEFECT`

Parser, mapping, identity, result, settlement or integration defect.

### 6.13 `NO_ACTION_GOOD_PROCESS`

The process was sound; the realized outcome does not justify a model change.

Winning picks can also receive this label when they won for reasons consistent with the model rather than lucky process failure.

## 7. Pregame-availability test

Every proposed improvement must answer:

> **Was this signal defensibly available before the original prediction cutoff?**

If **no**, it cannot be used to claim the original model should have known the outcome.

It may still be useful for future forecasting if it can be acquired prospectively.

Post-game facts are separated into:

- `PREGAME_AVAILABLE_UNDERWEIGHTED`;
- `PREGAME_AVAILABLE_MISSING`;
- `POSTGAME_ONLY_DIAGNOSTIC`;
- `UNKNOWN_AVAILABILITY`.

This prevents hindsight leakage.

## 8. Counterfactual re-evaluation

For important misses, the sport repository may produce a **PIT counterfactual** using only evidence that was available before the original cutoff.

Example question:

> If rolling pitcher velocity trend and opponent-specific pitch-shape interaction had been included using only pregame data, would the fair probability have changed materially?

The counterfactual must:

- use the original cutoff;
- block closing/result/postgame evidence;
- use a versioned candidate feature/model;
- report the old and candidate probabilities;
- state whether the original Recommendation Gate would have changed.

A counterfactual that uses future information is invalid.

## 9. Single-game forensic layer vs learning layer

### 9.1 Single-game forensic layer

Runs after settlement to explain the event.

It may create:

- findings;
- alerts;
- research candidates;
- data defects;
- feature-watch signals.

It may **not** directly alter production model weights.

### 9.2 Rolling learning layer

Aggregates findings across a predeclared evaluation window.

Required views include:

- calibration by probability bucket;
- Brier score;
- log loss;
- hit rate vs predicted probability;
- EV by market class/gate;
- closing-line comparison;
- miss-attribution frequency;
- feature-gap recurrence;
- EdgeStack joint-probability calibration by leg count/class;
- correlation residuals;
- anchor concentration frequency;
- provider quote/execution availability.

Exact minimum sample thresholds are sport/market specific and must be frozen **before** looking at the candidate evaluation window.

## 10. Model-improvement promotion rule

No individual game, player performance, or EdgeStack result may directly change production weights/features.

A production change requires the owning repository's normal research/promotion path:

1. finding/proposal;
2. predeclared hypothesis;
3. feature/data implementation in the owner repo;
4. PIT backtest with no future leakage;
5. out-of-sample/champion-challenger evaluation;
6. calibration and ablation review;
7. regression/safety checks;
8. documented approval/promotion;
9. new immutable model/config release.

The feedback loop proposes and measures. It does not self-modify production.

## 11. EdgeStack-specific feedback

For every settled EdgeStack, evaluate:

- which legs won/lost;
- whether the failed leg had the largest individual calibration residual;
- whether joint probability was over/underestimated;
- whether same-event correlation was directionally correct;
- whether the real provider quote remained favorable at publication;
- whether a repeated anchor leg caused recommendation-set concentration;
- whether the combination class (`CORE`, `VALUE`, `UPSIDE`) matched realized long-run calibration;
- whether adding/removing a leg would have improved ex-ante probability/value according to information available at the time.

Do **not** judge EdgeStack quality by whether one ticket won. Judge repeated probability buckets over time.

## 12. Recommendation-set concentration diagnostics

DLC may calculate non-staking portfolio diagnostics across the published EdgeStack set:

- `anchor_leg_frequency`;
- `max_shared_leg_count`;
- `universal_failure_leg_count`;
- `pairwise_overlap`;
- `event_concentration`;
- `sport_concentration`;
- `provider_concentration`.

A high concentration finding may influence which EdgeStacks are displayed together, but does not prescribe bankroll allocation.

## 13. Post-game product surfaces

A future website/report may expose a transparent audit:

### What we predicted
- fair probability;
- market probability;
- gate;
- EdgeStack class.

### What happened
- result;
- settlement;
- key sport-native advanced-stat evidence.

### Why it differed
- attribution categories;
- confidence;
- variance vs systematic-signal assessment.

### What changes
- `NO_ACTION`;
- `MONITOR`;
- `RESEARCH_CANDIDATE`;
- `BACKTEST_REQUIRED`;
- `CALIBRATION_REVIEW`;
- `CORRELATION_REVIEW`;
- `DATA_FIX_REQUIRED`.

The product should never claim that a future model change would have guaranteed the result.

## 14. Example — Toronto vs Texas, 2026-09-18

This example records the type of forensic reasoning the loop should support; it is not itself model-training authority.

Pregame product view:

- Toronto was rated as the more likely winner;
- the starting-pitcher season profile strongly favored Toronto;
- Toronto was promoted into multiple EdgeStack candidates.

Post-game forensic observations identified a mixture of:

### `OUTCOME_VARIANCE` — high
- an extreme command failure relative to the starter's normal profile;
- defensive/sequencing effects amplified baserunner traffic.

### `FORM_TREND_MISS` — high
- a pregame-available rolling fastball-velocity decline was underweighted.

### `MATCHUP_INTERACTION_MISS` — medium
- opponent/pitcher-specific matchup history and repertoire interaction suggested more uncertainty than the season aggregate implied.

### `GATE_POLICY_MISS` — medium
- the fair probability may have been directionally correct but overconfident for a Tier-A EdgeStack anchor.

### `ANCHOR_CONCENTRATION` — product-level
- Toronto appeared in multiple recommended combinations, so one miss killed several tickets.

Proposed research actions:

1. Daily-MLB: evaluate rolling velocity + command trend features using PIT data.
2. Daily-MLB: evaluate opponent pitch-shape/repertoire interaction beyond raw head-to-head outcome history.
3. Daily-MLB: test whether uncertainty should widen when current-form signals diverge from season-long quality.
4. DLC: test EdgeStack anchor-concentration penalties/display diversity.
5. No automatic production weight change from this one game.

## 15. Pipeline placement

```text
PRE-GAME
Sport models + gates ----+
DDC market evidence -----+--> DLC All Bets / EdgeStack
                               |
                               v
                     DailyLinePublicationPackage
                               |
                            EVENT
                               |
                               v
POST-GAME
Sport final outcome/settlement --------+
Sport advanced diagnostics ------------+
DDC closing/market evidence ------------+
                                        |
                                        v
                           DLC Feedback Evaluator
                         /          |           \
                        v           v            v
             Individual outcomes  EdgeStack   Calibration/
                                  outcomes    trend aggregates
                        \           |            /
                         +----------+-----------+
                                    |
                                    v
                              FeedbackFindings
                                    |
                      +-------------+-------------+
                      |                           |
                      v                           v
                Sport/DMC research            DLC product/
                   handoffs                  EdgeStack review
                      |                           |
                      +-------------+-------------+
                                    |
                             PIT backtest only
                                    |
                             approved promotion
```

## 16. Failure behavior

The loop fails closed when:

- the original publication package cannot be identified;
- final settlement is ambiguous;
- sport/market identity cannot be reconciled;
- post-game evidence cannot be separated from pregame-available evidence;
- a candidate counterfactual uses future information;
- a finding lacks evidence/confidence;
- a proposed production change bypasses the owning repository's research/promotion rules.

A missing post-game diagnostic must never mutate the original prediction record.

## 17. V1 invariants

1. Results never rewrite predictions.
2. Closing/result data never leak into the original pregame inference.
3. Wins and losses are both audited.
4. Single-game outcomes can create findings, not production model mutations.
5. Attribution may be multi-causal and confidence-weighted.
6. Sport-specific learning stays sport-owned.
7. DLC owns cross-sport/product calibration and EdgeStack feedback.
8. All model changes require PIT/out-of-sample evidence before promotion.
9. EdgeStack quality is judged by repeated calibration, not one ticket.
10. Recommendation-set concentration may be diagnosed without becoming bankroll management.
11. The loop can say `NO_ACTION_GOOD_PROCESS`.
12. No feedback finding may claim certainty that a proposed feature would have changed the realized outcome.
