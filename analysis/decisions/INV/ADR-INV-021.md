# ADR-INV-021 — Infrastructure errors are PLATFORM-STD catalog rows
Status      : ACCEPTED (amended at gate-analysis revise 3, G1, and revise 4, G3)
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

Amendment (gate-analysis revise 4, G3): revise 3 still deferred INV-400, INV-401, INV-500 and INV-409-STALE-VERSION
to platform texts, but the platform catalog holds no text under these INV-prefixed codes either, so `InvErrorCodes`
would have shipped four codes with no EN or AR text (checklist C11: every code has an EN and an AR message).

## Decision
The catalog carries these PLATFORM-STD rows, every code an instance of `{MOD}-{http}[-{SLUG}]`:
INV-400 (malformed request), INV-401 (unauthenticated — the given security filter), INV-403 (forbidden —
message of AC-INV-154 verbatim), INV-404-{WAREHOUSE|BRANCH|VARIANT|ITEM|STOCK-MOVE|TRANSFER|STOCK-COUNT|
STOCK-COUNT-LINE|STOCK-ADJUSTMENT|LOT}-NOT-FOUND, INV-404-LOOKUP-KEY-UNKNOWN, INV-409-STALE-VERSION
(OptimisticLockException) and INV-500.

**Who owns the texts.** INV owns, and the Error Catalog (§5) states in Arabic and English, the text of every
code INV raises: INV-403 (AC-INV-154), each `INV-404-*-NOT-FOUND`, INV-404-LOOKUP-KEY-UNKNOWN,
INV-422-LOAD-ROWS-INVALID (REQ-INV-072 — the wrapper; each `details` entry still carries its row's own code and
text) and — amended at gate-analysis revise 4, G3 — the four generic codes too, because the erp-core message
catalog holds no text under an INV-prefixed code:

| Code | ar | en |
|---|---|---|
| INV-400 | طلب غير صالح. | The request is invalid. |
| INV-401 | يلزم تسجيل الدخول. | Authentication is required. |
| INV-500 | حدث خطأ غير متوقع. حاول مرة أخرى. | An unexpected error occurred. Try again. |
| INV-409-STALE-VERSION | تغيّر السجل منذ قراءته. أعد التحميل. | The record changed since you read it. Reload. |

Every text of §5 and §5.1 is copied verbatim into the module's message bundles `messages.properties` (EN) and
`messages_ar.properties` (AR), the names erp-core checklist C11 uses; `InvErrorCodes` holds the codes.

Translation of exceptions is central (CORE): constraint-name mapping for UNIQUE violations, optimistic lock →
INV-409-STALE-VERSION, anything else → INV-500.

## Consequences
- Every operation of api-spec-inv.yaml answers 400, 401, 403 and 500 with these codes.
- `InvErrorCodes` and the bundles `messages.properties` / `messages_ar.properties` hold an EN and an AR text for
  every INV code; nothing is taken from the erp-core message catalog.
- The test plan asserts the texts of the not-found codes, the lookup-key code and the load wrapper
  (TC-INV-276, TC-INV-284, TC-INV-021, TC-INV-148, TC-INV-257, TC-INV-275) and, since revise 4, of the four generic
  codes (TC-INV-269, TC-INV-270, TC-INV-271 for INV-409-STALE-VERSION; TC-INV-277 for INV-400, INV-401, INV-500).

## Status
ACCEPTED — non-breaking.
