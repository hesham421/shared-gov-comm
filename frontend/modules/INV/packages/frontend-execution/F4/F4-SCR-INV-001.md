<!-- source: PHASE:F4 / SUB:F4-SCR-INV-001 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-006, AC-INV-010, AC-INV-012, AC-INV-034, AC-INV-035, AC-INV-036, API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008, API-INV-009, REQ-INV-003, REQ-INV-004, REQ-INV-005, REQ-INV-006, REQ-INV-007, REQ-INV-008, REQ-INV-009, REQ-INV-015, REQ-INV-029, REQ-INV-030, REQ-INV-031, SCR-INV-001 -->
<!-- SUB:F4-SCR-INV-001:START traces=SCR-INV-001,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-015,REQ-INV-029,REQ-INV-030,REQ-INV-031,AC-INV-006,AC-INV-010,AC-INV-012,AC-INV-034,AC-INV-035,AC-INV-036,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009 -->
### WAREHOUSES-SCREEN — SCR-INV-001
- **Guard**: `PERM_INV_WAREHOUSES_VIEW`. The "New" action needs `_CREATE`. Save on an existing record, Reactivate and Set as default need `_UPDATE`; Deactivate needs `_DELETE`.
- **Facade**: WAREHOUSES-FACADE.
- **Cross-module**: none.
- **Composition**: container drawer. `WarehouseSearchPage`, with `WarehouseEntryDrawer` as its sibling, opened by `?entry`. Fields → none; branch selector → inline (one select, hidden while one active branch).
- **Saves**: ONE (Save → API-INV-003 or API-INV-004). Row actions are separate confirmed actions in the row menu: Deactivate API-INV-005, Reactivate API-INV-006, Set as default API-INV-007.
- **Components**: `WarehouseSearchForm`; `WarehouseResultTable`, where the branch column hides while one branch is active, a default badge and an active badge are shown, and a row link "Stock balances" goes to `/inventory/stock-balances?warehousePk=<id>`; `WarehouseEntryDrawer`.
- **Routes**: incoming `?branchPk=<id>` seeds the filter.
<!-- SUB:F4-SCR-INV-001:END -->
