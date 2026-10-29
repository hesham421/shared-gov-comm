<!-- source: PHASE:F4 / SUB:F4-SCR-CAT-004 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-048, API-CAT-049, REQ-CAT-036, REQ-CAT-037, REQ-CAT-038, REQ-CAT-039, REQ-CAT-040, REQ-CAT-041, REQ-CAT-139, SCR-CAT-004 -->
<!-- SUB:F4-SCR-CAT-004:START traces=REQ-CAT-139,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,API-CAT-048,API-CAT-049,SCR-CAT-004 -->
### F4 · SCR-CAT-004 — Item import

#### ITEM-IMPORT-SCREEN — SCR-CAT-004            traces=REQ-CAT-036,REQ-CAT-039,API-CAT-048,API-CAT-049
- **Guard**: `/cat/item-import` needs PERM_CAT_ITEM_IMPORT_VIEW; the "Import" action needs PERM_CAT_ITEM_IMPORT_CREATE.
- **Facade**: IMPORT-FACADE only.
- **Cross-module**: none.
- **Components**: `ItemImportPage` (the three-step guide, "Download import sheet", file input, "Import", result panel with counts and the rejected-rows table). It is also exported as `ItemImportStep` for the setup wizard, with the same component and guard.
- **Composition**: container page. The import file has no secondary detail. The rejected rows are inline under the counts, read-only, in the page's own scroll.
- **Saves**: ONE (`IMPORT-SUBMIT`). Downloading the sheet is a read.
- **Navigation out**: "Open Items" → `/cat/items`.
<!-- SUB:F4-SCR-CAT-004:END -->
