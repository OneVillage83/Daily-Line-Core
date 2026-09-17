# CODEX_START_HERE — Daily-Line-Core

Use this file as the mandatory entry point for Codex/agent work in this repository.

## Read first

1. `README.md`
2. `AGENTS.md`
3. `docs/CURRENT_RESUME_POINT.md`
4. `docs/ARCHITECTURE_CERTIFICATION_LOG.md`
5. `docs/ARCHITECTURE.md`
6. `docs/OWNERSHIP_BOUNDARIES.md`
7. `docs/INTEGRATION_CONTRACTS.md`
8. `docs/EDGESTACK_PARLAY_OPTIMIZER_V1.md`
9. `docs/IMPLEMENTATION_ROADMAP.md`
10. `docs/CHANGE_JOURNAL.md`

## Current state

The repository was physically created and seeded on **2026-09-17** from architecture first staged in `OneVillage83/Daily-Data-Core/docs/daily_line_core/`.

**DLC-0 is documentation/architecture review pending. No production implementation is authoritative.**

## Exact next work

Do not jump to Kalshi integration, EdgeStack ranking code, website UI, report generation, or live sport ingestion.

The next intentional implementation step is:

> **DLC-0 review/freeze:** review ownership and canonical contracts, then define the first immutable contract schemas/fixtures for `SportDecisionPackage`, `MarketEvidenceBundleRef`, `AllBetsSnapshot`, EdgeStack contracts, and `DailyLinePublicationPackage`.

Implementation begins only when the owner explicitly authorizes DLC work and, for Bridge-driven execution, after DLC is added to the Bridge's authorized repository catalog.

## Hard boundaries

- DDC owns raw/shared provider evidence and generic market math.
- Daily-Model-Core owns shared independent modeling infrastructure through the fair-view boundary.
- Sport repos own sport probabilities, sport-specific decision logic, Recommendation Gates, same-event joint evidence, and settlement.
- DLC owns cross-sport assembly, All Bets, EdgeStack, product ranking, and final sealed publication packages.
- Website/report/infographic/automation are downstream renderers/consumers.
- No bankroll manager or automatic wagering in V1.

## Bridge constraint

The GrokBot-OpenAI-Bridge currently operates one repository per Codex turn. Never modify another Daily Line repository in the same turn unless the protocol and authorization are explicitly changed. Emit a handoff instead.
