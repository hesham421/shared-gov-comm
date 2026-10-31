# ADR-INV-026 — Locking order, insert-if-absent and serialisation points
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-037, REQ-INV-008, REQ-INV-027, REQ-INV-006, REQ-INV-044, REQ-INV-088, SRS D-09, QR-INV-002, QR-INV-005, QR-INV-012, QR-INV-017, QR-INV-023, QR-INV-024, QR-INV-031, QR-INV-040, QR-INV-041

## Context
Parallel channels move the same Variant in the same warehouse (REQ-INV-037); the negative-stock and
reservation checks read a value and then write; two default switches, two first lots of one number, two scans
of one Variant and a deactivation racing a post must all stay consistent. "Validated first" is not a guard.

## Decision
- Balance rows: INSERT … ON CONFLICT ON CONSTRAINT UQ_INV_STOCK_BALANCE_VAR_WH_LOT DO NOTHING, then SELECT …
  FOR UPDATE, all rows of a document in ascending (VARIANT_FK, WAREHOUSE_FK, LOT_FK NULLS FIRST) order;
  valuation rows next, ascending VARIANT_FK, the same way on UQ_INV_STOCK_VALUATION_VARIANT_FK. Checks read
  after the locks.
- Reservations and issues of a Variant × warehouse serialise on the lot-less balance row of the pair.
- Posting paths read the warehouse FOR SHARE; deactivation takes it FOR UPDATE before its usage probe. The
  same pattern guards a branch against a concurrent warehouse save.
- Default switches lock the current default first (partial unique indexes back them).
- Document headers are locked FOR UPDATE before any status check (post / cancel / line change).
- Lots: SELECT … FOR UPDATE by (VARIANT_FK, CODE) and insert-if-absent on UQ_INV_LOT_VAR_CODE.
- Numbers come atomically from NumberSeriesApi; masters use optimistic VERSION for edits.

## Consequences
- No lost update, no double-pass of a check, no deadlock between documents (one global lock order).
- Large opening stock loads hold many row locks for one transaction; acceptable at ≤ 1000 rows.

## Status
ACCEPTED — non-breaking.
