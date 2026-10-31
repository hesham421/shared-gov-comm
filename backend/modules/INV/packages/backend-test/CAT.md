<!-- source: PHASE:INT-XM / SUB:CAT -->
<!-- context: INT-XM-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-016, API-INV-017, API-INV-018, API-INV-021, API-INV-022, API-INV-023, API-INV-027, API-INV-029, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-040, API-INV-043, API-INV-044, API-INV-045, API-INV-047, API-INV-050, API-INV-051, REQ-INV-035, REQ-INV-039, REQ-INV-040, REQ-INV-042, REQ-INV-043, REQ-INV-049, REQ-INV-050, REQ-INV-051, REQ-INV-052, REQ-INV-053, REQ-INV-054, REQ-INV-059, REQ-INV-063, REQ-INV-070, REQ-INV-072, REQ-INV-075, REQ-INV-078, REQ-INV-080, REQ-INV-088, REQ-INV-091, REQ-INV-092, REQ-INV-097, REQ-INV-106, REQ-INV-109, REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-129, XM-INV-001, XM-INV-002, XM-INV-003, XM-INV-004, XM-INV-005, XM-INV-006, XM-INV-007, XM-INV-008, XM-INV-009, XM-INV-010, XM-INV-011, XM-INV-012, XM-INV-013, XM-INV-014, XM-INV-015 -->
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
