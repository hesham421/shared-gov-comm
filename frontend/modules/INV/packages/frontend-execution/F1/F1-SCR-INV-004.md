<!-- source: PHASE:F1 / SUB:F1-SCR-INV-004 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-001, API-INV-008, API-INV-018, API-INV-019, API-INV-040, REQ-INV-016, REQ-INV-055, REQ-INV-056, REQ-INV-075, REQ-INV-076, SCR-INV-004, UXD-INV-004 -->
<!-- SUB:F1-SCR-INV-004:START traces=SCR-INV-004,UXD-INV-004,REQ-INV-016,REQ-INV-055,REQ-INV-056,REQ-INV-075,REQ-INV-076,API-INV-001,API-INV-008,API-INV-018,API-INV-019,API-INV-040 -->
### F1 · SCR-INV-004 — Stock move history
- Schemas: `StockMove`, `StockMoveEnvelope`, `StockMovePageEnvelope`, `Warehouse` and `LotRow` (labels), `InventoryContext`.
- `MoveFilter {variantPk?, warehousePk?, lotPk?, moveTypeCode?, docNo?, sourceDocTypeCode?, sourceDocRef?, dateFrom?, dateTo?, page, size}`. `sourceDocTypeCode` + `sourceDocRef` identify one source document exactly; they are set only by the jumps from SCR-INV-005 and SCR-INV-007 and shown as a removable "document" chip, not as editable fields.
- Lot labels come from each row's `lotCode`; the bundle label of `bundleVariantPk` from UXD-INV-004 (CORE "Labels on every row").
- Derived `showRunningBalance = variantPk != null && warehousePk != null` (REQ-INV-076). When one warehouse is active, `warehousePk` is the default warehouse taken from the context.
- `MoveDetailNav = stockMovePk`, which comes from the `move` search param.
<!-- SUB:F1-SCR-INV-004:END -->
