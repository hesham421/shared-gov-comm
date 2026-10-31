<!-- source: PHASE:TEST-PLAN-BE / SUB:REVISE2-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-005, AC-INV-006, AC-INV-020, AC-INV-021, AC-INV-027, AC-INV-032, AC-INV-036, AC-INV-056, AC-INV-064, AC-INV-073, AC-INV-081, AC-INV-085, AC-INV-086, AC-INV-087, AC-INV-102, AC-INV-113, AC-INV-121, AC-INV-126, AC-INV-131, AC-INV-136, AC-INV-154, API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-006, API-INV-007, API-INV-009, API-INV-010, API-INV-012, API-INV-014, API-INV-015, API-INV-016, API-INV-017, API-INV-018, API-INV-019, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-026, API-INV-027, API-INV-028, API-INV-030, API-INV-032, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-041, API-INV-042, API-INV-044, API-INV-045, API-INV-047, API-INV-052, REQ-INV-004, REQ-INV-005, REQ-INV-017, REQ-INV-018, REQ-INV-023, REQ-INV-027, REQ-INV-031, REQ-INV-051, REQ-INV-058, REQ-INV-067, REQ-INV-074, REQ-INV-078, REQ-INV-079, REQ-INV-080, REQ-INV-092, REQ-INV-101, REQ-INV-108, REQ-INV-113, REQ-INV-117, REQ-INV-122, REQ-INV-138 -->
<!-- SUB:REVISE2-SCENARIOS:START traces=AC-INV-005,AC-INV-006,AC-INV-020,AC-INV-021,AC-INV-027,AC-INV-032,AC-INV-036,AC-INV-056,AC-INV-064,AC-INV-073,AC-INV-081,AC-INV-085,AC-INV-086,AC-INV-087,AC-INV-102,AC-INV-113,AC-INV-121,AC-INV-126,AC-INV-131,AC-INV-136,AC-INV-154,REQ-INV-004,REQ-INV-005,REQ-INV-017,REQ-INV-018,REQ-INV-023,REQ-INV-027,REQ-INV-031,REQ-INV-051,REQ-INV-058,REQ-INV-067,REQ-INV-074,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-092,REQ-INV-101,REQ-INV-108,REQ-INV-113,REQ-INV-117,REQ-INV-122,REQ-INV-138 -->
### REVISE2-SCENARIOS — catalogue coverage, line refusals and guards (gate-analysis revise 2)

These cases close the catalogue rule of this plan (every HTTP catalogue row and every §5.1 row is asserted by at
least one TC) and the gate-analysis revise 2 findings. Every INV code is asserted with its ar / en text — the
module-specific PLATFORM-STD codes carry INV's own text since gate-analysis revise 3, and the generic codes INV-400,
INV-401, INV-500 and INV-409-STALE-VERSION since revise 4 (ADR-INV-021 amended), so TC-INV-269, 270, 271 and 277 assert
those texts too.

<!-- TC:TC-INV-264:START traces=AC-INV-087,REQ-INV-080,API-INV-024,API-INV-038,API-INV-021,API-INV-035 -->
### TC-INV-264 — A post refused for one line of three names that line in details
Derived from : AC-INV-087  (REQ-INV-080)
Exercises    : API-INV-024 PATCH /api/v1/inv/transfers/{id}/post · API-INV-038 PATCH /api/v1/inv/stock-adjustments/{id}/post · read back API-INV-021, API-INV-035
Rule / code  : RULE-INV-018 → INV-422-VARIANT-INACTIVE (details names the line — ADR-INV-046)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; 2 active Warehouses W1, W2 with stock of V1, V2, V3 in W1; a DRAFT Transfer T from W1 to W2 with 3 lines (V1, V2, V3, 1 each) and a DRAFT adjustment A in W1 with reason DAMAGE and rows 1 … 3 (V1, V2, V3, −1 each), both saved while V2 was active; V2 then deactivated in CAT; T and A read with their versions
Host data    : none
Steps        : 1. PATCH T /post. 2. GET T. 3. PATCH A /post. 4. GET A.
Expected     : 1 → 422, code INV-422-VARIANT-INACTIVE, messageAr «متغيّر الصنف غير نشط.», messageEn "The Variant is inactive."; `details` holds exactly 1 entry {lineRef = the transferLinePk of the V2 line, lineRefKind LINE_PK, code INV-422-VARIANT-INACTIVE, the same texts}. 2 → statusCode DRAFT; no move. 3 → 422 with the same code and texts; `details` holds exactly 1 entry {lineRef = the stockAdjustmentLinePk of row 2, lineRefKind LINE_PK, rowNo 2}. 4 → statusCode DRAFT; no move.
Test data    : lines V1, V2, V3; INV_ADJUSTMENT_REASON DAMAGE (seeded)
<!-- TC:TC-INV-264:END -->

<!-- TC:TC-INV-265:START traces=AC-INV-102,REQ-INV-092,API-INV-032,API-INV-035,API-INV-016,API-INV-052 -->
### TC-INV-265 — A count posts although the tenant has deactivated COUNT_DIFFERENCE
Derived from : AC-INV-102  (REQ-INV-092)
Exercises    : API-INV-032 PATCH /api/v1/inv/stock-counts/{id}/post · read back API-INV-035, API-INV-016, API-INV-052 GET /api/v1/inv/lookups/{key}
Rule / code  : RULE-INV-033 (not checked for a system-generated adjustment — ADR-INV-047) → —
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: this case's own tenant; INV_ADJUSTMENT_REASON COUNT_DIFFERENCE inactive in it; Variant V valued, on-hand 10 in Warehouse W; a DRAFT count in W with V countedQty 8, read with its version
Host data    : INV_ADJUSTMENT_REASON COUNT_DIFFERENCE — made inactive in this case's own tenant by the given mdl call DELETE /api/v1/mdl/lookup-values/{id} ({id} = the COUNT_DIFFERENCE value of the INV_ADJUSTMENT_REASON type found by POST /api/v1/mdl/lookup-types/search); no other case runs in this tenant, because TC-INV-114 and TC-INV-284 need the value active
Steps        : 1. PATCH the count /post. 2. GET the resulting adjustment. 3. GET the balance of V in W. 4. GET /api/v1/inv/lookups/INV_ADJUSTMENT_REASON.
Expected     : 1 → 200; the count is POSTED and no INV-422-REASON-REQUIRED is answered. 2 → a POSTED adjustment with reasonCode COUNT_DIFFERENCE and 1 line (V, −2). 3 → on-hand 8. 4 → 4 values (DAMAGE, EXPIRY_WRITE_OFF, OPENING_STOCK, OTHER) — COUNT_DIFFERENCE is not offered.
Test data    : on-hand 10, counted 8
<!-- TC:TC-INV-265:END -->

<!-- TC:TC-INV-266:START traces=AC-INV-073,REQ-INV-067,API-INV-038,API-INV-016,API-INV-035 -->
### TC-INV-266 — A decrease naming a lot is refused when the lot holds less, even though the warehouse total covers it
Derived from : AC-INV-073  (REQ-INV-067)
Exercises    : API-INV-038 PATCH /api/v1/inv/stock-adjustments/{id}/post · InventoryApi.issueStock — CON-INV-015 (in-process, caller PUR, RETURN_OUT) · read back API-INV-016, API-INV-035
Rule / code  : RULE-INV-019 → INV-409-NEGATIVE-STOCK
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: INV_ALLOW_NEGATIVE_STOCK false; lotExpiry on; lot-tracked Variant V in Warehouse W with unexpired Lot L1 on-hand 2 and Lot L2 on-hand 8 (W total 10); a DRAFT adjustment A in W with reason DAMAGE and 1 line of V, lotNo L1, quantityChange −5, read with its version
Host data    : none
Steps        : 1. PATCH A /post. 2. PUR calls issueStock with move type RETURN_OUT, 5 of V from L1 in W. 3. GET balances of V in W. 4. GET A.
Expected     : 1 → 409, code INV-409-NEGATIVE-STOCK, messageAr «الرصيد لا يكفي: المتاح في المستودع 2. الرصيد السالب غير مسموح.», messageEn "Not enough stock: 2 on hand in this warehouse. Negative stock is not allowed." ({onHand} = L1's on-hand); `details` names A's line (LINE_PK, rowNo 1). 2 → the same code and texts, thrown in-process. 3 → L1 on-hand 2, L2 on-hand 8; no move recorded. 4 → statusCode DRAFT.
Test data    : L1 2, L2 8, decrease 5 of L1; INV_ADJUSTMENT_REASON DAMAGE (seeded); source document type PURCHASE_RETURN
<!-- TC:TC-INV-266:END -->

<!-- TC:TC-INV-267:START traces=AC-INV-056,REQ-INV-051,API-INV-018 -->
### TC-INV-267 — A receipt or customer return of a bundle Variant is refused, never exploded
Derived from : AC-INV-056  (REQ-INV-051)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, callers PUR and SLS) · read back API-INV-018
Rule / code  : RULE-INV-018 → INV-422-VARIANT-INACTIVE
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: bundles on; an active Bundle B made of 2 × C1 and 1 × C2, all active; Warehouse W
Host data    : none
Steps        : 1. PUR calls receiveStock with move type RECEIPT, 1 line of 1 of B at unit cost 30.0000. 2. SLS calls receiveStock with move type RETURN_IN, 1 line of 1 of B. 3. GET moves of B, C1 and C2.
Expected     : 1 and 2 → code INV-422-VARIANT-INACTIVE, messageAr «متغيّر الصنف غير نشط.», messageEn "The Variant is inactive.". 3 → no move of B, C1 or C2.
Test data    : source document types PURCHASE_RECEIPT and SALES_RETURN
<!-- TC:TC-INV-267:END -->

<!-- TC:TC-INV-268:START traces=AC-INV-136,REQ-INV-122,API-INV-036,API-INV-047,API-INV-034 -->
### TC-INV-268 — A dual-quantity adjustment or opening stock line without its measure change is refused
Derived from : AC-INV-136  (REQ-INV-122)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-034
Rule / code  : RULE-INV-040 → INV-422-MEASURE-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user and Owner sessions; dualQuantity on; one active Warehouse; Item "Cotton fabric" dual-quantity (metre), its Variant V with an active barcode B1 and stock in the warehouse
Host data    : none
Steps        : 1. POST an adjustment with reason DAMAGE and 1 line of V, quantityChange −1, no measure change. 2. POST it again with measureQtyChange −40.000. 3. As Owner, POST opening stock with 1 row: barcode B1, quantity 2, unit cost 10.0000, no measure. 4. GET adjustments.
Expected     : 1 → 422, code INV-422-MEASURE-REQUIRED, messageAr «أدخل القياس (الوزن أو الطول) لهذا الصنف.», messageEn "Enter the measure (weight or length) for this Item."; `details` {lineRef 1, lineRefKind REQUEST_POSITION, rowNo 1}. 2 → 201. 3 → 422, code INV-422-LOAD-ROWS-INVALID, messageAr «بعض أسطر التحميل غير صالحة؛ راجع الأسطر المذكورة.», messageEn "Some rows of the load are invalid; see the listed rows."; `details` rowNo 1 with code INV-422-MEASURE-REQUIRED and the same texts; nothing posted. 4 → only the adjustment of step 2.
Test data    : quantityChange −1, measureQtyChange −40.000; INV_ADJUSTMENT_REASON DAMAGE (seeded)
<!-- TC:TC-INV-268:END -->

<!-- TC:TC-INV-269:START traces=AC-INV-005,REQ-INV-004,API-INV-004,API-INV-006,API-INV-007,API-INV-012,API-INV-014,API-INV-015,API-INV-002,API-INV-010 -->
### TC-INV-269 — A stale version on a warehouse or branch change is refused
Derived from : AC-INV-005  (REQ-INV-004)
Exercises    : API-INV-004 PUT, API-INV-006 PATCH …/reactivate, API-INV-007 PATCH …/default (warehouses) · API-INV-012 PUT, API-INV-014 PATCH …/reactivate, API-INV-015 PATCH …/default (branches) · read back API-INV-002, API-INV-010
Rule / code  : PLATFORM-STD → INV-409-STALE-VERSION
Package      : SVC-API-CRUD
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Owner session; active non-default Warehouse W and Branch B, inactive Warehouse W2 and Branch B2 (B2's warehouses none), each read with its version; another session then renames each of them (version + 1)
Host data    : none
Steps        : 1. PUT W with a new nameEn and the version read. 2. PATCH W2 /reactivate with the version read. 3. PATCH W /default with the version read. 4. PUT B with a new nameEn and the version read. 5. PATCH B2 /reactivate with the version read. 6. PATCH B /default with the version read. 7. GET W, W2, B, B2.
Expected     : 1 … 6 → 409 each, code INV-409-STALE-VERSION, messageAr «تغيّر السجل منذ قراءته. أعد التحميل.», messageEn "The record changed since you read it. Reload." (INV's text — ADR-INV-021 amended). 7 → each record shows only the other session's rename; W2 and B2 are still inactive; the default warehouse and branch are unchanged.
Test data    : —
<!-- TC:TC-INV-269:END -->

<!-- TC:TC-INV-270:START traces=AC-INV-086,REQ-INV-079,API-INV-023,API-INV-024,API-INV-030,API-INV-032,API-INV-037,API-INV-038,API-INV-021,API-INV-027,API-INV-035 -->
### TC-INV-270 — A stale version on a document change or post is refused
Derived from : AC-INV-086  (REQ-INV-079)
Exercises    : API-INV-023 PUT, API-INV-024 PATCH …/post (transfer) · API-INV-030 PUT …/lines/{lineId}, API-INV-032 PATCH …/post (count) · API-INV-037 PUT, API-INV-038 PATCH …/post (adjustment) · read back API-INV-021, API-INV-027, API-INV-035
Rule / code  : PLATFORM-STD → INV-409-STALE-VERSION
Package      : SVC-API-CRUD
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Warehouse user session; a DRAFT Transfer T, a DRAFT count C with line L, a DRAFT adjustment A, each read with its version (and L with its own); another session then saves each of them once (T and A re-saved, L's counted quantity changed, C's line added)
Host data    : none
Steps        : 1. PUT T with the version read. 2. PATCH T /post with the version read. 3. PUT line L with the line version read. 4. PATCH C /post with the version read. 5. PUT A with the version read. 6. PATCH A /post with the version read. 7. GET T, C and A.
Expected     : 1 … 6 → 409 each, code INV-409-STALE-VERSION, messageAr «تغيّر السجل منذ قراءته. أعد التحميل.», messageEn "The record changed since you read it. Reload.". 7 → T, C and A are DRAFT and show only the other session's change; no move recorded.
Test data    : —
<!-- TC:TC-INV-270:END -->

<!-- TC:TC-INV-271:START traces=AC-INV-121,REQ-INV-108,API-INV-042,API-INV-045,API-INV-041,API-INV-044 -->
### TC-INV-271 — A stale version on a lot correction or an Item stock settings save is refused
Derived from : AC-INV-121  (REQ-INV-108)
Exercises    : API-INV-042 PATCH /api/v1/inv/lots/{id} · API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · read back API-INV-041, API-INV-044
Rule / code  : PLATFORM-STD → INV-409-STALE-VERSION
Package      : SVC-API-CRUD
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Owner session; lotExpiry on; Lot L-77 and the stock settings of Item "Milk 1L" (lot-tracked), each read with its version; another session then corrects L-77's nameEn and saves "Milk 1L" again
Host data    : none
Steps        : 1. PATCH L-77 with expiryDate 2027-02-20 and the version read. 2. PUT the settings of "Milk 1L" with lotTrackedFl true, dualQuantityFl false and the version read. 3. GET L-77 and the settings.
Expected     : 1 and 2 → 409 each, code INV-409-STALE-VERSION, messageAr «تغيّر السجل منذ قراءته. أعد التحميل.», messageEn "The record changed since you read it. Reload.". 3 → L-77 keeps its expiry; both show only the other session's change.
Test data    : expiryDate 2027-02-20
<!-- TC:TC-INV-271:END -->

<!-- TC:TC-INV-272:START traces=AC-INV-036,AC-INV-032,REQ-INV-031,REQ-INV-027,API-INV-003,API-INV-004,API-INV-006,API-INV-015,API-INV-001,API-INV-009 -->
### TC-INV-272 — A warehouse cannot use an inactive branch, and an inactive branch cannot become the default
Derived from : AC-INV-036  (REQ-INV-031)
Exercises    : API-INV-003 POST, API-INV-004 PUT, API-INV-006 PATCH …/reactivate (warehouses) · API-INV-015 PATCH /api/v1/inv/branches/{id}/default · read back API-INV-001, API-INV-009
Rule / code  : RULE-INV-009 → INV-422-BRANCH-INACTIVE · RULE-INV-015 → INV-409-BRANCH-DEFAULT-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; default Branch B1; an inactive Branch B2; an active Warehouse W in B1 and an inactive Warehouse W3 whose branch is B2, each read with its version
Host data    : none
Steps        : 1. POST a warehouse with a new nameAr and branchPk B2. 2. PUT W with branchPk B2. 3. PATCH W3 /reactivate. 4. PATCH B2 /default with its version. 5. GET warehouses and branches, active ALL.
Expected     : 1, 2 and 3 → 422 each, code INV-422-BRANCH-INACTIVE, messageAr «الفرع المختار غير نشط.», messageEn "The chosen branch is inactive.". 4 → 409, code INV-409-BRANCH-DEFAULT-INACTIVE, messageAr «الفرع الافتراضي يجب أن يكون نشطًا.», messageEn "The default branch must be active.". 5 → no new warehouse; W still in B1; W3 inactive; B1 still the only default branch.
Test data    : nameAr of step 1: any name not yet used (placeholder)
<!-- TC:TC-INV-272:END -->

<!-- TC:TC-INV-273:START traces=AC-INV-005,AC-INV-027,REQ-INV-004,REQ-INV-023,API-INV-004,API-INV-012,API-INV-023,API-INV-037 -->
### TC-INV-273 — A changed code or document number in an update is refused; the stored one is accepted
Derived from : AC-INV-005  (REQ-INV-004)
Exercises    : API-INV-004 PUT /api/v1/inv/warehouses/{id} · API-INV-012 PUT /api/v1/inv/branches/{id} · API-INV-023 PUT /api/v1/inv/transfers/{id} · API-INV-037 PUT /api/v1/inv/stock-adjustments/{id}
Rule / code  : RULE-INV-001 → INV-422-WAREHOUSE-CODE-READONLY · RULE-INV-010 → INV-422-BRANCH-CODE-READONLY · RULE-INV-030 → INV-422-DOC-NO-READONLY
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: Owner and Warehouse user sessions; Warehouse WH-0002, Branch BR-0002, DRAFT Transfer TR-0005 and DRAFT adjustment ADJ-0002, each read with its version
Host data    : none
Steps        : 1. PUT WH-0002 with code WH-9999. 2. PUT BR-0002 with code BR-9999. 3. PUT TR-0005 with docNo TR-9999. 4. PUT ADJ-0002 with docNo ADJ-9999. 5. Repeat 1 … 4 with the stored code or docNo echoed.
Expected     : 1 → 422, code INV-422-WAREHOUSE-CODE-READONLY, messageAr «رمز المستودع يُنشأ تلقائيًا ولا يمكن تغييره.», messageEn "The warehouse code is generated automatically and cannot be changed.". 2 → 422, code INV-422-BRANCH-CODE-READONLY, messageAr «رمز الفرع يُنشأ تلقائيًا ولا يمكن تغييره.», messageEn "The branch code is generated automatically and cannot be changed.". 3 and 4 → 422, code INV-422-DOC-NO-READONLY, messageAr «رقم المستند يُنشأ تلقائيًا ولا يمكن تغييره.», messageEn "The document number is generated automatically and cannot be changed.". 5 → 200 each; codes and numbers unchanged.
Test data    : WH-9999, BR-9999, TR-9999, ADJ-9999
<!-- TC:TC-INV-273:END -->

<!-- TC:TC-INV-274:START traces=AC-INV-021,AC-INV-020,REQ-INV-018,REQ-INV-017,API-INV-028,API-INV-036,API-INV-026,API-INV-034 -->
### TC-INV-274 — A count or an adjustment naming an inactive warehouse, or none while two are active, is refused over REST
Derived from : AC-INV-021  (REQ-INV-018)
Exercises    : API-INV-028 POST /api/v1/inv/stock-counts · API-INV-036 POST /api/v1/inv/stock-adjustments · read back API-INV-026, API-INV-034
Rule / code  : RULE-INV-007 → INV-422-WAREHOUSE-INACTIVE · RULE-INV-008 → INV-422-WAREHOUSE-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; active Warehouses W1, W2 and inactive Warehouse W3; active Variant V
Host data    : none
Steps        : 1. POST a count with warehousePk W3. 2. POST an adjustment with warehousePk W3, reason DAMAGE and 1 line of V −1. 3. POST a count with no warehouse. 4. POST the adjustment with no warehouse. 5. GET counts and adjustments.
Expected     : 1 and 2 → 422, code INV-422-WAREHOUSE-INACTIVE, messageAr «المستودع غير نشط.», messageEn "The warehouse is inactive.". 3 and 4 → 422, code INV-422-WAREHOUSE-REQUIRED, messageAr «اختر المستودع.», messageEn "Choose the warehouse.". 5 → nothing saved.
Test data    : INV_ADJUSTMENT_REASON DAMAGE (seeded)
<!-- TC:TC-INV-274:END -->

<!-- TC:TC-INV-275:START traces=AC-INV-081,REQ-INV-074,API-INV-047,API-INV-034 -->
### TC-INV-275 — Opening stock rows with no cost or a negative cost are refused
Derived from : AC-INV-081  (REQ-INV-074)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-034
Rule / code  : RULE-INV-026 → INV-422-LOAD-ROWS-INVALID with detail INV-422-OPENING-COST-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; one active Warehouse; Variants V1, V2, V3 with active barcodes
Host data    : none
Steps        : 1. POST opening stock with 3 rows: row 1 (V1, 10, 12.0000) valid; row 2 (V2, 5, unit cost −1.0000); row 3 (V3, 3, no unit cost). 2. GET adjustments with reasonCode OPENING_STOCK.
Expected     : 1 → 422, code INV-422-LOAD-ROWS-INVALID, messageAr «بعض أسطر التحميل غير صالحة؛ راجع الأسطر المذكورة.», messageEn "Some rows of the load are invalid; see the listed rows."; `details` lists rowNo 2 and 3, each with code INV-422-OPENING-COST-REQUIRED, messageAr «أدخل تكلفة الوحدة للمخزون الافتتاحي.», messageEn "Enter the unit cost of the opening stock.", and no entry for row 1. 2 → nothing posted.
Test data    : the three rows above
<!-- TC:TC-INV-275:END -->

<!-- TC:TC-INV-276:START traces=AC-INV-006,REQ-INV-005,API-INV-002,API-INV-010,API-INV-019,API-INV-021,API-INV-027,API-INV-030,API-INV-035,API-INV-041 -->
### TC-INV-276 — Unknown identifiers answer their not-found codes
Derived from : AC-INV-006  (REQ-INV-005)
Exercises    : API-INV-002, API-INV-010, API-INV-019, API-INV-021, API-INV-027, API-INV-030, API-INV-035, API-INV-041
Rule / code  : PLATFORM-STD → INV-404-WAREHOUSE-NOT-FOUND, INV-404-BRANCH-NOT-FOUND, INV-404-STOCK-MOVE-NOT-FOUND, INV-404-TRANSFER-NOT-FOUND, INV-404-STOCK-COUNT-NOT-FOUND, INV-404-STOCK-COUNT-LINE-NOT-FOUND, INV-404-STOCK-ADJUSTMENT-NOT-FOUND, INV-404-LOT-NOT-FOUND
Package      : SVC-API-SEARCH
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; identifier X exists for no record of the tenant; a DRAFT count C; a line of another count, L2
Host data    : none
Steps        : 1. GET warehouse X. 2. GET branch X. 3. GET stock move X. 4. GET transfer X. 5. GET count X. 6. PUT line L2 on count C. 7. GET adjustment X. 8. GET lot X.
Expected     : 404 each, with the codes and texts in step order: 1 INV-404-WAREHOUSE-NOT-FOUND, messageAr «المستودع غير موجود.», messageEn "The warehouse was not found."; 2 INV-404-BRANCH-NOT-FOUND, «الفرع غير موجود.» / "The branch was not found."; 3 INV-404-STOCK-MOVE-NOT-FOUND, «حركة المخزون غير موجودة.» / "The stock move was not found."; 4 INV-404-TRANSFER-NOT-FOUND, «التحويل غير موجود.» / "The transfer was not found."; 5 INV-404-STOCK-COUNT-NOT-FOUND, «الجرد غير موجود.» / "The stock count was not found."; 6 INV-404-STOCK-COUNT-LINE-NOT-FOUND, «سطر الجرد غير موجود في هذا الجرد.» / "The count line was not found on this count."; 7 INV-404-STOCK-ADJUSTMENT-NOT-FOUND, «التسوية غير موجودة.» / "The stock adjustment was not found."; 8 INV-404-LOT-NOT-FOUND, «التشغيلة غير موجودة.» / "The lot was not found." (INV's own texts — ADR-INV-021 amended).
Test data    : X (placeholder identifier); L2 belongs to another count
<!-- TC:TC-INV-276:END -->

<!-- TC:TC-INV-277:START traces=AC-INV-154,REQ-INV-138,API-INV-036,API-INV-001,API-INV-016 -->
### TC-INV-277 — Malformed, unauthenticated and failing requests answer the platform codes
Derived from : AC-INV-154  (REQ-INV-138)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · API-INV-001 GET /api/v1/inv/warehouses · API-INV-016 GET /api/v1/inv/stock-balances
Rule / code  : PLATFORM-STD → INV-400, INV-401, INV-500
Package      : SEC-BE
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: a Warehouse user session; a test double that makes the stock balance repository throw an unexpected exception
Host data    : none
Steps        : 1. POST an adjustment whose `lines` is a string, not an array. 2. GET warehouses with no bearer token. 3. GET balances with the failing repository.
Expected     : 1 → 400, code INV-400, messageAr «طلب غير صالح.», messageEn "The request is invalid.". 2 → 401, code INV-401, messageAr «يلزم تسجيل الدخول.», messageEn "Authentication is required.". 3 → 500, code INV-500, messageAr «حدث خطأ غير متوقع. حاول مرة أخرى.», messageEn "An unexpected error occurred. Try again."; no stack trace or SQL in the body (INV's texts, from messages.properties / messages_ar.properties — ADR-INV-021 amended).
Test data    : —
<!-- TC:TC-INV-277:END -->

<!-- TC:TC-INV-278:START traces=AC-INV-064,REQ-INV-058,API-INV-018,API-INV-017 -->
### TC-INV-278 — An idempotency key reused for a different request is refused
Derived from : AC-INV-064  (REQ-INV-058)
Exercises    : InventoryApi.issueStock — CON-INV-015 · InventoryApi.reserve — CON-INV-011 (in-process, caller SLS) · read back API-INV-018, API-INV-017
Rule / code  : RULE-INV-050 → INV-409-IDEMPOTENCY-KEY-REUSED (in-process here; also the HTTP code of API-INV-047, TC-INV-288 — ADR-INV-053)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Variant V on-hand 10 in Warehouse W; a POS sale already synchronised with key K1 that recorded 1 ISSUE of 2 of V in W
Host data    : none
Steps        : 1. SLS submits issueStock with key K1 for 3 of V in W. 2. SLS submits the original request again with key K1 (2 of V). 3. SLS reserves 4 of V in W with key K2, then reserves 5 of V in W with key K2. 4. GET moves of V in W and the availability of V in W.
Expected     : 1 → code INV-409-IDEMPOTENCY-KEY-REUSED, messageAr «مفتاح عدم التكرار مستخدم لطلب آخر. استخدم مفتاحًا جديدًا لهذا الطلب.», messageEn "This idempotency key was already used for a different request. Use a new key for this request."; nothing recorded. 2 → the first answer (RULE-INV-024). 3 → the first reservation is created; the second is refused with the same code and texts. 4 → still 1 move with key K1; reserved 4.
Test data    : keys K1, K2; source document types POS_SALE and SALES_ORDER
<!-- TC:TC-INV-278:END -->

<!-- TC:TC-INV-279:START traces=AC-INV-126,AC-INV-121,REQ-INV-113,REQ-INV-108,API-INV-042,API-INV-018,API-INV-041 -->
### TC-INV-279 — A sale racing an expiry correction never draws from a lot that has just expired
Derived from : AC-INV-126  (REQ-INV-113)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS, sale) racing API-INV-042 PATCH /api/v1/inv/lots/{id} · read back API-INV-018, API-INV-041
Rule / code  : RULE-INV-038 → —
Package      : SVC-API-INT
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: lotExpiry on; tenant clock 2026-11-01; lot-tracked Variant V in Warehouse W with Lot L1 (expiry 2026-12-01, on-hand 5) and Lot L2 (expiry 2027-01-15, on-hand 5), no stock without a lot; the Owner holds L1 read with its version; a test hook that pauses a sale after posting engine step 1 and one that pauses API-INV-042 after its lock
Host data    : none
Steps        : 1. Start a sale of 2 of V from W; it pauses after step 1 (L1 read FOR SHARE). 2. Send PATCH L1 with expiryDate 2026-10-30. 3. Release the sale. 4. Re-read L1 and start the correction of L2 to 2026-10-30; it pauses after its FOR UPDATE. 5. Send a sale of 2 of V from W. 6. Release the correction. 7. GET moves of V in W and lots L1, L2.
Expected     : 2 → the PATCH waits until the sale commits. 3 → the sale records 1 ISSUE of 2 from L1, then L1 has expiry 2026-10-30. 5 → the sale waits until the correction commits. 6 → the sale then finds L1 and L2 expired and records 1 ISSUE of 2 without a lot (REQ-INV-111); no move draws from an expired lot. 7 → L1 on-hand 3, L2 on-hand 5.
Test data    : tenant clock 2026-11-01; INV_ALLOW_NEGATIVE_STOCK false (the Warehouse total 8 still covers the no-lot issue of step 6)
<!-- TC:TC-INV-279:END -->

<!-- TC:TC-INV-280:START traces=AC-INV-085,REQ-INV-078,API-INV-022,API-INV-028,API-INV-036,API-INV-047,API-INV-020 -->
### TC-INV-280 — A stock document dated after today is refused; today is accepted
Derived from : AC-INV-085  (REQ-INV-078)
Exercises    : API-INV-022 POST /api/v1/inv/transfers · API-INV-028 POST /api/v1/inv/stock-counts · API-INV-036 POST /api/v1/inv/stock-adjustments · API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-020
Rule / code  : RULE-INV-051 → INV-422-DOC-DATE-FUTURE
Package      : SVC-API-CRUD
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: Owner session; tenant clock 2026-11-01 in the tenant's time zone; 2 active Warehouses W1, W2; Variant V with stock in W1 and an active barcode
Host data    : none
Steps        : 1. POST a transfer W1 → W2 with docDate 2026-11-02 and 1 line of V. 2. POST a count in W1 with docDate 2026-11-02. 3. POST an adjustment in W1 with docDate 2026-11-02, reason DAMAGE, 1 line of V −1. 4. POST opening stock in W1 with docDate 2026-11-02 and 1 valid row. 5. POST the transfer of step 1 with docDate 2026-11-01. 6. GET transfers.
Expected     : 1 … 4 → 422 each, code INV-422-DOC-DATE-FUTURE, messageAr «تاريخ المستند لا يكون بعد اليوم.», messageEn "The document date cannot be after today."; nothing saved or posted. 5 → 201 with docDate 2026-11-01. 6 → exactly that transfer.
Test data    : 2026-11-02 (tomorrow), 2026-11-01 (today); INV_ADJUSTMENT_REASON DAMAGE (seeded)
<!-- TC:TC-INV-280:END -->

<!-- TC:TC-INV-281:START traces=AC-INV-113,REQ-INV-101,API-INV-036,API-INV-037,API-INV-034,API-INV-035 -->
### TC-INV-281 — The stock adjustment screen cannot use the OPENING_STOCK reason
Derived from : AC-INV-113  (REQ-INV-101)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · API-INV-037 PUT /api/v1/inv/stock-adjustments/{id} · read back API-INV-034, API-INV-035
Rule / code  : RULE-INV-033 → INV-422-REASON-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: Warehouse user session (no PERM_INV_OPENING_STOCK_CREATE); one active Warehouse; Variant V; a DRAFT adjustment A with reason DAMAGE, read with its version
Host data    : none
Steps        : 1. POST an adjustment with reasonCode OPENING_STOCK and 1 line of V +5 at unit cost 10.0000. 2. PUT A with reasonCode OPENING_STOCK. 3. GET adjustments and A.
Expected     : 1 and 2 → 422, code INV-422-REASON-REQUIRED, messageAr «اختر سبب التسوية.», messageEn "Choose the adjustment reason.". 3 → no new adjustment; A keeps reasonCode DAMAGE.
Test data    : INV_ADJUSTMENT_REASON OPENING_STOCK and DAMAGE (seeded)
<!-- TC:TC-INV-281:END -->

<!-- TC:TC-INV-282:START traces=AC-INV-032,REQ-INV-027,API-INV-015,API-INV-009 -->
### TC-INV-282 — Two concurrent default switches leave exactly one default branch
Derived from : AC-INV-032  (REQ-INV-027)
Exercises    : API-INV-015 PATCH /api/v1/inv/branches/{id}/default for two targets at once · read back API-INV-009
Rule / code  : RULE-INV-015 → —
Package      : SVC-API-CRUD
Scenario     : CONCURRENCY · data class EDGE · language ALL
Preconditions: Owner session; default Branch A; active Branches B and C, each read with its version; a test hook that pauses the first switch after its advisory lock
Host data    : none
Steps        : 1. Send PATCH B /default; it pauses after taking the lock. 2. Send PATCH C /default. 3. Release the first switch. 4. GET branches, active ALL.
Expected     : 2 → waits on the tenant's default-switch advisory lock (ADR-INV-050). 3 → both answer 200, B first, then C; no INV-500 and no unique-index violation. 4 → exactly 1 Branch has isDefaultFl true — C; A and B have isDefaultFl false.
Test data    : —
<!-- TC:TC-INV-282:END -->

<!-- TC:TC-INV-283:START traces=AC-INV-131,REQ-INV-117,API-INV-041 -->
### TC-INV-283 — The daily check notifies a lot held in two warehouses once, with its total
Derived from : AC-INV-131  (REQ-INV-117)
Exercises    : daily near-expiry job (InvNearExpiryJob, TenantRunner — QR-INV-038 job mode) · notification INV_NEAR_EXPIRY · read back API-INV-041
Rule / code  : — → —
Package      : CORE
Scenario     : STATE · data class EDGE · language ALL
Preconditions: lotExpiry on; INV_NEAR_EXPIRY_DAYS 30; 2 active Warehouses W1, W2; Lot L2 of V, expiry 2026-11-20, on-hand 6 in W1 and 4 in W2, nearExpiryNotifiedAt empty; 2 users holding the Warehouse role
Host data    : none
Steps        : 1. Run the daily check on 2026-11-01. 2. Read each Warehouse-role user's notifications. 3. GET lot L2.
Expected     : 2 → each user received exactly 1 notification «تشغيلة قريبة الانتهاء: {Variant} — L2 تنتهي في 2026-11-20 (الكمية 10).» / "Lot near expiry: {Variant} — L2 expires on 2026-11-20 (quantity 10)." — not one per warehouse. 3 → nearExpiryNotifiedAt set on 2026-11-01.
Test data    : tenant clock 2026-11-01
<!-- TC:TC-INV-283:END -->

<!-- TC:TC-INV-284:START traces=AC-INV-113,REQ-INV-101,API-INV-052 -->
### TC-INV-284 — INV serves its own lookup keys, and no other
Derived from : AC-INV-113  (REQ-INV-101)
Exercises    : API-INV-052 GET /api/v1/inv/lookups/{key} (InvLookups — OwnedLookups.read)
Rule / code  : PLATFORM-STD → INV-404-LOOKUP-KEY-UNKNOWN · REQ-INV-138 → INV-403
Package      : SVC-API-SEARCH
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a newly provisioned tenant; a user holding only the Seller/Cashier role; a user holding no INV permission
Host data    : none
Steps        : 1. As the Seller/Cashier, GET each of INV_ADJUSTMENT_REASON, INV_MOVE_TYPE, INV_DOC_STATUS, INV_RESERVATION_STATUS, INV_SOURCE_DOC_TYPE and INV_CHANGE_KIND. 2. GET CAT_UOM. 3. As the user with no INV permission, GET INV_DOC_STATUS.
Expected     : 1 → 200 each, with the seeded active values in sortOrder and their ar / en names — 5, 8, 3, 3, 10 and 5 values; INV_ADJUSTMENT_REASON lists COUNT_DIFFERENCE, DAMAGE, EXPIRY_WRITE_OFF, OPENING_STOCK, OTHER. 2 → 404, code INV-404-LOOKUP-KEY-UNKNOWN, messageAr «مفتاح القائمة غير معروف.», messageEn "Unknown lookup key.". 3 → 403, code INV-403, messageAr «ليست لديك صلاحية لهذا الإجراء.», messageEn "You do not have permission for this action.".
Test data    : INV_ADJUSTMENT_REASON, INV_MOVE_TYPE, INV_DOC_STATUS, INV_RESERVATION_STATUS, INV_SOURCE_DOC_TYPE, INV_CHANGE_KIND — all values seeded
<!-- TC:TC-INV-284:END -->
<!-- SUB:REVISE2-SCENARIOS:END -->
