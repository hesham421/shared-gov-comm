# ADR-INV-056 — A lot received with an expiry already before today is accepted and is expired from creation
Status      : ACCEPTED
Stage       : gate analysis (revise 5, G8)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-141, AC-INV-160, REQ-INV-105, REQ-INV-106, REQ-INV-113, RULE-INV-035, RULE-INV-038, RULE-INV-039, ENT-INV-012, DBF-INV-177, API-INV-038, API-INV-047, CON-INV-014, TC-INV-295

## Context
REQ-INV-105 and RULE-INV-035 require a lot number and an expiry date on an incoming line of a lot-tracked Item, and
REQ-INV-106 creates the lot when it is new. Nothing stated what happens when the expiry date entered is already before
today in the tenant's time zone — a receipt of goods that are already expired, or a late-keyed receipt, or opening
stock of an expired lot that is physically on the shelf.

Options: (a) refuse the line — the goods stay physically in the warehouse but disappear from the books, and the
receipt document of PUR cannot be completed; (b) accept the line and treat the lot as expired from its creation.

## Decision
Option (b).
1. A receipt (CON-INV-014), an increase line of a stock adjustment (API-INV-038) or an opening stock row
   (API-INV-047) that names a new lot whose expiry date is before today is recorded; the lot is created with that
   expiry date. No refusal code is added and none is raised.
2. From its creation the lot is expired under RULE-INV-038: it is shown with expiredFl true («منتهية الصلاحية» /
   "Expired"), it is excluded from sale issue, FEFO selection and available quantity, and a non-sale issue or return to
   supplier naming it is refused by RULE-INV-039. It leaves stock through a stock adjustment (EXPIRY_WRITE_OFF).
3. A line naming an existing lot keeps RULE-INV-036 (its expiry must equal the stored one), whatever that date is.
4. The near-expiry list (REQ-INV-115, 116) shows the lot as expired while it holds stock.

## Consequences
- SRS: new REQ-INV-141 with AC-INV-160; RULE-INV-038 states the case.
- Backend plan: §1.3 RULE-INV-038, engine step 3, SVC-API-INT "Receive", API-INV-038 and API-INV-047.
- Test: TC-INV-295 receives a lot dated last week, checks it is listed as expired, excluded from availability and
  never drawn by a sale, and posts an opening stock row with a past expiry.

## Status
ACCEPTED — non-breaking.
