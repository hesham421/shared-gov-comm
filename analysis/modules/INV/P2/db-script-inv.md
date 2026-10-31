# DB SCRIPT — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Profile : commerce   Stage : P2 (Database) · pass 1 · round 1
Inputs : srs (srs-inv.md, P1 commit 8afe84c — REQ 138 · AC 154 · ENT 14 · RULE 48), registry-srs, contract (contract-inv.md, P1.5 commit 2d5372b), contracts (contract-cat.md CAT v2 — consumed; contract-pty.md — read, INV consumes nothing from PTY)
Counts : tables 14 · DBF 219 (DBF-INV-001 … DBF-INV-219) · XM 13 (XM-INV-001 … XM-INV-013: 11 HARD-FK · 2 SOFT-READ) · intra-module FK 19 (composite with TENANT_ID) · tenant FK 14 · lookups seeded 6 keys / 34 values · number series 5 · notification templates 2 · settings defaults 2 · ADR 7 raised at P2 (ADR-INV-014 … ADR-INV-020)
Status : DRAFT — P2 pass 1 · repair round 2 · no question raised · 1 BLOCKED (ADR-INV-019 — its unblock condition is met by ADR-INV-020, which rewords SRS D-13 / D-23; structure complete and unchanged)
══════════════════════════════════════════════════════════════════

## 1 — Header

| Item | Value |
|---|---|
| Module | Inventory / المخزون — INV |
| Version | v1 (first version — the script is a full CREATE, not a migration) |
| Date | 2026-10-31 |
| Dialect | postgresql16 (profile.stack.db.target_dialect) — the only dialect emitted |
| Schema prefix | none — every object is unqualified (S-7); the given `erp-core` resolves the schema |
| Tenancy | every table carries `TENANT_ID BIGINT NOT NULL` with `FK_<TABLE>_TENANT → CORE_TENANT(ID)` and an index led by `TENANT_ID`; every UNIQUE (constraint or unique index) leads with `TENANT_ID`; intra-module FKs are composite `(TENANT_ID, <FK>)` (given `erp-core` tenancy rule "every table carries TENANT_ID"; ADR-INV-014) |
| Platform columns | `TENANT_ID` and `VERSION BIGINT NOT NULL DEFAULT 0` on every table — the column set of the given `AuditableEntity` (ADR-INV-014) |
| Migration | the script is the module's single Flyway application migration `V1002__inv_inventory.sql` (V1000+, after CAT `V1000` and PTY `V1001` — the executor confirms the number from the directory listing); it holds no `BEGIN` / `COMMIT` (Flyway owns the transaction) |
| PK generation | `identity` (profile.stack.db.pk_generation) — every PK column carries `GENERATED ALWAYS AS IDENTITY`; no PK sequence, no PK trigger |
| Identifier transformation | logical camelCase field name (SRS A3) → physical UPPER_SNAKE_CASE column: an underscore is inserted before every upper-case letter and the whole name is upper-cased (`warehousePk` → `WAREHOUSE_PK`, `onHandMeasureQty` → `ON_HAND_MEASURE_QTY`, `isActiveFl` → `IS_ACTIVE_FL`, `key` → `KEY`). Identifiers are written unquoted, so PostgreSQL stores them folded to lower case — one spelling per field, no quoted identifier anywhere. No identifier exceeds the 63-byte limit; none is a reserved word (`KEY` is non-reserved in PostgreSQL and is the given `MDL_LOOKUP_TYPE` column name too). |
| Table names | `INV_` + entity name in UPPER_SNAKE_CASE: INV_WAREHOUSE, INV_BRANCH, INV_STOCK_BALANCE, INV_STOCK_MOVE, INV_RESERVATION, INV_TRANSFER, INV_TRANSFER_LINE, INV_STOCK_COUNT, INV_STOCK_COUNT_LINE, INV_STOCK_ADJUSTMENT, INV_STOCK_ADJUSTMENT_LINE, INV_LOT, INV_STOCK_VALUATION, INV_ITEM_STOCK_SETTINGS |
| Types declared outside §4.1 | logical `date` (docDate, expiryDate, lotExpiryDate) → `DATE`; logical `number` (whole — ENT-INV-011.rowNo) → `INTEGER` (the syntax map has neither; ADR-INV-016) |
| Precision | quantity and measure `NUMERIC(18,3)`, unit and average cost `NUMERIC(18,4)`, value `NUMERIC(18,2)` — scales from SRS A3 / D-06, precision 18 (ADR-INV-016); lookup codes `VARCHAR(50)` (given `mdl` convention); currency `VARCHAR(3)` ISO 4217 (ADR-INV-015) |

### 1.1 Entry check

```
  srs             : ✓ present (INV v1, P1 — REQ 138 · AC 154 · ENT 14 · RULE 48 · SCR-REQ 12)
  registry-srs    : ✓ present
  contract        : ✓ present (CON-INV-001 … CON-INV-021; identifier type BIGINT for every entity)
  contracts?      : ✓ present (contract-cat.md — CON-CAT-001 Item, CON-CAT-002 Variant, CON-CAT-005 Barcode,
                    CON-CAT-006 Bundle component, identifier type BIGINT; contract-pty.md — not consumed)
  domain-profile STEERING : vocabulary verbatim; identifier rules (BIGINT identity keys; LOV codes from given mdl)
  project-registry        : no name fixed for INV tables by another module; given tables listed in engine §4 are not re-created
  Extracted               : 14 entities → 14 tables · 19 intra-module FKs ·
                            4 XM candidates (SRS A8 `module-dependencies`) → 13 XM records (one per HARD-FK column, one per SOFT-READ) ·
                            6 lookups owned (SRS A6) → 34 seed values · 1 lookup consumed (CAT unit of measure)
```

Given entities used and not re-created [C:C-GIVEN-1]: AppConfiguration (`CU_APP_CONFIGURATION`, `erp-core:modules.cu` — holds the capability flags, `INV_ALLOW_NEGATIVE_STOCK` and `INV_NEAR_EXPIRY_DAYS`; platform defaults seeded only, BLOCK 8e), LookupType / LookupValue (`MDL_LOOKUP_TYPE`, `MDL_LOOKUP_VALUE`, `erp-core:modules.mdl` — seeded only, BLOCK 8a / 8b), NumberSeries (`CORE_NUMBER_SERIES`, `erp-core:modules.sequence` — seeded only, BLOCK 8c), NotificationTemplate (`NOTIF_TEMPLATE`, `erp-core:modules.notif` — seeded only, BLOCK 8d), User / Role (`SEC_USER`, `SEC_ROLE` — read through `SecUserDirectoryApi`, no column), AuditEvent (`CORE_AUDIT_EVENT` — written through `AuditApi`), Tenant (`CORE_TENANT` — target of every `FK_<TABLE>_TENANT`, the one core FK the core documents).

---

## 2 — DB field traceability matrix (`DBF`)

The single canonical source of DBF → column → type → SRS origin. Downstream artifacts reference columns by DBF id only. Order per table: PK, the entity's own fields in SRS A3 order, its FK fields in SRS order, the standard fields of the entity kind (profile entity_defaults: master `code, nameAr, nameEn, isActiveFl`; transactional `docNo, docDate, statusCode` as SRS D-01 assigns them; config `key, valueAr, valueEn, isActiveFl`), the platform columns `tenantId`, `version` (given `AuditableEntity`, ADR-INV-014), the CM-8 `currencyCode` on monetary rows (ADR-INV-015), and the audit fields last (`profile: entity_defaults.<kind>`; the config entity ENT-INV-014 carries them through the given `AuditableEntity` and REQ-INV-077). Every standard and platform column's `traces` name the REQs that create or update the row.

```yaml name=dbf-matrix
rows:
  - {id: DBF-INV-001, table: INV_WAREHOUSE, column: WAREHOUSE_PK, type: "BIGINT", entity_field: ENT-INV-001.warehousePk, traces: [REQ-INV-001, REQ-INV-003, REQ-INV-009], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-002, table: INV_WAREHOUSE, column: IS_DEFAULT_FL, type: "BOOLEAN", entity_field: ENT-INV-001.isDefaultFl, traces: [REQ-INV-001, REQ-INV-006, REQ-INV-008], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-INV-003, table: INV_WAREHOUSE, column: BRANCH_FK, type: "BIGINT", entity_field: ENT-INV-001.branchFk, traces: [REQ-INV-004, REQ-INV-025, REQ-INV-031], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-004, table: INV_WAREHOUSE, column: CODE, type: "VARCHAR(20)", entity_field: ENT-INV-001.code, traces: [REQ-INV-003, REQ-INV-004], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-005, table: INV_WAREHOUSE, column: NAME_AR, type: "VARCHAR(100)", entity_field: ENT-INV-001.nameAr, traces: [REQ-INV-003, REQ-INV-004, REQ-INV-009], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-006, table: INV_WAREHOUSE, column: NAME_EN, type: "VARCHAR(100)", entity_field: ENT-INV-001.nameEn, traces: [REQ-INV-003, REQ-INV-004], nullable: "NULL", default: "—"}
  - {id: DBF-INV-007, table: INV_WAREHOUSE, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-INV-001.isActiveFl, traces: [REQ-INV-005, REQ-INV-007, REQ-INV-018], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-INV-008, table: INV_WAREHOUSE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-001.tenantId, traces: [REQ-INV-001, REQ-INV-003], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-009, table: INV_WAREHOUSE, column: VERSION, type: "BIGINT", entity_field: ENT-INV-001.version, traces: [REQ-INV-004, REQ-INV-008], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-010, table: INV_WAREHOUSE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-001.createdBy, traces: [REQ-INV-003, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-011, table: INV_WAREHOUSE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-001.createdAt, traces: [REQ-INV-003, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-012, table: INV_WAREHOUSE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-001.updatedBy, traces: [REQ-INV-004, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-013, table: INV_WAREHOUSE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-001.updatedAt, traces: [REQ-INV-004, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-014, table: INV_BRANCH, column: BRANCH_PK, type: "BIGINT", entity_field: ENT-INV-002.branchPk, traces: [REQ-INV-020, REQ-INV-022, REQ-INV-028], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-015, table: INV_BRANCH, column: IS_DEFAULT_FL, type: "BOOLEAN", entity_field: ENT-INV-002.isDefaultFl, traces: [REQ-INV-020, REQ-INV-025, REQ-INV-027], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-INV-016, table: INV_BRANCH, column: CODE, type: "VARCHAR(20)", entity_field: ENT-INV-002.code, traces: [REQ-INV-022, REQ-INV-023], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-017, table: INV_BRANCH, column: NAME_AR, type: "VARCHAR(100)", entity_field: ENT-INV-002.nameAr, traces: [REQ-INV-022, REQ-INV-023, REQ-INV-028], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-018, table: INV_BRANCH, column: NAME_EN, type: "VARCHAR(100)", entity_field: ENT-INV-002.nameEn, traces: [REQ-INV-022, REQ-INV-023], nullable: "NULL", default: "—"}
  - {id: DBF-INV-019, table: INV_BRANCH, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-INV-002.isActiveFl, traces: [REQ-INV-024, REQ-INV-026], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-INV-020, table: INV_BRANCH, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-002.tenantId, traces: [REQ-INV-020, REQ-INV-022], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-021, table: INV_BRANCH, column: VERSION, type: "BIGINT", entity_field: ENT-INV-002.version, traces: [REQ-INV-023, REQ-INV-027], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-022, table: INV_BRANCH, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-002.createdBy, traces: [REQ-INV-022, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-023, table: INV_BRANCH, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-002.createdAt, traces: [REQ-INV-022, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-024, table: INV_BRANCH, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-002.updatedBy, traces: [REQ-INV-023, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-025, table: INV_BRANCH, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-002.updatedAt, traces: [REQ-INV-023, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-026, table: INV_STOCK_BALANCE, column: STOCK_BALANCE_PK, type: "BIGINT", entity_field: ENT-INV-003.stockBalancePk, traces: [REQ-INV-035, REQ-INV-039], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-027, table: INV_STOCK_BALANCE, column: ON_HAND_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-003.onHandQty, traces: [REQ-INV-036, REQ-INV-038, REQ-INV-067], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-028, table: INV_STOCK_BALANCE, column: ON_HAND_MEASURE_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-003.onHandMeasureQty, traces: [REQ-INV-121, REQ-INV-127], nullable: "NULL", default: "—"}
  - {id: DBF-INV-029, table: INV_STOCK_BALANCE, column: LAST_MOVE_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-003.lastMoveAt, traces: [REQ-INV-036], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-030, table: INV_STOCK_BALANCE, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-003.variantFk, traces: [REQ-INV-035, REQ-INV-040], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-031, table: INV_STOCK_BALANCE, column: WAREHOUSE_FK, type: "BIGINT", entity_field: ENT-INV-003.warehouseFk, traces: [REQ-INV-006, REQ-INV-035, REQ-INV-039], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-032, table: INV_STOCK_BALANCE, column: LOT_FK, type: "BIGINT", entity_field: ENT-INV-003.lotFk, traces: [REQ-INV-084, REQ-INV-109, REQ-INV-126], nullable: "NULL", default: "—"}
  - {id: DBF-INV-033, table: INV_STOCK_BALANCE, column: DOC_DATE, type: "DATE", entity_field: ENT-INV-003.docDate, traces: [REQ-INV-036], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-034, table: INV_STOCK_BALANCE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-003.tenantId, traces: [REQ-INV-035, REQ-INV-036], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-035, table: INV_STOCK_BALANCE, column: VERSION, type: "BIGINT", entity_field: ENT-INV-003.version, traces: [REQ-INV-036, REQ-INV-037], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-036, table: INV_STOCK_BALANCE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-003.createdBy, traces: [REQ-INV-036], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-037, table: INV_STOCK_BALANCE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-003.createdAt, traces: [REQ-INV-036], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-038, table: INV_STOCK_BALANCE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-003.updatedBy, traces: [REQ-INV-036], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-039, table: INV_STOCK_BALANCE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-003.updatedAt, traces: [REQ-INV-036], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-040, table: INV_STOCK_MOVE, column: STOCK_MOVE_PK, type: "BIGINT", entity_field: ENT-INV-004.stockMovePk, traces: [REQ-INV-055, REQ-INV-057], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-041, table: INV_STOCK_MOVE, column: MOVE_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-INV-004.moveTypeCode, traces: [REQ-INV-051, REQ-INV-052, REQ-INV-075], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-042, table: INV_STOCK_MOVE, column: QUANTITY, type: "NUMERIC(18,3)", entity_field: ENT-INV-004.quantity, traces: [REQ-INV-036, REQ-INV-051, REQ-INV-052], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-043, table: INV_STOCK_MOVE, column: MEASURE_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-004.measureQty, traces: [REQ-INV-120, REQ-INV-122, REQ-INV-123], nullable: "NULL", default: "—"}
  - {id: DBF-INV-044, table: INV_STOCK_MOVE, column: UNIT_COST, type: "NUMERIC(18,4)", entity_field: ENT-INV-004.unitCost, traces: [REQ-INV-051, REQ-INV-061, REQ-INV-064], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-045, table: INV_STOCK_MOVE, column: MOVE_VALUE, type: "NUMERIC(18,2)", entity_field: ENT-INV-004.moveValue, traces: [REQ-INV-051, REQ-INV-057], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-046, table: INV_STOCK_MOVE, column: BALANCE_AFTER_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-004.balanceAfterQty, traces: [REQ-INV-076], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-047, table: INV_STOCK_MOVE, column: SOURCE_DOC_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-INV-004.sourceDocTypeCode, traces: [REQ-INV-056, REQ-INV-075], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-048, table: INV_STOCK_MOVE, column: SOURCE_DOC_REF, type: "BIGINT", entity_field: ENT-INV-004.sourceDocRef, traces: [REQ-INV-056, REQ-INV-081], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-049, table: INV_STOCK_MOVE, column: SOURCE_DOC_NO, type: "VARCHAR(30)", entity_field: ENT-INV-004.sourceDocNo, traces: [REQ-INV-056, REQ-INV-075], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-050, table: INV_STOCK_MOVE, column: SOURCE_LINE_REF, type: "BIGINT", entity_field: ENT-INV-004.sourceLineRef, traces: [REQ-INV-056], nullable: "NULL", default: "—"}
  - {id: DBF-INV-051, table: INV_STOCK_MOVE, column: OFFLINE_FL, type: "BOOLEAN", entity_field: ENT-INV-004.offlineFl, traces: [REQ-INV-069], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-INV-052, table: INV_STOCK_MOVE, column: IDEMPOTENCY_KEY, type: "VARCHAR(64)", entity_field: ENT-INV-004.idempotencyKey, traces: [REQ-INV-058], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-053, table: INV_STOCK_MOVE, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-004.variantFk, traces: [REQ-INV-051, REQ-INV-052, REQ-INV-075], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-054, table: INV_STOCK_MOVE, column: WAREHOUSE_FK, type: "BIGINT", entity_field: ENT-INV-004.warehouseFk, traces: [REQ-INV-013, REQ-INV-016, REQ-INV-017], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-055, table: INV_STOCK_MOVE, column: LOT_FK, type: "BIGINT", entity_field: ENT-INV-004.lotFk, traces: [REQ-INV-105, REQ-INV-110, REQ-INV-114], nullable: "NULL", default: "—"}
  - {id: DBF-INV-056, table: INV_STOCK_MOVE, column: BUNDLE_VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-004.bundleVariantFk, traces: [REQ-INV-053], nullable: "NULL", default: "—"}
  - {id: DBF-INV-057, table: INV_STOCK_MOVE, column: RESERVATION_FK, type: "BIGINT", entity_field: ENT-INV-004.reservationFk, traces: [REQ-INV-047, REQ-INV-048], nullable: "NULL", default: "—"}
  - {id: DBF-INV-058, table: INV_STOCK_MOVE, column: DOC_DATE, type: "DATE", entity_field: ENT-INV-004.docDate, traces: [REQ-INV-057, REQ-INV-075], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-059, table: INV_STOCK_MOVE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-004.tenantId, traces: [REQ-INV-051, REQ-INV-052], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-060, table: INV_STOCK_MOVE, column: VERSION, type: "BIGINT", entity_field: ENT-INV-004.version, traces: [REQ-INV-055], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-061, table: INV_STOCK_MOVE, column: CURRENCY_CODE, type: "VARCHAR(3)", entity_field: ENT-INV-004.currencyCode, traces: [REQ-INV-057, REQ-INV-061], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-062, table: INV_STOCK_MOVE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-004.createdBy, traces: [REQ-INV-051, REQ-INV-052], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-063, table: INV_STOCK_MOVE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-004.createdAt, traces: [REQ-INV-051, REQ-INV-052], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-064, table: INV_STOCK_MOVE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-004.updatedBy, traces: [REQ-INV-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-065, table: INV_STOCK_MOVE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-004.updatedAt, traces: [REQ-INV-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-066, table: INV_RESERVATION, column: RESERVATION_PK, type: "BIGINT", entity_field: ENT-INV-005.reservationPk, traces: [REQ-INV-043, REQ-INV-050], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-067, table: INV_RESERVATION, column: ORIGINAL_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-005.originalQty, traces: [REQ-INV-043, REQ-INV-045], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-068, table: INV_RESERVATION, column: RESERVED_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-005.reservedQty, traces: [REQ-INV-038, REQ-INV-043, REQ-INV-046, REQ-INV-047], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-069, table: INV_RESERVATION, column: SHORTFALL_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-005.shortfallQty, traces: [REQ-INV-045, REQ-INV-050], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-070, table: INV_RESERVATION, column: SOURCE_DOC_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-INV-005.sourceDocTypeCode, traces: [REQ-INV-043, REQ-INV-050], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-071, table: INV_RESERVATION, column: SOURCE_DOC_REF, type: "BIGINT", entity_field: ENT-INV-005.sourceDocRef, traces: [REQ-INV-043, REQ-INV-050], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-072, table: INV_RESERVATION, column: SOURCE_DOC_NO, type: "VARCHAR(30)", entity_field: ENT-INV-005.sourceDocNo, traces: [REQ-INV-043, REQ-INV-050], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-073, table: INV_RESERVATION, column: SOURCE_LINE_REF, type: "BIGINT", entity_field: ENT-INV-005.sourceLineRef, traces: [REQ-INV-043], nullable: "NULL", default: "—"}
  - {id: DBF-INV-074, table: INV_RESERVATION, column: CLOSED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-005.closedAt, traces: [REQ-INV-046, REQ-INV-047], nullable: "NULL", default: "—"}
  - {id: DBF-INV-075, table: INV_RESERVATION, column: IDEMPOTENCY_KEY, type: "VARCHAR(64)", entity_field: ENT-INV-005.idempotencyKey, traces: [REQ-INV-058], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-076, table: INV_RESERVATION, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-005.variantFk, traces: [REQ-INV-043, REQ-INV-049], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-077, table: INV_RESERVATION, column: WAREHOUSE_FK, type: "BIGINT", entity_field: ENT-INV-005.warehouseFk, traces: [REQ-INV-006, REQ-INV-013, REQ-INV-043], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-078, table: INV_RESERVATION, column: BUNDLE_VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-005.bundleVariantFk, traces: [REQ-INV-049], nullable: "NULL", default: "—"}
  - {id: DBF-INV-079, table: INV_RESERVATION, column: DOC_DATE, type: "DATE", entity_field: ENT-INV-005.docDate, traces: [REQ-INV-043], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-080, table: INV_RESERVATION, column: STATUS_CODE, type: "VARCHAR(50)", entity_field: ENT-INV-005.statusCode, traces: [REQ-INV-043, REQ-INV-046, REQ-INV-047, REQ-INV-048], nullable: "NOT NULL", default: "'ACTIVE'"}
  - {id: DBF-INV-081, table: INV_RESERVATION, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-005.tenantId, traces: [REQ-INV-043], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-082, table: INV_RESERVATION, column: VERSION, type: "BIGINT", entity_field: ENT-INV-005.version, traces: [REQ-INV-046, REQ-INV-047], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-083, table: INV_RESERVATION, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-005.createdBy, traces: [REQ-INV-043], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-084, table: INV_RESERVATION, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-005.createdAt, traces: [REQ-INV-043], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-085, table: INV_RESERVATION, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-005.updatedBy, traces: [REQ-INV-046, REQ-INV-047], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-086, table: INV_RESERVATION, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-005.updatedAt, traces: [REQ-INV-046, REQ-INV-047], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-087, table: INV_TRANSFER, column: TRANSFER_PK, type: "BIGINT", entity_field: ENT-INV-006.transferPk, traces: [REQ-INV-078, REQ-INV-086], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-088, table: INV_TRANSFER, column: NOTE_TEXT, type: "VARCHAR(500)", entity_field: ENT-INV-006.noteText, traces: [REQ-INV-078, REQ-INV-079], nullable: "NULL", default: "—"}
  - {id: DBF-INV-089, table: INV_TRANSFER, column: POSTED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-006.postedAt, traces: [REQ-INV-080], nullable: "NULL", default: "—"}
  - {id: DBF-INV-090, table: INV_TRANSFER, column: FROM_WAREHOUSE_FK, type: "BIGINT", entity_field: ENT-INV-006.fromWarehouseFk, traces: [REQ-INV-078, REQ-INV-082, REQ-INV-086], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-091, table: INV_TRANSFER, column: TO_WAREHOUSE_FK, type: "BIGINT", entity_field: ENT-INV-006.toWarehouseFk, traces: [REQ-INV-078, REQ-INV-082, REQ-INV-086], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-092, table: INV_TRANSFER, column: DOC_NO, type: "VARCHAR(30)", entity_field: ENT-INV-006.docNo, traces: [REQ-INV-078, REQ-INV-086], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-093, table: INV_TRANSFER, column: DOC_DATE, type: "DATE", entity_field: ENT-INV-006.docDate, traces: [REQ-INV-078, REQ-INV-086], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-094, table: INV_TRANSFER, column: STATUS_CODE, type: "VARCHAR(50)", entity_field: ENT-INV-006.statusCode, traces: [REQ-INV-078, REQ-INV-080, REQ-INV-081, REQ-INV-085], nullable: "NOT NULL", default: "'DRAFT'"}
  - {id: DBF-INV-095, table: INV_TRANSFER, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-006.tenantId, traces: [REQ-INV-078], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-096, table: INV_TRANSFER, column: VERSION, type: "BIGINT", entity_field: ENT-INV-006.version, traces: [REQ-INV-079, REQ-INV-080], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-097, table: INV_TRANSFER, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-006.createdBy, traces: [REQ-INV-078, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-098, table: INV_TRANSFER, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-006.createdAt, traces: [REQ-INV-078, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-099, table: INV_TRANSFER, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-006.updatedBy, traces: [REQ-INV-079, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-100, table: INV_TRANSFER, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-006.updatedAt, traces: [REQ-INV-079, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-101, table: INV_TRANSFER_LINE, column: TRANSFER_LINE_PK, type: "BIGINT", entity_field: ENT-INV-007.transferLinePk, traces: [REQ-INV-078, REQ-INV-079], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-102, table: INV_TRANSFER_LINE, column: QUANTITY, type: "NUMERIC(18,3)", entity_field: ENT-INV-007.quantity, traces: [REQ-INV-078, REQ-INV-080], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-103, table: INV_TRANSFER_LINE, column: MEASURE_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-007.measureQty, traces: [REQ-INV-122], nullable: "NULL", default: "—"}
  - {id: DBF-INV-104, table: INV_TRANSFER_LINE, column: UNIT_COST, type: "NUMERIC(18,4)", entity_field: ENT-INV-007.unitCost, traces: [REQ-INV-062, REQ-INV-080], nullable: "NULL", default: "—"}
  - {id: DBF-INV-105, table: INV_TRANSFER_LINE, column: TRANSFER_FK, type: "BIGINT", entity_field: ENT-INV-007.transferFk, traces: [REQ-INV-078, REQ-INV-079], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-106, table: INV_TRANSFER_LINE, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-007.variantFk, traces: [REQ-INV-078, REQ-INV-080], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-107, table: INV_TRANSFER_LINE, column: LOT_FK, type: "BIGINT", entity_field: ENT-INV-007.lotFk, traces: [REQ-INV-084], nullable: "NULL", default: "—"}
  - {id: DBF-INV-108, table: INV_TRANSFER_LINE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-007.tenantId, traces: [REQ-INV-078], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-109, table: INV_TRANSFER_LINE, column: VERSION, type: "BIGINT", entity_field: ENT-INV-007.version, traces: [REQ-INV-079], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-110, table: INV_TRANSFER_LINE, column: CURRENCY_CODE, type: "VARCHAR(3)", entity_field: ENT-INV-007.currencyCode, traces: [REQ-INV-062, REQ-INV-080], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-111, table: INV_TRANSFER_LINE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-007.createdBy, traces: [REQ-INV-078], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-112, table: INV_TRANSFER_LINE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-007.createdAt, traces: [REQ-INV-078], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-113, table: INV_TRANSFER_LINE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-007.updatedBy, traces: [REQ-INV-079], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-114, table: INV_TRANSFER_LINE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-007.updatedAt, traces: [REQ-INV-079], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-115, table: INV_STOCK_COUNT, column: STOCK_COUNT_PK, type: "BIGINT", entity_field: ENT-INV-008.stockCountPk, traces: [REQ-INV-087, REQ-INV-095], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-116, table: INV_STOCK_COUNT, column: NOTE_TEXT, type: "VARCHAR(500)", entity_field: ENT-INV-008.noteText, traces: [REQ-INV-087], nullable: "NULL", default: "—"}
  - {id: DBF-INV-117, table: INV_STOCK_COUNT, column: POSTED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-008.postedAt, traces: [REQ-INV-092], nullable: "NULL", default: "—"}
  - {id: DBF-INV-118, table: INV_STOCK_COUNT, column: WAREHOUSE_FK, type: "BIGINT", entity_field: ENT-INV-008.warehouseFk, traces: [REQ-INV-013, REQ-INV-087, REQ-INV-095], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-119, table: INV_STOCK_COUNT, column: ADJUSTMENT_FK, type: "BIGINT", entity_field: ENT-INV-008.adjustmentFk, traces: [REQ-INV-092, REQ-INV-094], nullable: "NULL", default: "—"}
  - {id: DBF-INV-120, table: INV_STOCK_COUNT, column: DOC_NO, type: "VARCHAR(30)", entity_field: ENT-INV-008.docNo, traces: [REQ-INV-087, REQ-INV-095], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-121, table: INV_STOCK_COUNT, column: DOC_DATE, type: "DATE", entity_field: ENT-INV-008.docDate, traces: [REQ-INV-087, REQ-INV-095], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-122, table: INV_STOCK_COUNT, column: STATUS_CODE, type: "VARCHAR(50)", entity_field: ENT-INV-008.statusCode, traces: [REQ-INV-087, REQ-INV-092, REQ-INV-094, REQ-INV-096], nullable: "NOT NULL", default: "'DRAFT'"}
  - {id: DBF-INV-123, table: INV_STOCK_COUNT, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-008.tenantId, traces: [REQ-INV-087], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-124, table: INV_STOCK_COUNT, column: VERSION, type: "BIGINT", entity_field: ENT-INV-008.version, traces: [REQ-INV-092], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-125, table: INV_STOCK_COUNT, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-008.createdBy, traces: [REQ-INV-087, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-126, table: INV_STOCK_COUNT, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-008.createdAt, traces: [REQ-INV-087, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-127, table: INV_STOCK_COUNT, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-008.updatedBy, traces: [REQ-INV-092, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-128, table: INV_STOCK_COUNT, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-008.updatedAt, traces: [REQ-INV-092, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-129, table: INV_STOCK_COUNT_LINE, column: STOCK_COUNT_LINE_PK, type: "BIGINT", entity_field: ENT-INV-009.stockCountLinePk, traces: [REQ-INV-088, REQ-INV-089], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-130, table: INV_STOCK_COUNT_LINE, column: COUNTED_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-009.countedQty, traces: [REQ-INV-088, REQ-INV-089], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-131, table: INV_STOCK_COUNT_LINE, column: COUNTED_MEASURE_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-009.countedMeasureQty, traces: [REQ-INV-088, REQ-INV-122], nullable: "NULL", default: "—"}
  - {id: DBF-INV-132, table: INV_STOCK_COUNT_LINE, column: SYSTEM_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-009.systemQty, traces: [REQ-INV-092], nullable: "NULL", default: "—"}
  - {id: DBF-INV-133, table: INV_STOCK_COUNT_LINE, column: DIFFERENCE_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-009.differenceQty, traces: [REQ-INV-092], nullable: "NULL", default: "—"}
  - {id: DBF-INV-134, table: INV_STOCK_COUNT_LINE, column: STOCK_COUNT_FK, type: "BIGINT", entity_field: ENT-INV-009.stockCountFk, traces: [REQ-INV-088, REQ-INV-093], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-135, table: INV_STOCK_COUNT_LINE, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-009.variantFk, traces: [REQ-INV-088, REQ-INV-091], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-136, table: INV_STOCK_COUNT_LINE, column: LOT_FK, type: "BIGINT", entity_field: ENT-INV-009.lotFk, traces: [REQ-INV-088], nullable: "NULL", default: "—"}
  - {id: DBF-INV-137, table: INV_STOCK_COUNT_LINE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-009.tenantId, traces: [REQ-INV-088], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-138, table: INV_STOCK_COUNT_LINE, column: VERSION, type: "BIGINT", entity_field: ENT-INV-009.version, traces: [REQ-INV-089], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-139, table: INV_STOCK_COUNT_LINE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-009.createdBy, traces: [REQ-INV-088], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-140, table: INV_STOCK_COUNT_LINE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-009.createdAt, traces: [REQ-INV-088], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-141, table: INV_STOCK_COUNT_LINE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-009.updatedBy, traces: [REQ-INV-089], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-142, table: INV_STOCK_COUNT_LINE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-009.updatedAt, traces: [REQ-INV-089], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-143, table: INV_STOCK_ADJUSTMENT, column: STOCK_ADJUSTMENT_PK, type: "BIGINT", entity_field: ENT-INV-010.stockAdjustmentPk, traces: [REQ-INV-097, REQ-INV-104], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-144, table: INV_STOCK_ADJUSTMENT, column: REASON_CODE, type: "VARCHAR(50)", entity_field: ENT-INV-010.reasonCode, traces: [REQ-INV-070, REQ-INV-101, REQ-INV-104], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-145, table: INV_STOCK_ADJUSTMENT, column: NOTE_TEXT, type: "VARCHAR(500)", entity_field: ENT-INV-010.noteText, traces: [REQ-INV-098], nullable: "NULL", default: "—"}
  - {id: DBF-INV-146, table: INV_STOCK_ADJUSTMENT, column: POSTED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-010.postedAt, traces: [REQ-INV-099], nullable: "NULL", default: "—"}
  - {id: DBF-INV-147, table: INV_STOCK_ADJUSTMENT, column: WAREHOUSE_FK, type: "BIGINT", entity_field: ENT-INV-010.warehouseFk, traces: [REQ-INV-010, REQ-INV-013, REQ-INV-097], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-148, table: INV_STOCK_ADJUSTMENT, column: SOURCE_COUNT_FK, type: "BIGINT", entity_field: ENT-INV-010.sourceCountFk, traces: [REQ-INV-092], nullable: "NULL", default: "—"}
  - {id: DBF-INV-149, table: INV_STOCK_ADJUSTMENT, column: DOC_NO, type: "VARCHAR(30)", entity_field: ENT-INV-010.docNo, traces: [REQ-INV-097, REQ-INV-104], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-150, table: INV_STOCK_ADJUSTMENT, column: DOC_DATE, type: "DATE", entity_field: ENT-INV-010.docDate, traces: [REQ-INV-097, REQ-INV-104], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-151, table: INV_STOCK_ADJUSTMENT, column: STATUS_CODE, type: "VARCHAR(50)", entity_field: ENT-INV-010.statusCode, traces: [REQ-INV-097, REQ-INV-099, REQ-INV-100, REQ-INV-103], nullable: "NOT NULL", default: "'DRAFT'"}
  - {id: DBF-INV-152, table: INV_STOCK_ADJUSTMENT, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-010.tenantId, traces: [REQ-INV-097], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-153, table: INV_STOCK_ADJUSTMENT, column: VERSION, type: "BIGINT", entity_field: ENT-INV-010.version, traces: [REQ-INV-098, REQ-INV-099], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-154, table: INV_STOCK_ADJUSTMENT, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-010.createdBy, traces: [REQ-INV-097, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-155, table: INV_STOCK_ADJUSTMENT, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-010.createdAt, traces: [REQ-INV-097, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-156, table: INV_STOCK_ADJUSTMENT, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-010.updatedBy, traces: [REQ-INV-098, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-157, table: INV_STOCK_ADJUSTMENT, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-010.updatedAt, traces: [REQ-INV-098, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-158, table: INV_STOCK_ADJUSTMENT_LINE, column: STOCK_ADJUSTMENT_LINE_PK, type: "BIGINT", entity_field: ENT-INV-011.stockAdjustmentLinePk, traces: [REQ-INV-097, REQ-INV-098], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-159, table: INV_STOCK_ADJUSTMENT_LINE, column: ROW_NO, type: "INTEGER", entity_field: ENT-INV-011.rowNo, traces: [REQ-INV-071, REQ-INV-072], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-160, table: INV_STOCK_ADJUSTMENT_LINE, column: LOT_NO, type: "VARCHAR(40)", entity_field: ENT-INV-011.lotNo, traces: [REQ-INV-105, REQ-INV-106], nullable: "NULL", default: "—"}
  - {id: DBF-INV-161, table: INV_STOCK_ADJUSTMENT_LINE, column: LOT_EXPIRY_DATE, type: "DATE", entity_field: ENT-INV-011.lotExpiryDate, traces: [REQ-INV-105, REQ-INV-107], nullable: "NULL", default: "—"}
  - {id: DBF-INV-162, table: INV_STOCK_ADJUSTMENT_LINE, column: QUANTITY_CHANGE, type: "NUMERIC(18,3)", entity_field: ENT-INV-011.quantityChange, traces: [REQ-INV-070, REQ-INV-099], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-163, table: INV_STOCK_ADJUSTMENT_LINE, column: MEASURE_QTY_CHANGE, type: "NUMERIC(18,3)", entity_field: ENT-INV-011.measureQtyChange, traces: [REQ-INV-123], nullable: "NULL", default: "—"}
  - {id: DBF-INV-164, table: INV_STOCK_ADJUSTMENT_LINE, column: UNIT_COST, type: "NUMERIC(18,4)", entity_field: ENT-INV-011.unitCost, traces: [REQ-INV-070, REQ-INV-074, REQ-INV-102], nullable: "NULL", default: "—"}
  - {id: DBF-INV-165, table: INV_STOCK_ADJUSTMENT_LINE, column: LINE_VALUE, type: "NUMERIC(18,2)", entity_field: ENT-INV-011.lineValue, traces: [REQ-INV-099], nullable: "NULL", default: "—"}
  - {id: DBF-INV-166, table: INV_STOCK_ADJUSTMENT_LINE, column: STOCK_ADJUSTMENT_FK, type: "BIGINT", entity_field: ENT-INV-011.stockAdjustmentFk, traces: [REQ-INV-097, REQ-INV-098], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-167, table: INV_STOCK_ADJUSTMENT_LINE, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-011.variantFk, traces: [REQ-INV-072, REQ-INV-097], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-168, table: INV_STOCK_ADJUSTMENT_LINE, column: LOT_FK, type: "BIGINT", entity_field: ENT-INV-011.lotFk, traces: [REQ-INV-106, REQ-INV-114], nullable: "NULL", default: "—"}
  - {id: DBF-INV-169, table: INV_STOCK_ADJUSTMENT_LINE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-011.tenantId, traces: [REQ-INV-097], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-170, table: INV_STOCK_ADJUSTMENT_LINE, column: VERSION, type: "BIGINT", entity_field: ENT-INV-011.version, traces: [REQ-INV-098], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-171, table: INV_STOCK_ADJUSTMENT_LINE, column: CURRENCY_CODE, type: "VARCHAR(3)", entity_field: ENT-INV-011.currencyCode, traces: [REQ-INV-070, REQ-INV-099], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-172, table: INV_STOCK_ADJUSTMENT_LINE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-011.createdBy, traces: [REQ-INV-097, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-173, table: INV_STOCK_ADJUSTMENT_LINE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-011.createdAt, traces: [REQ-INV-097, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-174, table: INV_STOCK_ADJUSTMENT_LINE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-011.updatedBy, traces: [REQ-INV-098, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-175, table: INV_STOCK_ADJUSTMENT_LINE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-011.updatedAt, traces: [REQ-INV-098, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-176, table: INV_LOT, column: LOT_PK, type: "BIGINT", entity_field: ENT-INV-012.lotPk, traces: [REQ-INV-106, REQ-INV-109], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-177, table: INV_LOT, column: EXPIRY_DATE, type: "DATE", entity_field: ENT-INV-012.expiryDate, traces: [REQ-INV-105, REQ-INV-108, REQ-INV-113, REQ-INV-116], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-178, table: INV_LOT, column: FIRST_RECEIVED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-012.firstReceivedAt, traces: [REQ-INV-106], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-179, table: INV_LOT, column: NEAR_EXPIRY_NOTIFIED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-012.nearExpiryNotifiedAt, traces: [REQ-INV-117], nullable: "NULL", default: "—"}
  - {id: DBF-INV-180, table: INV_LOT, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-012.variantFk, traces: [REQ-INV-106, REQ-INV-109], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-181, table: INV_LOT, column: CODE, type: "VARCHAR(40)", entity_field: ENT-INV-012.code, traces: [REQ-INV-106, REQ-INV-107, REQ-INV-109], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-182, table: INV_LOT, column: NAME_AR, type: "VARCHAR(100)", entity_field: ENT-INV-012.nameAr, traces: [REQ-INV-108, REQ-INV-109], nullable: "NULL", default: "—"}
  - {id: DBF-INV-183, table: INV_LOT, column: NAME_EN, type: "VARCHAR(100)", entity_field: ENT-INV-012.nameEn, traces: [REQ-INV-108, REQ-INV-109], nullable: "NULL", default: "—"}
  - {id: DBF-INV-184, table: INV_LOT, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-INV-012.isActiveFl, traces: [REQ-INV-106, REQ-INV-109], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-INV-185, table: INV_LOT, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-012.tenantId, traces: [REQ-INV-106], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-186, table: INV_LOT, column: VERSION, type: "BIGINT", entity_field: ENT-INV-012.version, traces: [REQ-INV-108, REQ-INV-117], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-187, table: INV_LOT, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-012.createdBy, traces: [REQ-INV-106, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-188, table: INV_LOT, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-012.createdAt, traces: [REQ-INV-106, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-189, table: INV_LOT, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-012.updatedBy, traces: [REQ-INV-108, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-190, table: INV_LOT, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-012.updatedAt, traces: [REQ-INV-108, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-191, table: INV_STOCK_VALUATION, column: STOCK_VALUATION_PK, type: "BIGINT", entity_field: ENT-INV-013.stockValuationPk, traces: [REQ-INV-059, REQ-INV-063], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-192, table: INV_STOCK_VALUATION, column: TOTAL_ON_HAND_QTY, type: "NUMERIC(18,3)", entity_field: ENT-INV-013.totalOnHandQty, traces: [REQ-INV-059, REQ-INV-060, REQ-INV-063], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-193, table: INV_STOCK_VALUATION, column: AVERAGE_UNIT_COST, type: "NUMERIC(18,4)", entity_field: ENT-INV-013.averageUnitCost, traces: [REQ-INV-059, REQ-INV-060, REQ-INV-061], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-194, table: INV_STOCK_VALUATION, column: STOCK_VALUE, type: "NUMERIC(18,2)", entity_field: ENT-INV-013.stockValue, traces: [REQ-INV-059, REQ-INV-062, REQ-INV-063], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-195, table: INV_STOCK_VALUATION, column: LAST_MOVE_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-013.lastMoveAt, traces: [REQ-INV-059], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-196, table: INV_STOCK_VALUATION, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-INV-013.variantFk, traces: [REQ-INV-059, REQ-INV-063], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-197, table: INV_STOCK_VALUATION, column: DOC_DATE, type: "DATE", entity_field: ENT-INV-013.docDate, traces: [REQ-INV-059], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-198, table: INV_STOCK_VALUATION, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-013.tenantId, traces: [REQ-INV-059], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-199, table: INV_STOCK_VALUATION, column: VERSION, type: "BIGINT", entity_field: ENT-INV-013.version, traces: [REQ-INV-037, REQ-INV-059], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-200, table: INV_STOCK_VALUATION, column: CURRENCY_CODE, type: "VARCHAR(3)", entity_field: ENT-INV-013.currencyCode, traces: [REQ-INV-059, REQ-INV-063], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-201, table: INV_STOCK_VALUATION, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-013.createdBy, traces: [REQ-INV-059], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-202, table: INV_STOCK_VALUATION, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-013.createdAt, traces: [REQ-INV-059], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-203, table: INV_STOCK_VALUATION, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-013.updatedBy, traces: [REQ-INV-059], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-204, table: INV_STOCK_VALUATION, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-013.updatedAt, traces: [REQ-INV-059], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-205, table: INV_ITEM_STOCK_SETTINGS, column: ITEM_STOCK_SETTINGS_PK, type: "BIGINT", entity_field: ENT-INV-014.itemStockSettingsPk, traces: [REQ-INV-124, REQ-INV-129], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-INV-206, table: INV_ITEM_STOCK_SETTINGS, column: LOT_TRACKED_FL, type: "BOOLEAN", entity_field: ENT-INV-014.lotTrackedFl, traces: [REQ-INV-124, REQ-INV-126, REQ-INV-131], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-INV-207, table: INV_ITEM_STOCK_SETTINGS, column: DUAL_QUANTITY_FL, type: "BOOLEAN", entity_field: ENT-INV-014.dualQuantityFl, traces: [REQ-INV-125, REQ-INV-127, REQ-INV-131], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-INV-208, table: INV_ITEM_STOCK_SETTINGS, column: MEASURE_UOM_CODE, type: "VARCHAR(50)", entity_field: ENT-INV-014.measureUomCode, traces: [REQ-INV-125, REQ-INV-129], nullable: "NULL", default: "—"}
  - {id: DBF-INV-209, table: INV_ITEM_STOCK_SETTINGS, column: ITEM_FK, type: "BIGINT", entity_field: ENT-INV-014.itemFk, traces: [REQ-INV-124, REQ-INV-125, REQ-INV-129], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-210, table: INV_ITEM_STOCK_SETTINGS, column: KEY, type: "VARCHAR(40)", entity_field: ENT-INV-014.key, traces: [REQ-INV-124], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-211, table: INV_ITEM_STOCK_SETTINGS, column: VALUE_AR, type: "VARCHAR(100)", entity_field: ENT-INV-014.valueAr, traces: [REQ-INV-124, REQ-INV-125], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-212, table: INV_ITEM_STOCK_SETTINGS, column: VALUE_EN, type: "VARCHAR(100)", entity_field: ENT-INV-014.valueEn, traces: [REQ-INV-124, REQ-INV-125], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-213, table: INV_ITEM_STOCK_SETTINGS, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-INV-014.isActiveFl, traces: [REQ-INV-129], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-INV-214, table: INV_ITEM_STOCK_SETTINGS, column: TENANT_ID, type: "BIGINT", entity_field: ENT-INV-014.tenantId, traces: [REQ-INV-124], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-215, table: INV_ITEM_STOCK_SETTINGS, column: VERSION, type: "BIGINT", entity_field: ENT-INV-014.version, traces: [REQ-INV-126, REQ-INV-127], nullable: "NOT NULL", default: "0"}
  - {id: DBF-INV-216, table: INV_ITEM_STOCK_SETTINGS, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-014.createdBy, traces: [REQ-INV-124, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-217, table: INV_ITEM_STOCK_SETTINGS, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-014.createdAt, traces: [REQ-INV-124, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-218, table: INV_ITEM_STOCK_SETTINGS, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-INV-014.updatedBy, traces: [REQ-INV-125, REQ-INV-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-INV-219, table: INV_ITEM_STOCK_SETTINGS, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-INV-014.updatedAt, traces: [REQ-INV-125, REQ-INV-077], nullable: "NOT NULL", default: "—"}
```

Total: 219 DBF ids across 14 tables (INV_WAREHOUSE 13 · INV_BRANCH 12 · INV_STOCK_BALANCE 14 · INV_STOCK_MOVE 26 · INV_RESERVATION 21 · INV_TRANSFER 14 · INV_TRANSFER_LINE 14 · INV_STOCK_COUNT 14 · INV_STOCK_COUNT_LINE 14 · INV_STOCK_ADJUSTMENT 15 · INV_STOCK_ADJUSTMENT_LINE 18 · INV_LOT 15 · INV_STOCK_VALUATION 14 · INV_ITEM_STOCK_SETTINGS 15).

Every SRS A3 field is a column; no A3 field is left out. Derived values that SRS A3 marks "derived in every read (not held)" — ENT-INV-003 `reservedQty`, `availableQty` (REQ-INV-038) — are not columns.

Every RULE `Data source` field resolves to a column of this script [C:C6.9]:

| ENT | Field → DBF |
|---|---|
| ENT-INV-001 | code → DBF-INV-004 · nameAr → 005 · isDefaultFl → 002 · isActiveFl → 007 · branchFk → 003 |
| ENT-INV-002 | code → DBF-INV-016 · nameAr → 017 · isDefaultFl → 015 · isActiveFl → 019 |
| ENT-INV-003 | warehouseFk → DBF-INV-031 · onHandQty → 027 · onHandMeasureQty → 028 · lotFk → 032 |
| ENT-INV-004 | quantity → DBF-INV-042 · variantFk → 053 · offlineFl → 051 · reservationFk → 057 · bundleVariantFk → 056 · stockMovePk → 040 · idempotencyKey → 052 · unitCost → 044 · moveTypeCode → 041 · lotFk → 055 · measureQty → 043 |
| ENT-INV-005 | warehouseFk → DBF-INV-077 · statusCode → 080 · originalQty → 067 · variantFk → 076 · reservedQty → 068 · shortfallQty → 069 · bundleVariantFk → 078 · idempotencyKey → 075 |
| ENT-INV-006 | fromWarehouseFk → DBF-INV-090 · toWarehouseFk → 091 · statusCode → 094 · docNo → 092 |
| ENT-INV-007 | quantity → DBF-INV-102 · variantFk → 106 · transferFk → 105 · lotFk → 107 · measureQty → 103 |
| ENT-INV-008 | statusCode → DBF-INV-122 · docNo → 120 |
| ENT-INV-009 | variantFk → DBF-INV-135 · stockCountFk → 134 · lotFk → 136 · countedMeasureQty → 131 · countedQty → 130 |
| ENT-INV-010 | reasonCode → DBF-INV-144 · statusCode → 151 · docNo → 149 |
| ENT-INV-011 | variantFk → DBF-INV-167 · unitCost → 164 · quantityChange → 162 · rowNo → 159 · stockAdjustmentFk → 166 · measureQtyChange → 163 · lotNo → 160 · lotExpiryDate → 161 · lotFk → 168 |
| ENT-INV-012 | expiryDate → DBF-INV-177 · variantFk → 180 · code → 181 |
| ENT-INV-013 | averageUnitCost → DBF-INV-193 |
| ENT-INV-014 | dualQuantityFl → DBF-INV-207 · lotTrackedFl → 206 · measureUomCode → 208 · itemFk → 209 |

No RULE is DEFERRED.

---

## 3 — XM register (`XM`)

SRS A8 `module-dependencies` consumes four CAT entities. A HARD-FK dependency is registered once per physical column that holds the reference (each column receives its own `constraint → XM-…` pointer and, in P3.1, its own integration block); a SOFT-READ is registered once per consumed entity. CAT's contract `contract-cat.md` (CAT v2, P1.5) is published and carries every consumed entity, so every record is **CONTRACTED**; the column type is the contract's identifier type, BIGINT (CON-CAT-001, CON-CAT-002).

```yaml name=xm-register
records:
- id: XM-INV-001
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-035
  - REQ-INV-039
  - REQ-INV-040
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_STOCK_BALANCE · VARIANT_FK (DBF-INV-030)
- id: XM-INV-002
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-051
  - REQ-INV-052
  - REQ-INV-075
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_STOCK_MOVE · VARIANT_FK (DBF-INV-053)
- id: XM-INV-003
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-053
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_STOCK_MOVE · BUNDLE_VARIANT_FK (DBF-INV-056)
- id: XM-INV-004
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-043
  - REQ-INV-050
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_RESERVATION · VARIANT_FK (DBF-INV-076)
- id: XM-INV-005
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-049
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_RESERVATION · BUNDLE_VARIANT_FK (DBF-INV-078)
- id: XM-INV-006
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-078
  - REQ-INV-080
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_TRANSFER_LINE · VARIANT_FK (DBF-INV-106)
- id: XM-INV-007
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-088
  - REQ-INV-092
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_STOCK_COUNT_LINE · VARIANT_FK (DBF-INV-135)
- id: XM-INV-008
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-070
  - REQ-INV-072
  - REQ-INV-097
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_STOCK_ADJUSTMENT_LINE · VARIANT_FK (DBF-INV-167)
- id: XM-INV-009
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-106
  - REQ-INV-109
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_LOT · VARIANT_FK (DBF-INV-180)
- id: XM-INV-010
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-059
  - REQ-INV-063
  state: CONTRACTED
  contract_ref: CON-CAT-002
  column: INV_STOCK_VALUATION · VARIANT_FK (DBF-INV-196)
- id: XM-INV-011
  type: HARD-FK
  target_module: CAT
  target_entity: ENT-CAT-001
  traces:
  - REQ-INV-124
  - REQ-INV-125
  - REQ-INV-129
  state: CONTRACTED
  contract_ref: CON-CAT-001
  column: INV_ITEM_STOCK_SETTINGS · ITEM_FK (DBF-INV-209)
- id: XM-INV-012
  type: SOFT-READ
  target_module: CAT
  target_entity: ENT-CAT-007
  traces:
  - REQ-INV-042
  - REQ-INV-049
  - REQ-INV-053
  - REQ-INV-054
  state: CONTRACTED
  contract_ref: CON-CAT-006
  column: no column · the bundle availability, reservation and issue services read
    the Bundle component lines of the bundle Variant's Item through CAT getItem (CON-CAT-007)
- id: XM-INV-013
  type: SOFT-READ
  target_module: CAT
  target_entity: ENT-CAT-009
  traces:
  - REQ-INV-072
  - REQ-INV-088
  - REQ-INV-091
  state: CONTRACTED
  contract_ref: CON-CAT-005
  column: no column · the count-line and opening-stock services resolve a scanned
    or loaded barcode to its Variant through CAT lookupBarcode (CON-CAT-010)
- id: XM-INV-014
  type: SOFT-READ
  target_module: CAT
  target_entity: ENT-CAT-002
  traces:
  - REQ-INV-052
  - REQ-INV-072
  - REQ-INV-078
  - REQ-INV-097
  state: DELIVERED
  contract_ref: contract-cat.md#CON-CAT-002
- id: XM-INV-015
  type: SOFT-READ
  target_module: CAT
  target_entity: ENT-CAT-001
  traces:
  - REQ-INV-039
  - REQ-INV-124
  - REQ-INV-126
  - REQ-INV-127
  - REQ-INV-129
  state: DELIVERED
  contract_ref: contract-cat.md#CON-CAT-001
```

| XM id | Type | Target | Column / access | SRS A8 row | Traces |
|---|---|---|---|---|---|
| XM-INV-001 … XM-INV-010 | HARD-FK | CAT · ENT-CAT-002 Variant | ten `…VARIANT_FK` columns (above) — column only, pointer `constraint → XM-INV-0nn` beside each | `{module: CAT, entity: ENT-CAT-002, type: HARD-FK}` | see block |
| XM-INV-011 | HARD-FK | CAT · ENT-CAT-001 Item | INV_ITEM_STOCK_SETTINGS.ITEM_FK — column only | `{module: CAT, entity: ENT-CAT-001, type: HARD-FK}` | REQ-INV-124, 125, 129 |
| XM-INV-012 | SOFT-READ | CAT · ENT-CAT-007 Bundle component | read through CON-CAT-007 (component lines with `componentBaseUomCode`) | `{module: CAT, entity: ENT-CAT-007, type: SOFT-READ}` | REQ-INV-042, 049, 053, 054 |
| XM-INV-013 | SOFT-READ | CAT · ENT-CAT-009 Barcode | read through CON-CAT-010 | `{module: CAT, entity: ENT-CAT-009, type: SOFT-READ}` | REQ-INV-072, 088, 091 |

Not XMs, by design:
- ENT-INV-014.measureUomCode (DBF-INV-208) holds a code of the CAT-owned unit-of-measure lookup, stored as a code in a `VARCHAR(50)` column and validated through the given `MdlLookupApi` (SRS A6 "Lookups consumed") — lookup values live in the given `mdl`, not in a CAT table, so no edge exists.
- `SOURCE_DOC_REF` / `SOURCE_LINE_REF` (DBF-INV-048, 050, 071, 073) hold the identifier of the calling module's document with no FK (SRS ENT-INV-004.sourceDocRef "SOFT reference — INV holds no edge to PUR, SLS, SFR"; D-23). PUR, SLS and SFR call INV; the edges point into INV and are minted by their own P2 stages.
- `FK_<TABLE>_TENANT → CORE_TENANT(ID)` targets the given core, which the core documents; audit user columns hold a principal string. Neither is an XM [C:C-GIVEN-1].
- CAT's catalogue-change event (CON-CAT-018) is not consumed in v1 (SRS D-23).

---

## 4 — FULL_DATABASE_SCRIPT

One consolidated executable for postgresql16 — the module's Flyway migration `V1002__inv_inventory.sql`. Prerequisite: the given `erp-core` is installed in the target schema (its migrations run before any business module), because BLOCK 5d references `CORE_TENANT` and BLOCK 8 seeds the given `mdl`, `sequence`, `notif` and `cu` tables. CAT's tables are **not** a prerequisite: every cross-module column is column-only (§5.1); its constraint is written once, in the integration blocks of the backend plan (P3.1).

```sql
-- =====================================================================
-- INV v1 — Inventory — postgresql16 — no schema prefix
-- Flyway application migration V1002__inv_inventory.sql (V1000+; after CAT
-- V1000 and PTY V1001 — the executor confirms it from the directory listing)
-- Identifier transformation: camelCase field -> UPPER_SNAKE_CASE column,
-- unquoted (stored folded to lower case). PK generation: identity.
-- Logical "date" -> DATE; logical "number" (whole) -> INTEGER (declared once,
-- ADR-INV-016). Quantity NUMERIC(18,3), cost NUMERIC(18,4), value NUMERIC(18,2).
-- Platform columns on every table: TENANT_ID (FK to CORE_TENANT, index led by
-- it, every UNIQUE composite with it) and VERSION — the given AuditableEntity
-- column set (ADR-INV-014). CURRENCY_CODE on monetary rows (CM-8, ADR-INV-015).
-- Cross-module (CAT) columns are column-only: no constraint, live or
-- commented; each carries one pointer to its XM record beside the column.
-- No BEGIN / COMMIT: Flyway runs the migration in its own transaction.
-- =====================================================================

-- ---------------------------------------------------------------------
-- BLOCK 1 — SEQUENCES
-- none: every PK uses the identity clause; warehouse and branch codes and
-- the transfer, count and adjustment numbers come from the given number
-- series (CORE_NUMBER_SERIES, seeded in BLOCK 8c, through NumberSeriesApi),
-- not from a sequence of this module.
-- ---------------------------------------------------------------------

-- ---------------------------------------------------------------------
-- BLOCK 2 — PARENT TABLES (no intra-module FK)
-- ---------------------------------------------------------------------

CREATE TABLE INV_BRANCH (
    BRANCH_PK                BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    IS_DEFAULT_FL            BOOLEAN       DEFAULT FALSE NOT NULL,
    CODE                     VARCHAR(20)   NOT NULL,
    NAME_AR                  VARCHAR(100)  NOT NULL,
    NAME_EN                  VARCHAR(100),
    IS_ACTIVE_FL             BOOLEAN       DEFAULT TRUE NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_LOT (
    LOT_PK                   BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    EXPIRY_DATE              DATE          NOT NULL,
    FIRST_RECEIVED_AT        TIMESTAMPTZ   NOT NULL,
    NEAR_EXPIRY_NOTIFIED_AT  TIMESTAMPTZ,
    VARIANT_FK               BIGINT        NOT NULL, -- constraint → XM-INV-009
    CODE                     VARCHAR(40)   NOT NULL,
    NAME_AR                  VARCHAR(100),
    NAME_EN                  VARCHAR(100),
    IS_ACTIVE_FL             BOOLEAN       DEFAULT TRUE NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_STOCK_VALUATION (
    STOCK_VALUATION_PK       BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    TOTAL_ON_HAND_QTY        NUMERIC(18,3) NOT NULL,
    AVERAGE_UNIT_COST        NUMERIC(18,4) NOT NULL,
    STOCK_VALUE              NUMERIC(18,2) NOT NULL,
    LAST_MOVE_AT             TIMESTAMPTZ   NOT NULL,
    VARIANT_FK               BIGINT        NOT NULL, -- constraint → XM-INV-010
    DOC_DATE                 DATE          NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CURRENCY_CODE            VARCHAR(3)    NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_ITEM_STOCK_SETTINGS (
    ITEM_STOCK_SETTINGS_PK   BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    LOT_TRACKED_FL           BOOLEAN       DEFAULT FALSE NOT NULL,
    DUAL_QUANTITY_FL         BOOLEAN       DEFAULT FALSE NOT NULL,
    MEASURE_UOM_CODE         VARCHAR(50),
    ITEM_FK                  BIGINT        NOT NULL, -- constraint → XM-INV-011
    KEY                      VARCHAR(40)   NOT NULL,
    VALUE_AR                 VARCHAR(100)  NOT NULL,
    VALUE_EN                 VARCHAR(100)  NOT NULL,
    IS_ACTIVE_FL             BOOLEAN       DEFAULT TRUE NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

-- ---------------------------------------------------------------------
-- BLOCK 3 — CHILD TABLES
-- INV_BRANCH -> INV_WAREHOUSE -> INV_STOCK_BALANCE (also -> INV_LOT)
-- INV_WAREHOUSE -> INV_RESERVATION -> INV_STOCK_MOVE (also -> INV_LOT)
-- INV_WAREHOUSE -> INV_TRANSFER -> INV_TRANSFER_LINE (also -> INV_LOT)
-- INV_WAREHOUSE -> INV_STOCK_COUNT -> INV_STOCK_COUNT_LINE (also -> INV_LOT)
-- INV_WAREHOUSE -> INV_STOCK_ADJUSTMENT -> INV_STOCK_ADJUSTMENT_LINE (also -> INV_LOT)
-- INV_STOCK_COUNT <-> INV_STOCK_ADJUSTMENT (both references nullable; the
-- constraints are added in 5d once both tables exist)
-- ---------------------------------------------------------------------

CREATE TABLE INV_WAREHOUSE (
    WAREHOUSE_PK             BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    IS_DEFAULT_FL            BOOLEAN       DEFAULT FALSE NOT NULL,
    BRANCH_FK                BIGINT        NOT NULL,
    CODE                     VARCHAR(20)   NOT NULL,
    NAME_AR                  VARCHAR(100)  NOT NULL,
    NAME_EN                  VARCHAR(100),
    IS_ACTIVE_FL             BOOLEAN       DEFAULT TRUE NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_STOCK_BALANCE (
    STOCK_BALANCE_PK         BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    ON_HAND_QTY              NUMERIC(18,3) NOT NULL,
    ON_HAND_MEASURE_QTY      NUMERIC(18,3),
    LAST_MOVE_AT             TIMESTAMPTZ   NOT NULL,
    VARIANT_FK               BIGINT        NOT NULL, -- constraint → XM-INV-001
    WAREHOUSE_FK             BIGINT        NOT NULL,
    LOT_FK                   BIGINT,
    DOC_DATE                 DATE          NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_RESERVATION (
    RESERVATION_PK           BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    ORIGINAL_QTY             NUMERIC(18,3) NOT NULL,
    RESERVED_QTY             NUMERIC(18,3) NOT NULL,
    SHORTFALL_QTY            NUMERIC(18,3) NOT NULL,
    SOURCE_DOC_TYPE_CODE     VARCHAR(50)   NOT NULL,
    SOURCE_DOC_REF           BIGINT        NOT NULL,
    SOURCE_DOC_NO            VARCHAR(30)   NOT NULL,
    SOURCE_LINE_REF          BIGINT,
    CLOSED_AT                TIMESTAMPTZ,
    IDEMPOTENCY_KEY          VARCHAR(64)   NOT NULL,
    VARIANT_FK               BIGINT        NOT NULL, -- constraint → XM-INV-004
    WAREHOUSE_FK             BIGINT        NOT NULL,
    BUNDLE_VARIANT_FK        BIGINT,                 -- constraint → XM-INV-005
    DOC_DATE                 DATE          NOT NULL,
    STATUS_CODE              VARCHAR(50)   DEFAULT 'ACTIVE' NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_STOCK_MOVE (
    STOCK_MOVE_PK            BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    MOVE_TYPE_CODE           VARCHAR(50)   NOT NULL,
    QUANTITY                 NUMERIC(18,3) NOT NULL,
    MEASURE_QTY              NUMERIC(18,3),
    UNIT_COST                NUMERIC(18,4) NOT NULL,
    MOVE_VALUE               NUMERIC(18,2) NOT NULL,
    BALANCE_AFTER_QTY        NUMERIC(18,3) NOT NULL,
    SOURCE_DOC_TYPE_CODE     VARCHAR(50)   NOT NULL,
    SOURCE_DOC_REF           BIGINT        NOT NULL,
    SOURCE_DOC_NO            VARCHAR(30)   NOT NULL,
    SOURCE_LINE_REF          BIGINT,
    OFFLINE_FL               BOOLEAN       DEFAULT FALSE NOT NULL,
    IDEMPOTENCY_KEY          VARCHAR(64)   NOT NULL,
    VARIANT_FK               BIGINT        NOT NULL, -- constraint → XM-INV-002
    WAREHOUSE_FK             BIGINT        NOT NULL,
    LOT_FK                   BIGINT,
    BUNDLE_VARIANT_FK        BIGINT,                 -- constraint → XM-INV-003
    RESERVATION_FK           BIGINT,
    DOC_DATE                 DATE          NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CURRENCY_CODE            VARCHAR(3)    NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_TRANSFER (
    TRANSFER_PK              BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    NOTE_TEXT                VARCHAR(500),
    POSTED_AT                TIMESTAMPTZ,
    FROM_WAREHOUSE_FK        BIGINT        NOT NULL,
    TO_WAREHOUSE_FK          BIGINT        NOT NULL,
    DOC_NO                   VARCHAR(30)   NOT NULL,
    DOC_DATE                 DATE          NOT NULL,
    STATUS_CODE              VARCHAR(50)   DEFAULT 'DRAFT' NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_TRANSFER_LINE (
    TRANSFER_LINE_PK         BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    QUANTITY                 NUMERIC(18,3) NOT NULL,
    MEASURE_QTY              NUMERIC(18,3),
    UNIT_COST                NUMERIC(18,4),
    TRANSFER_FK              BIGINT        NOT NULL,
    VARIANT_FK               BIGINT        NOT NULL, -- constraint → XM-INV-006
    LOT_FK                   BIGINT,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CURRENCY_CODE            VARCHAR(3)    NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_STOCK_COUNT (
    STOCK_COUNT_PK           BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    NOTE_TEXT                VARCHAR(500),
    POSTED_AT                TIMESTAMPTZ,
    WAREHOUSE_FK             BIGINT        NOT NULL,
    ADJUSTMENT_FK            BIGINT,
    DOC_NO                   VARCHAR(30)   NOT NULL,
    DOC_DATE                 DATE          NOT NULL,
    STATUS_CODE              VARCHAR(50)   DEFAULT 'DRAFT' NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_STOCK_ADJUSTMENT (
    STOCK_ADJUSTMENT_PK      BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    REASON_CODE              VARCHAR(50)   NOT NULL,
    NOTE_TEXT                VARCHAR(500),
    POSTED_AT                TIMESTAMPTZ,
    WAREHOUSE_FK             BIGINT        NOT NULL,
    SOURCE_COUNT_FK          BIGINT,
    DOC_NO                   VARCHAR(30)   NOT NULL,
    DOC_DATE                 DATE          NOT NULL,
    STATUS_CODE              VARCHAR(50)   DEFAULT 'DRAFT' NOT NULL,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_STOCK_COUNT_LINE (
    STOCK_COUNT_LINE_PK      BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    COUNTED_QTY              NUMERIC(18,3) NOT NULL,
    COUNTED_MEASURE_QTY      NUMERIC(18,3),
    SYSTEM_QTY               NUMERIC(18,3),
    DIFFERENCE_QTY           NUMERIC(18,3),
    STOCK_COUNT_FK           BIGINT        NOT NULL,
    VARIANT_FK               BIGINT        NOT NULL, -- constraint → XM-INV-007
    LOT_FK                   BIGINT,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE INV_STOCK_ADJUSTMENT_LINE (
    STOCK_ADJUSTMENT_LINE_PK BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    ROW_NO                   INTEGER       NOT NULL,
    LOT_NO                   VARCHAR(40),
    LOT_EXPIRY_DATE          DATE,
    QUANTITY_CHANGE          NUMERIC(18,3) NOT NULL,
    MEASURE_QTY_CHANGE       NUMERIC(18,3),
    UNIT_COST                NUMERIC(18,4),
    LINE_VALUE               NUMERIC(18,2),
    STOCK_ADJUSTMENT_FK      BIGINT        NOT NULL,
    VARIANT_FK               BIGINT        NOT NULL, -- constraint → XM-INV-008
    LOT_FK                   BIGINT,
    TENANT_ID                BIGINT        NOT NULL,
    VERSION                  BIGINT        DEFAULT 0 NOT NULL,
    CURRENCY_CODE            VARCHAR(3)    NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

-- ---------------------------------------------------------------------
-- BLOCK 4 — COMMENTS (every column cites its DBF id)
-- ---------------------------------------------------------------------

COMMENT ON TABLE INV_WAREHOUSE IS 'ENT-INV-001 Warehouse — a stock location of the tenant (master, SHARED owner; CON-INV-001)';
COMMENT ON COLUMN INV_WAREHOUSE.WAREHOUSE_PK IS 'DBF-INV-001 · ENT-INV-001.warehousePk · identity key (BIGINT), the HARD-FK target of PUR, SLS, SFR, INS';
COMMENT ON COLUMN INV_WAREHOUSE.IS_DEFAULT_FL IS 'DBF-INV-002 · ENT-INV-001.isDefaultFl · exactly one active default per tenant (RULE-INV-006); the default cannot be deactivated (RULE-INV-003)';
COMMENT ON COLUMN INV_WAREHOUSE.BRANCH_FK IS 'DBF-INV-003 · ENT-INV-001.branchFk · intra-module FK (TENANT_ID, BRANCH_FK) to INV_BRANCH; active branch, the default branch when none is given (RULE-INV-009, REQ-INV-031)';
COMMENT ON COLUMN INV_WAREHOUSE.CODE IS 'DBF-INV-004 · ENT-INV-001.code · from the given number series INV_WAREHOUSE on first save, read-only, unique with TENANT_ID (RULE-INV-001)';
COMMENT ON COLUMN INV_WAREHOUSE.NAME_AR IS 'DBF-INV-005 · ENT-INV-001.nameAr · required, unique among the tenant warehouses (RULE-INV-002)';
COMMENT ON COLUMN INV_WAREHOUSE.NAME_EN IS 'DBF-INV-006 · ENT-INV-001.nameEn · optional; English display falls back to NAME_AR';
COMMENT ON COLUMN INV_WAREHOUSE.IS_ACTIVE_FL IS 'DBF-INV-007 · ENT-INV-001.isActiveFl · deactivation, not deletion (RULE-INV-004, RULE-INV-005, RULE-INV-007)';
COMMENT ON COLUMN INV_WAREHOUSE.TENANT_ID IS 'DBF-INV-008 · ENT-INV-001.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_WAREHOUSE.VERSION IS 'DBF-INV-009 · ENT-INV-001.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_WAREHOUSE.CREATED_BY IS 'DBF-INV-010 · ENT-INV-001.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_WAREHOUSE.CREATED_AT IS 'DBF-INV-011 · ENT-INV-001.createdAt · platform-filled';
COMMENT ON COLUMN INV_WAREHOUSE.UPDATED_BY IS 'DBF-INV-012 · ENT-INV-001.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_WAREHOUSE.UPDATED_AT IS 'DBF-INV-013 · ENT-INV-001.updatedAt · platform-filled';

COMMENT ON TABLE INV_BRANCH IS 'ENT-INV-002 Branch — an outlet of the tenant; every warehouse belongs to one (master, SHARED owner; CON-INV-002)';
COMMENT ON COLUMN INV_BRANCH.BRANCH_PK IS 'DBF-INV-014 · ENT-INV-002.branchPk · identity key (BIGINT), the HARD-FK target of PUR, SLS, STL, TAX, INS';
COMMENT ON COLUMN INV_BRANCH.IS_DEFAULT_FL IS 'DBF-INV-015 · ENT-INV-002.isDefaultFl · exactly one active default per tenant (RULE-INV-015); the default cannot be deactivated (RULE-INV-012)';
COMMENT ON COLUMN INV_BRANCH.CODE IS 'DBF-INV-016 · ENT-INV-002.code · from the given number series INV_BRANCH on first save, read-only, unique with TENANT_ID (RULE-INV-010)';
COMMENT ON COLUMN INV_BRANCH.NAME_AR IS 'DBF-INV-017 · ENT-INV-002.nameAr · required, unique among the tenant branches (RULE-INV-011)';
COMMENT ON COLUMN INV_BRANCH.NAME_EN IS 'DBF-INV-018 · ENT-INV-002.nameEn · optional; English display falls back to NAME_AR';
COMMENT ON COLUMN INV_BRANCH.IS_ACTIVE_FL IS 'DBF-INV-019 · ENT-INV-002.isActiveFl · deactivation, not deletion (RULE-INV-013, RULE-INV-014)';
COMMENT ON COLUMN INV_BRANCH.TENANT_ID IS 'DBF-INV-020 · ENT-INV-002.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_BRANCH.VERSION IS 'DBF-INV-021 · ENT-INV-002.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_BRANCH.CREATED_BY IS 'DBF-INV-022 · ENT-INV-002.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_BRANCH.CREATED_AT IS 'DBF-INV-023 · ENT-INV-002.createdAt · platform-filled';
COMMENT ON COLUMN INV_BRANCH.UPDATED_BY IS 'DBF-INV-024 · ENT-INV-002.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_BRANCH.UPDATED_AT IS 'DBF-INV-025 · ENT-INV-002.updatedAt · platform-filled';

COMMENT ON TABLE INV_STOCK_BALANCE IS 'ENT-INV-003 Stock balance — one row per Variant x warehouse x lot, maintained only by stock moves (transactional, SHARED owner; CON-INV-004)';
COMMENT ON COLUMN INV_STOCK_BALANCE.STOCK_BALANCE_PK IS 'DBF-INV-026 · ENT-INV-003.stockBalancePk · identity key (BIGINT)';
COMMENT ON COLUMN INV_STOCK_BALANCE.ON_HAND_QTY IS 'DBF-INV-027 · ENT-INV-003.onHandQty · base unit, 3 decimals; may be below zero (POL-INV-018); changed with the move in one transaction (REQ-INV-036)';
COMMENT ON COLUMN INV_STOCK_BALANCE.ON_HAND_MEASURE_QTY IS 'DBF-INV-028 · ENT-INV-003.onHandMeasureQty · dual-quantity Items only (POL-INV-009, RULE-INV-043)';
COMMENT ON COLUMN INV_STOCK_BALANCE.LAST_MOVE_AT IS 'DBF-INV-029 · ENT-INV-003.lastMoveAt · time of the last move';
COMMENT ON COLUMN INV_STOCK_BALANCE.VARIANT_FK IS 'DBF-INV-030 · ENT-INV-003.variantFk · CAT Variant identifier (BIGINT, CON-CAT-002); cross-module HARD-FK, column only (XM-INV-001)';
COMMENT ON COLUMN INV_STOCK_BALANCE.WAREHOUSE_FK IS 'DBF-INV-031 · ENT-INV-003.warehouseFk · intra-module FK (TENANT_ID, WAREHOUSE_FK) to INV_WAREHOUSE (RULE-INV-004)';
COMMENT ON COLUMN INV_STOCK_BALANCE.LOT_FK IS 'DBF-INV-032 · ENT-INV-003.lotFk · intra-module FK (TENANT_ID, LOT_FK) to INV_LOT; empty for stock without a lot (RULE-INV-042)';
COMMENT ON COLUMN INV_STOCK_BALANCE.DOC_DATE IS 'DBF-INV-033 · ENT-INV-003.docDate · date of the first move (standard field)';
COMMENT ON COLUMN INV_STOCK_BALANCE.TENANT_ID IS 'DBF-INV-034 · ENT-INV-003.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_BALANCE.VERSION IS 'DBF-INV-035 · ENT-INV-003.version · optimistic-lock version (ADR-INV-014); concurrent moves are serialised per Variant and warehouse (REQ-INV-037, SRS D-09)';
COMMENT ON COLUMN INV_STOCK_BALANCE.CREATED_BY IS 'DBF-INV-036 · ENT-INV-003.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_BALANCE.CREATED_AT IS 'DBF-INV-037 · ENT-INV-003.createdAt · platform-filled';
COMMENT ON COLUMN INV_STOCK_BALANCE.UPDATED_BY IS 'DBF-INV-038 · ENT-INV-003.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_BALANCE.UPDATED_AT IS 'DBF-INV-039 · ENT-INV-003.updatedAt · platform-filled';

COMMENT ON TABLE INV_STOCK_MOVE IS 'ENT-INV-004 Stock move — one immutable ledger row per quantity in or out; never updated or deleted (RULE-INV-023); published as the stock-move event (transactional, PRIVATE; CON-INV-020)';
COMMENT ON COLUMN INV_STOCK_MOVE.STOCK_MOVE_PK IS 'DBF-INV-040 · ENT-INV-004.stockMovePk · identity key (BIGINT), carried by the stock-move event (RULE-INV-023)';
COMMENT ON COLUMN INV_STOCK_MOVE.MOVE_TYPE_CODE IS 'DBF-INV-041 · ENT-INV-004.moveTypeCode · code of lookup INV_MOVE_TYPE (given mdl, closed set); gives the direction (RULE-INV-039)';
COMMENT ON COLUMN INV_STOCK_MOVE.QUANTITY IS 'DBF-INV-042 · ENT-INV-004.quantity · base unit, 3 decimals, greater than 0; 0 only on a measure-only adjustment move (RULE-INV-017, REQ-INV-123, ADR-INV-017)';
COMMENT ON COLUMN INV_STOCK_MOVE.MEASURE_QTY IS 'DBF-INV-043 · ENT-INV-004.measureQty · dual-quantity Items, greater than 0 (RULE-INV-040)';
COMMENT ON COLUMN INV_STOCK_MOVE.UNIT_COST IS 'DBF-INV-044 · ENT-INV-004.unitCost · receipt cost or current average, 4 decimals, 0 or more (RULE-INV-025, REQ-INV-061)';
COMMENT ON COLUMN INV_STOCK_MOVE.MOVE_VALUE IS 'DBF-INV-045 · ENT-INV-004.moveValue · QUANTITY x UNIT_COST, 2 decimals, in CURRENCY_CODE';
COMMENT ON COLUMN INV_STOCK_MOVE.BALANCE_AFTER_QTY IS 'DBF-INV-046 · ENT-INV-004.balanceAfterQty · Variant x warehouse on-hand after this move, all lots (REQ-INV-076)';
COMMENT ON COLUMN INV_STOCK_MOVE.SOURCE_DOC_TYPE_CODE IS 'DBF-INV-047 · ENT-INV-004.sourceDocTypeCode · code of lookup INV_SOURCE_DOC_TYPE (given mdl, open set)';
COMMENT ON COLUMN INV_STOCK_MOVE.SOURCE_DOC_REF IS 'DBF-INV-048 · ENT-INV-004.sourceDocRef · identifier of the source document in its module; SOFT reference, no FK, no XM (SRS D-23)';
COMMENT ON COLUMN INV_STOCK_MOVE.SOURCE_DOC_NO IS 'DBF-INV-049 · ENT-INV-004.sourceDocNo · number of the source document, shown in history';
COMMENT ON COLUMN INV_STOCK_MOVE.SOURCE_LINE_REF IS 'DBF-INV-050 · ENT-INV-004.sourceLineRef · source line identifier; SOFT reference, no FK';
COMMENT ON COLUMN INV_STOCK_MOVE.OFFLINE_FL IS 'DBF-INV-051 · ENT-INV-004.offlineFl · true for a synchronised offline POS sale; never checked against negative stock (POL-INV-020, RULE-INV-019)';
COMMENT ON COLUMN INV_STOCK_MOVE.IDEMPOTENCY_KEY IS 'DBF-INV-052 · ENT-INV-004.idempotencyKey · caller-supplied; one recording per key and request, which may hold several moves (RULE-INV-024)';
COMMENT ON COLUMN INV_STOCK_MOVE.VARIANT_FK IS 'DBF-INV-053 · ENT-INV-004.variantFk · CAT Variant identifier (BIGINT, CON-CAT-002), a component Variant for a bundle issue; cross-module HARD-FK, column only (XM-INV-002)';
COMMENT ON COLUMN INV_STOCK_MOVE.WAREHOUSE_FK IS 'DBF-INV-054 · ENT-INV-004.warehouseFk · intra-module FK (TENANT_ID, WAREHOUSE_FK) to INV_WAREHOUSE; recorded on every move (POL-INV-002, REQ-INV-016)';
COMMENT ON COLUMN INV_STOCK_MOVE.LOT_FK IS 'DBF-INV-055 · ENT-INV-004.lotFk · intra-module FK (TENANT_ID, LOT_FK) to INV_LOT; lot-tracked Items (RULE-INV-039)';
COMMENT ON COLUMN INV_STOCK_MOVE.BUNDLE_VARIANT_FK IS 'DBF-INV-056 · ENT-INV-004.bundleVariantFk · CAT bundle Variant identifier (BIGINT, CON-CAT-002) set on component moves; cross-module HARD-FK, column only (XM-INV-003)';
COMMENT ON COLUMN INV_STOCK_MOVE.RESERVATION_FK IS 'DBF-INV-057 · ENT-INV-004.reservationFk · intra-module FK (TENANT_ID, RESERVATION_FK) to INV_RESERVATION; the reservation this issue consumes (RULE-INV-021)';
COMMENT ON COLUMN INV_STOCK_MOVE.DOC_DATE IS 'DBF-INV-058 · ENT-INV-004.docDate · business date of the move';
COMMENT ON COLUMN INV_STOCK_MOVE.TENANT_ID IS 'DBF-INV-059 · ENT-INV-004.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_MOVE.VERSION IS 'DBF-INV-060 · ENT-INV-004.version · given AuditableEntity column; stays 0 because a move is never updated (RULE-INV-023)';
COMMENT ON COLUMN INV_STOCK_MOVE.CURRENCY_CODE IS 'DBF-INV-061 · ENT-INV-004.currencyCode · ISO 4217 currency of UNIT_COST and MOVE_VALUE — the tenant base currency (SRS A2, D-06; CM-8; ADR-INV-015)';
COMMENT ON COLUMN INV_STOCK_MOVE.CREATED_BY IS 'DBF-INV-062 · ENT-INV-004.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_MOVE.CREATED_AT IS 'DBF-INV-063 · ENT-INV-004.createdAt · platform-filled';
COMMENT ON COLUMN INV_STOCK_MOVE.UPDATED_BY IS 'DBF-INV-064 · ENT-INV-004.updatedBy · platform-filled principal, equal to CREATED_BY (never updated)';
COMMENT ON COLUMN INV_STOCK_MOVE.UPDATED_AT IS 'DBF-INV-065 · ENT-INV-004.updatedAt · platform-filled, equal to CREATED_AT (never updated)';

COMMENT ON TABLE INV_RESERVATION IS 'ENT-INV-005 Reservation — stock set aside for a source document of SLS or SFR (transactional, PRIVATE; reached through CON-INV-011 to CON-INV-013, CON-INV-015; lifecycle SRS A7)';
COMMENT ON COLUMN INV_RESERVATION.RESERVATION_PK IS 'DBF-INV-066 · ENT-INV-005.reservationPk · identity key (BIGINT); callers keep it as a SOFT reference';
COMMENT ON COLUMN INV_RESERVATION.ORIGINAL_QTY IS 'DBF-INV-067 · ENT-INV-005.originalQty · as requested, greater than 0 (RULE-INV-017)';
COMMENT ON COLUMN INV_RESERVATION.RESERVED_QTY IS 'DBF-INV-068 · ENT-INV-005.reservedQty · remaining after consumption, 0 to ORIGINAL_QTY (RULE-INV-020)';
COMMENT ON COLUMN INV_RESERVATION.SHORTFALL_QTY IS 'DBF-INV-069 · ENT-INV-005.shortfallQty · part not covered by availability when accepted, 0 or more (REQ-INV-045, RULE-INV-020)';
COMMENT ON COLUMN INV_RESERVATION.SOURCE_DOC_TYPE_CODE IS 'DBF-INV-070 · ENT-INV-005.sourceDocTypeCode · code of lookup INV_SOURCE_DOC_TYPE (given mdl, open set)';
COMMENT ON COLUMN INV_RESERVATION.SOURCE_DOC_REF IS 'DBF-INV-071 · ENT-INV-005.sourceDocRef · identifier of the source document; SOFT reference, no FK, no XM';
COMMENT ON COLUMN INV_RESERVATION.SOURCE_DOC_NO IS 'DBF-INV-072 · ENT-INV-005.sourceDocNo · number of the source document';
COMMENT ON COLUMN INV_RESERVATION.SOURCE_LINE_REF IS 'DBF-INV-073 · ENT-INV-005.sourceLineRef · source line identifier; SOFT reference, no FK';
COMMENT ON COLUMN INV_RESERVATION.CLOSED_AT IS 'DBF-INV-074 · ENT-INV-005.closedAt · set on CONSUMED or RELEASED';
COMMENT ON COLUMN INV_RESERVATION.IDEMPOTENCY_KEY IS 'DBF-INV-075 · ENT-INV-005.idempotencyKey · caller-supplied; one recording per key and request, which may hold one reservation per bundle component (RULE-INV-024)';
COMMENT ON COLUMN INV_RESERVATION.VARIANT_FK IS 'DBF-INV-076 · ENT-INV-005.variantFk · CAT Variant identifier (BIGINT, CON-CAT-002), a component Variant when exploded from a bundle; cross-module HARD-FK, column only (XM-INV-004)';
COMMENT ON COLUMN INV_RESERVATION.WAREHOUSE_FK IS 'DBF-INV-077 · ENT-INV-005.warehouseFk · intra-module FK (TENANT_ID, WAREHOUSE_FK) to INV_WAREHOUSE (RULE-INV-004)';
COMMENT ON COLUMN INV_RESERVATION.BUNDLE_VARIANT_FK IS 'DBF-INV-078 · ENT-INV-005.bundleVariantFk · CAT bundle Variant identifier (BIGINT, CON-CAT-002); cross-module HARD-FK, column only (XM-INV-005)';
COMMENT ON COLUMN INV_RESERVATION.DOC_DATE IS 'DBF-INV-079 · ENT-INV-005.docDate · date reserved';
COMMENT ON COLUMN INV_RESERVATION.STATUS_CODE IS 'DBF-INV-080 · ENT-INV-005.statusCode · code of lookup INV_RESERVATION_STATUS (closed: ACTIVE, CONSUMED, RELEASED — SRS A7, RULE-INV-021)';
COMMENT ON COLUMN INV_RESERVATION.TENANT_ID IS 'DBF-INV-081 · ENT-INV-005.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_RESERVATION.VERSION IS 'DBF-INV-082 · ENT-INV-005.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_RESERVATION.CREATED_BY IS 'DBF-INV-083 · ENT-INV-005.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_RESERVATION.CREATED_AT IS 'DBF-INV-084 · ENT-INV-005.createdAt · platform-filled';
COMMENT ON COLUMN INV_RESERVATION.UPDATED_BY IS 'DBF-INV-085 · ENT-INV-005.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_RESERVATION.UPDATED_AT IS 'DBF-INV-086 · ENT-INV-005.updatedAt · platform-filled';

COMMENT ON TABLE INV_TRANSFER IS 'ENT-INV-006 Transfer — a numbered document moving stock between two warehouses at unchanged cost (transactional, PRIVATE; lifecycle SRS A7)';
COMMENT ON COLUMN INV_TRANSFER.TRANSFER_PK IS 'DBF-INV-087 · ENT-INV-006.transferPk · identity key (BIGINT)';
COMMENT ON COLUMN INV_TRANSFER.NOTE_TEXT IS 'DBF-INV-088 · ENT-INV-006.noteText · optional (Advanced section)';
COMMENT ON COLUMN INV_TRANSFER.POSTED_AT IS 'DBF-INV-089 · ENT-INV-006.postedAt · set on posting';
COMMENT ON COLUMN INV_TRANSFER.FROM_WAREHOUSE_FK IS 'DBF-INV-090 · ENT-INV-006.fromWarehouseFk · intra-module FK (TENANT_ID, FROM_WAREHOUSE_FK) to INV_WAREHOUSE; active, different from the destination (RULE-INV-016)';
COMMENT ON COLUMN INV_TRANSFER.TO_WAREHOUSE_FK IS 'DBF-INV-091 · ENT-INV-006.toWarehouseFk · intra-module FK (TENANT_ID, TO_WAREHOUSE_FK) to INV_WAREHOUSE; active, different from the source (RULE-INV-016)';
COMMENT ON COLUMN INV_TRANSFER.DOC_NO IS 'DBF-INV-092 · ENT-INV-006.docNo · from the given number series INV_TRANSFER on first save, read-only, unique with TENANT_ID (RULE-INV-030)';
COMMENT ON COLUMN INV_TRANSFER.DOC_DATE IS 'DBF-INV-093 · ENT-INV-006.docDate · today in the tenant time zone by default (service-filled)';
COMMENT ON COLUMN INV_TRANSFER.STATUS_CODE IS 'DBF-INV-094 · ENT-INV-006.statusCode · code of lookup INV_DOC_STATUS (closed: DRAFT, POSTED, CANCELLED — SRS A7, RULE-INV-029)';
COMMENT ON COLUMN INV_TRANSFER.TENANT_ID IS 'DBF-INV-095 · ENT-INV-006.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_TRANSFER.VERSION IS 'DBF-INV-096 · ENT-INV-006.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_TRANSFER.CREATED_BY IS 'DBF-INV-097 · ENT-INV-006.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_TRANSFER.CREATED_AT IS 'DBF-INV-098 · ENT-INV-006.createdAt · platform-filled';
COMMENT ON COLUMN INV_TRANSFER.UPDATED_BY IS 'DBF-INV-099 · ENT-INV-006.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_TRANSFER.UPDATED_AT IS 'DBF-INV-100 · ENT-INV-006.updatedAt · platform-filled';

COMMENT ON TABLE INV_TRANSFER_LINE IS 'ENT-INV-007 Transfer line — one Variant (and lot) moved by a Transfer; takes docNo, docDate and statusCode from its header (transactional line, PRIVATE)';
COMMENT ON COLUMN INV_TRANSFER_LINE.TRANSFER_LINE_PK IS 'DBF-INV-101 · ENT-INV-007.transferLinePk · identity key (BIGINT)';
COMMENT ON COLUMN INV_TRANSFER_LINE.QUANTITY IS 'DBF-INV-102 · ENT-INV-007.quantity · base unit, 3 decimals, greater than 0 (RULE-INV-017)';
COMMENT ON COLUMN INV_TRANSFER_LINE.MEASURE_QTY IS 'DBF-INV-103 · ENT-INV-007.measureQty · dual-quantity Items, greater than 0 (RULE-INV-040)';
COMMENT ON COLUMN INV_TRANSFER_LINE.UNIT_COST IS 'DBF-INV-104 · ENT-INV-007.unitCost · filled at posting with the current average cost (REQ-INV-080)';
COMMENT ON COLUMN INV_TRANSFER_LINE.TRANSFER_FK IS 'DBF-INV-105 · ENT-INV-007.transferFk · intra-module FK (TENANT_ID, TRANSFER_FK) to INV_TRANSFER (RULE-INV-031)';
COMMENT ON COLUMN INV_TRANSFER_LINE.VARIANT_FK IS 'DBF-INV-106 · ENT-INV-007.variantFk · CAT Variant identifier (BIGINT, CON-CAT-002) of a STANDARD Item (RULE-INV-018); cross-module HARD-FK, column only (XM-INV-006)';
COMMENT ON COLUMN INV_TRANSFER_LINE.LOT_FK IS 'DBF-INV-107 · ENT-INV-007.lotFk · intra-module FK (TENANT_ID, LOT_FK) to INV_LOT; required for lot-tracked Items (RULE-INV-037)';
COMMENT ON COLUMN INV_TRANSFER_LINE.TENANT_ID IS 'DBF-INV-108 · ENT-INV-007.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_TRANSFER_LINE.VERSION IS 'DBF-INV-109 · ENT-INV-007.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_TRANSFER_LINE.CURRENCY_CODE IS 'DBF-INV-110 · ENT-INV-007.currencyCode · ISO 4217 currency of UNIT_COST — the tenant base currency (CM-8; ADR-INV-015)';
COMMENT ON COLUMN INV_TRANSFER_LINE.CREATED_BY IS 'DBF-INV-111 · ENT-INV-007.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_TRANSFER_LINE.CREATED_AT IS 'DBF-INV-112 · ENT-INV-007.createdAt · platform-filled';
COMMENT ON COLUMN INV_TRANSFER_LINE.UPDATED_BY IS 'DBF-INV-113 · ENT-INV-007.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_TRANSFER_LINE.UPDATED_AT IS 'DBF-INV-114 · ENT-INV-007.updatedAt · platform-filled';

COMMENT ON TABLE INV_STOCK_COUNT IS 'ENT-INV-008 Stock count — a numbered count sheet of one warehouse, posted through an adjustment (transactional, PRIVATE; lifecycle SRS A7)';
COMMENT ON COLUMN INV_STOCK_COUNT.STOCK_COUNT_PK IS 'DBF-INV-115 · ENT-INV-008.stockCountPk · identity key (BIGINT)';
COMMENT ON COLUMN INV_STOCK_COUNT.NOTE_TEXT IS 'DBF-INV-116 · ENT-INV-008.noteText · optional (Advanced section)';
COMMENT ON COLUMN INV_STOCK_COUNT.POSTED_AT IS 'DBF-INV-117 · ENT-INV-008.postedAt · set on posting';
COMMENT ON COLUMN INV_STOCK_COUNT.WAREHOUSE_FK IS 'DBF-INV-118 · ENT-INV-008.warehouseFk · intra-module FK (TENANT_ID, WAREHOUSE_FK) to INV_WAREHOUSE; the default while one is active (REQ-INV-013)';
COMMENT ON COLUMN INV_STOCK_COUNT.ADJUSTMENT_FK IS 'DBF-INV-119 · ENT-INV-008.adjustmentFk · intra-module FK (TENANT_ID, ADJUSTMENT_FK) to INV_STOCK_ADJUSTMENT; the adjustment generated at posting (REQ-INV-092)';
COMMENT ON COLUMN INV_STOCK_COUNT.DOC_NO IS 'DBF-INV-120 · ENT-INV-008.docNo · from the given number series INV_COUNT on first save, read-only, unique with TENANT_ID (RULE-INV-030)';
COMMENT ON COLUMN INV_STOCK_COUNT.DOC_DATE IS 'DBF-INV-121 · ENT-INV-008.docDate · today in the tenant time zone by default (service-filled)';
COMMENT ON COLUMN INV_STOCK_COUNT.STATUS_CODE IS 'DBF-INV-122 · ENT-INV-008.statusCode · code of lookup INV_DOC_STATUS (closed: DRAFT, POSTED, CANCELLED — SRS A7, RULE-INV-029)';
COMMENT ON COLUMN INV_STOCK_COUNT.TENANT_ID IS 'DBF-INV-123 · ENT-INV-008.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_COUNT.VERSION IS 'DBF-INV-124 · ENT-INV-008.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_COUNT.CREATED_BY IS 'DBF-INV-125 · ENT-INV-008.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_COUNT.CREATED_AT IS 'DBF-INV-126 · ENT-INV-008.createdAt · platform-filled';
COMMENT ON COLUMN INV_STOCK_COUNT.UPDATED_BY IS 'DBF-INV-127 · ENT-INV-008.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_COUNT.UPDATED_AT IS 'DBF-INV-128 · ENT-INV-008.updatedAt · platform-filled';

COMMENT ON TABLE INV_STOCK_COUNT_LINE IS 'ENT-INV-009 Stock count line — the counted quantity of one Variant (and lot); repeated scans add to one line (SRS D-14) (transactional line, PRIVATE)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.STOCK_COUNT_LINE_PK IS 'DBF-INV-129 · ENT-INV-009.stockCountLinePk · identity key (BIGINT)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.COUNTED_QTY IS 'DBF-INV-130 · ENT-INV-009.countedQty · 0 or more, 3 decimals (RULE-INV-047)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.COUNTED_MEASURE_QTY IS 'DBF-INV-131 · ENT-INV-009.countedMeasureQty · dual-quantity Items, 0 or more (RULE-INV-040)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.SYSTEM_QTY IS 'DBF-INV-132 · ENT-INV-009.systemQty · on-hand at posting (SRS D-15)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.DIFFERENCE_QTY IS 'DBF-INV-133 · ENT-INV-009.differenceQty · COUNTED_QTY minus SYSTEM_QTY, set at posting';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.STOCK_COUNT_FK IS 'DBF-INV-134 · ENT-INV-009.stockCountFk · intra-module FK (TENANT_ID, STOCK_COUNT_FK) to INV_STOCK_COUNT (RULE-INV-031)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.VARIANT_FK IS 'DBF-INV-135 · ENT-INV-009.variantFk · CAT Variant identifier (BIGINT, CON-CAT-002), from a barcode or a pick (RULE-INV-032); cross-module HARD-FK, column only (XM-INV-007)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.LOT_FK IS 'DBF-INV-136 · ENT-INV-009.lotFk · intra-module FK (TENANT_ID, LOT_FK) to INV_LOT; required for lot-tracked Items (RULE-INV-037)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.TENANT_ID IS 'DBF-INV-137 · ENT-INV-009.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.VERSION IS 'DBF-INV-138 · ENT-INV-009.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.CREATED_BY IS 'DBF-INV-139 · ENT-INV-009.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.CREATED_AT IS 'DBF-INV-140 · ENT-INV-009.createdAt · platform-filled';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.UPDATED_BY IS 'DBF-INV-141 · ENT-INV-009.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_COUNT_LINE.UPDATED_AT IS 'DBF-INV-142 · ENT-INV-009.updatedAt · platform-filled';

COMMENT ON TABLE INV_STOCK_ADJUSTMENT IS 'ENT-INV-010 Stock adjustment — a numbered document correcting stock with a reason, including opening stock and count differences (transactional, PRIVATE; lifecycle SRS A7)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.STOCK_ADJUSTMENT_PK IS 'DBF-INV-143 · ENT-INV-010.stockAdjustmentPk · identity key (BIGINT)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.REASON_CODE IS 'DBF-INV-144 · ENT-INV-010.reasonCode · code of lookup INV_ADJUSTMENT_REASON (given mdl, open set), an active value (RULE-INV-033, RULE-INV-026)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.NOTE_TEXT IS 'DBF-INV-145 · ENT-INV-010.noteText · optional (Advanced section)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.POSTED_AT IS 'DBF-INV-146 · ENT-INV-010.postedAt · set on posting';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.WAREHOUSE_FK IS 'DBF-INV-147 · ENT-INV-010.warehouseFk · intra-module FK (TENANT_ID, WAREHOUSE_FK) to INV_WAREHOUSE; the default while one is active (REQ-INV-010, REQ-INV-013)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.SOURCE_COUNT_FK IS 'DBF-INV-148 · ENT-INV-010.sourceCountFk · intra-module FK (TENANT_ID, SOURCE_COUNT_FK) to INV_STOCK_COUNT; set when generated by a count (REQ-INV-092)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.DOC_NO IS 'DBF-INV-149 · ENT-INV-010.docNo · from the given number series INV_ADJUSTMENT on first save, read-only, unique with TENANT_ID (RULE-INV-030)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.DOC_DATE IS 'DBF-INV-150 · ENT-INV-010.docDate · today in the tenant time zone by default (service-filled)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.STATUS_CODE IS 'DBF-INV-151 · ENT-INV-010.statusCode · code of lookup INV_DOC_STATUS (closed: DRAFT, POSTED, CANCELLED — SRS A7, RULE-INV-029)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.TENANT_ID IS 'DBF-INV-152 · ENT-INV-010.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.VERSION IS 'DBF-INV-153 · ENT-INV-010.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.CREATED_BY IS 'DBF-INV-154 · ENT-INV-010.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.CREATED_AT IS 'DBF-INV-155 · ENT-INV-010.createdAt · platform-filled';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.UPDATED_BY IS 'DBF-INV-156 · ENT-INV-010.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT.UPDATED_AT IS 'DBF-INV-157 · ENT-INV-010.updatedAt · platform-filled';

COMMENT ON TABLE INV_STOCK_ADJUSTMENT_LINE IS 'ENT-INV-011 Stock adjustment line — one signed change of a Variant (and lot); takes docNo, docDate and statusCode from its header (transactional line, PRIVATE)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.STOCK_ADJUSTMENT_LINE_PK IS 'DBF-INV-158 · ENT-INV-011.stockAdjustmentLinePk · identity key (BIGINT)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.ROW_NO IS 'DBF-INV-159 · ENT-INV-011.rowNo · line order, 1 or more, unique within the adjustment; the row number reported by a bulk load (REQ-INV-072, RULE-INV-027)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.LOT_NO IS 'DBF-INV-160 · ENT-INV-011.lotNo · lot-tracked Items; resolves or creates the Lot at posting (RULE-INV-035, RULE-INV-048)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.LOT_EXPIRY_DATE IS 'DBF-INV-161 · ENT-INV-011.lotExpiryDate · required with a new lot number on an increase (RULE-INV-035)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.QUANTITY_CHANGE IS 'DBF-INV-162 · ENT-INV-011.quantityChange · positive increase, negative decrease; non-zero unless the measure changes (RULE-INV-034, checked by the service on save and post)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.MEASURE_QTY_CHANGE IS 'DBF-INV-163 · ENT-INV-011.measureQtyChange · dual-quantity Items (REQ-INV-123)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.UNIT_COST IS 'DBF-INV-164 · ENT-INV-011.unitCost · increases, 0 or more; required for OPENING_STOCK (RULE-INV-025, RULE-INV-026)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.LINE_VALUE IS 'DBF-INV-165 · ENT-INV-011.lineValue · set at posting, in CURRENCY_CODE';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.STOCK_ADJUSTMENT_FK IS 'DBF-INV-166 · ENT-INV-011.stockAdjustmentFk · intra-module FK (TENANT_ID, STOCK_ADJUSTMENT_FK) to INV_STOCK_ADJUSTMENT (RULE-INV-031)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.VARIANT_FK IS 'DBF-INV-167 · ENT-INV-011.variantFk · CAT Variant identifier (BIGINT, CON-CAT-002) of a STANDARD Item (RULE-INV-018, RULE-INV-028); cross-module HARD-FK, column only (XM-INV-008)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.LOT_FK IS 'DBF-INV-168 · ENT-INV-011.lotFk · intra-module FK (TENANT_ID, LOT_FK) to INV_LOT; resolved from LOT_NO (RULE-INV-037)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.TENANT_ID IS 'DBF-INV-169 · ENT-INV-011.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.VERSION IS 'DBF-INV-170 · ENT-INV-011.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.CURRENCY_CODE IS 'DBF-INV-171 · ENT-INV-011.currencyCode · ISO 4217 currency of UNIT_COST and LINE_VALUE — the tenant base currency (CM-8; ADR-INV-015)';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.CREATED_BY IS 'DBF-INV-172 · ENT-INV-011.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.CREATED_AT IS 'DBF-INV-173 · ENT-INV-011.createdAt · platform-filled';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.UPDATED_BY IS 'DBF-INV-174 · ENT-INV-011.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_ADJUSTMENT_LINE.UPDATED_AT IS 'DBF-INV-175 · ENT-INV-011.updatedAt · platform-filled';

COMMENT ON TABLE INV_LOT IS 'ENT-INV-012 Lot — a lot number of a Variant with one expiry date (master, SHARED owner; CON-INV-003)';
COMMENT ON COLUMN INV_LOT.LOT_PK IS 'DBF-INV-176 · ENT-INV-012.lotPk · identity key (BIGINT)';
COMMENT ON COLUMN INV_LOT.EXPIRY_DATE IS 'DBF-INV-177 · ENT-INV-012.expiryDate · one expiry per lot (RULE-INV-036); a lot is expired when this date is before today in the tenant time zone (RULE-INV-038, RULE-INV-039)';
COMMENT ON COLUMN INV_LOT.FIRST_RECEIVED_AT IS 'DBF-INV-178 · ENT-INV-012.firstReceivedAt · time of the first receipt';
COMMENT ON COLUMN INV_LOT.NEAR_EXPIRY_NOTIFIED_AT IS 'DBF-INV-179 · ENT-INV-012.nearExpiryNotifiedAt · set when the near-expiry alert is sent; one alert per lot (REQ-INV-117, SRS D-19)';
COMMENT ON COLUMN INV_LOT.VARIANT_FK IS 'DBF-INV-180 · ENT-INV-012.variantFk · CAT Variant identifier (BIGINT, CON-CAT-002); cross-module HARD-FK, column only (XM-INV-009)';
COMMENT ON COLUMN INV_LOT.CODE IS 'DBF-INV-181 · ENT-INV-012.code · the lot number written on the goods, unique per Variant with TENANT_ID (RULE-INV-036)';
COMMENT ON COLUMN INV_LOT.NAME_AR IS 'DBF-INV-182 · ENT-INV-012.nameAr · optional; displays CODE when empty (SRS D-02)';
COMMENT ON COLUMN INV_LOT.NAME_EN IS 'DBF-INV-183 · ENT-INV-012.nameEn · optional; displays CODE when empty';
COMMENT ON COLUMN INV_LOT.IS_ACTIVE_FL IS 'DBF-INV-184 · ENT-INV-012.isActiveFl · false once the lot holds no stock in any warehouse';
COMMENT ON COLUMN INV_LOT.TENANT_ID IS 'DBF-INV-185 · ENT-INV-012.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_LOT.VERSION IS 'DBF-INV-186 · ENT-INV-012.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_LOT.CREATED_BY IS 'DBF-INV-187 · ENT-INV-012.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_LOT.CREATED_AT IS 'DBF-INV-188 · ENT-INV-012.createdAt · platform-filled';
COMMENT ON COLUMN INV_LOT.UPDATED_BY IS 'DBF-INV-189 · ENT-INV-012.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_LOT.UPDATED_AT IS 'DBF-INV-190 · ENT-INV-012.updatedAt · platform-filled';

COMMENT ON TABLE INV_STOCK_VALUATION IS 'ENT-INV-013 Stock valuation — perpetual weighted-average cost of one Variant across the tenant, maintained only by stock moves (transactional, SHARED owner; CON-INV-005)';
COMMENT ON COLUMN INV_STOCK_VALUATION.STOCK_VALUATION_PK IS 'DBF-INV-191 · ENT-INV-013.stockValuationPk · identity key (BIGINT)';
COMMENT ON COLUMN INV_STOCK_VALUATION.TOTAL_ON_HAND_QTY IS 'DBF-INV-192 · ENT-INV-013.totalOnHandQty · sum of on-hand over all warehouses and lots';
COMMENT ON COLUMN INV_STOCK_VALUATION.AVERAGE_UNIT_COST IS 'DBF-INV-193 · ENT-INV-013.averageUnitCost · perpetual weighted average, 4 decimals, 0 or more (REQ-INV-059, REQ-INV-060, RULE-INV-025)';
COMMENT ON COLUMN INV_STOCK_VALUATION.STOCK_VALUE IS 'DBF-INV-194 · ENT-INV-013.stockValue · TOTAL_ON_HAND_QTY x AVERAGE_UNIT_COST, 2 decimals, in CURRENCY_CODE';
COMMENT ON COLUMN INV_STOCK_VALUATION.LAST_MOVE_AT IS 'DBF-INV-195 · ENT-INV-013.lastMoveAt · time of the last valued move';
COMMENT ON COLUMN INV_STOCK_VALUATION.VARIANT_FK IS 'DBF-INV-196 · ENT-INV-013.variantFk · CAT Variant identifier (BIGINT, CON-CAT-002), one row per Variant; cross-module HARD-FK, column only (XM-INV-010)';
COMMENT ON COLUMN INV_STOCK_VALUATION.DOC_DATE IS 'DBF-INV-197 · ENT-INV-013.docDate · date of the first valued move (standard field)';
COMMENT ON COLUMN INV_STOCK_VALUATION.TENANT_ID IS 'DBF-INV-198 · ENT-INV-013.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_STOCK_VALUATION.VERSION IS 'DBF-INV-199 · ENT-INV-013.version · optimistic-lock version (ADR-INV-014); valuation updates are serialised per Variant (REQ-INV-037)';
COMMENT ON COLUMN INV_STOCK_VALUATION.CURRENCY_CODE IS 'DBF-INV-200 · ENT-INV-013.currencyCode · ISO 4217 currency of AVERAGE_UNIT_COST and STOCK_VALUE — the tenant base currency (CM-8; ADR-INV-015)';
COMMENT ON COLUMN INV_STOCK_VALUATION.CREATED_BY IS 'DBF-INV-201 · ENT-INV-013.createdBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_VALUATION.CREATED_AT IS 'DBF-INV-202 · ENT-INV-013.createdAt · platform-filled';
COMMENT ON COLUMN INV_STOCK_VALUATION.UPDATED_BY IS 'DBF-INV-203 · ENT-INV-013.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_STOCK_VALUATION.UPDATED_AT IS 'DBF-INV-204 · ENT-INV-013.updatedAt · platform-filled';

COMMENT ON TABLE INV_ITEM_STOCK_SETTINGS IS 'ENT-INV-014 Item stock settings — lot tracking and dual quantity of one STANDARD Item (config, PRIVATE; read through CON-INV-018, CON-INV-019)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.ITEM_STOCK_SETTINGS_PK IS 'DBF-INV-205 · ENT-INV-014.itemStockSettingsPk · identity key (BIGINT)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.LOT_TRACKED_FL IS 'DBF-INV-206 · ENT-INV-014.lotTrackedFl · settable only while lotExpiry is on (RULE-INV-044); cleared only when no lot holds stock (RULE-INV-042)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.DUAL_QUANTITY_FL IS 'DBF-INV-207 · ENT-INV-014.dualQuantityFl · settable only while dualQuantity is on (RULE-INV-044); cleared only when no measure is held (RULE-INV-043)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.MEASURE_UOM_CODE IS 'DBF-INV-208 · ENT-INV-014.measureUomCode · code of the CAT-owned unit-of-measure lookup (given mdl, consumed); required when DUAL_QUANTITY_FL is true (RULE-INV-041); no XM';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.ITEM_FK IS 'DBF-INV-209 · ENT-INV-014.itemFk · CAT Item identifier (BIGINT, CON-CAT-001) of a STANDARD Item, one row per Item (RULE-INV-045); cross-module HARD-FK, column only (XM-INV-011)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.KEY IS 'DBF-INV-210 · ENT-INV-014.key · the Item code (CON-CAT-001), system-filled, read-only';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.VALUE_AR IS 'DBF-INV-211 · ENT-INV-014.valueAr · system-filled tracking summary (Arabic)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.VALUE_EN IS 'DBF-INV-212 · ENT-INV-014.valueEn · system-filled tracking summary (English)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.IS_ACTIVE_FL IS 'DBF-INV-213 · ENT-INV-014.isActiveFl · standard config flag';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.TENANT_ID IS 'DBF-INV-214 · ENT-INV-014.tenantId · platform column of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.VERSION IS 'DBF-INV-215 · ENT-INV-014.version · optimistic-lock version of the given AuditableEntity (ADR-INV-014)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.CREATED_BY IS 'DBF-INV-216 · ENT-INV-014.createdBy · platform-filled principal (given AuditableEntity; REQ-INV-077)';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.CREATED_AT IS 'DBF-INV-217 · ENT-INV-014.createdAt · platform-filled';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.UPDATED_BY IS 'DBF-INV-218 · ENT-INV-014.updatedBy · platform-filled principal';
COMMENT ON COLUMN INV_ITEM_STOCK_SETTINGS.UPDATED_AT IS 'DBF-INV-219 · ENT-INV-014.updatedAt · platform-filled';

-- ---------------------------------------------------------------------
-- BLOCK 5 — CONSTRAINTS
-- ---------------------------------------------------------------------

-- 5a PRIMARY KEYS
ALTER TABLE INV_WAREHOUSE ADD CONSTRAINT PK_INV_WAREHOUSE PRIMARY KEY (WAREHOUSE_PK);
ALTER TABLE INV_BRANCH ADD CONSTRAINT PK_INV_BRANCH PRIMARY KEY (BRANCH_PK);
ALTER TABLE INV_STOCK_BALANCE ADD CONSTRAINT PK_INV_STOCK_BALANCE PRIMARY KEY (STOCK_BALANCE_PK);
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT PK_INV_STOCK_MOVE PRIMARY KEY (STOCK_MOVE_PK);
ALTER TABLE INV_RESERVATION ADD CONSTRAINT PK_INV_RESERVATION PRIMARY KEY (RESERVATION_PK);
ALTER TABLE INV_TRANSFER ADD CONSTRAINT PK_INV_TRANSFER PRIMARY KEY (TRANSFER_PK);
ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT PK_INV_TRANSFER_LINE PRIMARY KEY (TRANSFER_LINE_PK);
ALTER TABLE INV_STOCK_COUNT ADD CONSTRAINT PK_INV_STOCK_COUNT PRIMARY KEY (STOCK_COUNT_PK);
ALTER TABLE INV_STOCK_COUNT_LINE ADD CONSTRAINT PK_INV_STOCK_COUNT_LINE PRIMARY KEY (STOCK_COUNT_LINE_PK);
ALTER TABLE INV_STOCK_ADJUSTMENT ADD CONSTRAINT PK_INV_STOCK_ADJUSTMENT PRIMARY KEY (STOCK_ADJUSTMENT_PK);
ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT PK_INV_STOCK_ADJUSTMENT_LINE PRIMARY KEY (STOCK_ADJUSTMENT_LINE_PK);
ALTER TABLE INV_LOT ADD CONSTRAINT PK_INV_LOT PRIMARY KEY (LOT_PK);
ALTER TABLE INV_STOCK_VALUATION ADD CONSTRAINT PK_INV_STOCK_VALUATION PRIMARY KEY (STOCK_VALUATION_PK);
ALTER TABLE INV_ITEM_STOCK_SETTINGS ADD CONSTRAINT PK_INV_ITEM_STOCK_SETTINGS PRIMARY KEY (ITEM_STOCK_SETTINGS_PK);

-- 5b UNIQUE — every one led by TENANT_ID (ADR-INV-014)
-- Tenant-scoped parent keys, targets of the composite intra-module FKs (5d)
ALTER TABLE INV_BRANCH ADD CONSTRAINT UQ_INV_BRANCH_TENANT_PK UNIQUE (TENANT_ID, BRANCH_PK);
ALTER TABLE INV_WAREHOUSE ADD CONSTRAINT UQ_INV_WAREHOUSE_TENANT_PK UNIQUE (TENANT_ID, WAREHOUSE_PK);
ALTER TABLE INV_LOT ADD CONSTRAINT UQ_INV_LOT_TENANT_PK UNIQUE (TENANT_ID, LOT_PK);
ALTER TABLE INV_RESERVATION ADD CONSTRAINT UQ_INV_RESERVATION_TENANT_PK UNIQUE (TENANT_ID, RESERVATION_PK);
ALTER TABLE INV_TRANSFER ADD CONSTRAINT UQ_INV_TRANSFER_TENANT_PK UNIQUE (TENANT_ID, TRANSFER_PK);
ALTER TABLE INV_STOCK_COUNT ADD CONSTRAINT UQ_INV_STOCK_COUNT_TENANT_PK UNIQUE (TENANT_ID, STOCK_COUNT_PK);
ALTER TABLE INV_STOCK_ADJUSTMENT ADD CONSTRAINT UQ_INV_STOCK_ADJUSTMENT_TENANT_PK UNIQUE (TENANT_ID, STOCK_ADJUSTMENT_PK);
-- Codes and names of warehouses and branches (RULE-INV-001, RULE-INV-002, RULE-INV-010, RULE-INV-011)
ALTER TABLE INV_WAREHOUSE ADD CONSTRAINT UQ_INV_WAREHOUSE_CODE UNIQUE (TENANT_ID, CODE);
ALTER TABLE INV_WAREHOUSE ADD CONSTRAINT UQ_INV_WAREHOUSE_NAME_AR UNIQUE (TENANT_ID, NAME_AR);
ALTER TABLE INV_BRANCH ADD CONSTRAINT UQ_INV_BRANCH_CODE UNIQUE (TENANT_ID, CODE);
ALTER TABLE INV_BRANCH ADD CONSTRAINT UQ_INV_BRANCH_NAME_AR UNIQUE (TENANT_ID, NAME_AR);
-- One Stock balance per Variant x warehouse x lot, an empty lot counted as one value (REQ-INV-035, CON-INV-004)
ALTER TABLE INV_STOCK_BALANCE ADD CONSTRAINT UQ_INV_STOCK_BALANCE_VAR_WH_LOT
    UNIQUE NULLS NOT DISTINCT (TENANT_ID, VARIANT_FK, WAREHOUSE_FK, LOT_FK);
-- Document numbers unique per document type (RULE-INV-030)
ALTER TABLE INV_TRANSFER ADD CONSTRAINT UQ_INV_TRANSFER_DOC_NO UNIQUE (TENANT_ID, DOC_NO);
ALTER TABLE INV_STOCK_COUNT ADD CONSTRAINT UQ_INV_STOCK_COUNT_DOC_NO UNIQUE (TENANT_ID, DOC_NO);
ALTER TABLE INV_STOCK_ADJUSTMENT ADD CONSTRAINT UQ_INV_STOCK_ADJUSTMENT_DOC_NO UNIQUE (TENANT_ID, DOC_NO);
-- One line per Variant and lot on a transfer or adjustment (RULE-INV-048); repeated scans add to one count line (REQ-INV-088, SRS D-14)
ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT UQ_INV_TRANSFER_LINE_TRF_VAR_LOT
    UNIQUE NULLS NOT DISTINCT (TENANT_ID, TRANSFER_FK, VARIANT_FK, LOT_FK);
ALTER TABLE INV_STOCK_COUNT_LINE ADD CONSTRAINT UQ_INV_STOCK_COUNT_LINE_CNT_VAR_LOT
    UNIQUE NULLS NOT DISTINCT (TENANT_ID, STOCK_COUNT_FK, VARIANT_FK, LOT_FK);
ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_VAR_LOT
    UNIQUE NULLS NOT DISTINCT (TENANT_ID, STOCK_ADJUSTMENT_FK, VARIANT_FK, LOT_NO);
-- The row number identifies a line of an adjustment, as reported by a bulk load (REQ-INV-072)
ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_ROW
    UNIQUE (TENANT_ID, STOCK_ADJUSTMENT_FK, ROW_NO);
-- One lot number per Variant (RULE-INV-036)
ALTER TABLE INV_LOT ADD CONSTRAINT UQ_INV_LOT_VAR_CODE UNIQUE (TENANT_ID, VARIANT_FK, CODE);
-- One Stock valuation per Variant across the tenant (ENT-INV-013, CON-INV-005)
ALTER TABLE INV_STOCK_VALUATION ADD CONSTRAINT UQ_INV_STOCK_VALUATION_VARIANT_FK UNIQUE (TENANT_ID, VARIANT_FK);
-- At most one Item stock settings record per Item (RULE-INV-045)
ALTER TABLE INV_ITEM_STOCK_SETTINGS ADD CONSTRAINT UQ_INV_ITEM_STOCK_SETTINGS_ITEM_FK UNIQUE (TENANT_ID, ITEM_FK);
-- Conditional uniqueness: postgresql16 expresses a partial UNIQUE only as a
-- unique index (not an ALTER TABLE constraint); each leads with TENANT_ID.
-- At most one default warehouse and one default branch per tenant (RULE-INV-006, RULE-INV-015)
CREATE UNIQUE INDEX UQ_INV_WAREHOUSE_IS_DEFAULT_FL ON INV_WAREHOUSE (TENANT_ID) WHERE IS_DEFAULT_FL;
CREATE UNIQUE INDEX UQ_INV_BRANCH_IS_DEFAULT_FL ON INV_BRANCH (TENANT_ID) WHERE IS_DEFAULT_FL;

-- 5c CHECK
-- RULE-INV-006 — the default warehouse is active
ALTER TABLE INV_WAREHOUSE ADD CONSTRAINT CHK_INV_WAREHOUSE_IS_DEFAULT_FL
    CHECK (NOT IS_DEFAULT_FL OR IS_ACTIVE_FL);
-- RULE-INV-002 — a non-blank Arabic name
ALTER TABLE INV_WAREHOUSE ADD CONSTRAINT CHK_INV_WAREHOUSE_NAME_AR
    CHECK (BTRIM(NAME_AR) <> '');
-- RULE-INV-015 — the default branch is active
ALTER TABLE INV_BRANCH ADD CONSTRAINT CHK_INV_BRANCH_IS_DEFAULT_FL
    CHECK (NOT IS_DEFAULT_FL OR IS_ACTIVE_FL);
-- RULE-INV-011 — a non-blank Arabic name
ALTER TABLE INV_BRANCH ADD CONSTRAINT CHK_INV_BRANCH_NAME_AR
    CHECK (BTRIM(NAME_AR) <> '');
-- SRS A6 — closed set INV_MOVE_TYPE (fixed by REQ-INV-051, 052, 080, 099)
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT CHK_INV_STOCK_MOVE_MOVE_TYPE_CODE
    CHECK (MOVE_TYPE_CODE IN ('RECEIPT', 'RETURN_IN', 'ISSUE', 'RETURN_OUT', 'TRANSFER_OUT', 'TRANSFER_IN', 'ADJUSTMENT_IN', 'ADJUSTMENT_OUT'));
-- RULE-INV-017 — quantity greater than 0; a measure-only adjustment move carries 0 with a measure (REQ-INV-123, ADR-INV-017)
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT CHK_INV_STOCK_MOVE_QUANTITY
    CHECK (QUANTITY > 0 OR (QUANTITY = 0 AND MOVE_TYPE_CODE IN ('ADJUSTMENT_IN', 'ADJUSTMENT_OUT') AND COALESCE(MEASURE_QTY, 0) > 0));
-- RULE-INV-040 — a measure, when present, is greater than 0
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT CHK_INV_STOCK_MOVE_MEASURE_QTY
    CHECK (MEASURE_QTY IS NULL OR MEASURE_QTY > 0);
-- RULE-INV-025 — unit cost 0 or more
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT CHK_INV_STOCK_MOVE_UNIT_COST
    CHECK (UNIT_COST >= 0);
-- ADR-INV-015 — an ISO 4217 code (three capital letters)
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT CHK_INV_STOCK_MOVE_CURRENCY_CODE
    CHECK (CURRENCY_CODE ~ '^[A-Z]{3}$');
-- SRS A7 — status values of the closed reservation lifecycle (INV_RESERVATION_STATUS)
ALTER TABLE INV_RESERVATION ADD CONSTRAINT CHK_INV_RESERVATION_STATUS_CODE
    CHECK (STATUS_CODE IN ('ACTIVE', 'CONSUMED', 'RELEASED'));
-- RULE-INV-017 — requested quantity greater than 0
ALTER TABLE INV_RESERVATION ADD CONSTRAINT CHK_INV_RESERVATION_ORIGINAL_QTY
    CHECK (ORIGINAL_QTY > 0);
-- ENT-INV-005 — remaining quantity 0 or more and never above the requested quantity
ALTER TABLE INV_RESERVATION ADD CONSTRAINT CHK_INV_RESERVATION_RESERVED_QTY
    CHECK (RESERVED_QTY >= 0 AND RESERVED_QTY <= ORIGINAL_QTY);
-- RULE-INV-020 — shortfall 0 or more
ALTER TABLE INV_RESERVATION ADD CONSTRAINT CHK_INV_RESERVATION_SHORTFALL_QTY
    CHECK (SHORTFALL_QTY >= 0);
-- REQ-INV-046, REQ-INV-047 — a closed reservation records when it closed
ALTER TABLE INV_RESERVATION ADD CONSTRAINT CHK_INV_RESERVATION_CLOSED_AT
    CHECK (STATUS_CODE = 'ACTIVE' OR CLOSED_AT IS NOT NULL);
-- SRS A7 — status values of the closed document lifecycle (INV_DOC_STATUS)
ALTER TABLE INV_TRANSFER ADD CONSTRAINT CHK_INV_TRANSFER_STATUS_CODE
    CHECK (STATUS_CODE IN ('DRAFT', 'POSTED', 'CANCELLED'));
-- RULE-INV-016 — source and destination differ
ALTER TABLE INV_TRANSFER ADD CONSTRAINT CHK_INV_TRANSFER_TO_WAREHOUSE_FK
    CHECK (FROM_WAREHOUSE_FK <> TO_WAREHOUSE_FK);
-- REQ-INV-080 — a posted transfer records when it was posted
ALTER TABLE INV_TRANSFER ADD CONSTRAINT CHK_INV_TRANSFER_POSTED_AT
    CHECK (STATUS_CODE <> 'POSTED' OR POSTED_AT IS NOT NULL);
-- RULE-INV-017 — quantity greater than 0
ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT CHK_INV_TRANSFER_LINE_QUANTITY
    CHECK (QUANTITY > 0);
-- RULE-INV-040 — a measure, when present, is greater than 0
ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT CHK_INV_TRANSFER_LINE_MEASURE_QTY
    CHECK (MEASURE_QTY IS NULL OR MEASURE_QTY > 0);
-- RULE-INV-025 — unit cost 0 or more
ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT CHK_INV_TRANSFER_LINE_UNIT_COST
    CHECK (UNIT_COST IS NULL OR UNIT_COST >= 0);
-- ADR-INV-015 — an ISO 4217 code
ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT CHK_INV_TRANSFER_LINE_CURRENCY_CODE
    CHECK (CURRENCY_CODE ~ '^[A-Z]{3}$');
-- SRS A7 — INV_DOC_STATUS
ALTER TABLE INV_STOCK_COUNT ADD CONSTRAINT CHK_INV_STOCK_COUNT_STATUS_CODE
    CHECK (STATUS_CODE IN ('DRAFT', 'POSTED', 'CANCELLED'));
-- REQ-INV-092 — a posted count records when it was posted
ALTER TABLE INV_STOCK_COUNT ADD CONSTRAINT CHK_INV_STOCK_COUNT_POSTED_AT
    CHECK (STATUS_CODE <> 'POSTED' OR POSTED_AT IS NOT NULL);
-- RULE-INV-047 — counted quantity 0 or more
ALTER TABLE INV_STOCK_COUNT_LINE ADD CONSTRAINT CHK_INV_STOCK_COUNT_LINE_COUNTED_QTY
    CHECK (COUNTED_QTY >= 0);
-- ENT-INV-009 — counted measure, when present, 0 or more
ALTER TABLE INV_STOCK_COUNT_LINE ADD CONSTRAINT CHK_INV_STOCK_COUNT_LINE_COUNTED_MEASURE_QTY
    CHECK (COUNTED_MEASURE_QTY IS NULL OR COUNTED_MEASURE_QTY >= 0);
-- SRS A7 — INV_DOC_STATUS
ALTER TABLE INV_STOCK_ADJUSTMENT ADD CONSTRAINT CHK_INV_STOCK_ADJUSTMENT_STATUS_CODE
    CHECK (STATUS_CODE IN ('DRAFT', 'POSTED', 'CANCELLED'));
-- REQ-INV-099 — a posted adjustment records when it was posted
ALTER TABLE INV_STOCK_ADJUSTMENT ADD CONSTRAINT CHK_INV_STOCK_ADJUSTMENT_POSTED_AT
    CHECK (STATUS_CODE <> 'POSTED' OR POSTED_AT IS NOT NULL);
-- RULE-INV-025, RULE-INV-026 — unit cost, when present, 0 or more
ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT CHK_INV_STOCK_ADJUSTMENT_LINE_UNIT_COST
    CHECK (UNIT_COST IS NULL OR UNIT_COST >= 0);
-- RULE-INV-027 — row numbers start at 1
ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT CHK_INV_STOCK_ADJUSTMENT_LINE_ROW_NO
    CHECK (ROW_NO >= 1);
-- ADR-INV-015 — an ISO 4217 code
ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT CHK_INV_STOCK_ADJUSTMENT_LINE_CURRENCY_CODE
    CHECK (CURRENCY_CODE ~ '^[A-Z]{3}$');
-- RULE-INV-036 — a non-blank lot number
ALTER TABLE INV_LOT ADD CONSTRAINT CHK_INV_LOT_CODE
    CHECK (BTRIM(CODE) <> '');
-- REQ-INV-059 — average unit cost 0 or more
ALTER TABLE INV_STOCK_VALUATION ADD CONSTRAINT CHK_INV_STOCK_VALUATION_AVERAGE_UNIT_COST
    CHECK (AVERAGE_UNIT_COST >= 0);
-- ADR-INV-015 — an ISO 4217 code
ALTER TABLE INV_STOCK_VALUATION ADD CONSTRAINT CHK_INV_STOCK_VALUATION_CURRENCY_CODE
    CHECK (CURRENCY_CODE ~ '^[A-Z]{3}$');
-- RULE-INV-041 — a dual-quantity Item has a measure unit
ALTER TABLE INV_ITEM_STOCK_SETTINGS ADD CONSTRAINT CHK_INV_ITEM_STOCK_SETTINGS_MEASURE_UOM_CODE
    CHECK (NOT DUAL_QUANTITY_FL OR MEASURE_UOM_CODE IS NOT NULL);

-- 5d FOREIGN KEYS (no ON DELETE action — deactivation, not deletion, ARCH-5)
-- Tenant FKs to the given core table (the only core FK the core documents)
ALTER TABLE INV_WAREHOUSE ADD CONSTRAINT FK_INV_WAREHOUSE_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_BRANCH ADD CONSTRAINT FK_INV_BRANCH_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_STOCK_BALANCE ADD CONSTRAINT FK_INV_STOCK_BALANCE_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT FK_INV_STOCK_MOVE_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_RESERVATION ADD CONSTRAINT FK_INV_RESERVATION_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_TRANSFER ADD CONSTRAINT FK_INV_TRANSFER_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT FK_INV_TRANSFER_LINE_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_STOCK_COUNT ADD CONSTRAINT FK_INV_STOCK_COUNT_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_STOCK_COUNT_LINE ADD CONSTRAINT FK_INV_STOCK_COUNT_LINE_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_STOCK_ADJUSTMENT ADD CONSTRAINT FK_INV_STOCK_ADJUSTMENT_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT FK_INV_STOCK_ADJUSTMENT_LINE_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_LOT ADD CONSTRAINT FK_INV_LOT_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_STOCK_VALUATION ADD CONSTRAINT FK_INV_STOCK_VALUATION_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE INV_ITEM_STOCK_SETTINGS ADD CONSTRAINT FK_INV_ITEM_STOCK_SETTINGS_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
-- Intra-module FKs, composite with TENANT_ID: a child points to a parent of its own tenant (ADR-INV-014)
ALTER TABLE INV_WAREHOUSE ADD CONSTRAINT FK_INV_WAREHOUSE_INV_BRANCH
    FOREIGN KEY (TENANT_ID, BRANCH_FK) REFERENCES INV_BRANCH (TENANT_ID, BRANCH_PK);
ALTER TABLE INV_STOCK_BALANCE ADD CONSTRAINT FK_INV_STOCK_BALANCE_INV_WAREHOUSE
    FOREIGN KEY (TENANT_ID, WAREHOUSE_FK) REFERENCES INV_WAREHOUSE (TENANT_ID, WAREHOUSE_PK);
ALTER TABLE INV_STOCK_BALANCE ADD CONSTRAINT FK_INV_STOCK_BALANCE_INV_LOT
    FOREIGN KEY (TENANT_ID, LOT_FK) REFERENCES INV_LOT (TENANT_ID, LOT_PK);
ALTER TABLE INV_RESERVATION ADD CONSTRAINT FK_INV_RESERVATION_INV_WAREHOUSE
    FOREIGN KEY (TENANT_ID, WAREHOUSE_FK) REFERENCES INV_WAREHOUSE (TENANT_ID, WAREHOUSE_PK);
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT FK_INV_STOCK_MOVE_INV_WAREHOUSE
    FOREIGN KEY (TENANT_ID, WAREHOUSE_FK) REFERENCES INV_WAREHOUSE (TENANT_ID, WAREHOUSE_PK);
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT FK_INV_STOCK_MOVE_INV_LOT
    FOREIGN KEY (TENANT_ID, LOT_FK) REFERENCES INV_LOT (TENANT_ID, LOT_PK);
ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT FK_INV_STOCK_MOVE_INV_RESERVATION
    FOREIGN KEY (TENANT_ID, RESERVATION_FK) REFERENCES INV_RESERVATION (TENANT_ID, RESERVATION_PK);
ALTER TABLE INV_TRANSFER ADD CONSTRAINT FK_INV_TRANSFER_INV_WAREHOUSE_1
    FOREIGN KEY (TENANT_ID, FROM_WAREHOUSE_FK) REFERENCES INV_WAREHOUSE (TENANT_ID, WAREHOUSE_PK);
ALTER TABLE INV_TRANSFER ADD CONSTRAINT FK_INV_TRANSFER_INV_WAREHOUSE_2
    FOREIGN KEY (TENANT_ID, TO_WAREHOUSE_FK) REFERENCES INV_WAREHOUSE (TENANT_ID, WAREHOUSE_PK);
ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT FK_INV_TRANSFER_LINE_INV_TRANSFER
    FOREIGN KEY (TENANT_ID, TRANSFER_FK) REFERENCES INV_TRANSFER (TENANT_ID, TRANSFER_PK);
ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT FK_INV_TRANSFER_LINE_INV_LOT
    FOREIGN KEY (TENANT_ID, LOT_FK) REFERENCES INV_LOT (TENANT_ID, LOT_PK);
ALTER TABLE INV_STOCK_COUNT ADD CONSTRAINT FK_INV_STOCK_COUNT_INV_WAREHOUSE
    FOREIGN KEY (TENANT_ID, WAREHOUSE_FK) REFERENCES INV_WAREHOUSE (TENANT_ID, WAREHOUSE_PK);
ALTER TABLE INV_STOCK_COUNT ADD CONSTRAINT FK_INV_STOCK_COUNT_INV_STOCK_ADJUSTMENT
    FOREIGN KEY (TENANT_ID, ADJUSTMENT_FK) REFERENCES INV_STOCK_ADJUSTMENT (TENANT_ID, STOCK_ADJUSTMENT_PK);
ALTER TABLE INV_STOCK_COUNT_LINE ADD CONSTRAINT FK_INV_STOCK_COUNT_LINE_INV_STOCK_COUNT
    FOREIGN KEY (TENANT_ID, STOCK_COUNT_FK) REFERENCES INV_STOCK_COUNT (TENANT_ID, STOCK_COUNT_PK);
ALTER TABLE INV_STOCK_COUNT_LINE ADD CONSTRAINT FK_INV_STOCK_COUNT_LINE_INV_LOT
    FOREIGN KEY (TENANT_ID, LOT_FK) REFERENCES INV_LOT (TENANT_ID, LOT_PK);
ALTER TABLE INV_STOCK_ADJUSTMENT ADD CONSTRAINT FK_INV_STOCK_ADJUSTMENT_INV_WAREHOUSE
    FOREIGN KEY (TENANT_ID, WAREHOUSE_FK) REFERENCES INV_WAREHOUSE (TENANT_ID, WAREHOUSE_PK);
ALTER TABLE INV_STOCK_ADJUSTMENT ADD CONSTRAINT FK_INV_STOCK_ADJUSTMENT_INV_STOCK_COUNT
    FOREIGN KEY (TENANT_ID, SOURCE_COUNT_FK) REFERENCES INV_STOCK_COUNT (TENANT_ID, STOCK_COUNT_PK);
ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT FK_INV_STOCK_ADJUSTMENT_LINE_INV_STOCK_ADJUSTMENT
    FOREIGN KEY (TENANT_ID, STOCK_ADJUSTMENT_FK) REFERENCES INV_STOCK_ADJUSTMENT (TENANT_ID, STOCK_ADJUSTMENT_PK);
ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT FK_INV_STOCK_ADJUSTMENT_LINE_INV_LOT
    FOREIGN KEY (TENANT_ID, LOT_FK) REFERENCES INV_LOT (TENANT_ID, LOT_PK);
-- Cross-module (CAT) references are column-only (§5.1): the eleven HARD-FK
-- columns carry their pointer beside the column in BLOCK 2 / BLOCK 3
-- (XM-INV-001 … XM-INV-011); their constraints are written once, in the
-- integration blocks of the backend plan (P3.1).
--
-- XM-INV-012 SOFT-READ — this module's bundle availability, reservation and issue
-- services read CAT Bundle component lines (ENT-CAT-007: componentVariantFk,
-- quantity, componentBaseUomCode) from CAT without an FK, through getItem
-- (CON-CAT-007; entity promise CON-CAT-006). Rationale: a bundle holds no stock;
-- its availability, reservation and issue explode to its components (POL-INV-014,
-- SRS A8). Risk: changes to ENT-CAT-007 require impact assessment on REQ-INV-042,
-- REQ-INV-049, REQ-INV-053, REQ-INV-054.
--
-- XM-INV-013 SOFT-READ — this module's count-line and opening-stock services read
-- CAT barcodes (ENT-CAT-009: barcodeValue to variantPk) from CAT without an FK,
-- through lookupBarcode (CON-CAT-010; entity promise CON-CAT-005). Rationale: count
-- scanning and opening-stock rows resolve a barcode to its Variant (SRS A8).
-- Risk: changes to ENT-CAT-009 require impact assessment on REQ-INV-072,
-- REQ-INV-088, REQ-INV-091.

-- ---------------------------------------------------------------------
-- BLOCK 6 — TRIGGERS
-- none: no SRS RULE requires a database trigger. Change history
-- (REQ-INV-077) is the given audit module (AuditApi); the immutability of
-- stock moves (RULE-INV-023), the read-only codes and numbers (RULE-INV-001,
-- 010, 030), the balance and valuation updates (REQ-INV-036, 059) and the
-- read-only posted documents (RULE-INV-029) are service behaviour (P3.1).
-- ---------------------------------------------------------------------

-- ---------------------------------------------------------------------
-- BLOCK 7 — INDEXES (non-PK). One index per table is led by TENANT_ID.
-- FK columns already covered by a UNIQUE whose leading columns are
-- (TENANT_ID, FK) are not indexed twice: INV_STOCK_BALANCE.VARIANT_FK,
-- INV_TRANSFER_LINE.TRANSFER_FK, INV_STOCK_COUNT_LINE.STOCK_COUNT_FK,
-- INV_STOCK_ADJUSTMENT_LINE.STOCK_ADJUSTMENT_FK, INV_LOT.VARIANT_FK,
-- INV_STOCK_VALUATION.VARIANT_FK, INV_ITEM_STOCK_SETTINGS.ITEM_FK.
-- ---------------------------------------------------------------------

-- Tenant-led indexes, each serving a list or check of the SRS
-- active warehouse / branch lists and counts (REQ-INV-010, 019, 029, 033)
CREATE INDEX IDX_INV_WAREHOUSE_TENANT ON INV_WAREHOUSE (TENANT_ID, IS_ACTIVE_FL);
CREATE INDEX IDX_INV_BRANCH_TENANT ON INV_BRANCH (TENANT_ID, IS_ACTIVE_FL);
-- balances of a warehouse; deactivation check (RULE-INV-004); FK WAREHOUSE_FK
CREATE INDEX IDX_INV_STOCK_BALANCE_TENANT ON INV_STOCK_BALANCE (TENANT_ID, WAREHOUSE_FK);
-- move history of a Variant in a warehouse by date (REQ-INV-075, 076); FK VARIANT_FK
CREATE INDEX IDX_INV_STOCK_MOVE_TENANT ON INV_STOCK_MOVE (TENANT_ID, VARIANT_FK, WAREHOUSE_FK, DOC_DATE);
-- reserved quantity of a Variant in a warehouse (REQ-INV-038); FK VARIANT_FK
CREATE INDEX IDX_INV_RESERVATION_TENANT ON INV_RESERVATION (TENANT_ID, VARIANT_FK, WAREHOUSE_FK, STATUS_CODE);
-- document searches by status and date, newest first (REQ-INV-086, 095, 104)
CREATE INDEX IDX_INV_TRANSFER_TENANT ON INV_TRANSFER (TENANT_ID, STATUS_CODE, DOC_DATE);
CREATE INDEX IDX_INV_STOCK_COUNT_TENANT ON INV_STOCK_COUNT (TENANT_ID, STATUS_CODE, DOC_DATE);
CREATE INDEX IDX_INV_STOCK_ADJUSTMENT_TENANT ON INV_STOCK_ADJUSTMENT (TENANT_ID, STATUS_CODE, DOC_DATE);
-- lines by Variant; FK VARIANT_FK
CREATE INDEX IDX_INV_TRANSFER_LINE_TENANT ON INV_TRANSFER_LINE (TENANT_ID, VARIANT_FK);
CREATE INDEX IDX_INV_STOCK_COUNT_LINE_TENANT ON INV_STOCK_COUNT_LINE (TENANT_ID, VARIANT_FK);
CREATE INDEX IDX_INV_STOCK_ADJUSTMENT_LINE_TENANT ON INV_STOCK_ADJUSTMENT_LINE (TENANT_ID, VARIANT_FK);
-- lots by expiry: near-expiry list, daily check, FEFO (REQ-INV-110, 116, 117)
CREATE INDEX IDX_INV_LOT_TENANT ON INV_LOT (TENANT_ID, EXPIRY_DATE);
-- valuation non-zero filter (SCR-REQ-INV-012 B2)
CREATE INDEX IDX_INV_STOCK_VALUATION_TENANT ON INV_STOCK_VALUATION (TENANT_ID, TOTAL_ON_HAND_QTY);
-- capability turn-off check and settings filters (RULE-INV-046; SCR-REQ-INV-009 B2)
CREATE INDEX IDX_INV_ITEM_STOCK_SETTINGS_TENANT ON INV_ITEM_STOCK_SETTINGS (TENANT_ID, LOT_TRACKED_FL, DUAL_QUANTITY_FL);
-- Warehouse: FK BRANCH_FK, branch filter and branch deactivation check (RULE-INV-013; SCR-REQ-INV-001 B2); English name filter
CREATE INDEX IDX_INV_WAREHOUSE_BRANCH_FK ON INV_WAREHOUSE (TENANT_ID, BRANCH_FK, IS_ACTIVE_FL);
CREATE INDEX IDX_INV_WAREHOUSE_NAME_EN ON INV_WAREHOUSE (TENANT_ID, NAME_EN);
-- Branch: English name filter (SCR-REQ-INV-002 B2)
CREATE INDEX IDX_INV_BRANCH_NAME_EN ON INV_BRANCH (TENANT_ID, NAME_EN);
-- Stock balance: FK LOT_FK, lot filter, lot-tracking clear check (RULE-INV-042; SCR-REQ-INV-003 B2)
CREATE INDEX IDX_INV_STOCK_BALANCE_LOT_FK ON INV_STOCK_BALANCE (TENANT_ID, LOT_FK);
-- Stock move: FKs and search filters (SCR-REQ-INV-004 B2), source document (REQ-INV-056, 081, 100), idempotency (RULE-INV-024)
CREATE INDEX IDX_INV_STOCK_MOVE_WAREHOUSE_FK ON INV_STOCK_MOVE (TENANT_ID, WAREHOUSE_FK, DOC_DATE);
CREATE INDEX IDX_INV_STOCK_MOVE_LOT_FK ON INV_STOCK_MOVE (TENANT_ID, LOT_FK);
CREATE INDEX IDX_INV_STOCK_MOVE_BUNDLE_VARIANT_FK ON INV_STOCK_MOVE (TENANT_ID, BUNDLE_VARIANT_FK);
CREATE INDEX IDX_INV_STOCK_MOVE_RESERVATION_FK ON INV_STOCK_MOVE (TENANT_ID, RESERVATION_FK);
CREATE INDEX IDX_INV_STOCK_MOVE_MOVE_TYPE_CODE ON INV_STOCK_MOVE (TENANT_ID, MOVE_TYPE_CODE, DOC_DATE);
CREATE INDEX IDX_INV_STOCK_MOVE_SOURCE_DOC_NO ON INV_STOCK_MOVE (TENANT_ID, SOURCE_DOC_NO);
CREATE INDEX IDX_INV_STOCK_MOVE_SRC_TYPE_REF ON INV_STOCK_MOVE (TENANT_ID, SOURCE_DOC_TYPE_CODE, SOURCE_DOC_REF);
CREATE INDEX IDX_INV_STOCK_MOVE_IDEMPOTENCY_KEY ON INV_STOCK_MOVE (TENANT_ID, IDEMPOTENCY_KEY);
CREATE INDEX IDX_INV_STOCK_MOVE_DOC_DATE ON INV_STOCK_MOVE (TENANT_ID, DOC_DATE);
-- Reservation: FKs, warehouse deactivation check (RULE-INV-004), reservations of a document (REQ-INV-050), idempotency (RULE-INV-024)
CREATE INDEX IDX_INV_RESERVATION_WAREHOUSE_FK ON INV_RESERVATION (TENANT_ID, WAREHOUSE_FK, STATUS_CODE);
CREATE INDEX IDX_INV_RESERVATION_BUNDLE_VARIANT_FK ON INV_RESERVATION (TENANT_ID, BUNDLE_VARIANT_FK);
CREATE INDEX IDX_INV_RESERVATION_SRC_TYPE_REF ON INV_RESERVATION (TENANT_ID, SOURCE_DOC_TYPE_CODE, SOURCE_DOC_REF);
CREATE INDEX IDX_INV_RESERVATION_IDEMPOTENCY_KEY ON INV_RESERVATION (TENANT_ID, IDEMPOTENCY_KEY);
-- Transfer: FKs and warehouse filters (SCR-REQ-INV-005 B2)
CREATE INDEX IDX_INV_TRANSFER_FROM_WAREHOUSE_FK ON INV_TRANSFER (TENANT_ID, FROM_WAREHOUSE_FK);
CREATE INDEX IDX_INV_TRANSFER_TO_WAREHOUSE_FK ON INV_TRANSFER (TENANT_ID, TO_WAREHOUSE_FK);
CREATE INDEX IDX_INV_TRANSFER_DOC_DATE ON INV_TRANSFER (TENANT_ID, DOC_DATE);
-- Transfer line: FK LOT_FK
CREATE INDEX IDX_INV_TRANSFER_LINE_LOT_FK ON INV_TRANSFER_LINE (TENANT_ID, LOT_FK);
-- Stock count: FKs and warehouse filter (SCR-REQ-INV-006 B2)
CREATE INDEX IDX_INV_STOCK_COUNT_WAREHOUSE_FK ON INV_STOCK_COUNT (TENANT_ID, WAREHOUSE_FK);
CREATE INDEX IDX_INV_STOCK_COUNT_ADJUSTMENT_FK ON INV_STOCK_COUNT (TENANT_ID, ADJUSTMENT_FK);
CREATE INDEX IDX_INV_STOCK_COUNT_DOC_DATE ON INV_STOCK_COUNT (TENANT_ID, DOC_DATE);
-- Stock count line: FK LOT_FK
CREATE INDEX IDX_INV_STOCK_COUNT_LINE_LOT_FK ON INV_STOCK_COUNT_LINE (TENANT_ID, LOT_FK);
-- Stock adjustment: FKs, warehouse and reason filters (SCR-REQ-INV-007 B2)
CREATE INDEX IDX_INV_STOCK_ADJUSTMENT_WAREHOUSE_FK ON INV_STOCK_ADJUSTMENT (TENANT_ID, WAREHOUSE_FK);
CREATE INDEX IDX_INV_STOCK_ADJUSTMENT_SOURCE_COUNT_FK ON INV_STOCK_ADJUSTMENT (TENANT_ID, SOURCE_COUNT_FK);
CREATE INDEX IDX_INV_STOCK_ADJUSTMENT_REASON_CODE ON INV_STOCK_ADJUSTMENT (TENANT_ID, REASON_CODE);
CREATE INDEX IDX_INV_STOCK_ADJUSTMENT_DOC_DATE ON INV_STOCK_ADJUSTMENT (TENANT_ID, DOC_DATE);
-- Stock adjustment line: FK LOT_FK
CREATE INDEX IDX_INV_STOCK_ADJUSTMENT_LINE_LOT_FK ON INV_STOCK_ADJUSTMENT_LINE (TENANT_ID, LOT_FK);
-- Lot: lot-number filter across Variants (SCR-REQ-INV-008 B2)
CREATE INDEX IDX_INV_LOT_CODE ON INV_LOT (TENANT_ID, CODE);

-- ---------------------------------------------------------------------
-- BLOCK 8 — SEED DATA INTO GIVEN TABLES (TENANT_ID = 1, PLATFORM)
-- The tables are owned by the given erp-core and only seeded here, never
-- re-created; the keys, series, templates and settings are owned by INV, so
-- INV owns their seed (engine §4.2). The column lists are those erp-core
-- 1.6.0 writes in its own seeds (the form the CAT and PTY migrations use).
-- New tenants receive copies through the given provisioning (mdl, sequence
-- and notif tenant provisioning contributors); cu platform defaults are
-- inherited until a tenant writes an override. Every INSERT is idempotent.
-- No COMMIT: Flyway commits the migration (ADR-INV-014).
-- ---------------------------------------------------------------------

-- 8a — lookup types. Table owned by erp-core modules.mdl (MDL_LOOKUP_TYPE); the keys are owned by INV (SRS A6).
-- OWNER_MODULE_CODE 'INV' is the permission module INV's PermissionContributor declares.
INSERT INTO MDL_LOOKUP_TYPE (LOOKUP_TYPE_PK, TENANT_ID, KEY, OWNER_MODULE_CODE, NAME_AR, NAME_EN, IS_ACTIVE_FL, CREATED_BY, CREATED_AT)
SELECT nextval('SEQ_MDL_LOOKUP_TYPE'), 1, v.TYPE_KEY, 'INV', v.NAME_AR, v.NAME_EN, TRUE, 'SYSTEM', CURRENT_TIMESTAMP
FROM (VALUES
    ('INV_ADJUSTMENT_REASON',  'سبب تسوية المخزون',               'Stock adjustment reason'),
    ('INV_MOVE_TYPE',          'نوع حركة المخزون',                'Stock move type'),
    ('INV_DOC_STATUS',         'حالة مستند المخزون',              'Inventory document status'),
    ('INV_RESERVATION_STATUS', 'حالة حجز المخزون',                'Reservation status'),
    ('INV_SOURCE_DOC_TYPE',    'نوع المستند المصدر',              'Source document type'),
    ('INV_CHANGE_KIND',        'نوع تغيّر بيانات المخزون الأساسية', 'Inventory master-change kind')
) AS v (TYPE_KEY, NAME_AR, NAME_EN)
WHERE NOT EXISTS (SELECT 1 FROM MDL_LOOKUP_TYPE l WHERE l.TENANT_ID = 1 AND l.KEY = v.TYPE_KEY);

-- 8b — lookup values. Table owned by erp-core modules.mdl (MDL_LOOKUP_VALUE); the values are owned by INV (SRS A6, labels verbatim).
-- LOOKUP_TYPE_ID is resolved by KEY within TENANT_ID = 1.
INSERT INTO MDL_LOOKUP_VALUE (LOOKUP_VALUE_PK, TENANT_ID, LOOKUP_TYPE_ID, CODE, NAME_AR, NAME_EN, SORT_ORDER, IS_ACTIVE_FL, CREATED_BY, CREATED_AT)
SELECT nextval('SEQ_MDL_LOOKUP_VALUE'), 1, t.LOOKUP_TYPE_PK, v.CODE, v.NAME_AR, v.NAME_EN, v.SORT_ORDER, TRUE, 'SYSTEM', CURRENT_TIMESTAMP
FROM (VALUES
    -- INV_ADJUSTMENT_REASON (open; SRS D-22)
    ('INV_ADJUSTMENT_REASON',  'COUNT_DIFFERENCE',  'فرق جرد',                 'Count difference',       1),
    ('INV_ADJUSTMENT_REASON',  'DAMAGE',            'تلف',                     'Damage',                 2),
    ('INV_ADJUSTMENT_REASON',  'EXPIRY_WRITE_OFF',  'شطب تشغيلة منتهية',       'Expired lot write-off',  3),
    ('INV_ADJUSTMENT_REASON',  'OPENING_STOCK',     'مخزون افتتاحي',           'Opening stock',          4),
    ('INV_ADJUSTMENT_REASON',  'OTHER',             'أخرى',                    'Other',                  5),
    -- INV_MOVE_TYPE (closed; REQ-INV-051, 052, 080, 099)
    ('INV_MOVE_TYPE',          'RECEIPT',           'استلام',                  'Receipt',                1),
    ('INV_MOVE_TYPE',          'RETURN_IN',         'مرتجع وارد',              'Customer return',        2),
    ('INV_MOVE_TYPE',          'ISSUE',             'صرف',                     'Issue',                  3),
    ('INV_MOVE_TYPE',          'RETURN_OUT',        'مرتجع إلى المورّد',        'Return to supplier',     4),
    ('INV_MOVE_TYPE',          'TRANSFER_OUT',      'تحويل صادر',              'Transfer out',           5),
    ('INV_MOVE_TYPE',          'TRANSFER_IN',       'تحويل وارد',              'Transfer in',            6),
    ('INV_MOVE_TYPE',          'ADJUSTMENT_IN',     'تسوية بالزيادة',          'Adjustment in',          7),
    ('INV_MOVE_TYPE',          'ADJUSTMENT_OUT',    'تسوية بالنقص',            'Adjustment out',         8),
    -- INV_DOC_STATUS (closed; SRS A7)
    ('INV_DOC_STATUS',         'DRAFT',             'مسودة',                   'Draft',                  1),
    ('INV_DOC_STATUS',         'POSTED',            'مُرحَّل',                  'Posted',                 2),
    ('INV_DOC_STATUS',         'CANCELLED',         'ملغى',                    'Cancelled',              3),
    -- INV_RESERVATION_STATUS (closed; SRS A7)
    ('INV_RESERVATION_STATUS', 'ACTIVE',            'قائم',                    'Active',                 1),
    ('INV_RESERVATION_STATUS', 'CONSUMED',          'مُستهلك',                 'Consumed',               2),
    ('INV_RESERVATION_STATUS', 'RELEASED',          'مُحرَّر',                  'Released',               3),
    -- INV_SOURCE_DOC_TYPE (open; calling modules add their document types as data)
    ('INV_SOURCE_DOC_TYPE',    'INV_TRANSFER',      'تحويل مخزني',             'Stock transfer',         1),
    ('INV_SOURCE_DOC_TYPE',    'INV_COUNT',         'جرد مخزون',               'Stock count',            2),
    ('INV_SOURCE_DOC_TYPE',    'INV_ADJUSTMENT',    'تسوية مخزون',             'Stock adjustment',       3),
    ('INV_SOURCE_DOC_TYPE',    'PURCHASE_RECEIPT',  'استلام بضاعة',            'Goods receipt',          4),
    ('INV_SOURCE_DOC_TYPE',    'PURCHASE_RETURN',   'مرتجع مشتريات',           'Purchase return',        5),
    ('INV_SOURCE_DOC_TYPE',    'SALES_INVOICE',     'فاتورة مبيعات',           'Sales invoice',          6),
    ('INV_SOURCE_DOC_TYPE',    'POS_SALE',          'بيع نقطة البيع',          'POS sale',               7),
    ('INV_SOURCE_DOC_TYPE',    'SALES_RETURN',      'مرتجع مبيعات',            'Sales return',           8),
    ('INV_SOURCE_DOC_TYPE',    'SALES_ORDER',       'أمر بيع',                 'Sales order',            9),
    ('INV_SOURCE_DOC_TYPE',    'STOREFRONT_ORDER',  'طلب المتجر الإلكتروني',   'Storefront order',       10),
    -- INV_CHANGE_KIND (closed; event field of the inventory master-change event, REQ-INV-034)
    ('INV_CHANGE_KIND',        'CREATED',           'أُنشئ',                   'Created',                1),
    ('INV_CHANGE_KIND',        'UPDATED',           'عُدِّل',                   'Updated',                2),
    ('INV_CHANGE_KIND',        'DEACTIVATED',       'أُوقف',                   'Deactivated',            3),
    ('INV_CHANGE_KIND',        'REACTIVATED',       'أُعيد تفعيله',            'Reactivated',            4),
    ('INV_CHANGE_KIND',        'DEFAULT_CHANGED',   'أصبح افتراضيًا',          'Set as default',         5)
) AS v (TYPE_KEY, CODE, NAME_AR, NAME_EN, SORT_ORDER)
JOIN MDL_LOOKUP_TYPE t ON t.TENANT_ID = 1 AND t.KEY = v.TYPE_KEY
WHERE NOT EXISTS (
    SELECT 1 FROM MDL_LOOKUP_VALUE l
    WHERE l.TENANT_ID = 1 AND l.LOOKUP_TYPE_ID = t.LOOKUP_TYPE_PK AND l.CODE = v.CODE
);

-- 8c — number series. Table owned by erp-core modules.sequence (CORE_NUMBER_SERIES); the series codes are owned by INV
-- (SRS A9; RULE-INV-001, RULE-INV-010, RULE-INV-030). Prefixes follow the SRS examples WH-0002, BR-0002, TR-0005,
-- ADJ-0002. PERIOD_KEY, NEXT_VALUE, IS_ACTIVE, CREATED_AT and VERSION take their column defaults.
INSERT INTO CORE_NUMBER_SERIES (ID, TENANT_ID, CODE, PREFIX, PATTERN, RESET_POLICY, CREATED_BY)
SELECT nextval('SEQ_CORE_NUMBER_SERIES'), 1, v.CODE, v.PREFIX, '{PREFIX}-{SEQ:4}', 'NEVER', 'SYSTEM'
FROM (VALUES
    ('INV_WAREHOUSE',  'WH'),
    ('INV_BRANCH',     'BR'),
    ('INV_TRANSFER',   'TR'),
    ('INV_COUNT',      'CNT'),
    ('INV_ADJUSTMENT', 'ADJ')
) AS v (CODE, PREFIX)
WHERE NOT EXISTS (SELECT 1 FROM CORE_NUMBER_SERIES s WHERE s.TENANT_ID = 1 AND s.CODE = v.CODE);

-- 8d — notification templates. Table owned by erp-core modules.notif (NOTIF_TEMPLATE); the template codes are owned by INV
-- (REQ-INV-068, REQ-INV-117; texts from AC-INV-074 and AC-INV-131). Channel IN_APP, category UTILITY; columns not named
-- take their defaults. Variables are filled by INV through NotificationDispatchApi.
INSERT INTO NOTIF_TEMPLATE (ID, TENANT_ID, CODE, MODULE_CODE, CHANNEL, CATEGORY, SUBJECT_AR, SUBJECT_EN, BODY_AR, BODY_EN, IS_ACTIVE_FL, CREATED_BY, CREATED_AT)
SELECT nextval('SEQ_NOTIF_TEMPLATE'), 1, v.CODE, 'INV', 'IN_APP', 'UTILITY', v.SUBJECT_AR, v.SUBJECT_EN, v.BODY_AR, v.BODY_EN, TRUE, 'SYSTEM', CURRENT_TIMESTAMP
FROM (VALUES
    ('INV_NEGATIVE_STOCK',
     'رصيد سالب', 'Negative stock',
     'رصيد سالب: {variant} في {warehouse} = {onHand}.',
     'Negative stock: {variant} in {warehouse} = {onHand}.'),
    ('INV_NEAR_EXPIRY',
     'تشغيلة قريبة الانتهاء', 'Lot near expiry',
     'تشغيلة قريبة الانتهاء: {variant} — {lot} تنتهي في {expiryDate} (الكمية {quantity}).',
     'Lot near expiry: {variant} — {lot} expires on {expiryDate} (quantity {quantity}).')
) AS v (CODE, SUBJECT_AR, SUBJECT_EN, BODY_AR, BODY_EN)
WHERE NOT EXISTS (SELECT 1 FROM NOTIF_TEMPLATE n WHERE n.TENANT_ID = 1 AND n.CODE = v.CODE);

-- 8e — settings platform defaults. Table owned by erp-core modules.cu (CU_APP_CONFIGURATION, TENANT_ID NULL = platform
-- default every tenant inherits until it saves an override); the keys are owned by INV (SRS A6 "Settings consumed",
-- D-04): negative stock allowed by default (REQ-INV-066), near-expiry horizon 30 days (REQ-INV-118). Column list and
-- IS_ACTIVE_FL value as the given cu documents its own seed.
INSERT INTO CU_APP_CONFIGURATION (ID, TENANT_ID, CONFIG_KEY, CONFIG_VALUE, NOTES, IS_ACTIVE_FL, CREATED_BY, CREATED_AT)
SELECT nextval('SEQ_CU_APP_CONFIGURATION'), NULL, v.CONFIG_KEY, v.CONFIG_VALUE, v.NOTES, 1, 'SYSTEM', now()
FROM (VALUES
    ('INV_ALLOW_NEGATIVE_STOCK', 'true', 'Negative stock allowed (INV, REQ-INV-066)'),
    ('INV_NEAR_EXPIRY_DAYS',     '30',   'Near-expiry horizon in whole days 1 to 365 (INV, REQ-INV-118)')
) AS v (CONFIG_KEY, CONFIG_VALUE, NOTES)
WHERE NOT EXISTS (SELECT 1 FROM CU_APP_CONFIGURATION c WHERE c.TENANT_ID IS NULL AND c.CONFIG_KEY = v.CONFIG_KEY);

-- ---------------------------------------------------------------------
-- BLOCK 9 — VIEWS
-- none: reserved and available quantities (REQ-INV-038) and bundle
-- availability (REQ-INV-042) depend on today in the tenant time zone, on
-- capability flags and on CAT reads — they are computed by the service.
-- ---------------------------------------------------------------------

-- ---------------------------------------------------------------------
-- BLOCK 10 — FUNCTIONS / PROCEDURES
-- none: no SRS requirement needs a stored routine.
-- ---------------------------------------------------------------------
```

### 4.1 Self-verification (engine §7.2)

```
SYNTAX        ✓ S-1 every statement ends with ";" · no trailing comma · every referenced object is created here
                    (or is a given table: CORE_TENANT referenced by FK; mdl, sequence, notif, cu tables seeded only)
              ✓ S-2 types: BIGINT, VARCHAR(n), BOOLEAN, TIMESTAMPTZ, NUMERIC(p,s) from §4.1; DATE and INTEGER declared once in §1
              ✓ S-3 PK / UQ / CHK / FK in ALTER TABLE form, named per §3 (partial UNIQUE as unique index — dialect form;
                    the two warehouse FKs of a transfer are FK_INV_TRANSFER_INV_WAREHOUSE_1 / _2)
              ✓ S-4 no PK trigger, no sequence default on a PK
              ✓ S-5 seed INSERTs carry column lists, TENANT_ID = 1 (cu: NULL, platform default) and NOT EXISTS guards;
                    no BEGIN / COMMIT (Flyway's transaction — ADR-INV-014)
              ✓ S-6 no constraint references another business module's table, live or commented (CORE_TENANT is the given's)
              ✓ S-7 no schema prefix anywhere
              ✓ S-8 no placeholder in SQL ({variant}, {lot}, … are notification template variables, {PREFIX}/{SEQ:4} the series pattern)
PK STRATEGY   ✓ 14 / 14 PK columns carry GENERATED ALWAYS AS IDENTITY; BLOCK 1 empty
TENANCY       ✓ 14 / 14 tables: TENANT_ID NOT NULL · FK_<TABLE>_TENANT · IDX_<TABLE>_TENANT led by TENANT_ID
              ✓ 24 / 24 UNIQUE (22 constraints, 2 unique indexes) lead with TENANT_ID · 19 / 19 intra-module FKs composite with TENANT_ID
ORDER         ✓ parents before children · PK and tenant-scoped UNIQUE before FK · given DDL before the seed INSERTs
              ✓ the count ↔ adjustment cycle is two nullable FKs added after both tables exist
COMPLETENESS  ✓ 14 / 14 ENT → table · every A3 field → DBF (219) · derived reservedQty / availableQty not held (SRS A3)
              ✓ 6 / 6 owned lookup keys seeded (34 values) · 5 / 5 number series · 2 / 2 notification templates · 2 / 2 settings defaults
              ✓ indexes on every FK column (direct or via a leading UNIQUE), every B2 filter column, every UNIQUE business key
              ✓ shared lookup tables (MDL_*) not re-created
CROSS-MODULE  ✓ 11 / 11 HARD-FK XM have their column (BIGINT, per CON-CAT-001 / CON-CAT-002) and their pointer "constraint → XM-INV-0nn"
              ✓ 2 / 2 SOFT-READ XM have their commentary block (after 5d) · no cross-module constraint, live or commented
TRACE         ✓ 219 / 219 DBF trace to ENT.field + ≥ 1 REQ · 13 / 13 XM trace to ≥ 1 REQ and to an SRS A8 row · no orphan, no dangling id
PROFILE       ✓ CM-3 every flag column ends with _FL (IS_DEFAULT_FL, IS_ACTIVE_FL, OFFLINE_FL, LOT_TRACKED_FL, DUAL_QUANTITY_FL)
              ✓ CM-8 every stock-bearing row carries its warehouse (INV_STOCK_BALANCE, INV_STOCK_MOVE, INV_RESERVATION; documents on the
                    header — lines inherit it through their composite FK); every monetary row carries CURRENCY_CODE
                    (INV_STOCK_MOVE, INV_TRANSFER_LINE, INV_STOCK_ADJUSTMENT_LINE, INV_STOCK_VALUATION); INV holds no price (ADR-INV-015)
```

Counts: 14 tables · 14 PK · 22 UNIQUE constraints + 2 unique indexes · 34 CHECK · 19 intra-module FK + 14 tenant FK · 0 cross-module constraint (11 column-only HARD-FK) · 0 trigger · 45 non-unique indexes · 6 lookup types / 34 values · 5 number series · 2 notification templates · 2 settings defaults · 0 view · 0 routine.

---

## 5 — Decisions applied

### 5.1 DEFAULTs (structural best practice, no ADR needed)

| DEFAULT | What | Source |
|---|---|---|
| D-P2-01 | Identifier transformation camelCase → UPPER_SNAKE_CASE, unquoted; tables `INV_` + entity name | engine §3; given table naming (`MDL_LOOKUP_TYPE`, `CORE_NUMBER_SERIES`) |
| D-P2-02 | Column order per table: PK → entity fields in SRS order → FK fields → kind standard fields → TENANT_ID, VERSION → CURRENCY_CODE → audit fields; DBF sequence continuous across tables in ENT order | engine §2 ASSIGNMENT RULES |
| D-P2-03 | Audit user columns CREATED_BY / UPDATED_BY are `VARCHAR(100)` holding the platform principal, filled by the given auditing — never a client value, never a numeric FK | engine §3 AUDIT COLUMNS; SRS A3 ("system-filled") |
| D-P2-04 | Closed lookups that hold behaviour (INV_MOVE_TYPE, INV_DOC_STATUS, INV_RESERVATION_STATUS) are CHECK-constrained; open lookups (INV_ADJUSTMENT_REASON, INV_SOURCE_DOC_TYPE) and the consumed CAT unit of measure are validated against active `mdl` values at runtime; INV_CHANGE_KIND is an event field with no column, seeded only | SRS A6, A7; engine §1 (A7 → check constraints); profile LOOKUPS |
| D-P2-05 | IDEMPOTENCY_KEY is indexed, not unique: one cross-module request records several moves or reservations under one key (multi-line receipts and issues, bundle explosion REQ-INV-049 / 053, FEFO split REQ-INV-110); "first result, nothing new" is a service lookup by key (RULE-INV-024). INV-internal documents fill the key from the document (for example the transfer type and identifier) because the column is NOT NULL in SRS A3 | SRS ENT-INV-004, ENT-INV-005; RULE-INV-024 |
| D-P2-06 | No trigger and no routine: stock-move immutability (RULE-INV-023), read-only codes and numbers (RULE-INV-001, 010, 030), read-only posted documents (RULE-INV-029), balance / valuation maintenance (REQ-INV-036, 059) and audit (REQ-INV-077, given AuditApi) are service behaviour | engine BLOCK 6, BLOCK 10 |
| D-P2-07 | RULE-INV-034 (an adjustment line changes something) is not a CHECK: AC-INV-111 starts from a saved DRAFT line whose change is zero and expects the posting to refuse it, so the rule is enforced by the service on save and post | SRS AC-INV-111, RULE-INV-034 |
| D-P2-08 | Uniqueness over a nullable lot uses `UNIQUE NULLS NOT DISTINCT` (postgresql15+): stock without a lot is one balance, and two lines of the same Variant without a lot collide (AC-INV-109) | REQ-INV-035; RULE-INV-048; SRS D-14 |
| D-P2-09 | Column defaults only where the SRS states the initial value: IS_ACTIVE_FL TRUE, IS_DEFAULT_FL / OFFLINE_FL / LOT_TRACKED_FL / DUAL_QUANTITY_FL FALSE (A3 "false by default"), STATUS_CODE 'DRAFT' (REQ-INV-078, 087, 097) and 'ACTIVE' (REQ-INV-043), VERSION 0. DOC_DATE "today by default" is service-filled in the tenant time zone (RULE-INV-038's time-zone rule), not a database default; CURRENCY_CODE is service-filled from the tenant base currency | SRS A3, A7 |
| D-P2-10 | Intra-module FKs carry no ON DELETE action — master rows are deactivated, never deleted; lines of a DRAFT document are removed by the service | ARCH-5; REQ-INV-089 |
| D-P2-11 | Search indexes are B-tree on each filter column led by TENANT_ID; "contains" matching of names and barcode resolution (CAT CON-CAT-010) are query / service behaviour; this script creates no extension | engine §4.3 |
| D-P2-12 | BLOCK 8 seeds every key INV owns into the given tables: 6 lookup types / 34 values (MDL_*), 5 number series (CORE_NUMBER_SERIES), 2 notification templates (NOTIF_TEMPLATE), 2 settings platform defaults (CU_APP_CONFIGURATION) — idempotent, for the platform tenant. The capability flags `lotExpiry`, `dualQuantity`, `bundles` are not seeded: their owner writes them (SRS A2 out of scope) | engine §4.2; SRS A6, A9, D-04 |
| D-P2-13 | Stock valuation carries no warehouse: SRS POL-INV-021 / ENT-INV-013 value each Variant across the tenant, so it is not a per-location stock row; per-location stock is INV_STOCK_BALANCE. Line tables (transfer, count, adjustment lines) take the warehouse from their header | SRS ENT-INV-013; CM-8 |
| D-P2-14 | Cross-module HARD-FK columns are `BIGINT` exactly as CON-CAT-001 / CON-CAT-002 state; one XM per column so that each pointer maps to one integration block in P3.1 (whose constraint will be composite with TENANT_ID, as every intra-module FK here) | engine §5.1; contract-cat §0 |

### 5.2 ADRs raised by this stage (ADR-INV-014 … ADR-INV-020)

ADR-INV-014 … 018 are NON-BREAKING with status ACCEPTED. ADR-INV-019 (repair round 2) has status BLOCKED: it stops the pass because the only remaining findings are on P1 SRS text, which P2 cannot write. It is not a structural conflict, and no DBF, XM or SQL depends on it. The write scope of the first run was the two P2 files, so ADR-INV-014 … 017 are recorded here in full (Context · Decision · Consequences · Traces · Status) for the orchestrator to file under `analysis/decisions/INV/ADR-INV-{seq}.md`. ADR-INV-018, ADR-INV-019 and ADR-INV-020 are recorded here and are filed as `analysis/decisions/INV/ADR-INV-018.md`, `ADR-INV-019.md` and `ADR-INV-020.md`. ADR-INV-020 (ACCEPTED) rewords the two SRS cells and so meets the unblock condition of ADR-INV-019. The next free ADR id is ADR-INV-021.

#### ADR-INV-014 — Platform columns TENANT_ID and VERSION on every INV table
- **Context.** SRS A3 lists no tenant or version field. The given `erp-core` 1.6.0 states the tenancy rule "every table carries TENANT_ID" and requires every business entity to extend its `AuditableEntity` (TENANT_ID as Hibernate `@TenantId`, VERSION as the optimistic lock): every table has `TENANT_ID BIGINT NOT NULL`, `FK_<TABLE>_TENANT → CORE_TENANT(ID)`, an index led by TENANT_ID, every UNIQUE composite with TENANT_ID, and `VERSION BIGINT NOT NULL DEFAULT 0`. The SRS per-tenant rules (RULE-INV-002, 006, 011, 015, 030, 036, 045) need a tenant column to hold. NO-COLUMN-INVENTION admits a column the platform mandates for the entity. The project applied the same rule to PTY (ADR-PTY-035).
- **Decision.** Every INV table carries TENANT_ID and VERSION as platform columns (DBF-INV-008/009, 020/021, 034/035, 059/060, 081/082, 095/096, 108/109, 123/124, 137/138, 152/153, 169/170, 185/186, 198/199, 214/215), traced to the entity and to the REQs that create and update its rows. Every UNIQUE leads with TENANT_ID; every intra-module FK is composite `(TENANT_ID, <FK>)` against a `UQ_<TABLE>_TENANT_PK`; the script is one Flyway migration with no BEGIN / COMMIT.
- **Consequences.** No INV code reads or writes a tenant identifier (the given fills it). VERSION gives the optimistic lock that, with the per-Variant serialisation of SRS D-09, keeps concurrent moves consistent (REQ-INV-037). A later SRS revision may list `tenantId` / `version` in A3's standard fields; no column changes then.
- **Traces.** REQ-INV-001, REQ-INV-003, REQ-INV-035, REQ-INV-037, REQ-INV-043, REQ-INV-078; RULE-INV-002, RULE-INV-006, RULE-INV-030, RULE-INV-036, RULE-INV-045; ENT-INV-001 … ENT-INV-014.
- **Status.** ACCEPTED — non-breaking.

#### ADR-INV-015 — Currency code on every monetary INV row (profile check CM-8)
- **Context.** Profile check CM-8 (MAJOR) requires every monetary row to carry its currency, "the dimension exists from day one even while the tenant has one". INV holds amounts on Stock move (unitCost, moveValue), Transfer line (unitCost), Stock adjustment line (unitCost, lineValue) and Stock valuation (averageUnitCost, stockValue). SRS A2 and D-06 fix every value "in the tenant base currency" and declare multi-currency an advanced tier, but A3 holds no currency field.
- **Decision.** `CURRENCY_CODE VARCHAR(3) NOT NULL` with `CHECK (CURRENCY_CODE ~ '^[A-Z]{3}$')` (ISO 4217) on INV_STOCK_MOVE (DBF-INV-061), INV_TRANSFER_LINE (DBF-INV-110), INV_STOCK_ADJUSTMENT_LINE (DBF-INV-171) and INV_STOCK_VALUATION (DBF-INV-200). The service writes the tenant base currency read through the given `cu` SettingsApi (SRS A9 row 1); there is no column default because the base currency is a tenant setting, not a constant. INV holds no price, so the price-list part of CM-8 does not apply.
- **Consequences.** No SRS meaning changes: every amount stays in the base currency, now stated on the row. The stock-move event (CON-INV-020) may add the code as an ADDITIVE field in P3.1. When multi-currency arrives, valuation per currency is an additive change. A later SRS revision lists `currencyCode` among the fields of the four entities.
- **Traces.** ENT-INV-004, ENT-INV-007, ENT-INV-011, ENT-INV-013; REQ-INV-057, REQ-INV-059, REQ-INV-061, REQ-INV-062, REQ-INV-063, REQ-INV-070, REQ-INV-080, REQ-INV-099; SRS A2 (Currency), D-06.
- **Status.** ACCEPTED — non-breaking.

#### ADR-INV-016 — Physical types the SRS leaves open
- **Context.** SRS A3 gives the scale of every decimal (quantity and measure 3, unit and average cost 4, value 2 — D-06) but no precision; it uses the logical types `date` and `number` (ENT-INV-011.rowNo), which the profile syntax map lacks, and `lookup` without a length.
- **Decision.** Precision 18 for every decimal: `NUMERIC(18,3)` quantities and measures, `NUMERIC(18,4)` costs, `NUMERIC(18,2)` values (15 / 14 / 16 integer digits — far above any trader's stock). Logical `date` → `DATE` (a calendar date; RULE-INV-038 compares the expiry date with today in the tenant time zone, which a timestamp would blur); logical `number` → `INTEGER` (row numbers 1 … 1000, RULE-INV-027). Lookup codes → `VARCHAR(50)` (the given `mdl` convention for a stored code). Each is declared once in the script header.
- **Consequences.** Half-up rounding to the stated scale (SRS D-06) is the service's job; the database stores the scale exactly. A base currency with 3 minor units stores values to 2 decimals as SRS D-06 states; changing that is an SRS decision ("Change the places per tenant currency").
- **Traces.** DBF-INV-027, 042, 044, 045, 159, 177, 192, 193, 194 and every other NUMERIC / DATE / lookup column; REQ-INV-051, REQ-INV-059, REQ-INV-071, REQ-INV-113.
- **Status.** ACCEPTED — non-breaking.

#### ADR-INV-017 — A measure-only adjustment move carries quantity 0
- **Context.** SRS ENT-INV-004.quantity is typed "> 0" (RULE-INV-017 for receipts, issues, reservations and transfers), while REQ-INV-123 posts an adjustment line of a dual-quantity Item whose count change is zero and whose measure change is non-zero "as a measure-only move" (AC-INV-137: on-hand stays 3, measure 120.500 → 120.000). The move needs a quantity value.
- **Decision.** `CHK_INV_STOCK_MOVE_QUANTITY`: QUANTITY > 0, or QUANTITY = 0 only on an ADJUSTMENT_IN / ADJUSTMENT_OUT move whose MEASURE_QTY is greater than 0; the direction of the measure follows the move type, as for the quantity. Every other move keeps QUANTITY > 0, so RULE-INV-017 holds where it applies.
- **Consequences.** A measure-only move has MOVE_VALUE 0 and leaves the average cost unchanged; ACC receives it through the stock-move event with quantity 0. P3.1 documents this on CON-INV-020 (no field changes).
- **Traces.** ENT-INV-004.quantity, ENT-INV-004.measureQty; DBF-INV-042, DBF-INV-043; REQ-INV-123, REQ-INV-099; RULE-INV-017, RULE-INV-034; AC-INV-137.
- **Status.** ACCEPTED — non-breaking.

#### ADR-INV-018 — INV v1 uses no given events subscriber and no given file private store (C-GIVEN-2)
- **Context.** `gov.py analyze` (P2 round 1, C-GIVEN-2) found that the SRS names the given `erp-core:modules.events` subscriber extension point and the given `erp-core:modules.file` private-store interface with no `given-usage` row. Both names sit only in the "Override / status" column of SRS D-23 and D-13 — the alternative that was *not* chosen. The chosen decisions use neither: D-23 has PUR, SLS and SFR call INV operations and INV subscribe to none of their events (the DAG stays downward-only); D-13 has the browser read the opening stock sheet and submit rows, so INV parses no uploaded file. Nothing in this script, the contract or any REQ needs either interface.
- **Decision.** Take the finding's "stop using it" branch, not "add the row": INV v1 declares no use of either interface, and the SRS / contract `given-usage` blocks stay as they are (a row would declare a use that does not exist and would invite a dependency the DAG forbids). No table, column, seed row or XM of this script depends on either interface. The two SRS override cells are to be reworded at the next touch of P1 so that they describe the alternative without naming the interface — "subscribe to their events through the given events module" (D-23) and "server-side import through the given file module once the core publishes the read" (D-13). Adopting either alternative later is an SRS change that adds its `given-usage` row in the SRS and the contract at the same time.
- **Consequences.** No structural change in P2: DBF-INV-001 … 219 and XM-INV-001 … 013 are unchanged. The P2 artifacts cannot clear a finding raised on the SRS text; the finding clears when the two P1 cells are reworded (outside this stage's write scope), with no meaning change to D-13 or D-23.
- **Traces.** SRS D-13, D-23; REQ-INV-070, REQ-INV-071, REQ-INV-073 (opening stock without server-side file parsing); REQ-INV-043, REQ-INV-051, REQ-INV-052 (receipts, issues and reservations by call); ENT-INV-004, ENT-INV-005, ENT-INV-010, ENT-INV-011.
- **Status.** ACCEPTED — non-breaking.

#### ADR-INV-019 — P2 cannot clear the C-GIVEN-2 findings raised on the P1 SRS text; the pass stops
- **Context.** `gov.py analyze` refused P2 a second time (repair round 1) with the same two C-GIVEN-2 findings on artifact `srs`, after ADR-INV-018 took the "stop using it" branch. The two interface names appear only in the SRS D-23 and D-13 "Override / status" cells (`P1/srs-inv.md`, carried into `_state/current-srs.md`). Neither P2 file names them, and the check reads the SRS text only. Both remedies the finding offers ("add the row" or "stop using it") are P1 edits, outside this stage's write set. Another P2 repair round would be refused the same way.
- **Decision.** Stop the pass as BLOCKED instead of looping. ADR-INV-018 stands: INV v1 declares no use of either interface. The P2 artifacts are complete and kept as written; only the decision records cite this ADR.
- **Unblock condition.** Reword the two P1 cells with no change of meaning, through a P1 repair or an authorised edit, then regenerate `_state/current-srs.md` and re-run `gov.py analyze` for P2:
  - D-23 → "Subscribe to their events through the given events module"
  - D-13 → "Server-side import through the given file module once the core publishes the read"
  No P2 file changes for the findings to clear. This ADR then becomes ACCEPTED (unblocked).
- **Consequences.** P2 does not pass its gate, and P3.1 does not start, until the unblock condition holds. DBF-INV-001 … 219, XM-INV-001 … 013 and the script are unchanged. The contract needs no change, because neither name appears in it.
- **Traces.** ADR-INV-018; SRS D-13, D-23; REQ-INV-043, REQ-INV-051, REQ-INV-052, REQ-INV-070, REQ-INV-071, REQ-INV-073.
- **Status.** BLOCKED — stop the pass. The exit condition depends on a P1 artifact.

#### ADR-INV-020 — The SRS D-13 and D-23 override cells name no given interface (C-GIVEN-2 cleared on the SRS side)
- **Context.** P2 was refused again with the same two C-GIVEN-2 findings on artifact `srs`. This repair round authorises an in-place edit of `P1/srs-inv.md` for them, which is the unblock condition of ADR-INV-019.
- **Decision.** Stop using the interfaces, as ADR-INV-018 decided. Reword the two "Override / status" cells with no change of meaning: D-13 → "Server-side import through the given file module once the core publishes the read"; D-23 → "Subscribe to their events through the given events module". The SRS and contract `given-usage` blocks are unchanged and stay identical.
- **Consequences.** No P1 file names either interface. The unblock condition of ADR-INV-019 holds once `_state/current-srs.md` is regenerated. ADR-INV-019 is not rewritten under its id, and its resolution is left to its owner. No DBF, XM or SQL changes.
- **Traces.** ADR-INV-018, ADR-INV-019; SRS D-13, D-23; REQ-INV-043, REQ-INV-051, REQ-INV-052, REQ-INV-070, REQ-INV-071, REQ-INV-073.
- **Status.** ACCEPTED — non-breaking. Filed as `analysis/decisions/INV/ADR-INV-020.md`.

### 5.3 Decisions inherited and applied

| Id | Applied as |
|---|---|
| SRS D-01 | docNo only on INV_TRANSFER, INV_STOCK_COUNT, INV_STOCK_ADJUSTMENT; statusCode only where A6 declares a status lookup (transfer, count, adjustment, reservation); lines carry no docNo / docDate / statusCode |
| SRS D-02 | INV_LOT.CODE is the lot number; NAME_AR / NAME_EN nullable |
| SRS D-03 | INV_ITEM_STOCK_SETTINGS owned by INV with ITEM_FK unique per tenant |
| SRS D-04 | settings in the given `cu` — platform defaults seeded in BLOCK 8e, no INV config table |
| SRS D-06 | scales 3 / 4 / 2 (ADR-INV-016) |
| SRS D-07 | no in-transit column or state |
| SRS D-13 | opening stock is an INV_STOCK_ADJUSTMENT with REASON_CODE OPENING_STOCK; ROW_NO reports the failing row; no file is stored or parsed by INV (ADR-INV-018) |
| SRS D-14 | UQ_INV_STOCK_COUNT_LINE_CNT_VAR_LOT |
| SRS D-19 | INV_LOT.NEAR_EXPIRY_NOTIFIED_AT |
| SRS D-22 | INV_ADJUSTMENT_REASON seeded with five values |
| SRS D-23 | SOURCE_DOC_REF / SOURCE_LINE_REF soft references, no XM to PUR, SLS, SFR; CON-CAT-018 not consumed; INV subscribes to no event (ADR-INV-018) |
| SRS D-24 | no reservation expiry column |
| contract C-3 | Reservation and Stock move stay private; their keys are soft references for consumers |

BLOCKED: ADR-INV-019. Its unblock condition is met: ADR-INV-020 rewords the D-13 and D-23 override cells in the P1 SRS. Its status change is left to its owner. No structural element of this script is blocked.

---

## 6 — Registry content

The registry of this stage is `analysis/modules/INV/P2/registry-db-inv.md` (same DBF and XM ids as §2 and §3).

| Table | ENT | Kind | DBF range |
|---|---|---|---|
| INV_WAREHOUSE | ENT-INV-001 | master | DBF-INV-001 … DBF-INV-013 |
| INV_BRANCH | ENT-INV-002 | master | DBF-INV-014 … DBF-INV-025 |
| INV_STOCK_BALANCE | ENT-INV-003 | transactional | DBF-INV-026 … DBF-INV-039 |
| INV_STOCK_MOVE | ENT-INV-004 | transactional | DBF-INV-040 … DBF-INV-065 |
| INV_RESERVATION | ENT-INV-005 | transactional | DBF-INV-066 … DBF-INV-086 |
| INV_TRANSFER | ENT-INV-006 | transactional | DBF-INV-087 … DBF-INV-100 |
| INV_TRANSFER_LINE | ENT-INV-007 | transactional (line) | DBF-INV-101 … DBF-INV-114 |
| INV_STOCK_COUNT | ENT-INV-008 | transactional | DBF-INV-115 … DBF-INV-128 |
| INV_STOCK_COUNT_LINE | ENT-INV-009 | transactional (line) | DBF-INV-129 … DBF-INV-142 |
| INV_STOCK_ADJUSTMENT | ENT-INV-010 | transactional | DBF-INV-143 … DBF-INV-157 |
| INV_STOCK_ADJUSTMENT_LINE | ENT-INV-011 | transactional (line) | DBF-INV-158 … DBF-INV-175 |
| INV_LOT | ENT-INV-012 | master | DBF-INV-176 … DBF-INV-190 |
| INV_STOCK_VALUATION | ENT-INV-013 | transactional | DBF-INV-191 … DBF-INV-204 |
| INV_ITEM_STOCK_SETTINGS | ENT-INV-014 | config | DBF-INV-205 … DBF-INV-219 |

XM : XM-INV-001 … XM-INV-013 · Last DBF : DBF-INV-219 · Last XM : XM-INV-013 · Decisions : ADR-INV-014 … ADR-INV-020 (1 BLOCKED: ADR-INV-019 — unblock condition met by ADR-INV-020) · DEFAULTs D-P2-01 … D-P2-14

Event : "P2 completed: INV v1 — 14 tables, 219 DBF, 13 XM — BLOCKED by ADR-INV-019 (P1 SRS D-13 / D-23 wording)"
