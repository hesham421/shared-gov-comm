<!-- source: PHASE:F3 / SUB:F3-SCR-INV-007 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-109, AC-INV-111, AC-INV-113, API-INV-034, API-INV-036, API-INV-037, REQ-INV-010, REQ-INV-013, REQ-INV-017, REQ-INV-097, REQ-INV-098, REQ-INV-101, REQ-INV-102, REQ-INV-104, REQ-INV-105, REQ-INV-123, REQ-INV-139, SCR-INV-007, UXD-INV-007 -->
<!-- SUB:F3-SCR-INV-007:START traces=SCR-INV-007,REQ-INV-139,UXD-INV-007,REQ-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-097,REQ-INV-098,REQ-INV-101,REQ-INV-102,REQ-INV-104,REQ-INV-105,REQ-INV-123,AC-INV-109,AC-INV-111,AC-INV-113,API-INV-034,API-INV-036,API-INV-037 -->
### F3 · SCR-INV-007 — Stock adjustments
- `adjustmentSearchSchema`: `docNo` ≤ 30, date range, `warehousePk`, `reasonCode` (an INV_ADJUSTMENT_REASON option), `statusCode`.
- `adjustmentFormSchema`:
  - `reasonCode`: required, an active option other than OPENING_STOCK, which the reason select does not offer (only the opening stock load writes it — ADR-INV-047), text: RULE-INV-033 message (SRS) (AC-INV-113).
  - `docDate`: date, defaulted to today, on create only; a date after today → refinement, text: RULE-INV-051 message (SRS).
  - `warehousePk`: required on create when two or more are active, text: RULE-INV-008 message (SRS); null while one (REQ-INV-010, REQ-INV-013). Not editable after the first save.
  - `noteText`: ≤ 500.
  - `lines`: two lines with the same Variant and `lotNo` give a refinement, text: RULE-INV-048 message (SRS) (AC-INV-109).
- `adjustmentLineSchema`:
  - `variant`: required.
  - `quantityChange`: ≤ 3 decimals, text: RULE-INV-017 message (SRS). It must be ≠ 0 unless the Item is dual-quantity and `measureQtyChange` ≠ 0, text: RULE-INV-034 message (SRS) (AC-INV-111, REQ-INV-123).
  - `unitCost`: ≥ 0 with ≤ 4 decimals, text: RULE-INV-025 message (SRS); only on increase lines, optional (REQ-INV-102).
  - `lotNo`: ≤ 40.
  - `lotExpiryDate`: required with `lotNo` on an increase line of a lot-tracked Item, text: RULE-INV-035 message (SRS) (REQ-INV-105).
  - `lotNo` on a decrease line of a lot-tracked Item: required, text: RULE-INV-037 message (SRS), unless the user chose «بدون تشغيلة» / "Without a lot" (REQ-INV-139 — the server bounds the decrease by the lot-less stock).
  - `measureQtyChange`: ≤ 3 decimals, text: RULE-INV-017 message (SRS); required and ≠ 0 when `quantityChange` ≠ 0 and the Item is dual-quantity with dualQuantity on, text: RULE-INV-040 message (SRS) (REQ-INV-122).
<!-- SUB:F3-SCR-INV-007:END -->
