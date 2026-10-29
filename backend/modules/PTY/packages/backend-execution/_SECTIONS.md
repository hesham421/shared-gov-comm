<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# BACKEND EXECUTION PLAN — Party / الأطراف (PTY)
══════════════════════════════════════════════════════════════════
Module : PTY   Version : v1   Profile : commerce   Stage : P3.1 (Backend Execution Plan) · pass 1 · track backend · plan exec
Inputs : srs (REQ 114 · AC 140 · ENT 5 · RULE 35 · SCR-REQ 5), db-script (5 tables · DBF 79 · XM register empty · postgresql16 · identity PKs · TENANT_ID on every table), registry-srs, registry-db, contract (contract-pty.md — CON-PTY-001 … CON-PTY-024), contracts (one published contract of another module — PTY consumes nothing from it), erp-core 1.6.0 usage skill (checklist C1 … C22, F1 … F12)
Governance : FULL — the db-script is present; no entity is PENDING DB
Counts : API 38 (API-PTY-001 … API-PTY-038) · QR 40 (QR-PTY-001 … QR-PTY-040) · integration edges 0 · Error Catalog rows 39 · ADR raised at P3.1: 10 (ADR-PTY-023 … ADR-PTY-032) · gate-analysis revise: ADR-PTY-035 … ADR-PTY-043 and three CORE-GAP files · P2 repair: ADR-PTY-044 · gate-analysis revise 2: ADR-PTY-045 … ADR-PTY-048 — all ACCEPTED, non-breaking, 0 BLOCKED · P2 decisions ADR-PTY-018 … ADR-PTY-022 filed from the db-script §5.2 (ADR-PTY-030; 020 and 021 superseded)
Status : DRAFT — P3.1 pass 1 · gate-analysis revise (findings G1, G3 … G10, G12 … G15, G22 applied) · gate-analysis revise 2 (findings G1 … G4, G6 … G10, G12, G13 applied) · no question raised
══════════════════════════════════════════════════════════════════

The implementer reads this plan in order: index → DB alignment manifest → decisions → phases in profile order (CORE, DATA-DOM, SVC-API, DOC, SEC-BE, ALIGN-BE, INT-C) → Query Reference Catalog → Error Catalog. The API document `api-spec-pty.yaml` is derived from the `API-*` blocks and the Error Catalog of this plan, never written beside them.

## Execution Plan Index

```
EXECUTION PLAN INDEX — PTY v1 — backend-execution-plan-pty.md
Profile: commerce · dialect: postgresql16 · framework: Spring Boot (Java 25) on the given erp-core
Open ADRs: 0 — decisions/PTY/ (raised here: ADR-PTY-023 … ADR-PTY-032, ADR-PTY-035 … ADR-PTY-043, ADR-PTY-045 … ADR-PTY-048, all ACCEPTED; applied from P2: ADR-PTY-044) · open CORE-GAP questions to the core: 3 (CORE-GAP-in-process-caller-authority, CORE-GAP-tenant-base-currency, CORE-GAP-ordinal-within-parent)
```

### Entity registry

| ENT | Name (en / ar) | Kind | Table | Business code | OPERATIONS |
|---|---|---|---|---|---|
| ENT-PTY-001 | Party / الطرف | master | PTY_PARTY | Party code — column of DBF-PTY-007, format `P-` + 6 digits from series PTY_PARTY (seeded by the migration, ADR-PTY-036), unique UQ_PTY_PARTY_CODE (TENANT_ID, CODE), read-only (RULE-PTY-007) | VIEW, CREATE, UPDATE |
| ENT-PTY-002 | Customer / العميل | master | PTY_CUSTOMER | none of its own — presents the Party code (ADR-PTY-014) | VIEW, CREATE, UPDATE, DELETE |
| ENT-PTY-003 | Supplier / المورّد | master | PTY_SUPPLIER | none of its own — presents the Party code (ADR-PTY-014) | VIEW, CREATE, UPDATE, DELETE |
| ENT-PTY-004 | Address / العنوان | master | PTY_ADDRESS | none (DBF-PTY-044 is an ordinal within the Party, not a business number — ADR-PTY-040) | VIEW, CREATE, UPDATE, DELETE |
| ENT-PTY-005 | Credit override request / طلب تجاوز حد الائتمان | transactional | PTY_CREDIT_OVERRIDE_REQUEST | docNo — column of DBF-PTY-061, format `CO-` + 6 digits from series PTY_CREDIT_OVERRIDE (seeded by the migration, ADR-PTY-036), unique UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO (TENANT_ID, DOC_NO), read-only (RULE-PTY-031) | VIEW, CREATE, UPDATE, DELETE |

### Field registry

| DBF range | ENT | Read-only (never accepted from a caller) |
|---|---|---|
| DBF-PTY-001 … DBF-PTY-012, 068, 073 | ENT-PTY-001 | DBF-PTY-001 (PK), DBF-PTY-007 (code), DBF-PTY-008 (derived from roles), DBF-PTY-009 … DBF-PTY-012 (audit), DBF-PTY-068, 073 (TENANT_ID, VERSION — AuditableEntity) |
| DBF-PTY-013 … DBF-PTY-024, 069, 074, 078 | ENT-PTY-002 | DBF-PTY-013 (PK), DBF-PTY-017 (set by the invitation subscriber), DBF-PTY-018 (walk-in), DBF-PTY-019 (set on create), DBF-PTY-021 … DBF-PTY-024 (audit), DBF-PTY-069, 074 (TENANT_ID, VERSION), DBF-PTY-078 (currencyCode — system) |
| DBF-PTY-025 … DBF-PTY-032, 070, 075 | ENT-PTY-003 | DBF-PTY-025 (PK), DBF-PTY-027 (set on create), DBF-PTY-029 … DBF-PTY-032 (audit), DBF-PTY-070, 075 (TENANT_ID, VERSION) |
| DBF-PTY-033 … DBF-PTY-049, 071, 076 | ENT-PTY-004 | DBF-PTY-033 (PK), DBF-PTY-040 (system-maintained), DBF-PTY-041 (path), DBF-PTY-044 (ordinal), DBF-PTY-046 … DBF-PTY-049 (audit), DBF-PTY-071, 076 (TENANT_ID, VERSION) |
| DBF-PTY-050 … DBF-PTY-067, 072, 077, 079 | ENT-PTY-005 | DBF-PTY-050 (PK), DBF-PTY-052 … DBF-PTY-054, DBF-PTY-056, DBF-PTY-057, DBF-PTY-061 … DBF-PTY-063 (system), DBF-PTY-064 … DBF-PTY-067 (audit), DBF-PTY-072, 077 (TENANT_ID, VERSION), DBF-PTY-079 (currencyCode — system) |

### API registry

| API | Operation | Verb | Path | Traces (REQ — DBF in the block) |
|---|---|---|---|---|
| API-PTY-001 | create Customer / add Customer role | POST | /api/v1/pty/customers | REQ-PTY-004, 005, 006, 007, 008, 009, 010, 011, 026, 027, 029 |
| API-PTY-002 | read Customer | GET | /api/v1/pty/customers/{id} | REQ-PTY-001, 002, 003, 014, 071 |
| API-PTY-003 | update Customer | PUT | /api/v1/pty/customers/{id} | REQ-PTY-009, 010, 011, 012, 024, 039, 040 |
| API-PTY-004 | deactivate Customer | DELETE | /api/v1/pty/customers/{id} | REQ-PTY-014, 023, 028, 047, 111 |
| API-PTY-005 | reactivate Customer | PATCH | /api/v1/pty/customers/{id}/activation | REQ-PTY-010, 015, 029 |
| API-PTY-006 | create Supplier / add Supplier role | POST | /api/v1/pty/suppliers | REQ-PTY-006, 008, 009, 010, 011, 016, 022, 024, 025, 027, 029, 049 |
| API-PTY-007 | read Supplier | GET | /api/v1/pty/suppliers/{id} | REQ-PTY-003, 019, 020 |
| API-PTY-008 | update Supplier | PUT | /api/v1/pty/suppliers/{id} | REQ-PTY-009, 010, 011, 017, 022, 024 |
| API-PTY-009 | deactivate Supplier | DELETE | /api/v1/pty/suppliers/{id} | REQ-PTY-020, 023, 028 |
| API-PTY-010 | reactivate Supplier | PATCH | /api/v1/pty/suppliers/{id}/activation | REQ-PTY-010, 021, 029 |
| API-PTY-011 | create Address | POST | /api/v1/pty/parties/{partyId}/addresses | REQ-PTY-030, 034, 037, 038, 110 |
| API-PTY-012 | read Address | GET | /api/v1/pty/addresses/{id} | REQ-PTY-033 |
| API-PTY-013 | update Address | PUT | /api/v1/pty/parties/{partyId}/addresses/{id} | REQ-PTY-031, 037, 038, 110 |
| API-PTY-014 | deactivate Address | DELETE | /api/v1/pty/parties/{partyId}/addresses/{id} | REQ-PTY-033, 036 |
| API-PTY-015 | set default Address | PATCH | /api/v1/pty/parties/{partyId}/addresses/{id}/default | REQ-PTY-035, 110 |
| API-PTY-016 | read credit standing | GET | /api/v1/pty/customer-credits/{customerId} | REQ-PTY-068, 070, 073, 080, 085 |
| API-PTY-017 | update credit | PUT | /api/v1/pty/customer-credits/{customerId} | REQ-PTY-048, 064, 065, 066, 067, 072, 073, 106, 109, 110 |
| API-PTY-018 | clear credit | DELETE | /api/v1/pty/customer-credits/{customerId} | REQ-PTY-078, 106, 109 |
| API-PTY-019 | create Credit override request | POST | /api/v1/pty/credit-overrides | REQ-PTY-072, 089, 091, 092, 093, 104, 105, 110, 113, 114 |
| API-PTY-020 | read Credit override request | GET | /api/v1/pty/credit-overrides/{id} | REQ-PTY-103 |
| API-PTY-021 | approve Credit override request | PATCH | /api/v1/pty/credit-overrides/{id}/approval | REQ-PTY-094, 098, 107, 109, 112 |
| API-PTY-022 | reject Credit override request | PATCH | /api/v1/pty/credit-overrides/{id}/rejection | REQ-PTY-095, 098, 100, 107, 109, 112 |
| API-PTY-023 | cancel Credit override request | DELETE | /api/v1/pty/credit-overrides/{id} | REQ-PTY-096, 098, 112 |
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
| API-PTY-035 | credit check | POST | /api/v1/pty/credit-checks | REQ-PTY-068, 074, 079 … 084, 087, 088, 089, 110 |
| API-PTY-036 | use Credit override request | PATCH | /api/v1/pty/credit-overrides/{id}/use | REQ-PTY-097, 099, 110, 111, 112 |
| API-PTY-037 | `b2bCredit` switch-off dependency | GET | /api/v1/pty/flag-dependencies/b2bCredit | REQ-PTY-075, 076, 077 |
| API-PTY-038 | read a PTY lookup key (OwnedLookups) | GET | /api/v1/pty/lookups/{key} | REQ-PTY-013, 038, 039, 086 |

### Rule registry

| RULE | Name | Scope | Messages ar/en | Enforced by |
|---|---|---|---|---|
| RULE-PTY-001 | Party name required | ENT-PTY-001 | ✓ | API-PTY-001, 003, 006, 008, 034 · PTY-422-NAME-REQUIRED · CHK_PTY_PARTY_NAME |
| RULE-PTY-002 | Email unique among active Parties | ENT-PTY-001 | ✓ | API-PTY-001, 003, 005, 006, 008, 010 · PTY-409-EMAIL-TAKEN · UQ_PTY_PARTY_EMAIL |
| RULE-PTY-003 | Email format | ENT-PTY-001 | ✓ | API-PTY-001, 003, 006, 008, 034 · PTY-422-EMAIL-FORMAT · CHK_PTY_PARTY_EMAIL |
| RULE-PTY-004 | Mobile format | ENT-PTY-001 | ✓ | API-PTY-001, 003, 006, 008, 034 · PTY-422-MOBILE-FORMAT · CHK_PTY_PARTY_MOBILE |
| RULE-PTY-005 | Inactive role refused for new documents, credit operations and address writes | ENT-PTY-001 … ENT-PTY-005 | ✓ | API-PTY-002, 007, 011, 013, 015, 017, 019, 035, 036 · PTY-422-ROLE-INACTIVE |
| RULE-PTY-006 | Effect of deactivation | ENT-PTY-002, 003, 004 | ✓ (confirmation) | API-PTY-004, 009, 014 — success message + outbox event `pty.PartyRoleDeactivated`, no error |
| RULE-PTY-007 | Party code system-assigned, read-only | ENT-PTY-001 | ✓ | API-PTY-003, 008 · PTY-422-CODE-READ-ONLY · UQ_PTY_PARTY_CODE |
| RULE-PTY-008 | Payment terms range | ENT-PTY-002, ENT-PTY-003 | ✓ | API-PTY-006, 008, 017 · PTY-422-PAYMENT-TERMS-RANGE · CHK_PTY_CUSTOMER_PAYMENT_TERM_DAYS, CHK_PTY_SUPPLIER_PAYMENT_TERM_DAYS |
| RULE-PTY-009 | One role of each kind per Party | ENT-PTY-002, ENT-PTY-003 | ✓ | API-PTY-001, 006 · PTY-409-ROLE-HELD · UQ_PTY_CUSTOMER_PARTY_FK, UQ_PTY_SUPPLIER_PARTY_FK |
| RULE-PTY-010 | Walk-in Customer stays active | ENT-PTY-002 | ✓ | API-PTY-004 · PTY-422-WALK-IN-DEACTIVATE · CHK_PTY_CUSTOMER_WALK_IN_ACTIVE |
| RULE-PTY-011 | Walk-in Customer carries no credit | ENT-PTY-002 | ✓ | API-PTY-017 · PTY-422-WALK-IN-CREDIT · CHK_PTY_CUSTOMER_WALK_IN_CREDIT |
| RULE-PTY-012 | Walk-in Party has no Supplier | ENT-PTY-002, ENT-PTY-003 | ✓ | API-PTY-006 · PTY-422-WALK-IN-SUPPLIER |
| RULE-PTY-013 | Walk-in never matched nor linked | ENT-PTY-001, ENT-PTY-002 | ✓ | API-PTY-034 (matching filter, QR-PTY-036, QR-PTY-037) · invitation subscriber (outbox, CORE) · CHK_PTY_CUSTOMER_WALK_IN_SEC_USER |
| RULE-PTY-014 | One walk-in Customer per tenant | ENT-PTY-002 | ✓ | API-PTY-033 (idempotent ensure) · UQ_PTY_CUSTOMER_IS_WALK_IN_FL |
| RULE-PTY-015 | Address line required | ENT-PTY-004 | ✓ | API-PTY-011, 013, 034 · PTY-422-ADDRESS-LINE-REQUIRED · CHK_PTY_ADDRESS_LINE1 |
| RULE-PTY-016 | Address type from the lookup | ENT-PTY-004 | ✓ | API-PTY-011, 013 · PTY-422-ADDRESS-TYPE-INVALID |
| RULE-PTY-017 | One default Address per type per Party | ENT-PTY-004 | ✓ (confirmation) | API-PTY-011, 013, 014, 015 · UQ_PTY_ADDRESS_IS_DEFAULT_FL — success message, no error |
| RULE-PTY-018 | Customer group from the lookup | ENT-PTY-002 | ✓ | API-PTY-001, 003 · PTY-422-CUSTOMER-GROUP-INVALID |
| RULE-PTY-019 | Guest contact required | ENT-PTY-001 | ✓ | API-PTY-034 · PTY-422-GUEST-CONTACT-REQUIRED |
| RULE-PTY-020 | One Customer per CUSTOMER-realm user | ENT-PTY-002 | ✓ | invitation subscriber (outbox, CORE) · UQ_PTY_CUSTOMER_SEC_USER_ID |
| RULE-PTY-021 | Guest matched to an inactive Customer | ENT-PTY-002 | ✓ | API-PTY-034 · PTY-422-GUEST-NOT-COMPLETED |
| RULE-PTY-022 | Credit limit not negative | ENT-PTY-002 | ✓ | API-PTY-017 · PTY-422-CREDIT-LIMIT-NEGATIVE · CHK_PTY_CUSTOMER_CREDIT_LIMIT |
| RULE-PTY-023 | Credit data only while the flag is on | ENT-PTY-002, ENT-PTY-005 | ✓ | API-PTY-016, 017, 018, 019, 021, 022, 030, 031, 036 · PTY-422-CREDIT-NOT-ENABLED |
| RULE-PTY-024 | Flag switch-off blocked while credit is in use | ENT-PTY-002 | ✓ (answer) | API-PTY-037 — returned as the answer to the flag owner, no error |
| RULE-PTY-025 | Request only for a real overrun | ENT-PTY-005 | ✓ | API-PTY-019 · PTY-422-OVERRIDE-NOT-NEEDED · CHK_PTY_CREDIT_OVERRIDE_REQUEST_CREDIT_CHECK_RESULT_CODE |
| RULE-PTY-026 | Decisions only on PENDING requests | ENT-PTY-005 | ✓ | API-PTY-021, 022, 023 · PTY-409-REQUEST-NOT-PENDING |
| RULE-PTY-027 | Rejection reason required | ENT-PTY-005 | ✓ | API-PTY-022 · PTY-422-REJECTION-REASON-REQUIRED · CHK_PTY_CREDIT_OVERRIDE_REQUEST_DECISION_REASON |
| RULE-PTY-028 | Use only within the approval (same Customer, same sale, amount covered) | ENT-PTY-005 | ✓ | API-PTY-036 · PTY-422-APPROVAL-NOT-COVERING · CHK_PTY_CREDIT_OVERRIDE_REQUEST_USED_DOCUMENT_REF |
| RULE-PTY-029 | Only the requester cancels | ENT-PTY-005 | ✓ | API-PTY-023 · PTY-403-NOT-REQUESTER |
| RULE-PTY-030 | Credit change or overrun decision recorded or not saved | ENT-PTY-002, ENT-PTY-005 | ✓ | API-PTY-017, 018, 021, 022 · PTY-500-AUDIT-NOT-RECORDED |
| RULE-PTY-031 | Request number system-assigned, read-only | ENT-PTY-005 | ✓ | API-PTY-021, 022, 036 · PTY-422-DOC-NO-READ-ONLY · UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO |
| RULE-PTY-032 | Sale amount positive | ENT-PTY-005 | ✓ | API-PTY-019 · PTY-422-SALE-AMOUNT-NOT-POSITIVE · CHK_PTY_CREDIT_OVERRIDE_REQUEST_SALE_AMOUNT |
| RULE-PTY-033 | No credit override for the walk-in Customer | ENT-PTY-005 | ✓ | API-PTY-019 · PTY-422-WALK-IN-NO-CREDIT-SALE |
| RULE-PTY-034 | One open Credit override request per sale | ENT-PTY-005 | ✓ | API-PTY-019 · PTY-409-REQUEST-OPEN-FOR-SALE · UQ_PTY_CREDIT_OVERRIDE_REQUEST_OPEN_SOURCE |
| RULE-PTY-035 | Request only under BLOCK_WITH_OVERRIDE | ENT-PTY-005 | ✓ | API-PTY-019 · PTY-422-OVERRIDE-NOT-REQUIRED (ADR-PTY-045) |

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
| QR-PTY-039 | lock the open requests of a Customer | DATA-DOM | ENT-PTY-005 |
| QR-PTY-040 | find the open request of a sale | DATA-DOM | ENT-PTY-005 |

```
DB ALIGNMENT  see manifest below — ALIGNED ✓ / issues: 0
INTEGRATION   0 edges — the XM register is empty (SRS A8 consumes: []); `INT-C`, the last phase, carries no block (R7)
GIVEN         14 uses — the SRS A9 `given-usage` rows (erp-core); every one goes through the `via` the row names; this plan adds none
TENANCY       TENANT_ID on every table and query (checklist C1, C3); 2 native queries with @TenantUnscoped (QR-PTY-011, 037)
SECURITY      5 screens × 3 roles (Owner, Seller/Cashier, Warehouse)
```

### Totals

```yaml name=totals
DBF: 79
XM: 1
API: 38
QR: 40
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
| DBF-PTY-017 | ENT-PTY-002 | secUserId | Long | — | ✓ (set by the invitation subscriber `PtyCustomerInvitationRedeemedSubscriber`, SVC-API-INT) |
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
| DBF-PTY-068 | ENT-PTY-001 | tenantId | Long | — | ✓ core-provided (given AuditableEntity `@TenantId`; never in a DTO) |
| DBF-PTY-069 | ENT-PTY-002 | tenantId | Long | — | ✓ core-provided (given AuditableEntity `@TenantId`) |
| DBF-PTY-070 | ENT-PTY-003 | tenantId | Long | — | ✓ core-provided (given AuditableEntity `@TenantId`) |
| DBF-PTY-071 | ENT-PTY-004 | tenantId | Long | — | ✓ core-provided (given AuditableEntity `@TenantId`) |
| DBF-PTY-072 | ENT-PTY-005 | tenantId | Long | — | ✓ core-provided (given AuditableEntity `@TenantId`) |
| DBF-PTY-073 | ENT-PTY-001 | version | Long | — | ✓ core-provided (given AuditableEntity `@Version`; bodies do not echo it — ADR-PTY-035) |
| DBF-PTY-074 | ENT-PTY-002 | version | Long | — | ✓ core-provided (given AuditableEntity `@Version`) |
| DBF-PTY-075 | ENT-PTY-003 | version | Long | — | ✓ core-provided (given AuditableEntity `@Version`) |
| DBF-PTY-076 | ENT-PTY-004 | version | Long | — | ✓ core-provided (given AuditableEntity `@Version`) |
| DBF-PTY-077 | ENT-PTY-005 | version | Long | — | ✓ core-provided (given AuditableEntity `@Version`) |
| DBF-PTY-078 | ENT-PTY-002 | currencyCode | String | — | ✓ (Orchestration of API-PTY-001, 033, 034 — `PtyCurrency.baseCurrency()`, ADR-PTY-037) |
| DBF-PTY-079 | ENT-PTY-005 | currencyCode | String | — | ✓ (Orchestration of API-PTY-019 — copied from DBF-PTY-078) |

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
| ADR-PTY-015 | Guest matching, contact normalisation, CUSTOMER-realm invitation | ACCEPTED | API-PTY-034, invitation subscriber |
| ADR-PTY-016 | Credit override request, single step, sale held as text | ACCEPTED | API-PTY-019 … 023, 032, 036 |
| ADR-PTY-017 | Open exposure from contributors; empty limit = no credit; switch-off answer | ACCEPTED | API-PTY-016, 035, 037 |
| ADR-PTY-018 … ADR-PTY-022 | (P2) the five structural decisions the db-script records in its §5.2 (physical precision, audit principal columns, no currency column, tenant scoping, lookup seed) — filed verbatim from §5.2 by ADR-PTY-030; ADR-PTY-020 superseded by ADR-PTY-037, ADR-PTY-021 superseded by ADR-PTY-035, ADR-PTY-022's column list revised by ADR-PTY-035 | ACCEPTED (020, 021 SUPERSEDED) | every binding |
| ADR-PTY-023 | ADR sequence continued at 023 after the db-script's recorded decisions | ACCEPTED (new) | this plan |
| ADR-PTY-024 | Error Catalog: PLATFORM-STD rows, status mapping and slugs | ACCEPTED (new) | Error Catalog |
| ADR-PTY-025 | Permissions the SRS leaves open; in-process callers; invitation-handler outcomes — in-process callers and the Address permission revised by ADR-PTY-039 | ACCEPTED (new; revised) | SEC-BE, API-PTY-005, 010 … 015, 027, 033 … 037 |
| ADR-PTY-026 | Concurrency guards and Address default on a type change | ACCEPTED (new) | every write endpoint |
| ADR-PTY-027 | Query strategy — Party join, normalised search and matching as native queries | ACCEPTED (new) | QR-PTY-005, 010, 011, 013, 015, 020, 035, 036, 037 |
| ADR-PTY-028 | Credit computation and derived properties — the WARN bullet withdrawn by ADR-PTY-045 | ACCEPTED (new; revised) | API-PTY-016, 019, 030, 031, 035, 037 |
| ADR-PTY-029 | Bootstrap — number series, setting key, role grants, walk-in provisioning — series and template revised by ADR-PTY-036 | ACCEPTED (new; revised) | SEC-BE `bootstrap` |
| ADR-PTY-030 | P2 decisions ADR-PTY-018 … ADR-PTY-022 filed from the db-script §5.2 | ACCEPTED (new) | decisions/PTY/ |
| ADR-PTY-031 | Typed bodies for the reactivation and set-default PATCH endpoints | ACCEPTED (new) | API-PTY-005, 010, 015 |
| ADR-PTY-032 | The XM total counts the XM ids the db-script names | ACCEPTED (new) | `totals` |
| ADR-PTY-035 | Tenant columns, version column and tenant-composite uniqueness on every table (supersedes ADR-PTY-021) | ACCEPTED (gate-analysis revise) | CORE (Entities, Tenancy), DATA-DOM, every QR |
| ADR-PTY-036 | Number series and the approver notification template seeded by the migration (revises ADR-PTY-029) | ACCEPTED (gate-analysis revise) | CORE (Numbering), API-PTY-019, SEC-BE `bootstrap` |
| ADR-PTY-037 | currencyCode on every credit amount (supersedes ADR-PTY-020) · CORE-GAP-tenant-base-currency | ACCEPTED (gate-analysis revise) | CORE (Currency), API-PTY-001, 002, 016, 017, 019, 020, 030, 032 … 035 |
| ADR-PTY-038 | `pty.PartyRoleDeactivated` on the given outbox | ACCEPTED (gate-analysis revise) | CORE, API-PTY-004, 009 |
| ADR-PTY-039 | Service-method authority; in-process callers hold it; either UPDATE for a two-role Party's Address (revises ADR-PTY-025) · CORE-GAP-in-process-caller-authority | ACCEPTED (gate-analysis revise) | SEC-BE, every API block |
| ADR-PTY-040 | Address ordinal within the Party under the Party lock · CORE-GAP-ordinal-within-parent | ACCEPTED (gate-analysis revise) | QR-PTY-024, API-PTY-011, 034 |
| ADR-PTY-041 | Inactive Customer / Party on credit operations and address writes; open requests on deactivation and while the flag is off | ACCEPTED (gate-analysis revise) | API-PTY-004, 011, 013, 015, 017, 019, 021, 022, 023, 035, 036 |
| ADR-PTY-042 | One open request per sale; a use bound to its source sale | ACCEPTED (gate-analysis revise) | API-PTY-019, 036, QR-PTY-040 |
| ADR-PTY-043 | The Party named by PTY-409-EMAIL-TAKEN found through the role searches by email | ACCEPTED (gate-analysis revise) | API-PTY-028 |
| ADR-PTY-044 | The given-usage rows name only seams the given publishes: tenant re-binding through `TenantRunner`, the manifest through the module api doc, no row for the `modules.common` base class | ACCEPTED (P2 repair round 1) | CORE (Given usage, Transactions), API-PTY-019, 034 |
| ADR-PTY-045 | A Credit override request is accepted only under BLOCK_WITH_OVERRIDE (revises ADR-PTY-028) | ACCEPTED (gate-analysis revise 2) | RULE-PTY-035, API-PTY-019, Error Catalog |
| ADR-PTY-046 | Customer VIEW includes reading the Customer's credit values; Customer credit VIEW gates the credit work | ACCEPTED (gate-analysis revise 2) | API-PTY-002, SEC-BE |
| ADR-PTY-047 | One application root namespace `com.commerce` for every business module; PTY is `com.commerce.pty` | ACCEPTED (gate-analysis revise 2) | CORE (Module layout, manifest, boundary), SEC-BE, DOC |
| ADR-PTY-048 | PTY serves its own lookup keys through `OwnedLookups` (amends ADR-PTY-034) | ACCEPTED (gate-analysis revise 2) | CORE (Lookups), API-PTY-038, Error Catalog |

BLOCKED : none.















## Query Reference Catalog (QR-*)

These are logical specifications, not executable code. The implementer rewrites every entry with the real entity classes, mapped properties and the project's query strategy: Spring Data JPA derived queries or JPQL, and `@Query(nativeQuery = true)` only where an entry says NATIVE. Tables and columns are the db-script's. Engine defaults apply unless an entry overrides them; `:x` marks a bound parameter; every text "contains" filter escapes `%` and `_` in the value.

**Tenant predicate (checklist C1, C3).** Every logical spec below names `TENANT_ID = :tenantId` on every PTY table it reads or writes:
- For JPQL, derived and search-builder queries the predicate is added by the given tenant filter (`:tenantId` = the tenant the given filter binds to the request). It is written here so the spec is complete.
- For an INSERT, TENANT_ID is set by `AuditableEntity`, and VERSION starts at 0.
- The two NATIVE entries, QR-PTY-011 and QR-PTY-037, write the predicate themselves. Their repository methods carry `@TenantUnscoped(reason, adr = "ADR-PTY-027")`.

**QR-PTY-001 — lock Party by PK**
Phase: DATA-DOM · API: API-PTY-001, 003, 004, 005, 006, 008, 009, 010, 011, 013, 014, 015, 034 · Entity: ENT-PTY-001 · Operation: FIND_ONE
Intent: load the Party a role or Address change belongs to, and serialise every change of its roles, active state and Addresses
Logical spec: SELECT * FROM PTY_PARTY WHERE TENANT_ID = :tenantId AND PARTY_PK = :partyPk FOR UPDATE
Join: NONE · Transaction: READ_WRITE (joins the caller's) · Locking: row lock — the Party row is the mutex for role add (RULE-PTY-009), the Party active state (REQ-PTY-028, REQ-PTY-029), the Address ordinal (ADR-PTY-040) and the default switch (RULE-PTY-017) · Pagination: NO · Filters: partyPk EXACT · Result: full entity · Null: not found → PTY-404-PARTY-NOT-FOUND

**QR-PTY-002 — insert Party**
Phase: DATA-DOM · API: API-PTY-001, 006, 033, 034 · Entity: ENT-PTY-001 · Operation: SAVE
Intent: persist a new Party with its code from the number series
Logical spec: INSERT INTO PTY_PARTY (MOBILE, EMAIL, TAX_REGISTRATION_NO, NAME_AR, NAME_EN, CODE, IS_ACTIVE_FL, TENANT_ID, VERSION, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (…, :tenantId, 0, …). PARTY_PK comes from `GENERATED ALWAYS AS IDENTITY`.
Join: NONE · Transaction: READ_WRITE · Locking: NONE — uniqueness by UQ_PTY_PARTY_CODE (TENANT_ID, CODE) and UQ_PTY_PARTY_EMAIL (TENANT_ID, LOWER(BTRIM(EMAIL))). A violation of the email index answers PTY-409-EMAIL-TAKEN, or triggers a guest re-match (ADR-PTY-026). · Pagination: NO · Filters: — · Result: full entity with PK · Null: optional contacts stored as NULL (blank → NULL)

**QR-PTY-003 — update Party**
Phase: DATA-DOM · API: API-PTY-001, 003, 004, 005, 006, 008, 009, 010 · Entity: ENT-PTY-001 · Operation: UPDATE
Intent: write the shared identity fields or the derived active state of a locked Party
Logical spec: UPDATE PTY_PARTY SET NAME_AR = :nameAr, NAME_EN = :nameEn, MOBILE = :mobile, EMAIL = :email, TAX_REGISTRATION_NO = :tax, IS_ACTIVE_FL = :active, VERSION = VERSION + 1, UPDATED_BY, UPDATED_AT WHERE TENANT_ID = :tenantId AND PARTY_PK = :partyPk. CODE is never in the SET list.
Join: NONE · Transaction: READ_WRITE · Locking: the row is already locked by QR-PTY-001 · Pagination: NO · Filters: partyPk EXACT · Result: row count · Null: blank optional fields → NULL

**QR-PTY-004 — find the active Party holding an email**
Phase: DATA-DOM · API: API-PTY-001, 003, 005, 006, 008, 010 · Entity: ENT-PTY-001 · Operation: FIND_BY_CRITERIA
Intent: give `PartyDomain` the holder of an email for RULE-PTY-002, and name the holder's code in the message
Logical spec: SELECT PARTY_PK, CODE FROM PTY_PARTY WHERE TENANT_ID = :tenantId AND IS_ACTIVE_FL AND LOWER(BTRIM(EMAIL)) = LOWER(BTRIM(:email)) AND (:selfPk IS NULL OR PARTY_PK <> :selfPk) LIMIT 1. UQ_PTY_PARTY_EMAIL's expression serves it.
Join: NONE · Transaction: READ_ONLY within the caller · Locking: NONE — the read decides and the unique index guards the write. If two callers both read "free", the second insert or update fails on UQ_PTY_PARTY_EMAIL and is answered PTY-409-EMAIL-TAKEN. · Pagination: NO · Filters: email EXACT (normalised) · Result: projection PARTY_PK, CODE · Null: email empty → not executed

**QR-PTY-005 — read Customer with its Party**
Phase: SVC-API · API: API-PTY-002, 016, 019, 031, 035 · Entity: ENT-PTY-002 · Operation: FIND_ONE
Intent: return a Customer with the identity fields held once on its Party, and its Party's active state
Logical spec: SELECT c.*, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.EMAIL, p.TAX_REGISTRATION_NO FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.TENANT_ID = c.TENANT_ID AND p.PARTY_PK = c.PARTY_FK WHERE c.TENANT_ID = :tenantId AND c.CUSTOMER_PK = :id
Join: required — ADR-PTY-027 (the role's names and code live on the Party) · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Filters: id EXACT · Result: projection (Customer DTO, currencyCode included) · Null: not found → PTY-404-CUSTOMER-NOT-FOUND; optional contacts and credit fields returned as null (the credit fields and currencyCode are omitted while the flag is off)

**QR-PTY-006 — lock Customer by PK**
Phase: DATA-DOM · API: API-PTY-003, 004, 005, 017, 018 · Entity: ENT-PTY-002 · Operation: FIND_ONE
Intent: load a Customer that will be changed, and keep a concurrent change out until commit
Logical spec: SELECT * FROM PTY_CUSTOMER WHERE TENANT_ID = :tenantId AND CUSTOMER_PK = :id FOR UPDATE
Join: NONE · Transaction: READ_WRITE · Locking: row lock — credit edits chain their audit old values (API-PTY-017), activation changes do not interleave, and a deactivation and a request creation of one Customer are serialised (API-PTY-004, 019) · Pagination: NO · Filters: id EXACT · Result: full entity · Null: not found → PTY-404-CUSTOMER-NOT-FOUND

**QR-PTY-007 — find the Customer of a Party**
Phase: DATA-DOM · API: API-PTY-001, 006, 034 · Entity: ENT-PTY-002 · Operation: FIND_ONE
Intent: answer three questions: does this Party already hold a Customer (RULE-PTY-009); is it the walk-in Customer (RULE-PTY-012); which Customer does a matched guest attach to (REQ-PTY-055 … REQ-PTY-057)
Logical spec: SELECT * FROM PTY_CUSTOMER WHERE TENANT_ID = :tenantId AND PARTY_FK = :partyPk. There is at most one, by UQ_PTY_CUSTOMER_PARTY_FK (TENANT_ID, PARTY_FK).
Join: NONE · Transaction: within the caller · Locking: NONE of its own — the caller holds the Party lock (QR-PTY-001), which serialises role adds of the Party · Pagination: NO · Filters: partyPk EXACT · Result: full entity · Null: none → "no Customer"

**QR-PTY-008 — insert Customer**
Phase: DATA-DOM · API: API-PTY-001, 033, 034 · Entity: ENT-PTY-002 · Operation: SAVE
Intent: persist the Customer role of a Party
Logical spec: INSERT INTO PTY_CUSTOMER (CUSTOMER_GROUP_CODE, CREDIT_LIMIT, PAYMENT_TERM_DAYS, CURRENCY_CODE, SEC_USER_ID, IS_WALK_IN_FL, PARTY_FK, IS_ACTIVE_FL, TENANT_ID, VERSION, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (:group, NULL, NULL, :baseCurrency, NULL, :walkIn, :partyPk, TRUE, :tenantId, 0, …). :baseCurrency comes from `PtyCurrency.baseCurrency()` (ADR-PTY-037).
Join: NONE · Transaction: READ_WRITE (REQUIRES_NEW for the walk-in creation — ADR-PTY-026) · Locking: NONE — UQ_PTY_CUSTOMER_PARTY_FK and UQ_PTY_CUSTOMER_IS_WALK_IN_FL (both led by TENANT_ID) guard · Pagination: NO · Result: full entity with PK · Null: credit fields and secUserId NULL on create

**QR-PTY-009 — update Customer**
Phase: DATA-DOM · API: API-PTY-003, 004, 005, 017, 018 · Entity: ENT-PTY-002 · Operation: UPDATE
Intent: write the customer group, the active flag or the credit values of a locked Customer; the invitation subscriber also writes the CUSTOMER-realm user through it
Logical spec: UPDATE PTY_CUSTOMER SET CUSTOMER_GROUP_CODE | IS_ACTIVE_FL | CREDIT_LIMIT, PAYMENT_TERM_DAYS | SEC_USER_ID = :v, VERSION = VERSION + 1, UPDATED_BY, UPDATED_AT WHERE TENANT_ID = :tenantId AND CUSTOMER_PK = :id. PARTY_FK, IS_WALK_IN_FL and CURRENCY_CODE are never in the SET list. It is written through the managed entity, so the `@Audited` listener records the credit fields.
Join: NONE · Transaction: READ_WRITE · Locking: row already locked by QR-PTY-006 · Pagination: NO · Filters: id EXACT · Result: row count · Null: cleared credit → NULL

**QR-PTY-010 — search Customers**
Phase: SVC-API · API: API-PTY-026 · Entity: ENT-PTY-002 · Operation: FIND_BY_CRITERIA
Intent: one page of Customers matching the SRS B2 filters of the Customers screen
Logical spec:
- SELECT c.CUSTOMER_PK, c.CUSTOMER_GROUP_CODE, c.IS_WALK_IN_FL, c.IS_ACTIVE_FL, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.EMAIL, p.TAX_REGISTRATION_NO
- FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.TENANT_ID = c.TENANT_ID AND p.PARTY_PK = c.PARTY_FK
- WHERE c.TENANT_ID = :tenantId
- AND [UPPER(p.CODE) LIKE UPPER('%'||:code||'%')]
- AND [(LOWER(p.NAME_AR) LIKE … OR LOWER(p.NAME_EN) LIKE LOWER('%'||:name||'%'))]
- AND [p.MOBILE LIKE '%'||:mobile||'%']
- AND [LOWER(p.EMAIL) LIKE LOWER('%'||:email||'%')]
- AND [p.TAX_REGISTRATION_NO LIKE …]
- AND [c.CUSTOMER_GROUP_CODE = :group]
- AND [c.IS_ACTIVE_FL = :isActive]
- ORDER BY :sort LIMIT :size OFFSET :page*:size, plus a COUNT for totalElements
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: YES (Page<T>) · Filters: code LIKE, name LIKE, mobile LIKE, email LIKE (case-insensitive — also the RULE-PTY-002 action, ADR-PTY-043), taxRegistrationNo LIKE, customerGroupCode EXACT, isActive EXACT · Result: projection CustomerRow · Null: absent filter = not applied; empty result → empty page

**QR-PTY-011 — Customer global search**
Phase: SVC-API · API: API-PTY-027 · Entity: ENT-PTY-002 · Operation: NATIVE
Intent: at most 10 active Customers whose code, names, mobile, email or tax number contain the term, with Arabic letter forms unified and exact code matches first
Logical spec:
- SELECT c.CUSTOMER_PK, c.IS_WALK_IN_FL, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE
- FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.TENANT_ID = c.TENANT_ID AND p.PARTY_PK = c.PARTY_FK
- WHERE c.TENANT_ID = :tenantId AND p.TENANT_ID = :tenantId AND c.IS_ACTIVE_FL
- AND (LOWER(p.CODE) LIKE :t ESCAPE '\' OR TRANSLATE(p.NAME_AR, 'أإآةى', 'اااهي') LIKE :tAr ESCAPE '\' OR LOWER(p.NAME_EN) LIKE :t ESCAPE '\' OR (:tDigits IS NOT NULL AND REGEXP_REPLACE(p.MOBILE, '[ -]', '', 'g') LIKE :tDigits ESCAPE '\') OR LOWER(p.EMAIL) LIKE :t ESCAPE '\' OR LOWER(p.TAX_REGISTRATION_NO) LIKE :t ESCAPE '\')
- ORDER BY (UPPER(p.CODE) = UPPER(:term)) DESC, <display name of the request language, falling back to the other> ASC
- LIMIT 10
- Parameters — each is built from the trimmed term `term`, after `esc(x)` has escaped `\`, `%` and `_` in `x` with a leading `\`, so the three characters match literally (`ESCAPE '\'` on every LIKE):
  - `:term` = term, unescaped — used only by the equality of the ORDER BY;
  - `:t` = `'%' || esc(lower(term)) || '%'`;
  - `:tAr` = `'%' || esc(TRANSLATE(term, 'أإآةى', 'اااهي')) || '%'`;
  - `:tDigits` = `'%' || esc(REGEXP_REPLACE(term, '[ -]', '', 'g')) || '%'` — the term with its spaces and dashes removed, compared with the mobile with its spaces and dashes removed (AC-PTY-139). It is NULL, and the mobile predicate is false, when fewer than 2 characters remain after the removal, so a term of spaces and dashes never matches every mobile.
- The repository method carries `@TenantUnscoped(reason = "TRANSLATE / REGEXP_REPLACE search across PTY_CUSTOMER and PTY_PARTY, tenant named in the SQL", adr = "ADR-PTY-027")`.
Join: required — ADR-PTY-027 (native: TRANSLATE / REGEXP_REPLACE expressions, no database extension) · Transaction: READ_ONLY · Locking: NONE · Pagination: NO (LIMIT 10) · Filters: tenant EXACT, term LIKE across six columns · Result: projection CustomerSearchHit · Null: NULL columns never match

**QR-PTY-012 — find the walk-in Customer**
Phase: SVC-API · API: API-PTY-033 · Entity: ENT-PTY-002 · Operation: FIND_ONE
Intent: the tenant's single walk-in Customer, with its Party
Logical spec: SELECT c.*, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.EMAIL, p.TAX_REGISTRATION_NO FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.TENANT_ID = c.TENANT_ID AND p.PARTY_PK = c.PARTY_FK WHERE c.TENANT_ID = :tenantId AND c.IS_WALK_IN_FL. UQ_PTY_CUSTOMER_IS_WALK_IN_FL (TENANT_ID) serves it.
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE — creation races are settled by the unique index (ADR-PTY-026) · Pagination: NO · Result: projection (Customer DTO) · Null: none → create on demand (REQ-PTY-046)

**QR-PTY-013 — search Customer credit**
Phase: SVC-API · API: API-PTY-030 · Entity: ENT-PTY-002 · Operation: FIND_BY_CRITERIA
Intent: one page of Customers with their credit values for the Customer credit screen
Logical spec:
- SELECT c.CUSTOMER_PK, c.CUSTOMER_GROUP_CODE, c.CREDIT_LIMIT, c.PAYMENT_TERM_DAYS, c.CURRENCY_CODE, p.CODE, p.NAME_AR, p.NAME_EN
- FROM PTY_CUSTOMER c JOIN PTY_PARTY p ON p.TENANT_ID = c.TENANT_ID AND p.PARTY_PK = c.PARTY_FK
- WHERE c.TENANT_ID = :tenantId AND NOT c.IS_WALK_IN_FL
- AND [code LIKE] AND [name LIKE] AND [c.CUSTOMER_GROUP_CODE = :group]
- AND [(c.CREDIT_LIMIT IS NOT NULL OR c.PAYMENT_TERM_DAYS IS NOT NULL) = :carriesCredit]
- ORDER BY :sort LIMIT/OFFSET, plus a COUNT
- carriesCredit = true is served by IDX_PTY_CUSTOMER_CARRIES_CREDIT (TENANT_ID, CUSTOMER_PK).
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: YES (Page<T>) · Filters: code LIKE, name LIKE, customerGroupCode EXACT, carriesCredit EXACT · Result: projection CustomerCreditRow · Null: empty → empty page

**QR-PTY-014 — count Customers carrying credit**
Phase: SVC-API · API: API-PTY-037 · Entity: ENT-PTY-002 · Operation: COUNT
Intent: the switch-off dependency answer (REQ-PTY-075); `CustomerDomain.carriesCredit()` is the predicate
Logical spec: SELECT COUNT(*) FROM PTY_CUSTOMER WHERE TENANT_ID = :tenantId AND (CREDIT_LIMIT IS NOT NULL OR PAYMENT_TERM_DAYS IS NOT NULL), served by IDX_PTY_CUSTOMER_CARRIES_CREDIT
Join: NONE · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: count · Null: —

**QR-PTY-015 — read Supplier with its Party**
Phase: SVC-API · API: API-PTY-007 · Entity: ENT-PTY-003 · Operation: FIND_ONE
Intent: return a Supplier with the identity fields of its Party
Logical spec: SELECT s.*, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.EMAIL, p.TAX_REGISTRATION_NO FROM PTY_SUPPLIER s JOIN PTY_PARTY p ON p.TENANT_ID = s.TENANT_ID AND p.PARTY_PK = s.PARTY_FK WHERE s.TENANT_ID = :tenantId AND s.SUPPLIER_PK = :id
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Filters: id EXACT · Result: projection (Supplier DTO) · Null: not found → PTY-404-SUPPLIER-NOT-FOUND

**QR-PTY-016 — lock Supplier by PK**
Phase: DATA-DOM · API: API-PTY-008, 009, 010 · Entity: ENT-PTY-003 · Operation: FIND_ONE
Intent: load a Supplier that will be changed
Logical spec: SELECT * FROM PTY_SUPPLIER WHERE TENANT_ID = :tenantId AND SUPPLIER_PK = :id FOR UPDATE
Join: NONE · Transaction: READ_WRITE · Locking: row lock · Pagination: NO · Filters: id EXACT · Result: full entity · Null: not found → PTY-404-SUPPLIER-NOT-FOUND

**QR-PTY-017 — find the Supplier of a Party**
Phase: DATA-DOM · API: API-PTY-006 · Entity: ENT-PTY-003 · Operation: FIND_ONE
Intent: does this Party already hold a Supplier (RULE-PTY-009)
Logical spec: SELECT * FROM PTY_SUPPLIER WHERE TENANT_ID = :tenantId AND PARTY_FK = :partyPk. There is at most one, by UQ_PTY_SUPPLIER_PARTY_FK (TENANT_ID, PARTY_FK).
Join: NONE · Transaction: within the caller · Locking: the caller holds the Party lock · Pagination: NO · Result: full entity · Null: none → "no Supplier"

**QR-PTY-018 — insert Supplier**
Phase: DATA-DOM · API: API-PTY-006 · Entity: ENT-PTY-003 · Operation: SAVE
Intent: persist the Supplier role of a Party
Logical spec: INSERT INTO PTY_SUPPLIER (PAYMENT_TERM_DAYS, PARTY_FK, IS_ACTIVE_FL, TENANT_ID, VERSION, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (:terms, :partyPk, TRUE, :tenantId, 0, …)
Join: NONE · Transaction: READ_WRITE · Locking: NONE — UQ_PTY_SUPPLIER_PARTY_FK guards · Pagination: NO · Result: full entity with PK · Null: paymentTermDays NULL when absent

**QR-PTY-019 — update Supplier**
Phase: DATA-DOM · API: API-PTY-008, 009, 010 · Entity: ENT-PTY-003 · Operation: UPDATE
Intent: write payment terms or the active flag of a locked Supplier
Logical spec: UPDATE PTY_SUPPLIER SET PAYMENT_TERM_DAYS | IS_ACTIVE_FL = :v, VERSION = VERSION + 1, UPDATED_BY, UPDATED_AT WHERE TENANT_ID = :tenantId AND SUPPLIER_PK = :id. PARTY_FK is never in the SET list.
Join: NONE · Transaction: READ_WRITE · Locking: row locked by QR-PTY-016 · Pagination: NO · Result: row count · Null: —

**QR-PTY-020 — search Suppliers**
Phase: SVC-API · API: API-PTY-028 · Entity: ENT-PTY-003 · Operation: FIND_BY_CRITERIA
Intent: one page of Suppliers matching the SRS B2 filters of the Suppliers screen
Logical spec:
- SELECT s.SUPPLIER_PK, s.PAYMENT_TERM_DAYS, s.IS_ACTIVE_FL, p.CODE, p.NAME_AR, p.NAME_EN, p.MOBILE, p.EMAIL, p.TAX_REGISTRATION_NO
- FROM PTY_SUPPLIER s JOIN PTY_PARTY p ON p.TENANT_ID = s.TENANT_ID AND p.PARTY_PK = s.PARTY_FK
- WHERE s.TENANT_ID = :tenantId
- AND [code LIKE] AND [name LIKE on NAME_AR / NAME_EN] AND [MOBILE LIKE]
- AND [LOWER(p.EMAIL) LIKE LOWER('%'||:email||'%')]
- AND [TAX_REGISTRATION_NO LIKE] AND [s.IS_ACTIVE_FL = :isActive]
- ORDER BY :sort LIMIT/OFFSET, plus a COUNT
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: YES (Page<T>) · Filters: code LIKE, name LIKE, mobile LIKE, email LIKE (case-insensitive — ADR-PTY-043), taxRegistrationNo LIKE, isActive EXACT · Result: projection SupplierRow · Null: empty → empty page

**QR-PTY-021 — count the active roles of a Party**
Phase: DATA-DOM · API: API-PTY-004, 009 · Entity: ENT-PTY-001 · Operation: COUNT
Intent: whether a role is still active on this Party after a deactivation (REQ-PTY-028)
Logical spec: SELECT (SELECT COUNT(*) FROM PTY_CUSTOMER WHERE TENANT_ID = :tenantId AND PARTY_FK = :partyPk AND IS_ACTIVE_FL) + (SELECT COUNT(*) FROM PTY_SUPPLIER WHERE TENANT_ID = :tenantId AND PARTY_FK = :partyPk AND IS_ACTIVE_FL). It runs after the role's own update is flushed.
Join: NONE · Transaction: READ_WRITE (inside the deactivation) · Locking: the caller holds the Party lock, so the count and the Party write it decides cannot interleave with another role change of the Party · Pagination: NO · Result: count · Null: —

**QR-PTY-022 — read Address by PK**
Phase: SVC-API · API: API-PTY-012 · Entity: ENT-PTY-004 · Operation: FIND_ONE
Intent: an Address by its identifier, inactive included (AC-PTY-039)
Logical spec: SELECT * FROM PTY_ADDRESS WHERE TENANT_ID = :tenantId AND ADDRESS_PK = :id
Join: NONE · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: full entity · Null: not found → PTY-404-ADDRESS-NOT-FOUND

**QR-PTY-023 — lock Address of a Party**
Phase: DATA-DOM · API: API-PTY-013, 014, 015 · Entity: ENT-PTY-004 · Operation: FIND_ONE
Intent: load an Address of the Party in the path for a change
Logical spec: SELECT * FROM PTY_ADDRESS WHERE TENANT_ID = :tenantId AND ADDRESS_PK = :id AND PARTY_FK = :partyPk FOR UPDATE
Join: NONE · Transaction: READ_WRITE · Locking: row lock; the Party row is always locked before its Address (CORE lock order: role row → Party → Address and requests), so there is no deadlock · Pagination: NO · Result: full entity · Null: not found or of another Party → PTY-404-ADDRESS-NOT-FOUND

**QR-PTY-024 — next Address ordinal of a Party**
Phase: DATA-DOM · API: API-PTY-011, 034 · Entity: ENT-PTY-004 · Operation: AGGREGATE
Intent: the next system-assigned Address code within the Party ("1", "2", …). This is an ordinal within one parent, not a document number (ADR-PTY-040, CORE-GAP-ordinal-within-parent).
Logical spec: SELECT COALESCE(MAX(CAST(CODE AS INTEGER)), 0) + 1 FROM PTY_ADDRESS WHERE TENANT_ID = :tenantId AND PARTY_FK = :partyPk
Join: NONE · Transaction: READ_WRITE · Locking: read-then-write. The caller holds the Party lock (QR-PTY-001), so no second create of the Party reads the same maximum; UQ_PTY_ADDRESS_PARTY_CODE (TENANT_ID, PARTY_FK, CODE) is the backstop. A deactivated Address keeps its code, so codes are never reused. · Pagination: NO · Result: integer, written as text · Null: no Address → 1

**QR-PTY-025 — insert Address**
Phase: DATA-DOM · API: API-PTY-011, 034 · Entity: ENT-PTY-004 · Operation: SAVE
Intent: persist a new Address of a Party
Logical spec: INSERT INTO PTY_ADDRESS (ADDRESS_TYPE_CODE, LINE1, LINE2, CITY, REGION, POSTAL_CODE, IS_DEFAULT_FL, PARTY_FK, NAME_AR, NAME_EN, CODE, IS_ACTIVE_FL, TENANT_ID, VERSION, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (…, :tenantId, 0, …)
Join: NONE · Transaction: READ_WRITE · Locking: NONE of its own (Party lock held) · Pagination: NO · Result: full entity with PK · Null: optional lines NULL

**QR-PTY-026 — update Address**
Phase: DATA-DOM · API: API-PTY-013, 014, 015 · Entity: ENT-PTY-004 · Operation: UPDATE
Intent: write the fields, the active flag or the default flag of a locked Address
Logical spec: UPDATE PTY_ADDRESS SET <fields> | IS_ACTIVE_FL | IS_DEFAULT_FL = :v, VERSION = VERSION + 1, UPDATED_BY, UPDATED_AT WHERE TENANT_ID = :tenantId AND ADDRESS_PK = :id. PARTY_FK and CODE are never in the SET list, and a default is cleared before another is set.
Join: NONE · Transaction: READ_WRITE · Locking: rows locked by QR-PTY-023 under the Party lock · Pagination: NO · Result: row count · Null: —

**QR-PTY-027 — list active Addresses of a Party**
Phase: SVC-API · API: API-PTY-029 · Entity: ENT-PTY-004 · Operation: FIND_ALL
Intent: the Party's active Addresses with type and default mark (REQ-PTY-032)
Logical spec: SELECT * FROM PTY_ADDRESS WHERE TENANT_ID = :tenantId AND PARTY_FK = :partyPk AND IS_ACTIVE_FL ORDER BY ADDRESS_TYPE_CODE, IS_DEFAULT_FL DESC, CODE, served by IDX_PTY_ADDRESS_TENANT
Join: NONE · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: full entities · Null: none → empty list

**QR-PTY-028 — find the default Address of a type**
Phase: DATA-DOM · API: API-PTY-015 · Entity: ENT-PTY-004 · Operation: FIND_ONE
Intent: the Party's current default for an address type, to clear it before setting another
Logical spec: SELECT * FROM PTY_ADDRESS WHERE TENANT_ID = :tenantId AND PARTY_FK = :partyPk AND ADDRESS_TYPE_CODE = :type AND IS_DEFAULT_FL AND IS_ACTIVE_FL FOR UPDATE. There is at most one, by UQ_PTY_ADDRESS_IS_DEFAULT_FL.
Join: NONE · Transaction: READ_WRITE · Locking: row lock under the Party lock · Pagination: NO · Result: full entity · Null: none → nothing to clear

**QR-PTY-029 — find the most recently updated remaining Address of a type**
Phase: DATA-DOM · API: API-PTY-013, 014 · Entity: ENT-PTY-004 · Operation: FIND_ONE
Intent: the Address that becomes default when the default of its type leaves (REQ-PTY-036)
Logical spec: SELECT * FROM PTY_ADDRESS WHERE TENANT_ID = :tenantId AND PARTY_FK = :partyPk AND ADDRESS_TYPE_CODE = :type AND IS_ACTIVE_FL AND ADDRESS_PK <> :leaving ORDER BY UPDATED_AT DESC, ADDRESS_PK DESC LIMIT 1 FOR UPDATE, served by IDX_PTY_ADDRESS_TENANT
Join: NONE · Transaction: READ_WRITE · Locking: read-then-write under the Party lock · Pagination: NO · Result: full entity · Null: none → the type keeps no default (no active Address of it remains)

**QR-PTY-030 — exists active Address of a type**
Phase: DATA-DOM · API: API-PTY-011, 013 · Entity: ENT-PTY-004 · Operation: EXISTS
Intent: whether this is the first active Address of its type on the Party (REQ-PTY-034)
Logical spec: SELECT EXISTS (SELECT 1 FROM PTY_ADDRESS WHERE TENANT_ID = :tenantId AND PARTY_FK = :partyPk AND ADDRESS_TYPE_CODE = :type AND IS_ACTIVE_FL AND (:selfPk IS NULL OR ADDRESS_PK <> :selfPk))
Join: NONE · Transaction: READ_WRITE (inside the create / update) · Locking: read-then-write under the Party lock; UQ_PTY_ADDRESS_IS_DEFAULT_FL is the backstop · Pagination: NO · Result: boolean · Null: —

**QR-PTY-031 — insert Credit override request**
Phase: DATA-DOM · API: API-PTY-019 · Entity: ENT-PTY-005 · Operation: SAVE
Intent: persist a PENDING request with the credit picture at the time of the request
Logical spec: INSERT INTO PTY_CREDIT_OVERRIDE_REQUEST (SALE_AMOUNT, CREDIT_LIMIT_AT_REQUEST, OPEN_EXPOSURE, CURRENCY_CODE, CREDIT_CHECK_RESULT_CODE, SOURCE_DOCUMENT_REF, CUSTOMER_FK, DOC_NO, DOC_DATE, STATUS_CODE, TENANT_ID, VERSION, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT) VALUES (…, :customerCurrency, …, 'PENDING', :tenantId, 0, …)
Join: NONE · Transaction: READ_WRITE · Locking: NONE. docNo comes from NumberSeriesApi; UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO (TENANT_ID, DOC_NO) and UQ_PTY_CREDIT_OVERRIDE_REQUEST_OPEN_SOURCE (TENANT_ID, CUSTOMER_FK, SOURCE_DOCUMENT_REF — open requests) are the backstops. A violation of the second is answered PTY-409-REQUEST-OPEN-FOR-SALE after QR-PTY-040 re-reads the holder (ADR-PTY-042). · Pagination: NO · Result: full entity with PK · Null: creditLimitAtRequest / openExposure NULL when absent

**QR-PTY-032 — read Credit override request**
Phase: SVC-API · API: API-PTY-020 · Entity: ENT-PTY-005 · Operation: FIND_ONE
Intent: a request with its Customer's code and names, visible to the caller
Logical spec: SELECT r.*, p.CODE, p.NAME_AR, p.NAME_EN FROM PTY_CREDIT_OVERRIDE_REQUEST r JOIN PTY_CUSTOMER c ON c.TENANT_ID = r.TENANT_ID AND c.CUSTOMER_PK = r.CUSTOMER_FK JOIN PTY_PARTY p ON p.TENANT_ID = c.TENANT_ID AND p.PARTY_PK = c.PARTY_FK WHERE r.TENANT_ID = :tenantId AND r.CREDIT_OVERRIDE_REQUEST_PK = :id [AND r.CREATED_BY = :principal when the caller lacks PERM_PTY_CREDIT_OVERRIDE_UPDATE]
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: projection (CreditOverrideRequest DTO) · Null: not found / not visible → PTY-404-CREDIT-OVERRIDE-NOT-FOUND

**QR-PTY-033 — lock Credit override request**
Phase: DATA-DOM · API: API-PTY-021, 022, 023, 036 · Entity: ENT-PTY-005 · Operation: FIND_ONE
Intent: load a request whose status is about to be decided
Logical spec: SELECT * FROM PTY_CREDIT_OVERRIDE_REQUEST WHERE TENANT_ID = :tenantId AND CREDIT_OVERRIDE_REQUEST_PK = :id FOR UPDATE
Join: NONE · Transaction: READ_WRITE · Locking: row lock — the status read here is the status the transition is decided on; a second decider waits and then reads the first decision · Pagination: NO · Result: full entity · Null: not found → PTY-404-CREDIT-OVERRIDE-NOT-FOUND

**QR-PTY-034 — conditional status update of a request**
Phase: DATA-DOM · API: API-PTY-004, 021, 022, 023, 036 · Entity: ENT-PTY-005 · Operation: UPDATE
Intent: move a request along the A7 lifecycle only from the status the decision was made on
Logical spec: UPDATE PTY_CREDIT_OVERRIDE_REQUEST SET STATUS_CODE = :new, DECIDED_BY = :decider, DECIDED_AT = :now, DECISION_REASON = :reason, USED_DOCUMENT_REF = :usedRef, VERSION = VERSION + 1, UPDATED_BY, UPDATED_AT WHERE TENANT_ID = :tenantId AND CREDIT_OVERRIDE_REQUEST_PK = :id AND STATUS_CODE = :expected
- :expected is PENDING for approve, reject and cancel; APPROVED for use; and PENDING or APPROVED for the system cancellation on Customer deactivation (REQ-PTY-111).
- DOC_NO is never in the SET list.
- The update goes through the managed entity, so the `@Audited` listener records it.
Join: NONE · Transaction: READ_WRITE · Locking: compare-and-set on STATUS_CODE in addition to the row lock; 0 rows updated → re-read and answer RULE-PTY-026 (decisions) or RULE-PTY-028 (use) · Pagination: NO · Result: row count · Null: fields not set by the transition stay as they are

**QR-PTY-035 — search Credit override requests**
Phase: SVC-API · API: API-PTY-032 · Entity: ENT-PTY-005 · Operation: FIND_BY_CRITERIA
Intent: the approver's queue (PENDING first, newest first) or the requester's own requests
Logical spec:
- SELECT r.CREDIT_OVERRIDE_REQUEST_PK, r.DOC_NO, r.DOC_DATE, r.STATUS_CODE, r.CUSTOMER_FK, r.SALE_AMOUNT, r.CREDIT_LIMIT_AT_REQUEST, r.OPEN_EXPOSURE, r.CURRENCY_CODE, r.CREDIT_CHECK_RESULT_CODE, r.CREATED_BY, p.CODE, p.NAME_AR, p.NAME_EN
- FROM PTY_CREDIT_OVERRIDE_REQUEST r JOIN PTY_CUSTOMER c ON c.TENANT_ID = r.TENANT_ID AND c.CUSTOMER_PK = r.CUSTOMER_FK JOIN PTY_PARTY p ON p.TENANT_ID = c.TENANT_ID AND p.PARTY_PK = c.PARTY_FK
- WHERE r.TENANT_ID = :tenantId
- AND [r.DOC_NO = :docNo] AND [r.STATUS_CODE = :status] AND [r.CUSTOMER_FK = :customerId]
- AND [r.DOC_DATE >= :from] AND [r.DOC_DATE < :to + 1 day]
- AND [r.CREATED_BY = :principal when the caller lacks PERM_PTY_CREDIT_OVERRIDE_UPDATE]
- ORDER BY (r.STATUS_CODE = 'PENDING') DESC, r.DOC_DATE DESC LIMIT/OFFSET, plus a COUNT
- Served by IDX_PTY_CREDIT_OVERRIDE_REQUEST_TENANT, _STATUS_CODE, _DOC_DATE, _CREATED_BY and _CUSTOMER_FK.
Join: required — ADR-PTY-027 · Transaction: READ_ONLY · Locking: NONE · Pagination: YES (Page<T>) · Filters: docNo EXACT, statusCode EXACT, customerId EXACT, docDate DATE_RANGE, createdBy EXACT (visibility) · Result: projection CreditOverrideRow · Null: empty → empty page

**QR-PTY-036 — match an active Party by email for a guest**
Phase: SVC-API · API: API-PTY-034 · Entity: ENT-PTY-001 · Operation: FIND_BY_CRITERIA
Intent: the active Party whose email equals the guest's, ignoring letter case, and never the walk-in Party (REQ-PTY-055, RULE-PTY-013)
Logical spec: SELECT p.* FROM PTY_PARTY p WHERE p.TENANT_ID = :tenantId AND p.IS_ACTIVE_FL AND LOWER(BTRIM(p.EMAIL)) = LOWER(BTRIM(:email)) AND NOT EXISTS (SELECT 1 FROM PTY_CUSTOMER w WHERE w.TENANT_ID = p.TENANT_ID AND w.PARTY_FK = p.PARTY_PK AND w.IS_WALK_IN_FL). There is at most one, by UQ_PTY_PARTY_EMAIL.
Join: NONE (correlated NOT EXISTS) · Transaction: READ_WRITE (inside the resolution) · Locking: the matched Party is then locked by QR-PTY-001; a create racing on the same email is settled by UQ_PTY_PARTY_EMAIL and a re-match (ADR-PTY-026) · Pagination: NO · Result: full entity · Null: email empty → skipped; none → mobile matching

**QR-PTY-037 — match active Parties by normalised mobile for a guest**
Phase: SVC-API · API: API-PTY-034 · Entity: ENT-PTY-001 · Operation: NATIVE
Intent: the active Parties whose normalised mobile equals the guest's; exactly one is a match and more than one is ambiguous (REQ-PTY-056, REQ-PTY-061)
Logical spec:
- SELECT p.PARTY_PK FROM PTY_PARTY p
- WHERE p.TENANT_ID = :tenantId AND p.IS_ACTIVE_FL
- AND REGEXP_REPLACE(REGEXP_REPLACE(p.MOBILE, '[ -]', '', 'g'), '^00', '+') = :normalisedMobile
- AND NOT EXISTS (SELECT 1 FROM PTY_CUSTOMER w WHERE w.TENANT_ID = :tenantId AND w.PARTY_FK = p.PARTY_PK AND w.IS_WALK_IN_FL)
- LIMIT 2
- The repository method carries `@TenantUnscoped(reason = "REGEXP_REPLACE mobile normalisation on PTY_PARTY, tenant named in the SQL", adr = "ADR-PTY-027")`.
Join: NONE · Transaction: READ_WRITE (inside the resolution) · Locking: NONE — mobile is not unique (SRS D-03); an ambiguous or racing result creates a new Party, as REQ-PTY-061 prescribes · Pagination: NO (LIMIT 2 — only "one" or "more than one" matters) · Result: projection PARTY_PK list · Null: mobile empty → skipped

**QR-PTY-038 — exists Party by PK**
Phase: DATA-DOM · API: API-PTY-029 · Entity: ENT-PTY-001 · Operation: EXISTS
Intent: distinguish "unknown Party" (404) from "Party without active Address" (empty list)
Logical spec: SELECT EXISTS (SELECT 1 FROM PTY_PARTY WHERE TENANT_ID = :tenantId AND PARTY_PK = :partyPk)
Join: NONE · Transaction: READ_ONLY · Locking: NONE · Pagination: NO · Result: boolean · Null: false → PTY-404-PARTY-NOT-FOUND

**QR-PTY-039 — lock the open requests of a Customer**
Phase: DATA-DOM · API: API-PTY-004 · Entity: ENT-PTY-005 · Operation: FIND_BY_CRITERIA
Intent: the PENDING and APPROVED requests that a Customer's deactivation cancels (REQ-PTY-111, ADR-PTY-041)
Logical spec: SELECT * FROM PTY_CREDIT_OVERRIDE_REQUEST WHERE TENANT_ID = :tenantId AND CUSTOMER_FK = :customerPk AND STATUS_CODE IN ('PENDING', 'APPROVED') ORDER BY CREDIT_OVERRIDE_REQUEST_PK FOR UPDATE, served by IDX_PTY_CREDIT_OVERRIDE_REQUEST_CUSTOMER_FK
Join: NONE · Transaction: READ_WRITE (inside the deactivation, after the Customer lock QR-PTY-006 and the Party lock QR-PTY-001 — the CORE lock order) · Locking: row locks in PK order — an approver or a use holding one of the rows commits first, and the cancellation then finds its new status through QR-PTY-034 · Pagination: NO · Result: full entities · Null: none → nothing to cancel

**QR-PTY-040 — find the open request of a sale**
Phase: DATA-DOM · API: API-PTY-019 · Entity: ENT-PTY-005 · Operation: FIND_ONE
Intent: give `CreditOverrideRequestDomain.create` the open request of the same Customer and sale (RULE-PTY-034), and name its docNo in the message
Logical spec: SELECT CREDIT_OVERRIDE_REQUEST_PK, DOC_NO FROM PTY_CREDIT_OVERRIDE_REQUEST WHERE TENANT_ID = :tenantId AND CUSTOMER_FK = :customerPk AND SOURCE_DOCUMENT_REF = :sourceDocumentRef AND STATUS_CODE IN ('PENDING', 'APPROVED'). There is at most one, by UQ_PTY_CREDIT_OVERRIDE_REQUEST_OPEN_SOURCE.
Join: NONE · Transaction: within the creation · Locking: NONE of its own — the caller holds the Customer lock (QR-PTY-006), and the unique index is the race backstop (ADR-PTY-042) · Pagination: NO · Filters: customerPk EXACT, sourceDocumentRef EXACT · Result: projection PK, DOC_NO · Null: none → no open request

## Error Catalog

The single canonical catalog. Envelope `LocalizedException → {code, messageAr, messageEn}`; every code is an instance of `{MOD}-{http}[-{SLUG}]`; every `http` is one of 200, 201, 400, 401, 403, 404, 409, 413, 415, 422, 500. Messages of rule rows are copied character-perfect from the SRS; PLATFORM-STD rows carry ADR-PTY-024. Downstream consumers cite the code, not the text. No row describes a network failure — the platform is one deployable.

```yaml name=error-catalog
rows:
  - {code: "PTY-422-NAME-REQUIRED", rule: RULE-PTY-001, api: [API-PTY-001, API-PTY-003, API-PTY-006, API-PTY-008, API-PTY-034], http: 422, trigger: "a Party create or update carries neither nameAr nor nameEn", messages: {ar: "أدخل اسم الطرف بالعربية أو بالإنجليزية.", en: "Enter the party name in Arabic or English."}}
  - {code: "PTY-409-EMAIL-TAKEN", rule: RULE-PTY-002, api: [API-PTY-001, API-PTY-003, API-PTY-005, API-PTY-006, API-PTY-008, API-PTY-010], http: 409, trigger: "the email is held by another active Party (create, update of email, reactivation of a role)", messages: {ar: "هذا البريد الإلكتروني مسجّل لطرف نشط آخر ({code}). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.", en: "This email is already registered to another active party ({code}). Open that party instead of creating a new one."}}
  - {code: "PTY-422-EMAIL-FORMAT", rule: RULE-PTY-003, api: [API-PTY-001, API-PTY-003, API-PTY-006, API-PTY-008, API-PTY-034], http: 422, trigger: "a non-empty email is not one '@' with a non-empty local part and a '.' in the domain part", messages: {ar: "صيغة البريد الإلكتروني غير صحيحة، مثال: name@example.com", en: "The email format is not valid, for example: name@example.com"}}
  - {code: "PTY-422-MOBILE-FORMAT", rule: RULE-PTY-004, api: [API-PTY-001, API-PTY-003, API-PTY-006, API-PTY-008, API-PTY-034], http: 422, trigger: "a non-empty mobile, spaces and dashes removed, is not an optional '+' and 7 to 15 digits", messages: {ar: "رقم الجوال يتكون من 7 إلى 15 رقماً، ويمكن أن يبدأ بعلامة +.", en: "The mobile number has 7 to 15 digits and can start with +."}}
  - {code: "PTY-422-ROLE-INACTIVE", rule: RULE-PTY-005, api: [API-PTY-002, API-PTY-007, API-PTY-011, API-PTY-013, API-PTY-015, API-PTY-017, API-PTY-019, API-PTY-035, API-PTY-036], http: 422, trigger: "a Customer or Supplier read with forNewDocument = true is inactive; a credit check, a credit save, or the creation or use of a Credit override request targets an inactive Customer; an Address create, update or set-default targets an inactive Party", messages: {ar: "{code} غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.", en: "{code} is inactive and cannot be used on a new document. Reactivate it or choose another party."}}
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
  - {code: "PTY-422-CREDIT-NOT-ENABLED", rule: RULE-PTY-023, api: [API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-021, API-PTY-022, API-PTY-030, API-PTY-031, API-PTY-036], http: 422, trigger: "credit data is read or written, or a Credit override request is submitted, approved, rejected or used, while b2bCredit is off", messages: {ar: "الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.", en: "Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms."}}
  - {code: "PTY-422-OVERRIDE-NOT-NEEDED", rule: RULE-PTY-025, api: [API-PTY-019], http: 422, trigger: "the server-side credit check of the request answers WITHIN_LIMIT", messages: {ar: "لا حاجة إلى موافقة: البيع ضمن حد ائتمان العميل.", en: "No approval is needed: the sale is within the customer's credit limit."}}
  - {code: "PTY-422-OVERRIDE-NOT-REQUIRED", rule: RULE-PTY-035, api: [API-PTY-019], http: 422, trigger: "a Credit override request is submitted while the tenant's credit-overrun action is not BLOCK_WITH_OVERRIDE (WARN); checked after the server-side credit check", messages: {ar: "لا حاجة إلى موافقة: إعداد تجاوز الائتمان هو التنبيه فقط، ويمكن إتمام البيع دون طلب.", en: "No approval is needed: the credit-overrun setting is Warn only, so the sale can be completed without a request."}, adr: ADR-PTY-045}
  - {code: "PTY-409-REQUEST-NOT-PENDING", rule: RULE-PTY-026, api: [API-PTY-021, API-PTY-022, API-PTY-023], http: 409, trigger: "approve, reject or cancel of a request whose statusCode is not PENDING", messages: {ar: "هذا الطلب لم يعد معلّقاً (الحالة: {statusAr}).", en: "This request is no longer pending (status: {statusEn})."}}
  - {code: "PTY-422-REJECTION-REASON-REQUIRED", rule: RULE-PTY-027, api: [API-PTY-022], http: 422, trigger: "a rejection carries no decisionReason", messages: {ar: "أدخل سبب الرفض.", en: "Enter the reason for the rejection."}}
  - {code: "PTY-409-REQUEST-OPEN-FOR-SALE", rule: RULE-PTY-034, api: [API-PTY-019], http: 409, trigger: "a Credit override request is submitted for a Customer and sale document reference that already have a PENDING or APPROVED request", messages: {ar: "لهذا البيع طلب موافقة مفتوح بالفعل ({docNo}). تابع ذلك الطلب بدلاً من إنشاء طلب جديد.", en: "This sale already has an open approval request ({docNo}). Follow that request instead of creating a new one."}}
  - {code: "PTY-422-APPROVAL-NOT-COVERING", rule: RULE-PTY-028, api: [API-PTY-036], http: 422, trigger: "a use of a request that is not APPROVED, is for another Customer, carries another sale document reference than sourceDocumentRef, or covers a smaller saleAmount than the sale", messages: {ar: "الموافقة {docNo} لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.", en: "Approval {docNo} does not cover this sale. Check the customer and the amount, or request a new approval."}}
  - {code: "PTY-403-NOT-REQUESTER", rule: RULE-PTY-029, api: [API-PTY-023], http: 403, trigger: "a user other than the request's creator cancels it", messages: {ar: "لا يلغي هذا الطلب إلا من قدّمه.", en: "Only the requester can cancel this request."}}
  - {code: "PTY-500-AUDIT-NOT-RECORDED", rule: RULE-PTY-030, api: [API-PTY-017, API-PTY-018, API-PTY-021, API-PTY-022], http: 500, trigger: "the core audit module cannot record a creditLimit or paymentTermDays change, or an approval or rejection; the change or the decision is rolled back", messages: {ar: "تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.", en: "The change could not be recorded in the audit history, so it was not saved. Try again."}}
  - {code: "PTY-422-DOC-NO-READ-ONLY", rule: RULE-PTY-031, api: [API-PTY-021, API-PTY-022, API-PTY-036], http: 422, trigger: "a request change carries a docNo different from the stored one", messages: {ar: "رقم الطلب يُنشأ تلقائياً ولا يمكن تعديله.", en: "The request number is assigned by the system and cannot be changed."}}
  - {code: "PTY-422-SALE-AMOUNT-NOT-POSITIVE", rule: RULE-PTY-032, api: [API-PTY-019], http: 422, trigger: "a Credit override request carries a saleAmount of 0 or less", messages: {ar: "مبلغ البيع يجب أن يكون أكبر من صفر.", en: "The sale amount must be greater than zero."}}
  - {code: "PTY-422-WALK-IN-NO-CREDIT-SALE", rule: RULE-PTY-033, api: [API-PTY-019], http: 422, trigger: "a Credit override request is submitted for the walk-in Customer", messages: {ar: "لا يمكن البيع الآجل للعميل النقدي. سجّل العميل باسمه أولاً.", en: "The walk-in customer cannot buy on credit. Register the customer by name first."}}
  - {code: "PTY-400-VALIDATION", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037, API-PTY-038], http: 400, trigger: "the request is malformed: unreadable body, wrong type, missing required parameter, length or value outside the declared schema", messages: {ar: "الطلب غير صالح: تحقّق من الحقول المرسلة.", en: "The request is not valid: check the submitted fields."}, adr: ADR-PTY-024}
  - {code: "PTY-401-UNAUTHENTICATED", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037, API-PTY-038], http: 401, trigger: "no valid bearer token", messages: {ar: "سجّل الدخول للمتابعة.", en: "Sign in to continue."}, adr: ADR-PTY-024}
  - {code: "PTY-403-FORBIDDEN", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037, API-PTY-038], http: 403, trigger: "the caller lacks the PERM_* the endpoint requires (platform authorisation layer); API-PTY-027 answers an empty list instead (REQ-PTY-043)", messages: {ar: "ليست لديك صلاحية لتنفيذ هذا الإجراء.", en: "You do not have permission to perform this action."}, adr: ADR-PTY-024}
  - {code: "PTY-404-CUSTOMER-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-031, API-PTY-035], http: 404, trigger: "no Customer with the given identifier in the caller's tenant", messages: {ar: "العميل غير موجود.", en: "The customer was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-404-SUPPLIER-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010], http: 404, trigger: "no Supplier with the given identifier in the caller's tenant", messages: {ar: "المورّد غير موجود.", en: "The supplier was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-404-PARTY-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-006, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-029], http: 404, trigger: "no Party with the given identifier (partyId) in the caller's tenant", messages: {ar: "الطرف غير موجود.", en: "The party was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-404-ADDRESS-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015], http: 404, trigger: "no Address with the given identifier (of the Party in the path); set-default on an inactive Address", messages: {ar: "العنوان غير موجود.", en: "The address was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-404-CREDIT-OVERRIDE-NOT-FOUND", rule: PLATFORM-STD, api: [API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-036], http: 404, trigger: "no Credit override request with the given identifier visible to the caller", messages: {ar: "طلب تجاوز حد الائتمان غير موجود.", en: "The credit override request was not found."}, adr: ADR-PTY-024}
  - {code: "PTY-404-LOOKUP-KEY-UNKNOWN", rule: PLATFORM-STD, api: [API-PTY-038], http: 404, trigger: "the requested key is not one of the five PTY lookup keys, or mdl answers 404 for it (OwnedLookups translates mdl's 404 to this code)", messages: {ar: "هذه القائمة ليست من قوائم الأطراف. اختر إحدى قوائم الأطراف.", en: "This list is not one of the party lists. Choose a party list."}, adr: ADR-PTY-048}
  - {code: "PTY-500-INTERNAL", rule: PLATFORM-STD, api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037, API-PTY-038], http: 500, trigger: "an unexpected server-side failure", messages: {ar: "حدث خطأ غير متوقع. أعد المحاولة.", en: "An unexpected error occurred. Try again."}, adr: ADR-PTY-024}
```

Rules with no catalog row, and why:
- RULE-PTY-006 and RULE-PTY-017: confirmations returned on success.
- RULE-PTY-024: the answer to the flag owner (API-PTY-037).
- RULE-PTY-013 and RULE-PTY-020: outcomes of the invitation outbox subscriber, recorded through AuditApi, with no HTTP caller.
- RULE-PTY-014: an idempotent ensure never surfaces it. No RULE is DEFERRED, so none is missing for that reason.
