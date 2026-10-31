<!-- source: PHASE:INT-UXD / SUB:CAT -->
<!-- context: INT-UXD-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-044, AC-INV-069, AC-INV-082, AC-INV-085, AC-INV-097, AC-INV-107, AC-INV-122, AC-INV-135, AC-INV-138, AC-INV-140, REQ-INV-039, REQ-INV-063, REQ-INV-075, REQ-INV-078, REQ-INV-088, REQ-INV-097, REQ-INV-109, REQ-INV-121, REQ-INV-124, REQ-INV-125, SCR-INV-003, SCR-INV-004, SCR-INV-005, SCR-INV-006, SCR-INV-007, SCR-INV-008, SCR-INV-009, SCR-INV-012, UXD-INV-001, UXD-INV-002, UXD-INV-003, UXD-INV-004, UXD-INV-005, UXD-INV-006, UXD-INV-007, UXD-INV-008, UXD-INV-009, UXD-INV-010, UXD-INV-011 -->
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
