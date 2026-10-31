# ADR-INV-039 — Acceptance cases for F3-SCR-INV-012 and F4-SCR-INV-011
Status      : ACCEPTED
Stage       : P4 (Test Plan) · repair round 1        Module: INV        Version: v1
Lane        : test-gen · claude:opus
Decided     : 2026-10-31
traces      : TC-INV-238, TC-INV-240, TC-INV-241, AC-INV-069, AC-INV-071, REQ-INV-063, REQ-INV-065, UXD-INV-011, SCR-INV-011, SCR-INV-012, ADR-INV-037

## Context
`gov.py analyze` (C10.11) refused the P4 plans: no test case named the frontend units `F3-SCR-INV-012` and
`F4-SCR-INV-011` on its `Package` line. ADR-INV-037 point 4 listed both as units nothing derives a case for. That
was wrong for both:
- `F3-SCR-INV-012` (valuation search schema) binds the UXD-INV-011 pickers (`variantPk` / `itemPk`). ADR-INV-037
  point 3 sends a UXD's "rendered" TC to the F3 unit when the foreign field is a search-form picker that F3 binds.
  TC-INV-238 is that case, but it named `F4-SCR-INV-012`.
- `F4-SCR-INV-011` (settings screen: the "Allow negative stock" switch and the horizon field) traces AC-INV-071,
  whose Given / When has the Owner save the negative-stock setting. P4 had placed AC-INV-071 on the backend track
  only (TC-INV-017).
- Once TC-INV-238 moves, `F4-SCR-INV-012` (valuation result table, grand total, detail drawer) would have no case.
  It traces AC-INV-069. REQ-INV-063 names the Owner as a reader of the valuation, so the figures AC-INV-069 states
  can be observed on SCR-INV-012.

## Decision
1. TC-INV-238 names `F3-SCR-INV-012`. This applies ADR-INV-037 point 3; the case itself is unchanged.
2. TC-INV-240 is derived from AC-INV-069 (REQ-INV-063), package `F4-SCR-INV-012`. The valuation screen renders the
   AC's figures: total quantity 20, average unit cost 23.0000, stock value 460.00, plus the grand total.
3. TC-INV-241 is derived from AC-INV-071 (REQ-INV-065), package `F4-SCR-INV-011`. The Owner switches "Allow negative
   stock" off and saves, and the screen keeps the value. The refusal of the later online issue stays on the backend
   track (TC-INV-017).
4. Both ACs are now on both tracks (ADR-INV-037 point 1). Each frontend case asserts only the UI half of its AC.
   The values come from the AC and no text is asserted beyond the SRS B3 label.
5. ADR-INV-037 point 4 no longer applies to `F3-SCR-INV-012` and `F4-SCR-INV-011`. It still applies to the other
   units it lists.

## Consequences
- The frontend plan holds TC-INV-173 … TC-INV-241 (69 cases, ratio 1.05 against 55 ACs + 11 UXD).
- No REQ, AC, RULE, API, SCR, UXD or code changes. No profile `no_tests` flag is needed for these two units.

## Status
ACCEPTED — non-breaking.
