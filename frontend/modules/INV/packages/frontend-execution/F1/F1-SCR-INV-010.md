<!-- source: PHASE:F1 / SUB:F1-SCR-INV-010 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-001, API-INV-008, API-INV-046, API-INV-047, REQ-INV-013, REQ-INV-017, REQ-INV-018, REQ-INV-064, REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-073, REQ-INV-074, REQ-INV-105, REQ-INV-106, REQ-INV-107, REQ-INV-122, REQ-INV-140, SCR-INV-010 -->
<!-- SUB:F1-SCR-INV-010:START traces=SCR-INV-010,REQ-INV-140,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-122,API-INV-001,API-INV-008,API-INV-046,API-INV-047 -->
### F1 · SCR-INV-010 — Opening stock
- Schemas: `OpeningStockRequest`, `StockAdjustmentEnvelope`, `ErrorResponse.details` (row failures).
- `OpeningStockFormState {idempotencyKey, warehousePk | null, docDate?, rows: OpeningRowDraft[]}`. `idempotencyKey` is generated (`crypto.randomUUID()`) when the load form opens, kept across retries of the same load, and replaced after a successful post (ADR-INV-053); it is never shown.
- `OpeningRowDraft {key, rowNo, barcode, quantity, unitCost, lotNo?, lotExpiryDate?, measureQty?}`. The request sends `barcode` as entered and leaves `variantPk` null; v1 names a Variant by barcode only (ADR-INV-035).
- `RowFailure {rowNo, code, messageAr, messageEn}`.
- `SheetColumnMap`: the header text (ar | en) for each field, used to read the sheet.
<!-- SUB:F1-SCR-INV-010:END -->
