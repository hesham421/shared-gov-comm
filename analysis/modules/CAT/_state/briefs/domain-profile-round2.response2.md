Web research was denied again: all four searches and a direct fetch were refused. I've kept every external claim as `NO-RESEARCH`. I rewrote the contested proposals (P-04, P-07, P-08, P-09) so they don't depend on the unverified claims. That lets the dialogue converge without presenting guesses as facts.

<<<FILE: governance-shared/analysis/domain/domain-profile.md>>>
# DOMAIN PROFILE — Retail & Wholesale Commerce SaaS on erp-core
══════════════════════════════════════════════════════════════════
Profile         : commerce (COMMERCE)
Version         : 1            (per shared/VERSIONING.md)
Last Updated    : 2026-10-22
Status          : FRESH — dialogue round 2 (claude:sonnet; decisions D-01…D-12 answer proposals P-01…P-12; converged)
Research        : 2 internal sources cited (KB raw-idea.md; erp-core 1.5.0 manifest). Live web research was denied in rounds 1 and 2 — every external claim is NO-RESEARCH (block 9) and no decision rests on one
Given           : erp-core 1.5.0 — manifest `deps/erp-core/core-manifest.yaml`, skill `.claude/skills/erp-core-usage@1.5.0`
Run module      : CAT (Catalog)
Input note      : the `raw-idea` input arrived MISSING. The profile's knowledge file `profiles/commerce/knowledge/raw-idea.md` (Platform Brief, owner Hesham Ezzat, approved 2026-10-21) is used as the raw idea and platform brief.
══════════════════════════════════════════════════════════════════

## 1. SCOPE

**In bounds** (stated in [KB:raw-idea.md §1, §3 D1, §5]):
- A multi-tenant SaaS for small and medium **retail and wholesale traders** in Egypt and the Gulf.
- Back-office: purchasing, inventory, sales, receivables/payables, accounting, tax & e-invoicing.
- Point of sale (offline-capable POS mode inside Sales).
- An integrated e-commerce **storefront**: one catalogue, one stock, one customer base.
- Many trading activities from one codebase: electronics & appliances, apparel, household goods, cosmetics, grocery, spare parts, building materials, and so on. Behaviour is driven by six capability flags and per-activity presets ([KB:raw-idea.md §4]).
- Deployment `erp.tenancy.mode=multi` by default, with `single` for dedicated or on-prem customers ([KB:raw-idea.md §3 D3]).

**Out of bounds** ([KB:raw-idea.md §3 D1, §9]):
- Pharmacies, restaurants, gold, vehicles, manufacturing, contracting and services, because they use different business models.
- Native mobile apps in v1 (responsive web only).
- Marketplace (multi-vendor in one storefront).
- Report designer.
- BPM workflow engine.
- AI assistant (deferred core module, "advanced" delivery tier [KB:raw-idea.md §8]).

## 2. PURPOSE

Mandatory e-invoicing (Saudi ZATCA phase-2 waves; Egypt's B2C e-receipt expansion from Sept 2025) forces every small trader to adopt compliant software. Wholesale and retail trade is the largest activity segment: 54.4% of Egypt's establishments and 30.7% of Saudi SMEs ([KB:raw-idea.md §2]). The platform gives these traders **one product for back-office + POS + storefront**. It is activity-aware and **feature-complete but simple**: every capability exists, nothing is forced on the user, and the UI shows only what is enabled ([KB:raw-idea.md §1, §2]).

## 3. RESPONSIBILITIES

As stated in [KB:raw-idea.md §5]. The platform owns:
1. **Catalog**: items, variants, media, categories, bundles, publish state.
2. **Pricing**: price lists, promotions, coupons, loyalty points.
3. **Inventory**: stock, reservations, transfers, counts, lot/expiry, dual quantity.
4. **Party**: customers (including CUSTOMER-realm users), suppliers, addresses, credit.
5. **Purchasing**: purchase order, goods receipt, supplier invoice.
6. **Sales & POS**: quotation, order, invoice, returns/exchange; offline-capable POS mode.
7. **Settlements**: collections, payments, AR/AP ageing, payment-gateway adapter.
8. **Tax & E-invoicing**: VAT, country adapters (ETA / ZATCA / Oman), tax returns.
9. **Accounting**: auto-posting rules, GL, cost centres, budgets, period close. Fully present but invisible until "accounting mode" ([KB:raw-idea.md §3 D7]).
10. **SerialWarranty**: serials, warranty cards, service returns (flag `serialTracking`).
11. **Approvals**: per-document-type approval steps (none / one step / role-based) on one screen ([KB:raw-idea.md §3 D6]).
12. **Shipping**: delivery status, carrier adapters.
13. **Storefront**: public catalogue/search, guest cart, checkout, customer area, tenant-by-host resolver ([KB:raw-idea.md §3 D4, D5]).
14. **Insights**: dashboards, 15–20 standard reports, saved filters.
15. **Activity settings** (`SET`): the single `FeatureFlags` API, activity presets and the 5-step setup wizard ([KB:raw-idea.md §4.1, §6.9, §7]). Owner decided in D-02.

Capabilities the given `erp-core` 1.5.0 provides (tenancy, users/roles/customer auth, settings, lookups, files, numbering, events, audit, notifications, printing/reports, module entitlements) are **used, not owned** (block 4).

## 4. MAIN COMPONENTS

Module codes other than CAT are **PROPOSED** and carried into `P-1` as RESERVED (D-01, block 7.3).

| # | Component | Module code | Bounded context | Category (user-defined) | Core / extension | Notes |
|---|-----------|-------------|-----------------|--------------------------|------------------|-------|
| 1 | Catalog | CAT | catalog | Business — foundation | Core | Item, variants, media, categories, bundles, publish state. DAG #1, no business dependency. Variant attributes, tax-classification codes and barcodes are platform-own (D-03, D-04, D-07, D-08) |
| 2 | Pricing | PRC (PROPOSED) | pricing | Business | Core | Price lists, promotions, coupons, loyalty. Depends on Catalog. Loyalty is an advanced tier ([KB §8]) |
| 3 | Inventory | INV (PROPOSED) | inventory | Business | Core | Stock, reservations, transfers, counts, lot/expiry, dual quantity. Every stock move carries `warehouse_id` ([KB §6.8]). Lot and dual-quantity storage are platform-own (D-03) |
| 4 | Party | PTY (PROPOSED) | party | Business — foundation | Core | Customers, suppliers, addresses, credit. Partial given hit: credentials and customer realm are `erp-core:modules.sec` (D-10) |
| 5 | Purchasing | PUR (PROPOSED) | purchasing | Business | Core | PO, goods receipt, supplier invoice |
| 6 | Sales & POS | SAL (PROPOSED) | sales | Business | Core | Quotation, order, invoice, returns/exchange; POS mode offline-capable |
| 7 | Settlements | STL (PROPOSED) | settlements | Business | Core | Collections, payments, AR/AP ageing, payment-gateway adapter. The "cash & bank seams" the brief assumes are not in the erp-core 1.5.0 manifest, so cash & bank is platform-own (D-03) |
| 8 | Tax & E-invoicing | TAX (PROPOSED) | tax | Business — compliance | Core | VAT, ETA/ZATCA/Oman adapters, tax returns. The "tax adapter" seam is not in the manifest, so it is platform-own (D-03) |
| 9 | Accounting | ACC (PROPOSED) | accounting | Business — finance | Core (hidden until accounting mode) | Auto-posting, GL, cost centres, budgets, period close |
| 10 | SerialWarranty | SRW (PROPOSED) | after-sales | Business | Extension — flag `serialTracking` (+ `warranty`) | Serials, warranty cards, service returns |
| 11 | Approvals | APR (PROPOSED) | approvals | Business — workflow | Extension — lightweight | No BPM engine (D6). Uses given events + notif |
| 12 | Shipping | SHP (PROPOSED) | shipping | Business — fulfilment | Core (carrier adapters advanced) | Delivery status, carrier adapters |
| 13 | Storefront | STF (PROPOSED) | storefront | Channel | Core | Public catalogue/search, guest cart, checkout, customer area. Partial given hit: the host→tenant resolver feeds `erp-core:modules.tenant` TenantContext (D-11) |
| 14 | Insights | INS (PROPOSED) | insights | Analytics | Core | Dashboards, 15–20 standard reports, saved filters. Partial given hit: report/print engine is `erp-core:modules.report` |
| 15 | Activity settings (FeatureFlags API, presets, setup wizard) | SET (PROPOSED) | configuration | Platform — configuration | Core | Owns the semantics of the six flags and the presets. Persistence through `erp-core:modules.cu` (SettingsApi) — partial given hit. DAG level 0 (D-02) |
| 16 | Tenancy, tenant context, branding, public app-config | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.tenant` (Tenant; TenantLookupApi, TenantProvisioningContributor, TenantPolicy). `GET /api/public/app-config` ([KB technical base]) |
| 17 | Users, roles, permissions, staff & customer auth, customer accounts, signup | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.sec` (User, Role, …; PermissionContributor, SecUserDirectoryApi). Role presets Owner / Seller-Cashier / Warehouse are configured on it (D8) |
| 18 | Settings / configuration / tenant overrides | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.cu` (AppConfiguration; SettingsApi) |
| 19 | Lookups / master data code lists | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.mdl` (LookupType, LookupValue; MdlLookupApi). Brand, unit of measure and tax-classification schemes are lookup types (D-05, D-08) |
| 20 | Files, images, public files, attachments | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.file` (FileDocument, FileCategory; FileImageStoreApi, FileDocumentLookupApi). Catalog media uses it (D-12) |
| 21 | Number series / document numbers | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.sequence` (NumberSeries; NumberSeriesApi) |
| 22 | Domain events / outbox | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.events` (DomainEventPublisher, OutboxPublisher, OutboxSubscriber) |
| 23 | Audit trail / change history | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.audit` (AuditEvent; AuditApi) |
| 24 | Notifications (email, in-app, templates) | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.notif` (NotificationDispatchApi, ChannelProvider, RecipientDirectory) |
| 25 | Reports, exports, printing (Jasper) | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.report` (PrintApi, ReportProvider, PrintTemplateSource) |
| 26 | Business-module catalogue & entitlements | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.module` (ModuleCatalogApi, ModuleEntitlementApi) |
| 27 | Base entity, audit columns, optimistic locking, validation, search, paging, idempotency, API envelope | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.common` |

## 5. GOVERNING RULES

| # | Rule | Source |
|---|---|---|
| G1 | Business logic reads **capability flags only** (`variants`, `serialTracking`(+`warranty`), `lotExpiry`, `dualQuantity`, `bundles`, `b2bCredit`), never the activity name. An activity is a preset that sets flags. Enforced by ArchUnit/lint: `activity ==` or a preset read outside settings fails the build | [KB:raw-idea.md §3 D2, §4.1, §7] |
| G2 | Every flag is tested in both states. The UI shows fields, menus, reports and storefront features only when their flag is on | [KB:raw-idea.md §4.1, §6.3] |
| G3 | A flag may be turned **on** at any time. Turning it **off** is blocked while data depends on it. Changing presets never requires re-setup | [KB:raw-idea.md §6.2] |
| G4 | Business modules are tenant-agnostic. Tenancy is `multi` by default and `single` optional | [KB:raw-idea.md §3 D3]; given `erp-core:modules.tenant` |
| G5 | Module dependencies follow the fixed DAG of §5 and flow downward only. Cross-module calls go only through declared `crossmodule` APIs. Core is used only through manifest extension points | [KB:raw-idea.md §5 rules, §7]; [C:C-GIVEN-1] [C:C-GIVEN-2] |
| G6 | Nothing the given `erp-core` provides is rebuilt | [KB:raw-idea.md header]; [C:C-GIVEN-3] |
| G7 | Simplicity is measurable: ≤ 4 mandatory fields per screen. Setup plus first invoice takes ≤ 30 min without training; a new employee sells within ≤ 10 min | [KB:raw-idea.md §3 D9, §6.1, §6.10] |
| G8 | Multi-dimension defaults: one warehouse, branch, currency and price list to start. Every dimension exists in the data model from day one. A selector is hidden while its count is 1 and is revealed with its related actions and filters when a second one is added. No migration, no mode switch | [KB:raw-idea.md §6.8] |
| G9 | Guest checkout is allowed. The guest becomes a CUSTOMER-realm account after the first order | [KB:raw-idea.md §3 D5] |
| G10 | The storefront resolves the tenant by subdomain (custom domain optional). It feeds the core TenantContext and returns the default tenant in `single` mode | [KB:raw-idea.md §3 D4] |
| G11 | Approvals are a lightweight module (none / one step / role-based steps per document type). No BPM engine | [KB:raw-idea.md §3 D6] |
| G12 | Accounting is fully present but hidden by default, with auto-posting from documents | [KB:raw-idea.md §3 D7] |
| G13 | "Covers activity X" means: the preset exists, its conformance scenario (setup → receive → sell → return → collect → report) is green in CI, and every flag × activity matrix cell is traced | [KB:raw-idea.md §7] |
| G14 | Delivery is module-by-module in the fixed order of §8. A module is Done only with its reports, tests and conformance green | [KB:raw-idea.md §3 D10, §8] |
| G15 | E-invoicing compliance (ZATCA phase 2, Egypt ETA e-invoice/e-receipt, Oman) is a market driver and a Tax-module responsibility. Catalog holds the item-side data (classification codes per scheme, Arabic names) but TAX owns the scheme definitions and their validation (D-08, D-09). The exact country requirements are unverified (NO-RESEARCH) and are handed to the TAX stage to verify | [KB:raw-idea.md §2, §5 #8]; block 9 |
| G16 | The Item/Variant key rule: stock, price, barcode and tax-classification attach to the Variant, never to the Item. An Item always has at least one Variant, so turning `variants` on needs no data migration | [KB:raw-idea.md §4.1, §6.2, §6.8]; D-04 |

## 6. RELATIONSHIPS WITH OTHER DOMAINS

Business-module dependencies are taken from the fixed DAG [KB:raw-idea.md §5]. The kind for module-to-module edges follows D-01: HARD-FK where the dependent holds a mandatory key to the owner's record, SOFT-READ where it reads through a `crossmodule` API or an event.

| This component | Depends on | Kind | Direction | Stated by |
|---|---|---|---|---|
| Activity settings (SET) | erp-core cu (SettingsApi) | SOFT-READ | erp-core:modules.cu → SET | dialogue D-02 |
| All business modules | Activity settings (SET), via `FeatureFlags` | SOFT-READ | SET → every module | dialogue D-02; user [KB §4.1, §7] |
| Catalog | erp-core file (public images) | SOFT-READ (file id via FileImageStoreApi) | erp-core:modules.file → Catalog | user [KB §5] |
| Catalog | erp-core mdl (lookups) | SOFT-READ (MdlLookupApi) | erp-core:modules.mdl → Catalog | user [KB §5] |
| Pricing | Catalog | HARD-FK (to Variant) | Catalog → Pricing | user [KB §5] |
| Inventory | Catalog | HARD-FK (to Variant) | Catalog → Inventory | user [KB §5] |
| Inventory | erp-core sequence, events, audit | SOFT-READ (extension APIs) | erp-core → Inventory | user [KB §5] |
| Party | erp-core sec (customer realm), cu | SOFT-READ | erp-core → Party | user [KB §5] |
| Purchasing | Catalog, Inventory, Party | HARD-FK | owners → Purchasing | user [KB §5] |
| Sales & POS | Catalog, Pricing, Inventory, Party | HARD-FK | owners → Sales & POS | user [KB §5] |
| Settlements | Party, Purchasing, Sales & POS | HARD-FK | owners → Settlements | user [KB §5] |
| Tax & E-invoicing | Purchasing, Sales & POS | HARD-FK | owners → Tax | user [KB §5] |
| Tax & E-invoicing | Catalog (reads item classification codes) | SOFT-READ (crossmodule API, transitive through DAG) | Catalog → Tax | dialogue D-08 |
| Accounting | Purchasing, Sales & POS, Settlements, Tax | SOFT-READ (posting from events) | owners → Accounting | user [KB §5, D7] |
| SerialWarranty | Inventory, Sales & POS | HARD-FK | owners → SerialWarranty | user [KB §5] |
| Approvals | any document module | SOFT-READ (events) | document modules → Approvals | user [KB §5, D6] |
| Shipping | Sales & POS; erp-core notif | HARD-FK / SOFT-READ | owners → Shipping | user [KB §5] |
| Storefront | Catalog, Pricing, Inventory, Party, Sales & POS, Settlements, Shipping; erp-core sec, file, tenant | SOFT-READ (crossmodule APIs) | owners → Storefront | user [KB §5, D4] |
| Insights | all modules; erp-core report | SOFT-READ | owners → Insights | user [KB §5] |
| Tax & E-invoicing | external tax authorities (Egypt ETA, Saudi ZATCA, Oman) | external integration (adapter) | Tax → authority | user [KB §5 #8] |
| Settlements | external payment gateways | external integration (adapter) | Settlements → gateway | user [KB §5 #7] |
| Shipping | external carriers | external integration (adapter) | Shipping → carrier | user [KB §5 #12] |

## 7. STEERING  (read verbatim by every later stage)

### 7.1 Ubiquitous language

The terms come from the brief [KB:raw-idea.md] and the decisions of block 8.

| Term (en / ar) | Definition | Do not say | Module code |
|---|---|---|---|
| Item / صنف | A thing the trader buys, stocks or sells. It is the catalogue record that groups its variants | product (in code/UI labels), article, material, مادة, منتج (as entity name) | CAT |
| Variant / متغيّر الصنف | A concrete sellable and stockable version of an Item (for example size × colour). Every Item has at least one. Stock, price, barcode and tax-classification attach to it (D-04) | SKU (as entity name), child item, option, sub-item, صنف فرعي | CAT |
| Variant attribute / خاصية المتغيّر | A dimension that variants differ by (size, colour, …) | option, property, characteristic | CAT |
| Category / فئة | A node in the tenant's hierarchical item classification used for browsing and reporting (D-05) | group, family, department, مجموعة | CAT |
| Brand / علامة تجارية | The maker or brand label of an item, held as a given `mdl` lookup (D-05) | manufacturer (unless distinct), make | CAT (via mdl) |
| Unit of measure / وحدة القياس | The unit an item is counted, weighed or measured in, held as a given `mdl` lookup. Conversions between units of one item belong to Catalog (D-05) | UOM code, unit type | CAT (via mdl) |
| Barcode / باركود | A scannable code identifying a Variant. A Variant has zero or more, public (GTIN) or internal (D-07) | item code, SKU | CAT |
| Tax classification code / رمز التصنيف الضريبي | The code an item carries under a named tax-authority scheme, for e-invoice lines. Catalog stores it, TAX defines the scheme (D-08) | HS code (unless that is the scheme), tax code (ambiguous with tax rate) | CAT (data), TAX (schemes) |
| Bundle / حزمة | A composite item, kit or promo pack sold as one line and made of component Variants (flag `bundles`). Catalog owns the composition (D-06) | combo, package, composite (as entity name), طقم | CAT |
| Media / وسائط | The images and files attached to an item for display. Binaries live in given `file` (D-12) | gallery, pictures, assets | CAT |
| Publish state / حالة النشر | Whether an item is visible in the storefront | status (unqualified), active flag, visibility | CAT |
| Capability flag / مفتاح القدرة | One of six switches (`variants`, `serialTracking`, `lotExpiry`, `dualQuantity`, `bundles`, `b2bCredit`) that business logic reads | feature toggle (in domain text), mode, activity type | SET (PROPOSED) |
| Activity / النشاط التجاري | The trading activity a tenant declares (for example apparel). It is never read by business logic | business type, sector, vertical (in code) | SET (PROPOSED) |
| Activity preset / القالب المسبق للنشاط | A named set of flag values and default settings applied when an activity is chosen | template, profile, package | SET (PROPOSED) |
| Dual quantity / الكمية المزدوجة | Counting an item in two quantities (count + weight or length), priced by either | secondary unit, catch weight (in UI) | INV (PROPOSED) |
| Lot / التشغيلة | A batch of stock received together, carrying an expiry date (flag `lotExpiry`) | batch (in UI), دفعة | INV (PROPOSED) |
| Serial number / الرقم التسلسلي | The unique identifier of one physical unit (flag `serialTracking`) | IMEI (except as a kind of serial), serial code | SRW (PROPOSED) |
| Price list / قائمة الأسعار | A set of variant prices for a channel or customer group | tariff, rate card | PRC (PROPOSED) |
| Warehouse / المستودع | A physical stock location. Present from day one, with the selector hidden while there is only one | store (ambiguous with storefront), depot, مخزن | INV (PROPOSED) |
| Customer / العميل | A party that buys. May be linked to a CUSTOMER-realm account (D-10) | client, buyer, زبون | PTY (PROPOSED) |
| Supplier / المورّد | A party the trader buys from | vendor, provider | PTY (PROPOSED) |
| Guest / الزائر | A storefront shopper who checks out without an account. They become a Customer account after their first order | anonymous user, visitor account | STF (PROPOSED) |
| Storefront / المتجر الإلكتروني | The tenant's public online shop, resolved by subdomain or custom domain | webshop, e-shop, site | STF (PROPOSED) |
| Point of sale (POS) / نقطة البيع | The cashier selling mode of Sales & POS, offline-capable | till, checkout (reserved for storefront) | SAL (PROPOSED) |
| Tenant / المستأجر | One trader's isolated data space on the platform (given: erp-core) | company (as tenancy term), shop, account | — (given) |

### 7.2 Bounded contexts

The profile's own `bounded_contexts` list was not provided in this brief. The contexts below are PROPOSED, one per module (D-01, D-02).

| Context | Owns module codes | Boundary statement |
|---|---|---|
| configuration | SET | How the platform behaves per tenant: capability flags, activity presets, setup wizard. No business data |
| catalog | CAT | What is sold: items, variants, attributes, categories, bundles, barcodes, tax-classification codes, media, publish state. No price, no stock quantity |
| pricing | PRC | What it costs: price lists, promotions, coupons, loyalty |
| inventory | INV | Where it is and how much: stock, moves, reservations, transfers, counts, lots, dual quantity |
| party | PTY | Who: customers, suppliers, addresses, credit. Credentials belong to given sec |
| purchasing | PUR | Buying documents |
| sales | SAL | Selling documents and the POS mode |
| settlements | STL | Money in and out: collections, payments, ageing, gateway adapter |
| tax | TAX | Tax computation, tax-classification scheme definitions, e-invoice submission, returns |
| accounting | ACC | Ledger, posting rules, cost centres, budgets, close |
| after-sales | SRW | Serials, warranty, service returns |
| approvals | APR | Approval steps per document type |
| shipping | SHP | Delivery status and carriers |
| storefront | STF | Public online channel and host-to-tenant resolution |
| insights | INS | Dashboards, standard reports, saved filters |

### 7.3 Module prefixes proposal

| Code | Display (en / ar) | Status |
|---|---|---|
| CAT | Catalog / الكتالوج | IN PROFILE (module of this run) |
| SET | Activity settings / إعدادات النشاط | PROPOSED (D-02) — carried into `P-1` as RESERVED |
| PRC | Pricing / التسعير | PROPOSED (D-01) — RESERVED |
| INV | Inventory / المخزون | PROPOSED (D-01) — RESERVED |
| PTY | Party / الأطراف | PROPOSED (D-01) — RESERVED |
| PUR | Purchasing / المشتريات | PROPOSED (D-01) — RESERVED |
| SAL | Sales & POS / المبيعات ونقطة البيع | PROPOSED (D-01) — RESERVED |
| STL | Settlements / التسويات | PROPOSED (D-01) — RESERVED |
| TAX | Tax & E-invoicing / الضرائب والفوترة الإلكترونية | PROPOSED (D-01) — RESERVED |
| ACC | Accounting / المحاسبة | PROPOSED (D-01) — RESERVED |
| SRW | SerialWarranty / الأرقام التسلسلية والضمان | PROPOSED (D-01) — RESERVED |
| APR | Approvals / الموافقات | PROPOSED (D-01) — RESERVED |
| SHP | Shipping / الشحن | PROPOSED (D-01) — RESERVED |
| STF | Storefront / المتجر الإلكتروني | PROPOSED (D-01) — RESERVED |
| INS | Insights / التحليلات | PROPOSED (D-01) — RESERVED |

No code is proposed for the given rows (block 4 rows 16–27), because they are used, not built.

### 7.4 Identifier rules

Later stages build IDs as `{prefix}-{MOD}-{seq}` (seq width 3) using the module codes above. Entity kinds are `master, transactional, lookup, config, security`. This profile adds no domain-specific atom (`profile.ids.atoms`). Entities provided by erp-core (block 4 given rows) receive no entity ID from this platform [C:C-GIVEN-3].

### 7.5 Knowledge sources to cite

- `profiles/commerce/knowledge/raw-idea.md`
- The erp-core 1.5.0 manifest (`deps/erp-core/core-manifest.yaml`)
- No external research source has been accepted: web research was denied in rounds 1 and 2 (block 9).

## 8. RESOLVED DECISIONS

Each decision answers the round-1 proposal of the same number (D-nn ↔ P-nn). Round 1 was recommended by claude:opus, round 2 challenged by claude:sonnet. The user's confirmation is still pending for all of them.

| # | Point | Decision | Recommended by dialogue? | Confirmed by user | Sources |
|---|---|---|---|---|---|
| D-01 | Module codes and bounded contexts | Accepted: 14 codes as in 7.3, one context per module (7.2). Module-to-module edges are HARD-FK for mandatory keys, SOFT-READ for crossmodule/event reads | Yes — opus proposal accepted by sonnet | pending | KB §5, §7 |
| D-02 | Owner of FeatureFlags, presets, setup wizard | Amended: a new level-0 foundation module `SET` (context `configuration`) owns flag/preset semantics and the wizard. It persists through given `cu`. It has no business dependency, so the fixed DAG order is unchanged and it ships with the first delivery tier | Yes — amended | pending | KB §4.1, §6.2, §6.9, §7; manifest `cu` |
| D-03 | Seams the brief assumes but the erp-core 1.5.0 manifest lacks | Accepted, with a re-match duty: custom attributes, lot, dual-quantity storage, cash & bank and the tax adapter are platform-own (CAT, INV, INV, STL, TAX). If a later erp-core manifest provides one, the next profile version re-matches it as EXISTING (given) | Yes — accepted | pending | KB §4.1, §5; manifest §1.5 |
| D-04 | Item / Variant model | Accepted: every Item has at least one Variant (an implicit default when `variants` is off). Stock, price, barcode and tax-classification attach to the Variant (rule G16). The decision rests on KB §6.2/§6.8 (no migration when a flag flips), not on any industry comparison | Yes — accepted, rationale narrowed | pending | KB §4.1, §6.2, §6.8 |
| D-05 | Categories, brands, units of measure | Amended: Category is a CAT-owned tree. Brand and UoM codes are given `mdl` lookup types. Per-item unit conversions (for example box → piece) are CAT-owned because a lookup value holds a code, not a factor. Whether `mdl` can hold a hierarchy is not needed, since Category is not a lookup | Yes — amended | pending | KB §5 #1; manifest `mdl` |
| D-06 | Bundles | Accepted: CAT owns the composition (a bundle Item whose components are Variants). INV and SAL explode it to components at movement/sale. Bundle stock is derived from components, never stored. Whether bundles may nest is handed to the CAT requirements stage | Yes — accepted | pending | KB §4.1 |
| D-07 | Barcodes | Amended: a Variant carries 0..n barcodes of a kind (public GTIN, or internal). Weighed and variable-measure items use an internal code under `dualQuantity`. The embedding layout (price or weight position) is a field-level matter for the CAT/SAL stage. This profile hard-codes no GS1 prefix range | Yes — amended | pending | KB §4.1; claim unverified (NO-RESEARCH) |
| D-08 | Tax item classification | Amended: a Variant carries 0..n tax-classification codes, each tagged with a scheme. Schemes (for example ETA GS1 / EGS, others as TAX defines) are TAX-owned and held as `mdl` lookup types. CAT stores and TAX validates through a crossmodule API. This does not depend on the unverified claim of which scheme each country requires; TAX verifies that | Yes — amended | pending | KB §2, §5 #8; claim unverified (NO-RESEARCH) |
| D-09 | Item name languages | Amended: item names are stored per language. Arabic is mandatory, English optional, and further languages are allowed. Mandatory fields still fit G7's limit of 4. The per-country legal language rule for e-invoices is verified in the TAX stage | Yes — amended | pending | KB §1, §6.1; claim unverified (NO-RESEARCH) |
| D-10 | Customer identity | Accepted: given `sec` owns credentials and the CUSTOMER realm. PTY owns the commercial customer and links to the user by id (SOFT-READ). A guest is promoted by creating the link after the first order | Yes — accepted | pending | KB §3 D5, §5 #4; manifest `sec` |
| D-11 | Storefront tenant resolver | Accepted: STF owns the host→tenant resolver. Given `tenant` owns the context it feeds. In `single` mode it returns the default tenant | Yes — accepted | pending | KB §3 D4; manifest `tenant` |
| D-12 | Catalog media | Accepted: binaries live in the given `file` public image store. CAT owns ordering, primary image and alt text per Item/Variant | Yes — accepted | pending | KB §5 #1; manifest `file` |

## 9. RESEARCH LOG

Web research was requested in both rounds and denied both times (search and page fetch). Rows below are therefore internal sources or `NO-RESEARCH`. No decision in block 8 relies on a `NO-RESEARCH` claim as fact.

```yaml name=research-log
rows:
  - {point: "Platform scope, modules, fixed decisions", finding: "Approved brief fixes retail & wholesale multi-activity scope, 14-module DAG, decisions D1-D10, six capability flags and per-activity presets", source: {title: "Platform Brief — Retail & Wholesale Commerce SaaS on erp-core", url_or_path: "profiles/commerce/knowledge/raw-idea.md", date: "2026-10-21"}, used_in: "1, 2, 3, 4, 5, 6, 7"}
  - {point: "Market drivers", finding: "Trade is 54.4% of Egypt's establishments and 30.7% of Saudi SMEs; mandatory e-invoicing (ZATCA phase 2, Egypt B2C e-receipt from Sept 2025) drives adoption", source: {title: "Platform Brief §2", url_or_path: "profiles/commerce/knowledge/raw-idea.md", date: "2026-10-21"}, used_in: "2, 5 G15"}
  - {point: "Given capability match (D-03, D-05, D-10, D-11, D-12)", finding: "erp-core 1.5.0 provides tenant, sec, cu, mdl, file, sequence, events, audit, notif, report, module, common; it lists no custom-attributes, lot, dual-quantity, cash & bank or tax-adapter capability", source: {title: "erp-core 1.5.0 core manifest (as embedded in the domain-profile engine §1.5)", url_or_path: "deps/erp-core/core-manifest.yaml", date: "2026-10-22"}, used_in: "4, 6, 8"}
  - {point: "Item / Variant decomposition in established commerce systems (D-04)", finding: "Web research denied. Unverified belief that mature platforms keep a parent product with SKU-keyed variants and a default variant. The decision rests on KB §6.2/§6.8 instead", source: NO-RESEARCH, used_in: "8 D-04, 5 G16"}
  - {point: "Egypt ETA item classification codes (D-08)", finding: "Web research denied. Unverified belief that ETA e-invoice lines need a GS1 or EGS item code. The decision is scheme-agnostic; TAX stage must verify", source: NO-RESEARCH, used_in: "8 D-08, 5 G15"}
  - {point: "ZATCA invoice language (D-09)", finding: "Web research denied. Unverified belief that Saudi tax invoices need Arabic, other languages allowed alongside. TAX stage must verify", source: NO-RESEARCH, used_in: "8 D-09, 5 G15"}
  - {point: "Barcode standards for variable-measure items (D-07)", finding: "Web research denied. Unverified belief that GS1 reserves restricted-circulation prefixes for in-store weighed/priced items. No prefix is hard-coded", source: NO-RESEARCH, used_in: "8 D-07"}
  - {point: "Bundle / kit ownership patterns (D-06)", finding: "Web research denied. Unverified belief that systems differ between catalogue-level bundles and inventory-level kits. The decision follows the KB DAG (Catalog has no stock dependency)", source: NO-RESEARCH, used_in: "8 D-06"}
  - {point: "Feature-flag / preset ownership patterns (D-02)", finding: "Web research denied. No external comparison gathered. The decision follows KB §7 (one FeatureFlags API, preset reads only in settings)", source: NO-RESEARCH, used_in: "8 D-02"}
```

## 10. OPEN ITEMS

None. All twelve proposals are closed by decisions D-01…D-12 in the dialogue. Three claims stay unverified (country e-invoice classification scheme, e-invoice language rule, GS1 weighed-item prefixes). They are not open points: every decision is written so it holds whichever way they turn out, and verification is handed to the TAX stage and the CAT/SAL field-level stage.
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

---

DECISION: Module codes and bounded contexts follow one context per module, with the codes in 7.3
Accept P-01 option A. The brief's DAG already makes each module its own delivery and boundary unit (G5, G14). I added that module-to-module edges are HARD-FK for mandatory keys and SOFT-READ for crossmodule or event reads.
traces: KB §5, §7; block 7.2, 7.3; G5, G14

DECISION: A new level-0 module `SET` owns FeatureFlags, presets and the setup wizard
Amend P-02 option A. KB §7 allows preset reads only in settings, so there must be exactly one owner. I named it `SET` (context `configuration`). It persists through given `cu` and depends on no business module, so the fixed DAG order and delivery tiers are unchanged. I rejected putting it in Catalog (wrong context, and CAT would become everyone's dependency).
traces: KB §4.1, §6.9, §7; G1, G2, G3; block 4 row 15

DECISION: Seams the erp-core 1.5.0 manifest does not list are platform-own, with a re-match duty
Accept P-03 option A. The manifest lists no custom attributes, lot, dual-quantity, cash & bank or tax adapter, and assuming them would violate C-GIVEN-1. I added the duty to re-match them as EXISTING (given) when a later manifest provides them.
traces: KB §4.1, §5; C-GIVEN-1, C-GIVEN-3; block 4 rows 1, 3, 7, 8

DECISION: Every Item has at least one Variant, and stock, price, barcode and tax-classification attach to the Variant
Accept P-04, but narrowed the justification. No industry source could be fetched, so the decision rests on KB §6.2 and §6.8 (turning `variants` on needs no migration), not on a Shopify or Odoo comparison. It is recorded as rule G16.
traces: KB §4.1, §6.2, §6.8; G3, G8, G16

DECISION: Category is a CAT-owned tree, and brand and unit of measure are given `mdl` lookups
Amend P-05. The storefront needs hierarchy, ordering and publish state, so Category stays in CAT, which also makes the `mdl` hierarchy question moot. I added that per-item unit conversions are CAT-owned, because a lookup value holds a code and not a conversion factor.
traces: KB §5 #1; manifest `mdl`; G6

DECISION: CAT owns bundle composition and INV/SAL explode it
Accept P-06. Catalog has no stock dependency in the DAG, and the storefront can show a bundle without reading inventory. Bundle stock is derived from components. Nesting is a field-level rule handed to the CAT requirements stage.
traces: KB §4.1, §5; G5

DECISION: A Variant carries zero or more typed barcodes, and no GS1 prefix range is hard-coded
Amend P-07. The kind (public GTIN or internal) is recorded. The layout of weighed-item codes is a field-level matter for a later stage, because the GS1 restricted-circulation claim could not be verified.
traces: KB §4.1; G2; block 9 (NO-RESEARCH)

DECISION: A Variant carries tax-classification codes tagged by scheme, with schemes owned by TAX
Amend P-08. Round 1 named ETA GS1/EGS directly, but that is unverified, so the decision is scheme-agnostic: CAT stores the codes, and TAX defines the schemes as `mdl` lookups and validates them through a crossmodule API. This avoids the reverse TAX→CAT dependency the opus proposal rejected. Verification of per-country requirements is handed to the TAX stage.
traces: KB §2, §5 #8; G5, G15; block 9 (NO-RESEARCH)

DECISION: Item names are stored per language, with Arabic mandatory and English optional
Amend P-09. The ZATCA Arabic requirement is unverified, so per-language storage is used (further languages allowed) rather than two fixed columns. Arabic-mandatory still fits G7's limit of 4 mandatory fields. The legal language rule per country is verified in the TAX stage.
traces: KB §1, §6.1; G7, G15; block 9 (NO-RESEARCH)

DECISION: Given `sec` owns credentials and PTY owns the commercial customer
Accept P-10. PTY links to the CUSTOMER-realm user by id (SOFT-READ). A guest is promoted by creating that link after the first order.
traces: KB §3 D5, §5 #4; G9; C-GIVEN-3

DECISION: STF owns the host-to-tenant resolver and given `tenant` owns the context
Accept P-11. The resolver feeds TenantContext and returns the default tenant in `single` mode.
traces: KB §3 D4; G4, G10

DECISION: Catalog media binaries live in given `file`, and CAT owns ordering, primary image and alt text
Accept P-12. This follows the brief's use of the public file store for Catalog and avoids rebuilding storage.
traces: KB §5 #1; manifest `file`; G6

<!-- CONVERGED -->