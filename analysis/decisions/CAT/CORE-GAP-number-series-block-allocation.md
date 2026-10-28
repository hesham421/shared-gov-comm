# CORE-GAP-number-series-block-allocation — Can NumberSeriesApi allocate a block of numbers in one call?

Status : OPEN (asked of erp-core 1.6.0) · Raised by : CAT v2 (gate-analysis round 3, G7) · Date : 2026-10-28
Decision that depends on it : ADR-CAT-071

## The gap
`NumberSeriesApi.next(code)` allocates one number atomically, in its own `REQUIRES_NEW` transaction. CAT's item import (API-CAT-049) creates up to 5,000 Items, each with a default Variant, so it needs up to 10,000 codes in one request. The given offers no call that allocates n consecutive numbers at once, and the conventions forbid a module counter (checklist F6).

## What CAT does meanwhile (ADR-CAT-071)
The import draws the codes of each batch of 500 rows before inserting the batch, one `next` call per code. It stays inside the time budget of ADR-CAT-071, at the cost of one small transaction per code.

## Question to the core
Can `NumberSeriesApi` offer `nextBlock(code, n)`, returning n consecutive numbers allocated atomically in one transaction, with the same gap semantics as `next`?

## Impact when answered
CAT replaces the n calls per batch with one call. No endpoint, schema or code format changes.

## Traces
API-CAT-049 · RULE-CAT-005 · ADR-CAT-071
