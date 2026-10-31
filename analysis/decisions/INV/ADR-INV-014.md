# ADR-INV-014 — Platform columns TENANT_ID and VERSION on every INV table
Status      : ACCEPTED
Stage       : P2 (Database)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-001, REQ-INV-003, REQ-INV-035, REQ-INV-037, REQ-INV-043, REQ-INV-078, RULE-INV-002, RULE-INV-006, RULE-INV-030, RULE-INV-036, RULE-INV-045, ENT-INV-001, ENT-INV-002, ENT-INV-003, ENT-INV-004, ENT-INV-005, ENT-INV-006, ENT-INV-007, ENT-INV-008, ENT-INV-009, ENT-INV-010, ENT-INV-011, ENT-INV-012, ENT-INV-013, ENT-INV-014

Filed from db-script-inv.md §5.2, where the decision is recorded in full; the text is unchanged.

## Context
SRS A3 lists no tenant or version field. The given `erp-core` 1.6.0 states the tenancy rule "every table
carries TENANT_ID" and requires every business entity to extend its `AuditableEntity` (TENANT_ID as
Hibernate `@TenantId`, VERSION as the optimistic lock): every table has `TENANT_ID BIGINT NOT NULL`,
`FK_<TABLE>_TENANT → CORE_TENANT(ID)`, an index led by TENANT_ID, every UNIQUE composite with TENANT_ID, and
`VERSION BIGINT NOT NULL DEFAULT 0`. The SRS per-tenant rules (RULE-INV-002, 006, 011, 015, 030, 036, 045)
need a tenant column to hold. NO-COLUMN-INVENTION admits a column the platform mandates for the entity. The
project applied the same rule to PTY (ADR-PTY-035).

## Decision
Every INV table carries TENANT_ID and VERSION as platform columns (DBF-INV-008/009, 020/021, 034/035,
059/060, 081/082, 095/096, 108/109, 123/124, 137/138, 152/153, 169/170, 185/186, 198/199, 214/215), traced
to the entity and to the REQs that create and update its rows. Every UNIQUE leads with TENANT_ID; every
intra-module FK is composite `(TENANT_ID, <FK>)` against a `UQ_<TABLE>_TENANT_PK`; the script is one Flyway
migration with no BEGIN / COMMIT.

## Consequences
- No INV code reads or writes a tenant identifier (the given fills it).
- VERSION gives the optimistic lock that, with the per-Variant serialisation of SRS D-09, keeps concurrent
  moves consistent (REQ-INV-037).
- A later SRS revision may list `tenantId` / `version` in A3's standard fields; no column changes then.

## Status
ACCEPTED — non-breaking.
