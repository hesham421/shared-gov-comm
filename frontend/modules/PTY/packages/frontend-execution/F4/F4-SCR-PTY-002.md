<!-- source: PHASE:F4 / SUB:F4-SCR-PTY-002 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-011, AC-PTY-021, AC-PTY-029, AC-PTY-031, API-PTY-001, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-028, API-PTY-029, REQ-PTY-006, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-016, REQ-PTY-017, REQ-PTY-018, REQ-PTY-020, REQ-PTY-021, REQ-PTY-022, REQ-PTY-023, REQ-PTY-024, REQ-PTY-026, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-110, SCR-PTY-002 -->
<!-- SUB:F4-SCR-PTY-002:START traces=REQ-PTY-006,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-110,AC-PTY-011,AC-PTY-021,AC-PTY-029,AC-PTY-031,API-PTY-001,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-028,API-PTY-029,SCR-PTY-002 -->
### SUPPLIER-SCREEN — SCR-PTY-002            traces=REQ-PTY-008,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-030,API-PTY-006,API-PTY-008,API-PTY-028,API-PTY-029
Chunk        : `pty-suppliers`
Routes       :
| Route | Element | Guard |
|---|---|---|
| `/pty/suppliers` | `SupplierSearchPage` | PERM_PTY_SUPPLIER_VIEW |
| `/pty/suppliers/new` | `SupplierEntryLayout` (mode new) | PERM_PTY_SUPPLIER_CREATE |
| `/pty/suppliers/new/addresses/new` · `/pty/suppliers/new/addresses/:addressKey` | `AddressLineDrawer` | PERM_PTY_SUPPLIER_UPDATE |
| `/pty/suppliers/:supplierId` | `SupplierEntryLayout` (mode edit; read-only without UPDATE — Warehouse) | PERM_PTY_SUPPLIER_VIEW |
| `/pty/suppliers/:supplierId/addresses/new` | `AddressLineDrawer` | PERM_PTY_SUPPLIER_UPDATE |
| `/pty/suppliers/:supplierId/addresses/:addressKey` | `AddressLineDrawer` (read-only without UPDATE) | PERM_PTY_SUPPLIER_VIEW |
Guard        : as the table; no VIEW → the unauthorized page
Facade       : SUPPLIER-FACADE (F2)
Cross-module : none
Composition  : `container page`; same structure as SCR-PTY-001 — `SupplierEntryLayout` renders `SupplierEntryView` (with the inline `AddressPane`) and the drawer `<Outlet/>` as siblings; `AddressLineDrawer` and `AddressPane` are the shared components. `SupplierQuickRegister` is exported from `src/modules/pty/public` for purchase documents (props as `CustomerQuickRegister`).
Saves        : ONE — `Save` runs SUPPLIER-SAVE (record call, then ADDRESSES-SYNC only after success and only when the set changed). Commands (Deactivate, Reactivate, Make customer) are separate confirmed calls, disabled while dirty.
Navigation   : row click → `/pty/suppliers/:supplierId` · New supplier → `/pty/suppliers/new` · after Make customer → `/pty/customers/:customerPk` · RULE-PTY-002 action "Open that party" «فتح ذلك الطرف» → as SCR-PTY-001 (EMAIL-HOLDER-QUERY, ADR-PTY-043) · after a first save → replace with `/pty/suppliers/:supplierId`.
<!-- SUB:F4-SCR-PTY-002:END -->
