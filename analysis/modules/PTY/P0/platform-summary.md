# PLATFORM SUMMARY — Retail & Wholesale Commerce SaaS on erp-core
══════════════════════════════════════════════════════════════════
Profile : commerce   Domain profile : v1   Registry : v1.0.0
Given   : erp-core 1.6.0 (manifest `deps/erp-core/core-manifest.yaml`) — matched first (STEP 0)
Run     : P0 · module PTY · v1 · dialogue round 2 (claude:sonnet)
══════════════════════════════════════════════════════════════════

## OVERVIEW
A multi-tenant SaaS for small and medium retail and wholesale traders in Egypt and the Gulf. One product covers the back-office (purchasing, inventory, sales, receivables/payables, accounting, tax & e-invoicing), an offline-capable point of sale and an integrated storefront, all on **one catalogue, one stock and one customer base** [KB:raw-idea.md §1]. Mandatory e-invoicing (ZATCA phase 2, Egypt's B2C e-receipt from Sept 2025) drives adoption [KB:raw-idea.md §2]. Behaviour varies by trading activity only through six capability flags set by activity presets, never by the activity name [KB:raw-idea.md §3 D2, §4]. Everything `erp-core` 1.6.0 provides is used, not rebuilt: tenancy, users and the customer realm, settings, lookups, files, numbering, events, audit, notifications, printing, module entitlements and, new in 1.6.0, the general ledger. The 14 business modules form a downward-only graph whose two roots are Catalog (items) and Party (customers and suppliers) [KB:raw-idea.md §5].

## MODULES
| #   | Code | Module (en / ar) | Bounded context | Layer | Type | Status |
|-----|------|------------------|-----------------|-------|------|--------|
| 1.1 | CAT | Catalog / الكتالوج | catalog | L1 | master data | EXISTING |
| 1.2 | PTY | Party / الأطراف | party | L1 | master data | NEW |
| 2.1 | PRC | Pricing / التسعير | pricing | L2 | engine | NEW |
| 2.2 | INV | Inventory / المخزون | inventory | L2 | transactional | NEW |
| 3.1 | PUR | Purchasing / المشتريات | purchasing | L3 | transactional | NEW |
| 3.2 | SLS | Sales & POS / المبيعات ونقطة البيع | sales | L3 | transactional | NEW |
| 4.1 | STL | Settlements / التسويات | settlements | L3 | transactional | NEW |
| 4.2 | TAX | Tax & E-invoicing / الضرائب والفوترة الإلكترونية | tax | L3 | engine | NEW |
| 4.3 | SWR | SerialWarranty / الأرقام التسلسلية والضمان | after-sales | L3 | transactional | NEW |
| 4.4 | SHP | Shipping / الشحن | shipping | L3 | transactional | NEW |
| 5.1 | ACC | Accounting / المحاسبة | accounting | L4 | engine | NEW |
| 5.2 | APR | Approvals / الموافقات | approvals | L4 | engine | NEW |
| 5.3 | SFR | Storefront / المتجر الإلكتروني | storefront | L4 | transactional | NEW |
| 6.1 | INS | Insights / التحليلات | insights | L4 | reporting | NEW |
| 0.1 | — | Tenancy, tenant context, branding, public app-config / المستأجرون | — | L0 | reference | EXISTING (given) |
| 0.2 | — | Users, roles, permissions, staff & customer auth, customer accounts / المستخدمون والصلاحيات | — | L0 | reference | EXISTING (given) |
| 0.3 | — | Settings, capability flags & activity presets / الإعدادات ومفاتيح القدرة والقوالب المسبقة | — | L0 | reference | EXISTING (given) |
| 0.4 | — | Lookups / القوائم المرجعية | — | L0 | reference | EXISTING (given) |
| 0.5 | — | Files, images, attachments / الملفات والصور | — | L0 | reference | EXISTING (given) |
| 0.6 | — | Number series / تسلسل الأرقام | — | L0 | engine | EXISTING (given) |
| 0.7 | — | Domain events / الأحداث | — | L0 | engine | EXISTING (given) |
| 0.8 | — | Audit trail / سجل التدقيق | — | L0 | engine | EXISTING (given) |
| 0.9 | — | Notifications / الإشعارات | — | L0 | engine | EXISTING (given) |
| 0.10 | — | Reports, exports, printing / التقارير والطباعة | — | L0 | reporting | EXISTING (given) |
| 0.11 | — | Business-module catalogue & entitlements / كتالوج الوحدات والاستحقاقات | — | L0 | reference | EXISTING (given) |
| 0.12 | — | Common base (base entity, validation, search, paging, idempotency) / الأساس المشترك | — | L0 | reference | EXISTING (given) |
| 0.13 | — | General ledger (chart of accounts, journals, fiscal periods, trial balance) / دفتر الأستاذ العام | — | L0 | engine | EXISTING (given) |

Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is) · EXISTING (given) (the library provides it; its GIVEN MODULES row below names the entry; nothing is produced)
Numbering: [tier].[sequence within tier]. Ask for Phase 2 of a module by its number.
Codes: the profile's `vocabulary.module_prefixes` are binding (SLS, SWR, SFR). The domain-profile aliases SAL, SRW and STF are retired (decision 1).
CAT is EXISTING: the project registry (§9) records a prior module registry and a v2 re-run in progress. This PTY run does not re-run its Phase 2.
Partial given hits: ACC uses `erp-core:modules.gl` for the ledger itself. STL uses `erp-core:modules.report` for payment-file export. PTY uses `erp-core:modules.sec` for the CUSTOMER-realm account. SFR feeds `erp-core:modules.tenant` with the host-to-tenant result. Each module keeps only what the given does not provide (decision 2).

## GIVEN MODULES — data, not prose (`gov.py analyze` reads this named block; C0.1 holds it to its schema)
```yaml name=given-modules
rows:
  - {name: "Tenancy, tenant context, branding, public app-config", given: erp-core, entry: "erp-core:modules.tenant", provides_used: [tenancy, tenant-context, tenant-provisioning, tenant-export, tenant-branding, public-app-config]}
  - {name: "Users, roles, permissions, staff & customer auth, customer accounts (CUSTOMER realm)", given: erp-core, entry: "erp-core:modules.sec", provides_used: [users, roles, permissions, grants, staff-auth, customer-auth, customer-accounts, customer-default-roles, customer-invitations, login, signup]}
  - {name: "Settings, capability flags and activity presets", given: erp-core, entry: "erp-core:modules.cu", provides_used: [settings, configuration, app-configuration, platform-defaults, tenant-overrides]}
  - {name: "Lookups (brand, unit of measure, customer group, address type, …)", given: erp-core, entry: "erp-core:modules.mdl", provides_used: [lookups, lookup-types, lookup-values, code-lists, master-data]}
  - {name: "Files, public images, attachments", given: erp-core, entry: "erp-core:modules.file", provides_used: [files, images, public-files, uploads, downloads, storage, attachments]}
  - {name: "Number series / document numbers", given: erp-core, entry: "erp-core:modules.sequence", provides_used: [number-series, document-numbers]}
  - {name: "Domain events / outbox", given: erp-core, entry: "erp-core:modules.events", provides_used: [domain-events, event-bus, async-listeners]}
  - {name: "Audit trail / change history", given: erp-core, entry: "erp-core:modules.audit", provides_used: [audit-trail, change-history]}
  - {name: "Notifications (email, in-app, templates)", given: erp-core, entry: "erp-core:modules.notif", provides_used: [notifications, templates, email, in-app, dispatch]}
  - {name: "Reports, exports, printing (Jasper), payment files", given: erp-core, entry: "erp-core:modules.report", provides_used: [reports, exports, printing, pdf-export, csv-export, print-templates, payment-file-export]}
  - {name: "Business-module catalogue & entitlements", given: erp-core, entry: "erp-core:modules.module", provides_used: [business-modules, module-entitlements, module-catalog, home-cards]}
  - {name: "Base entity, audit columns, locking, validation, search, paging, idempotency, API envelope", given: erp-core, entry: "erp-core:modules.common", provides_used: [base-entity, audit-columns, optimistic-locking, validation, search, paging, idempotency, api-envelope, owned-lookups]}
  - {name: "General ledger (GL, chart of accounts, journal, period close)", given: erp-core, entry: "erp-core:modules.gl", provides_used: [chart-of-accounts, fiscal-periods, journal-entries, trial-balance]}
```

## DEPENDENCIES — data, not prose (`gov.py graph` reads this named block; the build order is DERIVED from it; C0.1 holds it to its schema)
```yaml name=platform-dependencies
modules:
  CAT: {tier: 1, depends_on: []}
  PTY: {tier: 1, depends_on: []}
  PRC: {tier: 2, depends_on: [CAT, PTY]}          # PTY edge: reads the customer-group lookup only (SOFT-READ) — decision 8
  INV: {tier: 2, depends_on: [CAT]}
  PUR: {tier: 3, depends_on: [CAT, INV, PTY]}
  SLS: {tier: 3, depends_on: [CAT, PRC, INV, PTY]}
  STL: {tier: 4, depends_on: [PTY, PUR, SLS]}     # fills PTY's credit-exposure extension point — decision 6
  TAX: {tier: 4, depends_on: [PUR, SLS]}          # party data reaches TAX on the document snapshot — decision 10
  SWR: {tier: 4, depends_on: [INV, SLS]}
  SHP: {tier: 4, depends_on: [SLS]}               # delivery address reaches SHP on the order snapshot — decision 10
  ACC: {tier: 5, depends_on: [PUR, SLS, STL, TAX]}
  APR: {tier: 5, depends_on: [INV, PUR, SLS, STL]} # "any document module" [KB §5 #11]; APR's own P0 may extend
  SFR: {tier: 5, depends_on: [CAT, PRC, INV, PTY, SLS, STL, SHP]}
  INS: {tier: 6, depends_on: [CAT, PTY, PRC, INV, PUR, SLS, STL, TAX, SWR, SHP, ACC, APR, SFR]}
```
Each tier is the longest path from a root. The edges are the fixed graph of [KB:raw-idea.md §5], plus one PRC → PTY edge that reads a lookup (decision 8). No lower tier depends on a higher one: the credit-exposure extension point is declared by PTY and filled by STL, so the compile-time edge runs STL → PTY (decision 6). The build order is derived by `gov.py plan-order`, not written here.

## DEFERRED (not in scope for this version)
| Item | Reason / activation trigger |
|---|---|
| Workflow (BPM) engine | profile: `forbidden`; Approvals stays lightweight [KB §3 D6, §9] |
| Native mobile apps | v1 is responsive web only [KB §9]; trigger: a post-v1 version |
| Marketplace (multi-vendor in one storefront) | out of scope [KB §9]. A Supplier never sells in the storefront |
| Report designer | out of scope [KB §9]; Insights ships 15–20 standard reports |
| AI assistant | deferred core module, advanced tier [KB §8 #4] |
| Pharmacy, restaurant, gold, vehicles, manufacturing, contracting, services models | different business models [KB §3 D1] |
| Multi-currency (visible feature) | advanced tier [KB §8 #4]. The currency dimension exists in the model from day one, with its selector hidden while the count is 1 [KB §6.8] |
| Loyalty (PRC), cost centres & budgets (ACC), carrier adapters (SHP) | in scope but delivered in wave 4 "advanced" [KB §8 #4] |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Binding codes for Sales & POS, SerialWarranty, Storefront (OI-01, OI-04) | Profile codes SLS, SWR, SFR are binding; SAL, SRW, STF are retired aliases. Bounded contexts as DP §7.2 | Converged in dialogue (round 2); user confirms | registry §2.2, E2; engine §2.1 detection table; DP §7.2 |
| 2 | Re-match against erp-core 1.6.0 (OI-02, OI-06) | `erp-core:modules.gl` becomes a given. ACC keeps posting rules, cost centres, budgets and accounting mode. Custom attributes, lot, dual quantity, cash & bank and the tax adapter remain absent from 1.6.0 and stay platform-own (CAT / INV / STL / TAX) | Converged in dialogue (round 2); user confirms | engine §1.5 manifest table (1.6.0); DP §9 research-log; KB §4.1, §5 |
| 3 | Owner of capability flags, activity presets, setup wizard (OI-05) | No new module. Flags and presets are given `cu` settings read through one FeatureFlags read facade with no entities. Each wizard step belongs to the module whose data it sets; the wizard is a composition, not a module | Converged in dialogue (round 2); user confirms | profile rule (module-registry template); KB §4.1, §6.9; manifest `cu` |
| 8 | Price lists per customer group | PRC reads the PTY-owned customer-group lookup: one new PRC → PTY SOFT-READ edge (lookup only, never a Customer), tier unchanged | Converged in dialogue (round 2); user confirms | KB §4.1 (`b2bCredit` → price lists); DP §7.1 "Price list … for a channel or customer group" |
| 10 | HARD-FK / SOFT-READ kind of module edges (OI-17) | Edges stated in KB §5 are HARD-FK, except ACC, APR, SFR and INS, which are SOFT-READ (events / crossmodule reads) as DP §6. Party data reaches TAX and SHP on the document's party snapshot, with no direct edge | Converged in dialogue (round 2); user confirms | DP §6; KB §5 rules |

## OPEN ITEMS
None — platform scope fully determined.
Note for the orchestrator: domain-profile v1 §4 was matched against erp-core 1.5.0, and this summary matches 1.6.0, which adds `gl` (decision 2). The domain-profile delta records the change and replaces the retired aliases (decision 1). Nothing is carried as an unanswered point.

## NEXT STEP
14 modules (13 NEW, 1 EXISTING), 0 exceptions, 13 given rows. Confirm, or state one change. To start Phase 2, reply with an instruction to adjust or with a module number. Module 1.2 (PTY) is produced in this run.
