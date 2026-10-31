<!-- source: PHASE:F4 / SUB:F4-SCR-INV-004 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-062, AC-INV-082, AC-INV-083, API-INV-001, API-INV-008, API-INV-018, API-INV-019, API-INV-040, REQ-INV-016, REQ-INV-055, REQ-INV-056, REQ-INV-075, REQ-INV-076, SCR-INV-004, UXD-INV-004 -->
<!-- SUB:F4-SCR-INV-004:START traces=SCR-INV-004,UXD-INV-004,REQ-INV-016,REQ-INV-055,REQ-INV-056,REQ-INV-075,REQ-INV-076,AC-INV-062,AC-INV-082,AC-INV-083,API-INV-001,API-INV-008,API-INV-018,API-INV-019,API-INV-040 -->
### MOVES-SCREEN — SCR-INV-004
- **Guard**: `PERM_INV_STOCK_MOVES_VIEW`.
- **Facade**: MOVES-FACADE.
- **Cross-module**: Variant filter → UXD-INV-004.
- **Composition**: container page. `MoveDetailDrawer` is a sibling opened by `?move`; it is read-only (→ none). Submits: none.
- **Saves**: none. Moves are never edited; the screen offers no edit or delete action (REQ-INV-055).
- **Components**:
  - `MoveSearchForm`.
  - `MoveResultTable`, newest first. The "Balance after move" column shows only while `showRunningBalance` (AC-INV-083). Document type labels come from INV_SOURCE_DOC_TYPE.
  - `MoveDetailDrawer`, which shows the source document type, identifier and number (AC-INV-062).
- **Routes**: incoming `?variantPk&warehousePk&lotPk`, `?sourceDocTypeCode&sourceDocRef` (one document exactly — from SCR-INV-005 and SCR-INV-007) or `?variantPk` seeds the filter.
<!-- SUB:F4-SCR-INV-004:END -->
