Round 2 (claude:sonnet): all ten proposals are decided below, and each decision is carried into the three files. I accepted eight and amended two. The changes beyond the proposals are listed next.

- **PTY status:** the registry pipeline row says NOT STARTED, but the commit log shows PTY v1's backend and frontend splits and their publish commits. Status comes from commits (registry E5), so I set PTY to EXCEPTION and kept its number 1.2. The orchestrator should refresh registry §9. This is a derivation row (R-06), not a DECISION.
- **Dependency block fixes:** the round-1 block listed 12 modules under INS while its note said 13, so I added SFR. I also added INV to STL and TAX, because both consume Branch.
- **APR layer:** moved from L2 to L3, since it reads L3 document modules.
- **Round-1 wording fixes:** POL-INV-010 no longer contains a second EARS trigger word. The registry no longer uses "kit", which is outside the steering vocabulary.
- **Not carried here:** the flag × activity matrix (KB §7) has no slot among this engine's three outputs, so it stays with the stages that trace it.

DECISION: Profile codes SLS, SWR and SFR are binding; the aliases SAL, SRW and STF are retired; one bounded context per module
Accept option A. `profile.vocabulary.module_prefixes` is the durable record, the engine's detection table uses SLS, SWR and SFR, and registry rule E2 mints IDs only from IN PROFILE codes. The domain-profile §4 and §7 text still uses the aliases and calls the codes "PROPOSED". Correcting that is a hand edit of the profile, outside this stage's three files. These artifacts use profile codes only. Contexts follow DP §7.2.
traces: OI-01, OI-04

DECISION: erp-core 1.6.0 re-match makes gl given; lot and dual quantity stay platform-own in INV
Accept option A, with one amendment. The 1.6.0 table in this brief lists `gl` (chart-of-accounts, fiscal-periods, journal-entries, trial-balance, ledger-currencies) and no entry for custom attributes, lot, dual quantity, cash & bank or a tax adapter. Rebuilding the ledger would break G6. The amendment is that INV never calls `GlPostingApi`. Stock valuation and adjustment postings reach the ledger through ACC, which consumes stock-move events and owns the posting rules (G5, KB §5 #9). KB §4.1 calls `lot_id` and the dual-quantity columns "core seams", but the manifest is authoritative (engine §1.5).
traces: OI-02, OI-06, POL-INV-006, POL-INV-009

DECISION: Capability flags and presets stay given cu settings behind one FeatureFlags facade, with no new module code
Accept option B. The profile rule says flags and presets are core settings read through FeatureFlags. `cu` already provides platform-defaults and tenant-overrides, which together are a preset plus overrides. A new code such as SET would rebuild `cu` (G6) and is not in the profile. Hosting in CAT (option C) would force PTY → CAT and INV → CAT-for-configuration edges.

- **Amendment 1:** the facade is a thin read wrapper over `SettingsApi`. It has no entities and no module code.
- **Amendment 2:** INV declares the flags it reads: `lotExpiry`, `dualQuantity`, `bundles`. It does not read `variants`, because stock always keys on the Variant. It does not read `serialTracking`, because serial identity belongs to SWR.
- **Setup wizard:** a frontend flow that calls each step's owner.

traces: OI-05, POL-INV-006, POL-INV-009, POL-INV-010, POL-INV-011, POL-INV-014

DECISION: DAG edge kinds are confirmed; ACC reads INV, APR reads INV, PUR, SLS and STL, STL and TAX read INV for Branch, and INS lists all 13 modules below it
Accept the proposal with three amendments to the block.

- **Confirmed:** the DP §6 kinds are HARD-FK to masters and documents, and SOFT-READ for ACC, APR, SFR and INS.
- **Added ACC → INV and APR → INV, PUR, SLS, STL.** Under auto-posting (D7), stock adjustments must reach the ledger. INV transfers and adjustments are documents, so they fall under "any document module" (D6). Approval outcomes return through given `events`, so there is no reverse edge.
- **Added STL → INV and TAX → INV (amendment).** Both consume Branch (cash and bank per branch; the issuer branch on the e-invoice). Round 1 named them as Branch consumers without the edge.
- **Added SFR to INS (amendment).** The round-1 block listed 12 modules where its own note said 13.

The tier rule holds: STL and TAX stay at tier 4, ACC and APR at 5, INS at 6. Receipt cost flows from PUR into INV on the receipt call, so INV has no edge to PUR.
traces: OI-17

DECISION: Stock, moves, reservations and lots key on the Variant; a default Variant applies when the variants flag is off
Accept option A. One code path avoids doubling the both-states test burden (G2). It matches DP P-04, so DP P-04 stands as the working assumption. The brief carries no CAT module registry, so nothing conflicts. If CAT's registry decides otherwise, engine STEP C makes the CAT registry win at the P0.5 consistency check.
traces: OI-07

DECISION: CAT owns the bundle composition; INV explodes a bundle into component stock moves; no bundle stock and no assembly
Accept option A. Bundle availability is derived from component availability. Option B needs assembly orders, which is light manufacturing, excluded by D1 and KB §9. The policy is reworded to a single EARS pattern ("Where …, the system shall …"), because the round-1 draft combined `Where` with `when`. It binds POL-INV-014.
traces: OI-09, POL-INV-014

DECISION: INV owns Branch next to Warehouse; one default branch is seeded and its selector stays hidden while there is one
Accept option A. G8 and KB §6.8 name branch as a day-one dimension that no registry row owns yet. The decisive argument for INV is the gate. PTY v1 is already split and CAT v2 is in flight, so adding Branch to either needs a delta version of a module that is already built. INV is the lowest-tier owner still open to extension. The ETA/ZATCA branch rationale is *(not live-verified)*. The amendment is that STL and TAX gain an edge to INV (see the edges decision). Branch is a new term introduced from G8 and is not in the steering vocabulary.
traces: POL-INV-015, POL-INV-016, POL-INV-017

DECISION: Negative stock is a tenant setting, default allowed with a Warehouse alert; online issues are blocked when the tenant disallows it; synced offline POS sales are always accepted
Accept option C, with the default amended from round 1. Round 1 defaulted to "disallowed".

- **Evidence:** G7 (first invoice in 30 minutes or less), KB §6.5 (no dead ends) and "nothing imposed" (§6) all argue against blocking an owner who has not yet loaded opening stock. The setup wizard's import step covers items, not balances.
- **Default allowed:** every crossing below zero alerts the Warehouse role.
- **Owner opt-in:** an owner who wants strict balances switches blocking on.
- **Block scope:** blocking applies to online issues. Synced offline POS sales are always accepted, because the goods have already left (KB §5 #6).
- **Odoo:** allows negative stock by default *(not live-verified)*.
- **Binds:** POL-INV-018, POL-INV-019 and POL-INV-020.

traces: POL-INV-018, POL-INV-019, POL-INV-020

DECISION: Stock is valued at perpetual weighted-average cost per Variant across the tenant; transfers carry cost unchanged; FIFO is not offered in v1
Accept option A. IAS 2 ¶25 permits FIFO or weighted average and bars LIFO. IFRS for SMEs Section 13 is the same. Specific identification (IAS 2 ¶23) applies only to non-interchangeable items, and serial units of one Variant are interchangeable. Tenant-wide average avoids revaluing on every transfer, which a per-warehouse average (option C) would force. All standards references are *(not live-verified)*. PUR supplies unit cost on the receipt call, and each stock move carries cost for ACC and SLS. It binds POL-INV-021.
traces: POL-INV-021

DECISION: An expired lot is excluded from issue for sale and leaves stock only through a stock adjustment
Accept option A. Selling expired goods is prohibited in Egypt and KSA *(not live-verified)*. Scope amendment: the exclusion covers sale issues (SLS, POS, SFR) and FEFO selection. The lot stays in stock and visible in near-expiry reporting until written off, and transfers are not barred. It binds POL-INV-022.
traces: POL-INV-022, POL-INV-007, POL-INV-008

<<<FILE: governance-shared\analysis\modules\INV\P0\platform-summary.md>>>
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

Module-scoped registry items OI-08, OI-10, OI-11, OI-12, OI-13, OI-14, OI-15 and OI-16 belong to the P0 runs of CAT, PTY, SFR and STL. Registry OI-03 is an orchestrator matter. None of them changes the platform scope set out above.

## OPEN ITEMS
None — platform scope fully determined.

## NEXT STEP
14 modules, 1 exception (PTY), 13 given capabilities. Reply with an instruction to adjust, or with a module number to start Phase 2. This run produces 2.1 INV.
<<<END FILE>>>

<<<FILE: governance-shared\analysis\modules\INV\P0\module-registry-inv.md>>>
## MODULE REGISTRY — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module Code    : INV   (profile.vocabulary.module_prefixes)
Bounded context: inventory — where stock is and how much: stock, moves, reservations, transfers, counts, lots, dual quantity
Layer / Type   : L2 / engine     Execution tier : 2.1
Source         : NEW
Knowledge      : KB:raw-idea.md §1, §4.1, §4.2, §5 #3, §6.2, §6.7, §6.8; DP §3 #3, §5 G1 G3 G8, §7.1; erp-core 1.6.0 manifest (§1.5)
Readiness      : READY
══════════════════════════════════════════════════════════════════

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity (ar/en) | Kind | PRIVATE / SHARED | Source |
|---|---|---|---|
| Warehouse / المستودع | master | SHARED (PUR, SLS, SFR, INS) | KB §6.8; DP §7.1, §5 G8; registry §4 |
| Branch / الفرع | master | SHARED (PUR, SLS, STL, TAX, INS) | KB §6.8 ("one warehouse/branch … to start"); DP §5 G8; DECISION INV-P07 |
| Stock balance / رصيد المخزون | transactional | SHARED (SLS, SFR, INS — read only) | KB §5 #3; DP §6 |
| Stock move / حركة المخزون | transactional | PRIVATE (published as domain events to ACC, SWR, INS) | KB §6.8; DP §4 #3 |
| Reservation / حجز المخزون | transactional | PRIVATE (created by SLS and SFR through an INV crossmodule API) | KB §5 #3 |
| Transfer / تحويل مخزني | transactional | PRIVATE | KB §5 #3, §6.8 |
| Stock count / جرد المخزون | transactional | PRIVATE | KB §5 #3, §6.7 |
| Stock adjustment / تسوية المخزون | transactional | PRIVATE | AUTO (count differences and write-offs need a stock document); KB §5 #3; DECISION INV-P10 |
| Lot / التشغيلة | master | SHARED (PUR receipt, SLS FEFO issue) | KB §4.1 (`lotExpiry`); DP §7.1; DECISION INV-P02 |
| Stock valuation (average cost) / تقييم المخزون (متوسط التكلفة) | transactional | SHARED (SLS margin, ACC posting, INS) | DECISION INV-P09 |

Not registered here:
- Dual quantity / الكمية المزدوجة is an attribute of stock balances, moves and lots, not an entity (registry §4).
- Stock held as a pre-built bundle is not registered (DECISION INV-P06).
- Serial number / الرقم التسلسلي belongs to SWR.
- Inventory settings are given `cu` settings.

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| inv.adjustment-reason | Stock adjustment reason / سبب تسوية المخزون (count difference, damage, expiry write-off and similar) | — (none named by the user) | AUTO; KB §5 #3 |

Rule (profile): all LOV values are loaded at runtime from the core lookup module (mdl). There are no hardcoded enums in APIs or field specs. Activity presets and capability flags are core settings, read through FeatureFlags.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| Unit of measure / وحدة القياس | CAT (owner is CAT whether it is a CAT-held list or a given `mdl` lookup type; settled in the CAT run, DP §8 P-05) |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| Item / صنف | CAT | HARD-FK | Stock belongs to an item of the catalogue |
| Variant / متغيّر الصنف | CAT | HARD-FK | Stock, moves, reservations and lots key on the Variant; the default Variant applies when `variants` is off (DECISION INV-P05) |
| Bundle / حزمة (composition) | CAT | SOFT-READ | Components are exploded into stock moves when a bundle is issued (DECISION INV-P06) |
| Unit of measure conversions | CAT | SOFT-READ | Stock is held in the Variant's base unit of measure |
| Tenant / المستأجر | given `erp-core:modules.tenant` | (given) | Default warehouse and default branch seeded at provisioning |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| CAT | HARD-FK | Item, Variant, Bundle composition, unit-of-measure conversions |
| `erp-core:modules.cu` | (given — no XM edge; the SRS declares the use in its `given-usage` block) | `SettingsApi` through `FeatureFlags`: `lotExpiry`, `dualQuantity`, `bundles`; inventory settings (negative-stock setting, near-expiry horizon) |
| `erp-core:modules.sequence` | (given — no XM edge) | `NumberSeriesApi`: transfer, count and adjustment numbers |
| `erp-core:modules.events` | (given — no XM edge) | `DomainEventPublisher`, `OutboxPublisher`: stock-move events for ACC, SWR, INS; `OutboxSubscriber`: offline POS sales and documents from SLS and PUR |
| `erp-core:modules.audit` | (given — no XM edge) | `AuditApi`: change history of stock documents |
| `erp-core:modules.mdl` | (given — no XM edge) | `MdlLookupApi`: adjustment reason, unit of measure |
| `erp-core:modules.tenant` | (given — no XM edge) | `TenantProvisioningContributor`: default warehouse and default branch at tenant creation |
| `erp-core:modules.sec` | (given — no XM edge) | `PermissionContributor`: inventory permissions for the Warehouse role preset (D8) |
| `erp-core:modules.notif` | (given — no XM edge) | `NotificationDispatchApi`: near-expiry and negative-stock alerts to the Warehouse role |
| `erp-core:modules.report` | (given — no XM edge) | `ReportProvider`, `PrintApi`: count sheets, transfer prints, near-expiry and stock reports |
| `erp-core:modules.module` | (given — no XM edge) | `ModuleCatalogApi`, `ModuleEntitlementApi`: registration and entitlement of the Inventory business module |
| `erp-core:modules.common` | (given — no XM edge) | base entity, audit columns, optimistic locking, paging, search, idempotency |
ROOT: NO
Consumed by: PUR, SLS, STL, TAX, SWR, ACC, APR, SFR, INS (platform-dependencies).

AUTO-DECISIONS
AUTO: Serial identity is owned by SWR. INV holds only the quantities of serial-tracked Variants.  FROM: KB §4.1 (`serialTracking` → new module SerialWarranty), §5 (SWR depends on INV)  IF WRONG: move the serial ledger into INV and make SWR consume it.
AUTO: Stock is held in the Variant's base unit of measure. Purchase and sale units are converted through CAT-owned conversions.  FROM: domain best practice; DP §8 P-05  IF WRONG: hold stock per unit of measure with INV-owned conversions.
AUTO: One default warehouse and one default branch are seeded at tenant provisioning through `TenantProvisioningContributor`.  FROM: KB §6.8; manifest `erp-core:modules.tenant`  IF WRONG: create the first warehouse and branch in the setup wizard.
AUTO: Inventory settings (negative-stock setting, near-expiry horizon) are given `cu` settings, not an INV entity.  FROM: profile rule (core settings read through FeatureFlags); DP §5 G6  IF WRONG: register an INV config entity.
AUTO: SLS (orders) and SFR (checkout) create reservations through an INV crossmodule API. A POS sale issues stock directly, without a reservation.  FROM: KB §1 (one stock), §5 (SLS, SFR depend on INV); DP §5 G5  IF WRONG: reserve on POS cart lines as well.
AUTO: Stock moves are published as domain events through the given outbox, each carrying its unit cost. ACC, SWR and INS consume them, and INV holds no reverse edge.  FROM: KB §5 #3 (Inventory uses events); DP §5 G5  IF WRONG: expose a crossmodule query API in their place.
AUTO: INV never calls the given `gl`. Valuation and adjustment postings reach the ledger through ACC, which owns the posting rules and calls `GlPostingApi`.  FROM: KB §5 #9; DP §5 G5; DECISION INV-P02  IF WRONG: let INV post its own journal entries.
AUTO: Receipt cost is supplied by PUR on the receipt call into INV, so INV holds no edge to PUR.  FROM: KB §5 (PUR depends on INV); DP §5 G5  IF WRONG: INV reads the receipt cost from PUR through a crossmodule API and the edge direction is revisited.
AUTO: Dual quantity is an attribute of stock balances, moves and lots, not a separate entity.  FROM: registry §4; KB §4.1  IF WRONG: register a dual-quantity entity.
AUTO: Stock adjustment is a transactional candidate of INV.  FROM: KB §5 #3 (counts); domain best practice (a count difference posts through an adjustment document)  IF WRONG: post count differences directly from the stock count.
AUTO: Branch is a new term, introduced from the G8 dimension list. It is not yet in the steering vocabulary.  FROM: DP §5 G8; KB §6.8  IF WRONG: rename in the vocabulary at the next domain-profile revision.

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| INV-P05 | Stock key (registry OI-07, INV side) | Stock, moves, reservations and lots key on the Variant; the default Variant applies when `variants` is off. A conflicting CAT registry decision wins at the P0.5 consistency check (engine STEP C) | Converged in dialogue (round 2); user confirms at the gate | DP §8 P-04; KB §4.1; DP §5 G2 |
| INV-P06 | Bundle stock (registry OI-09) | CAT owns the composition. INV explodes a bundle into component stock moves at issue (POL-INV-014). A bundle holds no stock of its own, and bundle assembly is excluded | Converged in dialogue (round 2); user confirms at the gate | DP §8 P-06; KB §3 D1, §9 |
| INV-P07 | Branch ownership | INV owns Branch next to Warehouse, because PTY and CAT are past or at their gate. One default branch is seeded and its selector stays hidden while its count is 1 (POL-INV-015, POL-INV-016, POL-INV-017). STL and TAX read it, so each has an edge to INV | Converged in dialogue (round 2); user confirms at the gate | KB §6.8; DP §5 G8 |
| INV-P08 | Negative stock | Tenant setting, default allowed with an alert to the Warehouse role. When the tenant disallows it, online issues below zero are blocked (POL-INV-018, POL-INV-019). Synced offline POS sales are always accepted (POL-INV-020) | Converged in dialogue (round 2); user confirms at the gate | KB §5 #6, §6.5, §6.10; DP §5 G7; domain best practice |
| INV-P09 | Costing method | Perpetual weighted-average cost per Variant across the tenant; transfers carry cost unchanged; FIFO not offered in v1 (POL-INV-021) | Converged in dialogue (round 2); user confirms at the gate | IAS 2 ¶23, ¶25; IFRS for SMEs Section 13 (not live-verified); KB §3 D7 |
| INV-P10 | Expired lots | An expired lot is excluded from issue for sale and from FEFO selection (POL-INV-022). It stays in stock, visible in near-expiry reporting, and leaves stock only through a stock adjustment | Converged in dialogue (round 2); user confirms at the gate | KB §4.1, §4.2; consumer-protection practice (not live-verified) |
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

<<<FILE: governance-shared\analysis\modules\INV\P0\business-policies-inv.md>>>
## BUSINESS POLICIES — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module   : INV     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-INV-001 — One stock across channels / مخزون واحد لجميع القنوات
  Statement : The system shall keep a single stock per warehouse that back-office documents, the point of sale and the storefront all draw from.
  Statement (ar) : يجب على النظام أن يحتفظ بمخزون واحد لكل مستودع تسحب منه مستندات المكتب الخلفي ونقطة البيع والمتجر الإلكتروني جميعًا.
  Pattern   : ubiquitous
  Trigger   : Stock move (any channel)
  Rationale : The product promise is one catalogue, one stock and one customer base across back-office, POS and storefront.
  Source    : KB:raw-idea.md §1 "one catalogue, one stock, one customer base"
  Status    : CONFIRMED

POL-INV-002 — Warehouse on every stock move / المستودع في كل حركة مخزون
  Statement : The system shall record the warehouse on every stock move, including while the tenant has only one warehouse.
  Statement (ar) : يجب على النظام أن يسجّل المستودع في كل حركة مخزون، حتى عندما يكون لدى المستأجر مستودع واحد فقط.
  Pattern   : ubiquitous
  Trigger   : Create (stock move)
  Rationale : A second warehouse can be added later without data migration.
  Source    : KB:raw-idea.md §6.8 "every stock move carries warehouse_id"; DP §5 G8
  Status    : CONFIRMED

POL-INV-003 — Default warehouse at start / مستودع افتراضي عند البدء
  Statement : When a tenant is provisioned, the system shall create one default warehouse so that stock can be received without any warehouse setup.
  Statement (ar) : عند إنشاء مستأجر جديد، يجب على النظام أن ينشئ مستودعًا افتراضيًا واحدًا بحيث يمكن استلام المخزون دون أي إعداد للمستودعات.
  Pattern   : event
  Trigger   : Tenant provisioning
  Rationale : The platform starts with one warehouse and nothing is imposed on the user.
  Source    : KB:raw-idea.md §6.8 "one warehouse/branch/currency/price list to start"; DP §5 G8
  Status    : CONFIRMED

POL-INV-004 — Hidden warehouse selector while one warehouse / إخفاء محدد المستودع عند وجود مستودع واحد
  Statement : While the tenant has exactly one active warehouse, the system shall hide the warehouse selector, the inter-warehouse transfer action and the warehouse report filters.
  Statement (ar) : أثناء امتلاك المستأجر مستودعًا نشطًا واحدًا فقط، يجب على النظام أن يخفي محدد المستودع وإجراء التحويل بين المستودعات ومرشحات المستودع في التقارير.
  Pattern   : state
  Trigger   : View (any inventory screen or report)
  Rationale : Only what is meaningful is shown, with no dead menus.
  Source    : KB:raw-idea.md §6.3, §6.8; DP §5 G8
  Status    : CONFIRMED

POL-INV-005 — Reveal on second warehouse / الإظهار عند إضافة مستودع ثانٍ
  Statement : When a second warehouse is added, the system shall reveal the warehouse selector, the inter-warehouse transfer action and the warehouse report filters without data migration or re-setup.
  Statement (ar) : عند إضافة مستودع ثانٍ، يجب على النظام أن يُظهر محدد المستودع وإجراء التحويل بين المستودعات ومرشحات المستودع في التقارير دون ترحيل للبيانات ودون إعادة إعداد.
  Pattern   : event
  Trigger   : Create (warehouse)
  Rationale : Multi-warehouse appears automatically when the business grows.
  Source    : KB:raw-idea.md §6.8 "reveals it — plus the related actions (e.g. inter-warehouse transfer) and report filters — automatically"
  Status    : CONFIRMED

POL-INV-006 — Lot and expiry on receipt / التشغيلة والصلاحية عند الاستلام
  Statement : Where the lotExpiry capability flag is on, the system shall capture a lot and its expiry date on every stock receipt of an item tracked by lot.
  Statement (ar) : حيثما كان مفتاح القدرة lotExpiry مفعّلًا، يجب على النظام أن يسجّل التشغيلة وتاريخ صلاحيتها في كل استلام مخزون لصنف يُتتبَّع بالتشغيلة.
  Pattern   : optional
  Trigger   : Receive (stock receipt)
  Rationale : Grocery, cleaning-supplies, feed and produce presets need lot and expiry.
  Source    : KB:raw-idea.md §4.1 (`lotExpiry`: "lot + expiry on receipt"), §4.2
  Status    : CONFIRMED

POL-INV-007 — FEFO issue / الصرف بالأقرب انتهاءً
  Statement : Where the lotExpiry capability flag is on, the system shall issue stock of items tracked by lot first-expiry-first-out (FEFO).
  Statement (ar) : حيثما كان مفتاح القدرة lotExpiry مفعّلًا، يجب على النظام أن يصرف مخزون الأصناف المتتبَّعة بالتشغيلة وفق مبدأ الأقرب انتهاءً يُصرف أولًا (FEFO).
  Pattern   : optional
  Trigger   : Issue (stock issue)
  Rationale : Stock nearest to expiry leaves first, which reduces write-offs.
  Source    : KB:raw-idea.md §4.1 (`lotExpiry`: "FEFO issue")
  Status    : CONFIRMED

POL-INV-008 — Near-expiry reporting / تقرير قرب انتهاء الصلاحية
  Statement : Where the lotExpiry capability flag is on, the system shall report the stock that is approaching its expiry date.
  Statement (ar) : حيثما كان مفتاح القدرة lotExpiry مفعّلًا، يجب على النظام أن يقدّم تقريرًا بالمخزون الذي يقترب من تاريخ انتهاء صلاحيته.
  Pattern   : optional
  Trigger   : Report
  Rationale : Traders act on stock close to expiry before it is lost.
  Source    : KB:raw-idea.md §4.1 (`lotExpiry`: "near-expiry reports")
  Status    : CONFIRMED

POL-INV-009 — Dual quantity stock / مخزون بالكمية المزدوجة
  Statement : Where the dualQuantity capability flag is on, the system shall track the stock of dual-quantity items in both a count and a measure of weight or length.
  Statement (ar) : حيثما كان مفتاح القدرة dualQuantity مفعّلًا، يجب على النظام أن يتتبّع مخزون الأصناف ذات الكمية المزدوجة بالعدد وبالقياس (الوزن أو الطول) معًا.
  Pattern   : optional
  Trigger   : Stock move (any)
  Rationale : Fabrics (metre), building materials and steel, and produce or meat by weight are stocked in two quantities.
  Source    : KB:raw-idea.md §4.1 (`dualQuantity`: "count + weight/length"), §4.2
  Status    : CONFIRMED

POL-INV-010 — Flag turn-off blocked when data depends on it / منع إيقاف المفتاح عند وجود بيانات تعتمد عليه
  Statement : If a user turns off the lotExpiry or dualQuantity capability flag and stock data depends on it, then the system shall block the change and show which data depends on it.
  Statement (ar) : إذا أوقف مستخدم مفتاح القدرة lotExpiry أو dualQuantity وكانت بيانات مخزون تعتمد عليه، فيجب على النظام أن يمنع التغيير وأن يبيّن البيانات التي تعتمد عليه.
  Pattern   : unwanted
  Trigger   : Update (capability flag)
  Rationale : A flag may be turned on at any time, but never off when data depends on it, and no error is a dead end.
  Source    : KB:raw-idea.md §6.2, §6.5; DP §5 G3
  Status    : CONFIRMED

POL-INV-011 — Flags only, never the activity / المفاتيح فقط وليس النشاط
  Statement : The system shall vary inventory behaviour only by capability flags and never by the tenant's declared activity.
  Statement (ar) : يجب على النظام أن يغيّر سلوك المخزون وفق مفاتيح القدرة فقط، وألّا يعتمد أبدًا على النشاط التجاري المعلن للمستأجر.
  Pattern   : ubiquitous
  Trigger   : Any inventory operation
  Rationale : One codebase serves every activity. The activity is only a preset that sets flags.
  Source    : KB:raw-idea.md §3 D2, §7; DP §5 G1
  Status    : CONFIRMED

POL-INV-012 — Mobile stock counting / الجرد عبر الهاتف المحمول
  Statement : The system shall let a user perform a stock count from a mobile-sized responsive web screen.
  Statement (ar) : يجب على النظام أن يتيح للمستخدم إجراء جرد المخزون من شاشة ويب متجاوبة بحجم الهاتف المحمول.
  Pattern   : ubiquitous
  Trigger   : Count (stock count)
  Rationale : Counting is a mobile-first activity, and there is no native app in v1.
  Source    : KB:raw-idea.md §6.7 "Mobile-first for selling, collecting, counting", §9
  Status    : CONFIRMED

POL-INV-013 — Warehouse role preset / الدور المسبق «المستودع»
  Statement : The system shall provide a Warehouse role preset that grants access to the inventory functions.
  Statement (ar) : يجب على النظام أن يوفّر دورًا مسبق الإعداد باسم «المستودع» يمنح الوصول إلى وظائف المخزون.
  Pattern   : ubiquitous
  Trigger   : Tenant provisioning / role assignment
  Rationale : Three role presets (Owner, Seller/Cashier, Warehouse) let a new tenant start without designing roles.
  Source    : KB:raw-idea.md §3 D8; registry §2.4 D8
  Status    : CONFIRMED

POL-INV-014 — Bundle issue moves component stock / صرف الحزمة يحرّك مخزون مكوّناتها
  Statement : Where the bundles capability flag is on, the system shall issue the stock of a bundle by moving the stock of its component items.
  Statement (ar) : حيثما كان مفتاح القدرة bundles مفعّلًا، يجب على النظام أن يصرف مخزون الحزمة بتحريك مخزون الأصناف المكوِّنة لها.
  Pattern   : optional
  Trigger   : Issue (bundle)
  Rationale : A bundle holds no stock of its own, so its availability always follows its components and no assembly step is needed.
  Source    : Dialogue resolution INV-P06; KB:raw-idea.md §4.1 (`bundles`), §3 D1, §9
  Status    : CONFIRMED

POL-INV-015 — Default branch at start / فرع افتراضي عند البدء
  Statement : When a tenant is provisioned, the system shall create one default branch so that documents can be issued without any branch setup.
  Statement (ar) : عند إنشاء مستأجر جديد، يجب على النظام أن ينشئ فرعًا افتراضيًا واحدًا بحيث يمكن إصدار المستندات دون أي إعداد للفروع.
  Pattern   : event
  Trigger   : Tenant provisioning
  Rationale : The platform starts with one branch and nothing is imposed on the user.
  Source    : Dialogue resolution INV-P07; KB:raw-idea.md §6.8 "one warehouse/branch/currency/price list to start"; DP §5 G8
  Status    : CONFIRMED

POL-INV-016 — Hidden branch selector while one branch / إخفاء محدد الفرع عند وجود فرع واحد
  Statement : While the tenant has exactly one active branch, the system shall hide the branch selector and the branch report filters.
  Statement (ar) : أثناء امتلاك المستأجر فرعًا نشطًا واحدًا فقط، يجب على النظام أن يخفي محدد الفرع ومرشحات الفرع في التقارير.
  Pattern   : state
  Trigger   : View (any screen or report with a branch)
  Rationale : Only what is meaningful is shown, with no dead menus.
  Source    : Dialogue resolution INV-P07; KB:raw-idea.md §6.3, §6.8; DP §5 G8
  Status    : CONFIRMED

POL-INV-017 — Reveal on second branch / الإظهار عند إضافة فرع ثانٍ
  Statement : When a second branch is added, the system shall reveal the branch selector and the branch report filters without data migration or re-setup.
  Statement (ar) : عند إضافة فرع ثانٍ، يجب على النظام أن يُظهر محدد الفرع ومرشحات الفرع في التقارير دون ترحيل للبيانات ودون إعادة إعداد.
  Pattern   : event
  Trigger   : Create (branch)
  Rationale : Multi-branch appears automatically when the business grows.
  Source    : Dialogue resolution INV-P07; KB:raw-idea.md §6.8 "reveals it … automatically"
  Status    : CONFIRMED

POL-INV-018 — Optional block on negative stock / منع الرصيد السالب اختياريًا
  Statement : Where the tenant disallows negative stock, the system shall block an online stock issue that would take a warehouse balance below zero.
  Statement (ar) : حيثما منع المستأجر الرصيد السالب، يجب على النظام أن يحظر أي صرف مخزون متصل بالإنترنت يؤدي إلى هبوط رصيد المستودع دون الصفر.
  Pattern   : optional
  Trigger   : Issue (online stock issue)
  Rationale : A tenant that wants strict balances can switch blocking on; the default allows negative stock so that a new owner is never stopped before opening stock is loaded.
  Source    : Dialogue resolution INV-P08; KB:raw-idea.md §6.5, §6.10; DP §5 G7
  Status    : CONFIRMED

POL-INV-019 — Alert on negative stock / تنبيه عند الرصيد السالب
  Statement : If a stock issue takes a warehouse balance below zero, then the system shall alert the Warehouse role.
  Statement (ar) : إذا أدّى صرف مخزون إلى هبوط رصيد مستودع دون الصفر، فيجب على النظام أن ينبّه دور «المستودع».
  Pattern   : unwanted
  Trigger   : Issue (stock issue)
  Rationale : A negative balance is allowed but never silent.
  Source    : Dialogue resolution INV-P08; KB:raw-idea.md §6.5
  Status    : CONFIRMED

POL-INV-020 — Offline POS sales always accepted / قبول مبيعات نقطة البيع دون اتصال دائمًا
  Statement : If a synchronised offline point-of-sale sale takes a warehouse balance below zero, then the system shall accept the sale.
  Statement (ar) : إذا أدّى بيع من نقطة البيع نُفّذ دون اتصال ثم تمت مزامنته إلى هبوط رصيد مستودع دون الصفر، فيجب على النظام أن يقبل عملية البيع.
  Pattern   : unwanted
  Trigger   : Synchronise (offline sale)
  Rationale : The goods have already left, so rejecting the sale would lose a real transaction.
  Source    : Dialogue resolution INV-P08; KB:raw-idea.md §5 #6 (POS offline-capable)
  Status    : CONFIRMED

POL-INV-021 — Weighted-average stock valuation / تقييم المخزون بالتكلفة المتوسطة المرجّحة
  Statement : The system shall value stock at the perpetual weighted-average cost of each variant across the tenant.
  Statement (ar) : يجب على النظام أن يقيّم المخزون بالتكلفة المتوسطة المرجّحة المستمرة لكل متغيّر صنف على مستوى المستأجر.
  Pattern   : ubiquitous
  Trigger   : Receive / Issue (stock move)
  Rationale : Weighted average is permitted by the accounting standards, is the simplest for a small trader and stays stable when stock moves between warehouses.
  Source    : Dialogue resolution INV-P09; IAS 2 ¶25 (not live-verified)
  Status    : CONFIRMED

POL-INV-022 — Expired lots excluded from sale / استبعاد التشغيلات المنتهية من البيع
  Statement : If a lot has passed its expiry date, then the system shall exclude it from issue for sale.
  Statement (ar) : إذا تجاوزت تشغيلة تاريخ صلاحيتها، فيجب على النظام أن يستبعدها من الصرف للبيع.
  Pattern   : unwanted
  Trigger   : Issue (sale)
  Rationale : Selling expired goods is prohibited; the lot stays in stock until a stock adjustment writes it off.
  Source    : Dialogue resolution INV-P10; KB:raw-idea.md §4.1, §4.2; consumer-protection practice (not live-verified)
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
None — standard values apply.

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Bundle assembly and disassembly (stock of pre-built bundles) / تجميع الحزم وتفكيكها | Assembly orders are light manufacturing, which is excluded. Bundles are exploded into component stock moves | A later version that admits manufacturing scope | KB §3 D1, §9; dialogue INV-P06 |
| Native mobile counting app / تطبيق جرد أصلي للهاتف | Counting runs on responsive web only in v1 | A version that admits native apps | KB §6.7, §9 |
| FIFO cost layers / طبقات التكلفة بالوارد أولًا | v1 values stock at weighted-average cost only | A later version, if a customer requires FIFO | dialogue INV-P09 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| INV-P06 | Bundle stock | Explode to component moves; no bundle stock. Adds POL-INV-014 | Converged in dialogue (round 2); user confirms at the gate | DP §8 P-06; KB §3 D1, §9 |
| INV-P07 | Branch ownership | INV owns Branch; a default branch is seeded and its selector is hidden while there is one. Adds POL-INV-015, POL-INV-016, POL-INV-017 | Converged in dialogue (round 2); user confirms at the gate | KB §6.8; DP §5 G8 |
| INV-P08 | Negative stock | Tenant setting, default allowed with a Warehouse alert; online issues blocked when the tenant disallows it; synced offline POS sales always accepted. Adds POL-INV-018, POL-INV-019, POL-INV-020 | Converged in dialogue (round 2); user confirms at the gate | KB §5 #6, §6.5, §6.10; DP §5 G7 |
| INV-P09 | Costing method | Perpetual weighted-average cost per Variant across the tenant. Adds POL-INV-021 | Converged in dialogue (round 2); user confirms at the gate | IAS 2 ¶25 (not live-verified) |
| INV-P10 | Expired lots | Excluded from issue for sale; removed only by a stock adjustment. Adds POL-INV-022 | Converged in dialogue (round 2); user confirms at the gate | KB §4.1 |
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

<!-- CONVERGED -->