<!-- source: PHASE:F4 / SUB:F4-SCR-INV-010 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-076, AC-INV-077, AC-INV-079, AC-INV-080, API-INV-001, API-INV-008, API-INV-046, API-INV-047, REQ-INV-013, REQ-INV-017, REQ-INV-018, REQ-INV-064, REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-073, REQ-INV-074, REQ-INV-105, REQ-INV-106, REQ-INV-107, REQ-INV-122, SCR-INV-010 -->
<!-- SUB:F4-SCR-INV-010:START traces=SCR-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-122,AC-INV-076,AC-INV-077,AC-INV-079,AC-INV-080,API-INV-001,API-INV-008,API-INV-046,API-INV-047 -->
### OPENING-STOCK-SCREEN — SCR-INV-010
- **Guard**: `PERM_INV_OPENING_STOCK_VIEW`. Post needs `_CREATE`.
- **Facade**: OPENING-FACADE.
- **Cross-module**: none (ADR-INV-035).
- **Composition**: container page. The header (warehouse, hidden while one) is inline. Rows → inline in `OpeningRowsPane` beneath it, which a loaded sheet fills. A row is added or changed in `OpeningRowDrawer`, a sibling opened by `?line`, which returns the row to the form state. The row failures list is inline above the pane.
- **Saves**: ONE (Post → API-INV-047, all rows in one call). Download sheet (API-INV-046) is a file read, not a save.
- **Components**:
  - `OpeningSheetBar`: "Download sheet", and "Load sheet", a file input that reads xlsx in the browser through the lazy sheet reader (ADR-INV-035).
  - `OpeningRowsPane`, showing row number, barcode, quantity, unit cost, lot and expiry (lotExpiry on) and measure (dualQuantity on), plus totals of rows and quantity.
  - `OpeningRowFailures`.
  - `OpeningActions` (Post).
  After a successful post the route goes to `/inventory/stock-adjustments/<stockAdjustmentPk>` (AC-INV-076).
<!-- SUB:F4-SCR-INV-010:END -->
