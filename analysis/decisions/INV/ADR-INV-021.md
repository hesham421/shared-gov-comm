# ADR-INV-021 — Infrastructure errors are PLATFORM-STD catalog rows
Status      : ACCEPTED (amended at gate-analysis revise 3, G1)
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-138, AC-INV-154, REQ-INV-072, API-INV-001 … API-INV-052

## Context
The engine requires infrastructure errors (not found, forbidden, server) to be Error Catalog rows with
RULE = PLATFORM-STD and an ADR. The SRS gives a text only for the forbidden case (AC-INV-154). Optimistic
locking on VERSION (ADR-INV-014) and malformed requests also produce errors that no RULE covers.

Amendment (gate-analysis revise 3, G1): the first version deferred every text but INV-403 to the erp-core
catalogue. The module-specific codes — the `INV-404-*-NOT-FOUND` codes, INV-404-LOOKUP-KEY-UNKNOWN and the
load wrapper INV-422-LOAD-ROWS-INVALID — do not exist there, so `InvErrorCodes`, `messages_ar` and
`messages_en` would have held codes with no text (erp-core checklist C11).

## Decision
The catalog carries these PLATFORM-STD rows, every code an instance of `{MOD}-{http}[-{SLUG}]`:
INV-400 (malformed request), INV-401 (unauthenticated — the given security filter), INV-403 (forbidden —
message of AC-INV-154 verbatim), INV-404-{WAREHOUSE|BRANCH|VARIANT|ITEM|STOCK-MOVE|TRANSFER|STOCK-COUNT|
STOCK-COUNT-LINE|STOCK-ADJUSTMENT|LOT}-NOT-FOUND, INV-404-LOOKUP-KEY-UNKNOWN, INV-409-STALE-VERSION
(OptimisticLockException) and INV-500.

**Who owns the texts.** INV owns, and the Error Catalog (§5) states in Arabic and English, the text of every
module-specific code: INV-403 (AC-INV-154), each `INV-404-*-NOT-FOUND`, INV-404-LOOKUP-KEY-UNKNOWN and
INV-422-LOAD-ROWS-INVALID (REQ-INV-072 — the wrapper; each `details` entry still carries its row's own code and
text). Only the generic platform codes INV-400, INV-401, INV-500 and INV-409-STALE-VERSION reuse the platform's
own `LocalizedException` texts from the erp-core message catalog; INV invents no text for them.

Translation of exceptions is central (CORE): constraint-name mapping for UNIQUE violations, optimistic lock →
INV-409-STALE-VERSION, anything else → INV-500.

## Consequences
- Every operation of api-spec-inv.yaml answers 400, 401, 403 and 500 with these codes.
- `InvErrorCodes` and the message bundle hold a text for every INV code; the implementer takes only the four
  generic texts from the erp-core message catalog.
- The test plan asserts the texts of the not-found codes, the lookup-key code and the load wrapper
  (TC-INV-276, TC-INV-284, TC-INV-021, TC-INV-148, TC-INV-257, TC-INV-275), and the code and status only of the
  four generic platform codes.

## Status
ACCEPTED — non-breaking.
