# ADR-CAT-071 — Item import: time budget, batched code allocation, one scale-prefix lock per import

Status : ACCEPTED (non-breaking) · Stage : P3.1 (gate-analysis round 3, G7) · Module : CAT v2 · Date : 2026-10-28 · Raises : CORE-GAP-number-series-block-allocation.md

## Context
API-CAT-049 imports up to 5,000 rows synchronously, in one transaction. It drew the Item and Variant codes row by row and held the tenant's scale-prefix lock for the whole write pass. The plan stated no time budget and no timeout, and did not say what happens when the import runs too long.

## Decision
1. **Time budget.** A file of 5,000 valid rows completes in at most 60 s on the reference deployment: at most 15 s for the validation pass and 45 s for the write pass.
2. **Timeout.** The service transaction carries `@Transactional(timeout = 120)`, and the endpoint's request timeout is 120 s.
3. **When the timeout is exceeded**, the transaction rolls back as a whole:
   - no Item, Variant or barcode is stored, and no catalogue-change event is delivered (RULE-CAT-049);
   - the codes already drawn stay as gaps in the series, as the given's REQUIRES_NEW allocation defines;
   - the scale-prefix lock is released;
   - the answer is `CAT-500-CHANGE-NOT-SAVED`, and the owner imports the file again, split into smaller files.
4. **Batches of 500 rows.** The write pass draws a batch's Item and Variant codes before inserting it, then inserts the batch with JDBC batching under one savepoint. On a unique violation, the batch rolls back to its savepoint and is replayed row by row, each row with its own savepoint, reusing the drawn codes. Only the row that lost a race is reported rejected (RULE-CAT-003 / 029).
5. **Code allocation.** erp-core 1.6.0 publishes `NumberSeriesApi.next(code)` only, with no block allocation. Until the core answers `CORE-GAP-number-series-block-allocation.md`, each code of a batch is one `next` call. When a block call exists, one call per batch replaces them, with no change to the endpoint.
6. **One scale-prefix lock per import.** The lock is taken once, at the start of the write pass, and only when the file holds a GTIN row. The active layouts are re-read under it and the GTIN rows re-checked in memory. It is held until commit, so a layout create (API-CAT-032) waits at most the import's time budget.

## Consequences
- API-CAT-049's Orchestration and Concurrency lines carry the budget, the batches, the lock and the timeout behaviour. No schema, endpoint or code changes.

## Traces
REQ-CAT-037 · REQ-CAT-038 · REQ-CAT-040 · REQ-CAT-041 · RULE-CAT-005 · RULE-CAT-032 · RULE-CAT-049 · API-CAT-049 · API-CAT-032 · CORE-GAP-number-series-block-allocation
