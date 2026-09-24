# Daily-Line-Core Integration Contracts V1

**Status:** DOCUMENTED — SCHEMA IMPLEMENTATION PENDING

This document defines the intended cross-repository contract boundaries for DLC. Exact JSON/Pydantic schemas and compatibility fixtures are a DLC-1 implementation task.

## 1. Core rule

DLC consumes **sealed, versioned, point-in-time-correct upstream artifacts** and emits one sealed cross-sport product artifact.

DLC does not read mutable upstream application tables as product authority unless a future versioned adapter contract explicitly wraps them into an immutable package.

## 2. `SportDecisionPackage`

Producer: Daily-MLB, Daily-NFL, Daily-NCAAF, or future Daily-* sport repository.

Purpose: transfer sport-authoritative prediction/decision truth to DLC without transferring ownership of sport logic.

Minimum required fields/concepts:

```text
sport_decision_package_id
schema_version
package_digest
sport
league
slate_scope
event_scope_refs[]
prediction_timestamp
data_cutoff_timestamp
sport_release_id
model/config provenance

markets[]:
  canonical sport market ref
  event/participant refs
  market class
  side
  threshold/line
  TDL fair probability/fair line
  uncertainty/calibration metadata
  sport market-aware estimate if certified
  Recommendation Gate state
  Recommendation Gate reason/evidence refs
  supported/degraded/unsupported state
  settlement rule version

joint_evidence_refs[]?
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
  source_state_ref
explanation_claim_refs[]
supersedes_package_id?
```

Rules:

- immutable once sealed;
- later sport rerun produces a new package;
- unsupported markets are explicit;
- DLC never fabricates missing probabilities;
- package digest/version must validate before admission;
- post-start information must not enter a pre-start package;
- any `signal_health_summary` is sport-authored, PIT-bound, publication-safe, and optional; DLC must not reconstruct sport regime state from raw data or treat absence as `STABLE`.

## 2.1 Pregame signal-health compatibility

Sport repositories may expose an optional signal-health/regime summary after they have incorporated that state into their own independent forecast, uncertainty, market-aware decision, and Recommendation Gate.

The cross-sport behavior is governed by `PREGAME_SIGNAL_HEALTH_INTEGRATION_V1.md`.

DLC may display and use certified uncertainty/regime metadata as a product-ranking dimension, but it may not apply its own sport-specific probability adjustment.

## 3. `MarketEvidenceBundleRef`

Producer/authority: Daily-Data-Core.

Purpose: give DLC exact point-in-time market observations and provenance without duplicating DDC acquisition logic.

Minimum required concepts:

```text
market_evidence_bundle_id / ref
schema_version
evidence_digest
cutoff_timestamp
provider/source set

quotes[]:
  provider market/event identity
  provider/book/exchange/prediction-market identity
  line/threshold/side
  price / decimal multiplier where applicable
  implied probability
  no-vig / hold / consensus fields where applicable
  available_at / observed_at / provider timestamp
  freshness state
  fee/execution metadata when known
  raw evidence refs

market_history_refs[]?
```

Rules:

- DDC evidence remains immutable;
- DLC may derive product comparison fields but must preserve DDC source refs;
- stale or incompatible quotes cannot silently remain active;
- real provider Combo/RFQ pricing must be stored as its own market observation when available.

## 4. Market reconciliation

DLC joins sport-authoritative canonical market refs to DDC provider market evidence through explicit versioned mappings/adapter output.

No name-only fuzzy reconciliation may become production authority without an explicit reviewed contract.

Admission must fail closed for ambiguous or contradictory mappings.

## 5. `AllBetsSnapshot`

Producer: DLC.

Purpose: canonical cross-sport product view of every supported/modelable market at one cutoff.

Conceptual fields:

```text
all_bets_snapshot_id
schema_version
created_at
product_cutoff
sport_package_refs[]
market_evidence_refs[]
policy_version

rows[]:
  product bet ID
  sport/league/event refs
  market key/class/side/threshold
  provider/venue
  current quote/multiplier
  market-implied probability
  no-vig/consensus refs where applicable
  sport fair probability/fair price
  probability edge
  gross/net EV where valid
  uncertainty/calibration metadata
  Recommendation Gate
  reason/evidence refs
  quote timestamp/freshness
  support/degradation state
  settlement rule version
  full provenance refs
```

Rules:

- deterministic from identical admitted inputs/policy;
- every supported market appears according to the certified product-universe rules;
- EdgeStack uses this snapshot rather than a hand-picked subset;
- a row's sport fair probability and gate remain upstream-authoritative values.

## 6. EdgeStack contracts

### `EdgeStackLeg`

References exactly one eligible All Bets row plus immutable upstream probability/quote/gate provenance.

### `EdgeStackCandidate`

Conceptual fields:

```text
candidate_id
leg_refs[2..5]
semantic_leg_set_digest
candidate_policy_version
same_event_dependency_state
joint_probability_method
joint_probability
joint_uncertainty
provider_quote_required
```

### `EdgeStackQuote`

```text
quote_id
candidate_id
provider
quote_timestamp
expires_at?
combo_multiplier / contract price
raw_break_even_probability
net_break_even_probability?
fee/execution metadata?
standalone_product_diagnostic?
provider evidence refs
```

### `EdgeStackRecommendation`

```text
edgestack_id
candidate_ref
quote_ref
class: CORE | VALUE | UPSIDE
ranking_views[]
joint_probability
break_even_probability
probability_edge
gross_ev
net_ev?
correlation/dependency method
uncertainty/confidence
reason/claim refs
publication eligibility state
```

Rules:

- 2–5 legs only;
- every leg independently eligible;
- same-event combinations require sport-authoritative dependency evidence;
- actual combo/parlay quote is pricing authority when available;
- no leg is added only to improve payout optics.

## 7. `DailyLinePublicationPackage`

Producer: DLC.

Consumers: report, infographic, website/app, TDLA and future approved product channels.

Conceptual fields:

```text
publication_package_id
schema_version
created_at
data_cutoff
market_cutoff
policy/config version
sport_package_refs[]
market_evidence_refs[]
all_bets_snapshot_ref
recommended_picks[]
edgestack_catalog
  core[]
  value[]
  upside[]
product ranking/index refs
report_claims[]
infographic_claims[]
website_view refs[]
automation_safe_fact_refs[]
provenance_manifest
quality/degradation_state
supersedes_package_id?
```

Rules:

- immutable once sealed;
- consumers render rather than recompute;
- every claim resolves to immutable source authority;
- multiple valid pregame revisions are permitted and explicitly lineaged;
- result/settlement enrichment must preserve the original prediction/market snapshot.

## 8. Downstream consumer acknowledgement

A downstream consumer should record at minimum:

- `publication_package_id`;
- consumer/version;
- received/rendered timestamp;
- output artifact/receipt ID;
- any presentation transformation version;
- failure/degradation status.

This receipt is not new recommendation authority.

## 9. Version compatibility

Cross-repository contracts require explicit semantic/schema versioning.

A consumer must reject unsupported breaking versions rather than guess compatibility. Additive optional fields must have documented default/absence semantics.

Exact compatibility matrices are defined during DLC-1 and corresponding upstream/downstream repository handoffs.

## 10. Point-in-time requirement

All inputs to a DLC snapshot must have been defensibly available by the snapshot cutoff. Historical replay at time `T` behaves as though `T` is the present.

Closing prices, final scores, later injury reports, later lineups and other future information cannot enter an earlier snapshot.

## 11. Explicit exclusions

These V1 contracts contain no authoritative fields for:

- recommended stake size;
- bankroll percentage;
- Kelly fraction;
- stop-loss/chase state;
- wallet/account IDs;
- order-placement authority.

Those require separately versioned future architecture.


## Fact Engine / Game Intel contract supplement

The detailed cross-system design is `docs/FACT_ENGINE_GAME_INTELLIGENCE_V1.md`.

### Sport-to-DLC fact handoff

A sport may emit immutable publication-safe structured facts bound to the same-or-earlier point-in-time cutoff as its decision package.

Conceptual fields include:

```text
FactRecord
  fact_id
  schema_version
  sport / league / event_ref
  subject_refs[]
  fact_type
  metric_key / metric_value
  rank? / cohort? / denominator?
  scope / window / sample_size?
  concise_statement
  as_of / observed_at? / available_at
  evidence_refs[] / source_refs[] / evidence_digest
  quality_state
  publication_eligibility
  predictive_status
  model_feature_refs[]
  overlap_cluster?
  supersedes_fact_id?
```

Only `VERIFIED` publication-eligible facts may enter normal customer-facing V1 output.

### Predictive boundary

`predictive_status` is one of:

- `DISPLAY_ONLY`
- `RESEARCH_CANDIDATE`
- `SHADOW_ELIGIBLE`
- `PROMOTED_FEATURE`

DLC cannot promote a fact between these states. Promotion belongs to the sport repository and requires PIT/OOS incremental evidence.

### Publication package extension

`DailyLinePublicationPackage` may carry:

```text
game_intel
  facts[]
  headline_fact_refs[]
  event_fact_index
  product_surface_selections

fact_provenance_manifest
```

All Bets rows/recommendations may reference `fact_refs[]` as explanatory context. A reference does not imply model influence unless the sport package separately exposes a promoted feature/influence record.
