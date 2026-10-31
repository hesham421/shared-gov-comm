<!-- source: PHASE:F1 / SUB:F1-SCR-INV-007 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-001, API-INV-008, API-INV-016, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-040, API-INV-041, API-INV-044, REQ-INV-010, REQ-INV-013, REQ-INV-017, REQ-INV-018, REQ-INV-064, REQ-INV-067, REQ-INV-097, REQ-INV-098, REQ-INV-099, REQ-INV-100, REQ-INV-101, REQ-INV-102, REQ-INV-103, REQ-INV-104, REQ-INV-105, REQ-INV-106, REQ-INV-107, REQ-INV-114, REQ-INV-123, SCR-INV-007, UXD-INV-007 -->
<!-- SUB:F1-SCR-INV-007:START traces=SCR-INV-007,UXD-INV-007,REQ-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-067,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-114,REQ-INV-123,API-INV-001,API-INV-008,API-INV-016,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-044 -->
### F1 · SCR-INV-007 — Stock adjustments
- Schemas: `StockAdjustment`, `StockAdjustmentLine`, `StockAdjustmentLineRequest`, `StockAdjustmentCreateRequest`, `StockAdjustmentUpdateRequest`, `StockAdjustmentEnvelope`, `StockAdjustmentPageEnvelope`, `VersionedActionRequest`, `Lot`, `LotRow`, `StockBalanceRow` (write-off seed), `ItemStockSettings`.
- `AdjustmentFilter {docNo?, dateFrom?, dateTo?, warehousePk?, reasonCode?, statusCode?, page, size}`.
- `AdjustmentFormState {warehousePk | null, docDate, reasonCode, noteText, lines: AdjustmentLineDraft[], version?}`. The update request carries no `warehousePk`, so the warehouse is fixed after the first save.
- `AdjustmentLineDraft {key, rowNo, variant: CatVariantRef, quantityChange, unitCost?, lotNo?, lotExpiryDate?, measureQtyChange?}`.
- `WriteOffSeed {reason: 'EXPIRY_WRITE_OFF', lotPk, warehousePk}`, which comes from the search params of `/new`.
<!-- SUB:F1-SCR-INV-007:END -->
