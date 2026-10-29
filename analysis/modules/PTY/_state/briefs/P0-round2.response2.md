Round 2 (claude:sonnet): I accept all ten proposals. Four have amendments: credit ownership (credit limit becomes an attribute, not an entity), guest linking (precision), the PRC → PTY edge (narrowed to a lookup read), and the walk-in customer (guards added). The three files are complete. POL-PTY-008 and POL-PTY-009 are now written. Nothing material is left open.

DECISION: Profile codes SLS, SWR and SFR are binding; SAL, SRW and STF are retired aliases
Accepted. `profile.vocabulary.module_prefixes` is the durable record, and the project registry forbids minting IDs with the aliases (§2.2, E2). The engine's detection table also uses SLS, SWR and SFR. Option B would need a profile change and a re-mint for no gain.
Amendment: the bounded-context ids (`sales`, `after-sales`, `storefront`) stay as in the domain-profile §7.2, because a context id does not change with its code. The domain-profile still prints the aliases in §4, §7.1 and §7.3. Its next hand re-run replaces them. Until then every artifact uses the profile codes.
traces: OI-01, OI-04

DECISION: erp-core 1.6.0 is re-matched: `gl` becomes a given; custom attributes, lot, dual quantity, cash and bank, and the tax adapter stay platform-own
Accepted. The 1.6.0 manifest table adds `gl` (chart-of-accounts, fiscal-periods, journal-entries, trial-balance; GlPostingApi). The other twelve entries are unchanged against the domain-profile's 1.5.0 match. No entry provides custom attributes, lot, dual quantity, cash and bank, or a tax adapter. `report` lists `payment-file-export`, which is a bank payment file, not a cash-and-bank module.
Amendment: STL's payment-file output is a partial hit on `report`. ACC keeps posting rules, cost centres, budgets and accounting mode. ACC's own P0 run moves GL account, journal entry and accounting period to the given, with no entity IDs. This run only records the match.
traces: OI-02, OI-06, CAND-ACC-002, CAND-ACC-003, CAND-ACC-006

DECISION: Capability flags and activity presets are given `cu` settings read through FeatureFlags; no new module is created
Accepted (option B). The profile rule states that activity presets and capability flags are core settings read through FeatureFlags. `cu` provides platform-defaults and tenant-overrides. A new `SET` module would need a profile change and would own no entity.
Amendments:
- The setup wizard is a composition of steps, each belonging to the module whose data it sets. It is not a module. If wizard progress needs persistence, it is a `cu` setting. P0.5 writes the stories.
- A flag that cannot be turned off while data depends on it needs a guard owned by the module holding that data. PTY states its guard in POL-PTY-005. How guards hook into `cu` is a P2/P3 design matter and is not decided here.
traces: OI-05, CAND-OPEN-003, CAND-OPEN-004

DECISION: One Party identity per counterparty, with Customer and Supplier roles
Accepted (option B). A wholesale counterparty often both buys and sells. The tax identity and addresses are then held once. The glossary definitions of Customer and Supplier both start from "a party", so the model matches the steering language.
Amendments:
- The UI keeps two entry points, "Customer" and "Supplier", so the four-mandatory-input limit is unaffected. Adding the other role to an existing record is an action in place [KB §6.4].
- No AR/AP netting in v1. The model only keeps that possible.
- The comparison with SAP Business Partner, Odoo `res.partner` and Dynamics is general knowledge and was not researched live (NO-RESEARCH). The decision rests on the glossary and KB §1, not on it.
traces: CAND-PTY-001, CAND-PTY-002, CAND-PTY-003, POL-PTY-001, POL-PTY-006

DECISION: `sec` owns the CUSTOMER-realm account; PTY owns the commercial Customer; a guest whose contact matches an existing Customer is linked by invitation
Accepted. Credentials, login and invitations are given `sec` (customer-accounts, customer-invitations). The Customer links to 0..1 account by SOFT-READ.
Precisions:
- A guest with no match becomes a new Customer, and the account is offered through `sec`.
- A guest with a match has the order attached to the existing Customer immediately, so the order is not held. The account link completes when the shopper accepts the invitation.
- Auto-linking on an unverified contact would hand a stranger the credit and price list of someone else's customer record. That is the reason for the invitation.
- POL-PTY-002 stays as the stated need (KB §3 D5). The mechanism lives here and in the registry.
traces: OI-13, CAND-PTY-001, POL-PTY-001, POL-PTY-002

DECISION: PTY owns customer credit limit and payment terms; STL contributes open exposure through a PTY extension point; credit limit is an attribute of Customer
Accepted (option C). Option A forces SLS (tier 3) to read STL (tier 4). Option B has the same upward edge through events. With C, STL depends on PTY, which the graph already says, and PTY knows no consumer.
Amendments:
- The credit limit is an attribute of Customer, not an entity. v1 has one limit per customer in one currency, and it has no lifecycle of its own. Payment term is an entity, because customers and suppliers reuse named terms. CAND-PTY-004 resolves to Payment term, and CAND-STL-003 is withdrawn.
- A contributor answers one thing: the customer's open exposure. PTY evaluates exposure plus the new amount against the limit and returns a verdict. SLS and SFR enforce it.
- A customer with no limit set is not checked.
- If `b2bCredit` is off, no limit exists and the check is inert.
traces: OI-16, CAND-PTY-004, CAND-STL-003, POL-PTY-003, POL-PTY-004, POL-PTY-005

DECISION: Credit overrun is a tenant setting in `cu`, offering warn or block-with-override, with the default block-with-override
Accepted (option D). "Nothing imposed" [KB §6.2] is met by the warn alternative. "Every error offers a next action" [KB §6.5] is met by the override. Unbounded credit is the larger financial risk, which justifies the stricter default. The override is a permission. Which role presets hold it (Owner, Seller/Cashier, Warehouse; KB §3 D8) is decided in P1. Recorded as POL-PTY-008, the next free id in the module's sequence.
traces: POL-PTY-008, POL-PTY-003

DECISION: Price lists follow customer groups; PRC reads PTY's customer-group lookup (SOFT-READ edge PRC → PTY)
Accepted with a narrower scope. The `customer-group` lookup type is owned by PTY and held in `mdl`. A price list is "for a channel or customer group" [DP §7.1]. PRC reads that lookup, and only that. Reading a lookup is a read from PTY, so the edge is declared. PRC never reads a Customer. SLS and SFR pass the customer's group to the price resolution. PRC stays tier 2, and no lower tier depends on a higher one.
traces: CAND-PRC-001, OI-17

DECISION: Seed one walk-in customer (عميل نقدي) per tenant; POS sales without a named customer use it
Accepted (option B), recorded as POL-PTY-009. This rests on KB §6.8 and §6.10 only. The Saudi simplified-invoice claim was not researched (NO-RESEARCH) and is not relied on.
Guards:
- The walk-in customer never receives a credit limit or credit terms. A credit sale needs a named customer.
- The walk-in customer cannot be deleted.
- A storefront guest is not the walk-in customer.
- Seeding runs through the given TenantProvisioningContributor.
traces: POL-PTY-009, CAND-PTY-001

DECISION: Module edges are HARD-FK except ACC, APR, SFR and INS (SOFT-READ); party data reaches TAX and SHP on the document's party snapshot
Accepted. KB §5 states the dependencies and DP §6 gives their kinds. A snapshot on the issued SLS or PUR document is needed anyway, so a legal document does not change when a party is edited. TAX reads the buyer data and SHP the delivery address from the document, so neither gets a PTY edge.
Note: APR depends on the document-producing modules of the graph (INV, PUR, SLS, STL), following "any document module" [KB §5 #11]. APR's own P0 run may extend that, and `gov.py` recomputes the tiers.
traces: OI-17

<<<FILE: governance-shared\analysis\modules\PTY\P0\platform-summary.md>>>
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
<<<END FILE>>>

<<<FILE: governance-shared\analysis\modules\PTY\P0\module-registry-pty.md>>>
## MODULE REGISTRY — Party / الأطراف (PTY)
══════════════════════════════════════════════════════════════════
Module Code    : PTY   (profile.vocabulary.module_prefixes)
Bounded context: party — "Who: customers, suppliers, addresses, credit. Credentials belong to given sec" (DP §7.2)
Layer / Type   : L1 / master data     Execution tier : 1.2
Source         : NEW
Knowledge      : KB:raw-idea.md §1, §3 D5, §4.1, §5 #4, §6.1–§6.6; DP §3 #4, §6, §7.1, §7.2; registry §4–§5 (CAND-PTY-001…004, CAND-STL-003), OI-13, OI-16
Readiness      : READY
══════════════════════════════════════════════════════════════════

ENTITIES OWNED   (names only; entity IDs are assigned by P1)
| Entity (ar/en) | Kind | PRIVATE / SHARED | Source |
|---|---|---|---|
| الطرف / Party | master | PRIVATE (the identity record; other modules reference it through its Customer or Supplier role) | decision 4: one identity record per counterparty |
| العميل / Customer | master | SHARED (SLS, STL, SFR, INS) | CAND-PTY-001; DP §7.1; KB §5 #4 |
| المورّد / Supplier | master | SHARED (PUR, STL, INS) | CAND-PTY-002; DP §7.1; KB §5 #4 |
| العنوان / Address | master | SHARED (SLS, PUR, SFR) | CAND-PTY-003; AUTO (PRIVATE → SHARED) |
| شرط الدفع / Payment term | config | SHARED (SLS, PUR, STL) | CAND-PTY-004 (terms part); KB §4.1 "terms"; decision 6 |

Not owned here:
- The CUSTOMER-realm account (credentials, login, invitations) is given `erp-core:modules.sec`, linked by SOFT-READ (decision 5).
- The credit limit is an attribute of Customer, not an entity (decision 6). CAND-PTY-004 resolves to Payment term.
- The customer's open balance and ageing belong to STL. CAND-STL-003 is withdrawn: STL owns balances, not limits (decision 6).
- The tax identity of a party is an attribute of Party, not an entity of TAX.

LOOKUPS OWNED    (value lists this module masters, held in given `mdl`)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| customer-group | Customer group / مجموعة العملاء: classifies customers (for example retail / wholesale) so that price lists and reports can follow it | none named | DP §7.1 (Price list "for a channel or customer group"); decision 8 |
| address-type | Address type / نوع العنوان: the purpose of an address (for example billing / delivery) | none named | AUTO (domain best practice) |
Rule (profile): all LOV values runtime-loaded from the core lookup module (mdl); no hardcoded enums in APIs or field specs; activity presets and capability flags are core settings, read through FeatureFlags

LOOKUPS CONSUMED (from other modules; each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| country / region | given `erp-core:modules.mdl` (AUTO) |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| User (CUSTOMER realm) | given `erp-core:modules.sec` | SOFT-READ | A Customer links to 0..1 CUSTOMER-realm account (decision 5) |
| AppConfiguration (capability flag `b2bCredit`, credit-overrun setting) | given `erp-core:modules.cu` | SOFT-READ (through FeatureFlags / SettingsApi) | Credit features show only while `b2bCredit` is on; the overrun behaviour is a tenant setting (decisions 3, 7) |
| LookupType / LookupValue | given `erp-core:modules.mdl` | SOFT-READ | customer-group, address-type, country / region |
| NumberSeries | given `erp-core:modules.sequence` | SOFT-READ | Customer and Supplier numbers (AUTO) |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| `erp-core:modules.sec` | (given — no XM edge; the SRS declares the use in its `given-usage` block) | SecUserDirectoryApi, SecCustomerInvitationApi, SecCustomerDefaultRoleApi; PermissionContributor (party, credit and credit-override permissions) |
| `erp-core:modules.cu` | (given — no XM edge) | SettingsApi through the FeatureFlags read facade (`b2bCredit`; credit-overrun setting) |
| `erp-core:modules.mdl` | (given — no XM edge) | MdlLookupApi (customer-group, address-type, country / region) |
| `erp-core:modules.sequence` | (given — no XM edge) | NumberSeriesApi (party numbers) |
| `erp-core:modules.events` | (given — no XM edge) | DomainEventPublisher / OutboxPublisher (party created / changed events for SFR and INS) |
| `erp-core:modules.audit` | (given — no XM edge) | AuditApi (credit-limit and payment-term change history) |
| `erp-core:modules.tenant` | (given — no XM edge) | TenantProvisioningContributor (walk-in customer seed, decision 9); TenantExportContributor (party data in tenant export) |
| `erp-core:modules.common` | (given — no XM edge) | base entity, audit columns, optimistic locking, validation, search (global search, POL-PTY-007), paging, owned lookups |
ROOT: YES (no business-module dependency; DAG #4 [KB:raw-idea.md §5])

Consumers (reverse edges, from the platform-dependencies block): PRC (SOFT-READ of the customer-group lookup only, decision 8), PUR (HARD-FK Supplier), SLS (HARD-FK Customer), STL (HARD-FK Customer / Supplier; it fills the credit-exposure extension point, decision 6), SFR (SOFT-READ Customer, Address), INS (SOFT-READ).

EXTENSION POINT DECLARED
| Point | Declared by | Filled by | What it carries |
|---|---|---|---|
| Customer credit exposure / تعرّض العميل الائتماني | PTY | STL (open receivables); other modules may contribute | The customer's open exposure only. PTY compares exposure plus the new amount with the credit limit and returns a verdict; SLS and SFR enforce it (decision 6) |

POLICIES SERVED   (index only; the statements live in business-policies-pty.md)
| Policy | Serves |
|---|---|
| POL-PTY-001 | Party, Customer — one customer base across back-office, POS and storefront |
| POL-PTY-002 | Customer, sec link — guest becomes a customer (decision 5) |
| POL-PTY-003 | Customer credit limit, Payment term — flag `b2bCredit` on |
| POL-PTY-004 | Customer credit limit, Payment term — flag `b2bCredit` off |
| POL-PTY-005 | Flag `b2bCredit` guard (decision 3) |
| POL-PTY-006 | Party, Customer, Supplier — quick registration |
| POL-PTY-007 | Customer — global search |
| POL-PTY-008 | Customer credit limit, credit-exposure extension point (decision 7) |
| POL-PTY-009 | Customer (walk-in) — POS sales without a named customer (decision 9) |

AUTO-DECISIONS
AUTO: Address is SHARED (SLS ship-to / bill-to, PUR supplier address, SFR customer area address book); an address belongs to the Party and is held once  FROM: STEP 4 domain best practice; DP §6 (Storefront → Party)  IF WRONG: keep Address PRIVATE; consumers read it only through Customer / Supplier
AUTO: documents (SLS, PUR) copy the party data they need as a snapshot; TAX and SHP read it from the document, not from PTY  FROM: KB §5 fixed DAG (TAX → 5,6; SHP → 6)  IF WRONG: add TAX → PTY and SHP → PTY SOFT-READ edges (tiers unchanged)
AUTO: Customer and Supplier numbers come from given `sequence`  FROM: manifest `sequence` (number-series, document-numbers); G6  IF WRONG: party numbers are entered by hand
AUTO: party created / changed events are published through given `events` for SFR and INS  FROM: manifest `events`; KB §5 rules (crossmodule only)  IF WRONG: consumers read on demand only
AUTO: credit-limit and payment-term changes are recorded in given `audit`  FROM: manifest `audit` (change-history); domain best practice  IF WRONG: base audit columns only
AUTO: the country / region lists are consumed from given `mdl`  FROM: manifest `mdl` (code-lists, master-data)  IF WRONG: PTY masters them as owned lookups
AUTO: an address-type lookup is owned  FROM: STEP 4 domain best practice  IF WRONG: one untyped address list per party
AUTO: supplier payment terms are flag-independent; customer credit limits and customer payment terms are under `b2bCredit`  FROM: KB §4.1 (`b2bCredit` = "wholesale customers: credit limits, terms"); KB §5 #7 (AP ageing needs due dates)  IF WRONG: put supplier terms under `b2bCredit` too
AUTO: a customer with no credit limit set is not credit-checked  FROM: KB §6.2 (nothing imposed)  IF WRONG: treat a missing limit as zero and block every credit sale
AUTO: the walk-in customer never receives a credit limit or credit terms, and cannot be deleted  FROM: KB §6.8 (defaults); domain best practice (credit needs a named party)  IF WRONG: allow an anonymous credit balance and deletion of the seed record
AUTO: no AR/AP netting between the customer and supplier roles of one Party in v1  FROM: KB §5 #7 (AR and AP ageing are separate); decision 4  IF WRONG: add netting to STL scope
AUTO: one credit limit per customer, in the tenant's single currency  FROM: KB §6.8, §8 #4 (multi-currency is advanced)  IF WRONG: promote credit limit to an entity keyed by currency when multi-currency ships

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 4 | Party model | One Party identity record per counterparty, with Customer and Supplier roles over it. The screens still say "Customer" and "Supplier"; adding the other role is an action in place | Converged in dialogue (round 2); user confirms | DP §7.1 (Customer / Supplier "a party that …"); KB §1 (one customer base), §6.4, §6.6 |
| 5 | Customer identity split (OI-13) and guest conversion | Given `sec` owns credentials and the CUSTOMER realm. PTY owns the commercial Customer, linked 0..1 by SOFT-READ. A guest whose contact matches an existing Customer has the order attached to that Customer and is linked through a `sec` customer invitation, never by an unverified contact | Converged in dialogue (round 2); user confirms | KB §3 D5, §5 #4; manifest `sec` (customer-accounts, customer-invitations) |
| 6 | Credit ownership (OI-16) and the graph | PTY owns the credit limit (an attribute of Customer) and the Payment term entity. STL owns balances and ageing. STL fills a PTY credit-exposure extension point, so no upward edge exists; PTY returns the verdict and SLS / SFR enforce it | Converged in dialogue (round 2); user confirms | KB §4.1, §5 #4, #7, §5 rules; manifest contributor pattern (TenantProvisioningContributor, PermissionContributor, PrintParameterContributor) |
| 9 | Walk-in customer | PTY seeds one walk-in customer (عميل نقدي) per tenant; POS uses it when the cashier names no customer. It carries no credit and is protected from deletion (POL-PTY-009) | Converged in dialogue (round 2); user confirms | KB §6.8 (defaults), §6.10 (sell within 10 min); manifest `tenant` (TenantProvisioningContributor) |
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

<<<FILE: governance-shared\analysis\modules\PTY\P0\business-policies-pty.md>>>
## BUSINESS POLICIES — Party / الأطراف (PTY)
══════════════════════════════════════════════════════════════════
Module   : PTY     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-PTY-001 — One customer base / قاعدة عملاء واحدة
  Statement    : The system shall keep one customer base that the back-office, the point of sale and the storefront share.
  Statement-ar : يجب أن يحتفظ النظام بقاعدة عملاء واحدة تتشاركها الأعمال الخلفية ونقطة البيع والمتجر الإلكتروني.
  Pattern   : ubiquitous
  Trigger   : Create / Update
  Rationale : the product's differentiator is one catalogue, one stock and one customer base across channels.
  Source    : KB:raw-idea.md §1 "one catalogue, one stock, one customer base"
  Status    : CONFIRMED

POL-PTY-002 — Guest becomes a customer after the first order / تحويل الزائر إلى عميل بعد أول طلب
  Statement    : When a guest completes their first storefront order, the system shall register the guest as a Customer linked to a CUSTOMER-realm account.
  Statement-ar : عندما يُتمّ الزائر أول طلب له في المتجر الإلكتروني، يجب أن يسجّل النظامُ الزائرَ عميلاً مرتبطاً بحساب في نطاق CUSTOMER.
  Pattern   : event
  Trigger   : Submit (first storefront order)
  Rationale : guest checkout removes friction, and the shopper still joins the one customer base.
  Source    : KB:raw-idea.md §3 D5 "the guest becomes a CUSTOMER-realm account after the first order"; DP §5 G9; dialogue resolution 5 (matching contact is linked by invitation)
  Status    : CONFIRMED

POL-PTY-003 — Credit for wholesale customers / الائتمان لعملاء الجملة
  Statement    : Where the capability flag `b2bCredit` is on, the system shall let the trader set a credit limit and payment terms for each customer.
  Statement-ar : حيثما كان مفتاح القدرة `b2bCredit` مفعّلاً، يجب أن يتيح النظام للتاجر تحديد حد ائتمان وشروط دفع لكل عميل.
  Pattern   : optional
  Trigger   : Create / Update (Customer)
  Rationale : wholesale customers buy on credit within limits and terms.
  Source    : KB:raw-idea.md §4.1 "`b2bCredit` — wholesale customers: credit limits, terms, price lists"
  Status    : CONFIRMED

POL-PTY-004 — Credit hidden while the flag is off / إخفاء الائتمان عند تعطيل المفتاح
  Statement    : While the capability flag `b2bCredit` is off, the system shall hide customer credit limits and customer payment terms.
  Statement-ar : طالما كان مفتاح القدرة `b2bCredit` معطّلاً، يجب أن يُخفي النظام حدود ائتمان العملاء وشروط دفعهم.
  Pattern   : state
  Trigger   : Display
  Rationale : only what is enabled is shown; no dead menus or fields.
  Source    : KB:raw-idea.md §4.1 "the UI shows fields/menus/reports only when the flag is on", §6.3
  Status    : CONFIRMED

POL-PTY-005 — Credit flag cannot be turned off while used / منع تعطيل مفتاح الائتمان أثناء استخدامه
  Statement    : If a user turns the capability flag `b2bCredit` off when any customer has a credit limit or payment terms, then the system shall refuse the change.
  Statement-ar : إذا حاول مستخدم تعطيل مفتاح القدرة `b2bCredit` عندما يكون لأي عميل حد ائتمان أو شروط دفع، فيجب أن يرفض النظام التغيير.
  Pattern   : unwanted
  Trigger   : Update (capability flag)
  Rationale : a flag may be turned on at any time; turning it off is blocked while data depends on it.
  Source    : KB:raw-idea.md §6.2 "turning off is blocked when data depends on it"; DP §5 G3
  Status    : CONFIRMED

POL-PTY-006 — Quick party registration / تسجيل سريع للأطراف
  Statement    : The system shall let a user register a Customer or a Supplier with no more than four mandatory inputs.
  Statement-ar : يجب أن يتيح النظام للمستخدم تسجيل عميل أو مورّد بما لا يزيد على أربعة مدخلات إلزامية.
  Pattern   : ubiquitous
  Trigger   : Create
  Rationale : simplicity is measurable: at most four mandatory fields per screen, and setup plus the first invoice in 30 minutes or less.
  Source    : KB:raw-idea.md §3 D9, §6.1 "≤ 4 mandatory fields per screen", §6.10; DP §5 G7
  Status    : CONFIRMED

POL-PTY-007 — Customers in global search / العملاء في البحث الشامل
  Statement    : The system shall make every Customer findable through the one global search.
  Statement-ar : يجب أن يجعل النظام كل عميل قابلاً للعثور عليه عبر البحث الشامل الموحّد.
  Pattern   : ubiquitous
  Trigger   : Search
  Rationale : one global search covers items, customers and documents.
  Source    : KB:raw-idea.md §6.6 "One global search (items, customers, documents)"
  Status    : CONFIRMED

POL-PTY-008 — Credit overrun handling / معالجة تجاوز حد الائتمان
  Statement    : If a sale would take a customer's open exposure above the customer's credit limit, then the system shall apply the tenant's credit-overrun setting, which by default blocks the sale unless a user holding the credit-override permission approves it.
  Statement-ar : إذا كان من شأن عملية بيع أن ترفع انكشاف العميل المفتوح فوق حد ائتمانه، فيجب أن يطبّق النظام إعداد المستأجر لتجاوز الائتمان، الذي يمنع البيع افتراضياً ما لم يوافق عليه مستخدم يملك صلاحية تجاوز الائتمان.
  Pattern   : unwanted
  Trigger   : Submit (sales document)
  Rationale : wholesale credit needs a control, yet nothing is imposed: the tenant may choose to warn only, and a permitted role always has a next action.
  Source    : dialogue resolution 7; KB:raw-idea.md §6.2 "nothing imposed", §6.5 "every error offers a next action", §3 D8 (role presets), §4.1 (`b2bCredit`)
  Status    : CONFIRMED

POL-PTY-009 — Walk-in customer for unnamed POS sales / العميل النقدي لمبيعات نقطة البيع بلا عميل مسمّى
  Statement    : When a cashier completes a point-of-sale sale without naming a customer, the system shall record the sale against the tenant's walk-in customer.
  Statement-ar : عندما يُتمّ الكاشير عملية بيع في نقطة البيع دون تحديد عميل، يجب أن يسجّل النظام البيع على العميل النقدي الخاص بالمستأجر.
  Pattern   : event
  Trigger   : Submit (point-of-sale sale)
  Rationale : a new employee sells within ten minutes, and every invoice still has a party for ageing and reports.
  Source    : dialogue resolution 9; KB:raw-idea.md §6.8 (defaults), §6.10 "a new employee sells after ≤ 10 minutes"
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
None — standard values apply.

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Marketplace sellers / البائعون في سوق متعدد البائعين | A Supplier never sells through the storefront; the storefront has one seller, the tenant | a later version that adds a marketplace | KB:raw-idea.md §9 |
| Multi-currency party balances / أرصدة الأطراف متعددة العملات | Parties trade in the tenant's single default currency in v1; the currency dimension exists in the model, hidden while its count is 1 | wave 4 "advanced" (multi-currency) | KB:raw-idea.md §6.8, §8 #4 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| 7 | Behaviour when a sale would exceed a customer's credit limit | A tenant setting in given `cu` offering warn, or block with override by a permitted role. The default is block with override. Recorded as POL-PTY-008 | Converged in dialogue (round 2); user confirms | KB §6.2 ("nothing imposed"), §6.5 ("every error offers a next action"), §3 D8 (role presets) |
| 9 | Walk-in customer for POS sales without a named customer | Seeded per tenant by PTY, with no credit and protected from deletion. Recorded as POL-PTY-009 | Converged in dialogue (round 2); user confirms | KB §6.8, §6.10 |
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

```
✓ Party (PTY) — P0 complete (dialogue round 2; READY)
  Next : P0.5 reads platform-summary.md · module-registry-pty.md · business-policies-pty.md
  Precondition for P0.5: PTY is ROOT (depends_on: []) — no upstream contract needed
  Another module: 2.2 INV (Inventory) — next in delivery order after Party [KB §8]
```

Requirements begin in P0.5 and later stages; these files are their input.

<!-- CONVERGED -->