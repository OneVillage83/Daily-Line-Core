# The Daily Line Service API + MCP / Plugin Architecture V1

**Status:** DOCUMENTED — REVIEW PENDING  
**Date:** 2026-09-29  
**Repository:** `OneVillage83/Daily-Line-Core`

## 1. Purpose

The Daily Line Service API is the presentation-agnostic access layer for authoritative Daily Line product data.

Its job is to make the same canonical Daily Line truth available to multiple clients without forcing each client to reconstruct analysis from web searches, duplicate business logic, or read mutable internal tables.

Target consumers include:

- `thedailyline.bet`;
- future native/mobile clients;
- internal One Village tooling and Dots;
- ChatGPT through a Daily Line MCP/plugin adapter;
- Codex and approved automation workflows;
- future approved partner/API clients.

The Service API is **not** a new prediction engine, Recommendation Gate, EdgeStack engine, market-acquisition system, or wagering execution system.

## 2. Position in the Daily Line architecture

```text
Daily-Data-Core -------------------------------+
shared PIT evidence / odds / provenance        |
                                               v
Daily-* sport repos --> sealed SportDecisionPackages
                                               |
                                               v
                                      +------------------+
                                      | Daily-Line-Core  |
                                      | All Bets         |
                                      | EdgeStack        |
                                      | Game Intel       |
                                      | product sealing  |
                                      +---------+--------+
                                                |
                              DailyLinePublicationPackage
                                                |
                                                v
                                  +-------------------------+
                                  | THE DAILY LINE          |
                                  | SERVICE API             |
                                  | read/query/access layer |
                                  +------------+------------+
                                               |
              +--------------------------------+--------------------------------+
              |                                |                                |
              v                                v                                v
        Website / App                  MCP / ChatGPT Plugin               Dot / Internal
              |                                |                                |
              +------------------------ future Mobile / API --------------------+
```

The immutable `DailyLinePublicationPackage` remains the product-truth boundary. The Service API exposes, indexes, filters, and projects that truth; it does not silently create new recommendation truth.

## 3. Authority and ownership

### 3.1 Upstream authority remains unchanged

- **Daily-Data-Core** owns shared evidence acquisition, odds/market observations, provenance, freshness, and sport-neutral market math.
- **Daily-Model-Core** owns shared independent modeling/research governance through the calibrated fair-view boundary.
- **Daily-* sport repositories** own sport-native modeling, fair probabilities, uncertainty, individual-market decision logic, Recommendation Gates, same-event dependency evidence, explanations, and settlement interpretation.
- **Daily-Line-Core** owns cross-sport All Bets assembly, EdgeStack, Game Intel product admission/ranking, cross-sport product indexing, and sealed publication packages.

### 3.2 Service API authority

The Service API may own:

- read/query contracts over sealed Daily Line product state;
- package lookup by immutable identity;
- latest-valid-revision resolution under explicit cutoff/freshness rules;
- stable client-facing DTO/view schemas;
- filtering, pagination, archive lookup, and search indexes;
- authentication and authorization hooks;
- entitlement-aware response shaping;
- rate limiting, caching, observability, and request receipts;
- MCP/tool adapters that map tool calls to Service API operations.

The Service API may **not**:

- alter sport fair probabilities;
- rerun or override Recommendation Gates;
- recompute EdgeStack candidate truth independently of DLC;
- reacquire raw provider evidence around DDC;
- blend later information into an earlier point-in-time snapshot;
- hide or replace provenance/cutoff metadata required by the contract;
- place wagers, submit sportsbook/exchange orders, or manage wallets/accounts in V1.

## 4. Canonical read model

Every analytical response must resolve to immutable upstream authority.

At minimum, a response that contains predictive/product analysis should carry or expose:

```text
response_schema_version
request_id
resolved_at

publication_package_id
publication_package_schema_version
publication_package_created_at
data_cutoff
market_cutoff
supersession_state

sport_package_refs[]
market_evidence_refs[]
policy/config version
quality/degradation_state

resource payload
provenance refs
```

When the response contains both an independent Daily Line estimate and market information, they must remain separately attributable.

The service must preserve the canonical sequence:

1. independent sport prediction/fair probability is produced and frozen upstream;
2. market evidence is admitted separately with its own timestamp/provenance;
3. market comparison / edge / EV / Recommendation Gate state is consumed from its authoritative sealed layer;
4. Service API only exposes the resulting sealed truth.

A client must never be able to mistake a market-derived value for the independent model forecast.

## 5. Initial REST/query surfaces

Exact HTTP paths are implementation details to freeze during contract review. The conceptual V1 capabilities are:

### Slate / discovery

- `get_today_slate`
- `get_slate(date, sport?, league?)`
- `get_event(event_ref)`
- `search_archive(query, filters, cutoff?)`

### Analysis

- `get_game_analysis(event_ref, package_id?)`
- `get_market_analysis(market_ref, package_id?)`
- `get_player_matchup(subject_refs, event_ref?, package_id?)`
- `get_matchup_lab(event_ref, subject_refs?, package_id?)`
- `get_game_intel(event_ref, package_id?)`

### Product state

- `get_all_bets_snapshot(snapshot_id | slate/cutoff)`
- `get_publication_package(publication_package_id)`
- `get_latest_publication(slate_scope, cutoff_policy)`
- `get_results(publication_package_id | event_ref)`

### Authenticated user/product features

These may be exposed only when a separate account/entitlement contract authorizes them:

- saved trends;
- saved matchup queries;
- alert definitions;
- subscriber-only analysis fields;
- archive depth or premium product surfaces.

User/account state must not become prediction authority.

## 6. MCP and ChatGPT plugin adapter

The public-facing MCP/plugin layer should be a **thin adapter over the Service API**.

```text
ChatGPT / Dot / Codex
        |
        v
Daily Line MCP / Plugin
        |
        v
Daily Line Service API
        |
        v
sealed Daily-Line-Core product truth
```

The MCP server must not bypass the Service API to query mutable model databases or provider feeds directly.

Initial tool candidates:

- `daily_line.today`
- `daily_line.event_analysis`
- `daily_line.market_analysis`
- `daily_line.matchup_lab`
- `daily_line.game_intel`
- `daily_line.archive_search`
- `daily_line.publication_get`

Tool descriptions should make clear that returned analysis is Daily Line-authored/model-derived output and should preserve timestamps, version IDs, provenance, and degradation state.

### Public plugin policy gate

A public ChatGPT marketplace/plugin surface is a **separate distribution-policy decision** from the Service API itself.

The API may support the full authorized Daily Line product internally, but any public plugin must have an explicit capability allowlist matching the platform's current review/policy requirements. If a platform restricts gambling-facilitation features, the public adapter must fail closed or expose only the permitted sports-data/analytics subset. It must not disguise prohibited functionality under different labels.

The Service API should therefore be capability-oriented so public, private, internal, and subscriber clients can use different approved scopes without forking product truth.

## 7. Authentication, authorization, and entitlements

V1 should support a separation between:

- public read surfaces;
- authenticated Daily Line user surfaces;
- paid/subscriber entitlements;
- internal One Village/Dot/service identities;
- administrative/operations identities.

Authorization determines **which already-authoritative fields/resources may be returned**. It must not alter probabilities or recommendation truth.

Recommended future auth direction:

- OAuth/OIDC for user-facing integrations;
- short-lived service credentials for internal services;
- scoped tokens/claims;
- explicit plugin/MCP scopes;
- auditable authorization decisions.

## 8. Versioning and compatibility

The Service API requires independent semantic versioning for:

- API/tool contract version;
- response DTO schema;
- MCP tool contract;
- underlying `DailyLinePublicationPackage` schema compatibility.

Breaking changes must create a new version rather than silently reinterpret an old field.

Clients must reject unsupported breaking publication-package versions rather than guessing compatibility.

## 9. Point-in-time, revisions, and "latest"

`latest` is not a mutable truth shortcut.

A latest-resolution operation must:

1. define a slate/event scope;
2. define the allowed as-of/cutoff policy;
3. select the newest sealed package valid under that policy;
4. return its immutable `publication_package_id`;
5. expose whether a later superseding revision exists.

Historical requests at cutoff `T` must behave as though `T` is the present. Later lineups, injuries, prices, results, closing lines, or other future information cannot leak into the response.

## 10. Caching

Caching is allowed only when the cache key is strong enough to preserve authority.

At minimum, analytical caches should be bound to the immutable package/snapshot identity or a latest-resolution key that is invalidated on a new compatible sealed revision.

A cache hit must never make stale data appear current without its original timestamps/freshness state.

## 11. Observability and audit

Every request should be traceable through:

- request/trace ID;
- authenticated principal/client identity where applicable;
- tool/client name and version;
- resolved package/snapshot IDs;
- response schema version;
- authorization scope;
- cache state;
- latency/status;
- explicit degradation/error code.

The API request receipt is operational evidence only. It does not supersede upstream product truth.

## 12. Error and degradation behavior

The API must fail clearly for:

- unknown/ambiguous event or market refs;
- unsupported sport/market;
- missing required sealed package;
- incompatible schema version;
- stale or unavailable required evidence;
- authorization/entitlement failure;
- unavailable downstream index/cache;
- public-channel capability restrictions.

It must not invent data or silently substitute a different event/market.

Where upstream packages are degraded, the response should preserve that state rather than converting it into apparent certainty.

## 13. V1 non-goals

The Service API V1 does not authorize:

- wager/order placement;
- wallet or sportsbook account connections;
- bankroll or personalized stake management;
- hidden re-ranking based on a user's risk profile;
- re-training/re-weighting sport models;
- provider scraping outside DDC;
- bypassing sealed publication packages;
- a public plugin launch before policy/security review.

## 14. Implementation sequencing

The Service API is documentation-only until DLC-0 and the prerequisite canonical product contracts are certified.

Recommended sequence:

1. **DLC-0:** include this architecture in ownership/conformance review.
2. **DLC-1:** freeze service-facing identifiers and canonical package schemas.
3. **DLC-4 / DLC-9:** prove deterministic All Bets and sealed publication-package truth.
4. **DLC-10A:** freeze Service API read/query contracts, DTOs, auth scopes, versioning, PIT/latest semantics.
5. **DLC-10B:** implement website/mobile/internal consumers against the Service API where appropriate.
6. **DLC-10C:** implement MCP/plugin adapter as a thin Service API client; run security, policy, entitlement, provenance, and tool-behavior acceptance.
7. **DLC-12 / One Village integration:** allow approved Dot/Bridge operational access only through scoped contracts.

## 15. Acceptance criteria before authoritative use

The Service API is not production-authoritative until tests demonstrate:

- identical package inputs produce semantically identical analytical responses;
- no API path recalculates sport probability or Recommendation Gate truth;
- exact package/snapshot IDs and cutoffs are preserved;
- historical cutoff queries cannot see future information;
- latest-resolution and supersession behavior is deterministic;
- unsupported/ambiguous identities fail closed;
- auth/entitlement scopes cannot cross privilege boundaries;
- cache behavior cannot conceal staleness or provenance;
- MCP tools call the Service API rather than bypassing it;
- public plugin capability restrictions are enforced by allowlist/policy;
- no V1 endpoint can place or execute a wager.

## 16. Product principle

The Service API should make this distinction explicit:

> **Search finds information about The Daily Line. The Service API asks The Daily Line for its own canonical, versioned analysis.**

That same answer should be reproducible across the website, app, ChatGPT plugin, Dot, or any other approved client because all clients resolve to the same sealed product truth.
