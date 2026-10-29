# DB SCRIPT — Party / الأطراف (PTY)
══════════════════════════════════════════════════════════════════
Module : PTY   Version : v1   Profile : commerce   Stage : P2 (Database) · pass 1
Inputs : srs (analysis/modules/PTY/P1/srs-pty.md, P1 commit f0653ef), registry-srs, contract (contract-pty.md, P1.5 commit 7dc362c), contracts (contract-cat.md — read, no PTY dependency on it)
Counts : tables 5 · DBF 67 (DBF-PTY-001 … DBF-PTY-067) · XM 0 · intra-module FK 4 · lookups seeded 5 keys / 16 values · ADR 5 (ADR-PTY-018 … ADR-PTY-022)
Status : DRAFT — P2 pass 1 · no question raised · 0 BLOCKED
══════════════════════════════════════════════════════════════════

## 1 — Header

| Item | Value |
|---|---|
| Module | Party / الأطراف — PTY |
| Version | v1 (first version — the script is a full CREATE, not a migration) |
| Date | 2026-10-29 |
| Dialect | postgresql16 (profile.stack.db.target_dialect) — the only dialect emitted |
| Schema prefix | none — every object is unqualified (S-7); the given `erp-core` resolves the schema / tenant (ADR-PTY-021) |
| PK generation | `identity` (profile.stack.db.pk_generation) — every PK column carries `GENERATED ALWAYS AS IDENTITY`; no PK sequence, no PK trigger |
| Identifier transformation | logical camelCase field name (SRS A3) → physical UPPER_SNAKE_CASE column: an underscore is inserted before every upper-case letter and the whole name is upper-cased; digits stay attached to the preceding word (`partyPk` → `PARTY_PK`, `taxRegistrationNo` → `TAX_REGISTRATION_NO`, `isActiveFl` → `IS_ACTIVE_FL`, `line1` → `LINE1`). Identifiers are written unquoted, so PostgreSQL stores them folded to lower case (`pty_party.party_pk`) — one spelling per field, no quoted identifier anywhere. No identifier exceeds the 63-byte limit; none is a reserved word. |
| Table names | `PTY_` + entity name in UPPER_SNAKE_CASE: PTY_PARTY, PTY_CUSTOMER, PTY_SUPPLIER, PTY_ADDRESS, PTY_CREDIT_OVERRIDE_REQUEST |
| Type declared outside §4.1 | logical `number` (whole number — ENT-PTY-002.paymentTermDays, ENT-PTY-003.paymentTermDays, 0 – 365) → `INTEGER` (the syntax map has no whole-number type; ADR-PTY-018) |

### 1.1 Entry check

```
  srs             : ✓ present (PTY v1, P1 — REQ 109 · AC 123 · ENT 5 · RULE 33)
  registry-srs    : ✓ present
  contract        : ✓ present (CON-PTY-001 … CON-PTY-024; identifier type BIGINT for every entity)
  contracts?      : ✓ present (contract-cat.md — PTY consumes nothing from CAT; SRS A8 `consumes: []`)
  domain-profile STEERING : vocabulary verbatim; identifier rules (BIGINT identity keys; LOV codes from given mdl)
  project-registry        : no name fixed for PTY tables by another module; given tables listed in engine §4 are not re-created
  Extracted               : 5 entities → 5 tables · 4 intra-module FKs ·
                            0 XM candidates (SRS A8 `consumes: []`) · 5 lookups (SRS A6) → 16 seed values
```

Given entities used and not re-created [C:C-GIVEN-1]: User (`SEC_USER`, `erp-core:modules.sec`), AppConfiguration (`CU_APP_CONFIGURATION`, `erp-core:modules.cu` — holds the tenant credit-overrun setting), LookupType / LookupValue (`MDL_LOOKUP_TYPE`, `MDL_LOOKUP_VALUE`, `erp-core:modules.mdl` — seeded only, BLOCK 8), NumberSeries (`CORE_NUMBER_SERIES`), AuditEvent (`CORE_AUDIT_EVENT`), Tenant (`CORE_TENANT`).

---

## 2 — DB field traceability matrix (`DBF`)

The single canonical source of DBF → column → type → SRS origin. Downstream artifacts reference columns by DBF id only. Order per table: PK, the entity's own columns in SRS order, FK columns, standard columns of the entity kind (profile.conventions.entity_defaults), audit columns last. Audit columns (createdBy, createdAt, updatedBy, updatedAt) and the standard master / transactional columns are mandated by `profile: entity_defaults.master` / `profile: entity_defaults.transactional`; their `traces` name the REQs that create or update the row.

```yaml name=dbf-matrix
rows:
  - {id: DBF-PTY-001, table: PTY_PARTY, column: PARTY_PK, type: "BIGINT", entity_field: ENT-PTY-001.partyPk, traces: [REQ-PTY-001, REQ-PTY-024], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-PTY-002, table: PTY_PARTY, column: MOBILE, type: "VARCHAR(20)", entity_field: ENT-PTY-001.mobile, traces: [REQ-PTY-011, REQ-PTY-013, REQ-PTY-056], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-003, table: PTY_PARTY, column: EMAIL, type: "VARCHAR(254)", entity_field: ENT-PTY-001.email, traces: [REQ-PTY-010, REQ-PTY-011, REQ-PTY-055], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-004, table: PTY_PARTY, column: TAX_REGISTRATION_NO, type: "VARCHAR(30)", entity_field: ENT-PTY-001.taxRegistrationNo, traces: [REQ-PTY-002, REQ-PTY-013, REQ-PTY-017], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-005, table: PTY_PARTY, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-PTY-001.nameAr, traces: [REQ-PTY-004, REQ-PTY-009, REQ-PTY-042], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-006, table: PTY_PARTY, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-PTY-001.nameEn, traces: [REQ-PTY-004, REQ-PTY-009], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-007, table: PTY_PARTY, column: CODE, type: "VARCHAR(20)", entity_field: ENT-PTY-001.code, traces: [REQ-PTY-006, REQ-PTY-012], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-008, table: PTY_PARTY, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-PTY-001.isActiveFl, traces: [REQ-PTY-028, REQ-PTY-029], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-PTY-009, table: PTY_PARTY, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-001.createdBy, traces: [REQ-PTY-004, REQ-PTY-016], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-010, table: PTY_PARTY, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-001.createdAt, traces: [REQ-PTY-004, REQ-PTY-016], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-011, table: PTY_PARTY, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-001.updatedBy, traces: [REQ-PTY-012, REQ-PTY-017], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-012, table: PTY_PARTY, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-001.updatedAt, traces: [REQ-PTY-012, REQ-PTY-017], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-013, table: PTY_CUSTOMER, column: CUSTOMER_PK, type: "BIGINT", entity_field: ENT-PTY-002.customerPk, traces: [REQ-PTY-001, REQ-PTY-002], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-PTY-014, table: PTY_CUSTOMER, column: CUSTOMER_GROUP_CODE, type: "VARCHAR(50)", entity_field: ENT-PTY-002.customerGroupCode, traces: [REQ-PTY-007, REQ-PTY-039, REQ-PTY-040], nullable: "NOT NULL", default: "'RETAIL'"}
  - {id: DBF-PTY-015, table: PTY_CUSTOMER, column: CREDIT_LIMIT, type: "NUMERIC(18,3)", entity_field: ENT-PTY-002.creditLimit, traces: [REQ-PTY-064, REQ-PTY-066, REQ-PTY-068], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-016, table: PTY_CUSTOMER, column: PAYMENT_TERM_DAYS, type: "INTEGER", entity_field: ENT-PTY-002.paymentTermDays, traces: [REQ-PTY-065, REQ-PTY-067], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-017, table: PTY_CUSTOMER, column: SEC_USER_ID, type: "BIGINT", entity_field: ENT-PTY-002.secUserId, traces: [REQ-PTY-052, REQ-PTY-054], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-018, table: PTY_CUSTOMER, column: IS_WALK_IN_FL, type: "BOOLEAN", entity_field: ENT-PTY-002.isWalkInFl, traces: [REQ-PTY-044, REQ-PTY-045, REQ-PTY-047], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-PTY-019, table: PTY_CUSTOMER, column: PARTY_FK, type: "BIGINT", entity_field: ENT-PTY-002.partyFk, traces: [REQ-PTY-004, REQ-PTY-025, REQ-PTY-026, REQ-PTY-027], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-020, table: PTY_CUSTOMER, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-PTY-002.isActiveFl, traces: [REQ-PTY-003, REQ-PTY-014, REQ-PTY-015], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-PTY-021, table: PTY_CUSTOMER, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-002.createdBy, traces: [REQ-PTY-004, REQ-PTY-026], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-022, table: PTY_CUSTOMER, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-002.createdAt, traces: [REQ-PTY-004, REQ-PTY-026], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-023, table: PTY_CUSTOMER, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-002.updatedBy, traces: [REQ-PTY-012, REQ-PTY-064], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-024, table: PTY_CUSTOMER, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-002.updatedAt, traces: [REQ-PTY-012, REQ-PTY-064], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-025, table: PTY_SUPPLIER, column: SUPPLIER_PK, type: "BIGINT", entity_field: ENT-PTY-003.supplierPk, traces: [REQ-PTY-016, REQ-PTY-019], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-PTY-026, table: PTY_SUPPLIER, column: PAYMENT_TERM_DAYS, type: "INTEGER", entity_field: ENT-PTY-003.paymentTermDays, traces: [REQ-PTY-017, REQ-PTY-022], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-027, table: PTY_SUPPLIER, column: PARTY_FK, type: "BIGINT", entity_field: ENT-PTY-003.partyFk, traces: [REQ-PTY-016, REQ-PTY-025, REQ-PTY-027], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-028, table: PTY_SUPPLIER, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-PTY-003.isActiveFl, traces: [REQ-PTY-003, REQ-PTY-020, REQ-PTY-021], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-PTY-029, table: PTY_SUPPLIER, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-003.createdBy, traces: [REQ-PTY-016, REQ-PTY-025], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-030, table: PTY_SUPPLIER, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-003.createdAt, traces: [REQ-PTY-016, REQ-PTY-025], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-031, table: PTY_SUPPLIER, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-003.updatedBy, traces: [REQ-PTY-017], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-032, table: PTY_SUPPLIER, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-003.updatedAt, traces: [REQ-PTY-017], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-033, table: PTY_ADDRESS, column: ADDRESS_PK, type: "BIGINT", entity_field: ENT-PTY-004.addressPk, traces: [REQ-PTY-030, REQ-PTY-032], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-PTY-034, table: PTY_ADDRESS, column: ADDRESS_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-PTY-004.addressTypeCode, traces: [REQ-PTY-030, REQ-PTY-038], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-035, table: PTY_ADDRESS, column: LINE1, type: "VARCHAR(200)", entity_field: ENT-PTY-004.line1, traces: [REQ-PTY-030, REQ-PTY-037], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-036, table: PTY_ADDRESS, column: LINE2, type: "VARCHAR(200)", entity_field: ENT-PTY-004.line2, traces: [REQ-PTY-031], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-037, table: PTY_ADDRESS, column: CITY, type: "VARCHAR(100)", entity_field: ENT-PTY-004.city, traces: [REQ-PTY-030, REQ-PTY-031], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-038, table: PTY_ADDRESS, column: REGION, type: "VARCHAR(100)", entity_field: ENT-PTY-004.region, traces: [REQ-PTY-031], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-039, table: PTY_ADDRESS, column: POSTAL_CODE, type: "VARCHAR(20)", entity_field: ENT-PTY-004.postalCode, traces: [REQ-PTY-031], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-040, table: PTY_ADDRESS, column: IS_DEFAULT_FL, type: "BOOLEAN", entity_field: ENT-PTY-004.isDefaultFl, traces: [REQ-PTY-034, REQ-PTY-035, REQ-PTY-036], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-PTY-041, table: PTY_ADDRESS, column: PARTY_FK, type: "BIGINT", entity_field: ENT-PTY-004.partyFk, traces: [REQ-PTY-030, REQ-PTY-032], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-042, table: PTY_ADDRESS, column: NAME_AR, type: "VARCHAR(100)", entity_field: ENT-PTY-004.nameAr, traces: [REQ-PTY-030, REQ-PTY-031], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-043, table: PTY_ADDRESS, column: NAME_EN, type: "VARCHAR(100)", entity_field: ENT-PTY-004.nameEn, traces: [REQ-PTY-030, REQ-PTY-031], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-044, table: PTY_ADDRESS, column: CODE, type: "VARCHAR(10)", entity_field: ENT-PTY-004.code, traces: [REQ-PTY-030], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-045, table: PTY_ADDRESS, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-PTY-004.isActiveFl, traces: [REQ-PTY-032, REQ-PTY-033], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-PTY-046, table: PTY_ADDRESS, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-004.createdBy, traces: [REQ-PTY-030], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-047, table: PTY_ADDRESS, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-004.createdAt, traces: [REQ-PTY-030], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-048, table: PTY_ADDRESS, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-004.updatedBy, traces: [REQ-PTY-031], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-049, table: PTY_ADDRESS, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-004.updatedAt, traces: [REQ-PTY-031, REQ-PTY-036], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-050, table: PTY_CREDIT_OVERRIDE_REQUEST, column: CREDIT_OVERRIDE_REQUEST_PK, type: "BIGINT", entity_field: ENT-PTY-005.creditOverrideRequestPk, traces: [REQ-PTY-091, REQ-PTY-103], nullable: "NOT NULL", default: "GENERATED ALWAYS AS IDENTITY"}
  - {id: DBF-PTY-051, table: PTY_CREDIT_OVERRIDE_REQUEST, column: SALE_AMOUNT, type: "NUMERIC(18,3)", entity_field: ENT-PTY-005.saleAmount, traces: [REQ-PTY-091, REQ-PTY-097, REQ-PTY-099], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-052, table: PTY_CREDIT_OVERRIDE_REQUEST, column: CREDIT_LIMIT_AT_REQUEST, type: "NUMERIC(18,3)", entity_field: ENT-PTY-005.creditLimitAtRequest, traces: [REQ-PTY-091], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-053, table: PTY_CREDIT_OVERRIDE_REQUEST, column: OPEN_EXPOSURE, type: "NUMERIC(18,3)", entity_field: ENT-PTY-005.openExposure, traces: [REQ-PTY-091, REQ-PTY-080], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-054, table: PTY_CREDIT_OVERRIDE_REQUEST, column: CREDIT_CHECK_RESULT_CODE, type: "VARCHAR(50)", entity_field: ENT-PTY-005.creditCheckResultCode, traces: [REQ-PTY-091, REQ-PTY-104], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-055, table: PTY_CREDIT_OVERRIDE_REQUEST, column: SOURCE_DOCUMENT_REF, type: "VARCHAR(60)", entity_field: ENT-PTY-005.sourceDocumentRef, traces: [REQ-PTY-091], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-056, table: PTY_CREDIT_OVERRIDE_REQUEST, column: DECIDED_BY, type: "BIGINT", entity_field: ENT-PTY-005.decidedBy, traces: [REQ-PTY-094, REQ-PTY-095, REQ-PTY-103], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-057, table: PTY_CREDIT_OVERRIDE_REQUEST, column: DECIDED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-005.decidedAt, traces: [REQ-PTY-094, REQ-PTY-095, REQ-PTY-103], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-058, table: PTY_CREDIT_OVERRIDE_REQUEST, column: DECISION_REASON, type: "VARCHAR(500)", entity_field: ENT-PTY-005.decisionReason, traces: [REQ-PTY-095, REQ-PTY-100], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-059, table: PTY_CREDIT_OVERRIDE_REQUEST, column: USED_DOCUMENT_REF, type: "VARCHAR(60)", entity_field: ENT-PTY-005.usedDocumentRef, traces: [REQ-PTY-097], nullable: "NULL", default: "—"}
  - {id: DBF-PTY-060, table: PTY_CREDIT_OVERRIDE_REQUEST, column: CUSTOMER_FK, type: "BIGINT", entity_field: ENT-PTY-005.customerFk, traces: [REQ-PTY-091, REQ-PTY-099, REQ-PTY-105], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-061, table: PTY_CREDIT_OVERRIDE_REQUEST, column: DOC_NO, type: "VARCHAR(20)", entity_field: ENT-PTY-005.docNo, traces: [REQ-PTY-092, REQ-PTY-101], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-062, table: PTY_CREDIT_OVERRIDE_REQUEST, column: DOC_DATE, type: "TIMESTAMPTZ", entity_field: ENT-PTY-005.docDate, traces: [REQ-PTY-092, REQ-PTY-101], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-063, table: PTY_CREDIT_OVERRIDE_REQUEST, column: STATUS_CODE, type: "VARCHAR(50)", entity_field: ENT-PTY-005.statusCode, traces: [REQ-PTY-091, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-097, REQ-PTY-098], nullable: "NOT NULL", default: "'PENDING'"}
  - {id: DBF-PTY-064, table: PTY_CREDIT_OVERRIDE_REQUEST, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-005.createdBy, traces: [REQ-PTY-091, REQ-PTY-096, REQ-PTY-102], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-065, table: PTY_CREDIT_OVERRIDE_REQUEST, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-005.createdAt, traces: [REQ-PTY-091], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-066, table: PTY_CREDIT_OVERRIDE_REQUEST, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-PTY-005.updatedBy, traces: [REQ-PTY-094, REQ-PTY-095, REQ-PTY-097], nullable: "NOT NULL", default: "—"}
  - {id: DBF-PTY-067, table: PTY_CREDIT_OVERRIDE_REQUEST, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-PTY-005.updatedAt, traces: [REQ-PTY-094, REQ-PTY-095, REQ-PTY-097], nullable: "NOT NULL", default: "—"}
```

Total: 67 DBF ids across 5 tables (PTY_PARTY 12 · PTY_CUSTOMER 12 · PTY_SUPPLIER 8 · PTY_ADDRESS 17 · PTY_CREDIT_OVERRIDE_REQUEST 18).

Fields of SRS A3 that are deliberately not columns: ENT-PTY-002.nameAr / nameEn / code and ENT-PTY-003.nameAr / nameEn / code — the SRS types them "presented from ENT-PTY-001", held once on the Party (ADR-PTY-014, REQ-PTY-024); the backend reads them through PARTY_FK (DBF-PTY-019, DBF-PTY-027) from DBF-PTY-005, DBF-PTY-006, DBF-PTY-007. A second copy would break the one-identity promise of CON-PTY-001.

Every RULE `Data source` field resolves to a column of this script [C:C6.9]: ENT-PTY-001.nameAr/nameEn/email/mobile/code/isActiveFl → DBF-PTY-005/006/003/002/007/008 · ENT-PTY-002.isActiveFl/paymentTermDays/partyFk/isWalkInFl/creditLimit/customerGroupCode/secUserId → DBF-PTY-020/016/019/018/015/014/017 · ENT-PTY-003.isActiveFl/paymentTermDays/partyFk → DBF-PTY-028/026/027 · ENT-PTY-004.line1/addressTypeCode/isDefaultFl/isActiveFl/partyFk/updatedAt → DBF-PTY-035/034/040/045/041/049 · ENT-PTY-005.saleAmount/openExposure/creditLimitAtRequest/creditCheckResultCode/statusCode/customerFk/decisionReason/createdBy/docNo → DBF-PTY-051/053/052/054/063/060/058/064/061. No RULE is DEFERRED.

---

## 3 — XM register (`XM`)

```yaml name=xm-register
records: []
```

No cross-module dependency exists: SRS A8 declares `consumes: []` — Party is DAG #4 with no business-module dependency and reaches only the given `erp-core` (SRS A9), which creates no XM edge. Consequently:

- no HARD-FK column, no SOFT-READ, no `constraint → XM-…` pointer in the script;
- the references to given entities — ENT-PTY-002.secUserId (DBF-PTY-017), ENT-PTY-005.decidedBy (DBF-PTY-056) and the audit user columns — are neither FK nor XM: the given is reached through its published interface (SRS A9: `SecCustomerInvitationApi`, `SecUserDirectoryApi`), never through a constraint on `SEC_USER` [C:C-GIVEN-1];
- ENT-PTY-005.sourceDocumentRef / usedDocumentRef hold the consumer's sale document as text (ADR-PTY-016) — no key into SLS or SFR, no XM;
- the edges that point INTO PTY (SLS, PUR, STL, TAX, SFR, PRC → CON-PTY-001 … CON-PTY-024) are minted by those consumers' P2 stages; `gov.py graph` derives them.

---

## 4 — FULL_DATABASE_SCRIPT

One consolidated executable for postgresql16. Prerequisite: the given `erp-core` is installed in the target schema (its migrations run before any business module), because BLOCK 8 seeds the given `mdl` lookup tables; BLOCKS 1 – 7 depend on nothing outside this script.

```sql
-- =====================================================================
-- PTY v1 — Party — postgresql16 — no schema prefix
-- Identifier transformation: camelCase field -> UPPER_SNAKE_CASE column,
-- unquoted (stored folded to lower case). PK generation: identity.
-- Logical type "number" (whole) -> INTEGER (declared once, ADR-PTY-018).
-- =====================================================================

-- ---------------------------------------------------------------------
-- BLOCK 1 — SEQUENCES
-- none: every PK uses the identity clause; the Party code and the
-- request docNo come from the given number series (CORE_NUMBER_SERIES
-- through NumberSeriesApi), not from a sequence of this module.
-- ---------------------------------------------------------------------

-- ---------------------------------------------------------------------
-- BLOCK 2 — PARENT TABLES
-- ---------------------------------------------------------------------

CREATE TABLE PTY_PARTY (
    PARTY_PK                 BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    MOBILE                   VARCHAR(20),
    EMAIL                    VARCHAR(254),
    TAX_REGISTRATION_NO      VARCHAR(30),
    NAME_AR                  VARCHAR(200),
    NAME_EN                  VARCHAR(200),
    CODE                     VARCHAR(20)   NOT NULL,
    IS_ACTIVE_FL             BOOLEAN       DEFAULT TRUE NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

-- ---------------------------------------------------------------------
-- BLOCK 3 — CHILD TABLES
-- PTY_PARTY -> PTY_CUSTOMER -> PTY_CREDIT_OVERRIDE_REQUEST
-- PTY_PARTY -> PTY_SUPPLIER
-- PTY_PARTY -> PTY_ADDRESS
-- ---------------------------------------------------------------------

CREATE TABLE PTY_CUSTOMER (
    CUSTOMER_PK              BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    CUSTOMER_GROUP_CODE      VARCHAR(50)   DEFAULT 'RETAIL' NOT NULL,
    CREDIT_LIMIT             NUMERIC(18,3),
    PAYMENT_TERM_DAYS        INTEGER,
    SEC_USER_ID              BIGINT,
    IS_WALK_IN_FL            BOOLEAN       DEFAULT FALSE NOT NULL,
    PARTY_FK                 BIGINT        NOT NULL,
    IS_ACTIVE_FL             BOOLEAN       DEFAULT TRUE NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE PTY_SUPPLIER (
    SUPPLIER_PK              BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    PAYMENT_TERM_DAYS        INTEGER,
    PARTY_FK                 BIGINT        NOT NULL,
    IS_ACTIVE_FL             BOOLEAN       DEFAULT TRUE NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE PTY_ADDRESS (
    ADDRESS_PK               BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    ADDRESS_TYPE_CODE        VARCHAR(50)   NOT NULL,
    LINE1                    VARCHAR(200)  NOT NULL,
    LINE2                    VARCHAR(200),
    CITY                     VARCHAR(100),
    REGION                   VARCHAR(100),
    POSTAL_CODE              VARCHAR(20),
    IS_DEFAULT_FL            BOOLEAN       DEFAULT FALSE NOT NULL,
    PARTY_FK                 BIGINT        NOT NULL,
    NAME_AR                  VARCHAR(100),
    NAME_EN                  VARCHAR(100),
    CODE                     VARCHAR(10)   NOT NULL,
    IS_ACTIVE_FL             BOOLEAN       DEFAULT TRUE NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

CREATE TABLE PTY_CREDIT_OVERRIDE_REQUEST (
    CREDIT_OVERRIDE_REQUEST_PK BIGINT GENERATED ALWAYS AS IDENTITY NOT NULL,
    SALE_AMOUNT              NUMERIC(18,3) NOT NULL,
    CREDIT_LIMIT_AT_REQUEST  NUMERIC(18,3),
    OPEN_EXPOSURE            NUMERIC(18,3),
    CREDIT_CHECK_RESULT_CODE VARCHAR(50)   NOT NULL,
    SOURCE_DOCUMENT_REF      VARCHAR(60)   NOT NULL,
    DECIDED_BY               BIGINT,
    DECIDED_AT               TIMESTAMPTZ,
    DECISION_REASON          VARCHAR(500),
    USED_DOCUMENT_REF        VARCHAR(60),
    CUSTOMER_FK              BIGINT        NOT NULL,
    DOC_NO                   VARCHAR(20)   NOT NULL,
    DOC_DATE                 TIMESTAMPTZ   NOT NULL,
    STATUS_CODE              VARCHAR(50)   DEFAULT 'PENDING' NOT NULL,
    CREATED_BY               VARCHAR(100)  NOT NULL,
    CREATED_AT               TIMESTAMPTZ   NOT NULL,
    UPDATED_BY               VARCHAR(100)  NOT NULL,
    UPDATED_AT               TIMESTAMPTZ   NOT NULL
);

-- ---------------------------------------------------------------------
-- BLOCK 4 — COMMENTS (every column cites its DBF id)
-- ---------------------------------------------------------------------

COMMENT ON TABLE PTY_PARTY IS 'ENT-PTY-001 Party — one identity per counterparty, shared by its Customer and Supplier roles (master, SHARED owner; CON-PTY-001)';
COMMENT ON COLUMN PTY_PARTY.PARTY_PK IS 'DBF-PTY-001 · ENT-PTY-001.partyPk · identity key (BIGINT)';
COMMENT ON COLUMN PTY_PARTY.MOBILE IS 'DBF-PTY-002 · ENT-PTY-001.mobile · optional, not unique; format RULE-PTY-004';
COMMENT ON COLUMN PTY_PARTY.EMAIL IS 'DBF-PTY-003 · ENT-PTY-001.email · unique among active Parties ignoring case and surrounding spaces (RULE-PTY-002); format RULE-PTY-003';
COMMENT ON COLUMN PTY_PARTY.TAX_REGISTRATION_NO IS 'DBF-PTY-004 · ENT-PTY-001.taxRegistrationNo · optional (Advanced section)';
COMMENT ON COLUMN PTY_PARTY.NAME_AR IS 'DBF-PTY-005 · ENT-PTY-001.nameAr · at least one of NAME_AR / NAME_EN (RULE-PTY-001)';
COMMENT ON COLUMN PTY_PARTY.NAME_EN IS 'DBF-PTY-006 · ENT-PTY-001.nameEn · at least one of NAME_AR / NAME_EN (RULE-PTY-001)';
COMMENT ON COLUMN PTY_PARTY.CODE IS 'DBF-PTY-007 · ENT-PTY-001.code · business number from the given number series, read-only after first save (RULE-PTY-007)';
COMMENT ON COLUMN PTY_PARTY.IS_ACTIVE_FL IS 'DBF-PTY-008 · ENT-PTY-001.isActiveFl · false when no role is active (REQ-PTY-028, REQ-PTY-029)';
COMMENT ON COLUMN PTY_PARTY.CREATED_BY IS 'DBF-PTY-009 · ENT-PTY-001.createdBy · platform-filled principal of the given sec user (ADR-PTY-019)';
COMMENT ON COLUMN PTY_PARTY.CREATED_AT IS 'DBF-PTY-010 · ENT-PTY-001.createdAt · platform-filled';
COMMENT ON COLUMN PTY_PARTY.UPDATED_BY IS 'DBF-PTY-011 · ENT-PTY-001.updatedBy · platform-filled principal of the given sec user (ADR-PTY-019)';
COMMENT ON COLUMN PTY_PARTY.UPDATED_AT IS 'DBF-PTY-012 · ENT-PTY-001.updatedAt · platform-filled';

COMMENT ON TABLE PTY_CUSTOMER IS 'ENT-PTY-002 Customer — the buying role of a Party; names and code are read from PTY_PARTY (master, SHARED owner; CON-PTY-002)';
COMMENT ON COLUMN PTY_CUSTOMER.CUSTOMER_PK IS 'DBF-PTY-013 · ENT-PTY-002.customerPk · identity key (BIGINT), the HARD-FK target of SLS, STL, SFR';
COMMENT ON COLUMN PTY_CUSTOMER.CUSTOMER_GROUP_CODE IS 'DBF-PTY-014 · ENT-PTY-002.customerGroupCode · code of lookup CUSTOMER_GROUP (given mdl), default RETAIL (REQ-PTY-007, RULE-PTY-018)';
COMMENT ON COLUMN PTY_CUSTOMER.CREDIT_LIMIT IS 'DBF-PTY-015 · ENT-PTY-002.creditLimit · tenant base currency, 0 or more (RULE-PTY-022); empty = no credit allowance (REQ-PTY-068); written only while b2bCredit is on (RULE-PTY-023)';
COMMENT ON COLUMN PTY_CUSTOMER.PAYMENT_TERM_DAYS IS 'DBF-PTY-016 · ENT-PTY-002.paymentTermDays · whole days 0 to 365 (RULE-PTY-008); written only while b2bCredit is on (RULE-PTY-023)';
COMMENT ON COLUMN PTY_CUSTOMER.SEC_USER_ID IS 'DBF-PTY-017 · ENT-PTY-002.secUserId · identifier of the given sec CUSTOMER-realm user, set on invitation acceptance (REQ-PTY-052); unique (RULE-PTY-020); no constraint, no XM — given reached through SecCustomerInvitationApi';
COMMENT ON COLUMN PTY_CUSTOMER.IS_WALK_IN_FL IS 'DBF-PTY-018 · ENT-PTY-002.isWalkInFl · true on the single walk-in Customer only (RULE-PTY-010 to RULE-PTY-014)';
COMMENT ON COLUMN PTY_CUSTOMER.PARTY_FK IS 'DBF-PTY-019 · ENT-PTY-002.partyFk · intra-module FK to PTY_PARTY, set on create and never changed; one Customer per Party (RULE-PTY-009)';
COMMENT ON COLUMN PTY_CUSTOMER.IS_ACTIVE_FL IS 'DBF-PTY-020 · ENT-PTY-002.isActiveFl · deactivation, not deletion (RULE-PTY-005, RULE-PTY-006)';
COMMENT ON COLUMN PTY_CUSTOMER.CREATED_BY IS 'DBF-PTY-021 · ENT-PTY-002.createdBy · platform-filled principal of the given sec user (ADR-PTY-019)';
COMMENT ON COLUMN PTY_CUSTOMER.CREATED_AT IS 'DBF-PTY-022 · ENT-PTY-002.createdAt · platform-filled';
COMMENT ON COLUMN PTY_CUSTOMER.UPDATED_BY IS 'DBF-PTY-023 · ENT-PTY-002.updatedBy · platform-filled principal of the given sec user (ADR-PTY-019)';
COMMENT ON COLUMN PTY_CUSTOMER.UPDATED_AT IS 'DBF-PTY-024 · ENT-PTY-002.updatedAt · platform-filled';

COMMENT ON TABLE PTY_SUPPLIER IS 'ENT-PTY-003 Supplier — the selling-to-us role of a Party; names and code are read from PTY_PARTY (master, SHARED owner; CON-PTY-003)';
COMMENT ON COLUMN PTY_SUPPLIER.SUPPLIER_PK IS 'DBF-PTY-025 · ENT-PTY-003.supplierPk · identity key (BIGINT), the HARD-FK target of PUR, STL';
COMMENT ON COLUMN PTY_SUPPLIER.PAYMENT_TERM_DAYS IS 'DBF-PTY-026 · ENT-PTY-003.paymentTermDays · whole days 0 to 365 (RULE-PTY-008), independent of b2bCredit (ADR-PTY-012)';
COMMENT ON COLUMN PTY_SUPPLIER.PARTY_FK IS 'DBF-PTY-027 · ENT-PTY-003.partyFk · intra-module FK to PTY_PARTY, set on create and never changed; one Supplier per Party (RULE-PTY-009)';
COMMENT ON COLUMN PTY_SUPPLIER.IS_ACTIVE_FL IS 'DBF-PTY-028 · ENT-PTY-003.isActiveFl · deactivation, not deletion (RULE-PTY-005, RULE-PTY-006)';
COMMENT ON COLUMN PTY_SUPPLIER.CREATED_BY IS 'DBF-PTY-029 · ENT-PTY-003.createdBy · platform-filled principal of the given sec user (ADR-PTY-019)';
COMMENT ON COLUMN PTY_SUPPLIER.CREATED_AT IS 'DBF-PTY-030 · ENT-PTY-003.createdAt · platform-filled';
COMMENT ON COLUMN PTY_SUPPLIER.UPDATED_BY IS 'DBF-PTY-031 · ENT-PTY-003.updatedBy · platform-filled principal of the given sec user (ADR-PTY-019)';
COMMENT ON COLUMN PTY_SUPPLIER.UPDATED_AT IS 'DBF-PTY-032 · ENT-PTY-003.updatedAt · platform-filled';

COMMENT ON TABLE PTY_ADDRESS IS 'ENT-PTY-004 Address — a reusable location of a Party (master, SHARED owner; CON-PTY-004)';
COMMENT ON COLUMN PTY_ADDRESS.ADDRESS_PK IS 'DBF-PTY-033 · ENT-PTY-004.addressPk · identity key (BIGINT), the HARD-FK target of SLS, PUR, SFR documents';
COMMENT ON COLUMN PTY_ADDRESS.ADDRESS_TYPE_CODE IS 'DBF-PTY-034 · ENT-PTY-004.addressTypeCode · code of lookup ADDRESS_TYPE (given mdl) (RULE-PTY-016)';
COMMENT ON COLUMN PTY_ADDRESS.LINE1 IS 'DBF-PTY-035 · ENT-PTY-004.line1 · required, non-blank (RULE-PTY-015)';
COMMENT ON COLUMN PTY_ADDRESS.LINE2 IS 'DBF-PTY-036 · ENT-PTY-004.line2 · optional (Advanced section)';
COMMENT ON COLUMN PTY_ADDRESS.CITY IS 'DBF-PTY-037 · ENT-PTY-004.city · optional';
COMMENT ON COLUMN PTY_ADDRESS.REGION IS 'DBF-PTY-038 · ENT-PTY-004.region · optional (Advanced section)';
COMMENT ON COLUMN PTY_ADDRESS.POSTAL_CODE IS 'DBF-PTY-039 · ENT-PTY-004.postalCode · optional (Advanced section)';
COMMENT ON COLUMN PTY_ADDRESS.IS_DEFAULT_FL IS 'DBF-PTY-040 · ENT-PTY-004.isDefaultFl · system-maintained, one active default per Party per address type (RULE-PTY-017)';
COMMENT ON COLUMN PTY_ADDRESS.PARTY_FK IS 'DBF-PTY-041 · ENT-PTY-004.partyFk · intra-module FK to PTY_PARTY, set on create and never changed';
COMMENT ON COLUMN PTY_ADDRESS.NAME_AR IS 'DBF-PTY-042 · ENT-PTY-004.nameAr · optional address label (Arabic)';
COMMENT ON COLUMN PTY_ADDRESS.NAME_EN IS 'DBF-PTY-043 · ENT-PTY-004.nameEn · optional address label (English)';
COMMENT ON COLUMN PTY_ADDRESS.CODE IS 'DBF-PTY-044 · ENT-PTY-004.code · system-assigned ordinal within the Party (1, 2, 3), not a business number';
COMMENT ON COLUMN PTY_ADDRESS.IS_ACTIVE_FL IS 'DBF-PTY-045 · ENT-PTY-004.isActiveFl · deactivation, not deletion (RULE-PTY-006)';
COMMENT ON COLUMN PTY_ADDRESS.CREATED_BY IS 'DBF-PTY-046 · ENT-PTY-004.createdBy · platform-filled principal of the given sec user (ADR-PTY-019)';
COMMENT ON COLUMN PTY_ADDRESS.CREATED_AT IS 'DBF-PTY-047 · ENT-PTY-004.createdAt · platform-filled';
COMMENT ON COLUMN PTY_ADDRESS.UPDATED_BY IS 'DBF-PTY-048 · ENT-PTY-004.updatedBy · platform-filled principal of the given sec user (ADR-PTY-019)';
COMMENT ON COLUMN PTY_ADDRESS.UPDATED_AT IS 'DBF-PTY-049 · ENT-PTY-004.updatedAt · platform-filled; read by RULE-PTY-017 to pick the next default';

COMMENT ON TABLE PTY_CREDIT_OVERRIDE_REQUEST IS 'ENT-PTY-005 Credit override request — one single-step approval per credit sale above the limit (transactional, SHARED owner; CON-PTY-005; lifecycle SRS A7)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.CREDIT_OVERRIDE_REQUEST_PK IS 'DBF-PTY-050 · ENT-PTY-005.creditOverrideRequestPk · identity key (BIGINT), the reference SLS and SFR store against a credit sale';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.SALE_AMOUNT IS 'DBF-PTY-051 · ENT-PTY-005.saleAmount · tenant base currency, greater than 0 (RULE-PTY-032)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.CREDIT_LIMIT_AT_REQUEST IS 'DBF-PTY-052 · ENT-PTY-005.creditLimitAtRequest · copied from PTY_CUSTOMER.CREDIT_LIMIT at creation; empty when the Customer has no limit';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.OPEN_EXPOSURE IS 'DBF-PTY-053 · ENT-PTY-005.openExposure · from the credit exposure contribution (REQ-PTY-080); empty when exposure was unavailable';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.CREDIT_CHECK_RESULT_CODE IS 'DBF-PTY-054 · ENT-PTY-005.creditCheckResultCode · code of lookup CREDIT_CHECK_RESULT; only OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE (RULE-PTY-025)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.SOURCE_DOCUMENT_REF IS 'DBF-PTY-055 · ENT-PTY-005.sourceDocumentRef · text reference of the consuming sale, no key (ADR-PTY-016)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.DECIDED_BY IS 'DBF-PTY-056 · ENT-PTY-005.decidedBy · identifier of the given sec user who approved or rejected; no constraint, no XM';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.DECIDED_AT IS 'DBF-PTY-057 · ENT-PTY-005.decidedAt · set on approval or rejection';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.DECISION_REASON IS 'DBF-PTY-058 · ENT-PTY-005.decisionReason · required on rejection (RULE-PTY-027)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.USED_DOCUMENT_REF IS 'DBF-PTY-059 · ENT-PTY-005.usedDocumentRef · text reference of the sale that used the approval; set on USED (REQ-PTY-097)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.CUSTOMER_FK IS 'DBF-PTY-060 · ENT-PTY-005.customerFk · intra-module FK to PTY_CUSTOMER; never the walk-in Customer (RULE-PTY-033)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.DOC_NO IS 'DBF-PTY-061 · ENT-PTY-005.docNo · business number from the given number series, read-only (RULE-PTY-031)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.DOC_DATE IS 'DBF-PTY-062 · ENT-PTY-005.docDate · time of creation (REQ-PTY-092)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.STATUS_CODE IS 'DBF-PTY-063 · ENT-PTY-005.statusCode · code of lookup CREDIT_OVERRIDE_STATUS; PENDING then APPROVED / REJECTED / CANCELLED, APPROVED then USED (SRS A7, RULE-PTY-026)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.CREATED_BY IS 'DBF-PTY-064 · ENT-PTY-005.createdBy · platform-filled principal of the requester; only the requester cancels (RULE-PTY-029, ADR-PTY-019)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.CREATED_AT IS 'DBF-PTY-065 · ENT-PTY-005.createdAt · platform-filled';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.UPDATED_BY IS 'DBF-PTY-066 · ENT-PTY-005.updatedBy · platform-filled principal of the given sec user (ADR-PTY-019)';
COMMENT ON COLUMN PTY_CREDIT_OVERRIDE_REQUEST.UPDATED_AT IS 'DBF-PTY-067 · ENT-PTY-005.updatedAt · platform-filled';

-- ---------------------------------------------------------------------
-- BLOCK 5 — CONSTRAINTS
-- ---------------------------------------------------------------------

-- 5a PRIMARY KEYS
ALTER TABLE PTY_PARTY ADD CONSTRAINT PK_PTY_PARTY PRIMARY KEY (PARTY_PK);
ALTER TABLE PTY_CUSTOMER ADD CONSTRAINT PK_PTY_CUSTOMER PRIMARY KEY (CUSTOMER_PK);
ALTER TABLE PTY_SUPPLIER ADD CONSTRAINT PK_PTY_SUPPLIER PRIMARY KEY (SUPPLIER_PK);
ALTER TABLE PTY_ADDRESS ADD CONSTRAINT PK_PTY_ADDRESS PRIMARY KEY (ADDRESS_PK);
ALTER TABLE PTY_CREDIT_OVERRIDE_REQUEST ADD CONSTRAINT PK_PTY_CREDIT_OVERRIDE_REQUEST PRIMARY KEY (CREDIT_OVERRIDE_REQUEST_PK);

-- 5b UNIQUE
-- Party code is a business number (RULE-PTY-007, CON-PTY-001)
ALTER TABLE PTY_PARTY ADD CONSTRAINT UQ_PTY_PARTY_CODE UNIQUE (CODE);
-- One Customer and one Supplier per Party (RULE-PTY-009)
ALTER TABLE PTY_CUSTOMER ADD CONSTRAINT UQ_PTY_CUSTOMER_PARTY_FK UNIQUE (PARTY_FK);
ALTER TABLE PTY_SUPPLIER ADD CONSTRAINT UQ_PTY_SUPPLIER_PARTY_FK UNIQUE (PARTY_FK);
-- One Customer per CUSTOMER-realm user (RULE-PTY-020); NULLs stay distinct
ALTER TABLE PTY_CUSTOMER ADD CONSTRAINT UQ_PTY_CUSTOMER_SEC_USER_ID UNIQUE (SEC_USER_ID);
-- Address code is an ordinal within the Party (ENT-PTY-004.code, CON-PTY-004)
ALTER TABLE PTY_ADDRESS ADD CONSTRAINT UQ_PTY_ADDRESS_PARTY_CODE UNIQUE (PARTY_FK, CODE);
-- Request number is a business number (RULE-PTY-031, CON-PTY-005)
ALTER TABLE PTY_CREDIT_OVERRIDE_REQUEST ADD CONSTRAINT UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO UNIQUE (DOC_NO);
-- Conditional / expression uniqueness: postgresql16 expresses a partial or
-- expression UNIQUE only as a unique index (not an ALTER TABLE constraint).
-- Email unique among active Parties, ignoring case and surrounding spaces (RULE-PTY-002)
CREATE UNIQUE INDEX UQ_PTY_PARTY_EMAIL ON PTY_PARTY (LOWER(BTRIM(EMAIL))) WHERE IS_ACTIVE_FL AND EMAIL IS NOT NULL;
-- One walk-in Customer per tenant (RULE-PTY-014)
CREATE UNIQUE INDEX UQ_PTY_CUSTOMER_IS_WALK_IN_FL ON PTY_CUSTOMER (IS_WALK_IN_FL) WHERE IS_WALK_IN_FL;
-- At most one active default Address per Party per address type (RULE-PTY-017)
CREATE UNIQUE INDEX UQ_PTY_ADDRESS_IS_DEFAULT_FL ON PTY_ADDRESS (PARTY_FK, ADDRESS_TYPE_CODE) WHERE IS_DEFAULT_FL AND IS_ACTIVE_FL;

-- 5c CHECK
-- RULE-PTY-001 — at least one non-blank name
ALTER TABLE PTY_PARTY ADD CONSTRAINT CHK_PTY_PARTY_NAME
    CHECK (NULLIF(BTRIM(NAME_AR), '') IS NOT NULL OR NULLIF(BTRIM(NAME_EN), '') IS NOT NULL);
-- RULE-PTY-003 — one "@", non-empty local part, a "." in the domain part
ALTER TABLE PTY_PARTY ADD CONSTRAINT CHK_PTY_PARTY_EMAIL
    CHECK (NULLIF(EMAIL, '') IS NULL OR EMAIL ~ '^[^@]+@[^@]*\.[^@]*$');
-- RULE-PTY-004 — after removing spaces and dashes: optional "+" then 7 to 15 digits
ALTER TABLE PTY_PARTY ADD CONSTRAINT CHK_PTY_PARTY_MOBILE
    CHECK (NULLIF(MOBILE, '') IS NULL OR REGEXP_REPLACE(MOBILE, '[ -]', '', 'g') ~ '^\+?[0-9]{7,15}$');
-- RULE-PTY-022 — credit limit 0 or more
ALTER TABLE PTY_CUSTOMER ADD CONSTRAINT CHK_PTY_CUSTOMER_CREDIT_LIMIT
    CHECK (CREDIT_LIMIT IS NULL OR CREDIT_LIMIT >= 0);
-- RULE-PTY-008 — payment terms 0 to 365 days
ALTER TABLE PTY_CUSTOMER ADD CONSTRAINT CHK_PTY_CUSTOMER_PAYMENT_TERM_DAYS
    CHECK (PAYMENT_TERM_DAYS IS NULL OR PAYMENT_TERM_DAYS BETWEEN 0 AND 365);
-- RULE-PTY-011 — walk-in Customer carries no credit
ALTER TABLE PTY_CUSTOMER ADD CONSTRAINT CHK_PTY_CUSTOMER_WALK_IN_CREDIT
    CHECK (NOT IS_WALK_IN_FL OR (CREDIT_LIMIT IS NULL AND PAYMENT_TERM_DAYS IS NULL));
-- RULE-PTY-010 — walk-in Customer stays active
ALTER TABLE PTY_CUSTOMER ADD CONSTRAINT CHK_PTY_CUSTOMER_WALK_IN_ACTIVE
    CHECK (NOT IS_WALK_IN_FL OR IS_ACTIVE_FL);
-- RULE-PTY-013 — walk-in Customer is never linked to a CUSTOMER-realm user
ALTER TABLE PTY_CUSTOMER ADD CONSTRAINT CHK_PTY_CUSTOMER_WALK_IN_SEC_USER
    CHECK (NOT IS_WALK_IN_FL OR SEC_USER_ID IS NULL);
-- RULE-PTY-008 — payment terms 0 to 365 days
ALTER TABLE PTY_SUPPLIER ADD CONSTRAINT CHK_PTY_SUPPLIER_PAYMENT_TERM_DAYS
    CHECK (PAYMENT_TERM_DAYS IS NULL OR PAYMENT_TERM_DAYS BETWEEN 0 AND 365);
-- RULE-PTY-015 — non-blank first address line
ALTER TABLE PTY_ADDRESS ADD CONSTRAINT CHK_PTY_ADDRESS_LINE1
    CHECK (BTRIM(LINE1) <> '');
-- RULE-PTY-032 — sale amount greater than 0
ALTER TABLE PTY_CREDIT_OVERRIDE_REQUEST ADD CONSTRAINT CHK_PTY_CREDIT_OVERRIDE_REQUEST_SALE_AMOUNT
    CHECK (SALE_AMOUNT > 0);
-- RULE-PTY-025 — a request exists only for a real overrun (closed set CREDIT_CHECK_RESULT, CON-PTY-008)
ALTER TABLE PTY_CREDIT_OVERRIDE_REQUEST ADD CONSTRAINT CHK_PTY_CREDIT_OVERRIDE_REQUEST_CREDIT_CHECK_RESULT_CODE
    CHECK (CREDIT_CHECK_RESULT_CODE IN ('OVER_LIMIT', 'NO_CREDIT', 'EXPOSURE_UNAVAILABLE'));
-- SRS A7 — status values of the closed lifecycle (CREDIT_OVERRIDE_STATUS, CON-PTY-008)
ALTER TABLE PTY_CREDIT_OVERRIDE_REQUEST ADD CONSTRAINT CHK_PTY_CREDIT_OVERRIDE_REQUEST_STATUS_CODE
    CHECK (STATUS_CODE IN ('PENDING', 'APPROVED', 'REJECTED', 'CANCELLED', 'USED'));
-- RULE-PTY-027 — a rejection carries a non-blank reason
ALTER TABLE PTY_CREDIT_OVERRIDE_REQUEST ADD CONSTRAINT CHK_PTY_CREDIT_OVERRIDE_REQUEST_DECISION_REASON
    CHECK (STATUS_CODE <> 'REJECTED' OR NULLIF(BTRIM(DECISION_REASON), '') IS NOT NULL);
-- REQ-PTY-094, REQ-PTY-095 — a decided request records decider and time
ALTER TABLE PTY_CREDIT_OVERRIDE_REQUEST ADD CONSTRAINT CHK_PTY_CREDIT_OVERRIDE_REQUEST_DECIDED
    CHECK (STATUS_CODE NOT IN ('APPROVED', 'REJECTED', 'USED') OR (DECIDED_BY IS NOT NULL AND DECIDED_AT IS NOT NULL));
-- REQ-PTY-097 — a used request records the sale that used it
ALTER TABLE PTY_CREDIT_OVERRIDE_REQUEST ADD CONSTRAINT CHK_PTY_CREDIT_OVERRIDE_REQUEST_USED_DOCUMENT_REF
    CHECK (STATUS_CODE <> 'USED' OR USED_DOCUMENT_REF IS NOT NULL);

-- 5d INTRA-MODULE FOREIGN KEYS (parent PK exists; no ON DELETE action —
-- deactivation, not deletion, ARCH-5)
ALTER TABLE PTY_CUSTOMER ADD CONSTRAINT FK_PTY_CUSTOMER_PTY_PARTY
    FOREIGN KEY (PARTY_FK) REFERENCES PTY_PARTY (PARTY_PK);
ALTER TABLE PTY_SUPPLIER ADD CONSTRAINT FK_PTY_SUPPLIER_PTY_PARTY
    FOREIGN KEY (PARTY_FK) REFERENCES PTY_PARTY (PARTY_PK);
ALTER TABLE PTY_ADDRESS ADD CONSTRAINT FK_PTY_ADDRESS_PTY_PARTY
    FOREIGN KEY (PARTY_FK) REFERENCES PTY_PARTY (PARTY_PK);
ALTER TABLE PTY_CREDIT_OVERRIDE_REQUEST ADD CONSTRAINT FK_PTY_CREDIT_OVERRIDE_REQUEST_PTY_CUSTOMER
    FOREIGN KEY (CUSTOMER_FK) REFERENCES PTY_CUSTOMER (CUSTOMER_PK);
-- No cross-module FK: SRS A8 consumes nothing (XM register empty).

-- ---------------------------------------------------------------------
-- BLOCK 6 — TRIGGERS
-- none: no SRS RULE requires a database trigger. Credit-change audit
-- (RULE-PTY-030) goes to the given audit module through AuditApi; the
-- read-only code and docNo (RULE-PTY-007, RULE-PTY-031), the role-derived
-- Party state (REQ-PTY-028, REQ-PTY-029) and default reassignment
-- (RULE-PTY-017) are service behaviour (P3.1).
-- ---------------------------------------------------------------------

-- ---------------------------------------------------------------------
-- BLOCK 7 — INDEXES (non-PK; FK columns already covered by a UNIQUE
-- whose leading column is the FK are not indexed twice:
-- PTY_CUSTOMER.PARTY_FK, PTY_SUPPLIER.PARTY_FK, PTY_ADDRESS.PARTY_FK)
-- ---------------------------------------------------------------------

-- Customer / Supplier search filters and global search (SCR-REQ-PTY-001 B2, SCR-REQ-PTY-002 B2, REQ-PTY-013, REQ-PTY-018, REQ-PTY-041)
CREATE INDEX IDX_PTY_PARTY_NAME_AR ON PTY_PARTY (NAME_AR);
CREATE INDEX IDX_PTY_PARTY_NAME_EN ON PTY_PARTY (NAME_EN);
CREATE INDEX IDX_PTY_PARTY_MOBILE ON PTY_PARTY (MOBILE);
CREATE INDEX IDX_PTY_PARTY_EMAIL ON PTY_PARTY (EMAIL);
CREATE INDEX IDX_PTY_PARTY_TAX_REGISTRATION_NO ON PTY_PARTY (TAX_REGISTRATION_NO);
CREATE INDEX IDX_PTY_PARTY_IS_ACTIVE_FL ON PTY_PARTY (IS_ACTIVE_FL);
-- Customer filters (SCR-REQ-PTY-001 B2, SCR-REQ-PTY-003 B2)
CREATE INDEX IDX_PTY_CUSTOMER_CUSTOMER_GROUP_CODE ON PTY_CUSTOMER (CUSTOMER_GROUP_CODE);
CREATE INDEX IDX_PTY_CUSTOMER_IS_ACTIVE_FL ON PTY_CUSTOMER (IS_ACTIVE_FL);
-- carries-credit filter and the b2bCredit switch-off count (REQ-PTY-069, REQ-PTY-075)
CREATE INDEX IDX_PTY_CUSTOMER_CARRIES_CREDIT ON PTY_CUSTOMER (CUSTOMER_PK) WHERE CREDIT_LIMIT IS NOT NULL OR PAYMENT_TERM_DAYS IS NOT NULL;
-- Supplier filter (SCR-REQ-PTY-002 B2)
CREATE INDEX IDX_PTY_SUPPLIER_IS_ACTIVE_FL ON PTY_SUPPLIER (IS_ACTIVE_FL);
-- Party addresses by type, default reassignment by most recent update (REQ-PTY-032, REQ-PTY-036)
CREATE INDEX IDX_PTY_ADDRESS_PARTY_TYPE ON PTY_ADDRESS (PARTY_FK, ADDRESS_TYPE_CODE, UPDATED_AT);
-- Credit override requests: FK and search filters (SCR-REQ-PTY-004 B2, REQ-PTY-101, REQ-PTY-102)
CREATE INDEX IDX_PTY_CREDIT_OVERRIDE_REQUEST_CUSTOMER_FK ON PTY_CREDIT_OVERRIDE_REQUEST (CUSTOMER_FK);
CREATE INDEX IDX_PTY_CREDIT_OVERRIDE_REQUEST_STATUS_CODE ON PTY_CREDIT_OVERRIDE_REQUEST (STATUS_CODE);
CREATE INDEX IDX_PTY_CREDIT_OVERRIDE_REQUEST_DOC_DATE ON PTY_CREDIT_OVERRIDE_REQUEST (DOC_DATE);
CREATE INDEX IDX_PTY_CREDIT_OVERRIDE_REQUEST_CREATED_BY ON PTY_CREDIT_OVERRIDE_REQUEST (CREATED_BY);

-- ---------------------------------------------------------------------
-- BLOCK 8 — LOOKUP SEED DATA
-- Keys owned by PTY (SRS A6); tables owned by the given erp-core:modules.mdl
-- (MDL_LOOKUP_TYPE, MDL_LOOKUP_VALUE) — seeded here, never re-created.
-- Column list: ADR-PTY-022. Idempotent (re-run inserts nothing twice).
-- ---------------------------------------------------------------------

BEGIN;

INSERT INTO MDL_LOOKUP_TYPE (CODE, NAME_AR, NAME_EN, IS_ACTIVE_FL)
SELECT T.CODE, T.NAME_AR, T.NAME_EN, TRUE
FROM (VALUES
    ('CUSTOMER_GROUP',         'مجموعة العملاء',               'Customer group'),
    ('ADDRESS_TYPE',           'نوع العنوان',                  'Address type'),
    ('CREDIT_OVERRUN_ACTION',  'إجراء تجاوز حد الائتمان',       'Credit-overrun action'),
    ('CREDIT_CHECK_RESULT',    'نتيجة فحص الائتمان',            'Credit check result'),
    ('CREDIT_OVERRIDE_STATUS', 'حالة طلب تجاوز حد الائتمان',    'Credit override request status')
) AS T (CODE, NAME_AR, NAME_EN)
WHERE NOT EXISTS (SELECT 1 FROM MDL_LOOKUP_TYPE L WHERE L.CODE = T.CODE);

INSERT INTO MDL_LOOKUP_VALUE (LOOKUP_TYPE_CODE, CODE, NAME_AR, NAME_EN, SORT_ORDER, IS_ACTIVE_FL)
SELECT V.LOOKUP_TYPE_CODE, V.CODE, V.NAME_AR, V.NAME_EN, V.SORT_ORDER, TRUE
FROM (VALUES
    -- CUSTOMER_GROUP (open; D-04)
    ('CUSTOMER_GROUP',         'RETAIL',               'تجزئة',                   'Retail',                1),
    ('CUSTOMER_GROUP',         'WHOLESALE',            'جملة',                    'Wholesale',             2),
    -- ADDRESS_TYPE (open; D-04)
    ('ADDRESS_TYPE',           'BILLING',              'فوترة',                   'Billing',               1),
    ('ADDRESS_TYPE',           'DELIVERY',             'توصيل',                   'Delivery',              2),
    -- CREDIT_OVERRUN_ACTION (closed; POL-PTY-008)
    ('CREDIT_OVERRUN_ACTION',  'WARN',                 'تنبيه فقط',               'Warn only',             1),
    ('CREDIT_OVERRUN_ACTION',  'BLOCK_WITH_OVERRIDE',  'منع مع إمكانية الموافقة',  'Block with approval',   2),
    -- CREDIT_CHECK_RESULT (closed; ADR-PTY-017)
    ('CREDIT_CHECK_RESULT',    'WITHIN_LIMIT',         'ضمن الحد',                'Within limit',          1),
    ('CREDIT_CHECK_RESULT',    'OVER_LIMIT',           'تجاوز الحد',              'Over limit',            2),
    ('CREDIT_CHECK_RESULT',    'NO_CREDIT',            'لا ائتمان',               'No credit',             3),
    ('CREDIT_CHECK_RESULT',    'EXPOSURE_UNAVAILABLE', 'المديونية غير متاحة',      'Exposure unavailable',  4),
    ('CREDIT_CHECK_RESULT',    'NOT_APPLICABLE',       'غير مطبّق',               'Not applicable',        5),
    -- CREDIT_OVERRIDE_STATUS (closed; SRS A7, ADR-PTY-016)
    ('CREDIT_OVERRIDE_STATUS', 'PENDING',              'معلّق',                   'Pending',               1),
    ('CREDIT_OVERRIDE_STATUS', 'APPROVED',             'موافَق عليه',             'Approved',              2),
    ('CREDIT_OVERRIDE_STATUS', 'REJECTED',             'مرفوض',                   'Rejected',              3),
    ('CREDIT_OVERRIDE_STATUS', 'CANCELLED',            'ملغى',                    'Cancelled',             4),
    ('CREDIT_OVERRIDE_STATUS', 'USED',                 'مستخدَم',                 'Used',                  5)
) AS V (LOOKUP_TYPE_CODE, CODE, NAME_AR, NAME_EN, SORT_ORDER)
WHERE NOT EXISTS (
    SELECT 1 FROM MDL_LOOKUP_VALUE L
    WHERE L.LOOKUP_TYPE_CODE = V.LOOKUP_TYPE_CODE AND L.CODE = V.CODE
);

COMMIT;

-- ---------------------------------------------------------------------
-- BLOCK 9 — VIEWS
-- none: no SRS requirement needs a database view.
-- ---------------------------------------------------------------------

-- ---------------------------------------------------------------------
-- BLOCK 10 — FUNCTIONS / PROCEDURES
-- none: no SRS requirement needs a stored routine.
-- ---------------------------------------------------------------------
```

### 4.1 Self-verification (engine §7.2)

```
SYNTAX        ✓ S-1 every statement ends with ";" · no trailing comma · every referenced object is created here
                    (or is a given mdl table, seeded only)
              ✓ S-2 types: BIGINT, VARCHAR(n), BOOLEAN, TIMESTAMPTZ, NUMERIC(18,3) from §4.1; INTEGER declared once in §1
              ✓ S-3 PK / UQ / CHK / FK in ALTER TABLE form, named per §3 (partial / expression UNIQUE as unique index — dialect form)
              ✓ S-4 no PK trigger, no sequence default on a PK
              ✓ S-5 seed INSERTs carry column lists; COMMIT closes BLOCK 8
              ✓ S-6 no constraint references another module's table
              ✓ S-7 no schema prefix anywhere
              ✓ S-8 no placeholder in SQL
PK STRATEGY   ✓ 5 / 5 PK columns carry GENERATED ALWAYS AS IDENTITY; BLOCK 1 empty
ORDER         ✓ parents before children · PK before FK · lookup DDL (given) before lookup INSERTs · COMMIT after the last INSERT
COMPLETENESS  ✓ 5 / 5 ENT → table · every A3 field → DBF (except the fields A3 types "presented from ENT-PTY-001", §2)
              ✓ 5 / 5 owned lookup keys seeded (16 values) · shared lookup tables not re-created
              ✓ indexes on every FK column (direct or via a leading UNIQUE column), every B2 filter column, every UNIQUE business key
CROSS-MODULE  ✓ no HARD-FK XM exists → no column-only pointer needed; no cross-module constraint, live or commented
TRACE         ✓ 67 / 67 DBF trace to ENT.field + ≥ 1 REQ · 0 XM · no orphan, no dangling id
```

Counts: 5 tables · 5 PK · 6 UNIQUE constraints + 3 unique indexes · 16 CHECK · 4 intra-module FK · 0 trigger · 15 non-unique indexes · 5 lookup types / 16 values seeded · 0 view · 0 routine.

---

## 5 — Decisions applied

### 5.1 DEFAULTs (structural best practice, no ADR needed)

| DEFAULT | What | Source |
|---|---|---|
| D-P2-01 | Identifier transformation camelCase → UPPER_SNAKE_CASE, unquoted; tables `PTY_` + entity name | engine §3; given table naming (`MDL_LOOKUP_TYPE`, `SEC_USER`) |
| D-P2-02 | Customer and Supplier carry no nameAr / nameEn / code columns — read through PARTY_FK from PTY_PARTY | SRS A3 ("presented from ENT-PTY-001"); ADR-PTY-014; REQ-PTY-024 |
| D-P2-03 | Open lookups (CUSTOMER_GROUP, ADDRESS_TYPE) are not CHECK-constrained — validated against active `mdl` values at runtime (RULE-PTY-016, RULE-PTY-018); closed sets (CREDIT_OVERRIDE_STATUS, CREDIT_CHECK_RESULT) are CHECK-constrained because their values are coded behaviour and a new value is BREAKING (CON-PTY-008, SRS A7, RULE-PTY-025) | profile LOOKUPS; engine §1 (A7 → check constraints) |
| D-P2-04 | Search indexes are B-tree on each filter column; "contains" matching, case-folding, Arabic letter-form unification (REQ-PTY-042) and mobile normalisation for guest matching (REQ-PTY-056) are query-level behaviour designed in P3.1; this script creates no database extension | engine §4.3; DP ownership of extensions by the platform |
| D-P2-05 | References to given entities (SEC_USER_ID, DECIDED_BY, audit user columns) carry no constraint and no XM — the given is reached through its interface | SRS A8, A9; engine §4 [C:C-GIVEN-1] |
| D-P2-06 | No trigger: RULE-PTY-007, RULE-PTY-017 (beyond its partial unique index), RULE-PTY-030, RULE-PTY-031 and REQ-PTY-028 / 029 are service behaviour | engine BLOCK 6 (audit triggers only when a RULE requires one) |
| D-P2-07 | No rows written to CORE_NUMBER_SERIES or CU_APP_CONFIGURATION: the Party code and the request docNo series are used through NumberSeriesApi; the credit-overrun setting is absent until saved and BLOCK_WITH_OVERRIDE applies meanwhile (REQ-PTY-087) | SRS A9; engine §4.2 (seeding duty covers lookup keys) |
| D-P2-08 | Intra-module FKs carry no ON DELETE action — rows are deactivated, never deleted | ARCH-5; RULE-PTY-006 |
| D-P2-09 | Column defaults only where the SRS states the initial value: IS_ACTIVE_FL TRUE (A3 "default true"), IS_WALK_IN_FL / IS_DEFAULT_FL FALSE, CUSTOMER_GROUP_CODE 'RETAIL' (REQ-PTY-007), STATUS_CODE 'PENDING' (REQ-PTY-091) | SRS A3, A7 |

### 5.2 ADRs raised by this stage (ADR-PTY-018 … ADR-PTY-022)

All five are NON-BREAKING — status ACCEPTED, the pass continued. The write scope of this run is the two P2 files, so each ADR is recorded here in full (Context · Decision · Consequences · Traces · Status) for the orchestrator to file under `analysis/decisions/PTY/ADR-PTY-{seq}.md`.

#### ADR-PTY-018 — Physical precision of fields the SRS leaves open
- **Context.** SRS A3 types creditLimit, saleAmount, creditLimitAtRequest and openExposure as `decimal` without precision, paymentTermDays as `number (0 – 365)` (a type the syntax map lacks), and the lookup-code fields as `lookup` without length. The platform serves Egypt and the Gulf, where some base currencies (OMR, KWD, BHD) carry 3 minor-unit decimals.
- **Decision.** Monetary decimals → `NUMERIC(18,3)` (15 integer digits, 3 decimals — covers every base currency of the target markets). Whole-number days → `INTEGER`, declared once in the script header. Lookup code columns → `VARCHAR(50)` (longest seeded code: EXPOSURE_UNAVAILABLE, 20 characters; room for open-lookup values added as data).
- **Consequences.** A 2-decimal currency stores a trailing zero digit; the backend rounds to the tenant currency's minor unit. No SRS meaning changes.
- **Traces.** ENT-PTY-002.creditLimit, ENT-PTY-002.paymentTermDays, ENT-PTY-003.paymentTermDays, ENT-PTY-005.saleAmount / creditLimitAtRequest / openExposure, DBF-PTY-014, 015, 016, 026, 034, 051, 052, 053, 054, 063; REQ-PTY-064, REQ-PTY-065, REQ-PTY-091.
- **Status.** ACCEPTED — non-breaking.

#### ADR-PTY-019 — Audit user columns hold the platform principal
- **Context.** SRS A3 lists createdBy / updatedBy as "reference (given sec User, BIGINT)", system-filled. The engine's AUDIT COLUMNS rule (from the given) states that audit user columns are filled by the platform and hold a principal string, not a numeric FK; the given's auditing fills them, and a business module never writes them.
- **Decision.** CREATED_BY and UPDATED_BY are `VARCHAR(100)` holding the principal the given `sec` module authenticates — the same sec User the SRS refers to, in the form the platform writes. Non-audit user references the module itself sets keep the SRS type: SEC_USER_ID (DBF-PTY-017) and DECIDED_BY (DBF-PTY-056) are `BIGINT` sec user identifiers.
- **Consequences.** RULE-PTY-029 ("only the requester cancels") compares PTY_CREDIT_OVERRIDE_REQUEST.CREATED_BY with the current principal; REQ-PTY-102 filters on it (indexed, IDX_PTY_CREDIT_OVERRIDE_REQUEST_CREATED_BY). User names shown in history and decisions are resolved through SecUserDirectoryApi (SRS A9). No constraint on SEC_USER either way, so no SRS behaviour changes.
- **Traces.** ENT-PTY-001 … ENT-PTY-005 .createdBy / .updatedBy; DBF-PTY-009, 011, 021, 023, 029, 031, 046, 048, 064, 066; REQ-PTY-096, REQ-PTY-102; RULE-PTY-029.
- **Status.** ACCEPTED — non-breaking.

#### ADR-PTY-020 — No currency column on PTY amounts (profile check CM-8)
- **Context.** Profile check CM-8 asks every monetary row to carry its currency. PTY holds amounts on ENT-PTY-002 (creditLimit) and ENT-PTY-005 (saleAmount, creditLimitAtRequest, openExposure). The approved SRS states a business-policies SCOPE EXCEPTION — "multi-currency party balances" — and types each amount as "tenant base currency"; no currency field exists in SRS A3. NO-COLUMN-INVENTION forbids a column with no SRS origin.
- **Decision.** No currency column. The amounts are thresholds and approval amounts expressed in the tenant base currency (held by the given `cu`), as the SRS and the contract's §0 rules ("Currency: every amount is in the tenant's base currency") promise. PTY carries no stock-bearing row and no price, so the warehouse and price-list parts of CM-8 do not apply.
- **Consequences.** Consumers convert a foreign-currency document to base currency before a credit check (CON-PTY-015) and a request (CON-PTY-016). Lifting the scope exception in a later version adds a currency column per amount as an ADDITIVE migration with its own SRS field.
- **Traces.** ENT-PTY-002.creditLimit, ENT-PTY-005.saleAmount; DBF-PTY-015, 051, 052, 053; REQ-PTY-064, REQ-PTY-079, REQ-PTY-091.
- **Status.** ACCEPTED — non-breaking (a MINOR deviation from CM-8 justified by the approved scope exception).

#### ADR-PTY-021 — Tenant scoping without a tenant column
- **Context.** RULE-PTY-002, RULE-PTY-014 and RULE-PTY-020 are per-tenant uniqueness rules. The SRS has no tenant field; business modules are tenant-agnostic and the given `erp-core` (CORE_TENANT, TenantContext) isolates tenants (platform brief §0, D3).
- **Decision.** No tenant column (NO-COLUMN-INVENTION). Every uniqueness rule is declared once in the script (UQ_PTY_PARTY_EMAIL, UQ_PTY_CUSTOMER_IS_WALK_IN_FL, UQ_PTY_CUSTOMER_SEC_USER_ID, UQ_PTY_PARTY_CODE, UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO, UQ_PTY_ADDRESS_PARTY_CODE) and holds per tenant under the isolation the given applies to every business table.
- **Consequences.** If the given isolates tenants by a discriminator column in shared tables, the given's tenancy mechanism adds that column to each PTY table and leads each UNIQUE above with it; that is the given's documented integration, applied by the executor in P3.1, not a PTY column. Nothing in PTY reads a tenant identifier.
- **Traces.** RULE-PTY-002, RULE-PTY-014, RULE-PTY-020; DBF-PTY-003, 007, 017, 018, 061; REQ-PTY-010, REQ-PTY-044, REQ-PTY-054.
- **Status.** ACCEPTED — non-breaking.

#### ADR-PTY-022 — Lookup seed rows written against the given mdl tables
- **Context.** PTY owns five lookup keys (SRS A6); their values live in the given `mdl` tables MDL_LOOKUP_TYPE and MDL_LOOKUP_VALUE, which this script must seed and must not re-create (engine §4.2). The given's internal column list is not among this stage's inputs.
- **Decision.** BLOCK 8 seeds both tables with the column list CODE, NAME_AR, NAME_EN, IS_ACTIVE_FL (type) and LOOKUP_TYPE_CODE, CODE, NAME_AR, NAME_EN, SORT_ORDER, IS_ACTIVE_FL (value) — the platform naming convention for a coded, bilingual, deactivatable lookup — inserted idempotently with `WHERE NOT EXISTS`. The values and labels are verbatim from SRS A6.
- **Consequences.** The executor of the backend plan (P3.1) checks this column list against the given's installed `mdl` DDL when it turns BLOCK 8 into the module's Flyway migration (V1000+) and aligns the column names if the given's differ; the seeded keys and values are fixed by the SRS and do not change. New tenants receive the values through the given's provisioning copy of `mdl` seeds.
- **Traces.** SRS A6 (CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT, CREDIT_OVERRIDE_STATUS); DBF-PTY-014, 034, 054, 063; REQ-PTY-007, REQ-PTY-038, REQ-PTY-040, REQ-PTY-086, REQ-PTY-091.
- **Status.** ACCEPTED — non-breaking.

### 5.3 Decisions inherited and applied

| Id | Applied as |
|---|---|
| ADR-PTY-012 | PTY_SUPPLIER.PAYMENT_TERM_DAYS (DBF-PTY-026) exists independently of `b2bCredit` |
| ADR-PTY-014 | one PTY_PARTY with PTY_CUSTOMER / PTY_SUPPLIER roles (UNIQUE PARTY_FK each); credit fields on PTY_CUSTOMER; PTY_ADDRESS on the Party |
| ADR-PTY-015 | email uniqueness among active Parties ignoring case (UQ_PTY_PARTY_EMAIL); email / mobile formats (CHK_PTY_PARTY_EMAIL, CHK_PTY_PARTY_MOBILE); SEC_USER_ID unique |
| ADR-PTY-016 | PTY_CREDIT_OVERRIDE_REQUEST with the A7 lifecycle as a closed CHECK; sale held as text (SOURCE_DOCUMENT_REF, USED_DOCUMENT_REF) |
| ADR-PTY-017 | empty CREDIT_LIMIT = no credit (nullable, no default); OPEN_EXPOSURE nullable for EXPOSURE_UNAVAILABLE |
| D-01, D-03, D-04, D-08, D-09 (SRS) | UQ_PTY_PARTY_CODE; mobile not unique; seed values of BLOCK 8; walk-in uniqueness and constraints; UQ_PTY_ADDRESS_IS_DEFAULT_FL |

BLOCKED: none.

---

## 6 — Registry content

The registry of this stage is `analysis/modules/PTY/P2/registry-db-pty.md` (same DBF and XM ids as §2 and §3).

| Table | ENT | Kind | DBF range |
|---|---|---|---|
| PTY_PARTY | ENT-PTY-001 | master | DBF-PTY-001 … DBF-PTY-012 |
| PTY_CUSTOMER | ENT-PTY-002 | master | DBF-PTY-013 … DBF-PTY-024 |
| PTY_SUPPLIER | ENT-PTY-003 | master | DBF-PTY-025 … DBF-PTY-032 |
| PTY_ADDRESS | ENT-PTY-004 | master | DBF-PTY-033 … DBF-PTY-049 |
| PTY_CREDIT_OVERRIDE_REQUEST | ENT-PTY-005 | transactional | DBF-PTY-050 … DBF-PTY-067 |

XM : none · Last DBF : DBF-PTY-067 · Last XM : none (next free XM-PTY-001) · Decisions : ADR-PTY-018 … ADR-PTY-022 (0 BLOCKED)

Event : "P2 completed: PTY v1 — 5 tables, 67 DBF, 0 XM"
