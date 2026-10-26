# Review checklist — a business module on erp-core

One line per convention and per forbidden item; answer yes / no, and name the file that proves it. A "no" on any
line is a rejection; a gap in the core is an ADR `CORE-GAP-<topic>.md` in the project, never a workaround.

## Conventions

| # | Check | Look for |
|---|---|---|
| C1 | Every table has `TENANT_ID BIGINT NOT NULL`, `FK_<TABLE>_TENANT → CORE_TENANT(ID)`, an index led by `TENANT_ID`, every `UNIQUE` composite with `TENANT_ID` | the `V1000+` migration |
| C2 | Every entity extends `com.erp.common.domain.AuditableEntity` and declares no tenant field | `entity/*.java` |
| C3 | Every native query / `JdbcTemplate` statement names `TENANT_ID` and its method carries `@TenantUnscoped(reason, adr)` | `repository/*.java`, `grep -r "nativeQuery\|JdbcTemplate"` |
| C4 | Jobs walk tenants through `TenantRunner`; async work runs on `erpCoreEventExecutor` / a decorated executor or re-binds with `TenantContext.runAs` | `@Scheduled`, `@Async`, listeners |
| C5 | Every user-maintained code list is an MDL lookup type seeded in `V1000+` (`MDL_LOOKUP_TYPE` + `MDL_LOOKUP_VALUE`, `TENANT_ID = 1`) and read through `MdlLookupApi.readActiveValuesByKey`; the module stores the code | the migration, `service/*.java` |
| C6 | The module serves only its own lookup keys through `OwnedLookups.read(...)` | `service/*.java` |
| C7 | Every byte goes through `FilePrivateStoreApi` / `FileImageStoreApi` / the FILE endpoints; the module keeps `FILE_DOCUMENT.ID` columns and asks `FileDocumentLookupApi` for URLs | `service/*.java`, `entity/*.java` |
| C8 | Every message is `NotificationDispatchApi.dispatch(DispatchCommand)` with a `NOTIF_TEMPLATE` code seeded in `V1000+` | `service/*.java`, the migration |
| C9 | Every document number is `NumberSeriesApi.next(code)` from a `CORE_NUMBER_SERIES` row seeded in `V1000+`, stored in a column unique with `TENANT_ID` | `service/*.java`, the migration |
| C10 | Every list export / tabular report is a `ReportProvider` bean; printed documents go through `PrintApi` + `PrintResponses` | `report/*.java` |
| C11 | Every error is `LocalizedException(Status, <Module>ErrorCodes.X, args)`; every code has an EN and an AR message | `exception/<Module>ErrorCodes.java`, `messages.properties`, `messages_ar.properties` |
| C12 | Business rules live on the entity's Domain companion (`create()` / `from()`), the service orchestrates | `domain/*Domain.java` |
| C13 | One `PermissionContributor` (`<Module>Permissions`) with a `VIEW` per screen; every service method has `@PreAuthorize("hasAuthority(T(…<Module>Permissions).PERM_…)")` referencing its own module only | `permission/<Module>Permissions.java`, `service/*.java` |
| C14 | Operator-changeable values are read through `SettingsApi` under `<MODULE>_<NAME>` keys | `service/*.java`, `grep -r "@Value"` |
| C15 | Migrations are `V1000+`, `V<N>__<module>_<snake_case>.sql`, sequential, additive once shipped; the entity and the migration agree on every physical name | `db/migration/` |
| C16 | Entities whose changes a reviewer must see carry `@Audited(entityType = "<TABLE>", ignore = {…})` | `entity/*.java` |
| C17 | `@Audited`, `@TransactionalEventListener(phase = AFTER_COMMIT, fallbackExecution = true)` for every listener; events carry plain values only | listeners, `*Event.java` |
| C18 | The module ships `META-INF/erp/modules/<code>.yaml`; its permission modules are contributed in its `package`; every endpoint is under its `api-paths` | `src/main/resources/META-INF/erp/modules/`, `controller/*.java` |
| C19 | Business events another module consumes are `OutboxPublisher.publish("<code>.<Name>", payload)` inside the writing transaction; every `OutboxSubscriber` is idempotent on `OutboxMessage.id()` | `service/*.java`, `*Subscriber.java` |
| C20 | `ModuleBoundaryRules.modulesDependOnlyOnTheirDeclaredDependencies()` runs in the module's tests and the module has its own CI job | `src/test/java`, the CI workflow |
| C21 | Every home-screen figure is a `home-cards` entry of the manifest (`key`, `title-key`, `description-key`, `permission` of this module, `order`, `target` = a screen of its `permission-modules`) valued by exactly one `HomeCardProvider` bean whose `moduleCode()` is the manifest's code; the provider only reads; both title keys are in the module's EN and AR bundles | `META-INF/erp/modules/<code>.yaml`, `*HomeCards.java`, `messages*.properties` |
| C22 | A module that posts to the ledger stores account roles as `AccountView.accountId`s (never codes), calls `GlPostingApi.post` with a stable per-document `idempotencyKey` and its own code as `postingModule`, posts a correction as a new entry under a new key, reads balances through `GlEntryQueryApi.totals` and has no journal, balance or chart table | the role-mapping setting or table, `*Posting*.java`, `V1000+` migrations |

## Forbidden — any "yes" is a rejection

| # | Found? | Look for |
|---|---|---|
| F1 | `erp.core.tenant.mode` or `erp.core.tenant.default-tenant-id` read anywhere in the module | `grep -r "erp.core.tenant"` (`TenancyModeIsolationRules` also fails the build) |
| F2 | A native query without `TENANT_ID`, or without `@TenantUnscoped` on its method | `grep -r "nativeQuery = true"` |
| F3 | A hardcoded lookup code list: an enum, a `Map` of labels, a `switch` over user-maintained codes, a module "types" table | `enum`, `Map.of(` of codes, the migration |
| F4 | Direct file-system or S3 access for business content, or a multipart endpoint of the module | `java.io.File`, `Files.write`, `S3Client`, `MultipartFile` |
| F5 | A second auth, permission or settings mechanism: a user / role / permission / grant / session table, a login or JWT filter, a `SecurityFilterChain`, a settings table, `@Value("${erp.core` | the migration, `grep -r "SecurityFilterChain\|@Value"` |
| F6 | A module counter for document numbers: a counter table or column, `MAX(` + 1, `nextval(` read for a user-visible number | the migration, `repository/*.java` |
| F7 | A second export path: a CSV / XLSX / PDF writer, an `/export` endpoint, a report permission of your own | `controller/*.java`, `grep -r "text/csv\|@GetMapping(\"/export"` |
| F8 | A copy of a core class, a `com.erp.*` package in the application, an edit to a core migration | `src/main/java/com/erp`, `db/migration/core` |
| F9 | A workaround for a missing core capability instead of `CORE-GAP-<topic>.md` | the ADR folder |
| F10 | A licence check of the module's own, a module on/off setting or a branch on the deployment mode | `grep -r "licen\|entitle\|tenant.mode"` |
| F11 | A business event to another module through `DomainEventPublisher`, or an event / outbox table of the module | `*Event.java`, the migration |
| F12 | A home-screen card outside the manifest: a dashboard / summary endpoint of the module's own, a `HomeCardProvider` that checks the licence or the permission itself, or one that writes | `controller/*.java`, `*HomeCards.java` |
