# erp-core conventions — what every business module built on the core follows

The checkable rules a module built on `com.erp:erp-core` obeys, one section per convention. Each section gives
the rule as sentences a reviewer can tick, the core type to use and what the module must **not** rebuild.
The `##` headings are the `conventions` keys of `core-manifest.yaml` (slug = lower case, spaces → `-`), so
a heading is renamed only together with the manifest (ADR-COMMON-004). Sources: `governance/rules/GOVERNANCE-RULES.md`
§ Tenancy (T1–T6), ADR-TENANT-009, `docs/CONSUMING.md`, `docs/RELEASE.md` and the module analyses under
`governance/analysis/modules/<MOD>/`.

## Tenancy

erp-core is a multi-tenant core deployed `single` or `multi` (`erp.core.tenant.mode`, ADR-TENANT-007). A
business module is tenant-agnostic: it never knows which.

Rules (GOVERNANCE-RULES T1–T6, ADR-TENANT-009):
1. Every table of the module has `TENANT_ID BIGINT NOT NULL`, the FK `FK_<TABLE>_TENANT → CORE_TENANT(ID)` and an
   index whose first column is `TENANT_ID`; every `UNIQUE` constraint is composite with `TENANT_ID`.
2. Every entity extends `com.erp.common.domain.AuditableEntity` (its `TENANT_ID` is Hibernate's `@TenantId`); the
   entity never declares its own tenant field. A global table needs an ADR, an analysis entry and an entry in
   `TenantScopedEntityTest.GLOBAL_ENTITIES` (RULE-TENANT-010).
3. Every read goes through the tenant filter: JPQL, derived queries or the shared search builders. A native query
   or `JdbcTemplate` statement names `TENANT_ID` and its method carries `@TenantUnscoped(reason, adr)`
   (`com.erp.tenant.TenantUnscoped`).
4. No module reads the tenancy mode: no `erp.core.tenant.mode` or `erp.core.tenant.default-tenant-id` in Java
   code outside `com.erp.tenant` and `com.erp.autoconfigure` (`TenancyModeIsolationRules`, in erp-core's test-jar,
   run by every application). The current tenant comes from `com.erp.tenant.TenantContext`
   (`current()`, `require()`, `runAs`, `callAs`); a decision that differs per deployment asks `com.erp.tenant.TenantPolicy`.
5. Every tenant-shaped resource carries the tenant: a cache key starts with the tenant id, a stored file path
   starts with the tenant id (the FILE module does this), a document number is per tenant (`NumberSeriesApi`), a
   scheduled or system job walks the tenants through `com.erp.tenant.TenantRunner` (`forEachTenant`,
   `forEachTenantIncludingSuspended`), async work runs on a decorated executor (`erpCoreEventExecutor` or Boot's
   executor with `erpCoreTenantContextTaskDecorator`) or re-binds with `TenantContext.runAs`.
6. Tests run in both modes; a test that needs several tenants carries `@MultiTenantOnly`.

Core types: `AuditableEntity`, `TenantContext`, `TenantPolicy`, `TenantRunner`, `TenantUnscoped`,
`TenantProvisioningContributor` (seed a new tenant's reference data), `TenantExportContributor` (export the
module's rows), `TenantLookupApi`.

Do not rebuild: a tenant table, a tenant resolver, a tenant column outside `AuditableEntity`, a tenant loop,
a "single-tenant" code path.

## Lookups

A code list (status, type, category, unit …) that a user may maintain is an MDL lookup type, never an enum of
codes in the module and never a module-owned table.

Rules:
1. The module reads the active values of a lookup type by its key through `com.erp.mdl.crossmodule.MdlLookupApi`
   (`readActiveValuesByKey(typeKey)` → `LookupOptionView(code, labelAr, labelEn, sortOrder)`); an unknown or
   inactive key raises MDL's 404, which the module translates with its own code through
   `com.erp.common.lookup.OwnedLookups.read(rawKey, ownedKeys, unknownKeyErrorCode, reader)`.
2. The module registers its own lookup types by data: a `V1000+` migration inserts the `MDL_LOOKUP_TYPE` row
   (`KEY`, `OWNER_MODULE_CODE` = the module code its `PermissionContributor` declares) and the `MDL_LOOKUP_VALUE`
   rows for `TENANT_ID = 1` (`PLATFORM`); new tenants receive a copy through the core's own
   `MdlTenantProvisioningContributor` (ADR-TENANT-009 reference-data table).
3. A module stores the lookup **code** (`VARCHAR(50)`) in its own column and validates it against the active
   values at write time; it never stores the `MDL_LOOKUP_VALUE` primary key and never hardcodes a code list.
4. A module exposes only its own keys (`OwnedLookups`, RULE-COMMON-006) — never a pass-through for arbitrary MDL keys.

Core types: `MdlLookupApi`, `LookupOptionView`, `OwnedLookups`, `LookupOptionResponse`; tables `MDL_LOOKUP_TYPE`,
`MDL_LOOKUP_VALUE`.

Do not rebuild: a lookup / code-list / reference-list table, a lookup admin screen, an enum of user-maintained codes.

## Files

Bytes never touch the module: every upload, generated file, image or attachment is a FILE document.

Rules:
1. A server-generated private file (export, archive, generated PDF) is stored through
   `com.erp.file.crossmodule.FilePrivateStoreApi.storePrivateFile(PrivateFileStoreRequest)` → `StoredPrivateFile`
   and handed out with `issueDownloadToken(documentId)` → `DownloadGrant` (single use, 10 minutes, bound to the
   caller; downloaded at `GET /api/v1/files/download?token=`).
2. A small public image (logo, photo) is stored through `com.erp.file.crossmodule.FileImageStoreApi.storePublicImage(ImageStoreRequest)`
   → `ImageStoreResult` (`isStored()` / `rejection()`), withdrawn with `discard(documentId)`.
3. A reference to a file is the `FILE_DOCUMENT.ID` (a `BIGINT` column, soft read, no FK); availability and public
   URLs come from `com.erp.file.crossmodule.FileDocumentLookupApi` (`isAvailable`, `publicUrl`, `publicUrls`).
4. User uploads go through the FILE endpoints (`/api/v1/files/**`, a category per use); the module never
   accepts multipart content itself.
5. The module never opens `java.io.File`, `java.nio.file.Files` or an S3 / object-store client for business
   content; a new storage backend is a `com.erp.file.storage.StorageProvider` bean.

Core types: `FilePrivateStoreApi`, `FileImageStoreApi`, `FileDocumentLookupApi`, `StorageProvider`; tables
`FILE_DOCUMENT`, `FILE_CATEGORY`.

Do not rebuild: a file or attachment table, a storage path scheme, a download token, a public URL scheme, an
image validator.

## Notifications

A module never sends: it asks NOTIF to dispatch a template to a recipient.

Rules:
1. The module calls `com.erp.notif.crossmodule.NotificationDispatchApi.dispatch(DispatchCommand)` inside its own
   transaction (the delivery happens after commit), or `dispatchIndependently` when its own writes must survive
   a dispatch failure; `DispatchCommand(recipientId, templateCode, channelHint, moduleCode, referenceId,
   referenceType, variables)` carries plain values only.
2. The template is a `NOTIF_TEMPLATE` row seeded by a `V1000+` migration for `TENANT_ID = 1` (copied to new
   tenants by the core's `NotifTenantProvisioningContributor`); the module never formats a subject or body.
3. A new channel (SMS, PUSH, …) is one `com.erp.notif.channel.ChannelProvider` bean (`channel()`, `send(OutboundMessage)`);
   an application provider wins over a core one for the same channel.
4. The outcome is read from `NotificationDispatchedEvent` / `NotificationFailedEvent` or
   `com.erp.notif.crossmodule.NotificationLogQueryApi`, never from a module table.

Core types: `NotificationDispatchApi`, `DispatchCommand`, `NotificationLogQueryApi`, `ChannelProvider`; tables
`NOTIF_TEMPLATE`, `NOTIF_CHANNEL_CONFIG`, `NOTIF_LOG`, `NOTIF_INBOX`.

Do not rebuild: a mail sender, a template table, a notification log, an inbox, a retry loop.

## Numbering

Every human-readable document number comes from a number series, never from a module counter, a `MAX(...) + 1`
or a database sequence read by the module.

Rules:
1. The module calls `com.erp.sequence.crossmodule.NumberSeriesApi.next(code)` inside the writing transaction
   (the allocation commits on its own, `REQUIRES_NEW`; a rolled-back caller leaves a gap) and stores the result in
   its own `VARCHAR` column, unique together with `TENANT_ID`; `preview(code)` shows the next number without consuming it.
2. The series is seeded by a `V1000+` migration for `TENANT_ID = 1` (`CORE_NUMBER_SERIES`: `CODE`, `PREFIX`,
   `PATTERN` with the tokens `{PREFIX} {YYYY} {YY} {MM} {SEQ:n} {TENANT}`, `RESET_POLICY` `NEVER | YEARLY | MONTHLY`);
   new tenants receive copies with the counter at 1; an unknown or inactive code raises `SEQUENCE_NOT_CONFIGURED`.
3. The series code is a module constant (`<MODULE>_<DOCUMENT>`), configurable per tenant through
   `/api/v1/sequence/series`, never a pattern hardcoded in the module.

Core types: `NumberSeriesApi`; table `CORE_NUMBER_SERIES`.

Do not rebuild: a counter table or column, a numbering pattern parser, a per-year reset.

## Reporting

A list export, a tabular report and a printed document all go through the REPORT module; the module ships no
second export path.

Rules:
1. A report is one `com.erp.report.ReportProvider` bean (`code()` upper snake ≤ 40, `moduleCode()` ≤ 10,
   `titleAr/En`, `params()` of `ReportParam`, `run(params, Pageable)` → `ReportResult(columns, rows, totals, totalRows)`);
   the core lists it under `/api/v1/report/definitions`, runs it at `/api/v1/report/{code}/run` and exports it at
   `/api/v1/report/{code}/export?format=csv|json|pdf|xlsx|docx|html`.
2. Its permission `<MODULE>:REPORT:<CODE>` (`ReportAuthorities.of(module, code)`) enters the catalog
   automatically; `run` reads through JPA / `SpecBuilder` (tenant-filtered) and honours `page.getPageSize()`.
3. A designed document (invoice, voucher) is rendered through `com.erp.report.crossmodule.PrintApi.render(PrintRequest)`
   from a `reports/<module>/<name>.jrxml` template; JDBC templates compare `= $P{TENANT_ID}`; extra parameters come
   from a `PrintParameterContributor`, a veto from a `PrintAuthorizationHook`, a tenant template from a `PrintTemplateSource`.
4. The module never writes CSV / XLSX / PDF itself and never adds an `/export` endpoint of its own.

Core types: `ReportProvider`, `ReportParam`, `ReportColumn`, `ReportResult`, `ReportAuthorities`, `ReportSpecs`,
`PrintApi`, `PrintRequest`, `PrintParameterContributor`, `PrintAuthorizationHook`, `PrintTemplateSource`,
`com.erp.common.web.PrintResponses`.

Do not rebuild: a CSV writer, an export endpoint, a report permission scheme, a PDF engine.

## Error codes

Every error a module raises is a registered, bilingual code answered in the shared envelope.

Rules (ADR-COMMON-001):
1. Every error path throws `com.erp.common.exception.LocalizedException(Status, code, args…)` with a `Status` of
   `com.erp.common.domain.status.Status`; never a raw `RuntimeException`, `IllegalArgumentException` or
   `EntityNotFoundException`.
2. Every code is a `public static final String` of the module's `<Module>ErrorCodes` class, named
   `<ENTITY>_<SCENARIO>` (e.g. `ORDER_NOT_FOUND`), and has a message in the application's `messages.properties` (EN)
   **and** `messages_ar.properties` (AR) — the application lists its bundles in `spring.messages.basename`; the core
   bundle is consulted after them.
3. The module never catches a `LocalizedException` to turn it into another status, never builds its own response
   envelope and never registers a second `@ControllerAdvice` for the shared codes (`GlobalExceptionHandler` owns
   `VALIDATION_ERROR`, `ACCESS_DENIED`, `NOT_FOUND`, `CONCURRENT_MODIFICATION`, `DATA_INTEGRITY_VIOLATION`, …).
4. Business rules live on the entity's Domain companion (`create()` / `from()` factories) and throw the code;
   services orchestrate, controllers return `ApiResponse` through the shared response helper.

Core types: `LocalizedException`, `Status`, `ServiceResult`, `ApiResponse`, `GlobalExceptionHandler`,
`CommonErrorCodes`.

Do not rebuild: an exception hierarchy, an error envelope, a message resolver, a status-to-HTTP mapping.

## Auth and permissions

Authentication and authorization are SEC's; a module contributes permissions and references them.

Rules:
1. The module declares one `@Component` implementing `com.erp.sec.permission.PermissionContributor` — the
   `<Module>Permissions` class — with its `PermissionModule`, `PermissionScreen`s and `PermissionDef`s; every
   screen declares its `VIEW` gateway action (RULE-SEC-007); the authority is `PERM_<SCREEN>_<ACTION>`.
2. Every service method carries `@PreAuthorize("hasAuthority(T(<base.package>.<module>.permission.<Module>Permissions).PERM_…)")`
   and references only its own module's constants; the catalog is synchronised at startup
   (`PermissionCatalogSynchronizer`), super roles hold every permission at once.
3. Staff endpoints sit on `erpCoreSecurityFilterChain`, customer endpoints on `erpCoreCustomerSecurityFilterChain`
   (`/api/v1/public/**`, `/api/v1/customers/**`); the module adds no filter chain, token format, user table, role
   table or session store; users are reached through `com.erp.sec.crossmodule.SecUserDirectoryApi`.
4. The caller is read through `com.erp.common.util.SecurityContextHelper`, never from a request header.

Core types: `PermissionContributor`, `PermissionDef`, `PermissionModule`, `PermissionScreen`, `SecUserDirectoryApi`,
`SecModuleRegistryApi`, `SecurityContextHelper`; tables `SEC_USER`, `SEC_ROLE`, `SEC_*_REG`, `SEC_ROLE_*_GRANT`.

Do not rebuild: a user, role, permission or grant table, a login, a JWT filter, a session store, a password reset.

## Settings

A module's configurable values are settings of the CU store, read through `SettingsApi`, never a module config
table and never `@Value` on an `erp.core.*` key.

Rules:
1. The module injects `com.erp.cu.crossmodule.SettingsApi` and reads `get(key)`, `get(key, type)`, `find(key[, type])`
   or `getOrDefault(key, type, defaultValue)` (`String`, `Integer`, `Long`, `Boolean`, `BigDecimal`, `Duration`);
   resolution is tenant override → platform default (`TENANT_ID NULL`) → `NoSuchSettingException`.
2. The module's keys are `<MODULE>_<NAME>` constants; a `V1000+` migration may seed the platform defaults in
   `CU_APP_CONFIGURATION`; values are changed through `/api/v1/common/configurations` so the `erpCoreSettings`
   cache is evicted (never by direct SQL at runtime).
3. Deployment properties of the application itself stay in the application's `@ConfigurationProperties`
   (never `erp.core.*`, which is bound by `ErpCoreProperties`).

Core types: `SettingsApi`, `NoSuchSettingException`; table `CU_APP_CONFIGURATION`.

Do not rebuild: a settings / parameters / configuration table, a settings screen, a settings cache.

## Migrations

Rules (`docs/RELEASE.md` § Migration rules, `MigrationNamingTest`):
1. The application owns `V1000+` in its own location (default `classpath:db/migration`); it never writes a script
   numbered `V1..V999` and never edits a core script.
2. Naming `V<N>__<module>_<snake_case_description>.sql`, strictly sequential, one logical change per file; the
   next number comes from the directory listing at creation time.
3. Every application table follows the physical convention: `UPPER_SNAKE_CASE` with the module prefix,
   `ID BIGINT` from `SEQ_<TABLE>` (`START WITH 1 INCREMENT BY 1 CACHE 1 NO CYCLE`), `TENANT_ID` + FK + index,
   `VERSION BIGINT NOT NULL DEFAULT 0`, `CREATED_BY VARCHAR(100)`, `CREATED_AT TIMESTAMPTZ`, `UPDATED_BY`,
   `UPDATED_AT`; the JPA entity and the migration agree on every physical name.
4. Application scripts are additive once shipped; a mistake is fixed forward with a new script. Seed rows for
   lookups, templates, series and settings name `TENANT_ID` explicitly (`1` = `PLATFORM`).
5. Core tables are referenced by FK only where the core documents it (`CORE_TENANT(ID)`); a reference to a
   core row of another module (user, file, lookup value) is a soft read by id or code.

Core types: `AuditableEntity` (the column set), Flyway through Boot's auto-configuration (`ErpCoreFlywayAutoConfiguration`
prepends the core chain).

Do not rebuild: a schema tool, `ddl-auto`, a second Flyway instance, a copy of a core table.

## Modules

A product built on erp-core is a set of business modules, each sold on its own (ADR-MODULE-001 … 004). A module
declares itself in a manifest; the core decides per tenant whether it exists.

Rules:
1. Every business module ships `src/main/resources/META-INF/erp/modules/<code>.yaml` in its own jar: `code`,
   `version`, `name`, `package`, `depends`, `optional-depends`, `permission-modules`, `api-paths`, `settings`,
   `events.publishes` / `events.consumes`, `migrations`, `reports` (template:
   `skills/erp-core-usage/templates/business-module/module-manifest.yaml`). A broken manifest or a missing
   dependency stops startup (`MODULE_MANIFEST_INVALID`).
2. The module never checks its own licence: an unentitled module's menu entries, authorities and grants are removed
   by the core and its `api-paths` answer 403 `MODULE_NOT_ENTITLED`. Its permission modules are contributed by a
   `PermissionContributor` inside its `package`; every API path sits under its `api-paths`.
3. Code never branches on the deployment mode or on the licence source: SaaS tenants are entitled by the platform
   operator, a single deployment by its signed licence — the same table, the same check.
4. A business event another module consumes is published with `OutboxPublisher.publish("<module>.<Name>", payload)`
   inside the writing transaction and consumed by an `OutboxSubscriber` (idempotent on `OutboxMessage.id()`).
   Enabling a module later replays the events it missed.
5. A module depends on another business module's classes only when it is in `depends`; an optional dependency is
   reached through outbox events or core APIs only (`ModuleBoundaryRules`, one CI job per module).
6. No second source of truth: a statement or balance is a computed view over the documents (invoices, payments),
   never a parallel ledger. No workflow engine: a document's lifecycle is a fixed state machine on its Domain object.
7. A key figure on the shared home screen is a `home-cards` entry of the manifest (`key`, `title-key`, `description-key`,
   `permission`, `order`, `target` — a screen of the module's `permission-modules`; the snake spellings `home_cards`,
   `title_key`, `description_key` are read too) valued by the module's one `HomeCardProvider` bean (`com.erp.module`,
   erp-core 1.6.0, ADR-MODULE-005). The core shows the card only when the module is entitled for the tenant and the
   user holds its permission, calls the provider inside the request tenant within
   `erp.core.modules.home-cards.timeout-ms` (status `UNAVAILABLE` otherwise), orders by `order`, module, key and
   localizes `title-key` / `description-key` from the module's bundle. A provider reads only; computing a value writes
   nothing. A permission, a target or a provider the manifest's own module does not contribute stops startup
   (`MODULE_MANIFEST_INVALID`).

Core types: `ModuleCatalogApi`, `ModuleEntitlementApi`, `HomeCardProvider`, `HomeCardApi`, `OutboxPublisher`,
`OutboxSubscriber`, `OutboxReplayApi`; tables `CORE_TENANT_MODULE`, `CORE_OUTBOX_EVENT`, `CORE_OUTBOX_DELIVERY`;
endpoint `GET /api/v1/modules/home-cards`.

Do not rebuild: a licence check in a module, a module flag in settings, a per-module event table or relay, a menu
filter in the module, a configurable workflow, a home-screen card wired outside the manifest (a module's own dashboard
endpoint, a card that checks its own licence or permission, a provider that writes).


## Ledger

A module that handles money posts its accounting effect into the core general ledger (`gl`, erp-core 1.6.0,
CCR-ACC-001, ADR-GL-001 … 007) and reads totals back from it; it keeps no journal, balance or chart of its own.

Rules:
1. Account roles are mapped to accounts by their **stable id**: the module's own configuration (a setting or a table
   of its own) stores `AccountView.accountId` per role (sales, VAT output, receivable, bank …), chosen through
   `GlAccountLookupApi.search(text, true, page)` / `findByCode(code)`, and resolves it with `get(accountId)` at posting
   time. It never stores an account code (a tenant may recode an account; the id never changes) and never creates an
   account or a period (the posting API never does either; the accountant does, on the `GL_*` screens).
2. One document's effect is posted once through `GlPostingApi.post(PostEntryRequest)` with an `idempotencyKey` of the
   module's own that is unique per tenant and stable for the document (e.g. `"<module>-<document type>-<document id>"`):
   a repeat with the same content answers the first entry with `alreadyPosted = true` and posts nothing, so a
   redelivered outbox event or a restarted job is harmless; the same key with different content is
   `IDEMPOTENCY_CONFLICT` (a change to a posted document is a **new** entry under a new key — a reversal or an
   adjustment — never a re-post). `postingModule` is the module's code (it must be entitled for the tenant:
   `MODULE_NOT_ENTITLED` otherwise), `sourceModule / sourceType / sourceRef` name the document, `kind` is
   `AUTOMATIC` (or `OPENING` for migrated balances).
3. A line carries one side only, in the currency's minor units (SAR 2, OMR 3 — the ledger never rounds:
   `INVALID_AMOUNT`), the counterparty as `partyRef` and the facts to read back as opaque `tags` (≤ 8, e.g. `country`,
   `tax-category`); the entry balances (`ENTRY_UNBALANCED`) in one currency enabled for the tenant
   (`CURRENCY_NOT_SUPPORTED`) and is dated in an OPEN fiscal period (`PERIOD_CLOSED` — subscribe to
   `gl.FiscalPeriodStatusChanged` and retry when the period reopens). Every refusal is a `LocalizedException` and
   posts nothing; the post commits in its own transaction (the key is the retry handle).
4. Read back through `GlEntryQueryApi`: `findBySource` for a document's entries, `lines(LineQuery, page)` for a
   statement, `totals(TotalsQuery)` grouped by party or by tag keys for a return (a line without the key is grouped
   under `""`; the rows add up to the ungrouped total). Balances are computed from posted lines, never stored by the
   module; the trial balance (`GET /api/v1/gl/trial-balance`, export `GL_TRIAL_BALANCE`) is gl's.
5. React to the ledger through the outbox only: `gl.JournalEntryPosted {tenant, entryId, kind, postingModule,
   sourceModule, sourceRef, postingDate}` and `gl.FiscalPeriodStatusChanged {tenant, periodStart, periodEnd, status}`,
   consumed by an `OutboxSubscriber`.

Core types: `GlAccountLookupApi`, `GlPostingApi`, `GlEntryQueryApi`, `PostEntryRequest`, `PostEntryLine`,
`PostedEntryRef`, `AccountView`, `EntryView`, `LineView`, `AccountTotal`, `LineQuery`, `TotalsQuery`, `GlEvents`
(`com.erp.gl.crossmodule`); tables `GL_ACCOUNT`, `GL_FISCAL_PERIOD`, `GL_TENANT_CURRENCY`, `GL_JOURNAL_ENTRY`,
`GL_JOURNAL_LINE`, `GL_JOURNAL_LINE_TAG` (never read directly — no `@SharedEntity`).

Do not rebuild: a chart of accounts or account roles stored by code, a journal or posting table, a balance or
running-total table, an idempotency store for posts, a fiscal calendar or period lock, a currency decimal table, a
reversal mechanism on posted entries, a trial balance.
