# ADR-INV-050 — Default switches serialise on a tenant advisory lock
Status      : ACCEPTED
Stage       : gate analysis (revise 2, G19)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : RULE-INV-006, RULE-INV-015, REQ-INV-008, REQ-INV-027, API-INV-007, API-INV-015, QR-INV-005, QR-INV-012, ADR-INV-026, ADR-INV-041, ADR-INV-044

## Context
API-INV-007 and API-INV-015 locked the current default with `WHERE IS_DEFAULT_FL = TRUE FOR UPDATE`. When two
switches run at once, the second waits on the row of the old default A. After the first commits, PostgreSQL
re-checks the WHERE clause on A's new version, which no longer matches: the second gets no row. The plan then
answered INV-500 ("none → provisioning defect"), or set C as default while B still was, and broke
UQ_INV_WAREHOUSE_IS_DEFAULT_FL. TC-INV-248 expects both switches to succeed, so the plan failed its own test.

## Decision
1. Every default switch first takes a transaction-scoped advisory lock on a key that does not move:
   `SELECT pg_advisory_xact_lock(hashtextextended(:tenantId || ':INV_WAREHOUSE_DEFAULT', 0))` for warehouses,
   `… ':INV_BRANCH_DEFAULT' …` for branches — the tenant in the hash, native SQL marked `@TenantUnscoped`
   (ADR-INV-041), as the idempotency lock of ADR-INV-044.
2. Only then does it read the current default (QR-INV-005 / QR-INV-012, `FOR UPDATE`) and the target, clear the
   old default and set the new one. The second switch waits on the advisory lock, then reads the default the
   first one committed, so it always finds exactly one row.
3. Provisioning and the first-warehouse path take the same lock before they set a default.
4. ADR-INV-026's "default switches lock the current default first" is replaced by this rule.

## Consequences
- Two concurrent switches both succeed, one after the other; the last committed target is the default.
  INV-500 "no default" can no longer come from a race (it remains a provisioning defect only).
- TC-INV-248 (warehouses) and a branch twin cover it.

## Status
ACCEPTED — non-breaking.
