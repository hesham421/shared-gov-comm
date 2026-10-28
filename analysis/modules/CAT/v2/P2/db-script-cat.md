# DB SCRIPT — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module : CAT   Version : v2   Profile : commerce   Stage : P2
Inputs : srs (P1, CAT v2 — REQ 127 · ENT 12 · RULE 49), registry-srs, contract (P1.5, CON 18)
Counts : tables 12 · DBF 161 · XM 0 · intra-module FK 13 · tenant FK 0 (column-only, ADR-CAT-054) · lookup keys 10 (34 seeded values)
Status : DRAFT — P2 pass 1 · no question raised · no ADR raised
══════════════════════════════════════════════════════════════════

## 1 — Header

| Item | Value |
|---|---|
| Module | CAT — Catalog / الكتالوج |
| Version | v2 — change set CS-CAT-001 (ADDITIVE). The current state holds no earlier db-script, so this migration creates every CAT object. The DBF sequence starts at 001, and no XM exists (SRS A8 `consumes: []`) |
| Dialect | postgresql16 (profile `stack.db.target_dialect`) |
| Schema prefix | none. No object is schema-qualified (S-7) |
| PK generation | `identity`: every PK column is `BIGINT GENERATED ALWAYS AS IDENTITY`. No sequence, no trigger, no PK default |
| Identifier transformation | The logical camelCase name (SRS A3) becomes UPPER_SNAKE_CASE: an underscore goes before each capital and the result is upper case, unquoted. Examples: `itemPk` → `ITEM_PK`, `isActiveFl` → `IS_ACTIVE_FL`, `einvoiceCodeTypeCode` → `EINVOICE_CODE_TYPE_CODE`. PostgreSQL folds unquoted names to lower case, which gives the same physical name as the given's UPPER_SNAKE convention and the default JPA physical naming. Table name = `CAT_` + entity abbreviation. Every identifier is at most 63 characters, and none is a reserved word (`KEY` and `VERSION` are non-reserved in PostgreSQL 16) |
| Type declared here (not in the syntax map) | logical `number` → `INTEGER` (sortOrder, scaleCodeLength, valueDecimals). The profile `syntax_map` has no integer entry, so it is declared once here |
| Date | 2026-10-26 |
| Prepared by | claude:opus (analysis lane) |
| Decisions | 18 DEFAULT (D-P2-01 … D-P2-18, §5). 1 ADR (ADR-CAT-054). The next free ADR sequence is 055 |

## 2 — DB field traceability matrix

```yaml name=dbf-matrix
rows:
  # ── CAT_ITEM — ENT-CAT-001 Item (master) ──
  - {id: DBF-CAT-001, table: CAT_ITEM, column: ITEM_PK, type: "BIGINT", entity_field: ENT-CAT-001.itemPk, traces: [ENT-CAT-001, REQ-CAT-001, REQ-CAT-002], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-002, table: CAT_ITEM, column: CODE, type: "VARCHAR(20)", entity_field: ENT-CAT-001.code, traces: [ENT-CAT-001, REQ-CAT-032, REQ-CAT-034], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-003, table: CAT_ITEM, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-001.nameAr, traces: [ENT-CAT-001, REQ-CAT-024, REQ-CAT-027, REQ-CAT-028, REQ-CAT-030, REQ-CAT-007], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-004, table: CAT_ITEM, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-001.nameEn, traces: [ENT-CAT-001, REQ-CAT-029, REQ-CAT-031], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-005, table: CAT_ITEM, column: ITEM_KIND_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.itemKindCode, traces: [ENT-CAT-001, REQ-CAT-024, REQ-CAT-063, REQ-CAT-015], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-006, table: CAT_ITEM, column: BASE_UOM_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.baseUomCode, traces: [ENT-CAT-001, REQ-CAT-024, REQ-CAT-074, REQ-CAT-082], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-007, table: CAT_ITEM, column: BRAND_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.brandCode, traces: [ENT-CAT-001, REQ-CAT-004, REQ-CAT-073, REQ-CAT-076], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-008, table: CAT_ITEM, column: TAX_CLASS_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.taxClassCode, traces: [ENT-CAT-001, REQ-CAT-024, REQ-CAT-119], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-009, table: CAT_ITEM, column: EINVOICE_CODE_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.einvoiceCodeTypeCode, traces: [ENT-CAT-001, REQ-CAT-117, REQ-CAT-123], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-010, table: CAT_ITEM, column: EINVOICE_CODE, type: "VARCHAR(100)", entity_field: ENT-CAT-001.einvoiceCode, traces: [ENT-CAT-001, REQ-CAT-117, REQ-CAT-123], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-011, table: CAT_ITEM, column: DESCRIPTION_AR, type: "VARCHAR(2000)", entity_field: ENT-CAT-001.descriptionAr, traces: [ENT-CAT-001, REQ-CAT-004], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-012, table: CAT_ITEM, column: DESCRIPTION_EN, type: "VARCHAR(2000)", entity_field: ENT-CAT-001.descriptionEn, traces: [ENT-CAT-001, REQ-CAT-004], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-013, table: CAT_ITEM, column: PUBLISH_STATE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.publishStateCode, traces: [ENT-CAT-001, REQ-CAT-005, REQ-CAT-113, REQ-CAT-114, REQ-CAT-115], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-014, table: CAT_ITEM, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-001.isActiveFl, traces: [ENT-CAT-001, REQ-CAT-005, REQ-CAT-006], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-015, table: CAT_ITEM, column: CATEGORY_FK, type: "BIGINT", entity_field: ENT-CAT-001.categoryFk, traces: [ENT-CAT-001, REQ-CAT-068, REQ-CAT-072], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-016, table: CAT_ITEM, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-001.tenantId, traces: [ENT-CAT-001, REQ-CAT-001], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-017, table: CAT_ITEM, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-001.version, traces: [ENT-CAT-001, REQ-CAT-010], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-018, table: CAT_ITEM, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-001.createdBy, traces: [ENT-CAT-001, REQ-CAT-024], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-019, table: CAT_ITEM, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-001.createdAt, traces: [ENT-CAT-001, REQ-CAT-024], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-020, table: CAT_ITEM, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-001.updatedBy, traces: [ENT-CAT-001, REQ-CAT-004], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-021, table: CAT_ITEM, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-001.updatedAt, traces: [ENT-CAT-001, REQ-CAT-017], nullable: "NULL", default: "—"}
  # ── CAT_VARIANT — ENT-CAT-002 Variant (master; nameAr / nameEn derived at read, D-P2-08) ──
  - {id: DBF-CAT-022, table: CAT_VARIANT, column: VARIANT_PK, type: "BIGINT", entity_field: ENT-CAT-002.variantPk, traces: [ENT-CAT-002, REQ-CAT-025, REQ-CAT-050], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-023, table: CAT_VARIANT, column: CODE, type: "VARCHAR(20)", entity_field: ENT-CAT-002.code, traces: [ENT-CAT-002, REQ-CAT-033, REQ-CAT-034], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-024, table: CAT_VARIANT, column: IS_DEFAULT_FL, type: "BOOLEAN", entity_field: ENT-CAT-002.isDefaultFl, traces: [ENT-CAT-002, REQ-CAT-025, REQ-CAT-018], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-CAT-025, table: CAT_VARIANT, column: EINVOICE_CODE_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-002.einvoiceCodeTypeCode, traces: [ENT-CAT-002, REQ-CAT-118, REQ-CAT-119, REQ-CAT-123], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-026, table: CAT_VARIANT, column: EINVOICE_CODE, type: "VARCHAR(100)", entity_field: ENT-CAT-002.einvoiceCode, traces: [ENT-CAT-002, REQ-CAT-118, REQ-CAT-119, REQ-CAT-123], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-027, table: CAT_VARIANT, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-002.isActiveFl, traces: [ENT-CAT-002, REQ-CAT-050, REQ-CAT-053, REQ-CAT-062], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-028, table: CAT_VARIANT, column: ITEM_FK, type: "BIGINT", entity_field: ENT-CAT-002.itemFk, traces: [ENT-CAT-002, REQ-CAT-025, REQ-CAT-049], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-029, table: CAT_VARIANT, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-002.tenantId, traces: [ENT-CAT-002, REQ-CAT-001], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-030, table: CAT_VARIANT, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-002.version, traces: [ENT-CAT-002, REQ-CAT-010], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-031, table: CAT_VARIANT, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-002.createdBy, traces: [ENT-CAT-002, REQ-CAT-025], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-032, table: CAT_VARIANT, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-002.createdAt, traces: [ENT-CAT-002, REQ-CAT-025], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-033, table: CAT_VARIANT, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-002.updatedBy, traces: [ENT-CAT-002, REQ-CAT-118], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-034, table: CAT_VARIANT, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-002.updatedAt, traces: [ENT-CAT-002, REQ-CAT-017, REQ-CAT-020], nullable: "NULL", default: "—"}
  # ── CAT_VAR_ATTR — ENT-CAT-003 Variant attribute (lookup) ──
  - {id: DBF-CAT-035, table: CAT_VAR_ATTR, column: VARIANT_ATTRIBUTE_PK, type: "BIGINT", entity_field: ENT-CAT-003.variantAttributePk, traces: [ENT-CAT-003, REQ-CAT-042, REQ-CAT-048], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-036, table: CAT_VAR_ATTR, column: CODE, type: "VARCHAR(30)", entity_field: ENT-CAT-003.code, traces: [ENT-CAT-003, REQ-CAT-042, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-037, table: CAT_VAR_ATTR, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-003.nameAr, traces: [ENT-CAT-003, REQ-CAT-042, REQ-CAT-043, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-038, table: CAT_VAR_ATTR, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-003.nameEn, traces: [ENT-CAT-003, REQ-CAT-043], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-039, table: CAT_VAR_ATTR, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-003.sortOrder, traces: [ENT-CAT-003, REQ-CAT-043, REQ-CAT-048], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-040, table: CAT_VAR_ATTR, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-003.isActiveFl, traces: [ENT-CAT-003, REQ-CAT-044, REQ-CAT-054], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-041, table: CAT_VAR_ATTR, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-003.tenantId, traces: [ENT-CAT-003, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-042, table: CAT_VAR_ATTR, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-003.version, traces: [ENT-CAT-003, REQ-CAT-043], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-043, table: CAT_VAR_ATTR, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-003.createdBy, traces: [ENT-CAT-003, REQ-CAT-042], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-044, table: CAT_VAR_ATTR, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-003.createdAt, traces: [ENT-CAT-003, REQ-CAT-042], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-045, table: CAT_VAR_ATTR, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-003.updatedBy, traces: [ENT-CAT-003, REQ-CAT-043], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-046, table: CAT_VAR_ATTR, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-003.updatedAt, traces: [ENT-CAT-003, REQ-CAT-043], nullable: "NULL", default: "—"}
  # ── CAT_VAR_ATTR_VALUE — ENT-CAT-004 Variant attribute value (lookup) ──
  - {id: DBF-CAT-047, table: CAT_VAR_ATTR_VALUE, column: VARIANT_ATTRIBUTE_VALUE_PK, type: "BIGINT", entity_field: ENT-CAT-004.variantAttributeValuePk, traces: [ENT-CAT-004, REQ-CAT-045, REQ-CAT-048], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-048, table: CAT_VAR_ATTR_VALUE, column: CODE, type: "VARCHAR(30)", entity_field: ENT-CAT-004.code, traces: [ENT-CAT-004, REQ-CAT-045, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-049, table: CAT_VAR_ATTR_VALUE, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-004.nameAr, traces: [ENT-CAT-004, REQ-CAT-045, REQ-CAT-046, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-050, table: CAT_VAR_ATTR_VALUE, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-004.nameEn, traces: [ENT-CAT-004, REQ-CAT-046], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-051, table: CAT_VAR_ATTR_VALUE, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-004.sortOrder, traces: [ENT-CAT-004, REQ-CAT-046, REQ-CAT-048], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-052, table: CAT_VAR_ATTR_VALUE, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-004.isActiveFl, traces: [ENT-CAT-004, REQ-CAT-047, REQ-CAT-054], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-053, table: CAT_VAR_ATTR_VALUE, column: VARIANT_ATTRIBUTE_FK, type: "BIGINT", entity_field: ENT-CAT-004.variantAttributeFk, traces: [ENT-CAT-004, REQ-CAT-045, REQ-CAT-048], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-054, table: CAT_VAR_ATTR_VALUE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-004.tenantId, traces: [ENT-CAT-004, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-055, table: CAT_VAR_ATTR_VALUE, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-004.version, traces: [ENT-CAT-004, REQ-CAT-046], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-056, table: CAT_VAR_ATTR_VALUE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-004.createdBy, traces: [ENT-CAT-004, REQ-CAT-045], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-057, table: CAT_VAR_ATTR_VALUE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-004.createdAt, traces: [ENT-CAT-004, REQ-CAT-045], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-058, table: CAT_VAR_ATTR_VALUE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-004.updatedBy, traces: [ENT-CAT-004, REQ-CAT-046], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-059, table: CAT_VAR_ATTR_VALUE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-004.updatedAt, traces: [ENT-CAT-004, REQ-CAT-046], nullable: "NULL", default: "—"}
  # ── CAT_VAR_ATTR_ASSIGN — ENT-CAT-005 Variant attribute assignment (master, dependent) ──
  - {id: DBF-CAT-060, table: CAT_VAR_ATTR_ASSIGN, column: VARIANT_ATTRIBUTE_ASSIGNMENT_PK, type: "BIGINT", entity_field: ENT-CAT-005.variantAttributeAssignmentPk, traces: [ENT-CAT-005, REQ-CAT-049, REQ-CAT-018], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-061, table: CAT_VAR_ATTR_ASSIGN, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-005.isActiveFl, traces: [ENT-CAT-005, REQ-CAT-022, REQ-CAT-054], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-062, table: CAT_VAR_ATTR_ASSIGN, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-CAT-005.variantFk, traces: [ENT-CAT-005, REQ-CAT-049, REQ-CAT-018], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-063, table: CAT_VAR_ATTR_ASSIGN, column: VARIANT_ATTRIBUTE_FK, type: "BIGINT", entity_field: ENT-CAT-005.variantAttributeFk, traces: [ENT-CAT-005, REQ-CAT-049, REQ-CAT-051, REQ-CAT-052], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-064, table: CAT_VAR_ATTR_ASSIGN, column: VARIANT_ATTRIBUTE_VALUE_FK, type: "BIGINT", entity_field: ENT-CAT-005.variantAttributeValueFk, traces: [ENT-CAT-005, REQ-CAT-049, REQ-CAT-051], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-065, table: CAT_VAR_ATTR_ASSIGN, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-005.tenantId, traces: [ENT-CAT-005, REQ-CAT-049], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-066, table: CAT_VAR_ATTR_ASSIGN, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-005.version, traces: [ENT-CAT-005, REQ-CAT-018], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-067, table: CAT_VAR_ATTR_ASSIGN, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-005.createdBy, traces: [ENT-CAT-005, REQ-CAT-049], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-068, table: CAT_VAR_ATTR_ASSIGN, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-005.createdAt, traces: [ENT-CAT-005, REQ-CAT-049], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-069, table: CAT_VAR_ATTR_ASSIGN, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-005.updatedBy, traces: [ENT-CAT-005, REQ-CAT-018], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-070, table: CAT_VAR_ATTR_ASSIGN, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-005.updatedAt, traces: [ENT-CAT-005, REQ-CAT-018], nullable: "NULL", default: "—"}
  # ── CAT_CATEGORY — ENT-CAT-006 Category (master) ──
  - {id: DBF-CAT-071, table: CAT_CATEGORY, column: CATEGORY_PK, type: "BIGINT", entity_field: ENT-CAT-006.categoryPk, traces: [ENT-CAT-006, REQ-CAT-064, REQ-CAT-067], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-072, table: CAT_CATEGORY, column: CODE, type: "VARCHAR(30)", entity_field: ENT-CAT-006.code, traces: [ENT-CAT-006, REQ-CAT-064, REQ-CAT-070], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-073, table: CAT_CATEGORY, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-006.nameAr, traces: [ENT-CAT-006, REQ-CAT-064, REQ-CAT-065, REQ-CAT-070], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-074, table: CAT_CATEGORY, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-006.nameEn, traces: [ENT-CAT-006, REQ-CAT-065], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-075, table: CAT_CATEGORY, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-006.sortOrder, traces: [ENT-CAT-006, REQ-CAT-065, REQ-CAT-067], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-076, table: CAT_CATEGORY, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-006.isActiveFl, traces: [ENT-CAT-006, REQ-CAT-066, REQ-CAT-071], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-077, table: CAT_CATEGORY, column: PARENT_CATEGORY_FK, type: "BIGINT", entity_field: ENT-CAT-006.parentCategoryFk, traces: [ENT-CAT-006, REQ-CAT-064, REQ-CAT-065, REQ-CAT-069], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-078, table: CAT_CATEGORY, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-006.tenantId, traces: [ENT-CAT-006, REQ-CAT-070], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-079, table: CAT_CATEGORY, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-006.version, traces: [ENT-CAT-006, REQ-CAT-065], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-080, table: CAT_CATEGORY, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-006.createdBy, traces: [ENT-CAT-006, REQ-CAT-064], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-081, table: CAT_CATEGORY, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-006.createdAt, traces: [ENT-CAT-006, REQ-CAT-064], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-082, table: CAT_CATEGORY, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-006.updatedBy, traces: [ENT-CAT-006, REQ-CAT-065], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-083, table: CAT_CATEGORY, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-006.updatedAt, traces: [ENT-CAT-006, REQ-CAT-065], nullable: "NULL", default: "—"}
  # ── CAT_BUNDLE_COMP — ENT-CAT-007 Bundle component (master, dependent) ──
  - {id: DBF-CAT-084, table: CAT_BUNDLE_COMP, column: BUNDLE_COMPONENT_PK, type: "BIGINT", entity_field: ENT-CAT-007.bundleComponentPk, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-058], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-085, table: CAT_BUNDLE_COMP, column: QUANTITY, type: "NUMERIC(18,3)", entity_field: ENT-CAT-007.quantity, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-057, REQ-CAT-059], nullable: "NOT NULL", default: "1"}
  - {id: DBF-CAT-086, table: CAT_BUNDLE_COMP, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-007.sortOrder, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-058], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-087, table: CAT_BUNDLE_COMP, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-007.isActiveFl, traces: [ENT-CAT-007, REQ-CAT-057, REQ-CAT-058, REQ-CAT-062], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-088, table: CAT_BUNDLE_COMP, column: BUNDLE_ITEM_FK, type: "BIGINT", entity_field: ENT-CAT-007.bundleItemFk, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-058], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-089, table: CAT_BUNDLE_COMP, column: COMPONENT_VARIANT_FK, type: "BIGINT", entity_field: ENT-CAT-007.componentVariantFk, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-060, REQ-CAT-062], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-090, table: CAT_BUNDLE_COMP, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-007.tenantId, traces: [ENT-CAT-007, REQ-CAT-056], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-091, table: CAT_BUNDLE_COMP, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-007.version, traces: [ENT-CAT-007, REQ-CAT-057], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-092, table: CAT_BUNDLE_COMP, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-007.createdBy, traces: [ENT-CAT-007, REQ-CAT-056], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-093, table: CAT_BUNDLE_COMP, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-007.createdAt, traces: [ENT-CAT-007, REQ-CAT-056], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-094, table: CAT_BUNDLE_COMP, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-007.updatedBy, traces: [ENT-CAT-007, REQ-CAT-057], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-095, table: CAT_BUNDLE_COMP, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-007.updatedAt, traces: [ENT-CAT-007, REQ-CAT-057], nullable: "NULL", default: "—"}
  # ── CAT_ITEM_UNIT — ENT-CAT-008 Item unit (master, dependent) ──
  - {id: DBF-CAT-096, table: CAT_ITEM_UNIT, column: ITEM_UNIT_PK, type: "BIGINT", entity_field: ENT-CAT-008.itemUnitPk, traces: [ENT-CAT-008, REQ-CAT-077, REQ-CAT-079], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-097, table: CAT_ITEM_UNIT, column: UOM_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-008.uomCode, traces: [ENT-CAT-008, REQ-CAT-074, REQ-CAT-077, REQ-CAT-080], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-098, table: CAT_ITEM_UNIT, column: CONVERSION_FACTOR, type: "NUMERIC(18,6)", entity_field: ENT-CAT-008.conversionFactor, traces: [ENT-CAT-008, REQ-CAT-077, REQ-CAT-080, REQ-CAT-081], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-099, table: CAT_ITEM_UNIT, column: IS_PURCHASE_FL, type: "BOOLEAN", entity_field: ENT-CAT-008.isPurchaseFl, traces: [ENT-CAT-008, REQ-CAT-077, REQ-CAT-078], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-100, table: CAT_ITEM_UNIT, column: IS_SALES_FL, type: "BOOLEAN", entity_field: ENT-CAT-008.isSalesFl, traces: [ENT-CAT-008, REQ-CAT-077, REQ-CAT-078], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-101, table: CAT_ITEM_UNIT, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-008.isActiveFl, traces: [ENT-CAT-008, REQ-CAT-079, REQ-CAT-080], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-102, table: CAT_ITEM_UNIT, column: ITEM_FK, type: "BIGINT", entity_field: ENT-CAT-008.itemFk, traces: [ENT-CAT-008, REQ-CAT-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-103, table: CAT_ITEM_UNIT, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-008.tenantId, traces: [ENT-CAT-008, REQ-CAT-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-104, table: CAT_ITEM_UNIT, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-008.version, traces: [ENT-CAT-008, REQ-CAT-078], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-105, table: CAT_ITEM_UNIT, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-008.createdBy, traces: [ENT-CAT-008, REQ-CAT-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-106, table: CAT_ITEM_UNIT, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-008.createdAt, traces: [ENT-CAT-008, REQ-CAT-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-107, table: CAT_ITEM_UNIT, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-008.updatedBy, traces: [ENT-CAT-008, REQ-CAT-078], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-108, table: CAT_ITEM_UNIT, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-008.updatedAt, traces: [ENT-CAT-008, REQ-CAT-078], nullable: "NULL", default: "—"}
  # ── CAT_BARCODE — ENT-CAT-009 Barcode (master, dependent) ──
  - {id: DBF-CAT-109, table: CAT_BARCODE, column: BARCODE_PK, type: "BIGINT", entity_field: ENT-CAT-009.barcodePk, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-086], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-110, table: CAT_BARCODE, column: BARCODE_VALUE, type: "VARCHAR(48)", entity_field: ENT-CAT-009.barcodeValue, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-084, REQ-CAT-086, REQ-CAT-087, REQ-CAT-088], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-111, table: CAT_BARCODE, column: BARCODE_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-009.barcodeTypeCode, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-084, REQ-CAT-096], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-112, table: CAT_BARCODE, column: UOM_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-009.uomCode, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-086, REQ-CAT-089], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-113, table: CAT_BARCODE, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-009.isActiveFl, traces: [ENT-CAT-009, REQ-CAT-085, REQ-CAT-086], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-114, table: CAT_BARCODE, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-CAT-009.variantFk, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-086, REQ-CAT-019], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-115, table: CAT_BARCODE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-009.tenantId, traces: [ENT-CAT-009, REQ-CAT-087], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-116, table: CAT_BARCODE, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-009.version, traces: [ENT-CAT-009, REQ-CAT-085], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-117, table: CAT_BARCODE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-009.createdBy, traces: [ENT-CAT-009, REQ-CAT-083], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-118, table: CAT_BARCODE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-009.createdAt, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-101], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-119, table: CAT_BARCODE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-009.updatedBy, traces: [ENT-CAT-009, REQ-CAT-085], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-120, table: CAT_BARCODE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-009.updatedAt, traces: [ENT-CAT-009, REQ-CAT-085], nullable: "NULL", default: "—"}
  # ── CAT_ITEM_MEDIA — ENT-CAT-010 Item media (master, dependent) ──
  - {id: DBF-CAT-121, table: CAT_ITEM_MEDIA, column: ITEM_MEDIA_PK, type: "BIGINT", entity_field: ENT-CAT-010.itemMediaPk, traces: [ENT-CAT-010, REQ-CAT-105, REQ-CAT-109], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-122, table: CAT_ITEM_MEDIA, column: FILE_DOCUMENT_REF, type: "BIGINT", entity_field: ENT-CAT-010.fileDocumentRef, traces: [ENT-CAT-010, REQ-CAT-105, REQ-CAT-110], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-123, table: CAT_ITEM_MEDIA, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-010.nameAr, traces: [ENT-CAT-010, REQ-CAT-105, REQ-CAT-106], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-124, table: CAT_ITEM_MEDIA, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-010.nameEn, traces: [ENT-CAT-010, REQ-CAT-106], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-125, table: CAT_ITEM_MEDIA, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-010.sortOrder, traces: [ENT-CAT-010, REQ-CAT-105, REQ-CAT-106, REQ-CAT-111], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-126, table: CAT_ITEM_MEDIA, column: IS_PRIMARY_FL, type: "BOOLEAN", entity_field: ENT-CAT-010.isPrimaryFl, traces: [ENT-CAT-010, REQ-CAT-106, REQ-CAT-108, REQ-CAT-111], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-CAT-127, table: CAT_ITEM_MEDIA, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-010.isActiveFl, traces: [ENT-CAT-010, REQ-CAT-107, REQ-CAT-110], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-128, table: CAT_ITEM_MEDIA, column: ITEM_FK, type: "BIGINT", entity_field: ENT-CAT-010.itemFk, traces: [ENT-CAT-010, REQ-CAT-105], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-129, table: CAT_ITEM_MEDIA, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-CAT-010.variantFk, traces: [ENT-CAT-010, REQ-CAT-106, REQ-CAT-109, REQ-CAT-112], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-130, table: CAT_ITEM_MEDIA, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-010.tenantId, traces: [ENT-CAT-010, REQ-CAT-105], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-131, table: CAT_ITEM_MEDIA, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-010.version, traces: [ENT-CAT-010, REQ-CAT-106], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-132, table: CAT_ITEM_MEDIA, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-010.createdBy, traces: [ENT-CAT-010, REQ-CAT-105], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-133, table: CAT_ITEM_MEDIA, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-010.createdAt, traces: [ENT-CAT-010, REQ-CAT-105], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-134, table: CAT_ITEM_MEDIA, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-010.updatedBy, traces: [ENT-CAT-010, REQ-CAT-106], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-135, table: CAT_ITEM_MEDIA, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-010.updatedAt, traces: [ENT-CAT-010, REQ-CAT-106], nullable: "NULL", default: "—"}
  # ── CAT_SCALE_LABEL_LAYOUT — ENT-CAT-011 Scale label layout (config) ──
  - {id: DBF-CAT-136, table: CAT_SCALE_LABEL_LAYOUT, column: SCALE_LABEL_LAYOUT_PK, type: "BIGINT", entity_field: ENT-CAT-011.scaleLabelLayoutPk, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-095], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-137, table: CAT_SCALE_LABEL_LAYOUT, column: KEY, type: "VARCHAR(2)", entity_field: ENT-CAT-011.key, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-097, REQ-CAT-098], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-138, table: CAT_SCALE_LABEL_LAYOUT, column: VALUE_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-011.valueAr, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-139, table: CAT_SCALE_LABEL_LAYOUT, column: VALUE_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-011.valueEn, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-140, table: CAT_SCALE_LABEL_LAYOUT, column: SCALE_CODE_LENGTH, type: "INTEGER", entity_field: ENT-CAT-011.scaleCodeLength, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-093, REQ-CAT-098, REQ-CAT-099], nullable: "NOT NULL", default: "5"}
  - {id: DBF-CAT-141, table: CAT_SCALE_LABEL_LAYOUT, column: EMBEDDED_VALUE_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-011.embeddedValueTypeCode, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-093, REQ-CAT-097], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-142, table: CAT_SCALE_LABEL_LAYOUT, column: VALUE_DECIMALS, type: "INTEGER", entity_field: ENT-CAT-011.valueDecimals, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-093, REQ-CAT-098], nullable: "NOT NULL", default: "3"}
  - {id: DBF-CAT-143, table: CAT_SCALE_LABEL_LAYOUT, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-011.isActiveFl, traces: [ENT-CAT-011, REQ-CAT-094, REQ-CAT-095], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-144, table: CAT_SCALE_LABEL_LAYOUT, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-011.tenantId, traces: [ENT-CAT-011, REQ-CAT-098], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-145, table: CAT_SCALE_LABEL_LAYOUT, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-011.version, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-146, table: CAT_SCALE_LABEL_LAYOUT, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-011.createdBy, traces: [ENT-CAT-011, REQ-CAT-092], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-147, table: CAT_SCALE_LABEL_LAYOUT, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-011.createdAt, traces: [ENT-CAT-011, REQ-CAT-092], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-148, table: CAT_SCALE_LABEL_LAYOUT, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-011.updatedBy, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-149, table: CAT_SCALE_LABEL_LAYOUT, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-011.updatedAt, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NULL", default: "—"}
  # ── CAT_UNIT_EINVOICE_CODE — ENT-CAT-012 Unit e-invoice code (config) ──
  - {id: DBF-CAT-150, table: CAT_UNIT_EINVOICE_CODE, column: UNIT_EINVOICE_CODE_PK, type: "BIGINT", entity_field: ENT-CAT-012.unitEinvoiceCodePk, traces: [ENT-CAT-012, REQ-CAT-120, REQ-CAT-095], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-151, table: CAT_UNIT_EINVOICE_CODE, column: KEY, type: "VARCHAR(50)", entity_field: ENT-CAT-012.key, traces: [ENT-CAT-012, REQ-CAT-120, REQ-CAT-124], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-152, table: CAT_UNIT_EINVOICE_CODE, column: VALUE_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-012.valueAr, traces: [ENT-CAT-012, REQ-CAT-095], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-153, table: CAT_UNIT_EINVOICE_CODE, column: VALUE_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-012.valueEn, traces: [ENT-CAT-012, REQ-CAT-095], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-154, table: CAT_UNIT_EINVOICE_CODE, column: EINVOICE_UNIT_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-012.einvoiceUnitCode, traces: [ENT-CAT-012, REQ-CAT-120, REQ-CAT-121], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-155, table: CAT_UNIT_EINVOICE_CODE, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-012.isActiveFl, traces: [ENT-CAT-012, REQ-CAT-122, REQ-CAT-124], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-156, table: CAT_UNIT_EINVOICE_CODE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-012.tenantId, traces: [ENT-CAT-012, REQ-CAT-124], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-157, table: CAT_UNIT_EINVOICE_CODE, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-012.version, traces: [ENT-CAT-012, REQ-CAT-121], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-158, table: CAT_UNIT_EINVOICE_CODE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-012.createdBy, traces: [ENT-CAT-012, REQ-CAT-120], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-159, table: CAT_UNIT_EINVOICE_CODE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-012.createdAt, traces: [ENT-CAT-012, REQ-CAT-120], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-160, table: CAT_UNIT_EINVOICE_CODE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-012.updatedBy, traces: [ENT-CAT-012, REQ-CAT-121], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-161, table: CAT_UNIT_EINVOICE_CODE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-012.updatedAt, traces: [ENT-CAT-012, REQ-CAT-121], nullable: "NULL", default: "—"}
```

Total: 161 DBF ids across 12 tables.

| Table | ENT | Kind | DBF range |
|---|---|---|---|
| CAT_ITEM | ENT-CAT-001 | master | DBF-CAT-001 … DBF-CAT-021 |
| CAT_VARIANT | ENT-CAT-002 | master | DBF-CAT-022 … DBF-CAT-034 |
| CAT_VAR_ATTR | ENT-CAT-003 | lookup | DBF-CAT-035 … DBF-CAT-046 |
| CAT_VAR_ATTR_VALUE | ENT-CAT-004 | lookup | DBF-CAT-047 … DBF-CAT-059 |
| CAT_VAR_ATTR_ASSIGN | ENT-CAT-005 | master (dependent) | DBF-CAT-060 … DBF-CAT-070 |
| CAT_CATEGORY | ENT-CAT-006 | master | DBF-CAT-071 … DBF-CAT-083 |
| CAT_BUNDLE_COMP | ENT-CAT-007 | master (dependent) | DBF-CAT-084 … DBF-CAT-095 |
| CAT_ITEM_UNIT | ENT-CAT-008 | master (dependent) | DBF-CAT-096 … DBF-CAT-108 |
| CAT_BARCODE | ENT-CAT-009 | master (dependent) | DBF-CAT-109 … DBF-CAT-120 |
| CAT_ITEM_MEDIA | ENT-CAT-010 | master (dependent) | DBF-CAT-121 … DBF-CAT-135 |
| CAT_SCALE_LABEL_LAYOUT | ENT-CAT-011 | config | DBF-CAT-136 … DBF-CAT-149 |
| CAT_UNIT_EINVOICE_CODE | ENT-CAT-012 | config | DBF-CAT-150 … DBF-CAT-161 |

Origin of the standard columns. Each table carries the SRS fields of its kind (A3). It also carries the given's entity column set: `tenantId`, `version`, `createdBy`, `createdAt`, `updatedBy`, `updatedAt` (erp-core `AuditableEntity` and the tenancy convention; D-P2-02). Each such column traces to its ENT and to a REQ that reads it. Examples: AC-CAT-004 reads `updatedBy`, and AC-CAT-018 and AC-CAT-021 read `updatedAt`.

RULE data sources. Every `ENT.field` named in a RULE `Data source` line of SRS A5 is bound to a DBF above (C6.9). The two exceptions are `ENT-CAT-002.nameAr` and `ENT-CAT-002.nameEn`. No RULE names them, and they are derived at read (D-P2-08).

## 3 — XM register

```yaml name=xm-register
records: []
```

CAT consumes no entity of another business module (SRS A8 `module-dependencies`: `consumes: []`). It is first in the fixed module order [KB:raw-idea.md §5]. So the module has no HARD-FK and no SOFT-READ edge, and the script creates no cross-module column.

Given references create no XM:
- `CAT_ITEM_MEDIA.FILE_DOCUMENT_REF` (DBF-CAT-122) holds the given `FILE_DOCUMENT` identifier. It is a given soft read with no constraint (SRS A3 "GIVEN, no XM edge").
- Every `TENANT_ID` column holds the given tenant identifier.
- Lookup codes are given lookup values read through `MdlLookupApi`.

## 4 — FULL_DATABASE_SCRIPT

```sql
-- ══════════════════════════════════════════════════════════════════
-- CAT v2 — P2 — FULL_DATABASE_SCRIPT — PostgreSQL 16
-- Migration for CS-CAT-001 (ADDITIVE): the current state holds no earlier CAT script,
-- so this migration creates every CAT object. No schema prefix. Identifiers are UPPER_SNAKE_CASE, unquoted.
-- PK generation: GENERATED ALWAYS AS IDENTITY on every PK column (profile stack.db.pk_generation).
-- ══════════════════════════════════════════════════════════════════

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 1 — SEQUENCES
-- none: every PK uses the identity clause. Item / Variant codes and internal
-- barcode digits come from the core number series (BLOCK 8c), never from a CAT sequence.
-- ──────────────────────────────────────────────────────────────────

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 2 — PARENT TABLES (no FK dependency on another CAT table)
-- ──────────────────────────────────────────────────────────────────

CREATE TABLE CAT_CATEGORY (
    CATEGORY_PK                 BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(30)   NOT NULL,
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    PARENT_CATEGORY_FK          BIGINT,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_VAR_ATTR (
    VARIANT_ATTRIBUTE_PK        BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(30)   NOT NULL,
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_SCALE_LABEL_LAYOUT (
    SCALE_LABEL_LAYOUT_PK       BIGINT        GENERATED ALWAYS AS IDENTITY,
    KEY                         VARCHAR(2)    NOT NULL,
    VALUE_AR                    VARCHAR(200),
    VALUE_EN                    VARCHAR(200),
    SCALE_CODE_LENGTH           INTEGER       NOT NULL DEFAULT 5,
    EMBEDDED_VALUE_TYPE_CODE    VARCHAR(50)   NOT NULL,
    VALUE_DECIMALS              INTEGER       NOT NULL DEFAULT 3,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_UNIT_EINVOICE_CODE (
    UNIT_EINVOICE_CODE_PK       BIGINT        GENERATED ALWAYS AS IDENTITY,
    KEY                         VARCHAR(50)   NOT NULL,
    VALUE_AR                    VARCHAR(200),
    VALUE_EN                    VARCHAR(200),
    EINVOICE_UNIT_CODE          VARCHAR(50)   NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 3 — CHILD TABLES (chain: CATEGORY → ITEM → VARIANT → assignment / bundle / unit / barcode / media)
-- ──────────────────────────────────────────────────────────────────

CREATE TABLE CAT_ITEM (
    ITEM_PK                     BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(20)   NOT NULL,
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    ITEM_KIND_CODE              VARCHAR(50)   NOT NULL,
    BASE_UOM_CODE               VARCHAR(50)   NOT NULL,
    BRAND_CODE                  VARCHAR(50),
    TAX_CLASS_CODE              VARCHAR(50)   NOT NULL,
    EINVOICE_CODE_TYPE_CODE     VARCHAR(50),
    EINVOICE_CODE               VARCHAR(100),
    DESCRIPTION_AR              VARCHAR(2000),
    DESCRIPTION_EN              VARCHAR(2000),
    PUBLISH_STATE_CODE          VARCHAR(50)   NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    CATEGORY_FK                 BIGINT,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_VAR_ATTR_VALUE (
    VARIANT_ATTRIBUTE_VALUE_PK  BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(30)   NOT NULL,
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    VARIANT_ATTRIBUTE_FK        BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_VARIANT (
    VARIANT_PK                  BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(20)   NOT NULL,
    IS_DEFAULT_FL               BOOLEAN       NOT NULL DEFAULT FALSE,
    EINVOICE_CODE_TYPE_CODE     VARCHAR(50),
    EINVOICE_CODE               VARCHAR(100),
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    ITEM_FK                     BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_VAR_ATTR_ASSIGN (
    VARIANT_ATTRIBUTE_ASSIGNMENT_PK BIGINT    GENERATED ALWAYS AS IDENTITY,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    VARIANT_FK                  BIGINT        NOT NULL,
    VARIANT_ATTRIBUTE_FK        BIGINT        NOT NULL,
    VARIANT_ATTRIBUTE_VALUE_FK  BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_BUNDLE_COMP (
    BUNDLE_COMPONENT_PK         BIGINT        GENERATED ALWAYS AS IDENTITY,
    QUANTITY                    NUMERIC(18,3) NOT NULL DEFAULT 1,
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    BUNDLE_ITEM_FK              BIGINT        NOT NULL,
    COMPONENT_VARIANT_FK        BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_ITEM_UNIT (
    ITEM_UNIT_PK                BIGINT        GENERATED ALWAYS AS IDENTITY,
    UOM_CODE                    VARCHAR(50)   NOT NULL,
    CONVERSION_FACTOR           NUMERIC(18,6) NOT NULL,
    IS_PURCHASE_FL              BOOLEAN       NOT NULL DEFAULT TRUE,
    IS_SALES_FL                 BOOLEAN       NOT NULL DEFAULT TRUE,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    ITEM_FK                     BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_BARCODE (
    BARCODE_PK                  BIGINT        GENERATED ALWAYS AS IDENTITY,
    BARCODE_VALUE               VARCHAR(48)   NOT NULL,
    BARCODE_TYPE_CODE           VARCHAR(50)   NOT NULL,
    UOM_CODE                    VARCHAR(50)   NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    VARIANT_FK                  BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_ITEM_MEDIA (
    ITEM_MEDIA_PK               BIGINT        GENERATED ALWAYS AS IDENTITY,
    FILE_DOCUMENT_REF           BIGINT        NOT NULL,   -- given erp-core FILE_DOCUMENT.ID: soft read, no constraint, no XM
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_PRIMARY_FL               BOOLEAN       NOT NULL DEFAULT FALSE,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    ITEM_FK                     BIGINT        NOT NULL,
    VARIANT_FK                  BIGINT,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 4 — COMMENTS (each column comment cites its DBF id)
-- ──────────────────────────────────────────────────────────────────

COMMENT ON TABLE CAT_CATEGORY IS 'ENT-CAT-006 Category / فئة — node of the tenant item classification tree (max 5 levels)';
COMMENT ON COLUMN CAT_CATEGORY.CATEGORY_PK IS 'DBF-CAT-071 — Category identifier (identity)';
COMMENT ON COLUMN CAT_CATEGORY.CODE IS 'DBF-CAT-072 — Category code, 1-30 Latin letters, digits or hyphens, unique per tenant (RULE-CAT-024)';
COMMENT ON COLUMN CAT_CATEGORY.NAME_AR IS 'DBF-CAT-073 — Arabic name, at least one Arabic letter (RULE-CAT-001)';
COMMENT ON COLUMN CAT_CATEGORY.NAME_EN IS 'DBF-CAT-074 — English name (RULE-CAT-002)';
COMMENT ON COLUMN CAT_CATEGORY.SORT_ORDER IS 'DBF-CAT-075 — Order under the parent';
COMMENT ON COLUMN CAT_CATEGORY.IS_ACTIVE_FL IS 'DBF-CAT-076 — Active flag; deactivation, never deletion (RULE-CAT-048)';
COMMENT ON COLUMN CAT_CATEGORY.PARENT_CATEGORY_FK IS 'DBF-CAT-077 — Parent Category; empty = top level (RULE-CAT-023)';
COMMENT ON COLUMN CAT_CATEGORY.TENANT_ID IS 'DBF-CAT-078 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_CATEGORY.VERSION IS 'DBF-CAT-079 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_CATEGORY.CREATED_BY IS 'DBF-CAT-080 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_CATEGORY.CREATED_AT IS 'DBF-CAT-081 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_CATEGORY.UPDATED_BY IS 'DBF-CAT-082 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_CATEGORY.UPDATED_AT IS 'DBF-CAT-083 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_VAR_ATTR IS 'ENT-CAT-003 Variant attribute / خاصية المتغيّر — dimension Variants differ by';
COMMENT ON COLUMN CAT_VAR_ATTR.VARIANT_ATTRIBUTE_PK IS 'DBF-CAT-035 — Variant attribute identifier (identity)';
COMMENT ON COLUMN CAT_VAR_ATTR.CODE IS 'DBF-CAT-036 — Attribute code, unique per tenant (RULE-CAT-016)';
COMMENT ON COLUMN CAT_VAR_ATTR.NAME_AR IS 'DBF-CAT-037 — Arabic name, unique among active attributes (RULE-CAT-001, RULE-CAT-016)';
COMMENT ON COLUMN CAT_VAR_ATTR.NAME_EN IS 'DBF-CAT-038 — English name (RULE-CAT-002)';
COMMENT ON COLUMN CAT_VAR_ATTR.SORT_ORDER IS 'DBF-CAT-039 — List order';
COMMENT ON COLUMN CAT_VAR_ATTR.IS_ACTIVE_FL IS 'DBF-CAT-040 — Active flag (RULE-CAT-017)';
COMMENT ON COLUMN CAT_VAR_ATTR.TENANT_ID IS 'DBF-CAT-041 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_VAR_ATTR.VERSION IS 'DBF-CAT-042 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_VAR_ATTR.CREATED_BY IS 'DBF-CAT-043 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR.CREATED_AT IS 'DBF-CAT-044 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR.UPDATED_BY IS 'DBF-CAT-045 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR.UPDATED_AT IS 'DBF-CAT-046 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_SCALE_LABEL_LAYOUT IS 'ENT-CAT-011 Scale label layout / إعداد ملصق الميزان — tenant layout of 13-digit scale labels';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.SCALE_LABEL_LAYOUT_PK IS 'DBF-CAT-136 — Scale label layout identifier (identity)';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.KEY IS 'DBF-CAT-137 — Label prefix 02 or 20-29, unique among active layouts (RULE-CAT-034)';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.VALUE_AR IS 'DBF-CAT-138 — Arabic layout description';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.VALUE_EN IS 'DBF-CAT-139 — English layout description';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.SCALE_CODE_LENGTH IS 'DBF-CAT-140 — Scale code length 4-6; embedded value takes 10 minus this length';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.EMBEDDED_VALUE_TYPE_CODE IS 'DBF-CAT-141 — Embedded value type, lookup CAT_SCALE_VALUE_TYPE code';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.VALUE_DECIMALS IS 'DBF-CAT-142 — Decimals of the embedded value 0-3';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.IS_ACTIVE_FL IS 'DBF-CAT-143 — Active flag';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.TENANT_ID IS 'DBF-CAT-144 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.VERSION IS 'DBF-CAT-145 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.CREATED_BY IS 'DBF-CAT-146 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.CREATED_AT IS 'DBF-CAT-147 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.UPDATED_BY IS 'DBF-CAT-148 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.UPDATED_AT IS 'DBF-CAT-149 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_UNIT_EINVOICE_CODE IS 'ENT-CAT-012 Unit e-invoice code / رمز الوحدة في الفاتورة الإلكترونية — e-invoice unit of one unit of measure';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.UNIT_EINVOICE_CODE_PK IS 'DBF-CAT-150 — Unit e-invoice code identifier (identity)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.KEY IS 'DBF-CAT-151 — Unit of measure, lookup CAT_UOM code; one active row per unit (RULE-CAT-043)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.VALUE_AR IS 'DBF-CAT-152 — Arabic name, copied from the CAT_UOM label at save (not entered)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.VALUE_EN IS 'DBF-CAT-153 — English name, copied from the CAT_UOM label at save (not entered)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.EINVOICE_UNIT_CODE IS 'DBF-CAT-154 — E-invoice unit, lookup CAT_EINVOICE_UNIT code';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.IS_ACTIVE_FL IS 'DBF-CAT-155 — Active flag';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.TENANT_ID IS 'DBF-CAT-156 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.VERSION IS 'DBF-CAT-157 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.CREATED_BY IS 'DBF-CAT-158 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.CREATED_AT IS 'DBF-CAT-159 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.UPDATED_BY IS 'DBF-CAT-160 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.UPDATED_AT IS 'DBF-CAT-161 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_ITEM IS 'ENT-CAT-001 Item / صنف — the one catalogue record every channel reads';
COMMENT ON COLUMN CAT_ITEM.ITEM_PK IS 'DBF-CAT-001 — Item identifier (identity)';
COMMENT ON COLUMN CAT_ITEM.CODE IS 'DBF-CAT-002 — Item code from core number series CAT_ITEM, read-only (RULE-CAT-004)';
COMMENT ON COLUMN CAT_ITEM.NAME_AR IS 'DBF-CAT-003 — Arabic name, at least one Arabic letter, unique among active Items (RULE-CAT-001, RULE-CAT-003)';
COMMENT ON COLUMN CAT_ITEM.NAME_EN IS 'DBF-CAT-004 — English name; NAME_AR is returned when empty (RULE-CAT-002)';
COMMENT ON COLUMN CAT_ITEM.ITEM_KIND_CODE IS 'DBF-CAT-005 — Item kind, lookup CAT_ITEM_KIND code, fixed after first save (RULE-CAT-022)';
COMMENT ON COLUMN CAT_ITEM.BASE_UOM_CODE IS 'DBF-CAT-006 — Base unit of measure, lookup CAT_UOM code, fixed after first save (RULE-CAT-007)';
COMMENT ON COLUMN CAT_ITEM.BRAND_CODE IS 'DBF-CAT-007 — Brand, lookup CAT_BRAND code (RULE-CAT-006)';
COMMENT ON COLUMN CAT_ITEM.TAX_CLASS_CODE IS 'DBF-CAT-008 — Tax class, lookup CAT_TAX_CLASS code (RULE-CAT-006)';
COMMENT ON COLUMN CAT_ITEM.EINVOICE_CODE_TYPE_CODE IS 'DBF-CAT-009 — E-invoice code type, lookup CAT_EINVOICE_ITEM_CODE_TYPE code (RULE-CAT-042)';
COMMENT ON COLUMN CAT_ITEM.EINVOICE_CODE IS 'DBF-CAT-010 — E-invoice code (RULE-CAT-042)';
COMMENT ON COLUMN CAT_ITEM.DESCRIPTION_AR IS 'DBF-CAT-011 — Arabic description';
COMMENT ON COLUMN CAT_ITEM.DESCRIPTION_EN IS 'DBF-CAT-012 — English description';
COMMENT ON COLUMN CAT_ITEM.PUBLISH_STATE_CODE IS 'DBF-CAT-013 — Publish state, lookup CAT_PUBLISH_STATE code (RULE-CAT-041)';
COMMENT ON COLUMN CAT_ITEM.IS_ACTIVE_FL IS 'DBF-CAT-014 — Active flag; deactivation, never deletion (RULE-CAT-048)';
COMMENT ON COLUMN CAT_ITEM.CATEGORY_FK IS 'DBF-CAT-015 — Category of the Item (RULE-CAT-026)';
COMMENT ON COLUMN CAT_ITEM.TENANT_ID IS 'DBF-CAT-016 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_ITEM.VERSION IS 'DBF-CAT-017 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_ITEM.CREATED_BY IS 'DBF-CAT-018 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM.CREATED_AT IS 'DBF-CAT-019 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_ITEM.UPDATED_BY IS 'DBF-CAT-020 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM.UPDATED_AT IS 'DBF-CAT-021 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_VAR_ATTR_VALUE IS 'ENT-CAT-004 Variant attribute value / قيمة خاصية المتغيّر — one value of a Variant attribute';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.VARIANT_ATTRIBUTE_VALUE_PK IS 'DBF-CAT-047 — Variant attribute value identifier (identity)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.CODE IS 'DBF-CAT-048 — Value code, unique per attribute (RULE-CAT-016)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.NAME_AR IS 'DBF-CAT-049 — Arabic name, unique among active values of the attribute (RULE-CAT-001, RULE-CAT-016)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.NAME_EN IS 'DBF-CAT-050 — English name (RULE-CAT-002)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.SORT_ORDER IS 'DBF-CAT-051 — Order within the attribute';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.IS_ACTIVE_FL IS 'DBF-CAT-052 — Active flag (RULE-CAT-017)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.VARIANT_ATTRIBUTE_FK IS 'DBF-CAT-053 — Attribute the value belongs to, fixed after creation';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.TENANT_ID IS 'DBF-CAT-054 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.VERSION IS 'DBF-CAT-055 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.CREATED_BY IS 'DBF-CAT-056 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.CREATED_AT IS 'DBF-CAT-057 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.UPDATED_BY IS 'DBF-CAT-058 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.UPDATED_AT IS 'DBF-CAT-059 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_VARIANT IS 'ENT-CAT-002 Variant / متغيّر الصنف — sellable, stockable version of an Item; names derived at read (D-15)';
COMMENT ON COLUMN CAT_VARIANT.VARIANT_PK IS 'DBF-CAT-022 — Variant identifier (identity)';
COMMENT ON COLUMN CAT_VARIANT.CODE IS 'DBF-CAT-023 — Variant code from core number series CAT_VARIANT, read-only (RULE-CAT-004)';
COMMENT ON COLUMN CAT_VARIANT.IS_DEFAULT_FL IS 'DBF-CAT-024 — True for the Variant created with the Item; one per Item';
COMMENT ON COLUMN CAT_VARIANT.EINVOICE_CODE_TYPE_CODE IS 'DBF-CAT-025 — E-invoice code type override, lookup CAT_EINVOICE_ITEM_CODE_TYPE code (RULE-CAT-042)';
COMMENT ON COLUMN CAT_VARIANT.EINVOICE_CODE IS 'DBF-CAT-026 — E-invoice code override (RULE-CAT-042)';
COMMENT ON COLUMN CAT_VARIANT.IS_ACTIVE_FL IS 'DBF-CAT-027 — Active flag (RULE-CAT-015, RULE-CAT-021, RULE-CAT-048)';
COMMENT ON COLUMN CAT_VARIANT.ITEM_FK IS 'DBF-CAT-028 — Item the Variant belongs to, fixed after creation';
COMMENT ON COLUMN CAT_VARIANT.TENANT_ID IS 'DBF-CAT-029 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_VARIANT.VERSION IS 'DBF-CAT-030 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_VARIANT.CREATED_BY IS 'DBF-CAT-031 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_VARIANT.CREATED_AT IS 'DBF-CAT-032 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_VARIANT.UPDATED_BY IS 'DBF-CAT-033 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_VARIANT.UPDATED_AT IS 'DBF-CAT-034 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_VAR_ATTR_ASSIGN IS 'ENT-CAT-005 Variant attribute assignment / إسناد قيمة خاصية للمتغيّر — the value a Variant carries for one attribute';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VARIANT_ATTRIBUTE_ASSIGNMENT_PK IS 'DBF-CAT-060 — Assignment identifier (identity)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.IS_ACTIVE_FL IS 'DBF-CAT-061 — Active flag, follows the Variant (RULE-CAT-010)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VARIANT_FK IS 'DBF-CAT-062 — Variant carrying the value';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VARIANT_ATTRIBUTE_FK IS 'DBF-CAT-063 — Attribute; one active assignment per Variant and attribute (RULE-CAT-013)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VARIANT_ATTRIBUTE_VALUE_FK IS 'DBF-CAT-064 — Value, of the same attribute (RULE-CAT-013)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.TENANT_ID IS 'DBF-CAT-065 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VERSION IS 'DBF-CAT-066 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.CREATED_BY IS 'DBF-CAT-067 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.CREATED_AT IS 'DBF-CAT-068 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.UPDATED_BY IS 'DBF-CAT-069 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.UPDATED_AT IS 'DBF-CAT-070 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_BUNDLE_COMP IS 'ENT-CAT-007 Bundle component / مكوّن الحزمة — one component Variant of a Bundle and its quantity';
COMMENT ON COLUMN CAT_BUNDLE_COMP.BUNDLE_COMPONENT_PK IS 'DBF-CAT-084 — Bundle component identifier (identity)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.QUANTITY IS 'DBF-CAT-085 — Component quantity above zero, 3 decimals (RULE-CAT-018)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.SORT_ORDER IS 'DBF-CAT-086 — Line order';
COMMENT ON COLUMN CAT_BUNDLE_COMP.IS_ACTIVE_FL IS 'DBF-CAT-087 — Active flag; removing a line deactivates it (RULE-CAT-048)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.BUNDLE_ITEM_FK IS 'DBF-CAT-088 — Bundle Item (kind BUNDLE)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.COMPONENT_VARIANT_FK IS 'DBF-CAT-089 — Component Variant of a STANDARD Item, once per Bundle (RULE-CAT-019)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.TENANT_ID IS 'DBF-CAT-090 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.VERSION IS 'DBF-CAT-091 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.CREATED_BY IS 'DBF-CAT-092 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_BUNDLE_COMP.CREATED_AT IS 'DBF-CAT-093 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_BUNDLE_COMP.UPDATED_BY IS 'DBF-CAT-094 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_BUNDLE_COMP.UPDATED_AT IS 'DBF-CAT-095 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_ITEM_UNIT IS 'ENT-CAT-008 Item unit / وحدة الصنف — extra unit of an Item with its factor to the base unit';
COMMENT ON COLUMN CAT_ITEM_UNIT.ITEM_UNIT_PK IS 'DBF-CAT-096 — Item unit identifier (identity)';
COMMENT ON COLUMN CAT_ITEM_UNIT.UOM_CODE IS 'DBF-CAT-097 — Unit of measure, lookup CAT_UOM code, once among active units (RULE-CAT-027)';
COMMENT ON COLUMN CAT_ITEM_UNIT.CONVERSION_FACTOR IS 'DBF-CAT-098 — Base units in one of this unit, above zero, fixed after saving (RULE-CAT-027, RULE-CAT-028)';
COMMENT ON COLUMN CAT_ITEM_UNIT.IS_PURCHASE_FL IS 'DBF-CAT-099 — Used for purchasing';
COMMENT ON COLUMN CAT_ITEM_UNIT.IS_SALES_FL IS 'DBF-CAT-100 — Used for selling';
COMMENT ON COLUMN CAT_ITEM_UNIT.IS_ACTIVE_FL IS 'DBF-CAT-101 — Active flag (RULE-CAT-048)';
COMMENT ON COLUMN CAT_ITEM_UNIT.ITEM_FK IS 'DBF-CAT-102 — Item the unit belongs to';
COMMENT ON COLUMN CAT_ITEM_UNIT.TENANT_ID IS 'DBF-CAT-103 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_ITEM_UNIT.VERSION IS 'DBF-CAT-104 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_ITEM_UNIT.CREATED_BY IS 'DBF-CAT-105 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_UNIT.CREATED_AT IS 'DBF-CAT-106 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_UNIT.UPDATED_BY IS 'DBF-CAT-107 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_UNIT.UPDATED_AT IS 'DBF-CAT-108 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_BARCODE IS 'ENT-CAT-009 Barcode / باركود — scannable code identifying a Variant in one unit';
COMMENT ON COLUMN CAT_BARCODE.BARCODE_PK IS 'DBF-CAT-109 — Barcode identifier (identity)';
COMMENT ON COLUMN CAT_BARCODE.BARCODE_VALUE IS 'DBF-CAT-110 — Barcode digits, unique among active barcodes, read-only (RULE-CAT-029 to RULE-CAT-033)';
COMMENT ON COLUMN CAT_BARCODE.BARCODE_TYPE_CODE IS 'DBF-CAT-111 — Barcode type, lookup CAT_BARCODE_TYPE code';
COMMENT ON COLUMN CAT_BARCODE.UOM_CODE IS 'DBF-CAT-112 — Unit the barcode identifies, lookup CAT_UOM code (RULE-CAT-031)';
COMMENT ON COLUMN CAT_BARCODE.IS_ACTIVE_FL IS 'DBF-CAT-113 — Active flag; inactive barcodes never match a lookup (RULE-CAT-048)';
COMMENT ON COLUMN CAT_BARCODE.VARIANT_FK IS 'DBF-CAT-114 — Variant the barcode identifies';
COMMENT ON COLUMN CAT_BARCODE.TENANT_ID IS 'DBF-CAT-115 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_BARCODE.VERSION IS 'DBF-CAT-116 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_BARCODE.CREATED_BY IS 'DBF-CAT-117 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_BARCODE.CREATED_AT IS 'DBF-CAT-118 — Creation time, filled by the platform; orders the earliest-created barcode for labels';
COMMENT ON COLUMN CAT_BARCODE.UPDATED_BY IS 'DBF-CAT-119 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_BARCODE.UPDATED_AT IS 'DBF-CAT-120 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_ITEM_MEDIA IS 'ENT-CAT-010 Item media / وسائط الصنف — one image of an Item; the file is kept by the given file module';
COMMENT ON COLUMN CAT_ITEM_MEDIA.ITEM_MEDIA_PK IS 'DBF-CAT-121 — Item media identifier (identity)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.FILE_DOCUMENT_REF IS 'DBF-CAT-122 — Given erp-core FILE_DOCUMENT identifier in public image storage; soft read, no constraint, no XM';
COMMENT ON COLUMN CAT_ITEM_MEDIA.NAME_AR IS 'DBF-CAT-123 — Arabic image description, default the Item NAME_AR (RULE-CAT-001)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.NAME_EN IS 'DBF-CAT-124 — English image description (RULE-CAT-002)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.SORT_ORDER IS 'DBF-CAT-125 — Image order for the Item';
COMMENT ON COLUMN CAT_ITEM_MEDIA.IS_PRIMARY_FL IS 'DBF-CAT-126 — Main image; at most one active per Item, exactly one kept by the service (RULE-CAT-039)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.IS_ACTIVE_FL IS 'DBF-CAT-127 — Active flag; removing an image deactivates it';
COMMENT ON COLUMN CAT_ITEM_MEDIA.ITEM_FK IS 'DBF-CAT-128 — Item the image belongs to';
COMMENT ON COLUMN CAT_ITEM_MEDIA.VARIANT_FK IS 'DBF-CAT-129 — Optional Variant of the same Item (RULE-CAT-040)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.TENANT_ID IS 'DBF-CAT-130 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.VERSION IS 'DBF-CAT-131 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.CREATED_BY IS 'DBF-CAT-132 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_MEDIA.CREATED_AT IS 'DBF-CAT-133 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_MEDIA.UPDATED_BY IS 'DBF-CAT-134 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_MEDIA.UPDATED_AT IS 'DBF-CAT-135 — Last update time, filled by the platform';

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 5 — CONSTRAINTS
-- ──────────────────────────────────────────────────────────────────

-- 5a — PRIMARY KEYS
ALTER TABLE CAT_CATEGORY           ADD CONSTRAINT PK_CAT_CATEGORY           PRIMARY KEY (CATEGORY_PK);
ALTER TABLE CAT_VAR_ATTR           ADD CONSTRAINT PK_CAT_VAR_ATTR           PRIMARY KEY (VARIANT_ATTRIBUTE_PK);
ALTER TABLE CAT_SCALE_LABEL_LAYOUT ADD CONSTRAINT PK_CAT_SCALE_LABEL_LAYOUT PRIMARY KEY (SCALE_LABEL_LAYOUT_PK);
ALTER TABLE CAT_UNIT_EINVOICE_CODE ADD CONSTRAINT PK_CAT_UNIT_EINVOICE_CODE PRIMARY KEY (UNIT_EINVOICE_CODE_PK);
ALTER TABLE CAT_ITEM               ADD CONSTRAINT PK_CAT_ITEM               PRIMARY KEY (ITEM_PK);
ALTER TABLE CAT_VAR_ATTR_VALUE     ADD CONSTRAINT PK_CAT_VAR_ATTR_VALUE     PRIMARY KEY (VARIANT_ATTRIBUTE_VALUE_PK);
ALTER TABLE CAT_VARIANT            ADD CONSTRAINT PK_CAT_VARIANT            PRIMARY KEY (VARIANT_PK);
ALTER TABLE CAT_VAR_ATTR_ASSIGN    ADD CONSTRAINT PK_CAT_VAR_ATTR_ASSIGN    PRIMARY KEY (VARIANT_ATTRIBUTE_ASSIGNMENT_PK);
ALTER TABLE CAT_BUNDLE_COMP        ADD CONSTRAINT PK_CAT_BUNDLE_COMP        PRIMARY KEY (BUNDLE_COMPONENT_PK);
ALTER TABLE CAT_ITEM_UNIT          ADD CONSTRAINT PK_CAT_ITEM_UNIT          PRIMARY KEY (ITEM_UNIT_PK);
ALTER TABLE CAT_BARCODE            ADD CONSTRAINT PK_CAT_BARCODE            PRIMARY KEY (BARCODE_PK);
ALTER TABLE CAT_ITEM_MEDIA         ADD CONSTRAINT PK_CAT_ITEM_MEDIA         PRIMARY KEY (ITEM_MEDIA_PK);

-- 5b — UNIQUE (composite with TENANT_ID per the given tenancy convention)
-- business keys (constraints)
ALTER TABLE CAT_CATEGORY       ADD CONSTRAINT UQ_CAT_CATEGORY_CODE       UNIQUE (TENANT_ID, CODE);                          -- RULE-CAT-024
ALTER TABLE CAT_VAR_ATTR       ADD CONSTRAINT UQ_CAT_VAR_ATTR_CODE       UNIQUE (TENANT_ID, CODE);                          -- RULE-CAT-016
ALTER TABLE CAT_ITEM           ADD CONSTRAINT UQ_CAT_ITEM_CODE           UNIQUE (TENANT_ID, CODE);                          -- RULE-CAT-004
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT UQ_CAT_VAR_ATTR_VALUE_CODE UNIQUE (TENANT_ID, VARIANT_ATTRIBUTE_FK, CODE);    -- RULE-CAT-016
ALTER TABLE CAT_VARIANT        ADD CONSTRAINT UQ_CAT_VARIANT_CODE        UNIQUE (TENANT_ID, CODE);                          -- RULE-CAT-004
-- targets of the same-parent composite FKs of 5d (RULE-CAT-013, RULE-CAT-040)
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT UQ_CAT_VAR_ATTR_VALUE_PK_ATTR UNIQUE (TENANT_ID, VARIANT_ATTRIBUTE_VALUE_PK, VARIANT_ATTRIBUTE_FK);
ALTER TABLE CAT_VARIANT        ADD CONSTRAINT UQ_CAT_VARIANT_PK_ITEM        UNIQUE (TENANT_ID, VARIANT_PK, ITEM_FK);
-- uniqueness among active records (partial unique indexes)
CREATE UNIQUE INDEX UQ_CAT_CATEGORY_NAME_AR       ON CAT_CATEGORY (TENANT_ID, PARENT_CATEGORY_FK, NAME_AR) NULLS NOT DISTINCT WHERE IS_ACTIVE_FL;  -- RULE-CAT-024
CREATE UNIQUE INDEX UQ_CAT_VAR_ATTR_NAME_AR       ON CAT_VAR_ATTR (TENANT_ID, NAME_AR) WHERE IS_ACTIVE_FL;                                      -- RULE-CAT-016
CREATE UNIQUE INDEX UQ_CAT_SCALE_LABEL_LAYOUT_KEY ON CAT_SCALE_LABEL_LAYOUT (TENANT_ID, KEY) WHERE IS_ACTIVE_FL;                                 -- RULE-CAT-034
CREATE UNIQUE INDEX UQ_CAT_UNIT_EINVOICE_CODE_KEY ON CAT_UNIT_EINVOICE_CODE (TENANT_ID, KEY) WHERE IS_ACTIVE_FL;                                 -- RULE-CAT-043
CREATE UNIQUE INDEX UQ_CAT_ITEM_NAME_AR           ON CAT_ITEM (TENANT_ID, regexp_replace(btrim(NAME_AR), '\s+', ' ', 'g')) WHERE IS_ACTIVE_FL;  -- RULE-CAT-003
CREATE UNIQUE INDEX UQ_CAT_VAR_ATTR_VALUE_NAME_AR ON CAT_VAR_ATTR_VALUE (TENANT_ID, VARIANT_ATTRIBUTE_FK, NAME_AR) WHERE IS_ACTIVE_FL;          -- RULE-CAT-016
CREATE UNIQUE INDEX UQ_CAT_VARIANT_DEFAULT        ON CAT_VARIANT (TENANT_ID, ITEM_FK) WHERE IS_DEFAULT_FL;                                      -- REQ-CAT-025
CREATE UNIQUE INDEX UQ_CAT_VAR_ATTR_ASSIGN_ATTR   ON CAT_VAR_ATTR_ASSIGN (TENANT_ID, VARIANT_FK, VARIANT_ATTRIBUTE_FK) WHERE IS_ACTIVE_FL;     -- RULE-CAT-013
CREATE UNIQUE INDEX UQ_CAT_BUNDLE_COMP_VARIANT    ON CAT_BUNDLE_COMP (TENANT_ID, BUNDLE_ITEM_FK, COMPONENT_VARIANT_FK) WHERE IS_ACTIVE_FL;     -- RULE-CAT-019
CREATE UNIQUE INDEX UQ_CAT_ITEM_UNIT_UOM          ON CAT_ITEM_UNIT (TENANT_ID, ITEM_FK, UOM_CODE) WHERE IS_ACTIVE_FL;                           -- RULE-CAT-027
CREATE UNIQUE INDEX UQ_CAT_BARCODE_VALUE          ON CAT_BARCODE (TENANT_ID, BARCODE_VALUE) WHERE IS_ACTIVE_FL;                                 -- RULE-CAT-029
CREATE UNIQUE INDEX UQ_CAT_ITEM_MEDIA_PRIMARY     ON CAT_ITEM_MEDIA (TENANT_ID, ITEM_FK) WHERE IS_PRIMARY_FL AND IS_ACTIVE_FL;                  -- RULE-CAT-039

-- 5c — CHECK
ALTER TABLE CAT_CATEGORY ADD CONSTRAINT CHK_CAT_CATEGORY_CODE CHECK (CODE ~ '^[A-Za-z0-9-]{1,30}$');                                     -- RULE-CAT-024
ALTER TABLE CAT_CATEGORY ADD CONSTRAINT CHK_CAT_CATEGORY_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                                     -- RULE-CAT-001
ALTER TABLE CAT_CATEGORY ADD CONSTRAINT CHK_CAT_CATEGORY_PARENT_CATEGORY_FK CHECK (PARENT_CATEGORY_FK IS NULL OR PARENT_CATEGORY_FK <> CATEGORY_PK);  -- RULE-CAT-023
ALTER TABLE CAT_VAR_ATTR ADD CONSTRAINT CHK_CAT_VAR_ATTR_CODE CHECK (CODE ~ '^[A-Za-z0-9-]{1,30}$');                                     -- RULE-CAT-016
ALTER TABLE CAT_VAR_ATTR ADD CONSTRAINT CHK_CAT_VAR_ATTR_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                                     -- RULE-CAT-001
ALTER TABLE CAT_SCALE_LABEL_LAYOUT ADD CONSTRAINT CHK_CAT_SCALE_LABEL_LAYOUT_KEY CHECK (KEY ~ '^(02|2[0-9])$');                            -- RULE-CAT-034
ALTER TABLE CAT_SCALE_LABEL_LAYOUT ADD CONSTRAINT CHK_CAT_SCALE_LABEL_LAYOUT_SCALE_CODE_LENGTH CHECK (SCALE_CODE_LENGTH BETWEEN 4 AND 6);  -- RULE-CAT-034
ALTER TABLE CAT_SCALE_LABEL_LAYOUT ADD CONSTRAINT CHK_CAT_SCALE_LABEL_LAYOUT_VALUE_DECIMALS CHECK (VALUE_DECIMALS BETWEEN 0 AND 3);         -- RULE-CAT-034
ALTER TABLE CAT_ITEM ADD CONSTRAINT CHK_CAT_ITEM_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                                             -- RULE-CAT-001
ALTER TABLE CAT_ITEM ADD CONSTRAINT CHK_CAT_ITEM_EINVOICE_CODE CHECK ((EINVOICE_CODE_TYPE_CODE IS NULL) = (EINVOICE_CODE IS NULL));       -- RULE-CAT-042
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT CHK_CAT_VAR_ATTR_VALUE_CODE CHECK (CODE ~ '^[A-Za-z0-9-]{1,30}$');                         -- RULE-CAT-016
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT CHK_CAT_VAR_ATTR_VALUE_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                         -- RULE-CAT-001
ALTER TABLE CAT_VARIANT ADD CONSTRAINT CHK_CAT_VARIANT_EINVOICE_CODE CHECK ((EINVOICE_CODE_TYPE_CODE IS NULL) = (EINVOICE_CODE IS NULL));  -- RULE-CAT-042
ALTER TABLE CAT_BUNDLE_COMP ADD CONSTRAINT CHK_CAT_BUNDLE_COMP_QUANTITY CHECK (QUANTITY > 0);                                             -- RULE-CAT-018
ALTER TABLE CAT_ITEM_UNIT ADD CONSTRAINT CHK_CAT_ITEM_UNIT_CONVERSION_FACTOR CHECK (CONVERSION_FACTOR > 0);                               -- RULE-CAT-027
ALTER TABLE CAT_BARCODE ADD CONSTRAINT CHK_CAT_BARCODE_BARCODE_VALUE CHECK (BARCODE_VALUE ~ '^[0-9]+$');                                  -- RULE-CAT-030, RULE-CAT-033
ALTER TABLE CAT_ITEM_MEDIA ADD CONSTRAINT CHK_CAT_ITEM_MEDIA_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                                 -- RULE-CAT-001

-- 5d — INTRA-MODULE FOREIGN KEYS (parent PK / UNIQUE already in place)
ALTER TABLE CAT_CATEGORY ADD CONSTRAINT FK_CAT_CATEGORY_CAT_CATEGORY
    FOREIGN KEY (PARENT_CATEGORY_FK) REFERENCES CAT_CATEGORY (CATEGORY_PK);
ALTER TABLE CAT_ITEM ADD CONSTRAINT FK_CAT_ITEM_CAT_CATEGORY
    FOREIGN KEY (CATEGORY_FK) REFERENCES CAT_CATEGORY (CATEGORY_PK);
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT FK_CAT_VAR_ATTR_VALUE_CAT_VAR_ATTR
    FOREIGN KEY (VARIANT_ATTRIBUTE_FK) REFERENCES CAT_VAR_ATTR (VARIANT_ATTRIBUTE_PK);
ALTER TABLE CAT_VARIANT ADD CONSTRAINT FK_CAT_VARIANT_CAT_ITEM
    FOREIGN KEY (ITEM_FK) REFERENCES CAT_ITEM (ITEM_PK);
ALTER TABLE CAT_VAR_ATTR_ASSIGN ADD CONSTRAINT FK_CAT_VAR_ATTR_ASSIGN_CAT_VARIANT
    FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
ALTER TABLE CAT_VAR_ATTR_ASSIGN ADD CONSTRAINT FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR
    FOREIGN KEY (VARIANT_ATTRIBUTE_FK) REFERENCES CAT_VAR_ATTR (VARIANT_ATTRIBUTE_PK);
ALTER TABLE CAT_VAR_ATTR_ASSIGN ADD CONSTRAINT FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE
    FOREIGN KEY (TENANT_ID, VARIANT_ATTRIBUTE_VALUE_FK, VARIANT_ATTRIBUTE_FK)
    REFERENCES CAT_VAR_ATTR_VALUE (TENANT_ID, VARIANT_ATTRIBUTE_VALUE_PK, VARIANT_ATTRIBUTE_FK);
ALTER TABLE CAT_BUNDLE_COMP ADD CONSTRAINT FK_CAT_BUNDLE_COMP_CAT_ITEM
    FOREIGN KEY (BUNDLE_ITEM_FK) REFERENCES CAT_ITEM (ITEM_PK);
ALTER TABLE CAT_BUNDLE_COMP ADD CONSTRAINT FK_CAT_BUNDLE_COMP_CAT_VARIANT
    FOREIGN KEY (COMPONENT_VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
ALTER TABLE CAT_ITEM_UNIT ADD CONSTRAINT FK_CAT_ITEM_UNIT_CAT_ITEM
    FOREIGN KEY (ITEM_FK) REFERENCES CAT_ITEM (ITEM_PK);
ALTER TABLE CAT_BARCODE ADD CONSTRAINT FK_CAT_BARCODE_CAT_VARIANT
    FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
ALTER TABLE CAT_ITEM_MEDIA ADD CONSTRAINT FK_CAT_ITEM_MEDIA_CAT_ITEM
    FOREIGN KEY (ITEM_FK) REFERENCES CAT_ITEM (ITEM_PK);
ALTER TABLE CAT_ITEM_MEDIA ADD CONSTRAINT FK_CAT_ITEM_MEDIA_CAT_VARIANT
    FOREIGN KEY (TENANT_ID, VARIANT_FK, ITEM_FK) REFERENCES CAT_VARIANT (TENANT_ID, VARIANT_PK, ITEM_FK);

-- TENANT_ID is column-only: the tenant table belongs to the given erp-core (ADR-CAT-054)


-- ──────────────────────────────────────────────────────────────────
-- BLOCK 6 — TRIGGERS
-- none: no SRS RULE requires a trigger; audit columns are filled by the platform; no PK trigger.
-- ──────────────────────────────────────────────────────────────────

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 7 — INDEXES (non-PK)
-- ──────────────────────────────────────────────────────────────────

-- tenant-leading index (given tenancy convention) where no non-partial UNIQUE already leads with TENANT_ID
CREATE INDEX IDX_CAT_ITEM_TENANT_ID                ON CAT_ITEM (TENANT_ID);
CREATE INDEX IDX_CAT_CATEGORY_TENANT_ID            ON CAT_CATEGORY (TENANT_ID);
CREATE INDEX IDX_CAT_VAR_ATTR_ASSIGN_TENANT_ID    ON CAT_VAR_ATTR_ASSIGN (TENANT_ID);
CREATE INDEX IDX_CAT_BUNDLE_COMP_TENANT_ID        ON CAT_BUNDLE_COMP (TENANT_ID);
CREATE INDEX IDX_CAT_ITEM_UNIT_TENANT_ID          ON CAT_ITEM_UNIT (TENANT_ID);
CREATE INDEX IDX_CAT_BARCODE_TENANT_ID            ON CAT_BARCODE (TENANT_ID);
CREATE INDEX IDX_CAT_ITEM_MEDIA_TENANT_ID         ON CAT_ITEM_MEDIA (TENANT_ID);
CREATE INDEX IDX_CAT_SCALE_LABEL_LAYOUT_TENANT_ID ON CAT_SCALE_LABEL_LAYOUT (TENANT_ID);
CREATE INDEX IDX_CAT_UNIT_EINVOICE_CODE_TENANT_ID ON CAT_UNIT_EINVOICE_CODE (TENANT_ID);

-- every FK column
CREATE INDEX IDX_CAT_CATEGORY_PARENT_CATEGORY_FK          ON CAT_CATEGORY (PARENT_CATEGORY_FK);
CREATE INDEX IDX_CAT_ITEM_CATEGORY_FK                     ON CAT_ITEM (CATEGORY_FK);
CREATE INDEX IDX_CAT_VAR_ATTR_VALUE_VARIANT_ATTRIBUTE_FK  ON CAT_VAR_ATTR_VALUE (VARIANT_ATTRIBUTE_FK);
CREATE INDEX IDX_CAT_VARIANT_ITEM_FK                      ON CAT_VARIANT (ITEM_FK);
CREATE INDEX IDX_CAT_VAR_ATTR_ASSIGN_VARIANT_FK           ON CAT_VAR_ATTR_ASSIGN (VARIANT_FK);
CREATE INDEX IDX_CAT_VAR_ATTR_ASSIGN_VARIANT_ATTRIBUTE_FK ON CAT_VAR_ATTR_ASSIGN (VARIANT_ATTRIBUTE_FK);
CREATE INDEX IDX_CAT_VAR_ATTR_ASSIGN_VARIANT_ATTRIBUTE_VALUE_FK ON CAT_VAR_ATTR_ASSIGN (VARIANT_ATTRIBUTE_VALUE_FK);
CREATE INDEX IDX_CAT_BUNDLE_COMP_BUNDLE_ITEM_FK           ON CAT_BUNDLE_COMP (BUNDLE_ITEM_FK);
CREATE INDEX IDX_CAT_BUNDLE_COMP_COMPONENT_VARIANT_FK     ON CAT_BUNDLE_COMP (COMPONENT_VARIANT_FK);
CREATE INDEX IDX_CAT_ITEM_UNIT_ITEM_FK                    ON CAT_ITEM_UNIT (ITEM_FK);
CREATE INDEX IDX_CAT_BARCODE_VARIANT_FK                   ON CAT_BARCODE (VARIANT_FK);
CREATE INDEX IDX_CAT_ITEM_MEDIA_ITEM_FK                   ON CAT_ITEM_MEDIA (ITEM_FK);
CREATE INDEX IDX_CAT_ITEM_MEDIA_VARIANT_FK                ON CAT_ITEM_MEDIA (VARIANT_FK);

-- search / list filters (SRS PART B B2); codes and active barcodes are covered by the UNIQUE indexes of 5b
CREATE INDEX IDX_CAT_ITEM_NAME_AR            ON CAT_ITEM (NAME_AR);
CREATE INDEX IDX_CAT_ITEM_NAME_EN            ON CAT_ITEM (NAME_EN);
CREATE INDEX IDX_CAT_ITEM_BRAND_CODE         ON CAT_ITEM (BRAND_CODE);
CREATE INDEX IDX_CAT_ITEM_ITEM_KIND_CODE     ON CAT_ITEM (ITEM_KIND_CODE);
CREATE INDEX IDX_CAT_ITEM_PUBLISH_STATE_CODE ON CAT_ITEM (PUBLISH_STATE_CODE);
CREATE INDEX IDX_CAT_ITEM_IS_ACTIVE_FL       ON CAT_ITEM (IS_ACTIVE_FL);
CREATE INDEX IDX_CAT_CATEGORY_NAME_AR        ON CAT_CATEGORY (NAME_AR);
CREATE INDEX IDX_CAT_CATEGORY_NAME_EN        ON CAT_CATEGORY (NAME_EN);
CREATE INDEX IDX_CAT_CATEGORY_IS_ACTIVE_FL   ON CAT_CATEGORY (IS_ACTIVE_FL);
CREATE INDEX IDX_CAT_VAR_ATTR_NAME_AR        ON CAT_VAR_ATTR (NAME_AR);
CREATE INDEX IDX_CAT_VAR_ATTR_IS_ACTIVE_FL   ON CAT_VAR_ATTR (IS_ACTIVE_FL);

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 8 — LOOKUP AND REFERENCE SEED DATA (PLATFORM tenant, TENANT_ID = 1;
-- new tenants receive copies through the given provisioning contributors)
-- ──────────────────────────────────────────────────────────────────

-- 8a — lookup types. Table owned by erp-core modules.mdl (MDL_LOOKUP_TYPE); the keys are owned by CAT (SRS A6).
INSERT INTO MDL_LOOKUP_TYPE (ID, TENANT_ID, KEY, OWNER_MODULE_CODE, CREATED_BY)
VALUES
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_ITEM_KIND', 'CAT', 'system'),
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_PUBLISH_STATE', 'CAT', 'system'),
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_BRAND', 'CAT', 'system'),
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_UOM', 'CAT', 'system'),
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_TAX_CLASS', 'CAT', 'system'),
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_EINVOICE_ITEM_CODE_TYPE', 'CAT', 'system'),
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_EINVOICE_UNIT', 'CAT', 'system'),
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_BARCODE_TYPE', 'CAT', 'system'),
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_SCALE_VALUE_TYPE', 'CAT', 'system'),
    (nextval('SEQ_MDL_LOOKUP_TYPE'), 1, 'CAT_CHANGE_KIND', 'CAT', 'system');

-- 8b — lookup values. Table owned by erp-core modules.mdl (MDL_LOOKUP_VALUE); the values are owned by CAT (SRS A6).
-- CAT_BRAND has no seeded value (the tenant builds its own list).
INSERT INTO MDL_LOOKUP_VALUE (ID, TENANT_ID, LOOKUP_TYPE_ID, CODE, LABEL_AR, LABEL_EN, SORT_ORDER, IS_ACTIVE_FL, CREATED_BY)
SELECT nextval('SEQ_MDL_LOOKUP_VALUE'), 1, t.ID, v.CODE, v.LABEL_AR, v.LABEL_EN, v.SORT_ORDER, TRUE, 'system'
FROM (VALUES
    ('CAT_ITEM_KIND', 'STANDARD', 'صنف عادي', 'Standard item', 1),
    ('CAT_ITEM_KIND', 'BUNDLE', 'حزمة', 'Bundle', 2),
    ('CAT_PUBLISH_STATE', 'UNPUBLISHED', 'غير منشور', 'Not published', 1),
    ('CAT_PUBLISH_STATE', 'PUBLISHED', 'منشور', 'Published', 2),
    ('CAT_UOM', 'PIECE', 'قطعة', 'Piece', 1),
    ('CAT_UOM', 'CARTON', 'كرتونة', 'Carton', 2),
    ('CAT_UOM', 'BOX', 'علبة', 'Box', 3),
    ('CAT_UOM', 'DOZEN', 'دستة', 'Dozen', 4),
    ('CAT_UOM', 'KILOGRAM', 'كيلوجرام', 'Kilogram', 5),
    ('CAT_UOM', 'GRAM', 'جرام', 'Gram', 6),
    ('CAT_UOM', 'METRE', 'متر', 'Metre', 7),
    ('CAT_UOM', 'LITRE', 'لتر', 'Litre', 8),
    ('CAT_TAX_CLASS', 'STANDARD', 'خاضع للنسبة العامة', 'Standard rate', 1),
    ('CAT_TAX_CLASS', 'ZERO_RATED', 'خاضع لنسبة صفر', 'Zero-rated', 2),
    ('CAT_TAX_CLASS', 'EXEMPT', 'معفى', 'Exempt', 3),
    ('CAT_EINVOICE_ITEM_CODE_TYPE', 'GS1', 'GS1', 'GS1', 1),
    ('CAT_EINVOICE_ITEM_CODE_TYPE', 'EGS', 'EGS', 'EGS', 2),
    ('CAT_EINVOICE_UNIT', 'EA', 'وحدة', 'Each', 1),
    ('CAT_EINVOICE_UNIT', 'KGM', 'كيلوجرام', 'Kilogram', 2),
    ('CAT_EINVOICE_UNIT', 'GRM', 'جرام', 'Gram', 3),
    ('CAT_EINVOICE_UNIT', 'MTR', 'متر', 'Metre', 4),
    ('CAT_EINVOICE_UNIT', 'LTR', 'لتر', 'Litre', 5),
    ('CAT_EINVOICE_UNIT', 'CT', 'كرتونة', 'Carton', 6),
    ('CAT_EINVOICE_UNIT', 'BX', 'علبة', 'Box', 7),
    ('CAT_EINVOICE_UNIT', 'DZN', 'دستة', 'Dozen', 8),
    ('CAT_BARCODE_TYPE', 'GTIN', 'GTIN', 'GTIN', 1),
    ('CAT_BARCODE_TYPE', 'INTERNAL', 'داخلي', 'Internal', 2),
    ('CAT_BARCODE_TYPE', 'SCALE', 'ميزان', 'Scale', 3),
    ('CAT_SCALE_VALUE_TYPE', 'WEIGHT', 'وزن', 'Weight', 1),
    ('CAT_SCALE_VALUE_TYPE', 'PRICE', 'سعر', 'Price', 2),
    ('CAT_CHANGE_KIND', 'CREATED', 'إنشاء', 'Created', 1),
    ('CAT_CHANGE_KIND', 'UPDATED', 'تعديل', 'Updated', 2),
    ('CAT_CHANGE_KIND', 'DEACTIVATED', 'إيقاف', 'Deactivated', 3),
    ('CAT_CHANGE_KIND', 'REACTIVATED', 'إعادة تفعيل', 'Reactivated', 4)
) AS v (TYPE_KEY, CODE, LABEL_AR, LABEL_EN, SORT_ORDER)
JOIN MDL_LOOKUP_TYPE t ON t.TENANT_ID = 1 AND t.KEY = v.TYPE_KEY;

-- 8c — number series (SRS D-11). Table owned by erp-core modules.sequence (CORE_NUMBER_SERIES); the series codes are owned by CAT.
INSERT INTO CORE_NUMBER_SERIES (ID, TENANT_ID, CODE, PREFIX, PATTERN, RESET_POLICY, CREATED_BY)
VALUES
    (nextval('SEQ_CORE_NUMBER_SERIES'), 1, 'CAT_ITEM', 'ITM', '{PREFIX}-{SEQ:6}', 'NEVER', 'system'),
    (nextval('SEQ_CORE_NUMBER_SERIES'), 1, 'CAT_VARIANT', 'VAR', '{PREFIX}-{SEQ:6}', 'NEVER', 'system'),
    (nextval('SEQ_CORE_NUMBER_SERIES'), 1, 'CAT_INTERNAL_BARCODE', '04', '{SEQ:10}', 'NEVER', 'system');

-- 8d — Unit e-invoice codes (SRS D-12: CAT_UOM → CAT_EINVOICE_UNIT). Table created by this script.
INSERT INTO CAT_UNIT_EINVOICE_CODE (KEY, VALUE_AR, VALUE_EN, EINVOICE_UNIT_CODE, IS_ACTIVE_FL, TENANT_ID, CREATED_BY, CREATED_AT)
VALUES
    ('PIECE', 'قطعة', 'Piece', 'EA', TRUE, 1, 'system', now()),
    ('CARTON', 'كرتونة', 'Carton', 'CT', TRUE, 1, 'system', now()),
    ('BOX', 'علبة', 'Box', 'BX', TRUE, 1, 'system', now()),
    ('DOZEN', 'دستة', 'Dozen', 'DZN', TRUE, 1, 'system', now()),
    ('KILOGRAM', 'كيلوجرام', 'Kilogram', 'KGM', TRUE, 1, 'system', now()),
    ('GRAM', 'جرام', 'Gram', 'GRM', TRUE, 1, 'system', now()),
    ('METRE', 'متر', 'Metre', 'MTR', TRUE, 1, 'system', now()),
    ('LITRE', 'لتر', 'Litre', 'LTR', TRUE, 1, 'system', now());

COMMIT;

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 9 — VIEWS
-- none: no SRS requirement asks for a view; derived Variant names are composed at read by the service.
-- ──────────────────────────────────────────────────────────────────

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 10 — FUNCTIONS / PROCEDURES
-- none: no SRS requirement asks for one.
-- ──────────────────────────────────────────────────────────────────
```

### Self-verification (§7.2)

| Check | Result |
|---|---|
| SYNTAX S-1 … S-8 | Every statement ends with `;`. No trailing comma. Every CAT object referenced is created above. The given tables of BLOCK 8 (MDL_LOOKUP_TYPE, MDL_LOOKUP_VALUE, CORE_NUMBER_SERIES) are created by the given's own migration chain, which runs before application scripts. Types come only from §4.1, plus INTEGER declared in the header. All constraints are in ALTER TABLE form. No PK trigger, no PK sequence default. INSERTs carry column lists, with COMMIT after the DML. No schema prefix. No placeholders |
| PK strategy | 12 of 12 PK columns use `GENERATED ALWAYS AS IDENTITY`. No PK sequence |
| Order | Parent tables come before child tables. Every PK is added before any UNIQUE or FK, and the UNIQUE targets of the composite FKs are added before those FKs. The lookup types are inserted before the lookup values. One COMMIT closes BLOCK 8 |
| Completeness | 12 of 12 ENT have a table, and every stored SRS field has a DBF. The two derived Variant names are not stored (D-P2-08). All 10 lookup keys are seeded (34 values). The shared lookup tables are not re-created |
| Cross-module | No XM, so no cross-module column. No constraint references another module's table, live or commented. The given references (TENANT_ID, FILE_DOCUMENT_REF) are column-only |
| Trace | Every DBF traces to its ENT.field and to at least 1 REQ. No XM exists, so no orphan XM and no dangling id |

## 5 — Decisions applied

| DEFAULT | What | Source | Override |
|---|---|---|---|
| D-P2-01 | Identifier transformation: camelCase → UPPER_SNAKE_CASE, unquoted. PostgreSQL stores the name in lower case, which matches the given's physical naming. Tables are named `CAT_` + entity abbreviation (VAR_ATTR = Variant attribute, COMP = component) | brief §3; given migrations convention | Rename in a later delta (breaking for consumers of physical names) |
| D-P2-02 | Every table carries the given entity column set: TENANT_ID, VERSION, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT. This includes the lookup tables (ENT-CAT-003, 004) and config tables (ENT-CAT-011, 012), whose SRS kind lists no audit fields. The source is the given's tenancy rule (every table has `TENANT_ID BIGINT NOT NULL`; every entity extends `AuditableEntity`). SRS A2 says "Tenancy is handled by the given core", and SRS A3 says audit fields are filled by the system. Without these columns the given's base entity cannot map the table | SRS A2, A3; given erp-core 1.6.0 conventions (tenancy, migrations) | Drop the columns from the lookup and config tables only if the given allows an entity that does not extend `AuditableEntity` |
| D-P2-03 | Every table has a `TENANT_ID` NOT NULL column, an index led by `TENANT_ID` (a non-partial UNIQUE of 5b or an `IDX_<TABLE>_TENANT_ID` of BLOCK 7), and every UNIQUE composite with `TENANT_ID`. The tenant table is the given erp-core `CORE_TENANT`; the column is column-only and no FK constraint to it is created here, live or commented (C6.15; ADR-CAT-054). The given's tenancy layer enforces tenant validity | brief §5.1; ADR-CAT-054 | — |
| D-P2-04 | The PK is `{entity}Pk` with the identity clause. This replaces the given's `ID` + `SEQ_<TABLE>` default, because the profile decision governs and one database carries one PK strategy | profile `stack.db.pk_generation`, `naming.pk_pattern`; brief §4 | Profile change only |
| D-P2-05 | Lookup-backed columns are `VARCHAR(50)` and hold the code. They carry no CHECK of allowed codes and no DB default. The service applies the SRS defaults (STANDARD, PIECE, STANDARD, UNPUBLISHED — D-09) and validates against the active values in `mdl` (RULE-CAT-006) | profile lookups rule; given lookups convention | — |
| D-P2-06 | Logical `number` → `INTEGER`, declared once in the header. Decimal precision: `quantity` NUMERIC(18,3) and `conversionFactor` NUMERIC(18,6). The SRS fixes the scale only; 15 and 12 integer digits are enough for any retail quantity | SRS A3 (3 and 6 decimals) | Widen the precision in a later delta (non-breaking) |
| D-P2-07 | `createdBy` and `updatedBy` are `VARCHAR(100)` principal strings. The created pair is NOT NULL and the updated pair is NULL until the first update | brief §3 audit rule; given migrations convention | — |
| D-P2-08 | Variant `nameAr` and `nameEn` are derived at read (SRS D-15) and are not stored. A stored copy would go stale when a value is renamed (AC-CAT-048) | SRS ENT-CAT-002, D-15 | Store an owner override name if D-15 changes |
| D-P2-09 | Unit e-invoice code `valueAr` and `valueEn` are stored (config-kind defaults) and nullable. The service copies them from the CAT_UOM labels at save; they are never entered | SRS ENT-CAT-012; profile entity_defaults.config | Derive at read and drop the columns in a later delta |
| D-P2-10 | Rules that apply among active records are partial unique indexes named `UQ_…`. The Item Arabic name is compared without leading, trailing or repeated spaces (RULE-CAT-003). Rows from the same import file are checked by the service | SRS RULE-CAT-003, 016, 019, 024, 027, 029, 034, 039, 043 | — |
| D-P2-11 | Barcode uniqueness is one partial unique index on the value among active barcodes. A SCALE code has 4–6 digits (RULE-CAT-033, RULE-CAT-034); a GTIN has 8–14 and an INTERNAL code has 13. So a SCALE code can never equal a GTIN or INTERNAL value, and RULE-CAT-029's per-type comparison holds without naming a lookup code in DDL | SRS RULE-CAT-029, 030, 033 | — |
| D-P2-12 | Same-parent integrity is structural. A composite FK makes an assignment's value belong to its attribute (RULE-CAT-013). Another makes a media record's Variant belong to its Item (RULE-CAT-040; no check when the Variant is empty). Both FKs include TENANT_ID | SRS RULE-CAT-013, RULE-CAT-040 | — |
| D-P2-13 | Lookup seeds go into the given's `mdl` tables. The given documents `KEY`, `OWNER_MODULE_CODE`, `TENANT_ID = 1` (PLATFORM) and the `SEQ_MDL_LOOKUP_*` id sequences. The value columns (`CODE`, `LABEL_AR`, `LABEL_EN`, `SORT_ORDER`) follow the given's `LookupOptionView`, and the type link is `LOOKUP_TYPE_ID`. Before P3.1 applies BLOCK 8, it must check these value-table column names against the given's `mdl` migration. No CAT table or column depends on them | brief §4.2 (the key owner owns the seed); given lookups convention | Adjust the column names only; keys and values are fixed by SRS A6 |
| D-P2-14 | The number series CAT_ITEM (ITM-000001), CAT_VARIANT (VAR-000001) and CAT_INTERNAL_BARCODE (10 digits) are seeded in `CORE_NUMBER_SERIES` with RESET_POLICY NEVER, so the first save does not fail with `SEQUENCE_NOT_CONFIGURED` | SRS D-11; given numbering convention | Patterns are configurable per tenant in the core sequence module |
| D-P2-15 | The 8 Unit e-invoice codes of D-12 are seeded for the PLATFORM tenant (AC-CAT-097 lists 8 active ones) | SRS D-12, ENT-CAT-012 | Re-map against the authority's code table |
| D-P2-16 | These rules stay with the service and are not DDL, because they need other rows, flags, files or sequences: RULE-CAT-004, 005, 006, 007, 008, 009, 010, 011, 012, 013 (unique combination), 014, 015, 017, 018 (at least one line), 019 (active, STANDARD), 020, 021, 022, 023 (active parent, no cycle, depth ≤ 5), 025, 026, 027 (differs from base unit), 028, 030 (check digit), 031, 032, 033 (length), 035, 036, 037, 038, 039 (exactly one), 040 (when the Variant is empty), 041, 042 (GS1 / EGS format), 044, 045, 046, 047, 048, 049 | SRS A5 | — |
| D-P2-17 | Profile check CM-8: CAT holds no stock quantity, no monetary amount and no price (SRS A2 out of scope: PRC, INV). No warehouse, currency or price-list dimension applies to a CAT row | SRS A2; profile CM-8 | — |
| D-P2-18 | Delta: the current state holds no earlier CAT db-script, so this v2 migration creates every object. The DBF sequence starts at 001 (next free 162). No XM exists (next free XM sequence 001) | brief delta rule; change-manifest CS-CAT-001 | — |

No ADR was raised and no question was asked. The next free ADR sequence for CAT is unchanged (044). Profile check CM-3 holds: every flag column (`IS_ACTIVE_FL`, `IS_DEFAULT_FL`, `IS_PURCHASE_FL`, `IS_SALES_FL`, `IS_PRIMARY_FL`) ends with the flag suffix `Fl` under the transformation of D-P2-01.

## 6 — Registry content

See `registry-db-cat.md` (same content). Event: "P2 completed: CAT v2 — 12 tables, 161 DBF, 0 XM".

