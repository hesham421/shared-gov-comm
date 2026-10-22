I couldn't run web research: both search calls were denied permission. Every claim below that relies on outside knowledge is therefore logged as `NO-RESEARCH` and marked unverified, never as a sourced fact. The only primary source cited is the knowledge file `profiles/commerce/knowledge/raw-idea.md`.

The orchestrator listed the `raw-idea` input as MISSING. The knowledge file has that name and is the approved platform brief, so I used it as the raw idea and noted this in the file header.

<<<FILE: governance-shared/analysis/domain/domain-profile.md>>>
# DOMAIN PROFILE — Retail & Wholesale Commerce SaaS on erp-core
══════════════════════════════════════════════════════════════════
Profile         : commerce (COMMERCE)
Version         : 1            (per shared/VERSIONING.md)
Last Updated    : 2026-10-22
Status          : FRESH — dialogue round 1 (claude:opus draft; proposals P-01…P-12 open in-dialogue, block 8)
Research        : 1 primary source cited (KB raw-idea.md); live web research unavailable this round — external rows are NO-RESEARCH (block 9)
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
1. **Catalog**: items, variants, media, categories, publish state.
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
15. **Activity capability flags & presets**: one `FeatureFlags` API, activity presets, 5-step setup wizard ([KB:raw-idea.md §4.1, §6.9]). Which module owns this is in-dialogue: P-02.

Capabilities the given `erp-core` 1.5.0 provides (tenancy, users/roles/customer auth, settings, lookups, files, numbering, events, audit, notifications, printing/reports, module entitlements) are **used, not owned** (block 4).

## 4. MAIN COMPONENTS

Module codes other than CAT are **PROPOSED** pending P-01. Bounded-context ids are PROPOSED pending P-01 (block 7.2).

| # | Component | Module code | Bounded context | Category (user-defined) | Core / extension | Notes |
|---|-----------|-------------|-----------------|--------------------------|------------------|-------|
| 1 | Catalog | CAT | catalog | Business — foundation | Core | Item, variants, media, categories, publish state. DAG #1, no business dependency. Variant attributes are platform-own unless a given provides them (P-03); item/variant model in P-04 |
| 2 | Pricing | PRC (PROPOSED) | pricing | Business | Core | Price lists, promotions, coupons, loyalty. Depends on Catalog. Loyalty is an advanced tier ([KB §8]) |
| 3 | Inventory | INV (PROPOSED) | inventory | Business | Core | Stock, reservations, transfers, counts, lot/expiry, dual quantity. Every stock move carries `warehouse_id` ([KB §6.8]). Lot/dual-quantity seams in P-03 |
| 4 | Party | PTY (PROPOSED) | party | Business — foundation | Core | Customers, suppliers, addresses, credit. Partial given hit: customer credentials/realm are `erp-core:modules.sec` (P-10) |
| 5 | Purchasing | PUR (PROPOSED) | purchasing | Business | Core | PO, goods receipt, supplier invoice |
| 6 | Sales & POS | SAL (PROPOSED) | sales | Business | Core | Quotation, order, invoice, returns/exchange; POS mode offline-capable |
| 7 | Settlements | STL (PROPOSED) | settlements | Business | Core | Collections, payments, AR/AP ageing, payment-gateway adapter. The "cash & bank seams" the brief assumes are not in the erp-core 1.5.0 manifest (P-03) |
| 8 | Tax & E-invoicing | TAX (PROPOSED) | tax | Business — compliance | Core | VAT, ETA/ZATCA/Oman adapters, tax returns. The "tax adapter" seam the brief assumes is not in the manifest (P-03) |
| 9 | Accounting | ACC (PROPOSED) | accounting | Business — finance | Core (hidden until accounting mode) | Auto-posting, GL, cost centres, budgets, period close |
| 10 | SerialWarranty | SRW (PROPOSED) | after-sales | Business | Extension — flag `serialTracking` (+ `warranty`) | Serials, warranty cards, service returns |
| 11 | Approvals | APR (PROPOSED) | approvals | Business — workflow | Extension — lightweight | No BPM engine (D6). Uses given events + notif |
| 12 | Shipping | SHP (PROPOSED) | shipping | Business — fulfilment | Core (carrier adapters advanced) | Delivery status, carrier adapters |
| 13 | Storefront | STF (PROPOSED) | storefront | Channel | Core | Public catalogue/search, guest cart, checkout, customer area. Partial given hit: tenant resolver feeds `erp-core:modules.tenant` TenantContext (P-11) |
| 14 | Insights | INS (PROPOSED) | insights | Analytics | Core | Dashboards, 15–20 standard reports, saved filters. Partial given hit: report/print engine is `erp-core:modules.report` |
| 15 | Activity flags & presets (FeatureFlags API, presets, setup wizard) | — owner in-dialogue (P-02) | — (P-02) | Platform — configuration | Core | Partial given hit: persisted through `erp-core:modules.cu` (SettingsApi). The flag/preset semantics are the platform's own |
| 16 | Tenancy, tenant context, branding, public app-config | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.tenant` (Tenant; TenantLookupApi, TenantProvisioningContributor, TenantPolicy). `GET /api/public/app-config` ([KB technical base]) |
| 17 | Users, roles, permissions, staff & customer auth, customer accounts, signup | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.sec` (User, Role, …; PermissionContributor, SecUserDirectoryApi). Role presets Owner / Seller-Cashier / Warehouse are configured on it (D8) |
| 18 | Settings / configuration / tenant overrides | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.cu` (AppConfiguration; SettingsApi) |
| 19 | Lookups / master data code lists | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.mdl` (LookupType, LookupValue; MdlLookupApi). Catalog's brand/UoM placement in P-05 |
| 20 | Files, images, public files, attachments | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.file` (FileDocument, FileCategory; FileImageStoreApi, FileDocumentLookupApi). Catalog media uses it (P-12) |
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
| G15 | E-invoicing compliance (ZATCA phase 2, Egypt ETA e-invoice/e-receipt, Oman) is a market driver and a Tax-module responsibility. Catalog-side obligations (item tax codes, Arabic names) are in-dialogue: P-08, P-09 | [KB:raw-idea.md §2, §5 #8]; external detail NO-RESEARCH (block 9) |

## 6. RELATIONSHIPS WITH OTHER DOMAINS

Business-module dependencies are taken from the fixed DAG [KB:raw-idea.md §5]. The HARD-FK / SOFT-READ kind for module-to-module edges is a recommendation (P-01 consequence) to be confirmed in-dialogue.

| This component | Depends on | Kind | Direction | Stated by |
|---|---|---|---|---|
| Catalog | erp-core file (public images) | SOFT-READ (file id via FileImageStoreApi) | erp-core:modules.file → Catalog | user [KB §5] |
| Catalog | erp-core mdl (lookups) | SOFT-READ (MdlLookupApi) | erp-core:modules.mdl → Catalog | user [KB §5] |
| Catalog | erp-core cu (settings / flags) | SOFT-READ (SettingsApi, via FeatureFlags) | erp-core:modules.cu → Catalog | user [KB §5, §4.1] |
| Pricing | Catalog | HARD-FK | Catalog → Pricing | user [KB §5] |
| Inventory | Catalog | HARD-FK | Catalog → Inventory | user [KB §5] |
| Inventory | erp-core sequence, events, audit | SOFT-READ (extension APIs) | erp-core → Inventory | user [KB §5] |
| Party | erp-core sec (customer realm), cu | SOFT-READ | erp-core → Party | user [KB §5] |
| Purchasing | Catalog, Inventory, Party | HARD-FK | owners → Purchasing | user [KB §5] |
| Sales & POS | Catalog, Pricing, Inventory, Party | HARD-FK | owners → Sales & POS | user [KB §5] |
| Settlements | Party, Purchasing, Sales & POS | HARD-FK | owners → Settlements | user [KB §5] |
| Tax & E-invoicing | Purchasing, Sales & POS | HARD-FK | owners → Tax | user [KB §5] |
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

The terms come from the brief [KB:raw-idea.md]. Terms marked † depend on an in-dialogue proposal and become binding when its DECISION lands.

| Term (en / ar) | Definition | Do not say | Module code |
|---|---|---|---|
| Item / صنف | A thing the trader buys, stocks or sells. It is the catalogue record that groups its variants | product (in code/UI labels), article, material, مادة, منتج (as entity name) | CAT |
| Variant / متغيّر الصنف † | A concrete sellable and stockable version of an Item (for example size × colour). It is the unit that stock, price and barcode attach to (P-04) | SKU (as entity name), child item, option, sub-item, صنف فرعي | CAT |
| Variant attribute / خاصية المتغيّر † | A dimension that variants differ by (size, colour, …) | option, property, characteristic | CAT |
| Category / فئة † | A node in the tenant's hierarchical item classification used for browsing and reporting (P-05) | group, family, department, مجموعة | CAT |
| Brand / علامة تجارية † | The maker or brand label of an item, held as a lookup (P-05) | manufacturer (unless distinct), make | CAT (via mdl) |
| Unit of measure / وحدة القياس † | The unit an item is counted, weighed or measured in (P-05) | UOM code, unit type | CAT (via mdl) |
| Barcode / باركود † | A scannable code (GTIN or internal) identifying a Variant (P-07) | item code, SKU | CAT |
| Bundle / حزمة | A composite item, kit or promo pack sold as one line and made of component items (flag `bundles`) | combo, package, composite (as entity name), طقم | CAT |
| Media / وسائط | The images and files attached to an item for display (P-12) | gallery, pictures, assets | CAT |
| Publish state / حالة النشر | Whether an item is visible in the storefront | status (unqualified), active flag, visibility | CAT |
| Capability flag / مفتاح القدرة | One of six switches (`variants`, `serialTracking`, `lotExpiry`, `dualQuantity`, `bundles`, `b2bCredit`) that business logic reads | feature toggle (in domain text), mode, activity type | P-02 |
| Activity / النشاط التجاري | The trading activity a tenant declares (for example apparel). It is never read by business logic | business type, sector, vertical (in code) | P-02 |
| Activity preset / القالب المسبق للنشاط | A named set of flag values and default settings applied when an activity is chosen | template, profile, package | P-02 |
| Dual quantity / الكمية المزدوجة | Counting an item in two quantities (count + weight or length), priced by either | secondary unit, catch weight (in UI) | INV (PROPOSED) |
| Lot / التشغيلة | A batch of stock received together, carrying an expiry date (flag `lotExpiry`) | batch (in UI), دفعة | INV (PROPOSED) |
| Serial number / الرقم التسلسلي | The unique identifier of one physical unit (flag `serialTracking`) | IMEI (except as a kind of serial), serial code | SRW (PROPOSED) |
| Price list / قائمة الأسعار | A set of variant prices for a channel or customer group | tariff, rate card | PRC (PROPOSED) |
| Warehouse / المستودع | A physical stock location. Present from day one, with the selector hidden while there is only one | store (ambiguous with storefront), depot, مخزن | INV (PROPOSED) |
| Customer / العميل | A party that buys. May be linked to a CUSTOMER-realm account | client, buyer, زبون | PTY (PROPOSED) |
| Supplier / المورّد | A party the trader buys from | vendor, provider | PTY (PROPOSED) |
| Guest / الزائر | A storefront shopper who checks out without an account. They become a Customer account after their first order | anonymous user, visitor account | STF (PROPOSED) |
| Storefront / المتجر الإلكتروني | The tenant's public online shop, resolved by subdomain or custom domain | webshop, e-shop, site | STF (PROPOSED) |
| Point of sale (POS) / نقطة البيع | The cashier selling mode of Sales & POS, offline-capable | till, checkout (reserved for storefront) | SAL (PROPOSED) |
| Tenant / المستأجر | One trader's isolated data space on the platform (given: erp-core) | company (as tenancy term), shop, account | — (given) |

### 7.2 Bounded contexts

The profile's own `bounded_contexts` list was not provided in this brief. The contexts below are PROPOSED with one context per business module (P-01).

| Context | Owns module codes | Boundary statement |
|---|---|---|
| catalog | CAT | What is sold: items, variants, attributes, categories, bundles, media, publish state. No price, no stock quantity |
| pricing | PRC | What it costs: price lists, promotions, coupons, loyalty |
| inventory | INV | Where it is and how much: stock, moves, reservations, transfers, counts, lots, dual quantity |
| party | PTY | Who: customers, suppliers, addresses, credit. Credentials belong to given sec |
| purchasing | PUR | Buying documents |
| sales | SAL | Selling documents and the POS mode |
| settlements | STL | Money in and out: collections, payments, ageing, gateway adapter |
| tax | TAX | Tax computation, e-invoice submission, returns |
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
| PRC | Pricing / التسعير | PROPOSED (P-01) — carried into `P-1` as RESERVED |
| INV | Inventory / المخزون | PROPOSED (P-01) — RESERVED |
| PTY | Party / الأطراف | PROPOSED (P-01) — RESERVED |
| PUR | Purchasing / المشتريات | PROPOSED (P-01) — RESERVED |
| SAL | Sales & POS / المبيعات ونقطة البيع | PROPOSED (P-01) — RESERVED |
| STL | Settlements / التسويات | PROPOSED (P-01) — RESERVED |
| TAX | Tax & E-invoicing / الضرائب والفوترة الإلكترونية | PROPOSED (P-01) — RESERVED |
| ACC | Accounting / المحاسبة | PROPOSED (P-01) — RESERVED |
| SRW | SerialWarranty / الأرقام التسلسلية والضمان | PROPOSED (P-01) — RESERVED |
| APR | Approvals / الموافقات | PROPOSED (P-01) — RESERVED |
| SHP | Shipping / الشحن | PROPOSED (P-01) — RESERVED |
| STF | Storefront / المتجر الإلكتروني | PROPOSED (P-01) — RESERVED |
| INS | Insights / التحليلات | PROPOSED (P-01) — RESERVED |

No code is proposed for the given rows (block 4 rows 16–27), because they are used, not built. A code for the activity-flags owner depends on P-02.

### 7.4 Identifier rules

Later stages build IDs as `{prefix}-{MOD}-{seq}` (seq width 3) using the module codes above. Entity kinds are `master, transactional, lookup, config, security`. This profile adds no domain-specific atom (`profile.ids.atoms`). Entities provided by erp-core (block 4 given rows) receive no entity ID from this platform [C:C-GIVEN-3].

### 7.5 Knowledge sources to cite

- `profiles/commerce/knowledge/raw-idea.md`
- No external research source has been accepted yet (web research unavailable in round 1; block 9).

## 8. RESOLVED DECISIONS

These are round-1 proposals, recommended by claude:opus. Round 2 (claude:sonnet) challenges them, and the user confirms. None is binding until its DECISION lands.

| # | Point | Decision | Recommended by dialogue? | Confirmed by user | Sources |
|---|---|---|---|---|---|
| P-01 | Module codes and bounded contexts for the 14 business modules | Rec.: codes in 7.3, one context per module (7.2) | Round 1 recommendation | pending | KB §5 |
| P-02 | Owner of FeatureFlags API, activity presets, setup wizard | Rec.: a small platform foundation module (code proposed in round 2, e.g. `SET`) built on given `cu` | Round 1 recommendation | pending | KB §4.1, §6.2, §6.9, §7; NO-RESEARCH |
| P-03 | Core seams the brief assumes but erp-core 1.5.0's manifest does not list (custom attributes, `lot_id`, dual-quantity columns, cash & bank, tax adapter) | Rec.: treat as platform-own in CAT / INV / STL / TAX. Re-match if a later erp-core manifest provides them | Round 1 recommendation | pending | KB §4.1, §5; manifest table §1.5 |
| P-04 | Item / Variant model | Rec.: every Item has ≥ 1 Variant (a default one when `variants` is off). Stock, price, barcode and tax code key on the Variant | Round 1 recommendation | pending | KB §4.1; NO-RESEARCH |
| P-05 | Categories, brands, units of measure | Rec.: Category is a CAT-owned hierarchy. Brand and UoM are given `mdl` lookup types | Round 1 recommendation | pending | KB §5; manifest `mdl`; NO-RESEARCH |
| P-06 | Bundle ownership | Rec.: CAT defines the bundle composition. INV/SAL explode it to components at movement/sale | Round 1 recommendation | pending | KB §4.1; NO-RESEARCH |
| P-07 | Barcodes and weighed-item codes | Rec.: a Variant carries 0..n barcodes (GTIN or internal). Weighed and variable-measure items use an internal price/weight-embedded code under `dualQuantity` | Round 1 recommendation | pending | KB §4.1; NO-RESEARCH |
| P-08 | Tax item classification on the catalogue | Rec.: a Variant carries the e-invoice item classification code (ETA GS1/EGS) and a tax-category reference owned by TAX | Round 1 recommendation | pending | KB §2, §5 #8; NO-RESEARCH |
| P-09 | Item name languages | Rec.: names are bilingual ar/en, with Arabic mandatory and English optional | Round 1 recommendation | pending | KB §1; NO-RESEARCH |
| P-10 | Customer identity split | Rec.: given `sec` owns credentials and the CUSTOMER realm. PTY owns the commercial customer and links to the user by SOFT-READ | Round 1 recommendation | pending | KB §3 D5, §5 #4; manifest `sec` |
| P-11 | Storefront tenant resolver | Rec.: the STF-owned host→tenant resolver feeds given `tenant` context (partial given hit) | Round 1 recommendation | pending | KB §3 D4; manifest `tenant` |
| P-12 | Catalog media | Rec.: binaries live in given `file` (public image store). CAT owns ordering, primary image and alt text per Item/Variant | Round 1 recommendation | pending | KB §5 #1; manifest `file` |

## 9. RESEARCH LOG

```yaml name=research-log
rows:
  - {point: "Platform scope, modules, fixed decisions", finding: "Approved brief fixes retail & wholesale multi-activity scope, 14-module DAG, decisions D1-D10, six capability flags and per-activity presets", source: {title: "Platform Brief — Retail & Wholesale Commerce SaaS on erp-core", url_or_path: "profiles/commerce/knowledge/raw-idea.md", date: "2026-10-21"}, used_in: "1, 2, 3, 4, 5, 6, 7"}
  - {point: "Market drivers", finding: "Trade is 54.4% of Egypt's establishments and 30.7% of Saudi SMEs; mandatory e-invoicing (ZATCA phase 2, Egypt B2C e-receipt from Sept 2025) drives adoption", source: {title: "Platform Brief §2", url_or_path: "profiles/commerce/knowledge/raw-idea.md", date: "2026-10-21"}, used_in: "2, 5 G15"}
  - {point: "Given capability match (P-03, P-05, P-10, P-11, P-12)", finding: "erp-core 1.5.0 provides tenant, sec, cu, mdl, file, sequence, events, audit, notif, report, module, common; it lists no custom-attributes, lot, dual-quantity, cash & bank or tax-adapter capability", source: {title: "erp-core 1.5.0 core manifest (as embedded in the domain-profile engine §1.5)", url_or_path: "deps/erp-core/core-manifest.yaml", date: "2026-10-22"}, used_in: "4, 6, 8"}
  - {point: "Item / Variant decomposition in established commerce systems (P-04)", finding: "Unverified (web research denied this round): mature platforms are believed to model a parent product with sellable variants keyed by SKU, with a default variant when there are no options", source: NO-RESEARCH, used_in: "8 P-04"}
  - {point: "Egypt ETA item classification codes (P-08)", finding: "Unverified: ETA e-invoice lines are believed to require a GS1 or EGS item code per item", source: NO-RESEARCH, used_in: "8 P-08"}
  - {point: "ZATCA invoice language (P-09)", finding: "Unverified: Saudi tax invoices are believed to require Arabic, with other languages allowed alongside", source: NO-RESEARCH, used_in: "8 P-09"}
  - {point: "Barcode standards for variable-measure items (P-07)", finding: "Unverified: GS1 is believed to reserve restricted-circulation prefixes for in-store weighed/priced items", source: NO-RESEARCH, used_in: "8 P-07"}
  - {point: "Bundle / kit ownership patterns (P-06)", finding: "Unverified: back-office systems differ between catalogue-level bundles and inventory-level kits", source: NO-RESEARCH, used_in: "8 P-06"}
  - {point: "Feature-flag / preset ownership patterns (P-02)", finding: "Unverified: no external comparison gathered", source: NO-RESEARCH, used_in: "8 P-02"}
```

## 10. OPEN ITEMS

None outside the dialogue. Proposals P-01…P-12 (block 8) are in-dialogue and close by DECISION in later rounds. Nothing is deferred to an open-questions file.
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

---

## Round-1 proposals

External research was unavailable for all of these. Sources are the knowledge file plus the erp-core 1.5.0 manifest from the engine's §1.5. Each external claim is `NO-RESEARCH` and needs verification in round 2.

**PROPOSAL: P-01: Module codes and bounded contexts**
- **Question:** Which 3-letter codes and contexts do the 14 business modules carry?
- **Options:**
  - A) The mnemonic set in 7.3, one context per module. It's simple and lines up with the DAG.
  - B) Group them into fewer contexts (for example commerce = PRC+SAL+STF). That means fewer seams but blurrier ownership.
  - C) Defer the codes to `P-1`. This conflicts with 7.3, which needs codes now.
- **Recommended:** A. The KB §5 DAG already treats each module as a separate delivery and boundary unit (G5, G14).
- **Consequence:** These codes are reserved in `P-1`, and every ID prefix follows them.

**PROPOSAL: P-02: Who owns FeatureFlags, presets and the setup wizard**
- **Options:**
  - A) A new small platform foundation module (for example `SET`) on top of given `cu`. This puts one owner behind the ArchUnit rule "no preset read outside settings".
  - B) Raw `cu` settings plus a per-module helper. This scatters the flag semantics.
  - C) Put it in Catalog. That's wrong context, and CAT would become everyone's dependency.
- **Recommended:** A. KB §7 requires one `FeatureFlags` API and limits preset reads to settings, which needs exactly one owner. It sits at DAG level 0.
- **Consequence:** This adds one code. All modules SOFT-READ it.

**PROPOSAL: P-03: Core seams assumed by the brief but absent from the erp-core 1.5.0 manifest**
- **Finding:** KB §4.1 and §5 cite "custom attributes", "core `lot_id` + policy", "core dual-quantity columns", "cash & bank seams" and "tax adapter". The manifest's `provides` and entities contain none of these.
- **Options:**
  - A) Treat them as platform-own (CAT attributes, INV lot and dual quantity, STL cash & bank, TAX adapter).
  - B) Block until erp-core adds them.
  - C) Assume they exist. That violates C-GIVEN-1.
- **Recommended:** A, re-matched if a later manifest lists them. This keeps delivery order D10 intact.
- **Consequence:** These become CAT, INV, STL and TAX responsibilities. The owner should confirm that no newer erp-core is planned to provide them.

**PROPOSAL: P-04: Item/Variant model**
- **Recommended:** Every Item has at least one Variant (an implicit default when `variants` is off). Stock, price, barcode and tax code key on the Variant.
- **Why:** This makes G3 safe, because turning `variants` on later needs no migration (KB §6.2, §6.8).
- **Alternatives:**
  - B) Stock on the Item, and on the Variant only when the flag is on. This forces a data migration when the flag flips.
  - C) Separate item types.
- **Research:** NO-RESEARCH (believed to match the Shopify/Odoo pattern; to verify).

**PROPOSAL: P-05: Categories / brand / UoM**
- **Recommended:** Category is a CAT-owned tree, since storefront navigation needs hierarchy, ordering and publish state. Brand and UoM are given `mdl` lookup types.
- **Alternative:** Everything as mdl lookups. This is flat and probably can't hold a hierarchy (to verify against the `mdl` API docs).

**PROPOSAL: P-06: Bundles**
- **Recommended:** CAT owns the composition. INV and SAL explode it to components.
- **Alternative:** INV owns kits, but then the storefront has to read inventory to display a bundle.

**PROPOSAL: P-07: Barcodes**
- **Recommended:** A Variant has 0..n barcodes (GTIN or internal). Weighed items use embedded price/weight codes when `dualQuantity` is on.
- **Research:** NO-RESEARCH (GS1 restricted-circulation numbers to verify).

**PROPOSAL: P-08: Tax item classification**
- **Recommended:** A Variant carries the ETA GS1/EGS item code and a reference to a TAX-owned tax category.
- **Alternative:** TAX keeps an item→code map. That adds a reverse dependency and breaks the DAG.
- **Research:** NO-RESEARCH. This needs verification first, because it is the most consequential claim here.

**PROPOSAL: P-09: Item names**
- **Recommended:** Names are bilingual ar/en, with Arabic mandatory. This still fits G7's limit of 4 mandatory fields.
- **Research:** NO-RESEARCH (ZATCA Arabic requirement to verify).

**PROPOSAL: P-10: Customer identity**
- **Recommended:** Given `sec` owns credentials and the realm. PTY owns the commercial customer, linked by user id with SOFT-READ (D5).

**PROPOSAL: P-11: Storefront resolver**
- **Recommended:** STF owns the host→tenant resolver. Given `tenant` owns the context (G10).

**PROPOSAL: P-12: Media**
- **Recommended:** Given `file` public store holds the binaries. CAT owns ordering, primary image and alt text.

---

**Where this could be sharper**
- External research is empty this round. P-04, P-07, P-08 and P-09 each depend on unverified regulatory or industry claims. If web research can be approved for round 2, those four should be verified first.
- The profile's own `module_prefixes` and `bounded_contexts` weren't included in the brief, so everything except CAT is PROPOSED.
- The orchestrator reported `raw-idea` as MISSING. I treated the knowledge file of the same name as that input; this is worth confirming.