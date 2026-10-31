<!-- source: PHASE:TEST-PLAN-BE / SUB:REVISE-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-008, AC-INV-010, AC-INV-040, AC-INV-042, AC-INV-044, AC-INV-052, AC-INV-062, AC-INV-064, AC-INV-076, AC-INV-087, AC-INV-089, AC-INV-093, AC-INV-102, AC-INV-108, AC-INV-119, AC-INV-142, AC-INV-148, AC-INV-153, API-INV-001, API-INV-002, API-INV-005, API-INV-007, API-INV-016, API-INV-017, API-INV-018, API-INV-021, API-INV-024, API-INV-025, API-INV-032, API-INV-033, API-INV-034, API-INV-035, API-INV-036, API-INV-038, API-INV-040, API-INV-044, API-INV-045, API-INV-047, API-INV-051, REQ-INV-006, REQ-INV-008, REQ-INV-035, REQ-INV-037, REQ-INV-039, REQ-INV-047, REQ-INV-056, REQ-INV-058, REQ-INV-070, REQ-INV-080, REQ-INV-081, REQ-INV-084, REQ-INV-092, REQ-INV-098, REQ-INV-106, REQ-INV-126, REQ-INV-132, REQ-INV-137 -->
<!-- SUB:REVISE-SCENARIOS:START traces=AC-INV-008,AC-INV-010,AC-INV-040,AC-INV-042,AC-INV-044,AC-INV-052,AC-INV-062,AC-INV-064,AC-INV-076,AC-INV-087,AC-INV-089,AC-INV-093,AC-INV-102,AC-INV-108,AC-INV-119,AC-INV-142,AC-INV-148,AC-INV-153,REQ-INV-006,REQ-INV-008,REQ-INV-035,REQ-INV-037,REQ-INV-039,REQ-INV-047,REQ-INV-056,REQ-INV-058,REQ-INV-070,REQ-INV-080,REQ-INV-081,REQ-INV-084,REQ-INV-092,REQ-INV-098,REQ-INV-106,REQ-INV-126,REQ-INV-132,REQ-INV-137 -->
### REVISE-SCENARIOS — concurrency guards, post-time checks and structure (gate-analysis revise 1)

Concurrency cases (scenario CONCURRENCY) send two requests at once. Where the order matters, a test hook pauses
the first transaction at a named step and releases it after the second has been sent, so the outcome is
deterministic. Each case asserts the single winner and the loser's catalogue code.

<!-- TC:TC-INV-242:START traces=AC-INV-148,REQ-INV-132 -->
### TC-INV-242 — The module manifest declares INV's package, paths, permission module and dependency
Derived from : AC-INV-148  (REQ-INV-132)
Exercises    : META-INF/erp/modules/inv.yaml (CORE — module manifest, checklist C18) · every operation of api-spec-inv.yaml
Rule / code  : — → —
Package      : CORE
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the INV build artifact
Host data    : none
Steps        : 1. Read the module manifest. 2. Compare every path of api-spec-inv.yaml with its api-paths. 3. List the packages of the INV classes.
Expected     : 1 → code INV, package com.commerce.inv, api-paths [/api/v1/inv/**], permission-modules [INV], depends-on [CAT], home-cards []. 2 → all 52 operations fall under /api/v1/inv/**. 3 → every INV class is under com.commerce.inv; none is under com.erp.
Test data    : —
<!-- TC:TC-INV-242:END -->

<!-- TC:TC-INV-243:START traces=AC-INV-148,REQ-INV-132 -->
### TC-INV-243 — The module boundary rules run in INV's own CI job and fail on a violation
Derived from : AC-INV-148  (REQ-INV-132)
Exercises    : the given ModuleBoundaryRules.modulesDependOnlyOnTheirDeclaredDependencies() and InvModuleBoundaryTest (CORE, checklist C20), in the CI job inv-backend
Rule / code  : — → —
Package      : CORE
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: the INV source code; the CI job inv-backend
Host data    : none
Steps        : 1. Run the job. 2. Add, on a throw-away branch, a class in com.commerce.inv.controller that imports com.commerce.inv.engine, and run the job again. 3. Add instead a class of another module that imports com.commerce.inv.service, and run it again.
Expected     : 1 → green, 0 violations: INV imports only its own packages, the given packages it declares and CAT's crossmodule package. 2 → the job fails on the engine rule (engine is used only by service and crossmodule). 3 → the job fails on the boundary rule (other modules import com.commerce.inv.crossmodule only).
Test data    : —
<!-- TC:TC-INV-243:END -->

<!-- TC:TC-INV-244:START traces=AC-INV-040,REQ-INV-035,API-INV-016,API-INV-017,API-INV-040,API-INV-047 -->
### TC-INV-244 — Native queries read and write only the current tenant's rows
Derived from : AC-INV-040  (REQ-INV-035)
Exercises    : QR-INV-015, 016, 017, 024, 038, 041 (native, `@TenantUnscoped` — ADR-INV-041) through API-INV-016, API-INV-017, API-INV-040 and API-INV-047
Rule / code  : — → —
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class ATTACK · language ALL
Preconditions: tenants T1 and T2; T2 holds stock balances, lots with stock and ACTIVE reservations; T1 has one active Warehouse and an active Variant V1 that never moved; an Owner session of T1
Host data    : none
Steps        : 1. As T1, GET balances (no filter), the availability of V1 and the lots. 2. As T1, POST opening stock with 1 row of V1, lot L-1, a quantity and a unit cost. 3. Read T2's balance, valuation and lot rows as T2.
Expected     : 1 → no row of T2 is returned; the reserved figures include no reservation of T2; V1 answers zeros. 2 → 201; the new balance, valuation and lot rows carry T1's tenant. 3 → T2's rows are unchanged. Every repository method of the six QR entries carries `@TenantUnscoped(adr = "ADR-INV-041")`.
Test data    : lot L-1 (placeholder quantity and cost)
<!-- TC:TC-INV-244:END -->

<!-- TC:TC-INV-245:START traces=AC-INV-064,REQ-INV-058,API-INV-018,API-INV-016 -->
### TC-INV-245 — Two concurrent submissions with one idempotency key record once
Derived from : AC-INV-064  (REQ-INV-058)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS, two concurrent calls; advisory lock — ADR-INV-044) · read back API-INV-018, API-INV-016
Rule / code  : RULE-INV-024 → —
Package      : SVC-API-INT
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: Variant V on-hand 10 in Warehouse W; idempotency key K9 never recorded
Host data    : none
Steps        : 1. SLS submits the same POS sale twice at once: issueStock of 2 of V in W with key K9, source document POS_SALE P-90. 2. GET the moves of V in W. 3. GET the balance of V in W.
Expected     : 1 → both calls succeed and answer the same moves (the same stockMovePk). 2 → exactly 1 ISSUE move of 2. 3 → on-hand 8.
Test data    : key K9; source document type POS_SALE, number P-90
<!-- TC:TC-INV-245:END -->

<!-- TC:TC-INV-246:START traces=AC-INV-087,REQ-INV-080,API-INV-024,API-INV-021,API-INV-018 -->
### TC-INV-246 — Two posts of one transfer post it once
Derived from : AC-INV-087  (REQ-INV-080)
Exercises    : API-INV-024 PATCH /api/v1/inv/transfers/{id}/post, twice at once · read back API-INV-021, API-INV-018
Rule / code  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Package      : SVC-API-CRUD
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: Warehouse user session; V on-hand 10 in W1; a DRAFT Transfer T from W1 to W2 of 4 of V, read with its version
Host data    : none
Steps        : 1. Send PATCH T /post twice at once with the version read. 2. GET T. 3. GET moves with sourceDocTypeCode INV_TRANSFER and sourceDocRef T.
Expected     : 1 → exactly one 200; the other 409, code INV-409-DOCUMENT-CLOSED, messageAr «المستند مُرحَّل أو ملغى ولا يمكن تعديله.», messageEn "The document is posted or cancelled and cannot be changed.". 2 → statusCode POSTED. 3 → 2 moves (1 TRANSFER_OUT, 1 TRANSFER_IN); V on-hand 6 in W1.
Test data    : —
<!-- TC:TC-INV-246:END -->

<!-- TC:TC-INV-247:START traces=AC-INV-089,REQ-INV-081,API-INV-024,API-INV-025,API-INV-021,API-INV-018 -->
### TC-INV-247 — A cancel racing a post leaves the transfer in one consistent state
Derived from : AC-INV-089  (REQ-INV-081)
Exercises    : API-INV-024 PATCH …/post and API-INV-025 DELETE /api/v1/inv/transfers/{id}, at once · read back API-INV-021, API-INV-018
Rule / code  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Package      : SVC-API-CRUD
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: a DRAFT Transfer T of 4 of V from W1 to W2, read with its version; V on-hand 10 in W1
Host data    : none
Steps        : 1. Send the post and the cancel of T at once. 2. GET T. 3. GET moves with sourceDocTypeCode INV_TRANSFER and sourceDocRef T.
Expected     : 1 → exactly one 200; the other 409, code INV-409-DOCUMENT-CLOSED with the RULE-INV-029 texts. 2 and 3 → either POSTED with 2 moves (post won) or CANCELLED with 0 moves (cancel won); never POSTED with no move and never CANCELLED with a move.
Test data    : —
<!-- TC:TC-INV-247:END -->

<!-- TC:TC-INV-248:START traces=AC-INV-010,REQ-INV-008,API-INV-007,API-INV-001 -->
### TC-INV-248 — Two concurrent default switches leave exactly one default warehouse
Derived from : AC-INV-010  (REQ-INV-008)
Exercises    : API-INV-007 PATCH /api/v1/inv/warehouses/{id}/default for two targets at once · read back API-INV-001
Rule / code  : RULE-INV-006 → —
Package      : SVC-API-CRUD
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: Owner session; default Warehouse A; active Warehouses B and C, each read with its version
Host data    : none
Steps        : 1. Send PATCH B /default and PATCH C /default at once. 2. GET warehouses, active ALL.
Expected     : 1 → both answer 200, one after the other (each takes the tenant's default-switch advisory lock before it reads the default — ADR-INV-050); no INV-500 and no unique-index violation. 2 → exactly 1 Warehouse has isDefaultFl true — the target of the switch that committed last; A has isDefaultFl false.
Test data    : —
<!-- TC:TC-INV-248:END -->

<!-- TC:TC-INV-249:START traces=AC-INV-119,REQ-INV-106,API-INV-040,API-INV-016 -->
### TC-INV-249 — Two first receipts of one lot number create one lot
Derived from : AC-INV-119  (REQ-INV-106)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR, two concurrent calls with different keys) · read back API-INV-040, API-INV-016
Rule / code  : RULE-INV-036 → INV-422-LOT-EXPIRY-MISMATCH
Package      : SVC-API-INT
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: lotExpiry on; lot-tracked Variant V with no lot L-90 and no lot L-91
Host data    : none
Steps        : 1. PUR submits two receipts at once: 5 of V and 3 of V, both naming new lot L-90 with expiry 2027-05-31. 2. GET lots of V with code L-90 and the L-90 balance. 3. PUR submits two receipts at once naming new lot L-91, one with expiry 2027-06-30 and one with expiry 2027-07-31.
Expected     : 2 → both receipts recorded; exactly one Lot L-90; its balance on-hand 8. 3 → exactly one receipt recorded and one Lot L-91; the other refused with code INV-422-LOT-EXPIRY-MISMATCH, messageAr «التشغيلة L-91 مسجّلة بتاريخ صلاحية {expiry}.», messageEn "Lot L-91 is recorded with expiry date {expiry}.", where {expiry} is the winner's expiry date.
Test data    : lots L-90, L-91; two distinct idempotency keys per pair
<!-- TC:TC-INV-249:END -->

<!-- TC:TC-INV-250:START traces=AC-INV-142,REQ-INV-126,API-INV-045,API-INV-044 -->
### TC-INV-250 — Clearing lot tracking while a lot receipt is in flight waits and is refused
Derived from : AC-INV-142  (REQ-INV-126)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} racing InventoryApi.receiveStock — CON-INV-014 · read back API-INV-044
Rule / code  : RULE-INV-042 → INV-409-LOT-TRACKING-IN-USE
Package      : SVC-API-CRUD
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: Owner session; lotExpiry on; Item "Milk 1L" lot-tracked, read with its version; no lot of its Variants holds stock; a test hook that pauses a receipt after posting engine step 1
Host data    : none
Steps        : 1. PUR starts a receipt of 6 of the Item's Variant in new lot L-95; it pauses after step 1 (the settings record is read FOR SHARE). 2. The Owner sends PUT with lotTrackedFl false and the version read. 3. Release the receipt. 4. GET the Item's settings.
Expected     : 2 → the PUT waits until step 3. Then: the receipt is recorded under L-95; the PUT answers 409, code INV-409-LOT-TRACKING-IN-USE, messageAr «لا يمكن إلغاء تتبع التشغيلة: توجد تشغيلات فيها مخزون (1). اشطبها أو اصرفها أولًا.», messageEn "Lot tracking cannot be cleared: lots still hold stock (1). Write them off or issue them first.". 4 → lotTrackedFl true.
Test data    : lot L-95, expiry a placeholder date
<!-- TC:TC-INV-250:END -->

<!-- TC:TC-INV-251:START traces=AC-INV-042,REQ-INV-037,API-INV-017 -->
### TC-INV-251 — A reservation and a lot issue of one Variant and warehouse serialise
Derived from : AC-INV-042  (REQ-INV-037)
Exercises    : InventoryApi.issueStock (RETURN_OUT of a lot) racing InventoryApi.reserve — CON-INV-015, CON-INV-011 · read back API-INV-017
Rule / code  : RULE-INV-020 → INV-409-RESERVE-NOT-AVAILABLE (in-process, §5.1)
Package      : SVC-API-INT
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: INV_ALLOW_NEGATIVE_STOCK false; lotExpiry on; lot-tracked Variant V in W with unexpired Lot L1 on-hand 3 and no stock without a lot; a test hook that pauses an issue after posting engine step 1
Host data    : none
Steps        : 1. PUR starts a return to supplier of 3 of V from L1 in W; it pauses after step 1 (the pair's lot-less row is locked first). 2. SLS sends a reservation of 3 of V in W. 3. Release the return. 4. GET the availability of V in W.
Expected     : 2 → the reservation waits until step 3. Then: the return is recorded; the reservation is refused with code INV-409-RESERVE-NOT-AVAILABLE, messageAr «الكمية المتاحة لا تكفي للحجز. المتاح: 0.», messageEn "Not enough available stock to reserve. Available: 0.". 4 → on-hand 0, reserved 0, available 0.
Test data    : source document types PURCHASE_RETURN and SALES_ORDER
<!-- TC:TC-INV-251:END -->

<!-- TC:TC-INV-252:START traces=AC-INV-008,REQ-INV-006,API-INV-005,API-INV-033,API-INV-002 -->
### TC-INV-252 — A warehouse named by a DRAFT document cannot be deactivated
Derived from : AC-INV-008  (REQ-INV-006)
Exercises    : API-INV-005 DELETE /api/v1/inv/warehouses/{id} · API-INV-033 DELETE /api/v1/inv/stock-counts/{id} · read back API-INV-002
Rule / code  : RULE-INV-004 → INV-409-WAREHOUSE-IN-USE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; a non-default Warehouse W2 with no stock and no ACTIVE reservation; a DRAFT Stock count C in W2
Host data    : none
Steps        : 1. DELETE W2. 2. GET W2. 3. Cancel C. 4. DELETE W2 again.
Expected     : 1 → 409, code INV-409-WAREHOUSE-IN-USE, messageAr «لا يمكن إيقاف مستودع فيه مخزون أو حجوزات قائمة. انقل المخزون أو سوِّه أولًا.», messageEn "A warehouse that holds stock or open reservations cannot be deactivated. Transfer or adjust the stock first.". 2 → isActiveFl true. 4 → 200, isActiveFl false.
Test data    : the same refusal holds for a DRAFT transfer naming W2 as source or destination and for a DRAFT adjustment in W2
<!-- TC:TC-INV-252:END -->

<!-- TC:TC-INV-253:START traces=AC-INV-087,REQ-INV-080,API-INV-024,API-INV-021,API-INV-018 -->
### TC-INV-253 — Posting a transfer whose Variant was deactivated since the save is refused
Derived from : AC-INV-087  (REQ-INV-080)
Exercises    : API-INV-024 PATCH /api/v1/inv/transfers/{id}/post · read back API-INV-021, API-INV-018
Rule / code  : RULE-INV-018 → INV-422-VARIANT-INACTIVE
Package      : SVC-API-CRUD
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Warehouse user session; a DRAFT Transfer T from W1 to W2 with 1 line of 4 of Variant V, saved while V was active; V then deactivated in CAT; T read with its version
Host data    : none
Steps        : 1. PATCH T /post. 2. GET T. 3. GET moves with sourceDocTypeCode INV_TRANSFER and sourceDocRef T.
Expected     : 1 → 422, code INV-422-VARIANT-INACTIVE, messageAr «متغيّر الصنف غير نشط.», messageEn "The Variant is inactive." (engine step 0 — ADR-INV-045). 2 → statusCode DRAFT. 3 → no move.
Test data    : —
<!-- TC:TC-INV-253:END -->

<!-- TC:TC-INV-254:START traces=AC-INV-093,REQ-INV-084,API-INV-045,API-INV-038,API-INV-035 -->
### TC-INV-254 — Posting an adjustment whose Item became lot-tracked since the save asks for the lot
Derived from : AC-INV-093  (REQ-INV-084)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · API-INV-038 PATCH /api/v1/inv/stock-adjustments/{id}/post · read back API-INV-035
Rule / code  : RULE-INV-037 → INV-422-LOT-REQUIRED
Package      : SVC-API-CRUD
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Owner session; lotExpiry on; Item I not lot-tracked; a DRAFT adjustment A in W with reason DAMAGE and 1 line of I's Variant V −2 with no lot, read with its version
Host data    : none
Steps        : 1. PUT I's settings with lotTrackedFl true (no lot holds stock, so it is allowed). 2. PATCH A /post. 3. GET A.
Expected     : 1 → 200. 2 → 422, code INV-422-LOT-REQUIRED, messageAr «اختر التشغيلة لهذا الصنف.», messageEn "Choose the lot for this Item." (engine step 0 — ADR-INV-045). 3 → statusCode DRAFT; no move recorded.
Test data    : reasonCode DAMAGE
<!-- TC:TC-INV-254:END -->

<!-- TC:TC-INV-255:START traces=AC-INV-052,REQ-INV-047,API-INV-018 -->
### TC-INV-255 — An issue cannot consume a reservation of another line or document
Derived from : AC-INV-052  (REQ-INV-047)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · InventoryApi.reservationsOfDocument — CON-INV-013 · read back API-INV-018
Rule / code  : RULE-INV-049 → INV-422-RESERVATION-MISMATCH (in-process, §5.1)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: an ACTIVE Reservation R of 4 of Variant V in Warehouse W for SALES_ORDER SO-100; Variant V2 with stock in W
Host data    : none
Steps        : 1. SLS issues 1 of V in W for SALES_INVOICE SI-7 referencing R. 2. SLS issues 1 of V2 in W for SALES_ORDER SO-100 referencing R. 3. Read the reservations of SO-100. 4. GET moves of V and V2 in W.
Expected     : 1 and 2 → code INV-422-RESERVATION-MISMATCH, messageAr «الحجز لا يخص هذا السطر: المتغيّر أو المستودع أو المستند مختلف.», messageEn "The reservation does not belong to this line: the Variant, warehouse or document differs.". 3 → R is ACTIVE with reservedQty 4. 4 → no move.
Test data    : source documents SALES_ORDER SO-100, SALES_INVOICE SI-7
<!-- TC:TC-INV-255:END -->

<!-- TC:TC-INV-256:START traces=AC-INV-108,REQ-INV-098,API-INV-036,API-INV-034 -->
### TC-INV-256 — An adjustment quantity with 4 decimals is refused, 3 decimals are accepted
Derived from : AC-INV-108  (REQ-INV-098)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · read back API-INV-034
Rule / code  : RULE-INV-017 → INV-422-QUANTITY-INVALID
Package      : SVC-API-CRUD
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: Warehouse user session; one active Warehouse; active Variant V
Host data    : none
Steps        : 1. POST an adjustment with reason DAMAGE and 1 line of V, quantityChange −1.2345. 2. POST it with quantityChange −1.234. 3. GET adjustments.
Expected     : 1 → 422, code INV-422-QUANTITY-INVALID, messageAr «أدخل كمية أكبر من صفر بثلاث خانات عشرية على الأكثر.», messageEn "Enter a quantity greater than zero with at most 3 decimal places."; nothing is saved or rounded. 2 → 201 with quantityChange −1.234. 3 → exactly one new adjustment.
Test data    : −1.2345 (4 decimals), −1.234 (3 decimals); the same boundary holds for measureQtyChange
<!-- TC:TC-INV-256:END -->

<!-- TC:TC-INV-257:START traces=AC-INV-076,REQ-INV-070,API-INV-047,API-INV-034 -->
### TC-INV-257 — An opening stock cost with 5 decimals is refused, 4 decimals are accepted
Derived from : AC-INV-076  (REQ-INV-070)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-034
Rule / code  : RULE-INV-025 → INV-422-LOAD-ROWS-INVALID with detail INV-422-NO-KNOWN-COST
Package      : SVC-API-CRUD
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: Owner session; one active Warehouse; Variant V with an active barcode, never valued
Host data    : none
Steps        : 1. POST opening stock with 1 row of V, quantity 10, unit cost 12.34567. 2. POST it with unit cost 12.3456. 3. GET adjustments with reasonCode OPENING_STOCK.
Expected     : 1 → 422, code INV-422-LOAD-ROWS-INVALID, messageAr «بعض أسطر التحميل غير صالحة؛ راجع الأسطر المذكورة.», messageEn "Some rows of the load are invalid; see the listed rows."; `details` rowNo 1 with code INV-422-NO-KNOWN-COST, messageAr «لا توجد تكلفة معروفة لهذا المتغيّر. أدخل تكلفة الوحدة.», messageEn "This Variant has no known cost. Enter the unit cost."; nothing posted or rounded. 2 → 201; the ADJUSTMENT_IN carries unitCost 12.3456. 3 → exactly one posted adjustment.
Test data    : 12.34567 (5 decimals), 12.3456 (4 decimals)
<!-- TC:TC-INV-257:END -->

<!-- TC:TC-INV-258:START traces=AC-INV-102,REQ-INV-092,API-INV-032,API-INV-051,API-INV-018 -->
### TC-INV-258 — A count that increases a never-valued Variant posts at cost 0
Derived from : AC-INV-102  (REQ-INV-092)
Exercises    : API-INV-032 PATCH /api/v1/inv/stock-counts/{id}/post · read back API-INV-051, API-INV-018
Rule / code  : RULE-INV-025 (count-generated exception — ADR-INV-030) → —
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: negative stock allowed; Variant V never valued, on-hand −2 in W (sold before any receipt); a DRAFT count in W with V countedQty 3, read with its version
Host data    : none
Steps        : 1. PATCH the count /post. 2. GET the moves of the resulting adjustment. 3. GET the valuation of V.
Expected     : 1 → 200; count POSTED; the adjustment has reasonCode COUNT_DIFFERENCE and 1 line (V, +5); no INV-422-NO-KNOWN-COST. 2 → 1 ADJUSTMENT_IN of 5 with unitCost 0.0000 and moveValue 0.00. 3 → totalOnHandQty 0, stockValue 0, averageUnitCost null (still never valued); V on-hand 3 in W.
Test data    : —
<!-- TC:TC-INV-258:END -->

<!-- TC:TC-INV-259:START traces=AC-INV-044,REQ-INV-039,API-INV-016,API-INV-017 -->
### TC-INV-259 — A Seller/Cashier reads each balance and availability row with its warehouse label
Derived from : AC-INV-044  (REQ-INV-039)
Exercises    : API-INV-016 GET /api/v1/inv/stock-balances · API-INV-017 GET …/availability/{variantId}
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Seller/Cashier session (no PERM_INV_WAREHOUSES_VIEW); Warehouses W1 «المستودع الرئيسي» / "Main warehouse" and W2 «مستودع الفرع»; Variant V on-hand 5 in W1 and 8 in W2
Host data    : none
Steps        : 1. GET balances with variantPk V. 2. GET the availability of V.
Expected     : 1 → each row carries warehouseCode, warehouseNameAr and warehouseNameEn of its warehouse (W1: «المستودع الرئيسي» / "Main warehouse"; W2: «مستودع الفرع», nameEn empty). 2 → each perWarehouse row carries the same three fields. No call to API-INV-001 is needed (ADR-INV-036).
Test data    : —
<!-- TC:TC-INV-259:END -->

<!-- TC:TC-INV-260:START traces=AC-INV-062,REQ-INV-056,API-INV-018 -->
### TC-INV-260 — The move search by source document returns that document's moves only
Derived from : AC-INV-062  (REQ-INV-056)
Exercises    : API-INV-018 GET /api/v1/inv/stock-moves (sourceDocTypeCode, sourceDocRef)
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Warehouse user session; a posted Transfer TR-0005 (transferPk T, 2 moves); a SALES_INVOICE whose number is also TR-0005, with 1 ISSUE move
Host data    : none
Steps        : 1. GET moves with sourceDocTypeCode INV_TRANSFER and sourceDocRef T. 2. GET moves with docNo TR-0005.
Expected     : 1 → exactly the 2 moves of the transfer. 2 → 3 moves (the number alone matches both documents).
Test data    : document number TR-0005 shared by two documents of different types
<!-- TC:TC-INV-260:END -->

<!-- TC:TC-INV-261:START traces=AC-INV-153,REQ-INV-137,API-INV-018 -->
### TC-INV-261 — In-process calls are authorised by the calling module; REST services check INV authority
Derived from : AC-INV-153  (REQ-INV-137)
Exercises    : InventoryApi.issueStock — CON-INV-015 (engine, no INV check — ADR-INV-042) · StockQueryService.searchMoves (REST-facing service, `@PreAuthorize` PERM_INV_STOCK_MOVES_VIEW) · API-INV-018
Rule / code  : REQ-INV-138 → INV-403
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a user holding only the Seller/Cashier role (PERM_INV_STOCK_BALANCES_VIEW); Variant V with stock in W
Host data    : none
Steps        : 1. SLS calls issueStock of 1 of V in W for a POS sale, in that user's security context. 2. In the same context, call StockQueryService.searchMoves directly (service-layer test). 3. In the same context, GET /api/v1/inv/stock-moves.
Expected     : 1 → success, 1 ISSUE move recorded (the calling module authorised its user). 2 → refused by `@PreAuthorize` (access denied, mapped to INV-403). 3 → 403, code INV-403, messageAr «ليست لديك صلاحية لهذا الإجراء.», messageEn "You do not have permission for this action.".
Test data    : source document type POS_SALE
<!-- TC:TC-INV-261:END -->
<!-- SUB:REVISE-SCENARIOS:END -->
