# ADR-INV-024 — The contract operations are one in-process bean with no REST surface
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : CON-INV-006 … CON-INV-021, RULE-INV-020, RULE-INV-021, RULE-INV-023, RULE-INV-039, REQ-INV-043 … REQ-INV-058

## Context
The contract promises sixteen operations and two events to other modules, all in-process
(conventions.module_interface: in_process). Several of them (reserve, release, receive, issue) have no
screen and no REST endpoint in SRS B5. Four rules are raised only on those paths (RULE-INV-020, 021, 023,
039). The Error Catalog block is derived into HTTP responses, and the QR catalog requires every entry to be
reached by an API-* block.

## Decision
- One Spring bean `com.commerce.inv.crossmodule.InventoryApi` honours CON-INV-006 … 019 (root namespace per
  ADR-PTY-047, gate-analysis revise 1 G22; it delegates to the internal engines — ADR-INV-042); the events
  CON-INV-020, 021 go through OutboxPublisher. No method has a REST route.
- The codes raised only in-process (INV-409-RESERVE-NOT-AVAILABLE, INV-409-RESERVATION-CLOSED,
  INV-409-STOCK-MOVE-IMMUTABLE, INV-422-LOT-EXPIRED, and INV-422-RESERVATION-MISMATCH added at gate-analysis
  revise 1) are listed in plan §5.1 and in the API document's
  document-level `x-inprocess-error-codes`, not in the HTTP catalog block, because no operation can answer
  them.
- Repository operations reached only in-process (reservation insert / lock / update / by key / by document,
  moves by idempotency key) are stated as intents R-1 … R-6 in SVC-API-INT, not as QR entries.

## Consequences
- api-spec-errors holds only codes an HTTP operation can raise.
- The calling modules receive these codes as LocalizedException and surface them on their own endpoints.

## Status
ACCEPTED — non-breaking.
