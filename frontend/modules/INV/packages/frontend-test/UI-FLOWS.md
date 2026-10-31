<!-- source: PHASE:TEST-PLAN-FE / SUB:UI-FLOWS -->
<!-- context: TEST-PLAN-FE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-004, AC-INV-007, AC-INV-008, AC-INV-011, AC-INV-013, AC-INV-014, AC-INV-015, AC-INV-026, AC-INV-029, AC-INV-030, AC-INV-034, AC-INV-035, AC-INV-044, AC-INV-062, AC-INV-069, AC-INV-071, AC-INV-079, AC-INV-081, AC-INV-083, AC-INV-087, AC-INV-088, AC-INV-090, AC-INV-091, AC-INV-093, AC-INV-098, AC-INV-101, AC-INV-103, AC-INV-106, AC-INV-109, AC-INV-111, AC-INV-113, AC-INV-129, AC-INV-130, AC-INV-132, AC-INV-133, AC-INV-135, AC-INV-139, AC-INV-141, AC-INV-142, AC-INV-143, AC-INV-150, AC-INV-151, AC-INV-152, AC-INV-153, AC-INV-154, REQ-INV-003, REQ-INV-006, REQ-INV-008, REQ-INV-010, REQ-INV-011, REQ-INV-012, REQ-INV-022, REQ-INV-025, REQ-INV-029, REQ-INV-030, REQ-INV-039, REQ-INV-056, REQ-INV-063, REQ-INV-065, REQ-INV-072, REQ-INV-074, REQ-INV-076, REQ-INV-080, REQ-INV-082, REQ-INV-083, REQ-INV-084, REQ-INV-088, REQ-INV-091, REQ-INV-093, REQ-INV-096, REQ-INV-098, REQ-INV-099, REQ-INV-101, REQ-INV-115, REQ-INV-116, REQ-INV-118, REQ-INV-119, REQ-INV-121, REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-134, REQ-INV-135, REQ-INV-136, REQ-INV-137, REQ-INV-138, SCR-INV-001, SCR-INV-002, SCR-INV-003, SCR-INV-004, SCR-INV-005, SCR-INV-006, SCR-INV-007, SCR-INV-008, SCR-INV-009, SCR-INV-010, SCR-INV-011, SCR-INV-012 -->
<!-- SUB:UI-FLOWS:START traces=AC-INV-044,AC-INV-062,REQ-INV-039,REQ-INV-056,AC-INV-004,AC-INV-007,AC-INV-008,AC-INV-011,AC-INV-013,AC-INV-014,AC-INV-015,AC-INV-026,AC-INV-029,AC-INV-030,AC-INV-034,AC-INV-035,AC-INV-069,AC-INV-071,AC-INV-079,AC-INV-081,AC-INV-083,AC-INV-087,AC-INV-088,AC-INV-090,AC-INV-091,AC-INV-093,AC-INV-098,AC-INV-101,AC-INV-103,AC-INV-106,AC-INV-109,AC-INV-111,AC-INV-113,AC-INV-129,AC-INV-130,AC-INV-132,AC-INV-133,AC-INV-135,AC-INV-139,AC-INV-141,AC-INV-142,AC-INV-143,AC-INV-150,AC-INV-151,AC-INV-152,AC-INV-153,AC-INV-154,REQ-INV-003,REQ-INV-006,REQ-INV-008,REQ-INV-010,REQ-INV-011,REQ-INV-012,REQ-INV-022,REQ-INV-025,REQ-INV-029,REQ-INV-030,REQ-INV-063,REQ-INV-065,REQ-INV-072,REQ-INV-074,REQ-INV-076,REQ-INV-080,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-088,REQ-INV-091,REQ-INV-093,REQ-INV-096,REQ-INV-098,REQ-INV-099,REQ-INV-101,REQ-INV-115,REQ-INV-116,REQ-INV-118,REQ-INV-119,REQ-INV-121,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-134,REQ-INV-135,REQ-INV-136,REQ-INV-137,REQ-INV-138 -->
### UI-FLOWS — per-screen flows

<!-- TC:TC-INV-173:START traces=AC-INV-004,REQ-INV-003,SCR-INV-001 -->
### TC-INV-173 — The warehouse entry shows the taken-name refusal on the Arabic name
Derived from : AC-INV-004  (REQ-INV-003)
Exercises    : SCR-INV-001 /inventory/warehouses?entry=new
Rule / code  : RULE-INV-002 → INV-409-WAREHOUSE-NAME-TAKEN
Package      : F3-SCR-INV-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; an active Warehouse «مستودع الفرع»; the server answers 409 INV-409-WAREHOUSE-NAME-TAKEN to the create
Host data    : none
Steps        : 1. Open the new-warehouse drawer. 2. Type nameAr «مستودع الفرع». 3. Save.
Expected     : the drawer stays open; nameAr shows «يوجد مستودع آخر بهذا الاسم.» (ar) / "Another warehouse already has this name." (en); the result list is unchanged.
Test data    : nameAr «مستودع الفرع»
<!-- TC:TC-INV-173:END -->

<!-- TC:TC-INV-174:START traces=AC-INV-007,REQ-INV-006,SCR-INV-001 -->
### TC-INV-174 — Deactivating the default warehouse shows the refusal and its next action
Derived from : AC-INV-007  (REQ-INV-006)
Exercises    : SCR-INV-001 /inventory/warehouses — row action Deactivate
Rule / code  : RULE-INV-003 → INV-409-WAREHOUSE-DEFAULT
Package      : F2-SCR-INV-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; the default Warehouse D listed; the server answers 409 INV-409-WAREHOUSE-DEFAULT
Host data    : none
Steps        : 1. Open D's row menu, choose Deactivate, confirm.
Expected     : message ar «لا يمكن إيقاف المستودع الافتراضي. اجعل مستودعًا آخر افتراضيًا أولًا.» · en «The default warehouse cannot be deactivated. Set another warehouse as the default first.» with the next action "Set as default" on another row; D stays active with its default badge.
Test data    : —
<!-- TC:TC-INV-174:END -->

<!-- TC:TC-INV-175:START traces=AC-INV-008,REQ-INV-006,SCR-INV-001 -->
### TC-INV-175 — Deactivating a warehouse holding stock shows the refusal and "View balances"
Derived from : AC-INV-008  (REQ-INV-006)
Exercises    : SCR-INV-001 /inventory/warehouses — row action Deactivate
Rule / code  : RULE-INV-004 → INV-409-WAREHOUSE-IN-USE
Package      : F2-SCR-INV-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; a non-default Warehouse W2 with on-hand 5 of one Variant; the server answers 409 INV-409-WAREHOUSE-IN-USE
Host data    : none
Steps        : 1. Deactivate W2 and confirm. 2. Choose the offered "View balances".
Expected     : 1 → message ar «لا يمكن إيقاف مستودع فيه مخزون أو حجوزات قائمة. انقل المخزون أو سوِّه أولًا.» · en «A warehouse that holds stock or open reservations cannot be deactivated. Transfer or adjust the stock first.»; W2 stays active. 2 → SCR-INV-003 opens filtered on W2.
Test data    : —
<!-- TC:TC-INV-175:END -->

<!-- TC:TC-INV-176:START traces=AC-INV-011,REQ-INV-008,SCR-INV-001 -->
### TC-INV-176 — Setting an inactive warehouse as default shows the refusal
Derived from : AC-INV-011  (REQ-INV-008)
Exercises    : SCR-INV-001 /inventory/warehouses?active=ALL — row action Set as default
Rule / code  : RULE-INV-006 → INV-409-WAREHOUSE-DEFAULT-INACTIVE
Package      : F2-SCR-INV-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; inactive Warehouse C listed; the server answers 409 INV-409-WAREHOUSE-DEFAULT-INACTIVE
Host data    : none
Steps        : 1. Choose Set as default on C's row.
Expected     : message ar «المستودع الافتراضي يجب أن يكون نشطًا.» · en «The default warehouse must be active.»; the default badge stays on the previous default.
Test data    : —
<!-- TC:TC-INV-176:END -->

<!-- TC:TC-INV-177:START traces=AC-INV-013,REQ-INV-010,SCR-INV-007 -->
### TC-INV-177 — With one warehouse the adjustment entry has no warehouse field
Derived from : AC-INV-013  (REQ-INV-010)
Exercises    : SCR-INV-007 /inventory/stock-adjustments/new
Rule / code  : RULE-INV-008 → —
Package      : F4-SCR-INV-007
Scenario     : STATE · data class VALID · language ALL
Preconditions: Warehouse user session; the context answers activeWarehouseCount 1; default Warehouse W
Host data    : none
Steps        : 1. Open the new adjustment. 2. Choose reason DAMAGE, add 1 line, Save.
Expected     : 1 → no warehouse field is displayed. 2 → the create request carries warehousePk null; the saved adjustment answered by the server has warehousePk W.
Test data    : reasonCode DAMAGE
<!-- TC:TC-INV-177:END -->

<!-- TC:TC-INV-178:START traces=AC-INV-014,REQ-INV-011,SCR-INV-005 -->
### TC-INV-178 — With one warehouse the menu and the create action are hidden, existing transfers stay readable
Derived from : AC-INV-014  (REQ-INV-011)
Exercises    : inventory menu · SCR-INV-005 /inventory/transfers, /inventory/transfers/:id, /inventory/transfers/new (route gate)
Rule / code  : RULE-INV-016 → —
Package      : F4-SCR-INV-005
Scenario     : STATE · data class VALID · language ALL
Preconditions: Warehouse user session; the context answers activeWarehouseCount 1 (the tenant deactivated its second warehouse); a POSTED Transfer TR-0005 (transferPk T) from before the deactivation
Host data    : none
Steps        : 1. Open the inventory menu. 2. Deep-link to /inventory/transfers. 3. Deep-link to /inventory/transfers/T and choose "Stock moves". 4. Deep-link to /inventory/transfers/new.
Expected     : 1 → no transfer entry. 2 → the transfer search is shown and lists TR-0005; no "New" action is offered. 3 → TR-0005 opens read-only and its "Stock moves" link goes to /inventory/stock-moves?sourceDocTypeCode=INV_TRANSFER&sourceDocRef=T. 4 → the route shows ar «التحويل يحتاج إلى مستودعين نشطين مختلفين.» · en «A transfer needs two different active warehouses.» with a link to Warehouses, not the entry (SRS SCR-REQ-INV-005 B1).
Test data    : —
<!-- TC:TC-INV-178:END -->

<!-- TC:TC-INV-179:START traces=AC-INV-015,REQ-INV-012,SCR-INV-003 -->
### TC-INV-179 — With one warehouse the balance screen has no warehouse filter or column
Derived from : AC-INV-015  (REQ-INV-012)
Exercises    : SCR-INV-003 /inventory/stock-balances
Rule / code  : — → —
Package      : F4-SCR-INV-003
Scenario     : STATE · data class VALID · language ALL
Preconditions: a session with PERM_INV_STOCK_BALANCES_VIEW; the context answers activeWarehouseCount 1; balance rows exist
Host data    : none
Steps        : 1. Open the stock balance screen and search.
Expected     : the search area shows no warehouse filter; the result shows no warehouse column.
Test data    : —
<!-- TC:TC-INV-179:END -->

<!-- TC:TC-INV-180:START traces=AC-INV-026,REQ-INV-022,SCR-INV-002 -->
### TC-INV-180 — The branch entry shows the taken-name refusal on the Arabic name
Derived from : AC-INV-026  (REQ-INV-022)
Exercises    : SCR-INV-002 /inventory/branches?entry=new
Rule / code  : RULE-INV-011 → INV-409-BRANCH-NAME-TAKEN
Package      : F3-SCR-INV-002
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; an active Branch «فرع المعادي»; the server answers 409 INV-409-BRANCH-NAME-TAKEN
Host data    : none
Steps        : 1. Open the new-branch drawer, type nameAr «فرع المعادي», Save.
Expected     : the drawer stays open; nameAr shows ar «يوجد فرع آخر بهذا الاسم.» · en «Another branch already has this name.».
Test data    : nameAr «فرع المعادي»
<!-- TC:TC-INV-180:END -->

<!-- TC:TC-INV-181:START traces=AC-INV-029,REQ-INV-025,SCR-INV-002 -->
### TC-INV-181 — Deactivating the default branch shows the refusal
Derived from : AC-INV-029  (REQ-INV-025)
Exercises    : SCR-INV-002 /inventory/branches — row action Deactivate
Rule / code  : RULE-INV-012 → INV-409-BRANCH-DEFAULT
Package      : F2-SCR-INV-002
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; the default Branch listed; the server answers 409 INV-409-BRANCH-DEFAULT
Host data    : none
Steps        : 1. Deactivate the default Branch and confirm.
Expected     : message ar «لا يمكن إيقاف الفرع الافتراضي. اجعل فرعًا آخر افتراضيًا أولًا.» · en «The default branch cannot be deactivated. Set another branch as the default first.»; the Branch stays active.
Test data    : —
<!-- TC:TC-INV-181:END -->

<!-- TC:TC-INV-182:START traces=AC-INV-030,REQ-INV-025,SCR-INV-002 -->
### TC-INV-182 — Deactivating a branch with active warehouses shows the refusal and "View warehouses"
Derived from : AC-INV-030  (REQ-INV-025)
Exercises    : SCR-INV-002 /inventory/branches — row action Deactivate
Rule / code  : RULE-INV-013 → INV-409-BRANCH-IN-USE
Package      : F2-SCR-INV-002
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; a non-default Branch B2 with 1 active Warehouse; the server answers 409 INV-409-BRANCH-IN-USE
Host data    : none
Steps        : 1. Deactivate B2 and confirm. 2. Choose "View warehouses".
Expected     : 1 → message ar «لا يمكن إيقاف فرع تتبعه مستودعات نشطة. انقل المستودعات إلى فرع آخر أو أوقفها أولًا.» · en «A branch with active warehouses cannot be deactivated. Move the warehouses to another branch or deactivate them first.»; B2 stays active. 2 → SCR-INV-001 opens filtered on B2.
Test data    : —
<!-- TC:TC-INV-182:END -->

<!-- TC:TC-INV-183:START traces=AC-INV-034,REQ-INV-029,SCR-INV-001 -->
### TC-INV-183 — With one branch the warehouse entry has no branch field
Derived from : AC-INV-034  (REQ-INV-029)
Exercises    : SCR-INV-001 /inventory/warehouses?entry=new
Rule / code  : — → —
Package      : F4-SCR-INV-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: Owner session; the context answers activeBranchCount 1
Host data    : none
Steps        : 1. Open the new-warehouse drawer.
Expected     : no branch field is displayed.
Test data    : —
<!-- TC:TC-INV-183:END -->

<!-- TC:TC-INV-184:START traces=AC-INV-035,REQ-INV-030,SCR-INV-001 -->
### TC-INV-184 — With one branch the warehouse search has no branch filter or column
Derived from : AC-INV-035  (REQ-INV-030)
Exercises    : SCR-INV-001 /inventory/warehouses
Rule / code  : — → —
Package      : F4-SCR-INV-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: Owner session; the context answers activeBranchCount 1; warehouses exist
Host data    : none
Steps        : 1. Open the warehouse search.
Expected     : the search area shows no branch filter; the result shows no branch column.
Test data    : —
<!-- TC:TC-INV-184:END -->

<!-- TC:TC-INV-185:START traces=AC-INV-079,REQ-INV-072,SCR-INV-010 -->
### TC-INV-185 — A load refused for one row lists that row with its message
Derived from : AC-INV-079  (REQ-INV-072)
Exercises    : SCR-INV-010 /inventory/opening-stock — Post
Rule / code  : RULE-INV-028 → INV-422-LOAD-ROWS-INVALID (detail INV-422-LOAD-ROW-VARIANT)
Package      : F2-SCR-INV-010
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; 20 rows in the form, row 7 with barcode V-404; the server answers 422 INV-422-LOAD-ROWS-INVALID with details [rowNo 7, INV-422-LOAD-ROW-VARIANT]
Host data    : none
Steps        : 1. Post.
Expected     : the row failures list shows row 7 with ar «لا يطابق هذا السطر متغيّر صنف نشطًا.» · en «This row matches no active Variant.»; the screen stays on opening stock (nothing posted, no navigation); the 20 rows are kept.
Test data    : row 7 barcode "V-404"
<!-- TC:TC-INV-185:END -->

<!-- TC:TC-INV-186:START traces=AC-INV-081,REQ-INV-074,SCR-INV-010 -->
### TC-INV-186 — An opening stock line without unit cost is refused on the line
Derived from : AC-INV-081  (REQ-INV-074)
Exercises    : SCR-INV-010 /inventory/opening-stock?line=new
Rule / code  : RULE-INV-026 → INV-422-OPENING-COST-REQUIRED
Package      : F3-SCR-INV-010
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session
Host data    : none
Steps        : 1. Add 1 line of 10 of V with an empty unit cost. 2. Post.
Expected     : the line shows ar «أدخل تكلفة الوحدة للمخزون الافتتاحي.» · en «Enter the unit cost of the opening stock.»; no opening stock request is sent (nothing posted).
Test data    : quantity 10, unit cost empty
<!-- TC:TC-INV-186:END -->

<!-- TC:TC-INV-187:START traces=AC-INV-083,REQ-INV-076,SCR-INV-004 -->
### TC-INV-187 — The running balance column shows only for one Variant and one warehouse
Derived from : AC-INV-083  (REQ-INV-076)
Exercises    : SCR-INV-004 /inventory/stock-moves
Rule / code  : — → —
Package      : F4-SCR-INV-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; 2 active Warehouses; moves of V in W: RECEIPT 10, ISSUE 4, ADJUSTMENT_OUT 1, answered with balanceAfterQty 10, 6, 5
Host data    : none
Steps        : 1. Filter on V only. 2. Filter on V and W.
Expected     : 1 → no "Balance after move" column. 2 → the column shows 5, 6 and 10 (newest first) for those moves.
Test data    : —
<!-- TC:TC-INV-187:END -->

<!-- TC:TC-INV-188:START traces=AC-INV-088,REQ-INV-080,SCR-INV-005 -->
### TC-INV-188 — Posting a transfer with no line shows the refusal and keeps it DRAFT
Derived from : AC-INV-088  (REQ-INV-080)
Exercises    : SCR-INV-005 /inventory/transfers/:id — Post
Rule / code  : RULE-INV-031 → INV-422-NO-LINES
Package      : F2-SCR-INV-005
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; 2 active Warehouses; a saved DRAFT transfer with no lines; the server answers 422 INV-422-NO-LINES to a post
Host data    : none
Steps        : 1. Open the transfer. 2. Post.
Expected     : message ar «أضف سطرًا واحدًا على الأقل قبل الترحيل.» · en «Add at least one line before posting.»; the status shows DRAFT.
Test data    : —
<!-- TC:TC-INV-188:END -->

<!-- TC:TC-INV-189:START traces=AC-INV-090,REQ-INV-082,SCR-INV-005 -->
### TC-INV-189 — The same warehouse as source and destination is refused before saving
Derived from : AC-INV-090  (REQ-INV-082)
Exercises    : SCR-INV-005 /inventory/transfers/new
Rule / code  : RULE-INV-016 → INV-422-TRANSFER-WAREHOUSES
Package      : F3-SCR-INV-005
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; 2 active Warehouses W1, W2
Host data    : none
Steps        : 1. Choose from W1 and to W1, add a line, Save.
Expected     : toWarehousePk shows ar «التحويل يحتاج إلى مستودعين نشطين مختلفين.» · en «A transfer needs two different active warehouses.»; no create request is sent.
Test data    : from = to = W1
<!-- TC:TC-INV-189:END -->

<!-- TC:TC-INV-190:START traces=AC-INV-091,REQ-INV-083,SCR-INV-005 -->
### TC-INV-190 — Posting a transfer beyond the source balance shows the negative-stock refusal
Derived from : AC-INV-091  (REQ-INV-083)
Exercises    : SCR-INV-005 /inventory/transfers/:id — Post
Rule / code  : RULE-INV-019 → INV-409-NEGATIVE-STOCK
Package      : F2-SCR-INV-005
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a saved DRAFT transfer W1 → W2 of 5 of V; the server answers 409 INV-409-NEGATIVE-STOCK with the texts for on-hand 3
Host data    : none
Steps        : 1. Post.
Expected     : message ar «الرصيد لا يكفي: المتاح في المستودع 3. الرصيد السالب غير مسموح.» · en «Not enough stock: 3 on hand in this warehouse. Negative stock is not allowed.»; the status shows DRAFT.
Test data    : on-hand 3
<!-- TC:TC-INV-190:END -->

<!-- TC:TC-INV-191:START traces=AC-INV-093,REQ-INV-084,SCR-INV-005 -->
### TC-INV-191 — A transfer line of a lot-tracked Item asks for its lot
Derived from : AC-INV-093  (REQ-INV-084)
Exercises    : SCR-INV-005 /inventory/transfers/new?line=new
Rule / code  : RULE-INV-037 → INV-422-LOT-REQUIRED
Package      : F3-SCR-INV-005
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: lotExpiry on; Variant V whose Item settings answer lotTrackedFl true; 2 active Warehouses
Host data    : none
Steps        : 1. In the line drawer pick V, enter a quantity, leave the lot empty, confirm. 2. Save.
Expected     : 1 → the lot field shows ar «اختر التشغيلة لهذا الصنف.» · en «Choose the lot for this Item.» and the line is not returned to the form. 2 → nothing is saved.
Test data    : —
<!-- TC:TC-INV-191:END -->

<!-- TC:TC-INV-192:START traces=AC-INV-098,REQ-INV-088,SCR-INV-006 -->
### TC-INV-192 — A negative counted quantity is refused before the scan is sent
Derived from : AC-INV-098  (REQ-INV-088)
Exercises    : SCR-INV-006 /inventory/stock-counts/:id — scan bar
Rule / code  : RULE-INV-047 → INV-422-COUNTED-NEGATIVE
Package      : F3-SCR-INV-006
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; a DRAFT count open
Host data    : none
Steps        : 1. Enter a barcode and counted quantity −1, submit.
Expected     : countedQty shows ar «الكمية المعدودة لا تكون سالبة.» · en «The counted quantity cannot be negative.»; no line call is sent; no line changes.
Test data    : countedQty −1
<!-- TC:TC-INV-192:END -->

<!-- TC:TC-INV-193:START traces=AC-INV-101,REQ-INV-091,SCR-INV-006 -->
### TC-INV-193 — An unknown barcode on a count offers the Variant picker
Derived from : AC-INV-101  (REQ-INV-091)
Exercises    : SCR-INV-006 /inventory/stock-counts/:id — scan bar
Rule / code  : RULE-INV-032 → INV-422-BARCODE-UNKNOWN
Package      : F2-SCR-INV-006
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a DRAFT count open; CAT knows no barcode 999; the server answers 422 INV-422-BARCODE-UNKNOWN to a line with barcode 999
Host data    : none
Steps        : 1. Scan 999.
Expected     : the barcode field shows ar «الباركود غير معروف. اختر متغيّر الصنف يدويًا.» · en «Unknown barcode. Pick the Variant by hand.»; the Variant picker replaces the barcode field; no line is added.
Test data    : barcode "999"
<!-- TC:TC-INV-193:END -->

<!-- TC:TC-INV-194:START traces=AC-INV-103,REQ-INV-093,SCR-INV-006 -->
### TC-INV-194 — Posting an empty count shows the refusal and keeps it DRAFT
Derived from : AC-INV-103  (REQ-INV-093)
Exercises    : SCR-INV-006 /inventory/stock-counts/:id — Post
Rule / code  : RULE-INV-031 → INV-422-NO-LINES
Package      : F2-SCR-INV-006
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a DRAFT count with no line; the server answers 422 INV-422-NO-LINES
Host data    : none
Steps        : 1. Post and confirm.
Expected     : message ar «أضف سطرًا واحدًا على الأقل قبل الترحيل.» · en «Add at least one line before posting.»; status DRAFT.
Test data    : —
<!-- TC:TC-INV-194:END -->

<!-- TC:TC-INV-195:START traces=AC-INV-106,REQ-INV-096,SCR-INV-006 -->
### TC-INV-195 — A scan on a count posted meanwhile shows the refusal and reloads read-only
Derived from : AC-INV-106  (REQ-INV-096)
Exercises    : SCR-INV-006 /inventory/stock-counts/:id — scan bar
Rule / code  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Package      : F2-SCR-INV-006
Scenario     : STATE · data class INVALID · language ALL
Preconditions: a count opened while DRAFT, then posted in another session (ADR-INV-038); the server answers 409 INV-409-DOCUMENT-CLOSED to the next line call and POSTED to the re-read
Host data    : none
Steps        : 1. Scan a barcode.
Expected     : message ar «المستند مُرحَّل أو ملغى ولا يمكن تعديله.» · en «The document is posted or cancelled and cannot be changed.»; the entry reloads read-only (scan bar hidden); no line changes.
Test data    : —
<!-- TC:TC-INV-195:END -->

<!-- TC:TC-INV-196:START traces=AC-INV-109,REQ-INV-098,SCR-INV-007 -->
### TC-INV-196 — A second line with the same Variant and lot is refused in the adjustment form
Derived from : AC-INV-109  (REQ-INV-098)
Exercises    : SCR-INV-007 /inventory/stock-adjustments/:id?line=new
Rule / code  : RULE-INV-048 → INV-422-DUPLICATE-LINE
Package      : F3-SCR-INV-007
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a DRAFT adjustment with a line of V −2 and no lot
Host data    : none
Steps        : 1. Add a second line of V −1 with no lot. 2. Save.
Expected     : the second line shows ar «هذا المتغيّر مذكور في سطر آخر بالتشغيلة نفسها.» · en «This Variant is already on another line with the same lot.»; no update request is sent.
Test data    : —
<!-- TC:TC-INV-196:END -->

<!-- TC:TC-INV-197:START traces=AC-INV-111,REQ-INV-099,SCR-INV-007 -->
### TC-INV-197 — Posting an adjustment with a zero-change line shows the refusal on the line
Derived from : AC-INV-111  (REQ-INV-099)
Exercises    : SCR-INV-007 /inventory/stock-adjustments/:id — Post
Rule / code  : RULE-INV-034 → INV-422-ZERO-CHANGE
Package      : F2-SCR-INV-007
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: the server answers a DRAFT adjustment with 1 line whose quantityChange is 0 and measureQtyChange empty, and 422 INV-422-ZERO-CHANGE (details rowNo 1) to a post
Host data    : none
Steps        : 1. Open the adjustment. 2. Post.
Expected     : the line's quantityChange shows ar «أدخل تغيّرًا في الكمية أو القياس لا يساوي صفرًا.» · en «Enter a non-zero change of quantity or measure.»; status DRAFT.
Test data    : —
<!-- TC:TC-INV-197:END -->

<!-- TC:TC-INV-198:START traces=AC-INV-113,REQ-INV-101,SCR-INV-007 -->
### TC-INV-198 — An adjustment without a reason is refused before saving
Derived from : AC-INV-113  (REQ-INV-101)
Exercises    : SCR-INV-007 /inventory/stock-adjustments/new
Rule / code  : RULE-INV-033 → INV-422-REASON-REQUIRED
Package      : F3-SCR-INV-007
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session
Host data    : none
Steps        : 1. Add lines, leave the reason empty, Save.
Expected     : reasonCode shows ar «اختر سبب التسوية.» · en «Choose the adjustment reason.»; no create request is sent.
Test data    : —
<!-- TC:TC-INV-198:END -->

<!-- TC:TC-INV-199:START traces=AC-INV-129,REQ-INV-115,SCR-INV-008 -->
### TC-INV-199 — The near-expiry view lists an expired lot with stock, marked "Expired"
Derived from : AC-INV-129  (REQ-INV-115)
Exercises    : SCR-INV-008 /inventory/lots?nearExpiryOnly=true
Rule / code  : RULE-INV-038 → —
Package      : F4-SCR-INV-008
Scenario     : STATE · data class EDGE · language ALL
Preconditions: lotExpiry on; Warehouse user session; the server answers lot L0 with onHandQty 5 and expiredFl true
Host data    : none
Steps        : 1. Open the near-expiry view.
Expected     : L0 is listed with on-hand 5 and the mark «منتهية الصلاحية» / "Expired".
Test data    : —
<!-- TC:TC-INV-199:END -->

<!-- TC:TC-INV-200:START traces=AC-INV-130,REQ-INV-116,SCR-INV-008 -->
### TC-INV-200 — The near-expiry view asks for the horizon list and shows days left
Derived from : AC-INV-130  (REQ-INV-116)
Exercises    : SCR-INV-008 /inventory/lots — "Near expiry only"
Rule / code  : — → —
Package      : F2-SCR-INV-008
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: lotExpiry on; the server answers nearExpiryOnly=true with L2 (expiry 2026-11-20, daysLeft 19, on-hand 6) and not L3
Host data    : none
Steps        : 1. Switch on "Near expiry only".
Expected     : the search is sent with nearExpiryOnly true; L2 is listed with 19 days left; L3 is not listed.
Test data    : —
<!-- TC:TC-INV-200:END -->

<!-- TC:TC-INV-201:START traces=AC-INV-132,REQ-INV-118,SCR-INV-011 -->
### TC-INV-201 — Inventory settings show the 30-day default and save 60
Derived from : AC-INV-132  (REQ-INV-118)
Exercises    : SCR-INV-011 /inventory/settings
Rule / code  : — → —
Package      : F2-SCR-INV-011
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; lotExpiry on; the server answers settings never saved (nearExpiryDays 30, allowNegativeStockFl true)
Host data    : none
Steps        : 1. Open inventory settings. 2. Set the horizon to 60 and Save. 3. Open the near-expiry view.
Expected     : 1 → the horizon shows 30 days and "Allow negative stock" is on. 2 → the request carries nearExpiryDays 60. 3 → the lot list is fetched again after the save (not served from the earlier cache).
Test data    : 60
<!-- TC:TC-INV-201:END -->

<!-- TC:TC-INV-202:START traces=AC-INV-133,REQ-INV-119,SCR-INV-011 -->
### TC-INV-202 — A horizon of 0 days is refused and the saved value stays
Derived from : AC-INV-133  (REQ-INV-119)
Exercises    : SCR-INV-011 /inventory/settings
Rule / code  : REQ-INV-119 → INV-422-HORIZON-RANGE
Package      : F3-SCR-INV-011
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: Owner session; lotExpiry on; saved horizon 30
Host data    : none
Steps        : 1. Enter 0 and Save. 2. Reload the screen.
Expected     : 1 → the field shows ar «أدخل عدد أيام من 1 إلى 365.» · en «Enter a number of days from 1 to 365.»; no save request is sent. 2 → the horizon shows 30.
Test data    : 0
<!-- TC:TC-INV-202:END -->

<!-- TC:TC-INV-203:START traces=AC-INV-135,REQ-INV-121,SCR-INV-003 -->
### TC-INV-203 — A dual-quantity balance row shows its measure with the unit
Derived from : AC-INV-135  (REQ-INV-121)
Exercises    : SCR-INV-003 /inventory/stock-balances
Rule / code  : — → —
Package      : F4-SCR-INV-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: dualQuantity on; the server answers "Cotton fabric" with onHandQty 3, onHandMeasureQty 120.500 and the metre code
Host data    : none
Steps        : 1. Search balances for "Cotton fabric".
Expected     : the row shows on-hand 3 and measure 120.500 with the metre unit label.
Test data    : —
<!-- TC:TC-INV-203:END -->

<!-- TC:TC-INV-204:START traces=AC-INV-139,REQ-INV-124,SCR-INV-009 -->
### TC-INV-204 — Stock settings for a bundle Item show the refusal on the Item
Derived from : AC-INV-139  (REQ-INV-124)
Exercises    : SCR-INV-009 /inventory/item-stock-settings?entry=new
Rule / code  : RULE-INV-045 → INV-422-BUNDLE-SETTINGS
Package      : F2-SCR-INV-009
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; lotExpiry on; a BUNDLE Item B offered by the Item pick; the server answers 422 INV-422-BUNDLE-SETTINGS
Host data    : none
Steps        : 1. Pick B, switch on "Tracked by lot", Save.
Expected     : the Item field shows ar «الحزمة لا تحمل مخزونًا؛ تُضبط إعدادات المخزون على مكوّناتها.» · en «A bundle holds no stock; set stock settings on its components.»; nothing saved.
Test data    : —
<!-- TC:TC-INV-204:END -->

<!-- TC:TC-INV-205:START traces=AC-INV-141,REQ-INV-125,SCR-INV-009 -->
### TC-INV-205 — Dual quantity without a measure unit is refused before saving
Derived from : AC-INV-141  (REQ-INV-125)
Exercises    : SCR-INV-009 /inventory/item-stock-settings?entry=<itemPk>
Rule / code  : RULE-INV-041 → INV-422-MEASURE-UNIT-REQUIRED
Package      : F3-SCR-INV-009
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; dualQuantity on; Item "Steel bar" with no settings
Host data    : none
Steps        : 1. Switch on "Dual quantity", leave the measure unit empty, Save.
Expected     : measureUomCode shows ar «اختر وحدة القياس الثانية (وزن أو طول).» · en «Choose the measure unit (weight or length).»; no save request is sent.
Test data    : —
<!-- TC:TC-INV-205:END -->

<!-- TC:TC-INV-206:START traces=AC-INV-142,REQ-INV-126,SCR-INV-009 -->
### TC-INV-206 — Clearing lot tracking while a lot holds stock shows the refusal
Derived from : AC-INV-142  (REQ-INV-126)
Exercises    : SCR-INV-009 /inventory/item-stock-settings?entry=<itemPk>
Rule / code  : RULE-INV-042 → INV-409-LOT-TRACKING-IN-USE
Package      : F2-SCR-INV-009
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; lotExpiry on; "Milk 1L" lot-tracked; the server answers 409 INV-409-LOT-TRACKING-IN-USE with the texts for count 1
Host data    : none
Steps        : 1. Switch off "Tracked by lot", Save.
Expected     : "Tracked by lot" shows ar «لا يمكن إلغاء تتبع التشغيلة: توجد تشغيلات فيها مخزون (1). اشطبها أو اصرفها أولًا.» · en «Lot tracking cannot be cleared: lots still hold stock (1). Write them off or issue them first.»; after reload the control is on.
Test data    : —
<!-- TC:TC-INV-206:END -->

<!-- TC:TC-INV-207:START traces=AC-INV-143,REQ-INV-127,SCR-INV-009 -->
### TC-INV-207 — Clearing dual quantity while a measure is held shows the refusal
Derived from : AC-INV-143  (REQ-INV-127)
Exercises    : SCR-INV-009 /inventory/item-stock-settings?entry=<itemPk>
Rule / code  : RULE-INV-043 → INV-409-DUAL-QUANTITY-IN-USE
Package      : F2-SCR-INV-009
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Owner session; dualQuantity on; "Cotton fabric" dual-quantity; the server answers 409 INV-409-DUAL-QUANTITY-IN-USE
Host data    : none
Steps        : 1. Switch off "Dual quantity", Save.
Expected     : "Dual quantity" shows ar «لا يمكن إلغاء الكمية المزدوجة: يوجد مخزون بقياس غير صفري. سوِّ القياس إلى صفر أولًا.» · en «Dual quantity cannot be cleared: stock still holds a non-zero measure. Adjust the measure to zero first.»; after reload the control is on.
Test data    : —
<!-- TC:TC-INV-207:END -->

<!-- TC:TC-INV-208:START traces=AC-INV-150,REQ-INV-134,SCR-INV-007 -->
### TC-INV-208 — With lotExpiry off there is no lot entry and no lot column
Derived from : AC-INV-150  (REQ-INV-134)
Exercises    : inventory menu · SCR-INV-007 /inventory/stock-adjustments/new
Rule / code  : — → —
Package      : F4-SCR-INV-007
Scenario     : STATE · data class VALID · language ALL
Preconditions: Warehouse user session; the context answers lotExpiryFl false
Host data    : none
Steps        : 1. Open the inventory menu. 2. Open a new adjustment and add a line.
Expected     : 1 → no lot or near-expiry entry. 2 → the lines show no lot number or expiry column.
Test data    : —
<!-- TC:TC-INV-208:END -->

<!-- TC:TC-INV-209:START traces=AC-INV-151,REQ-INV-135,SCR-INV-003 -->
### TC-INV-209 — With dualQuantity off the balance screen has no measure column
Derived from : AC-INV-151  (REQ-INV-135)
Exercises    : SCR-INV-003 /inventory/stock-balances
Rule / code  : — → —
Package      : F4-SCR-INV-003
Scenario     : STATE · data class VALID · language ALL
Preconditions: a session with PERM_INV_STOCK_BALANCES_VIEW; the context answers dualQuantityFl false
Host data    : none
Steps        : 1. Open the stock balance screen and search.
Expected     : the result shows no measure column.
Test data    : —
<!-- TC:TC-INV-209:END -->

<!-- TC:TC-INV-210:START traces=AC-INV-152,REQ-INV-136,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-006,SCR-INV-007,SCR-INV-008 -->
### TC-INV-210 — The Warehouse role sees its inventory entries and not the Owner's
Derived from : AC-INV-152  (REQ-INV-136)
Exercises    : inventory menu (SEC-FE)
Rule / code  : — → —
Package      : SEC-FE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a newly provisioned tenant; a user holding only the Warehouse role (its seeded permissions in the session); the context answers lotExpiryFl true and activeWarehouseCount 2
Host data    : none
Steps        : 1. Open the inventory menu. 2. Deep-link to /inventory/opening-stock.
Expected     : 1 → entries for stock balances, stock moves, counts, adjustments, lots and transfers; no entry for opening stock, stock valuation or inventory settings. 2 → the unauthorized view.
Test data    : —
<!-- TC:TC-INV-210:END -->

<!-- TC:TC-INV-211:START traces=AC-INV-153,REQ-INV-137,SCR-INV-003 -->
### TC-INV-211 — The Seller/Cashier sees only stock balances, with no action
Derived from : AC-INV-153  (REQ-INV-137)
Exercises    : inventory menu · SCR-INV-003 /inventory/stock-balances
Rule / code  : — → —
Package      : SEC-FE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a newly provisioned tenant; a user holding only the Seller/Cashier role
Host data    : none
Steps        : 1. Open the inventory menu. 2. Open stock balances.
Expected     : 1 → only the stock balance entry. 2 → the screen offers no create, update or cancel action.
Test data    : —
<!-- TC:TC-INV-211:END -->

<!-- TC:TC-INV-212:START traces=AC-INV-154,REQ-INV-138,SCR-INV-007 -->
### TC-INV-212 — A Seller/Cashier cannot submit a stock adjustment
Derived from : AC-INV-154  (REQ-INV-138)
Exercises    : SCR-INV-007 /inventory/stock-adjustments/new
Rule / code  : REQ-INV-138 → INV-403
Package      : SEC-FE
Scenario     : PERMISSION · data class ATTACK · language ALL
Preconditions: a user holding only the Seller/Cashier role; for step 2, a session whose cached permissions still list PERM_INV_ADJUSTMENTS_VIEW / _CREATE while the server answers 403 INV-403
Host data    : none
Steps        : 1. Deep-link to /inventory/stock-adjustments/new. 2. With the stale session, fill a valid adjustment and Save.
Expected     : 1 → the unauthorized view; no create request. 2 → message, text: catalogue INV-403 (bound by the frontend plan's SEC-FE forbidden-response row) — ar «ليست لديك صلاحية لهذا الإجراء.» · en "You do not have permission for this action." (never a raw status); nothing saved.
Test data    : reasonCode DAMAGE
<!-- TC:TC-INV-212:END -->

<!-- TC:TC-INV-240:START traces=AC-INV-069,REQ-INV-063,SCR-INV-012 -->
### TC-INV-240 — The valuation screen shows a Variant's total quantity, average cost, value and the grand total
Derived from : AC-INV-069  (REQ-INV-063)
Exercises    : SCR-INV-012 /inventory/stock-valuation
Rule / code  : — → —
Package      : F4-SCR-INV-012
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; the server answers the valuation search for V with totalOnHandQty 20, averageUnitCost 23.0000, stockValue 460.00 and grandTotalValue 460.00
Host data    : none
Steps        : 1. Open stock valuation. 2. Filter to V. 3. Open V's detail (?variant=<V>).
Expected     : 2 → one row: total quantity 20, average unit cost 23.0000, stock value 460.00; the grand total row shows 460.00. 3 → the detail drawer shows the same three figures and the link "Stock moves" to /inventory/stock-moves?variantPk=<V>.
Test data    : totalOnHandQty 20, averageUnitCost 23.0000, stockValue 460.00 (AC-INV-069)
<!-- TC:TC-INV-240:END -->

<!-- TC:TC-INV-241:START traces=AC-INV-071,REQ-INV-065,SCR-INV-011 -->
### TC-INV-241 — The Owner switches "Allow negative stock" off and saves it
Derived from : AC-INV-071  (REQ-INV-065)
Exercises    : SCR-INV-011 /inventory/settings
Rule / code  : — → —
Package      : F4-SCR-INV-011
Scenario     : STATE · data class VALID · language ALL
Preconditions: Owner session; lotExpiry off; the server answers allowNegativeStockFl true and nearExpiryDays 30
Host data    : none
Steps        : 1. Open inventory settings. 2. Switch «السماح بالرصيد السالب» / "Allow negative stock" off and Save. 3. Reload the screen (the server now answers allowNegativeStockFl false).
Expected     : 1 → the switch is on; the near-expiry horizon field is not displayed (lotExpiry off). 2 → one save request carrying allowNegativeStockFl false and the unchanged nearExpiryDays 30. 3 → the switch shows off. The refusal of a later online issue is asserted on the backend track (TC-INV-017).
Test data    : allowNegativeStockFl true → false (AC-INV-071)
<!-- TC:TC-INV-241:END -->

<!-- TC:TC-INV-262:START traces=AC-INV-044,REQ-INV-039,SCR-INV-003 -->
### TC-INV-262 — A Seller/Cashier sees each balance row labelled with its warehouse name
Derived from : AC-INV-044  (REQ-INV-039)
Exercises    : SCR-INV-003 /inventory/stock-balances
Rule / code  : — → —
Package      : F4-SCR-INV-003
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a Seller/Cashier session (no PERM_INV_WAREHOUSES_VIEW); the context answers activeWarehouseCount 2; the server answers 2 rows of Variant V: W1 (warehouseCode WH-0001, warehouseNameAr «المستودع الرئيسي», warehouseNameEn "Main warehouse", on-hand 5) and W2 (WH-0002, «مستودع الفرع», nameEn empty, on-hand 8, reserved 2)
Host data    : none
Steps        : 1. Search balances for V. 2. Look at the availability summary.
Expected     : 1 → the warehouse column shows «المستودع الرئيسي» / "Main warehouse" and «مستودع الفرع» (English falls back to the Arabic name); no request is sent to API-INV-001; no warehouse filter is offered. 2 → the per-warehouse rows of the summary carry the same labels (ADR-INV-036).
Test data    : —
<!-- TC:TC-INV-262:END -->

<!-- TC:TC-INV-263:START traces=AC-INV-062,REQ-INV-056,SCR-INV-005,SCR-INV-004 -->
### TC-INV-263 — The "Stock moves" link of a posted transfer opens that transfer's moves only
Derived from : AC-INV-062  (REQ-INV-056)
Exercises    : SCR-INV-005 /inventory/transfers/:id → SCR-INV-004 /inventory/stock-moves
Rule / code  : — → —
Package      : F4-SCR-INV-005
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; 2 active Warehouses; a posted Transfer TR-0005 with transferPk T
Host data    : none
Steps        : 1. Open the transfer and choose "Stock moves".
Expected     : the route becomes /inventory/stock-moves?sourceDocTypeCode=INV_TRANSFER&sourceDocRef=T; the move search is sent with sourceDocTypeCode INV_TRANSFER and sourceDocRef T (not docNo); the filter area shows the chip «المستند المصدر» / "Source document" with the transfer's type and number TR-0005.
Test data    : —
<!-- TC:TC-INV-263:END -->

<!-- TC:TC-INV-285:START traces=AC-INV-087,REQ-INV-080,SCR-INV-005 -->
### TC-INV-285 — A transfer post refused for one line of three shows the refusal on that line
Derived from : AC-INV-087  (REQ-INV-080)
Exercises    : SCR-INV-005 /inventory/transfers/:id — Post
Rule / code  : RULE-INV-018 → INV-422-VARIANT-INACTIVE
Package      : F2-SCR-INV-005
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Warehouse user session; 2 active Warehouses; a saved DRAFT Transfer TR-0007 W1 → W2 with 3 lines — V1 (transferLinePk P1), V2 (P2), V3 (P3), 1 each; the server answers the post with 422 INV-422-VARIANT-INACTIVE and `details` [{lineRef P2, lineRefKind LINE_PK, code INV-422-VARIANT-INACTIVE, messageAr, messageEn}] (ADR-INV-046)
Host data    : none
Steps        : 1. Open TR-0007 and Post.
Expected     : the V2 line is highlighted in the lines pane and carries ar «متغيّر الصنف غير نشط.» · en «The Variant is inactive.»; the V1 and V3 lines carry no message; the status shows DRAFT.
Test data    : lineRef P2, lineRefKind LINE_PK
<!-- TC:TC-INV-285:END -->

<!-- TC:TC-INV-294:START traces=AC-INV-044,REQ-INV-039,SCR-INV-003 -->
### TC-INV-294 — Reserved and available are shown once per warehouse when a pair has two lot rows
Derived from : AC-INV-044  (REQ-INV-039)
Exercises    : SCR-INV-003 /inventory/stock-balances
Rule / code  : — → —
Package      : F4-SCR-INV-003
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Warehouse user session; lotExpiry on; the context answers activeWarehouseCount 2; the server answers 2 rows of Variant V in Warehouse W1 — Lot L1 on-hand 4 and Lot L2 on-hand 6, each carrying the pair's reservedQty 3 and availableQty 7 — and 1 row of V in W2 (no lot, on-hand 5, reserved 0, available 5)
Host data    : none
Steps        : 1. Search balances for V. 2. Read the column headers.
Expected     : 1 → the L1 row (first row of the V × W1 pair) shows reserved 3 and available 7; the L2 row shows on-hand 6 and «—» in the reserved and available cells; the W2 row shows reserved 0 and available 5; adding the available column gives 12, never 19. 2 → the headers read «محجوز في المستودع» / "Reserved in warehouse" and «متاح في المستودع» / "Available in warehouse" (gate-analysis revise 5, G6).
Test data    : pair V × W1 with two lot rows
<!-- TC:TC-INV-294:END --><!-- SUB:UI-FLOWS:END -->
