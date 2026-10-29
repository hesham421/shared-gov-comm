<!-- source: PHASE:TEST-PLAN-FE / SUB:UI-FLOWS -->
<!-- context: TEST-PLAN-FE-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-007, AC-PTY-013, AC-PTY-018, AC-PTY-021, AC-PTY-023, AC-PTY-036, AC-PTY-055, AC-PTY-056, AC-PTY-057, AC-PTY-077, AC-PTY-078, AC-PTY-080, AC-PTY-088, AC-PTY-096, AC-PTY-097, AC-PTY-098, AC-PTY-101, AC-PTY-102, AC-PTY-106, AC-PTY-107, AC-PTY-109, AC-PTY-111, AC-PTY-114, AC-PTY-115, AC-PTY-116, AC-PTY-117, AC-PTY-122, AC-PTY-123, AC-PTY-132, AC-PTY-134, REQ-PTY-005, REQ-PTY-010, REQ-PTY-013, REQ-PTY-016, REQ-PTY-018, REQ-PTY-030, REQ-PTY-047, REQ-PTY-048, REQ-PTY-049, REQ-PTY-069, REQ-PTY-070, REQ-PTY-072, REQ-PTY-078, REQ-PTY-085, REQ-PTY-086, REQ-PTY-087, REQ-PTY-090, REQ-PTY-091, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-098, REQ-PTY-100, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-108, REQ-PTY-109, REQ-PTY-113, SCR-PTY-001, SCR-PTY-002, SCR-PTY-003, SCR-PTY-004, SCR-PTY-005 -->
<!-- SUB:UI-FLOWS:START traces=REQ-PTY-005,REQ-PTY-010,REQ-PTY-013,REQ-PTY-016,REQ-PTY-018,REQ-PTY-030,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-078,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-090,REQ-PTY-091,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-108,REQ-PTY-109,REQ-PTY-113,AC-PTY-007,AC-PTY-013,AC-PTY-018,AC-PTY-021,AC-PTY-023,AC-PTY-036,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-077,AC-PTY-078,AC-PTY-080,AC-PTY-088,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-101,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-122,AC-PTY-123,AC-PTY-132,AC-PTY-134,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005 -->
### UI-FLOWS — per-screen behaviour

<!-- TC:TC-PTY-122:START traces=AC-PTY-007,REQ-PTY-005,SCR-PTY-001 -->
### TC-PTY-122 — Quick register from a sale marks one input mandatory
Derived from : AC-PTY-007  (REQ-PTY-005)
Exercises    : SCR-PTY-001 Quick register sub-view (`CustomerQuickRegister`, opened in place from a sale)
Rule / code  : RULE-PTY-001 → — (the one mandatory name group)
Package      : F3-SCR-PTY-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier session holding PERM_PTY_CUSTOMER_CREATE; an open sale (test host) showing the Quick register; the mock answers API-PTY-001 with 201
Host data    : none
Steps        : 1. open the Quick register from the sale · 2. count the inputs marked mandatory · 3. enter nameEn "Walid Hassan" only · 4. Save «حفظ»
Expected     : step 2 → exactly 1 mandatory mark (the name group) · step 4 → one API-PTY-001 request whose body carries nameEn "Walid Hassan" and no customerGroupCode; the Customer is saved (the sub-view reports success to its host)
Test data    : nameEn "Walid Hassan" (AC)
<!-- TC:TC-PTY-122:END -->

<!-- TC:TC-PTY-123:START traces=AC-PTY-013,REQ-PTY-010,SCR-PTY-001 -->
### TC-PTY-123 — Duplicate email shown on the email field with the open-party action
Derived from : AC-PTY-013  (REQ-PTY-010)
Exercises    : SCR-PTY-001 /pty/customers/new
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN
Package      : F2-SCR-PTY-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a session holding PERM_PTY_CUSTOMER_VIEW and _CREATE; active Party P-000120 holds email "nour@example.com"; the mock answers API-PTY-001 with 409 PTY-409-EMAIL-TAKEN and the RULE-PTY-002 text filled with P-000120
Host data    : none
Steps        : 1. open /pty/customers/new · 2. enter a name `<nameEn>` and email "Nour@Example.com" · 3. Save «حفظ» · 4. choose "Open that party" «فتح ذلك الطرف»
Expected     : step 3 → no navigation; inline on email: ar «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000120). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · en «This email is already registered to another active party (P-000120). Open that party instead of creating a new one.»; the typed values are kept; no address call is sent · step 4 → one API-PTY-026 request with email "Nour@Example.com" and isActive=true; the mock returns Customer 5001 of P-000120 (email "nour@example.com"); the route becomes /pty/customers/5001 (EMAIL-HOLDER-QUERY, ADR-PTY-043 — F-N3)
Test data    : email "Nour@Example.com", code P-000120 (AC); `<nameEn>` placeholder
<!-- TC:TC-PTY-123:END -->

<!-- TC:TC-PTY-124:START traces=AC-PTY-021,REQ-PTY-016,SCR-PTY-002 -->
### TC-PTY-124 — Supplier entry needs one name only
Derived from : AC-PTY-021  (REQ-PTY-016)
Exercises    : SCR-PTY-002 /pty/suppliers/new
Rule / code  : RULE-PTY-001 → — (the one mandatory name group)
Package      : F3-SCR-PTY-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_VIEW and _CREATE; the mock answers API-PTY-006 with 201 (isActiveFl true)
Host data    : none
Steps        : 1. open /pty/suppliers/new · 2. count the inputs marked mandatory · 3. enter nameEn "Delta Imports" only · 4. Save «حفظ»
Expected     : step 2 → exactly 1 mandatory mark (the name group) · step 4 → API-PTY-006 body carries nameEn "Delta Imports" and no partyId; the route is replaced by /pty/suppliers/:supplierId of the response, showing the Supplier active
Test data    : nameEn "Delta Imports" (AC)
<!-- TC:TC-PTY-124:END -->

<!-- TC:TC-PTY-125:START traces=AC-PTY-055,REQ-PTY-047,SCR-PTY-001 -->
### TC-PTY-125 — Deactivating the walk-in Customer refused on the record
Derived from : AC-PTY-055  (REQ-PTY-047)
Exercises    : SCR-PTY-001 /pty/customers/:customerId
Rule / code  : RULE-PTY-010 → PTY-422-WALK-IN-DEACTIVATE
Package      : F2-SCR-PTY-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW and _DELETE; the walk-in Customer 5000; the mock answers API-PTY-004 with 422 PTY-422-WALK-IN-DEACTIVATE
Host data    : none
Steps        : 1. open /pty/customers/5000 · 2. Deactivate and confirm
Expected     : banner on the record: ar «العميل النقدي لازم لنقطة البيع ولا يمكن إيقافه.» · en «The walk-in customer is required by the point of sale and cannot be deactivated.» · the record still shows the Customer active; no toast of RULE-PTY-006
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-125:END -->

<!-- TC:TC-PTY-126:START traces=AC-PTY-056,REQ-PTY-048,SCR-PTY-003 -->
### TC-PTY-126 — Credit limit on the walk-in Customer stopped on the form
Derived from : AC-PTY-056  (REQ-PTY-048)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : RULE-PTY-011 → PTY-422-WALK-IN-CREDIT
Package      : F3-SCR-PTY-003
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW and _UPDATE; the mock answers API-PTY-016 for Customer 5000 with isWalkInFl true and both credit values empty
Host data    : none
Steps        : 1. open /pty/customer-credits/5000 · 2. enter credit limit 1000 · 3. Save «حفظ»
Expected     : form-level message ar «لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان.» · en «The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit.»; no API-PTY-017 request is sent; the shown standing is unchanged
Test data    : customerId 5000, creditLimit 1000 (AC)
<!-- TC:TC-PTY-126:END -->

<!-- TC:TC-PTY-127:START traces=AC-PTY-057,REQ-PTY-049,SCR-PTY-001 -->
### TC-PTY-127 — "Make supplier" on the walk-in Customer refused
Derived from : AC-PTY-057  (REQ-PTY-049)
Exercises    : SCR-PTY-001 /pty/customers/:customerId
Rule / code  : RULE-PTY-012 → PTY-422-WALK-IN-SUPPLIER
Package      : F2-SCR-PTY-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW and PERM_PTY_SUPPLIER_CREATE; the walk-in Customer 5000; the mock answers API-PTY-006 with 422 PTY-422-WALK-IN-SUPPLIER
Host data    : none
Steps        : 1. open /pty/customers/5000 · 2. choose "Make supplier" «إضافة كمورّد» and confirm
Expected     : API-PTY-006 body is {partyId} only · banner on the record: ar «لا يمكن إضافة صفة المورّد إلى العميل النقدي.» · en «The walk-in customer cannot also be a supplier.» · the route stays /pty/customers/5000 (no navigation to Suppliers)
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-127:END -->

<!-- TC:TC-PTY-128:START traces=AC-PTY-077,REQ-PTY-069,SCR-PTY-003 -->
### TC-PTY-128 — Customer credit list filtered on carries-credit
Derived from : AC-PTY-077  (REQ-PTY-069)
Exercises    : SCR-PTY-003 /pty/customer-credits
Rule / code  : — 
Package      : F2-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW; 4 Customers carry a credit limit or payment terms and 20 carry neither (the mock answers API-PTY-030 with carriesCredit=true by those 4)
Host data    : none
Steps        : 1. open /pty/customer-credits · 2. set carries-credit = yes and search
Expected     : the request carries carriesCredit=true and the route's search parameters mirror it · the table shows exactly the 4 Customers, each with its credit limit and payment terms columns
Test data    : counts 4 / 20 (AC); Customer identities are placeholders
<!-- TC:TC-PTY-128:END -->

<!-- TC:TC-PTY-129:START traces=AC-PTY-078,REQ-PTY-070,SCR-PTY-001 -->
### TC-PTY-129 — No credit menu, field or filter while the flag is off
Derived from : AC-PTY-078  (REQ-PTY-070)
Exercises    : SCR-PTY-001 /pty/customers/:customerId and the Party menu
Rule / code  : — 
Package      : F4-SCR-PTY-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off (application configuration); an Owner session holding every PTY permission; Customer 5001 (the mock answers API-PTY-002 without creditLimit and paymentTermDays)
Host data    : none
Steps        : 1. open the Party menu «الأطراف» · 2. open /pty/customers/5001 · 3. open the search form of /pty/customers · 4. open /pty/customer-credits
Expected     : step 1 → Customers «العملاء» and Suppliers «الموردون» only — 0 credit entries, no Credit «الائتمان» group · step 2 → 0 credit fields, no "Edit credit" «تعديل الائتمان» · step 3 → 0 credit filters · step 4 → the route is not mounted (not-found / home)
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-129:END -->

<!-- TC:TC-PTY-130:START traces=AC-PTY-088,REQ-PTY-078,SCR-PTY-003 -->
### TC-PTY-130 — Clear credit empties the limit and the payment terms
Derived from : AC-PTY-088  (REQ-PTY-078)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : — 
Package      : F2-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW and _UPDATE; Customer 5001 with credit limit 50000 and payment terms 45; the mock answers API-PTY-018 with both values null
Host data    : none
Steps        : 1. open /pty/customer-credits/5001 · 2. choose "Clear credit" «مسح الائتمان» · 3. confirm
Expected     : step 2 → confirmation ar «سيُمسح حد الائتمان ومدة السداد لهذا العميل. متابعة؟» · en "The credit limit and payment terms of this customer will be cleared. Continue?" · step 3 → one API-PTY-018 call; credit limit reads «لا ائتمان» / "No credit", payment terms empty. The dependency count is backend TC-PTY-103
Test data    : customerId 5001, 50000, 45 (AC)
<!-- TC:TC-PTY-130:END -->

<!-- TC:TC-PTY-131:START traces=AC-PTY-096,REQ-PTY-085,SCR-PTY-003 -->
### TC-PTY-131 — Credit standing shown to a Seller/Cashier read-only
Derived from : AC-PTY-096  (REQ-PTY-085)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : — 
Package      : F2-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; a Seller/Cashier session holding PERM_PTY_CUSTOMER_CREDIT_VIEW only; the mock answers API-PTY-016 for Customer 5001 with creditLimit 50000, openExposure 42000, availableCredit 8000, exposureAvailable true
Host data    : none
Steps        : 1. open /pty/customer-credits/5001
Expected     : the screen shows credit limit 50000, open exposure 42000 and available credit 8000; the credit form is read-only with no Save and no Clear credit
Test data    : values of the AC
<!-- TC:TC-PTY-131:END -->

<!-- TC:TC-PTY-132:START traces=AC-PTY-097,REQ-PTY-086,SCR-PTY-005 -->
### TC-PTY-132 — Owner saves WARN as the overrun action
Derived from : AC-PTY-097  (REQ-PTY-086)
Exercises    : SCR-PTY-005 /pty/credit-settings
Rule / code  : — 
Package      : F2-SCR-PTY-005
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_SETTINGS_VIEW and _UPDATE; the mock answers API-PTY-024 with BLOCK_WITH_OVERRIDE and API-PTY-025 with WARN
Host data    : none
Steps        : 1. open /pty/credit-settings · 2. select WARN «تنبيه فقط» · 3. Save «حفظ»
Expected     : the API-PTY-025 body is {"creditOverrunActionCode": "WARN"} (isDefault never sent) · after the save the screen shows WARN selected. The next credit check carrying WARN is backend TC-PTY-109
Test data    : WARN, BLOCK_WITH_OVERRIDE (AC; closed lookup labels as seeded)
<!-- TC:TC-PTY-132:END -->

<!-- TC:TC-PTY-133:START traces=AC-PTY-098,REQ-PTY-087,SCR-PTY-005 -->
### TC-PTY-133 — BLOCK_WITH_OVERRIDE preselected while nothing is saved
Derived from : AC-PTY-098  (REQ-PTY-087)
Exercises    : SCR-PTY-005 /pty/credit-settings
Rule / code  : — 
Package      : F3-SCR-PTY-005
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` just turned on; no setting saved — the mock answers API-PTY-024 with BLOCK_WITH_OVERRIDE and isDefault true; an Owner session holding PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. open /pty/credit-settings
Expected     : the two-option group shows BLOCK_WITH_OVERRIDE «منع مع إمكانية الموافقة» / "Block with approval" selected, with the note «القيمة الافتراضية» / "Default value"
Test data    : values of the AC
<!-- TC:TC-PTY-133:END -->

<!-- TC:TC-PTY-134:START traces=AC-PTY-101,REQ-PTY-090,SCR-PTY-005 -->
### TC-PTY-134 — Seller/Cashier cannot reach the overrun setting
Derived from : AC-PTY-101  (REQ-PTY-090)
Exercises    : SCR-PTY-005 /pty/credit-settings
Rule / code  : — (route guard; the server would answer PTY-403-FORBIDDEN — backend TC-PTY-113)
Package      : F4-SCR-PTY-005
Scenario     : PERMISSION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; a Seller/Cashier session without PERM_PTY_CREDIT_SETTINGS_UPDATE (and, per SRS B4, without _VIEW); the tenant setting is BLOCK_WITH_OVERRIDE
Host data    : none
Steps        : 1. open the Party menu · 2. deep-link to /pty/credit-settings
Expected     : step 1 → no Credit control settings «إعدادات ضبط الائتمان» entry · step 2 → the unauthorized page; no Save offered and no API-PTY-025 request sent; the setting read by an Owner stays BLOCK_WITH_OVERRIDE (F-N7)
Test data    : values of the AC
<!-- TC:TC-PTY-134:END -->

<!-- TC:TC-PTY-135:START traces=AC-PTY-102,REQ-PTY-091,SCR-PTY-004 -->
### TC-PTY-135 — Request approval from a sale's prefilled form
Derived from : AC-PTY-102  (REQ-PTY-091)
Exercises    : SCR-PTY-004 /pty/credit-overrides/new
Rule / code  : — 
Package      : F2-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; a Seller/Cashier session holding PERM_PTY_CREDIT_OVERRIDE_VIEW, _CREATE and PERM_PTY_CUSTOMER_VIEW; Customer 5001 with credit limit 50000 and open exposure 49000; the mock answers API-PTY-019 with statusCode PENDING, customerFk 5001, saleAmount 2000, creditLimitAtRequest 50000, openExposure 49000, creditCheckResultCode OVER_LIMIT, sourceDocumentRef "POS-1-000455", createdBy the Seller/Cashier
Host data    : none
Steps        : 1. open /pty/credit-overrides/new?customerId=5001&saleAmount=2000&sourceDocumentRef=POS-1-000455 · 2. choose "Request approval" «طلب موافقة»
Expected     : step 1 → the form is prefilled (Customer 5001 labelled by its code and name) · step 2 → one API-PTY-019 request with {customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000455"}; the route is replaced by /pty/credit-overrides/:requestId showing status Pending «معلّق», Customer 5001, sale amount 2000, credit limit at request 50000, open exposure 49000, result Over limit «تجاوز الحد», the sale reference and the Seller/Cashier as requester
Test data    : values of the AC
<!-- TC:TC-PTY-135:END -->

<!-- TC:TC-PTY-136:START traces=AC-PTY-106,REQ-PTY-094,SCR-PTY-004 -->
### TC-PTY-136 — Owner approves a PENDING request
Derived from : AC-PTY-106  (REQ-PTY-094)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F2-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; PENDING request CO-000031; the mock answers API-PTY-021 with statusCode APPROVED, decidedBy the Owner, decidedAt 2026-11-02 10:17
Host data    : none
Steps        : 1. open the Detail of CO-000031 · 2. Approve «موافقة» and confirm
Expected     : one API-PTY-021 call with no docNo · the Detail refreshes to status Approved «موافَق عليه», decided at 2026-11-02 10:17; the decision panel is gone. The decider's name is TC-PTY-152
Test data    : CO-000031, 2026-11-02 10:17 (AC)
<!-- TC:TC-PTY-136:END -->

<!-- TC:TC-PTY-137:START traces=AC-PTY-107,REQ-PTY-095,SCR-PTY-004 -->
### TC-PTY-137 — Owner rejects a PENDING request with a reason
Derived from : AC-PTY-107  (REQ-PTY-095)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F2-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; PENDING request CO-000032; the mock answers API-PTY-022 with statusCode REJECTED and decisionReason "Overdue invoices"
Host data    : none
Steps        : 1. open the Detail of CO-000032 · 2. enter the reason "Overdue invoices" · 3. Reject «رفض» and confirm
Expected     : the API-PTY-022 body is {"decisionReason": "Overdue invoices"} (no docNo) · the Detail shows status Rejected «مرفوض» and the reason "Overdue invoices"; the decision panel is gone
Test data    : CO-000032, "Overdue invoices" (AC)
<!-- TC:TC-PTY-137:END -->

<!-- TC:TC-PTY-138:START traces=AC-PTY-109,REQ-PTY-096,SCR-PTY-004 -->
### TC-PTY-138 — Cancel by someone other than the requester refused on the panel
Derived from : AC-PTY-109  (REQ-PTY-096)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : RULE-PTY-029 → PTY-403-NOT-REQUESTER
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW, _UPDATE and _DELETE (so the Detail of any request loads on the delivered backend — REQ-PTY-102); PENDING request CO-000033 created by Seller/Cashier S1; the server (or the mock) answers API-PTY-023 with 403 PTY-403-NOT-REQUESTER
Host data    : none
Steps        : 1. as the Owner, open the Detail of CO-000033 · 2. "Cancel request" «إلغاء الطلب» and confirm
Expected     : a banner on the decision panel — not the unauthorized page: ar «لا يلغي هذا الطلب إلا من قدّمه.» · en «Only the requester can cancel this request.» · the Detail still shows status Pending «معلّق»
Test data    : CO-000033, S1 (AC); the Owner stands for "a user other than the requester" (F-N4 removed)
<!-- TC:TC-PTY-138:END -->

<!-- TC:TC-PTY-139:START traces=AC-PTY-111,REQ-PTY-098,SCR-PTY-004 -->
### TC-PTY-139 — Approval of a request rejected meanwhile refused and refreshed
Derived from : AC-PTY-111  (REQ-PTY-098)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; the Detail of CO-000032 loaded while PENDING; CO-000032 then rejected (its statusCode is REJECTED when the approval reaches the server — F-N5); the mock answers API-PTY-021 with 409 PTY-409-REQUEST-NOT-PENDING and the refetch of API-PTY-020 with REJECTED
Host data    : none
Steps        : 1. Approve «موافقة» and confirm
Expected     : a banner on the decision panel: ar «هذا الطلب لم يعد معلّقاً (الحالة: مرفوض).» · en «This request is no longer pending (status: Rejected).» · the request is re-read: status Rejected «مرفوض», the decision panel is gone
Test data    : CO-000032 (AC)
<!-- TC:TC-PTY-139:END -->

<!-- TC:TC-PTY-140:START traces=AC-PTY-114,REQ-PTY-100,SCR-PTY-004 -->
### TC-PTY-140 — Rejection without a reason stopped on the reason field
Derived from : AC-PTY-114  (REQ-PTY-100)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : RULE-PTY-027 → PTY-422-REJECTION-REASON-REQUIRED
Package      : F3-SCR-PTY-004
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; PENDING request CO-000034
Host data    : none
Steps        : 1. open the Detail of CO-000034 · 2. leave the reason empty · 3. Reject «رفض»
Expected     : inline on the reason field: ar «أدخل سبب الرفض.» · en «Enter the reason for the rejection.» · no API-PTY-022 request sent; the Detail still shows status Pending «معلّق»
Test data    : CO-000034, empty reason (AC)
<!-- TC:TC-PTY-140:END -->

<!-- TC:TC-PTY-141:START traces=AC-PTY-115,REQ-PTY-101,SCR-PTY-004 -->
### TC-PTY-141 — Request list keeps the server's order
Derived from : AC-PTY-115  (REQ-PTY-101)
Exercises    : SCR-PTY-004 /pty/credit-overrides
Rule / code  : — 
Package      : F2-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; requests CO-000031 (APPROVED, 2026-11-02), CO-000035 (PENDING, 2026-11-01), CO-000036 (PENDING, 2026-11-03) — the mock answers API-PTY-032 in the order PENDING first then newest
Host data    : none
Steps        : 1. open /pty/credit-overrides with no filter
Expected     : the API-PTY-032 request carries no sort parameter · rows show CO-000036, CO-000035, CO-000031 in that order
Test data    : values of the AC
<!-- TC:TC-PTY-141:END -->

<!-- TC:TC-PTY-142:START traces=AC-PTY-116,REQ-PTY-102,SCR-PTY-004 -->
### TC-PTY-142 — Requester without UPDATE sees own requests and no decision commands
Derived from : AC-PTY-116  (REQ-PTY-102)
Exercises    : SCR-PTY-004 /pty/credit-overrides
Rule / code  : — 
Package      : SEC-FE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: `b2bCredit` on; Seller/Cashier S1 created CO-000035 and S2 created CO-000036; S1's session holds PERM_PTY_CREDIT_OVERRIDE_VIEW, _CREATE, _DELETE but not _UPDATE; the mock answers API-PTY-032 for S1 with CO-000035 only (server-side visibility)
Host data    : none
Steps        : 1. as S1, open /pty/credit-overrides with no filter · 2. open CO-000035
Expected     : step 1 → the list shows CO-000035 only; the request carries no requester filter added by the screen · step 2 → Approve and Reject are not offered
Test data    : values of the AC
<!-- TC:TC-PTY-142:END -->

<!-- TC:TC-PTY-143:START traces=AC-PTY-117,REQ-PTY-103,SCR-PTY-004 -->
### TC-PTY-143 — Request Detail shows the decision outcome
Derived from : AC-PTY-117  (REQ-PTY-103)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F4-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW; request CO-000031 for Customer 5001, sale amount 2000, approved by the Owner at 2026-11-02 10:17 (the mock answers API-PTY-020 accordingly) — F-N6
Host data    : none
Steps        : 1. open /pty/credit-overrides/:requestId of CO-000031
Expected     : the read-only request fields show docNo CO-000031, status Approved «موافَق عليه», Customer 5001 (code and name), sale amount 2000 and decided at 2026-11-02 10:17; no decision panel (not PENDING)
Test data    : values of the AC
<!-- TC:TC-PTY-143:END -->

<!-- TC:TC-PTY-144:START traces=AC-PTY-122,REQ-PTY-108,SCR-PTY-003 -->
### TC-PTY-144 — Credit history pane lists changes newest first
Derived from : AC-PTY-122  (REQ-PTY-108)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId (inline credit history pane)
Rule / code  : — 
Package      : F4-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW; Customer 5001 had creditLimit changed on 2026-10-01 and 2026-11-04 and paymentTermDays changed on 2026-10-15 (the mock answers API-PTY-031 newest first)
Host data    : none
Steps        : 1. open /pty/customer-credits/5001 · 2. read the credit history pane «سجل الائتمان»
Expected     : 3 rows in the order 2026-11-04, 2026-10-15, 2026-10-01, each showing field, old value, new value and user; the pane is read-only and pages with the page body (no scroll region of its own)
Test data    : dates of the AC; old/new values are placeholders
<!-- TC:TC-PTY-144:END -->

<!-- TC:TC-PTY-145:START traces=AC-PTY-123,REQ-PTY-109,SCR-PTY-003 -->
### TC-PTY-145 — Unrecorded credit change shown and kept for retry
Derived from : AC-PTY-123  (REQ-PTY-109)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Package      : F2-SCR-PTY-003
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW and _UPDATE; Customer 5001 with credit limit `<previous creditLimit>`; the mock answers API-PTY-017 with 500 PTY-500-AUDIT-NOT-RECORDED
Host data    : none
Steps        : 1. open /pty/customer-credits/5001 · 2. enter credit limit 70000 · 3. Save «حفظ»
Expected     : a form banner: ar «تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.» · en «The change could not be recorded in the audit history, so it was not saved. Try again.» · the form keeps 70000 for retry; the shown standing still reads `<previous creditLimit>`
Test data    : customerId 5001, 70000 (AC); `<previous creditLimit>` placeholder
<!-- TC:TC-PTY-145:END -->

<!-- TC:TC-PTY-179:START traces=AC-PTY-013,REQ-PTY-010,SCR-PTY-002 -->
### TC-PTY-179 — Duplicate email held by a Supplier-only Party opens that Supplier
Derived from : AC-PTY-013  (REQ-PTY-010)
Exercises    : SCR-PTY-002 /pty/suppliers/new
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN
Package      : F2-SCR-PTY-002
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_VIEW, _CREATE and PERM_PTY_CUSTOMER_VIEW; active Party P-000204 holds email "delta@example.com" and only the Supplier role (Supplier 7004). The mock answers API-PTY-006 with 409 PTY-409-EMAIL-TAKEN (text filled with P-000204), API-PTY-026 with an empty page, and API-PTY-028 with Supplier 7004 (email "delta@example.com").
Host data    : none
Steps        : 1. open /pty/suppliers/new · 2. enter a name `<nameEn>` and email "Delta@Example.com" · 3. Save «حفظ» · 4. choose "Open that party" «فتح ذلك الطرف»
Expected     : step 3 → inline on email: ar «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000204). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · en «This email is already registered to another active party (P-000204). Open that party instead of creating a new one.» · step 4 → one API-PTY-026 request with email "Delta@Example.com" and isActive=true, then one API-PTY-028 request with the same filter; the route becomes /pty/suppliers/7004 (ADR-PTY-043)
Test data    : P-000204, Supplier 7004, "Delta@Example.com" (AC-PTY-013 / AC-PTY-136 data); `<nameEn>` placeholder
<!-- TC:TC-PTY-179:END -->

<!-- TC:TC-PTY-180:START traces=AC-PTY-036,REQ-PTY-030,SCR-PTY-001 -->
### TC-PTY-180 — One Save: the record call first, address calls after it, a failing line kept unsent
Derived from : AC-PTY-036  (REQ-PTY-030)
Exercises    : SCR-PTY-001 /pty/customers/new (CUSTOMER-SAVE + ADDRESSES-SYNC)
Rule / code  : RULE-PTY-016 → PTY-422-ADDRESS-TYPE-INVALID
Package      : F2-SCR-PTY-001
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW, _CREATE and _UPDATE. The mock answers API-PTY-001 with 201 (customerPk `<customerPk>`, partyFk `<partyFk>`). It answers the first API-PTY-011 call with 201 and the second with 422 PTY-422-ADDRESS-TYPE-INVALID (the type was deactivated meanwhile).
Host data    : none
Steps        : 1. open /pty/customers/new · 2. enter nameEn `<nameEn>` · 3. add two address lines (DELIVERY "12 Tahrir St" / BILLING "5 Nile St") in the drawer, each confirmed · 4. Save «حفظ»
Expected     : 
- The requests run strictly in order: API-PTY-001 first, then API-PTY-011 for line 1, then API-PTY-011 for line 2. No address call starts before the API-PTY-001 response.
- The route is replaced by /pty/customers/`<customerPk>`.
- Line 1 shows as saved. Line 2 stays in the draft marked unsent, with an inline message on its address type: ar «اختر نوع عنوان من القائمة.» · en «Choose an address type from the list.»
- No API-PTY-015 call is made, because the first line of a type becomes default on the server.
Test data    : DELIVERY, "12 Tahrir St" (AC-PTY-036); other values placeholders
<!-- TC:TC-PTY-180:END -->

<!-- TC:TC-PTY-181:START traces=AC-PTY-055,REQ-PTY-047,SCR-PTY-001 -->
### TC-PTY-181 — Commands disabled while the form is dirty; leaving asks to confirm
Derived from : AC-PTY-055  (REQ-PTY-047)
Exercises    : SCR-PTY-001 /pty/customers/:customerId
Rule / code  : — (dirty tracking — ADR-PTY-034 §5)
Package      : F3-SCR-PTY-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW, _UPDATE, _DELETE and PERM_PTY_SUPPLIER_CREATE; Customer 5001 loaded from the mock
Host data    : none
Steps        : 1. open /pty/customers/5001 · 2. change the mobile without saving · 3. inspect Deactivate «إيقاف» and Make supplier «إضافة كمورّد» · 4. navigate to /pty/customers · 5. cancel the prompt · 6. Save «حفظ» (mock 200) · 7. inspect the commands again
Expected     : step 3 → both commands disabled · step 4 → the prompt ar «لديك تغييرات غير محفوظة. هل تريد المغادرة؟» / en "You have unsaved changes. Leave anyway?" is shown · step 5 → the route stays /pty/customers/5001 with the typed mobile kept · step 7 → both commands enabled again
Test data    : customerId 5001; the mobile value is a placeholder
<!-- TC:TC-PTY-181:END -->

<!-- TC:TC-PTY-182:START traces=AC-PTY-080,REQ-PTY-072,SCR-PTY-003 -->
### TC-PTY-182 — Flag switched off mid-session: the credit save is refused and the credit screens close
Derived from : AC-PTY-080  (REQ-PTY-072)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED
Package      : F2-SCR-PTY-003
Scenario     : STATE · data class EDGE · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW and _UPDATE. `b2bCredit` is on when /pty/customer-credits/5001 is opened. The flag is then switched off: the mock's app-config answers false from now on, and the mock answers API-PTY-017 with 422 PTY-422-CREDIT-NOT-ENABLED.
Host data    : none
Steps        : 1. on the open screen, enter creditLimit 1000 · 2. Save «حفظ» · 3. open the Party menu · 4. deep-link to /pty/customer-credits/5001
Expected     : step 2 → form banner ar «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · en «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» and the application configuration is re-read · step 3 → no Credit «الائتمان» group · step 4 → the route is not mounted (not-found / home)
Test data    : customerId 5001, creditLimit 1000 (AC)
<!-- TC:TC-PTY-182:END -->

<!-- TC:TC-PTY-183:START traces=AC-PTY-018,REQ-PTY-013,SCR-PTY-001 -->
### TC-PTY-183 — Customer search with no match shows the empty state
Derived from : AC-PTY-018  (REQ-PTY-013)
Exercises    : SCR-PTY-001 /pty/customers
Rule / code  : — 
Package      : F2-SCR-PTY-001
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW and _CREATE; the mock answers API-PTY-026 with an empty page (totalElements 0)
Host data    : none
Steps        : 1. open /pty/customers?customerGroupCode=WHOLESALE&isActive=true
Expected     : the list shows ar «لا يوجد عملاء مطابقون» / en "No matching customers" with the action "New customer" «عميل جديد»; no error banner
Test data    : WHOLESALE (seeded), filters of AC-PTY-018
<!-- TC:TC-PTY-183:END -->

<!-- TC:TC-PTY-184:START traces=AC-PTY-117,REQ-PTY-103,SCR-PTY-004 -->
### TC-PTY-184 — An unknown request opens the not-found state
Derived from : AC-PTY-117  (REQ-PTY-103)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : PLATFORM-STD → PTY-404-CREDIT-OVERRIDE-NOT-FOUND
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW; the mock answers API-PTY-020 for id 999999 with 404 PTY-404-CREDIT-OVERRIDE-NOT-FOUND
Host data    : none
Steps        : 1. open /pty/credit-overrides/999999
Expected     : the not-found state ar «الطلب غير موجود» / en "Request not found" is shown; no decision panel and no unauthorized page
Test data    : requestId 999999 (test-local)
<!-- TC:TC-PTY-184:END -->

<!-- TC:TC-PTY-185:START traces=AC-PTY-115,REQ-PTY-101,SCR-PTY-004 -->
### TC-PTY-185 — A server failure on the request list shows the error state with Retry
Derived from : AC-PTY-115  (REQ-PTY-101)
Exercises    : SCR-PTY-004 /pty/credit-overrides
Rule / code  : PLATFORM-STD → PTY-500-INTERNAL
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; the mock answers the first API-PTY-032 call with 500 PTY-500-INTERNAL and the second with the three requests of AC-PTY-115
Host data    : none
Steps        : 1. open /pty/credit-overrides · 2. choose "Retry" «إعادة المحاولة»
Expected     : step 1 → the error state ar «تعذّر تحميل البيانات» / en "The data could not be loaded" with Retry · step 2 → rows CO-000036, CO-000035, CO-000031 in that order
Test data    : values of AC-PTY-115
<!-- TC:TC-PTY-185:END -->

<!-- TC:TC-PTY-186:START traces=AC-PTY-078,REQ-PTY-070,SCR-PTY-004 -->
### TC-PTY-186 — Credit override routes not mounted while the flag is off
Derived from : AC-PTY-078  (REQ-PTY-070)
Exercises    : SCR-PTY-004 /pty/credit-overrides, /pty/credit-overrides/new, /pty/credit-overrides/:requestId
Rule / code  : — (route guard `RequireFlag('b2bCredit')`)
Package      : F4-SCR-PTY-004
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; an Owner session holding every PTY permission
Host data    : none
Steps        : 1. deep-link to each of the three routes
Expected     : each route is not mounted (not-found / home); no API-PTY-019, 020 or 032 request is sent
Test data    : none beyond the AC
<!-- TC:TC-PTY-186:END -->

<!-- TC:TC-PTY-187:START traces=AC-PTY-078,REQ-PTY-070,SCR-PTY-005 -->
### TC-PTY-187 — Credit control settings route not mounted while the flag is off
Derived from : AC-PTY-078  (REQ-PTY-070)
Exercises    : SCR-PTY-005 /pty/credit-settings
Rule / code  : — (route guard `RequireFlag('b2bCredit')`)
Package      : F4-SCR-PTY-005
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; an Owner session holding every PTY permission
Host data    : none
Steps        : 1. deep-link to /pty/credit-settings
Expected     : the route is not mounted (not-found / home); no API-PTY-024 request is sent
Test data    : none beyond the AC
<!-- TC:TC-PTY-187:END -->

<!-- TC:TC-PTY-188:START traces=AC-PTY-023,REQ-PTY-018,SCR-PTY-002 -->
### TC-PTY-188 — A Warehouse user sees Suppliers read-only
Derived from : AC-PTY-023  (REQ-PTY-018)
Exercises    : SCR-PTY-002 /pty/suppliers, /pty/suppliers/:supplierId
Rule / code  : — 
Package      : SEC-FE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a Warehouse session holding PERM_PTY_SUPPLIER_VIEW only; the mock answers API-PTY-028 with Suppliers P-000201 and P-000202 and API-PTY-007 with Supplier 7002
Host data    : none
Steps        : 1. open /pty/suppliers with name "Delta" and active = yes · 2. open Supplier 7002 · 3. deep-link to /pty/suppliers/new
Expected     : step 1 → rows P-000201 and P-000202; no "New supplier" «مورّد جديد» · step 2 → every control read-only; no Save, Deactivate, Reactivate or Make customer; the Addresses pane has no Add, Remove or Make default · step 3 → the unauthorized page
Test data    : values of AC-PTY-023
<!-- TC:TC-PTY-188:END -->

<!-- TC:TC-PTY-189:START traces=AC-PTY-132,REQ-PTY-113,SCR-PTY-004 -->
### TC-PTY-189 — A second request for the same sale refused on the form
Derived from : AC-PTY-132  (REQ-PTY-113)
Exercises    : SCR-PTY-004 /pty/credit-overrides/new
Rule / code  : RULE-PTY-034 → PTY-409-REQUEST-OPEN-FOR-SALE
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; a Seller/Cashier session holding PERM_PTY_CREDIT_OVERRIDE_VIEW, _CREATE and PERM_PTY_CUSTOMER_VIEW; the mock answers API-PTY-019 with 409 PTY-409-REQUEST-OPEN-FOR-SALE (text filled with CO-000031)
Host data    : none
Steps        : 1. open /pty/credit-overrides/new?customerId=5001&saleAmount=2000&sourceDocumentRef=POS-1-000455 · 2. "Request approval" «طلب موافقة»
Expected     : form banner ar «لهذا البيع طلب موافقة مفتوح بالفعل (CO-000031). تابع ذلك الطلب بدلاً من إنشاء طلب جديد.» · en «This sale already has an open approval request (CO-000031). Follow that request instead of creating a new one.» · no navigation; the action "show this sale's requests" opens /pty/credit-overrides?customerId=5001&statusCode=PENDING
Test data    : values of the AC
<!-- TC:TC-PTY-189:END -->

<!-- TC:TC-PTY-190:START traces=AC-PTY-134,REQ-PTY-109,SCR-PTY-004 -->
### TC-PTY-190 — An approval the audit cannot record is shown and the request stays pending
Derived from : AC-PTY-134  (REQ-PTY-109)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; PENDING request CO-000031; the mock answers API-PTY-021 with 500 PTY-500-AUDIT-NOT-RECORDED and the refetch of API-PTY-020 with PENDING
Host data    : none
Steps        : 1. open the Detail of CO-000031 · 2. Approve «موافقة» and confirm
Expected     : a banner on the decision panel — not the generic error page: ar «تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.» · en «The change could not be recorded in the audit history, so it was not saved. Try again.» · the request is re-read and still shows Pending «معلّق» with the decision panel
Test data    : CO-000031 (AC)
<!-- TC:TC-PTY-190:END -->
<!-- SUB:UI-FLOWS:END -->
