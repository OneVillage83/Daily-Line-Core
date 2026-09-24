# TDL Fact Engine / Game Intel V1

**Status:** SUPPLEMENTAL ARCHITECTURE — DOCUMENTED, REVIEW PENDING  
**Date:** 2026-09-24  
**Repository:** `OneVillage83/Daily-Line-Core`  
**Production authority:** NONE

## 1. Purpose

The TDL Fact Engine turns verified point-in-time sports evidence into concise, traceable facts that can be displayed throughout The Daily Line product without inventing claims or contaminating the prediction pipeline.

Customer-facing product label:

> **Game Intel**

Example user experience:

> **HOME EDGE** — Texas ranks 4th in the AL in home wins.

The sentence is presentation. The authority is the structured evidence underneath it: exact metric, scope, rank, denominator, cutoff, source/provenance, and the sport interpretation that made it publication-safe.

The Fact Engine has two intentionally separate lanes:

1. **Publication facts** — useful, interesting, verified facts for the website, report, infographic, social/video, and live game surfaces.
2. **Predictive fact signals** — research candidates that may eventually receive a very small sport-owned influence only after point-in-time out-of-sample validation proves incremental predictive value.

A fact being interesting does **not** make it predictive.

## 2. Core design rule

> **Facts are structured evidence first and prose second.**

No LLM, template renderer, website component, or automation worker may invent the numerical content of a fact.

A fact statement must be generated from or bound to structured authoritative fields. Natural-language paraphrasing may improve readability, but it may not change the metric, scope, rank, direction, cutoff, or evidentiary meaning.

## 3. Cross-repository ownership

### 3.1 Daily-Data-Core (DDC)

DDC owns the shared evidence/acquisition layer:

- immutable provider evidence;
- point-in-time `observed_at` / `available_at` provenance;
- shared neutral facts such as market, weather, venue, travel/rest, and generic statistical evidence where that evidence is provider-neutral;
- generic aggregation/ranking primitives that are truly reusable across sports;
- evidence digests and source references.

DDC does **not** decide that “4th in home wins” is predictive for MLB, nor does it create sport-specific betting meaning.

### 3.2 Daily-* sport repositories

Each sport repository owns:

- sport identity and metric semantics;
- sport-specific candidate fact derivation;
- cohort/split definitions such as AL/NL, conference, home/away, starter/bullpen/unit, position, surface, etc.;
- publication-safe sport interpretation;
- duplicate/overlap detection against existing model features;
- predictive eligibility research;
- any eventual probability/confidence/Recommendation-Gate influence;
- sport-specific live-fact interpretation.

Examples:

- Daily-MLB owns the meaning of home/road splits, starting-pitcher trends, bullpen state, lineup splits, pitch-type/repertoire matchups, park effects, etc.
- Daily-NFL owns pressure, coverage, route, EPA/success, red-zone, rest/travel, personnel and matchup facts.
- Other Daily-* repositories define their own sport-native fact catalog.

### 3.3 Daily-Line-Core (DLC)

DLC owns the cross-sport product layer:

- admission of sealed publication-safe sport facts;
- compatibility validation;
- fact ranking for product presentation;
- relevance / rarity / freshness / clarity scoring;
- diversity and repetition control;
- cross-sport product taxonomy;
- selection of the top facts for each event/product surface;
- sealing `PublicationFact` records into the immutable `DailyLinePublicationPackage`;
- product-level provenance and supersession.

DLC must not reinterpret raw sport data or apply sport-specific prediction adjustments.

### 3.4 Website/app

The website owns rendering only:

- Game Intel card/chip presentation;
- filters and expansion;
- archive/search;
- source/as-of display;
- accessible labels and explanation surfaces.

The website must not recompute ranks, statistics, predictive eligibility, or model influence.

### 3.5 TDLA / downstream content automation

TDLA may turn sealed facts into approved report/social/video content, but it consumes the published fact truth. It does not create new sports claims.

## 4. Fact taxonomy

V1 fact families:

### 4.1 Team / season rank
Examples:
- home wins rank;
- road win percentage rank;
- run differential / point differential rank;
- offensive/defensive efficiency rank.

### 4.2 Home / away / venue splits
Examples:
- record at home;
- scoring at venue;
- park/stadium environment;
- surface/roof context.

### 4.3 Recent form / trend
Examples:
- rolling 7/14/30-day performance;
- velocity or command trend;
- recent EPA/success trend;
- usage/role change.

### 4.4 Matchup interaction
Examples:
- batter/pitch-type interaction;
- offense vs handedness;
- pass protection vs pressure package;
- route/coverage matchup.

### 4.5 Player / starter / unit state
Examples:
- starting pitcher state;
- bullpen fatigue;
- quarterback pressure response;
- offensive line continuity;
- player usage trend.

### 4.6 Availability / lineup / personnel
Examples:
- confirmed lineup;
- inactive player;
- role/depth change;
- late scratch.

### 4.7 Rest / travel / schedule
Examples:
- travel distance;
- timezone shift;
- days rest;
- back-to-back / short-week / series schedule context.

### 4.8 Environment
Examples:
- wind;
- temperature;
- precipitation risk;
- roof state;
- altitude.

### 4.9 Market context
Examples:
- major line movement;
- unusually high disagreement;
- exchange/sportsbook divergence.

**Boundary:** market-context facts are publication/decision-layer facts only. They may not leak into the independent TDL Unified Line unless a separately certified market-aware layer explicitly owns that use.

### 4.10 Historical / milestone / trivia
Examples:
- franchise milestone;
- player streak;
- historical matchup note.

These are normally **display-only** unless a separate predictive feature proves forward value.

## 5. Canonical structured fact record

Conceptual V1 contract:

```text
FactRecord
  fact_id
  schema_version
  sport
  league
  event_ref
  subject_refs[]
  fact_type
  metric_key
  metric_value?
  unit?
  comparator?
  rank?
  cohort?
  denominator?
  scope
  window_start?
  window_end?
  sample_size?
  direction?
  statement_template_id
  statement_args
  concise_statement
  detail_statement?
  as_of
  observed_at?
  available_at
  expires_at?
  evidence_refs[]
  source_refs[]
  evidence_digest
  quality_state
  publication_eligibility
  predictive_status
  model_feature_refs[]
  overlap_cluster?
  display_score?
  product_tags[]
  supersedes_fact_id?
```

Required quality states:

- `VERIFIED`
- `DEGRADED`
- `STALE`
- `CONFLICTED`
- `UNVERIFIED`

Only `VERIFIED` facts are eligible for normal customer-facing publication in V1.

## 6. Predictive status

Every fact carries one predictive status independent of its display quality:

- `DISPLAY_ONLY`
- `RESEARCH_CANDIDATE`
- `SHADOW_ELIGIBLE`
- `PROMOTED_FEATURE`

Default is `DISPLAY_ONLY`.

A high display score never changes predictive status.

## 7. Example: ranked home-performance fact

The user-visible example:

> Texas ranks 4th in the AL in home wins.

The system should represent it approximately as:

```text
fact_type = TEAM_SEASON_RANK
metric_key = team.home_wins
subject = TEX
scope = HOME
cohort = AL
rank = 4
denominator = <AL team count at cutoff>
as_of = <prediction/publication cutoff>
quality_state = VERIFIED
publication_eligibility = true
predictive_status = DISPLAY_ONLY or RESEARCH_CANDIDATE
```

The exact rank must be recomputed/verified at the publication cutoff. The displayed sentence is never stored as the only evidence.

## 8. Display scoring

DLC may calculate a **display score** to decide which facts deserve limited screen space.

Candidate components:

- event relevance;
- matchup specificity;
- extremeness / rarity;
- rank significance;
- freshness;
- source confidence;
- sample sufficiency;
- customer clarity;
- novelty vs other displayed facts;
- diversity across fact types.

The score is a **presentation ranking**, not a prediction weight.

V1 should normally show:

- **1 headline fact** on a compact game card;
- **3–6 facts** on the full matchup page;
- at most **1–2 closely related facts** from the same statistical cluster unless the user expands the section.

This keeps the site informative without becoming a wall of stats.

## 9. Website product design

Recommended V1 surfaces:

### Compact game/slate card

A single small Game Intel line:

> **HOME EDGE** Texas ranks 4th in the AL in home wins.

Tap/click opens details.

### Matchup page

A dedicated **Game Intel** section with categorized fact cards:

- Team form
- Matchup
- Players / units
- Environment
- Rest / travel
- Market context

Each card may expose:

- concise fact;
- “Why it matters” sport-authored explanation;
- sample/window;
- “As of” timestamp;
- expandable provenance/source detail.

### Prediction / Recommendation Gate view

Relevant facts may be linked under:

> **Evidence behind this view**

This is explanatory provenance. It must not imply every displayed fact influenced the model.

### Archive

The archive stores and renders the exact facts sealed with that publication package. A later updated rank must not rewrite the historical page.

### Live view

Live facts use a separate live fact package and timestamp. They may update as game state changes, but may never rewrite the frozen pregame package.

## 10. Predictive-research lane

The eventual “tiny weight” requested by the owner is supported only through a controlled sport-owned research path.

### 10.1 First rule: no double counting

Before a fact can influence a decision, the sport repository must determine whether the underlying information is already represented by:

- team strength;
- home-field effects;
- rolling form;
- player state;
- matchup features;
- simulation inputs;
- signal-health/regime features;
- existing Recommendation Gate uncertainty inputs.

If the fact is a restatement of existing model information, incremental fact influence is **zero**.

Example:

“4th in AL home wins” may be highly display-worthy while adding no new predictive information beyond existing team-strength and home-field features.

### 10.2 Promotion evidence

A predictive candidate must pass:

1. exact point-in-time reconstruction;
2. no future/result/closing-line leakage;
3. minimum sample requirements declared before evaluation;
4. out-of-sample testing;
5. incremental lift over the current model;
6. calibration review;
7. feature-overlap / multicollinearity review;
8. stability across seasons/regimes;
9. ablation;
10. sport-owner approval and versioned promotion.

### 10.3 Preferred integration

The preferred destination is the sport model or sport Recommendation Gate as a structured feature, not an ad-hoc prose-based adjustment.

If a residual “fact-context micro-layer” is tested, it must:

- remain sport-owned;
- consume structured signals only;
- be versioned and auditable;
- start in shadow mode;
- preserve the independent/market-aware boundary;
- enforce cluster caps so correlated facts cannot stack;
- fail to zero influence when evidence is weak.

### 10.4 Initial shadow safety ceiling

For early research only, a useful ceiling is:

- **maximum absolute fair-probability movement from one promoted fact signal: 0.50 percentage points**;
- **maximum aggregate fact-context movement: 1.00 percentage point**.

These are **research guardrails, not production weights**.

Historical/OOS calibration may justify a smaller cap, no cap because the feature is integrated natively into the model, or a final result of **zero influence**.

The production value must be evidence-driven.

## 11. Recommendation Gate use

A fact may prove more valuable for **uncertainty/confidence** than for mean fair probability.

Examples:

- conflicting current-form signal;
- low sample size;
- lineup uncertainty;
- unusual travel/rest state;
- bullpen workload;
- weather uncertainty.

A sport repository may eventually use validated fact signals to widen uncertainty, downgrade confidence, or alter Recommendation Gate behavior without materially moving the fair probability.

That behavior still requires sport-owned backtesting/certification.

## 12. Market-contamination boundary

Facts derived from odds, line movement, exchange prices, prediction markets, or bookmaker consensus are marked `MARKET_CONTEXT`.

They are excluded from the independent prediction path.

They may enter only:

- the market-aware decision layer;
- Recommendation Gate/value/timing logic where already permitted;
- customer-facing Market Context cards;
- post-hoc evaluation.

This preserves the Daily Line rule: independent prediction first, market comparison second.

## 13. Live Fact Engine

A later live path should reuse the contract shape but remain a separate authority:

```text
Daily Data Live Core
        |
        v
sport live state / live fact candidate builder
        |
        v
LiveFactPackage
        |
        v
DLC/live product selector
        |
        +--> website live Game Intel
        +--> live decision explanation
```

Live facts can include:

- current pitcher/quarterback performance vs expectation;
- bullpen/bench availability;
- possession/inning leverage;
- live efficiency mismatch;
- foul/injury/usage state;
- weather changes;
- live market movement, clearly labeled market-aware.

Pregame `FactRecord` objects are immutable. Live facts cannot contaminate or rewrite the pregame prediction record.

## 14. Publication package integration

`DailyLinePublicationPackage` should eventually include:

```text
game_intel
  facts[]
  headline_fact_refs[]
  event_fact_index
  product_surface_selections

fact_provenance_manifest
```

Each recommendation / All Bets row may include optional `fact_refs[]`.

A fact reference means “relevant evidence/context,” not “this fact changed the probability,” unless the sport package explicitly records promoted model influence.

## 15. Fact influence transparency

If a future promoted fact actually affects a sport decision, the sport package should expose:

```text
predictive_fact_influence
  fact_ref
  promoted_feature_id
  model_or_gate_component
  influence_type
  pre_fact_probability?
  post_fact_probability?
  absolute_probability_delta?
  confidence_delta?
  gate_delta?
  model_version
  config_version
```

This gives the website/report a truthful distinction between:

- **Interesting fact**
- **Supporting context**
- **Model-used signal**

## 16. Anti-hallucination and quality rules

1. No claim without structured evidence refs.
2. Rank facts require explicit cohort and denominator.
3. Split facts require explicit scope and sample size when meaningful.
4. “Best/worst/top” claims require a complete or defined comparison cohort.
5. Streak/trend claims require an exact window.
6. Historical facts must be time-bounded.
7. Facts expire when their underlying state can materially change.
8. Conflicting providers fail closed or show a conflict state internally.
9. LLM wording may not alter numbers or causal meaning.
10. “Why it matters” must distinguish correlation/context from validated predictive influence.
11. Publication facts never silently mutate after sealing.
12. Live facts never rewrite frozen pregame facts.

## 17. Implementation roadmap

### FE-0 — contract / taxonomy freeze
Owner: DLC + cross-repository review.

- freeze FactRecord / PublicationFact contracts;
- freeze taxonomies and quality states;
- define compatibility/versioning;
- define source/provenance requirements.

### FE-1 — Daily-MLB display-only pilot
Owner: Daily-MLB.

Start with deterministic, easy-to-verify facts:

- team home/road ranks;
- run differential / scoring ranks;
- starter season/current-form facts;
- bullpen workload;
- lineup availability;
- park/weather;
- rest/travel.

No model influence.

### FE-2 — DLC fact admission / selection
Owner: DLC.

- validate sealed sport facts;
- compute display scores;
- de-duplicate correlated facts;
- select headline and matchup facts;
- add to publication package.

### FE-3 — Website Game Intel
Owner: website.

- compact headline fact;
- matchup Game Intel section;
- source/as-of expansion;
- archive persistence;
- accessibility/mobile behavior.

### FE-4 — report / infographic / social reuse
Owner: downstream renderers + TDLA.

Use only sealed fact refs.

### FE-5 — live facts
Owner: Daily Data Live Core + sport live engines + DLC live product layer.

Keep live and pregame authority separate.

### FE-6 — predictive shadow research
Owner: each sport repository.

- tag Research Candidates;
- run PIT/OOS incremental tests;
- detect overlap with existing features;
- test confidence/gate effects first;
- optionally test bounded micro-layer.

### FE-7 — evidence-based promotion
Owner: sport repository.

Only certified signals become `PROMOTED_FEATURE`.

No global generic “fact weight” is authorized.

## 18. Acceptance criteria for V1 display feature

The display-only Fact Engine is ready for production only when:

- facts are derived from structured data;
- every fact has immutable provenance;
- point-in-time cutoffs are enforced;
- rank/cohort/sample semantics are deterministic;
- stale/conflicted/unverified facts fail closed;
- website renders only sealed facts;
- archive preserves original publication facts;
- fact selection does not alter sport probabilities;
- automated wording cannot invent or alter numerical claims;
- cross-repository contract compatibility tests pass.

## 19. Architecture invariants

1. Facts are evidence, not prose.
2. Display relevance and predictive value are separate.
3. A fact is zero-weight by default.
4. No double counting.
5. Prediction influence is sport-owned.
6. Market facts never contaminate the independent fair line.
7. DLC ranks/selects facts for product surfaces; it does not reinterpret sport truth.
8. Website/report/TDLA render sealed fact truth.
9. Pregame facts are immutable after publication.
10. Live facts are separate from pregame authority.
11. Single-game outcomes cannot promote fact weights.
12. Any predictive promotion requires PIT/OOS evidence and versioned approval.

## 20. Current authority

This document is supplemental architecture only.

It does **not**:

- certify DLC-0;
- change the current DLC resume point;
- authorize FE-1 implementation;
- authorize website UI work;
- authorize live data integration;
- authorize any fact-based probability adjustment;
- authorize a generic production “fact weight.”

The current DLC resume remains the existing DLC-0 architecture/ownership conformance review.
