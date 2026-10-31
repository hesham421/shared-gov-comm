<!-- source: PHASE:F4 / SUB:F4-SCR-INV-006 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-096, AC-INV-097, AC-INV-099, AC-INV-100, AC-INV-101, AC-INV-102, AC-INV-104, AC-INV-105, API-INV-001, API-INV-008, API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-040, API-INV-044, REQ-INV-013, REQ-INV-017, REQ-INV-018, REQ-INV-087, REQ-INV-088, REQ-INV-089, REQ-INV-090, REQ-INV-091, REQ-INV-092, REQ-INV-093, REQ-INV-094, REQ-INV-095, REQ-INV-096, REQ-INV-122, SCR-INV-006, UXD-INV-006 -->
<!-- SUB:F4-SCR-INV-006:START traces=SCR-INV-006,UXD-INV-006,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,REQ-INV-122,AC-INV-096,AC-INV-097,AC-INV-099,AC-INV-100,AC-INV-101,AC-INV-102,AC-INV-104,AC-INV-105,API-INV-001,API-INV-008,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-040,API-INV-044 -->
### COUNTS-SCREEN — SCR-INV-006
- **Guard**: `PERM_INV_COUNTS_VIEW`. "Start count" and `/new` need `_CREATE`. Scan, line change and Post need `_UPDATE`; Cancel needs `_DELETE`.
- **Facade**: COUNTS-FACADE.
- **Cross-module**: count line Variant → UXD-INV-006.
- **Composition**: container page. The header is inline and read-only after start. Lines → inline: a `CountScanBar` and stacked `CountLineCard`s beneath it. A line is changed or removed in `CountLineDrawer`, a sibling opened by `?line`. Variant picker → inline: it replaces the barcode field after an unknown barcode (AC-INV-101).
- **Saves**: ONE per action (ADR-INV-034). Each scan is one API-INV-029 call, and each drawer confirm is one API-INV-030 or API-INV-031 call. No action owns two calls. Start (API-INV-028), Post (API-INV-032) and Cancel (API-INV-033) are document transitions. POSTED and CANCELLED render read-only, with systemQty and differenceQty — and, on a dual-quantity line, the system measure (`systemMeasureQty`, ADR-INV-054) — and a link to the resulting adjustment `/inventory/stock-adjustments/<adjustmentPk>` (AC-INV-102).
- **Layout (REQ-INV-090, AC-INV-100)**:
  - single column, `max-width: 100%`, no table and no horizontal scroll at 360 CSS px;
  - touch targets ≥ 44 px;
  - the barcode field is focused on load and after each scan;
  - each scan shows its result (Variant name and new counted total) as a non-blocking toast.
- **Components**:
  - `CountSearchForm`, `CountResultTable` (stacked cards below 600 px), newest first (AC-INV-105).
  - `CountStartForm`, shown on `/new` only when two or more warehouses are active; while one is active, "Start count" calls API-INV-028 directly (AC-INV-096).
  - `CountScanBar`, `CountLineCard`, `CountLineDrawer`, `CountActions` (Post · Cancel).
<!-- SUB:F4-SCR-INV-006:END -->
