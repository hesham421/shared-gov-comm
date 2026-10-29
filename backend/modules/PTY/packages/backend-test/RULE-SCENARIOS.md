<!-- source: PHASE:TEST-PLAN-BE / SUB:RULE-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-003, AC-PTY-004, AC-PTY-005, AC-PTY-008, AC-PTY-012, AC-PTY-013, AC-PTY-014, AC-PTY-015, AC-PTY-017, AC-PTY-027, AC-PTY-029, AC-PTY-032, AC-PTY-033, AC-PTY-034, AC-PTY-035, AC-PTY-036, AC-PTY-040, AC-PTY-042, AC-PTY-043, AC-PTY-044, AC-PTY-045, AC-PTY-046, AC-PTY-049, AC-PTY-052, AC-PTY-054, AC-PTY-055, AC-PTY-056, AC-PTY-057, AC-PTY-061, AC-PTY-062, AC-PTY-063, AC-PTY-070, AC-PTY-074, AC-PTY-075, AC-PTY-079, AC-PTY-080, AC-PTY-081, AC-PTY-082, AC-PTY-083, AC-PTY-085, AC-PTY-092, AC-PTY-093, AC-PTY-095, AC-PTY-103, AC-PTY-104, AC-PTY-106, AC-PTY-107, AC-PTY-108, AC-PTY-109, AC-PTY-110, AC-PTY-111, AC-PTY-112, AC-PTY-113, AC-PTY-114, AC-PTY-118, AC-PTY-119, AC-PTY-120, AC-PTY-123, AC-PTY-124, AC-PTY-125, AC-PTY-126, AC-PTY-127, AC-PTY-128, AC-PTY-129, AC-PTY-130, AC-PTY-131, AC-PTY-132, AC-PTY-133, AC-PTY-134, AC-PTY-137, AC-PTY-140, API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-027, API-PTY-029, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037, REQ-PTY-002, REQ-PTY-003, REQ-PTY-006, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012, REQ-PTY-022, REQ-PTY-024, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-034, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-039, REQ-PTY-040, REQ-PTY-041, REQ-PTY-044, REQ-PTY-046, REQ-PTY-047, REQ-PTY-048, REQ-PTY-049, REQ-PTY-050, REQ-PTY-053, REQ-PTY-054, REQ-PTY-055, REQ-PTY-062, REQ-PTY-066, REQ-PTY-067, REQ-PTY-071, REQ-PTY-072, REQ-PTY-073, REQ-PTY-074, REQ-PTY-076, REQ-PTY-081, REQ-PTY-082, REQ-PTY-084, REQ-PTY-091, REQ-PTY-092, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-097, REQ-PTY-098, REQ-PTY-099, REQ-PTY-100, REQ-PTY-104, REQ-PTY-105, REQ-PTY-106, REQ-PTY-109, REQ-PTY-110, REQ-PTY-111, REQ-PTY-112, REQ-PTY-113, REQ-PTY-114 -->
<!-- SUB:RULE-SCENARIOS:START traces=REQ-PTY-002,REQ-PTY-003,REQ-PTY-006,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-022,REQ-PTY-024,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-034,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-041,REQ-PTY-044,REQ-PTY-046,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-050,REQ-PTY-053,REQ-PTY-054,REQ-PTY-055,REQ-PTY-062,REQ-PTY-066,REQ-PTY-067,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-074,REQ-PTY-076,REQ-PTY-081,REQ-PTY-082,REQ-PTY-084,REQ-PTY-091,REQ-PTY-092,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-097,REQ-PTY-098,REQ-PTY-099,REQ-PTY-100,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-109,REQ-PTY-110,REQ-PTY-111,REQ-PTY-112,REQ-PTY-113,REQ-PTY-114,AC-PTY-003,AC-PTY-004,AC-PTY-005,AC-PTY-008,AC-PTY-012,AC-PTY-013,AC-PTY-014,AC-PTY-015,AC-PTY-017,AC-PTY-027,AC-PTY-029,AC-PTY-032,AC-PTY-033,AC-PTY-034,AC-PTY-035,AC-PTY-036,AC-PTY-040,AC-PTY-042,AC-PTY-043,AC-PTY-044,AC-PTY-045,AC-PTY-046,AC-PTY-049,AC-PTY-052,AC-PTY-054,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-061,AC-PTY-062,AC-PTY-063,AC-PTY-070,AC-PTY-074,AC-PTY-075,AC-PTY-079,AC-PTY-080,AC-PTY-081,AC-PTY-082,AC-PTY-083,AC-PTY-085,AC-PTY-092,AC-PTY-093,AC-PTY-095,AC-PTY-103,AC-PTY-104,AC-PTY-106,AC-PTY-107,AC-PTY-108,AC-PTY-109,AC-PTY-110,AC-PTY-111,AC-PTY-112,AC-PTY-113,AC-PTY-114,AC-PTY-118,AC-PTY-119,AC-PTY-120,AC-PTY-123,AC-PTY-124,AC-PTY-125,AC-PTY-126,AC-PTY-127,AC-PTY-128,AC-PTY-129,AC-PTY-130,AC-PTY-131,AC-PTY-132,AC-PTY-133,AC-PTY-134,AC-PTY-137,AC-PTY-140 -->
### RULE-SCENARIOS — violations, boundaries, state transitions

<!-- TC:TC-PTY-001:START traces=AC-PTY-004,REQ-PTY-003,API-PTY-002 -->
### TC-PTY-001 — Inactive Customer refused for a new document
Derived from : AC-PTY-004  (REQ-PTY-003)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id}?forNewDocument=true
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Customer 5002 of Party P-000121 with isActiveFl false; the caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5002 with forNewDocument=true (selection for a new sales invoice)
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.»
Test data    : customerId 5002, Party code P-000121 (AC)
<!-- TC:TC-PTY-001:END -->

<!-- TC:TC-PTY-002:START traces=AC-PTY-005,REQ-PTY-003,API-PTY-007 -->
### TC-PTY-002 — Inactive Supplier refused for a new document
Derived from : AC-PTY-005  (REQ-PTY-003)
Exercises    : API-PTY-007 GET /api/v1/pty/suppliers/{id}?forNewDocument=true
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Supplier 7001 of Party P-000200 with isActiveFl false; the caller (Purchasing principal) holds PERM_PTY_SUPPLIER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/suppliers/7001 with forNewDocument=true (selection for a new purchase order)
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000200 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000200 is inactive and cannot be used on a new document. Reactivate it or choose another party.»
Test data    : supplierId 7001, Party code P-000200 (AC)
<!-- TC:TC-PTY-002:END -->

<!-- TC:TC-PTY-003:START traces=AC-PTY-008,REQ-PTY-006,API-PTY-001 -->
### TC-PTY-003 — Party code taken from the core number series
Derived from : AC-PTY-008  (REQ-PTY-006)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : RULE-PTY-007 → — (assignment; no refusal)
Package      : CORE
Scenario     : STATE · data class VALID · language ALL
Preconditions: the given number series PTY_PARTY (`NumberSeriesApi`) returns P-000122 as its next value; caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with a registration carrying one name `<nameEn>` · 2. GET /api/v1/pty/customers/{customerPk of step 1}
Expected     : step 1 → 201, data.code P-000122 · step 2 → data.code P-000122; exactly one value was drawn from series PTY_PARTY
Test data    : series value P-000122 (AC); `<nameEn>` placeholder — the AC names no registration data
<!-- TC:TC-PTY-003:END -->

<!-- TC:TC-PTY-004:START traces=AC-PTY-012,REQ-PTY-009,API-PTY-001 -->
### TC-PTY-004 — Registration without any name refused
Derived from : AC-PTY-012  (REQ-PTY-009)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : RULE-PTY-001 → PTY-422-NAME-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: caller holds PERM_PTY_CUSTOMER_CREATE; the tenant's Party and Customer counts are recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameAr "" and nameEn ""
Expected     : 422 · code PTY-422-NAME-REQUIRED · messageAr «أدخل اسم الطرف بالعربية أو بالإنجليزية.» · messageEn «Enter the party name in Arabic or English.» · Party and Customer counts unchanged
Test data    : nameAr empty, nameEn empty (AC)
<!-- TC:TC-PTY-004:END -->

<!-- TC:TC-PTY-005:START traces=AC-PTY-013,REQ-PTY-010,API-PTY-001 -->
### TC-PTY-005 — Email held by another active Party refused, case ignored
Derived from : AC-PTY-013  (REQ-PTY-010)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: active Party P-000120 holds email "nour@example.com"; caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with name `<nameEn>` and email "Nour@Example.com"
Expected     : 409 · code PTY-409-EMAIL-TAKEN · messageAr «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000120). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · messageEn «This email is already registered to another active party (P-000120). Open that party instead of creating a new one.» · nothing saved (Party count unchanged). The action that opens P-000120 is the frontend's (TC-PTY-123)
Test data    : emails "nour@example.com", "Nour@Example.com", code P-000120 (AC); `<nameEn>` placeholder
<!-- TC:TC-PTY-005:END -->

<!-- TC:TC-PTY-006:START traces=AC-PTY-014,REQ-PTY-011,API-PTY-001 -->
### TC-PTY-006 — Email without "@" refused
Derived from : AC-PTY-014  (REQ-PTY-011)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : RULE-PTY-003 → PTY-422-EMAIL-FORMAT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with name `<nameEn>` and email "nour.example.com"
Expected     : 422 · code PTY-422-EMAIL-FORMAT · messageAr «صيغة البريد الإلكتروني غير صحيحة، مثال: name@example.com» · messageEn «The email format is not valid, for example: name@example.com» · nothing saved
Test data    : email "nour.example.com" (AC); `<nameEn>` placeholder
<!-- TC:TC-PTY-006:END -->

<!-- TC:TC-PTY-007:START traces=AC-PTY-015,REQ-PTY-011,API-PTY-006 -->
### TC-PTY-007 — Supplier mobile shorter than 7 digits refused
Derived from : AC-PTY-015  (REQ-PTY-011)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers
Rule / code  : RULE-PTY-004 → PTY-422-MOBILE-FORMAT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: caller holds PERM_PTY_SUPPLIER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with name `<nameEn>` and mobile "12345"
Expected     : 422 · code PTY-422-MOBILE-FORMAT · messageAr «رقم الجوال يتكون من 7 إلى 15 رقماً، ويمكن أن يبدأ بعلامة +.» · messageEn «The mobile number has 7 to 15 digits and can start with +.» · nothing saved
Test data    : mobile "12345" (AC); `<nameEn>` placeholder
<!-- TC:TC-PTY-007:END -->

<!-- TC:TC-PTY-008:START traces=AC-PTY-017,REQ-PTY-012,API-PTY-003 -->
### TC-PTY-008 — Party code cannot be changed by an update
Derived from : AC-PTY-017  (REQ-PTY-012)
Exercises    : API-PTY-003 PUT /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-007 → PTY-422-CODE-READ-ONLY
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Customer 5001 with Party code P-000120; caller holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customers/5001 with the stored editable fields and code "P-999999" · 2. GET /api/v1/pty/customers/5001
Expected     : step 1 → 422 · code PTY-422-CODE-READ-ONLY · messageAr «رقم الطرف يُنشأ تلقائياً ولا يمكن تعديله.» · messageEn «The party code is assigned by the system and cannot be changed.» · step 2 → data.code P-000120
Test data    : customerId 5001, codes P-000120 / P-999999 (AC)
<!-- TC:TC-PTY-008:END -->

<!-- TC:TC-PTY-009:START traces=AC-PTY-027,REQ-PTY-022,API-PTY-008 -->
### TC-PTY-009 — Supplier payment terms above 365 refused
Derived from : AC-PTY-027  (REQ-PTY-022)
Exercises    : API-PTY-008 PUT /api/v1/pty/suppliers/{id}
Rule / code  : RULE-PTY-008 → PTY-422-PAYMENT-TERMS-RANGE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Supplier 7002; caller (Owner) holds PERM_PTY_SUPPLIER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/suppliers/7002 with the stored fields and paymentTermDays 400 · 2. GET /api/v1/pty/suppliers/7002
Expected     : step 1 → 422 · code PTY-422-PAYMENT-TERMS-RANGE · messageAr «مدة السداد يجب أن تكون بين 0 و365 يوماً.» · messageEn «Payment terms must be between 0 and 365 days.» · step 2 → paymentTermDays unchanged
Test data    : supplierId 7002, paymentTermDays 400 (AC)
<!-- TC:TC-PTY-009:END -->

<!-- TC:TC-PTY-010:START traces=AC-PTY-029,REQ-PTY-024,API-PTY-008,API-PTY-002 -->
### TC-PTY-010 — Mobile changed from the Supplier is the Customer's mobile
Derived from : AC-PTY-029  (REQ-PTY-024)
Exercises    : API-PTY-008 PUT /api/v1/pty/suppliers/{id} · API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : — 
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000130 holds a Customer `<customerId>` and a Supplier `<supplierId>`; caller (Owner) holds PERM_PTY_SUPPLIER_UPDATE and PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. PUT /api/v1/pty/suppliers/{supplierId} with the stored fields and mobile "+966500001111" · 2. GET /api/v1/pty/customers/{customerId}
Expected     : step 1 → 200 · step 2 → 200, data.mobile "+966500001111" (one Party identity, held once)
Test data    : Party P-000130, mobile "+966500001111" (AC); role identifiers are placeholders bound to the rows of P-000130
<!-- TC:TC-PTY-010:END -->

<!-- TC:TC-PTY-011:START traces=AC-PTY-032,REQ-PTY-027,API-PTY-006 -->
### TC-PTY-011 — Second Supplier on the same Party refused
Derived from : AC-PTY-032  (REQ-PTY-027)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers (partyId)
Rule / code  : RULE-PTY-009 → PTY-409-ROLE-HELD
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Party P-000130 already holds a Supplier; caller holds PERM_PTY_SUPPLIER_CREATE; the Supplier count is recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with partyId = the identifier of Party P-000130
Expected     : 409 · code PTY-409-ROLE-HELD · messageAr «هذا الطرف له صفة مورّد بالفعل.» · messageEn «This party already holds the Supplier role.» · Supplier count unchanged
Test data    : Party P-000130 (AC)
<!-- TC:TC-PTY-011:END -->

<!-- TC:TC-PTY-012:START traces=AC-PTY-033,REQ-PTY-028,API-PTY-004 -->
### TC-PTY-012 — Party inactive once its last active role is deactivated
Derived from : AC-PTY-033  (REQ-PTY-028)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-006 → — (confirmation)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000124 holds an active Customer `<customerId>` and an inactive Supplier; caller (Owner) holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/{customerId} · 2. read the stored active flag of Party P-000124 (DBF-PTY-008)
Expected     : step 1 → 200 · step 2 → false
Test data    : Party P-000124 (AC)
<!-- TC:TC-PTY-012:END -->

<!-- TC:TC-PTY-013:START traces=AC-PTY-034,REQ-PTY-029,API-PTY-005 -->
### TC-PTY-013 — Party active again when its Customer is reactivated
Derived from : AC-PTY-034  (REQ-PTY-029)
Exercises    : API-PTY-005 PATCH /api/v1/pty/customers/{id}/activation
Rule / code  : — 
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000124 with active flag false and an inactive Customer `<customerId>`; no other active Party holds its email; caller (Owner) holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/customers/{customerId}/activation with {"isActiveFl": true} · 2. read the stored active flag of Party P-000124 (DBF-PTY-008)
Expected     : step 1 → 200, data.isActiveFl true · step 2 → true
Test data    : Party P-000124 (AC); body per ADR-PTY-031
<!-- TC:TC-PTY-013:END -->

<!-- TC:TC-PTY-014:START traces=AC-PTY-035,REQ-PTY-029,API-PTY-005 -->
### TC-PTY-014 — Reactivation refused while another active Party holds the email
Derived from : AC-PTY-035  (REQ-PTY-029)
Exercises    : API-PTY-005 PATCH /api/v1/pty/customers/{id}/activation
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: inactive Party P-000124 holds email "a@example.com" and an inactive Customer `<customerId>`; active Party P-000140 holds email "a@example.com"; caller (Owner) holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/customers/{customerId}/activation with {"isActiveFl": true} · 2. GET /api/v1/pty/customers/{customerId}
Expected     : step 1 → 409 · code PTY-409-EMAIL-TAKEN · messageAr «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000140). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · messageEn «This email is already registered to another active party (P-000140). Open that party instead of creating a new one.» · step 2 → isActiveFl false; Party P-000124 stays inactive
Test data    : Parties P-000124, P-000140, email "a@example.com" (AC)
<!-- TC:TC-PTY-014:END -->

<!-- TC:TC-PTY-015:START traces=AC-PTY-040,REQ-PTY-034,API-PTY-011 -->
### TC-PTY-015 — First active Address of a type becomes the default
Derived from : AC-PTY-040  (REQ-PTY-034)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-017 → — (system-maintained default)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000120 holds a Customer and has no active BILLING Address; caller (Owner) holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. POST /api/v1/pty/parties/{partyId of P-000120}/addresses with addressTypeCode BILLING and line1 `<line1>`
Expected     : 201 · data.addressTypeCode BILLING · data.isDefaultFl true
Test data    : Party P-000120, type BILLING (AC; seeded value); `<line1>` placeholder
<!-- TC:TC-PTY-015:END -->

<!-- TC:TC-PTY-016:START traces=AC-PTY-042,REQ-PTY-036,API-PTY-014,API-PTY-029 -->
### TC-PTY-016 — Default reassigned to the most recently updated remaining Address
Derived from : AC-PTY-042  (REQ-PTY-036)
Exercises    : API-PTY-014 DELETE /api/v1/pty/parties/{partyId}/addresses/{id} · API-PTY-029 GET /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-017 → — (system-maintained default)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000120 with active DELIVERY Addresses "1" (default), "2" last updated 2026-10-01 and "3" last updated 2026-10-20 (updatedAt fixed by the test clock at each Address's last update); caller (Owner) holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/parties/{partyId}/addresses/{id of "1"} · 2. GET /api/v1/pty/parties/{partyId}/addresses
Expected     : step 1 → 200 · step 2 → Address "3" isDefaultFl true, Address "2" isDefaultFl false, Address "1" absent
Test data    : Address codes "1", "2", "3", dates 2026-10-01, 2026-10-20 (AC)
<!-- TC:TC-PTY-016:END -->

<!-- TC:TC-PTY-017:START traces=AC-PTY-043,REQ-PTY-037,API-PTY-011 -->
### TC-PTY-017 — Address without a first line refused
Derived from : AC-PTY-043  (REQ-PTY-037)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-015 → PTY-422-ADDRESS-LINE-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a Party `<partyId>` holding a Customer; caller holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. POST /api/v1/pty/parties/{partyId}/addresses with addressTypeCode BILLING and line1 ""
Expected     : 422 · code PTY-422-ADDRESS-LINE-REQUIRED · messageAr «أدخل السطر الأول من العنوان.» · messageEn «Enter the first address line.» · nothing saved
Test data    : type BILLING, line1 empty (AC); `<partyId>` placeholder
<!-- TC:TC-PTY-017:END -->

<!-- TC:TC-PTY-018:START traces=AC-PTY-044,REQ-PTY-038,API-PTY-011 -->
### TC-PTY-018 — Address type outside the active lookup values refused
Derived from : AC-PTY-044  (REQ-PTY-038)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-016 → PTY-422-ADDRESS-TYPE-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: lookup ADDRESS_TYPE holds the seeded active values BILLING and DELIVERY only; a Party `<partyId>` holding a Customer; caller holds PERM_PTY_CUSTOMER_UPDATE
Host data    : ADDRESS_TYPE OFFICE — absent: never created, so the ADDRESS_TYPE type found by POST /api/v1/mdl/lookup-types/search has no OFFICE value, and this case makes no POST /api/v1/mdl/lookup-types/{id}/values call (RULE-PTY-016 refuses absent and inactive values alike)
Steps        : 1. POST /api/v1/pty/parties/{partyId}/addresses with addressTypeCode "OFFICE" and line1 `<line1>`
Expected     : 422 · code PTY-422-ADDRESS-TYPE-INVALID · messageAr «اختر نوع عنوان من القائمة.» · messageEn «Choose an address type from the list.» · nothing saved
Test data    : addressTypeCode "OFFICE" (AC); `<partyId>`, `<line1>` placeholders
<!-- TC:TC-PTY-018:END -->

<!-- TC:TC-PTY-019:START traces=AC-PTY-045,REQ-PTY-039,API-PTY-003,API-PTY-002 -->
### TC-PTY-019 — Customer holds exactly one customer group
Derived from : AC-PTY-045  (REQ-PTY-039)
Exercises    : API-PTY-003 PUT /api/v1/pty/customers/{id} · API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-018 → — (valid value)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Customer 5001 with customerGroupCode RETAIL; Owner holds PERM_PTY_CUSTOMER_UPDATE; the Pricing principal holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. PUT /api/v1/pty/customers/5001 with the stored fields and customerGroupCode WHOLESALE · 2. GET /api/v1/pty/customers/5001 as the Pricing principal
Expected     : step 1 → 200, data.customerGroupCode WHOLESALE · step 2 → data.customerGroupCode WHOLESALE (a single value)
Test data    : customerId 5001, RETAIL → WHOLESALE (AC; seeded values)
<!-- TC:TC-PTY-019:END -->

<!-- TC:TC-PTY-020:START traces=AC-PTY-046,REQ-PTY-040,API-PTY-003 -->
### TC-PTY-020 — Inactive customer group refused
Derived from : AC-PTY-046  (REQ-PTY-040)
Exercises    : API-PTY-003 PUT /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-018 → PTY-422-CUSTOMER-GROUP-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: lookup CUSTOMER_GROUP holds RETAIL and WHOLESALE active (seeded) and VIP inactive; an existing Customer `<customerId>`; caller holds PERM_PTY_CUSTOMER_UPDATE
Host data    : CUSTOMER_GROUP VIP — created active by the given mdl call POST /api/v1/mdl/lookup-types/{id}/values ({id} = the CUSTOMER_GROUP type found by POST /api/v1/mdl/lookup-types/search), then made inactive by DELETE /api/v1/mdl/lookup-values/{id} ({id} = the VIP value) — N4
Steps        : 1. PUT /api/v1/pty/customers/{customerId} with the stored fields and customerGroupCode "VIP" · 2. GET /api/v1/pty/customers/{customerId}
Expected     : step 1 → 422 · code PTY-422-CUSTOMER-GROUP-INVALID · messageAr «اختر مجموعة عملاء من القائمة.» · messageEn «Choose a customer group from the list.» · step 2 → customerGroupCode unchanged
Test data    : customerGroupCode "VIP" (AC); `<customerId>` placeholder
<!-- TC:TC-PTY-020:END -->

<!-- TC:TC-PTY-021:START traces=AC-PTY-049,REQ-PTY-041,API-PTY-027 -->
### TC-PTY-021 — Global search term of one character returns no Customer
Derived from : AC-PTY-049  (REQ-PTY-041)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — (term length below 2 → empty list, not an error)
Package      : SVC-API-SEARCH
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: 25 active Customers; caller (Seller/Cashier) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=n
Expected     : 200 · data is an empty list (0 customer results)
Test data    : 25 Customers, term "n" (AC); Customer names are placeholders
<!-- TC:TC-PTY-021:END -->

<!-- TC:TC-PTY-022:START traces=AC-PTY-052,REQ-PTY-044,API-PTY-033 -->
### TC-PTY-022 — Walk-in Customer created once at tenant provisioning
Derived from : AC-PTY-052  (REQ-PTY-044)
Exercises    : API-PTY-033 GET /api/v1/pty/customers/walk-in (read back) — provisioning through the given `TenantProvisioningContributor` (PTY contributor → the same ensure method)
Rule / code  : RULE-PTY-014 → — (idempotent ensure)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: a new tenant whose provisioning has not run
Host data    : none
Steps        : 1. provision the tenant through the given tenant provisioning · 2. count the tenant's stored Customers with isWalkInFl true (DBF-PTY-018) · 3. GET /api/v1/pty/customers/walk-in as a holder of PERM_PTY_CUSTOMER_VIEW
Expected     : step 2 → exactly 1 · step 3 → 200, data.isWalkInFl true, nameAr «عميل نقدي», nameEn "Walk-in customer", customerGroupCode RETAIL, isActiveFl true
Test data    : names, group (AC; SRS D-08)
<!-- TC:TC-PTY-022:END -->

<!-- TC:TC-PTY-023:START traces=AC-PTY-055,REQ-PTY-047,API-PTY-004,API-PTY-002 -->
### TC-PTY-023 — Walk-in Customer cannot be deactivated
Derived from : AC-PTY-055  (REQ-PTY-047)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-010 → PTY-422-WALK-IN-DEACTIVATE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: the walk-in Customer 5000; caller (Owner) holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/5000 · 2. GET /api/v1/pty/customers/5000
Expected     : step 1 → 422 · code PTY-422-WALK-IN-DEACTIVATE · messageAr «العميل النقدي لازم لنقطة البيع ولا يمكن إيقافه.» · messageEn «The walk-in customer is required by the point of sale and cannot be deactivated.» · step 2 → isActiveFl true; no party-role-deactivated event published
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-023:END -->

<!-- TC:TC-PTY-024:START traces=AC-PTY-056,REQ-PTY-048,API-PTY-017 -->
### TC-PTY-024 — Walk-in Customer cannot receive a credit limit
Derived from : AC-PTY-056  (REQ-PTY-048)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-011 → PTY-422-WALK-IN-CREDIT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; the walk-in Customer 5000 (credit fields empty); caller (Owner) holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5000 with creditLimit 1000 and paymentTermDays null
Expected     : 422 · code PTY-422-WALK-IN-CREDIT · messageAr «لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان.» · messageEn «The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit.» · nothing saved; no audit event recorded
Test data    : customerId 5000, creditLimit 1000 (AC)
<!-- TC:TC-PTY-024:END -->

<!-- TC:TC-PTY-025:START traces=AC-PTY-057,REQ-PTY-049,API-PTY-006 -->
### TC-PTY-025 — Supplier role refused on the walk-in Party
Derived from : AC-PTY-057  (REQ-PTY-049)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers (partyId)
Rule / code  : RULE-PTY-012 → PTY-422-WALK-IN-SUPPLIER
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: the walk-in Customer 5000; caller (Owner) holds PERM_PTY_SUPPLIER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with partyId = the partyFk of Customer 5000 ("Make supplier" / «إضافة كمورّد»)
Expected     : 422 · code PTY-422-WALK-IN-SUPPLIER · messageAr «لا يمكن إضافة صفة المورّد إلى العميل النقدي.» · messageEn «The walk-in customer cannot also be a supplier.» · no Supplier saved
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-025:END -->

<!-- TC:TC-PTY-026:START traces=AC-PTY-061,REQ-PTY-053,API-PTY-034 -->
### TC-PTY-026 — Guest details without email or mobile refused
Derived from : AC-PTY-061  (REQ-PTY-053)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : RULE-PTY-019 → PTY-422-GUEST-CONTACT-REQUIRED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: caller (storefront principal) holds PERM_PTY_CUSTOMER_CREATE; the tenant's Party count is recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with nameEn "Lina Fares", email null, mobile null, deliveryAddress {line1 `<line1>`}
Expected     : 422 · code PTY-422-GUEST-CONTACT-REQUIRED · messageAr «أدخل البريد الإلكتروني أو رقم الجوال لإتمام الطلب.» · messageEn «Enter an email or a mobile number to complete the order.» · Party count unchanged; no invitation requested
Test data    : nameEn "Lina Fares" (AC); `<line1>` placeholder (the schema requires a delivery address)
<!-- TC:TC-PTY-026:END -->

<!-- TC:TC-PTY-027:START traces=AC-PTY-062,REQ-PTY-054 -->
### TC-PTY-027 — CUSTOMER-realm user already linked is not linked twice
Derived from : AC-PTY-062  (REQ-PTY-054)
Exercises    : outbox subscriber `PtyCustomerInvitationRedeemedSubscriber` (no API — backend plan CORE, SVC-API-INT)
Rule / code  : RULE-PTY-020 → subscriber outcome REFUSED, recorded through AuditApi (no catalog code)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Customer 5001 holds secUserId 88001; Customer 5011 has a pending CUSTOMER-realm invitation
Host data    : none
Steps        : 1. deliver to the subscriber a `sec.CustomerInvitationRedeemed` outbox message (subject module PTY, subject type CUSTOMER, subject reference 5011, redeemer 88001) · 2. read the stored secUserId (DBF-PTY-017) of 5011 and of 5001 · 3. deliver the same message again (same OutboxMessage id)
Expected     : step 1 → outcome REFUSED recorded through AuditApi with messageAr «حساب العميل هذا مرتبط بعميل آخر.» · messageEn «This customer account is already linked to another customer.»; the delivery completes · step 2 → 5011 empty, 5001 88001 · step 3 → nothing changes
Test data    : customers 5001, 5011, user 88001 (AC)
<!-- TC:TC-PTY-027:END -->

<!-- TC:TC-PTY-028:START traces=AC-PTY-070,REQ-PTY-062,API-PTY-034 -->
### TC-PTY-028 — Guest matched to an inactive Customer refused without revealing it
Derived from : AC-PTY-070  (REQ-PTY-062)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : RULE-PTY-021 → PTY-422-GUEST-NOT-COMPLETED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: active Party P-000130 with email "kareem@example.com", an active Supplier and an inactive Customer; caller (storefront principal) holds PERM_PTY_CUSTOMER_CREATE; Party and Customer counts recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "kareem@example.com", deliveryAddress {line1 `<line1>`}
Expected     : 422 · code PTY-422-GUEST-NOT-COMPLETED · messageAr «تعذّر إتمام الطلب بهذه البيانات. يُرجى التواصل مع البائع.» · messageEn «The order cannot be completed with these details. Please contact the seller.» · no Customer saved or attached (counts unchanged, P-000130's Customer still inactive); no invitation requested
Test data    : Party P-000130, email "kareem@example.com" (AC); `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-028:END -->

<!-- TC:TC-PTY-029:START traces=AC-PTY-074,REQ-PTY-066,API-PTY-017 -->
### TC-PTY-029 — Negative credit limit refused
Derived from : AC-PTY-074  (REQ-PTY-066)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-022 → PTY-422-CREDIT-LIMIT-NEGATIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001; caller (Owner) holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit -100 and the stored paymentTermDays
Expected     : 422 · code PTY-422-CREDIT-LIMIT-NEGATIVE · messageAr «حد الائتمان يجب أن يكون صفراً أو أكثر.» · messageEn «The credit limit must be zero or more.» · nothing saved
Test data    : customerId 5001, creditLimit -100 (AC)
<!-- TC:TC-PTY-029:END -->

<!-- TC:TC-PTY-030:START traces=AC-PTY-075,REQ-PTY-067,API-PTY-017 -->
### TC-PTY-030 — Customer payment terms of 366 days refused
Derived from : AC-PTY-075  (REQ-PTY-067)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-008 → PTY-422-PAYMENT-TERMS-RANGE
Package      : SVC-API-CRUD
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; Customer 5001; caller (Owner) holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with the stored creditLimit and paymentTermDays 366
Expected     : 422 · code PTY-422-PAYMENT-TERMS-RANGE · messageAr «مدة السداد يجب أن تكون بين 0 و365 يوماً.» · messageEn «Payment terms must be between 0 and 365 days.» · nothing saved
Test data    : customerId 5001, paymentTermDays 366 — one above the RULE-PTY-008 limit 365 (AC)
<!-- TC:TC-PTY-030:END -->

<!-- TC:TC-PTY-031:START traces=AC-PTY-079,REQ-PTY-071,API-PTY-002 -->
### TC-PTY-031 — Customer read carries no credit fields while the flag is off
Derived from : AC-PTY-079  (REQ-PTY-071)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5001
Expected     : 200 · data has no creditLimit member and no paymentTermDays member (absent from the JSON, not null)
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-031:END -->

<!-- TC:TC-PTY-032:START traces=AC-PTY-080,REQ-PTY-072,API-PTY-017 -->
### TC-PTY-032 — Credit limit refused while the flag is off
Derived from : AC-PTY-080  (REQ-PTY-072)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; caller holds PERM_PTY_CUSTOMER_CREDIT_UPDATE; stored creditLimit (DBF-PTY-015) recorded
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit 1000 · 2. read the stored creditLimit of 5001
Expected     : step 1 → 422 · code PTY-422-CREDIT-NOT-ENABLED · messageAr «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · messageEn «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» · step 2 → unchanged
Test data    : customerId 5001, creditLimit 1000 (AC); the credit fields are written only through API-PTY-017 (API-PTY-003 excludes them)
<!-- TC:TC-PTY-032:END -->

<!-- TC:TC-PTY-033:START traces=AC-PTY-081,REQ-PTY-072,API-PTY-019 -->
### TC-PTY-033 — Credit override request refused while the flag is off
Derived from : AC-PTY-081  (REQ-PTY-072)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE; the request count is recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 500, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-CREDIT-NOT-ENABLED · messageAr «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · messageEn «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» · no request saved; no number drawn; no notification sent
Test data    : customerId 5001, saleAmount 500 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-033:END -->

<!-- TC:TC-PTY-034:START traces=AC-PTY-082,REQ-PTY-073,API-PTY-002,API-PTY-016 -->
### TC-PTY-034 — Credit data served from the next request after the flag is turned on
Derived from : AC-PTY-082  (REQ-PTY-073)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id} · API-PTY-016 GET /api/v1/pty/customer-credits/{customerId}
Rule / code  : — 
Package      : CORE
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; Owner holds every PTY permission
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5001 · 2. turn `b2bCredit` on through the given FeatureFlags (`SettingsApi`) — no PTY call · 3. GET /api/v1/pty/customers/5001 · 4. GET /api/v1/pty/customer-credits/5001
Expected     : step 1 → no credit members · step 3 → data carries the creditLimit and paymentTermDays members · step 4 → 200 (not PTY-422-CREDIT-NOT-ENABLED); no PTY setup call made between steps 2 and 3. The screen part of the AC is TC-PTY-150
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-034:END -->

<!-- TC:TC-PTY-035:START traces=AC-PTY-083,REQ-PTY-074,API-PTY-035 -->
### TC-PTY-035 — Credit check answers NOT_APPLICABLE while the flag is off
Derived from : AC-PTY-083  (REQ-PTY-074)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 999999
Expected     : 200 · data.resultCode NOT_APPLICABLE · data.approvalRequired false
Test data    : customerId 5001, saleAmount 999999 (AC)
<!-- TC:TC-PTY-035:END -->

<!-- TC:TC-PTY-036:START traces=AC-PTY-085,REQ-PTY-076,API-PTY-037 -->
### TC-PTY-036 — Switch-off answer refuses while Customers carry credit
Derived from : AC-PTY-085  (REQ-PTY-076)
Exercises    : API-PTY-037 GET /api/v1/pty/flag-dependencies/b2bCredit
Rule / code  : RULE-PTY-024 → answer message (no catalog row — the refusal is the flag owner's)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class VALID · language ALL
Preconditions: `b2bCredit` on; exactly 4 Customers carry a creditLimit or a paymentTermDays; caller (the flag owner's principal) holds PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/flag-dependencies/b2bCredit · 2. read `b2bCredit` through the given FeatureFlags
Expected     : step 1 → 200 · data.dependentCustomerCount 4 · data.switchOffAllowed false · data.messageAr «لا يمكن إيقاف الائتمان: لدى 4 من العملاء حد ائتمان أو مدة سداد. امسحها من شاشة ائتمان العملاء ثم أعد المحاولة.» · data.messageEn «Credit cannot be turned off: 4 customers still have a credit limit or payment terms. Clear them on the Customer credit screen, then try again.» · data.nextActionCode SHOW_CUSTOMERS_WITH_CREDIT · data.nextActionLabelAr «عرض العملاء ذوي الائتمان» · data.nextActionLabelEn "Show customers with credit" · step 2 → still on (PTY changes no flag)
Test data    : 4 Customers carrying credit (AC); their identities and values are placeholders
<!-- TC:TC-PTY-036:END -->

<!-- TC:TC-PTY-037:START traces=AC-PTY-092,REQ-PTY-081,API-PTY-035 -->
### TC-PTY-037 — Projected exposure equal to the limit is within the limit
Derived from : AC-PTY-092  (REQ-PTY-081)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; one registered test exposure contributor reports 45000 for 5001; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 5000
Expected     : 200 · data.projectedExposure 50000 · data.resultCode WITHIN_LIMIT
Test data    : creditLimit 50000, exposure 45000, saleAmount 5000 (AC)
<!-- TC:TC-PTY-037:END -->

<!-- TC:TC-PTY-038:START traces=AC-PTY-093,REQ-PTY-082,API-PTY-035 -->
### TC-PTY-038 — One unit above the limit is OVER_LIMIT with the overrun action
Derived from : AC-PTY-093  (REQ-PTY-082)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; a test contributor reports 45000; the tenant's credit-overrun action is BLOCK_WITH_OVERRIDE; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 5001
Expected     : 200 · data.projectedExposure 50001 · data.resultCode OVER_LIMIT · data.overrunActionCode BLOCK_WITH_OVERRIDE
Test data    : creditLimit 50000, exposure 45000, saleAmount 5001 (AC)
<!-- TC:TC-PTY-038:END -->

<!-- TC:TC-PTY-039:START traces=AC-PTY-095,REQ-PTY-084,API-PTY-035 -->
### TC-PTY-039 — Contributor silent beyond 3 seconds gives EXPOSURE_UNAVAILABLE
Derived from : AC-PTY-095  (REQ-PTY-084)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; Customer 5001; two registered test contributors — one answers at once, the other answers only after more than 3 seconds; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 100
Expected     : 200 · data.resultCode EXPOSURE_UNAVAILABLE · data.openExposure null · data.overrunActionCode present and equal to the tenant's credit-overrun action (BLOCK_WITH_OVERRIDE while none is saved — REQ-PTY-087)
Test data    : saleAmount 100, 3-second budget (AC)
<!-- TC:TC-PTY-039:END -->

<!-- TC:TC-PTY-040:START traces=AC-PTY-103,REQ-PTY-091,API-PTY-019 -->
### TC-PTY-040 — Credit override request with sale amount 0 refused
Derived from : AC-PTY-103  (REQ-PTY-091)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-032 → PTY-422-SALE-AMOUNT-NOT-POSITIVE
Package      : SVC-API-CRUD
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; a named (not walk-in) Customer `<customerId>`; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId `<customerId>`, saleAmount 0, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-SALE-AMOUNT-NOT-POSITIVE · messageAr «مبلغ البيع يجب أن يكون أكبر من صفر.» · messageEn «The sale amount must be greater than zero.» · nothing saved
Test data    : saleAmount 0 — the RULE-PTY-032 limit (AC); `<customerId>`, `<sale reference>` placeholders
<!-- TC:TC-PTY-040:END -->

<!-- TC:TC-PTY-041:START traces=AC-PTY-104,REQ-PTY-092,API-PTY-019,API-PTY-021,API-PTY-020 -->
### TC-PTY-041 — Request number from the series, read-only afterwards
Derived from : AC-PTY-104  (REQ-PTY-092)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides · API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval · API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-031 → PTY-422-DOC-NO-READ-ONLY
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; the given series PTY_CREDIT_OVERRIDE returns CO-000031 next; test clock 2026-11-02 10:15; a Customer `<customerId>` and sale amount `<saleAmount>` whose server-side credit check is not WITHIN_LIMIT; requester holds PERM_PTY_CREDIT_OVERRIDE_CREATE; an Owner holds PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides at 2026-11-02 10:15 · 2. as the Owner, PATCH …/{id}/approval with body {"docNo": "CO-999999"} · 3. GET /api/v1/pty/credit-overrides/{id}
Expected     : step 1 → 201, data.docNo CO-000031, data.docDate 2026-11-02 10:15 · step 2 → 422 · code PTY-422-DOC-NO-READ-ONLY · messageAr «رقم الطلب يُنشأ تلقائياً ولا يمكن تعديله.» · messageEn «The request number is assigned by the system and cannot be changed.» · step 3 → docNo CO-000031, statusCode PENDING (N6)
Test data    : CO-000031, CO-999999, 2026-11-02 10:15 (AC); `<customerId>`, `<saleAmount>` placeholders
<!-- TC:TC-PTY-041:END -->

<!-- TC:TC-PTY-042:START traces=AC-PTY-106,REQ-PTY-094,API-PTY-021 -->
### TC-PTY-042 — Approve a PENDING request
Derived from : AC-PTY-106  (REQ-PTY-094)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval
Rule / code  : RULE-PTY-026 → — (transition allowed)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: PENDING request CO-000031; an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE; test clock 2026-11-02 10:17
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000031}/approval with no body
Expected     : 200 · data.statusCode APPROVED · data.decidedBy the Owner's sec user identifier · data.decidedAt 2026-11-02 10:17
Test data    : CO-000031, 2026-11-02 10:17 (AC)
<!-- TC:TC-PTY-042:END -->

<!-- TC:TC-PTY-043:START traces=AC-PTY-107,REQ-PTY-095,API-PTY-022 -->
### TC-PTY-043 — Reject a PENDING request with a reason
Derived from : AC-PTY-107  (REQ-PTY-095)
Exercises    : API-PTY-022 PATCH /api/v1/pty/credit-overrides/{id}/rejection
Rule / code  : RULE-PTY-026, RULE-PTY-027 → — (transition allowed)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: PENDING request CO-000032; an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000032}/rejection with {"decisionReason": "Overdue invoices"}
Expected     : 200 · data.statusCode REJECTED · data.decisionReason "Overdue invoices" · data.decidedBy the Owner's sec user identifier · data.decidedAt set
Test data    : CO-000032, "Overdue invoices" (AC)
<!-- TC:TC-PTY-043:END -->

<!-- TC:TC-PTY-044:START traces=AC-PTY-108,REQ-PTY-096,API-PTY-023 -->
### TC-PTY-044 — Requester cancels a PENDING request
Derived from : AC-PTY-108  (REQ-PTY-096)
Exercises    : API-PTY-023 DELETE /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-026, RULE-PTY-029 → — (transition allowed)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: PENDING request CO-000033 created by Seller/Cashier S1; S1 holds PERM_PTY_CREDIT_OVERRIDE_DELETE
Host data    : none
Steps        : 1. as S1, DELETE /api/v1/pty/credit-overrides/{id of CO-000033}
Expected     : 200 · data.statusCode CANCELLED (the row is kept)
Test data    : CO-000033, S1 (AC)
<!-- TC:TC-PTY-044:END -->

<!-- TC:TC-PTY-045:START traces=AC-PTY-109,REQ-PTY-096,API-PTY-023 -->
### TC-PTY-045 — Only the requester can cancel
Derived from : AC-PTY-109  (REQ-PTY-096)
Exercises    : API-PTY-023 DELETE /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-029 → PTY-403-NOT-REQUESTER
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: PENDING request CO-000033 created by Seller/Cashier S1; Seller/Cashier S2 holds PERM_PTY_CREDIT_OVERRIDE_VIEW, _CREATE and _DELETE (preset)
Host data    : none
Steps        : 1. as S2, DELETE /api/v1/pty/credit-overrides/{id of CO-000033} · 2. as an Owner, GET /api/v1/pty/credit-overrides/{id of CO-000033}
Expected     : step 1 → 403 · code PTY-403-NOT-REQUESTER · messageAr «لا يلغي هذا الطلب إلا من قدّمه.» · messageEn «Only the requester can cancel this request.» · step 2 → statusCode PENDING
Test data    : CO-000033, S1, S2 (AC)
<!-- TC:TC-PTY-045:END -->

<!-- TC:TC-PTY-046:START traces=AC-PTY-110,REQ-PTY-097,API-PTY-036 -->
### TC-PTY-046 — Approved request used by a sale within its amount
Derived from : AC-PTY-110  (REQ-PTY-097)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → — (use allowed)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` on; APPROVED request CO-000031 for Customer 5001 with saleAmount 2000 and sourceDocumentRef "POS-1-000455"; caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000031}/use with {"customerId": 5001, "saleAmount": 1950, "usedDocumentRef": "POS-1-000455"}
Expected     : 200 · data.statusCode USED · data.usedDocumentRef "POS-1-000455"
Test data    : CO-000031, customer 5001, amounts 2000 / 1950, "POS-1-000455" (AC)
<!-- TC:TC-PTY-046:END -->

<!-- TC:TC-PTY-047:START traces=AC-PTY-111,REQ-PTY-098,API-PTY-021 -->
### TC-PTY-047 — Approval of a rejected request refused
Derived from : AC-PTY-111  (REQ-PTY-098)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: request CO-000032 with statusCode REJECTED; an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE; CREDIT_OVERRIDE_STATUS labels as seeded (REJECTED = مرفوض / Rejected)
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000032}/approval · 2. GET /api/v1/pty/credit-overrides/{id of CO-000032}
Expected     : step 1 → 409 · code PTY-409-REQUEST-NOT-PENDING · messageAr «هذا الطلب لم يعد معلّقاً (الحالة: مرفوض).» · messageEn «This request is no longer pending (status: Rejected).» · step 2 → statusCode REJECTED
Test data    : CO-000032 (AC)
<!-- TC:TC-PTY-047:END -->

<!-- TC:TC-PTY-048:START traces=AC-PTY-112,REQ-PTY-099,API-PTY-036 -->
### TC-PTY-048 — Use for an amount above the approval refused
Derived from : AC-PTY-112  (REQ-PTY-099)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; APPROVED request CO-000031 for Customer 5001 with saleAmount 2000 and sourceDocumentRef "POS-1-000455"; caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000031}/use with customerId 5001, saleAmount 2500, usedDocumentRef "POS-1-000455" · 2. GET /api/v1/pty/credit-overrides/{id of CO-000031}
Expected     : step 1 → 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000031 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000031 does not cover this sale. Check the customer and the amount, or request a new approval.» · step 2 → statusCode APPROVED
Test data    : CO-000031, customer 5001, amounts 2000 / 2500 (AC); the sale reference equals the source sale so only the amount refuses
<!-- TC:TC-PTY-048:END -->

<!-- TC:TC-PTY-049:START traces=AC-PTY-113,REQ-PTY-099,API-PTY-036 -->
### TC-PTY-049 — A used approval cannot be used again
Derived from : AC-PTY-113  (REQ-PTY-099)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; request CO-000031 with statusCode USED (for Customer 5001, sourceDocumentRef "POS-1-000455"); caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000031}/use with customerId 5001, saleAmount 100, usedDocumentRef "POS-1-000455"
Expected     : 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000031 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000031 does not cover this sale. Check the customer and the amount, or request a new approval.» · statusCode stays USED
Test data    : CO-000031, customer 5001, amount 100 (AC); the sale reference equals the source sale so only the status refuses
<!-- TC:TC-PTY-049:END -->

<!-- TC:TC-PTY-050:START traces=AC-PTY-114,REQ-PTY-100,API-PTY-022 -->
### TC-PTY-050 — Rejection without a reason refused
Derived from : AC-PTY-114  (REQ-PTY-100)
Exercises    : API-PTY-022 PATCH /api/v1/pty/credit-overrides/{id}/rejection
Rule / code  : RULE-PTY-027 → PTY-422-REJECTION-REASON-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: PENDING request CO-000034; an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000034}/rejection with {"decisionReason": ""} · 2. GET /api/v1/pty/credit-overrides/{id of CO-000034}
Expected     : step 1 → 422 · code PTY-422-REJECTION-REASON-REQUIRED · messageAr «أدخل سبب الرفض.» · messageEn «Enter the reason for the rejection.» · step 2 → statusCode PENDING
Test data    : CO-000034, empty reason (AC)
<!-- TC:TC-PTY-050:END -->

<!-- TC:TC-PTY-051:START traces=AC-PTY-118,REQ-PTY-104,API-PTY-019 -->
### TC-PTY-051 — Request refused when the sale is within the limit
Derived from : AC-PTY-118  (REQ-PTY-104)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-025 → PTY-422-OVERRIDE-NOT-NEEDED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; a test contributor reports 40000; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 5000, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-OVERRIDE-NOT-NEEDED · messageAr «لا حاجة إلى موافقة: البيع ضمن حد ائتمان العميل.» · messageEn «No approval is needed: the sale is within the customer's credit limit.» · no request saved
Test data    : creditLimit 50000, exposure 40000, saleAmount 5000 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-051:END -->

<!-- TC:TC-PTY-052:START traces=AC-PTY-119,REQ-PTY-105,API-PTY-019 -->
### TC-PTY-052 — No credit override request for the walk-in Customer
Derived from : AC-PTY-119  (REQ-PTY-105)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-033 → PTY-422-WALK-IN-NO-CREDIT-SALE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; the walk-in Customer 5000; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5000, saleAmount 300, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-WALK-IN-NO-CREDIT-SALE · messageAr «لا يمكن البيع الآجل للعميل النقدي. سجّل العميل باسمه أولاً.» · messageEn «The walk-in customer cannot buy on credit. Register the customer by name first.» · no request saved
Test data    : customerId 5000, saleAmount 300 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-052:END -->

<!-- TC:TC-PTY-053:START traces=AC-PTY-123,REQ-PTY-109,API-PTY-017,API-PTY-016 -->
### TC-PTY-053 — Credit change not saved when the audit cannot record it
Derived from : AC-PTY-123  (REQ-PTY-109)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId} · API-PTY-016 GET /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit `<previous creditLimit>`; the given audit module rejects writes (test double for the `@Audited` listener and `AuditApi`); caller (Owner) holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit 70000 and the stored paymentTermDays · 2. GET /api/v1/pty/customer-credits/5001
Expected     : step 1 → 500 · code PTY-500-AUDIT-NOT-RECORDED · messageAr «تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.» · messageEn «The change could not be recorded in the audit history, so it was not saved. Try again.» · step 2 → creditLimit `<previous creditLimit>`
Test data    : customerId 5001, creditLimit 70000 (AC); `<previous creditLimit>` placeholder
<!-- TC:TC-PTY-053:END -->

<!-- TC:TC-PTY-154:START traces=AC-PTY-124,REQ-PTY-110,API-PTY-035 -->
### TC-PTY-154 — Credit check refused for an inactive Customer
Derived from : AC-PTY-124  (REQ-PTY-110)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; Customer 5002 of Party P-000121 with isActiveFl false and creditLimit 10000; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5002 and saleAmount 100
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · no resultCode returned
Test data    : customerId 5002, P-000121, creditLimit 10000, saleAmount 100 (AC)
<!-- TC:TC-PTY-154:END -->

<!-- TC:TC-PTY-155:START traces=AC-PTY-125,REQ-PTY-110,API-PTY-019 -->
### TC-PTY-155 — Credit override request refused for an inactive Customer
Derived from : AC-PTY-125  (REQ-PTY-110)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; Customer 5002 of Party P-000121 with isActiveFl false; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE; request count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5002, saleAmount 500, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · request count unchanged; no number drawn from PTY_CREDIT_OVERRIDE
Test data    : customerId 5002, saleAmount 500 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-155:END -->

<!-- TC:TC-PTY-156:START traces=AC-PTY-126,REQ-PTY-110,API-PTY-011 -->
### TC-PTY-156 — Address refused on an inactive Party
Derived from : AC-PTY-126  (REQ-PTY-110)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Party P-000124 with isActiveFl false (its Customer and its Supplier inactive); the Owner holds PERM_PTY_CUSTOMER_UPDATE; Address count of P-000124 recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/parties/{identifier of P-000124}/addresses with addressTypeCode BILLING and line1 "5 Nile St"
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000124 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000124 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · Address count unchanged
Test data    : Party P-000124, BILLING, "5 Nile St" (AC)
<!-- TC:TC-PTY-156:END -->

<!-- TC:TC-PTY-157:START traces=AC-PTY-127,REQ-PTY-110,API-PTY-017,API-PTY-018 -->
### TC-PTY-157 — Credit save refused, credit clear allowed for an inactive Customer
Derived from : AC-PTY-127  (REQ-PTY-110)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId} · API-PTY-018 DELETE /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; Customer 5002 of Party P-000121 with isActiveFl false, creditLimit 10000, paymentTermDays 30; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5002 with creditLimit 20000 and paymentTermDays 30 · 2. DELETE /api/v1/pty/customer-credits/5002 · 3. GET /api/v1/pty/customer-credits/5002
Expected     : step 1 → 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · step 2 → 200 · step 3 → creditLimit null, paymentTermDays null
Test data    : customerId 5002, 10000, 30, 20000 (AC)
<!-- TC:TC-PTY-157:END -->

<!-- TC:TC-PTY-158:START traces=AC-PTY-128,REQ-PTY-111,API-PTY-004,API-PTY-020 -->
### TC-PTY-158 — Deactivating a Customer cancels its open requests
Derived from : AC-PTY-128  (REQ-PTY-111)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : — (system transition PENDING / APPROVED → CANCELLED)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` on; active Customer 5001 with requests CO-000040 PENDING, CO-000041 APPROVED and CO-000031 USED; the Owner holds PERM_PTY_CUSTOMER_DELETE and PERM_PTY_CREDIT_OVERRIDE_VIEW, _UPDATE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/5001 · 2. GET each of CO-000040, CO-000041, CO-000031
Expected     : step 1 → 200 · step 2 → CO-000040 CANCELLED, CO-000041 CANCELLED, CO-000031 USED; one audit row per cancelled request (entity type PTY_CREDIT_OVERRIDE_REQUEST)
Test data    : values of the AC
<!-- TC:TC-PTY-158:END -->

<!-- TC:TC-PTY-159:START traces=AC-PTY-129,REQ-PTY-111,API-PTY-036 -->
### TC-PTY-159 — An approval cancelled by the Customer's deactivation cannot be used after reactivation
Derived from : AC-PTY-129  (REQ-PTY-111)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING
Package      : SVC-API-INT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; request CO-000041 for Customer 5001 (saleAmount 2000, sourceDocumentRef "POS-1-000470") was APPROVED, Customer 5001 was then deactivated (CO-000041 CANCELLED) and reactivated; caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000041}/use with customerId 5001, saleAmount 1000, usedDocumentRef "POS-1-000470"
Expected     : 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000041 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000041 does not cover this sale. Check the customer and the amount, or request a new approval.» · CO-000041 stays CANCELLED
Test data    : CO-000041, customer 5001 (AC); amounts and reference chosen within the approval so only the status refuses
<!-- TC:TC-PTY-159:END -->

<!-- TC:TC-PTY-160:START traces=AC-PTY-130,REQ-PTY-112,API-PTY-021 -->
### TC-PTY-160 — Approval refused while the flag is off
Derived from : AC-PTY-130  (REQ-PTY-112)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval
Rule / code  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` off; PENDING request CO-000042; the Owner holds PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000042}/approval · 2. GET /api/v1/pty/credit-overrides/{id of CO-000042}
Expected     : step 1 → 422 · code PTY-422-CREDIT-NOT-ENABLED · messageAr «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · messageEn «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» · step 2 → statusCode PENDING, decidedBy and decidedAt empty
Test data    : CO-000042 (AC)
<!-- TC:TC-PTY-160:END -->

<!-- TC:TC-PTY-161:START traces=AC-PTY-131,REQ-PTY-112,API-PTY-023 -->
### TC-PTY-161 — Cancellation stays allowed while the flag is off
Derived from : AC-PTY-131  (REQ-PTY-112)
Exercises    : API-PTY-023 DELETE /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-023 → — (cancel is outside the flag rule)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; PENDING request CO-000043 created by Seller/Cashier S1; S1 holds PERM_PTY_CREDIT_OVERRIDE_DELETE
Host data    : none
Steps        : 1. as S1, DELETE /api/v1/pty/credit-overrides/{id of CO-000043}
Expected     : 200 · data.statusCode CANCELLED
Test data    : CO-000043, S1 (AC)
<!-- TC:TC-PTY-161:END -->

<!-- TC:TC-PTY-162:START traces=AC-PTY-132,REQ-PTY-113,API-PTY-019 -->
### TC-PTY-162 — Second open request for the same sale refused
Derived from : AC-PTY-132  (REQ-PTY-113)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-034 → PTY-409-REQUEST-OPEN-FOR-SALE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; Customer 5001 with creditLimit 50000 and a test contributor reporting 49000; PENDING request CO-000031 for Customer 5001 and sourceDocumentRef "POS-1-000455"; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE; request count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000455"
Expected     : 409 · code PTY-409-REQUEST-OPEN-FOR-SALE · messageAr «لهذا البيع طلب موافقة مفتوح بالفعل (CO-000031). تابع ذلك الطلب بدلاً من إنشاء طلب جديد.» · messageEn «This sale already has an open approval request (CO-000031). Follow that request instead of creating a new one.» · request count unchanged
Test data    : values of the AC
<!-- TC:TC-PTY-162:END -->

<!-- TC:TC-PTY-163:START traces=AC-PTY-133,REQ-PTY-099,API-PTY-036 -->
### TC-PTY-163 — An approval cannot be used on another sale
Derived from : AC-PTY-133  (REQ-PTY-099)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; APPROVED request CO-000031 for Customer 5001, saleAmount 2000, sourceDocumentRef "POS-1-000455"; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000031}/use with customerId 5001, saleAmount 1500, usedDocumentRef "POS-1-000460" · 2. GET /api/v1/pty/credit-overrides/{id of CO-000031}
Expected     : step 1 → 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000031 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000031 does not cover this sale. Check the customer and the amount, or request a new approval.» · step 2 → statusCode APPROVED, usedDocumentRef empty
Test data    : values of the AC
<!-- TC:TC-PTY-163:END -->

<!-- TC:TC-PTY-164:START traces=AC-PTY-134,REQ-PTY-109,API-PTY-021,API-PTY-020 -->
### TC-PTY-164 — Approval not saved when the audit cannot record it
Derived from : AC-PTY-134  (REQ-PTY-109)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval · API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; PENDING request CO-000031; the given audit module rejects writes (test double for `AuditApi.record` and the `@Audited` listener); the Owner holds PERM_PTY_CREDIT_OVERRIDE_UPDATE and _VIEW
Host data    : none
Steps        : 1. PATCH …/{id of CO-000031}/approval · 2. GET /api/v1/pty/credit-overrides/{id of CO-000031}
Expected     : step 1 → 500 · code PTY-500-AUDIT-NOT-RECORDED · messageAr «تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.» · messageEn «The change could not be recorded in the audit history, so it was not saved. Try again.» · step 2 → statusCode PENDING, decidedBy and decidedAt empty
Test data    : CO-000031 (AC)
<!-- TC:TC-PTY-164:END -->

<!-- TC:TC-PTY-168:START traces=AC-PTY-111,REQ-PTY-098,API-PTY-021 -->
### TC-PTY-168 — Two concurrent approvals: exactly one succeeds
Derived from : AC-PTY-111  (REQ-PTY-098)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval (twice, in parallel)
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING (guard: QR-PTY-033 row lock + QR-PTY-034 compare-and-set — ADR-PTY-026)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; PENDING request CO-000050; two Owner sessions O1, O2 holding PERM_PTY_CREDIT_OVERRIDE_UPDATE; CREDIT_OVERRIDE_STATUS labels as seeded
Host data    : none
Steps        : 1. two threads, released together by a barrier, send PATCH …/{id of CO-000050}/approval as O1 and as O2 · 2. GET the request
Expected     : exactly one call → 200 (statusCode APPROVED); the other → 409 · code PTY-409-REQUEST-NOT-PENDING · messageAr «هذا الطلب لم يعد معلّقاً (الحالة: موافَق عليه).» · messageEn «This request is no longer pending (status: Approved).» · step 2 → APPROVED, decidedBy = the winner; exactly one APPROVE business audit action
Test data    : CO-000050 (test-local request number)
<!-- TC:TC-PTY-168:END -->

<!-- TC:TC-PTY-169:START traces=AC-PTY-108,REQ-PTY-098,API-PTY-021,API-PTY-023 -->
### TC-PTY-169 — Approval racing a cancellation: exactly one succeeds
Derived from : AC-PTY-108  (REQ-PTY-098)
Exercises    : API-PTY-021 PATCH …/approval · API-PTY-023 DELETE /api/v1/pty/credit-overrides/{id} (in parallel)
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING (guard: row lock + compare-and-set — ADR-PTY-026)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; PENDING request CO-000051 created by Seller/Cashier S1 (PERM_PTY_CREDIT_OVERRIDE_DELETE); an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. two threads, released together by a barrier: the Owner approves CO-000051, S1 cancels it · 2. GET the request
Expected     : exactly one call → 200; the other → 409 · code PTY-409-REQUEST-NOT-PENDING with the status label of the winner's state (موافَق عليه / Approved or ملغى / Cancelled) · step 2 → the winner's status (APPROVED or CANCELLED), never both decided
Test data    : CO-000051 (test-local)
<!-- TC:TC-PTY-169:END -->

<!-- TC:TC-PTY-170:START traces=AC-PTY-113,REQ-PTY-099,API-PTY-036 -->
### TC-PTY-170 — Two sales using one approval at once: exactly one succeeds
Derived from : AC-PTY-113  (REQ-PTY-099)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use (twice, in parallel)
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING (guard: row lock + `WHERE STATUS_CODE = 'APPROVED'`)
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; APPROVED request CO-000052 for Customer 5001, saleAmount 2000, sourceDocumentRef "POS-1-000480"; two Sales & POS sessions holding PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. two threads, released together by a barrier, send PATCH …/{id of CO-000052}/use with customerId 5001, saleAmount 1500, usedDocumentRef "POS-1-000480" · 2. GET the request
Expected     : exactly one call → 200 (USED); the other → 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000052 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000052 does not cover this sale. Check the customer and the amount, or request a new approval.» · step 2 → USED once
Test data    : CO-000052 (test-local)
<!-- TC:TC-PTY-170:END -->

<!-- TC:TC-PTY-171:START traces=AC-PTY-054,REQ-PTY-046,API-PTY-033 -->
### TC-PTY-171 — Two first walk-in requests at once create one walk-in Customer
Derived from : AC-PTY-054  (REQ-PTY-046)
Exercises    : API-PTY-033 GET /api/v1/pty/customers/walk-in (twice, in parallel)
Rule / code  : RULE-PTY-014 → — (guard: UQ_PTY_CUSTOMER_IS_WALK_IN_FL (TENANT_ID) + REQUIRES_NEW re-read — ADR-PTY-026)
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: a tenant with no walk-in Customer; two sessions holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. two threads, released together by a barrier, send GET /api/v1/pty/customers/walk-in · 2. count the tenant's stored Customers with isWalkInFl true and the Parties named «عميل نقدي»
Expected     : both calls → 200 with the same customerPk · step 2 → exactly 1 walk-in Customer and exactly 1 walk-in Party (the loser's REQUIRES_NEW Party rolled back)
Test data    : none beyond the AC
<!-- TC:TC-PTY-171:END -->

<!-- TC:TC-PTY-172:START traces=AC-PTY-013,REQ-PTY-010,API-PTY-001 -->
### TC-PTY-172 — Two registrations of one email at once: exactly one succeeds
Derived from : AC-PTY-013  (REQ-PTY-010)
Exercises    : API-PTY-001 POST /api/v1/pty/customers (twice, in parallel)
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN (guard: UQ_PTY_PARTY_EMAIL (TENANT_ID, LOWER(BTRIM(EMAIL))) — ADR-PTY-026)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: no active Party holds "race@example.com"; two sessions holding PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. two threads, released together by a barrier, send POST /api/v1/pty/customers with nameEn "Race A" / "Race B" and email "race@example.com"
Expected     : exactly one call → 201; the other → 409 · code PTY-409-EMAIL-TAKEN whose messageAr / messageEn name the winner's Party code · Party count +1
Test data    : "race@example.com" (test-local)
<!-- TC:TC-PTY-172:END -->

<!-- TC:TC-PTY-173:START traces=AC-PTY-033,REQ-PTY-028,API-PTY-004,API-PTY-009 -->
### TC-PTY-173 — Customer and Supplier of one Party deactivated at once leave the Party inactive
Derived from : AC-PTY-033  (REQ-PTY-028)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-009 DELETE /api/v1/pty/suppliers/{id} (in parallel)
Rule / code  : — (guard: the Party row lock QR-PTY-001 serialises the "last active role" count)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Party P-000170 holds an active Customer `<customerId>` and an active Supplier `<supplierId>`; the Owner holds PERM_PTY_CUSTOMER_DELETE and PERM_PTY_SUPPLIER_DELETE
Host data    : none
Steps        : 1. two threads, released together by a barrier: DELETE the Customer and DELETE the Supplier · 2. read the stored active flag of P-000170 (DBF-PTY-008)
Expected     : both calls → 200 · step 2 → false (never left true by two interleaved counts); exactly two `pty.PartyRoleDeactivated` outbox events
Test data    : Party P-000170 (test-local); role identifiers are placeholders
<!-- TC:TC-PTY-173:END -->

<!-- TC:TC-PTY-174:START traces=AC-PTY-040,REQ-PTY-034,API-PTY-011 -->
### TC-PTY-174 — Two Address creates on one Party at once get distinct codes and one default
Derived from : AC-PTY-040  (REQ-PTY-034)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses (twice, in parallel)
Rule / code  : RULE-PTY-017 → — (guard: Party row lock; UQ_PTY_ADDRESS_PARTY_CODE and UQ_PTY_ADDRESS_IS_DEFAULT_FL backstops — ADR-PTY-026, ADR-PTY-040)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Party P-000120 (holding a Customer) with no active BILLING Address and highest Address code "2"; the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. two threads, released together by a barrier, send POST …/addresses with addressTypeCode BILLING and line1 "A St" / "B St" · 2. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : both calls → 201 · the two Addresses carry codes "3" and "4" · exactly one of them has isDefaultFl true
Test data    : Party P-000120, BILLING (AC; seeded value); lines are test-local
<!-- TC:TC-PTY-174:END -->

<!-- TC:TC-PTY-175:START traces=AC-PTY-120,REQ-PTY-106,API-PTY-017 -->
### TC-PTY-175 — Two concurrent credit edits chain their audit old values
Derived from : AC-PTY-120  (REQ-PTY-106)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId} (twice, in parallel)
Rule / code  : RULE-PTY-030 → — (guard: the Customer row lock QR-PTY-006)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; audit rows of entity type PTY_CUSTOMER captured; two Owner sessions holding PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. two threads, released together by a barrier, send PUT …/5001 with creditLimit 60000 and with creditLimit 70000 (paymentTermDays as stored) · 2. GET /api/v1/pty/customer-credits/5001
Expected     : both calls → 200 · exactly two creditLimit audit rows: the first old 50000 → new X, the second old X → new Y, where {X, Y} = {60000, 70000} · step 2 → creditLimit Y
Test data    : 50000, 60000, 70000 (AC base value + test-local)
<!-- TC:TC-PTY-175:END -->

<!-- TC:TC-PTY-176:START traces=AC-PTY-132,REQ-PTY-113,API-PTY-019 -->
### TC-PTY-176 — Two requests for one sale at once: exactly one is created
Derived from : AC-PTY-132  (REQ-PTY-113)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides (twice, in parallel)
Rule / code  : RULE-PTY-034 → PTY-409-REQUEST-OPEN-FOR-SALE (guard: Customer row lock + UQ_PTY_CREDIT_OVERRIDE_REQUEST_OPEN_SOURCE — ADR-PTY-042)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; Customer 5001 with creditLimit 50000 and a test contributor reporting 49000; no open request for sourceDocumentRef "POS-1-000490"; two sessions holding PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. two threads, released together by a barrier, send POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000490"
Expected     : exactly one call → 201 (PENDING); the other → 409 · code PTY-409-REQUEST-OPEN-FOR-SALE whose messages name the winner's docNo · exactly one request for (5001, "POS-1-000490")
Test data    : "POS-1-000490" (test-local)
<!-- TC:TC-PTY-176:END -->

<!-- TC:TC-PTY-178:START traces=AC-PTY-003,REQ-PTY-002,API-PTY-002 -->
### TC-PTY-178 — A Customer of another tenant reads as not found
Derived from : AC-PTY-003  (REQ-PTY-002)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : PLATFORM-STD → PTY-404-CUSTOMER-NOT-FOUND (tenant filter — ADR-PTY-035)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: two tenants T1 and T2 (`@MultiTenantOnly`); Customer 5001 exists in T1; a principal of T2 holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. as the T2 principal, GET /api/v1/pty/customers/5001
Expected     : 404 · code PTY-404-CUSTOMER-NOT-FOUND · messageAr «العميل غير موجود.» · messageEn «The customer was not found.»
Test data    : customerId 5001 (AC); tenants test-local
<!-- TC:TC-PTY-178:END -->
<!-- TC:TC-PTY-191:START traces=AC-PTY-137,REQ-PTY-114,API-PTY-019 -->
### TC-PTY-191 — Credit override request refused under WARN
Derived from : AC-PTY-137  (REQ-PTY-114)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-035 → PTY-422-OVERRIDE-NOT-REQUIRED (ADR-PTY-045)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; the tenant setting PTY_CREDIT_OVERRUN_ACTION is WARN; Customer 5001 with creditLimit 50000; a test contributor reports 49000 (the server-side check answers OVER_LIMIT); caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE; the request count and the PTY_CREDIT_OVERRIDE series value are recorded; `NotificationDispatchApi` calls captured
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000500"
Expected     : 422 · code PTY-422-OVERRIDE-NOT-REQUIRED · messageAr «لا حاجة إلى موافقة: إعداد تجاوز الائتمان هو التنبيه فقط، ويمكن إتمام البيع دون طلب.» · messageEn «No approval is needed: the credit-overrun setting is Warn only, so the sale can be completed without a request.» · request count unchanged; no number drawn from PTY_CREDIT_OVERRIDE; no notification dispatched
Test data    : WARN, customer 5001, 50000, 49000, 2000, "POS-1-000500" (AC)
<!-- TC:TC-PTY-191:END -->

<!-- TC:TC-PTY-192:START traces=AC-PTY-128,REQ-PTY-111,API-PTY-004,API-PTY-019 -->
### TC-PTY-192 — Deactivation racing a request creation leaves no open request
Derived from : AC-PTY-128  (REQ-PTY-111)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-019 POST /api/v1/pty/credit-overrides (in parallel)
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE (guard: the Customer row lock QR-PTY-006 taken by both, and the cancellation of open requests by QR-PTY-039 — ADR-PTY-041)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; active Customer 5001 of Party P-000120 with creditLimit 50000 and a test contributor reporting 49000; no request for sourceDocumentRef "POS-1-000510"; an Owner session holding PERM_PTY_CUSTOMER_DELETE and PERM_PTY_CREDIT_OVERRIDE_VIEW, _UPDATE; a Seller/Cashier session holding PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. two threads, released together by a barrier: the Owner sends DELETE /api/v1/pty/customers/5001; the Seller/Cashier sends POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000510" · 2. as the Owner, GET /api/v1/pty/credit-overrides?customerId=5001
Expected     : the deactivation → 200 in every interleaving. The creation ends in exactly one of two ways: (a) it waited and lost → 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000120 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000120 is inactive and cannot be used on a new document. Reactivate it or choose another party.», and no request for "POS-1-000510" exists; or (b) it committed first → 201, and the deactivation cancelled it. Single end state (step 2): Customer 5001 is inactive and none of its requests is PENDING or APPROVED
Test data    : customer 5001, P-000120, "POS-1-000510" (test-local reference)
<!-- TC:TC-PTY-192:END -->

<!-- TC:TC-PTY-193:START traces=AC-PTY-129,REQ-PTY-111,API-PTY-004,API-PTY-021,API-PTY-036 -->
### TC-PTY-193 — An approval and a use racing the Customer's deactivation
Derived from : AC-PTY-129  (REQ-PTY-111)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-021 PATCH …/approval · API-PTY-036 PATCH …/use (in parallel)
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING · RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING (guard: QR-PTY-039 row locks in PK order and the compare-and-set of QR-PTY-034)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; active Customer 5001; PENDING request CO-000053 and APPROVED request CO-000054 (saleAmount 2000, sourceDocumentRef "POS-1-000520"), both for Customer 5001; an Owner session O1 holding PERM_PTY_CUSTOMER_DELETE; an Owner session O2 holding PERM_PTY_CREDIT_OVERRIDE_UPDATE and _VIEW; a Sales & POS session holding PERM_PTY_CREDIT_OVERRIDE_CREATE; CREDIT_OVERRIDE_STATUS labels as seeded
Host data    : none
Steps        : 1. three threads, released together by a barrier: O1 sends DELETE /api/v1/pty/customers/5001; O2 sends PATCH …/{id of CO-000053}/approval; the Sales & POS session sends PATCH …/{id of CO-000054}/use with customerId 5001, saleAmount 1500, usedDocumentRef "POS-1-000520" · 2. GET CO-000053 and CO-000054
Expected     : the deactivation → 200. The three outcomes the backend plan states (API-PTY-004 Concurrency):
- the approval either loses → 409 · code PTY-409-REQUEST-NOT-PENDING · messageAr «هذا الطلب لم يعد معلّقاً (الحالة: ملغى).» · messageEn «This request is no longer pending (status: Cancelled).», or wins → 200 and the deactivation then cancels it; CO-000053 ends CANCELLED either way;
- the use either loses → 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000054 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000054 does not cover this sale. Check the customer and the amount, or request a new approval.», and CO-000054 ends CANCELLED;
- or the use wins → 200, and CO-000054 ends USED with usedDocumentRef "POS-1-000520" (the cancellation skips a USED request).
Never: a request both cancelled and used, or an APPROVED request left after the deactivation committed
Test data    : CO-000053, CO-000054, "POS-1-000520" (test-local)
<!-- TC:TC-PTY-193:END -->

<!-- TC:TC-PTY-194:START traces=AC-PTY-063,REQ-PTY-055,REQ-PTY-050,API-PTY-034 -->
### TC-PTY-194 — Two guest orders with one new email at once attach to one Customer
Derived from : AC-PTY-063  (REQ-PTY-055)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers (twice, in parallel)
Rule / code  : — (guard: UQ_PTY_PARTY_EMAIL, the REQUIRES_NEW creation and one re-match — ADR-PTY-026)
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: no active Party holds "race.guest@example.com"; two storefront sessions holding PERM_PTY_CUSTOMER_CREATE; `SecCustomerInvitationApi` calls captured; Party and Customer counts recorded
Host data    : none
Steps        : 1. two threads, released together by a barrier, send POST /api/v1/pty/guest-customers with nameEn "Race Guest", email "race.guest@example.com", deliveryAddress {line1 "1 Race St"} · 2. wait for both commits
Expected     : both calls → 200 with the same data.customerId; one carries data.created true, the other data.created false · Party +1, Customer +1, one DELIVERY Address · no error code is returned to either call (the loser's REQUIRES_NEW creation rolls back and its re-match attaches the order) · after commit, each call has requested its CUSTOMER-realm invitation for that Customer, addressed to "race.guest@example.com" (REQ-PTY-051, REQ-PTY-059)
Test data    : "race.guest@example.com", "Race Guest", "1 Race St" (test-local)
<!-- TC:TC-PTY-194:END -->

<!-- TC:TC-PTY-195:START traces=AC-PTY-036,REQ-PTY-030,REQ-PTY-031,REQ-PTY-034,API-PTY-011,API-PTY-013 -->
### TC-PTY-195 — An Address type change racing an Address create of the same type
Derived from : AC-PTY-036  (REQ-PTY-030)
Exercises    : API-PTY-013 PUT /api/v1/pty/parties/{partyId}/addresses/{id} · API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses (in parallel)
Rule / code  : RULE-PTY-017 → — (guard: the Party row lock QR-PTY-001; UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop — ADR-PTY-026)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Party P-000120 (holding a Customer) with active DELIVERY Addresses "1" (default) and "2", and no BILLING Address; the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. two threads, released together by a barrier: PUT …/addresses/{id of "2"} with the stored fields and addressTypeCode BILLING; POST …/addresses with addressTypeCode BILLING and line1 "5 Nile St" · 2. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : the PUT → 200 and the POST → 201 (neither is refused) · step 2 → exactly one active BILLING Address has isDefaultFl true (whichever of the two ran first), Address "1" is still the DELIVERY default, and the two BILLING Addresses carry distinct codes
Test data    : Party P-000120, BILLING, DELIVERY (seeded), "5 Nile St" (AC-PTY-036 Party; line test-local)
<!-- TC:TC-PTY-195:END -->

<!-- TC:TC-PTY-198:START traces=AC-PTY-140,REQ-PTY-110,REQ-PTY-074,API-PTY-035 -->
### TC-PTY-198 — Flag off and an inactive Customer: the credit check refuses before answering NOT_APPLICABLE
Derived from : AC-PTY-140  (REQ-PTY-110)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE · PLATFORM-STD → PTY-404-CUSTOMER-NOT-FOUND (order: load the Customer → RULE-PTY-005 → flag)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` off; Customer 5002 of Party P-000121 with isActiveFl false; no Customer 999999 in the tenant; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5002 and saleAmount 100 · 2. POST /api/v1/pty/credit-checks with customerId 999999 and saleAmount 100
Expected     : step 1 → 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · no resultCode (not NOT_APPLICABLE) · step 2 → 404 · code PTY-404-CUSTOMER-NOT-FOUND · messageAr «العميل غير موجود.» · messageEn «The customer was not found.»
Test data    : customerId 5002, P-000121, saleAmount 100 (AC); 999999 test-local
<!-- TC:TC-PTY-198:END -->
<!-- SUB:RULE-SCENARIOS:END -->
