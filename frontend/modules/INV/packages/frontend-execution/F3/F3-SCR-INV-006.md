<!-- source: PHASE:F3 / SUB:F3-SCR-INV-006 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-098, API-INV-026, API-INV-028, API-INV-029, API-INV-030, REQ-INV-013, REQ-INV-017, REQ-INV-087, REQ-INV-088, REQ-INV-089, REQ-INV-090, REQ-INV-091, REQ-INV-095, REQ-INV-122, REQ-INV-139, SCR-INV-006, UXD-INV-006 -->
<!-- SUB:F3-SCR-INV-006:START traces=SCR-INV-006,REQ-INV-139,UXD-INV-006,REQ-INV-013,REQ-INV-017,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-095,REQ-INV-122,AC-INV-098,API-INV-026,API-INV-028,API-INV-029,API-INV-030 -->
### F3 · SCR-INV-006 — Stock counts
- `countSearchSchema`: `docNo` ≤ 30, date range, `warehousePk`, `statusCode`.
- `countStartSchema`:
  - `warehousePk`: required when two or more warehouses are active, text: RULE-INV-008 message (SRS) (REQ-INV-017); null while one (REQ-INV-013).
  - `noteText`: ≤ 500.
- `scanSchema`:
  - `barcode` (≤ 64) or `variant`: one is required.
  - `countedQty`: ≥ 0 with ≤ 3 decimals, text: RULE-INV-047 message (SRS) (AC-INV-098).
  - `lotPk` and `countedMeasureQty`: required as in transfers, texts RULE-INV-037 and RULE-INV-040 — a lot-tracked line may choose «بدون تشغيلة» / "Without a lot" (REQ-INV-139).
  The barcode input is numeric-keyboard friendly (`inputmode="numeric"`) and accepts a scanner's Enter as submit (REQ-INV-090).
- `countLineEditSchema`: `countedQty` ≥ 0 (RULE-INV-047), `countedMeasureQty` ≥ 0, `version`.
<!-- SUB:F3-SCR-INV-006:END -->
