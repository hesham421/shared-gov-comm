<!-- source: PHASE:TEST-PLAN-FE / SUB:INT-FLOW -->
<!-- context: TEST-PLAN-FE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-018, AC-INV-037, AC-INV-076, AC-INV-100, AC-INV-128, REQ-INV-015, REQ-INV-032, REQ-INV-070, REQ-INV-090, REQ-INV-114, SCR-INV-001, SCR-INV-002, SCR-INV-003, SCR-INV-004, SCR-INV-005, SCR-INV-006, SCR-INV-007, SCR-INV-008, SCR-INV-010, SCR-INV-012 -->
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
