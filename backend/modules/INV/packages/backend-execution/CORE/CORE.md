<!-- source: PHASE:CORE -->
<!-- traces: REQ-INV-016, REQ-INV-034, REQ-INV-037, REQ-INV-057, REQ-INV-058, REQ-INV-077, REQ-INV-132, REQ-INV-133 -->
<!-- PHASE:CORE:START traces=REQ-INV-016,REQ-INV-034,REQ-INV-037,REQ-INV-057,REQ-INV-058,REQ-INV-077,REQ-INV-132,REQ-INV-133 -->
## CORE — architecture policies

**Module layout (Spring Boot, Java 25, on the given erp-core).** Package root `com.commerce.inv`, the
application namespace of every business module (platform summary decision 11; `com.erp.*` is the
given's namespace, checklist F8). The folders follow the checklist:

| Package (`com.commerce.inv.…`) | Holds |
|---|---|
| `entity` | JPA entities extending the given `AuditableEntity` |
| `domain` | one `<Entity>Domain` companion per entity (below) — the rules that need no other aggregate and the A7 transitions (checklist C12, `domain/*Domain.java`) |
| `repository` | Spring Data repositories, one per entity |
| `service` | transactional REST-facing services — orchestrate, lock and persist; every public method carries `@PreAuthorize` (SEC-BE, ADR-INV-042) |
| `engine` | the internal engines shared by the services and `InventoryApi` — `StockPostingEngine`, `AvailabilityEngine`, `ReservationEngine`, `InventoryReadEngine`; no permission check, no REST path (ADR-INV-042) |
| `controller` | REST controllers under `/api/v1/inv` |
| `crossmodule` | the in-process `InventoryApi` other modules inject (CON-INV-006 … 021) — the only package other modules may import |
| `port` | this module's own interfaces for data owned elsewhere — implemented only by INT-C packages |
| `integration.cat` | the INT-C adapters of the CAT edges (XM-INV-012 … 015) |
| `permission` | `InvPermissions implements PermissionContributor` — page codes, `PERM_INV_*` constants, preset grants |
| `exception` | `InvErrorCodes` — the runtime codes of §5 / §5.1 as constants |
| `report` | `InvReports implements ReportProvider` — report INV_OPENING_STOCK_SHEET |
| `tenant` | `InvTenantProvisioning implements TenantProvisioningContributor`; `InvNearExpiryJob` (TenantRunner) |
| `config` | `InvFeatureFlags`, `InvSettings`, `InvNotifier` |
| `lookup` | `InvLookups` — serves INV's own six lookup keys through `OwnedLookups.read` (below; API-INV-052) |

Layers: Controller → Service → (Domain companion, Engine, Repository). A controller holds no business rule; a
repository holds no rule; a service orchestrates — it loads, locks, calls the companion and the engine, and
persists; services call other services only inside the module.

**Domain companions (`<Entity>Domain`, package `com.commerce.inv.domain`).** Each entity has a companion with a static
`create(…)` (a new valid instance) and `from(entity)` (the domain view of a stored one, whose methods apply the
transitions). The companion holds every rule that needs no other aggregate, the A7 transitions and the
default / active invariants; the service never re-states them. A rule that needs another aggregate (a
repository probe, a CAT read, a balance) stays in the service or the engine (§1.3 names which).

| Companion | Rules and transitions it holds |
|---|---|
| `WarehouseDomain` | RULE-INV-001 (code set once, change refused), 002 (nameAr required, non-blank — uniqueness stays a repository probe + constraint), 003 and 006 (default / active invariants: the default cannot be deactivated, a default must be active), deactivate / reactivate / setDefault / clearDefault |
| `BranchDomain` | RULE-INV-010, 011 (nameAr required, non-blank), 012, 015, deactivate / reactivate / setDefault / clearDefault |
| `StockBalanceDomain` | apply(quantity, measure) — the only writer of onHandQty / onHandMeasureQty; returns whether the pair crossed below zero (REQ-INV-068) |
| `StockMoveDomain` | create only (RULE-INV-023 — no mutator exists; the `StockMove` entity is `@org.hibernate.annotations.Immutable` and the table carries TRG_INV_STOCK_MOVE_IMMUTABLE — ADR-INV-049), RULE-INV-017 (quantity > 0, or the measure-only form of ADR-INV-017), moveValue = quantity × unitCost |
| `ReservationDomain` | RULE-INV-017, the A7 reservation transitions consume(quantity) (only ACTIVE) / release() (ACTIVE → RELEASED, RELEASED → unchanged, CONSUMED refused — ADR-INV-055) with RULE-INV-021, shortfall |
| `TransferDomain` | RULE-INV-016 (source ≠ destination — the active check stays in the service), 017, 029, 030, 048 on its lines, the A7 transitions post() / cancel() |
| `TransferLineDomain` | RULE-INV-017 on quantity and measure |
| `StockCountDomain` | RULE-INV-029, 030, 047 on its lines, addCounted(variant, lot, qty) (REQ-INV-088), the A7 transitions post() / cancel() |
| `StockCountLineDomain` | RULE-INV-047 |
| `StockAdjustmentDomain` | RULE-INV-017, 029, 030, 034, 048 on its lines, rowNo assignment, the A7 transitions post() / cancel() |
| `StockAdjustmentLineDomain` | RULE-INV-017, 034 (with the Item's dual-quantity flag passed in), RULE-INV-025's 4-decimal part |
| `LotDomain` | RULE-INV-036 (expiry equality against the stored expiry), activate / deactivate from on-hand, correctExpiry (REQ-INV-108) |
| `StockValuationDomain` | the weighted-average arithmetic of REQ-INV-059 … 062 and ADR-INV-030 (restart when total ≤ 0, 4-dp average, 2-dp value) |
| `ItemStockSettingsDomain` | RULE-INV-041, the system-filled key / tracking summary (ADR-INV-029) |

**Module manifest (`META-INF/erp/modules/inv.yaml`, checklist C18).**
```
code: INV
package: com.commerce.inv
api-paths: [/api/v1/inv/**]
permission-modules: [INV]
depends-on: [CAT]
home-cards: []
```
Every endpoint of §1.2 is under `/api/v1/inv/**`; the only permission module the INV code references is `INV`.

**Module boundary (checklist C20).** The module's test suite runs the given
`ModuleBoundaryRules.modulesDependOnlyOnTheirDeclaredDependencies()` (ArchUnit), so INV imports only its own
packages, the given's `com.erp.*` crossmodule / extension packages it declares in §1.5, and
`com.commerce.cat.crossmodule` (its one declared dependency; CAT v2 still declares `com.erp.cat`, and its rename
to `com.commerce.cat` is the open platform finding of business-policies-pty PLATFORM FINDINGS row 1, cited in
business-policies-inv — the rule names CAT's crossmodule package as CAT delivers it). Its own test `InvModuleBoundaryTest` adds that no
other module imports `com.commerce.inv` outside `com.commerce.inv.crossmodule`, and that nothing in
`com.commerce.inv` reads the activity preset (REQ-INV-132). The module has its own CI job `inv-backend`
(build, unit and integration tests, the two boundary rules, `api-verify` against `api-spec-inv.yaml`).

**Type mapping (postgresql16 → Java)** — column types only; the PK-generation clause is not a type (every PK is
`GENERATED ALWAYS AS IDENTITY`, mapped `@GeneratedValue(strategy = IDENTITY)`):

| DB type (db-script) | Java type | Notes |
|---|---|---|
| BIGINT (PK, FK, reference) | Long | identifiers and soft references |
| VARCHAR(n) | String | `@Size(max = n)` on request fields |
| BOOLEAN | Boolean | flags end with `Fl` |
| NUMERIC(18,3) | BigDecimal | quantities and measures, scale 3, HALF_UP |
| NUMERIC(18,4) | BigDecimal | unit and average cost, scale 4, HALF_UP |
| NUMERIC(18,2) | BigDecimal | values, scale 2, HALF_UP (SRS D-06) |
| TIMESTAMPTZ | OffsetDateTime | audit, posted / closed / last-move times |
| DATE | LocalDate | business dates and expiry (ADR-INV-016) |
| INTEGER | Integer | row number |

**Runtime error-code format:** `{MOD}-{http}[-{SLUG}]` — MOD = `INV`, http = the response status, SLUG = upper
kebab case. Every Error Catalog row (§5, §5.1) is an instance of it. Errors are raised as the given
`LocalizedException → {code, messageAr, messageEn}`; one class `com.commerce.inv.exception.InvErrorCodes`
(checklist C11, `<Module>ErrorCodes`) holds the codes as constants, and the EN and AR text of every code of §5 and
§5.1 — the four generic codes INV-400, INV-401, INV-500 and INV-409-STALE-VERSION included (ADR-INV-021 as amended at
gate-analysis revise 4) — is copied verbatim into the module's message bundles `messages.properties` (EN) and
`messages_ar.properties` (AR), the names checklist C11 uses. A
`DataIntegrityViolationException` on a named constraint is translated by constraint name: UQ_INV_WAREHOUSE_NAME_AR
→ INV-409-WAREHOUSE-NAME-TAKEN, UQ_INV_BRANCH_NAME_AR → INV-409-BRANCH-NAME-TAKEN,
UQ_INV_TRANSFER_LINE_TRF_VAR_LOT and UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_VAR_LOT → INV-422-DUPLICATE-LINE; an
`OptimisticLockException` → INV-409-STALE-VERSION; a database error whose message is
INV-409-STOCK-MOVE-IMMUTABLE (raised by TRG_INV_STOCK_MOVE_IMMUTABLE) → INV-409-STOCK-MOVE-IMMUTABLE (ADR-INV-049); any
other → INV-500 (ADR-INV-021).

**Lookups.** All LOV values are runtime-loaded from the core lookup module (mdl); no enum is hard-coded in an API
or a field specification. Stored values are codes. A code written by a request is validated as an active value
of its key through `MdlLookupApi` (INV_ADJUSTMENT_REASON for reasonCode; for measureUomCode, the consumed
unit-of-measure key named by the SettingsApi setting INV_MEASURE_UOM_KEY, platform default `CAT_UOM` —
ADR-INV-043, which revises ADR-INV-022; no second settings mechanism such as a `@Value` property is used). Codes the service itself writes (INV_MOVE_TYPE, INV_DOC_STATUS,
INV_RESERVATION_STATUS, INV_SOURCE_DOC_TYPE for INV documents, INV_CHANGE_KIND) are the seeded values of SRS A6,
held as string constants of the behaviour they select; INV_MOVE_TYPE, INV_DOC_STATUS and INV_RESERVATION_STATUS
are also CHECK-constrained in the db-script. A code the system writes itself — COUNT_DIFFERENCE on a count's
adjustment, OPENING_STOCK on an opening stock load — is not checked against the tenant's active values (ADR-INV-047).
The backend returns codes only.

**InvLookups (erp-core checklist C6).** The component `com.commerce.inv.lookup.InvLookups` serves exactly the six
keys INV owns — INV_ADJUSTMENT_REASON, INV_MOVE_TYPE, INV_DOC_STATUS, INV_RESERVATION_STATUS, INV_SOURCE_DOC_TYPE
and INV_CHANGE_KIND — through the given common helper `OwnedLookups.read(key, mdlLookupApi::readActiveValuesByKey)`,
and no other key: a key outside that set answers INV-404-LOOKUP-KEY-UNKNOWN. It is the source of every INV lookup
label and option on INV's screens (API-INV-052; the frontend lookup hooks of ADR-INV-033); a key another module
owns — CAT's unit-of-measure key included — is never served by INV.

**Numbering.** Document numbers come from the core sequence module; never generated in a business module.
`NumberSeriesApi` issues, on first save: warehouse `code` (series INV_WAREHOUSE), branch `code` (INV_BRANCH),
transfer `docNo` (INV_TRANSFER), count `docNo` (INV_COUNT), adjustment `docNo` (INV_ADJUSTMENT) — pattern
`{PREFIX}-{SEQ:4}`, prefixes WH, BR, TR, CNT, ADJ, never reset (db-script BLOCK 8c). The allocation is atomic in
the given, so two simultaneous saves never receive the same number; the tenant-led UNIQUE constraints
(UQ_INV_WAREHOUSE_CODE, UQ_INV_BRANCH_CODE, UQ_INV_TRANSFER_DOC_NO, UQ_INV_STOCK_COUNT_DOC_NO,
UQ_INV_STOCK_ADJUSTMENT_DOC_NO) are the backstop.

**Workflow engine:** forbidden. Document lifecycles (SRS A7) are explicit service transitions.

**Search contract.** Each search endpoint filters only on its screen's SRS B2 filter list, sorts on the fixed
order its SRS requirement states (code; newest first; expiry date), pages with `Page<T>` (`page`, `size`;
size ≤ 100, default 20) and answers an empty page as success. No client-chosen sort field is accepted.

**Labels on every row (gate-analysis revise 3, G6).** Every identifier a screen renders carries its display label in
the same API row, so no screen resolves an INV identifier through a capped or scoped lookup: a lot identifier carries
`lotCode` (transfer, count and adjustment lines, stock moves, balance rows), a stock count carries `adjustmentDocNo`,
a stock adjustment carries `sourceCountDocNo`, balance and availability rows carry the warehouse code and names
(ADR-INV-036). The service reads the labels of a page in one batched read by identifier (QR-INV-039 for lots,
QR-INV-036 / QR-INV-031 for documents). Two families keep their own resolution and are named here so the rule has no
silent exception: warehouse and branch identifiers on the master and document screens are labelled by the shared
warehouse and branch lookups (every row of the tenant, `active=ALL`, with a read by identifier for one off the page),
and CAT identifiers (variantPk, bundleVariantPk, itemPk) by the cross-module UXD reads.

**Languages.** Every named record carries nameAr and nameEn (nameEn optional, English display falls back to
nameAr — names of lots fall back to their code); every error carries messageAr and messageEn.

**Tenancy.** Every entity extends the given `AuditableEntity` (TENANT_ID with `@TenantId`, VERSION as
`@Version`, audit columns filled by the given auditing) — no JPA code of INV reads or writes a tenant
identifier, version or audit value (ADR-INV-014). `@TenantId` does not filter native SQL: every NATIVE query of
§4, the advisory locks of ADR-INV-044 and ADR-INV-050 and any native form of R-1 … R-6 writes `TENANT_ID = :tenantId` on each
table and join and TENANT_ID in each INSERT column list, with the current tenant taken from the given tenant
context; each such repository method carries `@TenantUnscoped(reason, adr = "ADR-INV-041")`, and a repository
test runs every one under two tenants and asserts no row of the other tenant is read or written (ADR-INV-041).

**Capability flags and settings.** `InvFeatureFlags` (service) reads `lotExpiry`, `dualQuantity`, `bundles` through
`SettingsApi` behind the FeatureFlags API — never the activity or its preset (REQ-INV-132, REQ-INV-133; an
ArchUnit rule fails the build on any read of the activity preset in `com.commerce.inv`). `InvSettings` reads and
writes INV_ALLOW_NEGATIVE_STOCK (default true, REQ-INV-066) and INV_NEAR_EXPIRY_DAYS (default 30) through
`SettingsApi`, and reads INV_MEASURE_UOM_KEY (platform default `CAT_UOM`, never written by a tenant —
ADR-INV-043); every operator-changeable value is a SettingsApi key `INV_<NAME>` (checklist C14); the tenant base currency written to every CURRENCY_CODE column (DBF-INV-061, 110, 171, 200) is
read through `SettingsApi` as well (ADR-INV-015). "Today" is the current date in the tenant's time zone,
read once per request from the given tenant context (`modules.tenant` — the same context that supplies `:tenantId`,
ADR-INV-041) and used by every date rule: expiry and FEFO (RULE-INV-038, 039), the near-expiry horizon
(REQ-INV-116, 117) and the document-date bound (RULE-INV-051 — a docDate after today answers
INV-422-DOC-DATE-FUTURE). A lot expiring today is still sellable.

**Concurrency (ADR-INV-026, ADR-INV-044).** Cross-module receive, issue and reserve first take the
transaction-scoped advisory lock of their idempotency key (`pg_advisory_xact_lock` on a hash of tenant and key —
ADR-INV-044), then look the key up. An issue then locks every reservation it references `FOR UPDATE` in ascending
reservationPk — after the idempotency lock and before the pair's balance rows — whatever the line order, so two
issues of one source document naming the same reservations in opposite order cannot deadlock (ADR-INV-026 as
amended at gate-analysis revise 4). Stock-changing transactions read the warehouse `FOR SHARE` and the Item stock
settings records of the document's Items `FOR SHARE` in ascending itemFk order; they then lock, after an
insert-if-absent, first the lot-less balance row of every Variant × warehouse pair the document touches in
ascending (variantFk, warehouseFk), then its lot rows in ascending (variantFk, warehouseFk, lotFk), then the
valuation rows in ascending variantFk order. Document headers are locked `FOR UPDATE` before any status check.
Lots: an issue and a FEFO selection read the lots they draw from `FOR SHARE` after the balance rows, in ascending
lotPk order; an expiry correction locks the lot `FOR UPDATE` (ADR-INV-026). Masters use optimistic VERSION for edits
and `FOR UPDATE` for deactivation; a default switch first takes the tenant's default-switch advisory lock
(`pg_advisory_xact_lock(hashtextextended(:tenantId || ':INV_WAREHOUSE_DEFAULT' | ':INV_BRANCH_DEFAULT', 0))`),
then reads the current default — ADR-INV-050.

**Idempotency of internal documents.** IDEMPOTENCY_KEY (NOT NULL) of a move recorded by an INV document (the opening
stock load excepted, below) is
`<sourceDocTypeCode>:<sourceDocRef>` (e.g. `INV_TRANSFER:42`), SOURCE_DOC_TYPE_CODE / SOURCE_DOC_REF /
SOURCE_DOC_NO are the document's type code, identifier and number (db-script D-P2-05, REQ-INV-056); REQUEST_HASH is the
SHA-256 of that key (ADR-INV-048). A cross-module request records the hash of its canonical request with its key, and
a repeated key with a different hash answers INV-409-IDEMPOTENCY-KEY-REUSED (RULE-INV-050, SVC-API-INT). An opening
stock load (API-INV-047) carries a client idempotency key: its moves record `OPENING_STOCK:<idempotencyKey>` and the
SHA-256 of the load's canonical form, behind the advisory lock of ADR-INV-044, so a double submit or a retry posts once
(RULE-INV-024, RULE-INV-050 — ADR-INV-053). **The hash is taken over the request as submitted** — of a cross-module
request and of an opening stock load alike: absent fields stay absent, and no default (today's docDate, the default
warehouse), trim or lookup (barcode → Variant) is applied before hashing, so a load retried after midnight or after
the default warehouse changes is still the same request (ADR-INV-048, ADR-INV-053, gate-analysis revise 4).

**Events (OutboxPublisher).** Recorded in the change transaction, delivered after commit, never on rollback:
`inv.StockMoveRecorded` — one per inserted move (CON-INV-020, REQ-INV-057); `inv.InventoryMasterChanged` — one
per committed Warehouse or Branch create / update / deactivate / reactivate / default change, with
changeKindCode from INV_CHANGE_KIND (CON-INV-021, REQ-INV-034).

**Audit (AuditApi).** Every create, change, post and cancel of a Transfer, Stock count, Stock adjustment,
Warehouse, Branch, Lot or Item stock settings record writes one audit entry with user, time and changed fields
(REQ-INV-077, checklist C16). The seven audited entities carry the given annotation with their table:
`@Audited(entityType = "INV_WAREHOUSE")`, `@Audited(entityType = "INV_BRANCH")`,
`@Audited(entityType = "INV_TRANSFER")`, `@Audited(entityType = "INV_STOCK_COUNT")`,
`@Audited(entityType = "INV_STOCK_ADJUSTMENT")`, `@Audited(entityType = "INV_LOT")` and
`@Audited(entityType = "INV_ITEM_STOCK_SETTINGS")`; their line entities are audited as part of their header's
entry. Post and cancel add an explicit business action entry through `AuditApi`.

**Notifications.** `InvNotifier` collects alerts during a transaction and sends them after commit:
negative-stock (template INV_NEGATIVE_STOCK, variables variant / warehouse / onHand — REQ-INV-068, sent when a
Variant × warehouse on-hand crosses from ≥ 0 to < 0) and near-expiry (template INV_NEAR_EXPIRY, variables
variant / lot / expiryDate / quantity — REQ-INV-117) and offline sale on an inactive record (template
INV_OFFLINE_SALE_INACTIVE, variables document / record — REQ-INV-069, ADR-INV-052, sent when an offline sale was
recorded against an inactive warehouse, Variant or Item). Recipients: users holding the Warehouse role, resolved
through `SecUserDirectoryApi`; dispatch through `NotificationDispatchApi`.

**After-commit work (checklist C4, C17 — gate-analysis revise 5, G7).** Work that must run only once the change has
committed — the lot-activity recompute (engine step 7) and the `InvNotifier` alerts (negative stock, offline sale on an
inactive record, near expiry) — is published inside the change transaction as an INV application event and handled by
a listener annotated `@TransactionalEventListener(phase = AFTER_COMMIT, fallbackExecution = true)`; nothing runs on
rollback. The event payloads carry plain values only — `tenantId`, identifiers (lotPk, variantPk, warehousePk), codes,
quantities and texts — never an entity or a persistence context. Each piece of work runs in its own transaction
(`@Transactional(propagation = REQUIRES_NEW)` on the handling service method) inside
`TenantContext.runAs(event.tenantId(), …)`, so the tenant is re-bound explicitly whatever thread the listener runs on,
and one failed lot recompute or notification never undoes another or the committed change; a failure is logged and
retried by the next move of that lot (recompute) or is not retried (notification — at most once, never twice).

**Provisioning (TenantProvisioningContributor → `InvTenantProvisioning`).** For a new tenant, inside the
provisioning transaction: if the tenant has no Branch (QR-INV-013 = 0 and no inactive one), insert «الفرع
الرئيسي» / "Main branch" default active with a code from INV_BRANCH (QR-INV-011); if it has no Warehouse,
insert «المستودع الرئيسي» / "Main warehouse" default active under the default branch with a code from
INV_WAREHOUSE (QR-INV-004). A second run creates nothing (REQ-INV-001, 002, 020, 021; SRS D-08).

**Daily near-expiry job (TenantRunner → `InvNearExpiryJob`).** Once a day per tenant, when lotExpiry is on: read
the lots with non-zero on-hand, expiry within today + INV_NEAR_EXPIRY_DAYS and NEAR_EXPIRY_NOTIFIED_AT empty
(QR-INV-038, job mode — one row per lot, on-hand summed over every warehouse). For each lot read, **claim first, send
after commit** (gate-analysis revise 5, G5): in a short transaction of its own, run the near-expiry claim of QR-INV-041
— `UPDATE INV_LOT SET NEAR_EXPIRY_NOTIFIED_AT = :now WHERE LOT_PK = :id AND NEAR_EXPIRY_NOTIFIED_AT IS NULL` (DBF-INV-179)
— and queue the notification, with the lot's total on-hand as {quantity}, only when exactly 1 row was updated; the
notification is sent after that transaction commits (after-commit work, above). A concurrent run of the job finds the
row already stamped (0 rows) and queues nothing, and a crash between claim and send loses at most one notification —
it never sends one twice (AC-INV-131; REQ-INV-117, SRS D-19).

**Ports to data owned elsewhere** (interfaces of this module; the implementation of each is its INT-C package,
injected; until it is delivered the dependent check is unavailable and the executor follows that block's
`if_not_met`):

| Port (com.commerce.inv.port) | Reads | Edge |
|---|---|---|
| `VariantReadPort` | Variant active state, its Item, Item kind and active state, code and names, by identifier (batch) | XM-INV-014 |
| `ItemReadPort` | Item kind, code, names, active state and the identifiers of its Variants | XM-INV-015 |
| `BundleComponentPort` | active component lines of a bundle Variant (component Variant, quantity in the component's base unit) | XM-INV-012 |
| `BarcodePort` | the Variant a barcode identifies | XM-INV-013 |
<!-- PHASE:CORE:END -->
