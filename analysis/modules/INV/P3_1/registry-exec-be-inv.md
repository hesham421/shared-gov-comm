## REGISTRY — P3.1 — INV v1
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Stage : P3.1 (Backend Execution Plan)   Profile : commerce
Source : analysis/modules/INV/P3_1/backend-execution-plan-inv.md · api-spec-inv.yaml · Date : 2026-10-31
══════════════════════════════════════════════════════════════════

### ID ranges

| Atom | Count | Range | Last sequence | Next free |
|---|---|---|---|---|
| API | 52 | API-INV-001 … API-INV-052 | 52 | API-INV-053 |
| QR | 46 | QR-INV-001 … QR-INV-046 | 46 | QR-INV-047 |

API ids : API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008, API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015, API-INV-016, API-INV-017, API-INV-018, API-INV-019, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-040, API-INV-041, API-INV-042, API-INV-043, API-INV-044, API-INV-045, API-INV-046, API-INV-047, API-INV-048, API-INV-049, API-INV-050, API-INV-051, API-INV-052

QR ids : QR-INV-001, QR-INV-002, QR-INV-003, QR-INV-004, QR-INV-005, QR-INV-006, QR-INV-007, QR-INV-008, QR-INV-009, QR-INV-010, QR-INV-011, QR-INV-012, QR-INV-013, QR-INV-014, QR-INV-015, QR-INV-016, QR-INV-017, QR-INV-018, QR-INV-019, QR-INV-020, QR-INV-021, QR-INV-022, QR-INV-023, QR-INV-024, QR-INV-025, QR-INV-026, QR-INV-027, QR-INV-028, QR-INV-029, QR-INV-030, QR-INV-031, QR-INV-032, QR-INV-033, QR-INV-034, QR-INV-035, QR-INV-036, QR-INV-037, QR-INV-038, QR-INV-039, QR-INV-040, QR-INV-041, QR-INV-042, QR-INV-043, QR-INV-044, QR-INV-045, QR-INV-046

### Endpoints

| API | Verb | Path | Permission | Phase / SUB |
|---|---|---|---|---|
| API-INV-001 | GET | /api/v1/inv/warehouses | PERM_INV_WAREHOUSES_VIEW | SVC-API-SEARCH |
| API-INV-002 | GET | /api/v1/inv/warehouses/{id} | PERM_INV_WAREHOUSES_VIEW | SVC-API-SEARCH |
| API-INV-003 | POST | /api/v1/inv/warehouses | PERM_INV_WAREHOUSES_CREATE | SVC-API-CRUD |
| API-INV-004 | PUT | /api/v1/inv/warehouses/{id} | PERM_INV_WAREHOUSES_UPDATE | SVC-API-CRUD |
| API-INV-005 | DELETE | /api/v1/inv/warehouses/{id} | PERM_INV_WAREHOUSES_DELETE | SVC-API-CRUD |
| API-INV-006 | PATCH | /api/v1/inv/warehouses/{id}/reactivate | PERM_INV_WAREHOUSES_UPDATE | SVC-API-CRUD |
| API-INV-007 | PATCH | /api/v1/inv/warehouses/{id}/default | PERM_INV_WAREHOUSES_UPDATE | SVC-API-CRUD |
| API-INV-008 | GET | /api/v1/inv/context | any PERM_INV_*_VIEW | SVC-API-SEARCH |
| API-INV-009 | GET | /api/v1/inv/branches | PERM_INV_BRANCHES_VIEW | SVC-API-SEARCH |
| API-INV-010 | GET | /api/v1/inv/branches/{id} | PERM_INV_BRANCHES_VIEW | SVC-API-SEARCH |
| API-INV-011 | POST | /api/v1/inv/branches | PERM_INV_BRANCHES_CREATE | SVC-API-CRUD |
| API-INV-012 | PUT | /api/v1/inv/branches/{id} | PERM_INV_BRANCHES_UPDATE | SVC-API-CRUD |
| API-INV-013 | DELETE | /api/v1/inv/branches/{id} | PERM_INV_BRANCHES_DELETE | SVC-API-CRUD |
| API-INV-014 | PATCH | /api/v1/inv/branches/{id}/reactivate | PERM_INV_BRANCHES_UPDATE | SVC-API-CRUD |
| API-INV-015 | PATCH | /api/v1/inv/branches/{id}/default | PERM_INV_BRANCHES_UPDATE | SVC-API-CRUD |
| API-INV-016 | GET | /api/v1/inv/stock-balances | PERM_INV_STOCK_BALANCES_VIEW | SVC-API-SEARCH |
| API-INV-017 | GET | /api/v1/inv/stock-balances/availability/{variantId} | PERM_INV_STOCK_BALANCES_VIEW | SVC-API-SEARCH |
| API-INV-018 | GET | /api/v1/inv/stock-moves | PERM_INV_STOCK_MOVES_VIEW | SVC-API-SEARCH |
| API-INV-019 | GET | /api/v1/inv/stock-moves/{id} | PERM_INV_STOCK_MOVES_VIEW | SVC-API-SEARCH |
| API-INV-020 | GET | /api/v1/inv/transfers | PERM_INV_TRANSFERS_VIEW | SVC-API-SEARCH |
| API-INV-021 | GET | /api/v1/inv/transfers/{id} | PERM_INV_TRANSFERS_VIEW | SVC-API-SEARCH |
| API-INV-022 | POST | /api/v1/inv/transfers | PERM_INV_TRANSFERS_CREATE | SVC-API-CRUD |
| API-INV-023 | PUT | /api/v1/inv/transfers/{id} | PERM_INV_TRANSFERS_UPDATE | SVC-API-CRUD |
| API-INV-024 | PATCH | /api/v1/inv/transfers/{id}/post | PERM_INV_TRANSFERS_UPDATE | SVC-API-CRUD |
| API-INV-025 | DELETE | /api/v1/inv/transfers/{id} | PERM_INV_TRANSFERS_DELETE | SVC-API-CRUD |
| API-INV-026 | GET | /api/v1/inv/stock-counts | PERM_INV_COUNTS_VIEW | SVC-API-SEARCH |
| API-INV-027 | GET | /api/v1/inv/stock-counts/{id} | PERM_INV_COUNTS_VIEW | SVC-API-SEARCH |
| API-INV-028 | POST | /api/v1/inv/stock-counts | PERM_INV_COUNTS_CREATE | SVC-API-CRUD |
| API-INV-029 | POST | /api/v1/inv/stock-counts/{id}/lines | PERM_INV_COUNTS_UPDATE | SVC-API-CRUD |
| API-INV-030 | PUT | /api/v1/inv/stock-counts/{id}/lines/{lineId} | PERM_INV_COUNTS_UPDATE | SVC-API-CRUD |
| API-INV-031 | DELETE | /api/v1/inv/stock-counts/{id}/lines/{lineId} | PERM_INV_COUNTS_UPDATE | SVC-API-CRUD |
| API-INV-032 | PATCH | /api/v1/inv/stock-counts/{id}/post | PERM_INV_COUNTS_UPDATE | SVC-API-CRUD |
| API-INV-033 | DELETE | /api/v1/inv/stock-counts/{id} | PERM_INV_COUNTS_DELETE | SVC-API-CRUD |
| API-INV-034 | GET | /api/v1/inv/stock-adjustments | PERM_INV_ADJUSTMENTS_VIEW | SVC-API-SEARCH |
| API-INV-035 | GET | /api/v1/inv/stock-adjustments/{id} | PERM_INV_ADJUSTMENTS_VIEW | SVC-API-SEARCH |
| API-INV-036 | POST | /api/v1/inv/stock-adjustments | PERM_INV_ADJUSTMENTS_CREATE | SVC-API-CRUD |
| API-INV-037 | PUT | /api/v1/inv/stock-adjustments/{id} | PERM_INV_ADJUSTMENTS_UPDATE | SVC-API-CRUD |
| API-INV-038 | PATCH | /api/v1/inv/stock-adjustments/{id}/post | PERM_INV_ADJUSTMENTS_UPDATE | SVC-API-CRUD |
| API-INV-039 | DELETE | /api/v1/inv/stock-adjustments/{id} | PERM_INV_ADJUSTMENTS_DELETE | SVC-API-CRUD |
| API-INV-040 | GET | /api/v1/inv/lots | PERM_INV_LOTS_VIEW | SVC-API-SEARCH |
| API-INV-041 | GET | /api/v1/inv/lots/{id} | PERM_INV_LOTS_VIEW | SVC-API-SEARCH |
| API-INV-042 | PATCH | /api/v1/inv/lots/{id} | PERM_INV_LOTS_UPDATE | SVC-API-CRUD |
| API-INV-043 | GET | /api/v1/inv/item-stock-settings | PERM_INV_ITEM_STOCK_SETTINGS_VIEW | SVC-API-SEARCH |
| API-INV-044 | GET | /api/v1/inv/item-stock-settings/by-item/{itemId} | PERM_INV_ITEM_STOCK_SETTINGS_VIEW | SVC-API-SEARCH |
| API-INV-045 | PUT | /api/v1/inv/item-stock-settings/by-item/{itemId} | PERM_INV_ITEM_STOCK_SETTINGS_CREATE / _UPDATE | SVC-API-CRUD |
| API-INV-046 | GET | /api/v1/inv/opening-stock/sheet | PERM_INV_OPENING_STOCK_VIEW | SVC-API-SEARCH |
| API-INV-047 | POST | /api/v1/inv/opening-stock | PERM_INV_OPENING_STOCK_CREATE | SVC-API-CRUD |
| API-INV-048 | GET | /api/v1/inv/settings | PERM_INV_SETTINGS_VIEW | SVC-API-SEARCH |
| API-INV-049 | PUT | /api/v1/inv/settings | PERM_INV_SETTINGS_UPDATE | SVC-API-CRUD |
| API-INV-050 | GET | /api/v1/inv/stock-valuation | PERM_INV_STOCK_VALUATION_VIEW | SVC-API-SEARCH |
| API-INV-051 | GET | /api/v1/inv/stock-valuation/{variantId} | PERM_INV_STOCK_VALUATION_VIEW | SVC-API-SEARCH |
| API-INV-052 | GET | /api/v1/inv/lookups/{key} | any PERM_INV_*_VIEW | SVC-API-SEARCH |

### Query Reference Catalog

| QR | Entity | Operation | Reached by |
|---|---|---|---|
| QR-INV-001 | ENT-INV-001 | FIND_BY_CRITERIA | API-INV-001 |
| QR-INV-002 | ENT-INV-001 | FIND_ONE | API-INV-002, 004, 005, 006, 007, 017, 022, 023, 024, 028, 032, 036, 038, 047 |
| QR-INV-003 | ENT-INV-001 | EXISTS | API-INV-003, 004 |
| QR-INV-004 | ENT-INV-001 | SAVE / UPDATE | API-INV-003, 004, 005, 006, 007 |
| QR-INV-005 | ENT-INV-001 | FIND_ONE | API-INV-007, 028, 036, 047 |
| QR-INV-006 | ENT-INV-001 | COUNT | API-INV-008, 022, 023, 024, 028, 036, 047 |
| QR-INV-007 | ENT-INV-001 | EXISTS | API-INV-005 |
| QR-INV-008 | ENT-INV-002 | FIND_BY_CRITERIA | API-INV-009 |
| QR-INV-009 | ENT-INV-002 | FIND_ONE | API-INV-003, 004, 006, 010, 012, 013, 014, 015 |
| QR-INV-010 | ENT-INV-002 | EXISTS | API-INV-011, 012 |
| QR-INV-011 | ENT-INV-002 | SAVE / UPDATE | API-INV-011, 012, 013, 014, 015 |
| QR-INV-012 | ENT-INV-002 | FIND_ONE | API-INV-003, 015 |
| QR-INV-013 | ENT-INV-002 | COUNT | API-INV-008 |
| QR-INV-014 | ENT-INV-002 | EXISTS | API-INV-013 |
| QR-INV-015 | ENT-INV-003 | NATIVE (AGGREGATE) | API-INV-016 |
| QR-INV-016 | ENT-INV-003 | NATIVE (AGGREGATE) | API-INV-016, 017 |
| QR-INV-017 | ENT-INV-003 | NATIVE + FIND_ONE | API-INV-024, 032, 038, 047 |
| QR-INV-018 | ENT-INV-003 | UPDATE | API-INV-024, 032, 038, 047 |
| QR-INV-019 | ENT-INV-003 | AGGREGATE | API-INV-022, 023, 024, 029, 032, 036, 037, 038, 047 |
| QR-INV-020 | ENT-INV-004 | FIND_BY_CRITERIA | API-INV-018 |
| QR-INV-021 | ENT-INV-004 | FIND_ONE | API-INV-019 |
| QR-INV-022 | ENT-INV-004 | SAVE (insert only) | API-INV-024, 032, 038, 047 |
| QR-INV-023 | ENT-INV-013 | FIND_ONE | API-INV-024, 032, 038, 047, 051 |
| QR-INV-024 | ENT-INV-013 | NATIVE / UPDATE | API-INV-024, 032, 038, 047 |
| QR-INV-025 | ENT-INV-013 | FIND_BY_CRITERIA + AGGREGATE | API-INV-050 |
| QR-INV-026 | ENT-INV-005 | AGGREGATE | API-INV-016, 017 |
| QR-INV-027 | ENT-INV-006 | FIND_BY_CRITERIA | API-INV-020 |
| QR-INV-028 | ENT-INV-006 | FIND_ONE | API-INV-021, 023, 024, 025 |
| QR-INV-029 | ENT-INV-006 | SAVE / UPDATE | API-INV-022, 023, 024, 025 |
| QR-INV-030 | ENT-INV-008 | FIND_BY_CRITERIA | API-INV-026 |
| QR-INV-031 | ENT-INV-008 | FIND_ONE | API-INV-027, 029, 030, 031, 032, 033, 035 |
| QR-INV-032 | ENT-INV-008 | SAVE / UPDATE | API-INV-028, 032, 033 |
| QR-INV-033 | ENT-INV-009 | FIND_ONE | API-INV-029 |
| QR-INV-034 | ENT-INV-009 | SAVE / UPDATE / DELETE | API-INV-029, 030, 031, 032 |
| QR-INV-035 | ENT-INV-010 | FIND_BY_CRITERIA | API-INV-034 |
| QR-INV-036 | ENT-INV-010 | FIND_ONE | API-INV-027, 035, 037, 038, 039, 047 |
| QR-INV-037 | ENT-INV-010 | SAVE / UPDATE | API-INV-032, 036, 037, 038, 039, 047 |
| QR-INV-038 | ENT-INV-012 | NATIVE (AGGREGATE) | API-INV-040 |
| QR-INV-039 | ENT-INV-012 | FIND_ONE | API-INV-018, 019, 021, 022, 023, 024, 027, 029, 032, 035, 038, 041, 042 |
| QR-INV-040 | ENT-INV-012 | FIND_ONE | API-INV-038, 047 |
| QR-INV-041 | ENT-INV-012 | NATIVE / UPDATE | API-INV-038, 042, 047 |
| QR-INV-042 | ENT-INV-014 | FIND_BY_CRITERIA | API-INV-043 |
| QR-INV-043 | ENT-INV-014 | FIND_ONE | API-INV-016, 017, 022, 023, 024, 029, 030, 032, 036, 037, 038, 044, 045, 047 |
| QR-INV-044 | ENT-INV-014 | SAVE / UPDATE | API-INV-045 |
| QR-INV-045 | ENT-INV-003 | COUNT | API-INV-045 |
| QR-INV-046 | ENT-INV-003 | EXISTS | API-INV-045 |

### Entities / tables bound

14 / 14 — ENT-INV-001 … ENT-INV-014 bound to their db-script tables (INV_WAREHOUSE, INV_BRANCH, INV_STOCK_BALANCE,
INV_STOCK_MOVE, INV_RESERVATION, INV_TRANSFER, INV_TRANSFER_LINE, INV_STOCK_COUNT, INV_STOCK_COUNT_LINE,
INV_STOCK_ADJUSTMENT, INV_STOCK_ADJUSTMENT_LINE, INV_LOT, INV_STOCK_VALUATION, INV_ITEM_STOCK_SETTINGS);
221 / 221 DBF in the DB Alignment Manifest, 0 issues (DBF-INV-220, 221 REQUEST_HASH added at gate-analysis revise 2).

Lookups reused (keys owned by INV, seeded at P2): INV_ADJUSTMENT_REASON, INV_MOVE_TYPE, INV_DOC_STATUS,
INV_RESERVATION_STATUS, INV_SOURCE_DOC_TYPE, INV_CHANGE_KIND · consumed: the unit-of-measure key of the Item owner,
named by the SettingsApi setting INV_MEASURE_UOM_KEY (platform default CAT_UOM — ADR-INV-043, revising ADR-INV-022) ·
new: none.

### Integration

| XM | Type | Target | requires | State | Note |
|---|---|---|---|---|---|
| XM-INV-001 … XM-INV-010 | HARD-FK | CAT · ENT-CAT-002 | CAT:DELIVERED | CONTRACTED (P2) | one INT-C block each, ddl_patch FK_<TABLE>_CAT_VARIANT[_n] |
| XM-INV-011 | HARD-FK | CAT · ENT-CAT-001 | CAT:DELIVERED | CONTRACTED (P2) | ddl_patch FK_INV_ITEM_STOCK_SETTINGS_CAT_ITEM |
| XM-INV-012 | SOFT-READ | CAT · ENT-CAT-007 | CAT:DELIVERED | CONTRACTED (P2) | adapter CatalogBundleComponentAdapter |
| XM-INV-013 | SOFT-READ | CAT · ENT-CAT-009 | CAT:DELIVERED | CONTRACTED (P2) | adapter CatalogBarcodeAdapter |
| XM-INV-014 | SOFT-READ | CAT · ENT-CAT-002 | CAT:DELIVERED | minted P3.1 — back-registered in db-script and registry-db (CON-CAT-002 / 008) | adapter CatalogVariantAdapter |
| XM-INV-015 | SOFT-READ | CAT · ENT-CAT-001 | CAT:DELIVERED | minted P3.1 — back-registered in db-script and registry-db (CON-CAT-001 / 007 / 016) | adapter CatalogItemAdapter |

Minted records for the register (orchestrator back-registration):

```yaml name=xm-minted
records:
  - {id: XM-INV-014, type: SOFT-READ, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-052, REQ-INV-072, REQ-INV-078, REQ-INV-097], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "no column · RULE-INV-018 / 028 checks and Variant names read through CAT getVariant (CON-CAT-008) and getItem (CON-CAT-007)"}
  - {id: XM-INV-015, type: SOFT-READ, target_module: CAT, target_entity: ENT-CAT-001, traces: [REQ-INV-039, REQ-INV-124, REQ-INV-126, REQ-INV-127, REQ-INV-129], state: CONTRACTED, contract_ref: "CON-CAT-001", column: "no column · Item kind, code, names and Variants read through CAT getItem (CON-CAT-007); Item term search through searchCatalogue (CON-CAT-016)"}
```

Next free XM : XM-INV-016.

### Contract honoured

CON-INV-001 … 005 → DATA-DOM entity blocks (ENT-INV-001, 002, 012, 003, 013) · CON-INV-006 … 019 →
`InventoryApi` (SVC-API-INT) · CON-INV-020, 021 → OutboxPublisher events (CORE, SVC-API-INT).

Honours: CON-INV-001, CON-INV-002, CON-INV-003, CON-INV-004, CON-INV-005, CON-INV-006, CON-INV-007, CON-INV-008, CON-INV-009, CON-INV-010, CON-INV-011, CON-INV-012, CON-INV-013, CON-INV-014, CON-INV-015, CON-INV-016, CON-INV-017, CON-INV-018, CON-INV-019, CON-INV-020, CON-INV-021

### Catalog

Error codes : 59 HTTP catalog rows (`error-catalog` block — INV-422-DOC-DATE-FUTURE and INV-404-LOOKUP-KEY-UNKNOWN added at gate-analysis revise 2, INV-409-IDEMPOTENCY-KEY-REUSED moved in at revise 3 for API-INV-047) + 5 in-process-only codes (§5.1 — INV-422-RESERVATION-MISMATCH added at revise 1) · format `{MOD}-{http}[-{SLUG}]`
Rules with a code : 46 (41 in the HTTP catalog, 5 in-process only) · effect / answer rules without a code : RULE-INV-005, 014, 024, 038, 046 (messages kept in §1.3)
Rules without a message in a language : none · codes without an INV text : 4 (INV-400, INV-401, INV-500, INV-409-STALE-VERSION — the platform's texts, ADR-INV-021 amended); every other PLATFORM-STD row — INV-403, the ten `INV-404-*-NOT-FOUND`, INV-404-LOOKUP-KEY-UNKNOWN — and INV-422-LOAD-ROWS-INVALID carry INV's ar / en text (gate-analysis revise 3, G1)

### API document

api-spec-inv.yaml · OpenAPI 3.1.0 · operations 52 = API blocks 52 · line-level refusals carry `details` {lineRef, lineRefKind, rowNo?} (ADR-INV-046) · error responses: every catalog row answered
by each operation it names · paging page / size on 10 paginated operations · bearer JWT document-wide.

### Permissions

12 pages (SEC_PAGES) · 32 permissions declared · grants: Owner 32, Warehouse 18, Seller/Cashier 1 — all in the
`bootstrap` block with their producer.

### ALIGN

Verdict as stamped by the orchestrator from `gov.py analyze` (self-check block in ALIGN-BE). Findings fixed in
repair round 1: 37. The changes: XM register records 14 / 15 shape (db-script) and their back-registration
(registry-db); DBF citations of the HARD-FK blocks moved from the ddl_patch line to the block heading; ADR-INV-014 … 017
filed; typed `VersionedActionRequest` on the seven PATCH endpoints (ADR-INV-032); not-found code wording in
the ADR-INV-021 row; totals XM 15.

### ADRs

| ADR | Title | Status |
|---|---|---|
| ADR-INV-021 | PLATFORM-STD error rows | ACCEPTED |
| ADR-INV-022 | Consumed unit-of-measure lookup key | ACCEPTED |
| ADR-INV-023 | Cross-module foreign keys reference the target primary key | ACCEPTED |
| ADR-INV-024 | In-process InventoryApi, no REST surface | ACCEPTED |
| ADR-INV-025 | Native aggregate searches and term / Item filters | ACCEPTED |
| ADR-INV-026 | Locking order and insert-if-absent | ACCEPTED |
| ADR-INV-027 | Opening stock in one call, all or nothing | ACCEPTED |
| ADR-INV-028 | Inventory settings and context endpoints | ACCEPTED |
| ADR-INV-029 | Item stock settings idempotent save | ACCEPTED |
| ADR-INV-030 | "Never valued" and valuation row creation | ACCEPTED |
| ADR-INV-031 | Two SOFT-READ edges minted at P3.1 | ACCEPTED |
| ADR-INV-032 | State-change PATCH endpoints carry the caller's version as a typed body (repair round 1) | ACCEPTED |
| ADR-INV-041 | Native SQL writes its tenant predicate by hand and is marked @TenantUnscoped (gate-analysis revise 1, G1) | ACCEPTED |
| ADR-INV-042 | REST services check INV authority; InventoryApi delegates to unchecked internal engines (G3) | ACCEPTED |
| ADR-INV-043 | The consumed unit-of-measure key is the setting INV_MEASURE_UOM_KEY (G4; revises ADR-INV-022) | ACCEPTED |
| ADR-INV-044 | Concurrent requests with one idempotency key serialise on an advisory lock (G6) | ACCEPTED |
| ADR-INV-045 | A DRAFT is validated again when posted; a warehouse with DRAFT documents stays active (G8) | ACCEPTED |
| ADR-INV-046 | A line-level refusal names the failing line in `details` (gate-analysis revise 2, G2) | ACCEPTED |
| ADR-INV-047 | A system-generated adjustment does not check that its reason is tenant-active; OPENING_STOCK refused on the adjustment screen (G3, G18) | ACCEPTED |
| ADR-INV-048 | The idempotency key is recorded with a hash of its request; a reused key with another request is refused (G9) | ACCEPTED |
| ADR-INV-049 | Stock-move immutability also enforced by a database trigger and an `@Immutable` entity (G12, P2 delta) | ACCEPTED |
| ADR-INV-050 | Default switches serialise on a tenant advisory lock (G19) | ACCEPTED |
| ADR-INV-051 | A lot-less line of a lot-tracked Item is accepted up to the lot-less on-hand (gate-analysis revise 3, G2) | ACCEPTED |
| ADR-INV-052 | A synchronised offline sale skips the active-state checks and notifies the Warehouse role (G3; extends ADR-INV-045) | ACCEPTED |
| ADR-INV-053 | An opening stock load carries an idempotency key (G4) | ACCEPTED |

Revised in place at gate-analysis revise 1: ADR-INV-022 (G4), ADR-INV-024 (package, G22), ADR-INV-030 (count-difference
increase valued at 0, G11), ADR-INV-035 (barcode-only sheet, G12), ADR-INV-036 (warehouse labels on the row, G13),
ADR-INV-038 (schema-bound derivation rule, G14), ADR-INV-040 (CAT items INV relies on, G19).

Filed under analysis/decisions/INV/. BLOCKED : none. ADR-INV-019 (P2) is ACCEPTED — resolved by ADR-INV-020; P2 is complete.
ADR-INV-014 … 017 (P2, recorded in db-script §5.2) were filed there in repair round 1, unchanged, so the plan's citations resolve.
Revised in place at gate-analysis revise 2: ADR-INV-026 (lot locks, default-switch lock — G13, G19), ADR-INV-033 (lookup values through API-INV-052 — G7), ADR-INV-038 (AC-INV-144 / 147 aligned, AC-INV-111 reachable — G1, G17).

Revised in place at gate-analysis revise 3: ADR-INV-021 (INV owns the texts of its module-specific codes — G1).

Next free ADR : ADR-INV-054.

### Traceability

REQ covered by ≥ 1 API or DBF : 140 / 140 · orphan REQ : none.

### Event

"P3.1 completed: INV v1 — API 51, QR 46, XM blocks 15 (13 registered + 2 minted), ADR-INV-021 … 032" · "gate-analysis revise 1: root package com.commerce.inv, ADR-INV-041 … 045" · "gate-analysis revise 2: API-INV-052 (InvLookups), RULE-INV-050 / 051 codes, ADR-INV-046 … 050" · "gate-analysis revise 3: texts of every INV code, INV-409-IDEMPOTENCY-KEY-REUSED on API-INV-047, labels on every row, package domain, ADR-INV-051 … 053"
══════════════════════════════════════════════════════════════════
