# Pregame Signal Health Integration V1

**Status:** DOCUMENTED — SCHEMA/IMPLEMENTATION PENDING  
**Date:** 2026-09-19  
**Canonical owner of detection:** sport repositories  
**DLC role:** consume, expose, rank with certified uncertainty/gate information; never recompute sport regime state

## 1. Purpose

This document defines how Daily-Line-Core consumes pregame signal-health/regime output from sport repositories.

The initiating MLB architecture is:

`OneVillage83/Daily-MLB/docs/PREGAME_SIGNAL_HEALTH_REGIME_ENGINE_V1.md`

Daily-MLB names its sport-specific layer **SHARE — Signal Health & Regime Engine**.

DLC does not own SHARE. DLC receives the sport's final probability, uncertainty, Recommendation Gate, and publication-safe signal-health summary after the sport has completed its independent and market-aware decision process.

## 2. System placement

```text
sport evidence / ModelFeatureSet
          |
          v
sport signal-health / regime detection
          |
          v
sport model zoo + conditional trust
          |
          v
calibrated TDL Unified Line
          |
          v
sport market-aware decision
          |
          v
sport Recommendation Gate
          |
          v
sealed SportDecisionPackage
          |
          +--------------------+
                               v
                         DAILY-LINE-CORE
                    All Bets / EdgeStack / publish
```

DLC must never perform the sport-side state adjustment after receiving the package.

## 3. SportDecisionPackage extension

A future additive version of `SportDecisionPackage` should permit optional publication-safe signal-health fields:

```text
signal_health_summary?
  summary_id
  sport_signal_schema_version
  as_of
  overall_regime_class
  state_divergence_score?
  state_confidence?
  mean_shift_summary?
  uncertainty_shift_summary?
  major_signal_refs[]
  recommendation_risk_flags[]
  sport_policy_version
  source_state_ref
```

Exact fields and ranges are frozen in DLC-1 compatibility work.

Rules:

- the summary is produced by the sport;
- it is immutable and PIT-bound;
- it never contains sportsbook prices as inputs to the independent regime detector;
- it may summarize only publication-safe evidence;
- absence is not equivalent to `STABLE`;
- DLC cannot recalculate the state from raw sport data.

## 4. All Bets behavior

All Bets may expose:

- sport fair probability;
- market probability/price;
- edge/EV;
- Recommendation Gate;
- uncertainty;
- sport signal-health/regime class;
- state-divergence indicator;
- publication-safe reasons;
- quote and model cutoff.

This allows a user to distinguish:

> high fair probability + stable current state

from:

> high fair probability + elevated current-state uncertainty.

DLC must not create a new fair probability by applying its own percentage-point penalty.

## 5. EdgeStack behavior

EdgeStack already ranks candidates using probability, value, uncertainty, correlation quality and quote freshness.

A certified signal-health summary may contribute to:

- uncertainty ranking;
- leg eligibility already expressed through the sport Recommendation Gate;
- confidence display;
- anchor-concentration policy;
- diversity between candidate EdgeStacks.

Examples of valid product behavior:

- prefer a similarly priced leg with stable state over one marked high-divergence;
- retain a high-divergence leg in `VALUE` but not `CORE` when the sport's gate permits it and historical policy validates that behavior;
- avoid publishing five EdgeStacks that all share one elevated-uncertainty anchor.

Examples of invalid behavior:

- DLC subtracts 4 percentage points because an MLB pitcher lost velocity;
- DLC overrides a sport PASS to PLAY;
- DLC invents a matchup interaction from raw stats;
- DLC treats missing signal-health data as stable.

## 6. Core / Value / Upside interaction

Signal health does not define EdgeStack class by itself.

A future certified policy may use it as one of several ranking dimensions:

### Core
Prefer high hit probability, positive value, strong calibration and lower uncertainty.

### Value
May accept moderate regime uncertainty when price compensation is strong and the sport gate permits the leg.

### Upside
May accept higher variance, but no leg may bypass sport-level eligibility.

Exact thresholds must be prospectively frozen and backtested.

## 7. Product explanation

A publication-safe example:

```text
Toronto ML
TDL fair probability: 58%
Market break-even: 54%
Signal health: WATCH
Reason: starter current-state metrics diverge from season baseline
Uncertainty: elevated
Recommendation Gate: PLAY — reduced confidence
EdgeStack use: eligible for Value; not preferred as Core anchor
```

The wording must clearly distinguish model evidence from certainty.

## 8. Relationship to Post-Game Feedback Loop

The post-game loop may compare the pregame signal-health summary with the final sport diagnostics.

Examples:

- `STABLE` pregame but recurring postgame `FORM_TREND_MISS` -> sport research candidate;
- `SHIFT_LIKELY` pregame and poor outcome -> not automatically proof the detector worked;
- high-divergence anchor repeatedly harms EdgeStack calibration -> DLC product-policy research;
- current-state flag improves calibration over a large PIT sample -> candidate promotion evidence.

The original pregame summary remains immutable.

## 9. Anchor concentration

Signal health complements the existing EdgeStack concentration diagnostics.

A repeated leg may be especially undesirable as a universal anchor when:

- state divergence is elevated;
- uncertainty is wide;
- model disagreement is high;
- sport gate confidence is reduced.

DLC may use this in recommendation-set construction once validated.

This is not bankroll/stake management.

## 10. Contract and implementation sequence

This architecture should be realized through:

1. sport repo freezes its signal-health contract;
2. sport repo validates PIT signal generation;
3. DLC-1 defines compatible optional `SportDecisionPackage` summary fields;
4. DLC-2 admission validates version/digest/cutoff;
5. DLC-4 All Bets exposes the summary;
6. DLC-8 EdgeStack ranking experiments may consume certified uncertainty/regime metadata;
7. DLC-11 evaluates whether the product policy improves long-run calibration/value/diversity.

## 11. Fail-closed behavior

DLC must degrade explicitly when:

- signal-health version is unsupported;
- summary timestamp is later than the sport package cutoff;
- source-state reference does not resolve;
- confidence/divergence fields are malformed;
- sport gate and summary are inconsistent under the declared sport contract.

A malformed signal-health summary may be omitted/degraded without rewriting the sport fair probability.

## 12. Governing rule

> **The sport decides how current state changes the prediction. DLC decides how an already-approved, uncertainty-aware prediction fits the cross-sport product and EdgeStack set.**
