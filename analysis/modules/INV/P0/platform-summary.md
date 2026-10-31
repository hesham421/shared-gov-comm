# PLATFORM SUMMARY — Retail & Wholesale Commerce SaaS on erp-core
══════════════════════════════════════════════════════════════════
Profile : commerce   Domain profile : v1   Registry : v1.0.0   Given : erp-core 1.6.0
Run module : INV (Inventory / المخزون) · version 1 · dialogue round 2 (claude:sonnet)
══════════════════════════════════════════════════════════════════

## OVERVIEW
A multi-tenant SaaS for small and medium retail and wholesale traders in Egypt and the Gulf. It covers the back-office (purchasing, inventory, sales, receivables and payables, accounting, tax and e-invoicing), an offline-capable point of sale (POS) and an integrated storefront that share one catalogue, one stock and one customer base [KB:raw-idea.md §1]. Adoption is driven by regulation: ZATCA phase-2 waves and Egypt's B2C e-receipt expansion require every small trader to issue compliant documents [KB:raw-idea.md §2]. One codebase serves many trading activities. Business logic reads only six capability flags, and an activity is a preset that sets them [KB:raw-idea.md §3 D2, §4]. The platform builds on erp-core 1.6.0 and rebuilds nothing it provides: tenancy, identity, settings, lookups, files, numbering, events, audit, notifications, printing, entitlements and the general ledger [§1.5 manifest; DP §5 G6]. Simplicity is measured: at most 4 mandatory fields per screen, and setup plus the first invoice in 30 minutes or less [KB:raw-idea.md §6.10].

## MODULES
| #    | Code | Module (en / ar) | Bounded context | Layer | Type | Status |
|------|------|------------------|-----------------|-------|------|--------|
| 1.1  | CAT | Catalog / الكتالوج | catalog | L1 | master data | EXISTING |
| 1.2  | PTY | Party / الأطراف | party | L1 | master data | EXCEPTION |
| 2.1  | INV | Inventory / المخزون | inventory | L2 | engine | NEW |
| 2.2  | PRC | Pricing / التسعير | pricing | L2 | engine | NEW |
| 3.1  | PUR | Purchasing / المشتريات | purchasing | L3 | transactional | NEW |
| 3.2  | SLS | Sales & POS / المبيعات ونقطة البيع | sales | L3 | transactional | NEW |
| 4.1  | STL | Settlements / التسويات | settlements | L3 | transactional | NEW |
| 4.2  | TAX | Tax & E-invoicing / الضرائب والفوترة الإلكترونية | tax | L3 | engine | NEW |
| 4.3  | SWR | SerialWarranty / الأرقام التسلسلية والضمان | after-sales | L3 | transactional | NEW |
| 4.4  | SHP | Shipping / الشحن | shipping | L3 | transactional | NEW |
| 5.1  | ACC | Accounting / المحاسبة | accounting | L3 | engine | NEW |
| 5.2  | APR | Approvals / الموافقات | approvals | L3 | engine | NEW |
| 5.3  | SFR | Storefront / المتجر الإلكتروني | storefront | L4 | transactional | NEW |
| 6.1  | INS | Insights / التحليلات | insights | L4 | reporting | NEW |
| 0.1  | — | Tenancy, tenant context, branding, public app-config / المستأجرون | — (given) | L0 | reference | EXISTING (given) |
| 0.2  | — | Users, roles, permissions, staff & customer auth, customer accounts, signup / المستخدمون والصلاحيات | — (given) | L0 | reference | EXISTING (given) |
| 0.3  | — | Settings, capability flags and activity presets / الإعدادات ومفاتيح القدرة والقوالب المسبقة | — (given) | L0 | reference | EXISTING (given) |
| 0.4  | — | Lookups / master-data code lists / القوائم المرجعية | — (given) | L0 | reference | EXISTING (given) |
| 0.5  | — | Files, public images, attachments / الملفات | — (given) | L0 | reference | EXISTING (given) |
| 0.6  | — | Number series / document numbers / تسلسل الأرقام | — (given) | L0 | reference | EXISTING (given) |
| 0.7  | — | Domain events / outbox / الأحداث | — (given) | L0 | engine | EXISTING (given) |
| 0.8  | — | Audit trail / change history / سجل التدقيق | — (given) | L0 | reference | EXISTING (given) |
| 0.9  | — | Notifications (email, in-app) / الإشعارات | — (given) | L0 | engine | EXISTING (given) |
| 0.10 | — | Reports, exports, printing (Jasper) / التقارير والطباعة | — (given) | L0 | reporting | EXISTING (given) |
| 0.11 | — | Business-module catalogue & entitlements / كتالوج الوحدات والاستحقاقات | — (given) | L0 | reference | EXISTING (given) |
| 0.12 | — | Base entity, audit columns, locking, validation, search, paging, idempotency, API envelope / الأساس المشترك | — (given) | L0 | reference | EXISTING (given) |
| 0.13 | — | GL — chart of accounts, journal entries, fiscal periods, ledger currencies / دفتر الأستاذ العام | — (given) | L0 | engine | EXISTING (given) |

Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is) · EXISTING (given) (provided by erp-core; the GIVEN MODULES block below names which entry; nothing produced).
Numbering: [tier].[sequence within tier]. The user requests Phase 2 by this number. A number is fixed at first assignment.
Status source: CAT is re-running from P0 in v2 with a v1 baseline, so it is EXISTING. PTY is past this stage: its v1 backend and frontend splits are committed and published, so it is EXCEPTION and is read as-is. The registry §9 pipeline row for PTY lags the commits and is refreshed by the orchestrator (registry E5). INV is the module of this run and is NEW, as is every other module not yet started.

## GIVEN MODULES — data, not prose
```yaml name=given-modules
rows:
  - {name: "Tenancy, tenant context, branding, public app-config", given: erp-core, entry: "erp-core:modules.tenant", provides_used: [tenants, tenancy, tenant-context, tenant-provisioning, tenant-branding, public-app-config]}
  - {name: "Users, roles, permissions, staff & customer auth, customer accounts, signup", given: erp-core, entry: "erp-core:modules.sec", provides_used: [users, roles, permissions, grants, staff-auth, customer-auth, customer-accounts, signup]}
  - {name: "Settings, capability flags and activity presets", given: erp-core, entry: "erp-core:modules.cu", provides_used: [settings, configuration, app-configuration, platform-defaults, tenant-overrides]}
  - {name: "Lookups / master-data code lists", given: erp-core, entry: "erp-core:modules.mdl", provides_used: [lookups, lookup-types, lookup-values, code-lists]}
  - {name: "Files, public images, attachments", given: erp-core, entry: "erp-core:modules.file", provides_used: [files, images, public-files, uploads, attachments]}
  - {name: "Number series / document numbers", given: erp-core, entry: "erp-core:modules.sequence", provides_used: [number-series, document-numbers]}
  - {name: "Domain events / outbox", given: erp-core, entry: "erp-core:modules.events", provides_used: [events, domain-events, event-bus, async-listeners]}
  - {name: "Audit trail / change history", given: erp-core, entry: "erp-core:modules.audit", provides_used: [audit, audit-trail, change-history]}
  - {name: "Notifications (email, in-app)", given: erp-core, entry: "erp-core:modules.notif", provides_used: [notifications, email, in-app, dispatch]}
  - {name: "Reports, exports, printing (Jasper)", given: erp-core, entry: "erp-core:modules.report", provides_used: [reports, printing, pdf-export, csv-export]}
  - {name: "Business-module catalogue & entitlements", given: erp-core, entry: "erp-core:modules.module", provides_used: [business-modules, module-entitlements, module-catalog, home-cards]}
  - {name: "Base entity, audit columns, locking, validation, search, paging, idempotency, API envelope", given: erp-core, entry: "erp-core:modules.common", provides_used: [base-entity, audit-columns, optimistic-locking, validation, search, paging, idempotency, api-envelope, localized-errors]}
  - {name: "GL — chart of accounts, journal entries, fiscal periods, ledger currencies", given: erp-core, entry: "erp-core:modules.gl", provides_used: [chart-of-accounts, fiscal-periods, journal-entries, trial-balance, ledger-currencies]}
```
Each candidate here is labelled `EXISTING (given: erp-core)` elsewhere. No row is also a NEW module above. ACC keeps only what `gl` does not provide: auto-posting rules, cost centres, budgets and period close. It posts through `GlPostingApi`; no other module calls `gl` directly.

## DEPENDENCIES — data, not prose
```yaml name=platform-dependencies
modules:
  CAT: {tier: 1, depends_on: []}
  PTY: {tier: 1, depends_on: []}
  INV: {tier: 2, depends_on: [CAT]}
  PRC: {tier: 2, depends_on: [CAT]}
  PUR: {tier: 3, depends_on: [CAT, INV, PTY]}
  SLS: {tier: 3, depends_on: [CAT, PRC, INV, PTY]}
  STL: {tier: 4, depends_on: [PTY, INV, PUR, SLS]}
  TAX: {tier: 4, depends_on: [INV, PUR, SLS]}
  SWR: {tier: 4, depends_on: [INV, SLS]}
  SHP: {tier: 4, depends_on: [SLS]}
  ACC: {tier: 5, depends_on: [INV, PUR, SLS, STL, TAX]}
  APR: {tier: 5, depends_on: [INV, PUR, SLS, STL]}
  SFR: {tier: 5, depends_on: [CAT, PRC, INV, PTY, SLS, STL, SHP]}
  INS: {tier: 6, depends_on: [CAT, PRC, INV, PTY, PUR, SLS, STL, TAX, SWR, SHP, ACC, APR, SFR]}
```
Edges come from the fixed DAG [KB:raw-idea.md §5], with the refinements resolved in dialogue (R-04):
- ACC reads INV stock-move events, because stock adjustments and count differences post to the ledger.
- APR names its document modules explicitly. An approval outcome returns to them through given `events`, so there is no reverse edge.
- STL and TAX read INV because the Branch is owned there (STL for cash and bank per branch, TAX for the issuer branch of an e-invoice).
- INS reads every other module, so it lists all 13 below it.

Tiers are the longest path from a root. The build order, waves and critical path are derived from this block by `gov.py plan-order`.

## DEFERRED (not in scope for this version)
| Item | Reason / activation trigger |
|---|---|
| Workflow engine (BPM) | profile: `forbidden`; Approvals stays lightweight [KB §3 D6] |
| Pharmacy, restaurant, gold, vehicle models; manufacturing, contracting, services | user excluded: different business models [KB §3 D1, §9] |
| Native mobile apps | user excluded v1: responsive web only [KB §9, §6.7] |
| Marketplace (multi-vendor in one storefront) | user excluded v1 [KB §9] |
| Report designer | user excluded v1 [KB §9] |
| AI assistant | deferred core module, advanced delivery tier [KB §8] |
| Multi-currency, cost centres/budgets, loyalty, carrier adapters | in scope but delivered in wave 4 (advanced tier). Every such dimension exists in the data model from day one [KB §6.8, §8] |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| R-01 | Binding module codes and bounded contexts (registry OI-01, OI-04) | Profile codes CAT, PRC, INV, PTY, PUR, SLS, STL, TAX, ACC, SWR, APR, SHP, SFR, INS are binding. The aliases SAL, SRW and STF are retired. One bounded context per module, as in DP §7.2 | Converged in dialogue (round 2, INV-P01); user confirms at the gate | profile.vocabulary.module_prefixes; engine §2.1 detection table; registry §2.2, E2; DP §7.2 |
| R-02 | Re-match against erp-core 1.6.0 (registry OI-02, OI-06) | 1.6.0 adds `gl`, so GL accounts, journal entries and fiscal periods are given and ACC narrows. No entry provides custom attributes, lot, dual quantity, cash & bank or a tax adapter, so they are platform-own: variant attributes in CAT, lot and dual quantity in INV, cash & bank in STL, the tax adapter in TAX | Converged in dialogue (round 2, INV-P02); user confirms at the gate | §1.5 manifest table (erp-core 1.6.0); KB §4.1, §5; DP §8 P-03 |
| R-03 | Owner of capability flags, activity presets and the setup wizard (registry OI-05) | No new module code. Flags and presets are given `cu` settings, read through one `FeatureFlags` facade over `SettingsApi`. The 5-step setup wizard is a frontend flow that calls each step's owner | Converged in dialogue (round 2, INV-P03); user confirms at the gate | profile rule (flags and presets are core settings read through FeatureFlags); KB §4.1, §6.9; DP §5 G1, G6 |
| R-04 | Module-to-module edge kinds (registry OI-17) | DP §6 kinds confirmed (HARD-FK to masters and documents; SOFT-READ for ACC, APR, SFR, INS), plus ACC → INV, APR → INV/PUR/SLS/STL, STL → INV and TAX → INV (Branch), and SFR listed under INS | Converged in dialogue (round 2, INV-P04, INV-P07); user confirms at the gate | KB §5; DP §6; registry OI-17 |
| R-05 | Pricing is absent from the delivery order of KB §8, but Sales & POS depends on it [KB §5] | AUTO: the build order is derived from the dependency block, so PRC is built before SLS. Only loyalty stays in the advanced tier | AUTO (derivation) | KB §5, §8; engine §2.2 (build order derived, never hand-written) |
| R-06 | PTY status: the registry pipeline row says NOT STARTED, but PTY v1 backend and frontend splits are committed and published | AUTO: PTY is EXCEPTION (read as-is), number 1.2 kept. Its edges stay in the dependency block as the DAG states them | AUTO (derivation from commits) | registry §9, E5; engine §2.1 STEP 3 |
| R-07 | Application root namespace of the business modules (raised again by the INV gate-analysis revise 1, G22) | Applied as decided in the PTY run (its platform summary decision 11, ADR-PTY-047): every business module lives under `com.commerce.<code lower case>`, never under the given's `com.erp.*` (checklist F8). INV is `com.commerce.inv`; CAT's rename stays the open platform finding of business-policies-pty row 1 (cited in business-policies-inv PLATFORM FINDINGS row 3) | Inherited platform rule (non-breaking — no code delivered); user confirms at the gate | erp-core checklist F8; ADR-PTY-047 |

Module-scoped registry items OI-08, OI-10, OI-11, OI-12, OI-13, OI-14, OI-15 and OI-16 belong to the P0 runs of CAT, PTY, SFR and STL. Registry OI-03 is an orchestrator matter. None of them changes the platform scope set out above.

## OPEN ITEMS
None — platform scope fully determined.

## NEXT STEP
14 modules, 1 exception (PTY), 13 given capabilities. Reply with an instruction to adjust, or with a module number to start Phase 2. This run produces 2.1 INV.
