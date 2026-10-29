# ADR-PTY-002 — erp-core 1.6.0 is re-matched: `gl` becomes a given; custom attributes, lot, dual quantity, cash and bank, and the tax adapter stay platform-own
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: a6f53d1b52d4
traces      : —

## Decision
Accepted. The 1.6.0 manifest table adds `gl` (chart-of-accounts, fiscal-periods, journal-entries, trial-balance; GlPostingApi). The other twelve entries are unchanged against the domain-profile's 1.5.0 match. No entry provides custom attributes, lot, dual quantity, cash and bank, or a tax adapter. `report` lists `payment-file-export`, which is a bank payment file, not a cash-and-bank module.
Amendment: STL's payment-file output is a partial hit on `report`. ACC keeps posting rules, cost centres, budgets and accounting mode. ACC's own P0 run moves GL account, journal entry and accounting period to the given, with no entity IDs. This run only records the match.
traces: OI-02, OI-06, CAND-ACC-002, CAND-ACC-003, CAND-ACC-006

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md
