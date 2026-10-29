<!-- source: PHASE:F4 / SUB:F4-SCR-PTY-001 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-007, AC-PTY-010, AC-PTY-013, AC-PTY-055, AC-PTY-057, AC-PTY-078, AC-PTY-082, API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-026, API-PTY-029, REQ-PTY-001, REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012, REQ-PTY-013, REQ-PTY-014, REQ-PTY-015, REQ-PTY-023, REQ-PTY-024, REQ-PTY-025, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-039, REQ-PTY-040, REQ-PTY-047, REQ-PTY-049, REQ-PTY-070, REQ-PTY-071, REQ-PTY-073, REQ-PTY-110, SCR-PTY-001 -->
<!-- SUB:F4-SCR-PTY-001:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-049,REQ-PTY-070,REQ-PTY-071,REQ-PTY-073,REQ-PTY-110,AC-PTY-007,AC-PTY-010,AC-PTY-013,AC-PTY-055,AC-PTY-057,AC-PTY-078,AC-PTY-082,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-026,API-PTY-029,SCR-PTY-001 -->
### CUSTOMER-SCREEN — SCR-PTY-001            traces=REQ-PTY-004,REQ-PTY-008,REQ-PTY-012,REQ-PTY-013,REQ-PTY-030,API-PTY-001,API-PTY-003,API-PTY-026,API-PTY-029
Chunk        : `pty-customers`
Routes       :
| Route | Element | Guard |
|---|---|---|
| `/pty/customers` | `CustomerSearchPage` | PERM_PTY_CUSTOMER_VIEW |
| `/pty/customers/new` | `CustomerEntryLayout` (mode new) | PERM_PTY_CUSTOMER_CREATE |
| `/pty/customers/new/addresses/new` · `/pty/customers/new/addresses/:addressKey` | `AddressLineDrawer` (child of the layout) | PERM_PTY_CUSTOMER_UPDATE |
| `/pty/customers/:customerId` | `CustomerEntryLayout` (mode edit; read-only without UPDATE) | PERM_PTY_CUSTOMER_VIEW |
| `/pty/customers/:customerId/addresses/new` | `AddressLineDrawer` | PERM_PTY_CUSTOMER_UPDATE |
| `/pty/customers/:customerId/addresses/:addressKey` | `AddressLineDrawer` (read-only without UPDATE) | PERM_PTY_CUSTOMER_VIEW |
Guard        : as the table; no VIEW → the unauthorized page
Facade       : CUSTOMER-FACADE (F2) · pages do not call queries directly
Cross-module : none
Composition  : `container page`. `CustomerEntryLayout` holds the form provider and renders, as siblings, `CustomerEntryView` (header group, Advanced group, read-only credit group, record footer, the inline `AddressPane`) and the drawer `<Outlet/>`. `AddressPane` is inline beneath the header: one row per line (type, label, line 1, city, default mark) with Add / open / Remove / Make default — all edit the draft only. `AddressLineDrawer` is the one-line drawer: opened from the route, returns the line to the form state, no save. `CustomerQuickRegister` (exported from `src/modules/pty/public`) is the Quick register sub-view: props `{ onRegistered(result: QuickRegisterResult), onCancel() }`; the host owns its container and open state (ADR-PTY-034); it has no address pane.
Saves        : ONE — `Save` «حفظ» runs CUSTOMER-SAVE: record call first, ADDRESSES-SYNC only after it succeeded, skipped when the address set is unchanged (F2). Commands (Deactivate, Reactivate, Make supplier) are separate confirmed calls, disabled while the form is dirty.
Navigation   : row click → `/pty/customers/:customerId` · New customer → `/pty/customers/new` · "Edit credit" «تعديل الائتمان» (flag on, PERM_PTY_CUSTOMER_CREDIT_VIEW) → `/pty/customer-credits/:customerId` · after Make supplier → `/pty/suppliers/:supplierPk` · RULE-PTY-002 action "Open that party" «فتح ذلك الطرف» → `/pty/customers/:customerPk` of the Customer holding the email, else `/pty/suppliers/:supplierPk` of the Supplier holding it (EMAIL-HOLDER-QUERY, ADR-PTY-043) · after a first save → replace with `/pty/customers/:customerId`.
<!-- SUB:F4-SCR-PTY-001:END -->
