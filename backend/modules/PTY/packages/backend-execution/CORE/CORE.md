<!-- source: PHASE:CORE -->
<!-- traces: REQ-PTY-006, REQ-PTY-023, REQ-PTY-024, REQ-PTY-051, REQ-PTY-052, REQ-PTY-054, REQ-PTY-059, REQ-PTY-070, REQ-PTY-073, REQ-PTY-092, REQ-PTY-093, REQ-PTY-106, REQ-PTY-107, REQ-PTY-109 -->
<!-- PHASE:CORE:START traces=REQ-PTY-006,REQ-PTY-023,REQ-PTY-024,REQ-PTY-051,REQ-PTY-052,REQ-PTY-054,REQ-PTY-059,REQ-PTY-070,REQ-PTY-073,REQ-PTY-092,REQ-PTY-093,REQ-PTY-106,REQ-PTY-107,REQ-PTY-109 -->
## PHASE 1 — CORE

These architecture policies are declared once for the module. Everything below applies to every endpoint of SVC-API.

**Module layout (Spring Boot, Java 25, on the given erp-core).** The root package is `com.commerce.pty`: the application root namespace `com.commerce` that every business module uses (ADR-PTY-047, platform summary decision 11), never the given's `com.erp.*` (checklist F8). It has one sub-package per aggregate:
- `party` (ENT-PTY-001), `customer` (ENT-PTY-002), `supplier` (ENT-PTY-003), `address` (ENT-PTY-004), `creditoverride` (ENT-PTY-005);
- `credit`: the credit check, credit standing, settings and exposure;
- `guest`: guest resolution and the invitation subscriber;
- `lookup`: `PtyLookupController` and `PtyLookupService`, which serve PTY's own lookup keys (API-PTY-038, ADR-PTY-048).

Each sub-package holds five kinds of class:
- `*Controller` — the entry layer: `@RestController`, the request and response DTOs, and the same `@PreAuthorize` as the service method it calls;
- `*Service` — the transaction boundary and the orchestration. Every public method carries `@PreAuthorize` (PHASE SEC-BE).
- `*Domain` — the Domain companion of the entity, which owns every business rule (below);
- `*Repository` — Spring Data JPA, with queries per the QRC;
- `*Entity` — the JPA mapping per the DB Alignment Manifest.

Controllers never touch repositories. Services never check a role name, only permission names. The module also holds `com.commerce.pty.permission.PtyPermissions` (the one `PermissionContributor`, checklist C13), `com.commerce.pty.exception.PtyErrorCodes` (checklist C11), `com.commerce.pty.tenant.PtyTenantProvisioningContributor`, and `com.commerce.pty.currency.PtyCurrency` (ADR-PTY-037).

**Entities (checklist C2).** Every entity extends the given `com.erp.common.domain.AuditableEntity` and declares no tenant field of its own. `AuditableEntity` maps TENANT_ID as Hibernate's `@TenantId`, VERSION as `@Version`, and the four audit columns:

| Entity | Table |
|---|---|
| `PtyPartyEntity` | PTY_PARTY |
| `PtyCustomerEntity` | PTY_CUSTOMER |
| `PtySupplierEntity` | PTY_SUPPLIER |
| `PtyAddressEntity` | PTY_ADDRESS |
| `PtyCreditOverrideRequestEntity` | PTY_CREDIT_OVERRIDE_REQUEST |

The columns concerned are DBF-PTY-068 … 077 and the audit DBFs (ADR-PTY-035). PK columns use `@GeneratedValue(strategy = GenerationType.IDENTITY)`.

**Rule placement (checklist C12).** Each entity has a Domain companion: `PartyDomain`, `CustomerDomain`, `SupplierDomain`, `AddressDomain` and `CreditOverrideRequestDomain`. A companion owns every RULE of its entity:
- `create(…)` handles a new record and `from(stored, request)` a change;
- named transitions (`deactivate`, `reactivate`, `withCredit`, `clearCredit`, `markDefault`, `approve`, `reject`, `cancel`, `use`, `cancelOnCustomerDeactivation`) handle the state changes;
- each method throws the rule's `LocalizedException(Status, PtyErrorCodes.X, args)`.

The service gathers the facts a rule needs and passes them in as plain values:
- the email holder found by QR-PTY-004;
- the role already held (QR-PTY-007, QR-PTY-017);
- the active lookup codes from `MdlLookupApi`;
- the `b2bCredit` flag from `CreditFlag`;
- the server-side credit check result;
- the open request of a sale (QR-PTY-040);
- the current principal.

The service owns only what is not a rule: the transaction, the locks, the queries, the given calls, and the translation of a db-script constraint violation into its rule's catalog code. Each entity block of DATA-DOM names its companion and the companion method that owns each RULE. Two rules are not decisions on a record, and their owner is named as such:
- RULE-PTY-024 is an answer computed from a count. The companion defines the predicate `CustomerDomain.carriesCredit()` and QR-PTY-014 counts it.
- RULE-PTY-030 is the transaction's audit guarantee, enforced by the service and the given audit module.

**Tenancy (checklist C1, C3, C4).** The given tenant filter binds the request's tenant; PTY code reads no tenant identifier itself. Every JPQL, derived and search-builder query runs under that filter, which means `TENANT_ID = :tenantId` on every PTY table it reads. The QRC writes that predicate in every logical spec. Work that runs outside the request (the after-commit listeners) re-binds the event's tenant through the given's published `com.erp.tenant.TenantRunner` (given row `modules.tenant`, ADR-PTY-044).

The two native queries, QR-PTY-011 and QR-PTY-037, name `TENANT_ID = :tenantId` themselves on every table they read. Their repository methods carry `@TenantUnscoped(reason = "<the native expression>", adr = "ADR-PTY-027")` (`com.erp.tenant.TenantUnscoped`). No other native query or `JdbcTemplate` statement exists in PTY. A record of another tenant reads and writes as not found (PTY-404-PARTY-NOT-FOUND, PTY-404-CUSTOMER-NOT-FOUND, PTY-404-SUPPLIER-NOT-FOUND, PTY-404-ADDRESS-NOT-FOUND or PTY-404-CREDIT-OVERRIDE-NOT-FOUND, by the entity read). PTY never reads `erp.core.tenant.*`.

**Published in-process surface (this module's own, `profile.conventions.module_interface: in_process`).** Package `com.commerce.pty.crossmodule` publishes three things.

`PtyPartyApi` has these methods: `readCustomer`, `readSupplier`, `listPartyAddresses`, `readAddress`, `defaultPosCustomer`, `resolveGuestCustomer`, `checkCredit`, `createCreditOverrideRequest`, `readCreditOverrideRequest`, `useCreditOverrideRequest`, `b2bCreditSwitchOffDependency`, `globalSearchCustomers`, `registerCustomer` and `registerSupplier`.
- Each delegates to the same service method as the REST endpoint that honours the same contract item (the `Honours` line of the `API-*` block), so the REST document and the in-process call cannot diverge.
- The service method's `@PreAuthorize` applies to the in-process call too, so **the caller's security context must hold the PTY authority** (ADR-PTY-039).
- Callers that hold no PTY authority (the anonymous shopper, the CUSTOMER realm, system jobs) are refused until `CORE-GAP-in-process-caller-authority` is answered.

`CustomerCreditExposureContributor` is the extension point (CON-PTY-022): `BigDecimal openExposure(Long customerId)`, an amount in the Customer's `currencyCode`.
- Downstream modules implement it as Spring beans. PTY discovers every bean of the type, and zero beans means exposure 0.

The business event `pty.PartyRoleDeactivated` (CON-PTY-021, payload `{role CUSTOMER | SUPPLIER, identifier, code}`) is published with `OutboxPublisher.publish("pty.PartyRoleDeactivated", payload)` inside the deactivation transaction (checklist C19, ADR-PTY-038).
- Each subscriber is an `OutboxSubscriber`, idempotent on `OutboxMessage.id()`.
- PTY sends no event to another module through `DomainEventPublisher` (F11).

PTY imports no other business module's package. The dependency points only into PTY (DAG #4, `consumes: []`).

**Type mapping (postgresql16 → Java), stated once.** This table maps column types only. The PK-generation clause (`GENERATED ALWAYS AS IDENTITY`) is not a type; every PK is mapped `@GeneratedValue(strategy = GenerationType.IDENTITY)`.

| postgresql16 type (db-script) | Java type | JSON (API document) |
|---|---|---|
| BIGINT | `Long` (TENANT_ID and VERSION are `AuditableEntity`'s and are not in any DTO) | integer, int64 |
| VARCHAR(n) | `String` (max length n) | string, maxLength n |
| BOOLEAN | `Boolean` | boolean |
| TIMESTAMPTZ | `OffsetDateTime` | string, date-time |
| NUMERIC(18,3) | `BigDecimal` (scale 3) | number |
| INTEGER | `Integer` | integer, int32 |

**Runtime error-code format** (profile.stack.backend.api.error_code_format), verbatim: `{MOD}-{http}[-{SLUG}]`.
- MOD is this module's code `PTY`, and every Error Catalog code is an instance of that one format (e.g. `PTY-422-NAME-REQUIRED`).
- Errors are thrown as `LocalizedException → {code, messageAr, messageEn}` from the companion or the service. The HTTP status is the `{http}` segment of the code and is one of 200, 201, 400, 401, 403, 404, 409, 413, 415, 422, 500.
- Every catalog code is a `public static final String` of `PtyErrorCodes`, with entries in `messages.properties` (en) and `messages_ar.properties` (ar) whose text is the catalog row's `messages` (checklist C11).
- Message templates with placeholders (`{code}`, `{roleAr}`, `{roleEn}`, `{typeAr}`, `{typeEn}`, `{statusAr}`, `{statusEn}`, `{docNo}`, `{count}`) are filled before the exception is thrown.
- PTY builds no response envelope of its own. The given `GlobalExceptionHandler` answers (ADR-PTY-043).

**Envelope.** Every success response is `ApiResponse<T>` `{data, messageAr?, messageEn?}`. The two message members carry a confirmation, such as RULE-PTY-006 or RULE-PTY-017, and are absent otherwise. A page is `Page<T>` `{content, page, size, totalElements, totalPages}`, with query parameters `page` (0-based) and `size` (default 20, max 100). An empty result is success with empty `content`, never "not found".

**Lookups.** All LOV values are loaded at runtime from the core lookup module (mdl). No enum is hardcoded in an API or a field spec. Activity presets and capability flags are core settings, read through FeatureFlags.
- Lookup-backed properties store and return the **code**; the frontend resolves the label.
- An open-lookup code (CUSTOMER_GROUP, ADDRESS_TYPE) is validated against the active values read through `MdlLookupApi.readActiveValuesByKey` (given row `modules.mdl`).
- The closed sets the module branches on (CREDIT_CHECK_RESULT, CREDIT_OVERRUN_ACTION, CREDIT_OVERRIDE_STATUS — CON-PTY-008) are matched by code in the companion.
- Their labels (`{statusAr}` / `{statusEn}` in RULE-PTY-026, `{typeAr}` / `{typeEn}` in RULE-PTY-017) are read through `MdlLookupApi` and never typed into code.
- **Serving the keys (checklist C6, ADR-PTY-048).** PTY serves its own five keys — CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT and CREDIT_OVERRIDE_STATUS, seeded with OWNER_MODULE_CODE `PTY` (db-script BLOCK 8a) — to its screens through `com.erp.common.lookup.OwnedLookups.read(key, PTY_LOOKUP_KEYS, PtyErrorCodes.LOOKUP_KEY_UNKNOWN, mdlLookupApi::readActiveValuesByKey)`, with owner PTY (API-PTY-038). `PTY_LOOKUP_KEYS` holds exactly those five keys; any other key answers PTY-404-LOOKUP-KEY-UNKNOWN, so PTY is never a pass-through for another module's keys. Write-time validation keeps `MdlLookupApi.readActiveValuesByKey` (checklist C5).

**Numbering (checklist C9).** Document numbers come from the core sequence module and are never generated in a business module.
- The Party code (DBF-PTY-007) and the request docNo (DBF-PTY-061) are allocated through `NumberSeriesApi.next(code)` (given row `modules.sequence`) inside the creating transaction.
- The series are PTY_PARTY and PTY_CREDIT_OVERRIDE, seeded by the PTY migration (db-script BLOCK 8c, ADR-PTY-036). Each is stored in a column unique with TENANT_ID.
- The Address code (DBF-PTY-044) is an ordinal within its Party, not a document number. QR-PTY-024 assigns it under the Party lock (ADR-PTY-040, CORE-GAP-ordinal-within-parent).

**Capability flag.** `b2bCredit` is read only through the FeatureFlags API of `SettingsApi` (given row `modules.cu`), never through an activity name or preset. One component, `CreditFlag.isOn()`, is the single read point. Every credit endpoint and every Customer read calls it per request (REQ-PTY-073: a flag turned on is effective at the next request, and nothing is cached beyond the request).

**Currency (ADR-PTY-037).** `PtyCurrency.baseCurrency()` returns `SettingsApi.getOrDefault("PTY_BASE_CURRENCY_CODE", String.class, "XXX")` until `CORE-GAP-tenant-base-currency` is answered.
- Every Customer insert writes its result to DBF-PTY-078 (API-PTY-001, 033, 034).
- A request copies its Customer's DBF-PTY-078 into DBF-PTY-079 (API-PTY-019).
- Amounts are compared in the Customer's currency, and every credit response carries `currencyCode`.

**Workflow engine: forbidden.** The Credit override request lifecycle (SRS A7) is a module-specific single-step state machine on `CreditOverrideRequestDomain` (ADR-PTY-016).

**Search contract.** Each search allows sorting only on the fields of its screen's SRS filter list (stated per search endpoint). Paging is `Page<T>`. Text filters are case-insensitive "contains". An empty result is success.

**Languages.** Every named entity carries `nameAr` and `nameEn`, and every message is returned in ar and en (`messageAr`, `messageEn`).

**Transactions, audit and after-commit work (checklist C4, C16, C17).**
- **Transactions.** Each endpoint runs one `@Transactional` service method (reads `readOnly = true`).
- **Audit (C16).** `PtyCustomerEntity` carries `@Audited(entityType = "PTY_CUSTOMER", ignore = {"customerGroupCode", "secUserId", "isWalkInFl", "partyFk", "isActiveFl", "currencyCode"})`. The given listener therefore records every change of creditLimit and paymentTermDays as an UPDATE row with old and new values, the user and the time, in the same transaction (REQ-PTY-106).
- `PtyCreditOverrideRequestEntity` carries `@Audited(entityType = "PTY_CREDIT_OVERRIDE_REQUEST")`, so every create and status change of a request is a reviewable audit row.
- Approve and reject are business actions that C16 does not describe, because REQ-PTY-107 asks for the docNo, Customer, amount and decider in one event. The service records them with `AuditApi.record(AuditEntry)` (action `APPROVE` / `REJECT`) inside the decision transaction.
- **RULE-PTY-030.** The service flushes the change and the audit write inside the transaction. Any failure of the given audit (the listener on flush, or `AuditApi.record`) rolls the transaction back and is answered PTY-500-AUDIT-NOT-RECORDED. The change or the decision is not saved (REQ-PTY-109).
- **After-commit work (C4, C17).** The CUSTOMER-realm invitation (`SecCustomerInvitationApi.issue`) and the approver notifications (`NotificationDispatchApi.dispatch`) must not fail the request that triggered them. They are therefore not called inside the request transaction. The service publishes a PTY-internal event carrying plain values only, through `DomainEventPublisher`, inside the transaction:
  - `PtyInvitationRequestedEvent {customerId, channel EMAIL | SMS, address}`;
  - `PtyCreditOverrideRequestedEvent {creditOverrideRequestId, docNo, customerName}`.
  Each extends the given `DomainEvent`, which carries `tenantId`. Only PTY's own listeners consume them; no other module does (F11).
- The listeners `PtyInvitationListener` and `PtyApproverNotificationListener` are `@Async(ErpCoreEvents.EXECUTOR)` and `@TransactionalEventListener(phase = AFTER_COMMIT, fallbackExecution = true)`. Each runs on the given `erpCoreEventExecutor`, re-binds the tenant by running its body through the given's `TenantRunner` for `event.getTenantId()` (ADR-PTY-044), and opens its own transaction.
- A rollback publishes nothing. A failed issue or dispatch is logged and leaves the committed request as it is.
- PTY uses no `TransactionSynchronization`.
- **Lock order (ADR-PTY-026), stated once.** Every write that takes more than one row lock takes them in this order: **role row (Customer QR-PTY-006 or Supplier QR-PTY-016) → Party (QR-PTY-001) → Address (QR-PTY-023, 028, 029) and Credit override requests (QR-PTY-033, 039)**. An endpoint takes only the locks it needs, in that order, and never takes an earlier lock after a later one. The role endpoints (API-PTY-003, 004, 005, 008, 009, 010) lock the role row first and the Party second; the Address endpoints lock the Party first and the Address second; the request endpoints lock only the request row (API-PTY-021, 022, 023, 036) or the Customer and then its requests (API-PTY-004, 019). Two writers therefore never wait on each other in opposite orders.
- **Invitation redemption (C17, C19).** `PtyCustomerInvitationRedeemedSubscriber implements OutboxSubscriber` (`code() = "pty.customerInvitationRedeemed"`, `module() = "pty"`, `eventTypes() = {"sec.CustomerInvitationRedeemed"}`) handles the redemption that the given `sec` publishes on the outbox (SVC-API-INT).
  - It is idempotent on `OutboxMessage.id()`: a redelivered message finds the Customer already linked and changes nothing.
  - The given runs it in the event's tenant.

**Audit columns.** The `createdBy`, `createdAt`, `updatedBy` and `updatedAt` columns of every table are filled by `AuditableEntity` with the principal of the authenticated given sec user, as the db-script binds them. They are never accepted from a request and always returned in single-record responses.

**Given usage.** The module builds on `erp-core` (usage skill `.claude/skills/erp-core-usage@1.6.0`, installed first). The rows are the SRS A9 `given-usage` rows; this plan cites them and adds none.

| Given module | via | Used by |
|---|---|---|
| modules.sec | `com.erp.sec.crossmodule.SecCustomerInvitationApi` | `PtyInvitationListener` (issue after commit for API-PTY-034) |
| modules.sec | `com.erp.sec.crossmodule.SecUserDirectoryApi` | `PtyApproverNotificationListener` (approvers holding PERM_PTY_CREDIT_OVERRIDE_UPDATE), API-PTY-021, 022 (decider identifier), API-PTY-031 (user names) |
| modules.sec | `com.erp.sec.permission.PermissionContributor` | `PtyPermissions` — the five pages and their permissions; every `@PreAuthorize` (SEC-BE) |
| modules.cu | `com.erp.cu.crossmodule.SettingsApi` | `CreditFlag` (b2bCredit), API-PTY-024, 025, 035 (credit-overrun setting), `PtyCurrency` (PTY_BASE_CURRENCY_CODE) |
| modules.mdl | `com.erp.mdl.crossmodule.MdlLookupApi` | RULE-PTY-016, RULE-PTY-018 validation; lookup labels in messages; API-PTY-038, which serves PTY's five keys through `OwnedLookups.read(…, mdlLookupApi::readActiveValuesByKey)` (ADR-PTY-048) |
| modules.sequence | `com.erp.sequence.crossmodule.NumberSeriesApi` | API-PTY-001, 006, 033, 034 (Party code), API-PTY-019 (docNo) |
| modules.tenant | `com.erp.tenant.TenantProvisioningContributor` | `PtyTenantProvisioningContributor` — walk-in Customer at provisioning (REQ-PTY-044) |
| modules.tenant | `com.erp.tenant.TenantRunner` | the after-commit listeners `PtyInvitationListener` and `PtyApproverNotificationListener`, which re-bind the event's tenant (ADR-PTY-044) |
| modules.audit | `com.erp.audit.crossmodule.AuditApi` | `@Audited` on Customer and Credit override request (API-PTY-017, 018, 019, 021, 022, 023, 036); `AuditApi.record` for approve / reject (API-PTY-021, 022); the credit history read (API-PTY-031) |
| modules.events | `com.erp.events.OutboxPublisher` | API-PTY-004, 009 (`pty.PartyRoleDeactivated`) |
| modules.events | `com.erp.events.OutboxSubscriber` | `PtyCustomerInvitationRedeemedSubscriber` (`sec.CustomerInvitationRedeemed`) |
| modules.events | `com.erp.events.DomainEventPublisher` | API-PTY-019, 034 — PTY-internal after-commit events only |
| modules.notif | `com.erp.notif.crossmodule.NotificationDispatchApi` | `PtyApproverNotificationListener` — template PTY_CREDIT_OVERRIDE_REQUESTED (API-PTY-019) |
| modules.module | `docs/api-docs/module/index.md` | the module manifest `META-INF/erp/modules/pty.yaml` (below), in the format the given's module api documents (ADR-PTY-044) |

The table holds the 14 rows of SRS A9, in the same order. The entities extend the given `com.erp.common.domain.AuditableEntity`, and `OwnedLookups` is a class of the same module. `erp-core:modules.common` publishes no extension point, interface or api, so these are build dependencies of PTY's code, not uses through a published seam, and they have no row (ADR-PTY-044, ADR-PTY-048).

No entity or table the given owns is re-created; an operation on a given's entity is a call into the given.

**Module manifest, boundary, CI and migration (checklist C15, C18, C20).**
- **Manifest (C18).** The PTY jar ships `src/main/resources/META-INF/erp/modules/pty.yaml` with these entries:
  - `code: pty`, `version: 1.0.0`, `name` (ar «الأطراف» / en "Party"), `package: com.commerce.pty` (ADR-PTY-047);
  - `depends: []`, `optional-depends: []` — no business-module dependency (SRS A8);
  - `permission-modules: [PTY]` — the `PermissionModule` that `PtyPermissions` declares (menu «الأطراف / Party»; also the `OWNER_MODULE_CODE` of the PTY lookup types, db-script BLOCK 8a). Its `PermissionScreen`s are the five pages PTY_CUSTOMER, PTY_SUPPLIER, PTY_CUSTOMER_CREDIT, PTY_CREDIT_OVERRIDE and PTY_CREDIT_SETTINGS.
  - `api-paths: [/api/v1/pty/**]` — the sub-tree pattern, as in the library's module example (`/api/v1/leasing/**`); every API-PTY path sits under it;
  - `settings: [PTY_CREDIT_OVERRUN_ACTION, PTY_BASE_CURRENCY_CODE]`;
  - `events.publishes: [pty.PartyRoleDeactivated]`, `events.consumes: [sec.CustomerInvitationRedeemed]`;
  - `migrations: [V1001__pty_party.sql]`;
  - `reports: []`; no `home-cards`.
  - Acceptance: the application starts without `MODULE_MANIFEST_INVALID`, and an unentitled tenant receives 403 `MODULE_NOT_ENTITLED` on `/api/v1/pty/**`.
- **Boundary (C20).** `PtyModuleBoundaryTest` runs `ModuleBoundaryRules.modulesDependOnlyOnTheirDeclaredDependencies()` in the module's tests. Consumers may import `com.commerce.pty.crossmodule` only.
  - Acceptance: the test passes, and fails on a deliberate import of another business module's package.
- **CI (C20).** The CI workflow gains the job `module-pty` (template `templates/ci/module-matrix.yml`), which builds and tests the PTY module on its own.
  - Acceptance: the job is green on the module alone.
- **Migration (C15).** The db-script §4 (BLOCKS 1–10) is the module's single Flyway migration `V1001__pty_party.sql`. It is the next free number after CAT's `V1000`; the executor confirms it from the directory listing.
  - BLOCK 8 seeds the given mdl, sequence and notif tables for TENANT_ID = 1 and holds no transaction statement.
  - A later change is a new `V1002+` file.

**Core gaps (checklist F9).**

| CORE-GAP | Question to the core | What PTY does until the answer | ADR |
|---|---|---|---|
| decisions/PTY/CORE-GAP-in-process-caller-authority.md | How does a caller with no PTY staff permission reach a PTY service method? This covers the anonymous storefront shopper, the CUSTOMER realm and system jobs. | Every service method checks its PTY authority, so such callers are refused. No v1 caller is blocked. | ADR-PTY-039 |
| decisions/PTY/CORE-GAP-tenant-base-currency.md | Which given interface answers a tenant's base currency? | `PtyCurrency` reads the setting PTY_BASE_CURRENCY_CODE, or `XXX` while it is unset | ADR-PTY-037 |
| decisions/PTY/CORE-GAP-ordinal-within-parent.md | Is a per-parent ordinal outside F6, or which facility assigns it? | QR-PTY-024 computes `MAX + 1` within one Party under the Party lock, backed by a unique constraint | ADR-PTY-040 |
<!-- PHASE:CORE:END -->
