<!-- source: PHASE:F4 / SUB:F4-SCR-CAT-006 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-045, API-CAT-051, API-CAT-052, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023, REQ-CAT-092, REQ-CAT-093, REQ-CAT-094, REQ-CAT-095, REQ-CAT-098, REQ-CAT-120, REQ-CAT-121, REQ-CAT-122, REQ-CAT-123, REQ-CAT-124, REQ-CAT-134, SCR-CAT-006, UXD-CAT-001 -->
<!-- SUB:F4-SCR-CAT-006:START traces=API-CAT-052,REQ-CAT-134,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-045,API-CAT-051,UXD-CAT-001,SCR-CAT-006 -->
### F4 · SCR-CAT-006 — Catalogue settings

#### CATALOGUE-SETTINGS-SCREEN — SCR-CAT-006            traces=REQ-CAT-095,REQ-CAT-021,UXD-CAT-001,API-CAT-045,API-CAT-051
- **Guard**: `/cat/settings` needs PERM_CAT_SETTINGS_VIEW. "New layout" and "New unit code" need CREATE, Save on an existing entry needs UPDATE, and Deactivate needs DELETE.
- **Facade**: SETTINGS-FACADE only.
- **Cross-module**: embeddedValueTypeCode, key (CAT_UOM) and einvoiceUnitCode → UXD-CAT-001.
- **Components**: `CatalogueSettingsPage` (the two lists and the turn-off check panel), `ScaleLayoutDrawer` (`?layout=`), `UnitEinvoiceCodeDrawer` (`?unitCode=`).
- **Composition**: container drawer. Each drawer edits one record with no secondary detail. The turn-off check is a read panel on the page with no save.
- **Saves**: ONE per drawer (`LAYOUT-SAVE`, `UNIT-CODE-SAVE`). Deactivate is a record command in its drawer.
- **Navigation out**: a dependent Item of the check → `/cat/items/:itemPk`.
<!-- SUB:F4-SCR-CAT-006:END -->
