# BACKEND EXECUTION PLAN — Party / الأطراف (PTY)
══════════════════════════════════════════════════════════════════
Module : PTY   Version : v1   Profile : commerce   Stage : P3.1 (Backend Execution Plan) · pass 1 · track backend · plan exec
Inputs : srs (_state — REQ 109 · AC 123 · ENT 5 · RULE 33 · SCR-REQ 5), db-script (_state — 5 tables · DBF 67 · XM register empty · postgresql16 · identity PKs), registry-srs, registry-db, contract (contract-pty.md — CON-PTY-001 … CON-PTY-024), contracts (one published contract of another module — PTY consumes nothing from it)
Governance : FULL — the db-script is present; no entity is PENDING DB
Counts : API 37 (API-PTY-001 … API-PTY-037) · QR 38 (QR-PTY-001 … QR-PTY-038) · integration edges 0 · Error Catalog rows 36 · ADR raised 10 (ADR-PTY-023 … ADR-PTY-032 — all ACCEPTED, non-breaking, 0 BLOCKED) · P2 decisions ADR-PTY-018 … ADR-PTY-022 filed from the db-script §5.2 (ADR-PTY-030)
Status : DRAFT — P3.1 pass 1 · no question raised
══════════════════════════════════════════════════════════════════

The implementer reads this plan in order: index → DB alignment manifest → decisions → phases in profile order (CORE, DATA-DOM, SVC-API, DOC, SEC-BE, ALIGN-BE, INT-C) → Query Reference Catalog → Error Catalog. The API document `api-spec-pty.yaml` is derived from the `API-*` blocks and the Error Catalog of this plan, never written beside them.

## Execution Plan Index

```
EXECUTION PLAN INDEX — PTY v1 — backend-execution-plan-pty.md
Profile: commerce · dialect: postgresql16 · framework: Spring Boot (Java 25) on the given erp-core
Open ADRs: 0 — decisions/PTY/ (raised here: ADR-PTY-023 … ADR-PTY-032, all ACCEPTED)
```

### Entity registry

| ENT | Name (en / ar) | Kind | Table | Business code | OPERATIONS |
|---|---|---|---|---|---|
| ENT-PTY-001 | Party / الطرف | master | PTY_PARTY | Party code — column of DBF-PTY-007, format `P-` + 6 digits from series PTY_PARTY (ADR-PTY-029), unique UQ_PTY_PARTY_CODE, read-only (RULE-PTY-007) | VIEW, CREATE, UPDATE |
| ENT-PTY-002 | Customer / العميل | master | PTY_CUSTOMER | none of its own — presents the Party code (ADR-PTY-014) | VIEW, CREATE, UPDATE, DELETE |
| ENT-PTY-003 | Supplier / المورّد | master | PTY_SUPPLIER | none of its own — presents the Party code (ADR-PTY-014) | VIEW, CREATE, UPDATE, DELETE |
| ENT-PTY-004 | Address / العنوان | master | PTY_ADDRESS | none (DBF-PTY-044 is an ordinal within the Party, not a business number) | VIEW, CREATE, UPDATE, DELETE |
| ENT-PTY-005 | Credit override request / طلب تجاوز حد الائتمان | transactional | PTY_CREDIT_OVERRIDE_REQUEST | docNo — column of DBF-PTY-061, format `CO-` + 6 digits from series PTY_CREDIT_OVERRIDE (ADR-PTY-029), unique UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO, read-only (RULE-PTY-031) | VIEW, CREATE, UPDATE, DELETE |

### Field registry

| DBF range | ENT | Read-only (never accepted from a caller) |
|---|---|---|
| DBF-PTY-001 … DBF-PTY-012 | ENT-PTY-001 | DBF-PTY-001 (PK), DBF-PTY-007 (code), DBF-PTY-008 (derived from roles), DBF-PTY-009 … DBF-PTY-012 (audit) |
| DBF-PTY-013 … DBF-PTY-024 | ENT-PTY-002 | DBF-PTY-013 (PK), DBF-PTY-017 (set by the invitation handler), DBF-PTY-018 (walk-in), DBF-PTY-019 (set on create), DBF-PTY-021 … DBF-PTY-024 (audit) |
| DBF-PTY-025 … DBF-PTY-032 | ENT-PTY-003 | DBF-PTY-025 (PK), DBF-PTY-027 (set on create), DBF-PTY-029 … DBF-PTY-032 (audit) |
| DBF-PTY-033 … DBF-PTY-049 | ENT-PTY-004 | DBF-PTY-033 (PK), DBF-PTY-040 (system-maintained), DBF-PTY-041 (path), DBF-PTY-044 (ordinal), DBF-PTY-046 … DBF-PTY-049 (audit) |
| DBF-PTY-050 … DBF-PTY-067 | ENT-PTY-005 | DBF-PTY-050 (PK), DBF-PTY-052 … DBF-PTY-054, DBF-PTY-056, DBF-PTY-057, DBF-PTY-061 … DBF-PTY-063 (system), DBF-PTY-064 … DBF-PTY-067 (audit) |

### API registry

| API | Operation | Verb | Path | Traces (REQ — DBF in the block) |
|---|---|---|---|---|
| API-PTY-001 | create Customer / add Customer role | POST | /api/v1/pty/customers | REQ-PTY-004, 005, 006, 007, 008, 009, 010, 011, 026, 027, 029 |
| API-PTY-002 | read Customer | GET | /api/v1/pty/customers/{id} | REQ-PTY-001, 002, 003, 014, 071 |
| API-PTY-003 | update Customer | PUT | /api/v1/pty/customers/{id} | REQ-PTY-009, 010, 011, 012, 024, 039, 040 |
| API-PTY-004 | deactivate Customer | DELETE | /api/v1/pty/customers/{id} | REQ-PTY-014, 023, 028, 047 |
| API-PTY-005 | reactivate Customer | PATCH | /api/v1/pty/customers/{id}/activation | REQ-PTY-010, 015, 029 |
| API-PTY-006 | create Supplier / add Supplier role | POST | /api/v1/pty/suppliers | REQ-PTY-006, 008, 009, 010, 011, 016, 022, 024, 025, 027, 029, 049 |
| API-PTY-007 | read Supplier | GET | /api/v1/pty/suppliers/{id} | REQ-PTY-003, 019, 020 |
| API-PTY-008 | update Supplier | PUT | /api/v1/pty/suppliers/{id} | REQ-PTY-009, 010, 011, 017, 022, 024 |
| API-PTY-009 | deactivate Supplier | DELETE | /api/v1/pty/suppliers/{id} | REQ-PTY-020, 023, 028 |
| API-PTY-010 | reactivate Supplier | PATCH | /api/v1/pty/suppliers/{id}/activation | REQ-PTY-010, 021, 029 |
| API-PTY-011 | create Address | POST | /api/v1/pty/parties/{partyId}/addresses | REQ-PTY-030, 034, 037, 038 |
| API-PTY-012 | read Address | GET | /api/v1/pty/addresses/{id} | REQ-PTY-033 |
| API-PTY-013 | update Address | PUT | /api/v1/pty/parties/{partyId}/addresses/{id} | REQ-PTY-031, 037, 038 |
| API-PTY-014 | deactivate Address | DELETE | /api/v1/pty/parties/{partyId}/addresses/{id} | REQ-PTY-033, 036 |
| API-PTY-015 | set default Address | PATCH | /api/v1/pty/parties/{partyId}/addresses/{id}/default | REQ-PTY-035 |
| API-PTY-016 | read credit standing | GET | /api/v1/pty/customer-credits/{customerId} | REQ-PTY-068, 070, 073, 080, 085 |
| API-PTY-017 | update credit | PUT | /api/v1/pty/customer-credits/{customerId} | REQ-PTY-048, 064, 065, 066, 067, 072, 073, 106, 109 |
| API-PTY-018 | clear credit | DELETE | /api/v1/pty/customer-credits/{customerId} | REQ-PTY-078, 106, 109 |
| API-PTY-019 | create Credit override request | POST | /api/v1/pty/credit-overrides | REQ-PTY-072, 089, 091, 092, 093, 104, 105 |
| API-PTY-020 | read Credit override request | GET | /api/v1/pty/credit-overrides/{id} | REQ-PTY-103 |
| API-PTY-021 | approve Credit override request | PATCH | /api/v1/pty/credit-overrides/{id}/approval | REQ-PTY-094, 098, 107 |
| API-PTY-022 | reject Credit override request | PATCH | /api/v1/pty/credit-overrides/{id}/rejection | REQ-PTY-095, 098, 100, 107 |
| API-PTY-023 | cancel Credit override request | DELETE | /api/v1/pty/credit-overrides/{id} | REQ-PTY-096, 098 |
| API-PTY-024 | read credit settings | GET | /api/v1/pty/credit-settings | REQ-PTY-070, 087 |
| API-PTY-025 | update credit settings | PUT | /api/v1/pty/credit-settings | REQ-PTY-086, 088, 089, 090 |
| API-PTY-026 | search Customers | GET | /api/v1/pty/customers | REQ-PTY-001, 013 |
| API-PTY-027 | Customer global search | GET | /api/v1/pty/customers/global-search | REQ-PTY-041, 042, 043 |
| API-PTY-028 | search Suppliers | GET | /api/v1/pty/suppliers | REQ-PTY-018 |
| API-PTY-029 | list Party addresses | GET | /api/v1/pty/parties/{partyId}/addresses | REQ-PTY-032 |
| API-PTY-030 | search Customer credit | GET | /api/v1/pty/customer-credits | REQ-PTY-069, 070, 073, 077 |
| API-PTY-031 | read credit history | GET | /api/v1/pty/customer-credits/{customerId}/history | REQ-PTY-108 |
| API-PTY-032 | search Credit override requests | GET | /api/v1/pty/credit-overrides | REQ-PTY-101, 102 |
| API-PTY-033 | default Customer for a POS sale (walk-in) | GET | /api/v1/pty/customers/walk-in | REQ-PTY-044, 045, 046 |
| API-PTY-034 | guest order customer resolution | POST | /api/v1/pty/guest-customers | REQ-PTY-001, 050, 051, 053, 055 … 063 |
| API-PTY-035 | credit check | POST | /api/v1/pty/credit-checks | REQ-PTY-068, 074, 079 … 084, 087, 088, 089 |
| API-PTY-036 | use Credit override request | PATCH | /api/v1/pty/credit-overrides/{id}/use | REQ-PTY-097, 099 |
| API-PTY-037 | `b2bCredit` switch-off dependency | GET | /api/v1/pty/flag-dependencies/b2bCredit | REQ-PTY-075, 076, 077 |

### Rule registry

| RULE | Name | Scope | Messages ar/en | Enforced by |
|---|---|---|---|---|
| RULE-PTY-001 | Party name required | ENT-PTY-001 | ✓ | API-PTY-001, 003, 006, 008, 034 · PTY-422-NAME-REQUIRED · CHK_PTY_PARTY_NAME |
| RULE-PTY-002 | Email unique among active Parties | ENT-PTY-001 | ✓ | API-PTY-001, 003, 005, 006, 008, 010 · PTY-409-EMAIL-TAKEN · UQ_PTY_PARTY_EMAIL |
| RULE-PTY-003 | Email format | ENT-PTY-001 | ✓ | API-PTY-001, 003, 006, 008, 034 · PTY-422-EMAIL-FORMAT · CHK_PTY_PARTY_EMAIL |
| RULE-PTY-004 | Mobile format | ENT-PTY-001 | ✓ | API-PTY-001, 003, 006, 008, 034 · PTY-422-MOBILE-FORMAT · CHK_PTY_PARTY_MOBILE |
| RULE-PTY-005 | Inactive role refused for new documents | ENT-PTY-002, ENT-PTY-003 | ✓ | API-PTY-002, 007 · PTY-422-ROLE-INACTIVE |
| RULE-PTY-006 | Effect of deactivation | ENT-PTY-002, 003, 004 | ✓ (confirmation) | API-PTY-004, 009, 014 — success message + event, no error |
| RULE-PTY-007 | Party code system-assigned, read-only | ENT-PTY-001 | ✓ | API-PTY-003, 008 · PTY-422-CODE-READ-ONLY · UQ_PTY_PARTY_CODE |
| RULE-PTY-008 | Payment terms range | ENT-PTY-002, ENT-PTY-003 | ✓ | API-PTY-006, 008, 017 · PTY-422-PAYMENT-TERMS-RANGE · CHK_PTY_CUSTOMER_PAYMENT_TERM_DAYS, CHK_PTY_SUPPLIER_PAYMENT_TERM_DAYS |
| RULE-PTY-009 | One role of each kind per Party | ENT-PTY-002, ENT-PTY-003 | ✓ | API-PTY-001, 006 · PTY-409-ROLE-HELD · UQ_PTY_CUSTOMER_PARTY_FK, UQ_PTY_SUPPLIER_PARTY_FK |
| RULE-PTY-010 | Walk-in Customer stays active | ENT-PTY-002 | ✓ | API-PTY-004 · PTY-422-WALK-IN-DEACTIVATE · CHK_PTY_CUSTOMER_WALK_IN_ACTIVE |
| RULE-PTY-011 | Walk-in Customer carries no credit | ENT-PTY-002 | ✓ | API-PTY-017 · PTY-422-WALK-IN-CREDIT · CHK_PTY_CUSTOMER_WALK_IN_CREDIT |
| RULE-PTY-012 | Walk-in Party has no Supplier | ENT-PTY-002, ENT-PTY-003 | ✓ | API-PTY-006 · PTY-422-WALK-IN-SUPPLIER |
| RULE-PTY-013 | Walk-in never matched nor linked | ENT-PTY-001, ENT-PTY-002 | ✓ | API-PTY-034 (matching filter, QR-PTY-036, QR-PTY-037) · invitation handler (in-process, ADR-PTY-025) · CHK_PTY_CUSTOMER_WALK_IN_SEC_USER |
| RULE-PTY-014 | One walk-in Customer per tenant | ENT-PTY-002 | ✓ | API-PTY-033 (idempotent ensure) · UQ_PTY_CUSTOMER_IS_WALK_IN_FL |
| RULE-PTY-015 | Address line required | ENT-PTY-004 | ✓ | API-PTY-011, 013, 034 · PTY-422-ADDRESS-LINE-REQUIRED · CHK_PTY_ADDRESS_LINE1 |
| RULE-PTY-016 | Address type from the lookup | ENT-PTY-004 | ✓ | API-PTY-011, 013 · PTY-422-ADDRESS-TYPE-INVALID |
| RULE-PTY-017 | One default Address per type per Party | ENT-PTY-004 | ✓ (confirmation) | API-PTY-011, 013, 014, 015 · UQ_PTY_ADDRESS_IS_DEFAULT_FL — success message, no error |
| RULE-PTY-018 | Customer group from the lookup | ENT-PTY-002 | ✓ | API-PTY-001, 003 · PTY-422-CUSTOMER-GROUP-INVALID |
| RULE-PTY-019 | Guest contact required | ENT-PTY-001 | ✓ | API-PTY-034 · PTY-422-GUEST-CONTACT-REQUIRED |
| RULE-PTY-020 | One Customer per CUSTOMER-realm user | ENT-PTY-002 | ✓ | invitation handler (in-process, ADR-PTY-025) · UQ_PTY_CUSTOMER_SEC_USER_ID |
| RULE-PTY-021 | Guest matched to an inactive Customer | ENT-PTY-002 | ✓ | API-PTY-034 · PTY-422-GUEST-NOT-COMPLETED |
| RULE-PTY-022 | Credit limit not negative | ENT-PTY-002 | ✓ | API-PTY-017 · PTY-422-CREDIT-LIMIT-NEGATIVE · CHK_PTY_CUSTOMER_CREDIT_LIMIT |
| RULE-PTY-023 | Credit data only while the flag is on | ENT-PTY-002, ENT-PTY-005 | ✓ | API-PTY-016, 017, 018, 019, 030, 031 · PTY-422-CREDIT-NOT-ENABLED |
| RULE-PTY-024 | Flag switch-off blocked while credit is in use | ENT-PTY-002 | ✓ (answer) | API-PTY-037 — returned as the answer to the flag owner, no error |
| RULE-PTY-025 | Request only for a real overrun | ENT-PTY-005 | ✓ | API-PTY-019 · PTY-422-OVERRIDE-NOT-NEEDED · CHK_PTY_CREDIT_OVERRIDE_REQUEST_CREDIT_CHECK_RESULT_CODE |
| RULE-PTY-026 | Decisions only on PENDING requests | ENT-PTY-005 | ✓ | API-PTY-021, 022, 023 · PTY-409-REQUEST-NOT-PENDING |
| RULE-PTY-027 | Rejection reason required | ENT-PTY-005 | ✓ | API-PTY-022 · PTY-422-REJECTION-REASON-REQUIRED · CHK_PTY_CREDIT_OVERRIDE_REQUEST_DECISION_REASON |
| RULE-PTY-028 | Use only within the approval | ENT-PTY-005 | ✓ | API-PTY-036 · PTY-422-APPROVAL-NOT-COVERING |
| RULE-PTY-029 | Only the requester cancels | ENT-PTY-005 | ✓ | API-PTY-023 · PTY-403-NOT-REQUESTER |
| RULE-PTY-030 | Credit change recorded or not saved | ENT-PTY-002 | ✓ | API-PTY-017, 018 · PTY-500-AUDIT-NOT-RECORDED |
| RULE-PTY-031 | Request number system-assigned, read-only | ENT-PTY-005 | ✓ | API-PTY-021, 022, 036 · PTY-422-DOC-NO-READ-ONLY · UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO |
| RULE-PTY-032 | Sale amount positive | ENT-PTY-005 | ✓ | API-PTY-019 · PTY-422-SALE-AMOUNT-NOT-POSITIVE · CHK_PTY_CREDIT_OVERRIDE_REQUEST_SALE_AMOUNT |
| RULE-PTY-033 | No credit override for the walk-in Customer | ENT-PTY-005 | ✓ | API-PTY-019 · PTY-422-WALK-IN-NO-CREDIT-SALE |

No RULE is DEFERRED: every `Data source` resolves to a column of the db-script (registry-srs "DEFERRED rules : none").

### Screen registry

| Screen | Type | ENT | Page code | Permission names |
|---|---|---|---|---|
| SCR-REQ-PTY-001 Customers / العملاء | composite (Search + Entry) | ENT-PTY-001, ENT-PTY-002, ENT-PTY-004 | PTY_CUSTOMER | PERM_PTY_CUSTOMER_VIEW, PERM_PTY_CUSTOMER_CREATE, PERM_PTY_CUSTOMER_UPDATE, PERM_PTY_CUSTOMER_DELETE |
| SCR-REQ-PTY-002 Suppliers / الموردون | composite (Search + Entry) | ENT-PTY-001, ENT-PTY-003, ENT-PTY-004 | PTY_SUPPLIER | PERM_PTY_SUPPLIER_VIEW, PERM_PTY_SUPPLIER_CREATE, PERM_PTY_SUPPLIER_UPDATE, PERM_PTY_SUPPLIER_DELETE |
| SCR-REQ-PTY-003 Customer credit / ائتمان العملاء | composite (Search + Entry), flag `b2bCredit` | ENT-PTY-001, ENT-PTY-002 | PTY_CUSTOMER_CREDIT | PERM_PTY_CUSTOMER_CREDIT_VIEW, PERM_PTY_CUSTOMER_CREDIT_UPDATE |
| SCR-REQ-PTY-004 Credit override requests / طلبات تجاوز حد الائتمان | composite (Search + Entry), flag `b2bCredit` | ENT-PTY-005, ENT-PTY-002 | PTY_CREDIT_OVERRIDE | PERM_PTY_CREDIT_OVERRIDE_VIEW, PERM_PTY_CREDIT_OVERRIDE_CREATE, PERM_PTY_CREDIT_OVERRIDE_UPDATE, PERM_PTY_CREDIT_OVERRIDE_DELETE |
| SCR-REQ-PTY-005 Credit control settings / إعدادات ضبط الائتمان | single entry, flag `b2bCredit` | ENT-PTY-002 | PTY_CREDIT_SETTINGS | PERM_PTY_CREDIT_SETTINGS_VIEW, PERM_PTY_CREDIT_SETTINGS_UPDATE |

### Screen demand resolution

Every operation on each SRS screen's `Operations` line, read verbatim, closed by an `API-*` that names the operation and an `ENT-*` of the screen's `Entities` line. No operation is excluded and none is deferred.

| Screen | SRS operation (verbatim) | API (ENT named) |
|---|---|---|
| SCR-REQ-PTY-001 | search · list | API-PTY-026 (ENT-PTY-002, ENT-PTY-001) |
| SCR-REQ-PTY-001 | create | API-PTY-001 (ENT-PTY-002, ENT-PTY-001) |
| SCR-REQ-PTY-001 | read | API-PTY-002 (ENT-PTY-002, ENT-PTY-001) |
| SCR-REQ-PTY-001 | update | API-PTY-003 (ENT-PTY-002, ENT-PTY-001) |
| SCR-REQ-PTY-001 | deactivate | API-PTY-004 (ENT-PTY-002) |
| SCR-REQ-PTY-001 | reactivate | API-PTY-005 (ENT-PTY-002) |
| SCR-REQ-PTY-001 | add Supplier role | API-PTY-006 (ENT-PTY-001) |
| SCR-REQ-PTY-001 | address create / update / deactivate / set default | API-PTY-011 / API-PTY-013 / API-PTY-014 / API-PTY-015 (ENT-PTY-004) |
| SCR-REQ-PTY-002 | search · list | API-PTY-028 (ENT-PTY-003, ENT-PTY-001) |
| SCR-REQ-PTY-002 | create · read · update | API-PTY-006 · API-PTY-007 · API-PTY-008 (ENT-PTY-003, ENT-PTY-001) |
| SCR-REQ-PTY-002 | deactivate · reactivate | API-PTY-009 · API-PTY-010 (ENT-PTY-003) |
| SCR-REQ-PTY-002 | add Customer role | API-PTY-001 (ENT-PTY-001) |
| SCR-REQ-PTY-002 | address create / update / deactivate / set default | API-PTY-011 / API-PTY-013 / API-PTY-014 / API-PTY-015 (ENT-PTY-004) |
| SCR-REQ-PTY-003 | search · list | API-PTY-030 (ENT-PTY-002, ENT-PTY-001) |
| SCR-REQ-PTY-003 | read (credit standing) | API-PTY-016 (ENT-PTY-002, ENT-PTY-001) |
| SCR-REQ-PTY-003 | update (credit limit, payment terms) | API-PTY-017 (ENT-PTY-002) |
| SCR-REQ-PTY-003 | clear credit | API-PTY-018 (ENT-PTY-002) |
| SCR-REQ-PTY-003 | read credit history | API-PTY-031 (ENT-PTY-002) |
| SCR-REQ-PTY-004 | search · list | API-PTY-032 (ENT-PTY-005, ENT-PTY-002) |
| SCR-REQ-PTY-004 | create (from a sale) | API-PTY-019 (ENT-PTY-005, ENT-PTY-002) |
| SCR-REQ-PTY-004 | read | API-PTY-020 (ENT-PTY-005) |
| SCR-REQ-PTY-004 | approve · reject · cancel | API-PTY-021 · API-PTY-022 · API-PTY-023 (ENT-PTY-005) |
| SCR-REQ-PTY-005 | read · update | API-PTY-024 · API-PTY-025 (ENT-PTY-002) |

### QRC summary (agent reference only — the catalog follows the last phase)

| QR | Operation | Phase | ENT |
|---|---|---|---|
| QR-PTY-001 | lock Party by PK | DATA-DOM | ENT-PTY-001 |
| QR-PTY-002 | insert Party | DATA-DOM | ENT-PTY-001 |
| QR-PTY-003 | update Party | DATA-DOM | ENT-PTY-001 |
| QR-PTY-004 | find the active Party holding an email | DATA-DOM | ENT-PTY-001 |
| QR-PTY-005 | read Customer with its Party | SVC-API | ENT-PTY-002 |
| QR-PTY-006 | lock Customer by PK | DATA-DOM | ENT-PTY-002 |
| QR-PTY-007 | find the Customer of a Party | DATA-DOM | ENT-PTY-002 |
| QR-PTY-008 | insert Customer | DATA-DOM | ENT-PTY-002 |
| QR-PTY-009 | update Customer | DATA-DOM | ENT-PTY-002 |
| QR-PTY-010 | search Customers | SVC-API | ENT-PTY-002 |
| QR-PTY-011 | Customer global search | SVC-API | ENT-PTY-002 |
| QR-PTY-012 | find the walk-in Customer | SVC-API | ENT-PTY-002 |
| QR-PTY-013 | search Customer credit | SVC-API | ENT-PTY-002 |
| QR-PTY-014 | count Customers carrying credit | SVC-API | ENT-PTY-002 |
| QR-PTY-015 | read Supplier with its Party | SVC-API | ENT-PTY-003 |
| QR-PTY-016 | lock Supplier by PK | DATA-DOM | ENT-PTY-003 |
| QR-PTY-017 | find the Supplier of a Party | DATA-DOM | ENT-PTY-003 |
| QR-PTY-018 | insert Supplier | DATA-DOM | ENT-PTY-003 |
| QR-PTY-019 | update Supplier | DATA-DOM | ENT-PTY-003 |
| QR-PTY-020 | search Suppliers | SVC-API | ENT-PTY-003 |
| QR-PTY-021 | count the active roles of a Party | DATA-DOM | ENT-PTY-001 |
| QR-PTY-022 | read Address by PK | SVC-API | ENT-PTY-004 |
| QR-PTY-023 | lock Address of a Party | DATA-DOM | ENT-PTY-004 |
| QR-PTY-024 | next Address ordinal of a Party | DATA-DOM | ENT-PTY-004 |
| QR-PTY-025 | insert Address | DATA-DOM | ENT-PTY-004 |
| QR-PTY-026 | update Address | DATA-DOM | ENT-PTY-004 |
| QR-PTY-027 | list active Addresses of a Party | SVC-API | ENT-PTY-004 |
| QR-PTY-028 | find the default Address of a type | DATA-DOM | ENT-PTY-004 |
| QR-PTY-029 | find the most recently updated remaining Address of a type | DATA-DOM | ENT-PTY-004 |
| QR-PTY-030 | exists active Address of a type | DATA-DOM | ENT-PTY-004 |
| QR-PTY-031 | insert Credit override request | DATA-DOM | ENT-PTY-005 |
| QR-PTY-032 | read Credit override request | SVC-API | ENT-PTY-005 |
| QR-PTY-033 | lock Credit override request | DATA-DOM | ENT-PTY-005 |
| QR-PTY-034 | conditional status update of a request | DATA-DOM | ENT-PTY-005 |
| QR-PTY-035 | search Credit override requests | SVC-API | ENT-PTY-005 |
| QR-PTY-036 | match an active Party by email for a guest | SVC-API | ENT-PTY-001 |
| QR-PTY-037 | match active Parties by normalised mobile for a guest | SVC-API | ENT-PTY-001 |
| QR-PTY-038 | exists Party by PK | DATA-DOM | ENT-PTY-001 |

```
DB ALIGNMENT  see manifest below — ALIGNED ✓ / issues: 0
INTEGRATION   0 edges — the XM register is empty (SRS A8 consumes: []); `INT-C`, the last phase, carries no block (R7)
GIVEN         10 uses — the SRS A9 `given-usage` rows (erp-core); every one goes through the `via` the row names; this plan adds none
SECURITY      5 screens × 3 roles (Owner, Seller/Cashier, Warehouse)
```

### Totals

```yaml name=totals
DBF: 67
XM: 1
API: 37
QR: 38
```

`XM` is the number of distinct `XM-*` ids the db-script carries, as the totals rule defines it: the only one is `XM-PTY-001`, named in the db-script §6 as the *next free* XM id. The XM register (`xm-register`) holds 0 records, so this plan has 0 integration edges and `INT-C` carries no block (ADR-PTY-032).

## DB Alignment Manifest

Canonical binding of plan properties to db-script fields. Column names and DB types are sourced by lookup from the db-script (its `dbf-matrix`); this manifest does not reproduce them. Java types follow the type mapping of PHASE 1 — CORE. Every required column that no endpoint writes states its reason; every other required column is named on the `Request` or `Orchestration` line of at least one `API-*` block.

```
DB ALIGNMENT MANIFEST — PTY v1
```

| DBF | ENT | Plan property | Plan type | XM | Status |
|---|---|---|---|---|---|
| DBF-PTY-001 | ENT-PTY-001 | partyPk | Long | — | ✓ system-generated (identity) |
| DBF-PTY-002 | ENT-PTY-001 | mobile | String | — | ✓ |
| DBF-PTY-003 | ENT-PTY-001 | email | String | — | ✓ |
| DBF-PTY-004 | ENT-PTY-001 | taxRegistrationNo | String | — | ✓ |
| DBF-PTY-005 | ENT-PTY-001 | nameAr | String | — | ✓ |
| DBF-PTY-006 | ENT-PTY-001 | nameEn | String | — | ✓ |
| DBF-PTY-007 | ENT-PTY-001 | code | String | — | ✓ (Orchestration of API-PTY-001, 006, 033, 034 — NumberSeriesApi) |
| DBF-PTY-008 | ENT-PTY-001 | isActiveFl | Boolean | — | ✓ (Orchestration of API-PTY-001, 004, 005, 006, 009, 010) |
| DBF-PTY-009 | ENT-PTY-001 | createdBy | String | — | ✓ system-generated (platform auditing) |
| DBF-PTY-010 | ENT-PTY-001 | createdAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |
| DBF-PTY-011 | ENT-PTY-001 | updatedBy | String | — | ✓ system-generated (platform auditing) |
| DBF-PTY-012 | ENT-PTY-001 | updatedAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |
| DBF-PTY-013 | ENT-PTY-002 | customerPk | Long | — | ✓ system-generated (identity) |
| DBF-PTY-014 | ENT-PTY-002 | customerGroupCode | String | — | ✓ (Request of API-PTY-001, 003) |
| DBF-PTY-015 | ENT-PTY-002 | creditLimit | BigDecimal | — | ✓ |
| DBF-PTY-016 | ENT-PTY-002 | paymentTermDays | Integer | — | ✓ |
| DBF-PTY-017 | ENT-PTY-002 | secUserId | Long | — | ✓ (set by the in-process invitation handler, SVC-API-INT) |
| DBF-PTY-018 | ENT-PTY-002 | isWalkInFl | Boolean | — | ✓ (Orchestration of API-PTY-001, 033, 034) |
| DBF-PTY-019 | ENT-PTY-002 | partyFk | Long | — | ✓ (Request / Orchestration of API-PTY-001, 033, 034) |
| DBF-PTY-020 | ENT-PTY-002 | isActiveFl | Boolean | — | ✓ (Orchestration of API-PTY-001, 004, 005, 033, 034) |
| DBF-PTY-021 | ENT-PTY-002 | createdBy | String | — | ✓ system-generated (platform auditing) |
| DBF-PTY-022 | ENT-PTY-002 | createdAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |
| DBF-PTY-023 | ENT-PTY-002 | updatedBy | String | — | ✓ system-generated (platform auditing) |
| DBF-PTY-024 | ENT-PTY-002 | updatedAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |
| DBF-PTY-025 | ENT-PTY-003 | supplierPk | Long | — | ✓ system-generated (identity) |
| DBF-PTY-026 | ENT-PTY-003 | paymentTermDays | Integer | — | ✓ |
| DBF-PTY-027 | ENT-PTY-003 | partyFk | Long | — | ✓ (Request / Orchestration of API-PTY-006) |
| DBF-PTY-028 | ENT-PTY-003 | isActiveFl | Boolean | — | ✓ (Orchestration of API-PTY-006, 009, 010) |
| DBF-PTY-029 | ENT-PTY-003 | createdBy | String | — | ✓ system-generated (platform auditing) |
| DBF-PTY-030 | ENT-PTY-003 | createdAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |
| DBF-PTY-031 | ENT-PTY-003 | updatedBy | String | — | ✓ system-generated (platform auditing) |
| DBF-PTY-032 | ENT-PTY-003 | updatedAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |
| DBF-PTY-033 | ENT-PTY-004 | addressPk | Long | — | ✓ system-generated (identity) |
| DBF-PTY-034 | ENT-PTY-004 | addressTypeCode | String | — | ✓ (Request of API-PTY-011, 013; Orchestration of API-PTY-034) |
| DBF-PTY-035 | ENT-PTY-004 | line1 | String | — | ✓ (Request of API-PTY-011, 013, 034) |
| DBF-PTY-036 | ENT-PTY-004 | line2 | String | — | ✓ |
| DBF-PTY-037 | ENT-PTY-004 | city | String | — | ✓ |
| DBF-PTY-038 | ENT-PTY-004 | region | String | — | ✓ |
| DBF-PTY-039 | ENT-PTY-004 | postalCode | String | — | ✓ |
| DBF-PTY-040 | ENT-PTY-004 | isDefaultFl | Boolean | — | ✓ (Orchestration of API-PTY-011, 013, 014, 015, 034) |
| DBF-PTY-041 | ENT-PTY-004 | partyFk | Long | — | ✓ (Request of API-PTY-011 — path; Orchestration of API-PTY-034) |
| DBF-PTY-042 | ENT-PTY-004 | nameAr | String | — | ✓ |
| DBF-PTY-043 | ENT-PTY-004 | nameEn | String | — | ✓ |
| DBF-PTY-044 | ENT-PTY-004 | code | String | — | ✓ (Orchestration of API-PTY-011, 034 — ordinal) |
| DBF-PTY-045 | ENT-PTY-004 | isActiveFl | Boolean | — | ✓ (Orchestration of API-PTY-011, 014, 034) |
| DBF-PTY-046 | ENT-PTY-004 | createdBy | String | — | ✓ system-generated (platform auditing) |
| DBF-PTY-047 | ENT-PTY-004 | createdAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |
| DBF-PTY-048 | ENT-PTY-004 | updatedBy | String | — | ✓ system-generated (platform auditing) |
| DBF-PTY-049 | ENT-PTY-004 | updatedAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |
| DBF-PTY-050 | ENT-PTY-005 | creditOverrideRequestPk | Long | — | ✓ system-generated (identity) |
| DBF-PTY-051 | ENT-PTY-005 | saleAmount | BigDecimal | — | ✓ (Request of API-PTY-019) |
| DBF-PTY-052 | ENT-PTY-005 | creditLimitAtRequest | BigDecimal | — | ✓ |
| DBF-PTY-053 | ENT-PTY-005 | openExposure | BigDecimal | — | ✓ |
| DBF-PTY-054 | ENT-PTY-005 | creditCheckResultCode | String | — | ✓ (Orchestration of API-PTY-019) |
| DBF-PTY-055 | ENT-PTY-005 | sourceDocumentRef | String | — | ✓ (Request of API-PTY-019) |
| DBF-PTY-056 | ENT-PTY-005 | decidedBy | Long | — | ✓ |
| DBF-PTY-057 | ENT-PTY-005 | decidedAt | OffsetDateTime | — | ✓ |
| DBF-PTY-058 | ENT-PTY-005 | decisionReason | String | — | ✓ |
| DBF-PTY-059 | ENT-PTY-005 | usedDocumentRef | String | — | ✓ |
| DBF-PTY-060 | ENT-PTY-005 | customerFk | Long | — | ✓ (Request of API-PTY-019 — customerId) |
| DBF-PTY-061 | ENT-PTY-005 | docNo | String | — | ✓ (Orchestration of API-PTY-019 — NumberSeriesApi) |
| DBF-PTY-062 | ENT-PTY-005 | docDate | OffsetDateTime | — | ✓ (Orchestration of API-PTY-019) |
| DBF-PTY-063 | ENT-PTY-005 | statusCode | String | — | ✓ (Orchestration of API-PTY-019, 021, 022, 023, 036) |
| DBF-PTY-064 | ENT-PTY-005 | createdBy | String | — | ✓ system-generated (platform auditing — the requester) |
| DBF-PTY-065 | ENT-PTY-005 | createdAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |
| DBF-PTY-066 | ENT-PTY-005 | updatedBy | String | — | ✓ system-generated (platform auditing) |
| DBF-PTY-067 | ENT-PTY-005 | updatedAt | OffsetDateTime | — | ✓ system-generated (platform auditing) |

Derived / computed properties (no column — ADR-PTY-028):

| DBF | ENT | Plan property | Plan type | Source | Status |
|---|---|---|---|---|---|
| — (derived) | ENT-PTY-002 | openExposure | BigDecimal | sum of the registered `CustomerCreditExposureContributor` answers (REQ-PTY-080) | ✓ ADR-PTY-028 |
| — (derived) | ENT-PTY-002 | projectedExposure | BigDecimal | openExposure + saleAmount (REQ-PTY-079) | ✓ ADR-PTY-028 |
| — (derived) | ENT-PTY-002 | availableCredit | BigDecimal | creditLimit − openExposure (REQ-PTY-085) | ✓ ADR-PTY-028 |
| — (derived) | ENT-PTY-002 | creditCheckResult / overrunActionCode / approvalRequired | String / String / Boolean | credit check algorithm (REQ-PTY-079 … REQ-PTY-089) | ✓ ADR-PTY-028 |
| — (derived) | ENT-PTY-002 | carriesCredit | Boolean | creditLimit or paymentTermDays non-empty (REQ-PTY-069) | ✓ ADR-PTY-028 |
| — (derived) | ENT-PTY-002 | creditOverrunActionCode | String | tenant setting through the given SettingsApi, default BLOCK_WITH_OVERRIDE (REQ-PTY-087) | ✓ ADR-PTY-028 |
| — (derived) | ENT-PTY-002 | credit history entries | list | the given AuditApi (REQ-PTY-108) | ✓ ADR-PTY-028 |
| — (derived) | ENT-PTY-002, ENT-PTY-003 | code / nameAr / nameEn of a role | String | read through the role's partyFk from the Party (DBF-PTY-007, DBF-PTY-005, DBF-PTY-006) — never stored twice | ✓ ADR-PTY-027 |

Legend ✓ aligned · ✗ type mismatch (finding). Issues: 0.

## Decisions

| ADR | Title | Status | Applied in |
|---|---|---|---|
| ADR-PTY-011 … ADR-PTY-013 | inherited (P0.5) — priorities, supplier payment terms flag-independent, customers only in the global search | ACCEPTED | API-PTY-006, 008 (supplier terms), API-PTY-027 (customers only) |
| ADR-PTY-014 | Party identity with Customer and Supplier roles | ACCEPTED | every role and address block |
| ADR-PTY-015 | Guest matching, contact normalisation, CUSTOMER-realm invitation | ACCEPTED | API-PTY-034, invitation handler |
| ADR-PTY-016 | Credit override request, single step, sale held as text | ACCEPTED | API-PTY-019 … 023, 032, 036 |
| ADR-PTY-017 | Open exposure from contributors; empty limit = no credit; switch-off answer | ACCEPTED | API-PTY-016, 035, 037 |
| ADR-PTY-018 … ADR-PTY-022 | (P2) the five structural decisions the db-script records in its §5.2 (physical precision, audit principal columns, no currency column, tenant scoping, lookup seed) — applied as the db-script states them; filed verbatim from §5.2 by ADR-PTY-030 | ACCEPTED | every binding |
| ADR-PTY-023 | ADR sequence continued at 023 after the db-script's recorded decisions | ACCEPTED (new) | this plan |
| ADR-PTY-024 | Error Catalog: PLATFORM-STD rows, status mapping and slugs | ACCEPTED (new) | Error Catalog |
| ADR-PTY-025 | Permissions the SRS leaves open; in-process callers; invitation-handler outcomes | ACCEPTED (new) | SEC-BE, API-PTY-005, 010 … 015, 027, 033 … 037 |
| ADR-PTY-026 | Concurrency guards and Address default on a type change | ACCEPTED (new) | every write endpoint |
| ADR-PTY-027 | Query strategy — Party join, normalised search and matching as native queries | ACCEPTED (new) | QR-PTY-005, 010, 011, 013, 015, 020, 035, 036, 037 |
| ADR-PTY-028 | Credit computation and derived properties | ACCEPTED (new) | API-PTY-016, 019, 030, 031, 035, 037 |
| ADR-PTY-029 | Bootstrap — number series, setting key, role grants, walk-in provisioning | ACCEPTED (new) | SEC-BE `bootstrap` |
| ADR-PTY-030 | P2 decisions ADR-PTY-018 … ADR-PTY-022 filed from the db-script §5.2 | ACCEPTED (new) | decisions/PTY/ |
| ADR-PTY-031 | Typed bodies for the reactivation and set-default PATCH endpoints | ACCEPTED (new) | API-PTY-005, 010, 015 |
| ADR-PTY-032 | The XM total counts the XM ids the db-script names | ACCEPTED (new) | `totals` |

BLOCKED : none.

<!-- PHASE:CORE:START traces=REQ-PTY-006,REQ-PTY-024,REQ-PTY-070,REQ-PTY-073,REQ-PTY-092 -->
## PHASE 1 — CORE

Architecture policies declared once for the module. Everything below applies to every endpoint of SVC-API.

**Module layout (Spring Boot, Java 25, on the given erp-core).** Root package `com.erp.pty`; one sub-package per aggregate: `party` (ENT-PTY-001), `customer` (ENT-PTY-002), `supplier` (ENT-PTY-003), `address` (ENT-PTY-004), `creditoverride` (ENT-PTY-005), `credit` (credit check, credit standing, settings, exposure), `guest` (guest resolution, invitation handler). Each holds `*Controller` (entry layer, `@RestController`, request/response DTOs, permission annotation), `*Service` (transaction boundary, rules, orchestration), `*Repository` (Spring Data JPA; queries per the QRC), `*Entity` (JPA mapping per the DB Alignment Manifest). Controllers never touch repositories; services never check a role name — permission names only.

**Published in-process surface (this module's own, `profile.conventions.module_interface: in_process`).** Package `com.erp.pty.crossmodule`:
- `PtyPartyApi` — `readCustomer`, `readSupplier`, `listPartyAddresses`, `readAddress`, `defaultPosCustomer`, `resolveGuestCustomer`, `checkCredit`, `createCreditOverrideRequest`, `readCreditOverrideRequest`, `useCreditOverrideRequest`, `b2bCreditSwitchOffDependency`, `globalSearchCustomers`, `registerCustomer`, `registerSupplier` — each delegates to the same service method as the REST endpoint that honours the same contract item (the `Honours` line of the `API-*` block), so the REST document and the in-process call cannot diverge.
- `CustomerCreditExposureContributor` — the extension point (CON-PTY-022): `BigDecimal openExposure(Long customerId)`; implemented by downstream modules as Spring beans; PTY discovers every bean of the type (zero beans → exposure 0).
- `PartyRoleDeactivatedEvent` — the domain event (CON-PTY-021): `{String role (CUSTOMER | SUPPLIER), Long identifier, String code}`.
PTY imports no other business module's package; the dependency points only into PTY (DAG #4, `consumes: []`).

**Type mapping (postgresql16 → Java), stated once.** Column types only; the PK-generation clause (`GENERATED ALWAYS AS IDENTITY`) is not a type — every PK is mapped `@GeneratedValue(strategy = GenerationType.IDENTITY)`.

| postgresql16 type (db-script) | Java type | JSON (API document) |
|---|---|---|
| BIGINT | `Long` | integer, int64 |
| VARCHAR(n) | `String` (max length n) | string, maxLength n |
| BOOLEAN | `Boolean` | boolean |
| TIMESTAMPTZ | `OffsetDateTime` | string, date-time |
| NUMERIC(18,3) | `BigDecimal` (scale 3) | number |
| INTEGER | `Integer` | integer, int32 |

**Runtime error-code format** (profile.stack.backend.api.error_code_format), verbatim: `{MOD}-{http}[-{SLUG}]` — MOD is this module's code `PTY`; every Error Catalog code is an instance of that one format (e.g. `PTY-422-NAME-REQUIRED`). Errors are thrown as `LocalizedException → {code, messageAr, messageEn}`; the HTTP status is the `{http}` segment of the code and is one of 200, 201, 400, 401, 403, 404, 409, 413, 415, 422, 500. Message templates with placeholders (`{code}`, `{roleAr}`, `{roleEn}`, `{typeAr}`, `{typeEn}`, `{statusAr}`, `{statusEn}`, `{docNo}`, `{count}`) are filled by the service before the exception is thrown.

**Envelope.** Every success response is `ApiResponse<T>` `{data, messageAr?, messageEn?}` (the two message members carry a confirmation such as RULE-PTY-006 or RULE-PTY-017 and are absent otherwise); a page is `Page<T>` `{content, page, size, totalElements, totalPages}` with query parameters `page` (0-based) and `size` (default 20, max 100); an empty result is success with empty `content`, never "not found".

**Lookups.** All LOV values runtime-loaded from the core lookup module (mdl); no hardcoded enums in APIs or field specs; activity presets and capability flags are core settings, read through FeatureFlags. Lookup-backed properties store and return the **code**; the frontend resolves the label. Validation of an open-lookup code (CUSTOMER_GROUP, ADDRESS_TYPE) reads the active values through `MdlLookupApi` (given row `modules.mdl`). The closed sets the module branches on (CREDIT_CHECK_RESULT, CREDIT_OVERRUN_ACTION, CREDIT_OVERRIDE_STATUS — CON-PTY-008) are matched by code in the service; their labels (for `{statusAr}` / `{statusEn}` in RULE-PTY-026, `{typeAr}` / `{typeEn}` in RULE-PTY-017) are read through `MdlLookupApi`, never typed into code.

**Numbering.** Document numbers come from the core sequence module; never generated in a business module. The Party code (DBF-PTY-007) and the request docNo (DBF-PTY-061) are allocated through `NumberSeriesApi` (given row `modules.sequence`) inside the creating transaction — series PTY_PARTY and PTY_CREDIT_OVERRIDE (ADR-PTY-029). The Address code (DBF-PTY-044) is an ordinal within the Party, not a business number (QR-PTY-024).

**Capability flag.** `b2bCredit` is read through the FeatureFlags API of `SettingsApi` (given row `modules.cu`) only — never an activity name or preset. One component, `CreditFlag.isOn()`, is the single read point; every credit endpoint and every Customer read calls it per request (REQ-PTY-073: a flag turned on is effective at the next request, no cache beyond the request).

**Workflow engine: forbidden.** The Credit override request lifecycle (SRS A7) is a module-specific single-step state machine coded in `CreditOverrideService` (ADR-PTY-016).

**Search contract.** Each search allows sorting only on the fields of its screen's SRS filter list (stated per search endpoint); paging `Page<T>`; text filters are case-insensitive "contains"; an empty result is success.

**Languages.** Every named entity carries `nameAr` and `nameEn`; every message is returned in ar and en (`messageAr`, `messageEn`).

**Transactions and events.** One `@Transactional` service method per endpoint (reads `readOnly = true`). Calls to given audit (`AuditApi`) run inside the transaction (RULE-PTY-030 makes a failed audit write roll the change back). Invitations (`SecCustomerInvitationApi`) and approver notifications (`NotificationDispatchApi`) are sent after commit (`TransactionSynchronization.afterCommit`) — a rollback sends nothing, and a failed send never fails the committed request. The party-role-deactivated event is published through `DomainEventPublisher` inside the transaction and delivered by the given after commit.

**Audit columns.** `createdBy`, `createdAt`, `updatedBy`, `updatedAt` of every table are filled by the platform's auditing (principal of the authenticated given sec user, as the db-script binds them) — never accepted from a request, always returned in single-record responses.

**Given usage.** The module builds on `erp-core` (usage skill `.claude/skills/erp-core-usage@1.6.0`, installed first). The rows are the SRS A9 `given-usage` rows; this plan cites them and adds none:

| Given module | via | Used by |
|---|---|---|
| modules.sec | `com.erp.sec.crossmodule.SecCustomerInvitationApi` | API-PTY-034 (invitation request), invitation handler (acceptance → DBF-PTY-017) |
| modules.sec | `com.erp.sec.crossmodule.SecUserDirectoryApi` | API-PTY-019 (approvers holding PERM_PTY_CREDIT_OVERRIDE_UPDATE), API-PTY-021, 022 (decider identifier), API-PTY-031 (user names) |
| modules.sec | `com.erp.sec.permission.PermissionContributor` | SEC-BE — the five pages and their permissions |
| modules.cu | `com.erp.cu.crossmodule.SettingsApi` | `CreditFlag` (b2bCredit), API-PTY-024, 025, 035 (credit-overrun setting) |
| modules.mdl | `com.erp.mdl.crossmodule.MdlLookupApi` | RULE-PTY-016, RULE-PTY-018 validation; lookup labels in messages |
| modules.sequence | `com.erp.sequence.crossmodule.NumberSeriesApi` | API-PTY-001, 006, 033, 034 (Party code), API-PTY-019 (docNo) |
| modules.tenant | `com.erp.tenant.TenantProvisioningContributor` | walk-in Customer at provisioning (REQ-PTY-044), number series registration (ADR-PTY-029) |
| modules.audit | `com.erp.audit.crossmodule.AuditApi` | API-PTY-017, 018 (credit changes), API-PTY-021, 022 (decisions), API-PTY-031 (history) |
| modules.events | `com.erp.events.DomainEventPublisher` | API-PTY-004, 009 (party-role-deactivated) |
| modules.notif | `com.erp.notif.crossmodule.NotificationDispatchApi` | API-PTY-019 (approver notification) |

No entity or table the given owns is re-created; an operation on a given's entity is a call into the given.

**Database migration.** The db-script §4 (BLOCKS 1–10) is the module's single Flyway migration in the application range `V1000+` (version number allocated by the executor so it does not collide with any other module's migration); BLOCK 8 seeds the lookup keys into the given mdl tables, its column list checked against the installed given DDL as the db-script states.
<!-- PHASE:CORE:END -->

<!-- PHASE:DATA-DOM:START traces=REQ-PTY-001,REQ-PTY-006,REQ-PTY-024,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-034,REQ-PTY-036,REQ-PTY-039,REQ-PTY-044,REQ-PTY-064,REQ-PTY-091,REQ-PTY-092 -->
## PHASE 2 — DATA-DOM

One block per entity, every value bound from the db-script (tables, columns, constraints, identity clauses) and the SRS (rules, labels, lookups). Grouped by kind: four master entities, one transactional entity; the module owns no lookup table (its five lookup keys live in the given mdl tables).

<!-- SUB:DATA-DOM-MASTER:START traces=REQ-PTY-001,REQ-PTY-024,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-034,REQ-PTY-036,REQ-PTY-039,REQ-PTY-044,REQ-PTY-064 -->
### ENT-PTY-001 — Party / الطرف      kind: master

BINDINGS   table PTY_PARTY · PK PARTY_PK (DBF-PTY-001) · PK generation `identity` → `BIGINT GENERATED ALWAYS AS IDENTITY` (PK_PTY_PARTY) · db-script PTY v1 P2
BUSINESS CODE property `code` · column of DBF-PTY-007 · format `P-` + 6 digits (e.g. P-000120 — SRS AC-PTY-001; ADR-PTY-029) · uniqueness UQ_PTY_PARTY_CODE · generation source `NumberSeriesApi`, series PTY_PARTY, on first save; read-only after (RULE-PTY-007)
DEFAULT FIELDS master → nameAr, nameEn, code, isActiveFl, createdBy, createdAt, updatedBy, updatedAt — all present as columns below

| DBF | Property | Column (exact) | Type | Null | Read-only | Constraint / index | Label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-PTY-001 | partyPk | PARTY_PK | BIGINT | NOT NULL | yes | PK_PTY_PARTY | معرّف الطرف / Party ID |
| DBF-PTY-002 | mobile | MOBILE | VARCHAR(20) | NULL | no | CHK_PTY_PARTY_MOBILE · IDX_PTY_PARTY_MOBILE | رقم الجوال / Mobile |
| DBF-PTY-003 | email | EMAIL | VARCHAR(254) | NULL | no | CHK_PTY_PARTY_EMAIL · UQ_PTY_PARTY_EMAIL · IDX_PTY_PARTY_EMAIL | البريد الإلكتروني / Email |
| DBF-PTY-004 | taxRegistrationNo | TAX_REGISTRATION_NO | VARCHAR(30) | NULL | no | IDX_PTY_PARTY_TAX_REGISTRATION_NO | الرقم الضريبي / Tax registration number |
| DBF-PTY-005 | nameAr | NAME_AR | VARCHAR(200) | NULL | no | CHK_PTY_PARTY_NAME · IDX_PTY_PARTY_NAME_AR | الاسم بالعربية / Name (Arabic) |
| DBF-PTY-006 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | CHK_PTY_PARTY_NAME · IDX_PTY_PARTY_NAME_EN | الاسم بالإنجليزية / Name (English) |
| DBF-PTY-007 | code | CODE | VARCHAR(20) | NOT NULL | yes | UQ_PTY_PARTY_CODE | رقم الطرف / Party code |
| DBF-PTY-008 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL, default TRUE | yes (derived from roles) | IDX_PTY_PARTY_IS_ACTIVE_FL | نشط / Active |
| DBF-PTY-009 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes | — | أنشأه / Created by |
| DBF-PTY-010 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes | — | تاريخ الإنشاء / Created at |
| DBF-PTY-011 | updatedBy | UPDATED_BY | VARCHAR(100) | NOT NULL | yes | — | عدّله / Updated by |
| DBF-PTY-012 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NOT NULL | yes | — | تاريخ التعديل / Updated at |

DTO MEMBERSHIP  The Party has no endpoint of its own: it is created, read and changed through its roles (API-PTY-001, 003, 006, 008, 034). Role create-requests carry nameAr, nameEn, mobile, email, taxRegistrationNo and exclude partyPk, code, isActiveFl and audit; role update-requests carry the same five plus `code` compared only (RULE-PTY-007); role responses include code (business code), nameAr, nameEn, mobile, email, taxRegistrationNo.
LOOKUP FIELDS  none.
DOMAIN RULES
- **RULE-PTY-001 — Party name required.** Trigger: on create · on update. Statement: The system shall require at least one of nameAr or nameEn on every Party. Message — ar: أدخل اسم الطرف بالعربية أو بالإنجليزية. · en: Enter the party name in Arabic or English. Scope CREATE, UPDATE · DB enforcement CHK_PTY_PARTY_NAME (backstop) · owner layer service (`PartyValidator`, blank = empty).
- **RULE-PTY-002 — Email unique among active Parties.** Trigger: on create · on update of email · on reactivation of a role. Statement: The system shall prevent two active Parties of a tenant from holding the same email, compared without letter case and surrounding spaces. Message — ar: هذا البريد الإلكتروني مسجّل لطرف نشط آخر ({code}). افتح ذلك الطرف بدلاً من إنشاء طرف جديد. · en: This email is already registered to another active party ({code}). Open that party instead of creating a new one. Scope CREATE, UPDATE · DB enforcement UQ_PTY_PARTY_EMAIL (unique index on `LOWER(BTRIM(EMAIL))` among active Parties) · owner layer service (QR-PTY-004 names the holder's code; the index is the race backstop — ADR-PTY-026).
- **RULE-PTY-003 — Email format.** Trigger: on create · on update of email. Statement: The system shall validate that a non-empty email has one "@" with a non-empty local part and a domain part containing at least one ".". Message — ar: صيغة البريد الإلكتروني غير صحيحة، مثال: name@example.com · en: The email format is not valid, for example: name@example.com. Scope CREATE, UPDATE · DB enforcement CHK_PTY_PARTY_EMAIL · owner layer service.
- **RULE-PTY-004 — Mobile format.** Trigger: on create · on update of mobile. Statement: The system shall validate that a non-empty mobile, after removing spaces and dashes, is an optional leading "+" followed by 7 to 15 digits. Message — ar: رقم الجوال يتكون من 7 إلى 15 رقماً، ويمكن أن يبدأ بعلامة +. · en: The mobile number has 7 to 15 digits and can start with +. Scope CREATE, UPDATE · DB enforcement CHK_PTY_PARTY_MOBILE · owner layer service.
- **RULE-PTY-007 — Party code system-assigned and read-only.** Trigger: on create · on update. Statement: The system shall assign the Party code from the core number series on first save and prevent every later change to it. Message — ar: رقم الطرف يُنشأ تلقائياً ولا يمكن تعديله. · en: The party code is assigned by the system and cannot be changed. Scope CREATE, UPDATE · DB enforcement UQ_PTY_PARTY_CODE (uniqueness; immutability is service-level, `updatable = false` on the JPA mapping) · owner layer service.
- **RULE-PTY-019 — Guest contact required.** Trigger: on guest order details submission. Statement: The system shall require at least one of email or mobile in guest order details. Message — ar: أدخل البريد الإلكتروني أو رقم الجوال لإتمام الطلب. · en: Enter an email or a mobile number to complete the order. Scope CREATE (guest) · DB enforcement app-level · owner layer service (`GuestCustomerService`).
- **RULE-PTY-013** — stated in full under ENT-PTY-002 (scope ENT-PTY-001, ENT-PTY-002).
- **Party active state (REQ-PTY-028, REQ-PTY-029).** isActiveFl (DBF-PTY-008) is never set by a caller: it becomes false when the last active role is deactivated (QR-PTY-021 = 0) and true when a role is reactivated or added. Owner layer: `PartyService.syncActiveState`, called under the Party row lock (QR-PTY-001).
STATE MACHINE  not status-bearing (active / inactive only, SRS A7).
CROSS-MODULE   none — no XM touches this entity.
OPERATIONS     VIEW, CREATE, UPDATE
REPOSITORY OPS → QR-PTY-001 (FIND_ONE, lock), QR-PTY-002 (SAVE), QR-PTY-003 (UPDATE), QR-PTY-004 (FIND_BY_CRITERIA), QR-PTY-021 (COUNT), QR-PTY-036 (FIND_BY_CRITERIA), QR-PTY-037 (NATIVE), QR-PTY-038 (EXISTS)

### ENT-PTY-002 — Customer / العميل      kind: master

BINDINGS   table PTY_CUSTOMER · PK CUSTOMER_PK (DBF-PTY-013) · PK generation `identity` → `BIGINT GENERATED ALWAYS AS IDENTITY` (PK_PTY_CUSTOMER) · FK FK_PTY_CUSTOMER_PTY_PARTY (intra-module) · db-script PTY v1 P2
BUSINESS CODE none of its own — `code` is presented from the Party (DBF-PTY-007) through partyFk (DBF-PTY-019); never stored on PTY_CUSTOMER (ADR-PTY-014)
DEFAULT FIELDS master → nameAr, nameEn, code are presented from ENT-PTY-001 (read through DBF-PTY-019); isActiveFl and the four audit fields are columns below

| DBF | Property | Column (exact) | Type | Null | Read-only | Constraint / index | Label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-PTY-013 | customerPk | CUSTOMER_PK | BIGINT | NOT NULL | yes | PK_PTY_CUSTOMER | معرّف العميل / Customer ID |
| DBF-PTY-014 | customerGroupCode | CUSTOMER_GROUP_CODE | VARCHAR(50) | NOT NULL, default 'RETAIL' | no | IDX_PTY_CUSTOMER_CUSTOMER_GROUP_CODE | مجموعة العملاء / Customer group |
| DBF-PTY-015 | creditLimit | CREDIT_LIMIT | NUMERIC(18,3) | NULL | no (credit endpoints only) | CHK_PTY_CUSTOMER_CREDIT_LIMIT · CHK_PTY_CUSTOMER_WALK_IN_CREDIT · IDX_PTY_CUSTOMER_CARRIES_CREDIT | حد الائتمان / Credit limit |
| DBF-PTY-016 | paymentTermDays | PAYMENT_TERM_DAYS | INTEGER | NULL | no (credit endpoints only) | CHK_PTY_CUSTOMER_PAYMENT_TERM_DAYS · CHK_PTY_CUSTOMER_WALK_IN_CREDIT | مدة السداد (بالأيام) / Payment terms (days) |
| DBF-PTY-017 | secUserId | SEC_USER_ID | BIGINT | NULL | yes (invitation handler) | UQ_PTY_CUSTOMER_SEC_USER_ID · CHK_PTY_CUSTOMER_WALK_IN_SEC_USER | حساب العميل في المتجر الإلكتروني / Storefront customer account |
| DBF-PTY-018 | isWalkInFl | IS_WALK_IN_FL | BOOLEAN | NOT NULL, default FALSE | yes | UQ_PTY_CUSTOMER_IS_WALK_IN_FL | عميل نقدي / Walk-in customer |
| DBF-PTY-019 | partyFk | PARTY_FK | BIGINT | NOT NULL | yes after create | FK_PTY_CUSTOMER_PTY_PARTY · UQ_PTY_CUSTOMER_PARTY_FK | الطرف / Party |
| DBF-PTY-020 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL, default TRUE | no (deactivate / reactivate only) | CHK_PTY_CUSTOMER_WALK_IN_ACTIVE · IDX_PTY_CUSTOMER_IS_ACTIVE_FL | نشط / Active |
| DBF-PTY-021 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes | — | أنشأه / Created by |
| DBF-PTY-022 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes | — | تاريخ الإنشاء / Created at |
| DBF-PTY-023 | updatedBy | UPDATED_BY | VARCHAR(100) | NOT NULL | yes | — | عدّله / Updated by |
| DBF-PTY-024 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NOT NULL | yes | — | تاريخ التعديل / Updated at |

DTO MEMBERSHIP  create-request (API-PTY-001) excludes customerPk, code, isWalkInFl, isActiveFl, creditLimit, paymentTermDays, secUserId, audit; carries partyId (DBF-PTY-019) only to add the role to an existing Party. Update-request (API-PTY-003) excludes partyFk, isWalkInFl, isActiveFl, the credit fields (they belong to API-PTY-017), secUserId, audit. Response `Customer` includes customerPk, partyFk, code (business code), nameAr, nameEn, mobile, email, taxRegistrationNo, customerGroupCode, isWalkInFl, isActiveFl (flag), creditLimit and paymentTermDays **only while `b2bCredit` is on** (absent from the JSON otherwise — REQ-PTY-071), and the audit fields; secUserId is never returned (not promised, CON-PTY-009).
LOOKUP FIELDS  customerGroupCode │ DBF-PTY-014 │ lookup key `CUSTOMER_GROUP` (open; seeded RETAIL, WHOLESALE) │ values loaded by the client from the given mdl lookup service; PTY validates through `MdlLookupApi` — stores the code, not a numeric FK
DOMAIN RULES
- **RULE-PTY-005 — Inactive role refused for new documents.** Scope ENT-PTY-002, ENT-PTY-003. Trigger: on a consuming module's request for a new document. Statement: The system shall prevent an inactive Customer or Supplier from being used on a new document. Message — ar: {code} غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر. · en: {code} is inactive and cannot be used on a new document. Reactivate it or choose another party. Scope VIEW (forNewDocument = true) · DB enforcement app-level · owner layer service.
- **RULE-PTY-006 — Effect of deactivation on consuming modules.** Scope ENT-PTY-002, ENT-PTY-003, ENT-PTY-004. Trigger: on deactivation. Statement: The system shall keep a deactivated Customer, Supplier or Address readable by every existing reference, notify consuming modules through a party-role-deactivated event for Customers and Suppliers, and prevent its selection on new documents (prevent new use + notify; no cascade). Message (confirmation, returned on success) — ar: تم إيقاف {code}. تبقى المستندات السابقة كما هي، ولن يُعرض في المستندات الجديدة. · en: {code} is deactivated. Earlier documents keep it; it is no longer offered on new documents. Scope DELETE · DB enforcement none (soft delete; FKs carry no ON DELETE) · owner layer service + `DomainEventPublisher`.
- **RULE-PTY-008 — Payment terms range.** Scope ENT-PTY-002, ENT-PTY-003. Trigger: on create · on update. Statement: The system shall require a non-empty paymentTermDays to be a whole number from 0 to 365. Message — ar: مدة السداد يجب أن تكون بين 0 و365 يوماً. · en: Payment terms must be between 0 and 365 days. Scope CREATE, UPDATE · DB enforcement CHK_PTY_CUSTOMER_PAYMENT_TERM_DAYS, CHK_PTY_SUPPLIER_PAYMENT_TERM_DAYS · owner layer service.
- **RULE-PTY-009 — One role of each kind per Party.** Scope ENT-PTY-002, ENT-PTY-003. Trigger: on create of a Customer or a Supplier. Statement: The system shall prevent a Party from holding more than one Customer or more than one Supplier. Message — ar: هذا الطرف له صفة {roleAr} بالفعل. · en: This party already holds the {roleEn} role. ({roleAr}/{roleEn} = عميل/Customer or مورّد/Supplier.) Scope CREATE · DB enforcement UQ_PTY_CUSTOMER_PARTY_FK, UQ_PTY_SUPPLIER_PARTY_FK · owner layer service under the Party lock.
- **RULE-PTY-010 — Walk-in Customer stays active.** Trigger: on deactivation. Statement: The system shall prevent deactivation of the Customer whose isWalkInFl is true. Message — ar: العميل النقدي لازم لنقطة البيع ولا يمكن إيقافه. · en: The walk-in customer is required by the point of sale and cannot be deactivated. Scope DELETE · DB enforcement CHK_PTY_CUSTOMER_WALK_IN_ACTIVE · owner layer service.
- **RULE-PTY-011 — Walk-in Customer carries no credit.** Trigger: on update of creditLimit or paymentTermDays. Statement: The system shall prevent a creditLimit or a paymentTermDays on the Customer whose isWalkInFl is true. Message — ar: لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان. · en: The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit. Scope UPDATE · DB enforcement CHK_PTY_CUSTOMER_WALK_IN_CREDIT · owner layer service.
- **RULE-PTY-012 — Walk-in Party has no Supplier.** Scope ENT-PTY-002, ENT-PTY-003. Trigger: on create of a Supplier. Statement: The system shall prevent a Supplier on the Party of the Customer whose isWalkInFl is true. Message — ar: لا يمكن إضافة صفة المورّد إلى العميل النقدي. · en: The walk-in customer cannot also be a supplier. Scope CREATE · DB enforcement app-level · owner layer service.
- **RULE-PTY-013 — Walk-in Customer never matched nor linked.** Scope ENT-PTY-001, ENT-PTY-002. Trigger: on guest order matching · on invitation acceptance. Statement: The system shall exclude the walk-in Customer's Party and inactive Parties from guest matching, and prevent a secUserId on the walk-in Customer. Message — ar: لا يمكن ربط العميل النقدي بحساب عميل في المتجر الإلكتروني. · en: The walk-in customer cannot be linked to a storefront customer account. Scope guest matching (filter in QR-PTY-036, QR-PTY-037 — no error) · invitation acceptance (in-process outcome, ADR-PTY-025) · DB enforcement CHK_PTY_CUSTOMER_WALK_IN_SEC_USER · owner layer service.
- **RULE-PTY-014 — One walk-in Customer per tenant.** Trigger: on create of the walk-in Customer. Statement: The system shall prevent a second Customer with isWalkInFl true in a tenant. Message — ar: لهذا المستأجر عميل نقدي بالفعل. · en: This tenant already has a walk-in customer. Scope CREATE (walk-in ensure only — no endpoint sets isWalkInFl, so the message is never returned: a second creation attempt re-reads and returns the existing walk-in Customer, REQ-PTY-046) · DB enforcement UQ_PTY_CUSTOMER_IS_WALK_IN_FL · owner layer service (`WalkInCustomerService.ensure`).
- **RULE-PTY-018 — Customer group from the lookup.** Trigger: on create · on update. Statement: The system shall require customerGroupCode to be an active value of lookup CUSTOMER_GROUP. Message — ar: اختر مجموعة عملاء من القائمة. · en: Choose a customer group from the list. Scope CREATE, UPDATE · DB enforcement app-level (open lookup, no CHECK) · owner layer service via `MdlLookupApi`.
- **RULE-PTY-020 — One Customer per CUSTOMER-realm user.** Trigger: on invitation acceptance. Statement: The system shall prevent two Customers of a tenant from holding the same secUserId. Message — ar: حساب العميل هذا مرتبط بعميل آخر. · en: This customer account is already linked to another customer. Scope invitation acceptance (in-process outcome, ADR-PTY-025) · DB enforcement UQ_PTY_CUSTOMER_SEC_USER_ID · owner layer service.
- **RULE-PTY-021 — Guest matched to an inactive Customer.** Trigger: on guest order matching. Statement: The system shall prevent attaching guest order details to a matched Party whose Customer is inactive. Message — ar: تعذّر إتمام الطلب بهذه البيانات. يُرجى التواصل مع البائع. · en: The order cannot be completed with these details. Please contact the seller. (The message does not reveal that a Customer with this contact exists.) Scope guest matching · DB enforcement app-level · owner layer service.
- **RULE-PTY-022 — Credit limit not negative.** Trigger: on update of creditLimit. Statement: The system shall require a non-empty creditLimit to be 0 or more. Message — ar: حد الائتمان يجب أن يكون صفراً أو أكثر. · en: The credit limit must be zero or more. Scope UPDATE · DB enforcement CHK_PTY_CUSTOMER_CREDIT_LIMIT · owner layer service.
- **RULE-PTY-023 — Credit data only while the flag is on.** Scope ENT-PTY-002, ENT-PTY-005. Trigger: on update of creditLimit or paymentTermDays · on create of a Credit override request. Statement: The system shall prevent saving a Customer's creditLimit or paymentTermDays, and creating a Credit override request, while `b2bCredit` is off. Message — ar: الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد. · en: Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms. Flag b2bCredit (FeatureFlags) · Scope UPDATE, CREATE (and the credit screens' reads, REQ-PTY-070) · DB enforcement app-level · owner layer service (`CreditFlag`).
- **RULE-PTY-024 — Flag switch-off blocked while credit is in use.** Trigger: on a request to switch `b2bCredit` off. Statement: The system shall prevent switching `b2bCredit` off while at least one Customer carries a creditLimit or a paymentTermDays. Message — ar: لا يمكن إيقاف الائتمان: لدى {count} من العملاء حد ائتمان أو مدة سداد. امسحها من شاشة ائتمان العملاء ثم أعد المحاولة. · en: Credit cannot be turned off: {count} customers still have a credit limit or payment terms. Clear them on the Customer credit screen, then try again. Flag b2bCredit · Scope the switch-off dependency answer (API-PTY-037 returns the filled message to the flag owner — not an error of this module) · DB enforcement none · owner layer service.
- **RULE-PTY-030 — Credit change recorded or not saved.** Trigger: on update of creditLimit or paymentTermDays. Statement: The system shall prevent saving a creditLimit or paymentTermDays change that the core audit module has not recorded. Message — ar: تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة. · en: The change could not be recorded in the audit history, so it was not saved. Try again. Scope UPDATE · DB enforcement app-level (audit call inside the transaction) · owner layer service.
- **Default customer group (REQ-PTY-007, REQ-PTY-026, REQ-PTY-050, REQ-PTY-057).** A Customer created without customerGroupCode gets RETAIL (DBF-PTY-014 default 'RETAIL' and the service set it explicitly).
STATE MACHINE  not status-bearing (active / inactive only).
CROSS-MODULE   none — no XM touches this entity (inbound HARD-FKs of consumers are theirs, derived by `gov.py graph`).
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS → QR-PTY-005 (FIND_ONE + join), QR-PTY-006 (FIND_ONE, lock), QR-PTY-007 (FIND_ONE), QR-PTY-008 (SAVE), QR-PTY-009 (UPDATE), QR-PTY-010 (FIND_BY_CRITERIA), QR-PTY-011 (NATIVE), QR-PTY-012 (FIND_ONE), QR-PTY-013 (FIND_BY_CRITERIA), QR-PTY-014 (COUNT)

### ENT-PTY-003 — Supplier / المورّد      kind: master

BINDINGS   table PTY_SUPPLIER · PK SUPPLIER_PK (DBF-PTY-025) · PK generation `identity` → `BIGINT GENERATED ALWAYS AS IDENTITY` (PK_PTY_SUPPLIER) · FK FK_PTY_SUPPLIER_PTY_PARTY (intra-module) · db-script PTY v1 P2
BUSINESS CODE none of its own — presents the Party code (DBF-PTY-007) through partyFk (DBF-PTY-027) (ADR-PTY-014)
DEFAULT FIELDS master → nameAr, nameEn, code presented from ENT-PTY-001; isActiveFl and audit are columns below

| DBF | Property | Column (exact) | Type | Null | Read-only | Constraint / index | Label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-PTY-025 | supplierPk | SUPPLIER_PK | BIGINT | NOT NULL | yes | PK_PTY_SUPPLIER | معرّف المورّد / Supplier ID |
| DBF-PTY-026 | paymentTermDays | PAYMENT_TERM_DAYS | INTEGER | NULL | no | CHK_PTY_SUPPLIER_PAYMENT_TERM_DAYS | مدة السداد (بالأيام) / Payment terms (days) |
| DBF-PTY-027 | partyFk | PARTY_FK | BIGINT | NOT NULL | yes after create | FK_PTY_SUPPLIER_PTY_PARTY · UQ_PTY_SUPPLIER_PARTY_FK | الطرف / Party |
| DBF-PTY-028 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL, default TRUE | no (deactivate / reactivate only) | IDX_PTY_SUPPLIER_IS_ACTIVE_FL | نشط / Active |
| DBF-PTY-029 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes | — | أنشأه / Created by |
| DBF-PTY-030 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes | — | تاريخ الإنشاء / Created at |
| DBF-PTY-031 | updatedBy | UPDATED_BY | VARCHAR(100) | NOT NULL | yes | — | عدّله / Updated by |
| DBF-PTY-032 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NOT NULL | yes | — | تاريخ التعديل / Updated at |

DTO MEMBERSHIP  create-request (API-PTY-006) excludes supplierPk, code, isActiveFl, audit; carries partyId (DBF-PTY-027) only to add the role to an existing Party. Update-request (API-PTY-008) excludes partyFk, isActiveFl, audit; `code` compared only. Response `Supplier` includes supplierPk, partyFk, code, nameAr, nameEn, mobile, email, taxRegistrationNo, paymentTermDays (independent of `b2bCredit`, ADR-PTY-012), isActiveFl, audit.
LOOKUP FIELDS  none.
DOMAIN RULES  RULE-PTY-005, RULE-PTY-006, RULE-PTY-008, RULE-PTY-009, RULE-PTY-012 — stated in full under ENT-PTY-002 (shared scope); RULE-PTY-001 … RULE-PTY-004 and RULE-PTY-007 apply to the Supplier's Party (ENT-PTY-001).
STATE MACHINE  not status-bearing.
CROSS-MODULE   none.
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS → QR-PTY-015 (FIND_ONE + join), QR-PTY-016 (FIND_ONE, lock), QR-PTY-017 (FIND_ONE), QR-PTY-018 (SAVE), QR-PTY-019 (UPDATE), QR-PTY-020 (FIND_BY_CRITERIA)

### ENT-PTY-004 — Address / العنوان      kind: master

BINDINGS   table PTY_ADDRESS · PK ADDRESS_PK (DBF-PTY-033) · PK generation `identity` → `BIGINT GENERATED ALWAYS AS IDENTITY` (PK_PTY_ADDRESS) · FK FK_PTY_ADDRESS_PTY_PARTY (intra-module) · db-script PTY v1 P2
BUSINESS CODE none — `code` (DBF-PTY-044) is a system-assigned ordinal within the Party ("1", "2", …), unique UQ_PTY_ADDRESS_PARTY_CODE, allocated by QR-PTY-024 under the Party lock
DEFAULT FIELDS master → nameAr, nameEn (address label), code, isActiveFl, audit — all columns below

| DBF | Property | Column (exact) | Type | Null | Read-only | Constraint / index | Label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-PTY-033 | addressPk | ADDRESS_PK | BIGINT | NOT NULL | yes | PK_PTY_ADDRESS | معرّف العنوان / Address ID |
| DBF-PTY-034 | addressTypeCode | ADDRESS_TYPE_CODE | VARCHAR(50) | NOT NULL | no | IDX_PTY_ADDRESS_PARTY_TYPE | نوع العنوان / Address type |
| DBF-PTY-035 | line1 | LINE1 | VARCHAR(200) | NOT NULL | no | CHK_PTY_ADDRESS_LINE1 | العنوان (السطر الأول) / Address line 1 |
| DBF-PTY-036 | line2 | LINE2 | VARCHAR(200) | NULL | no | — | العنوان (السطر الثاني) / Address line 2 |
| DBF-PTY-037 | city | CITY | VARCHAR(100) | NULL | no | — | المدينة / City |
| DBF-PTY-038 | region | REGION | VARCHAR(100) | NULL | no | — | المنطقة / المحافظة / Region |
| DBF-PTY-039 | postalCode | POSTAL_CODE | VARCHAR(20) | NULL | no | — | الرمز البريدي / Postal code |
| DBF-PTY-040 | isDefaultFl | IS_DEFAULT_FL | BOOLEAN | NOT NULL, default FALSE | yes (system-maintained) | UQ_PTY_ADDRESS_IS_DEFAULT_FL | افتراضي / Default |
| DBF-PTY-041 | partyFk | PARTY_FK | BIGINT | NOT NULL | yes after create | FK_PTY_ADDRESS_PTY_PARTY · UQ_PTY_ADDRESS_PARTY_CODE | الطرف / Party |
| DBF-PTY-042 | nameAr | NAME_AR | VARCHAR(100) | NULL | no | — | اسم العنوان بالعربية / Address label (Arabic) |
| DBF-PTY-043 | nameEn | NAME_EN | VARCHAR(100) | NULL | no | — | اسم العنوان بالإنجليزية / Address label (English) |
| DBF-PTY-044 | code | CODE | VARCHAR(10) | NOT NULL | yes | UQ_PTY_ADDRESS_PARTY_CODE | رمز العنوان / Address code |
| DBF-PTY-045 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL, default TRUE | no (deactivate only) | UQ_PTY_ADDRESS_IS_DEFAULT_FL (predicate) | نشط / Active |
| DBF-PTY-046 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes | — | أنشأه / Created by |
| DBF-PTY-047 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes | — | تاريخ الإنشاء / Created at |
| DBF-PTY-048 | updatedBy | UPDATED_BY | VARCHAR(100) | NOT NULL | yes | — | عدّله / Updated by |
| DBF-PTY-049 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NOT NULL | yes | IDX_PTY_ADDRESS_PARTY_TYPE | تاريخ التعديل / Updated at |

DTO MEMBERSHIP  create/update-request `AddressRequest` carries addressTypeCode, line1, line2, city, region, postalCode, nameAr, nameEn; excludes addressPk, partyFk (taken from the path), code, isDefaultFl (set only by API-PTY-015 and the default rule), isActiveFl, audit. Response `Address` includes addressPk, partyFk, addressTypeCode, code, nameAr, nameEn, line1, line2, city, region, postalCode, isDefaultFl, isActiveFl, audit.
LOOKUP FIELDS  addressTypeCode │ DBF-PTY-034 │ lookup key `ADDRESS_TYPE` (open; seeded BILLING, DELIVERY) │ validated through `MdlLookupApi` — stores the code
DOMAIN RULES
- **RULE-PTY-015 — Address line required.** Trigger: on create · on update. Statement: The system shall require a non-empty line1 on every Address. Message — ar: أدخل السطر الأول من العنوان. · en: Enter the first address line. Scope CREATE, UPDATE · DB enforcement CHK_PTY_ADDRESS_LINE1 · owner layer service.
- **RULE-PTY-016 — Address type from the lookup.** Trigger: on create · on update. Statement: The system shall require addressTypeCode to be an active value of lookup ADDRESS_TYPE. Message — ar: اختر نوع عنوان من القائمة. · en: Choose an address type from the list. Scope CREATE, UPDATE · DB enforcement app-level · owner layer service via `MdlLookupApi`.
- **RULE-PTY-017 — One default Address per type per Party.** Trigger: on create · on set-default · on deactivation. Statement: The system shall keep exactly one active default Address per Party per address type while the Party has an active Address of that type, choosing the most recently updated remaining one when the default is deactivated. Message (confirmation on set-default) — ar: أصبح هذا العنوان هو العنوان الافتراضي من نوع {typeAr}. · en: This address is now the default {typeEn} address. Scope CREATE, UPDATE, DELETE · DB enforcement UQ_PTY_ADDRESS_IS_DEFAULT_FL (at most one) · owner layer service under the Party lock (ADR-PTY-026: a type change on update leaves the old type's default and joins the new type as default only when it has none).
- **RULE-PTY-006** — stated in full under ENT-PTY-002 (deactivation keeps the Address readable by its identifier; no event for Addresses).
STATE MACHINE  not status-bearing.
CROSS-MODULE   none.
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS → QR-PTY-022 (FIND_ONE), QR-PTY-023 (FIND_ONE, lock), QR-PTY-024 (AGGREGATE), QR-PTY-025 (SAVE), QR-PTY-026 (UPDATE), QR-PTY-027 (FIND_ALL), QR-PTY-028 (FIND_ONE), QR-PTY-029 (FIND_ONE), QR-PTY-030 (EXISTS)
<!-- SUB:DATA-DOM-MASTER:END -->

<!-- SUB:DATA-DOM-TRANSACTIONAL:START traces=REQ-PTY-091,REQ-PTY-092,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-097,REQ-PTY-098,REQ-PTY-103 -->
### ENT-PTY-005 — Credit override request / طلب تجاوز حد الائتمان      kind: transactional

BINDINGS   table PTY_CREDIT_OVERRIDE_REQUEST · PK CREDIT_OVERRIDE_REQUEST_PK (DBF-PTY-050) · PK generation `identity` → `BIGINT GENERATED ALWAYS AS IDENTITY` (PK_PTY_CREDIT_OVERRIDE_REQUEST) · FK FK_PTY_CREDIT_OVERRIDE_REQUEST_PTY_CUSTOMER (intra-module) · db-script PTY v1 P2
BUSINESS CODE property `docNo` · column of DBF-PTY-061 · format `CO-` + 6 digits (e.g. CO-000031 — SRS AC-PTY-104; ADR-PTY-029) · uniqueness UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO · generation source `NumberSeriesApi`, series PTY_CREDIT_OVERRIDE, on first save; read-only after (RULE-PTY-031)
DEFAULT FIELDS transactional → docNo, docDate, statusCode, createdBy, createdAt, updatedBy, updatedAt — all columns below

| DBF | Property | Column (exact) | Type | Null | Read-only | Constraint / index | Label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-PTY-050 | creditOverrideRequestPk | CREDIT_OVERRIDE_REQUEST_PK | BIGINT | NOT NULL | yes | PK_PTY_CREDIT_OVERRIDE_REQUEST | معرّف الطلب / Request ID |
| DBF-PTY-051 | saleAmount | SALE_AMOUNT | NUMERIC(18,3) | NOT NULL | yes after create | CHK_PTY_CREDIT_OVERRIDE_REQUEST_SALE_AMOUNT | مبلغ البيع / Sale amount |
| DBF-PTY-052 | creditLimitAtRequest | CREDIT_LIMIT_AT_REQUEST | NUMERIC(18,3) | NULL | yes (system) | — | حد الائتمان عند الطلب / Credit limit at request |
| DBF-PTY-053 | openExposure | OPEN_EXPOSURE | NUMERIC(18,3) | NULL | yes (system) | — | المديونية القائمة / Open exposure |
| DBF-PTY-054 | creditCheckResultCode | CREDIT_CHECK_RESULT_CODE | VARCHAR(50) | NOT NULL | yes (system) | CHK_PTY_CREDIT_OVERRIDE_REQUEST_CREDIT_CHECK_RESULT_CODE | نتيجة فحص الائتمان / Credit check result |
| DBF-PTY-055 | sourceDocumentRef | SOURCE_DOCUMENT_REF | VARCHAR(60) | NOT NULL | yes after create | — | مرجع مستند البيع / Sale document reference |
| DBF-PTY-056 | decidedBy | DECIDED_BY | BIGINT | NULL | yes (system) | CHK_PTY_CREDIT_OVERRIDE_REQUEST_DECIDED | صاحب القرار / Decided by |
| DBF-PTY-057 | decidedAt | DECIDED_AT | TIMESTAMPTZ | NULL | yes (system) | CHK_PTY_CREDIT_OVERRIDE_REQUEST_DECIDED | تاريخ القرار / Decided at |
| DBF-PTY-058 | decisionReason | DECISION_REASON | VARCHAR(500) | NULL | no (reject only) | CHK_PTY_CREDIT_OVERRIDE_REQUEST_DECISION_REASON | سبب القرار / Decision reason |
| DBF-PTY-059 | usedDocumentRef | USED_DOCUMENT_REF | VARCHAR(60) | NULL | no (use only) | CHK_PTY_CREDIT_OVERRIDE_REQUEST_USED_DOCUMENT_REF | مستند البيع المستخدِم / Used on sale document |
| DBF-PTY-060 | customerFk | CUSTOMER_FK | BIGINT | NOT NULL | yes after create | FK_PTY_CREDIT_OVERRIDE_REQUEST_PTY_CUSTOMER · IDX_PTY_CREDIT_OVERRIDE_REQUEST_CUSTOMER_FK | العميل / Customer |
| DBF-PTY-061 | docNo | DOC_NO | VARCHAR(20) | NOT NULL | yes | UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO | رقم الطلب / Request number |
| DBF-PTY-062 | docDate | DOC_DATE | TIMESTAMPTZ | NOT NULL | yes | IDX_PTY_CREDIT_OVERRIDE_REQUEST_DOC_DATE | تاريخ الطلب / Request date |
| DBF-PTY-063 | statusCode | STATUS_CODE | VARCHAR(50) | NOT NULL, default 'PENDING' | yes (state machine) | CHK_PTY_CREDIT_OVERRIDE_REQUEST_STATUS_CODE · IDX_PTY_CREDIT_OVERRIDE_REQUEST_STATUS_CODE | حالة الطلب / Request status |
| DBF-PTY-064 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes | IDX_PTY_CREDIT_OVERRIDE_REQUEST_CREATED_BY | مقدّم الطلب / Requested by |
| DBF-PTY-065 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes | — | تاريخ الإنشاء / Created at |
| DBF-PTY-066 | updatedBy | UPDATED_BY | VARCHAR(100) | NOT NULL | yes | — | عدّله / Updated by |
| DBF-PTY-067 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NOT NULL | yes | — | تاريخ التعديل / Updated at |

DTO MEMBERSHIP  create-request (API-PTY-019) carries customerId (DBF-PTY-060), saleAmount (DBF-PTY-051), sourceDocumentRef (DBF-PTY-055); excludes the PK, docNo, docDate, statusCode, creditLimitAtRequest, openExposure, creditCheckResultCode, decision fields, usedDocumentRef, audit. Decision requests carry only decisionReason (reject) and an optional docNo compared only (RULE-PTY-031); the use request carries customerId, saleAmount (compared), usedDocumentRef (DBF-PTY-059) and an optional docNo. Response `CreditOverrideRequest` includes every column above (docNo business code, statusCode, audit) plus the Customer's code and names read through DBF-PTY-060.
LOOKUP FIELDS  statusCode │ DBF-PTY-063 │ `CREDIT_OVERRIDE_STATUS` (closed) · creditCheckResultCode │ DBF-PTY-054 │ `CREDIT_CHECK_RESULT` (closed) — codes stored; labels from the given mdl
DOMAIN RULES
- **RULE-PTY-025 — Request only for a real overrun.** Trigger: on create. Statement: The system shall prevent creating a Credit override request unless its credit check result is OVER_LIMIT, NO_CREDIT or EXPOSURE_UNAVAILABLE. Message — ar: لا حاجة إلى موافقة: البيع ضمن حد ائتمان العميل. · en: No approval is needed: the sale is within the customer's credit limit. Scope CREATE · DB enforcement CHK_PTY_CREDIT_OVERRIDE_REQUEST_CREDIT_CHECK_RESULT_CODE · owner layer service (the server recomputes the credit check, ADR-PTY-028).
- **RULE-PTY-026 — Decisions only on PENDING requests.** Trigger: on approve · on reject · on cancel. Statement: The system shall prevent approving, rejecting or cancelling a Credit override request whose statusCode is not PENDING. Message — ar: هذا الطلب لم يعد معلّقاً (الحالة: {statusAr}). · en: This request is no longer pending (status: {statusEn}). Scope UPDATE, DELETE · DB enforcement CHK_PTY_CREDIT_OVERRIDE_REQUEST_STATUS_CODE (values) + conditional update QR-PTY-034 · owner layer service.
- **RULE-PTY-027 — Rejection reason required.** Trigger: on reject. Statement: The system shall require a non-empty decisionReason when a Credit override request is rejected. Message — ar: أدخل سبب الرفض. · en: Enter the reason for the rejection. Scope UPDATE · DB enforcement CHK_PTY_CREDIT_OVERRIDE_REQUEST_DECISION_REASON · owner layer service.
- **RULE-PTY-028 — Use only within the approval.** Trigger: on use by a consuming module. Statement: The system shall prevent using a Credit override request unless its statusCode is APPROVED, its customerFk is the sale's Customer and its saleAmount is greater than or equal to the sale amount. Message — ar: الموافقة {docNo} لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة. · en: Approval {docNo} does not cover this sale. Check the customer and the amount, or request a new approval. Scope UPDATE (use) · DB enforcement conditional update QR-PTY-034 · owner layer service.
- **RULE-PTY-029 — Only the requester cancels.** Trigger: on cancel. Statement: The system shall prevent a user other than the request's creator from cancelling a Credit override request. Message — ar: لا يلغي هذا الطلب إلا من قدّمه. · en: Only the requester can cancel this request. Scope DELETE · DB enforcement app-level (createdBy, DBF-PTY-064, compared with the current principal) · owner layer service.
- **RULE-PTY-031 — Request number system-assigned and read-only.** Trigger: on create · on update. Statement: The system shall assign docNo from the core number series on first save and prevent every later change to it. Message — ar: رقم الطلب يُنشأ تلقائياً ولا يمكن تعديله. · en: The request number is assigned by the system and cannot be changed. Scope CREATE, UPDATE · DB enforcement UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO (`updatable = false` mapping) · owner layer service.
- **RULE-PTY-032 — Sale amount positive.** Trigger: on create. Statement: The system shall require saleAmount to be greater than 0. Message — ar: مبلغ البيع يجب أن يكون أكبر من صفر. · en: The sale amount must be greater than zero. Scope CREATE · DB enforcement CHK_PTY_CREDIT_OVERRIDE_REQUEST_SALE_AMOUNT · owner layer service.
- **RULE-PTY-033 — No credit override for the walk-in Customer.** Trigger: on create. Statement: The system shall prevent creating a Credit override request whose Customer has isWalkInFl true. Message — ar: لا يمكن البيع الآجل للعميل النقدي. سجّل العميل باسمه أولاً. · en: The walk-in customer cannot buy on credit. Register the customer by name first. Scope CREATE · DB enforcement app-level · owner layer service.
- **RULE-PTY-023** — stated in full under ENT-PTY-002 (also refuses creation while `b2bCredit` is off).
STATE MACHINE  status column DBF-PTY-063 · values PENDING, APPROVED, REJECTED, CANCELLED, USED (lookup CREDIT_OVERRIDE_STATUS) · initial PENDING (API-PTY-019) · transitions: PENDING → APPROVED (approve, actor holding PERM_PTY_CREDIT_OVERRIDE_UPDATE, API-PTY-021, RULE-PTY-026) · PENDING → REJECTED (reject, same actor, API-PTY-022, RULE-PTY-026, RULE-PTY-027) · PENDING → CANCELLED (cancel, the requester, API-PTY-023, RULE-PTY-026, RULE-PTY-029) · APPROVED → USED (use, consuming module, API-PTY-036, RULE-PTY-028) · terminal USED, REJECTED, CANCELLED · invalid transition → RULE-PTY-026 (decisions) or RULE-PTY-028 (use). Every decided state records decidedBy (DBF-PTY-056) and decidedAt (DBF-PTY-057) — CHK_PTY_CREDIT_OVERRIDE_REQUEST_DECIDED; USED records usedDocumentRef (DBF-PTY-059) — CHK_PTY_CREDIT_OVERRIDE_REQUEST_USED_DOCUMENT_REF.
CROSS-MODULE   none — the sale is held as text (DBF-PTY-055, DBF-PTY-059), no key into a consumer (ADR-PTY-016).
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS → QR-PTY-031 (SAVE), QR-PTY-032 (FIND_ONE + join), QR-PTY-033 (FIND_ONE, lock), QR-PTY-034 (UPDATE, conditional), QR-PTY-035 (FIND_BY_CRITERIA)
<!-- SUB:DATA-DOM-TRANSACTIONAL:END -->
<!-- PHASE:DATA-DOM:END -->

<!-- PHASE:SVC-API:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-003,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-019,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,REQ-PTY-044,REQ-PTY-045,REQ-PTY-046,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-050,REQ-PTY-051,REQ-PTY-052,REQ-PTY-053,REQ-PTY-054,REQ-PTY-055,REQ-PTY-056,REQ-PTY-057,REQ-PTY-058,REQ-PTY-059,REQ-PTY-060,REQ-PTY-061,REQ-PTY-062,REQ-PTY-063,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-074,REQ-PTY-075,REQ-PTY-076,REQ-PTY-077,REQ-PTY-078,REQ-PTY-079,REQ-PTY-080,REQ-PTY-081,REQ-PTY-082,REQ-PTY-083,REQ-PTY-084,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-097,REQ-PTY-098,REQ-PTY-099,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-107,REQ-PTY-108,REQ-PTY-109 -->
## PHASE 3 — SVC-API

37 endpoints (≥ 8), grouped CRUD / SEARCH / INT. Every path is an instance of `/api/v1/{module}/{resource}` with module `pty`. Every endpoint verifies its permission before processing (Spring Security method security on the controller; the service re-checks only where the rule depends on data — ADR-PTY-025). Error codes are the Error Catalog's; messages are not repeated here — every code below has its ar and en text in the catalog. Shared response DTOs: `Customer` (as API-PTY-002), `Supplier` (as API-PTY-007), `Address` (as API-PTY-012), `CustomerCredit` (as API-PTY-016), `CreditOverrideRequest` (as API-PTY-020), `Confirmation` `{id, code}`.

<!-- SUB:SVC-API-CRUD:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-003,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-019,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-070,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-107,REQ-PTY-109 -->
### SVC-API-CRUD — create, read, change, deactivate

<!-- API:API-PTY-001:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,REQ-PTY-029,REQ-PTY-039,REQ-PTY-040,DBF-PTY-001,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-008,DBF-PTY-013,DBF-PTY-014,DBF-PTY-018,DBF-PTY-019,DBF-PTY-020 -->
### API-PTY-001 — create Customer
Entity       : ENT-PTY-002, ENT-PTY-001 · operation CREATE · serves: create (SCR-REQ-PTY-001), add Customer role (SCR-REQ-PTY-002)
Honours      : CON-PTY-023, CON-PTY-002, CON-PTY-001, CON-PTY-006
Endpoint     : /api/v1/pty/customers   verb: POST
Layers       : CustomerController.create → CustomerService.create ; in-process PtyPartyApi.registerCustomer → CustomerService.create
Request      : body `CustomerCreateRequest` — nameAr (DBF-PTY-005, String ≤ 200, optional; one of nameAr / nameEn required — RULE-PTY-001) · nameEn (DBF-PTY-006, String ≤ 200, optional) · mobile (DBF-PTY-002, String ≤ 20, optional — RULE-PTY-004) · email (DBF-PTY-003, String ≤ 254, optional — RULE-PTY-002, RULE-PTY-003) · taxRegistrationNo (DBF-PTY-004, String ≤ 30, optional) · customerGroupCode (DBF-PTY-014, String ≤ 50, optional, default RETAIL — RULE-PTY-018) · partyId (DBF-PTY-019, Long, optional — present = add the Customer role to that existing Party; the Party fields of the body are then ignored, REQ-PTY-026). Excluded system fields: customerPk, code, isWalkInFl, isActiveFl, creditLimit, paymentTermDays, secUserId, audit.
Response     : 201 · `Customer` (as API-PTY-002 — carries customerPk, partyFk and code for registration in place, REQ-PTY-008) · not paginated · ApiResponse<Customer>
Validations  : RULE-PTY-001 → PTY-422-NAME-REQUIRED · RULE-PTY-003 → PTY-422-EMAIL-FORMAT · RULE-PTY-004 → PTY-422-MOBILE-FORMAT · RULE-PTY-002 → PTY-409-EMAIL-TAKEN ({code} = the holder's Party code) · RULE-PTY-018 → PTY-422-CUSTOMER-GROUP-INVALID · RULE-PTY-009 → PTY-409-ROLE-HELD ({roleAr} عميل, {roleEn} Customer)
Errors       : PTY-400-VALIDATION (400), PTY-401-UNAUTHENTICATED (401), PTY-403-FORBIDDEN (403), PTY-404-PARTY-NOT-FOUND (404), PTY-409-EMAIL-TAKEN (409, RULE-PTY-002), PTY-409-ROLE-HELD (409, RULE-PTY-009), PTY-422-NAME-REQUIRED, PTY-422-EMAIL-FORMAT, PTY-422-MOBILE-FORMAT, PTY-422-CUSTOMER-GROUP-INVALID (422), PTY-500-INTERNAL (500)
Orchestration : (A) partyId absent — validate RULE-PTY-001, RULE-PTY-003, RULE-PTY-004, RULE-PTY-018 → QR-PTY-004 (email held by another active Party → RULE-PTY-002) → allocate the Party code DBF-PTY-007 from `NumberSeriesApi` series PTY_PARTY → insert Party QR-PTY-002 with DBF-PTY-008 (isActiveFl) true → insert Customer QR-PTY-008 with DBF-PTY-019 (partyFk) = new Party PK, DBF-PTY-014 (customerGroupCode, RETAIL when absent — REQ-PTY-007), DBF-PTY-018 (isWalkInFl) false, DBF-PTY-020 (isActiveFl) true. (B) partyId present — lock Party QR-PTY-001 (absent → PTY-404-PARTY-NOT-FOUND) → QR-PTY-007 (Party already holds a Customer → RULE-PTY-009) → validate RULE-PTY-018 → Party inactive: QR-PTY-004 (RULE-PTY-002) then DBF-PTY-008 (isActiveFl) true through QR-PTY-003 (REQ-PTY-029) → insert Customer QR-PTY-008 as in (A). Return the Customer with its Party's code and names.
Repository   : QR-PTY-001, QR-PTY-002, QR-PTY-003, QR-PTY-004, QR-PTY-007, QR-PTY-008 · SAVE · join NONE · transaction READ_WRITE
Concurrency  : Party code — atomic allocation by `NumberSeriesApi`, backstopped by UQ_PTY_PARTY_CODE. Email — two registrations with the same email both pass QR-PTY-004; UQ_PTY_PARTY_EMAIL rejects the second insert, translated to PTY-409-EMAIL-TAKEN after re-reading the holder (ADR-PTY-026). Role add — QR-PTY-001 locks the Party row (`FOR UPDATE`), so a second add waits and then finds the first Customer; UQ_PTY_CUSTOMER_PARTY_FK backstops → PTY-409-ROLE-HELD.
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_CREATE
Localization : messages ar + en (catalog); the Party carries nameAr / nameEn
<!-- API:API-PTY-001:END -->

<!-- API:API-PTY-002:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-003,REQ-PTY-014,REQ-PTY-071,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-013,DBF-PTY-014,DBF-PTY-015,DBF-PTY-016,DBF-PTY-018,DBF-PTY-019,DBF-PTY-020 -->
### API-PTY-002 — read Customer
Entity       : ENT-PTY-002, ENT-PTY-001 · operation VIEW · serves: read (SCR-REQ-PTY-001)
Honours      : CON-PTY-009, CON-PTY-002, CON-PTY-001
Endpoint     : /api/v1/pty/customers/{id}   verb: GET
Layers       : CustomerController.get → CustomerService.read ; in-process PtyPartyApi.readCustomer → CustomerService.read
Request      : path id (DBF-PTY-013, Long, required) · query forNewDocument (Boolean, optional, default false — set by a consumer selecting the Customer for a new document)
Response     : 200 · `Customer` {customerPk (DBF-PTY-013), partyFk (DBF-PTY-019), code (DBF-PTY-007), nameAr (DBF-PTY-005), nameEn (DBF-PTY-006), mobile (DBF-PTY-002), email (DBF-PTY-003), taxRegistrationNo (DBF-PTY-004), customerGroupCode (DBF-PTY-014), isWalkInFl (DBF-PTY-018), isActiveFl (DBF-PTY-020), creditLimit (DBF-PTY-015) and paymentTermDays (DBF-PTY-016) — present only while `b2bCredit` is on, absent from the JSON otherwise, createdBy, createdAt, updatedBy, updatedAt (DBF-PTY-021 … DBF-PTY-024)} · not paginated · ApiResponse<Customer>
Validations  : RULE-PTY-005 (forNewDocument = true and the Customer is inactive) → PTY-422-ROLE-INACTIVE ({code} = Party code)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-ROLE-INACTIVE, PTY-500-INTERNAL
Orchestration : load QR-PTY-005 (absent → PTY-404-CUSTOMER-NOT-FOUND) → forNewDocument: RULE-PTY-005 → `CreditFlag.isOn()` decides whether the credit fields are serialised (REQ-PTY-071) → map. An inactive Customer is returned when forNewDocument is false (REQ-PTY-014).
Repository   : QR-PTY-005 · FIND_ONE · join PTY_PARTY (ADR-PTY-027) · transaction READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_VIEW
Localization : nameAr / nameEn returned; messages ar + en
<!-- API:API-PTY-002:END -->

<!-- API:API-PTY-003:START traces=REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-024,REQ-PTY-039,REQ-PTY-040,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-013,DBF-PTY-014 -->
### API-PTY-003 — update Customer
Entity       : ENT-PTY-002, ENT-PTY-001 · operation UPDATE · serves: update (SCR-REQ-PTY-001)
Honours      : CON-PTY-002, CON-PTY-001, CON-PTY-006
Endpoint     : /api/v1/pty/customers/{id}   verb: PUT
Layers       : CustomerController.update → CustomerService.update
Request      : path id (DBF-PTY-013, Long, required) · body `CustomerUpdateRequest` — nameAr (DBF-PTY-005, String ≤ 200) · nameEn (DBF-PTY-006, String ≤ 200) · mobile (DBF-PTY-002, String ≤ 20) · email (DBF-PTY-003, String ≤ 254) · taxRegistrationNo (DBF-PTY-004, String ≤ 30) · customerGroupCode (DBF-PTY-014, String ≤ 50, required) · code (DBF-PTY-007, String, optional — compared only, never written: RULE-PTY-007). PUT replaces the editable set. Excluded: partyFk, isWalkInFl, isActiveFl, creditLimit, paymentTermDays (API-PTY-017), secUserId, audit.
Response     : 200 · `Customer` (as API-PTY-002) · ApiResponse<Customer>
Validations  : RULE-PTY-007 (code present and different) → PTY-422-CODE-READ-ONLY · RULE-PTY-001 → PTY-422-NAME-REQUIRED · RULE-PTY-003 → PTY-422-EMAIL-FORMAT · RULE-PTY-004 → PTY-422-MOBILE-FORMAT · RULE-PTY-002 → PTY-409-EMAIL-TAKEN · RULE-PTY-018 → PTY-422-CUSTOMER-GROUP-INVALID
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-409-EMAIL-TAKEN, PTY-422-CODE-READ-ONLY, PTY-422-NAME-REQUIRED, PTY-422-EMAIL-FORMAT, PTY-422-MOBILE-FORMAT, PTY-422-CUSTOMER-GROUP-INVALID, PTY-500-INTERNAL
Orchestration : lock Customer QR-PTY-006 (absent → 404) → lock its Party QR-PTY-001 → validate (RULE-PTY-007 first) → email changed and the Party active: QR-PTY-004 excluding this Party → write the Party's DBF-PTY-005, DBF-PTY-006, DBF-PTY-002, DBF-PTY-003, DBF-PTY-004 (QR-PTY-003) — shared by both roles (REQ-PTY-024) → write DBF-PTY-014 (QR-PTY-009) → return as API-PTY-002. The walk-in Customer may be renamed (SRS D-08).
Repository   : QR-PTY-006, QR-PTY-001, QR-PTY-004, QR-PTY-003, QR-PTY-009 · UPDATE · join NONE · transaction READ_WRITE
Concurrency  : The Party row lock (QR-PTY-001) serialises concurrent edits of the same Party from its Customer and its Supplier screens; email race backstopped by UQ_PTY_PARTY_EMAIL → PTY-409-EMAIL-TAKEN (ADR-PTY-026).
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_UPDATE
Localization : messages ar + en
<!-- API:API-PTY-003:END -->

<!-- API:API-PTY-004:START traces=REQ-PTY-014,REQ-PTY-023,REQ-PTY-028,REQ-PTY-047,DBF-PTY-008,DBF-PTY-013,DBF-PTY-018,DBF-PTY-020 -->
### API-PTY-004 — deactivate Customer
Entity       : ENT-PTY-002 · operation DELETE · serves: deactivate (SCR-REQ-PTY-001)
Honours      : CON-PTY-002, CON-PTY-021
Endpoint     : /api/v1/pty/customers/{id}   verb: DELETE
Layers       : CustomerController.deactivate → CustomerService.deactivate
Request      : path id (DBF-PTY-013, Long, required) · no body
Response     : 200 · `Confirmation` {id, code} with messageAr / messageEn = RULE-PTY-006 confirmation ({code} = Party code) · ApiResponse<Confirmation>
Validations  : RULE-PTY-010 (DBF-PTY-018 true) → PTY-422-WALK-IN-DEACTIVATE
Messages     : RULE-PTY-006 — ar: تم إيقاف {code}. تبقى المستندات السابقة كما هي، ولن يُعرض في المستندات الجديدة. · en: {code} is deactivated. Earlier documents keep it; it is no longer offered on new documents.
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-WALK-IN-DEACTIVATE, PTY-500-INTERNAL
Orchestration : lock QR-PTY-006 → RULE-PTY-010 → already inactive: return the confirmation, publish nothing (idempotent) → DBF-PTY-020 (isActiveFl) false (QR-PTY-009) → lock the Party QR-PTY-001 → QR-PTY-021 counts the Party's active roles → 0: DBF-PTY-008 (isActiveFl) false (QR-PTY-003, REQ-PTY-028) → publish `PartyRoleDeactivatedEvent` {role CUSTOMER, identifier customerPk, code} through `DomainEventPublisher` (REQ-PTY-023). Soft delete only (profile delete_semantics `soft`): nothing is removed, every reference stays readable.
Repository   : QR-PTY-006, QR-PTY-009, QR-PTY-001, QR-PTY-021, QR-PTY-003 · UPDATE · join NONE · transaction READ_WRITE
Concurrency  : The Party row lock serialises the "last active role" decision: a Customer and a Supplier of one Party deactivated at the same time cannot both read "one other role still active" and leave the Party active.
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_DELETE
Localization : confirmation in ar + en
<!-- API:API-PTY-004:END -->

<!-- API:API-PTY-005:START traces=REQ-PTY-010,REQ-PTY-015,REQ-PTY-029,DBF-PTY-003,DBF-PTY-008,DBF-PTY-013,DBF-PTY-020 -->
### API-PTY-005 — reactivate Customer
Entity       : ENT-PTY-002 · operation DELETE (the reverse of deactivation, same permission — ADR-PTY-025) · serves: reactivate (SCR-REQ-PTY-001)
Honours      : CON-PTY-002
Endpoint     : /api/v1/pty/customers/{id}/activation   verb: PATCH
Layers       : CustomerController.reactivate → CustomerService.reactivate
Request      : path id (DBF-PTY-013, Long, required) · body `ActivationRequest` { isActiveFl (DBF-PTY-020, Boolean, required, must be true — deactivation is API-PTY-004) } (ADR-PTY-031)
Response     : 200 · `Customer` (as API-PTY-002) · ApiResponse<Customer>
Validations  : body isActiveFl missing or not true → PTY-400-VALIDATION (ADR-PTY-031) · RULE-PTY-002 (the Party is inactive and its email is now held by another active Party) → PTY-409-EMAIL-TAKEN
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-409-EMAIL-TAKEN, PTY-500-INTERNAL
Orchestration : lock QR-PTY-006 → lock the Party QR-PTY-001 → Party DBF-PTY-008 false: QR-PTY-004 excluding this Party (RULE-PTY-002), then DBF-PTY-008 (isActiveFl) true (QR-PTY-003, REQ-PTY-029) → DBF-PTY-020 (isActiveFl) true (QR-PTY-009). Already active → returned unchanged.
Repository   : QR-PTY-006, QR-PTY-001, QR-PTY-004, QR-PTY-003, QR-PTY-009 · UPDATE · join NONE · transaction READ_WRITE
Concurrency  : Reactivating a Party makes its email active again: UQ_PTY_PARTY_EMAIL rejects a concurrent registration of the same email that passed its own check → the loser receives PTY-409-EMAIL-TAKEN (ADR-PTY-026).
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_DELETE
Localization : messages ar + en
<!-- API:API-PTY-005:END -->

<!-- API:API-PTY-006:START traces=REQ-PTY-006,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-016,REQ-PTY-022,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,REQ-PTY-029,REQ-PTY-049,DBF-PTY-001,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-008,DBF-PTY-018,DBF-PTY-025,DBF-PTY-026,DBF-PTY-027,DBF-PTY-028 -->
### API-PTY-006 — create Supplier
Entity       : ENT-PTY-003, ENT-PTY-001 · operation CREATE · serves: create (SCR-REQ-PTY-002), add Supplier role (SCR-REQ-PTY-001)
Honours      : CON-PTY-024, CON-PTY-003, CON-PTY-001
Endpoint     : /api/v1/pty/suppliers   verb: POST
Layers       : SupplierController.create → SupplierService.create ; in-process PtyPartyApi.registerSupplier → SupplierService.create
Request      : body `SupplierCreateRequest` — nameAr (DBF-PTY-005, String ≤ 200) · nameEn (DBF-PTY-006, String ≤ 200) — one of them required (RULE-PTY-001) · mobile (DBF-PTY-002, String ≤ 20) · email (DBF-PTY-003, String ≤ 254) · taxRegistrationNo (DBF-PTY-004, String ≤ 30) · paymentTermDays (DBF-PTY-026, Integer, optional — RULE-PTY-008) · partyId (DBF-PTY-027, Long, optional — present = add the Supplier role to that Party; its Party fields are then ignored, REQ-PTY-025). Excluded: supplierPk, code, isActiveFl, audit.
Response     : 201 · `Supplier` (as API-PTY-007) · ApiResponse<Supplier>
Validations  : RULE-PTY-001 → PTY-422-NAME-REQUIRED · RULE-PTY-003 → PTY-422-EMAIL-FORMAT · RULE-PTY-004 → PTY-422-MOBILE-FORMAT · RULE-PTY-002 → PTY-409-EMAIL-TAKEN · RULE-PTY-008 → PTY-422-PAYMENT-TERMS-RANGE · RULE-PTY-012 → PTY-422-WALK-IN-SUPPLIER · RULE-PTY-009 → PTY-409-ROLE-HELD ({roleAr} مورّد, {roleEn} Supplier)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-PARTY-NOT-FOUND, PTY-409-EMAIL-TAKEN, PTY-409-ROLE-HELD, PTY-422-NAME-REQUIRED, PTY-422-EMAIL-FORMAT, PTY-422-MOBILE-FORMAT, PTY-422-PAYMENT-TERMS-RANGE, PTY-422-WALK-IN-SUPPLIER, PTY-500-INTERNAL
Orchestration : (A) partyId absent — validate RULE-PTY-001, 003, 004, 008 → QR-PTY-004 (RULE-PTY-002) → Party code DBF-PTY-007 from `NumberSeriesApi` series PTY_PARTY → insert Party QR-PTY-002 with DBF-PTY-008 (isActiveFl) true → insert Supplier QR-PTY-018 with DBF-PTY-027 (partyFk) = new Party PK, DBF-PTY-026, DBF-PTY-028 (isActiveFl) true. (B) partyId present — lock QR-PTY-001 (404) → QR-PTY-007: the Party's Customer has DBF-PTY-018 (isWalkInFl) true → RULE-PTY-012 → QR-PTY-017 (Party already holds a Supplier → RULE-PTY-009) → RULE-PTY-008 → Party inactive: QR-PTY-004 then DBF-PTY-008 true (QR-PTY-003) → insert Supplier QR-PTY-018 as in (A). The Party count is unchanged in (B) (AC-PTY-030).
Repository   : QR-PTY-001, QR-PTY-002, QR-PTY-003, QR-PTY-004, QR-PTY-007, QR-PTY-017, QR-PTY-018 · SAVE · join NONE · transaction READ_WRITE
Concurrency  : as API-PTY-001 — `NumberSeriesApi` atomic code; UQ_PTY_PARTY_EMAIL backstop; the Party row lock serialises role adds, UQ_PTY_SUPPLIER_PARTY_FK backstops → PTY-409-ROLE-HELD.
Security     : SCR-REQ-PTY-002 · PERM_PTY_SUPPLIER_CREATE ("Make supplier" from the Customers screen also requires it — SRS B4)
Localization : messages ar + en
<!-- API:API-PTY-006:END -->

<!-- API:API-PTY-007:START traces=REQ-PTY-003,REQ-PTY-019,REQ-PTY-020,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-025,DBF-PTY-026,DBF-PTY-027,DBF-PTY-028 -->
### API-PTY-007 — read Supplier
Entity       : ENT-PTY-003, ENT-PTY-001 · operation VIEW · serves: read (SCR-REQ-PTY-002)
Honours      : CON-PTY-010, CON-PTY-003
Endpoint     : /api/v1/pty/suppliers/{id}   verb: GET
Layers       : SupplierController.get → SupplierService.read ; in-process PtyPartyApi.readSupplier → SupplierService.read
Request      : path id (DBF-PTY-025, Long, required) · query forNewDocument (Boolean, optional, default false)
Response     : 200 · `Supplier` {supplierPk (DBF-PTY-025), partyFk (DBF-PTY-027), code (DBF-PTY-007), nameAr (DBF-PTY-005), nameEn (DBF-PTY-006), mobile (DBF-PTY-002), email (DBF-PTY-003), taxRegistrationNo (DBF-PTY-004), paymentTermDays (DBF-PTY-026), isActiveFl (DBF-PTY-028), createdBy, createdAt, updatedBy, updatedAt (DBF-PTY-029 … DBF-PTY-032)} · ApiResponse<Supplier>
Validations  : RULE-PTY-005 (forNewDocument = true and inactive) → PTY-422-ROLE-INACTIVE
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-SUPPLIER-NOT-FOUND, PTY-422-ROLE-INACTIVE, PTY-500-INTERNAL
Orchestration : load QR-PTY-015 (absent → 404) → RULE-PTY-005 when forNewDocument → map. An inactive Supplier is returned otherwise (REQ-PTY-020).
Repository   : QR-PTY-015 · FIND_ONE · join PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-002 · PERM_PTY_SUPPLIER_VIEW
Localization : nameAr / nameEn; messages ar + en
<!-- API:API-PTY-007:END -->

<!-- API:API-PTY-008:START traces=REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-017,REQ-PTY-022,REQ-PTY-024,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-025,DBF-PTY-026 -->
### API-PTY-008 — update Supplier
Entity       : ENT-PTY-003, ENT-PTY-001 · operation UPDATE · serves: update (SCR-REQ-PTY-002)
Honours      : CON-PTY-003, CON-PTY-001
Endpoint     : /api/v1/pty/suppliers/{id}   verb: PUT
Layers       : SupplierController.update → SupplierService.update
Request      : path id (DBF-PTY-025, Long, required) · body `SupplierUpdateRequest` — nameAr (DBF-PTY-005) · nameEn (DBF-PTY-006) · mobile (DBF-PTY-002) · email (DBF-PTY-003) · taxRegistrationNo (DBF-PTY-004) · paymentTermDays (DBF-PTY-026, Integer, optional) · code (DBF-PTY-007, optional — compared only, RULE-PTY-007). Excluded: partyFk, isActiveFl, audit.
Response     : 200 · `Supplier` (as API-PTY-007) · ApiResponse<Supplier>
Validations  : RULE-PTY-007 → PTY-422-CODE-READ-ONLY · RULE-PTY-001 → PTY-422-NAME-REQUIRED · RULE-PTY-003 → PTY-422-EMAIL-FORMAT · RULE-PTY-004 → PTY-422-MOBILE-FORMAT · RULE-PTY-002 → PTY-409-EMAIL-TAKEN · RULE-PTY-008 → PTY-422-PAYMENT-TERMS-RANGE
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-SUPPLIER-NOT-FOUND, PTY-409-EMAIL-TAKEN, PTY-422-CODE-READ-ONLY, PTY-422-NAME-REQUIRED, PTY-422-EMAIL-FORMAT, PTY-422-MOBILE-FORMAT, PTY-422-PAYMENT-TERMS-RANGE, PTY-500-INTERNAL
Orchestration : lock QR-PTY-016 (404) → lock the Party QR-PTY-001 → validate → email changed: QR-PTY-004 → write the Party's DBF-PTY-005, 006, 002, 003, 004 (QR-PTY-003; the Customer of the same Party reads the change — REQ-PTY-024) → write DBF-PTY-026 (QR-PTY-019) → return as API-PTY-007.
Repository   : QR-PTY-016, QR-PTY-001, QR-PTY-004, QR-PTY-003, QR-PTY-019 · UPDATE · join NONE · READ_WRITE
Concurrency  : Party row lock; UQ_PTY_PARTY_EMAIL backstop (ADR-PTY-026).
Security     : SCR-REQ-PTY-002 · PERM_PTY_SUPPLIER_UPDATE
Localization : messages ar + en
<!-- API:API-PTY-008:END -->

<!-- API:API-PTY-009:START traces=REQ-PTY-020,REQ-PTY-023,REQ-PTY-028,DBF-PTY-008,DBF-PTY-025,DBF-PTY-028 -->
### API-PTY-009 — deactivate Supplier
Entity       : ENT-PTY-003 · operation DELETE · serves: deactivate (SCR-REQ-PTY-002)
Honours      : CON-PTY-003, CON-PTY-021
Endpoint     : /api/v1/pty/suppliers/{id}   verb: DELETE
Layers       : SupplierController.deactivate → SupplierService.deactivate
Request      : path id (DBF-PTY-025, Long, required) · no body
Response     : 200 · `Confirmation` {id, code} with messageAr / messageEn = RULE-PTY-006 confirmation · ApiResponse<Confirmation>
Validations  : none beyond existence
Messages     : RULE-PTY-006 — ar: تم إيقاف {code}. تبقى المستندات السابقة كما هي، ولن يُعرض في المستندات الجديدة. · en: {code} is deactivated. Earlier documents keep it; it is no longer offered on new documents.
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-SUPPLIER-NOT-FOUND, PTY-500-INTERNAL
Orchestration : lock QR-PTY-016 → already inactive: confirmation, no event → DBF-PTY-028 (isActiveFl) false (QR-PTY-019) → lock Party QR-PTY-001 → QR-PTY-021 = 0: DBF-PTY-008 (isActiveFl) false (QR-PTY-003) → publish `PartyRoleDeactivatedEvent` {role SUPPLIER, identifier supplierPk, code}.
Repository   : QR-PTY-016, QR-PTY-019, QR-PTY-001, QR-PTY-021, QR-PTY-003 · UPDATE · READ_WRITE
Concurrency  : Party row lock — as API-PTY-004.
Security     : SCR-REQ-PTY-002 · PERM_PTY_SUPPLIER_DELETE
Localization : confirmation in ar + en
<!-- API:API-PTY-009:END -->

<!-- API:API-PTY-010:START traces=REQ-PTY-010,REQ-PTY-021,REQ-PTY-029,DBF-PTY-003,DBF-PTY-008,DBF-PTY-025,DBF-PTY-028 -->
### API-PTY-010 — reactivate Supplier
Entity       : ENT-PTY-003 · operation DELETE (reverse of deactivation — ADR-PTY-025) · serves: reactivate (SCR-REQ-PTY-002)
Honours      : CON-PTY-003
Endpoint     : /api/v1/pty/suppliers/{id}/activation   verb: PATCH
Layers       : SupplierController.reactivate → SupplierService.reactivate
Request      : path id (DBF-PTY-025, Long, required) · body `ActivationRequest` { isActiveFl (DBF-PTY-028, Boolean, required, must be true — deactivation is API-PTY-009) } (ADR-PTY-031)
Response     : 200 · `Supplier` (as API-PTY-007) · ApiResponse<Supplier>
Validations  : body isActiveFl missing or not true → PTY-400-VALIDATION (ADR-PTY-031) · RULE-PTY-002 (Party inactive and its email held by another active Party) → PTY-409-EMAIL-TAKEN
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-SUPPLIER-NOT-FOUND, PTY-409-EMAIL-TAKEN, PTY-500-INTERNAL
Orchestration : lock QR-PTY-016 → lock Party QR-PTY-001 → Party inactive: QR-PTY-004 (RULE-PTY-002), DBF-PTY-008 (isActiveFl) true (QR-PTY-003) → DBF-PTY-028 (isActiveFl) true (QR-PTY-019).
Repository   : QR-PTY-016, QR-PTY-001, QR-PTY-004, QR-PTY-003, QR-PTY-019 · UPDATE · READ_WRITE
Concurrency  : as API-PTY-005 (UQ_PTY_PARTY_EMAIL backstop).
Security     : SCR-REQ-PTY-002 · PERM_PTY_SUPPLIER_DELETE
Localization : messages ar + en
<!-- API:API-PTY-010:END -->

<!-- API:API-PTY-011:START traces=REQ-PTY-030,REQ-PTY-034,REQ-PTY-037,REQ-PTY-038,DBF-PTY-033,DBF-PTY-034,DBF-PTY-035,DBF-PTY-036,DBF-PTY-037,DBF-PTY-038,DBF-PTY-039,DBF-PTY-040,DBF-PTY-041,DBF-PTY-042,DBF-PTY-043,DBF-PTY-044,DBF-PTY-045 -->
### API-PTY-011 — create Address
Entity       : ENT-PTY-004, ENT-PTY-001 · operation CREATE · serves: address create (SCR-REQ-PTY-001, SCR-REQ-PTY-002)
Honours      : CON-PTY-004, CON-PTY-007
Endpoint     : /api/v1/pty/parties/{partyId}/addresses   verb: POST
Layers       : AddressController.create → AddressService.create
Request      : path partyId (DBF-PTY-041, Long, required) · body `AddressRequest` — addressTypeCode (DBF-PTY-034, String ≤ 50, required — RULE-PTY-016) · line1 (DBF-PTY-035, String ≤ 200, required — RULE-PTY-015) · line2 (DBF-PTY-036, String ≤ 200) · city (DBF-PTY-037, String ≤ 100) · region (DBF-PTY-038, String ≤ 100) · postalCode (DBF-PTY-039, String ≤ 20) · nameAr (DBF-PTY-042, String ≤ 100) · nameEn (DBF-PTY-043, String ≤ 100). Excluded: addressPk, code, isDefaultFl, isActiveFl, audit.
Response     : 201 · `Address` (as API-PTY-012) · ApiResponse<Address>
Validations  : RULE-PTY-015 → PTY-422-ADDRESS-LINE-REQUIRED · RULE-PTY-016 → PTY-422-ADDRESS-TYPE-INVALID
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-PARTY-NOT-FOUND, PTY-422-ADDRESS-LINE-REQUIRED, PTY-422-ADDRESS-TYPE-INVALID, PTY-500-INTERNAL
Orchestration : lock Party QR-PTY-001 (absent → 404) → permission by role page (ADR-PTY-025) → validate RULE-PTY-015, RULE-PTY-016 → QR-PTY-024 gives the next ordinal → DBF-PTY-044 (code) → QR-PTY-030: no active Address of this type → DBF-PTY-040 (isDefaultFl) true (REQ-PTY-034, RULE-PTY-017), else false → DBF-PTY-041 (partyFk) = partyId, DBF-PTY-045 (isActiveFl) true → insert QR-PTY-025.
Repository   : QR-PTY-001, QR-PTY-024, QR-PTY-030, QR-PTY-025 · SAVE · join NONE · READ_WRITE
Concurrency  : The ordinal and the "first of its type" decision are read-then-write: the Party row lock (QR-PTY-001) makes two concurrent creates on one Party run one after the other; UQ_PTY_ADDRESS_PARTY_CODE and UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop (ADR-PTY-026).
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · PERM_PTY_CUSTOMER_UPDATE when the Party holds a Customer, PERM_PTY_SUPPLIER_UPDATE when it holds a Supplier (SRS B4 "Address create, update and deactivation require UPDATE on this page"; ADR-PTY-025)
Localization : address label nameAr / nameEn; messages ar + en
<!-- API:API-PTY-011:END -->

<!-- API:API-PTY-012:START traces=REQ-PTY-033,DBF-PTY-033,DBF-PTY-034,DBF-PTY-035,DBF-PTY-036,DBF-PTY-037,DBF-PTY-038,DBF-PTY-039,DBF-PTY-040,DBF-PTY-041,DBF-PTY-042,DBF-PTY-043,DBF-PTY-044,DBF-PTY-045 -->
### API-PTY-012 — read Address
Entity       : ENT-PTY-004 · operation VIEW · serves: read (an Address by its identifier, inactive included)
Honours      : CON-PTY-012, CON-PTY-004
Endpoint     : /api/v1/pty/addresses/{id}   verb: GET
Layers       : AddressController.get → AddressService.read ; in-process PtyPartyApi.readAddress → AddressService.read
Request      : path id (DBF-PTY-033, Long, required)
Response     : 200 · `Address` {addressPk (DBF-PTY-033), partyFk (DBF-PTY-041), addressTypeCode (DBF-PTY-034), code (DBF-PTY-044), nameAr (DBF-PTY-042), nameEn (DBF-PTY-043), line1 (DBF-PTY-035), line2 (DBF-PTY-036), city (DBF-PTY-037), region (DBF-PTY-038), postalCode (DBF-PTY-039), isDefaultFl (DBF-PTY-040), isActiveFl (DBF-PTY-045), createdBy, createdAt, updatedBy, updatedAt (DBF-PTY-046 … DBF-PTY-049)} · ApiResponse<Address>
Validations  : none
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-ADDRESS-NOT-FOUND, PTY-500-INTERNAL
Orchestration : load QR-PTY-022 (absent → 404) → map; an inactive Address is returned (AC-PTY-039).
Repository   : QR-PTY-022 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · PERM_PTY_CUSTOMER_VIEW or PERM_PTY_SUPPLIER_VIEW (ADR-PTY-025)
Localization : address label nameAr / nameEn
<!-- API:API-PTY-012:END -->

<!-- API:API-PTY-013:START traces=REQ-PTY-031,REQ-PTY-037,REQ-PTY-038,DBF-PTY-033,DBF-PTY-034,DBF-PTY-035,DBF-PTY-036,DBF-PTY-037,DBF-PTY-038,DBF-PTY-039,DBF-PTY-040,DBF-PTY-042,DBF-PTY-043 -->
### API-PTY-013 — update Address
Entity       : ENT-PTY-004 · operation UPDATE · serves: address update (SCR-REQ-PTY-001, SCR-REQ-PTY-002)
Honours      : CON-PTY-004, CON-PTY-007
Endpoint     : /api/v1/pty/parties/{partyId}/addresses/{id}   verb: PUT
Layers       : AddressController.update → AddressService.update
Request      : path partyId (DBF-PTY-041, Long, required), id (DBF-PTY-033, Long, required) · body `AddressRequest` — addressTypeCode (DBF-PTY-034, required) · line1 (DBF-PTY-035, required) · line2 (DBF-PTY-036) · city (DBF-PTY-037) · region (DBF-PTY-038) · postalCode (DBF-PTY-039) · nameAr (DBF-PTY-042) · nameEn (DBF-PTY-043). Excluded: partyFk (never changes), code, isDefaultFl, isActiveFl, audit.
Response     : 200 · `Address` (as API-PTY-012) · ApiResponse<Address>
Validations  : RULE-PTY-015 → PTY-422-ADDRESS-LINE-REQUIRED · RULE-PTY-016 → PTY-422-ADDRESS-TYPE-INVALID
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-PARTY-NOT-FOUND, PTY-404-ADDRESS-NOT-FOUND, PTY-422-ADDRESS-LINE-REQUIRED, PTY-422-ADDRESS-TYPE-INVALID, PTY-500-INTERNAL
Orchestration : lock Party QR-PTY-001 (404) → lock QR-PTY-023 (Address not of this Party → PTY-404-ADDRESS-NOT-FOUND) → permission by role page → validate → addressTypeCode changed on an active Address: if it was the default of the old type, DBF-PTY-040 (isDefaultFl) false and QR-PTY-029 promotes the most recently updated remaining active Address of the old type (QR-PTY-026); for the new type QR-PTY-030 — none active → DBF-PTY-040 true (RULE-PTY-017, ADR-PTY-026) → write the fields (QR-PTY-026); DBF-PTY-041 stays unchanged (AC-PTY-037).
Repository   : QR-PTY-001, QR-PTY-023, QR-PTY-029, QR-PTY-030, QR-PTY-026 · UPDATE · join NONE · READ_WRITE
Concurrency  : Party row lock serialises default decisions of the Party; UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop. The old default is cleared before the new one is set in the same transaction (the partial unique index is checked per statement).
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · PERM_PTY_CUSTOMER_UPDATE or PERM_PTY_SUPPLIER_UPDATE by role held (ADR-PTY-025)
Localization : messages ar + en
<!-- API:API-PTY-013:END -->

<!-- API:API-PTY-014:START traces=REQ-PTY-033,REQ-PTY-036,DBF-PTY-033,DBF-PTY-040,DBF-PTY-045,DBF-PTY-049 -->
### API-PTY-014 — deactivate Address
Entity       : ENT-PTY-004 · operation DELETE · serves: address deactivate (SCR-REQ-PTY-001, SCR-REQ-PTY-002)
Honours      : CON-PTY-004
Endpoint     : /api/v1/pty/parties/{partyId}/addresses/{id}   verb: DELETE
Layers       : AddressController.deactivate → AddressService.deactivate
Request      : path partyId (DBF-PTY-041, Long, required), id (DBF-PTY-033, Long, required) · no body
Response     : 200 · `Confirmation` {id, code} with messageAr / messageEn = RULE-PTY-006 confirmation ({code} = Address code) · ApiResponse<Confirmation>
Validations  : none beyond existence
Messages     : RULE-PTY-006 — ar: تم إيقاف {code}. تبقى المستندات السابقة كما هي، ولن يُعرض في المستندات الجديدة. · en: {code} is deactivated. Earlier documents keep it; it is no longer offered on new documents.
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-PARTY-NOT-FOUND, PTY-404-ADDRESS-NOT-FOUND, PTY-500-INTERNAL
Orchestration : lock Party QR-PTY-001 → lock QR-PTY-023 (404) → already inactive: confirmation → DBF-PTY-045 (isActiveFl) false; if DBF-PTY-040 was true: set it false and QR-PTY-029 finds the remaining active Address of the same type with the latest DBF-PTY-049 (updatedAt) → its DBF-PTY-040 (isDefaultFl) true (REQ-PTY-036) → write QR-PTY-026. No event for Addresses (RULE-PTY-006 names Customers and Suppliers).
Repository   : QR-PTY-001, QR-PTY-023, QR-PTY-029, QR-PTY-026 · UPDATE · READ_WRITE
Concurrency  : Party row lock serialises the reassignment; UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop.
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · PERM_PTY_CUSTOMER_UPDATE or PERM_PTY_SUPPLIER_UPDATE by role held (ADR-PTY-025)
Localization : confirmation in ar + en
<!-- API:API-PTY-014:END -->

<!-- API:API-PTY-015:START traces=REQ-PTY-035,DBF-PTY-033,DBF-PTY-034,DBF-PTY-040,DBF-PTY-041 -->
### API-PTY-015 — set default Address
Entity       : ENT-PTY-004 · operation UPDATE · serves: set default (SCR-REQ-PTY-001, SCR-REQ-PTY-002)
Honours      : CON-PTY-004
Endpoint     : /api/v1/pty/parties/{partyId}/addresses/{id}/default   verb: PATCH
Layers       : AddressController.setDefault → AddressService.setDefault
Request      : path partyId (DBF-PTY-041, Long, required), id (DBF-PTY-033, Long, required) · body `DefaultAddressRequest` { isDefaultFl (DBF-PTY-040, Boolean, required, must be true — a default is cleared only by marking another Address of the type, RULE-PTY-017) } (ADR-PTY-031)
Response     : 200 · `Address` (as API-PTY-012) with messageAr / messageEn = RULE-PTY-017 confirmation ({typeAr} / {typeEn} = ADDRESS_TYPE labels from mdl) · ApiResponse<Address>
Validations  : body isDefaultFl missing or not true → PTY-400-VALIDATION (ADR-PTY-031) · the Address must be active (inactive → PTY-404-ADDRESS-NOT-FOUND, ADR-PTY-026)
Messages     : RULE-PTY-017 — ar: أصبح هذا العنوان هو العنوان الافتراضي من نوع {typeAr}. · en: This address is now the default {typeEn} address.
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-PARTY-NOT-FOUND, PTY-404-ADDRESS-NOT-FOUND, PTY-500-INTERNAL
Orchestration : lock Party QR-PTY-001 → lock QR-PTY-023 → QR-PTY-028 finds the current default of the same Party and DBF-PTY-034 type → its DBF-PTY-040 (isDefaultFl) false (QR-PTY-026) → this Address DBF-PTY-040 true (QR-PTY-026). Already default → unchanged, message returned.
Repository   : QR-PTY-001, QR-PTY-023, QR-PTY-028, QR-PTY-026 · UPDATE · READ_WRITE
Concurrency  : Two concurrent "set default" on one Party are serialised by the Party row lock; the previous default is cleared before the new one is set; UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop.
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · PERM_PTY_CUSTOMER_UPDATE or PERM_PTY_SUPPLIER_UPDATE by role held (ADR-PTY-025)
Localization : confirmation in ar + en
<!-- API:API-PTY-015:END -->

<!-- API:API-PTY-016:START traces=REQ-PTY-068,REQ-PTY-070,REQ-PTY-073,REQ-PTY-080,REQ-PTY-085,DBF-PTY-013,DBF-PTY-015,DBF-PTY-016,DBF-PTY-018 -->
### API-PTY-016 — read credit standing
Entity       : ENT-PTY-002, ENT-PTY-001 · operation VIEW · serves: read (credit standing) (SCR-REQ-PTY-003)
Honours      : CON-PTY-002, CON-PTY-022
Endpoint     : /api/v1/pty/customer-credits/{customerId}   verb: GET
Layers       : CustomerCreditController.get → CustomerCreditService.standing
Request      : path customerId (DBF-PTY-013, Long, required)
Response     : 200 · `CustomerCredit` {customerPk (DBF-PTY-013), code (DBF-PTY-007), nameAr (DBF-PTY-005), nameEn (DBF-PTY-006), customerGroupCode (DBF-PTY-014), isWalkInFl (DBF-PTY-018), creditLimit (DBF-PTY-015), paymentTermDays (DBF-PTY-016), openExposure (derived — null when unavailable), availableCredit (derived = creditLimit − openExposure; null when either is null), exposureAvailable (derived, Boolean)} · ApiResponse<CustomerCredit>
Validations  : RULE-PTY-023 (`b2bCredit` off) → PTY-422-CREDIT-NOT-ENABLED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-CREDIT-NOT-ENABLED, PTY-500-INTERNAL
Orchestration : `CreditFlag.isOn()` (off → RULE-PTY-023, the screen is hidden — REQ-PTY-070) → load QR-PTY-005 (404) → `ExposureService.openExposure(customerId)` sums every registered contributor within the 3-second budget (REQ-PTY-080, ADR-PTY-028) → availableCredit = creditLimit − openExposure (REQ-PTY-085).
Repository   : QR-PTY-005 · FIND_ONE · join PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-003 · PERM_PTY_CUSTOMER_CREDIT_VIEW
Localization : nameAr / nameEn; messages ar + en
<!-- API:API-PTY-016:END -->

<!-- API:API-PTY-017:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-072,REQ-PTY-073,REQ-PTY-106,REQ-PTY-109,DBF-PTY-013,DBF-PTY-015,DBF-PTY-016,DBF-PTY-018 -->
### API-PTY-017 — update credit
Entity       : ENT-PTY-002 · operation UPDATE · serves: update (credit limit, payment terms) (SCR-REQ-PTY-003)
Honours      : CON-PTY-002
Endpoint     : /api/v1/pty/customer-credits/{customerId}   verb: PUT
Layers       : CustomerCreditController.update → CustomerCreditService.update
Request      : path customerId (DBF-PTY-013, Long, required) · body `CustomerCreditUpdateRequest` — creditLimit (DBF-PTY-015, BigDecimal scale 3, nullable — null = no credit allowance, REQ-PTY-068) · paymentTermDays (DBF-PTY-016, Integer, nullable). PUT replaces both values. Excluded: everything else.
Response     : 200 · `CustomerCredit` (as API-PTY-016) · ApiResponse<CustomerCredit>
Validations  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED · RULE-PTY-011 (walk-in and any non-null value) → PTY-422-WALK-IN-CREDIT · RULE-PTY-022 → PTY-422-CREDIT-LIMIT-NEGATIVE · RULE-PTY-008 → PTY-422-PAYMENT-TERMS-RANGE · RULE-PTY-030 (the audit write failed) → PTY-500-AUDIT-NOT-RECORDED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-CREDIT-NOT-ENABLED, PTY-422-WALK-IN-CREDIT, PTY-422-CREDIT-LIMIT-NEGATIVE, PTY-422-PAYMENT-TERMS-RANGE, PTY-500-AUDIT-NOT-RECORDED, PTY-500-INTERNAL
Orchestration : `CreditFlag.isOn()` (off → RULE-PTY-023, REQ-PTY-072) → lock QR-PTY-006 (404) → RULE-PTY-011 on DBF-PTY-018 → RULE-PTY-022, RULE-PTY-008 → for each of DBF-PTY-015 (creditLimit), DBF-PTY-016 (paymentTermDays) whose value changes: `AuditApi` records {Customer, field, old value, new value, user, time} inside the transaction (REQ-PTY-106); any audit failure → RULE-PTY-030 and the transaction rolls back (REQ-PTY-109) → write QR-PTY-009 → return as API-PTY-016 (exposure included).
Repository   : QR-PTY-006, QR-PTY-009 · UPDATE · join NONE · READ_WRITE
Concurrency  : The Customer row lock (QR-PTY-006) serialises two credit edits of one Customer, so each audit event's old value is the value the previous edit committed.
Security     : SCR-REQ-PTY-003 · PERM_PTY_CUSTOMER_CREDIT_UPDATE
Localization : messages ar + en
<!-- API:API-PTY-017:END -->

<!-- API:API-PTY-018:START traces=REQ-PTY-078,REQ-PTY-106,REQ-PTY-109,DBF-PTY-013,DBF-PTY-015,DBF-PTY-016 -->
### API-PTY-018 — clear credit
Entity       : ENT-PTY-002 · operation UPDATE (clears two fields; the page grants no DELETE — ADR-PTY-025) · serves: clear credit (SCR-REQ-PTY-003)
Honours      : CON-PTY-002
Endpoint     : /api/v1/pty/customer-credits/{customerId}   verb: DELETE
Layers       : CustomerCreditController.clear → CustomerCreditService.clear
Request      : path customerId (DBF-PTY-013, Long, required) · no body
Response     : 200 · `CustomerCredit` (as API-PTY-016, both values null) · ApiResponse<CustomerCredit>
Validations  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED · RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-CREDIT-NOT-ENABLED, PTY-500-AUDIT-NOT-RECORDED, PTY-500-INTERNAL
Orchestration : `CreditFlag.isOn()` → lock QR-PTY-006 (404) → for each non-null of DBF-PTY-015 (creditLimit), DBF-PTY-016 (paymentTermDays): `AuditApi` records old → empty (RULE-PTY-030 on failure) → set both null (QR-PTY-009). The switch-off dependency count drops by one (AC-PTY-088).
Repository   : QR-PTY-006, QR-PTY-009 · UPDATE · READ_WRITE
Concurrency  : Customer row lock — as API-PTY-017.
Security     : SCR-REQ-PTY-003 · PERM_PTY_CUSTOMER_CREDIT_UPDATE
Localization : messages ar + en
<!-- API:API-PTY-018:END -->

<!-- API:API-PTY-019:START traces=REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-104,REQ-PTY-105,DBF-PTY-050,DBF-PTY-051,DBF-PTY-052,DBF-PTY-053,DBF-PTY-054,DBF-PTY-055,DBF-PTY-060,DBF-PTY-061,DBF-PTY-062,DBF-PTY-063 -->
### API-PTY-019 — create Credit override request
Entity       : ENT-PTY-005, ENT-PTY-002 · operation CREATE · serves: create (from a sale) (SCR-REQ-PTY-004)
Honours      : CON-PTY-016, CON-PTY-005, CON-PTY-008
Endpoint     : /api/v1/pty/credit-overrides   verb: POST
Layers       : CreditOverrideController.create → CreditOverrideService.create ; in-process PtyPartyApi.createCreditOverrideRequest → CreditOverrideService.create
Request      : body `CreditOverrideCreateRequest` — customerId (DBF-PTY-060, Long, required) · saleAmount (DBF-PTY-051, BigDecimal scale 3, required — RULE-PTY-032) · sourceDocumentRef (DBF-PTY-055, String ≤ 60, required). Excluded: creditOverrideRequestPk, docNo, docDate, statusCode, creditLimitAtRequest, openExposure, creditCheckResultCode, decidedBy, decidedAt, decisionReason, usedDocumentRef, audit.
Response     : 201 · `CreditOverrideRequest` (as API-PTY-020, statusCode PENDING) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED · RULE-PTY-032 → PTY-422-SALE-AMOUNT-NOT-POSITIVE · RULE-PTY-033 → PTY-422-WALK-IN-NO-CREDIT-SALE · RULE-PTY-025 → PTY-422-OVERRIDE-NOT-NEEDED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-CREDIT-NOT-ENABLED, PTY-422-SALE-AMOUNT-NOT-POSITIVE, PTY-422-WALK-IN-NO-CREDIT-SALE, PTY-422-OVERRIDE-NOT-NEEDED, PTY-500-INTERNAL
Orchestration : `CreditFlag.isOn()` (off → RULE-PTY-023, REQ-PTY-072) → RULE-PTY-032 → load Customer QR-PTY-005 (404) → DBF-PTY-018 true → RULE-PTY-033 → the server re-runs the credit check of API-PTY-035 for (customerId, saleAmount) — never trusts a caller's result (ADR-PTY-028) → WITHIN_LIMIT → RULE-PTY-025 → allocate DBF-PTY-061 (docNo) from `NumberSeriesApi` series PTY_CREDIT_OVERRIDE, DBF-PTY-062 (docDate) = now, DBF-PTY-063 (statusCode) PENDING, DBF-PTY-052 (creditLimitAtRequest) = the Customer's creditLimit, DBF-PTY-053 (openExposure) = the check's open exposure (null when unavailable), DBF-PTY-054 (creditCheckResultCode) = the check's result, DBF-PTY-060 (customerFk) = customerId → insert QR-PTY-031 (createdBy = the requester, platform-filled) → after commit: `SecUserDirectoryApi` lists the active users holding PERM_PTY_CREDIT_OVERRIDE_UPDATE and `NotificationDispatchApi` sends each one in-app notification — ar «طلب تجاوز حد الائتمان {docNo} للعميل {name} بانتظار موافقتك.» · en «Credit override request {docNo} for {name} is waiting for your approval.» (REQ-PTY-093, AC-PTY-105). Creation is allowed under WARN too (the request is then optional — ADR-PTY-028).
Repository   : QR-PTY-005, QR-PTY-031 · SAVE · join PTY_PARTY on the Customer read (ADR-PTY-027) · READ_WRITE
Concurrency  : docNo — atomic allocation by `NumberSeriesApi`, backstopped by UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO. No shared row is read and then written: each request inserts its own row.
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_CREATE
Localization : notification and messages in ar + en
<!-- API:API-PTY-019:END -->

<!-- API:API-PTY-020:START traces=REQ-PTY-103,DBF-PTY-050,DBF-PTY-051,DBF-PTY-052,DBF-PTY-053,DBF-PTY-054,DBF-PTY-055,DBF-PTY-056,DBF-PTY-057,DBF-PTY-058,DBF-PTY-059,DBF-PTY-060,DBF-PTY-061,DBF-PTY-062,DBF-PTY-063,DBF-PTY-064 -->
### API-PTY-020 — read Credit override request
Entity       : ENT-PTY-005 · operation VIEW · serves: read (SCR-REQ-PTY-004)
Honours      : CON-PTY-017, CON-PTY-005
Endpoint     : /api/v1/pty/credit-overrides/{id}   verb: GET
Layers       : CreditOverrideController.get → CreditOverrideService.read ; in-process PtyPartyApi.readCreditOverrideRequest → CreditOverrideService.read
Request      : path id (DBF-PTY-050, Long, required)
Response     : 200 · `CreditOverrideRequest` {creditOverrideRequestPk (DBF-PTY-050), docNo (DBF-PTY-061), docDate (DBF-PTY-062), statusCode (DBF-PTY-063), customerFk (DBF-PTY-060), customerCode, customerNameAr, customerNameEn (read through DBF-PTY-060), saleAmount (DBF-PTY-051), creditLimitAtRequest (DBF-PTY-052), openExposure (DBF-PTY-053), creditCheckResultCode (DBF-PTY-054), sourceDocumentRef (DBF-PTY-055), decidedBy (DBF-PTY-056), decidedAt (DBF-PTY-057), decisionReason (DBF-PTY-058), usedDocumentRef (DBF-PTY-059), createdBy (DBF-PTY-064), createdAt, updatedBy, updatedAt (DBF-PTY-065 … DBF-PTY-067)} · ApiResponse<CreditOverrideRequest>
Validations  : visibility — a caller without PERM_PTY_CREDIT_OVERRIDE_UPDATE reads only the requests it created (REQ-PTY-102 applied to reads); another's request answers PTY-404-CREDIT-OVERRIDE-NOT-FOUND (ADR-PTY-025)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-500-INTERNAL
Orchestration : load QR-PTY-032 (absent or not visible → 404) → map.
Repository   : QR-PTY-032 · FIND_ONE · join PTY_CUSTOMER, PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_VIEW
Localization : Customer names ar + en
<!-- API:API-PTY-020:END -->

<!-- API:API-PTY-021:START traces=REQ-PTY-094,REQ-PTY-098,REQ-PTY-107,DBF-PTY-050,DBF-PTY-056,DBF-PTY-057,DBF-PTY-061,DBF-PTY-063 -->
### API-PTY-021 — approve Credit override request
Entity       : ENT-PTY-005 · operation UPDATE · serves: approve (SCR-REQ-PTY-004)
Honours      : CON-PTY-005, CON-PTY-008
Endpoint     : /api/v1/pty/credit-overrides/{id}/approval   verb: PATCH
Layers       : CreditOverrideController.approve → CreditOverrideService.approve
Request      : path id (DBF-PTY-050, Long, required) · body `CreditOverrideDecisionRequest` (optional) — docNo (DBF-PTY-061, String, optional — compared only, RULE-PTY-031)
Response     : 200 · `CreditOverrideRequest` (as API-PTY-020, statusCode APPROVED) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-031 (docNo present and different) → PTY-422-DOC-NO-READ-ONLY · RULE-PTY-026 (not PENDING) → PTY-409-REQUEST-NOT-PENDING ({statusAr} / {statusEn} = CREDIT_OVERRIDE_STATUS labels)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-409-REQUEST-NOT-PENDING, PTY-422-DOC-NO-READ-ONLY, PTY-500-INTERNAL
Orchestration : lock QR-PTY-033 (404) → RULE-PTY-031 → RULE-PTY-026 → DBF-PTY-063 (statusCode) APPROVED, DBF-PTY-056 (decidedBy) = the current sec user identifier, DBF-PTY-057 (decidedAt) = now → conditional write QR-PTY-034 (expected PENDING) → `AuditApi` records {docNo, Customer, saleAmount, APPROVED, decider, time} (REQ-PTY-107).
Repository   : QR-PTY-033, QR-PTY-034 · UPDATE · join NONE · READ_WRITE
Concurrency  : Two approvers acting at once: QR-PTY-033 locks the row (`FOR UPDATE`) and QR-PTY-034 updates only `WHERE STATUS_CODE = 'PENDING'` — the second decision finds APPROVED (or REJECTED) and receives PTY-409-REQUEST-NOT-PENDING (ADR-PTY-026).
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_UPDATE (the credit-override permission of POL-PTY-008, SRS D-06)
Localization : messages ar + en
<!-- API:API-PTY-021:END -->

<!-- API:API-PTY-022:START traces=REQ-PTY-095,REQ-PTY-098,REQ-PTY-100,REQ-PTY-107,DBF-PTY-050,DBF-PTY-056,DBF-PTY-057,DBF-PTY-058,DBF-PTY-061,DBF-PTY-063 -->
### API-PTY-022 — reject Credit override request
Entity       : ENT-PTY-005 · operation UPDATE · serves: reject (SCR-REQ-PTY-004)
Honours      : CON-PTY-005, CON-PTY-008
Endpoint     : /api/v1/pty/credit-overrides/{id}/rejection   verb: PATCH
Layers       : CreditOverrideController.reject → CreditOverrideService.reject
Request      : path id (DBF-PTY-050, Long, required) · body `CreditOverrideRejectRequest` — decisionReason (DBF-PTY-058, String ≤ 500, required non-blank — RULE-PTY-027) · docNo (DBF-PTY-061, optional — compared only, RULE-PTY-031)
Response     : 200 · `CreditOverrideRequest` (statusCode REJECTED) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-031 → PTY-422-DOC-NO-READ-ONLY · RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING · RULE-PTY-027 → PTY-422-REJECTION-REASON-REQUIRED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-409-REQUEST-NOT-PENDING, PTY-422-DOC-NO-READ-ONLY, PTY-422-REJECTION-REASON-REQUIRED, PTY-500-INTERNAL
Orchestration : lock QR-PTY-033 (404) → RULE-PTY-031 → RULE-PTY-026 → RULE-PTY-027 → DBF-PTY-063 (statusCode) REJECTED, DBF-PTY-056 (decidedBy), DBF-PTY-057 (decidedAt), DBF-PTY-058 (decisionReason) → QR-PTY-034 (expected PENDING) → `AuditApi` decision event (REQ-PTY-107).
Repository   : QR-PTY-033, QR-PTY-034 · UPDATE · READ_WRITE
Concurrency  : as API-PTY-021 — row lock + conditional update; the loser receives PTY-409-REQUEST-NOT-PENDING.
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_UPDATE
Localization : messages ar + en
<!-- API:API-PTY-022:END -->

<!-- API:API-PTY-023:START traces=REQ-PTY-096,REQ-PTY-098,DBF-PTY-050,DBF-PTY-063,DBF-PTY-064 -->
### API-PTY-023 — cancel Credit override request
Entity       : ENT-PTY-005 · operation DELETE · serves: cancel (SCR-REQ-PTY-004)
Honours      : CON-PTY-005
Endpoint     : /api/v1/pty/credit-overrides/{id}   verb: DELETE
Layers       : CreditOverrideController.cancel → CreditOverrideService.cancel
Request      : path id (DBF-PTY-050, Long, required) · no body
Response     : 200 · `CreditOverrideRequest` (statusCode CANCELLED) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-029 (caller is not the creator, DBF-PTY-064) → PTY-403-NOT-REQUESTER · RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-403-NOT-REQUESTER, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-409-REQUEST-NOT-PENDING, PTY-500-INTERNAL
Orchestration : lock QR-PTY-033 (404) → RULE-PTY-029 (DBF-PTY-064 createdBy equals the current principal) → RULE-PTY-026 → DBF-PTY-063 (statusCode) CANCELLED → QR-PTY-034 (expected PENDING). The row is kept (soft): cancelling is a state, not a deletion.
Repository   : QR-PTY-033, QR-PTY-034 · UPDATE · READ_WRITE
Concurrency  : A cancel racing an approval: row lock + conditional update; whichever commits second receives PTY-409-REQUEST-NOT-PENDING.
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_DELETE (limited to the requester by RULE-PTY-029, SRS D-06)
Localization : messages ar + en
<!-- API:API-PTY-023:END -->

<!-- API:API-PTY-024:START traces=REQ-PTY-070,REQ-PTY-087,DBF-PTY-015 -->
### API-PTY-024 — read credit settings
Entity       : ENT-PTY-002 (the setting governs the credit checks of DBF-PTY-015) · operation VIEW · serves: read (SCR-REQ-PTY-005)
Honours      : CON-PTY-008
Endpoint     : /api/v1/pty/credit-settings   verb: GET
Layers       : CreditSettingsController.get → CreditSettingsService.read
Request      : none
Response     : 200 · `CreditSettings` {creditOverrunActionCode (CREDIT_OVERRUN_ACTION code), isDefault (Boolean — true while no value has been saved)} · ApiResponse<CreditSettings>
Validations  : none
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-500-INTERNAL
Orchestration : `SettingsApi` reads the tenant setting PTY_CREDIT_OVERRUN_ACTION (ADR-PTY-029) → absent → BLOCK_WITH_OVERRIDE, isDefault true (REQ-PTY-087).
Repository   : NONE — the value is a tenant setting of the given `cu` (no PTY table, no QR)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-005 · PERM_PTY_CREDIT_SETTINGS_VIEW
Localization : the action label is resolved by the client from mdl
<!-- API:API-PTY-024:END -->

<!-- API:API-PTY-025:START traces=REQ-PTY-086,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,DBF-PTY-015 -->
### API-PTY-025 — update credit settings
Entity       : ENT-PTY-002 · operation UPDATE · serves: update (SCR-REQ-PTY-005)
Honours      : CON-PTY-008
Endpoint     : /api/v1/pty/credit-settings   verb: PUT
Layers       : CreditSettingsController.update → CreditSettingsService.update
Request      : body `CreditSettings` — creditOverrunActionCode (String ≤ 50, required; an active value of CREDIT_OVERRUN_ACTION — WARN or BLOCK_WITH_OVERRIDE; any other value → PTY-400-VALIDATION)
Response     : 200 · `CreditSettings` · ApiResponse<CreditSettings>
Validations  : value check against the closed lookup (PTY-400-VALIDATION); access through the platform authorisation layer (REQ-PTY-090 → PTY-403-FORBIDDEN)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-500-INTERNAL
Orchestration : validate through `MdlLookupApi` → `SettingsApi` saves PTY_CREDIT_OVERRUN_ACTION for the tenant (REQ-PTY-086) → the next credit check reads it (REQ-PTY-088, REQ-PTY-089).
Repository   : NONE — given `cu` setting (no QR)
Concurrency  : NONE — a single-value overwrite; the last save wins, which is the intended semantics of a setting
Security     : SCR-REQ-PTY-005 · PERM_PTY_CREDIT_SETTINGS_UPDATE
Localization : messages ar + en
<!-- API:API-PTY-025:END -->
<!-- SUB:SVC-API-CRUD:END -->

<!-- SUB:SVC-API-SEARCH:START traces=REQ-PTY-001,REQ-PTY-013,REQ-PTY-018,REQ-PTY-032,REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,REQ-PTY-069,REQ-PTY-070,REQ-PTY-073,REQ-PTY-077,REQ-PTY-101,REQ-PTY-102,REQ-PTY-108 -->
### SVC-API-SEARCH — searches and lists

<!-- API:API-PTY-026:START traces=REQ-PTY-001,REQ-PTY-013,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-013,DBF-PTY-014,DBF-PTY-018,DBF-PTY-020 -->
### API-PTY-026 — search Customers
Entity       : ENT-PTY-002, ENT-PTY-001 · operation VIEW · serves: search, list (SCR-REQ-PTY-001)
Honours      : CON-PTY-002, CON-PTY-006
Endpoint     : /api/v1/pty/customers   verb: GET
Layers       : CustomerController.search → CustomerQueryService.search
Request      : query code (DBF-PTY-007, contains) · name (DBF-PTY-005 / DBF-PTY-006, contains, case-insensitive) · mobile (DBF-PTY-002, contains) · email (DBF-PTY-003, contains, case-insensitive) · taxRegistrationNo (DBF-PTY-004, contains) · customerGroupCode (DBF-PTY-014, exact) · isActive (DBF-PTY-020, exact) · page, size · sort — one of code, nameAr, nameEn, mobile, email, taxRegistrationNo, customerGroupCode, isActiveFl (the SRS B2 filter list), default code ascending
Response     : 200 · Page<`CustomerRow`> {customerPk (DBF-PTY-013), code, nameAr, nameEn, mobile, email, taxRegistrationNo, customerGroupCode (DBF-PTY-014), isWalkInFl (DBF-PTY-018 — the walk-in Customer is listed and marked, SRS B2), isActiveFl (DBF-PTY-020)} · paginated · ApiResponse<Page<CustomerRow>>
Validations  : none (filters are optional; an empty page is success)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-500-INTERNAL
Orchestration : QR-PTY-010 → map. Credit fields are not part of this list (they belong to API-PTY-030).
Repository   : QR-PTY-010 · FIND_BY_CRITERIA · join PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_VIEW
Localization : nameAr / nameEn returned
<!-- API:API-PTY-026:END -->

<!-- API:API-PTY-027:START traces=REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-013,DBF-PTY-020 -->
### API-PTY-027 — Customer global search
Entity       : ENT-PTY-002, ENT-PTY-001 · operation VIEW · serves: the one global search (no screen of its own)
Honours      : CON-PTY-020
Endpoint     : /api/v1/pty/customers/global-search   verb: GET
Layers       : CustomerController.globalSearch → CustomerQueryService.globalSearch ; in-process PtyPartyApi.globalSearchCustomers → CustomerQueryService.globalSearch
Request      : query term (String, required)
Response     : 200 · list (≤ 10) of `CustomerSearchHit` {customerPk (DBF-PTY-013), code (DBF-PTY-007), nameAr (DBF-PTY-005), nameEn (DBF-PTY-006), mobile (DBF-PTY-002)} · not paginated · ApiResponse<List<CustomerSearchHit>>
Validations  : term shorter than 2 characters → empty list (REQ-PTY-041, AC-PTY-049) · caller without PERM_PTY_CUSTOMER_VIEW → empty list, not an error (REQ-PTY-043)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-500-INTERNAL
Orchestration : authorise softly (no VIEW → empty list) → length check → QR-PTY-011 (active Customers only; code, nameAr, nameEn, mobile, email, taxRegistrationNo contain the term without letter case; Arabic أ إ آ → ا, ة → ه, ى → ي on both sides — REQ-PTY-042; exact code matches first, then the display name in the request language ascending; LIMIT 10). Suppliers are never returned (ADR-PTY-013).
Repository   : QR-PTY-011 · NATIVE (ADR-PTY-027) · join PTY_PARTY · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_VIEW (checked in the service; absence yields an empty list — REQ-PTY-043, ADR-PTY-025)
Localization : names ar + en
<!-- API:API-PTY-027:END -->

<!-- API:API-PTY-028:START traces=REQ-PTY-018,DBF-PTY-002,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-025,DBF-PTY-026,DBF-PTY-028 -->
### API-PTY-028 — search Suppliers
Entity       : ENT-PTY-003, ENT-PTY-001 · operation VIEW · serves: search, list (SCR-REQ-PTY-002)
Honours      : CON-PTY-003
Endpoint     : /api/v1/pty/suppliers   verb: GET
Layers       : SupplierController.search → SupplierQueryService.search
Request      : query code (DBF-PTY-007, contains) · name (DBF-PTY-005 / DBF-PTY-006, contains, case-insensitive) · mobile (DBF-PTY-002, contains) · taxRegistrationNo (DBF-PTY-004, contains) · isActive (DBF-PTY-028, exact) · page, size · sort — one of code, nameAr, nameEn, mobile, taxRegistrationNo, isActiveFl, default code ascending
Response     : 200 · Page<`SupplierRow`> {supplierPk (DBF-PTY-025), code, nameAr, nameEn, mobile, taxRegistrationNo, paymentTermDays (DBF-PTY-026), isActiveFl (DBF-PTY-028)} · paginated · ApiResponse<Page<SupplierRow>>
Validations  : none
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-500-INTERNAL
Orchestration : QR-PTY-020 → map (AC-PTY-023: name "Delta" + isActive true returns exactly the two active matches).
Repository   : QR-PTY-020 · FIND_BY_CRITERIA · join PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-002 · PERM_PTY_SUPPLIER_VIEW
Localization : names ar + en
<!-- API:API-PTY-028:END -->

<!-- API:API-PTY-029:START traces=REQ-PTY-032,DBF-PTY-034,DBF-PTY-040,DBF-PTY-041,DBF-PTY-045 -->
### API-PTY-029 — list Party addresses
Entity       : ENT-PTY-004 · operation VIEW · serves: list (the Address section of SCR-REQ-PTY-001, SCR-REQ-PTY-002)
Honours      : CON-PTY-011, CON-PTY-004
Endpoint     : /api/v1/pty/parties/{partyId}/addresses   verb: GET
Layers       : AddressController.list → AddressService.listActive ; in-process PtyPartyApi.listPartyAddresses → AddressService.listActive
Request      : path partyId (DBF-PTY-041, Long, required)
Response     : 200 · list of `Address` (as API-PTY-012), active only, ordered by addressTypeCode, default first, then code · not paginated · ApiResponse<List<Address>>
Validations  : none
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-PARTY-NOT-FOUND, PTY-500-INTERNAL
Orchestration : QR-PTY-038 (Party absent → 404) → QR-PTY-027 (DBF-PTY-045 true only — REQ-PTY-032, AC-PTY-038).
Repository   : QR-PTY-038, QR-PTY-027 · FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · PERM_PTY_CUSTOMER_VIEW or PERM_PTY_SUPPLIER_VIEW (ADR-PTY-025)
Localization : address labels ar + en
<!-- API:API-PTY-029:END -->

<!-- API:API-PTY-030:START traces=REQ-PTY-069,REQ-PTY-070,REQ-PTY-073,REQ-PTY-077,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-013,DBF-PTY-014,DBF-PTY-015,DBF-PTY-016 -->
### API-PTY-030 — search Customer credit
Entity       : ENT-PTY-002, ENT-PTY-001 · operation VIEW · serves: search, list (SCR-REQ-PTY-003)
Honours      : CON-PTY-002
Endpoint     : /api/v1/pty/customer-credits   verb: GET
Layers       : CustomerCreditController.search → CustomerCreditService.search
Request      : query code (DBF-PTY-007, contains) · name (DBF-PTY-005 / DBF-PTY-006, contains) · customerGroupCode (DBF-PTY-014, exact) · carriesCredit (Boolean — DBF-PTY-015 or DBF-PTY-016 non-empty; `true` is the pre-filter of the switch-off next action, REQ-PTY-077) · page, size · sort — one of code, nameAr, nameEn, customerGroupCode, creditLimit, paymentTermDays, default code ascending
Response     : 200 · Page<`CustomerCreditRow`> {customerPk (DBF-PTY-013), code, nameAr, nameEn, customerGroupCode, creditLimit (DBF-PTY-015), paymentTermDays (DBF-PTY-016)} · paginated · ApiResponse<Page<CustomerCreditRow>>
Validations  : RULE-PTY-023 (`b2bCredit` off — the screen does not exist, REQ-PTY-070) → PTY-422-CREDIT-NOT-ENABLED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-422-CREDIT-NOT-ENABLED, PTY-500-INTERNAL
Orchestration : `CreditFlag.isOn()` → QR-PTY-013 (the walk-in Customer, which can never carry credit, is not listed — ADR-PTY-028; inactive Customers are listed so their credit can be cleared).
Repository   : QR-PTY-013 · FIND_BY_CRITERIA · join PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-003 · PERM_PTY_CUSTOMER_CREDIT_VIEW
Localization : names ar + en
<!-- API:API-PTY-030:END -->

<!-- API:API-PTY-031:START traces=REQ-PTY-108,DBF-PTY-013,DBF-PTY-015,DBF-PTY-016 -->
### API-PTY-031 — read credit history
Entity       : ENT-PTY-002 · operation VIEW · serves: read credit history (SCR-REQ-PTY-003)
Honours      : CON-PTY-002
Endpoint     : /api/v1/pty/customer-credits/{customerId}/history   verb: GET
Layers       : CustomerCreditController.history → CustomerCreditService.history
Request      : path customerId (DBF-PTY-013, Long, required) · query page, size (no sort parameter — newest first is the SRS order)
Response     : 200 · Page<`CreditHistoryEntry`> {field (creditLimit | paymentTermDays — DBF-PTY-015 / DBF-PTY-016), oldValue, newValue, userName, changedAt} newest first · paginated · ApiResponse<Page<CreditHistoryEntry>>
Validations  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-CREDIT-NOT-ENABLED, PTY-500-INTERNAL
Orchestration : `CreditFlag.isOn()` → QR-PTY-005 (404) → `AuditApi` reads the events of this Customer for the two fields, newest first, paged → `SecUserDirectoryApi` resolves user names (derived — ADR-PTY-028).
Repository   : QR-PTY-005 · FIND_ONE · join PTY_PARTY · READ_ONLY (history itself lives in the given audit module — no PTY table)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-003 · PERM_PTY_CUSTOMER_CREDIT_VIEW
Localization : field labels resolved by the client; messages ar + en
<!-- API:API-PTY-031:END -->

<!-- API:API-PTY-032:START traces=REQ-PTY-101,REQ-PTY-102,DBF-PTY-050,DBF-PTY-051,DBF-PTY-052,DBF-PTY-053,DBF-PTY-054,DBF-PTY-060,DBF-PTY-061,DBF-PTY-062,DBF-PTY-063,DBF-PTY-064 -->
### API-PTY-032 — search Credit override requests
Entity       : ENT-PTY-005, ENT-PTY-002 · operation VIEW · serves: search, list (SCR-REQ-PTY-004)
Honours      : CON-PTY-005
Endpoint     : /api/v1/pty/credit-overrides   verb: GET
Layers       : CreditOverrideController.search → CreditOverrideService.search
Request      : query docNo (DBF-PTY-061, exact) · statusCode (DBF-PTY-063, exact) · customerId (DBF-PTY-060, exact) · docDateFrom, docDateTo (DBF-PTY-062, date range, inclusive) · page, size · sort — optional, one of docNo, statusCode, docDate; default PENDING first then docDate descending (REQ-PTY-101)
Response     : 200 · Page<`CreditOverrideRow`> {creditOverrideRequestPk (DBF-PTY-050), docNo, docDate, statusCode, customerFk (DBF-PTY-060), customerCode, customerNameAr, customerNameEn, saleAmount (DBF-PTY-051), creditLimitAtRequest (DBF-PTY-052), openExposure (DBF-PTY-053), creditCheckResultCode (DBF-PTY-054), createdBy (DBF-PTY-064)} · paginated · ApiResponse<Page<CreditOverrideRow>>
Validations  : none — visibility: a caller without PERM_PTY_CREDIT_OVERRIDE_UPDATE sees only the requests it created (REQ-PTY-102)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-500-INTERNAL
Orchestration : decide the visibility filter from the caller's authorities → QR-PTY-035 → map.
Repository   : QR-PTY-035 · FIND_BY_CRITERIA · join PTY_CUSTOMER, PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_VIEW
Localization : Customer names ar + en
<!-- API:API-PTY-032:END -->
<!-- SUB:SVC-API-SEARCH:END -->

<!-- SUB:SVC-API-INT:START traces=REQ-PTY-001,REQ-PTY-044,REQ-PTY-045,REQ-PTY-046,REQ-PTY-050,REQ-PTY-051,REQ-PTY-052,REQ-PTY-053,REQ-PTY-054,REQ-PTY-055,REQ-PTY-056,REQ-PTY-057,REQ-PTY-058,REQ-PTY-059,REQ-PTY-060,REQ-PTY-061,REQ-PTY-062,REQ-PTY-063,REQ-PTY-068,REQ-PTY-074,REQ-PTY-075,REQ-PTY-076,REQ-PTY-077,REQ-PTY-079,REQ-PTY-080,REQ-PTY-081,REQ-PTY-082,REQ-PTY-083,REQ-PTY-084,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-097,REQ-PTY-099 -->
### SVC-API-INT — operations offered to consuming modules (SRS A8.1)

These endpoints are also exposed in-process through `PtyPartyApi` (PHASE 1 — CORE); the REST path and the in-process method share one service method. They describe what PTY offers; who calls them is the callers' concern (inbound edges, derived by `gov.py graph`).

<!-- API:API-PTY-033:START traces=REQ-PTY-044,REQ-PTY-045,REQ-PTY-046,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-008,DBF-PTY-013,DBF-PTY-014,DBF-PTY-018,DBF-PTY-019,DBF-PTY-020 -->
### API-PTY-033 — default Customer for a POS sale
Entity       : ENT-PTY-002 · operation VIEW · serves: the walk-in Customer for an unnamed sale (no screen of its own)
Honours      : CON-PTY-013, CON-PTY-002
Endpoint     : /api/v1/pty/customers/walk-in   verb: GET
Layers       : CustomerController.walkIn → WalkInCustomerService.ensure ; in-process PtyPartyApi.defaultPosCustomer → WalkInCustomerService.ensure ; `PtyTenantProvisioningContributor` (implements the given `TenantProvisioningContributor`) → WalkInCustomerService.ensure (REQ-PTY-044)
Request      : none
Response     : 200 · `Customer` (as API-PTY-002, isWalkInFl true) · ApiResponse<Customer>
Validations  : none
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-500-INTERNAL
Orchestration : QR-PTY-012 → found: return → none (REQ-PTY-046): in a new transaction (REQUIRES_NEW) allocate DBF-PTY-007 (code) from `NumberSeriesApi`, insert Party QR-PTY-002 with DBF-PTY-005 (nameAr) «عميل نقدي», DBF-PTY-006 (nameEn) "Walk-in customer", DBF-PTY-008 (isActiveFl) true, then Customer QR-PTY-008 with DBF-PTY-019 (partyFk), DBF-PTY-014 (customerGroupCode) RETAIL, DBF-PTY-018 (isWalkInFl) true, DBF-PTY-020 (isActiveFl) true (REQ-PTY-044, SRS D-08) → re-read QR-PTY-012 and return.
Repository   : QR-PTY-012, QR-PTY-002, QR-PTY-008 · FIND_ONE then SAVE · join PTY_PARTY · READ_ONLY outer, REQUIRES_NEW for the creation (ADR-PTY-026)
Concurrency  : Two first calls at once both find no walk-in Customer and both try to create it: UQ_PTY_CUSTOMER_IS_WALK_IN_FL (RULE-PTY-014) admits one; the loser's REQUIRES_NEW transaction rolls back (its Party row with it) and it re-reads QR-PTY-012 — both callers return the same identifier (AC-PTY-054).
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_VIEW (ADR-PTY-025)
Localization : the walk-in names are seeded in ar + en
<!-- API:API-PTY-033:END -->

<!-- API:API-PTY-034:START traces=REQ-PTY-001,REQ-PTY-050,REQ-PTY-051,REQ-PTY-053,REQ-PTY-055,REQ-PTY-056,REQ-PTY-057,REQ-PTY-058,REQ-PTY-059,REQ-PTY-060,REQ-PTY-061,REQ-PTY-062,REQ-PTY-063,DBF-PTY-002,DBF-PTY-003,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-008,DBF-PTY-013,DBF-PTY-014,DBF-PTY-017,DBF-PTY-018,DBF-PTY-019,DBF-PTY-020,DBF-PTY-034,DBF-PTY-035,DBF-PTY-036,DBF-PTY-037,DBF-PTY-038,DBF-PTY-039,DBF-PTY-040,DBF-PTY-041,DBF-PTY-044,DBF-PTY-045 -->
### API-PTY-034 — guest order customer resolution
Entity       : ENT-PTY-002, ENT-PTY-001, ENT-PTY-004 · operation CREATE · serves: guest checkout (no screen of its own)
Honours      : CON-PTY-014, CON-PTY-002, CON-PTY-007
Endpoint     : /api/v1/pty/guest-customers   verb: POST
Layers       : GuestCustomerController.resolve → GuestCustomerService.resolve ; in-process PtyPartyApi.resolveGuestCustomer → GuestCustomerService.resolve
Request      : body `GuestCustomerRequest` — nameAr (DBF-PTY-005, String ≤ 200) · nameEn (DBF-PTY-006, String ≤ 200) — one required (RULE-PTY-001) · email (DBF-PTY-003, String ≤ 254) · mobile (DBF-PTY-002, String ≤ 20) — one required (RULE-PTY-019) · deliveryAddress {line1 (DBF-PTY-035, String ≤ 200, required — RULE-PTY-015), line2 (DBF-PTY-036), city (DBF-PTY-037), region (DBF-PTY-038), postalCode (DBF-PTY-039)}
Response     : 200 · `GuestCustomerResult` {customerId (DBF-PTY-013), created (Boolean)} · ApiResponse<GuestCustomerResult>
Validations  : RULE-PTY-019 → PTY-422-GUEST-CONTACT-REQUIRED · RULE-PTY-001 → PTY-422-NAME-REQUIRED · RULE-PTY-003 → PTY-422-EMAIL-FORMAT · RULE-PTY-004 → PTY-422-MOBILE-FORMAT · RULE-PTY-015 → PTY-422-ADDRESS-LINE-REQUIRED · RULE-PTY-021 → PTY-422-GUEST-NOT-COMPLETED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-422-GUEST-CONTACT-REQUIRED, PTY-422-NAME-REQUIRED, PTY-422-EMAIL-FORMAT, PTY-422-MOBILE-FORMAT, PTY-422-ADDRESS-LINE-REQUIRED, PTY-422-GUEST-NOT-COMPLETED, PTY-500-INTERNAL
Orchestration : validate RULE-PTY-019, 001, 003, 004, 015 → normalise: email `lower(trim)`, mobile = remove spaces and dashes, leading "00" → "+" (ADR-PTY-015) → match (RULE-PTY-013 — the walk-in Customer's Party and inactive Parties are never candidates): QR-PTY-036 by email (REQ-PTY-055); none → QR-PTY-037 by normalised mobile — exactly one → matched (REQ-PTY-056), more than one → create (REQ-PTY-061), none → create.
  Matched: lock the Party QR-PTY-001 → QR-PTY-007 its Customer → none: insert a Customer QR-PTY-008 with DBF-PTY-019 (partyFk), DBF-PTY-014 RETAIL, DBF-PTY-018 false, DBF-PTY-020 true (REQ-PTY-057) → inactive Customer → RULE-PTY-021 (the message reveals nothing — REQ-PTY-062) → the Party's names, contacts and Addresses are left unchanged (REQ-PTY-058) → after commit, when the Customer's DBF-PTY-017 (secUserId) is empty: `SecCustomerInvitationApi` invitation to the email **recorded on the Party**, else its recorded mobile (REQ-PTY-059); when set: no invitation (REQ-PTY-060).
  Create (REQ-PTY-050): DBF-PTY-007 (code) from `NumberSeriesApi` → Party QR-PTY-002 (names, email, mobile, DBF-PTY-008 true) → Customer QR-PTY-008 (RETAIL, DBF-PTY-018 false, DBF-PTY-020 true) → Address QR-PTY-024 / QR-PTY-025 with DBF-PTY-034 (addressTypeCode) DELIVERY, DBF-PTY-044 (code) "1", DBF-PTY-040 (isDefaultFl) true, DBF-PTY-041 (partyFk), DBF-PTY-045 (isActiveFl) true and the delivery lines → after commit: invitation to the email, else the mobile (REQ-PTY-051); DBF-PTY-017 stays empty until acceptance (AC-PTY-059).
Repository   : QR-PTY-036, QR-PTY-037, QR-PTY-001, QR-PTY-007, QR-PTY-002, QR-PTY-008, QR-PTY-024, QR-PTY-025 · FIND_BY_CRITERIA, NATIVE, SAVE · join NONE · READ_WRITE
Concurrency  : Two orders of one new guest at once both match nothing and both create a Party with the same email: UQ_PTY_PARTY_EMAIL rejects the second insert; the creation runs in a REQUIRES_NEW transaction and the service retries the matching once, which now attaches the order to the first Customer (ADR-PTY-026). Mobile is not unique (SRS D-03), so two mobile-only guests created at once may produce two Parties — the same outcome REQ-PTY-061 prescribes for an ambiguous mobile.
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_CREATE for a REST caller; the in-process call is authorised by the calling module for its own anonymous shopper (ADR-PTY-025)
Localization : messages ar + en
<!-- API:API-PTY-034:END -->

<!-- API:API-PTY-035:START traces=REQ-PTY-068,REQ-PTY-074,REQ-PTY-079,REQ-PTY-080,REQ-PTY-081,REQ-PTY-082,REQ-PTY-083,REQ-PTY-084,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,DBF-PTY-013,DBF-PTY-015,DBF-PTY-018 -->
### API-PTY-035 — credit check
Entity       : ENT-PTY-002 · operation VIEW · serves: the credit check of a sale (no screen of its own)
Honours      : CON-PTY-015, CON-PTY-022, CON-PTY-008
Endpoint     : /api/v1/pty/credit-checks   verb: POST
Layers       : CreditCheckController.check → CreditCheckService.check ; in-process PtyPartyApi.checkCredit → CreditCheckService.check
Request      : body `CreditCheckRequest` — customerId (DBF-PTY-013, Long, required) · saleAmount (BigDecimal scale 3, required, > 0 — typed as DBF-PTY-051; ≤ 0 → PTY-400-VALIDATION)
Response     : 200 · `CreditCheckResult` {customerId, resultCode (CREDIT_CHECK_RESULT code), creditLimit (DBF-PTY-015), openExposure, projectedExposure, overrunActionCode (CREDIT_OVERRUN_ACTION code — present for OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE), approvalRequired (Boolean)} · ApiResponse<CreditCheckResult>
Validations  : none raise a rule error — every outcome is a result code
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-500-INTERNAL
Orchestration : `CreditFlag.isOn()` off → NOT_APPLICABLE, approvalRequired false (REQ-PTY-074) → load QR-PTY-005 (404) → openExposure = sum of every registered `CustomerCreditExposureContributor` (0 when none — REQ-PTY-080), each invoked under a 3-second budget; any contributor not answering in time or failing → exposure unavailable (REQ-PTY-084) → overrun action from `SettingsApi` PTY_CREDIT_OVERRUN_ACTION, default BLOCK_WITH_OVERRIDE (REQ-PTY-087) → result: DBF-PTY-015 empty → NO_CREDIT (REQ-PTY-068, REQ-PTY-083; the walk-in Customer, DBF-PTY-018 true, always lands here) · exposure unavailable → EXPOSURE_UNAVAILABLE with openExposure empty · projectedExposure = openExposure + saleAmount ≤ creditLimit → WITHIN_LIMIT (REQ-PTY-081) · otherwise OVER_LIMIT (REQ-PTY-082) → OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE carry the overrun action and approvalRequired = (action = BLOCK_WITH_OVERRIDE) (REQ-PTY-088, REQ-PTY-089). Contributors are in-process beans: the budget is a time limit on a local call, not a network timeout (ADR-PTY-028).
Repository   : QR-PTY-005 · FIND_ONE · join PTY_PARTY · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes (the answer is advisory; the approval it may lead to is decided under its own lock, API-PTY-021)
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_VIEW (the seller performing a sale holds it — ADR-PTY-025)
Localization : result and action codes; labels from mdl
<!-- API:API-PTY-035:END -->

<!-- API:API-PTY-036:START traces=REQ-PTY-097,REQ-PTY-099,DBF-PTY-050,DBF-PTY-051,DBF-PTY-059,DBF-PTY-060,DBF-PTY-061,DBF-PTY-063 -->
### API-PTY-036 — use Credit override request
Entity       : ENT-PTY-005 · operation UPDATE · serves: use of an approval by a completed sale (no screen of its own)
Honours      : CON-PTY-018, CON-PTY-005
Endpoint     : /api/v1/pty/credit-overrides/{id}/use   verb: PATCH
Layers       : CreditOverrideController.use → CreditOverrideService.use ; in-process PtyPartyApi.useCreditOverrideRequest → CreditOverrideService.use
Request      : path id (DBF-PTY-050, Long, required) · body `CreditOverrideUseRequest` — customerId (compared with DBF-PTY-060, Long, required) · saleAmount (compared with DBF-PTY-051, BigDecimal, required) · usedDocumentRef (DBF-PTY-059, String ≤ 60, required) · docNo (DBF-PTY-061, optional — compared only, RULE-PTY-031)
Response     : 200 · `CreditOverrideRequest` (statusCode USED) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-031 → PTY-422-DOC-NO-READ-ONLY · RULE-PTY-028 (not APPROVED, another Customer, or the approved amount is smaller than the sale) → PTY-422-APPROVAL-NOT-COVERING ({docNo})
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-422-DOC-NO-READ-ONLY, PTY-422-APPROVAL-NOT-COVERING, PTY-500-INTERNAL
Orchestration : lock QR-PTY-033 (404) → RULE-PTY-031 → RULE-PTY-028 → DBF-PTY-063 (statusCode) USED, DBF-PTY-059 (usedDocumentRef) → conditional write QR-PTY-034 (expected APPROVED).
Repository   : QR-PTY-033, QR-PTY-034 · UPDATE · join NONE · READ_WRITE
Concurrency  : One approval covers one sale: two sales using the same approval at once are serialised by the row lock, and QR-PTY-034 updates only `WHERE STATUS_CODE = 'APPROVED'` — the second finds USED and receives PTY-422-APPROVAL-NOT-COVERING (AC-PTY-113).
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_CREATE (the requester's action, held by whoever completes the credit sale — ADR-PTY-025)
Localization : messages ar + en
<!-- API:API-PTY-036:END -->

<!-- API:API-PTY-037:START traces=REQ-PTY-075,REQ-PTY-076,REQ-PTY-077,DBF-PTY-015,DBF-PTY-016 -->
### API-PTY-037 — b2bCredit switch-off dependency
Entity       : ENT-PTY-002 · operation VIEW · serves: the capability-flag owner's switch-off question (no screen of its own)
Honours      : CON-PTY-019
Endpoint     : /api/v1/pty/flag-dependencies/b2bCredit   verb: GET
Layers       : FlagDependencyController.b2bCredit → CreditFlagDependencyService.count ; in-process PtyPartyApi.b2bCreditSwitchOffDependency → CreditFlagDependencyService.count
Request      : none
Response     : 200 · `FlagDependency` {flag "b2bCredit", dependentCustomerCount (Integer), switchOffAllowed (Boolean = count is 0), messageAr, messageEn (RULE-PTY-024 with {count} filled — only when the count is above 0), nextActionCode "SHOW_CUSTOMERS_WITH_CREDIT", nextActionLabelAr «عرض العملاء ذوي الائتمان», nextActionLabelEn "Show customers with credit" (REQ-PTY-077; opens API-PTY-030 with carriesCredit = true)} · ApiResponse<FlagDependency>
Validations  : none — the refusal is the flag owner's, carried in the answer
Messages     : RULE-PTY-024 — ar: لا يمكن إيقاف الائتمان: لدى {count} من العملاء حد ائتمان أو مدة سداد. امسحها من شاشة ائتمان العملاء ثم أعد المحاولة. · en: Credit cannot be turned off: {count} customers still have a credit limit or payment terms. Clear them on the Customer credit screen, then try again.
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-500-INTERNAL
Orchestration : QR-PTY-014 counts Customers with DBF-PTY-015 or DBF-PTY-016 non-empty (REQ-PTY-075) → fill the answer.
Repository   : QR-PTY-014 · COUNT · join NONE · READ_ONLY
Concurrency  : NONE — the count is advisory; a credit saved between this answer and the owner's switch-off is refused afterwards by RULE-PTY-023
Security     : SCR-REQ-PTY-005 · PERM_PTY_CREDIT_SETTINGS_VIEW (ADR-PTY-025)
Localization : message and action label in ar + en
<!-- API:API-PTY-037:END -->

**In-process handler — CUSTOMER-realm invitation accepted (no endpoint; REQ-PTY-052, REQ-PTY-054).** `CustomerInvitationAcceptanceHandler` receives the acceptance that the given `SecCustomerInvitationApi` reports (customerId, accepting user identifier). It runs in one READ_WRITE transaction: lock the Customer with QR-PTY-006 → the walk-in Customer (DBF-PTY-018 true) → outcome REFUSED with RULE-PTY-013's message, Customer left unlinked → DBF-PTY-017 (secUserId) already set → unchanged → set DBF-PTY-017 to the accepting user's identifier through QR-PTY-009; UQ_PTY_CUSTOMER_SEC_USER_ID rejecting the write (the user already linked to another Customer) → outcome REFUSED with RULE-PTY-020's message, Customer left unlinked (AC-PTY-062). The outcome and the rule message (ar + en, as stated in DATA-DOM) are returned to the given's callback; no HTTP status and no Error Catalog row exists for it (ADR-PTY-025). The handler reuses QR-PTY-006 and QR-PTY-009 (cited by API-PTY-003 and API-PTY-017).
<!-- SUB:SVC-API-INT:END -->
<!-- PHASE:SVC-API:END -->

<!-- PHASE:DOC:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-019,REQ-PTY-032,REQ-PTY-079,REQ-PTY-091,REQ-PTY-103 -->
## PHASE 4 — DOC

- **API document.** `api-spec-pty.yaml` (OpenAPI 3.1.0) is derived from the 37 `API-*` blocks and the Error Catalog of this plan: one operation per block (`x-api-id`, `x-traces`, `operationId` = the block heading), request and response schemas typed from the db-script through the type mapping of CORE, one error response per catalog row the block raises (`x-error-codes`), `x-paginated: true` with `page` / `size` on the five paginated searches, and the bearer-JWT scheme on every operation. The frontend stage binds to it and serves it from a mock; `api-verify` holds the delivered api-docs to it.
- **Delivered api-docs.** Each controller method carries springdoc annotations (`@Operation(operationId, summary)`, `@ApiResponse` per status of its `Errors` line, `@Tag(name = "pty-<aggregate>")`) so the published `/v3/api-docs` matches the document operation for operation; the `x-error-codes` extension is emitted by an `OperationCustomizer` reading a `@PtyErrors({...})` annotation that lists the block's codes.
- **Message bundles.** Every catalog code has `messages_ar.properties` / `messages_en.properties` keys `pty.error.<CODE>` holding the catalog text verbatim; confirmations (RULE-PTY-006, RULE-PTY-017), the switch-off answer (RULE-PTY-024), the invitation-handler outcomes (RULE-PTY-013, RULE-PTY-020) and the approver notification (REQ-PTY-093) use keys `pty.message.<RULE-id>`.
- **Module README** (`backend/modules/PTY/README.md` in the delivery partition): the published in-process surface (`PtyPartyApi`, `CustomerCreditExposureContributor`, `PartyRoleDeactivatedEvent`), the CON-PTY item each method honours, the flag `b2bCredit` behaviour, and the bootstrap rows of SEC-BE.
- **Javadoc** on `PtyPartyApi` and `CustomerCreditExposureContributor` states, per method, the contract item, the errors (catalog codes) and the 3-second budget of a contributor (REQ-PTY-084).
<!-- PHASE:DOC:END -->

<!-- PHASE:SEC-BE:START traces=REQ-PTY-043,REQ-PTY-090,REQ-PTY-094,REQ-PTY-096,REQ-PTY-102 -->
## PHASE 5 — SEC-BE

Security model (profile): permission names `PERM_<PAGE_CODE>_<ACTION>`, actions VIEW / CREATE / UPDATE / DELETE, VIEW the gateway (without it no other permission of the page applies). Every API serving a screen verifies its permission before processing (`@PreAuthorize("hasAuthority('PERM_…')")` on the controller method); a refusal maps through `LocalizedException → {code, messageAr, messageEn}` to PTY-403-FORBIDDEN (unauthenticated → PTY-401-UNAUTHENTICATED). No service checks a role name. Permission names are minted only from the SRS access matrix (B4 of each screen).

**SCR-REQ-PTY-001 Customers — page PTY_CUSTOMER.** VIEW: API-PTY-002, 012, 026, 027 (soft), 029, 033, 035 · CREATE: API-PTY-001, 034 · UPDATE: API-PTY-003, 011, 013, 014, 015 (Address actions, by role held) · DELETE: API-PTY-004, 005. Grants (SRS B4): Owner VIEW, CREATE, UPDATE, DELETE · Seller/Cashier VIEW, CREATE, UPDATE · Warehouse none.
**SCR-REQ-PTY-002 Suppliers — page PTY_SUPPLIER.** VIEW: API-PTY-007, 012, 028, 029 · CREATE: API-PTY-006 · UPDATE: API-PTY-008, 011, 013, 014, 015 · DELETE: API-PTY-009, 010. Grants: Owner all four · Seller/Cashier none · Warehouse VIEW.
**SCR-REQ-PTY-003 Customer credit — page PTY_CUSTOMER_CREDIT** (flag `b2bCredit`). VIEW: API-PTY-016, 030, 031 · UPDATE: API-PTY-017, 018. Grants: Owner VIEW, UPDATE · Seller/Cashier VIEW · Warehouse none.
**SCR-REQ-PTY-004 Credit override requests — page PTY_CREDIT_OVERRIDE** (flag `b2bCredit`). VIEW: API-PTY-020, 032 · CREATE: API-PTY-019, 036 · UPDATE: API-PTY-021, 022 (the credit-override permission of POL-PTY-008) · DELETE: API-PTY-023 (cancel own — RULE-PTY-029). Grants: Owner all four · Seller/Cashier VIEW, CREATE, DELETE · Warehouse none. Visibility: without UPDATE a user sees only its own requests (REQ-PTY-102, API-PTY-020, 032).
**SCR-REQ-PTY-005 Credit control settings — page PTY_CREDIT_SETTINGS** (flag `b2bCredit`). VIEW: API-PTY-024, 037 · UPDATE: API-PTY-025 (REQ-PTY-090). Grants: Owner VIEW, UPDATE · others none.

**Seed data.** One `SEC_PAGES` row per composite screen, declared through PTY's `PermissionContributor` (given row `modules.sec`): page code, name ar / en, parent "Party / الأطراف" menu — PTY_CUSTOMER (العملاء / Customers), PTY_SUPPLIER (الموردون / Suppliers), PTY_CUSTOMER_CREDIT (ائتمان العملاء / Customer credit), PTY_CREDIT_OVERRIDE (طلبات تجاوز حد الائتمان / Credit override requests), PTY_CREDIT_SETTINGS (إعدادات ضبط الائتمان / Credit control settings); the given derives one permission per action; column names are the given's, not PTY's. The contributor also declares the default grants above to the three preset roles (ADR-PTY-029) — registering a page is not a grant. The three credit pages are hidden from menus while `b2bCredit` is off (REQ-PTY-070) through the page's flag binding on the contributor; their endpoints refuse with PTY-422-CREDIT-NOT-ENABLED where RULE-PTY-023 applies.

**In-process callers.** A consuming module calling `PtyPartyApi` runs under its own caller's security context; it authorises its caller against its own permissions before calling PTY (ADR-PTY-025). Tenancy is resolved by the given for every call.

```yaml name=permission-matrix
rows:
  - screen: SCR-REQ-PTY-001 Customers
    entity: ENT-PTY-002
    api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-026, API-PTY-027, API-PTY-029, API-PTY-033, API-PTY-034, API-PTY-035]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_CUSTOMER_VIEW}
      CREATE: {granted: true, permission: PERM_PTY_CUSTOMER_CREATE}
      UPDATE: {granted: true, permission: PERM_PTY_CUSTOMER_UPDATE}
      DELETE: {granted: true, permission: PERM_PTY_CUSTOMER_DELETE}
  - screen: SCR-REQ-PTY-002 Suppliers
    entity: ENT-PTY-003
    api: [API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-028, API-PTY-029]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_SUPPLIER_VIEW}
      CREATE: {granted: true, permission: PERM_PTY_SUPPLIER_CREATE}
      UPDATE: {granted: true, permission: PERM_PTY_SUPPLIER_UPDATE}
      DELETE: {granted: true, permission: PERM_PTY_SUPPLIER_DELETE}
  - screen: SCR-REQ-PTY-003 Customer credit
    entity: ENT-PTY-002
    api: [API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-030, API-PTY-031]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_CUSTOMER_CREDIT_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: true, permission: PERM_PTY_CUSTOMER_CREDIT_UPDATE}
      DELETE: {granted: false}
  - screen: SCR-REQ-PTY-004 Credit override requests
    entity: ENT-PTY-005
    api: [API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-032, API-PTY-036]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_CREDIT_OVERRIDE_VIEW}
      CREATE: {granted: true, permission: PERM_PTY_CREDIT_OVERRIDE_CREATE}
      UPDATE: {granted: true, permission: PERM_PTY_CREDIT_OVERRIDE_UPDATE}
      DELETE: {granted: true, permission: PERM_PTY_CREDIT_OVERRIDE_DELETE}
  - screen: SCR-REQ-PTY-005 Credit control settings
    entity: ENT-PTY-002
    api: [API-PTY-024, API-PTY-025, API-PTY-037]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_CREDIT_SETTINGS_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: true, permission: PERM_PTY_CREDIT_SETTINGS_UPDATE}
      DELETE: {granted: false}
```

**Bootstrap data.** The rows that must exist before an endpoint of PTY can succeed, each with who produces it:

```yaml name=bootstrap
rows:
  - {kind: lookup key, name: "CUSTOMER_GROUP", source: "PTY Flyway migration — db-script §4 BLOCK 8 (idempotent) into the given mdl lookup tables; values RETAIL, WHOLESALE", note: "read by API-PTY-001, 003 (RULE-PTY-018); new tenants receive it through the given's provisioning copy of mdl seeds"}
  - {kind: lookup key, name: "ADDRESS_TYPE", source: "PTY Flyway migration — db-script §4 BLOCK 8; values BILLING, DELIVERY", note: "read by API-PTY-011, 013 (RULE-PTY-016), API-PTY-034 (DELIVERY)"}
  - {kind: lookup key, name: "CREDIT_OVERRUN_ACTION", source: "PTY Flyway migration — db-script §4 BLOCK 8; values WARN, BLOCK_WITH_OVERRIDE", note: "read by API-PTY-025"}
  - {kind: lookup key, name: "CREDIT_CHECK_RESULT", source: "PTY Flyway migration — db-script §4 BLOCK 8; values WITHIN_LIMIT, OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE, NOT_APPLICABLE", note: "labels for API-PTY-035 results"}
  - {kind: lookup key, name: "CREDIT_OVERRIDE_STATUS", source: "PTY Flyway migration — db-script §4 BLOCK 8; values PENDING, APPROVED, REJECTED, CANCELLED, USED", note: "labels for RULE-PTY-026 {statusAr}/{statusEn}"}
  - {kind: page, name: "PTY_CUSTOMER", source: "PTY PermissionContributor (com.erp.sec.permission.PermissionContributor) — the given sec writes SEC_PAGES at startup and for every provisioned tenant"}
  - {kind: page, name: "PTY_SUPPLIER", source: "PTY PermissionContributor — given sec SEC_PAGES"}
  - {kind: page, name: "PTY_CUSTOMER_CREDIT", source: "PTY PermissionContributor — given sec SEC_PAGES", note: "menu bound to flag b2bCredit"}
  - {kind: page, name: "PTY_CREDIT_OVERRIDE", source: "PTY PermissionContributor — given sec SEC_PAGES", note: "menu bound to flag b2bCredit"}
  - {kind: page, name: "PTY_CREDIT_SETTINGS", source: "PTY PermissionContributor — given sec SEC_PAGES", note: "menu bound to flag b2bCredit"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_VIEW", source: "derived by the given sec from page PTY_CUSTOMER; default grant declared by PTY PermissionContributor (ADR-PTY-029)", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_CREATE", source: "given sec from PTY_CUSTOMER; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_UPDATE", source: "given sec from PTY_CUSTOMER; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_DELETE", source: "given sec from PTY_CUSTOMER; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_SUPPLIER_VIEW", source: "given sec from PTY_SUPPLIER; grant by PTY PermissionContributor", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_PTY_SUPPLIER_CREATE", source: "given sec from PTY_SUPPLIER; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_SUPPLIER_UPDATE", source: "given sec from PTY_SUPPLIER; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_SUPPLIER_DELETE", source: "given sec from PTY_SUPPLIER; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_CREDIT_VIEW", source: "given sec from PTY_CUSTOMER_CREDIT; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_CREDIT_UPDATE", source: "given sec from PTY_CUSTOMER_CREDIT; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_CREDIT_OVERRIDE_VIEW", source: "given sec from PTY_CREDIT_OVERRIDE; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CREDIT_OVERRIDE_CREATE", source: "given sec from PTY_CREDIT_OVERRIDE; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CREDIT_OVERRIDE_UPDATE", source: "given sec from PTY_CREDIT_OVERRIDE; grant by PTY PermissionContributor", note: "role: Owner — the approvers notified by API-PTY-019"}
  - {kind: permission, name: "PERM_PTY_CREDIT_OVERRIDE_DELETE", source: "given sec from PTY_CREDIT_OVERRIDE; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier (own requests only, RULE-PTY-029)"}
  - {kind: permission, name: "PERM_PTY_CREDIT_SETTINGS_VIEW", source: "given sec from PTY_CREDIT_SETTINGS; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_CREDIT_SETTINGS_UPDATE", source: "given sec from PTY_CREDIT_SETTINGS; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: number series, name: "PTY_PARTY", source: "PTY TenantProvisioningContributor registers it through NumberSeriesApi for every provisioned tenant (ADR-PTY-029)", note: "format P- + 6 digits; DBF-PTY-007"}
  - {kind: number series, name: "PTY_CREDIT_OVERRIDE", source: "PTY TenantProvisioningContributor registers it through NumberSeriesApi (ADR-PTY-029)", note: "format CO- + 6 digits; DBF-PTY-061"}
  - {kind: data row, name: "walk-in Customer", source: "PTY TenantProvisioningContributor → WalkInCustomerService.ensure at provisioning (REQ-PTY-044); on first demand by API-PTY-033 for tenants provisioned earlier (REQ-PTY-046)"}
  - {kind: setting, name: "PTY_CREDIT_OVERRUN_ACTION", source: "not seeded — absent until the Owner saves it through API-PTY-025; BLOCK_WITH_OVERRIDE applies meanwhile (REQ-PTY-087)"}
```

The frontend stage references these permission names; it does not redeclare them.
<!-- PHASE:SEC-BE:END -->

<!-- PHASE:ALIGN-BE:START traces=REQ-PTY-001,REQ-PTY-024,REQ-PTY-109 -->
## PHASE 6 — ALIGN-BE

The prose half of the alignment check. Each mark is the `gov.py analyze` result for the named check, copied by the orchestrator; the author does not adjudicate a row the machine decides.

```
ALIGN — PTY v1
row               backing check        assertion                                                                                     mark
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF        (from the analyze report)
COVERED           orphans              every REQ is covered by ≥1 API or DBF                                                          (from the analyze report)
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares for it                (from the analyze report)
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads                                         (from the analyze report)
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not                    (from the analyze report)
QRC (§5)          orphans              every catalogued query is reached by ≥1 API                                                   (from the analyze report)
API (R3)          code-format          every catalog code is an instance of the declared format and carries a status the platform can emit (from the analyze report)
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-pty.yaml and every operation one block           (from the analyze report)
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation of api-spec-pty.yaml with its status and code   (from the analyze report)
DOCUMENT VALID    api-spec-valid       api-spec-pty.yaml validates against OPENAPI 3.1.0 and reaches every required item             (from the analyze report)
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from, or is deferred        (from the analyze report)
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered               (from the analyze report)
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase, and nothing else names its target         (from the analyze report)
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's own registry               (from the analyze report)
BOOTSTRAP         bootstrap-complete   every lookup key and every permission has a row naming who produces it                        (from the analyze report)
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission (from the analyze report)
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why it is not      (from the analyze report)
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/PTY/                           (from the analyze report)
PATHS             paths-resolve        every path the generated manifest and execution state emit resolves to something that exists  (from the analyze report)
COVERAGE          (the report)         the clauses the analyze report lists as having examined nothing — verbatim, or `none`         (from the analyze report)
```

```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C7.24
- C7.26
- C7.27
- C7.28
- C7.5
- C7.5b
```

**Coverage — ENT / DBF → phases → QR → XM**

| ENT | DBF | Phases | QR | XM |
|---|---|---|---|---|
| ENT-PTY-001 | DBF-PTY-001 … 012 | DATA-DOM-MASTER, SVC-API-CRUD / SEARCH / INT | QR-PTY-001, 002, 003, 004, 021, 036, 037, 038 | — |
| ENT-PTY-002 | DBF-PTY-013 … 024 | DATA-DOM-MASTER, SVC-API-CRUD / SEARCH / INT | QR-PTY-005 … 014 | — |
| ENT-PTY-003 | DBF-PTY-025 … 032 | DATA-DOM-MASTER, SVC-API-CRUD / SEARCH | QR-PTY-015 … 020 | — |
| ENT-PTY-004 | DBF-PTY-033 … 049 | DATA-DOM-MASTER, SVC-API-CRUD / SEARCH / INT | QR-PTY-022 … 030 | — |
| ENT-PTY-005 | DBF-PTY-050 … 067 | DATA-DOM-TRANSACTIONAL, SVC-API-CRUD / SEARCH / INT | QR-PTY-031 … 035 | — |

**Coverage — RULE → API → catalog code**

| RULE | API | Code |
|---|---|---|
| RULE-PTY-001 | 001, 003, 006, 008, 034 | PTY-422-NAME-REQUIRED |
| RULE-PTY-002 | 001, 003, 005, 006, 008, 010 | PTY-409-EMAIL-TAKEN |
| RULE-PTY-003 | 001, 003, 006, 008, 034 | PTY-422-EMAIL-FORMAT |
| RULE-PTY-004 | 001, 003, 006, 008, 034 | PTY-422-MOBILE-FORMAT |
| RULE-PTY-005 | 002, 007 | PTY-422-ROLE-INACTIVE |
| RULE-PTY-006 | 004, 009, 014 | confirmation message (no error) |
| RULE-PTY-007 | 003, 008 | PTY-422-CODE-READ-ONLY |
| RULE-PTY-008 | 006, 008, 017 | PTY-422-PAYMENT-TERMS-RANGE |
| RULE-PTY-009 | 001, 006 | PTY-409-ROLE-HELD |
| RULE-PTY-010 | 004 | PTY-422-WALK-IN-DEACTIVATE |
| RULE-PTY-011 | 017 | PTY-422-WALK-IN-CREDIT |
| RULE-PTY-012 | 006 | PTY-422-WALK-IN-SUPPLIER |
| RULE-PTY-013 | 034 (matching filter), invitation handler | in-process outcome (no error code — ADR-PTY-025) |
| RULE-PTY-014 | 033 | none — idempotent ensure, UQ_PTY_CUSTOMER_IS_WALK_IN_FL |
| RULE-PTY-015 | 011, 013, 034 | PTY-422-ADDRESS-LINE-REQUIRED |
| RULE-PTY-016 | 011, 013 | PTY-422-ADDRESS-TYPE-INVALID |
| RULE-PTY-017 | 011, 013, 014, 015 | confirmation message (no error) |
| RULE-PTY-018 | 001, 003 | PTY-422-CUSTOMER-GROUP-INVALID |
| RULE-PTY-019 | 034 | PTY-422-GUEST-CONTACT-REQUIRED |
| RULE-PTY-020 | invitation handler | in-process outcome (no error code — ADR-PTY-025) |
| RULE-PTY-021 | 034 | PTY-422-GUEST-NOT-COMPLETED |
| RULE-PTY-022 | 017 | PTY-422-CREDIT-LIMIT-NEGATIVE |
| RULE-PTY-023 | 016, 017, 018, 019, 030, 031 | PTY-422-CREDIT-NOT-ENABLED |
| RULE-PTY-024 | 037 | answer message (no error) |
| RULE-PTY-025 | 019 | PTY-422-OVERRIDE-NOT-NEEDED |
| RULE-PTY-026 | 021, 022, 023 | PTY-409-REQUEST-NOT-PENDING |
| RULE-PTY-027 | 022 | PTY-422-REJECTION-REASON-REQUIRED |
| RULE-PTY-028 | 036 | PTY-422-APPROVAL-NOT-COVERING |
| RULE-PTY-029 | 023 | PTY-403-NOT-REQUESTER |
| RULE-PTY-030 | 017, 018 | PTY-500-AUDIT-NOT-RECORDED |
| RULE-PTY-031 | 021, 022, 036 | PTY-422-DOC-NO-READ-ONLY |
| RULE-PTY-032 | 019 | PTY-422-SALE-AMOUNT-NOT-POSITIVE |
| RULE-PTY-033 | 019 | PTY-422-WALK-IN-NO-CREDIT-SALE |

**Coverage — XM → requires → tests**: none (0 edges).
<!-- PHASE:ALIGN-BE:END -->

<!-- PHASE:INT-C:START traces=REQ-PTY-023,REQ-PTY-080,REQ-PTY-084 -->
## PHASE 7 — INT-C

The one place this plan speaks about another module. **PTY v1 has no outbound cross-module edge**: the db-script's XM register is `records: []` (SRS A8 `consumes: []` — Party is DAG #4 and depends on no business module), and this stage mints none — nothing in this plan reads another module's data. There is therefore no `XM-*` block, no `ddl_patch`, no adapter and no integration package.

- Access mechanism (CM-5): `profile.conventions.module_interface: in_process` — one deployable; PTY's dependencies are the given erp-core only (CORE, given usage), reached through the `via` interfaces the SRS names. No HTTP client, no base URL, no network error path exists in this module; no Error Catalog row describes one.
- Direction (CM-10): no PTY dependency points to any business module, so none can point upward.
- Inbound: the modules below PTY reach it through its published contract (CON-PTY-001 … CON-PTY-024) — the in-process `PtyPartyApi`, the extension point `CustomerCreditExposureContributor` (CON-PTY-022; downstream modules implement it, PTY calls whatever beans exist — REQ-PTY-080, REQ-PTY-084) and the event `PartyRoleDeactivatedEvent` (CON-PTY-021, REQ-PTY-023). Those edges are theirs, written in their own plans and derived by `gov.py graph` into `platform/dependency-graph.json`; this phase carries no state for them.
<!-- PHASE:INT-C:END -->

## Query Reference Catalog (QR-*)

Logical specifications, not executable code: the implementer rewrites every entry with the real entity classes, mapped properties and the project's query strategy (Spring Data JPA derived queries or JPQL; `@Query(nativeQuery = true)` only where an entry says NATIVE). Tables and columns are the db-script's. Defaults of the engine apply unless an entry overrides them; `:x` marks a bound parameter; every text "contains" filter escapes `%` and `_` in the value.

**QR-PTY-001 — lock Party by PK**
Phase: DATA-DOM · API: API-PTY-001, 003, 004, 005, 006, 008, 009, 010, 011, 013, 014, 015, 034 · Entity: ENT-PTY-001 · Operation: FIND_ONE
Intent: load the Party a role or Address change belongs to and serialise every change of its roles, active state and Addresses
Logical spec: SELECT * FROM PTY_PARTY WHERE PARTY_PK = :partyPk FOR UPDATE
Join: NONE · Transaction: READ_WRITE (joins the caller's) · Locking: row lock — the Party row is the mutex for role add (RULE-PTY-009), the Party active state (REQ-PTY-028, REQ-PTY-029), the Address ordinal and the default switch (RULE-PTY-017) · Pagination: NO · Filters: partyPk EXACT · Result: full entity · Null: not found → PTY-404-PARTY-NOT-FOUND

**QR-PTY-002 — insert Party**
Phase: DATA-DOM · API: API-PTY-001, 006, 033, 034 · Entity: ENT-PTY-001 · Operation: SAVE
Intent: persist a new Party with its code from the number series
Logical spec: INSERT INTO PTY_PARTY (MOBILE, EMAIL, TAX_REGISTRATION_NO, NAME_AR, NAME_EN, CODE, IS_ACTIVE_FL, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (…) — PARTY_PK by `GENERATED ALWAYS AS IDENTITY`
Join: NONE · Transaction: READ_WRITE · Locking: NONE — uniqueness by UQ_PTY_PARTY_CODE and UQ_PTY_PARTY_EMAIL (violation of the email index → PTY-409-EMAIL-TAKEN, or a guest re-match — ADR-PTY-026) · Pagination: NO · Filters: — · Result: full entity with PK · Null: optional contacts stored as NULL (blank → NULL)

**QR-PTY-003 — update Party**
Phase: DATA-DOM · API: API-PTY-001, 003, 004, 005, 006, 008, 009, 010 · Entity: ENT-PTY-001 · Operation: UPDATE
Intent: write the shared identity fields or the derived active state of a locked Party
Logical spec: UPDATE PTY_PARTY SET NAME_AR = :nameAr, NAME_EN = :nameEn, MOBILE = :mobile, EMAIL = :email, TAX_REGISTRATION_NO = :tax, IS_ACTIVE_FL = :active, UPDATED_BY, UPDATED_AT WHERE PARTY_PK = :partyPk — CODE never in the SET list
Join: NONE · Transaction: READ_WRITE · Locking: the row is already locked by QR-PTY-001 · Pagination: NO · Filters: partyPk EXACT · Result: row count · Null: blank optional fields → NULL

**QR-PTY-004 — find the active Party holding an email**
Phase: DATA-DOM · API: API-PTY-001, 003, 005, 006, 008, 010 · Entity: ENT-PTY-001 · Operation: FIND_BY_CRITERIA
Intent: answer RULE-PTY-002 and name the holder's code in the message
Logical spec: SELECT CODE FROM PTY_PARTY WHERE IS_ACTIVE_FL AND LOWER(BTRIM(EMAIL)) = LOWER(BTRIM(:email)) AND (:selfPk IS NULL OR PARTY_PK <> :selfPk) LIMIT 1 — served by UQ_PTY_PARTY_EMAIL's expression
Join: NONE · Transaction: READ_ONLY within the caller · Locking: NONE — the read decides, the unique index guards the write (two callers both reading "free" → the second insert / update fails on UQ_PTY_PARTY_EMAIL and is answered PTY-409-EMAIL-TAKEN) · Pagination: NO · Filters: email EXACT (normalised) · Result: projection CODE · Null: email empty → not executed

**QR-PTY-005 — read Customer with its Party**
Phase: SVC-API · API: API-PTY-002, 016, 019, 031, 035 · Entity: ENT-PTY-002 · Operation: FIND_ONE
Intent: return a Customer with the identity fields held once on its Party
Logical spec: SELECT c.*, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.EMAIL, p.TAX_REGISTRATION_NO FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.PARTY_PK = c.PARTY_FK WHERE c.CUSTOMER_PK = :id
Join: required — ADR-PTY-027 (the role's names and code live on the Party) · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Filters: id EXACT · Result: projection (Customer DTO) · Null: not found → PTY-404-CUSTOMER-NOT-FOUND; optional contacts and credit fields returned as null (credit fields omitted while the flag is off)

**QR-PTY-006 — lock Customer by PK**
Phase: DATA-DOM · API: API-PTY-003, 004, 005, 017, 018 · Entity: ENT-PTY-002 · Operation: FIND_ONE
Intent: load a Customer that will be changed and keep a concurrent change out until commit
Logical spec: SELECT * FROM PTY_CUSTOMER WHERE CUSTOMER_PK = :id FOR UPDATE
Join: NONE · Transaction: READ_WRITE · Locking: row lock — credit edits chain their audit old values (API-PTY-017), activation changes do not interleave · Pagination: NO · Filters: id EXACT · Result: full entity · Null: not found → PTY-404-CUSTOMER-NOT-FOUND

**QR-PTY-007 — find the Customer of a Party**
Phase: DATA-DOM · API: API-PTY-001, 006, 034 · Entity: ENT-PTY-002 · Operation: FIND_ONE
Intent: does this Party already hold a Customer (RULE-PTY-009), is it the walk-in Customer (RULE-PTY-012), which Customer does a matched guest attach to (REQ-PTY-055 … REQ-PTY-057)
Logical spec: SELECT * FROM PTY_CUSTOMER WHERE PARTY_FK = :partyPk — at most one by UQ_PTY_CUSTOMER_PARTY_FK
Join: NONE · Transaction: within the caller · Locking: NONE of its own — the caller holds the Party lock (QR-PTY-001), which serialises role adds of the Party · Pagination: NO · Filters: partyPk EXACT · Result: full entity · Null: none → "no Customer"

**QR-PTY-008 — insert Customer**
Phase: DATA-DOM · API: API-PTY-001, 033, 034 · Entity: ENT-PTY-002 · Operation: SAVE
Intent: persist the Customer role of a Party
Logical spec: INSERT INTO PTY_CUSTOMER (CUSTOMER_GROUP_CODE, CREDIT_LIMIT, PAYMENT_TERM_DAYS, SEC_USER_ID, IS_WALK_IN_FL, PARTY_FK, IS_ACTIVE_FL, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (:group, NULL, NULL, NULL, :walkIn, :partyPk, TRUE, …)
Join: NONE · Transaction: READ_WRITE (REQUIRES_NEW for the walk-in creation — ADR-PTY-026) · Locking: NONE — UQ_PTY_CUSTOMER_PARTY_FK and UQ_PTY_CUSTOMER_IS_WALK_IN_FL guard · Pagination: NO · Result: full entity with PK · Null: credit fields and secUserId NULL on create

**QR-PTY-009 — update Customer**
Phase: DATA-DOM · API: API-PTY-003, 004, 005, 017, 018 · Entity: ENT-PTY-002 · Operation: UPDATE
Intent: write the customer group, the active flag, the credit values, or (invitation handler) the CUSTOMER-realm user of a locked Customer
Logical spec: UPDATE PTY_CUSTOMER SET CUSTOMER_GROUP_CODE | IS_ACTIVE_FL | CREDIT_LIMIT, PAYMENT_TERM_DAYS | SEC_USER_ID = :v, UPDATED_BY, UPDATED_AT WHERE CUSTOMER_PK = :id — PARTY_FK and IS_WALK_IN_FL never in the SET list
Join: NONE · Transaction: READ_WRITE · Locking: row already locked by QR-PTY-006 · Pagination: NO · Filters: id EXACT · Result: row count · Null: cleared credit → NULL

**QR-PTY-010 — search Customers**
Phase: SVC-API · API: API-PTY-026 · Entity: ENT-PTY-002 · Operation: FIND_BY_CRITERIA
Intent: one page of Customers matching the SRS B2 filters of the Customers screen
Logical spec: SELECT c.CUSTOMER_PK, c.CUSTOMER_GROUP_CODE, c.IS_WALK_IN_FL, c.IS_ACTIVE_FL, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.EMAIL, p.TAX_REGISTRATION_NO FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.PARTY_PK = c.PARTY_FK WHERE [UPPER(p.CODE) LIKE UPPER('%'||:code||'%')] AND [(LOWER(p.NAME_AR) LIKE … OR LOWER(p.NAME_EN) LIKE LOWER('%'||:name||'%'))] AND [p.MOBILE LIKE '%'||:mobile||'%'] AND [LOWER(p.EMAIL) LIKE …] AND [p.TAX_REGISTRATION_NO LIKE …] AND [c.CUSTOMER_GROUP_CODE = :group] AND [c.IS_ACTIVE_FL = :isActive] ORDER BY :sort LIMIT :size OFFSET :page*:size (+ COUNT for totalElements)
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: YES (Page<T>) · Filters: code LIKE, name LIKE, mobile LIKE, email LIKE, taxRegistrationNo LIKE, customerGroupCode EXACT, isActive EXACT · Result: projection CustomerRow · Null: absent filter = not applied; empty result → empty page

**QR-PTY-011 — Customer global search**
Phase: SVC-API · API: API-PTY-027 · Entity: ENT-PTY-002 · Operation: NATIVE
Intent: at most 10 active Customers whose code, names, mobile, email or tax number contain the term, Arabic letter forms unified, exact code first
Logical spec: SELECT c.CUSTOMER_PK, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.PARTY_PK = c.PARTY_FK WHERE c.IS_ACTIVE_FL AND (LOWER(p.CODE) LIKE :t OR TRANSLATE(p.NAME_AR, 'أإآةى', 'اااهي') LIKE :tAr OR LOWER(p.NAME_EN) LIKE :t OR REGEXP_REPLACE(p.MOBILE, '[ -]', '', 'g') LIKE :tDigits OR LOWER(p.EMAIL) LIKE :t OR LOWER(p.TAX_REGISTRATION_NO) LIKE :t) ORDER BY (UPPER(p.CODE) = UPPER(:term)) DESC, <display name of the request language, falling back to the other> ASC LIMIT 10 — :t = '%'||lower(term)||'%', :tAr = '%'||TRANSLATE(term, 'أإآةى', 'اااهي')||'%'
Join: required — ADR-PTY-027 (native: TRANSLATE / REGEXP_REPLACE expressions, no database extension) · Transaction: READ_ONLY · Locking: NONE · Pagination: NO (LIMIT 10) · Filters: term LIKE across six columns · Result: projection CustomerSearchHit · Null: NULL columns never match

**QR-PTY-012 — find the walk-in Customer**
Phase: SVC-API · API: API-PTY-033 · Entity: ENT-PTY-002 · Operation: FIND_ONE
Intent: the tenant's single walk-in Customer, with its Party
Logical spec: SELECT c.*, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.EMAIL, p.TAX_REGISTRATION_NO FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.PARTY_PK = c.PARTY_FK WHERE c.IS_WALK_IN_FL — served by UQ_PTY_CUSTOMER_IS_WALK_IN_FL
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE — creation races are settled by the unique index (ADR-PTY-026) · Pagination: NO · Result: projection (Customer DTO) · Null: none → create on demand (REQ-PTY-046)

**QR-PTY-013 — search Customer credit**
Phase: SVC-API · API: API-PTY-030 · Entity: ENT-PTY-002 · Operation: FIND_BY_CRITERIA
Intent: one page of Customers with their credit values for the Customer credit screen
Logical spec: SELECT c.CUSTOMER_PK, c.CUSTOMER_GROUP_CODE, c.CREDIT_LIMIT, c.PAYMENT_TERM_DAYS, p.CODE, p.NAME_AR, p.NAME_EN FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.PARTY_PK = c.PARTY_FK WHERE NOT c.IS_WALK_IN_FL AND [code LIKE] AND [name LIKE] AND [c.CUSTOMER_GROUP_CODE = :group] AND [(c.CREDIT_LIMIT IS NOT NULL OR c.PAYMENT_TERM_DAYS IS NOT NULL) = :carriesCredit] ORDER BY :sort LIMIT/OFFSET (+ COUNT) — carriesCredit = true is served by IDX_PTY_CUSTOMER_CARRIES_CREDIT
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: YES (Page<T>) · Filters: code LIKE, name LIKE, customerGroupCode EXACT, carriesCredit EXACT · Result: projection CustomerCreditRow · Null: empty → empty page

**QR-PTY-014 — count Customers carrying credit**
Phase: SVC-API · API: API-PTY-037 · Entity: ENT-PTY-002 · Operation: COUNT
Intent: the switch-off dependency answer (REQ-PTY-075)
Logical spec: SELECT COUNT(*) FROM PTY_CUSTOMER WHERE CREDIT_LIMIT IS NOT NULL OR PAYMENT_TERM_DAYS IS NOT NULL — IDX_PTY_CUSTOMER_CARRIES_CREDIT
Join: NONE · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: count · Null: —

**QR-PTY-015 — read Supplier with its Party**
Phase: SVC-API · API: API-PTY-007 · Entity: ENT-PTY-003 · Operation: FIND_ONE
Intent: return a Supplier with the identity fields of its Party
Logical spec: SELECT s.*, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.EMAIL, p.TAX_REGISTRATION_NO FROM PTY_SUPPLIER s JOIN PTY_PARTY p ON p.PARTY_PK = s.PARTY_FK WHERE s.SUPPLIER_PK = :id
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Filters: id EXACT · Result: projection (Supplier DTO) · Null: not found → PTY-404-SUPPLIER-NOT-FOUND

**QR-PTY-016 — lock Supplier by PK**
Phase: DATA-DOM · API: API-PTY-008, 009, 010 · Entity: ENT-PTY-003 · Operation: FIND_ONE
Intent: load a Supplier that will be changed
Logical spec: SELECT * FROM PTY_SUPPLIER WHERE SUPPLIER_PK = :id FOR UPDATE
Join: NONE · Transaction: READ_WRITE · Locking: row lock · Pagination: NO · Filters: id EXACT · Result: full entity · Null: not found → PTY-404-SUPPLIER-NOT-FOUND

**QR-PTY-017 — find the Supplier of a Party**
Phase: DATA-DOM · API: API-PTY-006 · Entity: ENT-PTY-003 · Operation: FIND_ONE
Intent: does this Party already hold a Supplier (RULE-PTY-009)
Logical spec: SELECT * FROM PTY_SUPPLIER WHERE PARTY_FK = :partyPk — at most one by UQ_PTY_SUPPLIER_PARTY_FK
Join: NONE · Transaction: within the caller · Locking: the caller holds the Party lock · Pagination: NO · Result: full entity · Null: none → "no Supplier"

**QR-PTY-018 — insert Supplier**
Phase: DATA-DOM · API: API-PTY-006 · Entity: ENT-PTY-003 · Operation: SAVE
Intent: persist the Supplier role of a Party
Logical spec: INSERT INTO PTY_SUPPLIER (PAYMENT_TERM_DAYS, PARTY_FK, IS_ACTIVE_FL, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (:terms, :partyPk, TRUE, …)
Join: NONE · Transaction: READ_WRITE · Locking: NONE — UQ_PTY_SUPPLIER_PARTY_FK guards · Pagination: NO · Result: full entity with PK · Null: paymentTermDays NULL when absent

**QR-PTY-019 — update Supplier**
Phase: DATA-DOM · API: API-PTY-008, 009, 010 · Entity: ENT-PTY-003 · Operation: UPDATE
Intent: write payment terms or the active flag of a locked Supplier
Logical spec: UPDATE PTY_SUPPLIER SET PAYMENT_TERM_DAYS | IS_ACTIVE_FL = :v, UPDATED_BY, UPDATED_AT WHERE SUPPLIER_PK = :id — PARTY_FK never in the SET list
Join: NONE · Transaction: READ_WRITE · Locking: row locked by QR-PTY-016 · Pagination: NO · Result: row count · Null: —

**QR-PTY-020 — search Suppliers**
Phase: SVC-API · API: API-PTY-028 · Entity: ENT-PTY-003 · Operation: FIND_BY_CRITERIA
Intent: one page of Suppliers matching the SRS B2 filters of the Suppliers screen
Logical spec: SELECT s.SUPPLIER_PK, s.PAYMENT_TERM_DAYS, s.IS_ACTIVE_FL, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.TAX_REGISTRATION_NO FROM PTY_SUPPLIER s JOIN PTY_PARTY p ON p.PARTY_PK = s.PARTY_FK WHERE [code LIKE] AND [name LIKE on NAME_AR / NAME_EN] AND [MOBILE LIKE] AND [TAX_REGISTRATION_NO LIKE] AND [s.IS_ACTIVE_FL = :isActive] ORDER BY :sort LIMIT/OFFSET (+ COUNT)
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: YES (Page<T>) · Filters: code LIKE, name LIKE, mobile LIKE, taxRegistrationNo LIKE, isActive EXACT · Result: projection SupplierRow · Null: empty → empty page

**QR-PTY-021 — count the active roles of a Party**
Phase: DATA-DOM · API: API-PTY-004, 009 · Entity: ENT-PTY-001 · Operation: COUNT
Intent: is a role still active on this Party after a deactivation (REQ-PTY-028)
Logical spec: SELECT (SELECT COUNT(*) FROM PTY_CUSTOMER WHERE PARTY_FK = :partyPk AND IS_ACTIVE_FL) + (SELECT COUNT(*) FROM PTY_SUPPLIER WHERE PARTY_FK = :partyPk AND IS_ACTIVE_FL) — executed after the role's own update is flushed
Join: NONE · Transaction: READ_WRITE (inside the deactivation) · Locking: the caller holds the Party lock, so the count and the Party write it decides cannot interleave with another role change of the Party · Pagination: NO · Result: count · Null: —

**QR-PTY-022 — read Address by PK**
Phase: SVC-API · API: API-PTY-012 · Entity: ENT-PTY-004 · Operation: FIND_ONE
Intent: an Address by its identifier, inactive included (AC-PTY-039)
Logical spec: SELECT * FROM PTY_ADDRESS WHERE ADDRESS_PK = :id
Join: NONE · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: full entity · Null: not found → PTY-404-ADDRESS-NOT-FOUND

**QR-PTY-023 — lock Address of a Party**
Phase: DATA-DOM · API: API-PTY-013, 014, 015 · Entity: ENT-PTY-004 · Operation: FIND_ONE
Intent: load an Address of the Party in the path for a change
Logical spec: SELECT * FROM PTY_ADDRESS WHERE ADDRESS_PK = :id AND PARTY_FK = :partyPk FOR UPDATE
Join: NONE · Transaction: READ_WRITE · Locking: row lock (the Party row is locked first, always in that order — no deadlock) · Pagination: NO · Result: full entity · Null: not found or of another Party → PTY-404-ADDRESS-NOT-FOUND

**QR-PTY-024 — next Address ordinal of a Party**
Phase: DATA-DOM · API: API-PTY-011, 034 · Entity: ENT-PTY-004 · Operation: AGGREGATE
Intent: the next system-assigned Address code within the Party ("1", "2", …)
Logical spec: SELECT COALESCE(MAX(CAST(CODE AS INTEGER)), 0) + 1 FROM PTY_ADDRESS WHERE PARTY_FK = :partyPk
Join: NONE · Transaction: READ_WRITE · Locking: read-then-write — the caller holds the Party lock (QR-PTY-001), so no second create of the Party reads the same maximum; UQ_PTY_ADDRESS_PARTY_CODE backstops · Pagination: NO · Result: integer, written as text · Null: no Address → 1

**QR-PTY-025 — insert Address**
Phase: DATA-DOM · API: API-PTY-011, 034 · Entity: ENT-PTY-004 · Operation: SAVE
Intent: persist a new Address of a Party
Logical spec: INSERT INTO PTY_ADDRESS (ADDRESS_TYPE_CODE, LINE1, LINE2, CITY, REGION, POSTAL_CODE, IS_DEFAULT_FL, PARTY_FK, NAME_AR, NAME_EN, CODE, IS_ACTIVE_FL, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (…)
Join: NONE · Transaction: READ_WRITE · Locking: NONE of its own (Party lock held) · Pagination: NO · Result: full entity with PK · Null: optional lines NULL

**QR-PTY-026 — update Address**
Phase: DATA-DOM · API: API-PTY-013, 014, 015 · Entity: ENT-PTY-004 · Operation: UPDATE
Intent: write the fields, the active flag or the default flag of a locked Address
Logical spec: UPDATE PTY_ADDRESS SET <fields> | IS_ACTIVE_FL | IS_DEFAULT_FL = :v, UPDATED_BY, UPDATED_AT WHERE ADDRESS_PK = :id — PARTY_FK and CODE never in the SET list; a default is cleared before another is set
Join: NONE · Transaction: READ_WRITE · Locking: rows locked by QR-PTY-023 under the Party lock · Pagination: NO · Result: row count · Null: —

**QR-PTY-027 — list active Addresses of a Party**
Phase: SVC-API · API: API-PTY-029 · Entity: ENT-PTY-004 · Operation: FIND_ALL
Intent: the Party's active Addresses with type and default mark (REQ-PTY-032)
Logical spec: SELECT * FROM PTY_ADDRESS WHERE PARTY_FK = :partyPk AND IS_ACTIVE_FL ORDER BY ADDRESS_TYPE_CODE, IS_DEFAULT_FL DESC, CODE — IDX_PTY_ADDRESS_PARTY_TYPE
Join: NONE · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: full entities · Null: none → empty list

**QR-PTY-028 — find the default Address of a type**
Phase: DATA-DOM · API: API-PTY-015 · Entity: ENT-PTY-004 · Operation: FIND_ONE
Intent: the current default of the Party for an address type, to clear it before setting another
Logical spec: SELECT * FROM PTY_ADDRESS WHERE PARTY_FK = :partyPk AND ADDRESS_TYPE_CODE = :type AND IS_DEFAULT_FL AND IS_ACTIVE_FL FOR UPDATE — at most one by UQ_PTY_ADDRESS_IS_DEFAULT_FL
Join: NONE · Transaction: READ_WRITE · Locking: row lock under the Party lock · Pagination: NO · Result: full entity · Null: none → nothing to clear

**QR-PTY-029 — find the most recently updated remaining Address of a type**
Phase: DATA-DOM · API: API-PTY-013, 014 · Entity: ENT-PTY-004 · Operation: FIND_ONE
Intent: the Address that becomes default when the default of its type leaves (REQ-PTY-036)
Logical spec: SELECT * FROM PTY_ADDRESS WHERE PARTY_FK = :partyPk AND ADDRESS_TYPE_CODE = :type AND IS_ACTIVE_FL AND ADDRESS_PK <> :leaving ORDER BY UPDATED_AT DESC, ADDRESS_PK DESC LIMIT 1 FOR UPDATE — IDX_PTY_ADDRESS_PARTY_TYPE
Join: NONE · Transaction: READ_WRITE · Locking: read-then-write under the Party lock · Pagination: NO · Result: full entity · Null: none → the type keeps no default (no active Address of it remains)

**QR-PTY-030 — exists active Address of a type**
Phase: DATA-DOM · API: API-PTY-011, 013 · Entity: ENT-PTY-004 · Operation: EXISTS
Intent: is this the first active Address of its type on the Party (REQ-PTY-034)
Logical spec: SELECT EXISTS (SELECT 1 FROM PTY_ADDRESS WHERE PARTY_FK = :partyPk AND ADDRESS_TYPE_CODE = :type AND IS_ACTIVE_FL AND (:selfPk IS NULL OR ADDRESS_PK <> :selfPk))
Join: NONE · Transaction: READ_WRITE (inside the create / update) · Locking: read-then-write under the Party lock; UQ_PTY_ADDRESS_IS_DEFAULT_FL backstops · Pagination: NO · Result: boolean · Null: —

**QR-PTY-031 — insert Credit override request**
Phase: DATA-DOM · API: API-PTY-019 · Entity: ENT-PTY-005 · Operation: SAVE
Intent: persist a PENDING request with the credit picture at the time of the request
Logical spec: INSERT INTO PTY_CREDIT_OVERRIDE_REQUEST (SALE_AMOUNT, CREDIT_LIMIT_AT_REQUEST, OPEN_EXPOSURE, CREDIT_CHECK_RESULT_CODE, SOURCE_DOCUMENT_REF, CUSTOMER_FK, DOC_NO, DOC_DATE, STATUS_CODE, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (…, 'PENDING', …)
Join: NONE · Transaction: READ_WRITE · Locking: NONE — docNo from NumberSeriesApi, UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO backstops · Pagination: NO · Result: full entity with PK · Null: creditLimitAtRequest / openExposure NULL when absent

**QR-PTY-032 — read Credit override request**
Phase: SVC-API · API: API-PTY-020 · Entity: ENT-PTY-005 · Operation: FIND_ONE
Intent: a request with its Customer's code and names, visible to the caller
Logical spec: SELECT r.*, p.CODE, p.NAME_AR, p.NAME_EN FROM PTY_CREDIT_OVERRIDE_REQUEST r JOIN PTY_CUSTOMER c ON c.CUSTOMER_PK = r.CUSTOMER_FK JOIN PTY_PARTY p ON p.PARTY_PK = c.PARTY_FK WHERE r.CREDIT_OVERRIDE_REQUEST_PK = :id [AND r.CREATED_BY = :principal when the caller lacks PERM_PTY_CREDIT_OVERRIDE_UPDATE]
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: projection (CreditOverrideRequest DTO) · Null: not found / not visible → PTY-404-CREDIT-OVERRIDE-NOT-FOUND

**QR-PTY-033 — lock Credit override request**
Phase: DATA-DOM · API: API-PTY-021, 022, 023, 036 · Entity: ENT-PTY-005 · Operation: FIND_ONE
Intent: load a request whose status is about to be decided
Logical spec: SELECT * FROM PTY_CREDIT_OVERRIDE_REQUEST WHERE CREDIT_OVERRIDE_REQUEST_PK = :id FOR UPDATE
Join: NONE · Transaction: READ_WRITE · Locking: row lock — the status read here is the status the transition is decided on; a second decider waits and then reads the first decision · Pagination: NO · Result: full entity · Null: not found → PTY-404-CREDIT-OVERRIDE-NOT-FOUND

**QR-PTY-034 — conditional status update of a request**
Phase: DATA-DOM · API: API-PTY-021, 022, 023, 036 · Entity: ENT-PTY-005 · Operation: UPDATE
Intent: move a request along the A7 lifecycle only from the status the decision was made on
Logical spec: UPDATE PTY_CREDIT_OVERRIDE_REQUEST SET STATUS_CODE = :new, DECIDED_BY = :decider, DECIDED_AT = :now, DECISION_REASON = :reason, USED_DOCUMENT_REF = :usedRef, UPDATED_BY, UPDATED_AT WHERE CREDIT_OVERRIDE_REQUEST_PK = :id AND STATUS_CODE = :expected (PENDING for approve / reject / cancel, APPROVED for use) — DOC_NO never in the SET list
Join: NONE · Transaction: READ_WRITE · Locking: compare-and-set on STATUS_CODE in addition to the row lock; 0 rows updated → re-read and answer RULE-PTY-026 (decisions) or RULE-PTY-028 (use) · Pagination: NO · Result: row count · Null: fields not set by the transition stay as they are

**QR-PTY-035 — search Credit override requests**
Phase: SVC-API · API: API-PTY-032 · Entity: ENT-PTY-005 · Operation: FIND_BY_CRITERIA
Intent: the approver's queue (PENDING first, newest first) or the requester's own requests
Logical spec: SELECT r.CREDIT_OVERRIDE_REQUEST_PK, r.DOC_NO, r.DOC_DATE, r.STATUS_CODE, r.CUSTOMER_FK, r.SALE_AMOUNT, r.CREDIT_LIMIT_AT_REQUEST, r.OPEN_EXPOSURE, r.CREDIT_CHECK_RESULT_CODE, r.CREATED_BY, p.CODE, p.NAME_AR, p.NAME_EN FROM PTY_CREDIT_OVERRIDE_REQUEST r JOIN PTY_CUSTOMER c ON c.CUSTOMER_PK = r.CUSTOMER_FK JOIN PTY_PARTY p ON p.PARTY_PK = c.PARTY_FK WHERE [r.DOC_NO = :docNo] AND [r.STATUS_CODE = :status] AND [r.CUSTOMER_FK = :customerId] AND [r.DOC_DATE >= :from] AND [r.DOC_DATE < :to + 1 day] AND [r.CREATED_BY = :principal when the caller lacks PERM_PTY_CREDIT_OVERRIDE_UPDATE] ORDER BY (r.STATUS_CODE = 'PENDING') DESC, r.DOC_DATE DESC LIMIT/OFFSET (+ COUNT) — IDX_PTY_CREDIT_OVERRIDE_REQUEST_STATUS_CODE, _DOC_DATE, _CREATED_BY, _CUSTOMER_FK
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: YES (Page<T>) · Filters: docNo EXACT, statusCode EXACT, customerId EXACT, docDate DATE_RANGE, createdBy EXACT (visibility) · Result: projection CreditOverrideRow · Null: empty → empty page

**QR-PTY-036 — match an active Party by email for a guest**
Phase: SVC-API · API: API-PTY-034 · Entity: ENT-PTY-001 · Operation: FIND_BY_CRITERIA
Intent: the active Party whose email equals the guest's, without letter case, never the walk-in Party (REQ-PTY-055, RULE-PTY-013)
Logical spec: SELECT p.* FROM PTY_PARTY p WHERE p.IS_ACTIVE_FL AND LOWER(BTRIM(p.EMAIL)) = LOWER(BTRIM(:email)) AND NOT EXISTS (SELECT 1 FROM PTY_CUSTOMER w WHERE w.PARTY_FK = p.PARTY_PK AND w.IS_WALK_IN_FL) — at most one by UQ_PTY_PARTY_EMAIL
Join: NONE (correlated NOT EXISTS) · Transaction: READ_WRITE (inside the resolution) · Locking: the matched Party is then locked by QR-PTY-001; a create racing on the same email is settled by UQ_PTY_PARTY_EMAIL and a re-match (ADR-PTY-026) · Pagination: NO · Result: full entity · Null: email empty → skipped; none → mobile matching

**QR-PTY-037 — match active Parties by normalised mobile for a guest**
Phase: SVC-API · API: API-PTY-034 · Entity: ENT-PTY-001 · Operation: NATIVE
Intent: the active Parties whose normalised mobile equals the guest's — exactly one is a match, more than one is ambiguous (REQ-PTY-056, REQ-PTY-061)
Logical spec: SELECT p.PARTY_PK FROM PTY_PARTY p WHERE p.IS_ACTIVE_FL AND REGEXP_REPLACE(REGEXP_REPLACE(p.MOBILE, '[ -]', '', 'g'), '^00', '+') = :normalisedMobile AND NOT EXISTS (SELECT 1 FROM PTY_CUSTOMER w WHERE w.PARTY_FK = p.PARTY_PK AND w.IS_WALK_IN_FL) LIMIT 2
Join: NONE · Transaction: READ_WRITE (inside the resolution) · Locking: NONE — mobile is not unique (SRS D-03); an ambiguous or racing result creates a new Party, as REQ-PTY-061 prescribes · Pagination: NO (LIMIT 2 — only "one" or "more than one" matters) · Result: projection PARTY_PK list · Null: mobile empty → skipped

**QR-PTY-038 — exists Party by PK**
Phase: DATA-DOM · API: API-PTY-029 · Entity: ENT-PTY-001 · Operation: EXISTS
Intent: distinguish "unknown Party" (404) from "Party without active Address" (empty list)
Logical spec: SELECT EXISTS (SELECT 1 FROM PTY_PARTY WHERE PARTY_PK = :partyPk)
Join: NONE · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: boolean · Null: false → PTY-404-PARTY-NOT-FOUND

## Error Catalog

The single canonical catalog. Envelope `LocalizedException → {code, messageAr, messageEn}`; every code is an instance of `{MOD}-{http}[-{SLUG}]`; every `http` is one of 200, 201, 400, 401, 403, 404, 409, 413, 415, 422, 500. Messages of rule rows are copied character-perfect from the SRS; PLATFORM-STD rows carry ADR-PTY-024. Downstream consumers cite the code, not the text. No row describes a network failure — the platform is one deployable.

```yaml name=error-catalog
rows:
  - {code: "PTY-422-NAME-REQUIRED", rule: RULE-PTY-001, api: [API-PTY-001, API-PTY-003, API-PTY-006, API-PTY-008, API-PTY-034], http: 422, trigger: "a Party create or update carries neither nameAr nor nameEn", messages: {ar: "أدخل اسم الطرف بالعربية أو بالإنجليزية.", en: "Enter the party name in Arabic or English."}}
  - {code: "PTY-409-EMAIL-TAKEN", rule: RULE-PTY-002, api: [API-PTY-001, API-PTY-003, API-PTY-005, API-PTY-006, API-PTY-008, API-PTY-010], http: 409, trigger: "the email is held by another active Party (create, update of email, reactivation of a role)", messages: {ar: "هذا البريد الإلكتروني مسجّل لطرف نشط آخر ({code}). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.", en: "This email is already registered to another active party ({code}). Open that party instead of creating a new one."}}
  - {code: "PTY-422-EMAIL-FORMAT", rule: RULE-PTY-003, api: [API-PTY-001, API-PTY-003, API-PTY-006, API-PTY-008, API-PTY-034], http: 422, trigger: "a non-empty email is not one '@' with a non-empty local part and a '.' in the domain part", messages: {ar: "صيغة البريد الإلكتروني غير صحيحة، مثال: name@example.com", en: "The email format is not valid, for example: name@example.com"}}
  - {code: "PTY-422-MOBILE-FORMAT", rule: RULE-PTY-004, api: [API-PTY-001, API-PTY-003, API-PTY-006, API-PTY-008, API-PTY-034], http: 422, trigger: "a non-empty mobile, spaces and dashes removed, is not an optional '+' and 7 to 15 digits", messages: {ar: "رقم الجوال يتكون من 7 إلى 15 رقماً، ويمكن أن يبدأ بعلامة +.", en: "The mobile number has 7 to 15 digits and can start with +."}}
  - {code: "PTY-422-ROLE-INACTIVE", rule: RULE-PTY-005, api: [API-PTY-002, API-PTY-007], http: 422, trigger: "a Customer or Supplier read with forNewDocument = true is inactive", messages: {ar: "{code} غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.", en: "{code} is inactive and cannot be used on a new document. Reactivate it or choose another party."}}
  - {code: "PTY-422-CODE-READ-ONLY", rule: RULE-PTY-007, api: [API-PTY-003, API-PTY-008], http: 422, trigger: "an update carries a Party code different from the stored one", messages: {ar: "رقم الطرف يُنشأ تلقائياً ولا يمكن تعديله.", en: "The party code is assigned by the system and cannot be changed."}}
  - {code: "PTY-422-PAYMENT-TERMS-RANGE", rule: RULE-PTY-008, api: [API-PTY-006, API-PTY-008, API-PTY-017], http: 422, trigger: "a non-empty paymentTermDays is below 0 or above 365", messages: {ar: "مدة السداد يجب أن تكون بين 0 و365 يوماً.", en: "Payment terms must be between 0 and 365 days."}}
  - {code: "PTY-409-ROLE-HELD", rule: RULE-PTY-009, api: [API-PTY-001, API-PTY-006], http: 409, trigger: "a Customer or Supplier is added to a Party that already holds that role", messages: {ar: "هذا الطرف له صفة {roleAr} بالفعل.", en: "This party already holds the {roleEn} role."}}
  - {code: "PTY-422-WALK-IN-DEACTIVATE", rule: RULE-PTY-010, api: [API-PTY-004], http: 422, trigger: "a deactivation targets the walk-in Customer", messages: {ar: "العميل النقدي لازم لنقطة البيع ولا يمكن إيقافه.", en: "The walk-in customer is required by the point of sale and cannot be deactivated."}}
  - {code: "PTY-422-WALK-IN-CREDIT", rule: RULE-PTY-011, api: [API-PTY-017], http: 422, trigger: "a creditLimit or paymentTermDays is saved on the walk-in Customer", messages: {ar: "لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان.", en: "The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit."}}
  - {code: "PTY-422-WALK-IN-SUPPLIER", rule: RULE-PTY-012, api: [API-PTY-006], http: 422, trigger: "a Supplier is added to the Party of the walk-in Customer", messages: {ar: "لا يمكن إضافة صفة المورّد إلى العميل النقدي.", en: "The walk-in customer cannot also be a supplier."}}
  - {code: "PTY-422-ADDRESS-LINE-REQUIRED", rule: RULE-PTY-015, api: [API-PTY-011, API-PTY-013, API-PTY-034], http: 422, trigger: "an Address save (or a guest delivery address) carries no line1", messages: {ar: "أدخل السطر الأول من العنوان.", en: "Enter the first address line."}}
  - {code: "PTY-422-ADDRESS-TYPE-INVALID", rule: RULE-PTY-016, api: [API-PTY-011, API-PTY-013], http: 422, trigger: "addressTypeCode is not an active value of lookup ADDRESS_TYPE", messages: {ar: "اختر نوع عنوان من القائمة.", en: "Choose an address type from the list."}}
  - {code: "PTY-422-CUSTOMER-GROUP-INVALID", rule: RULE-PTY-018, api: [API-PTY-001, API-PTY-003], http: 422, trigger: "customerGroupCode is not an active value of lookup CUSTOMER_GROUP", messages: {ar: "اختر مجموعة عملاء من القائمة.", en: "Choose a customer group from the list."}}
  - {code: "PTY-422-GUEST-CONTACT-REQUIRED", rule: RULE-PTY-019, api: [API-PTY-034], http: 422, trigger: "guest order details carry neither an email nor a mobile", messages: {ar: "أدخل البريد الإلكتروني أو رقم الجوال لإتمام الطلب.", en: "Enter an email or a mobile number to complete the order."}}
  - {code: "PTY-422-GUEST-NOT-COMPLETED", rule: RULE-PTY-021, api: [API-PTY-034], http: 422, trigger: "guest order details match an active Party whose Customer is inactive", messages: {ar: "تعذّر إتمام الطلب بهذه البيانات. يُرجى التواصل مع البائع.", en: "The order cannot be completed with these details. Please contact the seller."}}
  - {code: "PTY-422-CREDIT-LIMIT-NEGATIVE", rule: RULE-PTY-022, api: [API-PTY-017], http: 422, trigger: "a non-empty creditLimit is below 0", messages: {ar: "حد الائتمان يجب أن يكون صفراً أو أكثر.", en: "The credit limit must be zero or more."}}
  - {code: "PTY-422-CREDIT-NOT-ENABLED", rule: RULE-PTY-023, api: [API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-030, API-PTY-031], http: 422, trigger: "credit data is read or written, or a Credit override request is submitted, while b2bCredit is off", messages: {ar: "الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.", en: "Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms."}}
  - {code: "PTY-422-OVERRIDE-NOT-NEEDED", rule: RULE-PTY-025, api: [API-PTY-019], http: 422, trigger: "the server-side credit check of the request answers WITHIN_LIMIT", messages: {ar: "لا حاجة إلى موافقة: البيع ضمن حد ائتمان العميل.", en: "No approval is needed: the sale is within the customer's credit limit."}}
  - {code: "PTY-409-REQUEST-NOT-PENDING", rule: RULE-PTY-026, api: [API-PTY-021, API-PTY-022, API-PTY-023], http: 409, trigger: "approve, reject or cancel of a request whose statusCode is not PENDING", messages: {ar: "هذا الطلب لم يعد معلّقاً (الحالة: {statusAr}).", en: "This request is no longer pending (status: {statusEn})."}}
  - {code: "PTY-422-REJECTION-REASON-REQUIRED", rule: RULE-PTY-027, api: [API-PTY-022], http: 422, trigger: "a rejection carries no decisionReason", messages: {ar: "أدخل سبب الرفض.", en: "Enter the reason for the rejection."}}
  - {code: "PTY-422-APPROVAL-NOT-COVERING", rule: RULE-PTY-028, api: [API-PTY-036], http: 422, trigger: "a use of a request that is not APPROVED, is for another Customer, or covers a smaller saleAmount than the sale", messages: {ar: "الموافقة {docNo} لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.", en: "Approval {docNo} does not cover this sale. Check the customer and the amount, or request a new approval."}}
  - {code: "PTY-403-NOT-REQUESTER", rule: RULE-PTY-029, api: [API-PTY-023], http: 403, trigger: "a user other than the request's creator cancels it", messages: {ar: "لا يلغي هذا الطلب إلا من قدّمه.", en: "Only the requester can cancel this request."}}
  - {code: "PTY-500-AUDIT-NOT-RECORDED", rule: RULE-PTY-030, api: [API-PTY-017, API-PTY-018], http: 500, trigger: "the core audit module cannot record a creditLimit or paymentTermDays change; the change is rolled back", messages: {ar: "تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.", en: "The change could not be recorded in the audit history, so it was not saved. Try again."}}
  - {code: "PTY-422-DOC-NO-READ-ONLY", rule: RULE-PTY-031, api: [API-PTY-021, API-PTY-022, API-PTY-036], http: 422, trigger: "a request change carries a docNo different from the stored one", messages: {ar: "رقم الطلب يُنشأ تلقائياً ولا يمكن تعديله.", en: "The request number is assigned by the system and cannot be changed."}}
  - {code: "PTY-422-SALE-AMOUNT-NOT-POSITIVE", rule: RULE-PTY-032, api: [API-PTY-019], http: 422, trigger: "a Credit override request carries a saleAmount of 0 or less", messages: {ar: "مبلغ البيع يجب أن يكون أكبر من صفر.", en: "The sale amount must be greater than zero."}}
  - {code: "PTY-422-WALK-IN-NO-CREDIT-SALE", rule: RULE-PTY-033, api: [API-PTY-019], http: 422, trigger: "a Credit override request is submitted for the walk-in Customer", messages: {ar: "لا يمكن البيع الآجل للعميل النقدي. سجّل العميل باسمه أولاً.", en: "The walk-in customer cannot buy on credit. Register the customer by name first."}}
  - {code: "PTY-400-VALIDATION", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037], http: 400, trigger: "the request is malformed: unreadable body, wrong type, missing required parameter, length or value outside the declared schema", messages: {ar: "الطلب غير صالح: تحقّق من الحقول المرسلة.", en: "The request is not valid: check the submitted fields."}, adr: ADR-PTY-024}
  - {code: "PTY-401-UNAUTHENTICATED", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037], http: 401, trigger: "no valid bearer token", messages: {ar: "سجّل الدخول للمتابعة.", en: "Sign in to continue."}, adr: ADR-PTY-024}
  - {code: "PTY-403-FORBIDDEN", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037], http: 403, trigger: "the caller lacks the PERM_* the endpoint requires (platform authorisation layer); API-PTY-027 answers an empty list instead (REQ-PTY-043)", messages: {ar: "ليست لديك صلاحية لتنفيذ هذا الإجراء.", en: "You do not have permission to perform this action."}, adr: ADR-PTY-024}
  - {code: "PTY-404-CUSTOMER-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-031, API-PTY-035], http: 404, trigger: "no Customer with the given identifier in the caller's tenant", messages: {ar: "العميل غير موجود.", en: "The customer was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-404-SUPPLIER-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010], http: 404, trigger: "no Supplier with the given identifier in the caller's tenant", messages: {ar: "المورّد غير موجود.", en: "The supplier was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-404-PARTY-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-006, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-029], http: 404, trigger: "no Party with the given identifier (partyId) in the caller's tenant", messages: {ar: "الطرف غير موجود.", en: "The party was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-404-ADDRESS-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015], http: 404, trigger: "no Address with the given identifier (of the Party in the path); set-default on an inactive Address", messages: {ar: "العنوان غير موجود.", en: "The address was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-404-CREDIT-OVERRIDE-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-036], http: 404, trigger: "no Credit override request with the given identifier visible to the caller", messages: {ar: "طلب تجاوز حد الائتمان غير موجود.", en: "The credit override request was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-500-INTERNAL", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037], http: 500, trigger: "an unexpected server-side failure", messages: {ar: "حدث خطأ غير متوقع. أعد المحاولة.", en: "An unexpected error occurred. Try again."}, adr: ADR-PTY-024}
```

Rules with no catalog row, and why: RULE-PTY-006 and RULE-PTY-017 (confirmations returned on success), RULE-PTY-024 (the answer to the flag owner, API-PTY-037), RULE-PTY-013 and RULE-PTY-020 (outcomes of the in-process invitation handler, returned to the given's callback — ADR-PTY-025), RULE-PTY-014 (an idempotent ensure never surfaces it). No RULE is DEFERRED, so none is missing for that reason.
