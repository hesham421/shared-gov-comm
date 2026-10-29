<!-- source: PHASE:TEST-PLAN-FE / SUB:INT-FLOW -->
<!-- context: TEST-PLAN-FE-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-010, AC-PTY-011, AC-PTY-029, AC-PTY-031, AC-PTY-082, AC-PTY-087, REQ-PTY-008, REQ-PTY-024, REQ-PTY-026, REQ-PTY-073, REQ-PTY-077, SCR-PTY-001, SCR-PTY-002, SCR-PTY-003 -->
<!-- SUB:INT-FLOW:START traces=REQ-PTY-008,REQ-PTY-024,REQ-PTY-026,REQ-PTY-073,REQ-PTY-077,AC-PTY-010,AC-PTY-011,AC-PTY-029,AC-PTY-031,AC-PTY-082,AC-PTY-087,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003 -->
### INT-FLOW — flows across screens, host documents and the flag lifecycle

<!-- TC:TC-PTY-146:START traces=AC-PTY-010,REQ-PTY-008,SCR-PTY-001 -->
### TC-PTY-146 — Quick register hands the new Customer back to the sale
Derived from : AC-PTY-010  (REQ-PTY-008)
Exercises    : SCR-PTY-001 Quick register sub-view (`CustomerQuickRegister`, props onRegistered / onCancel)
Rule / code  : — 
Package      : F2-SCR-PTY-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier session holding PERM_PTY_CUSTOMER_CREATE; an open sale (test host — F-N6) owning the sub-view's container; the mock answers API-PTY-001 with 201 carrying customerPk `<customerPk>` and code `<code>`
Host data    : none
Steps        : 1. open the Quick register from the sale · 2. enter nameEn "Omar Said" · 3. Save «حفظ»
Expected     : onRegistered is called once with {id: `<customerPk>`, code: `<code>`} of the response; onCancel is not called; the route does not change (the sale screen stays open)
Test data    : nameEn "Omar Said" (AC); `<customerPk>`, `<code>` placeholders from the mock response
<!-- TC:TC-PTY-146:END -->

<!-- TC:TC-PTY-147:START traces=AC-PTY-011,REQ-PTY-008,SCR-PTY-002 -->
### TC-PTY-147 — Quick register hands the new Supplier back to the purchase document
Derived from : AC-PTY-011  (REQ-PTY-008)
Exercises    : SCR-PTY-002 Quick register sub-view (`SupplierQuickRegister`)
Rule / code  : — 
Package      : F2-SCR-PTY-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_CREATE; an open purchase document (test host — F-N6); the mock answers API-PTY-006 with 201 carrying supplierPk `<supplierPk>` and code `<code>`
Host data    : none
Steps        : 1. open the Quick register from the purchase document · 2. enter nameAr «مؤسسة النور» · 3. Save «حفظ»
Expected     : onRegistered is called once with {id: `<supplierPk>`, code: `<code>`}; the route does not change
Test data    : nameAr «مؤسسة النور» (AC); `<supplierPk>`, `<code>` placeholders from the mock response
<!-- TC:TC-PTY-147:END -->

<!-- TC:TC-PTY-148:START traces=AC-PTY-029,REQ-PTY-024,SCR-PTY-002,SCR-PTY-001 -->
### TC-PTY-148 — Mobile changed on the Supplier screen shows on the Customer screen
Derived from : AC-PTY-029  (REQ-PTY-024)
Exercises    : SCR-PTY-002 /pty/suppliers/:supplierId → SCR-PTY-001 /pty/customers/:customerId
Rule / code  : — 
Package      : F2-SCR-PTY-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_VIEW, _UPDATE and PERM_PTY_CUSTOMER_VIEW; Party P-000130 holds a Customer `<customerId>` and a Supplier `<supplierId>`; the Customer screen of `<customerId>` was opened once before (its read is cached); the mock answers API-PTY-008 and the following API-PTY-002 with mobile "+966500001111"
Host data    : none
Steps        : 1. open /pty/suppliers/{supplierId} · 2. change mobile to "+966500001111" · 3. Save «حفظ» · 4. open /pty/customers/{customerId}
Expected     : step 3 → one API-PTY-008 call; no address call (the address set is unchanged) · step 4 → the Customer is re-read (its cached entry was invalidated by the Supplier save) and shows mobile "+966500001111"
Test data    : Party P-000130, "+966500001111" (AC); role identifiers are placeholders
<!-- TC:TC-PTY-148:END -->

<!-- TC:TC-PTY-149:START traces=AC-PTY-031,REQ-PTY-026,SCR-PTY-002,SCR-PTY-001 -->
### TC-PTY-149 — "Make customer" opens the new RETAIL Customer
Derived from : AC-PTY-031  (REQ-PTY-026)
Exercises    : SCR-PTY-002 /pty/suppliers/:supplierId → SCR-PTY-001 /pty/customers/:customerId
Rule / code  : — 
Package      : F4-SCR-PTY-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_VIEW and PERM_PTY_CUSTOMER_CREATE, _VIEW; Supplier 7004 of Party P-000204 and no Customer for that Party; the mock answers API-PTY-001 with 201 (partyFk of P-000204, customerGroupCode RETAIL, isActiveFl true)
Host data    : none
Steps        : 1. open /pty/suppliers/7004 · 2. choose "Make customer" «إضافة كعميل» and confirm
Expected     : the API-PTY-001 body is {partyId} only (no Party field asked) · the route becomes /pty/customers/:customerPk of the response, showing customer group Retail «تجزئة» and the Customer active
Test data    : Supplier 7004, Party P-000204 (AC)
<!-- TC:TC-PTY-149:END -->

<!-- TC:TC-PTY-150:START traces=AC-PTY-082,REQ-PTY-073,SCR-PTY-001 -->
### TC-PTY-150 — Credit appears on the next screen load after the flag is turned on
Derived from : AC-PTY-082  (REQ-PTY-073)
Exercises    : SCR-PTY-001 /pty/customers/:customerId and the Party menu
Rule / code  : — 
Package      : F2-SCR-PTY-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: an Owner session holding every PTY permission, on /pty/customers/5001 with `b2bCredit` off (no credit group, no Credit menu group)
Host data    : none
Steps        : 1. turn `b2bCredit` on in the given application configuration (the mock's app-config now answers true; API-PTY-002 now carries the credit members) · 2. reload /pty/customers/5001
Expected     : the application configuration is re-read on the route mount · the Customer screen shows the credit limit and payment terms (read-only — ADR-PTY-033) · the menu shows the Credit «الائتمان» group with Customer credit «ائتمان العملاء», Credit override requests «طلبات تجاوز حد الائتمان» and Credit control settings «إعدادات ضبط الائتمان» · 0 setup steps are presented
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-150:END -->

<!-- TC:TC-PTY-151:START traces=AC-PTY-087,REQ-PTY-077,SCR-PTY-003 -->
### TC-PTY-151 — Next action of the refused switch-off opens the filtered credit list
Derived from : AC-PTY-087  (REQ-PTY-077)
Exercises    : SCR-PTY-003 /pty/customer-credits?carriesCredit=true
Rule / code  : — 
Package      : F4-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW; the refusal of AC-PTY-085 is shown by the flag owner (F-N8); 4 Customers carry credit (the mock answers API-PTY-030 with carriesCredit=true by those 4)
Host data    : none
Steps        : 1. choose "Show customers with credit" «عرض العملاء ذوي الائتمان» — the route /pty/customer-credits?carriesCredit=true opens
Expected     : the carries-credit filter shows yes (seeded from the route) and the table lists the 4 Customers
Test data    : count 4 (AC); Customer identities are placeholders
<!-- TC:TC-PTY-151:END -->
<!-- SUB:INT-FLOW:END -->
