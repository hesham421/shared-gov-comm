# ADR-INV-047 — A system-generated adjustment does not check that its reason is tenant-active
Status      : ACCEPTED
Stage       : gate analysis (revise 2, G3, G18)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : RULE-INV-033, REQ-INV-070, REQ-INV-092, REQ-INV-101, API-INV-032, API-INV-036, API-INV-037, API-INV-038, API-INV-047

## Context
INV_ADJUSTMENT_REASON is an open lookup that a tenant may edit. Two of its seeded values are written by the system:
COUNT_DIFFERENCE by a count posting (REQ-INV-092) and OPENING_STOCK by an opening stock load (REQ-INV-070). The
SRS said they "stay active", but nothing enforced this. If `StockAdjustmentService.createAndPost` ran RULE-INV-033,
a tenant that deactivated COUNT_DIFFERENCE could no longer post any count, and API-INV-032 would answer a code it
does not list. Separately, the Warehouse role could save an adjustment with reason OPENING_STOCK on the adjustment
screen (API-INV-036, 037) and post it (API-INV-038), bypassing PERM_INV_OPENING_STOCK_CREATE, which only the Owner
holds.

## Decision
1. **System-written reasons are not validated against the tenant's active values.** `createAndPost` (reached by
   API-INV-032 and API-INV-047) writes COUNT_DIFFERENCE or OPENING_STOCK as constants and does not call the
   RULE-INV-033 active check. A deactivated value therefore never blocks a count or an opening stock load.
2. **The adjustment screen cannot use OPENING_STOCK.** API-INV-036 and 037 refuse reasonCode OPENING_STOCK with
   INV-422-REASON-REQUIRED (RULE-INV-033). API-INV-038 re-checks the stored reason with the same rule, so a draft
   cannot carry OPENING_STOCK to a post. The frontend does not offer the value on SCR-INV-007.
3. The remaining gap — a tenant can still hide a system value from its own lists — is a core matter: PLATFORM
   FINDINGS row 5 in business-policies-inv asks `mdl` for seeded values the tenant cannot deactivate.

## Consequences
- RULE-INV-033 states both parts; A6 no longer claims "stay active".
- Test cases: a count posts with COUNT_DIFFERENCE inactive (in its own tenant); OPENING_STOCK is refused on
  API-INV-036 and 037.
- Opening stock stays an Owner action (PERM_INV_OPENING_STOCK_CREATE).

## Status
ACCEPTED — non-breaking.
