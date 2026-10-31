<!-- source: PHASE:F1 / SUB:F1-SCR-INV-006 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-001, API-INV-008, API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-040, API-INV-044, REQ-INV-013, REQ-INV-017, REQ-INV-018, REQ-INV-087, REQ-INV-088, REQ-INV-089, REQ-INV-090, REQ-INV-091, REQ-INV-092, REQ-INV-093, REQ-INV-094, REQ-INV-095, REQ-INV-096, REQ-INV-122, SCR-INV-006, UXD-INV-006 -->
<!-- SUB:F1-SCR-INV-006:START traces=SCR-INV-006,UXD-INV-006,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,REQ-INV-122,API-INV-001,API-INV-008,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-040,API-INV-044 -->
### F1 · SCR-INV-006 — Stock counts
- Schemas: `StockCount`, `StockCountLine`, `StockCountCreateRequest`, `StockCountLineAddRequest`, `StockCountLineUpdateRequest`, `StockCountEnvelope`, `StockCountLineEnvelope`, `StockCountPageEnvelope`, `StockCountPostResultEnvelope`, `VersionedActionRequest`, `LotRow`, `ItemStockSettings`.
- `CountFilter {docNo?, dateFrom?, dateTo?, warehousePk?, statusCode?, page, size}`.
- `CountStartState {warehousePk | null, noteText}`.
- `ScanState {barcode?, variant?: CatVariantRef, countedQty, lotPk?, countedMeasureQty?}`. `countedQty` defaults to `1`.
- `CountLineEditState {countedQty, countedMeasureQty?, version}` and `CountLineNav = stockCountLinePk`.
<!-- SUB:F1-SCR-INV-006:END -->
