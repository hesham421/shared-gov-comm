# CONTRACT — Party / الأطراف (PTY) — outbound promise
══════════════════════════════════════════════════════════════════
Module : PTY   Version : v1   Profile : commerce   Stage : P1.5
Inputs : srs (analysis/modules/PTY/P1/srs-pty.md, P1 commit f0653ef), registry-srs
Counts : CON 24 (ENTITY 5 · LOOKUP 3 · OPERATION 14 · EVENT 1 · EXTENSION POINT 1) · new ADR 0
Status : DRAFT — P1.5 pass 1 · gate-analysis revise (ADR-PTY-037, 038, 039, 041, 042 — additive items and error meanings; no consumer is built yet)
══════════════════════════════════════════════════════════════════

## 0 — Scope of this promise

This contract states what other modules may depend on before Party is built. It promises;
it never implements. The backend stage (P3.1) chooses paths and verbs and states
`Honours: CON-PTY-…` on every block that implements an item.

| Rule | Applied here |
|---|---|
| Identifiers | Every entity identifier is a BIGINT identity key the database assigns (`stack.db.pk_generation = identity`). A consumer's HARD-FK column to a PTY entity is `BIGINT`, copied verbatim. No other identifier type exists in v1. |
| Business keys | Party `code` (from given `sequence`, read-only after first save — RULE-PTY-007) is the business key Customer and Supplier present; Credit override request `docNo` (from given `sequence`, read-only — RULE-PTY-031). |
| Capability flag | Credit items read `b2bCredit` through the FeatureFlags API only; while it is off, credit fields are omitted and credit operations answer as stated per item (REQ-PTY-070 … REQ-PTY-074). |
| Currency | Every amount is in the tenant's base currency (SRS A2 scope exception: no multi-currency party balances). Each credit amount carries the code of that currency (`currencyCode`, ISO 4217 — ADR-PTY-037); a consumer converts its own amounts into it before a credit check, a request or a use. |
| Authority | Every operation item is served by a PTY service method that checks its own PTY permission; an in-process caller's security context must hold it (ADR-PTY-039). |
| Errors | Named by meaning; each carries the bilingual message of the cited RULE. Access refusals come from the platform authorisation layer ("forbidden"). |
| Not promised | Party create/update by consumers other than registration in place, Customer/Supplier search and maintenance screens, address maintenance, Customer credit maintenance, credit history, credit-overrun setting, approve / reject / cancel of Credit override requests, CUSTOMER-realm invitation handling — these are internal to PTY (SCR-REQ-PTY-001 … SCR-REQ-PTY-005). |

### Consumer index (from SRS A3 Ownership and A8.1)

| Consumer | Items it may depend on |
|---|---|
| SLS — Sales & POS | CON-PTY-001, 002, 004, 005, 006, 007, 008, 009, 011, 012, 013, 015, 016, 017, 018, 020, 021, 022, 023 |
| PUR — Purchasing | CON-PTY-001, 003, 004, 007, 010, 011, 012, 021, 024 |
| STL — Settlements | CON-PTY-001, 002, 003, 009, 010, 021, 022 |
| TAX — Tax & E-invoicing | CON-PTY-001, 002, 003, 009, 010 |
| SFR — Storefront | CON-PTY-001, 002, 004, 005, 007, 008, 009, 011, 012, 014, 015, 016, 017, 018, 021 |
| PRC — Pricing | CON-PTY-002, 006, 009 |
| Capability-flag owner (OPEN — registry OI-05) | CON-PTY-019 |

Party sits at DAG #4 with `consumes: []` (SRS A8); every consumer above sits below it, so
every edge points into PTY.

---

# PART 1 — ENTITY ITEMS

### CON-PTY-001 — الطرف / Party (ar / en)

Entity    : ENT-PTY-001 — key columns: code (business key, text ≤ 20, from given `sequence`, read-only), partyPk (identifier) · identifier type: BIGINT
Promised  : one Party per counterparty, holding nameAr, nameEn, mobile, email and taxRegistrationNo once, shared by its Customer and its Supplier (REQ-PTY-024). Consumers reach the Party through the Customer / Supplier reads (CON-PTY-009, CON-PTY-010) and the address read (CON-PTY-011); a consumer storing a Party reference stores partyPk.
Traces    : ENT-PTY-001, REQ-PTY-001, REQ-PTY-006, REQ-PTY-024

### CON-PTY-002 — العميل / Customer (ar / en)

Entity    : ENT-PTY-002 — key columns: code (business key, presented from ENT-PTY-001.code), customerPk (identifier — the HARD-FK target for SLS, STL, SFR) · identifier type: BIGINT
Promised  : one Customer record per Party, with the same identifier for back-office, point of sale and storefront (REQ-PTY-001); partyFk never changes; customerGroupCode always present (REQ-PTY-039); isWalkInFl marks the tenant's single walk-in Customer (RULE-PTY-014); currencyCode names the currency of the Customer's credit amounts (ADR-PTY-037); a deactivated Customer stays readable by every existing reference (REQ-PTY-014).
Traces    : ENT-PTY-002, REQ-PTY-001, REQ-PTY-002, REQ-PTY-014, REQ-PTY-039

### CON-PTY-003 — المورّد / Supplier (ar / en)

Entity    : ENT-PTY-003 — key columns: code (business key, presented from ENT-PTY-001.code), supplierPk (identifier — the HARD-FK target for PUR, STL) · identifier type: BIGINT
Promised  : one Supplier record per Party; partyFk never changes; paymentTermDays is independent of `b2bCredit` (ADR-PTY-012); a deactivated Supplier stays readable by every existing reference (REQ-PTY-020).
Traces    : ENT-PTY-003, REQ-PTY-019, REQ-PTY-020

### CON-PTY-004 — العنوان / Address (ar / en)

Entity    : ENT-PTY-004 — key columns: partyFk + addressTypeCode + code (business key: ordinal within the Party, not a business number), addressPk (identifier — the HARD-FK target for SLS, PUR, SFR documents) · identifier type: BIGINT
Promised  : addresses belong to a Party (shared by its Customer and Supplier); one active default per Party per address type while the Party has an active Address of that type (RULE-PTY-017); a deactivated Address stays readable by its identifier (REQ-PTY-033).
Traces    : ENT-PTY-004, REQ-PTY-030, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034

### CON-PTY-005 — طلب تجاوز حد الائتمان / Credit override request (ar / en)

Entity    : ENT-PTY-005 — key columns: docNo (business key, text ≤ 20, from given `sequence`, read-only), creditOverrideRequestPk (identifier — the reference SLS and SFR store against a credit sale) · identifier type: BIGINT
Promised  : one open request (PENDING or APPROVED) per Customer and credit sale above the limit (RULE-PTY-034), status lifecycle PENDING → APPROVED → USED, PENDING → REJECTED, PENDING → CANCELLED, and PENDING or APPROVED → CANCELLED when the Customer is deactivated (SRS A7, REQ-PTY-111); the sale is held by text reference (sourceDocumentRef, usedDocumentRef — ADR-PTY-016), so the consumer passes its own document reference and PTY stores no key into the consumer; a use must carry the reference the request was raised for (ADR-PTY-042); amounts carry currencyCode. Exists only while `b2bCredit` is on (RULE-PTY-023).
Traces    : ENT-PTY-005, REQ-PTY-091, REQ-PTY-092, REQ-PTY-097, REQ-PTY-103, REQ-PTY-111, REQ-PTY-113

---

# PART 2 — LOOKUP ITEMS

Values are loaded at runtime from given `mdl` (profile LOOKUPS); consumers read them through
given `mdl`, never as constants, except the closed values they branch on (CON-PTY-008).

### CON-PTY-006 — مجموعة العملاء / Customer group lookup (ar / en)

Lookup    : CUSTOMER_GROUP (open; seeded RETAIL, WHOLESALE) — key column: value code, carried as ENT-PTY-002.customerGroupCode
Entity    : ENT-PTY-002 — key columns: customerGroupCode
Promised  : every Customer holds exactly one active value; RETAIL is the default (REQ-PTY-007). PRC keys price lists on this code.
Traces    : ENT-PTY-002, REQ-PTY-007, REQ-PTY-039, REQ-PTY-040

### CON-PTY-007 — نوع العنوان / Address type lookup (ar / en)

Lookup    : ADDRESS_TYPE (open; seeded BILLING, DELIVERY) — key column: value code, carried as ENT-PTY-004.addressTypeCode
Entity    : ENT-PTY-004 — key columns: addressTypeCode
Promised  : every Address holds one active value; a guest's address is created as DELIVERY (REQ-PTY-050).
Traces    : ENT-PTY-004, REQ-PTY-038, REQ-PTY-050

### CON-PTY-008 — رموز ضبط الائتمان / Credit control codes (ar / en)

Lookup    : CREDIT_CHECK_RESULT (closed: WITHIN_LIMIT, OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE, NOT_APPLICABLE) · CREDIT_OVERRUN_ACTION (closed: WARN, BLOCK_WITH_OVERRIDE) · CREDIT_OVERRIDE_STATUS (closed: PENDING, APPROVED, REJECTED, CANCELLED, USED)
Entity    : ENT-PTY-005 — key columns: creditCheckResultCode, statusCode
Promised  : closed value sets — a consumer may branch on these codes; a new value is a BREAKING change (§ Stability).
Traces    : ENT-PTY-005, REQ-PTY-074, REQ-PTY-081, REQ-PTY-082, REQ-PTY-083, REQ-PTY-084, REQ-PTY-086, REQ-PTY-094

---

# PART 3 — OPERATION ITEMS

### CON-PTY-009 — قراءة العميل / Read Customer (ar / en)

Signature : readCustomer(customerId: BIGINT, forNewDocument: flag) → Customer { customerPk, partyFk, code, nameAr, nameEn, mobile, email, taxRegistrationNo, customerGroupCode, isWalkInFl, isActiveFl, creditLimit, paymentTermDays, currencyCode } · errors: not found; inactive for a new document (RULE-PTY-005, when forNewDocument is set); forbidden
Entity    : ENT-PTY-002
Notes     : creditLimit, paymentTermDays and currencyCode are present only while `b2bCredit` is on and are omitted otherwise (REQ-PTY-071). An inactive Customer is returned when forNewDocument is not set (existing references stay readable, REQ-PTY-014). partyFk is part of the Customer entity returned (SRS A8.1 output "Customer") and is the input of CON-PTY-011.
Traces    : REQ-PTY-002, REQ-PTY-003, REQ-PTY-014, REQ-PTY-071

### CON-PTY-010 — قراءة المورّد / Read Supplier (ar / en)

Signature : readSupplier(supplierId: BIGINT, forNewDocument: flag) → Supplier { supplierPk, partyFk, code, nameAr, nameEn, mobile, email, taxRegistrationNo, paymentTermDays, isActiveFl } · errors: not found; inactive for a new document (RULE-PTY-005, when forNewDocument is set); forbidden
Entity    : ENT-PTY-003
Traces    : REQ-PTY-019, REQ-PTY-003, REQ-PTY-020

### CON-PTY-011 — عناوين الطرف / Read Party addresses (ar / en)

Signature : listPartyAddresses(partyId: BIGINT) → list of Address { addressPk, partyFk, addressTypeCode, code, nameAr, nameEn, line1, line2, city, region, postalCode, isDefaultFl } — active Addresses only · errors: Party not found; forbidden
Entity    : ENT-PTY-004
Traces    : REQ-PTY-032, REQ-PTY-034, REQ-PTY-035

### CON-PTY-012 — قراءة العنوان / Read Address (ar / en)

Signature : readAddress(addressId: BIGINT) → Address { addressPk, partyFk, addressTypeCode, code, nameAr, nameEn, line1, line2, city, region, postalCode, isDefaultFl, isActiveFl } — inactive Addresses included · errors: not found; forbidden
Entity    : ENT-PTY-004
Notes     : lets a document that stored addressPk show it after deactivation (AC-PTY-039).
Traces    : REQ-PTY-033

### CON-PTY-013 — العميل النقدي / Default Customer for a point-of-sale sale (ar / en)

Signature : defaultPosCustomer() → Customer (as CON-PTY-009, isWalkInFl true) · errors: forbidden
Entity    : ENT-PTY-002
Notes     : always answers — the walk-in Customer is created on first demand when the tenant has none, and repeated calls return the same identifier (REQ-PTY-046, RULE-PTY-014). The walk-in Customer is never inactive (RULE-PTY-010), carries no credit (RULE-PTY-011) and cannot receive a Credit override request (RULE-PTY-033).
Traces    : REQ-PTY-044, REQ-PTY-045, REQ-PTY-046, REQ-PTY-047

### CON-PTY-014 — بيانات طلب الزائر / Guest order customer resolution (ar / en)

Signature : resolveGuestCustomer(nameAr, nameEn, email, mobile, deliveryAddress { line1, line2, city, region, postalCode }) → customerId: BIGINT · errors: contact required (RULE-PTY-019); name required (RULE-PTY-001); email or mobile format (RULE-PTY-003, RULE-PTY-004); order cannot be completed with these details (RULE-PTY-021 — does not reveal that a matching Customer exists)
Entity    : ENT-PTY-002, ENT-PTY-001, ENT-PTY-004
Notes     : matches an active Party by email (letter case ignored), then by a normalised mobile held by exactly one active Party; otherwise creates Party + Customer (RETAIL) + default DELIVERY Address. Adds a Customer role to a matched Party holding none. Guest details never change a matched Party. The walk-in Party and inactive Parties are never matched. The CUSTOMER-realm invitation is PTY's own follow-up through given `sec`, not part of the answer.
Traces    : REQ-PTY-050, REQ-PTY-053, REQ-PTY-055, REQ-PTY-056, REQ-PTY-057, REQ-PTY-058, REQ-PTY-061, REQ-PTY-062, REQ-PTY-063

### CON-PTY-015 — فحص الائتمان / Credit check (ar / en)

Signature : checkCredit(customerId: BIGINT, saleAmount: decimal) → CreditCheck { resultCode (CREDIT_CHECK_RESULT), creditLimit, openExposure, projectedExposure, currencyCode, overrunActionCode (CREDIT_OVERRUN_ACTION), approvalRequired: flag } · errors: Customer not found; Customer inactive (RULE-PTY-005, REQ-PTY-110); forbidden
Entity    : ENT-PTY-002
Notes     : `b2bCredit` off → NOT_APPLICABLE. Empty creditLimit → NO_CREDIT. projectedExposure = openExposure + saleAmount; ≤ creditLimit → WITHIN_LIMIT, > creditLimit → OVER_LIMIT. A contributor not answering within 3 s → EXPOSURE_UNAVAILABLE with openExposure empty. OVER_LIMIT, NO_CREDIT and EXPOSURE_UNAVAILABLE carry the tenant's overrun action (default BLOCK_WITH_OVERRIDE) and approvalRequired = yes under BLOCK_WITH_OVERRIDE, no under WARN.
Traces    : REQ-PTY-068, REQ-PTY-074, REQ-PTY-079, REQ-PTY-080, REQ-PTY-081, REQ-PTY-082, REQ-PTY-083, REQ-PTY-084, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-110

### CON-PTY-016 — طلب موافقة على تجاوز الائتمان / Create Credit override request (ar / en)

Signature : createCreditOverrideRequest(customerId: BIGINT, saleAmount: decimal, sourceDocumentRef: text ≤ 60) → CreditOverrideRequest (as CON-PTY-017, statusCode PENDING) · errors: credit not enabled (RULE-PTY-023); sale within the limit — no approval needed (RULE-PTY-025); sale amount not greater than zero (RULE-PTY-032); walk-in Customer cannot buy on credit (RULE-PTY-033); Customer inactive (RULE-PTY-005); this sale already has an open request — names its docNo (RULE-PTY-034); Customer not found; forbidden
Entity    : ENT-PTY-005
Notes     : raised by the consumer when CON-PTY-015 answered approvalRequired = yes. PTY fills docNo, docDate, creditLimitAtRequest, openExposure, creditCheckResultCode and the requester, and notifies the approvers.
Traces    : REQ-PTY-072, REQ-PTY-089, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-104, REQ-PTY-105, REQ-PTY-110, REQ-PTY-113

### CON-PTY-017 — قراءة طلب تجاوز الائتمان / Read Credit override request (ar / en)

Signature : readCreditOverrideRequest(creditOverrideRequestId: BIGINT) → CreditOverrideRequest { creditOverrideRequestPk, docNo, docDate, statusCode (CREDIT_OVERRIDE_STATUS), customerFk, saleAmount, currencyCode, sourceDocumentRef, decidedBy, decidedAt, decisionReason } · errors: not found; forbidden
Entity    : ENT-PTY-005
Traces    : REQ-PTY-103

### CON-PTY-018 — استخدام الموافقة / Use Credit override request (ar / en)

Signature : useCreditOverrideRequest(creditOverrideRequestId: BIGINT, customerId: BIGINT, saleAmount: decimal, usedDocumentRef: text ≤ 60) → CreditOverrideRequest (statusCode USED) · errors: approval does not cover this sale — not APPROVED, another Customer, another sale reference than sourceDocumentRef, or a larger amount (RULE-PTY-028); Customer inactive (RULE-PTY-005); credit not enabled (RULE-PTY-023); not found; forbidden
Entity    : ENT-PTY-005
Notes     : one approval covers one sale — the sale it was requested for (usedDocumentRef equals sourceDocumentRef, ADR-PTY-042); a second use is refused (AC-PTY-113); an approval of a Customer deactivated meanwhile is CANCELLED and refused (AC-PTY-129).
Traces    : REQ-PTY-097, REQ-PTY-099, REQ-PTY-110, REQ-PTY-111, REQ-PTY-112

### CON-PTY-019 — اعتماد إيقاف الائتمان / `b2bCredit` switch-off dependency (ar / en)

Signature : b2bCreditSwitchOffDependency() → { dependentCustomerCount: number } · errors: forbidden
Entity    : ENT-PTY-002
Notes     : consumer is the capability-flag owner (registry OI-05, still OPEN). A count above 0 means the switch-off must be refused with the message of RULE-PTY-024 and the next action "Show customers with credit" / «عرض العملاء ذوي الائتمان»; 0 means PTY does not refuse.
Traces    : REQ-PTY-075, REQ-PTY-076, REQ-PTY-077

### CON-PTY-020 — البحث الشامل عن العملاء / Customer global search (ar / en)

Signature : globalSearchCustomers(term: text) → list of at most 10 Customer { customerPk, code, nameAr, nameEn, mobile } · errors: none — a term under 2 characters or a user without VIEW on PTY_CUSTOMER yields an empty list
Entity    : ENT-PTY-002
Notes     : active Customers only; matches code, nameAr, nameEn, mobile, email or taxRegistrationNo without letter case and with Arabic letter forms unified; exact code matches first, then name ascending. Suppliers are not returned (ADR-PTY-013).
Traces    : REQ-PTY-041, REQ-PTY-042, REQ-PTY-043

### CON-PTY-021 — حدث إيقاف صفة الطرف / Party role deactivated event (ar / en)

Signature : event `pty.PartyRoleDeactivated` → { role: CUSTOMER | SUPPLIER, identifier: BIGINT (customerPk or supplierPk), code } — published through the given outbox (`OutboxPublisher`) inside the deactivation transaction, delivered after commit, at least once · errors: —
Entity    : ENT-PTY-002, ENT-PTY-003
Notes     : prevent new use + notify; no cascade (RULE-PTY-006). Consumers keep their existing documents unchanged. A consumer subscribes with an `OutboxSubscriber` for type `pty.PartyRoleDeactivated` and must be idempotent on `OutboxMessage.id()`; a module enabled later receives the past events by replay (ADR-PTY-038).
Traces    : REQ-PTY-023

### CON-PTY-022 — نقطة امتداد مديونية العميل / Customer credit exposure extension point (ar / en)

Signature : contributor CustomerCreditExposureContributor.openExposure(customerId: BIGINT) → amount: decimal (in the Customer's currencyCode — the tenant base currency, ADR-PTY-037) · errors: no answer within 3 s → the credit check answers EXPOSURE_UNAVAILABLE
Entity    : ENT-PTY-002
Notes     : declared by PTY, implemented by downstream modules (STL receivables, SLS open orders); PTY sums every registered contributor's amount, 0 when none is registered (ADR-PTY-017). Registering a contributor creates no dependency from PTY on the contributor.
Traces    : REQ-PTY-080, REQ-PTY-084

### CON-PTY-023 — تسجيل عميل من مستند / Register Customer in place (ar / en)

Signature : registerCustomer(nameAr, nameEn, mobile, email, taxRegistrationNo, customerGroupCode) → { customerPk, partyFk, code } · errors: name required (RULE-PTY-001); email already held by another active Party — names its code (RULE-PTY-002); email format (RULE-PTY-003); mobile format (RULE-PTY-004); customer group not in the list (RULE-PTY-018); forbidden (CREATE on PTY_CUSTOMER)
Entity    : ENT-PTY-002, ENT-PTY-001
Notes     : one of nameAr / nameEn is the only mandatory input; customerGroupCode defaults to RETAIL; the code comes from given `sequence`. Used by a sale to register a Customer without leaving it.
Traces    : REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011

### CON-PTY-024 — تسجيل مورّد من مستند / Register Supplier in place (ar / en)

Signature : registerSupplier(nameAr, nameEn, mobile, email, taxRegistrationNo, paymentTermDays) → { supplierPk, partyFk, code } · errors: name required (RULE-PTY-001); email already held by another active Party — names its code (RULE-PTY-002); email format (RULE-PTY-003); mobile format (RULE-PTY-004); payment terms outside 0 – 365 (RULE-PTY-008); forbidden (CREATE on PTY_SUPPLIER)
Entity    : ENT-PTY-003, ENT-PTY-001
Notes     : one of nameAr / nameEn is the only mandatory input; the code comes from given `sequence`. Used by a purchase document to register a Supplier without leaving it.
Traces    : REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-016, REQ-PTY-022

---

# PART 4 — GIVEN USAGE (repeated verbatim from SRS A9)

```yaml name=given-usage
rows:
  - {given: erp-core, module: modules.sec, via: com.erp.sec.crossmodule.SecCustomerInvitationApi, purpose: "Issue the CUSTOMER-realm invitation for a Customer created from or matched to a guest order, with PTY's template, from PTY's after-commit listener (REQ-PTY-051, REQ-PTY-059)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.crossmodule.SecUserDirectoryApi, purpose: "Resolve the CUSTOMER-realm user linked to a Customer and the users holding UPDATE on PTY_CREDIT_OVERRIDE; show user names in credit history and request decisions (REQ-PTY-093, REQ-PTY-108)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.permission.PermissionContributor, purpose: "PtyPermissions declares permission module PTY with the five PTY screens (SEC_PAGES) and their VIEW / CREATE / UPDATE / DELETE actions; every service method checks its constant (B4, ADR-PTY-039)"}
  - {given: erp-core, module: modules.cu, via: com.erp.cu.crossmodule.SettingsApi, purpose: "Read capability flag b2bCredit through the FeatureFlags API, read / save the tenant's credit-overrun setting and read the tenant base currency PTY_BASE_CURRENCY_CODE (REQ-PTY-070 … REQ-PTY-074, REQ-PTY-086, REQ-PTY-087; ADR-PTY-037)"}
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "Load CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT and CREDIT_OVERRIDE_STATUS values at runtime (A6)"}
  - {given: erp-core, module: modules.sequence, via: com.erp.sequence.crossmodule.NumberSeriesApi, purpose: "Assign the Party code and the Credit override request docNo from the series PTY_PARTY and PTY_CREDIT_OVERRIDE seeded by the PTY migration (REQ-PTY-006, REQ-PTY-092)"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantProvisioningContributor, purpose: "Create the tenant's walk-in Customer when a tenant is provisioned (REQ-PTY-044)"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantRunner, purpose: "Re-bind the tenant of PTY's after-commit work through the given's published tenant runner on the given event executor (REQ-PTY-051, REQ-PTY-059, REQ-PTY-093; ADR-PTY-044)"}
  - {given: erp-core, module: modules.audit, via: com.erp.audit.crossmodule.AuditApi, purpose: "Record credit-limit and payment-term changes through the @Audited listener of Customer, record approve / reject decisions as business actions, and read the credit history back (REQ-PTY-106 … REQ-PTY-109)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.OutboxPublisher, purpose: "Publish pty.PartyRoleDeactivated to consuming modules inside the deactivation transaction (REQ-PTY-023, ADR-PTY-038)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.OutboxSubscriber, purpose: "Consume sec.CustomerInvitationRedeemed, idempotent on OutboxMessage.id(), to set secUserId (REQ-PTY-052, REQ-PTY-054)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.DomainEventPublisher, purpose: "Publish PTY's own in-process events that only PTY's after-commit listeners consume — invitation issue and approver notification — never an event for another module (REQ-PTY-051, REQ-PTY-059, REQ-PTY-093)"}
  - {given: erp-core, module: modules.notif, via: com.erp.notif.crossmodule.NotificationDispatchApi, purpose: "Dispatch template PTY_CREDIT_OVERRIDE_REQUESTED, seeded by the PTY migration, to each approver of a new Credit override request (REQ-PTY-093, ADR-PTY-036)"}
  - {given: erp-core, module: modules.module, via: docs/api-docs/module/index.md, purpose: "Declare PTY to the given module catalogue with the module manifest META-INF/erp/modules/pty.yaml in the format the given's module api documents: code, package, permission module, api-paths, settings, events and migration (ADR-PTY-044)"}
```

No contract item re-creates a given entity: CUSTOMER-realm users stay in given `sec` (the
Customer holds only `secUserId`, which is not promised to consumers), the credit-overrun
setting stays in given `cu`, lookup values in given `mdl`, numbers in given `sequence`.

---

# PART 5 — STABILITY

| Change | Kind | Rule |
|---|---|---|
| New CON item, new optional input, new output field | ADDITIVE | allowed in any version |
| New value in an open lookup (CUSTOMER_GROUP, ADDRESS_TYPE) | ADDITIVE | data, not a contract change |
| New value in a closed set of CON-PTY-008 | BREAKING | new version + ADR |
| Removing or renaming an item, an input, an output field or an error meaning; changing an identifier type or a key column | BREAKING | new version + ADR; `gov.py graph` raises a resolution event on every consumer edge that cited the item |

---

# PART 6 — SELF-CHECK

- [x] Every SHARED entity of SRS A3 (ENT-PTY-001 … ENT-PTY-005) has an ENTITY item (CON-PTY-001 … CON-PTY-005); no other module's A8 block is in this stage's inputs, and PTY's own A8 is `consumes: []`.
- [x] Every operation of SRS A8.1 has an item (read Customer → 009, read Supplier → 010, read Party addresses → 011, default Customer → 013, guest order details → 014, credit check → 015, create / read / use Credit override request → 016 / 017 / 018, switch-off dependency → 019, global search → 020); the declared extension point → 022 and the domain event → 021; registration in place (REQ-PTY-008) → 023, 024; read Address by identifier (AC-PTY-039) → 012.
- [x] Every operation item has a `Signature` line and names no path or verb.
- [x] Every item traces to an ENT or REQ; no private entity is promised.
- [x] Identifier type stated once per entity item as BIGINT.
- [x] No question raised; CON sequence 001 … 024 continuous; no ADR needed (every item rests on the SRS).
- [x] Part 4 carries the SRS's `given-usage` rows verbatim, same order.
