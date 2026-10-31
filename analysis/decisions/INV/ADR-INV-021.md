# ADR-INV-021 — Infrastructure errors are PLATFORM-STD catalog rows
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-138, AC-INV-154, API-INV-001 … API-INV-051

## Context
The engine requires infrastructure errors (not found, forbidden, server) to be Error Catalog rows with
RULE = PLATFORM-STD and an ADR. The SRS gives a text only for the forbidden case (AC-INV-154). Optimistic
locking on VERSION (ADR-INV-014) and malformed requests also produce errors that no RULE covers.

## Decision
The catalog carries these PLATFORM-STD rows, every code an instance of `{MOD}-{http}[-{SLUG}]`:
INV-400 (malformed request), INV-401 (unauthenticated — the given security filter), INV-403 (forbidden —
message of AC-INV-154 verbatim), INV-404-{WAREHOUSE|BRANCH|VARIANT|ITEM|STOCK-MOVE|TRANSFER|STOCK-COUNT|
STOCK-COUNT-LINE|STOCK-ADJUSTMENT|LOT}-NOT-FOUND, INV-409-STALE-VERSION (OptimisticLockException) and INV-500.
Rows other than INV-403 carry no text: their messages are the platform's own `LocalizedException` texts,
not invented here. Translation of exceptions is central (CORE): constraint-name mapping for UNIQUE
violations, optimistic lock → INV-409-STALE-VERSION, anything else → INV-500.

## Consequences
- Every operation of api-spec-inv.yaml answers 400, 401, 403 and 500 with these codes.
- The message bundle needs the platform texts for the rows without SRS text; the implementer takes them from
  the given erp-core message catalog.

## Status
ACCEPTED — non-breaking.
