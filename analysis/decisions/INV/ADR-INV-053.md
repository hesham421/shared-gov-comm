# ADR-INV-053 — An opening stock load carries an idempotency key
Status      : ACCEPTED (amended at gate-analysis revise 4, G5)
Stage       : gate analysis (revise 3, G4)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-140, REQ-INV-070, RULE-INV-024, RULE-INV-050, API-INV-047, AC-INV-158, DBF-INV-052, DBF-INV-220, ADR-INV-027, ADR-INV-044, ADR-INV-048, D-P2-05

## Context
API-INV-047 creates and posts opening stock in one call (ADR-INV-027). Its concurrency note accepted that a
resubmitted load creates a second adjustment and relied on the screen disabling the button. A client retry after a
timeout, or a double submit, would double the stock and its value. Moves are immutable (RULE-INV-023), so the only
correction would be a manual counter-adjustment.

## Decision
1. `OpeningStockRequest` requires `idempotencyKey` (text 1–50). The screen generates it once per load
   (`crypto.randomUUID()`), keeps it across retries of the same load and replaces it after a successful post.
2. The moves of the load carry `IDEMPOTENCY_KEY = OPENING_STOCK:<idempotencyKey>` (≤ 64 characters, DBF-INV-052) and
   `REQUEST_HASH` = the SHA-256 of the load's canonical form — warehouse, docDate and the rows in rowNo order, each
   with its variantPk or barcode, quantity, unit cost, lot number, expiry and measure (DBF-INV-220). The hash is
   taken over the request as submitted (amended at gate-analysis revise 4, G5): an absent warehousePk or docDate
   stays absent — it is never replaced by the default warehouse or by today before hashing — and no field is
   trimmed and no barcode is resolved before hashing, so a load retried after midnight or after the default
   warehouse changes still matches its first submission.
3. The guard is the one of ADR-INV-044 / ADR-INV-048: `pg_advisory_xact_lock(hashtextextended(:tenantId || ':' ||
   'OPENING_STOCK:' || :idempotencyKey, 0))`, then the R-6 lookup by key. The same hash → the first answer, 201 with
   the adjustment the first load posted (from the moves' sourceDocRef), nothing recorded (RULE-INV-024). A different
   hash → 409 `INV-409-IDEMPOTENCY-KEY-REUSED` (RULE-INV-050), nothing recorded. A load refused with 422 records
   nothing, so its key stays free.
4. INV-409-IDEMPOTENCY-KEY-REUSED therefore becomes an HTTP catalog row (§5, API-INV-047) and leaves the in-process-only
   list (§5.1, `x-inprocess-error-codes`); the in-process receive, issue and reserve still raise it.
5. The prefix keeps the load's keys apart from the keys cross-module callers send.

## Consequences
- SRS: new REQ-INV-140, AC-INV-158; RULE-INV-024 and RULE-INV-050 name the opening stock load.
- api-spec-inv.yaml: `idempotencyKey` on OpeningStockRequest; a 409 response on API-INV-047.
- db-script D-P2-05 notes the exception to the document-derived key; no column changes.
- Test: TC-INV-288 submits one load twice at once and then with a different row, and asserts one posting and the 409.

## Status
ACCEPTED — non-breaking (no consumer is built yet).
