# Daily-Line-Core Architecture Certification Log

This file is the authoritative milestone/status record for DLC architecture and implementation authority.

## Status meanings

- **DOCUMENTED — REVIEW PENDING**: architecture exists but has not completed conformance review.
- **ARCHITECTURE-CERTIFIED**: architecture has passed review and is authoritative for implementation.
- **IMPLEMENTATION-CANDIDATE**: implementation exists but is not production-authoritative.
- **IMPLEMENTATION-CERTIFIED**: implementation has passed defined quality/equivalence gates.
- **PRODUCTION-AUTHORIZED**: explicit authority granted for production use.
- **BLOCKED**: a named blocking condition prevents advancement.

## Current milestones

| Milestone | Scope | Status | Evidence / note |
|---|---|---|---|
| DLC-0 | Repository bootstrap, ownership, architecture foundation | **DOCUMENTED — REVIEW PENDING** | Repository seeded 2026-09-17 from DDC staging architecture. |
| DLC-1 | Canonical cross-repository contracts | Planned | Requires DLC-0 architecture review/freeze. |
| DLC-2 | Sport package admission | Planned | Requires DLC-1. |
| DLC-3 | DDC market-evidence admission | Planned | Requires DLC-1. |
| DLC-4 | All Bets Prediction Scanner | Planned | Requires DLC-2/3. |
| DLC-5 | EdgeStack candidate engine | Planned | Requires DLC-4. |
| DLC-6 | Joint probability / dependency engine | Planned | Requires sport joint-evidence contracts. |
| DLC-7 | Provider Combo/parlay quote adapters | Planned | No live provider integration authorized yet. |
| DLC-8 | EdgeStack optimizer/classes | Planned | Requires DLC-5/6/7. |
| DLC-9 | DailyLinePublicationPackage sealing | Planned | Requires prior product contracts. |
| DLC-10 | Downstream consumer contracts | Planned | Cross-repository work split by repository. |
| DLC-11 | Post-game feedback, PIT evaluation and certification | Planned | Governing feedback architecture documented; single-game findings cannot directly mutate production. |
| DLC-12 | Bridge + operational onboarding | Planned | Requires TDLA Bridge proving-ground acceptance + owner authorization. |

## Cross-system supplemental architecture milestones

| Milestone | Scope | Status | Evidence / note |
|---|---|---|---|
| TDL-LIVE-0 | Cross-system live topology, contamination firewall, DDLC / sport-live / DLLC ownership, synchronization, live modeling/gate and release plan | **DOCUMENTED — REVIEW PENDING** | `docs/THE_DAILY_LINE_LIVE_ARCHITECTURE_V1.md`; documentation only, no repository creation or implementation authority. |
| DDLC-1+ | Daily Data Live Core evidence/market implementation | Planned | Requires TDL-LIVE-0 review/freeze and explicit repository/implementation authorization. |
| Sport-Live-1+ | Sport-specific live state/model/gate implementations, beginning with MLB | Planned | Requires DDLC contracts plus sport-repository-specific authorization and validation. |
| DLLC-1+ | Cross-sport live product assembly | Planned | Requires sealed sport-live decision contracts and explicit DLLC repository authority. |

## Current authority statement

No DLC code path, recommendation output, EdgeStack output, publication package, provider integration, or Bridge workflow is production-authoritative.

No TDL Live repository, live provider integration, live model, live recommendation, live publication, Live EdgeStack, automated wagering path, or live Bridge workflow is authorized by TDL-LIVE-0 documentation.

The architecture files are design authority only after DLC-0 review/certification. Until then, they are the review baseline.

## Locked owner decisions already reflected in the review baseline

- DLC is a distinct peer repository, not a DDC submodule and not a TDLA feature implementation.
- Sport repositories own sport probabilities, individual-market decision semantics, Recommendation Gates, same-event joint evidence and settlement.
- DDC owns shared evidence/acquisition/generic market math.
- DLC owns cross-sport All Bets assembly, EdgeStack 2–5 leg optimization, cross-sport product ranking and sealed publication package assembly.
- Downstream report/infographic/website/TDLA consumers render sealed truth and do not recompute it.
- Bankroll/stake management and automated wagering are outside V1.
- Pregame signal-health detection remains sport-owned; DLC consumes only versioned publication-safe summaries and cannot recalculate sport fair probability.
- Post-game feedback may generate research proposals but not production self-modification.

## Next certification action

Perform a DLC-0 cross-repository architecture conformance review against the current DDC, DMC, sport-repo, website and TDLA boundaries. Correct contradictions through versioned documentation, then explicitly mark DLC-0 `ARCHITECTURE-CERTIFIED` before implementation work begins.
