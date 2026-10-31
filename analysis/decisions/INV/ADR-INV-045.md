# ADR-INV-045 — A DRAFT is validated again when it is posted; a warehouse with DRAFT documents stays active
Status      : ACCEPTED
Stage       : gate analysis (revise 1, G8)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : RULE-INV-004, RULE-INV-007, RULE-INV-008, RULE-INV-016, RULE-INV-018, RULE-INV-037, RULE-INV-040, REQ-INV-006, REQ-INV-080, REQ-INV-092, REQ-INV-099, API-INV-005, API-INV-024, API-INV-032, API-INV-038, QR-INV-002, QR-INV-007

## Context
A DRAFT transfer, count or adjustment is saved, then posted later. The post re-checked only some rules
(RULE-INV-016, 033, 034). Between save and post the warehouse can be deactivated, the Variant or Item deactivated
in CAT, or the Item marked lot-tracked or dual-quantity. The post could then move stock into an inactive warehouse,
or move a lot-tracked Item without a lot. RULE-INV-004 also ignored DRAFT documents, so a warehouse could be
deactivated under them and they were stranded.

## Decision
1. **Posting engine step 0.** For an INV document, after the locks of step 1, the engine re-runs on the header and on
   every line RULE-INV-007, 008, 016, 018, 037 and 040, with the same codes as at save. The six RULEs gain the
   trigger "on post" (SRS A5, plan §1.3). The in-process receive / issue already run them when they resolve.
2. **RULE-INV-004** also refuses a deactivation while a DRAFT Transfer (source or destination), Stock count or Stock
   adjustment names the warehouse, with the existing INV-409-WAREHOUSE-IN-USE text ("Transfer or adjust the stock
   first" — the Owner posts or cancels the draft). QR-INV-007 adds three single-table probes.
3. **No race.** Deactivation locks the warehouse FOR UPDATE before the probe; every posting path and every DRAFT
   save reads it FOR SHARE (QR-INV-002), so no draft or move slips in between.

## Consequences
- API-INV-024, 032 and 038 can answer INV-422-WAREHOUSE-INACTIVE (count, adjustment) or
  INV-422-TRANSFER-WAREHOUSES (transfer), INV-422-VARIANT-INACTIVE, INV-422-LOT-REQUIRED and, for transfers and
  counts, INV-422-MEASURE-REQUIRED; the API document lists them.
- A warehouse can be deactivated only after its drafts are posted or cancelled.
- Backend test cases cover a post after the warehouse, the Variant or the Item stock settings changed, and the
  deactivation refused by a DRAFT.

## Status
ACCEPTED — non-breaking.
