<!-- source: PHASE:F1 / SUB:F1-SCR-INV-005 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-001, API-INV-008, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-040, API-INV-044, REQ-INV-011, REQ-INV-014, REQ-INV-062, REQ-INV-078, REQ-INV-079, REQ-INV-080, REQ-INV-081, REQ-INV-082, REQ-INV-083, REQ-INV-084, REQ-INV-085, REQ-INV-086, REQ-INV-122, SCR-INV-005, UXD-INV-005 -->
<!-- SUB:F1-SCR-INV-005:START traces=SCR-INV-005,UXD-INV-005,REQ-INV-011,REQ-INV-014,REQ-INV-062,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,REQ-INV-122,API-INV-001,API-INV-008,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-040,API-INV-044 -->
### F1 · SCR-INV-005 — Transfers
- Schemas: `Transfer`, `TransferLine`, `TransferLineRequest`, `TransferCreateRequest`, `TransferUpdateRequest`, `TransferEnvelope`, `TransferPageEnvelope`, `VersionedActionRequest`, `LotRow`, `ItemStockSettings`.
- `TransferFilter {docNo?, dateFrom?, dateTo?, fromWarehousePk?, toWarehousePk?, statusCode?, page, size}`.
- `TransferFormState {docDate, fromWarehousePk, toWarehousePk, noteText, lines: TransferLineDraft[], version?}`.
- `TransferLineDraft {key, transferLinePk?, variant: CatVariantRef, lotPk?, quantity, measureQty?}`. `key` is a client row key for the line drawer's navigation param.
- `LineDrawerNav = 'new' | key`, which comes from the `line` search param.
- `ItemTracking {lotTrackedFl, dualQuantityFl, measureUomCode}` is read per line Item through API-INV-044.
<!-- SUB:F1-SCR-INV-005:END -->
