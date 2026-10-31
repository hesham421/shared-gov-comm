<!-- source: PHASE:F4 / SUB:F4-SCR-INV-002 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-028, AC-INV-032, AC-INV-033, AC-INV-037, API-INV-008, API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015, REQ-INV-022, REQ-INV-023, REQ-INV-024, REQ-INV-025, REQ-INV-026, REQ-INV-027, REQ-INV-028, REQ-INV-032, SCR-INV-002 -->
<!-- SUB:F4-SCR-INV-002:START traces=SCR-INV-002,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-032,AC-INV-028,AC-INV-032,AC-INV-033,AC-INV-037,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015 -->
### BRANCHES-SCREEN — SCR-INV-002
- **Guard**: `PERM_INV_BRANCHES_VIEW`; actions gated by `_CREATE` / `_UPDATE` / `_DELETE` as on SCR-INV-001.
- **Facade**: BRANCHES-FACADE.
- **Cross-module**: none.
- **Composition**: container drawer. `BranchSearchPage`, with `BranchEntryDrawer` as its sibling, opened by `?entry`. Fields → none.
- **Saves**: ONE (Save → API-INV-011 or API-INV-012). Row actions: Deactivate API-INV-013, Reactivate API-INV-014, Set as default API-INV-015.
- **Components**: `BranchSearchForm`; `BranchResultTable`, with a row link "Warehouses" to `/inventory/warehouses?branchPk=<id>`; `BranchEntryDrawer`.
<!-- SUB:F4-SCR-INV-002:END -->
