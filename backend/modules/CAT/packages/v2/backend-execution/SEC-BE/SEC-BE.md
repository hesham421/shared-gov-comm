<!-- source: PHASE:SEC-BE -->
<!-- traces: REQ-CAT-001, REQ-CAT-014, REQ-CAT-015, REQ-CAT-016, REQ-CAT-127, REQ-CAT-131 -->
<!-- PHASE:SEC-BE:START traces=REQ-CAT-131,REQ-CAT-001,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-127 -->
## PHASE SEC-BE — security, backend half (R5)

Every endpoint verifies its `PERM_<PAGE_CODE>_<ACTION>` before processing: mutations by `@PreAuthorize` on their service method, reads by `@PreAuthorize` on their controller-side facade (ADR-CAT-061). API-CAT-052 accepts the VIEW permission of any CAT page (`hasAnyAuthority`), because every CAT screen shows lookup-backed fields; it is listed under SCR-REQ-CAT-001 and SCR-REQ-CAT-006, the pages whose fields and filters read it. A caller without it receives CAT-403-FORBIDDEN and nothing changes (REQ-CAT-131). The one exception, API-CAT-047, answers an empty result instead of 403 (REQ-CAT-127): its facade `CatSearchQueryFacade.searchCatalogue` carries only `@PreAuthorize("isAuthenticated()")`, so Spring Security never answers 403 before the service runs, and `CatCatalogueReadService.searchCatalogue` checks PERM_CAT_ITEMS_VIEW in code and returns an empty list when the caller lacks it — the HTTP path and `CatalogueReadApi.searchCatalogue` behave the same. No service checks a role name; only permission names. A forbidden call maps through `LocalizedException → {code, messageAr, messageEn}` to CAT-403-FORBIDDEN. VIEW is the gateway: without it no other action of the page applies. Page registration: `CatPermissions` (given `PermissionContributor`) registers each page code with its name in ar / en and the parent menu «الكتالوج / Catalog»; the given `sec` module derives the permission names (no SQL seed of permission rows — SRS Access summary); preset-role grants per ADR-CAT-046.

### SCR-REQ-CAT-001 — Items · page CAT_ITEMS «الأصناف / Items»
VIEW (Owner, Seller/cashier, Warehouse staff): API-CAT-002, 007, 011, 038, 043, 044, 046, 047, 052 · CREATE (Owner): API-CAT-001, 006, 010, 014, 015, 018 · UPDATE (Owner): API-CAT-003, 005, 008, 012, 017, 019 · DELETE (Owner): API-CAT-004, 009, 013, 016, 020

### SCR-REQ-CAT-002 — Categories · page CAT_CATEGORIES «الفئات / Categories»
VIEW (Owner, Seller/cashier, Warehouse staff): API-CAT-022, 039, 040 · CREATE (Owner): API-CAT-021 · UPDATE (Owner): API-CAT-023, 053 (reactivation) · DELETE (Owner): API-CAT-024

### SCR-REQ-CAT-003 — Variant attributes · page CAT_VARIANT_ATTRIBUTES «خصائص المتغيّرات / Variant attributes»
VIEW (Owner, Seller/cashier, Warehouse staff): API-CAT-026, 041 · CREATE (Owner): API-CAT-025, 029 · UPDATE (Owner): API-CAT-027, 030, 054, 055 (reactivations) · DELETE (Owner): API-CAT-028, 031. The menu entry is hidden by the frontend while `variants` is off; the backend refuses the creates and updates (API-CAT-025, 027, 029, 030) with RULE-CAT-008 (REQ-CAT-016), and keeps the reads, deactivations and reactivations (API-CAT-026, 028, 031, 041, 054, 055) open so that existing attributes can be cleaned up or restored.

### SCR-REQ-CAT-004 — Item import · page CAT_ITEM_IMPORT «استيراد الأصناف / Item import»
VIEW (Owner): API-CAT-048 · CREATE (Owner): API-CAT-049 · UPDATE, DELETE not granted (no endpoint)

### SCR-REQ-CAT-005 — Barcode labels · page CAT_BARCODE_LABELS «ملصقات الباركود / Barcode labels»
VIEW (Owner, Warehouse staff): API-CAT-042 · CREATE (Owner, Warehouse staff): API-CAT-050, API-CAT-056 (the missing internal barcode of a label line — ADR-CAT-078) · UPDATE, DELETE not granted (no endpoint)

### SCR-REQ-CAT-006 — Catalogue settings · page CAT_SETTINGS «إعدادات الكتالوج / Catalogue settings»
VIEW (Owner): API-CAT-045, 051, 052 · CREATE (Owner): API-CAT-032, 035 · UPDATE (Owner): API-CAT-033, 036 · DELETE (Owner): API-CAT-034, 037

```yaml name=permission-matrix
rows:
  - screen: SCR-REQ-CAT-001 Items
    entity: ENT-CAT-001
    api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-038, API-CAT-043, API-CAT-044, API-CAT-046, API-CAT-047, API-CAT-052]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_ITEMS_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_ITEMS_CREATE}
      UPDATE: {granted: true, permission: PERM_CAT_ITEMS_UPDATE}
      DELETE: {granted: true, permission: PERM_CAT_ITEMS_DELETE}
  - screen: SCR-REQ-CAT-002 Categories
    entity: ENT-CAT-006
    api: [API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-039, API-CAT-040, API-CAT-053]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_CATEGORIES_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_CATEGORIES_CREATE}
      UPDATE: {granted: true, permission: PERM_CAT_CATEGORIES_UPDATE}
      DELETE: {granted: true, permission: PERM_CAT_CATEGORIES_DELETE}
  - screen: SCR-REQ-CAT-003 Variant attributes
    entity: ENT-CAT-003
    api: [API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-041, API-CAT-054, API-CAT-055]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_VARIANT_ATTRIBUTES_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_VARIANT_ATTRIBUTES_CREATE}
      UPDATE: {granted: true, permission: PERM_CAT_VARIANT_ATTRIBUTES_UPDATE}
      DELETE: {granted: true, permission: PERM_CAT_VARIANT_ATTRIBUTES_DELETE}
  - screen: SCR-REQ-CAT-004 Item import
    entity: ENT-CAT-001
    api: [API-CAT-048, API-CAT-049]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_ITEM_IMPORT_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_ITEM_IMPORT_CREATE}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-CAT-005 Barcode labels
    entity: ENT-CAT-002
    api: [API-CAT-042, API-CAT-050, API-CAT-056]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_BARCODE_LABELS_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_BARCODE_LABELS_CREATE}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-CAT-006 Catalogue settings
    entity: ENT-CAT-011
    api: [API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-045, API-CAT-051, API-CAT-052]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_SETTINGS_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_SETTINGS_CREATE}
      UPDATE: {granted: true, permission: PERM_CAT_SETTINGS_UPDATE}
      DELETE: {granted: true, permission: PERM_CAT_SETTINGS_DELETE}
```

### Bootstrap data
The rows every first call reads. Lookup keys and number series live in given tables but are CAT's to seed (db-script BLOCK 8 — PLATFORM tenant; every other tenant at its provisioning per ADR-CAT-058). Permissions are not seeded by SQL: each is derived by the given `sec` module from the page CAT registers, and each carries its preset-role grant (SRS B4, ADR-CAT-046).

```yaml name=bootstrap
rows:
  - {kind: lookup key, name: "CAT_ITEM_KIND", source: "db-script BLOCK 8a/8b — mdl lookup type + values STANDARD, BUNDLE (PLATFORM tenant)"}
  - {kind: lookup key, name: "CAT_PUBLISH_STATE", source: "db-script BLOCK 8a/8b — values UNPUBLISHED, PUBLISHED"}
  - {kind: lookup key, name: "CAT_BRAND", source: "db-script BLOCK 8a — type only", note: "no seeded value; the tenant maintains its brands in mdl"}
  - {kind: lookup key, name: "CAT_UOM", source: "db-script BLOCK 8a/8b — 8 values PIECE … LITRE"}
  - {kind: lookup key, name: "CAT_TAX_CLASS", source: "db-script BLOCK 8a/8b — values STANDARD, ZERO_RATED, EXEMPT"}
  - {kind: lookup key, name: "CAT_EINVOICE_ITEM_CODE_TYPE", source: "db-script BLOCK 8a/8b — values GS1, EGS"}
  - {kind: lookup key, name: "CAT_EINVOICE_UNIT", source: "db-script BLOCK 8a/8b — 8 values EA … DZN"}
  - {kind: lookup key, name: "CAT_BARCODE_TYPE", source: "db-script BLOCK 8a/8b — values GTIN, INTERNAL, SCALE"}
  - {kind: lookup key, name: "CAT_SCALE_VALUE_TYPE", source: "db-script BLOCK 8a/8b — values WEIGHT, PRICE"}
  - {kind: lookup key, name: "CAT_CHANGE_KIND", source: "db-script BLOCK 8a/8b — values CREATED, UPDATED, DEACTIVATED, REACTIVATED"}
  - {kind: number series, name: "CAT_ITEM", source: "db-script BLOCK 8c — given sequence module data, ITM-{SEQ:6}"}
  - {kind: number series, name: "CAT_VARIANT", source: "db-script BLOCK 8c — VAR-{SEQ:6}"}
  - {kind: number series, name: "CAT_INTERNAL_BARCODE", source: "db-script BLOCK 8c — {SEQ:10}"}
  - {kind: seed rows, name: "CAT_UNIT_EINVOICE_CODE (8 rows, D-12)", source: "db-script BLOCK 8d (PLATFORM); each new tenant: CatTenantProvisioningContributor (given TenantProvisioningContributor) — ADR-CAT-058"}
  - {kind: report, name: "CAT_BARCODE_LABEL", source: "CatBarcodeLabelReportProvider (given ReportProvider) at application start"}
  - {kind: file category, name: "CAT_ITEM_IMAGE", source: "the given FILE module's per-tenant category (FILE_CATEGORY): allowPublic = true, allowed types PNG / JPEG / WEBP, limit 5 MB; registered for the PLATFORM tenant at deployment and copied into each new tenant by CatTenantProvisioningContributor — ADR-CAT-076", note: "the FILE upload endpoint enforces the file part of RULE-CAT-038; the supported registration mechanism is question 3 of CORE-GAP-file-document-metadata-read"}
  - {kind: report, name: "CAT_ITEM_IMPORT_SHEET", source: "CatItemImportSheetReportProvider (given ReportProvider) at application start — ADR-CAT-057", note: "rendered as xlsx through PrintApi by API-CAT-048; the given derives the report authority CAT:REPORT:CAT_ITEM_IMPORT_SHEET"}
  - {kind: tenant seed, name: "CAT lookup types and values (10 keys, 34 values) in each new tenant", source: "given mdl copies them from PLATFORM at tenant provisioning (erp-core modules.mdl) — ADR-CAT-058"}
  - {kind: tenant seed, name: "number series CAT_ITEM, CAT_VARIANT, CAT_INTERNAL_BARCODE in each new tenant", source: "given sequence copies them at tenant provisioning with the counter at 1 — ADR-CAT-058"}
  - {kind: module manifest, name: "META-INF/erp/modules/cat.yaml", source: "the CAT jar; read by the given module catalog at application start (PHASE CORE)"}
  - {kind: page, name: "CAT_ITEMS", source: "CatPermissions (given PermissionContributor) at application start"}
  - {kind: page, name: "CAT_CATEGORIES", source: "CatPermissions at application start"}
  - {kind: page, name: "CAT_VARIANT_ATTRIBUTES", source: "CatPermissions at application start"}
  - {kind: page, name: "CAT_ITEM_IMPORT", source: "CatPermissions at application start"}
  - {kind: page, name: "CAT_BARCODE_LABELS", source: "CatPermissions at application start"}
  - {kind: page, name: "CAT_SETTINGS", source: "CatPermissions at application start"}
  - {kind: permission, name: "PERM_CAT_ITEMS_VIEW", source: "given sec derives it from page CAT_ITEMS", note: "role grant: Owner, Seller/cashier, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEMS_CREATE", source: "given sec derives it from page CAT_ITEMS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEMS_UPDATE", source: "given sec derives it from page CAT_ITEMS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEMS_DELETE", source: "given sec derives it from page CAT_ITEMS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_CATEGORIES_VIEW", source: "given sec derives it from page CAT_CATEGORIES", note: "role grant: Owner, Seller/cashier, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_CATEGORIES_CREATE", source: "given sec derives it from page CAT_CATEGORIES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_CATEGORIES_UPDATE", source: "given sec derives it from page CAT_CATEGORIES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_CATEGORIES_DELETE", source: "given sec derives it from page CAT_CATEGORIES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_VARIANT_ATTRIBUTES_VIEW", source: "given sec derives it from page CAT_VARIANT_ATTRIBUTES", note: "role grant: Owner, Seller/cashier, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_VARIANT_ATTRIBUTES_CREATE", source: "given sec derives it from page CAT_VARIANT_ATTRIBUTES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_VARIANT_ATTRIBUTES_UPDATE", source: "given sec derives it from page CAT_VARIANT_ATTRIBUTES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_VARIANT_ATTRIBUTES_DELETE", source: "given sec derives it from page CAT_VARIANT_ATTRIBUTES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEM_IMPORT_VIEW", source: "given sec derives it from page CAT_ITEM_IMPORT", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEM_IMPORT_CREATE", source: "given sec derives it from page CAT_ITEM_IMPORT", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_BARCODE_LABELS_VIEW", source: "given sec derives it from page CAT_BARCODE_LABELS", note: "role grant: Owner, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_BARCODE_LABELS_CREATE", source: "given sec derives it from page CAT_BARCODE_LABELS", note: "role grant: Owner, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_SETTINGS_VIEW", source: "given sec derives it from page CAT_SETTINGS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_SETTINGS_CREATE", source: "given sec derives it from page CAT_SETTINGS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_SETTINGS_UPDATE", source: "given sec derives it from page CAT_SETTINGS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_SETTINGS_DELETE", source: "given sec derives it from page CAT_SETTINGS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: capability flag, name: "variants", source: "core settings read through SettingsApi (flag owner OPEN, P-02 — ADR-CAT-041)", note: "CAT never writes it; an unreadable value refuses the operation (RULE-CAT-009)"}
  - {kind: capability flag, name: "bundles", source: "core settings read through SettingsApi (flag owner OPEN, P-02 — ADR-CAT-041)", note: "CAT never writes it"}
```
The frontend stage references these permission names; it does not redeclare them.
<!-- PHASE:SEC-BE:END -->
