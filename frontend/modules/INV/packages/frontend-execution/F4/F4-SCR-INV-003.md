<!-- source: PHASE:F4 / SUB:F4-SCR-INV-003 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-015, AC-INV-044, AC-INV-129, AC-INV-135, AC-INV-151, AC-INV-153, API-INV-001, API-INV-008, API-INV-016, API-INV-017, API-INV-040, REQ-INV-012, REQ-INV-035, REQ-INV-038, REQ-INV-039, REQ-INV-040, REQ-INV-041, REQ-INV-042, REQ-INV-113, REQ-INV-115, REQ-INV-121, REQ-INV-135, REQ-INV-137, SCR-INV-003, UXD-INV-001, UXD-INV-002, UXD-INV-003 -->
<!-- SUB:F4-SCR-INV-003:START traces=SCR-INV-003,UXD-INV-001,UXD-INV-002,UXD-INV-003,REQ-INV-012,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-113,REQ-INV-115,REQ-INV-121,REQ-INV-135,REQ-INV-137,AC-INV-015,AC-INV-044,AC-INV-129,AC-INV-135,AC-INV-151,AC-INV-153,API-INV-001,API-INV-008,API-INV-016,API-INV-017,API-INV-040 -->
### BALANCES-SCREEN — SCR-INV-003
- **Guard**: `PERM_INV_STOCK_BALANCES_VIEW`. The screen has no create, update or cancel action for any role (AC-INV-153).
- **Facade**: BALANCES-FACADE.
- **Cross-module**:
  - Variant filter → UXD-INV-001.
  - Item filter and Item name column → UXD-INV-002.
  - Measure unit → UXD-INV-003.
  - Warehouse label → read from the row (`warehouseCode`, `warehouseNameAr`, `warehouseNameEn`), for every role (ADR-INV-036).
- **Composition**: container page. Availability per warehouse → inline (`AvailabilitySummary` above the result when one Variant is filtered). No drawer. Submits: none.
- **Saves**: none (read-only inquiry).
- **Components**:
  - `BalanceSearchForm`: the Variant picker (UXD-INV-001), the Item picker (UXD-INV-002), a barcode field, the warehouse select (hidden while one; offered with warehouse VIEW), the lot select (lotExpiry on and lot VIEW), and "non-zero only".
  - `BalanceResultTable`: the warehouse column shows the row's warehouse code and name, and hides while one warehouse is active (AC-INV-015, AC-INV-044). The lot, expiry and `Expired` badge columns show only with lotExpiry on (AC-INV-129). The measure and unit columns show only with dualQuantity on (AC-INV-135, AC-INV-151). There is no cost column. **Reserved and available are per warehouse, not per lot** (gate-analysis revise 5, G6): their headers read «محجوز في المستودع» / "Reserved in warehouse" and «متاح في المستودع» / "Available in warehouse"; the two cells are filled only on the first row of each Variant × warehouse pair on the page (`pairFirstFl` — the rows arrive ordered by Variant, warehouse, expiry, so a pair is contiguous) and show «—» on its other lot rows, so a reader who adds the columns never counts a pair twice; on-hand stays per row. The availability summary carries the same pair figures once per warehouse.
  - Row link "Moves" goes to `/inventory/stock-moves?variantPk&warehousePk[&lotPk]`.
- **Routes**: incoming `?warehousePk` or `?lotPk` seeds the filter.
<!-- SUB:F4-SCR-INV-003:END -->
