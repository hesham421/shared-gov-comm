<!-- source: PHASE:F1 / SUB:F1-SCR-INV-001 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008, API-INV-009, REQ-INV-003, REQ-INV-004, REQ-INV-005, REQ-INV-006, REQ-INV-007, REQ-INV-008, REQ-INV-009, REQ-INV-015, REQ-INV-029, REQ-INV-030, REQ-INV-031, SCR-INV-001 -->
<!-- SUB:F1-SCR-INV-001:START traces=SCR-INV-001,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-015,REQ-INV-029,REQ-INV-030,REQ-INV-031,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009 -->
### F1 · SCR-INV-001 — Warehouses
- Schemas: `Warehouse`, `WarehouseCreateRequest`, `WarehouseUpdateRequest`, `WarehouseEnvelope`, `WarehousePageEnvelope`, `VersionedActionRequest`, `Branch` (labels), `InventoryContext`.
- `WarehouseFilter {code?, name?, branchPk?, active: 'ACTIVE_ONLY' | 'ALL', page, size}`. The default is `{active: 'ACTIVE_ONLY', page: 0, size: 20}`.
- `WarehouseFormState {nameAr, nameEn, branchPk | null, version?}`. `branchPk` is null for a new warehouse while the branch field is hidden. The server assigns the default branch (REQ-INV-031).
- `WarehouseEntryNav = 'new' | warehousePk`, which comes from the `entry` search param.
<!-- SUB:F1-SCR-INV-001:END -->
