<!-- source: PHASE:SVC-API / SUB:SVC-API-INT -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-PTY-002, DBF-PTY-003, DBF-PTY-005, DBF-PTY-006, DBF-PTY-007, DBF-PTY-008, DBF-PTY-013, DBF-PTY-014, DBF-PTY-015, DBF-PTY-016, DBF-PTY-017, DBF-PTY-018, DBF-PTY-019, DBF-PTY-020, DBF-PTY-034, DBF-PTY-035, DBF-PTY-036, DBF-PTY-037, DBF-PTY-038, DBF-PTY-039, DBF-PTY-040, DBF-PTY-041, DBF-PTY-044, DBF-PTY-045, DBF-PTY-050, DBF-PTY-051, DBF-PTY-055, DBF-PTY-059, DBF-PTY-060, DBF-PTY-061, DBF-PTY-063, DBF-PTY-078, REQ-PTY-001, REQ-PTY-044, REQ-PTY-045, REQ-PTY-046, REQ-PTY-050, REQ-PTY-051, REQ-PTY-052, REQ-PTY-053, REQ-PTY-054, REQ-PTY-055, REQ-PTY-056, REQ-PTY-057, REQ-PTY-058, REQ-PTY-059, REQ-PTY-060, REQ-PTY-061, REQ-PTY-062, REQ-PTY-063, REQ-PTY-068, REQ-PTY-074, REQ-PTY-075, REQ-PTY-076, REQ-PTY-077, REQ-PTY-079, REQ-PTY-080, REQ-PTY-081, REQ-PTY-082, REQ-PTY-083, REQ-PTY-084, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-097, REQ-PTY-099, REQ-PTY-110, REQ-PTY-111, REQ-PTY-112 -->
<!-- SUB:SVC-API-INT:START traces=REQ-PTY-001,REQ-PTY-044,REQ-PTY-045,REQ-PTY-046,REQ-PTY-050,REQ-PTY-051,REQ-PTY-052,REQ-PTY-053,REQ-PTY-054,REQ-PTY-055,REQ-PTY-056,REQ-PTY-057,REQ-PTY-058,REQ-PTY-059,REQ-PTY-060,REQ-PTY-061,REQ-PTY-062,REQ-PTY-063,REQ-PTY-068,REQ-PTY-074,REQ-PTY-075,REQ-PTY-076,REQ-PTY-077,REQ-PTY-079,REQ-PTY-080,REQ-PTY-081,REQ-PTY-082,REQ-PTY-083,REQ-PTY-084,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-097,REQ-PTY-099,REQ-PTY-110,REQ-PTY-111,REQ-PTY-112 -->
### SVC-API-INT — operations offered to consuming modules (SRS A8.1)

These endpoints are also exposed in-process through `PtyPartyApi` (PHASE 1 — CORE). The REST path and the in-process method share one service method and its `@PreAuthorize` (ADR-PTY-039). They describe what PTY offers; who calls them is the callers' concern (inbound edges, derived by `gov.py graph`).

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
Orchestration : QR-PTY-012 → found: return → none (REQ-PTY-046): in a new transaction (REQUIRES_NEW) allocate DBF-PTY-007 (code) from `NumberSeriesApi`, insert Party QR-PTY-002 with DBF-PTY-005 (nameAr) «عميل نقدي», DBF-PTY-006 (nameEn) "Walk-in customer", DBF-PTY-008 (isActiveFl) true, then Customer QR-PTY-008 with DBF-PTY-019 (partyFk), DBF-PTY-014 (customerGroupCode) RETAIL, DBF-PTY-018 (isWalkInFl) true, DBF-PTY-020 (isActiveFl) true, DBF-PTY-078 (currencyCode) = `PtyCurrency.baseCurrency()` (REQ-PTY-044, SRS D-08) → re-read QR-PTY-012 and return.
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
  Matched: lock the Party QR-PTY-001 → QR-PTY-007 its Customer → none: insert a Customer QR-PTY-008 with DBF-PTY-019 (partyFk), DBF-PTY-014 RETAIL, DBF-PTY-018 false, DBF-PTY-020 true, DBF-PTY-078 base currency (REQ-PTY-057) → inactive Customer → `CustomerDomain.requireActiveForGuest`: RULE-PTY-021 (the message reveals nothing — REQ-PTY-062) → the Party's names, contacts and Addresses are left unchanged (REQ-PTY-058) → when the Customer's DBF-PTY-017 (secUserId) is empty, publish the PTY-internal `PtyInvitationRequestedEvent {customerId, channel, address}` addressed to the email **recorded on the Party**, else its recorded mobile (REQ-PTY-059); when it is set, no event and no invitation (REQ-PTY-060).
  Create (REQ-PTY-050): DBF-PTY-007 (code) from `NumberSeriesApi` → `PartyDomain.createGuest` → Party QR-PTY-002 (names, email, mobile, DBF-PTY-008 true) → Customer QR-PTY-008 (RETAIL, DBF-PTY-018 false, DBF-PTY-020 true, DBF-PTY-078 base currency) → Address QR-PTY-024 / QR-PTY-025 with DBF-PTY-034 (addressTypeCode) DELIVERY, DBF-PTY-044 (code) "1", DBF-PTY-040 (isDefaultFl) true, DBF-PTY-041 (partyFk), DBF-PTY-045 (isActiveFl) true and the delivery lines → `PtyInvitationRequestedEvent` to the email, else the mobile (REQ-PTY-051); DBF-PTY-017 stays empty until redemption (AC-PTY-059).
  After commit: `PtyInvitationListener` (`@Async(ErpCoreEvents.EXECUTOR)`, `@TransactionalEventListener(phase = AFTER_COMMIT, fallbackExecution = true)`) runs its body through the given's `TenantRunner` for `event.getTenantId()` (ADR-PTY-044) and calls `SecCustomerInvitationApi.issue(new IssueInvitation("PTY", "CUSTOMER", customerId, channel, address, …))` in its own transaction. A rollback publishes nothing; a failed issue is logged and leaves the resolved Customer as it is (checklist C4, C17).
Repository   : QR-PTY-036, QR-PTY-037, QR-PTY-001, QR-PTY-007, QR-PTY-002, QR-PTY-008, QR-PTY-024, QR-PTY-025 · FIND_BY_CRITERIA, NATIVE, SAVE · join NONE · READ_WRITE
Concurrency  : Two orders of one new guest at once both match nothing and both create a Party with the same email: UQ_PTY_PARTY_EMAIL rejects the second insert; the creation runs in a REQUIRES_NEW transaction and the service retries the matching once, which now attaches the order to the first Customer (ADR-PTY-026). End state: one Party, one Customer, both callers answered 200 with the same customerId (one with created true, the other false); each call requests its invitation for that Customer, the creation under REQ-PTY-051 and the re-match under REQ-PTY-059, both after commit; the loser receives no error code (TC-PTY-194). Mobile is not unique (SRS D-03), so two mobile-only guests created at once may produce two Parties — the same outcome REQ-PTY-061 prescribes for an ambiguous mobile.
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_CREATE on the controller and on `GuestCustomerService.resolve`, for REST and in-process callers alike (ADR-PTY-039). The anonymous storefront shopper holds no PTY authority, and the core publishes no mechanism for it. `CORE-GAP-in-process-caller-authority` is OPEN, and until it is answered the call is refused. No v1 caller is blocked, because SFR is wave 2.
Localization : messages ar + en
<!-- API:API-PTY-034:END -->

<!-- API:API-PTY-035:START traces=REQ-PTY-068,REQ-PTY-074,REQ-PTY-079,REQ-PTY-080,REQ-PTY-081,REQ-PTY-082,REQ-PTY-083,REQ-PTY-084,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-110,DBF-PTY-013,DBF-PTY-015,DBF-PTY-018,DBF-PTY-020,DBF-PTY-078 -->
### API-PTY-035 — credit check
Entity       : ENT-PTY-002 · operation VIEW · serves: the credit check of a sale (no screen of its own)
Honours      : CON-PTY-015, CON-PTY-022, CON-PTY-008
Endpoint     : /api/v1/pty/credit-checks   verb: POST
Layers       : CreditCheckController.check → CreditCheckService.check ; in-process PtyPartyApi.checkCredit → CreditCheckService.check
Request      : body `CreditCheckRequest` — customerId (DBF-PTY-013, Long, required) · saleAmount (BigDecimal scale 3, required, > 0 — typed as DBF-PTY-051; ≤ 0 → PTY-400-VALIDATION)
Response     : 200 · `CreditCheckResult` {customerId, resultCode (CREDIT_CHECK_RESULT code), creditLimit (DBF-PTY-015), openExposure, projectedExposure, currencyCode (DBF-PTY-078 — the currency of the three amounts), overrunActionCode (CREDIT_OVERRUN_ACTION code — present for OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE), approvalRequired (Boolean)} · ApiResponse<CreditCheckResult>
Validations  : RULE-PTY-005 (inactive Customer) → PTY-422-ROLE-INACTIVE (REQ-PTY-110, ADR-PTY-041); every other outcome is a result code
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CUSTOMER-NOT-FOUND, PTY-422-ROLE-INACTIVE, PTY-500-INTERNAL
Orchestration : the order of the checks is fixed: **load the Customer (404) → RULE-PTY-005 → flag**. Load QR-PTY-005 (absent → PTY-404-CUSTOMER-NOT-FOUND) → `CustomerDomain.requireActive` on DBF-PTY-020 (RULE-PTY-005 → PTY-422-ROLE-INACTIVE, whatever the flag — AC-PTY-140) → `CreditFlag.isOn()` off → NOT_APPLICABLE, approvalRequired false (REQ-PTY-074) → openExposure = sum of every registered `CustomerCreditExposureContributor` (0 when none — REQ-PTY-080), each invoked under a 3-second budget; any contributor not answering in time or failing → exposure unavailable (REQ-PTY-084) → overrun action from `SettingsApi` PTY_CREDIT_OVERRUN_ACTION, default BLOCK_WITH_OVERRIDE (REQ-PTY-087) → result: DBF-PTY-015 empty → NO_CREDIT (REQ-PTY-068, REQ-PTY-083; the walk-in Customer, DBF-PTY-018 true, always lands here) · exposure unavailable → EXPOSURE_UNAVAILABLE with openExposure empty · projectedExposure = openExposure + saleAmount ≤ creditLimit → WITHIN_LIMIT (REQ-PTY-081) · otherwise OVER_LIMIT (REQ-PTY-082) → OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE carry the overrun action and approvalRequired = (action = BLOCK_WITH_OVERRIDE) (REQ-PTY-088, REQ-PTY-089). Contributors are in-process beans: the budget is a time limit on a local call, not a network timeout (ADR-PTY-028).
Repository   : QR-PTY-005 · FIND_ONE · join PTY_PARTY · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes (the answer is advisory; the approval it may lead to is decided under its own lock, API-PTY-021)
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_VIEW on the controller and `CreditCheckService.check`, for REST and in-process callers (the seller performing a sale holds it — ADR-PTY-039)
Localization : result and action codes; labels from mdl
<!-- API:API-PTY-035:END -->

<!-- API:API-PTY-036:START traces=REQ-PTY-097,REQ-PTY-099,REQ-PTY-110,REQ-PTY-111,REQ-PTY-112,DBF-PTY-020,DBF-PTY-050,DBF-PTY-051,DBF-PTY-055,DBF-PTY-059,DBF-PTY-060,DBF-PTY-061,DBF-PTY-063 -->
### API-PTY-036 — use Credit override request
Entity       : ENT-PTY-005 · operation UPDATE · serves: use of an approval by a completed sale (no screen of its own)
Honours      : CON-PTY-018, CON-PTY-005
Endpoint     : /api/v1/pty/credit-overrides/{id}/use   verb: PATCH
Layers       : CreditOverrideController.use → CreditOverrideService.use ; in-process PtyPartyApi.useCreditOverrideRequest → CreditOverrideService.use
Request      : path id (DBF-PTY-050, Long, required) · body `CreditOverrideUseRequest` — customerId (compared with DBF-PTY-060, Long, required) · saleAmount (compared with DBF-PTY-051, BigDecimal, required) · usedDocumentRef (DBF-PTY-059, String ≤ 60, required — must equal DBF-PTY-055 sourceDocumentRef, RULE-PTY-028, ADR-PTY-042) · docNo (DBF-PTY-061, optional — compared only, RULE-PTY-031)
Response     : 200 · `CreditOverrideRequest` (statusCode USED) · ApiResponse<CreditOverrideRequest>
Validations  : RULE-PTY-023 (`b2bCredit` off) → PTY-422-CREDIT-NOT-ENABLED · RULE-PTY-031 → PTY-422-DOC-NO-READ-ONLY · RULE-PTY-005 (the Customer is inactive) → PTY-422-ROLE-INACTIVE · RULE-PTY-028 (not APPROVED, another Customer, another sale reference than sourceDocumentRef, or the approved amount is smaller than the sale) → PTY-422-APPROVAL-NOT-COVERING ({docNo})
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-422-DOC-NO-READ-ONLY, PTY-422-APPROVAL-NOT-COVERING, PTY-422-ROLE-INACTIVE, PTY-422-CREDIT-NOT-ENABLED, PTY-500-INTERNAL
Orchestration : `CreditFlag.isOn()` (off → RULE-PTY-023, REQ-PTY-112) → lock QR-PTY-033 (404) → read the Customer's DBF-PTY-020 through QR-PTY-005 → `CreditOverrideRequestDomain.use(customerId, saleAmount, usedDocumentRef, docNo, customerActive)`: RULE-PTY-031 → RULE-PTY-005 → RULE-PTY-028 → DBF-PTY-063 (statusCode) USED, DBF-PTY-059 (usedDocumentRef) → conditional write QR-PTY-034 (expected APPROVED; the `@Audited` listener records it). A request CANCELLED by its Customer's deactivation answers RULE-PTY-028 (REQ-PTY-111).
Repository   : QR-PTY-033, QR-PTY-034 · UPDATE · join NONE · READ_WRITE
Concurrency  : One approval covers one sale: two sales using the same approval at once are serialised by the row lock, and QR-PTY-034 updates only `WHERE STATUS_CODE = 'APPROVED'` — the second finds USED and receives PTY-422-APPROVAL-NOT-COVERING (AC-PTY-113). CHK_PTY_CREDIT_OVERRIDE_REQUEST_USED_DOCUMENT_REF is the database backstop of the sale binding.
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_CREATE on the controller and `CreditOverrideService.use`, for REST and in-process callers (the requester's action, held by whoever completes the credit sale — ADR-PTY-039)
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

**Outbox subscriber — CUSTOMER-realm invitation redeemed (no endpoint; REQ-PTY-052, REQ-PTY-054).**

`PtyCustomerInvitationRedeemedSubscriber implements com.erp.events.OutboxSubscriber` subscribes to the given `sec` outbox event `sec.CustomerInvitationRedeemed`:
- `code()` = "pty.customerInvitationRedeemed", `module()` = "pty", `eventTypes()` = {"sec.CustomerInvitationRedeemed"};
- the manifest lists the type under `events.consumes` (checklist C17, C19);
- it handles only invitations PTY issued, that is subject module PTY and subject type CUSTOMER. The subject reference is the customerPk, and the redeemer is the accepting user's identifier, as the given publishes them;
- the given runs it after the redemption commits, at least once, in the event's tenant.

`handle(message)` runs in one READ_WRITE transaction:
1. Lock the Customer with QR-PTY-006.
2. `CustomerDomain.linkSecUser(redeemer)` decides:
   - the walk-in Customer (DBF-PTY-018 true) gives outcome REFUSED with RULE-PTY-013's message, and the Customer is left unlinked;
   - DBF-PTY-017 (secUserId) already equal to the redeemer leaves it unchanged — the handler is idempotent on `OutboxMessage.id()`, so a redelivered message changes nothing;
   - otherwise it sets DBF-PTY-017 to the redeemer's identifier through QR-PTY-009.
3. If UQ_PTY_CUSTOMER_SEC_USER_ID (TENANT_ID, SEC_USER_ID) rejects the write, because the user is already linked to another Customer, the outcome is REFUSED with RULE-PTY-020's message and the Customer is left unlinked (AC-PTY-062).

A REFUSED outcome is recorded with its rule message (ar + en, as stated in DATA-DOM) through `AuditApi.record(action LINK_REFUSED, entityType PTY_CUSTOMER, …)` and completes the delivery. It is a business outcome, not a retryable failure. No HTTP status and no Error Catalog row exists for it.

The subscriber reuses QR-PTY-006 and QR-PTY-009, which API-PTY-003 and API-PTY-017 also cite.
<!-- SUB:SVC-API-INT:END -->
