<!-- source: PHASE:TEST-PLAN-BE / SUB:REVISE3-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-155, AC-INV-156, AC-INV-157, AC-INV-158, API-INV-016, API-INV-018, API-INV-024, API-INV-032, API-INV-034, API-INV-036, API-INV-038, API-INV-045, API-INV-047, REQ-INV-069, REQ-INV-139, REQ-INV-140 -->
<!-- SUB:REVISE3-SCENARIOS:START traces=AC-INV-155,AC-INV-156,AC-INV-157,AC-INV-158,REQ-INV-139,REQ-INV-069,REQ-INV-140 -->
### REVISE3-SCENARIOS — lot-less stock, offline sales on inactive records, opening stock idempotency (gate-analysis revise 3)

<!-- TC:TC-INV-286:START traces=AC-INV-155,AC-INV-156,REQ-INV-139,API-INV-045,API-INV-032,API-INV-024,API-INV-036,API-INV-038,API-INV-016,API-INV-018 -->
### TC-INV-286 — Stock held without a lot is counted, transferred and written off after lot tracking is switched on
Derived from : AC-INV-155, AC-INV-156  (REQ-INV-139)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · API-INV-028 / 029 / 032 (count) · API-INV-022 / 024 (transfer) · API-INV-036 / 038 (adjustment) · read back API-INV-016, API-INV-018
Rule / code  : RULE-INV-037 (lot-less exception — ADR-INV-051) → INV-422-LOT-REQUIRED beyond the lot-less on-hand
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Owner and Warehouse user sessions; lotExpiry on; 2 active Warehouses W1, W2; Item I with no Item stock settings and its Variant V on-hand 5 without a lot in W1 (valued); INV_ALLOW_NEGATIVE_STOCK false
Host data    : none
Steps        : 1. As Owner, PUT I's settings with lotTrackedFl true (no lot holds stock — allowed). 2. Start a count in W1, add a line of V with no lot and countedQty 6, then change it to 4, and post. 3. POST a transfer W1 → W2 with 1 line of V, no lot, quantity 2, and post it. 4. POST an adjustment in W1 with reason DAMAGE and 1 line of V, no lot, −3. 5. POST it again with −2 and post it. 6. GET balances of V and moves of V.
Expected     : 1 → 200. 2 → countedQty 6 is refused with 422, code INV-422-LOT-REQUIRED, messageAr «اختر التشغيلة لهذا الصنف.», messageEn "Choose the lot for this Item." (6 > 5 lot-less); countedQty 4 is accepted; the count posts with systemQty 5, differenceQty −1, and its COUNT_DIFFERENCE adjustment has 1 line (V, no lot, −1). 3 → 201, then 200 POSTED: TRANSFER_OUT of 2 with no lot in W1, TRANSFER_IN of 2 with no lot in W2. 4 → 422, code INV-422-LOT-REQUIRED with the same texts (3 > 2 lot-less remaining in W1); nothing saved. 5 → 201, then 200 POSTED: ADJUSTMENT_OUT of 2 with no lot. 6 → V on-hand without a lot is 0 in W1 and 2 in W2; no lot was created; every move has lotPk null.
Test data    : reasonCode DAMAGE (seeded); lot-less on-hand 5 → 4 → 2 → 0 in W1
<!-- TC:TC-INV-286:END -->

<!-- TC:TC-INV-287:START traces=AC-INV-157,REQ-INV-069,API-INV-018 -->
### TC-INV-287 — An offline sale naming an inactive Variant or warehouse, or no warehouse, is recorded and reported
Derived from : AC-INV-157  (REQ-INV-069)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS, offlineFl true) · notification INV_OFFLINE_SALE_INACTIVE · read back API-INV-018
Rule / code  : RULE-INV-007, RULE-INV-008, RULE-INV-018 (offline exception — ADR-INV-052) → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: INV_ALLOW_NEGATIVE_STOCK false; active Warehouses W (default) and W3, inactive Warehouse W2; Variant V deactivated in CAT after the POS sold it, active Variant V2; 2 users holding the Warehouse role
Host data    : none
Steps        : 1. SLS synchronises offline sale P-77: issueStock with offlineFl true, 1 of V in W. 2. SLS synchronises offline sale P-78: 1 of V2 in W2. 3. SLS synchronises offline sale P-79: 1 of V2 with no warehouse. 4. SLS submits an online issue (offlineFl false) of 1 of V in W. 5. GET moves of V and V2; read each Warehouse-role user's notifications.
Expected     : 1, 2, 3 → success, each recording 1 ISSUE move with offlineFl true — P-77 in W, P-78 in W2, P-79 in the default warehouse W; no INV-422-VARIANT-INACTIVE, INV-422-WAREHOUSE-INACTIVE or INV-422-WAREHOUSE-REQUIRED is thrown. 4 → code INV-422-VARIANT-INACTIVE, messageAr «متغيّر الصنف غير نشط.», messageEn "The Variant is inactive."; no move. 5 → 3 moves as above; each user received exactly 2 notifications INV_OFFLINE_SALE_INACTIVE — «سُجّل بيع دون اتصال P-77 على {V} غير النشط. راجع المخزون وسوِّه.» / "Offline sale P-77 was recorded against the inactive {V}. Review and adjust the stock." and the same for P-78 naming W2 — and none for P-79.
Test data    : source document type POS_SALE, numbers P-77, P-78, P-79; three distinct idempotency keys
<!-- TC:TC-INV-287:END -->

<!-- TC:TC-INV-288:START traces=AC-INV-158,REQ-INV-140,API-INV-047,API-INV-034,API-INV-016 -->
### TC-INV-288 — A double-submitted opening stock load posts once; a different load with the same key is refused
Derived from : AC-INV-158  (REQ-INV-140)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock (twice at once, then again) · read back API-INV-034, API-INV-016
Rule / code  : RULE-INV-024 → — · RULE-INV-050 → INV-409-IDEMPOTENCY-KEY-REUSED
Package      : SVC-API-CRUD
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: Owner session; one active Warehouse; Variants V and V3 with no stock and active barcodes; no load recorded with key K-OS-1 or K-OS-2
Host data    : none
Steps        : 1. Send the load {idempotencyKey K-OS-1, rows [(V, 40, 12.5000)]} twice at once. 2. Send {idempotencyKey K-OS-1, rows [(V, 50, 12.5000)]}. 3. Send {idempotencyKey K-OS-2, rows [(V3, 10, -1.0000)]}, then {idempotencyKey K-OS-2, rows [(V3, 10, 1.0000)]}. 4. GET adjustments with reasonCode OPENING_STOCK and the balances of V and V3.
Expected     : 1 → both answer 201 with the same stockAdjustmentPk (the second waits on the advisory lock, then returns the first answer). 2 → 409, code INV-409-IDEMPOTENCY-KEY-REUSED, messageAr «مفتاح عدم التكرار مستخدم لطلب آخر. استخدم مفتاحًا جديدًا لهذا الطلب.», messageEn "This idempotency key was already used for a different request. Use a new key for this request."; nothing recorded. 3 → the first is refused with 422 INV-422-LOAD-ROWS-INVALID (row 1 INV-422-OPENING-COST-REQUIRED) and records nothing, so the corrected load with the same key K-OS-2 answers 201. 4 → exactly 2 OPENING_STOCK adjustments; V on-hand 40 (one ADJUSTMENT_IN of 40), V3 on-hand 10.
Test data    : keys K-OS-1, K-OS-2
<!-- TC:TC-INV-288:END -->
<!-- SUB:REVISE3-SCENARIOS:END -->
