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
