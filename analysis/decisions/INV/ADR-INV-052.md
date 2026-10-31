# ADR-INV-052 — A synchronised offline sale skips the active-state checks and is always recorded
Status      : ACCEPTED
Stage       : gate analysis (revise 3, G3)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-069, RULE-INV-007, RULE-INV-008, RULE-INV-018, RULE-INV-019, POL-INV-020, CON-INV-015, AC-INV-157, ADR-INV-045

## Context
POL-INV-020 and CON-INV-015 promise that a synchronised offline POS sale is always recorded: the goods have already
left. REQ-INV-069 and RULE-INV-019 exempted such a sale only from the negative-stock check. The same ISSUE was still
refused when, between the sale and its synchronisation, the warehouse was deactivated (RULE-INV-007), the Variant or
its Item was deactivated in CAT (RULE-INV-018), or a second warehouse was added and the sale names none
(RULE-INV-008). The sale was lost.

ADR-INV-045 made the engine re-validate a DRAFT on post. This ADR extends it with the opposite case: a request whose
facts are already final.

## Decision
For an issue with offlineFl true (CON-INV-015):
1. RULE-INV-007 is not applied: a named inactive warehouse receives the move.
2. RULE-INV-008: when the sale names no warehouse, the default warehouse is applied whatever the number of active
   warehouses.
3. RULE-INV-018 is not applied for an inactive Variant or Item (a bundle Item on a receipt is unrelated: an offline
   sale is an ISSUE).
4. RULE-INV-019 stays skipped (REQ-INV-069).
5. When a check was skipped because a record is inactive, one notification `INV_OFFLINE_SALE_INACTIVE` is sent after
   commit to the users holding the Warehouse role, naming the inactive record (warehouse code and name, or Variant
   code and name) and the sale's document number, so the Owner can reactivate it or adjust the stock.

The idempotency guard (RULE-INV-024, RULE-INV-050), quantity format (RULE-INV-017) and every other check still apply.

## Consequences
- SRS: REQ-INV-069 extended, AC-INV-157 added; RULE-INV-007, 008, 018 state the offline exception.
- db-script BLOCK 8d seeds the template INV_OFFLINE_SALE_INACTIVE (templates 2 → 3).
- Backend plan: SVC-API-INT "Common resolution" and "Issue"; bootstrap row for the template.
- An inactive warehouse may now hold a (usually negative) balance; it stays inactive until the Owner reactivates and
  adjusts it.
- Test: TC-INV-287 synchronises an offline sale of a Variant deactivated after the sale.

## Status
ACCEPTED — non-breaking (no consumer is built yet).
