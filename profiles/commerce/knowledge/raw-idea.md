# Platform Brief — Retail & Wholesale Commerce SaaS on erp-core

**Status:** approved vision, input to `domain-profile` · **Owner:** Hesham Ezzat · **Date:** 2026-10-21
**Given:** `erp-core` (multi-tenant core, deployment mode `single|multi`, business modules tenant-agnostic) — consumed via `gov.py given add erp-core --version <current>`; nothing the core provides is rebuilt.
**Technical base (approved):** Spring Boot (Java 25) backend on `erp-core` · React frontend, one build for all deployments reading `GET /api/public/app-config` (tenancy mode, feature flags, activity preset) · PostgreSQL 16 (Flyway, app migrations `V1000+`). Jasper for printing via the core `PrintApi`.

---

## 1. One-paragraph vision

A SaaS platform for small and medium **retail and wholesale traders** in Egypt and the Gulf, covering back-office (purchasing, inventory, sales, receivables/payables, accounting, tax & e-invoicing), point of sale, and an **integrated e-commerce storefront** — one catalogue, one stock, one customer base. It serves many trading activities (electronics & appliances, apparel, household goods, cosmetics, grocery, spare parts, building materials, …) from **one codebase** through six activity capability flags and per-activity presets. It is **feature-complete but simple to use**: every capability exists, nothing is forced on the user; power lives in the backend, the UI shows only what is enabled.

## 2. Market and positioning (facts gathered 2026-09)

- Wholesale & retail trade is the largest activity by number of establishments: **54.4%** of Egypt's 3.9M establishments (6th Economic Census 2022/23) and **30.7%** of Saudi SMEs (Monsha'at SME Monitor).
- Demand is regulation-driven: **mandatory e-invoicing** (Saudi ZATCA phase-2 waves; Egypt B2C e-receipt expansion from Sept 2025) forces every small trader to adopt compliant software.
- Competitors in the segment are all SaaS (Qoyod, Wafeq, Daftra, …); the differentiator is **one product for back-office + POS + storefront**, activity-aware, and simple.

## 3. Fixed decisions (P0 — not open questions)

| # | Decision |
|---|---|
| D1 | Activity scope: **retail & wholesale, multi-activity**, driven by six capability flags (§4). Pharmacies, restaurants, gold, vehicles, manufacturing, contracting, services are **out of scope** (different business models). |
| D2 | Business logic reads **capability flags only**, never the activity name. The activity is a **preset** that sets flags. Enforced by ArchUnit/lint. |
| D3 | Deployment: `erp.tenancy.mode=multi` (SaaS) is the default go-to-market; `single` available for dedicated/on-prem customers. Modules are tenant-agnostic (core rule). |
| D4 | Public storefront tenant resolution: **subdomain** (`<shop>.<platform>`) by default, **custom domain** optional; resolver feeds the core `TenantContext`; in `single` mode it returns the default tenant. Designed once in the Storefront API. |
| D5 | **Guest checkout allowed**; the guest becomes a CUSTOMER-realm account after the first order (core customer realm). |
| D6 | Approvals are a **lightweight business module** (`Approvals`): per document type → none / one step / role-based steps, configured on one screen. No BPM engine (core decision upheld). |
| D7 | Accounting is fully present (GL, cost centres, budgets) but invisible by default; auto-posting from documents; "accounting mode" reveals its screens. |
| D8 | Roles: three presets (Owner, Seller/Cashier, Warehouse) + custom roles via core permissions. |
| D9 | Simplicity is a measurable requirement (§6), not a feature cut. |
| D10 | Delivery is module-by-module in the fixed order of §5; a module is Done only with its reports, tests and conformance scenarios green. |

## 4. Capability flags and activity presets

### 4.1 The six flags (read through one `FeatureFlags` API)

| Flag | What it changes | Core seam used |
|---|---|---|
| `variants` | items have size/colour/… variants; catalogue, cart, stock at variant level | custom attributes |
| `serialTracking` (+ `warranty`) | serial captured on receipt/sale; warranty card; service returns | new business module `SerialWarranty` |
| `lotExpiry` | lot + expiry on receipt; FEFO issue; near-expiry reports | core `lot_id` + policy |
| `dualQuantity` | count + weight/length; pricing per either unit | core dual-quantity columns |
| `bundles` | composite items / kits / promo packs | — |
| `b2bCredit` | wholesale customers: credit limits, terms, price lists | Pricing + AR |

Each flag has tests in both states; the UI shows fields/menus/reports only when the flag is on.

### 4.2 Presets (✓ required · ○ optional)

| Activity | variants | serial | lotExpiry | dualQty | bundles | b2b |
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

Launch priority: electronics & appliances → apparel → household/cosmetics (zero or one extension each); grocery next (lot/expiry + delivery).

## 5. Module map (business modules the factory generates) — fixed DAG

| # | Module | Depends on | Core given used |
|---|---|---|---|
| 1 | **Catalog** — item, variants, media, categories, publish state | — | files (public), lookups, settings |
| 2 | **Pricing** — price lists, promotions, coupons, loyalty points | Catalog | settings |
| 3 | **Inventory** — stock, reservations, transfers, counts, lot/expiry, dual qty | Catalog | sequences, events, audit |
| 4 | **Party** — customers (incl. CUSTOMER-realm users), suppliers, addresses, credit | — | auth (realms), settings |
| 5 | **Purchasing** — PO, goods receipt, supplier invoice | 1,3,4 | sequences, print |
| 6 | **Sales & POS** — quotation, order, invoice, returns/exchange; POS mode (offline-capable) | 1,2,3,4 | sequences, print, events |
| 7 | **Settlements** — collections, payments, AR/AP ageing, payment gateway adapter | 4,5,6 | cash&bank seams, audit |
| 8 | **Tax & E-invoicing** — VAT, country adapters (ETA/ZATCA/Oman), tax returns | 5,6 | tax adapter |
| 9 | **Accounting** — auto-posting rules, GL, cost centres, budgets, period close | 5,6,7,8 | report/print |
| 10 | **SerialWarranty** — serials, warranty cards, service returns (flag `serialTracking`) | 3,6 | — |
| 11 | **Approvals** — per-document approval steps (D6) | any document module | events, notifications |
| 12 | **Shipping** — delivery status, carrier adapters | 6 | notifications |
| 13 | **Storefront** — public catalogue/search, guest cart, checkout, customer area; tenant-by-host resolver (D4) | 1,2,3,4,6,7,12 | auth realms, files public |
| 14 | **Insights** — dashboards, 15–20 standard reports, saved filters | all | report engine + Jasper |

Rules: dependencies flow downward only; cross-module calls only through declared `crossmodule` APIs; core only through manifest extension points (`C-GIVEN-1/2`).

## 6. Simplicity as a requirement ("everything available, nothing imposed")

1. **Progressive disclosure**: every screen starts minimal; "Advanced" reveals the rest; ≤ 4 mandatory fields per screen.
2. **Presets then customise**: choosing the activity fills all settings; later changes never require re-setup. Flags may be turned **on** at any time; turning off is blocked when data depends on it.
3. **Only enabled is shown**: menus, fields, reports, and storefront features bound to flags — no dead menus.
4. **Action in place**: return from the invoice, collect from the statement, receive from the PO.
5. **No dead ends**: every error offers a next action; undo for unposted operations.
6. **One global search** (items, customers, documents).
7. **Mobile-first** for selling, collecting, counting; responsive web, no separate app in v1.
8. **Defaults ("everything available, nothing imposed")**: one warehouse/branch/currency/price list to start; every such dimension exists in the data model from day one (e.g. every stock move carries `warehouse_id`); the UI hides the selector while the count is 1 and reveals it — plus the related actions (e.g. inter-warehouse transfer) and report filters — automatically when a second one is added. No data migration, no "mode" switch.
9. **Setup wizard (5 steps)**: company → activity preset → tax & e-invoice → payment methods → import items from Excel.
10. **Metric**: an owner with no technical background completes setup and issues the first invoice in **≤ 30 minutes** without training; a new employee sells after **≤ 10 minutes**.

## 7. Coverage and architecture guarantees (how we know it is sound)

| Risk | Guarantee | Enforced in |
|---|---|---|
| Activity "covered" on paper only | One **conformance scenario per preset** (setup → receive → sell → return → collect → report) runs in CI | `activity-conformance` test suite |
| Behaviour varies by activity ad hoc | Logic reads the six flags via `FeatureFlags` only; every flag tested in both states | ArchUnit + lint (`activity ==` or preset read outside settings = build failure) |
| Missing requirement for an activity | **Flag × activity matrix** is a P0 artifact; each cell traces to an EARS requirement ID (P1) and a `TC-*`; an untraced cell fails the consistency check | Factory traceability P0→P1→test-gen |
| Random inter-module design | Fixed DAG (§5); `crossmodule` contracts only; core via manifest extension points | ArchUnit boundaries + `C-GIVEN-1/2` + pass-review checklist (`erp-core-usage`) |
| Decisions drift during build | §3 decisions enter P0 as fixed + ADRs; no open questions after the PRD | One-gate analysis + adversarial reading |
| Uncontrolled growth | Fixed delivery order; a module is Done with reports, tests, conformance green | execution-state gates |

**Definition of "covers activity X":** preset exists + conformance scenario green + every matrix cell traced. Adding an activity later = one matrix column + one scenario.

## 8. Delivery order

1. Catalog, Party, Inventory, Purchasing, Sales & POS, Settlements, Tax & E-invoicing (sellable back-office + POS).
2. Storefront + Shipping (e-commerce live).
3. Accounting (visible mode), SerialWarranty, Approvals, Insights.
4. Advanced: multi-currency, cost centres/budgets, loyalty, carrier adapters, AI assistant (deferred core module).

## 9. Out of scope (v1)

Native mobile apps; marketplace (multi-vendor in one storefront); manufacturing/contracting/services modules; pharmacy/restaurant/gold/vehicle models; report designer; BPM workflow engine.

## 10. Next steps

1. Create project `commerce` from the governance-shared template; `gov.py given add erp-core --version <current>`.
2. Place this brief as the platform-brief input; run `domain-profile` → expect §4/§5 reproduced with core items marked `EXISTING (given)`.
3. P0 with §3 as fixed decisions and §4.2 as the matrix artifact → P1 (EARS + traceability) → … → packages → build in §8 order with the `erp-core-usage` skill.