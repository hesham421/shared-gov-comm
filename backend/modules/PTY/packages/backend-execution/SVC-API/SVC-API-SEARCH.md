<!-- source: PHASE:SVC-API / SUB:SVC-API-SEARCH -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-PTY-002, DBF-PTY-003, DBF-PTY-004, DBF-PTY-005, DBF-PTY-006, DBF-PTY-007, DBF-PTY-013, DBF-PTY-014, DBF-PTY-015, DBF-PTY-016, DBF-PTY-018, DBF-PTY-020, DBF-PTY-025, DBF-PTY-026, DBF-PTY-028, DBF-PTY-034, DBF-PTY-040, DBF-PTY-041, DBF-PTY-045, DBF-PTY-050, DBF-PTY-051, DBF-PTY-052, DBF-PTY-053, DBF-PTY-054, DBF-PTY-060, DBF-PTY-061, DBF-PTY-062, DBF-PTY-063, DBF-PTY-064, DBF-PTY-078, DBF-PTY-079, REQ-PTY-001, REQ-PTY-013, REQ-PTY-018, REQ-PTY-032, REQ-PTY-038, REQ-PTY-039, REQ-PTY-041, REQ-PTY-042, REQ-PTY-043, REQ-PTY-069, REQ-PTY-070, REQ-PTY-073, REQ-PTY-077, REQ-PTY-086, REQ-PTY-101, REQ-PTY-102, REQ-PTY-108 -->
<!-- SUB:SVC-API-SEARCH:START traces=REQ-PTY-001,REQ-PTY-013,REQ-PTY-018,REQ-PTY-032,REQ-PTY-038,REQ-PTY-039,REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,REQ-PTY-069,REQ-PTY-070,REQ-PTY-073,REQ-PTY-077,REQ-PTY-086,REQ-PTY-101,REQ-PTY-102,REQ-PTY-108 -->
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

<!-- API:API-PTY-027:START traces=REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-013,DBF-PTY-018,DBF-PTY-020 -->
### API-PTY-027 — Customer global search
Entity       : ENT-PTY-002, ENT-PTY-001 · operation VIEW · serves: the one global search (no screen of its own)
Honours      : CON-PTY-020
Endpoint     : /api/v1/pty/customers/global-search   verb: GET
Layers       : CustomerController.globalSearch → CustomerQueryService.globalSearch ; in-process PtyPartyApi.globalSearchCustomers → CustomerQueryService.globalSearch (each entry tests VIEW first — Security below)
Request      : query term (String, required)
Response     : 200 · list (≤ 10) of `CustomerSearchHit` {customerPk (DBF-PTY-013), code (DBF-PTY-007), nameAr (DBF-PTY-005), nameEn (DBF-PTY-006), mobile (DBF-PTY-002), isWalkInFl (DBF-PTY-018 — lets a picker hide the walk-in Customer)} · not paginated · ApiResponse<List<CustomerSearchHit>>
Validations  : term shorter than 2 characters → empty list (REQ-PTY-041, AC-PTY-049) · caller without PERM_PTY_CUSTOMER_VIEW → empty list, not an error (REQ-PTY-043)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-500-INTERNAL
Orchestration : entry layer — no VIEW → empty list, without calling the service → service: length check → QR-PTY-011 (active Customers only; code, nameAr, nameEn, mobile, email, taxRegistrationNo contain the term without letter case; Arabic أ إ آ → ا, ة → ه, ى → ي on both sides — REQ-PTY-042; the mobile is compared with spaces and dashes removed from both sides; `%` and `_` in the term match literally; exact code matches first, then the display name in the request language ascending; LIMIT 10). Suppliers are never returned (ADR-PTY-013). The walk-in Customer is active and is returned, marked by isWalkInFl.
Repository   : QR-PTY-011 · NATIVE (ADR-PTY-027) · join PTY_PARTY · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-001 · PERM_PTY_CUSTOMER_VIEW. REQ-PTY-043's empty answer is given at the entry layer: `CustomerController.globalSearch` and the in-process delegate `PtyPartyApi.globalSearchCustomers` each test PERM_PTY_CUSTOMER_VIEW through `SecurityContextHelper` and return an empty list without it; otherwise they call `CustomerQueryService.globalSearch`, which carries `@PreAuthorize("hasAuthority(T(com.commerce.pty.permission.PtyPermissions).PERM_PTY_CUSTOMER_VIEW)")` like every other service method (checklist C13, ADR-PTY-039 §3). No service method is exempt.
Localization : names ar + en
<!-- API:API-PTY-027:END -->

<!-- API:API-PTY-028:START traces=REQ-PTY-018,DBF-PTY-002,DBF-PTY-003,DBF-PTY-004,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-025,DBF-PTY-026,DBF-PTY-028 -->
### API-PTY-028 — search Suppliers
Entity       : ENT-PTY-003, ENT-PTY-001 · operation VIEW · serves: search, list (SCR-REQ-PTY-002)
Honours      : CON-PTY-003
Endpoint     : /api/v1/pty/suppliers   verb: GET
Layers       : SupplierController.search → SupplierQueryService.search
Request      : query code (DBF-PTY-007, contains) · name (DBF-PTY-005 / DBF-PTY-006, contains, case-insensitive) · mobile (DBF-PTY-002, contains) · email (DBF-PTY-003, contains, case-insensitive — the RULE-PTY-002 action reaches a Supplier-only Party through it, ADR-PTY-043) · taxRegistrationNo (DBF-PTY-004, contains) · isActive (DBF-PTY-028, exact) · page, size · sort — one of code, nameAr, nameEn, mobile, email, taxRegistrationNo, isActiveFl, default code ascending
Response     : 200 · Page<`SupplierRow`> {supplierPk (DBF-PTY-025), code, nameAr, nameEn, mobile, email (DBF-PTY-003), taxRegistrationNo, paymentTermDays (DBF-PTY-026), isActiveFl (DBF-PTY-028)} · paginated · ApiResponse<Page<SupplierRow>>
Validations  : none
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-500-INTERNAL
Orchestration : QR-PTY-020 → map (AC-PTY-023: name "Delta" + isActive true returns exactly the two active matches; AC-PTY-136: email "Delta@Example.com" + isActive true returns the one Supplier-only Party).
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
Security     : SCR-REQ-PTY-001 / SCR-REQ-PTY-002 · hasAnyAuthority(PERM_PTY_CUSTOMER_VIEW, PERM_PTY_SUPPLIER_VIEW) and the VIEW of a role the Party holds — either one when it holds both (ADR-PTY-039)
Localization : address labels ar + en
<!-- API:API-PTY-029:END -->

<!-- API:API-PTY-030:START traces=REQ-PTY-069,REQ-PTY-070,REQ-PTY-073,REQ-PTY-077,DBF-PTY-005,DBF-PTY-006,DBF-PTY-007,DBF-PTY-013,DBF-PTY-014,DBF-PTY-015,DBF-PTY-016,DBF-PTY-078 -->
### API-PTY-030 — search Customer credit
Entity       : ENT-PTY-002, ENT-PTY-001 · operation VIEW · serves: search, list (SCR-REQ-PTY-003)
Honours      : CON-PTY-002
Endpoint     : /api/v1/pty/customer-credits   verb: GET
Layers       : CustomerCreditController.search → CustomerCreditService.search
Request      : query code (DBF-PTY-007, contains) · name (DBF-PTY-005 / DBF-PTY-006, contains) · customerGroupCode (DBF-PTY-014, exact) · carriesCredit (Boolean — DBF-PTY-015 or DBF-PTY-016 non-empty; `true` is the pre-filter of the switch-off next action, REQ-PTY-077) · page, size · sort — one of code, nameAr, nameEn, customerGroupCode, creditLimit, paymentTermDays, default code ascending
Response     : 200 · Page<`CustomerCreditRow`> {customerPk (DBF-PTY-013), code, nameAr, nameEn, customerGroupCode, creditLimit (DBF-PTY-015), paymentTermDays (DBF-PTY-016), currencyCode (DBF-PTY-078)} · paginated · ApiResponse<Page<CustomerCreditRow>>
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
Orchestration : `CreditFlag.isOn()` → QR-PTY-005 (404) → `AuditApi` reads the audit rows of entity type PTY_CUSTOMER and entity id customerPk written by the `@Audited` listener whose changes name creditLimit or paymentTermDays, one entry per changed field, newest first, paged → `SecUserDirectoryApi` resolves user names (derived — ADR-PTY-028). The history stays readable for an inactive Customer (ADR-PTY-041).
Repository   : QR-PTY-005 · FIND_ONE · join PTY_PARTY · READ_ONLY (history itself lives in the given audit module — no PTY table)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-003 · PERM_PTY_CUSTOMER_CREDIT_VIEW
Localization : field labels resolved by the client; messages ar + en
<!-- API:API-PTY-031:END -->

<!-- API:API-PTY-032:START traces=REQ-PTY-101,REQ-PTY-102,DBF-PTY-050,DBF-PTY-051,DBF-PTY-052,DBF-PTY-053,DBF-PTY-054,DBF-PTY-060,DBF-PTY-061,DBF-PTY-062,DBF-PTY-063,DBF-PTY-064,DBF-PTY-079 -->
### API-PTY-032 — search Credit override requests
Entity       : ENT-PTY-005, ENT-PTY-002 · operation VIEW · serves: search, list (SCR-REQ-PTY-004)
Honours      : CON-PTY-005
Endpoint     : /api/v1/pty/credit-overrides   verb: GET
Layers       : CreditOverrideController.search → CreditOverrideService.search
Request      : query docNo (DBF-PTY-061, exact) · statusCode (DBF-PTY-063, exact) · customerId (DBF-PTY-060, exact) · docDateFrom, docDateTo (DBF-PTY-062, date range, inclusive) · page, size · sort — optional, one of docNo, statusCode, docDate; default PENDING first then docDate descending (REQ-PTY-101)
Response     : 200 · Page<`CreditOverrideRow`> {creditOverrideRequestPk (DBF-PTY-050), docNo, docDate, statusCode, customerFk (DBF-PTY-060), customerCode, customerNameAr, customerNameEn, saleAmount (DBF-PTY-051), creditLimitAtRequest (DBF-PTY-052), openExposure (DBF-PTY-053), currencyCode (DBF-PTY-079), creditCheckResultCode (DBF-PTY-054), createdBy (DBF-PTY-064)} · paginated · ApiResponse<Page<CreditOverrideRow>>
Validations  : none — visibility: a caller without PERM_PTY_CREDIT_OVERRIDE_UPDATE sees only the requests it created (REQ-PTY-102)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-500-INTERNAL
Orchestration : decide the visibility filter from the caller's authorities → QR-PTY-035 → map.
Repository   : QR-PTY-035 · FIND_BY_CRITERIA · join PTY_CUSTOMER, PTY_PARTY (ADR-PTY-027) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-004 · PERM_PTY_CREDIT_OVERRIDE_VIEW
Localization : Customer names ar + en
<!-- API:API-PTY-032:END -->

<!-- API:API-PTY-038:START traces=REQ-PTY-013,REQ-PTY-038,REQ-PTY-039,REQ-PTY-086,DBF-PTY-014,DBF-PTY-034,DBF-PTY-054,DBF-PTY-063 -->
### API-PTY-038 — read a PTY lookup key
Entity       : ENT-PTY-002, ENT-PTY-004, ENT-PTY-005 · operation VIEW · serves: the lookup-backed fields and filters of SCR-REQ-PTY-001 … SCR-REQ-PTY-005 (customer group, address type, credit-overrun action, credit check result, request status)
Honours      : CON-PTY-006, CON-PTY-007, CON-PTY-008
Endpoint     : /api/v1/pty/lookups/{key}   verb: GET
Layers       : PtyLookupController.read → PtyLookupService.read → `com.erp.common.lookup.OwnedLookups.read` over `MdlLookupApi` (ADR-PTY-048)
Request      : path key (String ≤ 50, required) — one of the five PTY keys of SRS A6: CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT, CREDIT_OVERRIDE_STATUS
Response     : 200 · list of `LookupOption` {code, labelAr, labelEn, sortOrder} — the active values of the key, in sortOrder · not paginated · ApiResponse<List<LookupOption>>
Validations  : key not one of the five PTY keys, or answered 404 by mdl → PTY-404-LOOKUP-KEY-UNKNOWN (OwnedLookups translates mdl's 404 into PTY's code)
Errors       : PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN, PTY-404-LOOKUP-KEY-UNKNOWN, PTY-500-INTERNAL
Orchestration : `OwnedLookups.read(key, PTY_LOOKUP_KEYS, PtyErrorCodes.LOOKUP_KEY_UNKNOWN, mdlLookupApi::readActiveValuesByKey)` → map each `LookupOptionView` to `LookupOption` → return the list. `PTY_LOOKUP_KEYS` holds the five keys, all with OWNER_MODULE_CODE `PTY` (db-script BLOCK 8a); PTY is never a pass-through for another module's keys (checklist C6). No PTY table is read.
Repository   : NONE — no database operation; the values are read through the given `MdlLookupApi` (no QR)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-PTY-001 … SCR-REQ-PTY-005 · `@PreAuthorize("hasAnyAuthority(T(com.commerce.pty.permission.PtyPermissions).PERM_PTY_CUSTOMER_VIEW, …PERM_PTY_SUPPLIER_VIEW, …PERM_PTY_CUSTOMER_CREDIT_VIEW, …PERM_PTY_CREDIT_OVERRIDE_VIEW, …PERM_PTY_CREDIT_SETTINGS_VIEW)")` on the controller and the service method — the VIEW of any PTY page, PTY's own constants only (checklist C13, ADR-PTY-048)
Localization : labels ar + en from mdl
<!-- API:API-PTY-038:END -->
<!-- SUB:SVC-API-SEARCH:END -->
