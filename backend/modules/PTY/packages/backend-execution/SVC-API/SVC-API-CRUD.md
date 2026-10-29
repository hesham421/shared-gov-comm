<!-- source: PHASE:SVC-API / SUB:SVC-API-CRUD -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-PTY-001, DBF-PTY-002, DBF-PTY-003, DBF-PTY-004, DBF-PTY-005, DBF-PTY-006, DBF-PTY-007, DBF-PTY-008, DBF-PTY-013, DBF-PTY-014, DBF-PTY-015, DBF-PTY-016, DBF-PTY-018, DBF-PTY-019, DBF-PTY-020, DBF-PTY-025, DBF-PTY-026, DBF-PTY-027, DBF-PTY-028, DBF-PTY-033, DBF-PTY-034, DBF-PTY-035, DBF-PTY-036, DBF-PTY-037, DBF-PTY-038, DBF-PTY-039, DBF-PTY-040, DBF-PTY-041, DBF-PTY-042, DBF-PTY-043, DBF-PTY-044, DBF-PTY-045, DBF-PTY-049, DBF-PTY-050, DBF-PTY-051, DBF-PTY-052, DBF-PTY-053, DBF-PTY-054, DBF-PTY-055, DBF-PTY-056, DBF-PTY-057, DBF-PTY-058, DBF-PTY-059, DBF-PTY-060, DBF-PTY-061, DBF-PTY-062, DBF-PTY-063, DBF-PTY-064, DBF-PTY-078, DBF-PTY-079, REQ-PTY-001, REQ-PTY-002, REQ-PTY-003, REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012, REQ-PTY-014, REQ-PTY-015, REQ-PTY-016, REQ-PTY-017, REQ-PTY-019, REQ-PTY-020, REQ-PTY-021, REQ-PTY-022, REQ-PTY-023, REQ-PTY-024, REQ-PTY-025, REQ-PTY-026, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-039, REQ-PTY-040, REQ-PTY-047, REQ-PTY-048, REQ-PTY-049, REQ-PTY-064, REQ-PTY-065, REQ-PTY-066, REQ-PTY-067, REQ-PTY-068, REQ-PTY-070, REQ-PTY-071, REQ-PTY-072, REQ-PTY-073, REQ-PTY-078, REQ-PTY-080, REQ-PTY-085, REQ-PTY-086, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-090, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-098, REQ-PTY-100, REQ-PTY-103, REQ-PTY-104, REQ-PTY-105, REQ-PTY-106, REQ-PTY-107, REQ-PTY-109, REQ-PTY-110, REQ-PTY-111, REQ-PTY-112, REQ-PTY-113, REQ-PTY-114 -->
<!-- SUB:SVC-API-CRUD:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-003,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-019,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-070,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-107,REQ-PTY-109,REQ-PTY-110,REQ-PTY-111,REQ-PTY-112,REQ-PTY-113,REQ-PTY-114 -->
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
Orchestration : (A) partyId absent — validate RULE-PTY-001, RULE-PTY-003, RULE-PTY-004, RULE-PTY-018 → QR-PTY-004 (email held by another active Party → RULE-PTY-002) → allocate the Party code DBF-PTY-007 from `NumberSeriesApi` series PTY_PARTY → insert Party QR-PTY-002 with DBF-PTY-008 (isActiveFl) true → insert Customer QR-PTY-008 with DBF-PTY-019 (partyFk) = new Party PK, DBF-PTY-014 (customerGroupCode, RETAIL when absent — REQ-PTY-007), DBF-PTY-018 (isWalkInFl) false, DBF-PTY-020 (isActiveFl) true, DBF-PTY-078 (currencyCode) = `PtyCurrency.baseCurrency()` (ADR-PTY-037). (B) partyId present — lock Party QR-PTY-001 (absent → PTY-404-PARTY-NOT-FOUND) → QR-PTY-007 (Party already holds a Customer → RULE-PTY-009) → validate RULE-PTY-018 → Party inactive: QR-PTY-004 (RULE-PTY-002) then DBF-PTY-008 (isActiveFl) true through QR-PTY-003 (REQ-PTY-029) → insert Customer QR-PTY-008 as in (A). Return the Customer with its Party's code and names.
Repository   : QR-PTY-001, QR-PTY-002, QR-PTY-003, QR-PTY-004, QR-PTY-007, QR-PTY-008 · SAVE · join NONE · transaction READ_WRITE
Concurrency  : Party code — atomic allocation by `NumberSeriesApi`, backstopped by UQ_PTY_PARTY_CODE. Email — two registrations with the same email both pass QR-PTY-004; UQ_PTY_PARTY_EMAIL rejects the second insert, translated to PTY-409-EMAIL-TAKEN after re-reading the holder (ADR-PTY-026). Role add — QR-PTY-001 locks the Party row (`FOR UPDATE`), so a second add waits and then finds the first Customer; UQ_PTY_CUSTOMER_PARTY_FK backstops → PTY-409-ROLE-HELD.
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_CREATE
Localization : messages ar + en (catalog); the Party carries nameAr / nameEn
<!-- API:API-PTY-001:END -->

<!-- API:API-PTY-002:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-003,REQ-PTY-014,REQ-PTY-071,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-013,DBF-PTY-014,DBF-PTY-015,DBF-PTY-016,DBF-PTY-018,DBF-PTY-019,DBF-PTY-020,DBF-PTY-078 -->
### API-PTY-002 — read Customer
Entity       : ENT-PTY-002, ENT-PTY-001 · operation VIEW · serves: read (SCR-REQ-PTY-001)
Honours      : CON-PTY-009, CON-PTY-002, CON-PTY-001
Endpoint     : /api/v1/pty/customers/{id}   verb: GET
Layers       : CustomerController.get → CustomerService.read ; in-process PtyPartyApi.readCustomer → CustomerService.read
Request      : path id (DBF-PTY-013, Long, required) · query forNewDocument (Boolean, optional, default false — set by a consumer selecting the Customer for a new document)
Response     : 200 · `Customer` {customerPk (DBF-PTY-013), partyFk (DBF-PTY-019), code (DBF-PTY-007), nameAr (DBF-PTY-005), nameEn (DBF-PTY-006), mobile (DBF-PTY-002), email (DBF-PTY-003), taxRegistrationNo (DBF-PTY-004), customerGroupCode (DBF-PTY-014), isWalkInFl (DBF-PTY-018), isActiveFl (DBF-PTY-020), creditLimit (DBF-PTY-015), paymentTermDays (DBF-PTY-016) and currencyCode (DBF-PTY-078) — present only while `b2bCredit` is on, absent from the JSON otherwise, createdBy, createdAt, updatedBy, updatedAt (DBF-PTY-021 … DBF-PTY-024)} · not paginated · ApiResponse<Customer>
Validations  : RULE-PTY-005 (forNewDocument = true and the Customer is inactive) → PTY-422-ROLE-INACTIVE ({code} = Party code)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-ROLE-INACTIVE, PTY-500-INTERNAL
Orchestration : load QR-PTY-005 (absent → PTY-404-CUSTOMER-NOT-FOUND) → forNewDocument: RULE-PTY-005 → `CreditFlag.isOn()` decides whether the credit fields are serialised (REQ-PTY-071); while it is on they are serialised to every holder of PERM_PTY_CUSTOMER_VIEW, without PERM_PTY_CUSTOMER_CREDIT_VIEW (ADR-PTY-046, AC-PTY-138) → map. An inactive Customer is returned when forNewDocument is false (REQ-PTY-014).
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

<!-- API:API-PTY-004:START traces=REQ-PTY-014,REQ-PTY-023,REQ-PTY-028,REQ-PTY-047,REQ-PTY-111,DBF-PTY-008,DBF-PTY-013,DBF-PTY-018,DBF-PTY-020,DBF-PTY-063 -->
### API-PTY-004 — deactivate Customer
Entity       : ENT-PTY-002, ENT-PTY-005 · operation DELETE · serves: deactivate (SCR-REQ-PTY-001)
Honours      : CON-PTY-002, CON-PTY-021, CON-PTY-005
Endpoint     : /api/v1/pty/customers/{id}   verb: DELETE
Layers       : CustomerController.deactivate → CustomerService.deactivate
Request      : path id (DBF-PTY-013, Long, required) · no body
Response     : 200 · `Confirmation` {id, code} with messageAr / messageEn = RULE-PTY-006 confirmation ({code} = Party code) · ApiResponse<Confirmation>
Validations  : RULE-PTY-010 (DBF-PTY-018 true) → PTY-422-WALK-IN-DEACTIVATE
Messages     : RULE-PTY-006 — ar: تم إيقاف {code}. تبقى المستندات السابقة كما هي، ولن يُعرض في المستندات الجديدة. · en: {code} is deactivated. Earlier documents keep it; it is no longer offered on new documents.
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-WALK-IN-DEACTIVATE, PTY-500-INTERNAL
Orchestration :
1. Lock QR-PTY-006, then `CustomerDomain.deactivate` applies RULE-PTY-010.
2. If the Customer is already inactive, return the confirmation and publish nothing (idempotent).
3. Set DBF-PTY-020 (isActiveFl) false (QR-PTY-009).
4. Lock the Party QR-PTY-001. QR-PTY-021 counts the Party's active roles; on 0, set DBF-PTY-008 (isActiveFl) false (QR-PTY-003, REQ-PTY-028).
5. QR-PTY-039 locks the Customer's PENDING and APPROVED requests. `CreditOverrideRequestDomain.cancelOnCustomerDeactivation` sets each to CANCELLED through QR-PTY-034, expecting its read status, and the `@Audited` listener records it (REQ-PTY-111, ADR-PTY-041).
   Steps 1, 4 and 5 take their locks in the CORE lock order: Customer → Party → requests.
6. Call `OutboxPublisher.publish("pty.PartyRoleDeactivated", {role CUSTOMER, identifier customerPk, code})` inside the transaction. The given delivers it to the subscribers after commit; a rollback publishes nothing (REQ-PTY-023, ADR-PTY-038).
Soft delete only (profile delete_semantics `soft`): nothing is removed, and every reference stays readable.
Repository   : QR-PTY-006, QR-PTY-009, QR-PTY-001, QR-PTY-021, QR-PTY-003, QR-PTY-039, QR-PTY-034 · UPDATE · join NONE · transaction READ_WRITE
Concurrency  :
- The Party row lock serialises the "last active role" decision. A Customer and a Supplier of one Party deactivated at the same time cannot both read "one other role still active" and leave the Party active.
- The Customer row lock (QR-PTY-006) serialises a deactivation with a request creation of the same Customer (API-PTY-019 locks it too), so a request is never created for a Customer whose deactivation has committed. End state either way: no PENDING or APPROVED request of the Customer remains — the creation that waits answers PTY-422-ROLE-INACTIVE, and a request created first is cancelled by step 5 (TC-PTY-192).
- An approval or a use racing the cancellation is settled by the request row lock (QR-PTY-039) and the compare-and-set of QR-PTY-034. Whichever commits second finds the other's status, with three outcomes (TC-PTY-193):
  - an approval that loses answers PTY-409-REQUEST-NOT-PENDING (the request is CANCELLED); one that wins leaves an APPROVED request, which step 5 then cancels;
  - a use that loses answers PTY-422-APPROVAL-NOT-COVERING (the request is CANCELLED);
  - a use that wins leaves the request USED, and the cancellation skips it (QR-PTY-039 selects PENDING and APPROVED only).
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
Orchestration : lock QR-PTY-016 → already inactive: confirmation, no event → `SupplierDomain.deactivate`, DBF-PTY-028 (isActiveFl) false (QR-PTY-019) → lock Party QR-PTY-001 → QR-PTY-021 = 0: DBF-PTY-008 (isActiveFl) false (QR-PTY-003) → `OutboxPublisher.publish("pty.PartyRoleDeactivated", {role SUPPLIER, identifier supplierPk, code})` inside the transaction (REQ-PTY-023, ADR-PTY-038).
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

<!-- API:API-PTY-011:START traces=REQ-PTY-030,REQ-PTY-034,REQ-PTY-037,REQ-PTY-038,REQ-PTY-110,DBF-PTY-008,DBF-PTY-033,DBF-PTY-034,DBF-PTY-035,DBF-PTY-036,DBF-PTY-037,DBF-PTY-038,DBF-PTY-039,DBF-PTY-040,DBF-PTY-041,DBF-PTY-042,DBF-PTY-043,DBF-PTY-044,DBF-PTY-045 -->
### API-PTY-011 — create Address
Entity       : ENT-PTY-004, ENT-PTY-001 · operation CREATE · serves: address create (SCR-REQ-PTY-001, SCR-REQ-PTY-002)
Honours      : CON-PTY-004, CON-PTY-007
Endpoint     : /api/v1/pty/parties/{partyId}/addresses   verb: POST
Layers       : AddressController.create → AddressService.create
Request      : path partyId (DBF-PTY-041, Long, required) · body `AddressRequest` — addressTypeCode (DBF-PTY-034, String ≤ 50, required — RULE-PTY-016) · line1 (DBF-PTY-035, String ≤ 200, required — RULE-PTY-015) · line2 (DBF-PTY-036, String ≤ 200) · city (DBF-PTY-037, String ≤ 100) · region (DBF-PTY-038, String ≤ 100) · postalCode (DBF-PTY-039, String ≤ 20) · nameAr (DBF-PTY-042, String ≤ 100) · nameEn (DBF-PTY-043, String ≤ 100). Excluded: addressPk, code, isDefaultFl, isActiveFl, audit.
Response     : 201 · `Address` (as API-PTY-012) · ApiResponse<Address>
Validations  : RULE-PTY-005 (the Party is inactive) → PTY-422-ROLE-INACTIVE ({code} = Party code) · RULE-PTY-015 → PTY-422-ADDRESS-LINE-REQUIRED · RULE-PTY-016 → PTY-422-ADDRESS-TYPE-INVALID
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-PARTY-NOT-FOUND, PTY-422-ADDRESS-LINE-REQUIRED, PTY-422-ADDRESS-TYPE-INVALID, PTY-422-ROLE-INACTIVE, PTY-500-INTERNAL
Orchestration : lock Party QR-PTY-001 (absent → 404) → the caller holds the UPDATE of a role the Party holds (ADR-PTY-039; else PTY-403-FORBIDDEN) → `PartyDomain.requireActive` on DBF-PTY-008 (RULE-PTY-005, ADR-PTY-041) → `AddressDomain.create` validates RULE-PTY-015, RULE-PTY-016 → QR-PTY-024 gives the next ordinal → DBF-PTY-044 (code) → QR-PTY-030: no active Address of this type → DBF-PTY-040 (isDefaultFl) true (REQ-PTY-034, RULE-PTY-017), else false → DBF-PTY-041 (partyFk) = partyId, DBF-PTY-045 (isActiveFl) true → insert QR-PTY-025.
Repository   : QR-PTY-001, QR-PTY-024, QR-PTY-030, QR-PTY-025 · SAVE · join NONE · READ_WRITE
Concurrency  : The ordinal and the "first of its type" decision are read-then-write: the Party row lock (QR-PTY-001) makes two concurrent creates on one Party run one after the other; UQ_PTY_ADDRESS_PARTY_CODE and UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop (ADR-PTY-026, ADR-PTY-040).
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · `@PreAuthorize` hasAnyAuthority(PERM_PTY_CUSTOMER_UPDATE, PERM_PTY_SUPPLIER_UPDATE), then the service requires the UPDATE of a role the Party holds — when the Party holds both roles either one suffices (SRS B4 "Address create, update and deactivation require UPDATE on this page"; ADR-PTY-039)
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
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · `@PreAuthorize` hasAnyAuthority(PERM_PTY_CUSTOMER_VIEW, PERM_PTY_SUPPLIER_VIEW), then the VIEW of a role the Address's Party holds — either one when it holds both (ADR-PTY-039)
Localization : address label nameAr / nameEn
<!-- API:API-PTY-012:END -->

<!-- API:API-PTY-013:START traces=REQ-PTY-031,REQ-PTY-037,REQ-PTY-038,REQ-PTY-110,DBF-PTY-008,DBF-PTY-033,DBF-PTY-034,DBF-PTY-035,DBF-PTY-036,DBF-PTY-037,DBF-PTY-038,DBF-PTY-039,DBF-PTY-040,DBF-PTY-042,DBF-PTY-043 -->
### API-PTY-013 — update Address
Entity       : ENT-PTY-004 · operation UPDATE · serves: address update (SCR-REQ-PTY-001, SCR-REQ-PTY-002)
Honours      : CON-PTY-004, CON-PTY-007
Endpoint     : /api/v1/pty/parties/{partyId}/addresses/{id}   verb: PUT
Layers       : AddressController.update → AddressService.update
Request      : path partyId (DBF-PTY-041, Long, required), id (DBF-PTY-033, Long, required) · body `AddressRequest` — addressTypeCode (DBF-PTY-034, required) · line1 (DBF-PTY-035, required) · line2 (DBF-PTY-036) · city (DBF-PTY-037) · region (DBF-PTY-038) · postalCode (DBF-PTY-039) · nameAr (DBF-PTY-042) · nameEn (DBF-PTY-043). Excluded: partyFk (never changes), code, isDefaultFl, isActiveFl, audit.
Response     : 200 · `Address` (as API-PTY-012) · ApiResponse<Address>
Validations  : RULE-PTY-005 (the Party is inactive) → PTY-422-ROLE-INACTIVE · RULE-PTY-015 → PTY-422-ADDRESS-LINE-REQUIRED · RULE-PTY-016 → PTY-422-ADDRESS-TYPE-INVALID
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-PARTY-NOT-FOUND, PTY-404-ADDRESS-NOT-FOUND, PTY-422-ADDRESS-LINE-REQUIRED, PTY-422-ADDRESS-TYPE-INVALID, PTY-422-ROLE-INACTIVE, PTY-500-INTERNAL
Orchestration : lock Party QR-PTY-001 (404) → lock QR-PTY-023 (Address not of this Party → PTY-404-ADDRESS-NOT-FOUND) → the caller holds the UPDATE of a role the Party holds (ADR-PTY-039) → `PartyDomain.requireActive` (RULE-PTY-005) → `AddressDomain.from` validates → addressTypeCode changed on an active Address: if it was the default of the old type, DBF-PTY-040 (isDefaultFl) false and QR-PTY-029 promotes the most recently updated remaining active Address of the old type (QR-PTY-026); for the new type QR-PTY-030 — none active → DBF-PTY-040 true (RULE-PTY-017, ADR-PTY-026) → write the fields (QR-PTY-026); DBF-PTY-041 stays unchanged (AC-PTY-037).
Repository   : QR-PTY-001, QR-PTY-023, QR-PTY-029, QR-PTY-030, QR-PTY-026 · UPDATE · join NONE · READ_WRITE
Concurrency  : Party row lock serialises default decisions of the Party; UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop. The old default is cleared before the new one is set in the same transaction (the partial unique index is checked per statement). A type change racing an Address create (API-PTY-011) or a set-default (API-PTY-015) of the same type waits on the Party lock; whichever runs second reads the first's default, so the type ends with exactly one active default and neither call is refused (TC-PTY-195).
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · hasAnyAuthority(PERM_PTY_CUSTOMER_UPDATE, PERM_PTY_SUPPLIER_UPDATE) and the UPDATE of a role held — either one when the Party holds both (ADR-PTY-039)
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
Orchestration : lock Party QR-PTY-001 → lock QR-PTY-023 (404) → the caller holds the UPDATE of a role held (ADR-PTY-039) → already inactive: confirmation → `AddressDomain.deactivate` (allowed on an inactive Party — ADR-PTY-041): DBF-PTY-045 (isActiveFl) false; if DBF-PTY-040 was true: set it false and QR-PTY-029 finds the remaining active Address of the same type with the latest DBF-PTY-049 (updatedAt) → its DBF-PTY-040 (isDefaultFl) true (REQ-PTY-036) → write QR-PTY-026. No event for Addresses (RULE-PTY-006 names Customers and Suppliers).
Repository   : QR-PTY-001, QR-PTY-023, QR-PTY-029, QR-PTY-026 · UPDATE · READ_WRITE
Concurrency  : Party row lock serialises the reassignment; UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop.
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · hasAnyAuthority(PERM_PTY_CUSTOMER_UPDATE, PERM_PTY_SUPPLIER_UPDATE) and the UPDATE of a role held — either one when the Party holds both (ADR-PTY-039)
Localization : confirmation in ar + en
<!-- API:API-PTY-014:END -->

<!-- API:API-PTY-015:START traces=REQ-PTY-035,REQ-PTY-110,DBF-PTY-008,DBF-PTY-033,DBF-PTY-034,DBF-PTY-040,DBF-PTY-041 -->
### API-PTY-015 — set default Address
Entity       : ENT-PTY-004 · operation UPDATE · serves: set default (SCR-REQ-PTY-001, SCR-REQ-PTY-002)
Honours      : CON-PTY-004
Endpoint     : /api/v1/pty/parties/{partyId}/addresses/{id}/default   verb: PATCH
Layers       : AddressController.setDefault → AddressService.setDefault
Request      : path partyId (DBF-PTY-041, Long, required), id (DBF-PTY-033, Long, required) · body `DefaultAddressRequest` { isDefaultFl (DBF-PTY-040, Boolean, required, must be true — a default is cleared only by marking another Address of the type, RULE-PTY-017) } (ADR-PTY-031)
Response     : 200 · `Address` (as API-PTY-012) with messageAr / messageEn = RULE-PTY-017 confirmation ({typeAr} / {typeEn} = ADDRESS_TYPE labels from mdl) · ApiResponse<Address>
Validations  : body isDefaultFl missing or not true → PTY-400-VALIDATION (ADR-PTY-031) · the Address must be active (inactive → PTY-404-ADDRESS-NOT-FOUND, ADR-PTY-026) · RULE-PTY-005 (the Party is inactive) → PTY-422-ROLE-INACTIVE
Messages     : RULE-PTY-017 — ar: أصبح هذا العنوان هو العنوان الافتراضي من نوع {typeAr}. · en: This address is now the default {typeEn} address.
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-PARTY-NOT-FOUND, PTY-404-ADDRESS-NOT-FOUND, PTY-422-ROLE-INACTIVE, PTY-500-INTERNAL
Orchestration : lock Party QR-PTY-001 → lock QR-PTY-023 → the caller holds the UPDATE of a role held (ADR-PTY-039) → `PartyDomain.requireActive` (RULE-PTY-005) → `AddressDomain.markDefault` → QR-PTY-028 finds the current default of the same Party and DBF-PTY-034 type → its DBF-PTY-040 (isDefaultFl) false (QR-PTY-026) → this Address DBF-PTY-040 true (QR-PTY-026). Already default → unchanged, message returned.
Repository   : QR-PTY-001, QR-PTY-023, QR-PTY-028, QR-PTY-026 · UPDATE · READ_WRITE
Concurrency  : Two concurrent "set default" on one Party are serialised by the Party row lock; the previous default is cleared before the new one is set; UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop.
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · hasAnyAuthority(PERM_PTY_CUSTOMER_UPDATE, PERM_PTY_SUPPLIER_UPDATE) and the UPDATE of a role held — either one when the Party holds both (ADR-PTY-039)
Localization : confirmation in ar + en
<!-- API:API-PTY-015:END -->

<!-- API:API-PTY-016:START traces=REQ-PTY-068,REQ-PTY-070,REQ-PTY-073,REQ-PTY-080,REQ-PTY-085,DBF-PTY-013,DBF-PTY-015,DBF-PTY-016,DBF-PTY-018,DBF-PTY-078 -->
### API-PTY-016 — read credit standing
Entity       : ENT-PTY-002, ENT-PTY-001 · operation VIEW · serves: read (credit standing) (SCR-REQ-PTY-003)
Honours      : CON-PTY-002, CON-PTY-022
Endpoint     : /api/v1/pty/customer-credits/{customerId}   verb: GET
Layers       : CustomerCreditController.get → CustomerCreditService.standing
Request      : path customerId (DBF-PTY-013, Long, required)
Response     : 200 · `CustomerCredit` {customerPk (DBF-PTY-013), code (DBF-PTY-007), nameAr (DBF-PTY-005), nameEn (DBF-PTY-006), customerGroupCode (DBF-PTY-014), isWalkInFl (DBF-PTY-018), creditLimit (DBF-PTY-015), paymentTermDays (DBF-PTY-016), openExposure (derived — null when unavailable), availableCredit (derived = creditLimit − openExposure; null when either is null), currencyCode (DBF-PTY-078 — the currency of every amount), exposureAvailable (derived, Boolean)} · ApiResponse<CustomerCredit>
Validations  : RULE-PTY-023 (`b2bCredit` off) → PTY-422-CREDIT-NOT-ENABLED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-CREDIT-NOT-ENABLED, PTY-500-INTERNAL
Orchestration : `CreditFlag.isOn()` (off → RULE-PTY-023, the screen is hidden — REQ-PTY-070) → load QR-PTY-005 (404) → `ExposureService.openExposure(customerId)` sums every registered contributor within the 3-second budget (REQ-PTY-080, ADR-PTY-028) → availableCredit = creditLimit − openExposure (REQ-PTY-085).
Repository   : QR-PTY-005 · FIND_ONE · join PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-003 · PERM_PTY_CUSTOMER_CREDIT_VIEW
Localization : nameAr / nameEn; messages ar + en
<!-- API:API-PTY-016:END -->

<!-- API:API-PTY-017:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-072,REQ-PTY-073,REQ-PTY-106,REQ-PTY-109,REQ-PTY-110,DBF-PTY-013,DBF-PTY-015,DBF-PTY-016,DBF-PTY-018,DBF-PTY-020,DBF-PTY-078 -->
### API-PTY-017 — update credit
Entity       : ENT-PTY-002 · operation UPDATE · serves: update (credit limit, payment terms) (SCR-REQ-PTY-003)
Honours      : CON-PTY-002
Endpoint     : /api/v1/pty/customer-credits/{customerId}   verb: PUT
Layers       : CustomerCreditController.update → CustomerCreditService.update
Request      : path customerId (DBF-PTY-013, Long, required) · body `CustomerCreditUpdateRequest` — creditLimit (DBF-PTY-015, BigDecimal scale 3, nullable — null = no credit allowance, REQ-PTY-068) · paymentTermDays (DBF-PTY-016, Integer, nullable). PUT replaces both values. Excluded: everything else.
Response     : 200 · `CustomerCredit` (as API-PTY-016) · ApiResponse<CustomerCredit>
Validations  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED · RULE-PTY-005 (inactive Customer) → PTY-422-ROLE-INACTIVE · RULE-PTY-011 (walk-in and any non-null value) → PTY-422-WALK-IN-CREDIT · RULE-PTY-022 → PTY-422-CREDIT-LIMIT-NEGATIVE · RULE-PTY-008 → PTY-422-PAYMENT-TERMS-RANGE · RULE-PTY-030 (the audit write failed) → PTY-500-AUDIT-NOT-RECORDED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-CREDIT-NOT-ENABLED, PTY-422-ROLE-INACTIVE, PTY-422-WALK-IN-CREDIT, PTY-422-CREDIT-LIMIT-NEGATIVE, PTY-422-PAYMENT-TERMS-RANGE, PTY-500-AUDIT-NOT-RECORDED, PTY-500-INTERNAL
Orchestration :
1. `CreditFlag.isOn()`: off → RULE-PTY-023 (REQ-PTY-072).
2. Lock QR-PTY-006 (404).
3. `CustomerDomain.withCredit(creditLimit, paymentTermDays, flagOn)` applies RULE-PTY-005 on DBF-PTY-020 (ADR-PTY-041), RULE-PTY-011 on DBF-PTY-018, RULE-PTY-022 and RULE-PTY-008.
4. Write QR-PTY-009 through the managed entity and flush. The `@Audited(entityType = "PTY_CUSTOMER")` listener records one UPDATE audit row per changed field of DBF-PTY-015 (creditLimit) and DBF-PTY-016 (paymentTermDays): Customer, field, old value, new value, user, time, in the same transaction (REQ-PTY-106). Any failure of the given audit rolls the transaction back and is answered RULE-PTY-030 (REQ-PTY-109).
5. Return as API-PTY-016, exposure included.
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
Orchestration : `CreditFlag.isOn()` → lock QR-PTY-006 (404) → `CustomerDomain.clearCredit` (allowed for an inactive Customer — ADR-PTY-041) sets both DBF-PTY-015 (creditLimit) and DBF-PTY-016 (paymentTermDays) null (QR-PTY-009) → flush: the `@Audited` listener records old → empty for each non-null field (RULE-PTY-030 on failure, rolled back). The switch-off dependency count drops by one (AC-PTY-088).
Repository   : QR-PTY-006, QR-PTY-009 · UPDATE · READ_WRITE
Concurrency  : Customer row lock — as API-PTY-017.
Security     : SCR-REQ-PTY-003 · PERM_PTY_CUSTOMER_CREDIT_UPDATE
Localization : messages ar + en
<!-- API:API-PTY-018:END -->

<!-- API:API-PTY-019:START traces=REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-104,REQ-PTY-105,REQ-PTY-110,REQ-PTY-113,REQ-PTY-114,DBF-PTY-020,DBF-PTY-050,DBF-PTY-051,DBF-PTY-052,DBF-PTY-053,DBF-PTY-054,DBF-PTY-055,DBF-PTY-060,DBF-PTY-061,DBF-PTY-062,DBF-PTY-063,DBF-PTY-079 -->
### API-PTY-019 — create Credit override request
Entity       : ENT-PTY-005, ENT-PTY-002 · operation CREATE · serves: create (from a sale) (SCR-REQ-PTY-004)
Honours      : CON-PTY-016, CON-PTY-005, CON-PTY-008
Endpoint     : /api/v1/pty/credit-overrides   verb: POST
Layers       : CreditOverrideController.create → CreditOverrideService.create ; in-process PtyPartyApi.createCreditOverrideRequest → CreditOverrideService.create
Request      : body `CreditOverrideCreateRequest` — customerId (DBF-PTY-060, Long, required) · saleAmount (DBF-PTY-051, BigDecimal scale 3, required — RULE-PTY-032) · sourceDocumentRef (DBF-PTY-055, String ≤ 60, required). Excluded: creditOverrideRequestPk, docNo, docDate, statusCode, creditLimitAtRequest, openExposure, creditCheckResultCode, decidedBy, decidedAt, decisionReason, usedDocumentRef, audit.
Response     : 201 · `CreditOverrideRequest` (as API-PTY-020, statusCode PENDING) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED · RULE-PTY-032 → PTY-422-SALE-AMOUNT-NOT-POSITIVE · RULE-PTY-005 (inactive Customer) → PTY-422-ROLE-INACTIVE · RULE-PTY-033 → PTY-422-WALK-IN-NO-CREDIT-SALE · RULE-PTY-034 (an open request of the same sale) → PTY-409-REQUEST-OPEN-FOR-SALE ({docNo} = the open request's number) · RULE-PTY-025 → PTY-422-OVERRIDE-NOT-NEEDED · RULE-PTY-035 (the tenant's credit-overrun action is not BLOCK_WITH_OVERRIDE) → PTY-422-OVERRIDE-NOT-REQUIRED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-409-REQUEST-OPEN-FOR-SALE, PTY-422-CREDIT-NOT-ENABLED, PTY-422-ROLE-INACTIVE, PTY-422-SALE-AMOUNT-NOT-POSITIVE, PTY-422-WALK-IN-NO-CREDIT-SALE, PTY-422-OVERRIDE-NOT-NEEDED, PTY-422-OVERRIDE-NOT-REQUIRED, PTY-500-INTERNAL
Orchestration : load → validate (RULE-PTY-023, RULE-PTY-032, RULE-PTY-005, RULE-PTY-033, RULE-PTY-034, RULE-PTY-025, RULE-PTY-035) → persist (QR-PTY-031); sets the system columns DBF-PTY-061, DBF-PTY-062, DBF-PTY-063, DBF-PTY-052, DBF-PTY-053, DBF-PTY-054, DBF-PTY-060 and DBF-PTY-079 — in steps:
1. `CreditFlag.isOn()`: off → RULE-PTY-023 (REQ-PTY-072).
2. Lock the Customer QR-PTY-006 (404) and read its Party fields through QR-PTY-005.
3. QR-PTY-040 looks for an open request of (customerId, sourceDocumentRef).
4. The server re-runs the credit check of API-PTY-035 for (customerId, saleAmount). It never trusts a caller's result (ADR-PTY-028). The check's answer carries the result code and the tenant's credit-overrun action, read through `SettingsApi` in this request.
5. `CreditOverrideRequestDomain.create(request, customer, openRequest, checkResult, flagOn)` applies RULE-PTY-032, RULE-PTY-005 on DBF-PTY-020 (ADR-PTY-041), RULE-PTY-033 on DBF-PTY-018, RULE-PTY-034 (ADR-PTY-042), RULE-PTY-025 (result WITHIN_LIMIT) and, after the server-side check, RULE-PTY-035 (overrun action ≠ BLOCK_WITH_OVERRIDE → PTY-422-OVERRIDE-NOT-REQUIRED, ADR-PTY-045).
6. Allocate DBF-PTY-061 (docNo) from `NumberSeriesApi` series PTY_CREDIT_OVERRIDE, and set:
   - DBF-PTY-062 (docDate) = now; DBF-PTY-063 (statusCode) PENDING;
   - DBF-PTY-052 (creditLimitAtRequest) = the Customer's creditLimit;
   - DBF-PTY-053 (openExposure) = the check's open exposure (null when unavailable);
   - DBF-PTY-079 (currencyCode) = the Customer's DBF-PTY-078 (ADR-PTY-037);
   - DBF-PTY-054 (creditCheckResultCode) = the check's result; DBF-PTY-060 (customerFk) = customerId.
7. Insert QR-PTY-031 (createdBy = the requester, platform-filled; the `@Audited` listener records the CREATE row).
8. Publish the PTY-internal `PtyCreditOverrideRequestedEvent {creditOverrideRequestId, docNo, customerName}` through `DomainEventPublisher`.
9. After commit, `PtyApproverNotificationListener` (`@Async(ErpCoreEvents.EXECUTOR)`, `@TransactionalEventListener(phase = AFTER_COMMIT, fallbackExecution = true)`, its body run through the given's `TenantRunner` for `event.getTenantId()` — ADR-PTY-044) lists the active users holding PERM_PTY_CREDIT_OVERRIDE_UPDATE through `SecUserDirectoryApi`. It dispatches one `DispatchCommand(approverId, "PTY_CREDIT_OVERRIDE_REQUESTED", IN_APP, "PTY", creditOverrideRequestId, "PTY_CREDIT_OVERRIDE_REQUEST", {docNo, name})` per approver through `NotificationDispatchApi`. The template is seeded by the migration (BLOCK 8d, ADR-PTY-036) and reads ar «طلب تجاوز حد الائتمان {docNo} للعميل {name} بانتظار موافقتك.» · en «Credit override request {docNo} for {name} is waiting for your approval.» (REQ-PTY-093, AC-PTY-105). A failed dispatch leaves the committed request as it is.
   **Deep link.** The dispatch carries referenceType `PTY_CREDIT_OVERRIDE_REQUEST` and referenceId = creditOverrideRequestPk (DBF-PTY-050). The frontend maps referenceType `PTY_CREDIT_OVERRIDE_REQUEST` to the route `/pty/credit-overrides/:referenceId` (frontend plan F4, SCR-PTY-004); no other referenceType is dispatched by PTY.
Creation is refused while the tenant's credit-overrun action is WARN (RULE-PTY-035, ADR-PTY-045, which withdraws the WARN bullet of ADR-PTY-028): a request is accepted only under BLOCK_WITH_OVERRIDE, as REQ-PTY-091 and ADR-PTY-016 state.
Repository   : QR-PTY-006, QR-PTY-005, QR-PTY-040, QR-PTY-031 · SAVE · join PTY_PARTY on the Customer read (ADR-PTY-027) · READ_WRITE
Concurrency  : docNo is allocated atomically by `NumberSeriesApi`, with UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO as the backstop. Two requests for the same sale are serialised by the Customer lock. UQ_PTY_CREDIT_OVERRIDE_REQUEST_OPEN_SOURCE is the backstop, and its violation is answered RULE-PTY-034 after QR-PTY-040 re-reads the holder (ADR-PTY-042). A deactivation of the Customer waits on the same lock (API-PTY-004).
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_CREATE
Localization : notification and messages in ar + en
<!-- API:API-PTY-019:END -->

<!-- API:API-PTY-020:START traces=REQ-PTY-103,DBF-PTY-050,DBF-PTY-051,DBF-PTY-052,DBF-PTY-053,DBF-PTY-054,DBF-PTY-055,DBF-PTY-056,DBF-PTY-057,DBF-PTY-058,DBF-PTY-059,DBF-PTY-060,DBF-PTY-061,DBF-PTY-062,DBF-PTY-063,DBF-PTY-064,DBF-PTY-079 -->
### API-PTY-020 — read Credit override request
Entity       : ENT-PTY-005 · operation VIEW · serves: read (SCR-REQ-PTY-004)
Honours      : CON-PTY-017, CON-PTY-005
Endpoint     : /api/v1/pty/credit-overrides/{id}   verb: GET
Layers       : CreditOverrideController.get → CreditOverrideService.read ; in-process PtyPartyApi.readCreditOverrideRequest → CreditOverrideService.read
Request      : path id (DBF-PTY-050, Long, required)
Response     : 200 · `CreditOverrideRequest` {creditOverrideRequestPk (DBF-PTY-050), docNo (DBF-PTY-061), docDate (DBF-PTY-062), statusCode (DBF-PTY-063), customerFk (DBF-PTY-060), customerCode, customerNameAr, customerNameEn (read through DBF-PTY-060), saleAmount (DBF-PTY-051), creditLimitAtRequest (DBF-PTY-052), openExposure (DBF-PTY-053), currencyCode (DBF-PTY-079), creditCheckResultCode (DBF-PTY-054), sourceDocumentRef (DBF-PTY-055), decidedBy (DBF-PTY-056), decidedAt (DBF-PTY-057), decisionReason (DBF-PTY-058), usedDocumentRef (DBF-PTY-059), createdBy (DBF-PTY-064), createdAt, updatedBy, updatedAt (DBF-PTY-065 … DBF-PTY-067)} · ApiResponse<CreditOverrideRequest>
Validations  : visibility — a caller without PERM_PTY_CREDIT_OVERRIDE_UPDATE reads only the requests it created (REQ-PTY-102 applied to reads); another's request answers PTY-404-CREDIT-OVERRIDE-NOT-FOUND (ADR-PTY-025)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-500-INTERNAL
Orchestration : load QR-PTY-032 (absent or not visible → 404) → map.
Repository   : QR-PTY-032 · FIND_ONE · join PTY_CUSTOMER, PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_VIEW
Localization : Customer names ar + en
<!-- API:API-PTY-020:END -->

<!-- API:API-PTY-021:START traces=REQ-PTY-094,REQ-PTY-098,REQ-PTY-107,REQ-PTY-109,REQ-PTY-112,DBF-PTY-050,DBF-PTY-056,DBF-PTY-057,DBF-PTY-061,DBF-PTY-063 -->
### API-PTY-021 — approve Credit override request
Entity       : ENT-PTY-005 · operation UPDATE · serves: approve (SCR-REQ-PTY-004)
Honours      : CON-PTY-005, CON-PTY-008
Endpoint     : /api/v1/pty/credit-overrides/{id}/approval   verb: PATCH
Layers       : CreditOverrideController.approve → CreditOverrideService.approve
Request      : path id (DBF-PTY-050, Long, required) · body `CreditOverrideDecisionRequest` (optional) — docNo (DBF-PTY-061, String, optional — compared only, RULE-PTY-031)
Response     : 200 · `CreditOverrideRequest` (as API-PTY-020, statusCode APPROVED) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-023 (`b2bCredit` off) → PTY-422-CREDIT-NOT-ENABLED · RULE-PTY-031 (docNo present and different) → PTY-422-DOC-NO-READ-ONLY · RULE-PTY-026 (not PENDING) → PTY-409-REQUEST-NOT-PENDING ({statusAr} / {statusEn} = CREDIT_OVERRIDE_STATUS labels) · RULE-PTY-030 (the audit write failed) → PTY-500-AUDIT-NOT-RECORDED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-409-REQUEST-NOT-PENDING, PTY-422-DOC-NO-READ-ONLY, PTY-422-CREDIT-NOT-ENABLED, PTY-500-AUDIT-NOT-RECORDED, PTY-500-INTERNAL
Orchestration :
1. `CreditFlag.isOn()`: off → RULE-PTY-023 (REQ-PTY-112).
2. Lock QR-PTY-033 (404).
3. `CreditOverrideRequestDomain.approve(docNo, decider, now)` applies RULE-PTY-031 and RULE-PTY-026, then sets DBF-PTY-063 (statusCode) APPROVED, DBF-PTY-056 (decidedBy) = the current sec user identifier and DBF-PTY-057 (decidedAt) = now.
4. Conditional write QR-PTY-034 (expected PENDING); the `@Audited` listener records the UPDATE row.
5. `AuditApi.record(action APPROVE, entityType PTY_CREDIT_OVERRIDE_REQUEST, {docNo, Customer, saleAmount, APPROVED, decider, time})` runs inside the transaction (REQ-PTY-107).
6. Flush. Any failure of the given audit rolls the transaction back, and the request stays PENDING: RULE-PTY-030 (REQ-PTY-109, ADR-PTY-041).
Repository   : QR-PTY-033, QR-PTY-034 · UPDATE · join NONE · READ_WRITE
Concurrency  : Two approvers acting at once: QR-PTY-033 locks the row (`FOR UPDATE`) and QR-PTY-034 updates only `WHERE STATUS_CODE = 'PENDING'` — the second decision finds APPROVED (or REJECTED) and receives PTY-409-REQUEST-NOT-PENDING (ADR-PTY-026).
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_UPDATE (the credit-override permission of POL-PTY-008, SRS D-06)
Localization : messages ar + en
<!-- API:API-PTY-021:END -->

<!-- API:API-PTY-022:START traces=REQ-PTY-095,REQ-PTY-098,REQ-PTY-100,REQ-PTY-107,REQ-PTY-109,REQ-PTY-112,DBF-PTY-050,DBF-PTY-056,DBF-PTY-057,DBF-PTY-058,DBF-PTY-061,DBF-PTY-063 -->
### API-PTY-022 — reject Credit override request
Entity       : ENT-PTY-005 · operation UPDATE · serves: reject (SCR-REQ-PTY-004)
Honours      : CON-PTY-005, CON-PTY-008
Endpoint     : /api/v1/pty/credit-overrides/{id}/rejection   verb: PATCH
Layers       : CreditOverrideController.reject → CreditOverrideService.reject
Request      : path id (DBF-PTY-050, Long, required) · body `CreditOverrideRejectRequest` — decisionReason (DBF-PTY-058, String ≤ 500, required non-blank — RULE-PTY-027) · docNo (DBF-PTY-061, optional — compared only, RULE-PTY-031)
Response     : 200 · `CreditOverrideRequest` (statusCode REJECTED) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED · RULE-PTY-031 → PTY-422-DOC-NO-READ-ONLY · RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING · RULE-PTY-027 → PTY-422-REJECTION-REASON-REQUIRED · RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-409-REQUEST-NOT-PENDING, PTY-422-DOC-NO-READ-ONLY, PTY-422-REJECTION-REASON-REQUIRED, PTY-422-CREDIT-NOT-ENABLED, PTY-500-AUDIT-NOT-RECORDED, PTY-500-INTERNAL
Orchestration :
1. `CreditFlag.isOn()`: off → RULE-PTY-023 (REQ-PTY-112).
2. Lock QR-PTY-033 (404).
3. `CreditOverrideRequestDomain.reject(docNo, reason, decider, now)` applies RULE-PTY-031, RULE-PTY-026 and RULE-PTY-027, then sets DBF-PTY-063 (statusCode) REJECTED, DBF-PTY-056 (decidedBy), DBF-PTY-057 (decidedAt) and DBF-PTY-058 (decisionReason).
4. Write QR-PTY-034 (expected PENDING).
5. `AuditApi.record(action REJECT, …)` runs inside the transaction (REQ-PTY-107).
6. Flush. An audit failure rolls back: RULE-PTY-030 (REQ-PTY-109).
Repository   : QR-PTY-033, QR-PTY-034 · UPDATE · READ_WRITE
Concurrency  : as API-PTY-021 — row lock + conditional update; the loser receives PTY-409-REQUEST-NOT-PENDING.
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_UPDATE
Localization : messages ar + en
<!-- API:API-PTY-022:END -->

<!-- API:API-PTY-023:START traces=REQ-PTY-096,REQ-PTY-098,REQ-PTY-112,DBF-PTY-050,DBF-PTY-063,DBF-PTY-064 -->
### API-PTY-023 — cancel Credit override request
Entity       : ENT-PTY-005 · operation DELETE · serves: cancel (SCR-REQ-PTY-004)
Honours      : CON-PTY-005
Endpoint     : /api/v1/pty/credit-overrides/{id}   verb: DELETE
Layers       : CreditOverrideController.cancel → CreditOverrideService.cancel
Request      : path id (DBF-PTY-050, Long, required) · no body
Response     : 200 · `CreditOverrideRequest` (statusCode CANCELLED) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-029 (caller is not the creator, DBF-PTY-064) → PTY-403-NOT-REQUESTER · RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-403-NOT-REQUESTER, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-409-REQUEST-NOT-PENDING, PTY-500-INTERNAL
Orchestration : lock QR-PTY-033 (404) → `CreditOverrideRequestDomain.cancel(principal)`: RULE-PTY-029 (DBF-PTY-064 createdBy equals the current principal) → RULE-PTY-026 → DBF-PTY-063 (statusCode) CANCELLED → QR-PTY-034 (expected PENDING; the `@Audited` listener records it). The row is kept (soft): cancelling is a state, not a deletion. The flag is not read: a cancellation stays allowed while `b2bCredit` is off (REQ-PTY-112, ADR-PTY-041).
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
