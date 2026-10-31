<!-- source: PHASE:F4 / SUB:F4-SCR-INV-007 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-013, AC-INV-107, AC-INV-108, AC-INV-110, AC-INV-112, AC-INV-114, AC-INV-115, AC-INV-116, AC-INV-128, AC-INV-150, API-INV-001, API-INV-008, API-INV-016, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-040, API-INV-041, API-INV-044, REQ-INV-010, REQ-INV-013, REQ-INV-017, REQ-INV-018, REQ-INV-064, REQ-INV-067, REQ-INV-097, REQ-INV-098, REQ-INV-099, REQ-INV-100, REQ-INV-101, REQ-INV-102, REQ-INV-103, REQ-INV-104, REQ-INV-105, REQ-INV-106, REQ-INV-107, REQ-INV-114, REQ-INV-123, SCR-INV-007, UXD-INV-007 -->
<!-- SUB:F4-SCR-INV-007:START traces=SCR-INV-007,UXD-INV-007,REQ-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-067,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-114,REQ-INV-123,AC-INV-013,AC-INV-107,AC-INV-108,AC-INV-110,AC-INV-112,AC-INV-114,AC-INV-115,AC-INV-116,AC-INV-128,AC-INV-150,API-INV-001,API-INV-008,API-INV-016,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-044 -->
### ADJUSTMENTS-SCREEN — SCR-INV-007
- **Guard**: `PERM_INV_ADJUSTMENTS_VIEW`. `/new` needs `_CREATE`. Save on `/:id` and Post need `_UPDATE`; Cancel needs `_DELETE`.
- **Facade**: ADJUSTMENTS-FACADE.
- **Cross-module**: line Variant → UXD-INV-007.
- **Composition**: container page. The header (reason, warehouse hidden while one, Advanced note) is inline. Lines → inline in `AdjustmentLinesPane` beneath the header. A line is added or changed in `AdjustmentLineDrawer`, a sibling opened by `?line`, which returns the line to the form state. Variant picker → inline inside the line drawer.
- **Saves**: ONE (Save → API-INV-036 on `/new`, API-INV-037 on `/:id`). Post (API-INV-038) and Cancel (API-INV-039) are offered only on a saved, clean DRAFT. POSTED and CANCELLED render read-only (AC-INV-115), with line values and the total.
- **Components**:
  - `AdjustmentSearchForm`; `AdjustmentResultTable`, newest first (AC-INV-116).
  - `AdjustmentHeader`, `AdjustmentLinesPane`, `AdjustmentLineDrawer`. Lot number and expiry columns are absent with lotExpiry off (AC-INV-150). The unit cost is behind "Advanced" on increase lines and is opened by a cost error.
  - `AdjustmentActions` (Save · Post · Cancel).
  - On a POSTED entry, the link "Stock moves" goes to `/inventory/stock-moves?sourceDocTypeCode=INV_ADJUSTMENT&sourceDocRef=<stockAdjustmentPk>`.
- **Routes**: `/new?reason=EXPIRY_WRITE_OFF&lotPk&warehousePk` seeds the reason and one decrease line from WRITE-OFF-LOT-QUERY and WRITE-OFF-BALANCE-QUERY (AC-INV-128). The seed fills form state only, and nothing is saved until Save.
<!-- SUB:F4-SCR-INV-007:END -->
