# TDL Shadow Audit Ledger V1

**Status:** research-only / non-authoritative  
**Purpose:** preserve manual Daily Line-style audit snapshots while the production sport pipelines remain under development.

## Scope

This ledger records point-in-time shadow forecasts and Recommendation Gate states generated during manual audits. It exists to answer two different questions:

1. **Prediction quality:** did the independent fair probability describe outcomes well?
2. **Decision quality:** did the Recommendation Gate correctly distinguish actionable prices from likely-but-overpriced outcomes?

A winning recommendation is not automatically good process, and a losing recommendation is not automatically bad process. PASS, AVOID, WATCH, and excluded rows remain first-class evidence.

## Hard boundaries

- Shadow audit records are **not production Daily Line model outputs**.
- They do not grant recommendation, publishing, provider, execution, or wagering authority.
- They may not overwrite certified sport predictions, gates, model registries, or publication packages.
- Market prices are admitted only after the independent shadow projection is formed.
- Revisions create new snapshots or supersession records; prior snapshots are never rewritten to match later information.
- Already-started games cannot be backfilled into a pregame snapshot.
- Historical reconstructed records must be labeled `reconstructed`; they are not equivalent to exact point-in-time artifacts.

## Canonical shadow fields

Each future machine-readable snapshot should retain, where available:

- sport / event / slate identity;
- snapshot timestamp and information cutoff;
- independent projected score/runs;
- independent fair probability;
- fair odds;
- uncertainty/model range;
- model-disagreement state;
- data-quality state;
- market quote and no-vig probability;
- edge;
- expected value;
- conservative/lower-bound edge when available;
- Gate result and reason codes;
- maximum acceptable price / execution threshold;
- revision/supersession linkage;
- closing market (post-hoc only);
- settlement/outcome;
- Brier/log-loss contribution where meaningful;
- CLV / state-matched price diagnostics where available.

## Shadow Gate vocabulary

The research ledger uses:

- `RECOMMEND` — clears the current shadow actionability policy;
- `LEAN` — predictive/value signal exists but uncertainty, disagreement, market maturity, or model maturity blocks a full recommendation;
- `PASS` — prediction remains valid but current price/value does not clear;
- `AVOID` — critical evidence/state/market quality makes action unreliable;
- `WATCH` — potentially actionable but waiting on a material state/lineup/starter/quote update;
- `EXCLUDED_ALREADY_STARTED` — not eligible for the pregame snapshot.

These labels are research vocabulary and do not supersede any certified sport Recommendation Gate contract.

## Current shadow methodology

The current MLB shadow procedure approximates the planned TDL philosophy without claiming to be the trained production model:

1. build a market-independent run expectation from season production;
2. incorporate starter run-prevention evidence, including Statcast expected metrics where available;
3. model starter-to-bullpen transition;
4. add heavily shrunk current-roster pitcher matchup evidence;
5. account for park/weather and current information quality;
6. derive an independent score/run distribution and moneyline probability;
7. compare an independent team-strength/record baseline only as a **disagreement diagnostic**, not with arbitrary ensemble weights;
8. only after sealing the fair view, compare to current market price/no-vig probability;
9. apply the shadow Recommendation Gate.

No arbitrary permanent ensemble weights are authorized. The production architecture requires learned OOS contribution.

## Settlement

After the slate is final, append settlement/evaluation rather than editing the original snapshot. For historical reconstructed material, keep the reconstruction label permanently.
