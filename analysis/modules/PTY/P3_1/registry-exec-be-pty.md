## REGISTRY — P3.1 — PTY v1

Source artifact : analysis/modules/PTY/P3_1/backend-execution-plan-pty.md · API document : analysis/modules/PTY/P3_1/api-spec-pty.yaml · Date : 2026-10-29 · Profile : commerce · Track : backend · Plan : exec

### ID ranges
| Kind | Range | Count | Next free |
|---|---|---|---|
| API | API-PTY-001 … API-PTY-037 | 37 | API-PTY-038 |
| QR | QR-PTY-001 … QR-PTY-040 | 40 | QR-PTY-041 |

API ids : API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037

QR ids : QR-PTY-001, QR-PTY-002, QR-PTY-003, QR-PTY-004, QR-PTY-005, QR-PTY-006, QR-PTY-007, QR-PTY-008, QR-PTY-009, QR-PTY-010, QR-PTY-011, QR-PTY-012, QR-PTY-013, QR-PTY-014, QR-PTY-015, QR-PTY-016, QR-PTY-017, QR-PTY-018, QR-PTY-019, QR-PTY-020, QR-PTY-021, QR-PTY-022, QR-PTY-023, QR-PTY-024, QR-PTY-025, QR-PTY-026, QR-PTY-027, QR-PTY-028, QR-PTY-029, QR-PTY-030, QR-PTY-031, QR-PTY-032, QR-PTY-033, QR-PTY-034, QR-PTY-035, QR-PTY-036, QR-PTY-037, QR-PTY-038, QR-PTY-039, QR-PTY-040

### API index
| API | Verb | Path | ENT · operation | Permission | QR | Honours |
|---|---|---|---|---|---|---|
| API-PTY-001 | POST | /api/v1/pty/customers | ENT-PTY-002, ENT-PTY-001 · CREATE | PERM_PTY_CUSTOMER_CREATE | QR-PTY-001, 002, 003, 004, 007, 008 | CON-PTY-023, CON-PTY-002, CON-PTY-001, CON-PTY-006 |
| API-PTY-002 | GET | /api/v1/pty/customers/{id} | ENT-PTY-002, ENT-PTY-001 · VIEW | PERM_PTY_CUSTOMER_VIEW | QR-PTY-005 | CON-PTY-009, CON-PTY-002, CON-PTY-001 |
| API-PTY-003 | PUT | /api/v1/pty/customers/{id} | ENT-PTY-002, ENT-PTY-001 · UPDATE | PERM_PTY_CUSTOMER_UPDATE | QR-PTY-006, 001, 004, 003, 009 | CON-PTY-002, CON-PTY-001, CON-PTY-006 |
| API-PTY-004 | DELETE | /api/v1/pty/customers/{id} | ENT-PTY-002, ENT-PTY-005 · DELETE | PERM_PTY_CUSTOMER_DELETE | QR-PTY-006, 009, 039, 034, 001, 021, 003 | CON-PTY-002, CON-PTY-021, CON-PTY-005 |
| API-PTY-005 | PATCH | /api/v1/pty/customers/{id}/activation | ENT-PTY-002 · DELETE (reverse) | PERM_PTY_CUSTOMER_DELETE | QR-PTY-006, 001, 004, 003, 009 | CON-PTY-002 |
| API-PTY-006 | POST | /api/v1/pty/suppliers | ENT-PTY-003, ENT-PTY-001 · CREATE | PERM_PTY_SUPPLIER_CREATE | QR-PTY-001, 002, 003, 004, 007, 017, 018 | CON-PTY-024, CON-PTY-003, CON-PTY-001 |
| API-PTY-007 | GET | /api/v1/pty/suppliers/{id} | ENT-PTY-003, ENT-PTY-001 · VIEW | PERM_PTY_SUPPLIER_VIEW | QR-PTY-015 | CON-PTY-010, CON-PTY-003 |
| API-PTY-008 | PUT | /api/v1/pty/suppliers/{id} | ENT-PTY-003, ENT-PTY-001 · UPDATE | PERM_PTY_SUPPLIER_UPDATE | QR-PTY-016, 001, 004, 003, 019 | CON-PTY-003, CON-PTY-001 |
| API-PTY-009 | DELETE | /api/v1/pty/suppliers/{id} | ENT-PTY-003 · DELETE | PERM_PTY_SUPPLIER_DELETE | QR-PTY-016, 019, 001, 021, 003 | CON-PTY-003, CON-PTY-021 |
| API-PTY-010 | PATCH | /api/v1/pty/suppliers/{id}/activation | ENT-PTY-003 · DELETE (reverse) | PERM_PTY_SUPPLIER_DELETE | QR-PTY-016, 001, 004, 003, 019 | CON-PTY-003 |
| API-PTY-011 | POST | /api/v1/pty/parties/{partyId}/addresses | ENT-PTY-004, ENT-PTY-001 · CREATE | PERM_PTY_CUSTOMER_UPDATE / PERM_PTY_SUPPLIER_UPDATE — either, of a role the Party holds (ADR-PTY-039) | QR-PTY-001, 024, 030, 025 | CON-PTY-004, CON-PTY-007 |
| API-PTY-012 | GET | /api/v1/pty/addresses/{id} | ENT-PTY-004 · VIEW | PERM_PTY_CUSTOMER_VIEW / PERM_PTY_SUPPLIER_VIEW — either, of a role the Party holds | QR-PTY-022 | CON-PTY-012, CON-PTY-004 |
| API-PTY-013 | PUT | /api/v1/pty/parties/{partyId}/addresses/{id} | ENT-PTY-004 · UPDATE | PERM_PTY_CUSTOMER_UPDATE / PERM_PTY_SUPPLIER_UPDATE — either, of a role the Party holds | QR-PTY-001, 023, 029, 030, 026 | CON-PTY-004, CON-PTY-007 |
| API-PTY-014 | DELETE | /api/v1/pty/parties/{partyId}/addresses/{id} | ENT-PTY-004 · DELETE | PERM_PTY_CUSTOMER_UPDATE / PERM_PTY_SUPPLIER_UPDATE — either, of a role the Party holds | QR-PTY-001, 023, 029, 026 | CON-PTY-004 |
| API-PTY-015 | PATCH | /api/v1/pty/parties/{partyId}/addresses/{id}/default | ENT-PTY-004 · UPDATE | PERM_PTY_CUSTOMER_UPDATE / PERM_PTY_SUPPLIER_UPDATE — either, of a role the Party holds | QR-PTY-001, 023, 028, 026 | CON-PTY-004 |
| API-PTY-016 | GET | /api/v1/pty/customer-credits/{customerId} | ENT-PTY-002, ENT-PTY-001 · VIEW | PERM_PTY_CUSTOMER_CREDIT_VIEW | QR-PTY-005 | CON-PTY-002, CON-PTY-022 |
| API-PTY-017 | PUT | /api/v1/pty/customer-credits/{customerId} | ENT-PTY-002 · UPDATE | PERM_PTY_CUSTOMER_CREDIT_UPDATE | QR-PTY-006, 009 | CON-PTY-002 |
| API-PTY-018 | DELETE | /api/v1/pty/customer-credits/{customerId} | ENT-PTY-002 · UPDATE | PERM_PTY_CUSTOMER_CREDIT_UPDATE | QR-PTY-006, 009 | CON-PTY-002 |
| API-PTY-019 | POST | /api/v1/pty/credit-overrides | ENT-PTY-005, ENT-PTY-002 · CREATE | PERM_PTY_CREDIT_OVERRIDE_CREATE | QR-PTY-006, 005, 040, 031 | CON-PTY-016, CON-PTY-005, CON-PTY-008 |
| API-PTY-020 | GET | /api/v1/pty/credit-overrides/{id} | ENT-PTY-005 · VIEW | PERM_PTY_CREDIT_OVERRIDE_VIEW | QR-PTY-032 | CON-PTY-017, CON-PTY-005 |
| API-PTY-021 | PATCH | /api/v1/pty/credit-overrides/{id}/approval | ENT-PTY-005 · UPDATE | PERM_PTY_CREDIT_OVERRIDE_UPDATE | QR-PTY-033, 034 | CON-PTY-005, CON-PTY-008 |
| API-PTY-022 | PATCH | /api/v1/pty/credit-overrides/{id}/rejection | ENT-PTY-005 · UPDATE | PERM_PTY_CREDIT_OVERRIDE_UPDATE | QR-PTY-033, 034 | CON-PTY-005, CON-PTY-008 |
| API-PTY-023 | DELETE | /api/v1/pty/credit-overrides/{id} | ENT-PTY-005 · DELETE | PERM_PTY_CREDIT_OVERRIDE_DELETE | QR-PTY-033, 034 | CON-PTY-005 |
| API-PTY-024 | GET | /api/v1/pty/credit-settings | ENT-PTY-002 · VIEW | PERM_PTY_CREDIT_SETTINGS_VIEW | — (given cu setting) | CON-PTY-008 |
| API-PTY-025 | PUT | /api/v1/pty/credit-settings | ENT-PTY-002 · UPDATE | PERM_PTY_CREDIT_SETTINGS_UPDATE | — (given cu setting) | CON-PTY-008 |
| API-PTY-026 | GET | /api/v1/pty/customers | ENT-PTY-002, ENT-PTY-001 · VIEW | PERM_PTY_CUSTOMER_VIEW | QR-PTY-010 | CON-PTY-002, CON-PTY-006 |
| API-PTY-027 | GET | /api/v1/pty/customers/global-search | ENT-PTY-002, ENT-PTY-001 · VIEW | PERM_PTY_CUSTOMER_VIEW (checked in code — `isAuthenticated()` on the method, ADR-PTY-039) | QR-PTY-011 | CON-PTY-020 |
| API-PTY-028 | GET | /api/v1/pty/suppliers | ENT-PTY-003, ENT-PTY-001 · VIEW | PERM_PTY_SUPPLIER_VIEW | QR-PTY-020 | CON-PTY-003 |
| API-PTY-029 | GET | /api/v1/pty/parties/{partyId}/addresses | ENT-PTY-004 · VIEW | PERM_PTY_CUSTOMER_VIEW / PERM_PTY_SUPPLIER_VIEW | QR-PTY-038, 027 | CON-PTY-011, CON-PTY-004 |
| API-PTY-030 | GET | /api/v1/pty/customer-credits | ENT-PTY-002, ENT-PTY-001 · VIEW | PERM_PTY_CUSTOMER_CREDIT_VIEW | QR-PTY-013 | CON-PTY-002 |
| API-PTY-031 | GET | /api/v1/pty/customer-credits/{customerId}/history | ENT-PTY-002 · VIEW | PERM_PTY_CUSTOMER_CREDIT_VIEW | QR-PTY-005 | CON-PTY-002 |
| API-PTY-032 | GET | /api/v1/pty/credit-overrides | ENT-PTY-005, ENT-PTY-002 · VIEW | PERM_PTY_CREDIT_OVERRIDE_VIEW | QR-PTY-035 | CON-PTY-005 |
| API-PTY-033 | GET | /api/v1/pty/customers/walk-in | ENT-PTY-002 · VIEW | PERM_PTY_CUSTOMER_VIEW | QR-PTY-012, 002, 008 | CON-PTY-013, CON-PTY-002 |
| API-PTY-034 | POST | /api/v1/pty/guest-customers | ENT-PTY-002, ENT-PTY-001, ENT-PTY-004 · CREATE | PERM_PTY_CUSTOMER_CREATE | QR-PTY-036, 037, 001, 007, 002, 008, 024, 025 | CON-PTY-014, CON-PTY-002, CON-PTY-007 |
| API-PTY-035 | POST | /api/v1/pty/credit-checks | ENT-PTY-002 · VIEW | PERM_PTY_CUSTOMER_VIEW | QR-PTY-005 | CON-PTY-015, CON-PTY-022, CON-PTY-008 |
| API-PTY-036 | PATCH | /api/v1/pty/credit-overrides/{id}/use | ENT-PTY-005 · UPDATE | PERM_PTY_CREDIT_OVERRIDE_CREATE | QR-PTY-033, 005, 034 | CON-PTY-018, CON-PTY-005 |
| API-PTY-037 | GET | /api/v1/pty/flag-dependencies/b2bCredit | ENT-PTY-002 · VIEW | PERM_PTY_CREDIT_SETTINGS_VIEW | QR-PTY-014 | CON-PTY-019 |

### Contract honoured (CON-PTY → API)
| CON | Item | Honoured by |
|---|---|---|
| CON-PTY-001 | Party (entity) | API-PTY-001, 002, 003, 006, 008 |
| CON-PTY-002 | Customer (entity) | API-PTY-001 … 005, 016 … 018, 026, 030, 031, 033, 034 |
| CON-PTY-003 | Supplier (entity) | API-PTY-006 … 010, 028 |
| CON-PTY-004 | Address (entity) | API-PTY-011 … 015, 029 |
| CON-PTY-005 | Credit override request (entity) | API-PTY-004 (system cancellation), 019 … 023, 032, 036 |
| CON-PTY-006 | Customer group lookup | API-PTY-001, 003, 026 |
| CON-PTY-007 | Address type lookup | API-PTY-011, 013, 034 |
| CON-PTY-008 | Credit control codes | API-PTY-019, 021, 022, 024, 025, 035 |
| CON-PTY-009 | Read Customer | API-PTY-002 |
| CON-PTY-010 | Read Supplier | API-PTY-007 |
| CON-PTY-011 | Read Party addresses | API-PTY-029 |
| CON-PTY-012 | Read Address | API-PTY-012 |
| CON-PTY-013 | Default Customer for a POS sale | API-PTY-033 |
| CON-PTY-014 | Guest order customer resolution | API-PTY-034 |
| CON-PTY-015 | Credit check | API-PTY-035 |
| CON-PTY-016 | Create Credit override request | API-PTY-019 |
| CON-PTY-017 | Read Credit override request | API-PTY-020 |
| CON-PTY-018 | Use Credit override request | API-PTY-036 |
| CON-PTY-019 | b2bCredit switch-off dependency | API-PTY-037 |
| CON-PTY-020 | Customer global search | API-PTY-027 |
| CON-PTY-021 | Party role deactivated event | API-PTY-004, 009 (`OutboxPublisher.publish("pty.PartyRoleDeactivated", …)` inside the transaction — ADR-PTY-038) |
| CON-PTY-022 | Customer credit exposure extension point | API-PTY-016, 035 (`CustomerCreditExposureContributor`) |
| CON-PTY-023 | Register Customer in place | API-PTY-001 |
| CON-PTY-024 | Register Supplier in place | API-PTY-006 |

### Entities / tables bound
| ENT | Table | DBF range | Business code |
|---|---|---|---|
| ENT-PTY-001 | PTY_PARTY | DBF-PTY-001 … 012, 068, 073 | code — series PTY_PARTY (`P-` + 6 digits, seeded by the migration), UQ_PTY_PARTY_CODE (TENANT_ID, CODE) |
| ENT-PTY-002 | PTY_CUSTOMER | DBF-PTY-013 … 024, 069, 074, 078 | presents the Party code |
| ENT-PTY-003 | PTY_SUPPLIER | DBF-PTY-025 … 032, 070, 075 | presents the Party code |
| ENT-PTY-004 | PTY_ADDRESS | DBF-PTY-033 … 049, 071, 076 | ordinal code within the Party (ADR-PTY-040) |
| ENT-PTY-005 | PTY_CREDIT_OVERRIDE_REQUEST | DBF-PTY-050 … 067, 072, 077, 079 | docNo — series PTY_CREDIT_OVERRIDE (`CO-` + 6 digits, seeded by the migration), UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO (TENANT_ID, DOC_NO) |

Every entity extends the given `AuditableEntity` and has a Domain companion that owns its RULEs (`PartyDomain`, `CustomerDomain`, `SupplierDomain`, `AddressDomain`, `CreditOverrideRequestDomain`); `@Audited`: PTY_CUSTOMER (credit fields), PTY_CREDIT_OVERRIDE_REQUEST.

Lookups reused (owned by PTY, seeded by the db-script BLOCK 8 into the given mdl) : CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT, CREDIT_OVERRIDE_STATUS · new lookup keys : none.
Settings keys (given cu) : PTY_CREDIT_OVERRUN_ACTION — not seeded, default BLOCK_WITH_OVERRIDE (ADR-PTY-029) · PTY_BASE_CURRENCY_CODE — set per tenant at setup, `XXX` while unset (ADR-PTY-037).
Seeded by the migration V1001 (TENANT_ID = 1) : 5 lookup keys / 16 values, number series PTY_PARTY and PTY_CREDIT_OVERRIDE, notification template PTY_CREDIT_OVERRIDE_REQUESTED (ADR-PTY-036).

### Integration
XM : none — the db-script register is `records: []`; this stage mints none. `INT-C` (last phase) carries no block; requires per edge : —.
Published in-process surface (inbound only) : `com.erp.pty.crossmodule.PtyPartyApi` (callers hold the PTY authority — ADR-PTY-039), `CustomerCreditExposureContributor` (CON-PTY-022), outbox event `pty.PartyRoleDeactivated` (CON-PTY-021, ADR-PTY-038).
Given event consumed : `sec.CustomerInvitationRedeemed` (outbox subscriber `PtyCustomerInvitationRedeemedSubscriber`) — given, no XM edge.
Module manifest : `META-INF/erp/modules/pty.yaml` · boundary test `PtyModuleBoundaryTest` (ModuleBoundaryRules) · CI job `module-pty`.

### Catalog
Code count : 37 (28 rule rows · 9 PLATFORM-STD rows, ADR-PTY-024) · format `{MOD}-{http}[-{SLUG}]` · statuses used : 400, 401, 403, 404, 409, 422, 500 · new in the gate-analysis revise : PTY-409-REQUEST-OPEN-FOR-SALE (RULE-PTY-034); PTY-422-ROLE-INACTIVE, PTY-422-CREDIT-NOT-ENABLED, PTY-500-AUDIT-NOT-RECORDED raised by more endpoints (ADR-PTY-041).
Rules without a catalog row (by design, not missing text) : RULE-PTY-006, RULE-PTY-017 (confirmations), RULE-PTY-024 (answer to the flag owner), RULE-PTY-013, RULE-PTY-020 (invitation-subscriber outcomes, recorded through AuditApi), RULE-PTY-014 (idempotent ensure). Rules without a message : none. DEFERRED rules : none.

### API document
api-spec-pty.yaml (OpenAPI 3.1.0) · operations 37 = API blocks 37 · error rows answered 37 = catalog rows 37 · paginated operations 5 (API-PTY-026, 028, 030, 031, 032 — `page`, `size`) · security : http bearer JWT, document-level.

### Security
5 pages (PTY_CUSTOMER, PTY_SUPPLIER, PTY_CUSTOMER_CREDIT, PTY_CREDIT_OVERRIDE, PTY_CREDIT_SETTINGS) · 16 permission names used, each with its role grant in the `bootstrap` block · roles : Owner, Seller/Cashier, Warehouse · every service method carries `@PreAuthorize` with a `PtyPermissions` constant; in-process callers hold it (ADR-PTY-039).

### ALIGN
Verdict : as stamped by the orchestrator from `gov.py analyze` (the plan's `self-check` block is a placeholder) · findings fixed in repair round 1 : 15 (C7.13 ×10 — P2 ADR files filed, ADR-PTY-030; C7.31 ×3 — typed PATCH bodies, ADR-PTY-031; C7.11 — format stated once, verbatim; C7.18 — XM total, ADR-PTY-032).

### ADRs
| ADR | Title | Status |
|---|---|---|
| decisions/PTY/ADR-PTY-023 | ADR sequence continued after the db-script's recorded decisions | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-024 | Error Catalog — PLATFORM-STD rows, status mapping, slugs | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-025 | Permissions the SRS leaves open; in-process callers; invitation-handler outcomes | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-026 | Concurrency guards; Address default on a type change | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-027 | Query strategy — Party join, native normalised search and matching | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-028 | Credit computation and derived properties | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-029 | Bootstrap — number series, setting key, role grants, walk-in provisioning | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-030 | P2 decisions ADR-PTY-018 … ADR-PTY-022 filed from the db-script §5.2 | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-031 | Typed bodies for the reactivation and set-default PATCH endpoints | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-032 | The XM total counts the XM ids the db-script names | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-035 | Tenant columns, version column, tenant-composite uniqueness (supersedes ADR-PTY-021) | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-036 | Number series and notification template seeded by the migration (revises ADR-PTY-029) | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-037 | currencyCode on every credit amount (supersedes ADR-PTY-020) | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-038 | `pty.PartyRoleDeactivated` on the given outbox | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-039 | Service-method authority; in-process callers hold it (revises ADR-PTY-025) | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-040 | Address ordinal within the Party under the Party lock | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-041 | Inactive Customer / Party; open requests on deactivation and while the flag is off | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-042 | One open request per sale; use bound to the source sale | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-043 | The Party of PTY-409-EMAIL-TAKEN found through the role searches by email | ACCEPTED (non-breaking) |
| decisions/PTY/CORE-GAP-in-process-caller-authority | How a caller with no PTY staff permission reaches a PTY service method | OPEN (question to erp-core) |
| decisions/PTY/CORE-GAP-tenant-base-currency | Which given interface answers a tenant's base currency | OPEN (question to erp-core) |
| decisions/PTY/CORE-GAP-ordinal-within-parent | Whether a per-parent ordinal is outside F6, or which facility assigns it | OPEN (question to erp-core) |

Filed from the db-script §5.2 (P2, text verbatim) : decisions/PTY/ADR-PTY-018 … ADR-PTY-022 — ACCEPTED (non-breaking).

BLOCKED : none. Next free ADR : ADR-PTY-044.

### Traceability
REQ covered by ≥ 1 API or DBF : 113 / 113 — 111 by `API-*` traces; REQ-PTY-052 and REQ-PTY-054 by DBF-PTY-017 (db-script) and the invitation outbox subscriber of SVC-API-INT · orphan REQ : none.
QR reached by ≥ 1 API : 40 / 40 · orphan QR : none.

### Event
"P3.1 completed: PTY v1 — API 37 · QR 38 · integration edges 0 · catalog 36 · ADR 10 (023–032), 0 BLOCKED"
"gate-analysis revise: PTY v1 — API 37 · QR 40 · catalog 37 · DBF 79 · ADR 035–043 · CORE-GAP 3 (OPEN), 0 BLOCKED"
