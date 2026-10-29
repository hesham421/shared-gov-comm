# ADR-PTY-035 — Tenant columns, version column and tenant-composite uniqueness on every PTY table

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise (G2, G7) · Module : PTY v1 · Date : 2026-10-29
Supersedes : ADR-PTY-021 · Revises : ADR-PTY-022 (seed column list)

## Context
ADR-PTY-021 left tenant scoping to the given and gave no PTY table a tenant column. The erp-core checklist does not allow that:
- C1: every table has `TENANT_ID BIGINT NOT NULL`, `FK_<TABLE>_TENANT → CORE_TENANT(ID)` and an index led by `TENANT_ID`, and every `UNIQUE` is composite with `TENANT_ID`;
- C2: every entity extends `com.erp.common.domain.AuditableEntity`;
- C5: lookup rows are seeded for `TENANT_ID = 1`.

`AuditableEntity` maps `TENANT_ID` (Hibernate `@TenantId`), `VERSION` (`@Version`) and the four audit columns (erp-core `common`, ADR-COMMON-003). An entity that extends it needs both columns in its table.

## Decision
1. Each of the five tables carries `TENANT_ID BIGINT NOT NULL`, `FK_<TABLE>_TENANT` → `CORE_TENANT(ID)` and an index `IDX_<TABLE>_TENANT` whose first column is `TENANT_ID`. It also carries `VERSION BIGINT NOT NULL DEFAULT 0`. All seven are new DBFs: TENANT_ID is DBF-PTY-068 … 072 and VERSION is DBF-PTY-073 … 077. Both are system columns that only `AuditableEntity` writes.
2. Every unique constraint and unique index leads with `TENANT_ID`. This includes the partial and expression indexes UQ_PTY_PARTY_EMAIL, UQ_PTY_CUSTOMER_IS_WALK_IN_FL, UQ_PTY_ADDRESS_IS_DEFAULT_FL and the new UQ_PTY_CREDIT_OVERRIDE_REQUEST_OPEN_SOURCE (ADR-PTY-042). Each per-tenant rule is therefore enforced per tenant by the database: RULE-PTY-002, 009, 014, 017, 020 and 034, and the business numbers of RULE-PTY-007 and 031.
3. Each parent carries `UQ_<TABLE>_TENANT_PK (TENANT_ID, <PK>)`, and each intra-module FK is composite `(TENANT_ID, <FK>)`, so that a child can only point to a parent of its own tenant.
4. Every query names the tenant. JPQL, derived and search-builder queries run under the given tenant filter. The native queries QR-PTY-011 and QR-PTY-037 name `TENANT_ID = :tenantId` themselves and carry `@TenantUnscoped(reason, adr = "ADR-PTY-027")`.
5. The BLOCK 8 seeds write the given tables with `TENANT_ID = 1` (PLATFORM), using the column names erp-core 1.6.0 writes in its own seeds. These are `KEY` and `OWNER_MODULE_CODE` on MDL_LOOKUP_TYPE, `LOOKUP_TYPE_ID` resolved by key on MDL_LOOKUP_VALUE, and the CAT module's form. New tenants receive copies through the given provisioning. Flyway runs the migration in its own transaction, so the block holds no `BEGIN` and no `COMMIT`.
6. Bodies do not carry `VERSION`. Every PTY write loads and saves its row inside one transaction under the row locks of ADR-PTY-026, so the version check never fails for a PTY caller. It stays the given backstop against a detached write.

## Consequences
- The DBF count goes from 67 to 79: 5 TENANT_ID, 5 VERSION, and 2 CURRENCY_CODE (ADR-PTY-037).
- A record of another tenant reads and writes as not found. The PTY-404-* rows say "in the caller's tenant".
- ADR-PTY-022's instruction to check the column list at execution is replaced by the column list above.

## Traces
ENT-PTY-001 … ENT-PTY-005 · DBF-PTY-068 … DBF-PTY-077 · RULE-PTY-002, 007, 009, 014, 017, 020, 031, 034 · QR-PTY-011, QR-PTY-037 · REQ-PTY-010, REQ-PTY-044, REQ-PTY-054
