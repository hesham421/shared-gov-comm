<!-- source: PHASE:F1 / SUB:F1-SCR-INV-003 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-001, API-INV-008, API-INV-016, API-INV-017, API-INV-040, REQ-INV-012, REQ-INV-035, REQ-INV-038, REQ-INV-039, REQ-INV-040, REQ-INV-041, REQ-INV-042, REQ-INV-113, REQ-INV-115, REQ-INV-121, REQ-INV-135, REQ-INV-137, SCR-INV-003, UXD-INV-001, UXD-INV-002, UXD-INV-003 -->
<!-- SUB:F1-SCR-INV-003:START traces=SCR-INV-003,UXD-INV-001,UXD-INV-002,UXD-INV-003,REQ-INV-012,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-113,REQ-INV-115,REQ-INV-121,REQ-INV-135,REQ-INV-137,API-INV-001,API-INV-008,API-INV-016,API-INV-017,API-INV-040 -->
### F1 · SCR-INV-003 — Stock balances
- Schemas: `StockBalanceRow`, `StockBalanceRowPageEnvelope`, `Availability`, `AvailabilityFigures`, `AvailabilityEnvelope`, `Warehouse` (labels), `LotRow` (lot options), `InventoryContext`.
- `BalanceFilter {variantPk?, term?, itemPk?, warehousePk?, lotPk?, nonZero, page, size}`. `term` holds a scanned barcode. A Variant chosen through UXD-INV-001 sets `variantPk`, and an Item chosen through UXD-INV-002 sets `itemPk`.
- `BalanceRowView`: the row plus `itemName` (UXD-INV-002), `warehouseLabel` (from the row's `warehouseCode` / `warehouseNameAr` / `warehouseNameEn`, for every role — ADR-INV-036) and `measureUnitLabel` (UXD-INV-003), plus `pairFirstFl` — true on the first row of each Variant × warehouse pair on the page, computed by the facade (gate-analysis revise 5, G6). `AvailabilityRowView` labels each per-warehouse row the same way.
<!-- SUB:F1-SCR-INV-003:END -->
