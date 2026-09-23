# Reconstructed TDL Shadow Audit Baseline — 2026-09-20 through 2026-09-22

**Status:** reconstructed research baseline  
**Point-in-time fidelity:** partial; reconstructed from retained chat audit outputs and final results  
**Use:** baseline only; do not treat as an exact production/PIT ledger

## Why this exists

The manual audits from Sunday 2026-09-20 through Tuesday 2026-09-22 preceded this ledger. This document preserves the major Gate/outcome record so later TDL Shadow audits have a starting comparison set.

## Gate summary

| Gate | Wins | Losses | Pushes | Notes |
|---|---:|---:|---:|---|
| RECOMMEND | 18 | 20 | 0 | Includes Sunday NFL/MLB and Monday player/game markets; heavily affected by Giants-Rams/Dart injury slate |
| LEAN | 6 | 6 | 1 | Mixed market families |
| PASS directional predictions | 7 | 5 | 0 | Measures prediction direction, not wager performance |
| AVOID | 1 | 0 | 0 | Avoid outcome is not graded as a wager |
| WATCH | 1 | 1 | 0 | Execution/state validation incomplete |

## Key sport/day findings

### 2026-09-20 NFL
- Raw winner predictions: **8-6**.
- Actionable RECOMMEND sides: approximately **6-6**.
- CORE bucket: approximately **3-3**.
- Finding: confidence labels appeared too aggressive in several high-probability favorites and require calibration rather than hand adjustment.

### 2026-09-20 MLB
- Actionable side recommendations: **8-3**.
- ATL-HOU was a useful process example: the likely-winner direction and the value recommendation were not the same thing.
- Finding: MLB Statcast/season-data workflow produced materially stronger separation between prediction and price than the free-form audit.

### 2026-09-21 MLB
Latest/superseding pregame state:
- BAL ML small PLAY: win.
- DET directional PASS prediction: correct direction.
- MIN ML PLAY: loss.
- Actionable latest-snapshot record: **1-1**.

Earlier Toronto lean is retained conceptually as a superseded information-state decision; it should not be erased because later lineup/workload evidence changed the side.

### 2026-09-21 NYG-LAR
- Game market: Giants +7 PLAY lost; Under 47.5 LEAN won; Rams ML AVOID was not a wager even though LAR won.
- Recommended player/game markets: approximately **1-8**.
- LEAN markets: approximately **1-3**.
- Jaxson Dart was injured very early, materially affecting multiple correlated rushing/game-script markets.
- Finding: injury variance explains part, not all, of the miss. The audit also overestimated the Giants rushing matchup and should be retained as a formal miss-attribution case.

### 2026-09-22 MLB — first TDL-style shadow slate
Moneyline buckets:
- strict PLAY: **2-1**;
- LEAN: **1-3**;
- directional PASS: **1-4**.

Totals:
- NYM-TEX Over 8 LEAN: win;
- ARI-COL Over 11 PLAY: loss;
- HOU-SEA Over 7: push.

The preliminary ordering PLAY > LEAN > PASS was directionally encouraging but far too small a sample for statistical claims.

### 2026-09-22 live shadow audit
- HOU ML PASS despite high win probability: HOU won; price/value remained the reason for PASS.
- HOU-SEA O9.5 WATCH/provisional: lost; also exposed a conditional-total formulation issue.
- TEX live ML PASS: lost, so the gate avoided an expensive losing favorite.
- NYM-TEX O8.5 LEAN: win.
- ARI WATCH due unsynchronized price: ARI won, but no execution claim was fabricated.

## Research conclusions

1. Prediction accuracy and Recommendation Gate performance must be scored separately.
2. PASS/AVOID/WATCH rows are essential training/evaluation evidence.
3. Revision history matters; late lineup/starter information can legitimately supersede an earlier recommendation without rewriting it.
4. MLB should use the TDL-style quantitative structure rather than free-form narrative handicapping.
5. Market-family calibration must be separate: moneyline success does not imply total/run-line calibration.
6. Live state-price synchronization is a hard prerequisite for executable live recommendations.
7. Future exact ledgers should calculate Brier, log loss, CLV, and EV using immutable contemporaneous probabilities/quotes rather than reconstructed estimates.
