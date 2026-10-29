<!-- source: PHASE:TEST-PLAN-BE / SUB:API-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-001, AC-PTY-002, AC-PTY-003, AC-PTY-006, AC-PTY-007, AC-PTY-009, AC-PTY-010, AC-PTY-011, AC-PTY-016, AC-PTY-018, AC-PTY-019, AC-PTY-020, AC-PTY-021, AC-PTY-022, AC-PTY-023, AC-PTY-024, AC-PTY-025, AC-PTY-026, AC-PTY-028, AC-PTY-030, AC-PTY-031, AC-PTY-036, AC-PTY-037, AC-PTY-038, AC-PTY-039, AC-PTY-041, AC-PTY-045, AC-PTY-047, AC-PTY-048, AC-PTY-050, AC-PTY-051, AC-PTY-053, AC-PTY-054, AC-PTY-058, AC-PTY-059, AC-PTY-060, AC-PTY-063, AC-PTY-064, AC-PTY-065, AC-PTY-066, AC-PTY-067, AC-PTY-068, AC-PTY-069, AC-PTY-071, AC-PTY-072, AC-PTY-073, AC-PTY-076, AC-PTY-077, AC-PTY-084, AC-PTY-086, AC-PTY-088, AC-PTY-089, AC-PTY-090, AC-PTY-091, AC-PTY-094, AC-PTY-096, AC-PTY-097, AC-PTY-098, AC-PTY-099, AC-PTY-100, AC-PTY-101, AC-PTY-102, AC-PTY-105, AC-PTY-115, AC-PTY-116, AC-PTY-117, AC-PTY-120, AC-PTY-121, AC-PTY-122, AC-PTY-135, AC-PTY-136, AC-PTY-138, AC-PTY-139, API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-037, API-PTY-038, REQ-PTY-001, REQ-PTY-002, REQ-PTY-004, REQ-PTY-005, REQ-PTY-007, REQ-PTY-008, REQ-PTY-012, REQ-PTY-013, REQ-PTY-014, REQ-PTY-015, REQ-PTY-016, REQ-PTY-017, REQ-PTY-018, REQ-PTY-019, REQ-PTY-020, REQ-PTY-021, REQ-PTY-023, REQ-PTY-025, REQ-PTY-026, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-035, REQ-PTY-039, REQ-PTY-041, REQ-PTY-042, REQ-PTY-043, REQ-PTY-045, REQ-PTY-046, REQ-PTY-050, REQ-PTY-051, REQ-PTY-052, REQ-PTY-055, REQ-PTY-056, REQ-PTY-057, REQ-PTY-058, REQ-PTY-059, REQ-PTY-060, REQ-PTY-061, REQ-PTY-063, REQ-PTY-064, REQ-PTY-065, REQ-PTY-068, REQ-PTY-069, REQ-PTY-075, REQ-PTY-076, REQ-PTY-078, REQ-PTY-079, REQ-PTY-080, REQ-PTY-083, REQ-PTY-085, REQ-PTY-086, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-090, REQ-PTY-091, REQ-PTY-093, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-106, REQ-PTY-107, REQ-PTY-108 -->
<!-- SUB:API-SCENARIOS:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-004,REQ-PTY-005,REQ-PTY-007,REQ-PTY-008,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-019,REQ-PTY-020,REQ-PTY-021,REQ-PTY-023,REQ-PTY-025,REQ-PTY-026,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-035,REQ-PTY-039,REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,REQ-PTY-045,REQ-PTY-046,REQ-PTY-050,REQ-PTY-051,REQ-PTY-052,REQ-PTY-055,REQ-PTY-056,REQ-PTY-057,REQ-PTY-058,REQ-PTY-059,REQ-PTY-060,REQ-PTY-061,REQ-PTY-063,REQ-PTY-064,REQ-PTY-065,REQ-PTY-068,REQ-PTY-069,REQ-PTY-075,REQ-PTY-076,REQ-PTY-078,REQ-PTY-079,REQ-PTY-080,REQ-PTY-083,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-093,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-106,REQ-PTY-107,REQ-PTY-108,AC-PTY-001,AC-PTY-002,AC-PTY-003,AC-PTY-006,AC-PTY-007,AC-PTY-009,AC-PTY-010,AC-PTY-011,AC-PTY-016,AC-PTY-018,AC-PTY-019,AC-PTY-020,AC-PTY-021,AC-PTY-022,AC-PTY-023,AC-PTY-024,AC-PTY-025,AC-PTY-026,AC-PTY-028,AC-PTY-030,AC-PTY-031,AC-PTY-036,AC-PTY-037,AC-PTY-038,AC-PTY-039,AC-PTY-041,AC-PTY-045,AC-PTY-047,AC-PTY-048,AC-PTY-050,AC-PTY-051,AC-PTY-053,AC-PTY-054,AC-PTY-058,AC-PTY-059,AC-PTY-060,AC-PTY-063,AC-PTY-064,AC-PTY-065,AC-PTY-066,AC-PTY-067,AC-PTY-068,AC-PTY-069,AC-PTY-071,AC-PTY-072,AC-PTY-073,AC-PTY-076,AC-PTY-077,AC-PTY-084,AC-PTY-086,AC-PTY-088,AC-PTY-089,AC-PTY-090,AC-PTY-091,AC-PTY-094,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-099,AC-PTY-100,AC-PTY-101,AC-PTY-102,AC-PTY-105,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-120,AC-PTY-121,AC-PTY-122,AC-PTY-135,AC-PTY-136,AC-PTY-138,AC-PTY-139 -->
### API-SCENARIOS — endpoint behaviour, permission, paging

<!-- TC:TC-PTY-054:START traces=AC-PTY-001,REQ-PTY-001,API-PTY-026 -->
### TC-PTY-054 — Customer registered at the point of sale found once in the back-office
Derived from : AC-PTY-001  (REQ-PTY-001)
Exercises    : API-PTY-026 GET /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Customer registered from the point of sale (API-PTY-001 by the Sales & POS principal) received identifier 5001 and Party code P-000120; the Owner holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. as the Owner, GET /api/v1/pty/customers?code=P-000120
Expected     : 200 · data.totalElements 1 · data.content[0].customerPk 5001 · data.content[0].code P-000120
Test data    : customerId 5001, code P-000120 (AC)
<!-- TC:TC-PTY-054:END -->

<!-- TC:TC-PTY-055:START traces=AC-PTY-002,REQ-PTY-001,API-PTY-002,API-PTY-034,API-PTY-026 -->
### TC-PTY-055 — Storefront-attached Customer is the same back-office Customer
Derived from : AC-PTY-002  (REQ-PTY-001)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id} · API-PTY-026 GET /api/v1/pty/customers (after API-PTY-034)
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5001 with email `<email of 5001>`; the storefront has attached a guest order to it (API-PTY-034 with that email returned customerId 5001); the Owner holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5001 · 2. GET /api/v1/pty/customers?email=`<email of 5001>`
Expected     : step 1 → 200, data.customerPk 5001 · step 2 → data.totalElements 1 (no second Customer with that email in the tenant)
Test data    : customerId 5001 (AC); `<email of 5001>` placeholder — the AC names no email
<!-- TC:TC-PTY-055:END -->

<!-- TC:TC-PTY-056:START traces=AC-PTY-003,REQ-PTY-002,API-PTY-002 -->
### TC-PTY-056 — Customer read for a consuming module
Derived from : AC-PTY-003  (REQ-PTY-002)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5001 with Party code P-000120, nameEn "Nour Trading", customerGroupCode WHOLESALE, isActiveFl true; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5001
Expected     : 200 · data.customerPk 5001 · data.code P-000120 · data.nameEn "Nour Trading" · data.customerGroupCode WHOLESALE · data.isActiveFl true
Test data    : values of the AC
<!-- TC:TC-PTY-056:END -->

<!-- TC:TC-PTY-057:START traces=AC-PTY-006,REQ-PTY-004,API-PTY-001 -->
### TC-PTY-057 — Registration creates one Party and one active Customer
Derived from : AC-PTY-006  (REQ-PTY-004)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier holding PERM_PTY_CUSTOMER_CREATE; Party and Customer counts recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameAr «محمد علي» and nothing else
Expected     : 201 · data.customerPk and data.partyFk returned · data.nameAr «محمد علي» · data.isActiveFl true · Party count +1, Customer count +1, the new Customer's partyFk = the new Party's identifier
Test data    : nameAr «محمد علي» (AC)
<!-- TC:TC-PTY-057:END -->

<!-- TC:TC-PTY-058:START traces=AC-PTY-007,REQ-PTY-005,API-PTY-001 -->
### TC-PTY-058 — One name is the only input a registration needs
Derived from : AC-PTY-007  (REQ-PTY-005)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier holding PERM_PTY_CUSTOMER_CREATE (registration opened from a sale)
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameEn "Walid Hassan" only
Expected     : 201 · the Customer is saved. The count of mandatory marks on the form is TC-PTY-122
Test data    : nameEn "Walid Hassan" (AC)
<!-- TC:TC-PTY-058:END -->

<!-- TC:TC-PTY-059:START traces=AC-PTY-009,REQ-PTY-007,API-PTY-001 -->
### TC-PTY-059 — Customer without a group gets RETAIL
Derived from : AC-PTY-009  (REQ-PTY-007)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameEn "Sara Adel" and no customerGroupCode
Expected     : 201 · data.customerGroupCode RETAIL
Test data    : nameEn "Sara Adel" (AC)
<!-- TC:TC-PTY-059:END -->

<!-- TC:TC-PTY-060:START traces=AC-PTY-010,REQ-PTY-008,API-PTY-001 -->
### TC-PTY-060 — In-place registration returns identifier and code
Derived from : AC-PTY-010  (REQ-PTY-008)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier holding PERM_PTY_CUSTOMER_CREATE, registering from an open sale
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameEn "Omar Said"
Expected     : 201 · data.customerPk present · data.code present ("P-" + 6 digits). Handing them to the sale screen is TC-PTY-146
Test data    : nameEn "Omar Said" (AC)
<!-- TC:TC-PTY-060:END -->

<!-- TC:TC-PTY-061:START traces=AC-PTY-011,REQ-PTY-008,API-PTY-006 -->
### TC-PTY-061 — In-place Supplier registration returns identifier and code
Derived from : AC-PTY-011  (REQ-PTY-008)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner holding PERM_PTY_SUPPLIER_CREATE, registering from an open purchase document
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with nameAr «مؤسسة النور»
Expected     : 201 · data.supplierPk present · data.code present
Test data    : nameAr «مؤسسة النور» (AC)
<!-- TC:TC-PTY-061:END -->

<!-- TC:TC-PTY-062:START traces=AC-PTY-016,REQ-PTY-012,API-PTY-003 -->
### TC-PTY-062 — Seller/Cashier updates a Customer's mobile
Derived from : AC-PTY-016  (REQ-PTY-012)
Exercises    : API-PTY-003 PUT /api/v1/pty/customers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5001 with mobile "+201001112222"; a Seller/Cashier holding PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customers/5001 with the stored fields and mobile "+201003334444"
Expected     : 200 · data.mobile "+201003334444" · data.updatedBy the Seller/Cashier's principal
Test data    : customerId 5001, mobiles (AC)
<!-- TC:TC-PTY-062:END -->

<!-- TC:TC-PTY-063:START traces=AC-PTY-018,REQ-PTY-013,API-PTY-026 -->
### TC-PTY-063 — Customer search by group and active state, paged
Derived from : AC-PTY-018  (REQ-PTY-013)
Exercises    : API-PTY-026 GET /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: exactly 3 active Customers with customerGroupCode WHOLESALE and 5 with RETAIL; the Owner holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers?customerGroupCode=WHOLESALE&isActive=true&size=20
Expected     : 200 · data.content has 3 rows, each customerGroupCode WHOLESALE · data.totalElements 3
Test data    : counts 3 / 5, page size 20 (AC; seeded group values)
<!-- TC:TC-PTY-063:END -->

<!-- TC:TC-PTY-064:START traces=AC-PTY-019,REQ-PTY-014,API-PTY-004,API-PTY-002 -->
### TC-PTY-064 — Deactivated Customer stays readable, with the confirmation
Derived from : AC-PTY-019  (REQ-PTY-014)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-006 → confirmation (no catalog code)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Customer 5003 with Party code P-000123 (earlier sales invoices referencing it belong to Sales & POS — N11); the Owner holds PERM_PTY_CUSTOMER_DELETE and _VIEW
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/5003 · 2. GET /api/v1/pty/customers/5003
Expected     : step 1 → 200 · data {id 5003, code P-000123} · messageAr «تم إيقاف P-000123. تبقى المستندات السابقة كما هي، ولن يُعرض في المستندات الجديدة.» · messageEn «P-000123 is deactivated. Earlier documents keep it; it is no longer offered on new documents.» · step 2 → 200, isActiveFl false
Test data    : customerId 5003, code P-000123 (AC)
<!-- TC:TC-PTY-064:END -->

<!-- TC:TC-PTY-065:START traces=AC-PTY-020,REQ-PTY-015,API-PTY-005,API-PTY-026 -->
### TC-PTY-065 — Reactivated Customer returned by the active search
Derived from : AC-PTY-020  (REQ-PTY-015)
Exercises    : API-PTY-005 PATCH /api/v1/pty/customers/{id}/activation · API-PTY-026 GET /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5003 with isActiveFl false; no other active Party holds its email; the Owner holds PERM_PTY_CUSTOMER_DELETE and _VIEW
Host data    : none
Steps        : 1. PATCH /api/v1/pty/customers/5003/activation with {"isActiveFl": true} · 2. GET /api/v1/pty/customers?isActive=true&code=`<code of 5003>`
Expected     : step 1 → 200, data.isActiveFl true · step 2 → Customer 5003 in data.content
Test data    : customerId 5003 (AC); body per ADR-PTY-031
<!-- TC:TC-PTY-065:END -->

<!-- TC:TC-PTY-066:START traces=AC-PTY-021,REQ-PTY-016,API-PTY-006 -->
### TC-PTY-066 — Supplier registration with one name
Derived from : AC-PTY-021  (REQ-PTY-016)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner holding PERM_PTY_SUPPLIER_CREATE; Party and Supplier counts recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with nameEn "Delta Imports" only
Expected     : 201 · data.nameEn "Delta Imports" · data.isActiveFl true · data.partyFk = the new Party · Party +1, Supplier +1. The mandatory-mark count is TC-PTY-124
Test data    : nameEn "Delta Imports" (AC)
<!-- TC:TC-PTY-066:END -->

<!-- TC:TC-PTY-067:START traces=AC-PTY-022,REQ-PTY-017,API-PTY-008 -->
### TC-PTY-067 — Supplier payment terms and tax number updated
Derived from : AC-PTY-022  (REQ-PTY-017)
Exercises    : API-PTY-008 PUT /api/v1/pty/suppliers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Supplier 7002 with paymentTermDays empty and taxRegistrationNo empty; the Owner holds PERM_PTY_SUPPLIER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/suppliers/7002 with the stored fields, paymentTermDays 30 and taxRegistrationNo "300123456700003"
Expected     : 200 · data.paymentTermDays 30 · data.taxRegistrationNo "300123456700003" (held on the Party)
Test data    : supplierId 7002, 30, "300123456700003" (AC)
<!-- TC:TC-PTY-067:END -->

<!-- TC:TC-PTY-068:START traces=AC-PTY-023,REQ-PTY-018,API-PTY-028 -->
### TC-PTY-068 — Supplier search by name and active state
Derived from : AC-PTY-023  (REQ-PTY-018)
Exercises    : API-PTY-028 GET /api/v1/pty/suppliers
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Suppliers "Delta Imports" (P-000201) and "Delta Steel" (P-000202), inactive Supplier "Delta Old" (P-000203); a Warehouse user holding PERM_PTY_SUPPLIER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/suppliers?name=Delta&isActive=true
Expected     : 200 · data.content holds exactly P-000201 and P-000202
Test data    : names and codes of the AC
<!-- TC:TC-PTY-068:END -->

<!-- TC:TC-PTY-069:START traces=AC-PTY-024,REQ-PTY-019,API-PTY-007 -->
### TC-PTY-069 — Supplier read for a consuming module
Derived from : AC-PTY-024  (REQ-PTY-019)
Exercises    : API-PTY-007 GET /api/v1/pty/suppliers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Supplier 7002 with Party code P-000202 and paymentTermDays 30; caller (Settlements principal) holds PERM_PTY_SUPPLIER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/suppliers/7002
Expected     : 200 · data.supplierPk 7002 · data.code P-000202 · data.paymentTermDays 30 · data.isActiveFl true
Test data    : values of the AC
<!-- TC:TC-PTY-069:END -->

<!-- TC:TC-PTY-070:START traces=AC-PTY-025,REQ-PTY-020,API-PTY-009,API-PTY-007 -->
### TC-PTY-070 — Deactivated Supplier stays readable
Derived from : AC-PTY-025  (REQ-PTY-020)
Exercises    : API-PTY-009 DELETE /api/v1/pty/suppliers/{id} · API-PTY-007 GET /api/v1/pty/suppliers/{id}
Rule / code  : RULE-PTY-006 → confirmation (no catalog code)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Supplier 7003 with Party code P-000203 (an earlier purchase order referencing it belongs to Purchasing — N11); the Owner holds PERM_PTY_SUPPLIER_DELETE and _VIEW
Host data    : none
Steps        : 1. DELETE /api/v1/pty/suppliers/7003 · 2. GET /api/v1/pty/suppliers/7003
Expected     : step 1 → 200 · step 2 → 200, isActiveFl false
Test data    : supplierId 7003, code P-000203 (AC)
<!-- TC:TC-PTY-070:END -->

<!-- TC:TC-PTY-071:START traces=AC-PTY-026,REQ-PTY-021,API-PTY-010 -->
### TC-PTY-071 — Supplier reactivated
Derived from : AC-PTY-026  (REQ-PTY-021)
Exercises    : API-PTY-010 PATCH /api/v1/pty/suppliers/{id}/activation
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Supplier 7003 with isActiveFl false; its Party's email (if any) held by no other active Party; the Owner holds PERM_PTY_SUPPLIER_DELETE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/suppliers/7003/activation with {"isActiveFl": true}
Expected     : 200 · data.isActiveFl true
Test data    : supplierId 7003 (AC); body per ADR-PTY-031
<!-- TC:TC-PTY-071:END -->

<!-- TC:TC-PTY-072:START traces=AC-PTY-028,REQ-PTY-023,API-PTY-004 -->
### TC-PTY-072 — Deactivation publishes party-role-deactivated
Derived from : AC-PTY-028  (REQ-PTY-023)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-006 → confirmation + outbox event
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Customer 5003 with Party code P-000123; calls to the given `OutboxPublisher` are captured; the Owner holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/5003 · 2. roll back a second, identical deactivation of another Customer inside a failing transaction (test hook)
Expected     : step 1 → 200 · exactly one `OutboxPublisher.publish("pty.PartyRoleDeactivated", {role CUSTOMER, identifier 5003, code P-000123})` inside the deactivation transaction (ADR-PTY-038) · step 2 → no outbox row stored for the rolled-back deactivation
Test data    : customerId 5003, code P-000123 (AC)
<!-- TC:TC-PTY-072:END -->

<!-- TC:TC-PTY-073:START traces=AC-PTY-030,REQ-PTY-025,API-PTY-006 -->
### TC-PTY-073 — "Make supplier" adds the role to the same Party
Derived from : AC-PTY-030  (REQ-PTY-025)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers (partyId)
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5004 of Party P-000124 and no Supplier for that Party; the Owner holds PERM_PTY_SUPPLIER_CREATE; the tenant's Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with {"partyId": <identifier of P-000124>} only ("Make supplier" / «إضافة كمورّد»)
Expected     : 201 · data.partyFk = P-000124's identifier · data.isActiveFl true · Party count unchanged
Test data    : Customer 5004, Party P-000124 (AC)
<!-- TC:TC-PTY-073:END -->

<!-- TC:TC-PTY-074:START traces=AC-PTY-031,REQ-PTY-026,API-PTY-001 -->
### TC-PTY-074 — "Make customer" adds a RETAIL Customer to the Supplier's Party
Derived from : AC-PTY-031  (REQ-PTY-026)
Exercises    : API-PTY-001 POST /api/v1/pty/customers (partyId)
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Supplier 7004 of Party P-000204 and no Customer for that Party; the Owner holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with {"partyId": <identifier of P-000204>} only ("Make customer" / «إضافة كعميل»)
Expected     : 201 · data.partyFk = P-000204's identifier · data.customerGroupCode RETAIL · data.isActiveFl true
Test data    : Supplier 7004, Party P-000204 (AC)
<!-- TC:TC-PTY-074:END -->

<!-- TC:TC-PTY-075:START traces=AC-PTY-036,REQ-PTY-030,API-PTY-011 -->
### TC-PTY-075 — Address created on a Party
Derived from : AC-PTY-036  (REQ-PTY-030)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Party P-000120 (holding a Customer) with no Address; the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. POST /api/v1/pty/parties/{identifier of P-000120}/addresses with addressTypeCode DELIVERY, line1 "12 Tahrir St", city "Cairo"
Expected     : 201 · data.partyFk = P-000120's identifier · data.addressTypeCode DELIVERY · data.code "1" · data.isActiveFl true
Test data    : values of the AC (DELIVERY seeded)
<!-- TC:TC-PTY-075:END -->

<!-- TC:TC-PTY-076:START traces=AC-PTY-037,REQ-PTY-031,API-PTY-013 -->
### TC-PTY-076 — Address city updated, Party unchanged
Derived from : AC-PTY-037  (REQ-PTY-031)
Exercises    : API-PTY-013 PUT /api/v1/pty/parties/{partyId}/addresses/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Address code "1" of Party P-000120 with city "Cairo"; the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/parties/{identifier of P-000120}/addresses/{id of "1"} with the stored fields and city "Giza"
Expected     : 200 · data.city "Giza" · data.partyFk unchanged
Test data    : values of the AC
<!-- TC:TC-PTY-076:END -->

<!-- TC:TC-PTY-077:START traces=AC-PTY-038,REQ-PTY-032,API-PTY-029 -->
### TC-PTY-077 — Party addresses read returns the active ones only
Derived from : AC-PTY-038  (REQ-PTY-032)
Exercises    : API-PTY-029 GET /api/v1/pty/parties/{partyId}/addresses
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Party P-000120 with 2 active Addresses (BILLING default, DELIVERY default) and 1 inactive DELIVERY Address; caller (storefront customer area principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : 200 · data holds exactly the 2 active Addresses, each isDefaultFl true
Test data    : values of the AC
<!-- TC:TC-PTY-077:END -->

<!-- TC:TC-PTY-078:START traces=AC-PTY-039,REQ-PTY-033,API-PTY-014,API-PTY-029,API-PTY-012 -->
### TC-PTY-078 — Deactivated Address leaves the list but stays readable
Derived from : AC-PTY-039  (REQ-PTY-033)
Exercises    : API-PTY-014 DELETE /api/v1/pty/parties/{partyId}/addresses/{id} · API-PTY-029 GET …/addresses · API-PTY-012 GET /api/v1/pty/addresses/{id}
Rule / code  : RULE-PTY-006 → confirmation (no catalog code)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Address 9001 of a Party `<partyId>` (an earlier sales order referencing it belongs to Sales & POS — N11); the Owner holds PERM_PTY_CUSTOMER_UPDATE and _VIEW
Host data    : none
Steps        : 1. DELETE /api/v1/pty/parties/{partyId}/addresses/9001 · 2. GET /api/v1/pty/parties/{partyId}/addresses · 3. GET /api/v1/pty/addresses/9001
Expected     : step 1 → 200 · step 2 → Address 9001 absent · step 3 → 200, isActiveFl false
Test data    : addressId 9001 (AC); `<partyId>` placeholder
<!-- TC:TC-PTY-078:END -->

<!-- TC:TC-PTY-079:START traces=AC-PTY-041,REQ-PTY-035,API-PTY-015,API-PTY-029 -->
### TC-PTY-079 — Marking an Address default clears the previous default
Derived from : AC-PTY-041  (REQ-PTY-035)
Exercises    : API-PTY-015 PATCH /api/v1/pty/parties/{partyId}/addresses/{id}/default · API-PTY-029 GET …/addresses
Rule / code  : RULE-PTY-017 → confirmation (no catalog code)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Party P-000120 with DELIVERY Address "1" default and DELIVERY Address "2" not default; ADDRESS_TYPE labels as seeded (DELIVERY = توصيل / Delivery); the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. PATCH …/addresses/{id of "2"}/default with {"isDefaultFl": true} · 2. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : step 1 → 200 · data.isDefaultFl true · messageAr «أصبح هذا العنوان هو العنوان الافتراضي من نوع توصيل.» · messageEn «This address is now the default Delivery address.» · step 2 → "2" isDefaultFl true, "1" isDefaultFl false
Test data    : Address codes "1", "2" (AC); body per ADR-PTY-031
<!-- TC:TC-PTY-079:END -->

<!-- TC:TC-PTY-080:START traces=AC-PTY-047,REQ-PTY-041,API-PTY-027 -->
### TC-PTY-080 — Global search returns Customers only, by name
Derived from : AC-PTY-047  (REQ-PTY-041)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language en
Preconditions: active Customers "Nour Trading" (P-000120, mobile "+201001112222") and "Nour Home" (P-000150); Supplier "Nour Supplies"; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW; request language en (display-name order)
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=nour
Expected     : 200 · data is exactly ["Nour Home", "Nour Trading"] in that order; "Nour Supplies" is not among them
Test data    : values of the AC
<!-- TC:TC-PTY-080:END -->

<!-- TC:TC-PTY-081:START traces=AC-PTY-048,REQ-PTY-041,API-PTY-027 -->
### TC-PTY-081 — Global search matches part of a mobile
Derived from : AC-PTY-048  (REQ-PTY-041)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an active Customer `<customerId>` with mobile "+201001112222"; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=1112222
Expected     : 200 · that Customer is in data
Test data    : mobile "+201001112222", term "1112222" (AC)
<!-- TC:TC-PTY-081:END -->

<!-- TC:TC-PTY-082:START traces=AC-PTY-050,REQ-PTY-042,API-PTY-027 -->
### TC-PTY-082 — Arabic letter forms unified in the global search
Derived from : AC-PTY-050  (REQ-PTY-042)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class EDGE · language ar
Preconditions: an active Customer with nameAr «أحمد مصطفى»; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=احمد
Expected     : 200 · the Customer «أحمد مصطفى» is in data
Test data    : nameAr «أحمد مصطفى», term «احمد» (AC)
<!-- TC:TC-PTY-082:END -->

<!-- TC:TC-PTY-083:START traces=AC-PTY-051,REQ-PTY-043,API-PTY-027 -->
### TC-PTY-083 — Global search reveals no Customer to a user without VIEW
Derived from : AC-PTY-051  (REQ-PTY-043)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — (empty list, not PTY-403-FORBIDDEN)
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a Warehouse user without PERM_PTY_CUSTOMER_VIEW; an active Customer "Nour Trading"
Host data    : none
Steps        : 1. as the Warehouse user, GET /api/v1/pty/customers/global-search?term=Nour
Expected     : 200 · data is an empty list (0 customer results)
Test data    : values of the AC
<!-- TC:TC-PTY-083:END -->

<!-- TC:TC-PTY-084:START traces=AC-PTY-053,REQ-PTY-045,API-PTY-033 -->
### TC-PTY-084 — Default Customer of an unnamed POS sale is the walk-in Customer
Derived from : AC-PTY-053  (REQ-PTY-045)
Exercises    : API-PTY-033 GET /api/v1/pty/customers/walk-in
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the tenant's walk-in Customer has identifier 5000; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/walk-in
Expected     : 200 · data.customerPk 5000 · data.isWalkInFl true
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-084:END -->

<!-- TC:TC-PTY-085:START traces=AC-PTY-054,REQ-PTY-046,API-PTY-033 -->
### TC-PTY-085 — Walk-in Customer created on first demand, once
Derived from : AC-PTY-054  (REQ-PTY-046)
Exercises    : API-PTY-033 GET /api/v1/pty/customers/walk-in
Rule / code  : RULE-PTY-014 → — (idempotent ensure)
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: a tenant with no Customer having isWalkInFl true; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/walk-in · 2. GET /api/v1/pty/customers/walk-in · 3. count the tenant's stored Customers with isWalkInFl true
Expected     : step 1 → 200, data.isWalkInFl true · step 2 → the same customerPk as step 1 · step 3 → 1
Test data    : none beyond the AC
<!-- TC:TC-PTY-085:END -->

<!-- TC:TC-PTY-086:START traces=AC-PTY-058,REQ-PTY-050,API-PTY-034,API-PTY-002,API-PTY-029 -->
### TC-PTY-086 — New Customer from a guest's first order
Derived from : AC-PTY-058  (REQ-PTY-050)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers · API-PTY-002 · API-PTY-029 (read back)
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: no active Party has email "lina@example.com" or mobile "+966551234567"; caller (storefront principal) holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with nameEn "Lina Fares", email "lina@example.com", mobile "+966551234567", deliveryAddress {line1 "King Fahd Rd 10", city "Riyadh"} · 2. GET /api/v1/pty/customers/{customerId of step 1} · 3. GET /api/v1/pty/parties/{partyFk}/addresses
Expected     : step 1 → 200, data.customerId returned · Party count +1 · step 2 → customerGroupCode RETAIL · step 3 → one DELIVERY Address, isDefaultFl true
Test data    : values of the AC
<!-- TC:TC-PTY-086:END -->

<!-- TC:TC-PTY-087:START traces=AC-PTY-059,REQ-PTY-051,API-PTY-034 -->
### TC-PTY-087 — Invitation requested for a Customer created from a guest order
Derived from : AC-PTY-059  (REQ-PTY-051)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: as TC-PTY-086 (the request creates Customer 5010 with email "lina@example.com"); calls to the given `SecCustomerInvitationApi` are captured
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with the details of AC-PTY-058 · 2. wait for the commit · 3. read the stored secUserId (DBF-PTY-017) of 5010
Expected     : exactly one CUSTOMER-realm invitation requested, addressed to "lina@example.com", for Customer 5010, after commit · step 3 → empty
Test data    : customerId 5010, "lina@example.com" (AC)
<!-- TC:TC-PTY-087:END -->

<!-- TC:TC-PTY-088:START traces=AC-PTY-060,REQ-PTY-052 -->
### TC-PTY-088 — Accepted invitation links the CUSTOMER-realm user
Derived from : AC-PTY-060  (REQ-PTY-052)
Exercises    : outbox subscriber `PtyCustomerInvitationRedeemedSubscriber` (no API — backend plan CORE, SVC-API-INT)
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a pending invitation for Customer 5010; user 88001 linked to no Customer
Host data    : none
Steps        : 1. deliver to the subscriber a `sec.CustomerInvitationRedeemed` outbox message (subject module PTY, subject type CUSTOMER, subject reference 5010, redeemer 88001) · 2. read the stored secUserId (DBF-PTY-017) of 5010 · 3. redeliver the same message (same OutboxMessage id)
Expected     : step 1 → outcome linked (not REFUSED) · step 2 → 88001 · step 3 → secUserId stays 88001, no second change (idempotent on OutboxMessage.id())
Test data    : customerId 5010, user 88001 (AC)
<!-- TC:TC-PTY-088:END -->

<!-- TC:TC-PTY-089:START traces=AC-PTY-063,REQ-PTY-055,API-PTY-034 -->
### TC-PTY-089 — Guest matched by email without regard to case
Derived from : AC-PTY-063  (REQ-PTY-055)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Party P-000120 with email "nour@example.com" and active Customer 5001; caller (storefront principal) holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "NOUR@example.com", deliveryAddress {line1 `<line1>`}
Expected     : 200 · data.customerId 5001 · Party count unchanged
Test data    : emails, P-000120, 5001 (AC); `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-089:END -->

<!-- TC:TC-PTY-090:START traces=AC-PTY-064,REQ-PTY-056,API-PTY-034 -->
### TC-PTY-090 — Guest matched by a normalised mobile
Derived from : AC-PTY-064  (REQ-PTY-056)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: exactly one active Party P-000150 with mobile "+20 100 555 6666" and active Customer 5015; no Party with email "guest1@example.com"; caller holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "guest1@example.com", mobile "00201005556666", deliveryAddress {line1 `<line1>`}
Expected     : 200 · data.customerId 5015 · Party count unchanged
Test data    : mobiles, email, P-000150, 5015 (AC); `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-090:END -->

<!-- TC:TC-PTY-091:START traces=AC-PTY-065,REQ-PTY-057,API-PTY-034 -->
### TC-PTY-091 — Customer role added to a matched Supplier's Party
Derived from : AC-PTY-065  (REQ-PTY-057)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Party P-000204 with email "delta@example.com" holding a Supplier and no Customer; caller holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "delta@example.com", deliveryAddress {line1 `<line1>`} · 2. GET /api/v1/pty/customers/{customerId of step 1}
Expected     : step 1 → 200, data.customerId returned · Party count unchanged · step 2 → partyFk = P-000204's identifier, customerGroupCode RETAIL
Test data    : P-000204, "delta@example.com" (AC); `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-091:END -->

<!-- TC:TC-PTY-092:START traces=AC-PTY-066,REQ-PTY-058,API-PTY-034,API-PTY-002,API-PTY-029 -->
### TC-PTY-092 — Guest details never change a matched Party
Derived from : AC-PTY-066  (REQ-PTY-058)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers · API-PTY-002 · API-PTY-029 (read back)
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: active Party P-000120 with email "nour@example.com", nameEn "Nour Trading", mobile "+201001112222", 1 active DELIVERY Address, and its active Customer `<customerId>`; caller holds PERM_PTY_CUSTOMER_CREATE and _VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with email "nour@example.com", nameEn "Someone Else", mobile "+201009998888", deliveryAddress {line1 "1 New St"} · 2. GET /api/v1/pty/customers/{customerId} · 3. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : step 1 → 200, data.customerId = `<customerId>` · step 2 → nameEn "Nour Trading", mobile "+201001112222" · step 3 → exactly 1 active DELIVERY Address
Test data    : values of the AC
<!-- TC:TC-PTY-092:END -->

<!-- TC:TC-PTY-093:START traces=AC-PTY-067,REQ-PTY-059,API-PTY-034 -->
### TC-PTY-093 — Invitation goes to the matched Party's recorded contact
Derived from : AC-PTY-067  (REQ-PTY-059)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Customer 5015 of Party P-000150 with secUserId empty, email empty and mobile "+201005556666"; no Party holds "guest1@example.com"; invitation calls captured; caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "guest1@example.com", mobile "00201005556666", deliveryAddress {line1 `<line1>`} · 2. wait for the commit
Expected     : data.customerId 5015 · exactly one CUSTOMER-realm invitation requested, addressed to "+201005556666"; none addressed to "guest1@example.com"
Test data    : values of the AC; `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-093:END -->

<!-- TC:TC-PTY-094:START traces=AC-PTY-068,REQ-PTY-060,API-PTY-034 -->
### TC-PTY-094 — No invitation for a Customer already linked
Derived from : AC-PTY-068  (REQ-PTY-060)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Customer 5001 (Party email "nour@example.com") holds secUserId 88001; invitation calls captured; caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "nour@example.com", deliveryAddress {line1 `<line1>`} · 2. wait for the commit
Expected     : data.customerId 5001 · 0 invitations requested
Test data    : values of the AC; `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-094:END -->

<!-- TC:TC-PTY-095:START traces=AC-PTY-069,REQ-PTY-061,API-PTY-034 -->
### TC-PTY-095 — Mobile shared by two Parties creates a new Customer
Derived from : AC-PTY-069  (REQ-PTY-061)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: active Parties P-000160 and P-000161 both hold mobile "+201007770000"; no Party holds "sam@example.com"; caller holds PERM_PTY_CUSTOMER_CREATE; Party and Customer counts recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "sam@example.com", mobile "+201007770000", deliveryAddress {line1 `<line1>`}
Expected     : 200 · Party +1 and Customer +1 · data.customerId is neither the Customer of P-000160 nor of P-000161
Test data    : values of the AC; `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-095:END -->

<!-- TC:TC-PTY-096:START traces=AC-PTY-071,REQ-PTY-063,API-PTY-034 -->
### TC-PTY-096 — Walk-in Party never matched to a guest
Derived from : AC-PTY-071  (REQ-PTY-063)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : RULE-PTY-013 → — (matching filter; no error)
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: the walk-in Customer's Party holds mobile "+201000000000" (entered by an Owner through API-PTY-003) and no other Party holds it; caller holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "new@example.com", mobile "+201000000000", deliveryAddress {line1 `<line1>`} · 2. read the stored secUserId (DBF-PTY-017) of the walk-in Customer
Expected     : step 1 → 200 · Party +1 · data.customerId is not the walk-in Customer · step 2 → empty
Test data    : values of the AC; `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-096:END -->

<!-- TC:TC-PTY-097:START traces=AC-PTY-072,REQ-PTY-064,API-PTY-017 -->
### TC-PTY-097 — Credit limit saved while the flag is on
Derived from : AC-PTY-072  (REQ-PTY-064)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit empty; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit 50000 and the stored paymentTermDays
Expected     : 200 · data.creditLimit 50000
Test data    : customerId 5001, creditLimit 50000 (AC)
<!-- TC:TC-PTY-097:END -->

<!-- TC:TC-PTY-098:START traces=AC-PTY-073,REQ-PTY-065,API-PTY-017 -->
### TC-PTY-098 — Payment terms saved while the flag is on
Derived from : AC-PTY-073  (REQ-PTY-065)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with paymentTermDays empty; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with the stored creditLimit and paymentTermDays 45
Expected     : 200 · data.paymentTermDays 45
Test data    : customerId 5001, 45 (AC)
<!-- TC:TC-PTY-098:END -->

<!-- TC:TC-PTY-099:START traces=AC-PTY-076,REQ-PTY-068,API-PTY-035 -->
### TC-PTY-099 — Empty credit limit means NO_CREDIT
Derived from : AC-PTY-076  (REQ-PTY-068)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5002 with creditLimit empty; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5002 and saleAmount 10
Expected     : 200 · data.resultCode NO_CREDIT
Test data    : customerId 5002, saleAmount 10 (AC)
<!-- TC:TC-PTY-099:END -->

<!-- TC:TC-PTY-100:START traces=AC-PTY-077,REQ-PTY-069,API-PTY-030 -->
### TC-PTY-100 — Customer credit list filtered on carries-credit
Derived from : AC-PTY-077  (REQ-PTY-069)
Exercises    : API-PTY-030 GET /api/v1/pty/customer-credits
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; 4 Customers carry a creditLimit or a paymentTermDays and 20 carry neither; the Owner holds PERM_PTY_CUSTOMER_CREDIT_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customer-credits?carriesCredit=true
Expected     : 200 · data.totalElements 4 · data.content holds exactly those 4, each with its creditLimit and paymentTermDays
Test data    : counts 4 / 20 (AC); Customer identities and values are placeholders
<!-- TC:TC-PTY-100:END -->

<!-- TC:TC-PTY-101:START traces=AC-PTY-084,REQ-PTY-075,API-PTY-037 -->
### TC-PTY-101 — Switch-off dependency counts Customers carrying either credit field
Derived from : AC-PTY-084  (REQ-PTY-075)
Exercises    : API-PTY-037 GET /api/v1/pty/flag-dependencies/b2bCredit
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: 3 Customers carry a creditLimit and 1 other Customer carries only a paymentTermDays; caller holds PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/flag-dependencies/b2bCredit
Expected     : 200 · data.dependentCustomerCount 4
Test data    : counts 3 + 1 (AC)
<!-- TC:TC-PTY-101:END -->

<!-- TC:TC-PTY-102:START traces=AC-PTY-086,REQ-PTY-076,API-PTY-037 -->
### TC-PTY-102 — No dependent Customer, no refusal
Derived from : AC-PTY-086  (REQ-PTY-076)
Exercises    : API-PTY-037 GET /api/v1/pty/flag-dependencies/b2bCredit
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; 0 Customers carry credit data; caller holds PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/flag-dependencies/b2bCredit
Expected     : 200 · data.dependentCustomerCount 0 · data.switchOffAllowed true · no RULE-PTY-024 message (messageAr / messageEn null)
Test data    : count 0 (AC)
<!-- TC:TC-PTY-102:END -->

<!-- TC:TC-PTY-103:START traces=AC-PTY-088,REQ-PTY-078,API-PTY-018,API-PTY-037 -->
### TC-PTY-103 — Clear credit empties both fields and lowers the dependency count
Derived from : AC-PTY-088  (REQ-PTY-078)
Exercises    : API-PTY-018 DELETE /api/v1/pty/customer-credits/{customerId} · API-PTY-037 GET …/flag-dependencies/b2bCredit
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000 and paymentTermDays 45; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE and PERM_PTY_CREDIT_SETTINGS_VIEW; the dependency count N is read first
Host data    : none
Steps        : 1. GET …/flag-dependencies/b2bCredit (N) · 2. DELETE /api/v1/pty/customer-credits/5001 ("Clear credit" / «مسح الائتمان») · 3. GET …/flag-dependencies/b2bCredit
Expected     : step 2 → 200, data.creditLimit null, data.paymentTermDays null · step 3 → dependentCustomerCount N − 1
Test data    : customerId 5001, 50000, 45 (AC)
<!-- TC:TC-PTY-103:END -->

<!-- TC:TC-PTY-104:START traces=AC-PTY-089,REQ-PTY-079,API-PTY-035 -->
### TC-PTY-104 — Credit check within the limit
Derived from : AC-PTY-089  (REQ-PTY-079)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; a test contributor reports open exposure 42000; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 5000
Expected     : 200 · data.creditLimit 50000 · data.openExposure 42000 · data.projectedExposure 47000 · data.resultCode WITHIN_LIMIT
Test data    : values of the AC
<!-- TC:TC-PTY-104:END -->

<!-- TC:TC-PTY-105:START traces=AC-PTY-090,REQ-PTY-080,API-PTY-035 -->
### TC-PTY-105 — Open exposure is the sum of the contributors
Derived from : AC-PTY-090  (REQ-PTY-080)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on (N8); two registered test contributors report 30000 and 12000 for Customer 5001; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount `<saleAmount>`
Expected     : 200 · data.openExposure 42000
Test data    : 30000, 12000 (AC); `<saleAmount>` placeholder (> 0)
<!-- TC:TC-PTY-105:END -->

<!-- TC:TC-PTY-106:START traces=AC-PTY-091,REQ-PTY-080,API-PTY-035 -->
### TC-PTY-106 — No contributor registered means exposure 0
Derived from : AC-PTY-091  (REQ-PTY-080)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: `b2bCredit` on (N8); no `CustomerCreditExposureContributor` registered; Customer 5001; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount `<saleAmount>`
Expected     : 200 · data.openExposure 0
Test data    : customerId 5001 (AC); `<saleAmount>` placeholder (> 0)
<!-- TC:TC-PTY-106:END -->

<!-- TC:TC-PTY-107:START traces=AC-PTY-094,REQ-PTY-083,API-PTY-035 -->
### TC-PTY-107 — Walk-in Customer gets NO_CREDIT with the overrun action
Derived from : AC-PTY-094  (REQ-PTY-083)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; the walk-in Customer 5000 with creditLimit empty; the tenant's credit-overrun action is WARN; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5000 and saleAmount 100
Expected     : 200 · data.resultCode NO_CREDIT · data.overrunActionCode WARN
Test data    : values of the AC
<!-- TC:TC-PTY-107:END -->

<!-- TC:TC-PTY-108:START traces=AC-PTY-096,REQ-PTY-085,API-PTY-016 -->
### TC-PTY-108 — Credit standing shows limit, exposure and available credit
Derived from : AC-PTY-096  (REQ-PTY-085)
Exercises    : API-PTY-016 GET /api/v1/pty/customer-credits/{customerId}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; a test contributor reports 42000; a Seller/Cashier holding PERM_PTY_CUSTOMER_CREDIT_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customer-credits/5001
Expected     : 200 · data.creditLimit 50000 · data.openExposure 42000 · data.availableCredit 8000 · data.exposureAvailable true
Test data    : values of the AC
<!-- TC:TC-PTY-108:END -->

<!-- TC:TC-PTY-109:START traces=AC-PTY-097,REQ-PTY-086,API-PTY-025,API-PTY-024,API-PTY-035 -->
### TC-PTY-109 — Saved overrun action applies to the next credit check
Derived from : AC-PTY-097  (REQ-PTY-086)
Exercises    : API-PTY-025 PUT /api/v1/pty/credit-settings · API-PTY-024 GET /api/v1/pty/credit-settings · API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; tenant setting BLOCK_WITH_OVERRIDE; the Owner holds PERM_PTY_CREDIT_SETTINGS_UPDATE and _VIEW; a Customer `<customerId>` and sale amount `<saleAmount>` whose check is OVER_LIMIT
Host data    : none
Steps        : 1. PUT /api/v1/pty/credit-settings with {"creditOverrunActionCode": "WARN"} · 2. GET /api/v1/pty/credit-settings · 3. POST /api/v1/pty/credit-checks with `<customerId>`, `<saleAmount>`
Expected     : step 1 → 200 · step 2 → creditOverrunActionCode WARN · step 3 → resultCode OVER_LIMIT, overrunActionCode WARN
Test data    : WARN, BLOCK_WITH_OVERRIDE (AC; closed lookup); `<customerId>`, `<saleAmount>` placeholders
<!-- TC:TC-PTY-109:END -->

<!-- TC:TC-PTY-110:START traces=AC-PTY-098,REQ-PTY-087,API-PTY-035,API-PTY-024 -->
### TC-PTY-110 — BLOCK_WITH_OVERRIDE applies while nothing is saved
Derived from : AC-PTY-098  (REQ-PTY-087)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks · API-PTY-024 GET /api/v1/pty/credit-settings
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` just turned on; no tenant setting PTY_CREDIT_OVERRUN_ACTION saved; a Customer `<customerId>` and sale amount `<saleAmount>` whose check is OVER_LIMIT; caller holds PERM_PTY_CUSTOMER_VIEW and PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with `<customerId>`, `<saleAmount>` · 2. GET /api/v1/pty/credit-settings
Expected     : step 1 → resultCode OVER_LIMIT, overrunActionCode BLOCK_WITH_OVERRIDE · step 2 → creditOverrunActionCode BLOCK_WITH_OVERRIDE, isDefault true. The screen part is TC-PTY-133
Test data    : `<customerId>`, `<saleAmount>` placeholders
<!-- TC:TC-PTY-110:END -->

<!-- TC:TC-PTY-111:START traces=AC-PTY-099,REQ-PTY-088,API-PTY-035 -->
### TC-PTY-111 — Under WARN an overrun needs no approval
Derived from : AC-PTY-099  (REQ-PTY-088)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on (N8); credit-overrun action WARN; Customer 5001 with creditLimit 50000; a test contributor reports 49000; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 2000
Expected     : 200 · data.resultCode OVER_LIMIT · data.approvalRequired false
Test data    : values of the AC
<!-- TC:TC-PTY-111:END -->

<!-- TC:TC-PTY-112:START traces=AC-PTY-100,REQ-PTY-089,API-PTY-035 -->
### TC-PTY-112 — Under BLOCK_WITH_OVERRIDE an overrun needs approval
Derived from : AC-PTY-100  (REQ-PTY-089)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on (N8); credit-overrun action BLOCK_WITH_OVERRIDE; Customer 5001 with creditLimit 50000; a test contributor reports 49000; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 2000
Expected     : 200 · data.resultCode OVER_LIMIT · data.approvalRequired true
Test data    : values of the AC
<!-- TC:TC-PTY-112:END -->

<!-- TC:TC-PTY-113:START traces=AC-PTY-101,REQ-PTY-090,API-PTY-025,API-PTY-024 -->
### TC-PTY-113 — Overrun setting refused to a user without UPDATE
Derived from : AC-PTY-101  (REQ-PTY-090)
Exercises    : API-PTY-025 PUT /api/v1/pty/credit-settings · API-PTY-024 GET /api/v1/pty/credit-settings
Rule / code  : PLATFORM-STD → PTY-403-FORBIDDEN (ADR-PTY-024)
Package      : SEC-BE
Scenario     : PERMISSION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; tenant setting BLOCK_WITH_OVERRIDE; a Seller/Cashier without PERM_PTY_CREDIT_SETTINGS_UPDATE; an Owner holding PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. as the Seller/Cashier, PUT /api/v1/pty/credit-settings with {"creditOverrunActionCode": "WARN"} · 2. as the Owner, GET /api/v1/pty/credit-settings
Expected     : step 1 → 403 · code PTY-403-FORBIDDEN · messageAr «ليست لديك صلاحية لتنفيذ هذا الإجراء.» · messageEn «You do not have permission to perform this action.» · step 2 → BLOCK_WITH_OVERRIDE
Test data    : values of the AC
<!-- TC:TC-PTY-113:END -->

<!-- TC:TC-PTY-114:START traces=AC-PTY-102,REQ-PTY-091,API-PTY-019 -->
### TC-PTY-114 — Request approval records the credit picture
Derived from : AC-PTY-102  (REQ-PTY-091)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-025, RULE-PTY-032, RULE-PTY-033 → — (all satisfied)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; Customer 5001 with creditLimit 50000; a test contributor reports 49000; a Seller/Cashier holding PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. as the Seller/Cashier, POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000455" ("Request approval" / «طلب موافقة»)
Expected     : 201 · data.statusCode PENDING · data.customerFk 5001 · data.saleAmount 2000 · data.creditLimitAtRequest 50000 · data.openExposure 49000 · data.creditCheckResultCode OVER_LIMIT · data.sourceDocumentRef "POS-1-000455" · data.createdBy the Seller/Cashier
Test data    : values of the AC
<!-- TC:TC-PTY-114:END -->

<!-- TC:TC-PTY-115:START traces=AC-PTY-105,REQ-PTY-093,API-PTY-019 -->
### TC-PTY-115 — Approvers notified of a new request
Derived from : AC-PTY-105  (REQ-PTY-093)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; exactly 2 active users hold PERM_PTY_CREDIT_OVERRIDE_UPDATE; the series PTY_CREDIT_OVERRIDE returns CO-000031 next; Customer "Nour Trading" `<customerId>` and a sale amount `<saleAmount>` whose check is not WITHIN_LIMIT; calls to the given `NotificationDispatchApi` captured
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides for `<customerId>`, `<saleAmount>`, sourceDocumentRef `<sale reference>` · 2. wait for the commit
Expected     : 201, docNo CO-000031 · after commit, each of the 2 users receives exactly one `NotificationDispatchApi.dispatch` with template PTY_CREDIT_OVERRIDE_REQUESTED, channel IN_APP and variables {docNo CO-000031, name Nour Trading}, which renders ar «طلب تجاوز حد الائتمان CO-000031 للعميل Nour Trading بانتظار موافقتك.» · en «Credit override request CO-000031 for Nour Trading is waiting for your approval.» · no dispatch happens before the commit
Test data    : CO-000031, "Nour Trading", 2 approvers (AC); `<customerId>`, `<saleAmount>`, `<sale reference>` placeholders
<!-- TC:TC-PTY-115:END -->

<!-- TC:TC-PTY-116:START traces=AC-PTY-115,REQ-PTY-101,API-PTY-032 -->
### TC-PTY-116 — Request list puts PENDING first, then newest
Derived from : AC-PTY-115  (REQ-PTY-101)
Exercises    : API-PTY-032 GET /api/v1/pty/credit-overrides
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: requests CO-000031 (APPROVED, docDate 2026-11-02), CO-000035 (PENDING, 2026-11-01), CO-000036 (PENDING, 2026-11-03); an Owner holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE
Host data    : none
Steps        : 1. GET /api/v1/pty/credit-overrides with no filter and no sort
Expected     : 200 · data.content lists CO-000036, CO-000035, CO-000031 in that order
Test data    : values of the AC
<!-- TC:TC-PTY-116:END -->

<!-- TC:TC-PTY-117:START traces=AC-PTY-116,REQ-PTY-102,API-PTY-032 -->
### TC-PTY-117 — Requester without UPDATE sees only own requests
Derived from : AC-PTY-116  (REQ-PTY-102)
Exercises    : API-PTY-032 GET /api/v1/pty/credit-overrides
Rule / code  : — 
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: Seller/Cashier S1 created CO-000035, Seller/Cashier S2 created CO-000036; neither holds PERM_PTY_CREDIT_OVERRIDE_UPDATE; both hold _VIEW
Host data    : none
Steps        : 1. as S1, GET /api/v1/pty/credit-overrides with no filter
Expected     : 200 · data.content lists CO-000035 only
Test data    : values of the AC
<!-- TC:TC-PTY-117:END -->

<!-- TC:TC-PTY-118:START traces=AC-PTY-117,REQ-PTY-103,API-PTY-020 -->
### TC-PTY-118 — Credit override request read by its identifier
Derived from : AC-PTY-117  (REQ-PTY-103)
Exercises    : API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: request CO-000031 for Customer 5001, saleAmount 2000, approved by the Owner at 2026-11-02 10:17; caller (Sales & POS principal) is the request's requester holding PERM_PTY_CREDIT_OVERRIDE_VIEW (N7)
Host data    : none
Steps        : 1. GET /api/v1/pty/credit-overrides/{id of CO-000031}
Expected     : 200 · data.docNo CO-000031 · data.statusCode APPROVED · data.customerFk 5001 · data.saleAmount 2000 · data.decidedBy the Owner's sec user identifier · data.decidedAt 2026-11-02 10:17
Test data    : values of the AC
<!-- TC:TC-PTY-118:END -->

<!-- TC:TC-PTY-119:START traces=AC-PTY-120,REQ-PTY-106,API-PTY-017 -->
### TC-PTY-119 — Credit limit change recorded in the audit
Derived from : AC-PTY-120  (REQ-PTY-106)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-030 → — (audit recorded)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; test clock 2026-11-04 09:00; audit rows of entity type PTY_CUSTOMER (the given `@Audited` listener) captured; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit 60000 and the stored paymentTermDays
Expected     : 200 · exactly one audit row {entity PTY_CUSTOMER 5001, change creditLimit old 50000 → new 60000, user the Owner, time 2026-11-04 09:00}; no change recorded for the unchanged paymentTermDays
Test data    : values of the AC
<!-- TC:TC-PTY-119:END -->

<!-- TC:TC-PTY-120:START traces=AC-PTY-121,REQ-PTY-107,API-PTY-021 -->
### TC-PTY-120 — Approval recorded in the audit
Derived from : AC-PTY-121  (REQ-PTY-107)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; PENDING request CO-000031 for Customer 5001 with saleAmount 2000; test clock 2026-11-02 10:17; `AuditApi.record` business actions captured; the Owner holds PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000031}/approval
Expected     : 200 · exactly one business audit action APPROVE {docNo CO-000031, Customer 5001, saleAmount 2000, statusCode APPROVED, decider the Owner, time 2026-11-02 10:17} (the entity's own `@Audited` UPDATE row is separate)
Test data    : values of the AC
<!-- TC:TC-PTY-120:END -->

<!-- TC:TC-PTY-121:START traces=AC-PTY-122,REQ-PTY-108,API-PTY-031 -->
### TC-PTY-121 — Credit history newest first
Derived from : AC-PTY-122  (REQ-PTY-108)
Exercises    : API-PTY-031 GET /api/v1/pty/customer-credits/{customerId}/history
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001's creditLimit changed on 2026-10-01 and on 2026-11-04 and its paymentTermDays changed on 2026-10-15 (recorded by the `@Audited` listener of PTY_CUSTOMER through API-PTY-017); the Owner holds PERM_PTY_CUSTOMER_CREDIT_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customer-credits/5001/history
Expected     : 200 · data.content has 3 entries in the order 2026-11-04, 2026-10-15, 2026-10-01, each with field, oldValue, newValue and userName
Test data    : dates of the AC; old/new values are placeholders
<!-- TC:TC-PTY-121:END -->

<!-- TC:TC-PTY-165:START traces=AC-PTY-135,REQ-PTY-079,API-PTY-035,API-PTY-019 -->
### TC-PTY-165 — Credit amounts carry the Customer's currency
Derived from : AC-PTY-135  (REQ-PTY-079)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks · API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; the tenant setting PTY_BASE_CURRENCY_CODE is EGP; Customer 5001 created after that setting (currencyCode EGP) with creditLimit 50000; a test contributor reports 49000; caller holds PERM_PTY_CUSTOMER_VIEW and PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 5000 · 2. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 5000, sourceDocumentRef `<sale reference>`
Expected     : step 1 → 200, data.currencyCode EGP · step 2 → 201, data.currencyCode EGP
Test data    : EGP, 50000, 5000 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-165:END -->

<!-- TC:TC-PTY-166:START traces=AC-PTY-136,REQ-PTY-018,API-PTY-028 -->
### TC-PTY-166 — Supplier search by email finds a Supplier-only Party
Derived from : AC-PTY-136  (REQ-PTY-018)
Exercises    : API-PTY-028 GET /api/v1/pty/suppliers
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Party P-000204 holds email "delta@example.com" and only the Supplier role (Supplier 7004); no other Party holds that email; the Owner holds PERM_PTY_SUPPLIER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/suppliers?email=Delta@Example.com&isActive=true
Expected     : 200 · data.totalElements 1 · data.content[0].supplierPk 7004 · data.content[0].code P-000204 · data.content[0].email "delta@example.com"
Test data    : values of the AC
<!-- TC:TC-PTY-166:END -->

<!-- TC:TC-PTY-167:START traces=AC-PTY-036,REQ-PTY-030,API-PTY-011 -->
### TC-PTY-167 — Either role's UPDATE suffices for the Address of a two-role Party
Derived from : AC-PTY-036  (REQ-PTY-030)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : — (authority — ADR-PTY-039)
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: Party P-000130 holds a Customer and a Supplier; user U1 holds PERM_PTY_SUPPLIER_VIEW and PERM_PTY_SUPPLIER_UPDATE only (custom role); user U2 holds PERM_PTY_SUPPLIER_UPDATE only and Party P-000120 holds a Customer only
Host data    : none
Steps        : 1. as U1, POST /api/v1/pty/parties/{identifier of P-000130}/addresses with addressTypeCode DELIVERY and line1 "12 Tahrir St" · 2. as U2, POST /api/v1/pty/parties/{identifier of P-000120}/addresses with the same body
Expected     : step 1 → 201 (the Supplier role's UPDATE is enough) · step 2 → 403 · code PTY-403-FORBIDDEN · messageAr «ليست لديك صلاحية لتنفيذ هذا الإجراء.» · messageEn «You do not have permission to perform this action.» (P-000120 holds no Supplier)
Test data    : DELIVERY, "12 Tahrir St" (AC); users test-local
<!-- TC:TC-PTY-167:END -->

<!-- TC:TC-PTY-177:START traces=AC-PTY-003,REQ-PTY-002,API-PTY-002 -->
### TC-PTY-177 — An in-process caller without the PTY authority is refused
Derived from : AC-PTY-003  (REQ-PTY-002)
Exercises    : API-PTY-002 through the in-process `PtyPartyApi.readCustomer` (same service method — ADR-PTY-039)
Rule / code  : PLATFORM-STD → PTY-403-FORBIDDEN
Package      : SEC-BE
Scenario     : PERMISSION · data class INVALID · language ALL
Preconditions: Customer 5001; a security context holding no PTY permission (a CUSTOMER-realm principal); a second context holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. under the first context, call `PtyPartyApi.readCustomer(5001, false)` · 2. under the second context, the same call
Expected     : step 1 → `LocalizedException` with code PTY-403-FORBIDDEN · step 2 → the Customer 5001 (code P-000120)
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-177:END -->
<!-- TC:TC-PTY-196:START traces=AC-PTY-138,REQ-PTY-002,API-PTY-002,API-PTY-016 -->
### TC-PTY-196 — Customer VIEW without Customer credit VIEW reads the credit values, not the standing
Derived from : AC-PTY-138  (REQ-PTY-002)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id} · API-PTY-016 GET /api/v1/pty/customer-credits/{customerId}
Rule / code  : PLATFORM-STD → PTY-403-FORBIDDEN (ADR-PTY-046)
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000, paymentTermDays 45 and currencyCode EGP; user U3 of a custom role holding PERM_PTY_CUSTOMER_VIEW only (no permission on PTY_CUSTOMER_CREDIT)
Host data    : none
Steps        : 1. as U3, GET /api/v1/pty/customers/5001 · 2. as U3, GET /api/v1/pty/customer-credits/5001
Expected     : step 1 → 200 · data.creditLimit 50000 · data.paymentTermDays 45 · data.currencyCode EGP · step 2 → 403 · code PTY-403-FORBIDDEN · messageAr «ليست لديك صلاحية لتنفيذ هذا الإجراء.» · messageEn «You do not have permission to perform this action.»
Test data    : values of the AC; U3 test-local
<!-- TC:TC-PTY-196:END -->

<!-- TC:TC-PTY-197:START traces=AC-PTY-139,REQ-PTY-041,API-PTY-027 -->
### TC-PTY-197 — Global search term with spaces and dashes matches a mobile; `%` matches literally
Derived from : AC-PTY-139  (REQ-PTY-041)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — (QR-PTY-011 parameters `:tDigits` and the escaping of `%` / `_`)
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: an active Customer `<customerId>` with mobile "+20 100 111 2222"; no Customer whose code, names, mobile, email or tax number contains "%%"; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=100 111-2222 · 2. GET /api/v1/pty/customers/global-search?term=%25%25 (the term "%%")
Expected     : step 1 → 200 · that Customer is in data, with isWalkInFl false · step 2 → 200 · data is an empty list (the two `%` match only a literal "%%", never every row)
Test data    : mobile "+20 100 111 2222", term "100 111-2222" (AC); "%%" test-local
<!-- TC:TC-PTY-197:END -->

<!-- TC:TC-PTY-199:START traces=AC-PTY-116,REQ-PTY-102,REQ-PTY-103,API-PTY-020 -->
### TC-PTY-199 — A requester without UPDATE cannot read another user's request
Derived from : AC-PTY-116  (REQ-PTY-102)
Exercises    : API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : PLATFORM-STD → PTY-404-CREDIT-OVERRIDE-NOT-FOUND (visibility — REQ-PTY-102 applied to reads, ADR-PTY-039)
Package      : SEC-BE
Scenario     : PERMISSION · data class INVALID · language ALL
Preconditions: Seller/Cashier S1 created CO-000035 and Seller/Cashier S2 created CO-000036; both hold PERM_PTY_CREDIT_OVERRIDE_VIEW and neither holds PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. as S1, GET /api/v1/pty/credit-overrides/{id of CO-000036} · 2. as S1, GET /api/v1/pty/credit-overrides/{id of CO-000035}
Expected     : step 1 → 404 · code PTY-404-CREDIT-OVERRIDE-NOT-FOUND · messageAr «طلب تجاوز حد الائتمان غير موجود.» · messageEn «The credit override request was not found.» (the request's existence is not revealed) · step 2 → 200, data.docNo CO-000035
Test data    : values of the AC
<!-- TC:TC-PTY-199:END -->

<!-- TC:TC-PTY-200:START traces=AC-PTY-045,REQ-PTY-039,API-PTY-038 -->
### TC-PTY-200 — PTY serves its own lookup key with the active values only
Derived from : AC-PTY-045  (REQ-PTY-039)
Exercises    : API-PTY-038 GET /api/v1/pty/lookups/{key}
Rule / code  : PLATFORM-STD → PTY-404-LOOKUP-KEY-UNKNOWN (OwnedLookups — ADR-PTY-048)
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: lookup CUSTOMER_GROUP holds RETAIL and WHOLESALE active (seeded) and VIP inactive; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW
Host data    : CUSTOMER_GROUP VIP — created active by the given mdl call POST /api/v1/mdl/lookup-types/{id}/values ({id} = the CUSTOMER_GROUP type found by POST /api/v1/mdl/lookup-types/search), then made inactive by DELETE /api/v1/mdl/lookup-values/{id} ({id} = the VIP value) — the same state as TC-PTY-020 (N4)
Steps        : 1. GET /api/v1/pty/lookups/CUSTOMER_GROUP · 2. GET /api/v1/pty/lookups/CAT_BRAND (a key another module owns)
Expected     : step 1 → 200 · data holds exactly RETAIL (labelAr «تجزئة», labelEn "Retail") and WHOLESALE (labelAr «جملة», labelEn "Wholesale"), in sortOrder; VIP is absent · step 2 → 404 · code PTY-404-LOOKUP-KEY-UNKNOWN · messageAr «هذه القائمة ليست من قوائم الأطراف. اختر إحدى قوائم الأطراف.» · messageEn «This list is not one of the party lists. Choose a party list.»
Test data    : RETAIL, WHOLESALE, VIP (SRS A6 `values`); CAT_BRAND test-local
<!-- TC:TC-PTY-200:END -->
<!-- SUB:API-SCENARIOS:END -->
