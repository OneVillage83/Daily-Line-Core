# Post-Game Improvement Reconciliation — 2026-09-20

**Status:** DOCUMENTED CROSS-REPOSITORY RESEARCH MAP  
**Purpose:** Map the seven post-game audit suggestions to existing architecture so The Daily Line improves current systems instead of creating duplicate engines.

## 1. Summary

The seven suggestions are **not seven new systems**.

They map into four existing owners:

1. **Daily-NFL state/model architecture**
2. **Daily-MLB SHARE + bullpen/model architecture**
3. **Sport Recommendation Gates**
4. **DLC Post-Game Feedback Loop**

Daily-Model-Core remains the generic research/governance layer for conditional trust, learned weighting, calibration and ablation.

## 2. Reconciliation matrix

| # | Audit suggestion | Existing owner | Existing capability | Refinement |
|---:|---|---|---|---|
| 1 | NFL early-season prior shrinkage | Daily-NFL F-6/M7/M9 + DMC | early-season prior state already implemented and projected | add sample-maturity context; learn prior decay rather than overreacting to Week 1 |
| 2 | NFL Pressure Response State | Daily-NFL F-7/F-8/F-13/F-16/F-22 | QB pressure response, pass-protection/pass-rush, matchup interaction and tracking features already defined | activate versioned pressure/NGS features when rights/PIT support exists; learn pressure-family interactions |
| 3 | NFL offensive-system regime state | Daily-NFL F-9 + M7 + Unified Ensemble | coaching regime, empirical scheme, player/unit continuity already exist | derive system-maturity/support context; increase uncertainty for low-support new systems |
| 4 | MLB pitcher health/current-state divergence | Daily-MLB SHARE | velocity/command/workload/current-state engine already documented | strengthen health/workload observations and baseline-vs-current contradiction handling |
| 5 | MLB bullpen-state model | Daily-MLB Unified Modeling + ModelFeatureSet | bullpen latent state, availability/workload and bullpen-stress specialist already exist | add explicit starter→bullpen transition view for full-game distributions; enrich future reliever Statcast state |
| 6 | Recommendation Gate contradiction penalty | Daily-NFL F-20 + Daily-MLB Gate/SHARE | both sports already separate prediction from recommendation uncertainty | add state-regime contradiction inputs/reason codes and prospectively validated stricter actionability |
| 7 | Conservative outcome attribution | DLC Post-Game Feedback Loop | multi-causal attribution, PIT counterfactuals and no self-modification already exist | require layer-specific failure attribution and separately score prediction, decision and outcome quality |

## 3. New documentation created

### Daily-NFL

`docs/architecture/POSTGAME_RESEARCH_REFINEMENTS_20260920.md`

This is an addendum to the locked F-0 through F-24 architecture. It explicitly avoids creating separate early-season, pressure or offensive-system engines.

### Daily-MLB

`docs/POSTGAME_RESEARCH_REFINEMENTS_20260920.md`

This extends SHARE, the existing bullpen architecture and Recommendation Gate integration.

### Daily-Model-Core

`docs/SIGNAL_HEALTH_AND_CONDITIONAL_TRUST_V1.md`

Now also recognizes sport-owned sample-maturity/prior-support context for future conditional model trust.

## 4. System-wide architecture after reconciliation

```text
SPORT-SPECIFIC PIT EVIDENCE
          |
          v
SPORT STATE ENGINES
  NFL: Team / Player / Unit / Coaching / Injury
  MLB: ModelFeatureSet / SHARE / Bullpen state
          |
          v
MATCHUP + CURRENT-STATE CONTEXT
          |
          v
MODEL ZOO / SPECIALISTS
          |
          v
DMC-GOVERNED LEARNED ENSEMBLE / CALIBRATION
          |
          v
SPORT FAIR PROBABILITY
          |
          v
SPORT RECOMMENDATION GATE
  edge + uncertainty + contradiction + calibration
          |
          v
SportDecisionPackage
          |
          v
DLC All Bets / EdgeStack
          |
          v
GAME / SETTLEMENT
          |
          v
DLC POST-GAME FEEDBACK
          |
          v
LAYER-SPECIFIC FINDING
          |
          v
SPORT/DMC/DLC RESEARCH HANDOFF
          |
          v
PIT/OOS BACKTEST
          |
          v
REVIEWED PROMOTION ONLY
```

## 5. No duplicate-system rule

When a future post-game audit proposes a new capability, first ask:

1. Is the underlying state already owned by an existing sport state engine?
2. Is the feature already defined but unavailable in the feature registry?
3. Is the behavior already supported by conditional ensemble weighting?
4. Is the desired action actually a Recommendation Gate policy rather than a model change?
5. Is the post-game insight actually an attribution/research problem already owned by DLC feedback?

Only create a new subsystem when the existing owner cannot represent the required semantics without violating its boundary.

## 6. Promotion rule

None of the seven refinements may become a production weight/penalty because one or two games illustrated the problem.

Required path:

```text
post-game finding
→ owner-repo hypothesis
→ PIT feature/state reconstruction
→ chronological OOS experiment
→ ablation
→ calibration
→ subgroup/robustness review
→ shadow/challenger
→ explicit promotion
```

## 7. Current authority

This reconciliation is architecture/research documentation only.

It does not:

- start Daily-NFL M11/M14/M15/M17/M19;
- activate unavailable Next Gen/tracking features;
- change Daily-MLB ModelFeatureSet V1;
- change current sport Recommendation Gate thresholds;
- modify production model weights;
- retrain or promote models;
- alter DLC's current implementation resume point.

## 8. Governing principle

> **Prefer deeper use of existing state, model, gate and feedback architecture over parallel handicapping systems. Every post-game lesson must have one clear owner and one evidence-based promotion path.**
