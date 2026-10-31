# ADR-INV-004 — DAG edge kinds are confirmed; ACC reads INV, APR reads INV, PUR, SLS and STL, STL and TAX read INV for Branch, and INS lists all 13 modules below it
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:34:05+00:00
Dialogue-key: 732890b828de
traces      : —

## Decision
Accept the proposal with three amendments to the block.

- **Confirmed:** the DP §6 kinds are HARD-FK to masters and documents, and SOFT-READ for ACC, APR, SFR and INS.
- **Amendment 1 — added ACC → INV and APR → INV, PUR, SLS, STL.** Under auto-posting (D7), stock adjustments must
  reach the ledger. INV transfers and adjustments are documents, so they fall under "any document module" (D6).
  Approval outcomes return through the given `events`, so there is no reverse edge.
- **Amendment 2 — added STL → INV and TAX → INV.** Both consume Branch (cash and bank per branch; the issuer branch
  on the e-invoice). Round 1 named them as Branch consumers without the edge.
- **Amendment 3 — added SFR to INS.** The round-1 block listed 12 modules where its own note said 13.

The `platform-dependencies` block of the platform summary carries these edges (R-04).

The tier rule holds: STL and TAX stay at tier 4, ACC and APR at 5, INS at 6. Receipt cost flows from PUR into INV on
the receipt call, so INV has no edge to PUR.

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0-round2.response2.md (decision "DAG edge kinds are confirmed …"; filled in at gate-analysis revise 1, G21)
