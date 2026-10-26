# ADR-CAT-014 — Accounting stays a NEW module and posts through the given `gl`; GL account, journal entry and accounting period are given
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 219a175d726d
traces      : —

## Decision
traces: OI-02, CAND-ACC-002, CAND-ACC-003, CAND-ACC-006
Accept A. The 1.6.0 manifest lists `gl` with chart-of-accounts, fiscal-periods, journal-entries, trial-balance and ledger-currencies. Those match the three candidates one for one, and G6 forbids rebuilding them. ACC keeps posting rules, cost centres, budgets and the screens that stay hidden by default. ACC owns no period entity. How period close is driven is settled when ACC is requested in Phase 2, against the `gl` entry. It has no CAT effect. `ledger-currencies` is noted as the base for the advanced multi-currency tier and decides nothing now. The domain-profile is re-run by hand to cite 1.6.0 and add the `gl` row.
Sources: manifest §1.5; DP §9; G6; change-manifest summary.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
