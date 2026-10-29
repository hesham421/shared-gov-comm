# ADR-INV-002 — erp-core 1.6.0 re-match makes gl given; lot and dual quantity stay platform-own in INV
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:34:05+00:00
Dialogue-key: eb01eafd8bb3
traces      : POL-INV-006, POL-INV-009

## Decision
Accept option A, with one amendment. The 1.6.0 table in this brief lists `gl` (chart-of-accounts, fiscal-periods, journal-entries, trial-balance, ledger-currencies) and no entry for custom attributes, lot, dual quantity, cash & bank or a tax adapter. Rebuilding the ledger would break G6. The amendment is that INV never calls `GlPostingApi`. Stock valuation and adjustment postings reach the ledger through ACC, which consumes stock-move events and owns the posting rules (G5, KB §5 #9). KB §4.1 calls `lot_id` and the dual-quantity columns "core seams", but the manifest is authoritative (engine §1.5).
traces: OI-02, OI-06, POL-INV-006, POL-INV-009

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0-round2.response2.md
