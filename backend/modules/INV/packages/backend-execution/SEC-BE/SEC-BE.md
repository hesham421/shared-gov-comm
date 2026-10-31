<!-- source: PHASE:SEC-BE -->
<!-- traces: DBF-INV-001, DBF-INV-014, REQ-INV-136, REQ-INV-137, REQ-INV-138 -->
<!-- PHASE:SEC-BE:START traces=REQ-INV-136,REQ-INV-137,REQ-INV-138,DBF-INV-001,DBF-INV-014 -->
## SEC-BE — security (backend half)

Security model: one `SEC_PAGES` row per composite screen, registered by
`com.commerce.inv.permission.InvPermissions` (PermissionContributor, permission module `INV`); the security
module derives `PERM_<PAGE_CODE>_<ACTION>` for VIEW / CREATE / UPDATE / DELETE, VIEW being the gateway. Every
endpoint verifies its permission before any processing, and so does every public method of the REST-facing
service it calls: both carry
`@PreAuthorize("hasAuthority(T(com.commerce.inv.permission.InvPermissions).PERM_…)")` with the permission its
API block's Security line names (also listed in registry-exec-be "Endpoints"), INV's own constants only
(checklist C13). API-INV-008 carries
`hasAnyAuthority(…)` over the twelve VIEW constants, and so does API-INV-052 (the INV lookup keys every INV screen reads); API-INV-045 carries `hasAnyAuthority(…_CREATE, …_UPDATE)` and
the service requires CREATE when the record is absent and UPDATE when it exists (ADR-INV-029). A refusal answers
INV-403 through `LocalizedException → {code, messageAr, messageEn}` (REQ-INV-138). No service checks a role
name.

**In-process surface (ADR-INV-042).** `InventoryApi` (CON-INV-006 … 019) delegates to the engines of
`com.commerce.inv.engine`, which carry no permission check and have no REST path; it never calls a REST-facing
service. Every one of its fourteen methods therefore bypasses the INV permission check, because the calling
module authorises its own caller (SRS access summary; a POS cashier holds only PERM_INV_STOCK_BALANCES_VIEW).
It follows CORE-GAP-in-process-caller-authority when the core answers.

| Page code | Name ar / en | Parent | Screen |
|---|---|---|---|
| INV_WAREHOUSES | المستودعات / Warehouses | Inventory → Setup | SCR-REQ-INV-001 |
| INV_BRANCHES | الفروع / Branches | Inventory → Setup | SCR-REQ-INV-002 |
| INV_STOCK_BALANCES | أرصدة المخزون / Stock balances | Inventory | SCR-REQ-INV-003 |
| INV_STOCK_MOVES | سجل حركات المخزون / Stock move history | Inventory | SCR-REQ-INV-004 |
| INV_TRANSFERS | التحويلات بين المستودعات / Transfers | Inventory | SCR-REQ-INV-005 |
| INV_COUNTS | جرد المخزون / Stock counts | Inventory | SCR-REQ-INV-006 |
| INV_ADJUSTMENTS | تسويات المخزون / Stock adjustments | Inventory | SCR-REQ-INV-007 |
| INV_LOTS | التشغيلات وقرب انتهاء الصلاحية / Lots and near expiry | Inventory | SCR-REQ-INV-008 |
| INV_ITEM_STOCK_SETTINGS | إعدادات مخزون الصنف / Item stock settings | Inventory → Setup | SCR-REQ-INV-009 |
| INV_OPENING_STOCK | المخزون الافتتاحي / Opening stock | Inventory → Setup | SCR-REQ-INV-010 |
| INV_SETTINGS | إعدادات المخزون / Inventory settings | Inventory → Setup | SCR-REQ-INV-011 |
| INV_STOCK_VALUATION | تقييم المخزون / Stock valuation | Inventory | SCR-REQ-INV-012 |

```yaml name=permission-matrix
rows:
  - screen: SCR-REQ-INV-001 Warehouses (INV_WAREHOUSES)
    entity: ENT-INV-001
    api: [API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008]
    actions:
      VIEW: {granted: true, permission: PERM_INV_WAREHOUSES_VIEW}
      CREATE: {granted: true, permission: PERM_INV_WAREHOUSES_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_WAREHOUSES_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_WAREHOUSES_DELETE}
  - screen: SCR-REQ-INV-002 Branches (INV_BRANCHES)
    entity: ENT-INV-002
    api: [API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015]
    actions:
      VIEW: {granted: true, permission: PERM_INV_BRANCHES_VIEW}
      CREATE: {granted: true, permission: PERM_INV_BRANCHES_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_BRANCHES_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_BRANCHES_DELETE}
  - screen: SCR-REQ-INV-003 Stock balances (INV_STOCK_BALANCES)
    entity: ENT-INV-003
    api: [API-INV-016, API-INV-017, API-INV-008]
    actions:
      VIEW: {granted: true, permission: PERM_INV_STOCK_BALANCES_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-004 Stock move history (INV_STOCK_MOVES)
    entity: ENT-INV-004
    api: [API-INV-018, API-INV-019, API-INV-052]
    actions:
      VIEW: {granted: true, permission: PERM_INV_STOCK_MOVES_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-005 Transfers (INV_TRANSFERS)
    entity: ENT-INV-006
    api: [API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-052]
    actions:
      VIEW: {granted: true, permission: PERM_INV_TRANSFERS_VIEW}
      CREATE: {granted: true, permission: PERM_INV_TRANSFERS_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_TRANSFERS_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_TRANSFERS_DELETE}
  - screen: SCR-REQ-INV-006 Stock counts (INV_COUNTS)
    entity: ENT-INV-008
    api: [API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-052]
    actions:
      VIEW: {granted: true, permission: PERM_INV_COUNTS_VIEW}
      CREATE: {granted: true, permission: PERM_INV_COUNTS_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_COUNTS_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_COUNTS_DELETE}
  - screen: SCR-REQ-INV-007 Stock adjustments (INV_ADJUSTMENTS)
    entity: ENT-INV-010
    api: [API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-052]
    actions:
      VIEW: {granted: true, permission: PERM_INV_ADJUSTMENTS_VIEW}
      CREATE: {granted: true, permission: PERM_INV_ADJUSTMENTS_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_ADJUSTMENTS_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_ADJUSTMENTS_DELETE}
  - screen: SCR-REQ-INV-008 Lots and near expiry (INV_LOTS)
    entity: ENT-INV-012
    api: [API-INV-040, API-INV-041, API-INV-042]
    actions:
      VIEW: {granted: true, permission: PERM_INV_LOTS_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: true, permission: PERM_INV_LOTS_UPDATE}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-009 Item stock settings (INV_ITEM_STOCK_SETTINGS)
    entity: ENT-INV-014
    api: [API-INV-043, API-INV-044, API-INV-045]
    actions:
      VIEW: {granted: true, permission: PERM_INV_ITEM_STOCK_SETTINGS_VIEW}
      CREATE: {granted: true, permission: PERM_INV_ITEM_STOCK_SETTINGS_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_ITEM_STOCK_SETTINGS_UPDATE}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-010 Opening stock (INV_OPENING_STOCK)
    entity: ENT-INV-010
    api: [API-INV-046, API-INV-047]
    actions:
      VIEW: {granted: true, permission: PERM_INV_OPENING_STOCK_VIEW}
      CREATE: {granted: true, permission: PERM_INV_OPENING_STOCK_CREATE}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-011 Inventory settings (INV_SETTINGS)
    entity: ENT-INV-004
    api: [API-INV-048, API-INV-049]
    actions:
      VIEW: {granted: true, permission: PERM_INV_SETTINGS_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: true, permission: PERM_INV_SETTINGS_UPDATE}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-012 Stock valuation (INV_STOCK_VALUATION)
    entity: ENT-INV-013
    api: [API-INV-050, API-INV-051]
    actions:
      VIEW: {granted: true, permission: PERM_INV_STOCK_VALUATION_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: false}
      DELETE: {granted: false}
```

SCR-REQ-INV-011 has no entity of its own (given cu settings); its row names ENT-INV-004, whose moves the
negative-stock setting governs (REQ-INV-065). Role-preset grants (SRS B4 / access summary — REQ-INV-136, 137):

| Role preset | Permissions granted |
|---|---|
| Owner | all 32 permissions below |
| Warehouse | PERM_INV_WAREHOUSES_VIEW, PERM_INV_BRANCHES_VIEW, PERM_INV_STOCK_BALANCES_VIEW, PERM_INV_STOCK_MOVES_VIEW, PERM_INV_TRANSFERS_VIEW / _CREATE / _UPDATE / _DELETE, PERM_INV_COUNTS_VIEW / _CREATE / _UPDATE / _DELETE, PERM_INV_ADJUSTMENTS_VIEW / _CREATE / _UPDATE / _DELETE, PERM_INV_LOTS_VIEW, PERM_INV_ITEM_STOCK_SETTINGS_VIEW |
| Seller/Cashier | PERM_INV_STOCK_BALANCES_VIEW |

**Bootstrap data** — the rows that must exist before any endpoint can succeed, each with who produces it:

```yaml name=bootstrap
rows:
  - {kind: lookup key, name: "INV_ADJUSTMENT_REASON", source: "db-script BLOCK 8a/8b (V1002__inv_inventory.sql) into the given MDL_LOOKUP_TYPE / MDL_LOOKUP_VALUE for TENANT_ID 1; copied to each new tenant by the given mdl provisioning", note: "5 values, open; COUNT_DIFFERENCE and OPENING_STOCK are written by the system without the tenant-active check (ADR-INV-047); served to INV screens by InvLookups (API-INV-052)"}
  - {kind: lookup key, name: "INV_MOVE_TYPE", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "8 values, closed"}
  - {kind: lookup key, name: "INV_DOC_STATUS", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "3 values, closed"}
  - {kind: lookup key, name: "INV_RESERVATION_STATUS", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "3 values, closed"}
  - {kind: lookup key, name: "INV_SOURCE_DOC_TYPE", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "10 values, open — calling modules add their types as data"}
  - {kind: lookup key, name: "INV_CHANGE_KIND", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "5 values, event field"}
  - {kind: number series, name: "INV_WAREHOUSE", source: "db-script BLOCK 8c into the given CORE_NUMBER_SERIES; given sequence provisioning per tenant", note: "WH-{SEQ:4}"}
  - {kind: number series, name: "INV_BRANCH", source: "db-script BLOCK 8c; given sequence provisioning per tenant", note: "BR-{SEQ:4}"}
  - {kind: number series, name: "INV_TRANSFER", source: "db-script BLOCK 8c; given sequence provisioning per tenant", note: "TR-{SEQ:4}"}
  - {kind: number series, name: "INV_COUNT", source: "db-script BLOCK 8c; given sequence provisioning per tenant", note: "CNT-{SEQ:4}"}
  - {kind: number series, name: "INV_ADJUSTMENT", source: "db-script BLOCK 8c; given sequence provisioning per tenant", note: "ADJ-{SEQ:4}"}
  - {kind: notification template, name: "INV_NEGATIVE_STOCK", source: "db-script BLOCK 8d into the given NOTIF_TEMPLATE; given notif provisioning per tenant"}
  - {kind: notification template, name: "INV_NEAR_EXPIRY", source: "db-script BLOCK 8d; given notif provisioning per tenant"}
  - {kind: notification template, name: "INV_OFFLINE_SALE_INACTIVE", source: "db-script BLOCK 8d; given notif provisioning per tenant", note: "an offline sale recorded against an inactive warehouse, Variant or Item (ADR-INV-052)"}
  - {kind: setting, name: "INV_ALLOW_NEGATIVE_STOCK", source: "db-script BLOCK 8e platform default (true) in the given CU_APP_CONFIGURATION; tenants inherit until API-INV-049 saves an override"}
  - {kind: setting, name: "INV_NEAR_EXPIRY_DAYS", source: "db-script BLOCK 8e platform default (30); tenants inherit until API-INV-049 saves an override"}
  - {kind: setting, name: "INV_MEASURE_UOM_KEY", source: "db-script BLOCK 8e platform default (CAT_UOM, the key CAT SRS A6 declares); every tenant inherits it, no INV endpoint writes it (ADR-INV-043)", note: "the key of the consumed unit-of-measure lookup; CAT is asked to publish it in contract-cat"}
  - {kind: report, name: "INV_OPENING_STOCK_SHEET", source: "InvReports (ReportProvider) registers the report at start-up; rendered by PrintApi (API-INV-046)"}
  - {kind: master row, name: "default branch «الفرع الرئيسي» / Main branch", source: "InvTenantProvisioning (TenantProvisioningContributor) at tenant provisioning, idempotent (REQ-INV-020, 021)"}
  - {kind: master row, name: "default warehouse «المستودع الرئيسي» / Main warehouse", source: "InvTenantProvisioning (TenantProvisioningContributor) at tenant provisioning, under the default branch, idempotent (REQ-INV-001, 002)"}
  - {kind: permission, name: "PERM_INV_WAREHOUSES_VIEW", source: "InvPermissions (PermissionContributor) registers page INV_WAREHOUSES; the given sec derives the permission; the preset grant is seeded at tenant provisioning by the same contributor", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_WAREHOUSES_CREATE", source: "InvPermissions (PermissionContributor) — page INV_WAREHOUSES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_WAREHOUSES_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_WAREHOUSES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_WAREHOUSES_DELETE", source: "InvPermissions (PermissionContributor) — page INV_WAREHOUSES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_BRANCHES_VIEW", source: "InvPermissions (PermissionContributor) — page INV_BRANCHES; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_BRANCHES_CREATE", source: "InvPermissions (PermissionContributor) — page INV_BRANCHES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_BRANCHES_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_BRANCHES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_BRANCHES_DELETE", source: "InvPermissions (PermissionContributor) — page INV_BRANCHES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_STOCK_BALANCES_VIEW", source: "InvPermissions (PermissionContributor) — page INV_STOCK_BALANCES; preset grant at provisioning", note: "role: Owner, Warehouse, Seller/Cashier"}
  - {kind: permission, name: "PERM_INV_STOCK_MOVES_VIEW", source: "InvPermissions (PermissionContributor) — page INV_STOCK_MOVES; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_TRANSFERS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_TRANSFERS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_TRANSFERS_CREATE", source: "InvPermissions (PermissionContributor) — page INV_TRANSFERS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_TRANSFERS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_TRANSFERS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_TRANSFERS_DELETE", source: "InvPermissions (PermissionContributor) — page INV_TRANSFERS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_COUNTS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_COUNTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_COUNTS_CREATE", source: "InvPermissions (PermissionContributor) — page INV_COUNTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_COUNTS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_COUNTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_COUNTS_DELETE", source: "InvPermissions (PermissionContributor) — page INV_COUNTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ADJUSTMENTS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_ADJUSTMENTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ADJUSTMENTS_CREATE", source: "InvPermissions (PermissionContributor) — page INV_ADJUSTMENTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ADJUSTMENTS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_ADJUSTMENTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ADJUSTMENTS_DELETE", source: "InvPermissions (PermissionContributor) — page INV_ADJUSTMENTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_LOTS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_LOTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_LOTS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_LOTS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_ITEM_STOCK_SETTINGS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_ITEM_STOCK_SETTINGS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ITEM_STOCK_SETTINGS_CREATE", source: "InvPermissions (PermissionContributor) — page INV_ITEM_STOCK_SETTINGS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_ITEM_STOCK_SETTINGS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_ITEM_STOCK_SETTINGS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_OPENING_STOCK_VIEW", source: "InvPermissions (PermissionContributor) — page INV_OPENING_STOCK; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_OPENING_STOCK_CREATE", source: "InvPermissions (PermissionContributor) — page INV_OPENING_STOCK; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_SETTINGS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_SETTINGS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_SETTINGS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_SETTINGS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_STOCK_VALUATION_VIEW", source: "InvPermissions (PermissionContributor) — page INV_STOCK_VALUATION; preset grant at provisioning", note: "role: Owner"}
```

The frontend references these permission names; it does not redeclare them.
<!-- PHASE:SEC-BE:END -->
