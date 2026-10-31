# ADR-INV-048 — The idempotency key is recorded with a hash of its request
Status      : ACCEPTED
Stage       : gate analysis (revise 2, G9)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : RULE-INV-024, RULE-INV-050, REQ-INV-058, ENT-INV-004, ENT-INV-005, DBF-INV-052, DBF-INV-075, DBF-INV-220, DBF-INV-221, CON-INV-011, CON-INV-014, CON-INV-015, ADR-INV-044, D-P2-05

## Context
RULE-INV-024 returns the first result whenever an idempotency key repeats. A caller that reuses a key for a
different request (another Variant, another quantity, another document) silently gets the first answer and
believes its new request was recorded.

## Decision
1. **A request hash is stored with the key.** INV_STOCK_MOVE and INV_RESERVATION gain `REQUEST_HASH VARCHAR(64) NOT
   NULL` (DBF-INV-220, DBF-INV-221; ENT-INV-004.requestHash, ENT-INV-005.requestHash): the lower-case hex
   SHA-256 of the request's canonical form. The canonical form is the operation name, the move type, the sale
   and offline flags, the warehouse, the source document (type, reference, number) and the lines in their
   request order, each with its Variant, quantity, measure, unit cost, lot or lot number and expiry, reservation
   and source line — written as JSON with fixed key order and plain decimal strings. Every row a request records
   carries the same hash.
2. **INV's own documents** (transfer, count, adjustment, opening stock) record moves with the key
   `<sourceDocTypeCode>:<sourceDocRef>` (D-P2-05); their hash is the SHA-256 of that key, so the column is always
   filled.
3. **The check.** After the advisory lock of ADR-INV-044, the R-2 / R-6 lookup by key reads the stored hash:
   the same hash → the first result (RULE-INV-024); a different hash → refusal `INV-409-IDEMPOTENCY-KEY-REUSED`
   (RULE-INV-050), raised in-process only (§5.1, `x-inprocess-error-codes`).
4. IDEMPOTENCY_KEY stays a non-unique index (D-P2-05); no new index is needed, because the hash is read from the
   rows the key lookup already returns.

## Consequences
- db-script: two DBF ids added at the end of the sequence (DBF 219 → 221), two columns, two comments.
- Contract CON-INV-011, 014, 015 add the error meaning "idempotency key already used for a different request"
  (ADDITIVE).
- A test case reuses a key with a different quantity and asserts the refusal and that nothing is recorded.

## Status
ACCEPTED — non-breaking (no consumer is built yet).
