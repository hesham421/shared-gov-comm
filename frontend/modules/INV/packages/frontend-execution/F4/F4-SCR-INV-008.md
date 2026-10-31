<!-- source: PHASE:F4 / SUB:F4-SCR-INV-008 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-121, AC-INV-122, AC-INV-129, AC-INV-130, AC-INV-131, AC-INV-150, API-INV-001, API-INV-008, API-INV-040, API-INV-041, API-INV-042, REQ-INV-108, REQ-INV-109, REQ-INV-114, REQ-INV-115, REQ-INV-116, REQ-INV-117, REQ-INV-134, SCR-INV-008, UXD-INV-008 -->
<!-- SUB:F4-SCR-INV-008:START traces=SCR-INV-008,UXD-INV-008,REQ-INV-108,REQ-INV-109,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-134,AC-INV-121,AC-INV-122,AC-INV-129,AC-INV-130,AC-INV-131,AC-INV-150,API-INV-001,API-INV-008,API-INV-040,API-INV-041,API-INV-042 -->
### LOTS-SCREEN — SCR-INV-008
- **Guard**: `PERM_INV_LOTS_VIEW`, with lotExpiry on (REQ-INV-134). Save expiry needs `_UPDATE` (Owner); for others the entry drawer is read-only.
- **Facade**: LOTS-FACADE.
- **Cross-module**: Variant filter and Variant label → UXD-INV-008.
- **Composition**: container drawer. `LotSearchPage`, with `LotEntryDrawer` as its sibling, opened by `?entry`. Fields → none.
- **Saves**: ONE (Save expiry → API-INV-042). Write off is navigation, not a save.
- **Components**:
  - `LotSearchForm` with a "Near expiry only" toggle (REQ-INV-116); the notification link opens `/inventory/lots?nearExpiryOnly=true` (REQ-INV-117).
  - `LotResultTable`, ordered by expiry: days left, plus an `Expired` badge «منتهية الصلاحية» / "Expired" (AC-INV-129).
  - Row actions:
    - "Write off", on expired rows with on-hand, goes to `/inventory/stock-adjustments/new?reason=EXPIRY_WRITE_OFF&lotPk&warehousePk`;
    - "Stock balances" goes to `/inventory/stock-balances?lotPk[&warehousePk]`.
  - `LotEntryDrawer`.
<!-- SUB:F4-SCR-INV-008:END -->
