<!-- source: PHASE:TEST-PLAN-BE / SUB:REVISE5-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-051, AC-INV-083, AC-INV-102, AC-INV-160, API-INV-016, API-INV-017, API-INV-018, API-INV-022, API-INV-024, API-INV-032, API-INV-035, API-INV-040, API-INV-047, REQ-INV-046, REQ-INV-076, REQ-INV-092, REQ-INV-141 -->
<!-- SUB:REVISE5-SCENARIOS:START traces=AC-INV-102,AC-INV-083,AC-INV-051,AC-INV-160,REQ-INV-092,REQ-INV-076,REQ-INV-046,REQ-INV-141 -->
### REVISE5-SCENARIOS — several lots on one count, posting-order balance, release retry, lot expired on receipt (gate-analysis revise 5)

<!-- TC:TC-INV-291:START traces=AC-INV-102,REQ-INV-092,API-INV-032,API-INV-035,API-INV-016 -->
### TC-INV-291 — A count holding two lots of one Variant posts one line per lot
Derived from : AC-INV-102  (REQ-INV-092)
Exercises    : API-INV-032 PATCH /api/v1/inv/stock-counts/{id}/post · read back API-INV-035, API-INV-016
Rule / code  : RULE-INV-048 (not raised — the generated lines differ by lotNo) → —
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Warehouse user session; lotExpiry on; Variant V of a lot-tracked Item, valued, in Warehouse W with Lot L1 (system quantity 5) and Lot L2 (system quantity 4); a DRAFT count in W with a line of V in L1 countedQty 7 and a line of V in L2 countedQty 2, read with its version
Host data    : none
Steps        : 1. PATCH the count /post. 2. GET the resulting adjustment. 3. GET balances of V in W.
Expected     : 1 → 200; count POSTED with L1 systemQty 5, differenceQty 2 and L2 systemQty 4, differenceQty −2; no INV-422-DUPLICATE-LINE and no INV-500. 2 → exactly 1 POSTED adjustment with reasonCode COUNT_DIFFERENCE and 2 lines: (V, lotPk L1, lotNo L1, quantityChange +2) and (V, lotPk L2, lotNo L2, quantityChange −2), each with lotExpiryDate empty; 1 ADJUSTMENT_IN of 2 under L1 and 1 ADJUSTMENT_OUT of 2 under L2. 3 → L1 on-hand 7 and L2 on-hand 2.
Test data    : L1 counted 7 against 5, L2 counted 2 against 4 (gate-analysis revise 5, G1, G2)
<!-- TC:TC-INV-291:END -->

<!-- TC:TC-INV-292:START traces=AC-INV-083,REQ-INV-076,API-INV-022,API-INV-024,API-INV-018 -->
### TC-INV-292 — A backdated transfer keeps the running balance in posting order
Derived from : AC-INV-083  (REQ-INV-076)
Exercises    : API-INV-022 POST, API-INV-024 PATCH /api/v1/inv/transfers/{id}/post · read back API-INV-018 GET /api/v1/inv/stock-moves
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Warehouse user session; tenant clock 2026-10-26; 2 active Warehouses W1, W2; Variant V with, in W1, a RECEIPT of 10 dated 2026-10-20 posted first and an ISSUE of 4 dated 2026-10-25 posted second
Host data    : none
Steps        : 1. POST a transfer W1 → W2 with docDate 2026-10-22 and 1 line of 2 of V, then PATCH it /post. 2. GET moves with variantPk V and warehousePk W1. 3. GET moves with variantPk V only.
Expected     : 2 → 3 rows in posting order, newest posted first: TRANSFER_OUT 2 (docDate 2026-10-22, balanceAfterQty 4), ISSUE 4 (2026-10-25, balanceAfterQty 6), RECEIPT 10 (2026-10-20, balanceAfterQty 10) — each balance follows the one below it. 3 → no balance column; rows newest first by document date: the ISSUE (2026-10-25), then the TRANSFER_OUT and TRANSFER_IN (2026-10-22), then the RECEIPT (2026-10-20).
Test data    : backdated docDate 2026-10-22 (gate-analysis revise 5, G3)
<!-- TC:TC-INV-292:END -->

<!-- TC:TC-INV-293:START traces=AC-INV-051,REQ-INV-046 -->
### TC-INV-293 — A release retried after it succeeded answers the released reservation
Derived from : AC-INV-051  (REQ-INV-046)
Exercises    : InventoryApi.releaseReservation — CON-INV-012 · InventoryApi.readAvailability — CON-INV-010 (in-process, caller SFR)
Rule / code  : RULE-INV-021 (a RELEASED reservation is returned unchanged — ADR-INV-055) → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: an ACTIVE Reservation R of 4 of V in W; V available 6 in W
Host data    : none
Steps        : 1. SFR releases R and keeps the answer. 2. SFR releases R again (a retry after a lost answer). 3. SFR reads the availability of V in W.
Expected     : 1 → R RELEASED, reservedQty 0, closedAt t1. 2 → success with no error: R RELEASED, reservedQty 0, closedAt t1 (unchanged), and no INV-409-RESERVATION-CLOSED. 3 → available 10 (released once, not twice).
Test data    : — (gate-analysis revise 5, G4)
<!-- TC:TC-INV-293:END -->

<!-- TC:TC-INV-295:START traces=AC-INV-160,REQ-INV-141,API-INV-047,API-INV-040,API-INV-017,API-INV-018 -->
### TC-INV-295 — A lot received already past its expiry is recorded and never sold
Derived from : AC-INV-160  (REQ-INV-141)
Exercises    : InventoryApi.receiveStock — CON-INV-014 · InventoryApi.issueStock — CON-INV-015 (in-process, callers PUR and SLS) · API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-040, API-INV-017, API-INV-018
Rule / code  : RULE-INV-038 (a lot created with a past expiry is expired from creation — ADR-INV-056) → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: lotExpiry on; tenant clock 2026-11-01; lot-tracked Variant V with unexpired Lot L2 (expiry 2026-12-01, on-hand 5) in Warehouse W and no lot L-OLD; Variant V3 lot-tracked with an active barcode B3 and no stock
Host data    : none
Steps        : 1. PUR receives 6 of V naming lot L-OLD with expiry 2026-10-25. 2. GET lots of V. 3. GET the availability of V in W. 4. SLS issues 3 of V from W for a sale. 5. The Owner posts opening stock with 1 row: barcode B3, quantity 4, unit cost 10.0000, lot L-PAST, expiry 2026-10-30. 6. GET lots of V3.
Expected     : 1 → 1 RECEIPT move of 6 under L-OLD; no INV-422-LOT-EXPIRY-REQUIRED, no other refusal. 2 → L-OLD listed with expiryDate 2026-10-25, onHandQty 6 and expiredFl true, beside L2. 3 → on-hand 11, available 5 (L-OLD excluded). 4 → 1 ISSUE of 3 from L2; L-OLD stays at 6. 5 → 201, a POSTED OPENING_STOCK adjustment with 1 ADJUSTMENT_IN of 4 under L-PAST. 6 → L-PAST listed with expiredFl true.
Test data    : L-OLD expiry 2026-10-25, L-PAST expiry 2026-10-30 (gate-analysis revise 5, G8)
<!-- TC:TC-INV-295:END -->
<!-- SUB:REVISE5-SCENARIOS:END -->
