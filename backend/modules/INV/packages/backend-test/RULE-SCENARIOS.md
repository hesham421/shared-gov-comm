<!-- source: PHASE:TEST-PLAN-BE / SUB:RULE-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-004, AC-INV-007, AC-INV-008, AC-INV-011, AC-INV-017, AC-INV-020, AC-INV-021, AC-INV-026, AC-INV-029, AC-INV-030, AC-INV-049, AC-INV-053, AC-INV-058, AC-INV-060, AC-INV-061, AC-INV-070, AC-INV-071, AC-INV-073, AC-INV-075, AC-INV-078, AC-INV-079, AC-INV-088, AC-INV-090, AC-INV-091, AC-INV-093, AC-INV-094, AC-INV-098, AC-INV-101, AC-INV-103, AC-INV-106, AC-INV-109, AC-INV-111, AC-INV-113, AC-INV-115, AC-INV-118, AC-INV-120, AC-INV-126, AC-INV-127, AC-INV-133, AC-INV-136, AC-INV-139, AC-INV-141, AC-INV-142, AC-INV-143, AC-INV-144, AC-INV-147, API-INV-001, API-INV-002, API-INV-003, API-INV-005, API-INV-007, API-INV-009, API-INV-010, API-INV-011, API-INV-013, API-INV-016, API-INV-017, API-INV-018, API-INV-019, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-027, API-INV-029, API-INV-032, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-044, API-INV-045, API-INV-047, API-INV-048, API-INV-049, API-INV-051, REQ-INV-003, REQ-INV-006, REQ-INV-008, REQ-INV-014, REQ-INV-017, REQ-INV-018, REQ-INV-022, REQ-INV-025, REQ-INV-044, REQ-INV-048, REQ-INV-052, REQ-INV-054, REQ-INV-055, REQ-INV-064, REQ-INV-065, REQ-INV-067, REQ-INV-069, REQ-INV-071, REQ-INV-072, REQ-INV-080, REQ-INV-082, REQ-INV-083, REQ-INV-084, REQ-INV-085, REQ-INV-088, REQ-INV-091, REQ-INV-093, REQ-INV-096, REQ-INV-098, REQ-INV-099, REQ-INV-101, REQ-INV-103, REQ-INV-105, REQ-INV-107, REQ-INV-113, REQ-INV-114, REQ-INV-119, REQ-INV-122, REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-128, REQ-INV-131 -->
<!-- SUB:RULE-SCENARIOS:START traces=AC-INV-004,AC-INV-007,AC-INV-008,AC-INV-011,AC-INV-017,AC-INV-020,AC-INV-021,AC-INV-026,AC-INV-029,AC-INV-030,AC-INV-049,AC-INV-053,AC-INV-058,AC-INV-060,AC-INV-061,AC-INV-070,AC-INV-071,AC-INV-073,AC-INV-075,AC-INV-078,AC-INV-079,AC-INV-088,AC-INV-090,AC-INV-091,AC-INV-093,AC-INV-094,AC-INV-098,AC-INV-101,AC-INV-103,AC-INV-106,AC-INV-109,AC-INV-111,AC-INV-113,AC-INV-115,AC-INV-118,AC-INV-120,AC-INV-126,AC-INV-127,AC-INV-133,AC-INV-136,AC-INV-139,AC-INV-141,AC-INV-142,AC-INV-143,AC-INV-144,AC-INV-147,REQ-INV-003,REQ-INV-006,REQ-INV-008,REQ-INV-014,REQ-INV-017,REQ-INV-018,REQ-INV-022,REQ-INV-025,REQ-INV-044,REQ-INV-048,REQ-INV-052,REQ-INV-054,REQ-INV-055,REQ-INV-064,REQ-INV-065,REQ-INV-067,REQ-INV-069,REQ-INV-071,REQ-INV-072,REQ-INV-080,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-088,REQ-INV-091,REQ-INV-093,REQ-INV-096,REQ-INV-098,REQ-INV-099,REQ-INV-101,REQ-INV-103,REQ-INV-105,REQ-INV-107,REQ-INV-113,REQ-INV-114,REQ-INV-119,REQ-INV-122,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-131 -->
### RULE-SCENARIOS — rule violations and state transitions

<!-- TC:TC-INV-001:START traces=AC-INV-004,REQ-INV-003,API-INV-003,API-INV-001 -->
### TC-INV-001 — A second warehouse with a taken Arabic name is refused
Derived from : AC-INV-004  (REQ-INV-003)
Exercises    : API-INV-003 POST /api/v1/inv/warehouses · read back API-INV-001 GET /api/v1/inv/warehouses
Rule / code  : RULE-INV-002 → INV-409-WAREHOUSE-NAME-TAKEN
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; an active Warehouse with nameAr «مستودع الفرع»
Host data    : none
Steps        : 1. POST a new warehouse with nameAr «مستودع الفرع». 2. GET the warehouses with name «مستودع الفرع», active ALL.
Expected     : 1 → 409, code INV-409-WAREHOUSE-NAME-TAKEN, messageAr «يوجد مستودع آخر بهذا الاسم.», messageEn "Another warehouse already has this name.". 2 → exactly 1 Warehouse carries that name (nothing saved).
Test data    : nameAr «مستودع الفرع»
<!-- TC:TC-INV-001:END -->

<!-- TC:TC-INV-002:START traces=AC-INV-007,REQ-INV-006,API-INV-005,API-INV-002 -->
### TC-INV-002 — The default warehouse cannot be deactivated
Derived from : AC-INV-007  (REQ-INV-006)
Exercises    : API-INV-005 DELETE /api/v1/inv/warehouses/{id} · read back API-INV-002 GET /api/v1/inv/warehouses/{id}
Rule / code  : RULE-INV-003 → INV-409-WAREHOUSE-DEFAULT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; the default Warehouse D (isDefaultFl true, active)
Host data    : none
Steps        : 1. DELETE warehouse D. 2. GET warehouse D.
Expected     : 1 → 409, code INV-409-WAREHOUSE-DEFAULT, messageAr «لا يمكن إيقاف المستودع الافتراضي. اجعل مستودعًا آخر افتراضيًا أولًا.», messageEn "The default warehouse cannot be deactivated. Set another warehouse as the default first.". 2 → D has isActiveFl true and isDefaultFl true.
Test data    : D = the tenant's default warehouse
<!-- TC:TC-INV-002:END -->

<!-- TC:TC-INV-003:START traces=AC-INV-008,REQ-INV-006,API-INV-005,API-INV-002 -->
### TC-INV-003 — A warehouse holding stock cannot be deactivated
Derived from : AC-INV-008  (REQ-INV-006)
Exercises    : API-INV-005 DELETE /api/v1/inv/warehouses/{id} · read back API-INV-002
Rule / code  : RULE-INV-004 → INV-409-WAREHOUSE-IN-USE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; a non-default active Warehouse W2 whose Stock balance for Variant V shows on-hand 5
Host data    : none
Steps        : 1. DELETE warehouse W2. 2. GET warehouse W2.
Expected     : 1 → 409, code INV-409-WAREHOUSE-IN-USE, messageAr «لا يمكن إيقاف مستودع فيه مخزون أو حجوزات قائمة. انقل المخزون أو سوِّه أولًا.», messageEn "A warehouse that holds stock or open reservations cannot be deactivated. Transfer or adjust the stock first.". 2 → W2 has isActiveFl true.
Test data    : on-hand 5 of V in W2
<!-- TC:TC-INV-003:END -->

<!-- TC:TC-INV-004:START traces=AC-INV-011,REQ-INV-008,API-INV-007,API-INV-001 -->
### TC-INV-004 — An inactive warehouse cannot become the default
Derived from : AC-INV-011  (REQ-INV-008)
Exercises    : API-INV-007 PATCH /api/v1/inv/warehouses/{id}/default · read back API-INV-001
Rule / code  : RULE-INV-006 → INV-409-WAREHOUSE-DEFAULT-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; default Warehouse D; inactive Warehouse C read with its version
Host data    : none
Steps        : 1. PATCH warehouse C /default with the version read. 2. GET warehouses, active ALL.
Expected     : 1 → 409, code INV-409-WAREHOUSE-DEFAULT-INACTIVE, messageAr «المستودع الافتراضي يجب أن يكون نشطًا.», messageEn "The default warehouse must be active.". 2 → nothing changed: D is still the only Warehouse with isDefaultFl true, C is inactive.
Test data    : C (inactive), D (default)
<!-- TC:TC-INV-004:END -->

<!-- TC:TC-INV-005:START traces=AC-INV-017,REQ-INV-014,API-INV-022,API-INV-020 -->
### TC-INV-005 — A transfer submitted while one warehouse is active is refused
Derived from : AC-INV-017  (REQ-INV-014)
Exercises    : API-INV-022 POST /api/v1/inv/transfers · read back API-INV-020 GET /api/v1/inv/transfers
Rule / code  : RULE-INV-016 → INV-422-TRANSFER-WAREHOUSES
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; the tenant has exactly 1 active Warehouse W
Host data    : none
Steps        : 1. POST a transfer directly to the backend, from W to W, with 1 line of 1 of Variant V. 2. GET transfers.
Expected     : 1 → 422, code INV-422-TRANSFER-WAREHOUSES, messageAr «التحويل يحتاج إلى مستودعين نشطين مختلفين.», messageEn "A transfer needs two different active warehouses.". 2 → empty page (nothing saved).
Test data    : W = the only active warehouse; a request built by hand (the screen hides the action)
<!-- TC:TC-INV-005:END -->

<!-- TC:TC-INV-006:START traces=AC-INV-020,REQ-INV-017,API-INV-018 -->
### TC-INV-006 — A receipt without a warehouse is refused when two warehouses are active
Derived from : AC-INV-020  (REQ-INV-017)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-018 GET /api/v1/inv/stock-moves
Rule / code  : RULE-INV-008 → INV-422-WAREHOUSE-REQUIRED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: 2 active Warehouses W1, W2; active Variant V of a STANDARD Item
Host data    : none
Steps        : 1. PUR calls receiveStock with move type RECEIPT, no warehouse, 1 line of V, a unit cost and an idempotency key. 2. GET moves of V.
Expected     : 1 → LocalizedException code INV-422-WAREHOUSE-REQUIRED, messageAr «اختر المستودع.», messageEn "Choose the warehouse.". 2 → no Stock move recorded.
Test data    : source document type PURCHASE_RECEIPT; quantity, cost and key are placeholders of the caller
<!-- TC:TC-INV-006:END -->

<!-- TC:TC-INV-007:START traces=AC-INV-021,REQ-INV-018,API-INV-018 -->
### TC-INV-007 — An issue naming an inactive warehouse is refused
Derived from : AC-INV-021  (REQ-INV-018)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : RULE-INV-007 → INV-422-WAREHOUSE-INACTIVE
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: inactive Warehouse W2; active Variant V
Host data    : none
Steps        : 1. SLS calls issueStock naming W2, move type ISSUE, 1 line of V, an idempotency key. 2. GET moves of V in W2.
Expected     : 1 → code INV-422-WAREHOUSE-INACTIVE, messageAr «المستودع غير نشط.», messageEn "The warehouse is inactive.". 2 → no Stock move recorded.
Test data    : W2 (isActiveFl false)
<!-- TC:TC-INV-007:END -->

<!-- TC:TC-INV-008:START traces=AC-INV-026,REQ-INV-022,API-INV-011,API-INV-009 -->
### TC-INV-008 — A second branch with a taken Arabic name is refused
Derived from : AC-INV-026  (REQ-INV-022)
Exercises    : API-INV-011 POST /api/v1/inv/branches · read back API-INV-009 GET /api/v1/inv/branches
Rule / code  : RULE-INV-011 → INV-409-BRANCH-NAME-TAKEN
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; an active Branch with nameAr «فرع المعادي»
Host data    : none
Steps        : 1. POST a new branch with nameAr «فرع المعادي». 2. GET branches with name «فرع المعادي», active ALL.
Expected     : 1 → 409, code INV-409-BRANCH-NAME-TAKEN, messageAr «يوجد فرع آخر بهذا الاسم.», messageEn "Another branch already has this name.". 2 → exactly 1 Branch carries that name.
Test data    : nameAr «فرع المعادي»
<!-- TC:TC-INV-008:END -->

<!-- TC:TC-INV-009:START traces=AC-INV-029,REQ-INV-025,API-INV-013,API-INV-010 -->
### TC-INV-009 — The default branch cannot be deactivated
Derived from : AC-INV-029  (REQ-INV-025)
Exercises    : API-INV-013 DELETE /api/v1/inv/branches/{id} · read back API-INV-010 GET /api/v1/inv/branches/{id}
Rule / code  : RULE-INV-012 → INV-409-BRANCH-DEFAULT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; the default Branch B
Host data    : none
Steps        : 1. DELETE branch B. 2. GET branch B.
Expected     : 1 → 409, code INV-409-BRANCH-DEFAULT, messageAr «لا يمكن إيقاف الفرع الافتراضي. اجعل فرعًا آخر افتراضيًا أولًا.», messageEn "The default branch cannot be deactivated. Set another branch as the default first.". 2 → B stays active.
Test data    : B = the tenant's default branch
<!-- TC:TC-INV-009:END -->

<!-- TC:TC-INV-010:START traces=AC-INV-030,REQ-INV-025,API-INV-013,API-INV-010 -->
### TC-INV-010 — A branch with an active warehouse cannot be deactivated
Derived from : AC-INV-030  (REQ-INV-025)
Exercises    : API-INV-013 DELETE /api/v1/inv/branches/{id} · read back API-INV-010
Rule / code  : RULE-INV-013 → INV-409-BRANCH-IN-USE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; a non-default Branch B2 with 1 active Warehouse
Host data    : none
Steps        : 1. DELETE branch B2. 2. GET branch B2.
Expected     : 1 → 409, code INV-409-BRANCH-IN-USE, messageAr «لا يمكن إيقاف فرع تتبعه مستودعات نشطة. انقل المستودعات إلى فرع آخر أو أوقفها أولًا.», messageEn "A branch with active warehouses cannot be deactivated. Move the warehouses to another branch or deactivate them first.". 2 → B2 stays active.
Test data    : B2 with 1 active Warehouse (branchPk = B2)
<!-- TC:TC-INV-010:END -->

<!-- TC:TC-INV-011:START traces=AC-INV-049,REQ-INV-044,API-INV-049,API-INV-017 -->
### TC-INV-011 — A reservation beyond availability is refused while negative stock is disallowed
Derived from : AC-INV-049  (REQ-INV-044)
Exercises    : InventoryApi.reserve — CON-INV-011 (in-process, caller SLS) · setup API-INV-049 PUT /api/v1/inv/settings · read back API-INV-017
Rule / code  : RULE-INV-020 → INV-409-RESERVE-NOT-AVAILABLE (in-process, §5.1)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: INV_ALLOW_NEGATIVE_STOCK false; Variant V available 3 in Warehouse W
Host data    : none
Steps        : 1. SLS calls reserve for 5 of V in W with a source document and an idempotency key. 2. GET the availability of V in W.
Expected     : 1 → code INV-409-RESERVE-NOT-AVAILABLE, messageAr «الكمية المتاحة لا تكفي للحجز. المتاح: 3.», messageEn "Not enough available stock to reserve. Available: 3.". 2 → reserved unchanged, available still 3 (no Reservation created).
Test data    : available 3, requested 5
<!-- TC:TC-INV-011:END -->

<!-- TC:TC-INV-012:START traces=AC-INV-053,REQ-INV-048,API-INV-018 -->
### TC-INV-012 — A consumed reservation cannot be released, and a closed reservation cannot be consumed
Derived from : AC-INV-053  (REQ-INV-048)
Exercises    : InventoryApi.releaseReservation — CON-INV-012 · InventoryApi.issueStock — CON-INV-015 · InventoryApi.reservationsOfDocument — CON-INV-013 (in-process, callers SFR and SLS) · read back API-INV-018
Rule / code  : RULE-INV-021 → INV-409-RESERVATION-CLOSED (in-process, §5.1)
Package      : SVC-API-INT
Scenario     : STATE · data class INVALID · language ALL
Preconditions: a Reservation R of Variant V in Warehouse W with statusCode CONSUMED (fully issued); a Reservation R2 of V in W with statusCode RELEASED
Host data    : none
Steps        : 1. SFR calls releaseReservation for R. 2. Read the reservations of R's document. 3. SLS calls issueStock with 1 line of V in W referencing R2. 4. GET moves of V in W.
Expected     : 1 → code INV-409-RESERVATION-CLOSED, messageAr «الحجز مغلق ولا يمكن استخدامه.», messageEn "The reservation is closed and cannot be used.". 2 → R is unchanged: statusCode CONSUMED, reservedQty 0, its closedAt unchanged. 3 → the same code and texts. 4 → no new Stock move recorded.
Test data    : R consumed by an earlier issue; R2 released earlier through releaseReservation (ADR-INV-055)
<!-- TC:TC-INV-012:END -->

<!-- TC:TC-INV-013:START traces=AC-INV-058,REQ-INV-052,API-INV-018 -->
### TC-INV-013 — An issue line of an Item inactive in the catalogue is refused
Derived from : AC-INV-058  (REQ-INV-052)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : RULE-INV-018 → INV-422-VARIANT-INACTIVE
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a Variant V whose Item is inactive in CAT (read through XM-INV-014 at request time)
Host data    : none
Steps        : 1. SLS calls issueStock with 1 line of V in Warehouse W. 2. GET moves of V.
Expected     : 1 → code INV-422-VARIANT-INACTIVE, messageAr «متغيّر الصنف غير نشط.», messageEn "The Variant is inactive.". 2 → no Stock move recorded.
Test data    : V of an inactive Item
<!-- TC:TC-INV-013:END -->

<!-- TC:TC-INV-014:START traces=AC-INV-060,REQ-INV-054,API-INV-018 -->
### TC-INV-014 — An issue of a bundle without active components is refused
Derived from : AC-INV-060  (REQ-INV-054)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : RULE-INV-022 → INV-422-BUNDLE-NO-COMPONENTS
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: bundles on; Bundle B whose component lines are all inactive in CAT
Host data    : none
Steps        : 1. SLS calls issueStock for 1 of B in Warehouse W. 2. GET moves with bundle B.
Expected     : 1 → code INV-422-BUNDLE-NO-COMPONENTS, messageAr «الحزمة ليس لها مكوّنات نشطة.», messageEn "The bundle has no active components.". 2 → no Stock move recorded.
Test data    : quantity 1 of B
<!-- TC:TC-INV-014:END -->

<!-- TC:TC-INV-015:START traces=AC-INV-061,REQ-INV-055,API-INV-019 -->
### TC-INV-015 — A recorded stock move is never changed
Derived from : AC-INV-061  (REQ-INV-055)
Exercises    : the stock-move persistence guard (insert-only repository, `@Immutable` entity, database trigger TRG_INV_STOCK_MOVE_IMMUTABLE — ENT-INV-004, ADR-INV-049) · API-INV-019 GET /api/v1/inv/stock-moves/{id}
Rule / code  : RULE-INV-023 → INV-409-STOCK-MOVE-IMMUTABLE (in-process, §5.1)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: a recorded Stock move M (any type)
Host data    : none
Steps        : 1. GET move M and keep its quantity. 2. Submit a change of M's quantity through the module's stock-move persistence path. 3. Load M as an entity, change its quantity and flush. 4. Run a direct SQL UPDATE of M's QUANTITY, then a direct SQL DELETE of M. 5. GET move M again.
Expected     : 2 → code INV-409-STOCK-MOVE-IMMUTABLE, messageAr «لا تُعدَّل حركات المخزون. صحّح الرصيد بتسوية مخزون.», messageEn "Stock moves cannot be changed. Correct the balance with a stock adjustment.". 3 → no UPDATE statement is issued (the entity is `@Immutable`). 4 → each statement fails with SQLSTATE P0001 and message INV-409-STOCK-MOVE-IMMUTABLE (the trigger). 5 → M equals step 1. The API document offers no change or delete operation on /stock-moves/{id}.
Test data    : M = any move; the new quantity is any value ≠ the stored one
<!-- TC:TC-INV-015:END -->

<!-- TC:TC-INV-016:START traces=AC-INV-070,REQ-INV-064,API-INV-051 -->
### TC-INV-016 — A customer return without cost of a never-valued Variant is refused
Derived from : AC-INV-070  (REQ-INV-064)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller SLS) · read back API-INV-051 GET /api/v1/inv/stock-valuation/{variantId}
Rule / code  : RULE-INV-025 → INV-422-NO-KNOWN-COST
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Variant V with no Stock valuation record
Host data    : none
Steps        : 1. SLS calls receiveStock with move type RETURN_IN, 1 line of 1 of V, no unit cost. 2. GET the valuation of V.
Expected     : 1 → code INV-422-NO-KNOWN-COST, messageAr «لا توجد تكلفة معروفة لهذا المتغيّر. أدخل تكلفة الوحدة.», messageEn "This Variant has no known cost. Enter the unit cost.". 2 → totalOnHandQty 0, stockValue 0, averageUnitCost null (no move recorded).
Test data    : source document type SALES_RETURN
<!-- TC:TC-INV-016:END -->

<!-- TC:TC-INV-017:START traces=AC-INV-071,REQ-INV-065,API-INV-049,API-INV-048 -->
### TC-INV-017 — A saved "not allowed" setting applies to the next online issue
Derived from : AC-INV-071  (REQ-INV-065)
Exercises    : API-INV-049 PUT /api/v1/inv/settings · API-INV-048 GET /api/v1/inv/settings · InventoryApi.issueStock — CON-INV-015
Rule / code  : RULE-INV-019 → INV-409-NEGATIVE-STOCK
Package      : SVC-API-CRUD
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Owner session; INV_ALLOW_NEGATIVE_STOCK true; Variant V on-hand 0 in Warehouse W
Host data    : none
Steps        : 1. PUT settings with allowNegativeStockFl false and the stored nearExpiryDays. 2. GET settings. 3. SLS calls issueStock, online (offlineFl false), 1 of V in W.
Expected     : 1 → 200. 2 → allowNegativeStockFl false. 3 → code INV-409-NEGATIVE-STOCK, messageAr «الرصيد لا يكفي: المتاح في المستودع 0. الرصيد السالب غير مسموح.», messageEn "Not enough stock: 0 on hand in this warehouse. Negative stock is not allowed."; no move recorded.
Test data    : {onHand} = 0
<!-- TC:TC-INV-017:END -->

<!-- TC:TC-INV-018:START traces=AC-INV-073,REQ-INV-067,API-INV-018 -->
### TC-INV-018 — An online issue below zero is refused while negative stock is disallowed
Derived from : AC-INV-073  (REQ-INV-067)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : RULE-INV-019 → INV-409-NEGATIVE-STOCK
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: INV_ALLOW_NEGATIVE_STOCK false; Variant V on-hand 3 in Warehouse W
Host data    : none
Steps        : 1. SLS calls issueStock, online, 5 of V in W. 2. GET moves of V in W.
Expected     : 1 → code INV-409-NEGATIVE-STOCK, messageAr «الرصيد لا يكفي: المتاح في المستودع 3. الرصيد السالب غير مسموح.», messageEn "Not enough stock: 3 on hand in this warehouse. Negative stock is not allowed.". 2 → no Stock move recorded; on-hand stays 3.
Test data    : on-hand 3, issue 5
<!-- TC:TC-INV-018:END -->

<!-- TC:TC-INV-019:START traces=AC-INV-075,REQ-INV-069,API-INV-018,API-INV-016 -->
### TC-INV-019 — A synchronised offline POS sale is recorded below zero
Derived from : AC-INV-075  (REQ-INV-069)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS, offlineFl true) · read back API-INV-018, API-INV-016
Rule / code  : RULE-INV-019 (not applied to offlineFl true) → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: INV_ALLOW_NEGATIVE_STOCK false; Variant V on-hand 1 in Warehouse W; 2 users holding the Warehouse role
Host data    : none
Steps        : 1. SLS synchronises an offline POS sale: issueStock with offlineFl true, 3 of V in W. 2. GET moves of V in W. 3. GET the balance of V in W. 4. Read the notifications of the 2 Warehouse-role users.
Expected     : 1 → success. 2 → 1 ISSUE move of 3 with offlineFl true. 3 → on-hand −2. 4 → each user received the REQ-INV-068 notification «رصيد سالب: {Variant} في {المستودع} = −2.» / "Negative stock: {Variant} in {Warehouse} = −2.".
Test data    : source document type POS_SALE
<!-- TC:TC-INV-019:END -->

<!-- TC:TC-INV-020:START traces=AC-INV-078,REQ-INV-071,API-INV-047,API-INV-034 -->
### TC-INV-020 — An opening stock load of 1001 rows is refused
Derived from : AC-INV-078  (REQ-INV-071)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-034 GET /api/v1/inv/stock-adjustments
Rule / code  : RULE-INV-027 → INV-422-LOAD-ROWS
Package      : SVC-API-CRUD
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: Owner session; one active Warehouse
Host data    : none
Steps        : 1. POST opening stock with 1001 rows. 2. GET adjustments with reasonCode OPENING_STOCK.
Expected     : 1 → 422, code INV-422-LOAD-ROWS, messageAr «الحد الأقصى 1000 سطر في التحميل الواحد.», messageEn "At most 1000 rows per load." (ADR-INV-038 — catalogue code asserted). 2 → no new adjustment (nothing posted).
Test data    : 1001 rows (limit 1000 stated by RULE-INV-027)
<!-- TC:TC-INV-020:END -->

<!-- TC:TC-INV-021:START traces=AC-INV-079,REQ-INV-072,API-INV-047,API-INV-034 -->
### TC-INV-021 — A load with one invalid row posts nothing and reports the row
Derived from : AC-INV-079  (REQ-INV-072)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-034
Rule / code  : RULE-INV-028 → INV-422-LOAD-ROWS-INVALID with detail INV-422-LOAD-ROW-VARIANT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; one active Warehouse; no active Variant has barcode V-404
Host data    : none
Steps        : 1. POST opening stock with 20 rows, row 7 naming barcode V-404 and rows 1–6, 8–20 valid. 2. GET adjustments with reasonCode OPENING_STOCK.
Expected     : 1 → 422, code INV-422-LOAD-ROWS-INVALID, messageAr «بعض أسطر التحميل غير صالحة؛ راجع الأسطر المذكورة.», messageEn "Some rows of the load are invalid; see the listed rows."; `details` lists rowNo 7 with code INV-422-LOAD-ROW-VARIANT, messageAr «لا يطابق هذا السطر متغيّر صنف نشطًا.», messageEn "This row matches no active Variant.", and no other row. 2 → nothing posted.
Test data    : row 7 barcode "V-404" (v1 names a Variant by barcode only — ADR-INV-035); other rows: active barcodes of active Variants, with quantity > 0 and a unit cost
<!-- TC:TC-INV-021:END -->

<!-- TC:TC-INV-022:START traces=AC-INV-088,REQ-INV-080,API-INV-024,API-INV-021 -->
### TC-INV-022 — Posting a transfer with no line is refused
Derived from : AC-INV-088  (REQ-INV-080)
Exercises    : API-INV-024 PATCH /api/v1/inv/transfers/{id}/post · read back API-INV-021 GET /api/v1/inv/transfers/{id}
Rule / code  : RULE-INV-031 → INV-422-NO-LINES
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; 2 active Warehouses; a DRAFT Transfer T with no lines, read with its version
Host data    : none
Steps        : 1. PATCH transfer T /post with its version. 2. GET transfer T.
Expected     : 1 → 422, code INV-422-NO-LINES, messageAr «أضف سطرًا واحدًا على الأقل قبل الترحيل.», messageEn "Add at least one line before posting.". 2 → statusCode DRAFT.
Test data    : T created with lines []
<!-- TC:TC-INV-022:END -->

<!-- TC:TC-INV-023:START traces=AC-INV-090,REQ-INV-082,API-INV-022,API-INV-020 -->
### TC-INV-023 — A transfer within one warehouse is refused
Derived from : AC-INV-090  (REQ-INV-082)
Exercises    : API-INV-022 POST /api/v1/inv/transfers · read back API-INV-020
Rule / code  : RULE-INV-016 → INV-422-TRANSFER-WAREHOUSES
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; 2 active Warehouses W1, W2
Host data    : none
Steps        : 1. POST a transfer from W1 to W1 with 1 line of Variant V. 2. GET transfers.
Expected     : 1 → 422, code INV-422-TRANSFER-WAREHOUSES, messageAr «التحويل يحتاج إلى مستودعين نشطين مختلفين.», messageEn "A transfer needs two different active warehouses.". 2 → no transfer saved.
Test data    : from = to = W1
<!-- TC:TC-INV-023:END -->

<!-- TC:TC-INV-024:START traces=AC-INV-091,REQ-INV-083,API-INV-024,API-INV-021 -->
### TC-INV-024 — Posting a transfer beyond the source balance is refused while negative stock is disallowed
Derived from : AC-INV-091  (REQ-INV-083)
Exercises    : API-INV-024 PATCH /api/v1/inv/transfers/{id}/post · read back API-INV-021
Rule / code  : RULE-INV-019 → INV-409-NEGATIVE-STOCK
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: INV_ALLOW_NEGATIVE_STOCK false; Variant V on-hand 3 in W1; a DRAFT Transfer W1 → W2 with 1 line of 5 of V
Host data    : none
Steps        : 1. PATCH the transfer /post with its version. 2. GET the transfer.
Expected     : 1 → 409, code INV-409-NEGATIVE-STOCK, messageAr «الرصيد لا يكفي: المتاح في المستودع 3. الرصيد السالب غير مسموح.», messageEn "Not enough stock: 3 on hand in this warehouse. Negative stock is not allowed.". 2 → statusCode DRAFT; no move recorded.
Test data    : on-hand 3, line 5
<!-- TC:TC-INV-024:END -->

<!-- TC:TC-INV-025:START traces=AC-INV-093,REQ-INV-084,API-INV-022,API-INV-020 -->
### TC-INV-025 — A transfer line of a lot-tracked Item without a lot is refused
Derived from : AC-INV-093  (REQ-INV-084)
Exercises    : API-INV-022 POST /api/v1/inv/transfers · read back API-INV-020
Rule / code  : RULE-INV-037 → INV-422-LOT-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: lotExpiry on; Variant V of an Item with lotTrackedFl true; 2 active Warehouses W1, W2
Host data    : none
Steps        : 1. POST a transfer W1 → W2 with 1 line of V and no lotPk. 2. GET transfers.
Expected     : 1 → 422, code INV-422-LOT-REQUIRED, messageAr «اختر التشغيلة لهذا الصنف.», messageEn "Choose the lot for this Item.". 2 → nothing saved.
Test data    : line quantity is a placeholder > 0
<!-- TC:TC-INV-025:END -->

<!-- TC:TC-INV-026:START traces=AC-INV-094,REQ-INV-085,API-INV-023,API-INV-021 -->
### TC-INV-026 — A posted transfer cannot be changed
Derived from : AC-INV-094  (REQ-INV-085)
Exercises    : API-INV-023 PUT /api/v1/inv/transfers/{id} · read back API-INV-021
Rule / code  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Package      : SVC-API-CRUD
Scenario     : STATE · data class INVALID · language ALL
Preconditions: a POSTED Transfer T read with its version and lines
Host data    : none
Steps        : 1. PUT transfer T with the line quantity changed. 2. GET transfer T.
Expected     : 1 → 409, code INV-409-DOCUMENT-CLOSED, messageAr «المستند مُرحَّل أو ملغى ولا يمكن تعديله.», messageEn "The document is posted or cancelled and cannot be changed.". 2 → T unchanged.
Test data    : new line quantity = stored quantity + 1
<!-- TC:TC-INV-026:END -->

<!-- TC:TC-INV-027:START traces=AC-INV-098,REQ-INV-088,API-INV-029,API-INV-027 -->
### TC-INV-027 — A negative counted quantity is refused
Derived from : AC-INV-098  (REQ-INV-088)
Exercises    : API-INV-029 POST /api/v1/inv/stock-counts/{id}/lines · read back API-INV-027 GET /api/v1/inv/stock-counts/{id}
Rule / code  : RULE-INV-047 → INV-422-COUNTED-NEGATIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; a DRAFT Stock count C; active Variant V
Host data    : none
Steps        : 1. POST a line on C with variantPk V and countedQty −1. 2. GET count C.
Expected     : 1 → 422, code INV-422-COUNTED-NEGATIVE, messageAr «الكمية المعدودة لا تكون سالبة.», messageEn "The counted quantity cannot be negative." (ADR-INV-038). 2 → no line changed.
Test data    : countedQty −1
<!-- TC:TC-INV-027:END -->

<!-- TC:TC-INV-028:START traces=AC-INV-101,REQ-INV-091,API-INV-029,API-INV-027 -->
### TC-INV-028 — An unknown barcode adds no count line
Derived from : AC-INV-101  (REQ-INV-091)
Exercises    : API-INV-029 POST /api/v1/inv/stock-counts/{id}/lines · read back API-INV-027
Rule / code  : RULE-INV-032 → INV-422-BARCODE-UNKNOWN
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a DRAFT Stock count C; CAT knows no barcode 999
Host data    : none
Steps        : 1. POST a line on C with barcode 999 and countedQty 1. 2. GET count C.
Expected     : 1 → 422, code INV-422-BARCODE-UNKNOWN, messageAr «الباركود غير معروف. اختر متغيّر الصنف يدويًا.», messageEn "Unknown barcode. Pick the Variant by hand.". 2 → no line added.
Test data    : barcode "999"
<!-- TC:TC-INV-028:END -->

<!-- TC:TC-INV-029:START traces=AC-INV-103,REQ-INV-093,API-INV-032,API-INV-027 -->
### TC-INV-029 — Posting an empty count is refused
Derived from : AC-INV-103  (REQ-INV-093)
Exercises    : API-INV-032 PATCH /api/v1/inv/stock-counts/{id}/post · read back API-INV-027
Rule / code  : RULE-INV-031 → INV-422-NO-LINES
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a DRAFT Stock count C with no line, read with its version
Host data    : none
Steps        : 1. PATCH count C /post with its version. 2. GET count C.
Expected     : 1 → 422, code INV-422-NO-LINES, messageAr «أضف سطرًا واحدًا على الأقل قبل الترحيل.», messageEn "Add at least one line before posting.". 2 → statusCode DRAFT.
Test data    : C without lines
<!-- TC:TC-INV-029:END -->

<!-- TC:TC-INV-030:START traces=AC-INV-106,REQ-INV-096,API-INV-029,API-INV-027 -->
### TC-INV-030 — A scan on a posted count changes nothing
Derived from : AC-INV-106  (REQ-INV-096)
Exercises    : API-INV-029 POST /api/v1/inv/stock-counts/{id}/lines · read back API-INV-027
Rule / code  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Package      : SVC-API-CRUD
Scenario     : STATE · data class INVALID · language ALL
Preconditions: a POSTED Stock count C; a barcode identifying active Variant V
Host data    : none
Steps        : 1. POST a line on C with that barcode and countedQty 1. 2. GET count C.
Expected     : 1 → 409, code INV-409-DOCUMENT-CLOSED, messageAr «المستند مُرحَّل أو ملغى ولا يمكن تعديله.», messageEn "The document is posted or cancelled and cannot be changed.". 2 → no line changed.
Test data    : countedQty 1
<!-- TC:TC-INV-030:END -->

<!-- TC:TC-INV-031:START traces=AC-INV-109,REQ-INV-098,API-INV-037,API-INV-035 -->
### TC-INV-031 — A second line with the same Variant and lot is refused
Derived from : AC-INV-109  (REQ-INV-098)
Exercises    : API-INV-037 PUT /api/v1/inv/stock-adjustments/{id} · read back API-INV-035 GET /api/v1/inv/stock-adjustments/{id}
Rule / code  : RULE-INV-048 → INV-422-DUPLICATE-LINE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a DRAFT Stock adjustment A with 1 line of Variant V −2 and no lot, read with its version
Host data    : none
Steps        : 1. PUT A with its lines plus a second line of V −1 with no lot. 2. GET A.
Expected     : 1 → 422, code INV-422-DUPLICATE-LINE, messageAr «هذا المتغيّر مذكور في سطر آخر بالتشغيلة نفسها.», messageEn "This Variant is already on another line with the same lot.". 2 → A still has 1 line (V −2).
Test data    : lines (V, no lot, −2) and (V, no lot, −1)
<!-- TC:TC-INV-031:END -->

<!-- TC:TC-INV-032:START traces=AC-INV-111,REQ-INV-099,API-INV-036,API-INV-045,API-INV-038,API-INV-035 -->
### TC-INV-032 — An adjustment line that changes nothing is refused
Derived from : AC-INV-111  (REQ-INV-099)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · API-INV-038 PATCH /api/v1/inv/stock-adjustments/{id}/post · read back API-INV-035
Rule / code  : RULE-INV-034 → INV-422-ZERO-CHANGE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner and Warehouse user sessions; dualQuantity on; one active Warehouse; Variant V of an Item that is not dual-quantity; Variant D of Item I, dual-quantity (measure unit metre), whose balances hold no measure
Host data    : none
Steps        : 1. POST an adjustment with 1 line of V, quantityChange 0, no measure change. 2. POST adjustment A with 1 line of D, quantityChange 0, measureQtyChange −0.500 (accepted: I is dual-quantity). 3. As Owner, PUT I's settings with dualQuantityFl false and its version (allowed: no balance of I holds a measure). 4. PATCH A /post with its version. 5. GET A.
Expected     : 1 → 422, code INV-422-ZERO-CHANGE, messageAr «أدخل تغيّرًا في الكمية أو القياس لا يساوي صفرًا.», messageEn "Enter a non-zero change of quantity or measure."; nothing saved. 2 → 201, A in DRAFT. 3 → 200. 4 → 422 with the same code and texts; `details` names A's line (lineRefKind LINE_PK, rowNo 1 — ADR-INV-046). 5 → statusCode DRAFT; no move recorded.
Test data    : quantityChange 0; measureQtyChange −0.500; INV_ADJUSTMENT_REASON DAMAGE (seeded); the AC's state is built through the API — no fixture (ADR-INV-038 as revised)
<!-- TC:TC-INV-032:END -->

<!-- TC:TC-INV-033:START traces=AC-INV-113,REQ-INV-101,API-INV-036,API-INV-034 -->
### TC-INV-033 — An adjustment without a reason is refused
Derived from : AC-INV-113  (REQ-INV-101)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · read back API-INV-034
Rule / code  : RULE-INV-033 → INV-422-REASON-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; one active Warehouse; active Variant V
Host data    : none
Steps        : 1. POST an adjustment with lines and no reason. 2. GET adjustments.
Expected     : 1 → 422, code INV-422-REASON-REQUIRED, messageAr «اختر سبب التسوية.», messageEn "Choose the adjustment reason.". 2 → nothing saved.
Test data    : reasonCode "" (present, empty — ADR-INV-038); 1 line of V −1
<!-- TC:TC-INV-033:END -->

<!-- TC:TC-INV-034:START traces=AC-INV-115,REQ-INV-103,API-INV-037,API-INV-035 -->
### TC-INV-034 — A posted adjustment cannot be changed
Derived from : AC-INV-115  (REQ-INV-103)
Exercises    : API-INV-037 PUT /api/v1/inv/stock-adjustments/{id} · read back API-INV-035
Rule / code  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Package      : SVC-API-CRUD
Scenario     : STATE · data class INVALID · language ALL
Preconditions: a POSTED Stock adjustment A with reasonCode DAMAGE, read with its version and lines
Host data    : none
Steps        : 1. PUT A with reasonCode OTHER and its lines unchanged. 2. GET A.
Expected     : 1 → 409, code INV-409-DOCUMENT-CLOSED, messageAr «المستند مُرحَّل أو ملغى ولا يمكن تعديله.», messageEn "The document is posted or cancelled and cannot be changed.". 2 → A unchanged (reasonCode DAMAGE).
Test data    : DAMAGE → OTHER (both seeded)
<!-- TC:TC-INV-034:END -->

<!-- TC:TC-INV-035:START traces=AC-INV-118,REQ-INV-105,API-INV-018 -->
### TC-INV-035 — A receipt line of a lot-tracked Item without a lot is refused
Derived from : AC-INV-118  (REQ-INV-105)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-018
Rule / code  : RULE-INV-035 → INV-422-LOT-EXPIRY-REQUIRED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: lotExpiry on; Variant V of an Item with lotTrackedFl true
Host data    : none
Steps        : 1. PUR calls receiveStock with 1 line of 10 of V, a unit cost and no lot number. 2. GET moves of V.
Expected     : 1 → code INV-422-LOT-EXPIRY-REQUIRED, messageAr «أدخل رقم التشغيلة وتاريخ الصلاحية لهذا الصنف.», messageEn "Enter the lot number and expiry date for this Item.". 2 → no Stock move recorded.
Test data    : quantity 10; source document type PURCHASE_RECEIPT
<!-- TC:TC-INV-035:END -->

<!-- TC:TC-INV-036:START traces=AC-INV-120,REQ-INV-107,API-INV-018 -->
### TC-INV-036 — A lot number received with a different expiry is refused
Derived from : AC-INV-120  (REQ-INV-107)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-018
Rule / code  : RULE-INV-036 → INV-422-LOT-EXPIRY-MISMATCH
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: lotExpiry on; lot-tracked Variant V with Lot L-77 expiry 2027-02-28
Host data    : none
Steps        : 1. PUR calls receiveStock with 1 line of V naming lot L-77 with expiry 2027-03-31. 2. GET moves of V.
Expected     : 1 → code INV-422-LOT-EXPIRY-MISMATCH, messageAr «التشغيلة L-77 مسجّلة بتاريخ صلاحية 2027-02-28.», messageEn "Lot L-77 is recorded with expiry date 2027-02-28.". 2 → no Stock move recorded.
Test data    : lot L-77; stored expiry 2027-02-28; submitted expiry 2027-03-31
<!-- TC:TC-INV-036:END -->

<!-- TC:TC-INV-037:START traces=AC-INV-126,REQ-INV-113,API-INV-018,API-INV-016 -->
### TC-INV-037 — An expired lot is kept out of a sale issue
Derived from : AC-INV-126  (REQ-INV-113)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS, sale) · read back API-INV-018, API-INV-016
Rule / code  : RULE-INV-038 (exclusion, raises no code) → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: lotExpiry on; today 2026-11-01; in W, lot-tracked V has L0 (expiry 2026-10-30, on-hand 5) and L2 (expiry 2026-12-01, on-hand 6)
Host data    : none
Steps        : 1. SLS calls issueStock for a sale of 3 of V from W. 2. GET moves of V in W. 3. GET balances of V in W.
Expected     : 2 → 1 ISSUE move of 3 from L2. 3 → L0 on-hand 5, L2 on-hand 3.
Test data    : tenant clock 2026-11-01
<!-- TC:TC-INV-037:END -->

<!-- TC:TC-INV-038:START traces=AC-INV-127,REQ-INV-114,API-INV-018 -->
### TC-INV-038 — A return to supplier from an expired lot is refused
Derived from : AC-INV-127  (REQ-INV-114)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller PUR, RETURN_OUT) · read back API-INV-018
Rule / code  : RULE-INV-039 → INV-422-LOT-EXPIRED (in-process, §5.1)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Lot L0 of V expired on 2026-10-30 with stock in Warehouse W; today 2026-11-01
Host data    : none
Steps        : 1. PUR calls issueStock with move type RETURN_OUT, 2 of V from L0 in W. 2. GET moves of V.
Expected     : 1 → code INV-422-LOT-EXPIRED, messageAr «التشغيلة منتهية الصلاحية. اشطبها بتسوية مخزون.», messageEn "The lot has expired. Write it off with a stock adjustment.". 2 → no Stock move recorded.
Test data    : source document type PURCHASE_RETURN; tenant clock 2026-11-01
<!-- TC:TC-INV-038:END -->

<!-- TC:TC-INV-039:START traces=AC-INV-133,REQ-INV-119,API-INV-049,API-INV-048 -->
### TC-INV-039 — A near-expiry horizon of 0 days is refused
Derived from : AC-INV-133  (REQ-INV-119)
Exercises    : API-INV-049 PUT /api/v1/inv/settings · read back API-INV-048 GET /api/v1/inv/settings
Rule / code  : REQ-INV-119 (no RULE — SRS D-04) → INV-422-HORIZON-RANGE
Package      : SVC-API-CRUD
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: Owner session; INV_NEAR_EXPIRY_DAYS 30
Host data    : none
Steps        : 1. PUT settings with nearExpiryDays 0 and the stored allowNegativeStockFl. 2. GET settings.
Expected     : 1 → 422, code INV-422-HORIZON-RANGE, messageAr «أدخل عدد أيام من 1 إلى 365.», messageEn "Enter a number of days from 1 to 365.". 2 → nearExpiryDays 30.
Test data    : 0 (range 1–365 stated by the AC)
<!-- TC:TC-INV-039:END -->

<!-- TC:TC-INV-040:START traces=AC-INV-136,REQ-INV-122,API-INV-018 -->
### TC-INV-040 — An issue of a dual-quantity Item without its measure is refused
Derived from : AC-INV-136  (REQ-INV-122)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : RULE-INV-040 → INV-422-MEASURE-REQUIRED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: dualQuantity on; Item "Cotton fabric" with dualQuantityFl true, its Variant V
Host data    : none
Steps        : 1. SLS calls issueStock for 1 roll of V with no measure. 2. GET moves of V.
Expected     : 1 → code INV-422-MEASURE-REQUIRED, messageAr «أدخل القياس (الوزن أو الطول) لهذا الصنف.», messageEn "Enter the measure (weight or length) for this Item.". 2 → no Stock move recorded.
Test data    : quantity 1
<!-- TC:TC-INV-040:END -->

<!-- TC:TC-INV-041:START traces=AC-INV-139,REQ-INV-124,API-INV-045,API-INV-044 -->
### TC-INV-041 — Stock settings for a bundle Item are refused
Derived from : AC-INV-139  (REQ-INV-124)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · read back API-INV-044
Rule / code  : RULE-INV-045 → INV-422-BUNDLE-SETTINGS
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; lotExpiry on; a BUNDLE Item B with no Item stock settings
Host data    : none
Steps        : 1. PUT settings of B with lotTrackedFl true, dualQuantityFl false. 2. GET settings of B.
Expected     : 1 → 422, code INV-422-BUNDLE-SETTINGS, messageAr «الحزمة لا تحمل مخزونًا؛ تُضبط إعدادات المخزون على مكوّناتها.», messageEn "A bundle holds no stock; set stock settings on its components.". 2 → lotTrackedFl false, dualQuantityFl false (nothing saved).
Test data    : B (itemKindCode BUNDLE in CAT)
<!-- TC:TC-INV-041:END -->

<!-- TC:TC-INV-042:START traces=AC-INV-141,REQ-INV-125,API-INV-045,API-INV-044 -->
### TC-INV-042 — Dual quantity without a measure unit is refused
Derived from : AC-INV-141  (REQ-INV-125)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · read back API-INV-044
Rule / code  : RULE-INV-041 → INV-422-MEASURE-UNIT-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; dualQuantity on; Item "Steel bar" with no Item stock settings
Host data    : none
Steps        : 1. PUT settings of "Steel bar" with dualQuantityFl true, lotTrackedFl false, no measureUomCode. 2. GET its settings.
Expected     : 1 → 422, code INV-422-MEASURE-UNIT-REQUIRED, messageAr «اختر وحدة القياس الثانية (وزن أو طول).», messageEn "Choose the measure unit (weight or length).". 2 → dualQuantityFl false (nothing saved).
Test data    : measureUomCode absent
<!-- TC:TC-INV-042:END -->

<!-- TC:TC-INV-043:START traces=AC-INV-142,REQ-INV-126,API-INV-045,API-INV-044 -->
### TC-INV-043 — Lot tracking cannot be cleared while a lot holds stock
Derived from : AC-INV-142  (REQ-INV-126)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · read back API-INV-044
Rule / code  : RULE-INV-042 → INV-409-LOT-TRACKING-IN-USE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; lotExpiry on; Item "Milk 1L" with lotTrackedFl true and Lot L2 on-hand 6
Host data    : none
Steps        : 1. PUT its settings with lotTrackedFl false and its version. 2. GET its settings.
Expected     : 1 → 409, code INV-409-LOT-TRACKING-IN-USE, messageAr «لا يمكن إلغاء تتبع التشغيلة: توجد تشغيلات فيها مخزون (1). اشطبها أو اصرفها أولًا.», messageEn "Lot tracking cannot be cleared: lots still hold stock (1). Write them off or issue them first.". 2 → lotTrackedFl true.
Test data    : {count} = 1 (lot L2)
<!-- TC:TC-INV-043:END -->

<!-- TC:TC-INV-044:START traces=AC-INV-143,REQ-INV-127,API-INV-045,API-INV-044 -->
### TC-INV-044 — Dual quantity cannot be cleared while a measure is held
Derived from : AC-INV-143  (REQ-INV-127)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · read back API-INV-044
Rule / code  : RULE-INV-043 → INV-409-DUAL-QUANTITY-IN-USE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; dualQuantity on; dual-quantity Item "Cotton fabric" with on-hand measure 120.000 metres in W
Host data    : none
Steps        : 1. PUT its settings with dualQuantityFl false and its version. 2. GET its settings.
Expected     : 1 → 409, code INV-409-DUAL-QUANTITY-IN-USE, messageAr «لا يمكن إلغاء الكمية المزدوجة: يوجد مخزون بقياس غير صفري. سوِّ القياس إلى صفر أولًا.», messageEn "Dual quantity cannot be cleared: stock still holds a non-zero measure. Adjust the measure to zero first.". 2 → dualQuantityFl true.
Test data    : on-hand measure 120.000
<!-- TC:TC-INV-044:END -->

<!-- TC:TC-INV-045:START traces=AC-INV-144,REQ-INV-128,API-INV-045,API-INV-044 -->
### TC-INV-045 — Lot tracking cannot be set while lotExpiry is off
Derived from : AC-INV-144  (REQ-INV-128)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · read back API-INV-044
Rule / code  : RULE-INV-044 → INV-422-CAPABILITY-OFF
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: Owner session; lotExpiry off; Item "Milk 1L" with no Item stock settings
Host data    : none
Steps        : 1. Send a request marking "Milk 1L" as tracked by lot: PUT with lotTrackedFl true, dualQuantityFl false. 2. GET its settings.
Expected     : 1 → 422, code INV-422-CAPABILITY-OFF, messageAr «فعِّل مفتاح القدرة الخاص بهذا الإعداد أولًا.», messageEn "Switch on the capability flag for this setting first." (RULE-INV-044 text, as AC-INV-144 states it). 2 → lotTrackedFl false (nothing saved).
Test data    : a request built by hand (the control is hidden while lotExpiry is off)
<!-- TC:TC-INV-045:END -->

<!-- TC:TC-INV-046:START traces=AC-INV-147,REQ-INV-131 -->
### TC-INV-046 — The lotExpiry turn-off check answers not allowed while Items depend on it
Derived from : AC-INV-147  (REQ-INV-131)
Exercises    : InventoryApi.canTurnOff(lotExpiry) — CON-INV-019 (in-process, caller: the flag owner)
Rule / code  : RULE-INV-046 → — (an answer, not an error)
Package      : SVC-API-INT
Scenario     : STATE · data class VALID · language ALL
Preconditions: 3 Items with lotTrackedFl true
Host data    : none
Steps        : 1. The flag owner calls canTurnOff for lotExpiry.
Expected     : allowedFl false, dependent count 3, the 3 Items with code and names, and the RULE-INV-046 message with {flag} «التشغيلة والصلاحية» / "Lot and expiry" and {count} 3: «لا يمكن إيقاف «التشغيلة والصلاحية»: 3 أصناف تعتمد عليه. ألغِ الإعداد لها أولًا.» / "Lot and expiry cannot be switched off: 3 Items depend on it. Clear the setting on them first.", as AC-INV-147 states it.
Test data    : 3 lot-tracked Items
<!-- TC:TC-INV-046:END -->
<!-- SUB:RULE-SCENARIOS:END -->
