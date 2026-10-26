Round 2 accepts the round-1 proposal and closes it. Both refusal causes stay fixed: the manifest uses the artifact ids `module-registry` and `business-policies`, and the module registry indexes POL-CAT-001 to POL-CAT-009. One dangling note in the policies file, "Written as POL-CAT-010 once confirmed", is also removed. No policy ID was added, renumbered or removed.

DECISION: The CAT module registry indexes every POL-CAT id, and business-policies-cat stays the only home of policy text
  Reasoning : Option A is accepted as drafted.
  - Contract C3.7 (`registry-agree`, kind POL) needs each POL id present in the registry artifact.
  - Engine §4 makes `module-registry-{mod}.md` this stage's registry output, and §3.4 makes business-policies the source of truth for policy statements. Option A satisfies both, and option B would break the three-file output and C3.3.
  - Amendment: the index's "Next free policy id: 010" line stays, so a later version adds its rows in the same version that defines them.
  traces : POL-CAT-001, POL-CAT-002, POL-CAT-003, POL-CAT-004, POL-CAT-005, POL-CAT-006, POL-CAT-007, POL-CAT-008, POL-CAT-009

DECISION: Item-name language (OI-12) is a standard language rule applied by P0.5, so it gets no POL id
  Reasoning : The baseline note "Written as POL-CAT-010 once confirmed" left a promised id unwritten.
  - The rule restates the profile language policy (`ar, en`) plus the KSA tax-invoice language rule. Engine §3.4 says standard domain behaviour is applied by P0.5 from the knowledge sources and is not repeated as a client-specific POL.
  - The decision stays in the RESOLVED DECISIONS tables, and the sequence still continues from 010.
  traces : POL-CAT-001, POL-CAT-009

<<<FILE: governance-shared\analysis\modules\CAT\v2\P0\platform-summary.md>>>
# PLATFORM SUMMARY — Retail & Wholesale Commerce SaaS on erp-core
══════════════════════════════════════════════════════════════════
Profile : commerce   Domain profile : v1   Registry : v1.0.0
Given   : erp-core 1.6.0 (re-matched in this version; v1 matched 1.5.0)
Run     : module CAT · version 2 · change set CS-CAT-001 (ADDITIVE) · dialogue round 2 (claude:sonnet)
══════════════════════════════════════════════════════════════════

## OVERVIEW
The platform is a multi-tenant SaaS for small and medium retail and wholesale traders in Egypt and the Gulf. It combines the back-office (purchasing, inventory, sales, receivables and payables, accounting, tax and e-invoicing), an offline-capable point of sale and an integrated storefront. All three channels share one catalogue, one stock and one customer base [KB:raw-idea.md §1]. Mandatory e-invoicing (ZATCA phase 2, Egypt's B2C e-receipt) drives adoption, so tax compliance must be part of the core design [KB:raw-idea.md §2]. One codebase serves twelve activity presets. Behaviour depends only on six capability flags, never on the activity name [KB:raw-idea.md §3 D2, §4]. The platform is built on erp-core 1.6.0 and rebuilds nothing the core already provides. In 1.6.0 that includes the general ledger, so Accounting becomes a posting, cost-centre and budget layer on top of the given `gl` [deps/erp-core/core-manifest.yaml].

## WHAT CHANGED IN v2 (delta against the erp-core 1.5.0 match)
| Change | Effect | Source |
|---|---|---|
| erp-core 1.6.0 adds the module `gl` (chart-of-accounts, fiscal-periods, journal-entries, trial-balance, ledger-currencies) | New given row 0.13. ACC stays NEW but no longer owns GL account, journal entry or accounting period; it posts through `GlPostingApi` | manifest §1.5; DP §9 research-log (1.5.0 module set) |
| The seams the brief assumes (custom attributes, `lot_id`, dual-quantity columns, cash & bank, tax adapter) are still absent in 1.6.0 | Platform-own: CAT (variant attributes), INV (lot, dual quantity), STL (cash & bank), TAX (tax adapter) | manifest §1.5; KB §4.1, §5 |
| Module codes follow `profile.vocabulary.module_prefixes` | SLS, SWR and SFR are binding. The domain-profile spellings SAL, SRW and STF are retired | registry §2.2 |
| The capability-flag and activity-preset store is matched to given `cu` (+ `tenant` public-app-config) | No new module code. Registry row 15 becomes a given use | KB header, §4.1, §6.2; manifest `cu`, `tenant` |

## MODULES
| #   | Code | Module (en / ar) | Bounded context | Layer | Type | Status | Note |
|-----|------|------------------|-----------------|-------|------|--------|------|
| 1.1 | CAT | Catalog / الكتالوج | catalog | L1 | master data | NEW | Module of this run. No prior module-registry-cat in the current state |
| 1.2 | PTY | Party / الأطراف | party | L1 | master data | NEW | Credentials and CUSTOMER realm are given `sec` (OI-13) |
| 2.1 | PRC | Pricing / التسعير | pricing | L2 | engine | NEW | Loyalty is an advanced tier |
| 2.2 | INV | Inventory / المخزون | inventory | L2 | engine | NEW | Lot and dual quantity are platform-own (OI-06) |
| 3.1 | PUR | Purchasing / المشتريات | purchasing | L3 | transactional | NEW | |
| 3.2 | SLS | Sales & POS / المبيعات ونقطة البيع | sales | L3 | transactional | NEW | POS mode is offline-capable |
| 4.1 | STL | Settlements / التسويات | settlements | L3 | transactional | NEW | Cash & bank are platform-own (OI-06) |
| 4.2 | TAX | Tax & E-invoicing / الضرائب والفوترة الإلكترونية | tax | L3 | engine | NEW | Tax adapter is platform-own (OI-06). Reads the CAT item tax class (OI-11) |
| 4.3 | SWR | SerialWarranty / الأرقام التسلسلية والضمان | after-sales | L3 | transactional | NEW | Flag `serialTracking` (+ `warranty`) |
| 4.4 | SHP | Shipping / الشحن | shipping | L3 | transactional | NEW | Carrier adapters are an advanced tier |
| 5.1 | ACC | Accounting / المحاسبة | accounting | L3 | engine | NEW | Partial given hit: the ledger is `erp-core:modules.gl`. ACC owns posting rules, cost centres, budgets and the accounting-mode reveal |
| 5.2 | APR | Approvals / الموافقات | approvals | L3 | engine | NEW | Lightweight. No BPM engine (D6) |
| 5.3 | SFR | Storefront / المتجر الإلكتروني | storefront | L4 | transactional | NEW | Host→tenant resolver feeds the given TenantContext (OI-14) |
| 6.1 | INS | Insights / التحليلات | insights | L4 | reporting | NEW | Report engine and printing are given `report` |
| 0.1 | — | Tenancy, tenant context, public app-config / المستأجرون وسياق المستأجر | — | L0 | reference | EXISTING (given) | erp-core:modules.tenant |
| 0.2 | — | Users, roles, permissions, staff & customer auth / المستخدمون والأدوار والصلاحيات | — | L0 | reference | EXISTING (given) | erp-core:modules.sec |
| 0.3 | — | Settings; capability-flag and activity-preset store / الإعدادات ومخزن مفاتيح القدرة والقوالب | — | L0 | reference | EXISTING (given) | erp-core:modules.cu |
| 0.4 | — | Lookups / القوائم المرجعية | — | L0 | reference | EXISTING (given) | erp-core:modules.mdl |
| 0.5 | — | Files (public), images, attachments / الملفات والصور | — | L0 | reference | EXISTING (given) | erp-core:modules.file |
| 0.6 | — | Number series / تسلسل الأرقام | — | L0 | reference | EXISTING (given) | erp-core:modules.sequence |
| 0.7 | — | Domain events / الأحداث | — | L0 | reference | EXISTING (given) | erp-core:modules.events |
| 0.8 | — | Audit trail / سجل التدقيق | — | L0 | reference | EXISTING (given) | erp-core:modules.audit |
| 0.9 | — | Notifications / الإشعارات | — | L0 | reference | EXISTING (given) | erp-core:modules.notif |
| 0.10 | — | Report engine & printing (Jasper) / محرك التقارير والطباعة | — | L0 | reference | EXISTING (given) | erp-core:modules.report |
| 0.11 | — | Business-module catalogue & entitlements / كتالوج الوحدات والاستحقاقات | — | L0 | reference | EXISTING (given) | erp-core:modules.module |
| 0.12 | — | Base entity, validation, search, paging, idempotency, API envelope / الأساس المشترك | — | L0 | reference | EXISTING (given) | erp-core:modules.common |
| 0.13 | — | General ledger (GL) / دفتر الأستاذ العام | — | L0 | reference | EXISTING (given) | erp-core:modules.gl. New in 1.6.0 |

Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is) · EXISTING (given) (provided by the library; a row of GIVEN MODULES below names which; nothing produced).
Numbering: [tier].[sequence within tier]. The user requests Phase 2 by this number.
Name-collision check: the given `module` provides `module-catalog`, the catalogue of business modules and entitlements. It is not the item catalogue, so CAT is not a given hit.

## GIVEN MODULES
```yaml name=given-modules
rows:
  - {name: "Tenancy, tenant context, public app-config", given: erp-core, entry: "erp-core:modules.tenant", provides_used: [tenants, tenancy, tenant-context, tenant-provisioning, tenant-branding, tenant-logo, public-app-config]}
  - {name: "Users, roles, permissions, staff & customer auth (CUSTOMER realm)", given: erp-core, entry: "erp-core:modules.sec", provides_used: [users, roles, permissions, grants, staff-auth, customer-auth, customer-accounts, customer-default-roles, customer-invitations, login, jwt, sessions, password-reset, signup]}
  - {name: "Settings; capability-flag and activity-preset store", given: erp-core, entry: "erp-core:modules.cu", provides_used: [settings, configuration, app-configuration, platform-defaults, tenant-overrides]}
  - {name: "Lookups (brand, unit of measure, item tax class)", given: erp-core, entry: "erp-core:modules.mdl", provides_used: [lookups, lookup-types, lookup-values, code-lists, master-data]}
  - {name: "Files (public) for item media and attachments", given: erp-core, entry: "erp-core:modules.file", provides_used: [files, images, uploads, downloads, public-files, storage, attachments]}
  - {name: "Sequences (document and item numbers)", given: erp-core, entry: "erp-core:modules.sequence", provides_used: [number-series, numbering, document-numbers, sequences]}
  - {name: "Events", given: erp-core, entry: "erp-core:modules.events", provides_used: [events, domain-events, event-bus, async-listeners]}
  - {name: "Audit", given: erp-core, entry: "erp-core:modules.audit", provides_used: [audit, audit-events, audit-trail, change-history]}
  - {name: "Notifications", given: erp-core, entry: "erp-core:modules.notif", provides_used: [notifications, templates, channels, email, in-app, dispatch]}
  - {name: "Report engine + Jasper printing", given: erp-core, entry: "erp-core:modules.report", provides_used: [reports, exports, printing, csv-export, pdf-export, print-templates]}
  - {name: "Module entitlements (delivery tiers)", given: erp-core, entry: "erp-core:modules.module", provides_used: [business-modules, module-entitlements, module-catalog, home-cards]}
  - {name: "Base entity, validation, global search, paging, idempotency", given: erp-core, entry: "erp-core:modules.common", provides_used: [base-entity, audit-columns, optimistic-locking, service-result, error-codes, localized-errors, api-envelope, validation, search, paging, idempotency, owned-lookups]}
  - {name: "GL", given: erp-core, entry: "erp-core:modules.gl", provides_used: [chart-of-accounts, fiscal-periods, journal-entries, trial-balance, ledger-currencies]}
```
Elsewhere these rows are labelled `EXISTING (given: erp-core)`. No NEW module above restates any of them. The build order ignores them because the given is delivered before the platform exists.

## DEPENDENCIES
```yaml name=platform-dependencies
modules:
  CAT: {tier: 1, depends_on: []}
  PTY: {tier: 1, depends_on: []}
  PRC: {tier: 2, depends_on: [CAT]}
  INV: {tier: 2, depends_on: [CAT]}
  PUR: {tier: 3, depends_on: [CAT, INV, PTY]}
  SLS: {tier: 3, depends_on: [CAT, PRC, INV, PTY]}
  STL: {tier: 4, depends_on: [PTY, PUR, SLS]}
  TAX: {tier: 4, depends_on: [CAT, PUR, SLS]}
  SWR: {tier: 4, depends_on: [INV, SLS]}
  SHP: {tier: 4, depends_on: [SLS]}
  ACC: {tier: 5, depends_on: [PUR, SLS, STL, TAX]}
  APR: {tier: 5, depends_on: [INV, PUR, SLS, STL]}
  SFR: {tier: 5, depends_on: [CAT, PRC, INV, PTY, SLS, STL, SHP]}
  INS: {tier: 6, depends_on: [CAT, PTY, PRC, INV, PUR, SLS, STL, TAX, SWR, SHP, ACC, APR, SFR]}
```
The edges reproduce the fixed DAG [KB:raw-idea.md §5] with two explicit extensions:
- TAX → CAT, because TAX reads the item tax class and the e-invoice item code (OI-11).
- APR → INV, PUR, SLS, STL, which makes "any document module" concrete (OI-17).

Edge kinds follow DP §6 (OI-17): HARD-FK for reference edges; SOFT-READ (events or crossmodule queries) for ACC, APR, SFR and INS. The build order, waves and critical path are derived by `gov.py plan-order`.

## CAPABILITY FLAGS — owners and activity matrix
The flag × activity matrix is a P0 artifact [KB:raw-idea.md §7]. Each cell is traced to an EARS requirement in P1 and to a test in P4. Flags are read through the one `FeatureFlags` facade over given `cu` (OI-05).

| Flag | Owning module (semantics) | Also read by | Source |
|---|---|---|---|
| `variants` | CAT | PRC, INV, PUR, SLS, SFR | KB §4.1 |
| `bundles` | CAT | INV, SLS, PRC | KB §4.1; OI-09 |
| `serialTracking` (+ `warranty`) | SWR | INV, PUR, SLS | KB §4.1 |
| `lotExpiry` | INV | PUR, SLS, INS | KB §4.1 |
| `dualQuantity` | INV | PRC, PUR, SLS | KB §4.1 |
| `b2bCredit` | PTY (credit limit and terms) | PRC, STL, SLS | KB §4.1; OI-16 |

| Activity (preset) | variants | serial | lotExpiry | dualQty | bundles | b2b |
|---|---|---|---|---|---|---|
| Electronics & appliances, mobiles, computers | ○ | ✓ | | | ✓ | ✓ |
| Apparel & footwear | ✓ | | | | | ✓ |
| Household, cosmetics, stationery, toys, accessories | ○ | | | | ✓ | ✓ |
| Grocery & food | | | ✓ | ○ | | ✓ |
| Cleaning supplies, feed | | | ✓ | | | ✓ |
| Auto spare parts | | ○ | | | | ✓ |
| Power tools & hardware | | ✓ | | | ✓ | ✓ |
| Furniture & lighting | ✓ | | | | ✓ | |
| Sanitary & plumbing | ✓ | | | | | ✓ |
| Fabrics & furnishings | ✓ | | | ✓ (metre) | | ✓ |
| Building materials & steel | | | | ✓ | | ✓ |
| Produce / meat by weight | | | ✓ | ✓ | | |
✓ required · ○ optional [KB:raw-idea.md §4.2]. Launch priority: electronics & appliances → apparel → household/cosmetics, then grocery.

## DEFERRED (not in scope for this version)
| Item | Reason / activation trigger |
|---|---|
| Workflow (BPM) engine | Excluded; Approvals is lightweight [KB §3 D6, §9] |
| Native mobile apps | v1 is responsive web only [KB §9] |
| Marketplace (multi-vendor storefront) | Excluded [KB §9] |
| Report designer | Excluded [KB §9] |
| AI assistant | Deferred core module, advanced tier [KB §8] |
| Pharmacy, restaurant, gold, vehicle, manufacturing, contracting and services models | Different business models [KB §3 D1] |
| Multi-currency, cost centres/budgets, loyalty, carrier adapters | In scope but delivered in wave 4 (advanced) [KB §8]. The dimensions exist in the model from day one (G8) |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| OI-01 | Binding codes for Sales & POS, SerialWarranty, Storefront | `profile.vocabulary.module_prefixes` binds: SLS, SWR, SFR. The SAL, SRW and STF aliases are retired. All 14 codes are IN PROFILE | Round-1 recommendation; confirmed at convergence | registry §2.2; engine §2.1 detection table |
| OI-02 | Effect of erp-core 1.6.0 on the 1.5.0 match | Module set adds `gl`. ACC loses GL account, journal entry and accounting period (CAND-ACC-002, -003, -006 become given, no entity ID). The domain-profile needs a hand re-run to cite 1.6.0 and add the `gl` row | Round-1 recommendation; confirmed at convergence | manifest §1.5; DP header, §9; change-manifest summary |
| OI-03 | Earlier project-registry baseline | None exists in the generated current state. Registry 1.0.0 (bootstrap) is the baseline | Round-1 recommendation; confirmed at convergence | registry §1; brief inputs |
| OI-04 | Module codes and bounded contexts (P-01) | 14 profile codes; one bounded context per module as in DP §7.2 | Round-1 recommendation; confirmed at convergence | DP §7.2; KB §5 |
| OI-05 | Owner of FeatureFlags, activity presets, setup wizard (P-02) | No new module. Flags and presets are settings in given `cu`, exposed by `tenant` public-app-config. `FeatureFlags` is a thin read facade in the platform's shared kernel. Each module contributes the flag-off guard for its own flags. The wizard is a front-end shell whose steps call the owning modules | Round-1 recommendation; confirmed at convergence | KB header, §3 D2, §4.1, §6.2, §6.9; manifest `cu`, `tenant` |
| OI-06 | Seams the brief assumes (P-03) | Not provided by 1.6.0, so platform-own: variant attributes → CAT; lot, dual quantity → INV; cash & bank → STL; tax adapter → TAX | Round-1 recommendation; confirmed at convergence | manifest §1.5; KB §4.1, §5 |
| OI-13 | Customer identity split (P-10) | `sec` owns credentials, CUSTOMER-realm accounts and invitations. PTY owns the commercial Customer with a SOFT-READ link (`SecUserDirectoryApi`). Guest → account uses `SecCustomerInvitationApi` | Round-1 recommendation; confirmed at convergence | KB §3 D5, §5 #4; manifest `sec` |
| OI-14 | Storefront tenant resolver (P-11) | SFR owns the storefront domain mapping and the host→tenant resolver. The resolver feeds the given TenantContext (`TenantLookupApi`) and returns the default tenant in `single` mode | Round-1 recommendation; confirmed at convergence | KB §3 D4; manifest `tenant` |
| OI-16 | Owner of credit limit and terms | PTY owns them as party master data. STL reads them for exposure and ageing; SLS and PRC read them under `b2bCredit`. CAND-STL-003 is withdrawn | Round-1 recommendation; confirmed at convergence | KB §4.1, §5 #4, §5 #7 |
| OI-17 | Edge kinds | DP §6 kinds accepted. APR and TAX extensions as in DEPENDENCIES | Round-1 recommendation; confirmed at convergence | DP §6; KB §5 |
CAT-scoped decisions OI-07…OI-12 and OI-15, and the policy index of the CAT registry, are recorded in `module-registry-cat.md` and `business-policies-cat.md`.

## OPEN ITEMS
None. The platform scope is fully determined.

## NEXT STEP
14 modules, 0 exceptions, 13 given rows. Confirm, or state one change. Phase 2 for module 1.1 (CAT) follows in this run. To adjust, reply with a plain instruction; to start Phase 2 for another module, reply with its number.
<<<END FILE>>>

<<<FILE: governance-shared\analysis\modules\CAT\v2\P0\module-registry-cat.md>>>
## MODULE REGISTRY — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module Code    : CAT   (profile.vocabulary.module_prefixes)
Bounded context: catalog
Layer / Type   : L1 / master data     Execution tier : 1.1
Source         : NEW (no prior module-registry-cat in the generated current state)
Knowledge      : profiles/commerce/knowledge/raw-idea.md §1, §4, §5 #1, §6; domain-profile §3 #1, §4 #1, §7
Readiness      : READY (dialogue converged in round 2)
══════════════════════════════════════════════════════════════════

Boundary: what is sold. Items, variants, variant attributes, categories, bundles, barcodes, media and publish state. No price and no stock quantity [DP §7.2].

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity (ar/en) | Kind | PRIVATE / SHARED | Source |
|---|---|---|---|
| صنف / Item | master | SHARED (PRC, INV, PUR, SLS, TAX, SFR, INS) | DP §3 #1, §7.1; KB §5 #1. A bundle is an Item of the bundle kind (OI-09) |
| متغيّر الصنف / Variant | master | SHARED (PRC, INV, PUR, SLS, SWR, SFR) | OI-07: every Item has at least one Variant. Stock, price, barcode and e-invoice item code key on the Variant |
| خاصية المتغيّر / Variant attribute | master | PRIVATE | OI-06: platform-own (no custom-attributes capability in erp-core 1.6.0); KB §4.1 |
| قيمة خاصية المتغيّر / Variant attribute value | master | PRIVATE | OI-06; KB §4.1 (size, colour, …) |
| فئة / Category | master | SHARED (SFR, INS) | OI-08: CAT-owned hierarchy; KB §5 #1 |
| مكوّن الحزمة / Bundle component | master | SHARED (INV, SLS, PRC) | OI-09: CAT owns the composition; INV and SLS explode it to components |
| وسائط الصنف / Item media | master | PRIVATE | OI-15: ordering, primary image, alt text (ar/en), variant association. Binaries live in given `file` |
| باركود / Barcode | master | SHARED (SLS, PUR, INV) | OI-10: 0..n per Variant (GTIN or internal) |
| تحويل وحدات الصنف / Item unit conversion | master | SHARED (PUR, SLS, INV) | AUTO (below): the units an item is bought or sold in, converted to its base unit |

Not entities:
- Publish state is an attribute of Item, with values from the lookup `publish-state`.
- The e-invoice item code is an attribute of Variant, defaultable from the Item (OI-11).
- Brand, unit of measure and item tax class are lookup types in given `mdl` that CAT masters (LOOKUPS OWNED). They receive no entity ID.

LOOKUPS OWNED    (value lists this module masters; stored in given `erp-core:modules.mdl`)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| brand | Brand / علامة تجارية | — none named | OI-08; DP §7.1 |
| unit-of-measure | Unit of measure / وحدة القياس | — none named | OI-08; DP §7.1. Each value carries its e-invoice unit code (OI-11) |
| item-tax-class | Item tax class / الفئة الضريبية للصنف | — none named | OI-11: TAX maps class × country to rate and category |
| einvoice-item-code-type | E-invoice item code type / نوع رمز الصنف في الفاتورة الإلكترونية | — none named (standard values applied by P0.5 from the ETA source) | OI-11 |
| publish-state | Publish state / حالة النشر | — none named | DP §7.1; KB §5 #1 |
Rule (profile): all LOV values are runtime-loaded from the core lookup module (mdl), with no hardcoded enums in APIs or field specs. Activity presets and capability flags are core settings, read through FeatureFlags.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| — | None. CAT is a root module; every lookup it reads is CAT-owned in given `mdl` |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| FileDocument | given erp-core:modules.file | SOFT-READ (file id) | Item media binaries in the public image store |
| LookupType / LookupValue | given erp-core:modules.mdl | SOFT-READ | brand, unit-of-measure, item-tax-class, einvoice-item-code-type, publish-state |
| AppConfiguration | given erp-core:modules.cu | SOFT-READ (via FeatureFlags) | Flags `variants` and `bundles`; scale-barcode pattern (OI-10) |
| NumberSeries | given erp-core:modules.sequence | SOFT-READ | Automatic item codes |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| erp-core:modules.file | (given — no XM edge; the SRS declares the use in its `given-usage` block) | FileImageStoreApi (public item images), FileDocumentLookupApi |
| erp-core:modules.mdl | (given — no XM edge) | MdlLookupApi; owned lookup types (common `owned-lookups`) |
| erp-core:modules.cu | (given — no XM edge) | SettingsApi behind the FeatureFlags facade; CAT contributes the flag-off guard for `variants` and `bundles` |
| erp-core:modules.sequence | (given — no XM edge) | NumberSeriesApi (item codes) |
| erp-core:modules.events | (given — no XM edge) | DomainEventPublisher / OutboxPublisher (catalogue changes for PRC, INV, SFR, INS) |
| erp-core:modules.audit | (given — no XM edge) | AuditApi (catalogue change history) |
| erp-core:modules.sec | (given — no XM edge) | PermissionContributor (catalogue permissions for the Owner, Seller/Cashier and Warehouse presets) |
| erp-core:modules.report | (given — no XM edge) | PrintApi / PrintTemplateSource (barcode labels) |
| erp-core:modules.common | (given — no XM edge) | base-entity, validation, search (global search), paging, idempotency, api-envelope |
ROOT: YES (no business-module dependency; DAG #1 [KB §5])

POLICIES REGISTERED   (POL ids defined in `business-policies-cat.md`; the statements live there only — this index keeps the registry and the policies in agreement)
| Policy | Name (en / ar) | Pattern | Status |
|---|---|---|---|
| POL-CAT-001 | One catalogue for every channel / كتالوج واحد لكل القنوات | ubiquitous | CONFIRMED |
| POL-CAT-002 | Catalogue behaviour from capability flags only / سلوك الكتالوج من مفاتيح القدرة فقط | ubiquitous | CONFIRMED |
| POL-CAT-003 | Show only what is enabled / عرض المُفعَّل فقط | state | CONFIRMED |
| POL-CAT-004 | Turning a catalogue flag on / تشغيل مفتاح قدرة للكتالوج | event | CONFIRMED |
| POL-CAT-005 | Turning a catalogue flag off while data depends on it / إيقاف مفتاح قدرة تعتمد عليه بيانات | unwanted | CONFIRMED |
| POL-CAT-006 | Minimal item creation / إنشاء صنف بأقل المدخلات | ubiquitous | CONFIRMED |
| POL-CAT-007 | Item import in the setup wizard / استيراد الأصناف في معالج الإعداد | event | CONFIRMED |
| POL-CAT-008 | Publish state controls storefront visibility / حالة النشر تحكم الظهور في المتجر | state | CONFIRMED |
| POL-CAT-009 | Items in the one global search / الأصناف في البحث الشامل | ubiquitous | CONFIRMED |
Next free policy id: 010. Every new POL-CAT id is added to this index in the same version that defines it.

AUTO-DECISIONS
AUTO: Status NEW, artifacts written in full  FROM: engine §2.1 STEP 3 — no `current-module-registry-cat` in the generated current state  IF WRONG: supply the prior registry; a later round re-casts this file as EXTENDED and keeps its entries
AUTO: Variant attribute is a CAT master entity (with attribute values), not an mdl lookup type  FROM: STEP 3/4 — KB §4.1 (attributes drive variant generation per item); domain practice (Shopify product options, Odoo product attributes)  IF WRONG: model attributes as CAT-owned mdl lookup types; Variant attribute value becomes LookupValue
AUTO: Item unit conversion owned by CAT  FROM: STEP 4 — wholesale buys and sells by carton or piece [KB §1 "wholesale traders"]; dual quantity (count + weight) stays in INV [KB §4.1]  IF WRONG: one unit per item; drop the entity
AUTO: Item codes are numbered automatically by given `sequence`, so a code is never a mandatory entry  FROM: KB §6.1 (≤ 4 mandatory fields), §5 (sequences used); manifest `sequence`  IF WRONG: the code is a manual entry, defaulted from the series
AUTO: Barcode labels print through given `report` (PrintApi, Jasper)  FROM: STEP 4 retail practice; KB header (Jasper via core PrintApi)  IF WRONG: drop label printing from CAT; INS owns it
AUTO: CAT publishes catalogue-change events; it never reads a downstream module  FROM: KB §5 rules (downward only); DP §6  IF WRONG: consumers query a CAT crossmodule API instead of subscribing
AUTO: CAT holds no price and no stock quantity  FROM: DP §7.2 catalog boundary  IF WRONG: none (boundary is stated)
AUTO: Given `module` (`module-catalog`) is not a hit for CAT  FROM: engine §1.5 STEP 0 — `module-catalog` is the catalogue of business modules and entitlements, not of items  IF WRONG: re-match against erp-core:modules.module
AUTO: The module registry indexes every POL-CAT id (POLICIES REGISTERED); the policy text stays in business-policies-cat only  FROM: engine §4 (the module registry IS this stage's registry output); contract C3.7 registry-agree (business-policies ↔ module-registry, kind POL)  IF WRONG: none (settled by the dialogue DECISION on the policy index)

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| OI-06 | Variant attributes: core seam or platform-own (P-03, CAT part) | Platform-own in CAT. erp-core 1.6.0 lists no custom-attributes capability | Round-1 recommendation; confirmed at convergence | manifest §1.5; KB §4.1 |
| OI-07 | Item / Variant model (P-04) | Every Item has ≥ 1 Variant. A default Variant exists while `variants` is off. Stock, price, barcode and e-invoice item code key on the Variant. Turning `variants` on later needs no migration | Round-1 recommendation; confirmed at convergence | KB §4.1, §6.2, §6.8; Shopify ProductVariant (every product has ≥ 1 variant); Odoo product.template / product.product |
| OI-08 | Category, Brand, UoM placement (P-05) | Category is a CAT-owned hierarchy (mdl lists are flat). Brand and UoM are CAT-owned lookup types in given `mdl` (no entity ID) | Round-1 recommendation; confirmed at convergence | KB §5 #1; manifest `mdl`, `common` owned-lookups; profile LOV rule |
| OI-09 | Bundle ownership (P-06) | CAT owns the composition (Bundle component). A bundle is an Item of the bundle kind and holds no stock of its own; INV moves and SLS sells its components. Pre-assembled kits are out of scope (manufacturing). CAND-INV-008 is withdrawn | Round-1 recommendation; confirmed at convergence | KB §3 D1, §4.1; NetSuite Kit/Package vs Assembly items |
| OI-10 | Barcodes and weighed-item codes (P-07) | A Variant carries 0..n barcodes (GTIN-8/12/13/14 or internal). Items sold by a weight or measure unit carry a scale code. The tenant's scale-barcode pattern (GS1 restricted-circulation prefixes 20–29, embedded weight or price) is a `cu` setting. No new flag | Round-1 recommendation; confirmed at convergence | KB §4.2 (produce by weight); GS1 General Specifications (RCN prefixes 20–29) |
| OI-11 | Catalogue-side e-invoice obligations (P-08) | The Variant carries the e-invoice item code (type GS1 or EGS + code), defaulted from the Item. The Item carries an item tax class from the CAT-owned lookup. TAX maps class × country to rate and category (ZATCA S/Z/E/O). UoM values carry the e-invoice unit code. No CAT → TAX edge; TAX reads CAT | Round-1 recommendation; confirmed at convergence | KB §2, §5 #8; Egypt ETA e-invoice SDK (itemType GS1/EGS, unitType); ZATCA XML implementation standard (UNCL5305 categories); Adobe Commerce product tax class |
| OI-12 | Item name languages (P-09) | Arabic name mandatory, English name optional. The Arabic name may contain Latin brand or model text. Applied by P0.5 as a standard language rule; not a client-specific POL | Round-1 recommendation; confirmed at convergence | profile language policy `ar, en`; KSA VAT Implementing Regulations Art. 53 (tax invoice in Arabic, other languages alongside) |
| OI-15 | Catalogue media (P-12) | Binaries live in given `file` (public image store). CAT owns Item media: ordering, primary image, alt text (ar/en), variant association | Round-1 recommendation; confirmed at convergence | KB §5 #1 (files public); manifest `file` |
| POL-IDX | Policy index in the module registry | `POLICIES REGISTERED` lists every POL-CAT id; the policy text lives only in `business-policies-cat.md` (satisfies C3.7) | Round-1 proposal; accepted in round 2 | engine §3.4, §4; contract C3.7 |
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

<<<FILE: governance-shared\analysis\modules\CAT\v2\P0\business-policies-cat.md>>>
## BUSINESS POLICIES — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module   : CAT     Source of truth : user vision text (approved brief, profiles/commerce/knowledge/raw-idea.md) + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
Sequence : POL-CAT-001 … POL-CAT-009 (no earlier POL-CAT id in the generated current state; the stream starts here and continues from 010)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-CAT-001 — One catalogue for every channel / كتالوج واحد لكل القنوات
  Statement : The system shall maintain one catalogue that serves the back-office, the point of sale and the storefront.
  Statement (ar): يجب على النظام أن يحتفظ بكتالوج واحد يخدم الإدارة الخلفية ونقطة البيع والمتجر الإلكتروني.
  Pattern   : ubiquitous
  Trigger   : Create / Update
  Rationale : The differentiator is one product with one catalogue, one stock and one customer base.
  Source    : KB:raw-idea.md §1 "one catalogue, one stock, one customer base"; §2
  Status    : CONFIRMED

POL-CAT-002 — Catalogue behaviour from capability flags only / سلوك الكتالوج من مفاتيح القدرة فقط
  Statement : The system shall derive every activity-dependent catalogue behaviour from the capability flags `variants` and `bundles` only, never from the tenant's activity.
  Statement (ar): يجب على النظام أن يستمد كل سلوك للكتالوج يختلف باختلاف النشاط من مفتاحَي القدرة `variants` و`bundles` فقط، وألا يستمده أبدًا من النشاط التجاري للمستأجر.
  Pattern   : ubiquitous
  Trigger   : Create / Update / Display
  Rationale : One codebase serves every activity; an activity is only a preset that sets flags.
  Source    : KB:raw-idea.md §3 D2, §4.1, §7
  Status    : CONFIRMED

POL-CAT-003 — Show only what is enabled / عرض المُفعَّل فقط
  Statement : While a catalogue capability flag (`variants` or `bundles`) is off, the system shall hide the catalogue functions bound to that flag.
  Statement (ar): أثناء إيقاف مفتاح قدرة خاص بالكتالوج (`variants` أو `bundles`)، يجب على النظام أن يُخفي وظائف الكتالوج المرتبطة بذلك المفتاح.
  Pattern   : state
  Trigger   : Display
  Rationale : No dead menus; the UI shows only what is enabled.
  Source    : KB:raw-idea.md §4.1, §6.3
  Status    : CONFIRMED

POL-CAT-004 — Turning a catalogue flag on / تشغيل مفتاح قدرة للكتالوج
  Statement : When a catalogue capability flag is turned on, the system shall make the catalogue functions bound to it available to existing items without re-setup or data migration.
  Statement (ar): عند تشغيل مفتاح قدرة خاص بالكتالوج، يجب على النظام أن يتيح وظائف الكتالوج المرتبطة به للأصناف القائمة دون إعادة إعداد أو ترحيل بيانات.
  Pattern   : event
  Trigger   : Flag change (on)
  Rationale : Presets first, then customise; flags may be turned on at any time.
  Source    : KB:raw-idea.md §6.2, §6.8 "No data migration, no mode switch"
  Status    : CONFIRMED

POL-CAT-005 — Turning a catalogue flag off while data depends on it / إيقاف مفتاح قدرة تعتمد عليه بيانات
  Statement : If a user attempts to turn off a catalogue capability flag while catalogue data depends on it, then the system shall refuse the change and offer the user a next action.
  Statement (ar): إذا حاول مستخدم إيقاف مفتاح قدرة خاص بالكتالوج بينما توجد بيانات كتالوج تعتمد عليه، فيجب على النظام أن يرفض التغيير وأن يعرض على المستخدم الإجراء التالي.
  Pattern   : unwanted
  Trigger   : Flag change (off)
  Rationale : Turning a flag off is blocked while data depends on it, and no error is a dead end.
  Source    : KB:raw-idea.md §6.2, §6.5
  Status    : CONFIRMED

POL-CAT-006 — Minimal item creation / إنشاء صنف بأقل المدخلات
  Statement : The system shall let a user create an item by completing at most four mandatory entries.
  Statement (ar): يجب على النظام أن يتيح للمستخدم إنشاء صنف بإكمال أربعة مدخلات إلزامية على الأكثر.
  Pattern   : ubiquitous
  Trigger   : Create
  Rationale : Simplicity is a measurable requirement; setup plus the first invoice takes at most 30 minutes.
  Source    : KB:raw-idea.md §3 D9, §6.1, §6.10
  Status    : CONFIRMED

POL-CAT-007 — Item import in the setup wizard / استيراد الأصناف في معالج الإعداد
  Statement : When the owner reaches the item-import step of the setup wizard, the system shall import the tenant's items from an Excel file.
  Statement (ar): عند وصول المالك إلى خطوة استيراد الأصناف في معالج الإعداد، يجب على النظام أن يستورد أصناف المستأجر من ملف Excel.
  Pattern   : event
  Trigger   : Import
  Rationale : The fifth setup step brings an existing item list in without re-typing it.
  Source    : KB:raw-idea.md §6.9
  Status    : CONFIRMED

POL-CAT-008 — Publish state controls storefront visibility / حالة النشر تحكم الظهور في المتجر
  Statement : While an item is unpublished, the system shall keep the item out of the storefront.
  Statement (ar): أثناء كون الصنف غير منشور، يجب على النظام أن يُبقي الصنف خارج المتجر الإلكتروني.
  Pattern   : state
  Trigger   : Publish / Unpublish
  Rationale : The catalogue is shared with the storefront, so the trader decides what is offered online.
  Source    : KB:raw-idea.md §5 #1 "publish state"
  Status    : CONFIRMED

POL-CAT-009 — Items in the one global search / الأصناف في البحث الشامل
  Statement : The system shall make every item findable through the one global search.
  Statement (ar): يجب على النظام أن يجعل كل صنف قابلًا للعثور عليه من خلال البحث الشامل الموحّد.
  Pattern   : ubiquitous
  Trigger   : Search
  Rationale : One global search covers items, customers and documents.
  Source    : KB:raw-idea.md §6.6
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
None. Standard values apply.

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Pre-assembled kits (bill of materials, assembly into stock) | Bundles are sold as one line and stock moves on their components; nothing is assembled into stock | A decision to bring manufacturing into scope | KB §3 D1, §9; OI-09 |
| Activity-specific catalogue models (pharmacy drug data, restaurant menus/recipes, gold karat pricing, vehicle VIN) | Not modelled in the catalogue | A new activity enters scope (one matrix column + one conformance scenario) | KB §3 D1, §7 |
| Multi-vendor catalogue (marketplace) | One trader's catalogue per tenant | Marketplace enters scope | KB §9 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| OI-05 | Where are the flags POL-CAT-002…005 read from | The FeatureFlags facade over given `cu`. CAT contributes the flag-off guard for `variants` and `bundles` | Round-1 recommendation; confirmed at convergence | KB §4.1, §6.2; manifest `cu` |
| OI-12 | Item name languages | Arabic name mandatory, English optional. A standard language rule applied by P0.5 from the profile language policy, so it is not written as a POL | Round-1 recommendation; confirmed at convergence | profile `ar, en`; KSA VAT Implementing Regulations Art. 53 |
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

<<<FILE: governance-shared\analysis\modules\CAT\v2\change-manifest.md>>>
# CHANGE MANIFEST — CAT v2 (baseline v1)

```yaml name=change-manifest
change_set: CS-CAT-001
type: ADDITIVE
summary: 'given erp-core upgraded 1.5.0 → 1.6.0: re-run from `P0` (the first stage
  that matches candidates against the given) through `P1`, `P1.5`, `P2`, `P0` (the
  stages the C-GIVEN-1, C-GIVEN-2, C-GIVEN-3 clauses read); `domain-profile` is platform-level
  — re-run it by hand when the given''s module set changed'
artifacts:
  project-registry:
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  platform-summary:          # owns no IDs; written in full (no prior platform-summary in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  module-registry:           # file module-registry-cat.md; owns no IDs (indexes the POL-CAT ids of business-policies); written in full (no prior module registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  business-policies:         # file business-policies-cat.md
    ADDED: [POL-CAT-001, POL-CAT-002, POL-CAT-003, POL-CAT-004, POL-CAT-005, POL-CAT-006, POL-CAT-007, POL-CAT-008, POL-CAT-009]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
```

P0 delta note: the given's module set changed (1.6.0 adds `gl`), so the domain-profile hand re-run named in the summary applies (OI-02).
<<<END FILE>>>

<!-- CONVERGED -->