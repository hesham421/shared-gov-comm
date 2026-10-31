# BACKEND TEST PLAN — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Profile : commerce   Stage : P4 (Test Plan) · pass 2 · round 1 · gate-analysis revise 3
Track  : backend · plan test · lane test-gen · prepared by claude:opus
Sources: _state/current-srs.md (INV v1 — REQ 140 · AC 158 · RULE 51 · SCR-REQ 12)
         _state/current-registry-srs.md (P1) · _state/current-registry-db.md (P2 — XM-INV-001 … XM-INV-015)
         _state/current-backend-execution-plan.md (P3.1 — API-INV-001 … 052, Error Catalog §5 / §5.1, INT-C)
         _state/current-frontend-execution-plan.md (P3.2) · _state/current-api-spec (api-spec-inv.yaml, OpenAPI 3.1.0)
         _state/current-dependency-graph.json (INV → CAT edges DELIVERED)
Owns   : TC-INV-001 … TC-INV-172, TC-INV-242 … TC-INV-261 and TC-INV-264 … TC-INV-284 and TC-INV-286 … TC-INV-288 (this file) — TC-INV-173 … 241,
         262, 263 and 285 are in frontend-test-plan-inv.md; TC-INV-242 … 261 were added at gate-analysis revise 1 (G5,
         G16 and the cases of G1, G3, G6 … G13, G15), TC-INV-264 … 284 at gate-analysis revise 2 (G2 … G9, G13, G14,
         G17 … G19), continuing the module's TC sequence
ADRs   : raised here ADR-INV-037 (track placement and package), ADR-INV-038 (asserted texts and codes; schema-bound
         derivation rule added at gate-analysis revise 1) — ACCEPTED, non-breaking · cited from the revise:
         ADR-INV-030, 035, 041, 042, 043, 044, 045 · cited from gate-analysis revise 2: ADR-INV-046, 047, 048, 049, 050
         (ADR-INV-038 point 1 narrowed there) · open BLOCKED: none raised here
══════════════════════════════════════════════════════════════════

## How to read this plan

- **Framework.** `profile.stack.testing` is agnostic. Each TC block below is the whole contract. The consumer
  repository chooses its tool and turns each block into one test.
- **Shapes.** Each TC cites the `API-*` it calls. Method, path, request and response schemas, the
  `ApiResponse<T>` envelope, paging and the error responses are read in `api-spec-inv.yaml`; they are not
  restated here.
- **In-process operations.** The contract operations that other modules call (CON-INV-006 … 019) are the injected
  `InventoryApi` bean, with no REST route (ADR-INV-024). Their TCs cite the method and the contract item in
  `Exercises` and name the package `SVC-API-INT` (ADR-INV-037).
- **Refusals.** A refusal is asserted as the HTTP status of its catalogue row, `code`, `messageAr` and
  `messageEn`. The texts are copied from the SRS. An in-process refusal is the same `LocalizedException` thrown
  to the caller. "Nothing saved" is checked by reading back through the cited read operation.
- **Clock.** "today" is the tenant's date. A TC that names a date fixes the tenant clock to it.
- **Catalogue data.** Every lookup value a case names is seeded by INV (SRS A6 `seeded`). Only TC-INV-265 needs host
  data: it makes the seeded COUNT_DIFFERENCE inactive in its own tenant through the given `mdl` call, because INV
  seeds every value of its keys and the set of non-seeded A6 values is empty by design (ADR-INV-047). That case is
  the one that exercises an inactive tenant value; no other case runs in its tenant. Unit-of-measure values (metre,
  KG) belong to CAT's consumed lookup and are preconditions of the CAT catalogue, not INV data.
- **Catalogue coverage.** Every row of the HTTP Error Catalog (§5) and every row of §5.1 (in-process codes) is
  asserted by at least one TC — the code is named on its `Rule / code` line and asserted in `Expected`. A row added to
  either catalogue adds its TC in the same change. The RULE/code → TC index below is the check.
- **Line refusals.** A refusal raised for one line names it in `details` (`lineRef`, `lineRefKind`, `rowNo` —
  ADR-INV-046); a TC that refuses a line asserts that entry.

<!-- PHASE:TEST-PLAN-BE:START traces=AC-INV-155,AC-INV-156,AC-INV-157,AC-INV-158,REQ-INV-139,REQ-INV-140,AC-INV-001,AC-INV-002,AC-INV-003,AC-INV-004,AC-INV-005,AC-INV-006,AC-INV-007,AC-INV-008,AC-INV-009,AC-INV-010,AC-INV-011,AC-INV-012,AC-INV-013,AC-INV-016,AC-INV-017,AC-INV-018,AC-INV-019,AC-INV-020,AC-INV-021,AC-INV-022,AC-INV-023,AC-INV-024,AC-INV-025,AC-INV-026,AC-INV-027,AC-INV-028,AC-INV-029,AC-INV-030,AC-INV-031,AC-INV-032,AC-INV-033,AC-INV-036,AC-INV-037,AC-INV-038,AC-INV-039,AC-INV-040,AC-INV-041,AC-INV-042,AC-INV-043,AC-INV-044,AC-INV-045,AC-INV-046,AC-INV-047,AC-INV-048,AC-INV-049,AC-INV-050,AC-INV-051,AC-INV-052,AC-INV-053,AC-INV-054,AC-INV-055,AC-INV-056,AC-INV-057,AC-INV-058,AC-INV-059,AC-INV-060,AC-INV-061,AC-INV-062,AC-INV-063,AC-INV-064,AC-INV-065,AC-INV-066,AC-INV-067,AC-INV-068,AC-INV-069,AC-INV-070,AC-INV-071,AC-INV-072,AC-INV-073,AC-INV-074,AC-INV-075,AC-INV-076,AC-INV-077,AC-INV-078,AC-INV-079,AC-INV-080,AC-INV-081,AC-INV-082,AC-INV-083,AC-INV-084,AC-INV-085,AC-INV-086,AC-INV-087,AC-INV-088,AC-INV-089,AC-INV-090,AC-INV-091,AC-INV-092,AC-INV-093,AC-INV-094,AC-INV-095,AC-INV-096,AC-INV-097,AC-INV-098,AC-INV-099,AC-INV-101,AC-INV-102,AC-INV-103,AC-INV-104,AC-INV-105,AC-INV-106,AC-INV-107,AC-INV-108,AC-INV-109,AC-INV-110,AC-INV-111,AC-INV-112,AC-INV-113,AC-INV-114,AC-INV-115,AC-INV-116,AC-INV-117,AC-INV-118,AC-INV-119,AC-INV-120,AC-INV-121,AC-INV-122,AC-INV-123,AC-INV-124,AC-INV-125,AC-INV-126,AC-INV-127,AC-INV-128,AC-INV-129,AC-INV-130,AC-INV-131,AC-INV-132,AC-INV-133,AC-INV-134,AC-INV-135,AC-INV-136,AC-INV-137,AC-INV-138,AC-INV-139,AC-INV-140,AC-INV-141,AC-INV-142,AC-INV-143,AC-INV-144,AC-INV-145,AC-INV-146,AC-INV-147,AC-INV-148,AC-INV-149,AC-INV-152,AC-INV-153,AC-INV-154,REQ-INV-001,REQ-INV-002,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-010,REQ-INV-013,REQ-INV-014,REQ-INV-015,REQ-INV-016,REQ-INV-017,REQ-INV-018,REQ-INV-019,REQ-INV-020,REQ-INV-021,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-031,REQ-INV-032,REQ-INV-033,REQ-INV-034,REQ-INV-035,REQ-INV-036,REQ-INV-037,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-043,REQ-INV-044,REQ-INV-045,REQ-INV-046,REQ-INV-047,REQ-INV-048,REQ-INV-049,REQ-INV-050,REQ-INV-051,REQ-INV-052,REQ-INV-053,REQ-INV-054,REQ-INV-055,REQ-INV-056,REQ-INV-057,REQ-INV-058,REQ-INV-059,REQ-INV-060,REQ-INV-061,REQ-INV-062,REQ-INV-063,REQ-INV-064,REQ-INV-065,REQ-INV-066,REQ-INV-067,REQ-INV-068,REQ-INV-069,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-075,REQ-INV-076,REQ-INV-077,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-108,REQ-INV-109,REQ-INV-110,REQ-INV-111,REQ-INV-112,REQ-INV-113,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-118,REQ-INV-119,REQ-INV-120,REQ-INV-121,REQ-INV-122,REQ-INV-123,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-130,REQ-INV-131,REQ-INV-132,REQ-INV-133,REQ-INV-136,REQ-INV-137,REQ-INV-138 -->
## TEST-PLAN-BE — module test cases

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
### TC-INV-012 — An issue referencing a released reservation is refused
Derived from : AC-INV-053  (REQ-INV-048)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : RULE-INV-021 → INV-409-RESERVATION-CLOSED (in-process, §5.1)
Package      : SVC-API-INT
Scenario     : STATE · data class INVALID · language ALL
Preconditions: a Reservation R of Variant V in Warehouse W with statusCode RELEASED
Host data    : none
Steps        : 1. SLS calls issueStock with 1 line of V in W referencing reservation R. 2. GET moves of V in W.
Expected     : 1 → code INV-409-RESERVATION-CLOSED, messageAr «الحجز مغلق ولا يمكن استخدامه.», messageEn "The reservation is closed and cannot be used.". 2 → no Stock move recorded.
Test data    : R released earlier through releaseReservation
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

<!-- SUB:API-SCENARIOS:START traces=AC-INV-001,AC-INV-002,AC-INV-003,AC-INV-005,AC-INV-006,AC-INV-009,AC-INV-010,AC-INV-012,AC-INV-013,AC-INV-016,AC-INV-018,AC-INV-019,AC-INV-022,AC-INV-023,AC-INV-024,AC-INV-025,AC-INV-027,AC-INV-028,AC-INV-031,AC-INV-032,AC-INV-033,AC-INV-036,AC-INV-037,AC-INV-038,AC-INV-039,AC-INV-040,AC-INV-041,AC-INV-042,AC-INV-043,AC-INV-044,AC-INV-045,AC-INV-046,AC-INV-047,AC-INV-048,AC-INV-050,AC-INV-051,AC-INV-052,AC-INV-054,AC-INV-055,AC-INV-056,AC-INV-057,AC-INV-059,AC-INV-062,AC-INV-063,AC-INV-064,AC-INV-065,AC-INV-066,AC-INV-067,AC-INV-068,AC-INV-069,AC-INV-072,AC-INV-074,AC-INV-076,AC-INV-077,AC-INV-080,AC-INV-082,AC-INV-083,AC-INV-084,AC-INV-085,AC-INV-086,AC-INV-087,AC-INV-089,AC-INV-092,AC-INV-095,AC-INV-096,AC-INV-097,AC-INV-099,AC-INV-102,AC-INV-104,AC-INV-105,AC-INV-107,AC-INV-108,AC-INV-110,AC-INV-112,AC-INV-114,AC-INV-116,AC-INV-117,AC-INV-119,AC-INV-121,AC-INV-122,AC-INV-123,AC-INV-124,AC-INV-125,AC-INV-128,AC-INV-129,AC-INV-130,AC-INV-131,AC-INV-132,AC-INV-134,AC-INV-135,AC-INV-137,AC-INV-138,AC-INV-140,AC-INV-145,AC-INV-146,AC-INV-148,AC-INV-149,AC-INV-152,AC-INV-153,AC-INV-154,REQ-INV-001,REQ-INV-002,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-010,REQ-INV-013,REQ-INV-015,REQ-INV-016,REQ-INV-019,REQ-INV-020,REQ-INV-021,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-031,REQ-INV-032,REQ-INV-033,REQ-INV-034,REQ-INV-035,REQ-INV-036,REQ-INV-037,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-043,REQ-INV-045,REQ-INV-046,REQ-INV-047,REQ-INV-049,REQ-INV-050,REQ-INV-051,REQ-INV-052,REQ-INV-053,REQ-INV-056,REQ-INV-057,REQ-INV-058,REQ-INV-059,REQ-INV-060,REQ-INV-061,REQ-INV-062,REQ-INV-063,REQ-INV-066,REQ-INV-068,REQ-INV-070,REQ-INV-071,REQ-INV-073,REQ-INV-075,REQ-INV-076,REQ-INV-077,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-084,REQ-INV-086,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-092,REQ-INV-094,REQ-INV-095,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-102,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-108,REQ-INV-109,REQ-INV-110,REQ-INV-111,REQ-INV-112,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-118,REQ-INV-120,REQ-INV-121,REQ-INV-123,REQ-INV-124,REQ-INV-125,REQ-INV-129,REQ-INV-130,REQ-INV-132,REQ-INV-133,REQ-INV-136,REQ-INV-137,REQ-INV-138 -->
### API-SCENARIOS — endpoint, in-process and permission cases

<!-- TC:TC-INV-047:START traces=AC-INV-001,REQ-INV-001,API-INV-001,API-INV-009 -->
### TC-INV-047 — Provisioning seeds one active default warehouse under the default branch
Derived from : AC-INV-001  (REQ-INV-001)
Exercises    : tenant provisioning (InvTenantProvisioning, TenantProvisioningContributor) · read back API-INV-001 GET /api/v1/inv/warehouses, API-INV-009 GET /api/v1/inv/branches
Rule / code  : — → —
Package      : CORE
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a tenant being provisioned; Owner session once provisioning completes
Host data    : none
Steps        : 1. Complete provisioning of the tenant. 2. GET warehouses, active ALL. 3. GET branches, active ALL.
Expected     : 2 → exactly 1 Warehouse: isActiveFl true, isDefaultFl true, nameAr «المستودع الرئيسي», nameEn "Main warehouse", branchPk = the branchPk of the default Branch from step 3.
Test data    : a new tenant
<!-- TC:TC-INV-047:END -->

<!-- TC:TC-INV-048:START traces=AC-INV-002,REQ-INV-002,API-INV-001 -->
### TC-INV-048 — A second provisioning run creates no further warehouse
Derived from : AC-INV-002  (REQ-INV-002)
Exercises    : tenant provisioning (InvTenantProvisioning) run twice · read back API-INV-001
Rule / code  : — → —
Package      : CORE
Scenario     : STATE · data class EDGE · language ALL
Preconditions: a tenant with 1 Warehouse, its warehousePk recorded
Host data    : none
Steps        : 1. Run the INV provisioning contribution for the tenant a second time. 2. GET warehouses, active ALL.
Expected     : exactly 1 Warehouse, with the recorded warehousePk.
Test data    : —
<!-- TC:TC-INV-048:END -->

<!-- TC:TC-INV-049:START traces=AC-INV-003,REQ-INV-003,API-INV-003,API-INV-001 -->
### TC-INV-049 — Creating a warehouse issues its code from the series
Derived from : AC-INV-003  (REQ-INV-003)
Exercises    : API-INV-003 POST /api/v1/inv/warehouses · read back API-INV-001
Rule / code  : RULE-INV-001 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; a tenant with 1 active Warehouse
Host data    : none
Steps        : 1. POST a warehouse with nameAr «مستودع الفرع». 2. GET warehouses, active ALL.
Expected     : 1 → 201 with isActiveFl true, isDefaultFl false and a code issued by series INV_WAREHOUSE (prefix WH-, 4 digits). 2 → 2 Warehouses.
Test data    : nameAr «مستودع الفرع»
<!-- TC:TC-INV-049:END -->

<!-- TC:TC-INV-050:START traces=AC-INV-005,REQ-INV-004,API-INV-004 -->
### TC-INV-050 — Updating a warehouse keeps its code
Derived from : AC-INV-005  (REQ-INV-004)
Exercises    : API-INV-004 PUT /api/v1/inv/warehouses/{id}
Rule / code  : RULE-INV-001 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; a Warehouse with code WH-0002, read with its version
Host data    : none
Steps        : 1. PUT the warehouse with its stored nameAr and branchPk, nameEn "North warehouse" and its version.
Expected     : 200 with nameEn "North warehouse" and code WH-0002.
Test data    : nameEn "North warehouse"
<!-- TC:TC-INV-050:END -->

<!-- TC:TC-INV-051:START traces=AC-INV-006,REQ-INV-005,API-INV-005,API-INV-001,API-INV-002 -->
### TC-INV-051 — A deactivated warehouse leaves the active list and stays readable
Derived from : AC-INV-006  (REQ-INV-005)
Exercises    : API-INV-005 DELETE /api/v1/inv/warehouses/{id} · API-INV-001 · API-INV-002 GET /api/v1/inv/warehouses/{id}
Rule / code  : RULE-INV-005 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; a non-default Warehouse W with no stock and no ACTIVE reservation
Host data    : none
Steps        : 1. DELETE warehouse W. 2. GET warehouses, active ACTIVE_ONLY. 3. GET warehouse W.
Expected     : 1 → 200, isActiveFl false. 2 → W absent. 3 → 200, W returned with isActiveFl false.
Test data    : —
<!-- TC:TC-INV-051:END -->

<!-- TC:TC-INV-052:START traces=AC-INV-009,REQ-INV-007,API-INV-006,API-INV-001 -->
### TC-INV-052 — Reactivating a warehouse returns it to the active list
Derived from : AC-INV-009  (REQ-INV-007)
Exercises    : API-INV-006 PATCH /api/v1/inv/warehouses/{id}/reactivate · API-INV-001
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; an inactive Warehouse W (its branch active), read with its version
Host data    : none
Steps        : 1. PATCH W /reactivate with its version. 2. GET warehouses, active ACTIVE_ONLY.
Expected     : 1 → 200, isActiveFl true. 2 → W listed.
Test data    : —
<!-- TC:TC-INV-052:END -->

<!-- TC:TC-INV-053:START traces=AC-INV-010,REQ-INV-008,API-INV-007,API-INV-001 -->
### TC-INV-053 — Setting a new default warehouse clears the previous one
Derived from : AC-INV-010  (REQ-INV-008)
Exercises    : API-INV-007 PATCH /api/v1/inv/warehouses/{id}/default · API-INV-001
Rule / code  : RULE-INV-006 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; default Warehouse A; active Warehouse B read with its version
Host data    : none
Steps        : 1. PATCH B /default with its version. 2. GET warehouses, active ALL.
Expected     : B isDefaultFl true, A isDefaultFl false, exactly 1 Warehouse of the tenant with isDefaultFl true.
Test data    : —
<!-- TC:TC-INV-053:END -->

<!-- TC:TC-INV-054:START traces=AC-INV-012,REQ-INV-009,API-INV-001 -->
### TC-INV-054 — Warehouse search with "active only" returns the active ones ordered by code
Derived from : AC-INV-012  (REQ-INV-009)
Exercises    : API-INV-001 GET /api/v1/inv/warehouses
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a session holding PERM_INV_WAREHOUSES_VIEW; 3 Warehouses, 1 of them inactive
Host data    : none
Steps        : 1. GET warehouses with active ACTIVE_ONLY.
Expected     : 200, a page of the 2 active Warehouses in ascending code order.
Test data    : —
<!-- TC:TC-INV-054:END -->

<!-- TC:TC-INV-055:START traces=AC-INV-013,REQ-INV-010,API-INV-036,API-INV-008 -->
### TC-INV-055 — With one warehouse an adjustment saved without warehouse gets the default
Derived from : AC-INV-013  (REQ-INV-010)
Exercises    : API-INV-008 GET /api/v1/inv/context · API-INV-036 POST /api/v1/inv/stock-adjustments
Rule / code  : RULE-INV-008 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; a tenant with exactly 1 active Warehouse W (the default)
Host data    : none
Steps        : 1. GET context. 2. POST an adjustment with warehousePk null, reason DAMAGE, 1 line of V −1.
Expected     : 1 → activeWarehouseCount 1. 2 → 201 with warehousePk W.
Test data    : reasonCode DAMAGE (seeded); line V −1 (placeholder Variant)
<!-- TC:TC-INV-055:END -->

<!-- TC:TC-INV-056:START traces=AC-INV-016,REQ-INV-013,API-INV-018 -->
### TC-INV-056 — With one warehouse an issue that names none is recorded in the default
Derived from : AC-INV-016  (REQ-INV-013)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : RULE-INV-008 → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a tenant with exactly 1 active Warehouse W; Variant V with stock in W
Host data    : none
Steps        : 1. SLS calls issueStock with no warehouse, 1 line of V. 2. GET moves of V.
Expected     : the recorded Stock move has warehousePk W.
Test data    : quantity 1; source document type SALES_INVOICE
<!-- TC:TC-INV-056:END -->

<!-- TC:TC-INV-057:START traces=AC-INV-018,REQ-INV-015,API-INV-003,API-INV-008,API-INV-018 -->
### TC-INV-057 — A second warehouse raises the active count and leaves existing moves unchanged
Derived from : AC-INV-018  (REQ-INV-015)
Exercises    : API-INV-003 POST /api/v1/inv/warehouses · API-INV-008 GET /api/v1/inv/context · API-INV-018 GET /api/v1/inv/stock-moves
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : STATE · data class VALID · language ALL
Preconditions: a tenant with 1 active Warehouse W and 40 Stock moves, all with warehousePk W
Host data    : none
Steps        : 1. GET context. 2. Owner POSTs a second warehouse. 3. GET context. 4. GET all moves.
Expected     : 1 → activeWarehouseCount 1. 3 → activeWarehouseCount 2. 4 → the 40 moves, each still with warehousePk W.
Test data    : 40 moves (placeholders of any type)
<!-- TC:TC-INV-057:END -->

<!-- TC:TC-INV-058:START traces=AC-INV-019,REQ-INV-016,API-INV-018,API-INV-002 -->
### TC-INV-058 — Every recorded move references an existing warehouse
Derived from : AC-INV-019  (REQ-INV-016)
Exercises    : API-INV-018 GET /api/v1/inv/stock-moves · API-INV-002 GET /api/v1/inv/warehouses/{id}
Rule / code  : RULE-INV-008 → — (FK_INV_STOCK_MOVE_INV_WAREHOUSE)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a tenant with recorded Stock moves of several types
Host data    : none
Steps        : 1. GET all moves. 2. GET each distinct warehousePk of step 1.
Expected     : 1 → every move carries a warehousePk. 2 → every one answers 200.
Test data    : —
<!-- TC:TC-INV-058:END -->

<!-- TC:TC-INV-059:START traces=AC-INV-022,REQ-INV-019 -->
### TC-INV-059 — Another module reads the active warehouses with their count
Derived from : AC-INV-022  (REQ-INV-019)
Exercises    : InventoryApi.listActiveWarehouses — CON-INV-007 (in-process, caller SLS)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: 2 active Warehouses and 1 inactive Warehouse
Host data    : none
Steps        : 1. SLS calls listActiveWarehouses.
Expected     : 2 Warehouses (identifier, code, names, branch, default flag) and activeCount 2.
Test data    : —
<!-- TC:TC-INV-059:END -->

<!-- TC:TC-INV-060:START traces=AC-INV-023,REQ-INV-020,API-INV-009 -->
### TC-INV-060 — Provisioning seeds one active default branch
Derived from : AC-INV-023  (REQ-INV-020)
Exercises    : tenant provisioning (InvTenantProvisioning) · read back API-INV-009 GET /api/v1/inv/branches
Rule / code  : — → —
Package      : CORE
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a tenant being provisioned
Host data    : none
Steps        : 1. Complete provisioning. 2. GET branches, active ALL.
Expected     : exactly 1 Branch: isActiveFl true, isDefaultFl true, nameAr «الفرع الرئيسي», nameEn "Main branch".
Test data    : a new tenant
<!-- TC:TC-INV-060:END -->

<!-- TC:TC-INV-061:START traces=AC-INV-024,REQ-INV-021,API-INV-009 -->
### TC-INV-061 — A second provisioning run creates no further branch
Derived from : AC-INV-024  (REQ-INV-021)
Exercises    : tenant provisioning run twice · read back API-INV-009
Rule / code  : — → —
Package      : CORE
Scenario     : STATE · data class EDGE · language ALL
Preconditions: a tenant with 1 Branch, its branchPk recorded
Host data    : none
Steps        : 1. Run the INV provisioning contribution a second time. 2. GET branches, active ALL.
Expected     : exactly 1 Branch, with the recorded branchPk.
Test data    : —
<!-- TC:TC-INV-061:END -->

<!-- TC:TC-INV-062:START traces=AC-INV-025,REQ-INV-022,API-INV-011,API-INV-009 -->
### TC-INV-062 — Creating a branch issues its code from the series
Derived from : AC-INV-025  (REQ-INV-022)
Exercises    : API-INV-011 POST /api/v1/inv/branches · API-INV-009
Rule / code  : RULE-INV-010 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; a tenant with 1 active Branch
Host data    : none
Steps        : 1. POST a branch with nameAr «فرع المعادي». 2. GET branches, active ALL.
Expected     : 1 → 201 with isActiveFl true, isDefaultFl false and a code issued by series INV_BRANCH (prefix BR-, 4 digits). 2 → 2 Branches.
Test data    : nameAr «فرع المعادي»
<!-- TC:TC-INV-062:END -->

<!-- TC:TC-INV-063:START traces=AC-INV-027,REQ-INV-023,API-INV-012 -->
### TC-INV-063 — Updating a branch keeps its code
Derived from : AC-INV-027  (REQ-INV-023)
Exercises    : API-INV-012 PUT /api/v1/inv/branches/{id}
Rule / code  : RULE-INV-010 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; a Branch with code BR-0002, read with its version
Host data    : none
Steps        : 1. PUT the branch with its stored nameAr, nameEn "Maadi branch" and its version.
Expected     : 200 with nameEn "Maadi branch" and code BR-0002.
Test data    : nameEn "Maadi branch"
<!-- TC:TC-INV-063:END -->

<!-- TC:TC-INV-064:START traces=AC-INV-028,REQ-INV-024,API-INV-013,API-INV-009,API-INV-010 -->
### TC-INV-064 — A deactivated branch leaves the active list and stays readable
Derived from : AC-INV-028  (REQ-INV-024)
Exercises    : API-INV-013 DELETE /api/v1/inv/branches/{id} · API-INV-009 · API-INV-010 GET /api/v1/inv/branches/{id}
Rule / code  : RULE-INV-014 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; a non-default Branch B2 with no active Warehouse
Host data    : none
Steps        : 1. DELETE branch B2. 2. GET branches, active ACTIVE_ONLY. 3. GET branch B2.
Expected     : 1 → isActiveFl false. 2 → B2 absent. 3 → 200, B2 returned.
Test data    : —
<!-- TC:TC-INV-064:END -->

<!-- TC:TC-INV-065:START traces=AC-INV-031,REQ-INV-026,API-INV-014,API-INV-009 -->
### TC-INV-065 — Reactivating a branch returns it to the active list
Derived from : AC-INV-031  (REQ-INV-026)
Exercises    : API-INV-014 PATCH /api/v1/inv/branches/{id}/reactivate · API-INV-009
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; an inactive Branch B2 read with its version
Host data    : none
Steps        : 1. PATCH B2 /reactivate with its version. 2. GET branches, active ACTIVE_ONLY.
Expected     : 1 → isActiveFl true. 2 → B2 listed.
Test data    : —
<!-- TC:TC-INV-065:END -->

<!-- TC:TC-INV-066:START traces=AC-INV-032,REQ-INV-027,API-INV-015,API-INV-009 -->
### TC-INV-066 — Setting a new default branch clears the previous one
Derived from : AC-INV-032  (REQ-INV-027)
Exercises    : API-INV-015 PATCH /api/v1/inv/branches/{id}/default · API-INV-009
Rule / code  : RULE-INV-015 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; default Branch A; active Branch B read with its version
Host data    : none
Steps        : 1. PATCH B /default with its version. 2. GET branches, active ALL.
Expected     : B isDefaultFl true, A isDefaultFl false, exactly 1 Branch with isDefaultFl true.
Test data    : —
<!-- TC:TC-INV-066:END -->

<!-- TC:TC-INV-067:START traces=AC-INV-033,REQ-INV-028,API-INV-009 -->
### TC-INV-067 — Branch search with "active only" returns the active ones ordered by code
Derived from : AC-INV-033  (REQ-INV-028)
Exercises    : API-INV-009 GET /api/v1/inv/branches
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a session holding PERM_INV_BRANCHES_VIEW; 3 Branches, 1 inactive
Host data    : none
Steps        : 1. GET branches with active ACTIVE_ONLY.
Expected     : 200, the 2 active Branches in ascending code order.
Test data    : —
<!-- TC:TC-INV-067:END -->

<!-- TC:TC-INV-068:START traces=AC-INV-036,REQ-INV-031,API-INV-003 -->
### TC-INV-068 — A warehouse saved without a branch gets the default branch
Derived from : AC-INV-036  (REQ-INV-031)
Exercises    : API-INV-003 POST /api/v1/inv/warehouses
Rule / code  : RULE-INV-009 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; the default Branch is B1
Host data    : none
Steps        : 1. POST a warehouse with a new nameAr and branchPk null.
Expected     : 201 with branchPk B1.
Test data    : nameAr: any name not yet used (placeholder)
<!-- TC:TC-INV-068:END -->

<!-- TC:TC-INV-069:START traces=AC-INV-037,REQ-INV-032,API-INV-011,API-INV-008,API-INV-001 -->
### TC-INV-069 — A second branch raises the active count and leaves warehouses unchanged
Derived from : AC-INV-037  (REQ-INV-032)
Exercises    : API-INV-011 POST /api/v1/inv/branches · API-INV-008 GET /api/v1/inv/context · API-INV-001
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : STATE · data class VALID · language ALL
Preconditions: a tenant with 1 active Branch B1 and 2 Warehouses (branchPk B1)
Host data    : none
Steps        : 1. Owner POSTs a second branch. 2. GET context. 3. GET warehouses, active ALL.
Expected     : 2 → activeBranchCount 2. 3 → both Warehouses keep branchPk B1.
Test data    : —
<!-- TC:TC-INV-069:END -->

<!-- TC:TC-INV-070:START traces=AC-INV-038,REQ-INV-033 -->
### TC-INV-070 — Another module reads the active branches with their count
Derived from : AC-INV-038  (REQ-INV-033)
Exercises    : InventoryApi.listActiveBranches — CON-INV-009 (in-process, caller SLS)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: 1 active Branch
Host data    : none
Steps        : 1. SLS calls listActiveBranches.
Expected     : 1 Branch with isDefaultFl true and activeCount 1.
Test data    : —
<!-- TC:TC-INV-070:END -->

<!-- TC:TC-INV-071:START traces=AC-INV-039,REQ-INV-034,API-INV-005 -->
### TC-INV-071 — Deactivating a warehouse publishes one master-change event after commit
Derived from : AC-INV-039  (REQ-INV-034)
Exercises    : API-INV-005 DELETE /api/v1/inv/warehouses/{id} · event inv.InventoryMasterChanged (CON-INV-021)
Rule / code  : RULE-INV-005 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; an active non-default Warehouse W (no stock, no ACTIVE reservation); an event consumer capturing deliveries
Host data    : none
Steps        : 1. DELETE W. 2. Read the delivered events. 3. Repeat on a second such Warehouse W' with the transaction forced to roll back after the flag change.
Expected     : 2 → exactly 1 event with entity ENT-INV-001, identifier W, changeKindCode DEACTIVATED, delivered after commit. 3 → no event for W', and W' stays active.
Test data    : changeKindCode DEACTIVATED (seeded)
<!-- TC:TC-INV-071:END -->

<!-- TC:TC-INV-072:START traces=AC-INV-040,REQ-INV-035,API-INV-016 -->
### TC-INV-072 — POS and storefront issues change one shared balance
Derived from : AC-INV-040  (REQ-INV-035)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, callers SLS and SFR) · read back API-INV-016 GET /api/v1/inv/stock-balances
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V on-hand 10 in Warehouse W
Host data    : none
Steps        : 1. SLS issues 2 of V in W for a POS sale. 2. SFR issues 3 of V in W for a storefront order. 3. GET balances of V in W.
Expected     : 3 → one Stock balance row of V in W with on-hand 5.
Test data    : source document types POS_SALE and STOREFRONT_ORDER (seeded)
<!-- TC:TC-INV-072:END -->

<!-- TC:TC-INV-073:START traces=AC-INV-041,REQ-INV-036,API-INV-016,API-INV-018 -->
### TC-INV-073 — A receipt changes the balance in the same transaction
Derived from : AC-INV-041  (REQ-INV-036)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-016, API-INV-018
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V on-hand 4 in Warehouse W (from earlier moves)
Host data    : none
Steps        : 1. PUR records a RECEIPT of 6 of V in W and the transaction commits. 2. GET the balance of V in W. 3. GET all moves of V in W.
Expected     : 2 → on-hand 10. 3 → incoming quantities minus outgoing quantities = 10.
Test data    : unit cost: placeholder ≥ 0
<!-- TC:TC-INV-073:END -->

<!-- TC:TC-INV-074:START traces=AC-INV-042,REQ-INV-037,API-INV-016,API-INV-018 -->
### TC-INV-074 — Ten parallel issues of one Variant are all applied
Derived from : AC-INV-042  (REQ-INV-037)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, callers SLS and SFR, concurrent) · read back API-INV-016, API-INV-018
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Variant V on-hand 20 in Warehouse W
Host data    : none
Steps        : 1. Submit 10 issues of 1 of V in W in parallel, from POS and storefront, each with its own idempotency key. 2. GET moves of V in W. 3. GET the balance.
Expected     : 2 → 10 ISSUE moves recorded. 3 → on-hand 10.
Test data    : 10 distinct idempotency keys
<!-- TC:TC-INV-074:END -->

<!-- TC:TC-INV-075:START traces=AC-INV-043,REQ-INV-038,API-INV-017 -->
### TC-INV-075 — Availability excludes expired lots and ACTIVE reservations
Derived from : AC-INV-043  (REQ-INV-038)
Exercises    : API-INV-017 GET /api/v1/inv/stock-balances/availability/{variantId}
Rule / code  : RULE-INV-038 → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V in Warehouse W on-hand 12, of which 2 in an expired Lot; ACTIVE reservations of 3
Host data    : none
Steps        : 1. GET the availability of V with warehousePk W.
Expected     : W: onHandQty 12, reservedQty 3, availableQty 7.
Test data    : the expired lot's expiry is before the tenant's today
<!-- TC:TC-INV-075:END -->

<!-- TC:TC-INV-076:START traces=AC-INV-044,REQ-INV-039,API-INV-016 -->
### TC-INV-076 — A Seller/Cashier searches balances per warehouse
Derived from : AC-INV-044  (REQ-INV-039)
Exercises    : API-INV-016 GET /api/v1/inv/stock-balances
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Seller/Cashier session; Variant V on-hand 5 in W1 and 8 in W2; an ACTIVE reservation of 2 in W2
Host data    : none
Steps        : 1. GET balances with variantPk V.
Expected     : 200 with 2 rows: W1 on-hand 5, reserved 0, available 5; W2 on-hand 8, reserved 2, available 6. No cost or value field.
Test data    : —
<!-- TC:TC-INV-076:END -->

<!-- TC:TC-INV-077:START traces=AC-INV-045,REQ-INV-040 -->
### TC-INV-077 — The storefront reads availability per warehouse and in total
Derived from : AC-INV-045  (REQ-INV-040)
Exercises    : InventoryApi.readAvailability — CON-INV-010 (in-process, caller SFR)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V available 5 in W1 and 6 in W2
Host data    : none
Steps        : 1. SFR calls readAvailability for V with no warehouse.
Expected     : W1 available 5, W2 available 6, total available 11.
Test data    : —
<!-- TC:TC-INV-077:END -->

<!-- TC:TC-INV-078:START traces=AC-INV-046,REQ-INV-041 -->
### TC-INV-078 — A Variant that never moved answers zeros
Derived from : AC-INV-046  (REQ-INV-041)
Exercises    : InventoryApi.readAvailability — CON-INV-010 (in-process, caller SLS)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: an active Variant V that has never moved
Host data    : none
Steps        : 1. SLS calls readAvailability for V.
Expected     : on-hand 0, reserved 0, available 0; no error.
Test data    : —
<!-- TC:TC-INV-078:END -->

<!-- TC:TC-INV-079:START traces=AC-INV-047,REQ-INV-042,API-INV-017 -->
### TC-INV-079 — Bundle availability is the smallest whole quotient of its components
Derived from : AC-INV-047  (REQ-INV-042)
Exercises    : API-INV-017 GET /api/v1/inv/stock-balances/availability/{variantId}
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: bundles on; Bundle B made of 2 × C1 and 1 × C2; in W, C1 available 7 and C2 available 5
Host data    : none
Steps        : 1. GET the availability of B with warehousePk W.
Expected     : B available 3 in W (7 ÷ 2 = 3.5 → 3; 5 ÷ 1 = 5; smallest 3).
Test data    : —
<!-- TC:TC-INV-079:END -->

<!-- TC:TC-INV-080:START traces=AC-INV-048,REQ-INV-043 -->
### TC-INV-080 — The storefront reserves stock for an order
Derived from : AC-INV-048  (REQ-INV-043)
Exercises    : InventoryApi.reserve — CON-INV-011 · InventoryApi.readAvailability — CON-INV-010 (in-process, caller SFR)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V available 10 in W
Host data    : none
Steps        : 1. SFR reserves 4 of V in W for storefront order SO-100. 2. SFR reads the availability of V in W.
Expected     : 1 → an ACTIVE Reservation with originalQty 4, reservedQty 4, shortfallQty 0, sourceDocNo SO-100. 2 → available 6.
Test data    : source document type STOREFRONT_ORDER, number SO-100
<!-- TC:TC-INV-080:END -->

<!-- TC:TC-INV-081:START traces=AC-INV-050,REQ-INV-045 -->
### TC-INV-081 — A reservation beyond availability records its shortfall while negative stock is allowed
Derived from : AC-INV-050  (REQ-INV-045)
Exercises    : InventoryApi.reserve — CON-INV-011 (in-process, caller SLS)
Rule / code  : RULE-INV-020 (shortfall branch) → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: INV_ALLOW_NEGATIVE_STOCK true; Variant V available 3 in W
Host data    : none
Steps        : 1. SLS reserves 5 of V in W.
Expected     : an ACTIVE Reservation with reservedQty 5 and shortfallQty 2; the answer carries shortfallQty 2.
Test data    : source document type SALES_ORDER
<!-- TC:TC-INV-081:END -->

<!-- TC:TC-INV-082:START traces=AC-INV-051,REQ-INV-046 -->
### TC-INV-082 — Releasing a reservation returns its goods to availability
Derived from : AC-INV-051  (REQ-INV-046)
Exercises    : InventoryApi.releaseReservation — CON-INV-012 · InventoryApi.readAvailability — CON-INV-010 (in-process, caller SFR)
Rule / code  : RULE-INV-021 → —
Package      : SVC-API-INT
Scenario     : STATE · data class VALID · language ALL
Preconditions: an ACTIVE Reservation R of 4 of V in W; V available 6 in W
Host data    : none
Steps        : 1. SFR releases R. 2. SFR reads the availability of V in W.
Expected     : 1 → R has statusCode RELEASED, reservedQty 0 and a closedAt time. 2 → available 10.
Test data    : —
<!-- TC:TC-INV-082:END -->

<!-- TC:TC-INV-083:START traces=AC-INV-052,REQ-INV-047 -->
### TC-INV-083 — Issues referencing a reservation consume it down to CONSUMED
Derived from : AC-INV-052  (REQ-INV-047)
Exercises    : InventoryApi.issueStock — CON-INV-015 · InventoryApi.reservationsOfDocument — CON-INV-013 (in-process, caller SLS)
Rule / code  : RULE-INV-021 → —
Package      : SVC-API-INT
Scenario     : STATE · data class VALID · language ALL
Preconditions: an ACTIVE Reservation R with reservedQty 4 of V in W, for a SALES_ORDER document
Host data    : none
Steps        : 1. SLS issues 3 referencing R. 2. Read R. 3. SLS issues 1 referencing R. 4. Read R.
Expected     : 2 → reservedQty 1, statusCode ACTIVE. 4 → reservedQty 0, statusCode CONSUMED, closedAt set. Both issue moves carry reservationPk R.
Test data    : two distinct idempotency keys
<!-- TC:TC-INV-083:END -->

<!-- TC:TC-INV-084:START traces=AC-INV-054,REQ-INV-049 -->
### TC-INV-084 — A bundle is reserved through its components
Derived from : AC-INV-054  (REQ-INV-049)
Exercises    : InventoryApi.reserve — CON-INV-011 · InventoryApi.reservationsOfDocument — CON-INV-013 (in-process, caller SLS)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: bundles on; Bundle B made of 2 × C1 and 1 × C2, enough available in W
Host data    : none
Steps        : 1. SLS reserves 3 of B in W. 2. Read the reservations of the document.
Expected     : 2 ACTIVE Reservations: C1 reservedQty 6 and C2 reservedQty 3, each with bundleVariantPk B.
Test data    : source document type SALES_ORDER
<!-- TC:TC-INV-084:END -->

<!-- TC:TC-INV-085:START traces=AC-INV-055,REQ-INV-050 -->
### TC-INV-085 — The reservations of a source document are read back with their status
Derived from : AC-INV-055  (REQ-INV-050)
Exercises    : InventoryApi.reservationsOfDocument — CON-INV-013 (in-process, caller SLS)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: order SO-100 with 2 Reservations, 1 ACTIVE and 1 CONSUMED
Host data    : none
Steps        : 1. SLS reads the reservations of SO-100 (its source document type and identifier).
Expected     : both Reservations, with Variant, warehouse, statusCode ACTIVE and CONSUMED, remaining quantity and shortfall.
Test data    : source document type SALES_ORDER, number SO-100
<!-- TC:TC-INV-085:END -->

<!-- TC:TC-INV-086:START traces=AC-INV-056,REQ-INV-051,API-INV-018,API-INV-016 -->
### TC-INV-086 — A purchase receipt records one move at the line cost
Derived from : AC-INV-056  (REQ-INV-051)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-018, API-INV-016
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V on-hand 0 in W
Host data    : none
Steps        : 1. PUR submits receipt GR-7 with 1 line of 10 of V at unit cost 25.0000. 2. GET moves with docNo GR-7. 3. GET the balance of V in W.
Expected     : 2 → 1 RECEIPT move of 10, unitCost 25.0000, moveValue 250.00, sourceDocNo GR-7. 3 → on-hand 10.
Test data    : source document type PURCHASE_RECEIPT, number GR-7
<!-- TC:TC-INV-086:END -->

<!-- TC:TC-INV-087:START traces=AC-INV-057,REQ-INV-052,API-INV-018,API-INV-016 -->
### TC-INV-087 — A POS sale issues at the current average cost
Derived from : AC-INV-057  (REQ-INV-052)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018, API-INV-016
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V on-hand 10 in W, averageUnitCost 25.0000
Host data    : none
Steps        : 1. SLS submits POS sale P-31 issuing 4 of V in W. 2. GET moves with docNo P-31. 3. GET the balance.
Expected     : 2 → 1 ISSUE move of 4, unitCost 25.0000, moveValue 100.00. 3 → on-hand 6.
Test data    : source document type POS_SALE, number P-31
<!-- TC:TC-INV-087:END -->

<!-- TC:TC-INV-088:START traces=AC-INV-059,REQ-INV-053,API-INV-018 -->
### TC-INV-088 — A bundle is issued through its components
Derived from : AC-INV-059  (REQ-INV-053)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: bundles on; Bundle B made of 2 × C1 and 1 × C2; stock of C1 and C2 in W
Host data    : none
Steps        : 1. SLS issues 3 of B from W. 2. GET the moves of the source document.
Expected     : 2 ISSUE moves: C1 quantity 6 and C2 quantity 3, each with bundleVariantPk B; no move for B itself.
Test data    : source document type SALES_INVOICE
<!-- TC:TC-INV-088:END -->

<!-- TC:TC-INV-089:START traces=AC-INV-062,REQ-INV-056,API-INV-018,API-INV-019 -->
### TC-INV-089 — The moves of a posted transfer carry its document
Derived from : AC-INV-062  (REQ-INV-056)
Exercises    : API-INV-018 GET /api/v1/inv/stock-moves · API-INV-019 GET /api/v1/inv/stock-moves/{id}
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a posted Transfer TR-0005 with transferPk T
Host data    : none
Steps        : 1. GET moves with sourceDocTypeCode INV_TRANSFER and sourceDocRef T. 2. GET each move by id.
Expected     : each move has sourceDocTypeCode INV_TRANSFER, sourceDocRef T and sourceDocNo TR-0005.
Test data    : TR-0005
<!-- TC:TC-INV-089:END -->

<!-- TC:TC-INV-090:START traces=AC-INV-063,REQ-INV-057 -->
### TC-INV-090 — A committed issue publishes one stock-move event
Derived from : AC-INV-063  (REQ-INV-057)
Exercises    : InventoryApi.issueStock — CON-INV-015 · event inv.StockMoveRecorded (CON-INV-020)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V with stock in W and averageUnitCost 25.0000; an event consumer capturing deliveries
Host data    : none
Steps        : 1. Issue 4 of V and let the transaction commit. 2. Read the delivered events. 3. Issue 4 of V again with the transaction forced to roll back.
Expected     : 2 → exactly 1 event with quantity 4, unitCost 25.0000, value 100.00, delivered after commit. 3 → no event delivered.
Test data    : —
<!-- TC:TC-INV-090:END -->

<!-- TC:TC-INV-091:START traces=AC-INV-064,REQ-INV-058,API-INV-018 -->
### TC-INV-091 — A repeated submission with the same key is recorded once
Derived from : AC-INV-064  (REQ-INV-058)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : RULE-INV-024 → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: a POS sale already synchronised with idempotency key K1, which recorded 1 ISSUE of 2
Host data    : none
Steps        : 1. SLS submits the same sale again with key K1. 2. GET the moves of the sale.
Expected     : 1 → the answer equals the first answer (it may carry «سبق تسجيل هذا الطلب؛ أُعيدت النتيجة الأولى.» / "This request was already recorded; the first result is returned."). 2 → still 1 move with key K1.
Test data    : idempotency key K1
<!-- TC:TC-INV-091:END -->

<!-- TC:TC-INV-092:START traces=AC-INV-065,REQ-INV-059,API-INV-051 -->
### TC-INV-092 — A receipt recalculates the weighted-average cost across the tenant
Derived from : AC-INV-065  (REQ-INV-059)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-051 GET /api/v1/inv/stock-valuation/{variantId}
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V total quantity 10 at average 20.0000 (W1 6, W2 4)
Host data    : none
Steps        : 1. PUR records a RECEIPT of 10 of V at unit cost 26.0000 in W1. 2. GET the valuation of V.
Expected     : totalOnHandQty 20, averageUnitCost 23.0000, stockValue 460.00.
Test data    : —
<!-- TC:TC-INV-092:END -->

<!-- TC:TC-INV-093:START traces=AC-INV-066,REQ-INV-060,API-INV-051 -->
### TC-INV-093 — An incoming cost restarts the average when total stock is negative
Derived from : AC-INV-066  (REQ-INV-060)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-051
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Variant V with totalOnHandQty −3 and averageUnitCost 18.0000
Host data    : none
Steps        : 1. Record a RECEIPT of 10 of V at unit cost 22.0000. 2. GET the valuation of V.
Expected     : totalOnHandQty 7, averageUnitCost 22.0000.
Test data    : —
<!-- TC:TC-INV-093:END -->

<!-- TC:TC-INV-094:START traces=AC-INV-067,REQ-INV-061,API-INV-036,API-INV-038,API-INV-018,API-INV-051 -->
### TC-INV-094 — An adjustment out is valued at the average and keeps it
Derived from : AC-INV-067  (REQ-INV-061)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · API-INV-038 PATCH …/{id}/post · read back API-INV-018, API-INV-051
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; Variant V with averageUnitCost 23.0000 and at least 2 on hand in W
Host data    : none
Steps        : 1. POST an adjustment with 1 line of V −2. 2. PATCH it /post. 3. GET its moves. 4. GET the valuation of V.
Expected     : 3 → 1 ADJUSTMENT_OUT of 2 with unitCost 23.0000 and moveValue 46.00. 4 → averageUnitCost 23.0000.
Test data    : reasonCode: the AC names none — DAMAGE (seeded) is used
<!-- TC:TC-INV-094:END -->

<!-- TC:TC-INV-095:START traces=AC-INV-068,REQ-INV-062,API-INV-024,API-INV-051 -->
### TC-INV-095 — A posted transfer leaves the valuation unchanged
Derived from : AC-INV-068  (REQ-INV-062)
Exercises    : API-INV-024 PATCH /api/v1/inv/transfers/{id}/post · read back API-INV-051
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V totalOnHandQty 20, averageUnitCost 23.0000, stockValue 460.00, with ≥ 5 on hand in W1; a DRAFT Transfer W1 → W2 of 5 of V
Host data    : none
Steps        : 1. PATCH the transfer /post with its version. 2. GET the valuation of V.
Expected     : totalOnHandQty 20, averageUnitCost 23.0000, stockValue 460.00.
Test data    : —
<!-- TC:TC-INV-095:END -->

<!-- TC:TC-INV-096:START traces=AC-INV-069,REQ-INV-063 -->
### TC-INV-096 — Another module reads the valuation of a Variant
Derived from : AC-INV-069  (REQ-INV-063)
Exercises    : InventoryApi.readValuation — CON-INV-016 (in-process, caller SLS)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V with totalOnHandQty 20 and averageUnitCost 23.0000
Host data    : none
Steps        : 1. SLS calls readValuation for V.
Expected     : totalOnHandQty 20, averageUnitCost 23.0000, stockValue 460.00.
Test data    : —
<!-- TC:TC-INV-096:END -->

<!-- TC:TC-INV-097:START traces=AC-INV-072,REQ-INV-066,API-INV-048,API-INV-016 -->
### TC-INV-097 — A tenant that never saved the setting may go below zero
Derived from : AC-INV-072  (REQ-INV-066)
Exercises    : API-INV-048 GET /api/v1/inv/settings · InventoryApi.issueStock — CON-INV-015 · read back API-INV-016
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: a new tenant that never saved INV_ALLOW_NEGATIVE_STOCK; Variant V on-hand 0 in W
Host data    : none
Steps        : 1. GET settings. 2. SLS submits an online issue of 2 of V in W. 3. GET the balance.
Expected     : 1 → allowNegativeStockFl true. 2 → 1 ISSUE move of 2. 3 → on-hand −2.
Test data    : —
<!-- TC:TC-INV-097:END -->

<!-- TC:TC-INV-098:START traces=AC-INV-074,REQ-INV-068 -->
### TC-INV-098 — Crossing below zero notifies each Warehouse-role user once
Derived from : AC-INV-074  (REQ-INV-068)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process) · notification INV_NEGATIVE_STOCK (NotificationDispatchApi)
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: negative stock allowed; Variant V on-hand 1 in W; 2 users holding the Warehouse role
Host data    : none
Steps        : 1. Issue 3 of V in W. 2. Issue 1 of V in W. 3. Read each user's notifications.
Expected     : each of the 2 users received exactly 1 notification «رصيد سالب: {Variant} في {المستودع} = −2.» / "Negative stock: {Variant} in {Warehouse} = −2."; the second issue sent none.
Test data    : {Variant}, {Warehouse} = V and W labels
<!-- TC:TC-INV-098:END -->

<!-- TC:TC-INV-099:START traces=AC-INV-076,REQ-INV-070,API-INV-047,API-INV-018,API-INV-051 -->
### TC-INV-099 — Opening stock posts an OPENING_STOCK adjustment and sets the first average
Derived from : AC-INV-076  (REQ-INV-070)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-018, API-INV-051
Rule / code  : RULE-INV-026 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; one active Warehouse; Variant V with no stock and no valuation
Host data    : none
Steps        : 1. POST opening stock with 1 row: V, quantity 40, unit cost 12.5000. 2. GET moves of the returned docNo. 3. GET the valuation of V.
Expected     : 1 → 201, a POSTED Stock adjustment with reasonCode OPENING_STOCK. 2 → 1 ADJUSTMENT_IN of 40 at 12.5000. 3 → averageUnitCost 12.5000, stockValue 500.00.
Test data    : row 1 = (V, 40, 12.5000)
<!-- TC:TC-INV-099:END -->

<!-- TC:TC-INV-100:START traces=AC-INV-077,REQ-INV-071,API-INV-047,API-INV-035 -->
### TC-INV-100 — An opening stock load of 650 rows posts as one adjustment
Derived from : AC-INV-077  (REQ-INV-071)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-035
Rule / code  : RULE-INV-027 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; one active Warehouse; 650 active Variants of STANDARD Items
Host data    : none
Steps        : 1. POST opening stock with 650 valid rows read from the opening stock sheet. 2. GET the returned adjustment. 3. GET its moves.
Expected     : 2 → 1 POSTED Stock adjustment with 650 lines. 3 → 650 ADJUSTMENT_IN moves.
Test data    : 650 rows, each quantity > 0 and unit cost ≥ 0 (placeholders)
<!-- TC:TC-INV-100:END -->

<!-- TC:TC-INV-101:START traces=AC-INV-080,REQ-INV-073,API-INV-046 -->
### TC-INV-101 — The opening stock sheet has 3 columns while lot and measure are off
Derived from : AC-INV-080  (REQ-INV-073)
Exercises    : API-INV-046 GET /api/v1/inv/opening-stock/sheet
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; lotExpiry off and dualQuantity off
Host data    : none
Steps        : 1. GET the sheet.
Expected     : 200, an xlsx file with exactly 3 columns «الباركود» / "Barcode", «الكمية» / "Quantity", «تكلفة الوحدة» / "Unit cost"; no lot, expiry or measure column.
Test data    : —
<!-- TC:TC-INV-101:END -->

<!-- TC:TC-INV-102:START traces=AC-INV-082,REQ-INV-075,API-INV-018 -->
### TC-INV-102 — Move search returns the moves of a period newest first with their documents
Derived from : AC-INV-082  (REQ-INV-075)
Exercises    : API-INV-018 GET /api/v1/inv/stock-moves
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; Variant V with 3 moves in W during October 2026: RECEIPT GR-7, ISSUE P-31, ADJUSTMENT_OUT ADJ-0002
Host data    : none
Steps        : 1. GET moves with variantPk V, warehousePk W, dateFrom 2026-10-01, dateTo 2026-10-31.
Expected     : 3 rows newest first, document numbers ADJ-0002, P-31, GR-7, each with its sourceDocTypeCode.
Test data    : —
<!-- TC:TC-INV-102:END -->

<!-- TC:TC-INV-103:START traces=AC-INV-083,REQ-INV-076,API-INV-018 -->
### TC-INV-103 — Moves filtered to one Variant and one warehouse carry the running balance
Derived from : AC-INV-083  (REQ-INV-076)
Exercises    : API-INV-018 GET /api/v1/inv/stock-moves
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; moves of V in W in this order: RECEIPT 10, ISSUE 4, ADJUSTMENT_OUT 1 (no earlier stock)
Host data    : none
Steps        : 1. GET moves with variantPk V and warehousePk W.
Expected     : balanceAfterQty 10 on the RECEIPT, 6 on the ISSUE, 5 on the ADJUSTMENT_OUT.
Test data    : —
<!-- TC:TC-INV-103:END -->

<!-- TC:TC-INV-104:START traces=AC-INV-084,REQ-INV-077,API-INV-037 -->
### TC-INV-104 — Changing a draft adjustment line writes one audit entry
Derived from : AC-INV-084  (REQ-INV-077)
Exercises    : API-INV-037 PUT /api/v1/inv/stock-adjustments/{id} · change history (AuditApi)
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; a DRAFT Stock adjustment A with a line whose quantityChange is 2
Host data    : none
Steps        : 1. PUT A with that line's quantityChange 3 and its version. 2. Read the change history of A.
Expected     : exactly 1 audit entry for A with the user, the time and quantityChange 2 → 3.
Test data    : —
<!-- TC:TC-INV-104:END -->

<!-- TC:TC-INV-105:START traces=AC-INV-085,REQ-INV-078,API-INV-022,API-INV-021,API-INV-018 -->
### TC-INV-105 — Saving a new transfer creates a numbered DRAFT and no move
Derived from : AC-INV-085  (REQ-INV-078)
Exercises    : API-INV-022 POST /api/v1/inv/transfers · API-INV-021 · API-INV-018
Rule / code  : RULE-INV-030 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; 2 active Warehouses W1, W2; Variant V
Host data    : none
Steps        : 1. POST a transfer W1 → W2 with 1 line of 5 of V. 2. GET it. 3. GET moves with its docNo.
Expected     : 1 → 201, statusCode DRAFT, docNo issued by series INV_TRANSFER (prefix TR-). 2 → 1 line of 5 of V. 3 → no move.
Test data    : —
<!-- TC:TC-INV-105:END -->

<!-- TC:TC-INV-106:START traces=AC-INV-086,REQ-INV-079,API-INV-023,API-INV-021 -->
### TC-INV-106 — Saving a draft transfer replaces its lines
Derived from : AC-INV-086  (REQ-INV-079)
Exercises    : API-INV-023 PUT /api/v1/inv/transfers/{id} · API-INV-021
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a DRAFT Transfer with 1 line of 5 of V, read with its version
Host data    : none
Steps        : 1. PUT the transfer with the line changed to 7 (same transferLinePk) and a new line of 2 of V2. 2. GET it.
Expected     : 2 lines: 7 of V and 2 of V2.
Test data    : —
<!-- TC:TC-INV-106:END -->

<!-- TC:TC-INV-107:START traces=AC-INV-087,REQ-INV-080,API-INV-024,API-INV-018,API-INV-016 -->
### TC-INV-107 — Posting a transfer moves the stock out and in at the average cost
Derived from : AC-INV-087  (REQ-INV-080)
Exercises    : API-INV-024 PATCH /api/v1/inv/transfers/{id}/post · API-INV-018 · API-INV-016
Rule / code  : RULE-INV-016 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: V on-hand 10 in W1 and 0 in W2, averageUnitCost 23.0000; a DRAFT Transfer W1 → W2 of 4 of V
Host data    : none
Steps        : 1. PATCH the transfer /post with its version. 2. GET its moves. 3. GET balances of V.
Expected     : 1 → statusCode POSTED. 2 → TRANSFER_OUT of 4 at 23.0000 in W1 and TRANSFER_IN of 4 at 23.0000 in W2. 3 → on-hand 6 in W1 and 4 in W2.
Test data    : —
<!-- TC:TC-INV-107:END -->

<!-- TC:TC-INV-108:START traces=AC-INV-089,REQ-INV-081,API-INV-025,API-INV-018 -->
### TC-INV-108 — Cancelling a draft transfer records no move
Derived from : AC-INV-089  (REQ-INV-081)
Exercises    : API-INV-025 DELETE /api/v1/inv/transfers/{id} · API-INV-018
Rule / code  : RULE-INV-029 → —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: a DRAFT Transfer with lines
Host data    : none
Steps        : 1. DELETE the transfer. 2. GET moves with its docNo.
Expected     : 1 → statusCode CANCELLED. 2 → 0 moves with its sourceDocRef.
Test data    : —
<!-- TC:TC-INV-108:END -->

<!-- TC:TC-INV-109:START traces=AC-INV-092,REQ-INV-084,API-INV-024,API-INV-016,API-INV-041 -->
### TC-INV-109 — A transferred lot keeps its expiry in the destination warehouse
Derived from : AC-INV-092  (REQ-INV-084)
Exercises    : API-INV-024 PATCH /api/v1/inv/transfers/{id}/post · API-INV-016 · API-INV-041 GET /api/v1/inv/lots/{id}
Rule / code  : RULE-INV-037 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: lotExpiry on; lot-tracked V with Lot L1 (expiry 2027-03-01) on-hand 8 in W1; a DRAFT Transfer W1 → W2 of 3 of V in L1
Host data    : none
Steps        : 1. Post the transfer. 2. GET balances of V with lotPk L1. 3. GET lot L1.
Expected     : 2 → the W2 row for L1 shows on-hand 3. 3 → expiryDate 2027-03-01.
Test data    : —
<!-- TC:TC-INV-109:END -->

<!-- TC:TC-INV-110:START traces=AC-INV-095,REQ-INV-086,API-INV-020 -->
### TC-INV-110 — Transfer search by status returns the posted ones newest first
Derived from : AC-INV-095  (REQ-INV-086)
Exercises    : API-INV-020 GET /api/v1/inv/transfers
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: 3 Transfers, 2 POSTED and 1 DRAFT
Host data    : none
Steps        : 1. GET transfers with statusCode POSTED.
Expected     : the 2 POSTED Transfers, newest first.
Test data    : —
<!-- TC:TC-INV-110:END -->

<!-- TC:TC-INV-111:START traces=AC-INV-096,REQ-INV-087,API-INV-028 -->
### TC-INV-111 — Starting a count with one warehouse creates a numbered DRAFT in it
Derived from : AC-INV-096  (REQ-INV-087)
Exercises    : API-INV-028 POST /api/v1/inv/stock-counts
Rule / code  : RULE-INV-030 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; a tenant with 1 active Warehouse W
Host data    : none
Steps        : 1. POST a stock count with warehousePk null.
Expected     : 201, statusCode DRAFT, warehousePk W, docNo issued by series INV_COUNT (prefix CNT-).
Test data    : —
<!-- TC:TC-INV-111:END -->

<!-- TC:TC-INV-112:START traces=AC-INV-097,REQ-INV-088,API-INV-029,API-INV-027 -->
### TC-INV-112 — Two scans of the same barcode add up on one line
Derived from : AC-INV-097  (REQ-INV-088)
Exercises    : API-INV-029 POST /api/v1/inv/stock-counts/{id}/lines · API-INV-027
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a DRAFT Stock count C with no line for V; barcode 6221000000017 identifies V in CAT
Host data    : none
Steps        : 1. POST a line with barcode 6221000000017 and countedQty 1. 2. POST again with countedQty 2. 3. GET count C.
Expected     : 1 line for V with countedQty 3.
Test data    : barcode 6221000000017
<!-- TC:TC-INV-112:END -->

<!-- TC:TC-INV-113:START traces=AC-INV-099,REQ-INV-089,API-INV-030,API-INV-031,API-INV-027 -->
### TC-INV-113 — A counted line is changed and another removed on a draft count
Derived from : AC-INV-099  (REQ-INV-089)
Exercises    : API-INV-030 PUT …/stock-counts/{id}/lines/{lineId} · API-INV-031 DELETE …/lines/{lineId} · API-INV-027
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a DRAFT count with a line of V countedQty 3 and a line of V2
Host data    : none
Steps        : 1. PUT the V line with countedQty 5 and its version. 2. DELETE the V2 line. 3. GET the count.
Expected     : the V line has countedQty 5; there is no line of V2.
Test data    : —
<!-- TC:TC-INV-113:END -->

<!-- TC:TC-INV-114:START traces=AC-INV-102,REQ-INV-092,API-INV-032,API-INV-035,API-INV-016 -->
### TC-INV-114 — Posting a count records differences and posts a COUNT_DIFFERENCE adjustment
Derived from : AC-INV-102  (REQ-INV-092)
Exercises    : API-INV-032 PATCH /api/v1/inv/stock-counts/{id}/post · API-INV-035 · API-INV-016
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: V on-hand 10 and V2 on-hand 4 in W (both valued); a DRAFT count in W with V countedQty 8 and V2 countedQty 4
Host data    : none
Steps        : 1. PATCH the count /post with its version. 2. GET the returned adjustment. 3. GET the balance of V in W.
Expected     : 1 → count POSTED; V systemQty 10, differenceQty −2; V2 differenceQty 0. 2 → a POSTED adjustment with reasonCode COUNT_DIFFERENCE and 1 line (V, −2). 3 → on-hand 8.
Test data    : —
<!-- TC:TC-INV-114:END -->

<!-- TC:TC-INV-115:START traces=AC-INV-104,REQ-INV-094,API-INV-033,API-INV-027,API-INV-018 -->
### TC-INV-115 — Cancelling a draft count changes no stock
Derived from : AC-INV-104  (REQ-INV-094)
Exercises    : API-INV-033 DELETE /api/v1/inv/stock-counts/{id} · API-INV-027 · API-INV-018
Rule / code  : RULE-INV-029 → —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: a DRAFT count with 3 lines
Host data    : none
Steps        : 1. DELETE the count. 2. GET it.
Expected     : 1 → statusCode CANCELLED. 2 → statusCode CANCELLED and adjustmentPk null; GET moves with sourceDocTypeCode INV_COUNT and sourceDocRef of the count answers 0 rows (AC-INV-104).
Test data    : —
<!-- TC:TC-INV-115:END -->

<!-- TC:TC-INV-116:START traces=AC-INV-105,REQ-INV-095,API-INV-026 -->
### TC-INV-116 — Count search by status returns the drafts newest first
Derived from : AC-INV-105  (REQ-INV-095)
Exercises    : API-INV-026 GET /api/v1/inv/stock-counts
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: 2 DRAFT counts and 1 POSTED count
Host data    : none
Steps        : 1. GET counts with statusCode DRAFT.
Expected     : the 2 DRAFT counts, newest first.
Test data    : —
<!-- TC:TC-INV-116:END -->

<!-- TC:TC-INV-117:START traces=AC-INV-107,REQ-INV-097,API-INV-036,API-INV-035,API-INV-018 -->
### TC-INV-117 — Saving a new adjustment creates a numbered DRAFT and no move
Derived from : AC-INV-107  (REQ-INV-097)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · API-INV-035 · API-INV-018
Rule / code  : RULE-INV-030 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; an active Warehouse W; Variant V
Host data    : none
Steps        : 1. POST an adjustment with reasonCode DAMAGE and 1 line of V −2. 2. GET it. 3. GET moves with its docNo.
Expected     : 1 → 201, statusCode DRAFT, docNo issued by series INV_ADJUSTMENT (prefix ADJ-). 3 → no move.
Test data    : reasonCode DAMAGE
<!-- TC:TC-INV-117:END -->

<!-- TC:TC-INV-118:START traces=AC-INV-108,REQ-INV-098,API-INV-037,API-INV-035 -->
### TC-INV-118 — Saving a draft adjustment replaces its line
Derived from : AC-INV-108  (REQ-INV-098)
Exercises    : API-INV-037 PUT /api/v1/inv/stock-adjustments/{id} · API-INV-035
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a DRAFT adjustment with reasonCode DAMAGE and 1 line of V −2, read with its version
Host data    : none
Steps        : 1. PUT it with the line changed to −3. 2. GET it.
Expected     : 1 line of V −3.
Test data    : —
<!-- TC:TC-INV-118:END -->

<!-- TC:TC-INV-119:START traces=AC-INV-110,REQ-INV-099,API-INV-038,API-INV-018,API-INV-016 -->
### TC-INV-119 — Posting an adjustment records an adjustment-out at the average
Derived from : AC-INV-110  (REQ-INV-099)
Exercises    : API-INV-038 PATCH /api/v1/inv/stock-adjustments/{id}/post · API-INV-018 · API-INV-016
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: V on-hand 10 in W at averageUnitCost 23.0000; a DRAFT adjustment with reasonCode DAMAGE and 1 line of V −2
Host data    : none
Steps        : 1. PATCH it /post with its version. 2. GET its moves. 3. GET the balance of V in W.
Expected     : 1 → POSTED. 2 → 1 ADJUSTMENT_OUT of 2 at 23.0000. 3 → on-hand 8.
Test data    : —
<!-- TC:TC-INV-119:END -->

<!-- TC:TC-INV-120:START traces=AC-INV-112,REQ-INV-100,API-INV-039,API-INV-018 -->
### TC-INV-120 — Cancelling a draft adjustment records no move
Derived from : AC-INV-112  (REQ-INV-100)
Exercises    : API-INV-039 DELETE /api/v1/inv/stock-adjustments/{id} · API-INV-018
Rule / code  : RULE-INV-029 → —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: a DRAFT adjustment with lines
Host data    : none
Steps        : 1. DELETE the adjustment. 2. GET moves with its docNo.
Expected     : 1 → statusCode CANCELLED. 2 → 0 moves with its sourceDocRef.
Test data    : —
<!-- TC:TC-INV-120:END -->

<!-- TC:TC-INV-121:START traces=AC-INV-114,REQ-INV-102,API-INV-038,API-INV-018,API-INV-051 -->
### TC-INV-121 — An increase line without cost enters at the average
Derived from : AC-INV-114  (REQ-INV-102)
Exercises    : API-INV-038 PATCH /api/v1/inv/stock-adjustments/{id}/post · API-INV-018 · API-INV-051
Rule / code  : RULE-INV-025 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: V with averageUnitCost 23.0000; a DRAFT adjustment with reasonCode COUNT_DIFFERENCE and 1 line of V +2 with no unit cost
Host data    : none
Steps        : 1. PATCH it /post with its version. 2. GET its moves. 3. GET the valuation of V.
Expected     : 2 → 1 ADJUSTMENT_IN of 2 at 23.0000 with value 46.00. 3 → averageUnitCost 23.0000.
Test data    : —
<!-- TC:TC-INV-121:END -->

<!-- TC:TC-INV-122:START traces=AC-INV-116,REQ-INV-104,API-INV-034 -->
### TC-INV-122 — Adjustment search by reason returns the matching ones newest first
Derived from : AC-INV-116  (REQ-INV-104)
Exercises    : API-INV-034 GET /api/v1/inv/stock-adjustments
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: 2 adjustments with reasonCode DAMAGE and 1 with OPENING_STOCK
Host data    : none
Steps        : 1. GET adjustments with reasonCode DAMAGE.
Expected     : the 2 DAMAGE adjustments, newest first.
Test data    : —
<!-- TC:TC-INV-122:END -->

<!-- TC:TC-INV-123:START traces=AC-INV-117,REQ-INV-105,API-INV-018,API-INV-016 -->
### TC-INV-123 — A receipt of a lot-tracked Item records its lot
Derived from : AC-INV-117  (REQ-INV-105)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · API-INV-018 · API-INV-016
Rule / code  : RULE-INV-035 → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: lotExpiry on; lot-tracked Variant V
Host data    : none
Steps        : 1. PUR submits a receipt line of 10 of V with lot number L-77 and expiry 2027-02-28. 2. GET its move. 3. GET balances of V.
Expected     : 2 → 1 RECEIPT move of 10 with the lot L-77. 3 → the L-77 row shows on-hand 10.
Test data    : lot L-77, expiry 2027-02-28
<!-- TC:TC-INV-123:END -->

<!-- TC:TC-INV-124:START traces=AC-INV-119,REQ-INV-106,API-INV-040,API-INV-016 -->
### TC-INV-124 — A second receipt of the same lot reuses it
Derived from : AC-INV-119  (REQ-INV-106)
Exercises    : InventoryApi.receiveStock — CON-INV-014 · API-INV-040 GET /api/v1/inv/lots · API-INV-016
Rule / code  : RULE-INV-036 → — (UQ_INV_LOT_VAR_CODE)
Package      : DATA-DOM-MASTER
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Variant V with Lot L-77 expiry 2027-02-28 and its L-77 balance recorded
Host data    : none
Steps        : 1. A second receipt of 5 of V names L-77 with expiry 2027-02-28. 2. GET lots of V with code L-77. 3. GET balances of V for L-77.
Expected     : 2 → one Lot L-77 (no new Lot). 3 → the L-77 balance rose by 5.
Test data    : —
<!-- TC:TC-INV-124:END -->

<!-- TC:TC-INV-125:START traces=AC-INV-121,REQ-INV-108,API-INV-042,API-INV-041 -->
### TC-INV-125 — Correcting a lot's expiry is saved and audited
Derived from : AC-INV-121  (REQ-INV-108)
Exercises    : API-INV-042 PATCH /api/v1/inv/lots/{id} · API-INV-041 · change history (AuditApi)
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; Lot L-77 with expiry 2027-02-28, read with its version
Host data    : none
Steps        : 1. PATCH L-77 with expiryDate 2027-02-20 and its version. 2. GET L-77. 3. Read its change history.
Expected     : 2 → expiryDate 2027-02-20. 3 → 1 audit entry recording 2027-02-28 → 2027-02-20.
Test data    : —
<!-- TC:TC-INV-125:END -->

<!-- TC:TC-INV-126:START traces=AC-INV-122,REQ-INV-109,API-INV-040 -->
### TC-INV-126 — Lot search returns the lots ordered by expiry with their on-hand
Derived from : AC-INV-122  (REQ-INV-109)
Exercises    : API-INV-040 GET /api/v1/inv/lots
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; Variant V with L1 (expiry 2027-01-10, on-hand 4) and L2 (expiry 2026-12-01, on-hand 6)
Host data    : none
Steps        : 1. GET lots with variantPk V.
Expected     : 2 rows in the order L2, L1 with onHandQty 6 and 4.
Test data    : —
<!-- TC:TC-INV-126:END -->

<!-- TC:TC-INV-127:START traces=AC-INV-123,REQ-INV-110,API-INV-018 -->
### TC-INV-127 — A sale issues the nearest expiry first
Derived from : AC-INV-123  (REQ-INV-110)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS, sale) · API-INV-018
Rule / code  : RULE-INV-038 → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: lotExpiry on; today 2026-11-01; in W, V has L1 (expiry 2027-01-10, on-hand 4) and L2 (expiry 2026-12-01, on-hand 6)
Host data    : none
Steps        : 1. SLS issues 8 of V from W for a sale. 2. GET the moves of the sale.
Expected     : 2 ISSUE moves: 6 from L2, then 2 from L1.
Test data    : tenant clock 2026-11-01
<!-- TC:TC-INV-127:END -->

<!-- TC:TC-INV-128:START traces=AC-INV-124,REQ-INV-111,API-INV-018,API-INV-016 -->
### TC-INV-128 — A sale the lots cannot cover posts the rest without a lot
Derived from : AC-INV-124  (REQ-INV-111)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS, sale) · API-INV-018 · API-INV-016
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: lotExpiry on; negative stock allowed; in W, V has only L1 on-hand 2
Host data    : none
Steps        : 1. SLS issues 5 of V from W for a sale. 2. GET the moves. 3. GET balances of V in W.
Expected     : 2 → 1 ISSUE of 2 from L1 and 1 ISSUE of 3 with no lot. 3 → the no-lot row shows on-hand −3.
Test data    : —
<!-- TC:TC-INV-128:END -->

<!-- TC:TC-INV-129:START traces=AC-INV-125,REQ-INV-112,API-INV-018 -->
### TC-INV-129 — Stock without a lot is issued before any lot
Derived from : AC-INV-125  (REQ-INV-112)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS, sale) · API-INV-018
Rule / code  : — → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: lotExpiry on; in W, V has 3 on-hand without a lot and L2 on-hand 6
Host data    : none
Steps        : 1. SLS issues 4 of V from W for a sale. 2. GET the moves.
Expected     : 1 ISSUE of 3 with no lot and 1 ISSUE of 1 from L2.
Test data    : —
<!-- TC:TC-INV-129:END -->

<!-- TC:TC-INV-130:START traces=AC-INV-128,REQ-INV-114,API-INV-036,API-INV-038,API-INV-016 -->
### TC-INV-130 — An expired lot is written off by an adjustment
Derived from : AC-INV-128  (REQ-INV-114)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · API-INV-038 PATCH …/{id}/post · API-INV-016
Rule / code  : RULE-INV-039 (adjustment path allowed) → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Warehouse user session; Lot L0 of V expired, on-hand 5 in W
Host data    : none
Steps        : 1. POST an adjustment in W with reasonCode EXPIRY_WRITE_OFF and 1 line of V, lotNo L0, quantityChange −5. 2. PATCH it /post. 3. GET balances of V for L0.
Expected     : 2 → POSTED with 1 ADJUSTMENT_OUT of 5 from L0. 3 → L0 on-hand 0 in W.
Test data    : reasonCode EXPIRY_WRITE_OFF
<!-- TC:TC-INV-130:END -->

<!-- TC:TC-INV-131:START traces=AC-INV-129,REQ-INV-115,API-INV-040 -->
### TC-INV-131 — An expired lot with stock stays listed and marked expired
Derived from : AC-INV-129  (REQ-INV-115)
Exercises    : API-INV-040 GET /api/v1/inv/lots
Rule / code  : RULE-INV-038 → —
Package      : SVC-API-SEARCH
Scenario     : STATE · data class EDGE · language ALL
Preconditions: lotExpiry on; Lot L0 of V expired, on-hand 5 in W
Host data    : none
Steps        : 1. GET lots with nearExpiryOnly true (the near-expiry view).
Expected     : L0 listed with onHandQty 5 and expiredFl true.
Test data    : —
<!-- TC:TC-INV-131:END -->

<!-- TC:TC-INV-132:START traces=AC-INV-130,REQ-INV-116,API-INV-040 -->
### TC-INV-132 — The near-expiry view lists the lots inside the horizon
Derived from : AC-INV-130  (REQ-INV-116)
Exercises    : API-INV-040 GET /api/v1/inv/lots
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: lotExpiry on; INV_NEAR_EXPIRY_DAYS 30; today 2026-11-01; L2 (expiry 2026-11-20, on-hand 6), L3 (expiry 2027-01-15, on-hand 4)
Host data    : none
Steps        : 1. GET lots with nearExpiryOnly true.
Expected     : L2 listed with daysLeft 19; L3 not listed.
Test data    : tenant clock 2026-11-01
<!-- TC:TC-INV-132:END -->

<!-- TC:TC-INV-133:START traces=AC-INV-131,REQ-INV-117,API-INV-041 -->
### TC-INV-133 — The daily check notifies a lot entering the horizon once
Derived from : AC-INV-131  (REQ-INV-117)
Exercises    : daily near-expiry job (InvNearExpiryJob, TenantRunner) · notification INV_NEAR_EXPIRY · read back API-INV-041
Rule / code  : — → —
Package      : CORE
Scenario     : STATE · data class VALID · language ALL
Preconditions: lotExpiry on; INV_NEAR_EXPIRY_DAYS 30; Lot L2 of V expiry 2026-11-20, on-hand 6, nearExpiryNotifiedAt empty; users holding the Warehouse role
Host data    : none
Steps        : 1. Run the daily check on 2026-11-01. 2. Run it again on 2026-11-02. 3. Read each Warehouse-role user's notifications. 4. GET lot L2.
Expected     : 3 → each user received exactly 1 notification «تشغيلة قريبة الانتهاء: {Variant} — L2 تنتهي في 2026-11-20 (الكمية 6).» / "Lot near expiry: {Variant} — L2 expires on 2026-11-20 (quantity 6).". 4 → nearExpiryNotifiedAt set on 2026-11-01.
Test data    : tenant clock 2026-11-01, then 2026-11-02
<!-- TC:TC-INV-133:END -->

<!-- TC:TC-INV-134:START traces=AC-INV-132,REQ-INV-118,API-INV-048,API-INV-049,API-INV-040 -->
### TC-INV-134 — The horizon defaults to 30 and a saved 60 widens the near-expiry list
Derived from : AC-INV-132  (REQ-INV-118)
Exercises    : API-INV-048 GET /api/v1/inv/settings · API-INV-049 PUT /api/v1/inv/settings · API-INV-040
Rule / code  : — → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; lotExpiry on; INV_NEAR_EXPIRY_DAYS never saved; a lot Lx with non-zero on-hand whose expiry is 31–60 days after today
Host data    : none
Steps        : 1. GET settings. 2. GET lots with nearExpiryOnly true. 3. PUT settings with nearExpiryDays 60. 4. GET lots with nearExpiryOnly true.
Expected     : 1 → nearExpiryDays 30. 2 → Lx not listed. 4 → Lx listed.
Test data    : Lx (placeholder lot)
<!-- TC:TC-INV-134:END -->

<!-- TC:TC-INV-135:START traces=AC-INV-134,REQ-INV-120,API-INV-018 -->
### TC-INV-135 — A receipt of a dual-quantity Item records its measure
Derived from : AC-INV-134  (REQ-INV-120)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · API-INV-018
Rule / code  : RULE-INV-040 → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: dualQuantity on; Item "Cotton fabric" dual-quantity with measure unit metre (a CAT unit-of-measure value)
Host data    : none
Steps        : 1. PUR receives 3 rolls measuring 120.500 metres. 2. GET the move.
Expected     : 1 RECEIPT move with quantity 3 and measureQty 120.500.
Test data    : —
<!-- TC:TC-INV-135:END -->

<!-- TC:TC-INV-136:START traces=AC-INV-135,REQ-INV-121,API-INV-016 -->
### TC-INV-136 — A balance of a dual-quantity Item carries its measure and unit
Derived from : AC-INV-135  (REQ-INV-121)
Exercises    : API-INV-016 GET /api/v1/inv/stock-balances
Rule / code  : — → —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: dualQuantity on; "Cotton fabric" on-hand 3 rolls and 120.500 metres in W
Host data    : none
Steps        : 1. GET balances of its Variant.
Expected     : the row shows onHandQty 3, onHandMeasureQty 120.500 and measureUomCode = the metre code.
Test data    : —
<!-- TC:TC-INV-136:END -->

<!-- TC:TC-INV-137:START traces=AC-INV-137,REQ-INV-123,API-INV-038,API-INV-016 -->
### TC-INV-137 — A measure-only adjustment changes the measure and not the count
Derived from : AC-INV-137  (REQ-INV-123)
Exercises    : API-INV-038 PATCH /api/v1/inv/stock-adjustments/{id}/post · API-INV-016
Rule / code  : RULE-INV-034 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: dualQuantity on; "Cotton fabric" on-hand 3 rolls and 120.500 metres in W; a DRAFT adjustment with 1 line quantityChange 0 and measureQtyChange −0.500
Host data    : none
Steps        : 1. PATCH it /post. 2. GET the balance.
Expected     : on-hand stays 3; measure 120.000.
Test data    : reasonCode: the AC names none — DAMAGE (seeded) is used
<!-- TC:TC-INV-137:END -->

<!-- TC:TC-INV-138:START traces=AC-INV-138,REQ-INV-124,API-INV-045,API-INV-043 -->
### TC-INV-138 — Marking an Item as tracked by lot creates its stock settings
Derived from : AC-INV-138  (REQ-INV-124)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · API-INV-043 GET /api/v1/inv/item-stock-settings
Rule / code  : RULE-INV-044 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; lotExpiry on; Item "Milk 1L" with no Item stock settings
Host data    : none
Steps        : 1. PUT its settings with lotTrackedFl true, dualQuantityFl false, no version. 2. GET settings with lotTracked true.
Expected     : a record with lotTrackedFl true, dualQuantityFl false and key equal to the Item's code.
Test data    : —
<!-- TC:TC-INV-138:END -->

<!-- TC:TC-INV-139:START traces=AC-INV-140,REQ-INV-125,API-INV-045 -->
### TC-INV-139 — Marking an Item as dual-quantity saves its measure unit
Derived from : AC-INV-140  (REQ-INV-125)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId}
Rule / code  : RULE-INV-041 → —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Owner session; dualQuantity on; Item "Steel bar" with no Item stock settings; the setting INV_MEASURE_UOM_KEY at its platform default CAT_UOM, and no deployment property set (ADR-INV-043); KG is an active value of CAT_UOM
Host data    : none
Steps        : 1. PUT its settings with dualQuantityFl true, lotTrackedFl false, measureUomCode KG.
Expected     : 200 with dualQuantityFl true and measureUomCode KG.
Test data    : KG (CAT's consumed lookup)
<!-- TC:TC-INV-139:END -->

<!-- TC:TC-INV-140:START traces=AC-INV-145,REQ-INV-129,API-INV-044,API-INV-043 -->
### TC-INV-140 — An Item without stock settings answers false, false and no unit
Derived from : AC-INV-145  (REQ-INV-129)
Exercises    : InventoryApi.readItemStockSettings — CON-INV-018 (in-process, caller PUR) · API-INV-044 GET /api/v1/inv/item-stock-settings/by-item/{itemId} · API-INV-043 GET /api/v1/inv/item-stock-settings
Rule / code  : — → —
Package      : DATA-DOM-LOOKUP
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Item "Rice 5kg" with no Item stock settings
Host data    : none
Steps        : 1. PUR calls readItemStockSettings for the Item. 2. GET the same Item through API-INV-044.
Expected     : both answer exactly 3 values — lotTrackedFl false, dualQuantityFl false and measureUomCode empty — and API-INV-043 with itemPk of "Rice 5kg" answers 0 records afterwards (no record was created by the reads).
Test data    : —
<!-- TC:TC-INV-140:END -->

<!-- TC:TC-INV-141:START traces=AC-INV-146,REQ-INV-130 -->
### TC-INV-141 — The dualQuantity turn-off check allows a turn-off with no dependent Item
Derived from : AC-INV-146  (REQ-INV-130)
Exercises    : InventoryApi.canTurnOff(dualQuantity) — CON-INV-019 (in-process, caller: the flag owner)
Rule / code  : RULE-INV-046 → —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: no Item stock settings with dualQuantityFl true
Host data    : none
Steps        : 1. The flag owner calls canTurnOff for dualQuantity.
Expected     : allowedFl true, dependent count 0, an empty list.
Test data    : —
<!-- TC:TC-INV-141:END -->

<!-- TC:TC-INV-142:START traces=AC-INV-148,REQ-INV-132 -->
### TC-INV-142 — No INV class reads the activity; every flag read goes through FeatureFlags
Derived from : AC-INV-148  (REQ-INV-132)
Exercises    : the architecture rule over package com.commerce.inv (CORE — InvFeatureFlags)
Rule / code  : — → —
Package      : CORE
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the INV source code
Host data    : none
Steps        : 1. Run the architecture test suite.
Expected     : 0 INV classes read the activity or the activity preset; every capability-flag read goes through the FeatureFlags API.
Test data    : —
<!-- TC:TC-INV-142:END -->

<!-- TC:TC-INV-143:START traces=AC-INV-149,REQ-INV-133,API-INV-018 -->
### TC-INV-143 — Changing only the activity keeps the lot requirement of a receipt
Derived from : AC-INV-149  (REQ-INV-133)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · API-INV-018
Rule / code  : RULE-INV-035 → INV-422-LOT-EXPIRY-REQUIRED
Package      : SVC-API-INT
Scenario     : STATE · data class VALID · language ALL
Preconditions: a tenant with activity "Grocery & food", lotExpiry on, lot-tracked Variant V
Host data    : none
Steps        : 1. PUR receives V without a lot number. 2. Change the activity to "Cleaning supplies, feed" with lotExpiry still on. 3. PUR receives V without a lot number. 4. PUR receives V with a lot number and expiry date.
Expected     : 1 and 3 → the same refusal, code INV-422-LOT-EXPIRY-REQUIRED, messageAr «أدخل رقم التشغيلة وتاريخ الصلاحية لهذا الصنف.», messageEn "Enter the lot number and expiry date for this Item.", and 0 Stock moves recorded. 4 → 1 RECEIPT move.
Test data    : lot number and expiry in step 4 are placeholders
<!-- TC:TC-INV-143:END -->

<!-- TC:TC-INV-144:START traces=AC-INV-152,REQ-INV-136,API-INV-016,API-INV-018,API-INV-020,API-INV-026,API-INV-034,API-INV-040,API-INV-046,API-INV-048,API-INV-050 -->
### TC-INV-144 — The Warehouse role preset reaches its pages and not the Owner's
Derived from : AC-INV-152  (REQ-INV-136)
Exercises    : API-INV-016, API-INV-018, API-INV-020, API-INV-026, API-INV-034, API-INV-040 (allowed) · API-INV-046, API-INV-048, API-INV-050 (refused)
Rule / code  : REQ-INV-138 → INV-403
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a newly provisioned tenant with lotExpiry on and 2 active Warehouses; a user holding only the Warehouse role
Host data    : none
Steps        : 1. Call the stock balance, stock move, transfer, count, adjustment and lot searches. 2. Call the opening stock sheet, inventory settings and stock valuation reads.
Expected     : 1 → 200 each. 2 → 403 each, code INV-403, messageAr «ليست لديك صلاحية لهذا الإجراء.», messageEn "You do not have permission for this action.".
Test data    : —
<!-- TC:TC-INV-144:END -->

<!-- TC:TC-INV-145:START traces=AC-INV-153,REQ-INV-137,API-INV-016,API-INV-017,API-INV-018,API-INV-020,API-INV-034,API-INV-036 -->
### TC-INV-145 — The Seller/Cashier preset reaches stock balances only
Derived from : AC-INV-153  (REQ-INV-137)
Exercises    : API-INV-016, API-INV-017 (allowed) · API-INV-018, API-INV-020, API-INV-034, API-INV-036 (refused)
Rule / code  : REQ-INV-138 → INV-403
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a newly provisioned tenant; a user holding only the Seller/Cashier role
Host data    : none
Steps        : 1. GET stock balances and the availability of a Variant. 2. Call the move, transfer and adjustment searches and the adjustment create.
Expected     : 1 → 200. 2 → 403 each, code INV-403, messageAr «ليست لديك صلاحية لهذا الإجراء.», messageEn "You do not have permission for this action.".
Test data    : —
<!-- TC:TC-INV-145:END -->

<!-- TC:TC-INV-146:START traces=AC-INV-154,REQ-INV-138,API-INV-036,API-INV-034 -->
### TC-INV-146 — A Seller/Cashier submitting a stock adjustment is refused
Derived from : AC-INV-154  (REQ-INV-138)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · read back API-INV-034 (Owner session)
Rule / code  : REQ-INV-138 → INV-403
Package      : SEC-BE
Scenario     : PERMISSION · data class ATTACK · language ALL
Preconditions: a user holding only the Seller/Cashier role; an Owner session for the read-back
Host data    : none
Steps        : 1. As the Seller/Cashier, POST a valid stock adjustment. 2. As the Owner, GET adjustments.
Expected     : 1 → 403, code INV-403, messageAr «ليست لديك صلاحية لهذا الإجراء.», messageEn "You do not have permission for this action.". 2 → nothing saved.
Test data    : reasonCode DAMAGE, 1 line (placeholder Variant)
<!-- TC:TC-INV-146:END -->
<!-- SUB:API-SCENARIOS:END -->

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

<!-- SUB:REVISE2-SCENARIOS:START traces=AC-INV-005,AC-INV-006,AC-INV-020,AC-INV-021,AC-INV-027,AC-INV-032,AC-INV-036,AC-INV-056,AC-INV-064,AC-INV-073,AC-INV-081,AC-INV-085,AC-INV-086,AC-INV-087,AC-INV-102,AC-INV-113,AC-INV-121,AC-INV-126,AC-INV-131,AC-INV-136,AC-INV-154,REQ-INV-004,REQ-INV-005,REQ-INV-017,REQ-INV-018,REQ-INV-023,REQ-INV-027,REQ-INV-031,REQ-INV-051,REQ-INV-058,REQ-INV-067,REQ-INV-074,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-092,REQ-INV-101,REQ-INV-108,REQ-INV-113,REQ-INV-117,REQ-INV-122,REQ-INV-138 -->
### REVISE2-SCENARIOS — catalogue coverage, line refusals and guards (gate-analysis revise 2)

These cases close the catalogue rule of this plan (every HTTP catalogue row and every §5.1 row is asserted by at
least one TC) and the gate-analysis revise 2 findings. Every INV code is asserted with its ar / en text — the
module-specific PLATFORM-STD codes carry INV's own text since gate-analysis revise 3 (ADR-INV-021 amended); only the
generic platform codes INV-400, INV-401, INV-500 and INV-409-STALE-VERSION are asserted by code and status, because
their texts are the platform's.

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
Expected     : 1 … 6 → 409 each, code INV-409-STALE-VERSION (platform text — ADR-INV-021). 7 → each record shows only the other session's rename; W2 and B2 are still inactive; the default warehouse and branch are unchanged.
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
Expected     : 1 … 6 → 409 each, code INV-409-STALE-VERSION. 7 → T, C and A are DRAFT and show only the other session's change; no move recorded.
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
Expected     : 1 and 2 → 409 each, code INV-409-STALE-VERSION. 3 → L-77 keeps its expiry; both show only the other session's change.
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
Expected     : 1 → 400, code INV-400. 2 → 401, code INV-401. 3 → 500, code INV-500; no stack trace or SQL in the body. Each body carries messageAr and messageEn (platform texts — ADR-INV-021).
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
<!-- PHASE:TEST-PLAN-BE:END -->

<!-- PHASE:INT-XM:START traces=REQ-INV-035,REQ-INV-039,REQ-INV-040,REQ-INV-042,REQ-INV-043,REQ-INV-049,REQ-INV-050,REQ-INV-051,REQ-INV-052,REQ-INV-053,REQ-INV-054,REQ-INV-059,REQ-INV-063,REQ-INV-070,REQ-INV-072,REQ-INV-075,REQ-INV-078,REQ-INV-080,REQ-INV-088,REQ-INV-091,REQ-INV-092,REQ-INV-097,REQ-INV-106,REQ-INV-109,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-129,XM-INV-001,XM-INV-002,XM-INV-003,XM-INV-004,XM-INV-005,XM-INV-006,XM-INV-007,XM-INV-008,XM-INV-009,XM-INV-010,XM-INV-011,XM-INV-012,XM-INV-013,XM-INV-014,XM-INV-015 -->
## INT-XM — integration test cases (this module's edges)

Source: the 15 `XM-*` blocks of INT-C in the backend plan (cross-checked against registry-db: 11 HARD-FK, 4
SOFT-READ, all to CAT). Each block's `requires` is CAT:DELIVERED. A HARD-FK yields an EXISTS case and a
MISSING case. In both, the block's migration has run, so its constraint exists, and its pre-check query (orphan
count) returns 0. A SOFT-READ yields one GRACEFUL-DEGRADATION case. Each case exercises INV's own surface; the
CAT entity is cited by id only. When `requires` is not met, the block's `if_not_met` path applies (skip-block,
recorded in execution-state.json → deferred_xm) and its cases are not run.

<!-- SUB:CAT:START traces=REQ-INV-035,REQ-INV-039,REQ-INV-040,REQ-INV-042,REQ-INV-043,REQ-INV-049,REQ-INV-050,REQ-INV-051,REQ-INV-052,REQ-INV-053,REQ-INV-054,REQ-INV-059,REQ-INV-063,REQ-INV-070,REQ-INV-072,REQ-INV-075,REQ-INV-078,REQ-INV-080,REQ-INV-088,REQ-INV-091,REQ-INV-092,REQ-INV-097,REQ-INV-106,REQ-INV-109,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-129,XM-INV-001,XM-INV-002,XM-INV-003,XM-INV-004,XM-INV-005,XM-INV-006,XM-INV-007,XM-INV-008,XM-INV-009,XM-INV-010,XM-INV-011,XM-INV-012,XM-INV-013,XM-INV-014,XM-INV-015 -->
### CAT — edges to the catalogue module (ENT-CAT-001, ENT-CAT-002, ENT-CAT-007, ENT-CAT-009)

<!-- TC:TC-INV-147:START traces=XM-INV-001,REQ-INV-035,REQ-INV-039,REQ-INV-040,API-INV-047,API-INV-016 -->
### TC-INV-147 — Stock balance Variant key: an existing Variant gets a balance row
Derived from : XM-INV-001  (REQ-INV-035)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-016 GET /api/v1/inv/stock-balances
Rule / code  : — → —
Package      : XM-INV-001
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-001 applied (FK_INV_STOCK_BALANCE_CAT_VARIANT present); the referenced CAT Variant V (ENT-CAT-002) is present and active
Host data    : none
Steps        : 1. POST opening stock with 1 row of V. 2. GET balances of V. 3. Run the block's pre-check query.
Expected     : 1 → 201. 2 → a balance row with variantPk V. 3 → 0 orphan rows.
Test data    : row (V, quantity > 0, unit cost ≥ 0) — placeholders
<!-- TC:TC-INV-147:END -->

<!-- TC:TC-INV-148:START traces=XM-INV-001,REQ-INV-035,REQ-INV-039,REQ-INV-040,API-INV-047,API-INV-016 -->
### TC-INV-148 — Stock balance Variant key: a missing Variant gets no balance row
Derived from : XM-INV-001  (REQ-INV-035)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-016
Rule / code  : RULE-INV-028 → INV-422-LOAD-ROWS-INVALID (detail INV-422-LOAD-ROW-VARIANT)
Package      : XM-INV-001
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-001 applied; no CAT Variant has identifier X
Host data    : none
Steps        : 1. POST opening stock with 1 row, variantPk X. 2. GET balances with variantPk X. 3. Run the pre-check query.
Expected     : 1 → 422, code INV-422-LOAD-ROWS-INVALID, messageAr «بعض أسطر التحميل غير صالحة؛ راجع الأسطر المذكورة.», messageEn "Some rows of the load are invalid; see the listed rows."; `details` rowNo 1, code INV-422-LOAD-ROW-VARIANT, messageAr «لا يطابق هذا السطر متغيّر صنف نشطًا.», messageEn "This row matches no active Variant.". 2 → no row. 3 → 0 orphan rows.
Test data    : X = an identifier absent from CAT (placeholder)
<!-- TC:TC-INV-148:END -->

<!-- TC:TC-INV-149:START traces=XM-INV-002,REQ-INV-051,REQ-INV-052,REQ-INV-075,API-INV-018 -->
### TC-INV-149 — Stock move Variant key: a receipt of an existing Variant records its move
Derived from : XM-INV-002  (REQ-INV-051)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-018
Rule / code  : — → —
Package      : XM-INV-002
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-002 applied (FK_INV_STOCK_MOVE_CAT_VARIANT_1); CAT Variant V present and active
Host data    : none
Steps        : 1. PUR receives 1 line of V at a unit cost. 2. GET moves of V. 3. Run the pre-check query.
Expected     : 2 → 1 RECEIPT move with variantPk V. 3 → 0 orphan rows.
Test data    : source document type PURCHASE_RECEIPT
<!-- TC:TC-INV-149:END -->

<!-- TC:TC-INV-150:START traces=XM-INV-002,REQ-INV-051,REQ-INV-052,REQ-INV-075,API-INV-018 -->
### TC-INV-150 — Stock move Variant key: a receipt of a missing Variant records nothing
Derived from : XM-INV-002  (REQ-INV-051)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-018
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-002
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-002 applied; no CAT Variant has identifier X
Host data    : none
Steps        : 1. PUR receives 1 line of X. 2. GET moves with variantPk X. 3. Run the pre-check query.
Expected     : 1 → code INV-404-VARIANT-NOT-FOUND, messageAr «متغيّر الصنف غير موجود.», messageEn "The Variant was not found." (ADR-INV-021 amended). 2 → no move. 3 → 0 orphan rows.
Test data    : X (placeholder)
<!-- TC:TC-INV-150:END -->

<!-- TC:TC-INV-151:START traces=XM-INV-003,REQ-INV-053,API-INV-018 -->
### TC-INV-151 — Stock move bundle key: an existing bundle is recorded on its component moves
Derived from : XM-INV-003  (REQ-INV-053)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : — → —
Package      : XM-INV-003
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-003 applied (FK_INV_STOCK_MOVE_CAT_VARIANT_2); bundles on; CAT bundle Variant B present with active components in stock
Host data    : none
Steps        : 1. SLS issues 1 of B. 2. GET the moves of the document. 3. Run the pre-check query.
Expected     : 2 → each component move carries bundleVariantPk B. 3 → 0 orphan rows.
Test data    : —
<!-- TC:TC-INV-151:END -->

<!-- TC:TC-INV-152:START traces=XM-INV-003,REQ-INV-053,API-INV-018 -->
### TC-INV-152 — Stock move bundle key: a missing bundle Variant records nothing
Derived from : XM-INV-003  (REQ-INV-053)
Exercises    : InventoryApi.issueStock — CON-INV-015 (in-process, caller SLS) · read back API-INV-018
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-003
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-003 applied; bundles on; no CAT Variant has identifier X
Host data    : none
Steps        : 1. SLS issues 1 of X. 2. GET moves of the document. 3. Run the pre-check query.
Expected     : 1 → code INV-404-VARIANT-NOT-FOUND. 2 → no move. 3 → 0 orphan rows.
Test data    : X (placeholder)
<!-- TC:TC-INV-152:END -->

<!-- TC:TC-INV-153:START traces=XM-INV-004,REQ-INV-043,REQ-INV-050,API-INV-017 -->
### TC-INV-153 — Reservation Variant key: an existing Variant is reserved
Derived from : XM-INV-004  (REQ-INV-043)
Exercises    : InventoryApi.reserve — CON-INV-011 (in-process, caller SFR) · read back API-INV-017
Rule / code  : — → —
Package      : XM-INV-004
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-004 applied (FK_INV_RESERVATION_CAT_VARIANT_1); CAT Variant V present with stock in W
Host data    : none
Steps        : 1. SFR reserves 1 of V in W. 2. GET the availability of V in W. 3. Run the pre-check query.
Expected     : 1 → an ACTIVE Reservation of V. 2 → reserved includes it. 3 → 0 orphan rows.
Test data    : source document type STOREFRONT_ORDER
<!-- TC:TC-INV-153:END -->

<!-- TC:TC-INV-154:START traces=XM-INV-004,REQ-INV-043,REQ-INV-050 -->
### TC-INV-154 — Reservation Variant key: a missing Variant is not reserved
Derived from : XM-INV-004  (REQ-INV-043)
Exercises    : InventoryApi.reserve — CON-INV-011 · InventoryApi.reservationsOfDocument — CON-INV-013 (in-process, caller SFR)
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-004
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-004 applied; no CAT Variant has identifier X
Host data    : none
Steps        : 1. SFR reserves 1 of X for a document D. 2. Read the reservations of D. 3. Run the pre-check query.
Expected     : 1 → code INV-404-VARIANT-NOT-FOUND. 2 → empty list. 3 → 0 orphan rows.
Test data    : X, D (placeholders)
<!-- TC:TC-INV-154:END -->

<!-- TC:TC-INV-155:START traces=XM-INV-005,REQ-INV-049 -->
### TC-INV-155 — Reservation bundle key: an existing bundle is recorded on its component reservations
Derived from : XM-INV-005  (REQ-INV-049)
Exercises    : InventoryApi.reserve — CON-INV-011 · InventoryApi.reservationsOfDocument — CON-INV-013 (in-process, caller SLS)
Rule / code  : — → —
Package      : XM-INV-005
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-005 applied (FK_INV_RESERVATION_CAT_VARIANT_2); bundles on; CAT bundle Variant B present with active components
Host data    : none
Steps        : 1. SLS reserves 1 of B for document D. 2. Read the reservations of D. 3. Run the pre-check query.
Expected     : 2 → each component Reservation carries bundleVariantPk B. 3 → 0 orphan rows.
Test data    : —
<!-- TC:TC-INV-155:END -->

<!-- TC:TC-INV-156:START traces=XM-INV-005,REQ-INV-049 -->
### TC-INV-156 — Reservation bundle key: a missing bundle Variant is not reserved
Derived from : XM-INV-005  (REQ-INV-049)
Exercises    : InventoryApi.reserve — CON-INV-011 · InventoryApi.reservationsOfDocument — CON-INV-013 (in-process, caller SLS)
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-005
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-005 applied; bundles on; no CAT Variant has identifier X
Host data    : none
Steps        : 1. SLS reserves 1 of X for document D. 2. Read the reservations of D. 3. Run the pre-check query.
Expected     : 1 → code INV-404-VARIANT-NOT-FOUND. 2 → empty list. 3 → 0 orphan rows.
Test data    : X, D (placeholders)
<!-- TC:TC-INV-156:END -->

<!-- TC:TC-INV-157:START traces=XM-INV-006,REQ-INV-078,REQ-INV-080,API-INV-022,API-INV-021 -->
### TC-INV-157 — Transfer line Variant key: an existing Variant is saved on the line
Derived from : XM-INV-006  (REQ-INV-078)
Exercises    : API-INV-022 POST /api/v1/inv/transfers · read back API-INV-021
Rule / code  : — → —
Package      : XM-INV-006
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-006 applied (FK_INV_TRANSFER_LINE_CAT_VARIANT); 2 active Warehouses; CAT Variant V present and active
Host data    : none
Steps        : 1. POST a transfer with 1 line of V. 2. GET it. 3. Run the pre-check query.
Expected     : 1 → 201. 2 → the line has variantPk V. 3 → 0 orphan rows.
Test data    : —
<!-- TC:TC-INV-157:END -->

<!-- TC:TC-INV-158:START traces=XM-INV-006,REQ-INV-078,REQ-INV-080,API-INV-023,API-INV-021 -->
### TC-INV-158 — Transfer line Variant key: a missing Variant is refused on update
Derived from : XM-INV-006  (REQ-INV-078)
Exercises    : API-INV-023 PUT /api/v1/inv/transfers/{id} · read back API-INV-021
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-006
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-006 applied; a DRAFT Transfer with 1 line of V; no CAT Variant has identifier X
Host data    : none
Steps        : 1. PUT the transfer with its line's variantPk set to X and its version. 2. GET it. 3. Run the pre-check query.
Expected     : 1 → 404, code INV-404-VARIANT-NOT-FOUND. 2 → the line still has variantPk V. 3 → 0 orphan rows.
Test data    : X (placeholder)
<!-- TC:TC-INV-158:END -->

<!-- TC:TC-INV-159:START traces=XM-INV-007,REQ-INV-088,REQ-INV-092,API-INV-029,API-INV-027 -->
### TC-INV-159 — Count line Variant key: an existing Variant is counted
Derived from : XM-INV-007  (REQ-INV-088)
Exercises    : API-INV-029 POST /api/v1/inv/stock-counts/{id}/lines · read back API-INV-027
Rule / code  : — → —
Package      : XM-INV-007
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-007 applied (FK_INV_STOCK_COUNT_LINE_CAT_VARIANT); a DRAFT count; CAT Variant V present and active
Host data    : none
Steps        : 1. POST a line with variantPk V and countedQty 1. 2. GET the count. 3. Run the pre-check query.
Expected     : 1 → 200. 2 → a line of V. 3 → 0 orphan rows.
Test data    : —
<!-- TC:TC-INV-159:END -->

<!-- TC:TC-INV-160:START traces=XM-INV-007,REQ-INV-088,REQ-INV-092,API-INV-029,API-INV-027 -->
### TC-INV-160 — Count line Variant key: a missing Variant adds no line
Derived from : XM-INV-007  (REQ-INV-088)
Exercises    : API-INV-029 POST /api/v1/inv/stock-counts/{id}/lines · read back API-INV-027
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-007
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-007 applied; a DRAFT count; no CAT Variant has identifier X
Host data    : none
Steps        : 1. POST a line with variantPk X and countedQty 1. 2. GET the count. 3. Run the pre-check query.
Expected     : 1 → 404, code INV-404-VARIANT-NOT-FOUND. 2 → no line of X. 3 → 0 orphan rows.
Test data    : X (placeholder)
<!-- TC:TC-INV-160:END -->

<!-- TC:TC-INV-161:START traces=XM-INV-008,REQ-INV-070,REQ-INV-072,REQ-INV-097,API-INV-036,API-INV-035 -->
### TC-INV-161 — Adjustment line Variant key: an existing Variant is saved on the line
Derived from : XM-INV-008  (REQ-INV-097)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · read back API-INV-035
Rule / code  : — → —
Package      : XM-INV-008
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-008 applied (FK_INV_STOCK_ADJUSTMENT_LINE_CAT_VARIANT); CAT Variant V present and active
Host data    : none
Steps        : 1. POST an adjustment with 1 line of V. 2. GET it. 3. Run the pre-check query.
Expected     : 1 → 201. 2 → the line has variantPk V. 3 → 0 orphan rows.
Test data    : reasonCode DAMAGE
<!-- TC:TC-INV-161:END -->

<!-- TC:TC-INV-162:START traces=XM-INV-008,REQ-INV-070,REQ-INV-072,REQ-INV-097,API-INV-037,API-INV-035 -->
### TC-INV-162 — Adjustment line Variant key: a missing Variant is refused on update
Derived from : XM-INV-008  (REQ-INV-097)
Exercises    : API-INV-037 PUT /api/v1/inv/stock-adjustments/{id} · read back API-INV-035
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-008
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-008 applied; a DRAFT adjustment with 1 line of V; no CAT Variant has identifier X
Host data    : none
Steps        : 1. PUT the adjustment with its line's variantPk set to X and its version. 2. GET it. 3. Run the pre-check query.
Expected     : 1 → 404, code INV-404-VARIANT-NOT-FOUND. 2 → the line still has variantPk V. 3 → 0 orphan rows.
Test data    : X (placeholder)
<!-- TC:TC-INV-162:END -->

<!-- TC:TC-INV-163:START traces=XM-INV-009,REQ-INV-106,REQ-INV-109,API-INV-040 -->
### TC-INV-163 — Lot Variant key: a lot is created for an existing Variant
Derived from : XM-INV-009  (REQ-INV-106)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-040
Rule / code  : — → —
Package      : XM-INV-009
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-009 applied (FK_INV_LOT_CAT_VARIANT); lotExpiry on; CAT Variant V present, lot-tracked
Host data    : none
Steps        : 1. PUR receives V with a new lot number and expiry. 2. GET lots of V. 3. Run the pre-check query.
Expected     : 2 → the new Lot with variantPk V. 3 → 0 orphan rows.
Test data    : lot number and expiry (placeholders)
<!-- TC:TC-INV-163:END -->

<!-- TC:TC-INV-164:START traces=XM-INV-009,REQ-INV-106,REQ-INV-109,API-INV-040 -->
### TC-INV-164 — Lot Variant key: no lot is created for a missing Variant
Derived from : XM-INV-009  (REQ-INV-106)
Exercises    : InventoryApi.receiveStock — CON-INV-014 (in-process, caller PUR) · read back API-INV-040
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-009
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-009 applied; lotExpiry on; no CAT Variant has identifier X
Host data    : none
Steps        : 1. PUR receives X with lot number LX and an expiry. 2. GET lots with code LX. 3. Run the pre-check query.
Expected     : 1 → code INV-404-VARIANT-NOT-FOUND. 2 → no Lot. 3 → 0 orphan rows.
Test data    : X, LX (placeholders)
<!-- TC:TC-INV-164:END -->

<!-- TC:TC-INV-165:START traces=XM-INV-010,REQ-INV-059,REQ-INV-063,API-INV-047,API-INV-051 -->
### TC-INV-165 — Valuation Variant key: the first valued receipt creates the valuation of an existing Variant
Derived from : XM-INV-010  (REQ-INV-059)
Exercises    : API-INV-047 POST /api/v1/inv/opening-stock · read back API-INV-051
Rule / code  : — → —
Package      : XM-INV-010
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-010 applied (FK_INV_STOCK_VALUATION_CAT_VARIANT); CAT Variant V present, never valued
Host data    : none
Steps        : 1. POST opening stock with 1 row of V at a unit cost. 2. GET the valuation of V. 3. Run the pre-check query.
Expected     : 2 → a valuation of V with averageUnitCost = the row cost. 3 → 0 orphan rows.
Test data    : quantity and cost (placeholders)
<!-- TC:TC-INV-165:END -->

<!-- TC:TC-INV-166:START traces=XM-INV-010,REQ-INV-059,REQ-INV-063,API-INV-051,API-INV-050 -->
### TC-INV-166 — Valuation Variant key: a missing Variant has no valuation
Derived from : XM-INV-010  (REQ-INV-063)
Exercises    : API-INV-051 GET /api/v1/inv/stock-valuation/{variantId} · API-INV-050 GET /api/v1/inv/stock-valuation
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-010
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-010 applied; Owner session; no CAT Variant has identifier X
Host data    : none
Steps        : 1. GET the valuation of X. 2. GET the valuation search with variantPk X. 3. Run the pre-check query.
Expected     : 1 → 404, code INV-404-VARIANT-NOT-FOUND. 2 → no row. 3 → 0 orphan rows.
Test data    : X (placeholder)
<!-- TC:TC-INV-166:END -->

<!-- TC:TC-INV-167:START traces=XM-INV-011,REQ-INV-124,REQ-INV-125,REQ-INV-129,API-INV-045,API-INV-043 -->
### TC-INV-167 — Item settings Item key: an existing Item gets its settings record
Derived from : XM-INV-011  (REQ-INV-124)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · read back API-INV-043
Rule / code  : — → —
Package      : XM-INV-011
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-011 applied (FK_INV_ITEM_STOCK_SETTINGS_CAT_ITEM); lotExpiry on; CAT STANDARD Item I (ENT-CAT-001) present
Host data    : none
Steps        : 1. PUT settings of I with lotTrackedFl true. 2. GET settings with itemPk I. 3. Run the pre-check query.
Expected     : 1 → 200. 2 → one record for I. 3 → 0 orphan rows.
Test data    : —
<!-- TC:TC-INV-167:END -->

<!-- TC:TC-INV-168:START traces=XM-INV-011,REQ-INV-124,REQ-INV-125,REQ-INV-129,API-INV-045,API-INV-043 -->
### TC-INV-168 — Item settings Item key: a missing Item gets no record
Derived from : XM-INV-011  (REQ-INV-124)
Exercises    : API-INV-045 PUT /api/v1/inv/item-stock-settings/by-item/{itemId} · read back API-INV-043
Rule / code  : — → INV-404-ITEM-NOT-FOUND
Package      : XM-INV-011
Scenario     : INTEGRATION · data class INVALID · language ALL
Preconditions: CAT:DELIVERED; XM-INV-011 applied; lotExpiry on; no CAT Item has identifier Y
Host data    : none
Steps        : 1. PUT settings of Y with lotTrackedFl true. 2. GET settings with itemPk Y. 3. Run the pre-check query.
Expected     : 1 → 404, code INV-404-ITEM-NOT-FOUND, messageAr «الصنف غير موجود.», messageEn "The Item was not found.". 2 → no record. 3 → 0 orphan rows.
Test data    : Y (placeholder)
<!-- TC:TC-INV-168:END -->

<!-- TC:TC-INV-169:START traces=XM-INV-012,REQ-INV-042,REQ-INV-049,REQ-INV-053,REQ-INV-054,API-INV-017 -->
### TC-INV-169 — Bundle component read returns no active line: availability answers a defined refusal
Derived from : XM-INV-012  (REQ-INV-042)
Exercises    : API-INV-017 GET /api/v1/inv/stock-balances/availability/{variantId}
Rule / code  : RULE-INV-022 → INV-422-BUNDLE-NO-COMPONENTS
Package      : XM-INV-012
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: CAT:DELIVERED; bundles on; the CAT read of bundle Variant B's component lines (ENT-CAT-007) returns no active line
Host data    : none
Steps        : 1. GET the availability of B.
Expected     : 422, code INV-422-BUNDLE-NO-COMPONENTS, messageAr «الحزمة ليس لها مكوّنات نشطة.», messageEn "The bundle has no active components."; no 500.
Test data    : —
<!-- TC:TC-INV-169:END -->

<!-- TC:TC-INV-170:START traces=XM-INV-013,REQ-INV-072,REQ-INV-088,REQ-INV-091,API-INV-016 -->
### TC-INV-170 — Barcode read finds nothing: the balance search answers an empty page
Derived from : XM-INV-013  (REQ-INV-091)
Exercises    : API-INV-016 GET /api/v1/inv/stock-balances
Rule / code  : — → —
Package      : XM-INV-013
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: CAT:DELIVERED; the CAT barcode lookup (ENT-CAT-009) finds no Variant for barcode Z
Host data    : none
Steps        : 1. GET balances with term Z.
Expected     : 200 with an empty page; no 500.
Test data    : Z (placeholder barcode)
<!-- TC:TC-INV-170:END -->

<!-- TC:TC-INV-171:START traces=XM-INV-014,REQ-INV-052,REQ-INV-072,REQ-INV-078,REQ-INV-097,API-INV-036,API-INV-034 -->
### TC-INV-171 — Variant read finds nothing: a new adjustment answers a defined refusal
Derived from : XM-INV-014  (REQ-INV-097)
Exercises    : API-INV-036 POST /api/v1/inv/stock-adjustments · read back API-INV-034
Rule / code  : — → INV-404-VARIANT-NOT-FOUND
Package      : XM-INV-014
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: CAT:DELIVERED; the CAT Variant read (ENT-CAT-002) answers "not found" for identifier X
Host data    : none
Steps        : 1. POST an adjustment with 1 line of X. 2. GET adjustments.
Expected     : 1 → 404, code INV-404-VARIANT-NOT-FOUND; no 500. 2 → nothing saved.
Test data    : X (placeholder); reasonCode DAMAGE
<!-- TC:TC-INV-171:END -->

<!-- TC:TC-INV-172:START traces=XM-INV-015,REQ-INV-039,REQ-INV-124,REQ-INV-126,REQ-INV-127,REQ-INV-129,API-INV-044 -->
### TC-INV-172 — Item read finds nothing: reading its stock settings answers a defined refusal
Derived from : XM-INV-015  (REQ-INV-129)
Exercises    : API-INV-044 GET /api/v1/inv/item-stock-settings/by-item/{itemId}
Rule / code  : — → INV-404-ITEM-NOT-FOUND
Package      : XM-INV-015
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: CAT:DELIVERED; the CAT Item read (ENT-CAT-001) answers "not found" for identifier Y
Host data    : none
Steps        : 1. GET the stock settings of Y.
Expected     : 404, code INV-404-ITEM-NOT-FOUND; no 500.
Test data    : Y (placeholder)
<!-- TC:TC-INV-172:END -->
<!-- SUB:CAT:END -->
<!-- PHASE:INT-XM:END -->

## TC TRACEABILITY INDEX

Generated from the TC blocks above (and, for the AC column of the other track, from frontend-test-plan-inv.md).

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
| AC-INV-044 | TC-INV-076, TC-INV-259 | TC-INV-218, TC-INV-219, TC-INV-220, TC-INV-221, TC-INV-262 |
| AC-INV-045 | TC-INV-077 | — |
| AC-INV-046 | TC-INV-078 | — |
| AC-INV-047 | TC-INV-079 | — |
| AC-INV-048 | TC-INV-080 | — |
| AC-INV-049 | TC-INV-011 | — |
| AC-INV-050 | TC-INV-081 | — |
| AC-INV-051 | TC-INV-082 | — |
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
| AC-INV-083 | TC-INV-103 | TC-INV-187 |
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
| AC-INV-102 | TC-INV-114, TC-INV-258, TC-INV-265 | — |
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

### REQ → TC

| REQ | TC |
|---|---|
| REQ-INV-001 | TC-INV-047 |
| REQ-INV-002 | TC-INV-048 |
| REQ-INV-003 | TC-INV-001, TC-INV-049 |
| REQ-INV-004 | TC-INV-050, TC-INV-269, TC-INV-273 |
| REQ-INV-005 | TC-INV-051, TC-INV-276 |
| REQ-INV-006 | TC-INV-002, TC-INV-003, TC-INV-252 |
| REQ-INV-007 | TC-INV-052 |
| REQ-INV-008 | TC-INV-004, TC-INV-053, TC-INV-248 |
| REQ-INV-009 | TC-INV-054 |
| REQ-INV-010 | TC-INV-055 |
| REQ-INV-013 | TC-INV-056 |
| REQ-INV-014 | TC-INV-005 |
| REQ-INV-015 | TC-INV-057 |
| REQ-INV-016 | TC-INV-058 |
| REQ-INV-017 | TC-INV-006, TC-INV-274 |
| REQ-INV-018 | TC-INV-007, TC-INV-274 |
| REQ-INV-019 | TC-INV-059 |
| REQ-INV-020 | TC-INV-060 |
| REQ-INV-021 | TC-INV-061 |
| REQ-INV-022 | TC-INV-008, TC-INV-062 |
| REQ-INV-023 | TC-INV-063, TC-INV-273 |
| REQ-INV-024 | TC-INV-064 |
| REQ-INV-025 | TC-INV-009, TC-INV-010 |
| REQ-INV-026 | TC-INV-065 |
| REQ-INV-027 | TC-INV-066, TC-INV-272, TC-INV-282 |
| REQ-INV-028 | TC-INV-067 |
| REQ-INV-031 | TC-INV-068, TC-INV-272 |
| REQ-INV-032 | TC-INV-069 |
| REQ-INV-033 | TC-INV-070 |
| REQ-INV-034 | TC-INV-071 |
| REQ-INV-035 | TC-INV-072, TC-INV-147, TC-INV-148, TC-INV-244 |
| REQ-INV-036 | TC-INV-073 |
| REQ-INV-037 | TC-INV-074, TC-INV-251 |
| REQ-INV-038 | TC-INV-075 |
| REQ-INV-039 | TC-INV-076, TC-INV-147, TC-INV-148, TC-INV-172, TC-INV-259 |
| REQ-INV-040 | TC-INV-077, TC-INV-147, TC-INV-148 |
| REQ-INV-041 | TC-INV-078 |
| REQ-INV-042 | TC-INV-079, TC-INV-169 |
| REQ-INV-043 | TC-INV-080, TC-INV-153, TC-INV-154 |
| REQ-INV-044 | TC-INV-011 |
| REQ-INV-045 | TC-INV-081 |
| REQ-INV-046 | TC-INV-082 |
| REQ-INV-047 | TC-INV-083, TC-INV-255 |
| REQ-INV-048 | TC-INV-012 |
| REQ-INV-049 | TC-INV-084, TC-INV-155, TC-INV-156, TC-INV-169 |
| REQ-INV-050 | TC-INV-085, TC-INV-153, TC-INV-154 |
| REQ-INV-051 | TC-INV-086, TC-INV-149, TC-INV-150, TC-INV-267 |
| REQ-INV-052 | TC-INV-013, TC-INV-087, TC-INV-149, TC-INV-150, TC-INV-171 |
| REQ-INV-053 | TC-INV-088, TC-INV-151, TC-INV-152, TC-INV-169 |
| REQ-INV-054 | TC-INV-014, TC-INV-169 |
| REQ-INV-055 | TC-INV-015 |
| REQ-INV-056 | TC-INV-089, TC-INV-260 |
| REQ-INV-057 | TC-INV-090 |
| REQ-INV-058 | TC-INV-091, TC-INV-245, TC-INV-278 |
| REQ-INV-059 | TC-INV-092, TC-INV-165, TC-INV-166 |
| REQ-INV-060 | TC-INV-093 |
| REQ-INV-061 | TC-INV-094 |
| REQ-INV-062 | TC-INV-095 |
| REQ-INV-063 | TC-INV-096, TC-INV-165, TC-INV-166 |
| REQ-INV-064 | TC-INV-016 |
| REQ-INV-065 | TC-INV-017 |
| REQ-INV-066 | TC-INV-097 |
| REQ-INV-067 | TC-INV-018, TC-INV-266 |
| REQ-INV-068 | TC-INV-098 |
| REQ-INV-069 | TC-INV-019, TC-INV-287 |
| REQ-INV-070 | TC-INV-099, TC-INV-161, TC-INV-162, TC-INV-257 |
| REQ-INV-071 | TC-INV-020, TC-INV-100 |
| REQ-INV-072 | TC-INV-021, TC-INV-161, TC-INV-162, TC-INV-170, TC-INV-171 |
| REQ-INV-073 | TC-INV-101 |
| REQ-INV-074 | TC-INV-275 |
| REQ-INV-075 | TC-INV-102, TC-INV-149, TC-INV-150 |
| REQ-INV-076 | TC-INV-103 |
| REQ-INV-077 | TC-INV-104 |
| REQ-INV-078 | TC-INV-105, TC-INV-157, TC-INV-158, TC-INV-171, TC-INV-280 |
| REQ-INV-079 | TC-INV-106, TC-INV-270 |
| REQ-INV-080 | TC-INV-022, TC-INV-107, TC-INV-157, TC-INV-158, TC-INV-246, TC-INV-253, TC-INV-264 |
| REQ-INV-081 | TC-INV-108, TC-INV-247 |
| REQ-INV-082 | TC-INV-023 |
| REQ-INV-083 | TC-INV-024 |
| REQ-INV-084 | TC-INV-025, TC-INV-109, TC-INV-254 |
| REQ-INV-085 | TC-INV-026 |
| REQ-INV-086 | TC-INV-110 |
| REQ-INV-087 | TC-INV-111 |
| REQ-INV-088 | TC-INV-027, TC-INV-112, TC-INV-159, TC-INV-160, TC-INV-170 |
| REQ-INV-089 | TC-INV-113 |
| REQ-INV-091 | TC-INV-028, TC-INV-170 |
| REQ-INV-092 | TC-INV-114, TC-INV-159, TC-INV-160, TC-INV-258, TC-INV-265 |
| REQ-INV-093 | TC-INV-029 |
| REQ-INV-094 | TC-INV-115 |
| REQ-INV-095 | TC-INV-116 |
| REQ-INV-096 | TC-INV-030 |
| REQ-INV-097 | TC-INV-117, TC-INV-161, TC-INV-162, TC-INV-171 |
| REQ-INV-098 | TC-INV-031, TC-INV-118, TC-INV-256 |
| REQ-INV-099 | TC-INV-032, TC-INV-119 |
| REQ-INV-100 | TC-INV-120 |
| REQ-INV-101 | TC-INV-033, TC-INV-281, TC-INV-284 |
| REQ-INV-102 | TC-INV-121 |
| REQ-INV-103 | TC-INV-034 |
| REQ-INV-104 | TC-INV-122 |
| REQ-INV-105 | TC-INV-035, TC-INV-123 |
| REQ-INV-106 | TC-INV-124, TC-INV-163, TC-INV-164, TC-INV-249 |
| REQ-INV-107 | TC-INV-036 |
| REQ-INV-108 | TC-INV-125, TC-INV-271, TC-INV-279 |
| REQ-INV-109 | TC-INV-126, TC-INV-163, TC-INV-164 |
| REQ-INV-110 | TC-INV-127 |
| REQ-INV-111 | TC-INV-128 |
| REQ-INV-112 | TC-INV-129 |
| REQ-INV-113 | TC-INV-037, TC-INV-279 |
| REQ-INV-114 | TC-INV-038, TC-INV-130 |
| REQ-INV-115 | TC-INV-131 |
| REQ-INV-116 | TC-INV-132 |
| REQ-INV-117 | TC-INV-133, TC-INV-283 |
| REQ-INV-118 | TC-INV-134 |
| REQ-INV-119 | TC-INV-039 |
| REQ-INV-120 | TC-INV-135 |
| REQ-INV-121 | TC-INV-136 |
| REQ-INV-122 | TC-INV-040, TC-INV-268 |
| REQ-INV-123 | TC-INV-137 |
| REQ-INV-124 | TC-INV-041, TC-INV-138, TC-INV-167, TC-INV-168, TC-INV-172 |
| REQ-INV-125 | TC-INV-042, TC-INV-139, TC-INV-167, TC-INV-168 |
| REQ-INV-126 | TC-INV-043, TC-INV-172, TC-INV-250 |
| REQ-INV-127 | TC-INV-044, TC-INV-172 |
| REQ-INV-128 | TC-INV-045 |
| REQ-INV-129 | TC-INV-140, TC-INV-167, TC-INV-168, TC-INV-172 |
| REQ-INV-130 | TC-INV-141 |
| REQ-INV-131 | TC-INV-046 |
| REQ-INV-132 | TC-INV-142, TC-INV-242, TC-INV-243 |
| REQ-INV-133 | TC-INV-143 |
| REQ-INV-136 | TC-INV-144 |
| REQ-INV-137 | TC-INV-145, TC-INV-261 |
| REQ-INV-138 | TC-INV-146, TC-INV-277 |
| REQ-INV-139 | TC-INV-286 |
| REQ-INV-140 | TC-INV-288 |

### API → TC

| API | TC |
|---|---|
| API-INV-001 | TC-INV-001, TC-INV-004, TC-INV-047, TC-INV-048, TC-INV-049, TC-INV-051, TC-INV-052, TC-INV-053, TC-INV-054, TC-INV-069, TC-INV-248, TC-INV-272, TC-INV-277 |
| API-INV-002 | TC-INV-002, TC-INV-003, TC-INV-051, TC-INV-058, TC-INV-252, TC-INV-269, TC-INV-276 |
| API-INV-003 | TC-INV-001, TC-INV-049, TC-INV-057, TC-INV-068, TC-INV-272 |
| API-INV-004 | TC-INV-050, TC-INV-269, TC-INV-272, TC-INV-273 |
| API-INV-005 | TC-INV-002, TC-INV-003, TC-INV-051, TC-INV-071, TC-INV-252 |
| API-INV-006 | TC-INV-052, TC-INV-269, TC-INV-272 |
| API-INV-007 | TC-INV-004, TC-INV-053, TC-INV-248, TC-INV-269 |
| API-INV-008 | TC-INV-055, TC-INV-057, TC-INV-069 |
| API-INV-009 | TC-INV-008, TC-INV-047, TC-INV-060, TC-INV-061, TC-INV-062, TC-INV-064, TC-INV-065, TC-INV-066, TC-INV-067, TC-INV-272, TC-INV-282 |
| API-INV-010 | TC-INV-009, TC-INV-010, TC-INV-064, TC-INV-269, TC-INV-276 |
| API-INV-011 | TC-INV-008, TC-INV-062, TC-INV-069 |
| API-INV-012 | TC-INV-063, TC-INV-269, TC-INV-273 |
| API-INV-013 | TC-INV-009, TC-INV-010, TC-INV-064 |
| API-INV-014 | TC-INV-065, TC-INV-269 |
| API-INV-015 | TC-INV-066, TC-INV-269, TC-INV-272, TC-INV-282 |
| API-INV-016 | TC-INV-019, TC-INV-037, TC-INV-072, TC-INV-073, TC-INV-074, TC-INV-076, TC-INV-086, TC-INV-087, TC-INV-097, TC-INV-107, TC-INV-109, TC-INV-114, TC-INV-119, TC-INV-123, TC-INV-124, TC-INV-128, TC-INV-130, TC-INV-136, TC-INV-137, TC-INV-144, TC-INV-145, TC-INV-147, TC-INV-148, TC-INV-170, TC-INV-244, TC-INV-245, TC-INV-249, TC-INV-259, TC-INV-265, TC-INV-266, TC-INV-277, TC-INV-286, TC-INV-288 |
| API-INV-017 | TC-INV-011, TC-INV-075, TC-INV-079, TC-INV-145, TC-INV-153, TC-INV-169, TC-INV-244, TC-INV-251, TC-INV-259, TC-INV-278 |
| API-INV-018 | TC-INV-006, TC-INV-007, TC-INV-012, TC-INV-013, TC-INV-014, TC-INV-018, TC-INV-019, TC-INV-035, TC-INV-036, TC-INV-037, TC-INV-038, TC-INV-040, TC-INV-056, TC-INV-057, TC-INV-058, TC-INV-073, TC-INV-074, TC-INV-086, TC-INV-087, TC-INV-088, TC-INV-089, TC-INV-091, TC-INV-094, TC-INV-099, TC-INV-102, TC-INV-103, TC-INV-105, TC-INV-107, TC-INV-108, TC-INV-115, TC-INV-117, TC-INV-119, TC-INV-120, TC-INV-121, TC-INV-123, TC-INV-127, TC-INV-128, TC-INV-129, TC-INV-135, TC-INV-143, TC-INV-144, TC-INV-145, TC-INV-149, TC-INV-150, TC-INV-151, TC-INV-152, TC-INV-245, TC-INV-246, TC-INV-247, TC-INV-253, TC-INV-255, TC-INV-258, TC-INV-260, TC-INV-261, TC-INV-267, TC-INV-278, TC-INV-279, TC-INV-286, TC-INV-287 |
| API-INV-019 | TC-INV-015, TC-INV-089, TC-INV-276 |
| API-INV-020 | TC-INV-005, TC-INV-023, TC-INV-025, TC-INV-110, TC-INV-144, TC-INV-145, TC-INV-280 |
| API-INV-021 | TC-INV-022, TC-INV-024, TC-INV-026, TC-INV-105, TC-INV-106, TC-INV-157, TC-INV-158, TC-INV-246, TC-INV-247, TC-INV-253, TC-INV-264, TC-INV-270, TC-INV-276 |
| API-INV-022 | TC-INV-005, TC-INV-023, TC-INV-025, TC-INV-105, TC-INV-157, TC-INV-280 |
| API-INV-023 | TC-INV-026, TC-INV-106, TC-INV-158, TC-INV-270, TC-INV-273 |
| API-INV-024 | TC-INV-022, TC-INV-024, TC-INV-095, TC-INV-107, TC-INV-109, TC-INV-246, TC-INV-247, TC-INV-253, TC-INV-264, TC-INV-270, TC-INV-286 |
| API-INV-025 | TC-INV-108, TC-INV-247 |
| API-INV-026 | TC-INV-116, TC-INV-144, TC-INV-274 |
| API-INV-027 | TC-INV-027, TC-INV-028, TC-INV-029, TC-INV-030, TC-INV-112, TC-INV-113, TC-INV-115, TC-INV-159, TC-INV-160, TC-INV-270, TC-INV-276 |
| API-INV-028 | TC-INV-111, TC-INV-274, TC-INV-280 |
| API-INV-029 | TC-INV-027, TC-INV-028, TC-INV-030, TC-INV-112, TC-INV-159, TC-INV-160 |
| API-INV-030 | TC-INV-113, TC-INV-270, TC-INV-276 |
| API-INV-031 | TC-INV-113 |
| API-INV-032 | TC-INV-029, TC-INV-114, TC-INV-258, TC-INV-265, TC-INV-270, TC-INV-286 |
| API-INV-033 | TC-INV-115, TC-INV-252 |
| API-INV-034 | TC-INV-020, TC-INV-021, TC-INV-033, TC-INV-122, TC-INV-144, TC-INV-145, TC-INV-146, TC-INV-171, TC-INV-256, TC-INV-257, TC-INV-268, TC-INV-274, TC-INV-275, TC-INV-281, TC-INV-288 |
| API-INV-035 | TC-INV-031, TC-INV-032, TC-INV-034, TC-INV-100, TC-INV-114, TC-INV-117, TC-INV-118, TC-INV-161, TC-INV-162, TC-INV-254, TC-INV-264, TC-INV-265, TC-INV-266, TC-INV-270, TC-INV-276, TC-INV-281 |
| API-INV-036 | TC-INV-032, TC-INV-033, TC-INV-055, TC-INV-094, TC-INV-117, TC-INV-130, TC-INV-145, TC-INV-146, TC-INV-161, TC-INV-171, TC-INV-256, TC-INV-268, TC-INV-274, TC-INV-277, TC-INV-280, TC-INV-281 |
| API-INV-037 | TC-INV-031, TC-INV-034, TC-INV-104, TC-INV-118, TC-INV-162, TC-INV-270, TC-INV-273, TC-INV-281 |
| API-INV-038 | TC-INV-032, TC-INV-094, TC-INV-119, TC-INV-121, TC-INV-130, TC-INV-137, TC-INV-254, TC-INV-264, TC-INV-266, TC-INV-270, TC-INV-286 |
| API-INV-039 | TC-INV-120 |
| API-INV-040 | TC-INV-124, TC-INV-126, TC-INV-131, TC-INV-132, TC-INV-134, TC-INV-144, TC-INV-163, TC-INV-164, TC-INV-244, TC-INV-249 |
| API-INV-041 | TC-INV-109, TC-INV-125, TC-INV-133, TC-INV-271, TC-INV-276, TC-INV-279, TC-INV-283 |
| API-INV-042 | TC-INV-125, TC-INV-271, TC-INV-279 |
| API-INV-043 | TC-INV-138, TC-INV-140, TC-INV-167, TC-INV-168 |
| API-INV-044 | TC-INV-041, TC-INV-042, TC-INV-043, TC-INV-044, TC-INV-045, TC-INV-140, TC-INV-172, TC-INV-250, TC-INV-271 |
| API-INV-045 | TC-INV-032, TC-INV-041, TC-INV-042, TC-INV-043, TC-INV-044, TC-INV-045, TC-INV-138, TC-INV-139, TC-INV-167, TC-INV-168, TC-INV-250, TC-INV-254, TC-INV-271, TC-INV-286 |
| API-INV-046 | TC-INV-101, TC-INV-144 |
| API-INV-047 | TC-INV-020, TC-INV-021, TC-INV-099, TC-INV-100, TC-INV-147, TC-INV-148, TC-INV-165, TC-INV-244, TC-INV-257, TC-INV-268, TC-INV-275, TC-INV-280, TC-INV-288 |
| API-INV-048 | TC-INV-017, TC-INV-039, TC-INV-097, TC-INV-134, TC-INV-144 |
| API-INV-049 | TC-INV-011, TC-INV-017, TC-INV-039, TC-INV-134 |
| API-INV-050 | TC-INV-144, TC-INV-166 |
| API-INV-051 | TC-INV-016, TC-INV-092, TC-INV-093, TC-INV-094, TC-INV-095, TC-INV-099, TC-INV-121, TC-INV-165, TC-INV-166, TC-INV-258 |
| API-INV-052 | TC-INV-265, TC-INV-284 |

### RULE / code → TC

| RULE / code | TC |
|---|---|
| RULE-INV-001 | TC-INV-049, TC-INV-050, TC-INV-273 |
| RULE-INV-002 | TC-INV-001 |
| RULE-INV-003 | TC-INV-002 |
| RULE-INV-004 | TC-INV-003, TC-INV-252 |
| RULE-INV-005 | TC-INV-051, TC-INV-071 |
| RULE-INV-006 | TC-INV-004, TC-INV-053, TC-INV-248 |
| RULE-INV-007 | TC-INV-007, TC-INV-274, TC-INV-287 |
| RULE-INV-008 | TC-INV-006, TC-INV-055, TC-INV-056, TC-INV-058, TC-INV-274, TC-INV-287 |
| RULE-INV-009 | TC-INV-068, TC-INV-272 |
| RULE-INV-010 | TC-INV-062, TC-INV-063, TC-INV-273 |
| RULE-INV-011 | TC-INV-008 |
| RULE-INV-012 | TC-INV-009 |
| RULE-INV-013 | TC-INV-010 |
| RULE-INV-014 | TC-INV-064 |
| RULE-INV-015 | TC-INV-066, TC-INV-272, TC-INV-282 |
| RULE-INV-016 | TC-INV-005, TC-INV-023, TC-INV-107 |
| RULE-INV-017 | TC-INV-256 |
| RULE-INV-018 | TC-INV-013, TC-INV-253, TC-INV-264, TC-INV-267, TC-INV-287 |
| RULE-INV-019 | TC-INV-017, TC-INV-018, TC-INV-019, TC-INV-024, TC-INV-266 |
| RULE-INV-020 | TC-INV-011, TC-INV-081, TC-INV-251 |
| RULE-INV-021 | TC-INV-012, TC-INV-082, TC-INV-083 |
| RULE-INV-022 | TC-INV-014, TC-INV-169 |
| RULE-INV-023 | TC-INV-015 |
| RULE-INV-024 | TC-INV-091, TC-INV-245, TC-INV-288 |
| RULE-INV-025 | TC-INV-016, TC-INV-121, TC-INV-257, TC-INV-258 |
| RULE-INV-026 | TC-INV-099, TC-INV-275 |
| RULE-INV-027 | TC-INV-020, TC-INV-100 |
| RULE-INV-028 | TC-INV-021, TC-INV-148 |
| RULE-INV-029 | TC-INV-026, TC-INV-030, TC-INV-034, TC-INV-108, TC-INV-115, TC-INV-120, TC-INV-246, TC-INV-247 |
| RULE-INV-030 | TC-INV-105, TC-INV-111, TC-INV-117, TC-INV-273 |
| RULE-INV-031 | TC-INV-022, TC-INV-029 |
| RULE-INV-032 | TC-INV-028 |
| RULE-INV-033 | TC-INV-033, TC-INV-265, TC-INV-281 |
| RULE-INV-034 | TC-INV-032, TC-INV-137 |
| RULE-INV-035 | TC-INV-035, TC-INV-123, TC-INV-143 |
| RULE-INV-036 | TC-INV-036, TC-INV-124, TC-INV-249 |
| RULE-INV-037 | TC-INV-025, TC-INV-109, TC-INV-254, TC-INV-286 |
| RULE-INV-038 | TC-INV-037, TC-INV-075, TC-INV-127, TC-INV-131, TC-INV-279 |
| RULE-INV-039 | TC-INV-038, TC-INV-130 |
| RULE-INV-040 | TC-INV-040, TC-INV-135, TC-INV-268 |
| RULE-INV-041 | TC-INV-042, TC-INV-139 |
| RULE-INV-042 | TC-INV-043, TC-INV-250 |
| RULE-INV-043 | TC-INV-044 |
| RULE-INV-044 | TC-INV-045, TC-INV-138 |
| RULE-INV-045 | TC-INV-041 |
| RULE-INV-046 | TC-INV-046, TC-INV-141 |
| RULE-INV-047 | TC-INV-027 |
| RULE-INV-048 | TC-INV-031 |
| RULE-INV-049 | TC-INV-255 |
| RULE-INV-050 | TC-INV-278, TC-INV-288 |
| RULE-INV-051 | TC-INV-280 |
| INV-400 | TC-INV-277 |
| INV-401 | TC-INV-277 |
| INV-403 | TC-INV-144, TC-INV-145, TC-INV-146, TC-INV-261, TC-INV-284 |
| INV-404-BRANCH-NOT-FOUND | TC-INV-276 |
| INV-404-ITEM-NOT-FOUND | TC-INV-168, TC-INV-172 |
| INV-404-LOOKUP-KEY-UNKNOWN | TC-INV-284 |
| INV-404-LOT-NOT-FOUND | TC-INV-276 |
| INV-404-STOCK-ADJUSTMENT-NOT-FOUND | TC-INV-276 |
| INV-404-STOCK-COUNT-LINE-NOT-FOUND | TC-INV-276 |
| INV-404-STOCK-COUNT-NOT-FOUND | TC-INV-276 |
| INV-404-STOCK-MOVE-NOT-FOUND | TC-INV-276 |
| INV-404-TRANSFER-NOT-FOUND | TC-INV-276 |
| INV-404-VARIANT-NOT-FOUND | TC-INV-150, TC-INV-152, TC-INV-154, TC-INV-156, TC-INV-158, TC-INV-160, TC-INV-162, TC-INV-164, TC-INV-166, TC-INV-171 |
| INV-404-WAREHOUSE-NOT-FOUND | TC-INV-276 |
| INV-409-BRANCH-DEFAULT | TC-INV-009 |
| INV-409-BRANCH-DEFAULT-INACTIVE | TC-INV-272 |
| INV-409-BRANCH-IN-USE | TC-INV-010 |
| INV-409-BRANCH-NAME-TAKEN | TC-INV-008 |
| INV-409-DOCUMENT-CLOSED | TC-INV-026, TC-INV-030, TC-INV-034, TC-INV-246, TC-INV-247 |
| INV-409-DUAL-QUANTITY-IN-USE | TC-INV-044 |
| INV-409-IDEMPOTENCY-KEY-REUSED | TC-INV-278, TC-INV-288 |
| INV-409-LOT-TRACKING-IN-USE | TC-INV-043, TC-INV-250 |
| INV-409-NEGATIVE-STOCK | TC-INV-017, TC-INV-018, TC-INV-024, TC-INV-266 |
| INV-409-RESERVATION-CLOSED | TC-INV-012 |
| INV-409-RESERVE-NOT-AVAILABLE | TC-INV-011, TC-INV-251 |
| INV-409-STALE-VERSION | TC-INV-269, TC-INV-270, TC-INV-271 |
| INV-409-STOCK-MOVE-IMMUTABLE | TC-INV-015 |
| INV-409-WAREHOUSE-DEFAULT | TC-INV-002 |
| INV-409-WAREHOUSE-DEFAULT-INACTIVE | TC-INV-004 |
| INV-409-WAREHOUSE-IN-USE | TC-INV-003, TC-INV-252 |
| INV-409-WAREHOUSE-NAME-TAKEN | TC-INV-001 |
| INV-422-BARCODE-UNKNOWN | TC-INV-028 |
| INV-422-BRANCH-CODE-READONLY | TC-INV-273 |
| INV-422-BRANCH-INACTIVE | TC-INV-272 |
| INV-422-BUNDLE-NO-COMPONENTS | TC-INV-014, TC-INV-169 |
| INV-422-BUNDLE-SETTINGS | TC-INV-041 |
| INV-422-CAPABILITY-OFF | TC-INV-045 |
| INV-422-COUNTED-NEGATIVE | TC-INV-027 |
| INV-422-DOC-DATE-FUTURE | TC-INV-280 |
| INV-422-DOC-NO-READONLY | TC-INV-273 |
| INV-422-DUPLICATE-LINE | TC-INV-031 |
| INV-422-HORIZON-RANGE | TC-INV-039 |
| INV-422-LOAD-ROW-VARIANT | TC-INV-021, TC-INV-148 |
| INV-422-LOAD-ROWS | TC-INV-020 |
| INV-422-LOAD-ROWS-INVALID | TC-INV-021, TC-INV-148, TC-INV-257, TC-INV-275 |
| INV-422-LOT-EXPIRED | TC-INV-038 |
| INV-422-LOT-EXPIRY-MISMATCH | TC-INV-036, TC-INV-249 |
| INV-422-LOT-EXPIRY-REQUIRED | TC-INV-035, TC-INV-143 |
| INV-422-LOT-REQUIRED | TC-INV-025, TC-INV-254, TC-INV-286 |
| INV-422-MEASURE-REQUIRED | TC-INV-040, TC-INV-268 |
| INV-422-MEASURE-UNIT-REQUIRED | TC-INV-042 |
| INV-422-NO-KNOWN-COST | TC-INV-016, TC-INV-257 |
| INV-422-NO-LINES | TC-INV-022, TC-INV-029 |
| INV-422-OPENING-COST-REQUIRED | TC-INV-275 |
| INV-422-QUANTITY-INVALID | TC-INV-256 |
| INV-422-REASON-REQUIRED | TC-INV-033, TC-INV-281 |
| INV-422-RESERVATION-MISMATCH | TC-INV-255 |
| INV-422-TRANSFER-WAREHOUSES | TC-INV-005, TC-INV-023 |
| INV-422-VARIANT-INACTIVE | TC-INV-013, TC-INV-253, TC-INV-264, TC-INV-267 |
| INV-422-WAREHOUSE-CODE-READONLY | TC-INV-273 |
| INV-422-WAREHOUSE-INACTIVE | TC-INV-007, TC-INV-274 |
| INV-422-WAREHOUSE-REQUIRED | TC-INV-006, TC-INV-274 |
| INV-422-ZERO-CHANGE | TC-INV-032 |
| INV-500 | TC-INV-277 |

### XM → TC

| XM | TC |
|---|---|
| XM-INV-001 | TC-INV-147, TC-INV-148 |
| XM-INV-002 | TC-INV-149, TC-INV-150 |
| XM-INV-003 | TC-INV-151, TC-INV-152 |
| XM-INV-004 | TC-INV-153, TC-INV-154 |
| XM-INV-005 | TC-INV-155, TC-INV-156 |
| XM-INV-006 | TC-INV-157, TC-INV-158 |
| XM-INV-007 | TC-INV-159, TC-INV-160 |
| XM-INV-008 | TC-INV-161, TC-INV-162 |
| XM-INV-009 | TC-INV-163, TC-INV-164 |
| XM-INV-010 | TC-INV-165, TC-INV-166 |
| XM-INV-011 | TC-INV-167, TC-INV-168 |
| XM-INV-012 | TC-INV-169 |
| XM-INV-013 | TC-INV-170 |
| XM-INV-014 | TC-INV-171 |
| XM-INV-015 | TC-INV-172 |

### Package → TC

| Package | TC |
|---|---|
| CORE | TC-INV-047, TC-INV-048, TC-INV-060, TC-INV-061, TC-INV-133, TC-INV-142, TC-INV-242, TC-INV-243, TC-INV-283 |
| DATA-DOM-LOOKUP | TC-INV-140 |
| DATA-DOM-MASTER | TC-INV-124 |
| DATA-DOM-TRANSACTIONAL | TC-INV-015, TC-INV-058, TC-INV-244 |
| SEC-BE | TC-INV-144, TC-INV-145, TC-INV-146, TC-INV-261, TC-INV-277 |
| SVC-API-CRUD | TC-INV-001, TC-INV-002, TC-INV-003, TC-INV-004, TC-INV-005, TC-INV-008, TC-INV-009, TC-INV-010, TC-INV-017, TC-INV-020, TC-INV-021, TC-INV-022, TC-INV-023, TC-INV-024, TC-INV-025, TC-INV-026, TC-INV-027, TC-INV-028, TC-INV-029, TC-INV-030, TC-INV-031, TC-INV-032, TC-INV-033, TC-INV-034, TC-INV-039, TC-INV-041, TC-INV-042, TC-INV-043, TC-INV-044, TC-INV-045, TC-INV-049, TC-INV-050, TC-INV-051, TC-INV-052, TC-INV-053, TC-INV-055, TC-INV-062, TC-INV-063, TC-INV-064, TC-INV-065, TC-INV-066, TC-INV-068, TC-INV-071, TC-INV-094, TC-INV-095, TC-INV-099, TC-INV-100, TC-INV-104, TC-INV-105, TC-INV-106, TC-INV-107, TC-INV-108, TC-INV-109, TC-INV-111, TC-INV-112, TC-INV-113, TC-INV-114, TC-INV-115, TC-INV-117, TC-INV-118, TC-INV-119, TC-INV-120, TC-INV-121, TC-INV-125, TC-INV-130, TC-INV-134, TC-INV-137, TC-INV-138, TC-INV-139, TC-INV-246, TC-INV-247, TC-INV-248, TC-INV-250, TC-INV-252, TC-INV-253, TC-INV-254, TC-INV-256, TC-INV-257, TC-INV-258, TC-INV-264, TC-INV-265, TC-INV-266, TC-INV-268, TC-INV-269, TC-INV-270, TC-INV-271, TC-INV-272, TC-INV-273, TC-INV-274, TC-INV-275, TC-INV-280, TC-INV-281, TC-INV-282, TC-INV-286, TC-INV-288 |
| SVC-API-INT | TC-INV-006, TC-INV-007, TC-INV-011, TC-INV-012, TC-INV-013, TC-INV-014, TC-INV-016, TC-INV-018, TC-INV-019, TC-INV-035, TC-INV-036, TC-INV-037, TC-INV-038, TC-INV-040, TC-INV-046, TC-INV-056, TC-INV-059, TC-INV-070, TC-INV-072, TC-INV-073, TC-INV-074, TC-INV-077, TC-INV-078, TC-INV-080, TC-INV-081, TC-INV-082, TC-INV-083, TC-INV-084, TC-INV-085, TC-INV-086, TC-INV-087, TC-INV-088, TC-INV-090, TC-INV-091, TC-INV-092, TC-INV-093, TC-INV-096, TC-INV-097, TC-INV-098, TC-INV-123, TC-INV-127, TC-INV-128, TC-INV-129, TC-INV-135, TC-INV-141, TC-INV-143, TC-INV-245, TC-INV-249, TC-INV-251, TC-INV-255, TC-INV-267, TC-INV-278, TC-INV-279, TC-INV-287 |
| SVC-API-SEARCH | TC-INV-054, TC-INV-057, TC-INV-067, TC-INV-069, TC-INV-075, TC-INV-076, TC-INV-079, TC-INV-089, TC-INV-101, TC-INV-102, TC-INV-103, TC-INV-110, TC-INV-116, TC-INV-122, TC-INV-126, TC-INV-131, TC-INV-132, TC-INV-136, TC-INV-259, TC-INV-260, TC-INV-276, TC-INV-284 |
| XM-INV-001 | TC-INV-147, TC-INV-148 |
| XM-INV-002 | TC-INV-149, TC-INV-150 |
| XM-INV-003 | TC-INV-151, TC-INV-152 |
| XM-INV-004 | TC-INV-153, TC-INV-154 |
| XM-INV-005 | TC-INV-155, TC-INV-156 |
| XM-INV-006 | TC-INV-157, TC-INV-158 |
| XM-INV-007 | TC-INV-159, TC-INV-160 |
| XM-INV-008 | TC-INV-161, TC-INV-162 |
| XM-INV-009 | TC-INV-163, TC-INV-164 |
| XM-INV-010 | TC-INV-165, TC-INV-166 |
| XM-INV-011 | TC-INV-167, TC-INV-168 |
| XM-INV-012 | TC-INV-169 |
| XM-INV-013 | TC-INV-170 |
| XM-INV-014 | TC-INV-171 |
| XM-INV-015 | TC-INV-172 |

## COVERAGE

| Measure | Result |
|---|---|
| AC covered (both tracks) | ✓ 158/158 · gaps: none |
| AC covered on this track | 151/158 (the rest on the other track; 50 ACs on both) |
| REQ covered (both tracks) | ✓ 140/140 · gaps: none |
| REQ covered on this track | 133/140 |
| API covered | ✓ 52/52 · gaps: none |
| Error Catalog rows asserted | ✓ 59/59 HTTP rows (§5) and 5/5 in-process rows (§5.1) each named on a `Rule / code` line · gaps: none |
| XM edges covered | ✓ 15/15 (11 HARD-FK → EXISTS + MISSING, 4 SOFT-READ → GRACEFUL-DEGRADATION) · gaps: none |
| Split units with no TC | 2 of 25: DOC, ALIGN-BE · no AC, XM or UXD derives a case for them (ADR-INV-037) |
| Concurrency and post-time guards (gate-analysis revise 1) | TC-INV-242 … 261: two posts of one document, a post racing a cancel, two default switches, two first receipts of one lot, a flag clear racing a receipt, a reservation racing a lot issue, one idempotency key submitted twice at once, a post after the Variant or the Item stock settings changed, a deactivation refused by a DRAFT, a reservation of another line, 4-decimal quantities and 5-decimal costs, the count of a never-valued Variant, native-query tenancy, the manifest and the boundary rules |
| Catalogue coverage and guards (gate-analysis revise 2) | TC-INV-264 … 284: a line refusal named in `details`, a system-generated reason not checked, a lot-level negative check, a bundle receipt refused, the measure of an adjustment or opening stock line, stale versions on masters, documents, lots and settings, an inactive branch, the read-only code and docNo, inactive or missing warehouse over REST, the opening-stock cost, every not-found code, INV-400 / 401 / 500, a reused idempotency key, a sale racing an expiry correction, a future docDate, OPENING_STOCK refused on the adjustment screen, two concurrent branch default switches, one near-expiry notification per lot, the INV lookup endpoint |
| Lot-less stock, offline sales, opening stock idempotency (gate-analysis revise 3) | TC-INV-286 … 288: lot-less stock counted, transferred and written off after lot tracking is switched on, and refused beyond its quantity; offline sales naming an inactive Variant or warehouse or no warehouse recorded and reported; a double-submitted opening stock load posted once and a reused key refused. Every INV code is asserted with its text (TC-INV-276, TC-INV-284 and the load-wrapper cases — ADR-INV-021 amended) |
| Over-engineering guard | 216 TCs for 151 ACs + 15 XM (ratio 1.30 ≤ 2) |

