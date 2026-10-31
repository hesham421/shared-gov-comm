<!-- source: PHASE:F3 / SUB:F3-SCR-INV-010 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-078, AC-INV-081, API-INV-047, REQ-INV-013, REQ-INV-017, REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-074, REQ-INV-105, REQ-INV-122, REQ-INV-140, SCR-INV-010 -->
<!-- SUB:F3-SCR-INV-010:START traces=SCR-INV-010,REQ-INV-140,REQ-INV-013,REQ-INV-017,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-074,REQ-INV-105,REQ-INV-122,AC-INV-078,AC-INV-081,API-INV-047 -->
### F3 · SCR-INV-010 — Opening stock
- `openingStockSchema`:
  - `idempotencyKey`: set by the form, never edited by the user (ADR-INV-053).
  - `warehousePk`: required when two or more are active, text: RULE-INV-008 message (SRS).
  - `rows`: 1–1000 entries, text: RULE-INV-027 message (SRS) (AC-INV-078).
- `openingRowSchema`:
  - `rowNo`: integer ≥ 1.
  - `barcode`: required, ≤ 64; empty → «أدخل الباركود.» / "Enter the barcode." (v1 names a Variant by barcode only — ADR-INV-035).
  - `quantity`: > 0, text: RULE-INV-026 message (SRS); ≤ 3 decimals, text: RULE-INV-017 message (SRS).
  - `unitCost`: required and ≥ 0, text: RULE-INV-026 message (SRS) (REQ-INV-074, AC-INV-081); ≤ 4 decimals, text: RULE-INV-025 message (SRS).
  - `lotNo`: ≤ 40.
  - `lotExpiryDate`: required with `lotNo`, text: RULE-INV-035 message (SRS).
  - `measureQty`: ≥ 0 with ≤ 3 decimals, text: RULE-INV-017 message (SRS).
- Rows read from the sheet go through the same schema before posting. Rows that fail are listed by `rowNo` with their message, and nothing is sent until they pass (REQ-INV-072). Variant resolution is the server's (ADR-INV-035).
<!-- SUB:F3-SCR-INV-010:END -->
