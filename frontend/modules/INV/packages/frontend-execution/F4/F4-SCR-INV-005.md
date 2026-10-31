<!-- source: PHASE:F4 / SUB:F4-SCR-INV-005 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-014, AC-INV-017, AC-INV-085, AC-INV-086, AC-INV-087, AC-INV-089, AC-INV-092, AC-INV-094, AC-INV-095, API-INV-001, API-INV-008, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-040, API-INV-044, REQ-INV-011, REQ-INV-014, REQ-INV-062, REQ-INV-078, REQ-INV-079, REQ-INV-080, REQ-INV-081, REQ-INV-082, REQ-INV-083, REQ-INV-084, REQ-INV-085, REQ-INV-086, REQ-INV-122, SCR-INV-005, UXD-INV-005 -->
<!-- SUB:F4-SCR-INV-005:START traces=SCR-INV-005,UXD-INV-005,REQ-INV-011,REQ-INV-014,REQ-INV-062,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,REQ-INV-122,AC-INV-014,AC-INV-017,AC-INV-085,AC-INV-086,AC-INV-087,AC-INV-089,AC-INV-092,AC-INV-094,AC-INV-095,API-INV-001,API-INV-008,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-040,API-INV-044 -->
### TRANSFERS-SCREEN — SCR-INV-005
- **Guard**: `PERM_INV_TRANSFERS_VIEW`. Below two active warehouses the menu hides the entry and the "New" action is hidden (REQ-INV-011, AC-INV-014); the search, `/:id` of a posted or cancelled transfer and its "Stock moves" link stay reachable by deep link, so a tenant that deactivated its second warehouse still reads its history (SRS SCR-REQ-INV-005 B1); only `/new` renders the RULE-INV-016 message with a link to SCR-INV-001 (REQ-INV-014). `/new` needs `_CREATE` and two or more active warehouses. Save on `/:id` and Post need `_UPDATE`; Cancel needs `_DELETE`.
- **Facade**: TRANSFERS-FACADE.
- **Cross-module**: line Variant → UXD-INV-005.
- **Composition**: container page. The header (from / to warehouse, date, Advanced note) is inline. Lines → inline in a `TransferLinesPane` beneath the header. A line is added or changed in `TransferLineDrawer`, a sibling opened by `?line`. Its confirm returns the line to the form state and saves nothing; cancel discards the drawer's draft. Variant picker → inline: one combobox plus a barcode field inside the line drawer.
- **Saves**: ONE (Save → API-INV-022 on `/new`, API-INV-023 on `/:id`), sending header and lines in one call. Post (API-INV-024) and Cancel (API-INV-025) are document transitions offered only on a saved, clean DRAFT, so no action owns two calls. POSTED and CANCELLED render read-only (AC-INV-094).
- **Components**:
  - `TransferSearchForm`; `TransferResultTable`, newest first (AC-INV-095).
  - `TransferHeader`, `TransferLinesPane`, `TransferLineDrawer`. The lot select appears when the Item is lot-tracked and lotExpiry is on; measure appears when the Item is dual-quantity and dualQuantity is on.
  - `TransferActions` (Save · Post · Cancel).
  - On a POSTED entry, the link "Stock moves" goes to `/inventory/stock-moves?sourceDocTypeCode=INV_TRANSFER&sourceDocRef=<transferPk>`.
<!-- SUB:F4-SCR-INV-005:END -->
