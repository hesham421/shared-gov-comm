<!-- source: PHASE:F3 / SUB:F3-SCR-INV-003 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-016, REQ-INV-039, SCR-INV-003, UXD-INV-001, UXD-INV-002 -->
<!-- SUB:F3-SCR-INV-003:START traces=SCR-INV-003,UXD-INV-001,UXD-INV-002,REQ-INV-039,API-INV-016 -->
### F3 · SCR-INV-003 — Stock balances
- `balanceSearchSchema`:
  - `variantPk` / `itemPk`: from the pickers (UXD-INV-001, UXD-INV-002).
  - `term`: ≤ 64, a scanned barcode.
  - `warehousePk`, `lotPk`: from the lookups.
  - `nonZero`: boolean.
- A change of any filter resets `page` to 0. No entry form.
<!-- SUB:F3-SCR-INV-003:END -->
