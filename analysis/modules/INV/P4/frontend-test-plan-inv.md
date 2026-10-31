# FRONTEND TEST PLAN — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Profile : commerce   Stage : P4 (Test Plan) · pass 2 · round 1 · gate-analysis revise 5
Track  : frontend · plan test · lane test-gen · prepared by claude:opus
Sources: _state/current-srs.md (INV v1 — REQ 141 · AC 160 · SCR-REQ 12)
         _state/current-frontend-execution-plan.md (P3.2 — SCR-INV-001 … 012, UXD-INV-001 … 011, F1 … F4, SEC-FE)
         _state/current-api-spec (api-spec-inv.yaml — the document the mock server serves)
         _state/current-backend-execution-plan.md (Error Catalog texts) · _state/current-registry-srs.md
Owns   : TC-INV-173 … TC-INV-241, TC-INV-262 … TC-INV-263, TC-INV-285 and TC-INV-294 (this file) — the backend cases are
         in backend-test-plan-inv.md (TC-INV-001 … 172, 242 … 261, 264 … 284, 286 … 293, 295); TC-INV-262, 263 were added
         at gate-analysis revise 1, TC-INV-285 at gate-analysis revise 2 (G2), TC-INV-294 at gate-analysis revise 5 (G6)
ADRs   : raised here ADR-INV-037 (track placement and package), ADR-INV-038 (asserted texts and codes),
         ADR-INV-039 (acceptance of F3-SCR-INV-012 and F4-SCR-INV-011 — repair round 1) — ACCEPTED, non-breaking · cited from P3.2: ADR-INV-033 (reads outside the document), ADR-INV-034 (count commits per
         line), ADR-INV-035 (browser reads the sheet — barcode only in v1), ADR-INV-036 (warehouse label for a
         Seller/Cashier read from the row — closed at gate-analysis revise 1; TC-INV-262 asserts it) · cited from
         gate-analysis revise 2: ADR-INV-046 (a line refusal names its line in `details`; TC-INV-285 asserts it),
         ADR-INV-047 (OPENING_STOCK not offered on the adjustment screen), ADR-INV-033 as amended (INV lookup values
         through API-INV-052)
══════════════════════════════════════════════════════════════════

## How to read this plan

- **Framework.** `profile.stack.testing` is agnostic. Each TC block below is the whole contract. The consumer
  repository chooses its tool and turns each block into one test.
- **Server.** The screens run against a mock server that serves `api-spec-inv.yaml`, including the INV lookup values
  (API-INV-052). The CAT and session fixtures come from ADR-INV-033. "The server answers X" in Preconditions sets the mock's answer for that call.
- **Texts.** A refusal is asserted by the text the frontend plan binds to its code ("text: RULE-INV-nnn message
  (SRS)" = the SRS A5 message, ar and en). `Expected` writes it as `ar «…» · en «…»`, copied from the SRS with its
  placeholders filled. Each text is asserted in both languages, by switching the UI language. A screen label (a
  chip, a status, a lookup name) is written `«…» / "…"` and is not a refusal text.
- **Routes.** Each TC cites the screen (`SCR-INV-*`) and its route. Drawers are search params (`entry`, `line`,
  `move`, `variant`).
- **Catalogue data.** Every lookup value a case names is seeded by INV. No case needs host data.

<!-- PHASE:TEST-PLAN-FE:START traces=AC-INV-044,AC-INV-062,REQ-INV-039,REQ-INV-056,AC-INV-004,AC-INV-007,AC-INV-008,AC-INV-011,AC-INV-013,AC-INV-014,AC-INV-015,AC-INV-018,AC-INV-026,AC-INV-029,AC-INV-030,AC-INV-034,AC-INV-035,AC-INV-037,AC-INV-069,AC-INV-071,AC-INV-076,AC-INV-079,AC-INV-081,AC-INV-083,AC-INV-087,AC-INV-088,AC-INV-090,AC-INV-091,AC-INV-093,AC-INV-098,AC-INV-100,AC-INV-101,AC-INV-103,AC-INV-106,AC-INV-109,AC-INV-111,AC-INV-113,AC-INV-128,AC-INV-129,AC-INV-130,AC-INV-132,AC-INV-133,AC-INV-135,AC-INV-139,AC-INV-141,AC-INV-142,AC-INV-143,AC-INV-150,AC-INV-151,AC-INV-152,AC-INV-153,AC-INV-154,REQ-INV-003,REQ-INV-006,REQ-INV-008,REQ-INV-010,REQ-INV-011,REQ-INV-012,REQ-INV-015,REQ-INV-022,REQ-INV-025,REQ-INV-029,REQ-INV-030,REQ-INV-032,REQ-INV-063,REQ-INV-065,REQ-INV-070,REQ-INV-072,REQ-INV-074,REQ-INV-076,REQ-INV-080,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-088,REQ-INV-090,REQ-INV-091,REQ-INV-093,REQ-INV-096,REQ-INV-098,REQ-INV-099,REQ-INV-101,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-118,REQ-INV-119,REQ-INV-121,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-134,REQ-INV-135,REQ-INV-136,REQ-INV-137,REQ-INV-138 -->
## TEST-PLAN-FE — module test cases

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

<!-- SUB:INT-FLOW:START traces=AC-INV-018,AC-INV-037,AC-INV-076,AC-INV-100,AC-INV-128,REQ-INV-015,REQ-INV-032,REQ-INV-070,REQ-INV-090,REQ-INV-114 -->
### INT-FLOW — flows across screens

<!-- TC:TC-INV-213:START traces=AC-INV-018,REQ-INV-015,SCR-INV-001,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-007 -->
### TC-INV-213 — A second warehouse reveals the transfer entry, the selector and the filter
Derived from : AC-INV-018  (REQ-INV-015)
Exercises    : SCR-INV-001 → inventory menu → SCR-INV-005, SCR-INV-007, SCR-INV-003, SCR-INV-004
Rule / code  : — → —
Package      : SEC-FE
Scenario     : STATE · data class VALID · language ALL
Preconditions: a tenant with 1 active Warehouse W and 40 Stock moves in W; an Owner session and a Warehouse user session
Host data    : none
Steps        : 1. As Owner, create a second warehouse on SCR-INV-001. 2. As the Warehouse user, reload the inventory menu. 3. Open a new adjustment. 4. Open stock balances. 5. Open stock moves filtered on W.
Expected     : 2 → the transfer entry is displayed. 3 → the warehouse selector is displayed. 4 → the warehouse filter is displayed. 5 → the 40 moves are listed under W. No migration or setup step is asked.
Test data    : the new warehouse's nameAr is a placeholder
<!-- TC:TC-INV-213:END -->

<!-- TC:TC-INV-214:START traces=AC-INV-037,REQ-INV-032,SCR-INV-002,SCR-INV-001 -->
### TC-INV-214 — A second branch reveals the branch field and filter
Derived from : AC-INV-037  (REQ-INV-032)
Exercises    : SCR-INV-002 → SCR-INV-001
Rule / code  : — → —
Package      : F4-SCR-INV-002
Scenario     : STATE · data class VALID · language ALL
Preconditions: Owner session; a tenant with 1 active Branch B1 and 2 Warehouses in B1
Host data    : none
Steps        : 1. Create a second branch on SCR-INV-002. 2. Open the warehouse entry of a warehouse. 3. Open the warehouse search.
Expected     : 2 → the branch field is displayed with B1. 3 → the branch filter and column are displayed; both Warehouses show B1.
Test data    : the new branch's nameAr is a placeholder
<!-- TC:TC-INV-214:END -->

<!-- TC:TC-INV-215:START traces=AC-INV-076,REQ-INV-070,SCR-INV-010,SCR-INV-007,SCR-INV-012 -->
### TC-INV-215 — Posting opening stock opens the posted adjustment
Derived from : AC-INV-076  (REQ-INV-070)
Exercises    : SCR-INV-010 → SCR-INV-007 → SCR-INV-012
Rule / code  : RULE-INV-026 → —
Package      : F4-SCR-INV-010
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; one active Warehouse; Variant V with no stock and no valuation
Host data    : none
Steps        : 1. Add 1 line of V, quantity 40, unit cost 12.5000. 2. Post. 3. Open stock valuation for V.
Expected     : 2 → the route becomes /inventory/stock-adjustments/<id>, showing a POSTED adjustment with reason «مخزون افتتاحي» / "Opening stock". 3 → average unit cost 12.5000 and stock value 500.00.
Test data    : (V, 40, 12.5000)
<!-- TC:TC-INV-215:END -->

<!-- TC:TC-INV-216:START traces=AC-INV-100,REQ-INV-090,SCR-INV-006 -->
### TC-INV-216 — A count is started, scanned and posted on a 360-pixel phone viewport
Derived from : AC-INV-100  (REQ-INV-090)
Exercises    : SCR-INV-006 /inventory/stock-counts → /new → /:id
Rule / code  : — → —
Package      : F4-SCR-INV-006
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: a phone web browser with a 360 × 640 CSS-pixel viewport and no installed app; Warehouse user session; 3 barcodes of active Variants
Host data    : none
Steps        : 1. Start a count. 2. Scan the 3 barcodes. 3. Post and confirm.
Expected     : every step completes on that viewport; the page width never exceeds 360 CSS pixels (no horizontal scroll); after posting the count shows POSTED.
Test data    : 3 barcodes (placeholders)
<!-- TC:TC-INV-216:END -->

<!-- TC:TC-INV-217:START traces=AC-INV-128,REQ-INV-114,SCR-INV-008,SCR-INV-007 -->
### TC-INV-217 — An expired lot is written off from the lot screen
Derived from : AC-INV-128  (REQ-INV-114)
Exercises    : SCR-INV-008 → SCR-INV-007 /inventory/stock-adjustments/new?reason=EXPIRY_WRITE_OFF&lotPk&warehousePk
Rule / code  : RULE-INV-039 → —
Package      : F4-SCR-INV-007
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: lotExpiry on; Warehouse user session; Lot L0 of V expired, on-hand 5 in W
Host data    : none
Steps        : 1. On the lot screen choose "Write off" on L0. 2. Save. 3. Post. 4. Return to the lot screen.
Expected     : 1 → the new adjustment opens with reason «شطب تشغيلة منتهية» / "Expired lot write-off" and 1 line of V in L0, −5; nothing is saved yet. 3 → status POSTED. 4 → L0 shows on-hand 0.
Test data    : reasonCode EXPIRY_WRITE_OFF
<!-- TC:TC-INV-217:END -->
<!-- SUB:INT-FLOW:END -->
<!-- PHASE:TEST-PLAN-FE:END -->

<!-- PHASE:INT-UXD:START traces=AC-INV-044,AC-INV-069,AC-INV-082,AC-INV-085,AC-INV-097,AC-INV-107,AC-INV-122,AC-INV-135,AC-INV-138,AC-INV-140,REQ-INV-039,REQ-INV-063,REQ-INV-075,REQ-INV-078,REQ-INV-088,REQ-INV-097,REQ-INV-109,REQ-INV-121,REQ-INV-124,REQ-INV-125,UXD-INV-001,UXD-INV-002,UXD-INV-003,UXD-INV-004,UXD-INV-005,UXD-INV-006,UXD-INV-007,UXD-INV-008,UXD-INV-009,UXD-INV-010,UXD-INV-011 -->
## INT-UXD — integration test cases (foreign fields this module displays)

Source: the 11 `UXD-*` the frontend plan cites (F1 … F4 blocks of SCR-INV-003 … 009, 012). Every foreign field
is owned by CAT and read through the CAT lookups the plan declares (ADR-INV-033). Each UXD yields a "rendered"
case and an "empty / failure" case. The empty or failure state is the one the plan's hook row declares; no copy
is invented.

<!-- SUB:CAT:START traces=AC-INV-044,AC-INV-069,AC-INV-082,AC-INV-085,AC-INV-097,AC-INV-107,AC-INV-122,AC-INV-135,AC-INV-138,AC-INV-140,REQ-INV-039,REQ-INV-063,REQ-INV-075,REQ-INV-078,REQ-INV-088,REQ-INV-097,REQ-INV-109,REQ-INV-121,REQ-INV-124,REQ-INV-125,UXD-INV-001,UXD-INV-002,UXD-INV-003,UXD-INV-004,UXD-INV-005,UXD-INV-006,UXD-INV-007,UXD-INV-008,UXD-INV-009,UXD-INV-010,UXD-INV-011 -->
### CAT — fields owned by the catalogue module

<!-- TC:TC-INV-218:START traces=UXD-INV-001,REQ-INV-039,AC-INV-044,SCR-INV-003 -->
### TC-INV-218 — The Variant filter of stock balances picks a CAT Variant
Derived from : UXD-INV-001  (REQ-INV-039, AC-INV-044)
Exercises    : SCR-INV-003 /inventory/stock-balances — Variant picker
Rule / code  : — → —
Package      : F3-SCR-INV-003
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: the CAT Variant search answers Variant V (code and names)
Host data    : none
Steps        : 1. Type part of V's code in the Variant picker. 2. Choose V.
Expected     : 1 → V is offered with the code and name CAT returns. 2 → the balance search is sent with variantPk V.
Test data    : —
<!-- TC:TC-INV-218:END -->

<!-- TC:TC-INV-219:START traces=UXD-INV-001,REQ-INV-039,AC-INV-044,SCR-INV-003 -->
### TC-INV-219 — The Variant filter of stock balances shows "no results" when CAT finds nothing
Derived from : UXD-INV-001  (REQ-INV-039, AC-INV-044)
Exercises    : SCR-INV-003 /inventory/stock-balances — Variant picker
Rule / code  : — → —
Package      : F2-SCR-INV-003
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: the CAT Variant search answers no match (or fails)
Host data    : none
Steps        : 1. Type a term in the Variant picker.
Expected     : the picker shows its "no results" state; the screen stays usable; no crash.
Test data    : any term (placeholder)
<!-- TC:TC-INV-219:END -->

<!-- TC:TC-INV-220:START traces=UXD-INV-002,REQ-INV-039,AC-INV-044,SCR-INV-003 -->
### TC-INV-220 — The Item name column shows the CAT Item name
Derived from : UXD-INV-002  (REQ-INV-039, AC-INV-044)
Exercises    : SCR-INV-003 /inventory/stock-balances — Item name column, Item filter
Rule / code  : — → —
Package      : F4-SCR-INV-003
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: balance rows of Variant V; the CAT Variant read answers its itemPk I and the CAT Item read answers I's names
Host data    : none
Steps        : 1. Search balances. 2. Choose Item I in the Item filter.
Expected     : 1 → each row of V shows I's name in the user's language. 2 → the search is sent with itemPk I.
Test data    : —
<!-- TC:TC-INV-220:END -->

<!-- TC:TC-INV-221:START traces=UXD-INV-002,REQ-INV-039,AC-INV-044,SCR-INV-003 -->
### TC-INV-221 — An Item the CAT read cannot find leaves its name empty
Derived from : UXD-INV-002  (REQ-INV-039, AC-INV-044)
Exercises    : SCR-INV-003 /inventory/stock-balances — Item name column
Rule / code  : — → —
Package      : F2-SCR-INV-003
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: balance rows of Variant V; the CAT Item read answers "not found" (or fails)
Host data    : none
Steps        : 1. Search balances.
Expected     : the rows are shown; the Item name cell is left empty; no crash.
Test data    : —
<!-- TC:TC-INV-221:END -->

<!-- TC:TC-INV-222:START traces=UXD-INV-003,REQ-INV-121,AC-INV-135,SCR-INV-003 -->
### TC-INV-222 — The measure shows the CAT unit label
Derived from : UXD-INV-003  (REQ-INV-121, AC-INV-135)
Exercises    : SCR-INV-003 /inventory/stock-balances — measure column
Rule / code  : — → —
Package      : F4-SCR-INV-003
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: dualQuantity on; a row with onHandMeasureQty 120.500 and the metre code; the CAT unit-of-measure lookup answers its labels
Host data    : none
Steps        : 1. Search balances.
Expected     : the measure shows 120.500 with the metre label in the user's language.
Test data    : —
<!-- TC:TC-INV-222:END -->

<!-- TC:TC-INV-223:START traces=UXD-INV-003,REQ-INV-121,AC-INV-135,SCR-INV-003 -->
### TC-INV-223 — An unknown unit code is shown as the code
Derived from : UXD-INV-003  (REQ-INV-121, AC-INV-135)
Exercises    : SCR-INV-003 /inventory/stock-balances — measure column
Rule / code  : — → —
Package      : F2-SCR-INV-003
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: dualQuantity on; a row whose measureUomCode the CAT unit-of-measure lookup does not answer
Host data    : none
Steps        : 1. Search balances.
Expected     : the measure shows the raw code beside the quantity; no crash.
Test data    : —
<!-- TC:TC-INV-223:END -->

<!-- TC:TC-INV-224:START traces=UXD-INV-004,REQ-INV-075,AC-INV-082,SCR-INV-004 -->
### TC-INV-224 — The Variant filter of move history picks a CAT Variant
Derived from : UXD-INV-004  (REQ-INV-075, AC-INV-082)
Exercises    : SCR-INV-004 /inventory/stock-moves — Variant picker
Rule / code  : — → —
Package      : F3-SCR-INV-004
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: the CAT Variant search answers Variant V
Host data    : none
Steps        : 1. Choose V in the Variant filter.
Expected     : V is offered with its CAT code and name; the move search is sent with variantPk V.
Test data    : —
<!-- TC:TC-INV-224:END -->

<!-- TC:TC-INV-225:START traces=UXD-INV-004,REQ-INV-075,AC-INV-082,SCR-INV-004 -->
### TC-INV-225 — The Variant filter of move history shows "no results" when CAT finds nothing
Derived from : UXD-INV-004  (REQ-INV-075, AC-INV-082)
Exercises    : SCR-INV-004 /inventory/stock-moves — Variant picker
Rule / code  : — → —
Package      : F2-SCR-INV-004
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: the CAT Variant search answers no match (or fails)
Host data    : none
Steps        : 1. Type a term in the Variant picker.
Expected     : the picker shows "no results"; the screen stays usable.
Test data    : —
<!-- TC:TC-INV-225:END -->

<!-- TC:TC-INV-226:START traces=UXD-INV-005,REQ-INV-078,AC-INV-085,SCR-INV-005 -->
### TC-INV-226 — A transfer line resolves a barcode to the CAT Variant
Derived from : UXD-INV-005  (REQ-INV-078, AC-INV-085)
Exercises    : SCR-INV-005 /inventory/transfers/new?line=new
Rule / code  : — → —
Package      : F4-SCR-INV-005
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: 2 active Warehouses; the CAT barcode lookup answers Variant V for a barcode; the CAT Variant read answers V's names and itemPk
Host data    : none
Steps        : 1. In the line drawer enter the barcode. 2. Confirm the line with a quantity.
Expected     : the line shows V's name as CAT returns it; the saved request line carries variantPk V.
Test data    : —
<!-- TC:TC-INV-226:END -->

<!-- TC:TC-INV-227:START traces=UXD-INV-005,REQ-INV-078,AC-INV-085,SCR-INV-005 -->
### TC-INV-227 — An unknown barcode on a transfer line marks the barcode field
Derived from : UXD-INV-005  (REQ-INV-078, AC-INV-085)
Exercises    : SCR-INV-005 /inventory/transfers/new?line=new
Rule / code  : — → —
Package      : F2-SCR-INV-005
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: the CAT barcode lookup answers "not found"
Host data    : none
Steps        : 1. Enter the barcode in the line drawer.
Expected     : the barcode field is in its error state; no line is added; no crash.
Test data    : —
<!-- TC:TC-INV-227:END -->

<!-- TC:TC-INV-228:START traces=UXD-INV-006,REQ-INV-088,AC-INV-097,SCR-INV-006 -->
### TC-INV-228 — A count scan shows the CAT Variant name and the new total
Derived from : UXD-INV-006  (REQ-INV-088, AC-INV-097)
Exercises    : SCR-INV-006 /inventory/stock-counts/:id — scan bar
Rule / code  : — → —
Package      : F4-SCR-INV-006
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: a DRAFT count; barcode 6221000000017 identifies V in CAT; V's Item has no lot or measure
Host data    : none
Steps        : 1. Scan 6221000000017 with quantity 1. 2. Scan it again with quantity 2.
Expected     : each scan shows a toast with V's name as CAT returns it and the counted total (1, then 3); the line card shows V with 3.
Test data    : barcode 6221000000017
<!-- TC:TC-INV-228:END -->

<!-- TC:TC-INV-229:START traces=UXD-INV-006,REQ-INV-088,AC-INV-097,SCR-INV-006 -->
### TC-INV-229 — A count scan that CAT cannot resolve shows the unknown-barcode text
Derived from : UXD-INV-006  (REQ-INV-088, AC-INV-097)
Exercises    : SCR-INV-006 /inventory/stock-counts/:id — scan bar
Rule / code  : RULE-INV-032 → INV-422-BARCODE-UNKNOWN
Package      : F2-SCR-INV-006
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: a DRAFT count; the CAT barcode lookup answers "not found"
Host data    : none
Steps        : 1. Scan the barcode.
Expected     : the barcode field shows ar «الباركود غير معروف. اختر متغيّر الصنف يدويًا.» · en «Unknown barcode. Pick the Variant by hand.»; no line is added; no crash.
Test data    : —
<!-- TC:TC-INV-229:END -->

<!-- TC:TC-INV-230:START traces=UXD-INV-007,REQ-INV-097,AC-INV-107,SCR-INV-007 -->
### TC-INV-230 — An adjustment line shows the CAT Variant it names
Derived from : UXD-INV-007  (REQ-INV-097, AC-INV-107)
Exercises    : SCR-INV-007 /inventory/stock-adjustments/new?line=new
Rule / code  : — → —
Package      : F4-SCR-INV-007
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: the CAT Variant search answers V; the CAT Variant read answers V's names and itemPk
Host data    : none
Steps        : 1. In the line drawer pick V and enter −2. 2. Confirm.
Expected     : the line shows V's name as CAT returns it; the saved request line carries variantPk V.
Test data    : quantityChange −2
<!-- TC:TC-INV-230:END -->

<!-- TC:TC-INV-231:START traces=UXD-INV-007,REQ-INV-097,AC-INV-107,SCR-INV-007 -->
### TC-INV-231 — An unknown barcode on an adjustment line marks the barcode field
Derived from : UXD-INV-007  (REQ-INV-097, AC-INV-107)
Exercises    : SCR-INV-007 /inventory/stock-adjustments/new?line=new
Rule / code  : — → —
Package      : F2-SCR-INV-007
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: the CAT barcode lookup answers "not found"
Host data    : none
Steps        : 1. Enter the barcode in the line drawer.
Expected     : the barcode field is in its error state; no line is added; no crash.
Test data    : —
<!-- TC:TC-INV-231:END -->

<!-- TC:TC-INV-232:START traces=UXD-INV-008,REQ-INV-109,AC-INV-122,SCR-INV-008 -->
### TC-INV-232 — The lot screen filters and labels lots by their CAT Variant
Derived from : UXD-INV-008  (REQ-INV-109, AC-INV-122)
Exercises    : SCR-INV-008 /inventory/lots — Variant filter and label
Rule / code  : — → —
Package      : F3-SCR-INV-008
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: lotExpiry on; the CAT Variant search and read answer V; lots L1, L2 of V
Host data    : none
Steps        : 1. Choose V in the Variant filter.
Expected     : the lot search is sent with variantPk V; rows L2 and L1 show V's name as CAT returns it.
Test data    : —
<!-- TC:TC-INV-232:END -->

<!-- TC:TC-INV-233:START traces=UXD-INV-008,REQ-INV-109,AC-INV-122,SCR-INV-008 -->
### TC-INV-233 — The lot screen's Variant filter shows "no results" when CAT finds nothing
Derived from : UXD-INV-008  (REQ-INV-109, AC-INV-122)
Exercises    : SCR-INV-008 /inventory/lots — Variant picker
Rule / code  : — → —
Package      : F2-SCR-INV-008
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: lotExpiry on; the CAT Variant search answers no match (or fails)
Host data    : none
Steps        : 1. Type a term in the Variant picker.
Expected     : the picker shows "no results"; the screen stays usable.
Test data    : —
<!-- TC:TC-INV-233:END -->

<!-- TC:TC-INV-234:START traces=UXD-INV-009,REQ-INV-124,AC-INV-138,SCR-INV-009 -->
### TC-INV-234 — The Item pick of stock settings offers CAT Items with their kind
Derived from : UXD-INV-009  (REQ-INV-124, AC-INV-138)
Exercises    : SCR-INV-009 /inventory/item-stock-settings?entry=new — Item pick
Rule / code  : — → —
Package      : F4-SCR-INV-009
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: lotExpiry on; the CAT Item search answers "Milk 1L" (STANDARD)
Host data    : none
Steps        : 1. Type "Milk" in the Item pick. 2. Choose "Milk 1L".
Expected     : "Milk 1L" is offered with its CAT name and kind; the entry opens for that Item.
Test data    : —
<!-- TC:TC-INV-234:END -->

<!-- TC:TC-INV-235:START traces=UXD-INV-009,REQ-INV-124,AC-INV-138,SCR-INV-009 -->
### TC-INV-235 — The Item pick shows "no results" when CAT finds nothing
Derived from : UXD-INV-009  (REQ-INV-124, AC-INV-138)
Exercises    : SCR-INV-009 /inventory/item-stock-settings?entry=new — Item pick
Rule / code  : — → —
Package      : F2-SCR-INV-009
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: lotExpiry on; the CAT Item search answers no match (or fails)
Host data    : none
Steps        : 1. Type a term in the Item pick.
Expected     : the pick shows "no results"; Save stays unavailable without an Item; no crash.
Test data    : —
<!-- TC:TC-INV-235:END -->

<!-- TC:TC-INV-236:START traces=UXD-INV-010,REQ-INV-125,AC-INV-140,SCR-INV-009 -->
### TC-INV-236 — The measure unit options come from the CAT unit-of-measure lookup
Derived from : UXD-INV-010  (REQ-INV-125, AC-INV-140)
Exercises    : SCR-INV-009 /inventory/item-stock-settings?entry=<itemPk> — measure unit
Rule / code  : — → —
Package      : F4-SCR-INV-009
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: dualQuantity on; Item "Steel bar"; the CAT unit-of-measure lookup answers KG with its labels
Host data    : none
Steps        : 1. Switch on "Dual quantity". 2. Open the measure unit options and choose KG. 3. Save.
Expected     : 2 → KG is offered with its CAT label in the user's language. 3 → the request carries measureUomCode KG.
Test data    : KG (CAT's consumed lookup)
<!-- TC:TC-INV-236:END -->

<!-- TC:TC-INV-237:START traces=UXD-INV-010,REQ-INV-125,AC-INV-140,SCR-INV-009 -->
### TC-INV-237 — A saved unit code the lookup does not answer is shown as the code
Derived from : UXD-INV-010  (REQ-INV-125, AC-INV-140)
Exercises    : SCR-INV-009 /inventory/item-stock-settings?entry=<itemPk> — measure unit
Rule / code  : — → —
Package      : F2-SCR-INV-009
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: dualQuantity on; an Item whose saved measureUomCode the CAT unit-of-measure lookup does not answer
Host data    : none
Steps        : 1. Open the Item's entry.
Expected     : the measure unit shows the raw code; no crash.
Test data    : —
<!-- TC:TC-INV-237:END -->

<!-- TC:TC-INV-238:START traces=UXD-INV-011,REQ-INV-063,AC-INV-069,SCR-INV-012 -->
### TC-INV-238 — The valuation filters pick a CAT Variant and Item
Derived from : UXD-INV-011  (REQ-INV-063, AC-INV-069)
Exercises    : SCR-INV-012 /inventory/stock-valuation — Variant and Item filters
Rule / code  : — → —
Package      : F3-SCR-INV-012
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: Owner session; the CAT Variant search answers V and the CAT Item search answers Item I
Host data    : none
Steps        : 1. Choose V in the Variant filter. 2. Clear it and choose I in the Item filter.
Expected     : 1 → the valuation search is sent with variantPk V. 2 → it is sent with itemPk I. Each pick shows the CAT code and name.
Test data    : —
<!-- TC:TC-INV-238:END -->

<!-- TC:TC-INV-239:START traces=UXD-INV-011,REQ-INV-063,AC-INV-069,SCR-INV-012 -->
### TC-INV-239 — The valuation filters show "no results" when CAT finds nothing
Derived from : UXD-INV-011  (REQ-INV-063, AC-INV-069)
Exercises    : SCR-INV-012 /inventory/stock-valuation — Variant and Item filters
Rule / code  : — → —
Package      : F2-SCR-INV-012
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: Owner session; the CAT Variant and Item searches answer no match (or fail)
Host data    : none
Steps        : 1. Type a term in each picker.
Expected     : each picker shows "no results"; the screen stays usable.
Test data    : —
<!-- TC:TC-INV-239:END -->
<!-- SUB:CAT:END -->
<!-- PHASE:INT-UXD:END -->

## TC TRACEABILITY INDEX

Generated from the TC blocks above (and, for the AC column of the other track, from backend-test-plan-inv.md).

### AC → TC

| AC | TC (backend) | TC (frontend) |
|---|---|---|
| AC-INV-001 | TC-INV-047 | — |
| AC-INV-002 | TC-INV-048 | — |
| AC-INV-003 | TC-INV-049 | — |
| AC-INV-004 | TC-INV-001 | TC-INV-173 |
| AC-INV-005 | TC-INV-050, TC-INV-269, TC-INV-273 | — |
| AC-INV-006 | TC-INV-051, TC-INV-276 | — |
| AC-INV-007 | TC-INV-002 | TC-INV-174 |
| AC-INV-008 | TC-INV-003, TC-INV-252 | TC-INV-175 |
| AC-INV-009 | TC-INV-052 | — |
| AC-INV-010 | TC-INV-053, TC-INV-248 | — |
| AC-INV-011 | TC-INV-004 | TC-INV-176 |
| AC-INV-012 | TC-INV-054 | — |
| AC-INV-013 | TC-INV-055 | TC-INV-177 |
| AC-INV-014 | — | TC-INV-178 |
| AC-INV-015 | — | TC-INV-179 |
| AC-INV-016 | TC-INV-056 | — |
| AC-INV-017 | TC-INV-005 | — |
| AC-INV-018 | TC-INV-057 | TC-INV-213 |
| AC-INV-019 | TC-INV-058 | — |
| AC-INV-020 | TC-INV-006, TC-INV-274 | — |
| AC-INV-021 | TC-INV-007, TC-INV-274 | — |
| AC-INV-022 | TC-INV-059 | — |
| AC-INV-023 | TC-INV-060 | — |
| AC-INV-024 | TC-INV-061 | — |
| AC-INV-025 | TC-INV-062 | — |
| AC-INV-026 | TC-INV-008 | TC-INV-180 |
| AC-INV-027 | TC-INV-063, TC-INV-273 | — |
| AC-INV-028 | TC-INV-064 | — |
| AC-INV-029 | TC-INV-009 | TC-INV-181 |
| AC-INV-030 | TC-INV-010 | TC-INV-182 |
| AC-INV-031 | TC-INV-065 | — |
| AC-INV-032 | TC-INV-066, TC-INV-272, TC-INV-282 | — |
| AC-INV-033 | TC-INV-067 | — |
| AC-INV-034 | — | TC-INV-183 |
| AC-INV-035 | — | TC-INV-184 |
| AC-INV-036 | TC-INV-068, TC-INV-272 | — |
| AC-INV-037 | TC-INV-069 | TC-INV-214 |
| AC-INV-038 | TC-INV-070 | — |
| AC-INV-039 | TC-INV-071 | — |
| AC-INV-040 | TC-INV-072, TC-INV-244 | — |
| AC-INV-041 | TC-INV-073 | — |
| AC-INV-042 | TC-INV-074, TC-INV-251 | — |
| AC-INV-043 | TC-INV-075 | — |
| AC-INV-044 | TC-INV-076, TC-INV-259 | TC-INV-218, TC-INV-219, TC-INV-220, TC-INV-221, TC-INV-262, TC-INV-294 |
| AC-INV-045 | TC-INV-077 | — |
| AC-INV-046 | TC-INV-078 | — |
| AC-INV-047 | TC-INV-079 | — |
| AC-INV-048 | TC-INV-080 | — |
| AC-INV-049 | TC-INV-011 | — |
| AC-INV-050 | TC-INV-081 | — |
| AC-INV-051 | TC-INV-082, TC-INV-293 | — |
| AC-INV-052 | TC-INV-083, TC-INV-255 | — |
| AC-INV-053 | TC-INV-012 | — |
| AC-INV-054 | TC-INV-084 | — |
| AC-INV-055 | TC-INV-085 | — |
| AC-INV-056 | TC-INV-086, TC-INV-267 | — |
| AC-INV-057 | TC-INV-087 | — |
| AC-INV-058 | TC-INV-013 | — |
| AC-INV-059 | TC-INV-088 | — |
| AC-INV-060 | TC-INV-014 | — |
| AC-INV-061 | TC-INV-015 | — |
| AC-INV-062 | TC-INV-089, TC-INV-260 | TC-INV-263 |
| AC-INV-063 | TC-INV-090 | — |
| AC-INV-064 | TC-INV-091, TC-INV-245, TC-INV-278 | — |
| AC-INV-065 | TC-INV-092 | — |
| AC-INV-066 | TC-INV-093 | — |
| AC-INV-067 | TC-INV-094 | — |
| AC-INV-068 | TC-INV-095 | — |
| AC-INV-069 | TC-INV-096 | TC-INV-238, TC-INV-239, TC-INV-240 |
| AC-INV-070 | TC-INV-016 | — |
| AC-INV-071 | TC-INV-017 | TC-INV-241 |
| AC-INV-072 | TC-INV-097 | — |
| AC-INV-073 | TC-INV-018, TC-INV-266 | — |
| AC-INV-074 | TC-INV-098 | — |
| AC-INV-075 | TC-INV-019 | — |
| AC-INV-076 | TC-INV-099, TC-INV-257 | TC-INV-215 |
| AC-INV-077 | TC-INV-100 | — |
| AC-INV-078 | TC-INV-020 | — |
| AC-INV-079 | TC-INV-021 | TC-INV-185 |
| AC-INV-080 | TC-INV-101 | — |
| AC-INV-081 | TC-INV-275 | TC-INV-186 |
| AC-INV-082 | TC-INV-102 | TC-INV-224, TC-INV-225 |
| AC-INV-083 | TC-INV-103, TC-INV-292 | TC-INV-187 |
| AC-INV-084 | TC-INV-104 | — |
| AC-INV-085 | TC-INV-105, TC-INV-280 | TC-INV-226, TC-INV-227 |
| AC-INV-086 | TC-INV-106, TC-INV-270 | — |
| AC-INV-087 | TC-INV-107, TC-INV-246, TC-INV-253, TC-INV-264 | TC-INV-285 |
| AC-INV-088 | TC-INV-022 | TC-INV-188 |
| AC-INV-089 | TC-INV-108, TC-INV-247 | — |
| AC-INV-090 | TC-INV-023 | TC-INV-189 |
| AC-INV-091 | TC-INV-024 | TC-INV-190 |
| AC-INV-092 | TC-INV-109 | — |
| AC-INV-093 | TC-INV-025, TC-INV-254 | TC-INV-191 |
| AC-INV-094 | TC-INV-026 | — |
| AC-INV-095 | TC-INV-110 | — |
| AC-INV-096 | TC-INV-111 | — |
| AC-INV-097 | TC-INV-112 | TC-INV-228, TC-INV-229 |
| AC-INV-098 | TC-INV-027 | TC-INV-192 |
| AC-INV-099 | TC-INV-113 | — |
| AC-INV-100 | — | TC-INV-216 |
| AC-INV-101 | TC-INV-028 | TC-INV-193 |
| AC-INV-102 | TC-INV-114, TC-INV-258, TC-INV-265, TC-INV-289, TC-INV-291 | — |
| AC-INV-103 | TC-INV-029 | TC-INV-194 |
| AC-INV-104 | TC-INV-115 | — |
| AC-INV-105 | TC-INV-116 | — |
| AC-INV-106 | TC-INV-030 | TC-INV-195 |
| AC-INV-107 | TC-INV-117 | TC-INV-230, TC-INV-231 |
| AC-INV-108 | TC-INV-118, TC-INV-256 | — |
| AC-INV-109 | TC-INV-031 | TC-INV-196 |
| AC-INV-110 | TC-INV-119 | — |
| AC-INV-111 | TC-INV-032 | TC-INV-197 |
| AC-INV-112 | TC-INV-120 | — |
| AC-INV-113 | TC-INV-033, TC-INV-281, TC-INV-284 | TC-INV-198 |
| AC-INV-114 | TC-INV-121 | — |
| AC-INV-115 | TC-INV-034 | — |
| AC-INV-116 | TC-INV-122 | — |
| AC-INV-117 | TC-INV-123 | — |
| AC-INV-118 | TC-INV-035 | — |
| AC-INV-119 | TC-INV-124, TC-INV-249 | — |
| AC-INV-120 | TC-INV-036 | — |
| AC-INV-121 | TC-INV-125, TC-INV-271, TC-INV-279 | — |
| AC-INV-122 | TC-INV-126 | TC-INV-232, TC-INV-233 |
| AC-INV-123 | TC-INV-127 | — |
| AC-INV-124 | TC-INV-128 | — |
| AC-INV-125 | TC-INV-129 | — |
| AC-INV-126 | TC-INV-037, TC-INV-279 | — |
| AC-INV-127 | TC-INV-038 | — |
| AC-INV-128 | TC-INV-130 | TC-INV-217 |
| AC-INV-129 | TC-INV-131 | TC-INV-199 |
| AC-INV-130 | TC-INV-132 | TC-INV-200 |
| AC-INV-131 | TC-INV-133, TC-INV-283 | — |
| AC-INV-132 | TC-INV-134 | TC-INV-201 |
| AC-INV-133 | TC-INV-039 | TC-INV-202 |
| AC-INV-134 | TC-INV-135 | — |
| AC-INV-135 | TC-INV-136 | TC-INV-203, TC-INV-222, TC-INV-223 |
| AC-INV-136 | TC-INV-040, TC-INV-268 | — |
| AC-INV-137 | TC-INV-137 | — |
| AC-INV-138 | TC-INV-138 | TC-INV-234, TC-INV-235 |
| AC-INV-139 | TC-INV-041 | TC-INV-204 |
| AC-INV-140 | TC-INV-139 | TC-INV-236, TC-INV-237 |
| AC-INV-141 | TC-INV-042 | TC-INV-205 |
| AC-INV-142 | TC-INV-043, TC-INV-250 | TC-INV-206 |
| AC-INV-143 | TC-INV-044 | TC-INV-207 |
| AC-INV-144 | TC-INV-045 | — |
| AC-INV-145 | TC-INV-140 | — |
| AC-INV-146 | TC-INV-141 | — |
| AC-INV-147 | TC-INV-046 | — |
| AC-INV-148 | TC-INV-142, TC-INV-242, TC-INV-243 | — |
| AC-INV-149 | TC-INV-143 | — |
| AC-INV-150 | — | TC-INV-208 |
| AC-INV-151 | — | TC-INV-209 |
| AC-INV-152 | TC-INV-144 | TC-INV-210 |
| AC-INV-153 | TC-INV-145, TC-INV-261 | TC-INV-211 |
| AC-INV-154 | TC-INV-146, TC-INV-277 | TC-INV-212 |
| AC-INV-155 | TC-INV-286 | — |
| AC-INV-156 | TC-INV-286 | — |
| AC-INV-157 | TC-INV-287 | — |
| AC-INV-158 | TC-INV-288 | — |
| AC-INV-159 | TC-INV-290 | — |
| AC-INV-160 | TC-INV-295 | — |

### REQ → TC

| REQ | TC |
|---|---|
| REQ-INV-003 | TC-INV-173 |
| REQ-INV-006 | TC-INV-174, TC-INV-175 |
| REQ-INV-008 | TC-INV-176 |
| REQ-INV-010 | TC-INV-177 |
| REQ-INV-011 | TC-INV-178 |
| REQ-INV-012 | TC-INV-179 |
| REQ-INV-015 | TC-INV-213 |
| REQ-INV-022 | TC-INV-180 |
| REQ-INV-025 | TC-INV-181, TC-INV-182 |
| REQ-INV-029 | TC-INV-183 |
| REQ-INV-030 | TC-INV-184 |
| REQ-INV-032 | TC-INV-214 |
| REQ-INV-039 | TC-INV-218, TC-INV-219, TC-INV-220, TC-INV-221, TC-INV-262, TC-INV-294 |
| REQ-INV-056 | TC-INV-263 |
| REQ-INV-063 | TC-INV-238, TC-INV-239, TC-INV-240 |
| REQ-INV-065 | TC-INV-241 |
| REQ-INV-070 | TC-INV-215 |
| REQ-INV-072 | TC-INV-185 |
| REQ-INV-074 | TC-INV-186 |
| REQ-INV-075 | TC-INV-224, TC-INV-225 |
| REQ-INV-076 | TC-INV-187 |
| REQ-INV-078 | TC-INV-226, TC-INV-227 |
| REQ-INV-080 | TC-INV-188, TC-INV-285 |
| REQ-INV-082 | TC-INV-189 |
| REQ-INV-083 | TC-INV-190 |
| REQ-INV-084 | TC-INV-191 |
| REQ-INV-088 | TC-INV-192, TC-INV-228, TC-INV-229 |
| REQ-INV-090 | TC-INV-216 |
| REQ-INV-091 | TC-INV-193 |
| REQ-INV-093 | TC-INV-194 |
| REQ-INV-096 | TC-INV-195 |
| REQ-INV-097 | TC-INV-230, TC-INV-231 |
| REQ-INV-098 | TC-INV-196 |
| REQ-INV-099 | TC-INV-197 |
| REQ-INV-101 | TC-INV-198 |
| REQ-INV-109 | TC-INV-232, TC-INV-233 |
| REQ-INV-114 | TC-INV-217 |
| REQ-INV-115 | TC-INV-199 |
| REQ-INV-116 | TC-INV-200 |
| REQ-INV-118 | TC-INV-201 |
| REQ-INV-119 | TC-INV-202 |
| REQ-INV-121 | TC-INV-203, TC-INV-222, TC-INV-223 |
| REQ-INV-124 | TC-INV-204, TC-INV-234, TC-INV-235 |
| REQ-INV-125 | TC-INV-205, TC-INV-236, TC-INV-237 |
| REQ-INV-126 | TC-INV-206 |
| REQ-INV-127 | TC-INV-207 |
| REQ-INV-134 | TC-INV-208 |
| REQ-INV-135 | TC-INV-209 |
| REQ-INV-136 | TC-INV-210 |
| REQ-INV-137 | TC-INV-211 |
| REQ-INV-138 | TC-INV-212 |

### SCR → TC

| SCR | TC |
|---|---|
| SCR-INV-001 | TC-INV-173, TC-INV-174, TC-INV-175, TC-INV-176, TC-INV-183, TC-INV-184, TC-INV-213, TC-INV-214 |
| SCR-INV-002 | TC-INV-180, TC-INV-181, TC-INV-182, TC-INV-214 |
| SCR-INV-003 | TC-INV-179, TC-INV-203, TC-INV-209, TC-INV-210, TC-INV-211, TC-INV-213, TC-INV-218, TC-INV-219, TC-INV-220, TC-INV-221, TC-INV-222, TC-INV-223, TC-INV-262, TC-INV-294 |
| SCR-INV-004 | TC-INV-187, TC-INV-210, TC-INV-213, TC-INV-224, TC-INV-225, TC-INV-263 |
| SCR-INV-005 | TC-INV-178, TC-INV-188, TC-INV-189, TC-INV-190, TC-INV-191, TC-INV-210, TC-INV-213, TC-INV-226, TC-INV-227, TC-INV-263, TC-INV-285 |
| SCR-INV-006 | TC-INV-192, TC-INV-193, TC-INV-194, TC-INV-195, TC-INV-210, TC-INV-216, TC-INV-228, TC-INV-229 |
| SCR-INV-007 | TC-INV-177, TC-INV-196, TC-INV-197, TC-INV-198, TC-INV-208, TC-INV-210, TC-INV-212, TC-INV-213, TC-INV-215, TC-INV-217, TC-INV-230, TC-INV-231 |
| SCR-INV-008 | TC-INV-199, TC-INV-200, TC-INV-210, TC-INV-217, TC-INV-232, TC-INV-233 |
| SCR-INV-009 | TC-INV-204, TC-INV-205, TC-INV-206, TC-INV-207, TC-INV-234, TC-INV-235, TC-INV-236, TC-INV-237 |
| SCR-INV-010 | TC-INV-185, TC-INV-186, TC-INV-215 |
| SCR-INV-011 | TC-INV-201, TC-INV-202, TC-INV-241 |
| SCR-INV-012 | TC-INV-215, TC-INV-238, TC-INV-239, TC-INV-240 |

### RULE / code → TC

| RULE / code | TC |
|---|---|
| RULE-INV-002 | TC-INV-173 |
| RULE-INV-003 | TC-INV-174 |
| RULE-INV-004 | TC-INV-175 |
| RULE-INV-006 | TC-INV-176 |
| RULE-INV-008 | TC-INV-177 |
| RULE-INV-011 | TC-INV-180 |
| RULE-INV-012 | TC-INV-181 |
| RULE-INV-013 | TC-INV-182 |
| RULE-INV-016 | TC-INV-178, TC-INV-189 |
| RULE-INV-018 | TC-INV-285 |
| RULE-INV-019 | TC-INV-190 |
| RULE-INV-026 | TC-INV-186, TC-INV-215 |
| RULE-INV-028 | TC-INV-185 |
| RULE-INV-029 | TC-INV-195 |
| RULE-INV-031 | TC-INV-188, TC-INV-194 |
| RULE-INV-032 | TC-INV-193, TC-INV-229 |
| RULE-INV-033 | TC-INV-198 |
| RULE-INV-034 | TC-INV-197 |
| RULE-INV-037 | TC-INV-191 |
| RULE-INV-038 | TC-INV-199 |
| RULE-INV-039 | TC-INV-217 |
| RULE-INV-041 | TC-INV-205 |
| RULE-INV-042 | TC-INV-206 |
| RULE-INV-043 | TC-INV-207 |
| RULE-INV-045 | TC-INV-204 |
| RULE-INV-047 | TC-INV-192 |
| RULE-INV-048 | TC-INV-196 |
| INV-403 | TC-INV-212 |
| INV-409-BRANCH-DEFAULT | TC-INV-181 |
| INV-409-BRANCH-IN-USE | TC-INV-182 |
| INV-409-BRANCH-NAME-TAKEN | TC-INV-180 |
| INV-409-DOCUMENT-CLOSED | TC-INV-195 |
| INV-409-DUAL-QUANTITY-IN-USE | TC-INV-207 |
| INV-409-LOT-TRACKING-IN-USE | TC-INV-206 |
| INV-409-NEGATIVE-STOCK | TC-INV-190 |
| INV-409-WAREHOUSE-DEFAULT | TC-INV-174 |
| INV-409-WAREHOUSE-DEFAULT-INACTIVE | TC-INV-176 |
| INV-409-WAREHOUSE-IN-USE | TC-INV-175 |
| INV-409-WAREHOUSE-NAME-TAKEN | TC-INV-173 |
| INV-422-BARCODE-UNKNOWN | TC-INV-193, TC-INV-229 |
| INV-422-BUNDLE-SETTINGS | TC-INV-204 |
| INV-422-COUNTED-NEGATIVE | TC-INV-192 |
| INV-422-DUPLICATE-LINE | TC-INV-196 |
| INV-422-HORIZON-RANGE | TC-INV-202 |
| INV-422-LOAD-ROW-VARIANT | TC-INV-185 |
| INV-422-LOAD-ROWS-INVALID | TC-INV-185 |
| INV-422-LOT-REQUIRED | TC-INV-191 |
| INV-422-MEASURE-UNIT-REQUIRED | TC-INV-205 |
| INV-422-NO-LINES | TC-INV-188, TC-INV-194 |
| INV-422-OPENING-COST-REQUIRED | TC-INV-186 |
| INV-422-REASON-REQUIRED | TC-INV-198 |
| INV-422-TRANSFER-WAREHOUSES | TC-INV-189 |
| INV-422-VARIANT-INACTIVE | TC-INV-285 |
| INV-422-ZERO-CHANGE | TC-INV-197 |

### UXD → TC

| UXD | TC |
|---|---|
| UXD-INV-001 | TC-INV-218, TC-INV-219 |
| UXD-INV-002 | TC-INV-220, TC-INV-221 |
| UXD-INV-003 | TC-INV-222, TC-INV-223 |
| UXD-INV-004 | TC-INV-224, TC-INV-225 |
| UXD-INV-005 | TC-INV-226, TC-INV-227 |
| UXD-INV-006 | TC-INV-228, TC-INV-229 |
| UXD-INV-007 | TC-INV-230, TC-INV-231 |
| UXD-INV-008 | TC-INV-232, TC-INV-233 |
| UXD-INV-009 | TC-INV-234, TC-INV-235 |
| UXD-INV-010 | TC-INV-236, TC-INV-237 |
| UXD-INV-011 | TC-INV-238, TC-INV-239 |

### Package → TC

| Package | TC |
|---|---|
| F2-SCR-INV-001 | TC-INV-174, TC-INV-175, TC-INV-176 |
| F2-SCR-INV-002 | TC-INV-181, TC-INV-182 |
| F2-SCR-INV-003 | TC-INV-219, TC-INV-221, TC-INV-223 |
| F2-SCR-INV-004 | TC-INV-225 |
| F2-SCR-INV-005 | TC-INV-188, TC-INV-190, TC-INV-227, TC-INV-285 |
| F2-SCR-INV-006 | TC-INV-193, TC-INV-194, TC-INV-195, TC-INV-229 |
| F2-SCR-INV-007 | TC-INV-197, TC-INV-231 |
| F2-SCR-INV-008 | TC-INV-200, TC-INV-233 |
| F2-SCR-INV-009 | TC-INV-204, TC-INV-206, TC-INV-207, TC-INV-235, TC-INV-237 |
| F2-SCR-INV-010 | TC-INV-185 |
| F2-SCR-INV-011 | TC-INV-201 |
| F2-SCR-INV-012 | TC-INV-239 |
| F3-SCR-INV-001 | TC-INV-173 |
| F3-SCR-INV-002 | TC-INV-180 |
| F3-SCR-INV-003 | TC-INV-218 |
| F3-SCR-INV-004 | TC-INV-224 |
| F3-SCR-INV-005 | TC-INV-189, TC-INV-191 |
| F3-SCR-INV-006 | TC-INV-192 |
| F3-SCR-INV-007 | TC-INV-196, TC-INV-198 |
| F3-SCR-INV-008 | TC-INV-232 |
| F3-SCR-INV-009 | TC-INV-205 |
| F3-SCR-INV-010 | TC-INV-186 |
| F3-SCR-INV-011 | TC-INV-202 |
| F3-SCR-INV-012 | TC-INV-238 |
| F4-SCR-INV-001 | TC-INV-183, TC-INV-184 |
| F4-SCR-INV-002 | TC-INV-214 |
| F4-SCR-INV-003 | TC-INV-179, TC-INV-203, TC-INV-209, TC-INV-220, TC-INV-222, TC-INV-262, TC-INV-294 |
| F4-SCR-INV-004 | TC-INV-187 |
| F4-SCR-INV-005 | TC-INV-178, TC-INV-226, TC-INV-263 |
| F4-SCR-INV-006 | TC-INV-216, TC-INV-228 |
| F4-SCR-INV-007 | TC-INV-177, TC-INV-208, TC-INV-217, TC-INV-230 |
| F4-SCR-INV-008 | TC-INV-199 |
| F4-SCR-INV-009 | TC-INV-234, TC-INV-236 |
| F4-SCR-INV-010 | TC-INV-215 |
| F4-SCR-INV-011 | TC-INV-241 |
| F4-SCR-INV-012 | TC-INV-240 |
| SEC-FE | TC-INV-210, TC-INV-211, TC-INV-212, TC-INV-213 |

## COVERAGE

| Measure | Result |
|---|---|
| AC covered (both tracks) | ✓ 160/160 · gaps: none |
| AC covered on this track | 57/160 (AC-INV-160 of gate-analysis revise 5 is backend case TC-INV-295; the rest on the other track; 50 ACs on both; AC-INV-155 … 158 of gate-analysis revise 3 are backend cases TC-INV-286 … 288, AC-INV-159 of revise 4 is backend case TC-INV-290) |
| REQ covered (both tracks) | ✓ 141/141 · gaps: none |
| REQ covered on this track | 51/141 |
| SCR covered | ✓ 12/12 · gaps: none |
| UXD covered | ✓ 11/11 (rendered + empty/failure each) · gaps: none |
| Split units with no TC | 13 of 50: F1-SCR-INV-001, F1-SCR-INV-002, F1-SCR-INV-003, F1-SCR-INV-004, F1-SCR-INV-005, F1-SCR-INV-006, F1-SCR-INV-007, F1-SCR-INV-008, F1-SCR-INV-009, F1-SCR-INV-010, F1-SCR-INV-011, F1-SCR-INV-012, ALIGN-FE · no AC, XM or UXD derives a case for them (ADR-INV-037); F3-SCR-INV-012 and F4-SCR-INV-011 carry TC-INV-238 and TC-INV-241 (ADR-INV-039) |
| Line refusals (gate-analysis revise 2) | TC-INV-285: a post refused for line 2 of 3 places the text on that line through `details[0].lineRef` (ADR-INV-046) |
| Pair figures on lot rows (gate-analysis revise 5) | TC-INV-294: reserved and available, figures of the Variant × warehouse pair, are shown once on the pair's first lot row and labelled per warehouse |
| Over-engineering guard | 73 TCs for 57 ACs + 11 UXD (ratio 1.07 ≤ 2) |

