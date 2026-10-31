# ADR-INV-041 — Native SQL writes its tenant predicate by hand and is marked @TenantUnscoped
Status      : ACCEPTED
Stage       : gate analysis (revise 1, G1)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : ADR-INV-014, ADR-INV-025, ADR-INV-026, ADR-INV-044, QR-INV-015, QR-INV-016, QR-INV-017, QR-INV-024, QR-INV-038, QR-INV-041, REQ-INV-035, REQ-INV-038, REQ-INV-040, REQ-INV-109

## Context
The backend plan's §4 said every query is tenant-scoped by the given `AuditableEntity` / Hibernate `@TenantId`,
so "the TENANT_ID predicate is implied and not repeated". That holds for JPQL and derived queries only. Hibernate
does not add the tenant predicate to native SQL. Six catalogue entries are native:
- QR-INV-015 and QR-INV-016 (balances and availability, with joins to INV_LOT, INV_WAREHOUSE and a sub-select
  over INV_RESERVATION);
- QR-INV-017, QR-INV-024 and QR-INV-041 (insert-if-absent on a tenant-led unique constraint);
- QR-INV-038 (lots with a join to INV_STOCK_BALANCE).
As written, none of them was tenant-scoped. Erp-core checklist C3 requires every native query or JdbcTemplate
statement to name TENANT_ID, with its method marked `@TenantUnscoped(reason, adr)`.

## Decision
1. **JPQL and derived queries** keep the given's `@TenantId` filter; their logical spec does not repeat it.
2. **Every native statement** writes `TENANT_ID = :tenantId` on each table and in each join predicate (including
   sub-selects), and TENANT_ID in every INSERT column list (with VERSION 0 for the given `AuditableEntity`
   columns). `:tenantId` is the current tenant of the given tenant context; it is never taken from a request.
3. **Marking.** Each such repository method carries
   `@TenantUnscoped(reason = "native SQL — tenant predicate written by hand", adr = "ADR-INV-041")`.
4. **Scope.** QR-INV-015, 016, 017, 024, 038, 041; the advisory lock of ADR-INV-044 (tenant in the hash); any
   native form an implementer chooses for the in-process intents R-1 … R-6 (which are JPA as planned).
5. **Test.** A repository test runs each marked method with rows of two tenants and asserts that no row of the
   other tenant is read, locked or written.

## Consequences
- The six QR entries spell the predicate in their logical spec, so the executor cannot omit it.
- The `ON CONFLICT ON CONSTRAINT UQ_…` clauses are unchanged: every such constraint already leads with TENANT_ID
  (ADR-INV-014).
- No endpoint, table or contract item changes.

## Status
ACCEPTED — non-breaking.
