<!-- source: PHASE:F4 / SUB:F4-SCR-INV-009 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-138, AC-INV-140, AC-INV-142, AC-INV-143, AC-INV-145, API-INV-008, API-INV-043, API-INV-044, API-INV-045, REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-128, REQ-INV-129, REQ-INV-134, REQ-INV-135, SCR-INV-009, UXD-INV-009, UXD-INV-010 -->
<!-- SUB:F4-SCR-INV-009:START traces=SCR-INV-009,UXD-INV-009,UXD-INV-010,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,AC-INV-138,AC-INV-140,AC-INV-142,AC-INV-143,AC-INV-145,API-INV-008,API-INV-043,API-INV-044,API-INV-045 -->
### ITEM-SETTINGS-SCREEN — SCR-INV-009
- **Guard**: `PERM_INV_ITEM_STOCK_SETTINGS_VIEW`, with lotExpiry or dualQuantity on. "New" needs `_CREATE`; Save on an existing record needs `_UPDATE`.
- **Facade**: ITEM-SETTINGS-FACADE.
- **Cross-module**: Item pick → UXD-INV-009; measure unit → UXD-INV-010.
- **Composition**: container drawer. `ItemSettingsSearchPage`, with `ItemSettingsEntryDrawer` as its sibling, opened by `?entry`. Fields → none. Item picker → inline (one combobox, new records only).
- **Saves**: ONE (Save → API-INV-045).
- **Components**:
  - `ItemSettingsSearchForm`, `ItemSettingsResultTable`.
  - `ItemSettingsEntryDrawer`. "Tracked by lot" shows only with lotExpiry on (REQ-INV-134). "Dual quantity" and "Measure unit" show only with dualQuantity on (REQ-INV-135). The measure unit is required when dual.
<!-- SUB:F4-SCR-INV-009:END -->
