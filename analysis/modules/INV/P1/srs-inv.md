# SRS — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Profile : commerce
Inputs : prd, domain-profile, project-registry (PRD approved 2026-10-29); module-registry-inv, business-policies-inv (P0); contract-cat (CAT v2, P1.5)
Counts : REQ 138 · AC 154 · ENT 14 · RULE 48 · SCR-REQ 12 · ADR 0
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information

| Item | Value |
|---|---|
| Module | Inventory / المخزون (INV) |
| Feature code | INV |
| Version | v1 (first version — no delta) |
| Date | 2026-10-31 |
| Status | DRAFT — P1 pass 1, round 1 |
| Prepared by | claude:opus (P1 SRS engine) |
| Inputs | PRD INV v1 (28 stories, approved 2026-10-29 by Hesham), business-policies-inv (POL-INV-001 … POL-INV-022), module-registry-inv, domain-profile v1, project-registry 1.0.0, contract-cat (CAT v2) |
| Decisions applied | 27 DEFAULT · 0 ADR raised by this stage (next free ADR id stays ADR-INV-014) |
| Questions | None — ambiguity self-resolved (Decisions applied) |

## A2 — Functional context

### In scope
- Warehouses and branches, each with one default seeded at tenant provisioning and its selector hidden while the tenant has one active record (POL-INV-003 … 005, 015 … 017).
- One stock per Variant and warehouse (and lot, where tracked) that back-office documents, the point of sale and the storefront all draw from (POL-INV-001).
- Stock moves with their warehouse, source document and unit cost (POL-INV-002); move history.
- Reservations created by Sales & POS orders and storefront checkout (module-registry AUTO).
- Receipts and issues submitted by other modules (Purchasing, Sales & POS, Storefront) through cross-module operations.
- Transfers between warehouses at unchanged cost (POL-INV-021, INV-P09).
- Stock counts from a phone's web browser (POL-INV-012) and stock adjustments with a reason (registry lookup `inv.adjustment-reason`), including opening stock (US-INV-012).
- Perpetual weighted-average valuation per Variant across the tenant (POL-INV-021).
- Negative-stock setting, alert and offline point-of-sale acceptance (POL-INV-018 … 020).
- Bundle issue and availability through component Variants (POL-INV-014).
- Lot and expiry, FEFO issue, near-expiry reporting and alerts, exclusion of expired lots from sale (POL-INV-006 … 008, 022).
- Dual quantity — count plus a weight or length measure (POL-INV-009).
- Protection against switching `lotExpiry` or `dualQuantity` off while data depends on it (POL-INV-010).
- The INV page grants of the Owner, Seller/Cashier and Warehouse role presets (POL-INV-013).

### Out of scope
- Bundle assembly and disassembly (business-policies-inv, scope exceptions; KB §3 D1, §9).
- A native counting app (KB §6.7, §9).
- FIFO cost layers (INV-P09).
- Serial identity — owned by SWR; INV holds only the quantities of serial-tracked Variants (module-registry AUTO).
- Ledger postings — ACC consumes the stock-move event and owns posting rules (module-registry AUTO).
- Purchase receipts, sales invoices, POS sales and storefront orders as documents — PUR, SLS and SFR own them and call INV.
- Writing capability flags — the flag owner (P-02, OI-05) writes them; INV answers the turn-off check.
- In-transit stock between warehouses (DEFAULT D-07).

### Module function
Inventory keeps the single truth of how much of each Variant the tenant holds, where, in which lot and at what weighted-average cost. Every channel changes it only through stock moves that INV records, values and publishes, so back-office, POS and storefront always see the same balance and availability.

### Detailed description
A new tenant starts with one default branch and one default warehouse. While each count stays 1, no screen asks for a branch or a warehouse, and no transfer action is offered. The Owner loads opening stock with quantities and costs, many lines at once. Purchasing submits receipts with their unit cost. Sales & POS and the storefront reserve stock for orders and checkout, and submit issues when goods leave. A POS sale issues directly. Each receipt recalculates the Variant's weighted-average cost across the tenant. Each issue, adjustment-out and transfer is valued at that average. A Warehouse user transfers stock between warehouses, counts stock on the shelf from a phone and adjusts stock with a reason. Where `lotExpiry` is on, receipts of lot-tracked Items capture a lot and its expiry, sales issue the nearest expiry first, expired lots are kept out of every sale, and the Warehouse role is told about lots entering the near-expiry horizon. Where `dualQuantity` is on, the stock of dual-quantity Items is held as a count and a measure. Where `bundles` is on, a bundle's availability and issue follow its components.

Roles: **Owner** (everything), **Warehouse** (the stock functions), **Seller/Cashier** (stock balances read-only) — KB §3 D8.

### Current situation
| Step | Party | Notes |
|---|---|---|
| Stock kept per channel | Owner | Point-of-sale stock, online stock and back-office stock are often kept apart, so the same goods are promised twice (KB §1, §2) |
| Counting on paper | Warehouse | Counts are written down and keyed in later |
| Cost by estimate | Owner | Small traders seldom value stock consistently |

### Current difficulties
- Overselling across channels when stock is not shared.
- Multi-warehouse setups that force a data migration when a second location opens (KB §6.8).
- Expired goods sold or forgotten on the shelf.
- No explanation of a balance: moves and their documents are not linked.

### Proposed system and benefits
One stock and one valuation for every channel; move history that explains each balance; warehouses and branches that appear only when the business needs them; counting on a phone; lot, expiry and dual quantity switched on by capability flag, never by activity name (POL-INV-011).

### General notes
- Currency: stock values are in the tenant's base currency; multi-currency is an advanced tier (KB §8).
- Quantities are held in the base unit of the Item (CON-CAT-001 `baseUomCode`); callers convert other units before calling INV (DEFAULT D-05).
- Deferred: none of the 28 stories is deferred.

## A3 — Entities and fields

**Standard fields — per profile (listed once, not repeated per entity):**

| Kind | Fields |
|---|---|
| master | nameAr, nameEn, code, isActiveFl, createdBy, createdAt, updatedBy, updatedAt |
| transactional | docNo, docDate, statusCode, createdBy, createdAt, updatedBy, updatedAt |
| config | key, valueAr, valueEn, isActiveFl |

- Every entity identifier is `{entity}Pk`, logical type **reference — BIGINT identity key assigned by the database**. A HARD-FK to another module's entity is a BIGINT reference.
- Audit fields are system-filled and never accepted from the caller.
- `docNo` is carried only by the numbered documents (Transfer, Stock count, Stock adjustment — §3.3 numbering test). A transactional entity that is not a numbered document uses `docDate` as its business date and carries `statusCode` only when A6 declares a status lookup for it (DEFAULT D-01).
- A line entity (Transfer line, Stock count line, Stock adjustment line) takes `docNo`, `docDate` and `statusCode` from its header and carries only the audit fields itself (DEFAULT D-01).
- Logical types: text · number · decimal (places stated) · flag · date · date-time · lookup · reference.
- Quantity: decimal, 3 places, in the Item's base unit. Unit cost and average cost: decimal, 4 places. Value: decimal, 2 places, tenant base currency (DEFAULT D-06).

### ENT-INV-001 — Warehouse / المستودع

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master | SHARED (owner) | yes — `code` from series INV_WAREHOUSE (used on documents and by PUR, SLS, SFR) | create, read, search, update, deactivate, reactivate, set as default | read by PUR, SLS, SFR, INS (HARD-FK) | module-registry ENTITIES OWNED; POL-INV-002 … 005 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| warehousePk | reference (BIGINT) | yes | identity | system-assigned | المعرّف | Identifier |
| code | text (20) | yes | series INV_WAREHOUSE | system-generated on first save, read-only (RULE-INV-001) | رمز المستودع | Warehouse code |
| nameAr | text (100) | yes | — | unique among the tenant's warehouses (RULE-INV-002) | اسم المستودع بالعربية | Warehouse name (Arabic) |
| nameEn | text (100) | no | — | English display falls back to nameAr | اسم المستودع بالإنجليزية | Warehouse name (English) |
| branchFk | reference → ENT-INV-002 | yes | active Branch | the default branch when not chosen (REQ-INV-031) | الفرع | Branch |
| isDefaultFl | flag | yes | — | exactly one active default (RULE-INV-006) | المستودع الافتراضي | Default warehouse |
| isActiveFl | flag | yes | — | standard | نشط | Active |

### ENT-INV-002 — Branch / الفرع

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master | SHARED (owner) | yes — `code` from series INV_BRANCH (printed on documents by SLS, PUR, TAX) | create, read, search, update, deactivate, reactivate, set as default | read by PUR, SLS, STL, TAX, INS (HARD-FK) | module-registry ENTITIES OWNED; INV-P07; POL-INV-015 … 017 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| branchPk | reference (BIGINT) | yes | identity | system-assigned | المعرّف | Identifier |
| code | text (20) | yes | series INV_BRANCH | system-generated on first save, read-only (RULE-INV-010) | رمز الفرع | Branch code |
| nameAr | text (100) | yes | — | unique among the tenant's branches (RULE-INV-011) | اسم الفرع بالعربية | Branch name (Arabic) |
| nameEn | text (100) | no | — | English display falls back to nameAr | اسم الفرع بالإنجليزية | Branch name (English) |
| isDefaultFl | flag | yes | — | exactly one active default (RULE-INV-015) | الفرع الافتراضي | Default branch |
| isActiveFl | flag | yes | — | standard | نشط | Active |

### ENT-INV-003 — Stock balance / رصيد المخزون

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | SHARED (owner) | no — never referenced outside the system by a number | read, search (system-maintained by stock moves; never created or changed by a user) | availability read by SLS, SFR, INS | module-registry ENTITIES OWNED; POL-INV-001; INV-P05 |

One record per Variant × warehouse × lot (lot empty for stock without a lot).

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| stockBalancePk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| variantFk | reference → ENT-CAT-002 | yes | CAT Variant | HARD-FK | متغيّر الصنف | Variant |
| warehouseFk | reference → ENT-INV-001 | yes | Warehouse | — | المستودع | Warehouse |
| lotFk | reference → ENT-INV-012 | no | Lot | empty for stock without a lot | التشغيلة | Lot |
| onHandQty | decimal (3) | yes | — | may be below zero (POL-INV-018 default) | الكمية الفعلية | On-hand quantity |
| onHandMeasureQty | decimal (3) | no | — | dual-quantity Items only (POL-INV-009) | القياس الفعلي | On-hand measure |
| lastMoveAt | date-time | yes | — | time of the last move | آخر حركة | Last move |
| docDate | date | yes | — | date of the first move (standard field) | تاريخ أول حركة | First move date |

Derived in every read (not held): `reservedQty` (sum of ACTIVE reservations of the Variant in the warehouse), `availableQty` (REQ-INV-038).

### ENT-INV-004 — Stock move / حركة المخزون

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE (published as the stock-move event) | no — identified by its source document number | create (by INV documents and cross-module receipts and issues), read, search; never updated or deleted (RULE-INV-023) | event consumed by ACC, SWR, INS | module-registry ENTITIES OWNED, AUTO; POL-INV-002 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| stockMovePk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| docDate | date | yes | — | business date of the move | تاريخ الحركة | Move date |
| moveTypeCode | lookup | yes | INV_MOVE_TYPE | — | نوع الحركة | Move type |
| variantFk | reference → ENT-CAT-002 | yes | CAT Variant | HARD-FK; a component Variant for a bundle issue | متغيّر الصنف | Variant |
| warehouseFk | reference → ENT-INV-001 | yes | Warehouse | POL-INV-002 | المستودع | Warehouse |
| lotFk | reference → ENT-INV-012 | no | Lot | lot-tracked Items | التشغيلة | Lot |
| quantity | decimal (3) | yes | > 0 | direction from moveTypeCode | الكمية | Quantity |
| measureQty | decimal (3) | no | > 0 | dual-quantity Items (RULE-INV-040) | القياس | Measure |
| unitCost | decimal (4) | yes | — | receipt cost or current average (REQ-INV-061) | تكلفة الوحدة | Unit cost |
| moveValue | decimal (2) | yes | quantity × unitCost | — | قيمة الحركة | Move value |
| balanceAfterQty | decimal (3) | yes | — | Variant × warehouse on-hand after this move, all lots (REQ-INV-076) | الرصيد بعد الحركة | Balance after move |
| sourceDocTypeCode | lookup | yes | INV_SOURCE_DOC_TYPE | — | نوع المستند | Document type |
| sourceDocRef | reference (BIGINT, no FK) | yes | identifier of the source document in its module | SOFT reference — INV holds no edge to PUR, SLS, SFR | معرّف المستند | Document identifier |
| sourceDocNo | text (30) | yes | number of the source document | shown in history | رقم المستند | Document number |
| sourceLineRef | reference (BIGINT, no FK) | no | source line identifier | — | معرّف السطر | Line identifier |
| bundleVariantFk | reference → ENT-CAT-002 | no | bundle Variant | set on component moves of a bundle issue | الحزمة | Bundle |
| reservationFk | reference → ENT-INV-005 | no | Reservation | issue that consumes a reservation | الحجز | Reservation |
| offlineFl | flag | yes | — | true for a synchronised offline POS sale (POL-INV-020) | بيع دون اتصال | Offline sale |
| idempotencyKey | text (64) | yes | caller-supplied | one recording per key (RULE-INV-024) | مفتاح عدم التكرار | Idempotency key |

### ENT-INV-005 — Reservation / حجز المخزون

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no — identified by its source document | create, read, release, consume (by SLS and SFR through INV operations) | created by SLS, SFR | module-registry ENTITIES OWNED, AUTO |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| reservationPk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| docDate | date | yes | — | date reserved | تاريخ الحجز | Reservation date |
| statusCode | lookup | yes | INV_RESERVATION_STATUS | A7 | حالة الحجز | Reservation status |
| variantFk | reference → ENT-CAT-002 | yes | CAT Variant | a component Variant when exploded from a bundle | متغيّر الصنف | Variant |
| warehouseFk | reference → ENT-INV-001 | yes | Warehouse | — | المستودع | Warehouse |
| originalQty | decimal (3) | yes | > 0 | as requested | الكمية المحجوزة أصلًا | Reserved quantity |
| reservedQty | decimal (3) | yes | ≥ 0 | remaining after consumption | الكمية المحجوزة المتبقية | Remaining reserved |
| shortfallQty | decimal (3) | yes | ≥ 0 | part not covered by availability when accepted (REQ-INV-045) | النقص | Shortfall |
| bundleVariantFk | reference → ENT-CAT-002 | no | bundle Variant | REQ-INV-049 | الحزمة | Bundle |
| sourceDocTypeCode | lookup | yes | INV_SOURCE_DOC_TYPE | — | نوع المستند | Document type |
| sourceDocRef | reference (BIGINT, no FK) | yes | — | SOFT reference | معرّف المستند | Document identifier |
| sourceDocNo | text (30) | yes | — | — | رقم المستند | Document number |
| sourceLineRef | reference (BIGINT, no FK) | no | — | — | معرّف السطر | Line identifier |
| closedAt | date-time | no | — | set on CONSUMED or RELEASED | تاريخ الإغلاق | Closed at |
| idempotencyKey | text (64) | yes | caller-supplied | one recording per key (RULE-INV-024) | مفتاح عدم التكرار | Idempotency key |

### ENT-INV-006 — Transfer / تحويل مخزني

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | yes — `docNo` from series INV_TRANSFER (printed; numbered document) | create, read, search, update (DRAFT), post, cancel | — | module-registry ENTITIES OWNED; POL-INV-004, 005 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| transferPk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| docNo | text (30) | yes | series INV_TRANSFER | RULE-INV-030 | رقم التحويل | Transfer number |
| docDate | date | yes | today by default | — | تاريخ التحويل | Transfer date |
| statusCode | lookup | yes | INV_DOC_STATUS | A7 | حالة المستند | Document status |
| fromWarehouseFk | reference → ENT-INV-001 | yes | active Warehouse | RULE-INV-016 | من المستودع | From warehouse |
| toWarehouseFk | reference → ENT-INV-001 | yes | active Warehouse | RULE-INV-016 | إلى المستودع | To warehouse |
| noteText | text (500) | no | — | Advanced | ملاحظات | Notes |
| postedAt | date-time | no | — | set on posting | تاريخ الترحيل | Posted at |

### ENT-INV-007 — Transfer line / سطر التحويل

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional (line) | PRIVATE | no — part of the Transfer | create, update, remove while the Transfer is DRAFT | — | child of ENT-INV-006 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| transferLinePk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| transferFk | reference → ENT-INV-006 | yes | — | — | التحويل | Transfer |
| variantFk | reference → ENT-CAT-002 | yes | active Variant of a STANDARD Item | RULE-INV-018 | متغيّر الصنف | Variant |
| lotFk | reference → ENT-INV-012 | no | Lot of the Variant | required for lot-tracked Items (RULE-INV-037) | التشغيلة | Lot |
| quantity | decimal (3) | yes | > 0 | RULE-INV-017 | الكمية | Quantity |
| measureQty | decimal (3) | no | > 0 | dual-quantity Items (RULE-INV-040) | القياس | Measure |
| unitCost | decimal (4) | no | — | filled at posting with the current average cost | تكلفة الوحدة | Unit cost |

### ENT-INV-008 — Stock count / جرد المخزون

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | yes — `docNo` from series INV_COUNT (count sheet reference) | create, read, search, update (DRAFT), post, cancel | — | module-registry ENTITIES OWNED; POL-INV-012 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| stockCountPk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| docNo | text (30) | yes | series INV_COUNT | RULE-INV-030 | رقم الجرد | Count number |
| docDate | date | yes | today by default | — | تاريخ الجرد | Count date |
| statusCode | lookup | yes | INV_DOC_STATUS | A7 | حالة المستند | Document status |
| warehouseFk | reference → ENT-INV-001 | yes | active Warehouse | default warehouse while one is active (REQ-INV-013) | المستودع | Warehouse |
| noteText | text (500) | no | — | Advanced | ملاحظات | Notes |
| adjustmentFk | reference → ENT-INV-010 | no | — | the adjustment generated at posting (REQ-INV-092) | التسوية الناتجة | Resulting adjustment |
| postedAt | date-time | no | — | set on posting | تاريخ الترحيل | Posted at |

### ENT-INV-009 — Stock count line / سطر الجرد

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional (line) | PRIVATE | no — part of the Stock count | create, update, remove while the Stock count is DRAFT | — | child of ENT-INV-008 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| stockCountLinePk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| stockCountFk | reference → ENT-INV-008 | yes | — | — | الجرد | Stock count |
| variantFk | reference → ENT-CAT-002 | yes | from a barcode (CON-CAT-010) or a Variant pick | RULE-INV-032 | متغيّر الصنف | Variant |
| lotFk | reference → ENT-INV-012 | no | Lot of the Variant | required for lot-tracked Items (RULE-INV-037) | التشغيلة | Lot |
| countedQty | decimal (3) | yes | ≥ 0 | RULE-INV-047 | الكمية المعدودة | Counted quantity |
| countedMeasureQty | decimal (3) | no | ≥ 0 | dual-quantity Items | القياس المعدود | Counted measure |
| systemQty | decimal (3) | no | — | on-hand at posting | الكمية في النظام | System quantity |
| differenceQty | decimal (3) | no | countedQty − systemQty | set at posting | الفرق | Difference |

### ENT-INV-010 — Stock adjustment / تسوية المخزون

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | yes — `docNo` from series INV_ADJUSTMENT (numbered document) | create, read, search, update (DRAFT), post, cancel | — | module-registry ENTITIES OWNED, AUTO; INV-P10 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| stockAdjustmentPk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| docNo | text (30) | yes | series INV_ADJUSTMENT | RULE-INV-030 | رقم التسوية | Adjustment number |
| docDate | date | yes | today by default | — | تاريخ التسوية | Adjustment date |
| statusCode | lookup | yes | INV_DOC_STATUS | A7 | حالة المستند | Document status |
| warehouseFk | reference → ENT-INV-001 | yes | active Warehouse | default warehouse while one is active | المستودع | Warehouse |
| reasonCode | lookup | yes | INV_ADJUSTMENT_REASON | RULE-INV-033 | سبب التسوية | Adjustment reason |
| noteText | text (500) | no | — | Advanced | ملاحظات | Notes |
| sourceCountFk | reference → ENT-INV-008 | no | — | set when generated by a count | الجرد المصدر | Source count |
| postedAt | date-time | no | — | set on posting | تاريخ الترحيل | Posted at |

### ENT-INV-011 — Stock adjustment line / سطر التسوية

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional (line) | PRIVATE | no — part of the Stock adjustment | create, update, remove while the Stock adjustment is DRAFT | — | child of ENT-INV-010 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| stockAdjustmentLinePk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| stockAdjustmentFk | reference → ENT-INV-010 | yes | — | — | التسوية | Stock adjustment |
| rowNo | number | yes | — | line order; the row number reported by a bulk load (REQ-INV-072) | رقم السطر | Row number |
| variantFk | reference → ENT-CAT-002 | yes | active Variant of a STANDARD Item | RULE-INV-018, RULE-INV-028 | متغيّر الصنف | Variant |
| lotNo | text (40) | no | — | lot-tracked Items; resolves or creates the Lot at posting | رقم التشغيلة | Lot number |
| lotExpiryDate | date | no | — | required with a new lot number on an increase (RULE-INV-035) | تاريخ الصلاحية | Expiry date |
| lotFk | reference → ENT-INV-012 | no | — | resolved from lotNo | التشغيلة | Lot |
| quantityChange | decimal (3) | yes | ≠ 0 unless measureQtyChange ≠ 0 | positive = increase, negative = decrease (RULE-INV-034) | التغيّر في الكمية | Quantity change |
| measureQtyChange | decimal (3) | no | — | dual-quantity Items | التغيّر في القياس | Measure change |
| unitCost | decimal (4) | no | ≥ 0 | increases; required for OPENING_STOCK (RULE-INV-025, RULE-INV-026) | تكلفة الوحدة | Unit cost |
| lineValue | decimal (2) | no | — | set at posting | قيمة السطر | Line value |

### ENT-INV-012 — Lot / التشغيلة

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master | SHARED (owner) | no system number — `code` is the lot number written on the goods, entered on receipt | create (on receipt or increase), read, search, update (expiry correction) | read by PUR (receipt), SLS (FEFO issue) | module-registry ENTITIES OWNED; POL-INV-006 … 008, 022 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| lotPk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| variantFk | reference → ENT-CAT-002 | yes | CAT Variant | HARD-FK | متغيّر الصنف | Variant |
| code | text (40) | yes | entered | the lot number; unique per Variant (RULE-INV-036) | رقم التشغيلة | Lot number |
| nameAr | text (100) | no | — | displays `code` when empty (DEFAULT D-02) | وصف التشغيلة بالعربية | Lot description (Arabic) |
| nameEn | text (100) | no | — | displays `code` when empty | وصف التشغيلة بالإنجليزية | Lot description (English) |
| expiryDate | date | yes | — | POL-INV-006 | تاريخ انتهاء الصلاحية | Expiry date |
| firstReceivedAt | date-time | yes | — | first receipt | تاريخ أول استلام | First received |
| nearExpiryNotifiedAt | date-time | no | — | set when the near-expiry alert is sent (REQ-INV-117) | تاريخ تنبيه قرب الانتهاء | Near-expiry alert sent |
| isActiveFl | flag | yes | — | standard; false once the lot holds no stock in any warehouse | نشط | Active |

### ENT-INV-013 — Stock valuation / تقييم المخزون

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | SHARED (owner) | no | read, search (system-maintained by stock moves) | read by SLS (margin), INS; ACC receives costs through the stock-move event | module-registry ENTITIES OWNED; POL-INV-021; INV-P09 |

One record per Variant across the tenant.

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| stockValuationPk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| variantFk | reference → ENT-CAT-002 | yes | CAT Variant | one record per Variant | متغيّر الصنف | Variant |
| totalOnHandQty | decimal (3) | yes | — | sum of on-hand over all warehouses and lots | إجمالي الكمية | Total quantity |
| averageUnitCost | decimal (4) | yes | ≥ 0 | perpetual weighted average (REQ-INV-059) | متوسط تكلفة الوحدة | Average unit cost |
| stockValue | decimal (2) | yes | totalOnHandQty × averageUnitCost | — | قيمة المخزون | Stock value |
| lastMoveAt | date-time | yes | — | — | آخر حركة | Last move |
| docDate | date | yes | — | date of the first valued move (standard field) | تاريخ أول تقييم | First valued |

### ENT-INV-014 — Item stock settings / إعدادات مخزون الصنف

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| config | PRIVATE (read by other modules only through INV receipt and issue checks) | no | create, read, update | — | POL-INV-006, 009, 010; DEFAULT D-03 (CAT declares no lot or dual-quantity marker — contract-cat CON-CAT-001, CON-CAT-008) |

One record per STANDARD Item; an Item without a record is neither lot-tracked nor dual-quantity.

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| itemStockSettingsPk | reference (BIGINT) | yes | identity | — | المعرّف | Identifier |
| key | text (40) | yes | the Item's code (CON-CAT-001) | system-filled, read-only | مفتاح الإعداد | Setting key |
| valueAr | text (100) | yes | — | system-filled tracking summary, for example «تشغيلة وصلاحية» | ملخص التتبع بالعربية | Tracking summary (Arabic) |
| valueEn | text (100) | yes | — | system-filled tracking summary, for example "Lot and expiry" | ملخص التتبع بالإنجليزية | Tracking summary (English) |
| itemFk | reference → ENT-CAT-001 | yes | STANDARD Item | HARD-FK; unique (RULE-INV-045) | الصنف | Item |
| lotTrackedFl | flag | yes | false by default | `lotExpiry` on only (RULE-INV-044) | يُتتبَّع بالتشغيلة | Tracked by lot |
| dualQuantityFl | flag | yes | false by default | `dualQuantity` on only (RULE-INV-044) | كمية مزدوجة | Dual quantity |
| measureUomCode | lookup | no | CAT unit-of-measure lookup (consumed — owner CAT) | required when dualQuantityFl is true (RULE-INV-041) | وحدة القياس الثانية | Measure unit |
| isActiveFl | flag | yes | — | standard | نشط | Active |

## A4 — Functional requirements (EARS) and acceptance criteria

Priorities come from the stories (INV-P11). Capability flags are read through the FeatureFlags API (REQ-INV-132); the negative-stock setting and the near-expiry horizon are given `cu` settings `INV_ALLOW_NEGATIVE_STOCK` and `INV_NEAR_EXPIRY_DAYS` (module-registry AUTO; DEFAULT D-04).

### A4.1 — Warehouses

### REQ-INV-001 — Seed the default warehouse
  Pattern    : event
  Statement  : When a tenant is provisioned, the system shall create one active default warehouse named «المستودع الرئيسي» / "Main warehouse" under the default branch.
  Traces     : US-INV-004
  Entities   : ENT-INV-001, ENT-INV-002
  Rationale  : Stock can be received and sold with no warehouse setup.
  Source     : POL-INV-003; module-registry AUTO (TenantProvisioningContributor)
  Priority   : HIGH

#### AC-INV-001 — [REQ-INV-001]
  Given  : a tenant that is being provisioned
  When   : provisioning completes
  Then   : the tenant has exactly 1 Warehouse, with isActiveFl true, isDefaultFl true, nameAr «المستودع الرئيسي», nameEn "Main warehouse" and branchFk equal to the default Branch

### REQ-INV-002 — Seed the default warehouse once
  Pattern    : unwanted
  Statement  : If tenant provisioning runs again for a tenant that already has a Warehouse, then the system shall create no further Warehouse.
  Traces     : US-INV-004
  Entities   : ENT-INV-001
  Rationale  : A repeated provisioning step never duplicates the default.
  Source     : POL-INV-003; DEFAULT D-08 (idempotent provisioning)
  Priority   : HIGH

#### AC-INV-002 — [REQ-INV-002]
  Given  : a tenant with 1 Warehouse
  When   : the provisioning contribution runs a second time
  Then   : the tenant still has exactly 1 Warehouse and its warehousePk is unchanged

### REQ-INV-003 — Create a warehouse
  Pattern    : event
  Statement  : When an Owner saves a new warehouse, the system shall create it as active with a code from the number series INV_WAREHOUSE.
  Traces     : US-INV-007
  Entities   : ENT-INV-001
  Rationale  : A growing business adds locations without migration.
  Source     : US-INV-007; RULE-INV-001; NUMBERING rule
  Priority   : HIGH

#### AC-INV-003 — [REQ-INV-003]
  Given  : a tenant with 1 active Warehouse
  When   : the Owner saves a new warehouse with nameAr «مستودع الفرع»
  Then   : a second Warehouse exists with isActiveFl true, isDefaultFl false and a code issued by series INV_WAREHOUSE

#### AC-INV-004 — [REQ-INV-003]
  Given  : an active Warehouse with nameAr «مستودع الفرع»
  When   : the Owner saves a new warehouse with nameAr «مستودع الفرع»
  Then   : nothing is saved and the message «يوجد مستودع آخر بهذا الاسم.» / "Another warehouse already has this name." is shown (RULE-INV-002)

### REQ-INV-004 — Update a warehouse
  Pattern    : event
  Statement  : When an Owner saves changes to a warehouse, the system shall update its nameAr, nameEn and branchFk and keep its code unchanged.
  Traces     : US-INV-007
  Entities   : ENT-INV-001
  Rationale  : Details stay current; the code is a fixed reference.
  Source     : US-INV-007; RULE-INV-001
  Priority   : HIGH

#### AC-INV-005 — [REQ-INV-004]
  Given  : a Warehouse with code WH-0002
  When   : the Owner changes its nameEn to "North warehouse" and saves
  Then   : the Warehouse has nameEn "North warehouse" and code WH-0002

### REQ-INV-005 — Deactivate a warehouse
  Pattern    : event
  Statement  : When an Owner deactivates a warehouse, the system shall set its isActiveFl to false and keep it readable by its identifier.
  Traces     : US-INV-007
  Entities   : ENT-INV-001
  Rationale  : A location taken out of use leaves new selections while its history stays.
  Source     : US-INV-007; RULE-INV-005 (ARCH-5)
  Priority   : HIGH

#### AC-INV-006 — [REQ-INV-005]
  Given  : a non-default Warehouse with no stock and no ACTIVE reservation
  When   : the Owner deactivates it
  Then   : its isActiveFl is false, it is absent from the active warehouse list and a read by its identifier still returns it

### REQ-INV-006 — Refuse an unsafe warehouse deactivation
  Pattern    : unwanted
  Statement  : If an Owner deactivates the default warehouse or a warehouse that holds non-zero stock or an ACTIVE reservation, then the system shall refuse the deactivation.
  Traces     : US-INV-007
  Entities   : ENT-INV-001, ENT-INV-003, ENT-INV-005
  Rationale  : Stock is never stranded in a location nobody can select.
  Source     : RULE-INV-003, RULE-INV-004
  Priority   : HIGH

#### AC-INV-007 — [REQ-INV-006]
  Given  : the default Warehouse
  When   : the Owner deactivates it
  Then   : it stays active and the message «لا يمكن إيقاف المستودع الافتراضي. اجعل مستودعًا آخر افتراضيًا أولًا.» / "The default warehouse cannot be deactivated. Set another warehouse as the default first." is shown

#### AC-INV-008 — [REQ-INV-006]
  Given  : a non-default Warehouse with an on-hand quantity of 5 for one Variant
  When   : the Owner deactivates it
  Then   : it stays active and the message «لا يمكن إيقاف مستودع فيه مخزون أو حجوزات قائمة. انقل المخزون أو سوِّه أولًا.» / "A warehouse that holds stock or open reservations cannot be deactivated. Transfer or adjust the stock first." is shown

### REQ-INV-007 — Reactivate a warehouse
  Pattern    : event
  Statement  : When an Owner reactivates an inactive warehouse, the system shall set its isActiveFl to true.
  Traces     : US-INV-007
  Entities   : ENT-INV-001
  Rationale  : A location can return to use with its history.
  Source     : US-INV-007
  Priority   : HIGH

#### AC-INV-009 — [REQ-INV-007]
  Given  : an inactive Warehouse
  When   : the Owner reactivates it
  Then   : its isActiveFl is true and it appears in the active warehouse list

### REQ-INV-008 — Set a warehouse as the default
  Pattern    : event
  Statement  : When an Owner sets an active warehouse as the default, the system shall set its isDefaultFl to true and clear isDefaultFl on the previous default in the same transaction.
  Traces     : US-INV-007, US-INV-006
  Entities   : ENT-INV-001
  Rationale  : Exactly one default receives stock when no warehouse is chosen.
  Source     : RULE-INV-006
  Priority   : HIGH

#### AC-INV-010 — [REQ-INV-008]
  Given  : default Warehouse A and active Warehouse B
  When   : the Owner sets B as the default
  Then   : B has isDefaultFl true, A has isDefaultFl false, and exactly 1 Warehouse of the tenant has isDefaultFl true

#### AC-INV-011 — [REQ-INV-008]
  Given  : an inactive Warehouse C
  When   : the Owner sets C as the default
  Then   : nothing changes and the message «المستودع الافتراضي يجب أن يكون نشطًا.» / "The default warehouse must be active." is shown (RULE-INV-006)

### REQ-INV-009 — Search and read warehouses
  Pattern    : event
  Statement  : When a user searches warehouses, the system shall return the page of warehouses matching code, name, branch and active state, ordered by code.
  Traces     : US-INV-007
  Entities   : ENT-INV-001
  Rationale  : The Owner finds and maintains locations.
  Source     : US-INV-007
  Priority   : HIGH

#### AC-INV-012 — [REQ-INV-009]
  Given  : 3 Warehouses, 1 of them inactive
  When   : a user searches with active state "active only"
  Then   : the result holds the 2 active Warehouses ordered by code

### REQ-INV-010 — Hide the warehouse selector while one warehouse
  Pattern    : state
  Statement  : While the tenant has exactly one active warehouse, the system shall hide the warehouse selector on every inventory screen.
  Traces     : US-INV-006
  Entities   : ENT-INV-001
  Rationale  : Only meaningful choices are shown.
  Source     : POL-INV-004; DP §5 G8
  Priority   : HIGH

#### AC-INV-013 — [REQ-INV-010]
  Given  : a tenant with exactly 1 active Warehouse
  When   : a Warehouse user opens the stock adjustment entry
  Then   : no warehouse field is displayed and the adjustment is saved with warehouseFk equal to the default Warehouse

### REQ-INV-011 — Hide the transfer action while one warehouse
  Pattern    : state
  Statement  : While the tenant has exactly one active warehouse, the system shall hide the transfer menu entry and the transfer action.
  Traces     : US-INV-006
  Entities   : ENT-INV-001, ENT-INV-006
  Rationale  : No dead menus.
  Source     : POL-INV-004; KB §6.3
  Priority   : HIGH

#### AC-INV-014 — [REQ-INV-011]
  Given  : a tenant with exactly 1 active Warehouse
  When   : a Warehouse user opens the inventory menu
  Then   : the menu shows no transfer entry

### REQ-INV-012 — Hide warehouse report filters while one warehouse
  Pattern    : state
  Statement  : While the tenant has exactly one active warehouse, the system shall hide the warehouse filter and the warehouse column on the stock balance, move history, lot and near-expiry screens.
  Traces     : US-INV-006
  Entities   : ENT-INV-001, ENT-INV-003, ENT-INV-004, ENT-INV-012
  Rationale  : Reports show only meaningful dimensions.
  Source     : POL-INV-004
  Priority   : HIGH

#### AC-INV-015 — [REQ-INV-012]
  Given  : a tenant with exactly 1 active Warehouse
  When   : a user opens the stock balance screen
  Then   : the search area shows no warehouse filter and the result shows no warehouse column

### REQ-INV-013 — Apply the default warehouse while one warehouse
  Pattern    : state
  Statement  : While the tenant has exactly one active warehouse, the system shall assign the default warehouse to every stock document and every cross-module stock request that names no warehouse.
  Traces     : US-INV-006, US-INV-005
  Entities   : ENT-INV-001, ENT-INV-004, ENT-INV-005, ENT-INV-008, ENT-INV-010
  Rationale  : The warehouse is recorded even when nobody chooses it.
  Source     : POL-INV-002, POL-INV-004
  Priority   : HIGH

#### AC-INV-016 — [REQ-INV-013]
  Given  : a tenant with exactly 1 active Warehouse W
  When   : SLS submits an issue with no warehouse
  Then   : the recorded Stock move has warehouseFk W

### REQ-INV-014 — Refuse a transfer while one warehouse
  Pattern    : unwanted
  Statement  : If a transfer is submitted while the tenant has fewer than two active warehouses, then the system shall refuse it.
  Traces     : US-INV-006
  Entities   : ENT-INV-006
  Rationale  : A hidden action is also closed at the backend.
  Source     : POL-INV-004; RULE-INV-016
  Priority   : HIGH

#### AC-INV-017 — [REQ-INV-014]
  Given  : a tenant with exactly 1 active Warehouse
  When   : a transfer is submitted directly to the backend
  Then   : nothing is saved and the message «التحويل يحتاج إلى مستودعين نشطين مختلفين.» / "A transfer needs two different active warehouses." is returned

### REQ-INV-015 — Reveal the warehouse dimension on the second warehouse
  Pattern    : event
  Statement  : When the count of active warehouses rises from one to two, the system shall show the controls that REQ-INV-010, REQ-INV-011 and REQ-INV-012 hide, with no data migration and no re-setup.
  Traces     : US-INV-007
  Entities   : ENT-INV-001
  Rationale  : Multi-warehouse work starts as soon as it is needed.
  Source     : POL-INV-005; KB §6.8
  Priority   : HIGH

#### AC-INV-018 — [REQ-INV-015]
  Given  : a tenant with 1 active Warehouse and 40 Stock moves
  When   : the Owner creates a second Warehouse and a Warehouse user reloads the inventory menu
  Then   : the transfer entry, the warehouse selector and the warehouse filter are displayed, and the 40 Stock moves keep their warehouseFk unchanged

### REQ-INV-016 — Record the warehouse on every stock move
  Pattern    : ubiquitous
  Statement  : The system shall record a warehouse on every stock move.
  Traces     : US-INV-005
  Entities   : ENT-INV-004
  Rationale  : A second warehouse can be added later with no migration.
  Source     : POL-INV-002; KB §6.8
  Priority   : HIGH

#### AC-INV-019 — [REQ-INV-016]
  Given  : any recorded Stock move
  When   : it is read
  Then   : its warehouseFk references an existing Warehouse

### REQ-INV-017 — Refuse a stock request without a warehouse when several are active
  Pattern    : unwanted
  Statement  : If a stock document or cross-module stock request names no warehouse while the tenant has two or more active warehouses, then the system shall refuse it.
  Traces     : US-INV-005
  Entities   : ENT-INV-001, ENT-INV-004
  Rationale  : The default applies only when there is nothing to choose.
  Source     : RULE-INV-008
  Priority   : HIGH

#### AC-INV-020 — [REQ-INV-017]
  Given  : a tenant with 2 active Warehouses
  When   : PUR submits a receipt with no warehouse
  Then   : no Stock move is recorded and the message «اختر المستودع.» / "Choose the warehouse." is returned

### REQ-INV-018 — Refuse an inactive warehouse
  Pattern    : unwanted
  Statement  : If a stock document or cross-module stock request names an inactive warehouse, then the system shall refuse it.
  Traces     : US-INV-005, US-INV-007
  Entities   : ENT-INV-001
  Rationale  : A location out of use takes no new stock.
  Source     : RULE-INV-007
  Priority   : HIGH

#### AC-INV-021 — [REQ-INV-018]
  Given  : an inactive Warehouse W2
  When   : SLS submits an issue naming W2
  Then   : no Stock move is recorded and the message «المستودع غير نشط.» / "The warehouse is inactive." is returned

### REQ-INV-019 — Offer the active warehouse list to other modules
  Pattern    : event
  Statement  : When another module requests the active warehouses, the system shall return each active warehouse with its identifier, code, names, branch and default flag, together with the active count.
  Traces     : US-INV-006
  Entities   : ENT-INV-001
  Rationale  : PUR, SLS and SFR hide their own warehouse selectors while the count is 1.
  Source     : POL-INV-004; module-registry (Warehouse read by PUR, SLS, SFR)
  Priority   : HIGH

#### AC-INV-022 — [REQ-INV-019]
  Given  : 2 active Warehouses and 1 inactive Warehouse
  When   : SLS requests the active warehouses
  Then   : the answer lists 2 Warehouses and an active count of 2

### A4.2 — Branches

### REQ-INV-020 — Seed the default branch
  Pattern    : event
  Statement  : When a tenant is provisioned, the system shall create one active default branch named «الفرع الرئيسي» / "Main branch".
  Traces     : US-INV-009
  Entities   : ENT-INV-002
  Rationale  : Documents can be issued with no branch setup.
  Source     : POL-INV-015; INV-P07
  Priority   : HIGH

#### AC-INV-023 — [REQ-INV-020]
  Given  : a tenant that is being provisioned
  When   : provisioning completes
  Then   : the tenant has exactly 1 Branch, with isActiveFl true, isDefaultFl true, nameAr «الفرع الرئيسي» and nameEn "Main branch"

### REQ-INV-021 — Seed the default branch once
  Pattern    : unwanted
  Statement  : If tenant provisioning runs again for a tenant that already has a Branch, then the system shall create no further Branch.
  Traces     : US-INV-009
  Entities   : ENT-INV-002
  Rationale  : A repeated provisioning step never duplicates the default.
  Source     : POL-INV-015; DEFAULT D-08
  Priority   : HIGH

#### AC-INV-024 — [REQ-INV-021]
  Given  : a tenant with 1 Branch
  When   : the provisioning contribution runs a second time
  Then   : the tenant still has exactly 1 Branch and its branchPk is unchanged

### REQ-INV-022 — Create a branch
  Pattern    : event
  Statement  : When an Owner saves a new branch, the system shall create it as active with a code from the number series INV_BRANCH.
  Traces     : US-INV-011
  Entities   : ENT-INV-002
  Rationale  : A new outlet is added without migration.
  Source     : US-INV-011; RULE-INV-010
  Priority   : HIGH

#### AC-INV-025 — [REQ-INV-022]
  Given  : a tenant with 1 active Branch
  When   : the Owner saves a new branch with nameAr «فرع المعادي»
  Then   : a second Branch exists with isActiveFl true, isDefaultFl false and a code issued by series INV_BRANCH

#### AC-INV-026 — [REQ-INV-022]
  Given  : an active Branch with nameAr «فرع المعادي»
  When   : the Owner saves a new branch with nameAr «فرع المعادي»
  Then   : nothing is saved and the message «يوجد فرع آخر بهذا الاسم.» / "Another branch already has this name." is shown (RULE-INV-011)

### REQ-INV-023 — Update a branch
  Pattern    : event
  Statement  : When an Owner saves changes to a branch, the system shall update its nameAr and nameEn and keep its code unchanged.
  Traces     : US-INV-011
  Entities   : ENT-INV-002
  Rationale  : Details stay current; the code is a fixed reference.
  Source     : US-INV-011; RULE-INV-010
  Priority   : HIGH

#### AC-INV-027 — [REQ-INV-023]
  Given  : a Branch with code BR-0002
  When   : the Owner changes its nameEn to "Maadi branch" and saves
  Then   : the Branch has nameEn "Maadi branch" and code BR-0002

### REQ-INV-024 — Deactivate a branch
  Pattern    : event
  Statement  : When an Owner deactivates a branch, the system shall set its isActiveFl to false and keep it readable by its identifier.
  Traces     : US-INV-011
  Entities   : ENT-INV-002
  Rationale  : An outlet taken out of use leaves new selections while documents that cite it stay readable.
  Source     : US-INV-011; RULE-INV-014 (ARCH-5)
  Priority   : HIGH

#### AC-INV-028 — [REQ-INV-024]
  Given  : a non-default Branch with no active Warehouse
  When   : the Owner deactivates it
  Then   : its isActiveFl is false, it is absent from the active branch list and a read by its identifier still returns it

### REQ-INV-025 — Refuse an unsafe branch deactivation
  Pattern    : unwanted
  Statement  : If an Owner deactivates the default branch or a branch that an active warehouse belongs to, then the system shall refuse the deactivation.
  Traces     : US-INV-011
  Entities   : ENT-INV-002, ENT-INV-001
  Rationale  : An active warehouse always belongs to an active branch.
  Source     : RULE-INV-012, RULE-INV-013
  Priority   : HIGH

#### AC-INV-029 — [REQ-INV-025]
  Given  : the default Branch
  When   : the Owner deactivates it
  Then   : it stays active and the message «لا يمكن إيقاف الفرع الافتراضي. اجعل فرعًا آخر افتراضيًا أولًا.» / "The default branch cannot be deactivated. Set another branch as the default first." is shown

#### AC-INV-030 — [REQ-INV-025]
  Given  : a non-default Branch with 1 active Warehouse
  When   : the Owner deactivates it
  Then   : it stays active and the message «لا يمكن إيقاف فرع تتبعه مستودعات نشطة. انقل المستودعات إلى فرع آخر أو أوقفها أولًا.» / "A branch with active warehouses cannot be deactivated. Move the warehouses to another branch or deactivate them first." is shown

### REQ-INV-026 — Reactivate a branch
  Pattern    : event
  Statement  : When an Owner reactivates an inactive branch, the system shall set its isActiveFl to true.
  Traces     : US-INV-011
  Entities   : ENT-INV-002
  Rationale  : An outlet can return to use.
  Source     : US-INV-011
  Priority   : HIGH

#### AC-INV-031 — [REQ-INV-026]
  Given  : an inactive Branch
  When   : the Owner reactivates it
  Then   : its isActiveFl is true and it appears in the active branch list

### REQ-INV-027 — Set a branch as the default
  Pattern    : event
  Statement  : When an Owner sets an active branch as the default, the system shall set its isDefaultFl to true and clear isDefaultFl on the previous default in the same transaction.
  Traces     : US-INV-011
  Entities   : ENT-INV-002
  Rationale  : Exactly one default branch applies when none is chosen.
  Source     : RULE-INV-015
  Priority   : HIGH

#### AC-INV-032 — [REQ-INV-027]
  Given  : default Branch A and active Branch B
  When   : the Owner sets B as the default
  Then   : B has isDefaultFl true, A has isDefaultFl false, and exactly 1 Branch of the tenant has isDefaultFl true

### REQ-INV-028 — Search and read branches
  Pattern    : event
  Statement  : When a user searches branches, the system shall return the page of branches matching code, name and active state, ordered by code.
  Traces     : US-INV-011
  Entities   : ENT-INV-002
  Rationale  : The Owner finds and maintains outlets.
  Source     : US-INV-011
  Priority   : HIGH

#### AC-INV-033 — [REQ-INV-028]
  Given  : 3 Branches, 1 of them inactive
  When   : a user searches with active state "active only"
  Then   : the result holds the 2 active Branches ordered by code

### REQ-INV-029 — Hide the branch selector while one branch
  Pattern    : state
  Statement  : While the tenant has exactly one active branch, the system shall hide the branch selector on every inventory screen.
  Traces     : US-INV-010
  Entities   : ENT-INV-002
  Rationale  : Only meaningful choices are shown.
  Source     : POL-INV-016
  Priority   : HIGH

#### AC-INV-034 — [REQ-INV-029]
  Given  : a tenant with exactly 1 active Branch
  When   : the Owner opens the warehouse entry
  Then   : no branch field is displayed

### REQ-INV-030 — Hide branch report filters while one branch
  Pattern    : state
  Statement  : While the tenant has exactly one active branch, the system shall hide the branch filter and the branch column on the warehouse search.
  Traces     : US-INV-010
  Entities   : ENT-INV-002, ENT-INV-001
  Rationale  : Reports show only meaningful dimensions.
  Source     : POL-INV-016
  Priority   : HIGH

#### AC-INV-035 — [REQ-INV-030]
  Given  : a tenant with exactly 1 active Branch
  When   : the Owner opens the warehouse search
  Then   : the search area shows no branch filter and the result shows no branch column

### REQ-INV-031 — Assign the default branch to a warehouse
  Pattern    : unwanted
  Statement  : If a warehouse is saved without a branch, then the system shall assign the default branch to it.
  Traces     : US-INV-010
  Entities   : ENT-INV-001, ENT-INV-002
  Rationale  : The branch dimension is recorded even when nobody chooses it.
  Source     : POL-INV-015, POL-INV-016; DP §5 G8
  Priority   : HIGH

#### AC-INV-036 — [REQ-INV-031]
  Given  : a tenant whose default Branch is B1
  When   : the Owner saves a new warehouse without a branch
  Then   : the new Warehouse has branchFk B1

### REQ-INV-032 — Reveal the branch dimension on the second branch
  Pattern    : event
  Statement  : When the count of active branches rises from one to two, the system shall show the controls that REQ-INV-029 and REQ-INV-030 hide, with no data migration and no re-setup.
  Traces     : US-INV-011
  Entities   : ENT-INV-002
  Rationale  : Multi-branch work starts as soon as it is needed.
  Source     : POL-INV-017
  Priority   : HIGH

#### AC-INV-037 — [REQ-INV-032]
  Given  : a tenant with 1 active Branch and 2 Warehouses
  When   : the Owner creates a second Branch and reloads the warehouse entry
  Then   : the branch field and the branch filter are displayed, and both Warehouses keep their branchFk unchanged

### REQ-INV-033 — Offer the active branch list to other modules
  Pattern    : event
  Statement  : When another module requests the active branches, the system shall return each active branch with its identifier, code, names and default flag, together with the active count.
  Traces     : US-INV-010
  Entities   : ENT-INV-002
  Rationale  : PUR, SLS, STL and TAX hide their own branch selectors while the count is 1.
  Source     : POL-INV-016; module-registry (Branch read by PUR, SLS, STL, TAX, INS)
  Priority   : HIGH

#### AC-INV-038 — [REQ-INV-033]
  Given  : 1 active Branch
  When   : SLS requests the active branches
  Then   : the answer lists 1 Branch with isDefaultFl true and an active count of 1

### REQ-INV-034 — Publish warehouse and branch changes
  Pattern    : event
  Statement  : When a warehouse or a branch is created, updated, deactivated, reactivated or set as the default, the system shall publish one inventory master-change event after the change commits.
  Traces     : US-INV-007, US-INV-011
  Entities   : ENT-INV-001, ENT-INV-002
  Rationale  : Consumers refresh their selectors and counts without polling (ARCH-5: notify, never cascade).
  Source     : RULE-INV-005, RULE-INV-014; module-registry AUTO (events)
  Priority   : HIGH

#### AC-INV-039 — [REQ-INV-034]
  Given  : an active non-default Warehouse
  When   : the Owner deactivates it
  Then   : exactly 1 inventory master-change event with entity ENT-INV-001, the warehousePk and change kind DEACTIVATED is delivered after commit, and none is delivered if the change rolls back

### A4.3 — One stock and balances

### REQ-INV-035 — One stock for every channel
  Pattern    : ubiquitous
  Statement  : The system shall keep one stock balance per Variant, warehouse and lot that the moves of back-office documents, point-of-sale sales and storefront orders all update.
  Traces     : US-INV-001
  Entities   : ENT-INV-003, ENT-INV-004
  Rationale  : The platform promise is one catalogue, one stock and one customer base.
  Source     : POL-INV-001; KB §1
  Priority   : HIGH

#### AC-INV-040 — [REQ-INV-035]
  Given  : Variant V with on-hand 10 in Warehouse W
  When   : a POS sale issues 2 and a storefront order issues 3 of V in W
  Then   : the single Stock balance of V in W shows on-hand 5

### REQ-INV-036 — Update the balance with the move
  Pattern    : event
  Statement  : When a stock move is recorded, the system shall change the on-hand quantity of its Stock balance in the same transaction.
  Traces     : US-INV-001, US-INV-002
  Entities   : ENT-INV-003, ENT-INV-004
  Rationale  : A balance never disagrees with the sum of its moves.
  Source     : POL-INV-001
  Priority   : HIGH

#### AC-INV-041 — [REQ-INV-036]
  Given  : Variant V with on-hand 4 in Warehouse W
  When   : a RECEIPT move of 6 for V in W is recorded and its transaction commits
  Then   : the Stock balance of V in W shows on-hand 10, and the sum of all moves of V in W (in minus out) equals 10

### REQ-INV-037 — Keep concurrent moves consistent
  Pattern    : unwanted
  Statement  : If two stock moves of the same Variant and warehouse are submitted at the same time, then the system shall apply both so that the on-hand quantity equals the sum of the moves.
  Traces     : US-INV-001
  Entities   : ENT-INV-003, ENT-INV-004
  Rationale  : Channels selling the same goods at the same moment never lose an update.
  Source     : POL-INV-001; DEFAULT D-09
  Priority   : HIGH

#### AC-INV-042 — [REQ-INV-037]
  Given  : Variant V with on-hand 20 in Warehouse W
  When   : 10 issues of 1 for V in W are submitted in parallel from POS and storefront
  Then   : all 10 are recorded and the Stock balance shows on-hand 10

### REQ-INV-038 — Compute available quantity
  Pattern    : ubiquitous
  Statement  : The system shall compute the available quantity of a Variant in a warehouse as its on-hand quantity outside expired lots minus the remaining quantity of its ACTIVE reservations.
  Traces     : US-INV-002, US-INV-003
  Entities   : ENT-INV-003, ENT-INV-005, ENT-INV-012
  Rationale  : What can still be promised excludes what is promised and what cannot be sold.
  Source     : POL-INV-001, POL-INV-022; INV-P10
  Priority   : HIGH

#### AC-INV-043 — [REQ-INV-038]
  Given  : Variant V in Warehouse W with on-hand 12, of which 2 are in an expired Lot, and ACTIVE reservations of 3
  When   : the availability of V in W is read
  Then   : on-hand is 12, reserved is 3 and available is 7

### REQ-INV-039 — Search stock balances
  Pattern    : event
  Statement  : When a user searches stock balances, the system shall return per Variant and warehouse the on-hand, reserved and available quantities, filtered by Variant, Item, warehouse and lot.
  Traces     : US-INV-002
  Entities   : ENT-INV-003, ENT-INV-005
  Rationale  : Sellers and Warehouse users promise and supply goods with confidence.
  Source     : US-INV-002
  Priority   : HIGH

#### AC-INV-044 — [REQ-INV-039]
  Given  : Variant V with on-hand 5 in W1 and 8 in W2, and an ACTIVE reservation of 2 in W2
  When   : a Seller/Cashier searches balances for V
  Then   : 2 rows are returned: W1 on-hand 5, reserved 0, available 5; W2 on-hand 8, reserved 2, available 6

### REQ-INV-040 — Offer availability to other modules
  Pattern    : event
  Statement  : When another module requests the availability of a Variant, the system shall return its on-hand, reserved and available quantities per active warehouse and in total.
  Traces     : US-INV-002, US-INV-001
  Entities   : ENT-INV-003, ENT-INV-005
  Rationale  : SLS and SFR show and check availability from the same stock.
  Source     : POL-INV-001; module-registry (Stock balance read by SLS, SFR, INS)
  Priority   : HIGH

#### AC-INV-045 — [REQ-INV-040]
  Given  : Variant V available 5 in W1 and 6 in W2
  When   : SFR requests the availability of V
  Then   : the answer lists W1 available 5, W2 available 6 and a total available of 11

### REQ-INV-041 — Answer zero for a Variant with no stock record
  Pattern    : unwanted
  Statement  : If an availability or balance request names an active Variant that has no Stock balance, then the system shall return zero on-hand, zero reserved and zero available quantities.
  Traces     : US-INV-002
  Entities   : ENT-INV-003
  Rationale  : A new Variant is a valid answer, not an error.
  Source     : DEFAULT D-10
  Priority   : HIGH

#### AC-INV-046 — [REQ-INV-041]
  Given  : an active Variant V that has never moved
  When   : SLS requests the availability of V
  Then   : the answer shows on-hand 0, reserved 0 and available 0 with no error

### REQ-INV-042 — Bundle availability follows its components
  Pattern    : optional
  Statement  : Where the bundles capability flag is on, the system shall compute the available quantity of a bundle Variant in a warehouse as the smallest whole number obtained by dividing each component Variant's available quantity by its component quantity.
  Traces     : US-INV-020
  Entities   : ENT-INV-003
  Rationale  : A bundle holds no stock; its availability always follows its components.
  Source     : POL-INV-014; INV-P06; CON-CAT-006
  Priority   : HIGH

#### AC-INV-047 — [REQ-INV-042]
  Given  : bundles on; Bundle B made of 2 × Variant C1 and 1 × Variant C2; in W, C1 available 7 and C2 available 5
  When   : the availability of B in W is read
  Then   : B available is 3 (7 ÷ 2 = 3.5 → 3; 5 ÷ 1 = 5; smallest 3)

### A4.4 — Reservations

### REQ-INV-043 — Create a reservation
  Pattern    : event
  Statement  : When SLS or SFR requests a reservation for a Variant quantity in a warehouse against a source document, the system shall create an ACTIVE Reservation with that quantity.
  Traces     : US-INV-003
  Entities   : ENT-INV-005
  Rationale  : Goods of an order are set aside until fulfilled or cancelled.
  Source     : module-registry AUTO (SLS and SFR create reservations through INV)
  Priority   : HIGH

#### AC-INV-048 — [REQ-INV-043]
  Given  : Variant V available 10 in W
  When   : SFR requests a reservation of 4 of V in W for storefront order SO-100
  Then   : an ACTIVE Reservation with originalQty 4, reservedQty 4, shortfallQty 0 and sourceDocNo SO-100 exists, and V available in W is 6

### REQ-INV-044 — Refuse a reservation beyond availability while negative stock is disallowed
  Pattern    : unwanted
  Statement  : If a reservation request exceeds the available quantity while the tenant disallows negative stock, then the system shall refuse the reservation.
  Traces     : US-INV-003
  Entities   : ENT-INV-005, ENT-INV-003
  Rationale  : The same goods are not promised twice.
  Source     : POL-INV-001, POL-INV-018; RULE-INV-020; DEFAULT D-11
  Priority   : HIGH

#### AC-INV-049 — [REQ-INV-044]
  Given  : INV_ALLOW_NEGATIVE_STOCK false; Variant V available 3 in W
  When   : SLS requests a reservation of 5 of V in W
  Then   : no Reservation is created and the message «الكمية المتاحة لا تكفي للحجز. المتاح: 3.» / "Not enough available stock to reserve. Available: 3." is returned

### REQ-INV-045 — Accept a reservation with a shortfall while negative stock is allowed
  Pattern    : unwanted
  Statement  : If a reservation request exceeds the available quantity while the tenant allows negative stock, then the system shall create the Reservation with the uncovered quantity recorded as its shortfall.
  Traces     : US-INV-003
  Entities   : ENT-INV-005
  Rationale  : A tenant that sells before loading stock is not stopped; the caller sees the shortfall and decides.
  Source     : POL-INV-018 rationale; INV-P08; DEFAULT D-11
  Priority   : HIGH

#### AC-INV-050 — [REQ-INV-045]
  Given  : INV_ALLOW_NEGATIVE_STOCK true; Variant V available 3 in W
  When   : SLS requests a reservation of 5 of V in W
  Then   : an ACTIVE Reservation with reservedQty 5 and shortfallQty 2 is created and the answer carries shortfallQty 2

### REQ-INV-046 — Release a reservation
  Pattern    : event
  Statement  : When SLS or SFR releases an ACTIVE reservation, the system shall set its status to RELEASED and its remaining quantity to zero.
  Traces     : US-INV-003
  Entities   : ENT-INV-005
  Rationale  : A cancelled order returns its goods to availability.
  Source     : US-INV-003
  Priority   : HIGH

#### AC-INV-051 — [REQ-INV-046]
  Given  : an ACTIVE Reservation of 4 of V in W, V available 6
  When   : SFR releases it
  Then   : the Reservation has statusCode RELEASED, reservedQty 0 and a closedAt time, and V available in W is 10

### REQ-INV-047 — Consume a reservation by its issue
  Pattern    : event
  Statement  : When an issue references an ACTIVE reservation, the system shall reduce the reservation's remaining quantity by the issued quantity and set its status to CONSUMED when the remainder reaches zero.
  Traces     : US-INV-003
  Entities   : ENT-INV-005, ENT-INV-004
  Rationale  : Fulfilment turns the promise into a move without counting it twice.
  Source     : US-INV-003
  Priority   : HIGH

#### AC-INV-052 — [REQ-INV-047]
  Given  : an ACTIVE Reservation with reservedQty 4
  When   : SLS submits an issue of 3 referencing it, then another issue of 1 referencing it
  Then   : after the first issue reservedQty is 1 and statusCode ACTIVE; after the second reservedQty is 0 and statusCode CONSUMED

### REQ-INV-048 — Refuse changes to a closed reservation
  Pattern    : unwanted
  Statement  : If a release or an issue references a reservation that is not ACTIVE, then the system shall refuse the request.
  Traces     : US-INV-003
  Entities   : ENT-INV-005
  Rationale  : A closed reservation cannot be released or consumed twice.
  Source     : RULE-INV-021
  Priority   : HIGH

#### AC-INV-053 — [REQ-INV-048]
  Given  : a Reservation with statusCode RELEASED
  When   : SLS submits an issue referencing it
  Then   : no Stock move is recorded and the message «الحجز مغلق ولا يمكن استخدامه.» / "The reservation is closed and cannot be used." is returned

### REQ-INV-049 — Reserve a bundle through its components
  Pattern    : optional
  Statement  : Where the bundles capability flag is on, the system shall reserve a bundle Variant by creating one Reservation per component Variant for the bundle quantity multiplied by the component quantity.
  Traces     : US-INV-020
  Entities   : ENT-INV-005
  Rationale  : A bundle holds no stock; its components are set aside.
  Source     : POL-INV-014; CON-CAT-006
  Priority   : HIGH

#### AC-INV-054 — [REQ-INV-049]
  Given  : bundles on; Bundle B made of 2 × C1 and 1 × C2
  When   : SLS requests a reservation of 3 of B in W
  Then   : 2 ACTIVE Reservations exist: C1 reservedQty 6 and C2 reservedQty 3, each with bundleVariantFk B

### REQ-INV-050 — Read the reservations of a source document
  Pattern    : event
  Statement  : When SLS or SFR requests the reservations of a source document, the system shall return each Reservation of that document with its Variant, warehouse, status, remaining quantity and shortfall.
  Traces     : US-INV-003
  Entities   : ENT-INV-005
  Rationale  : The owning document shows what is set aside for it.
  Source     : US-INV-003
  Priority   : HIGH

#### AC-INV-055 — [REQ-INV-050]
  Given  : order SO-100 with 2 Reservations, 1 ACTIVE and 1 CONSUMED
  When   : SLS requests the reservations of SO-100
  Then   : both are returned with their statusCode values ACTIVE and CONSUMED

### A4.5 — Receipts, issues and stock moves

### REQ-INV-051 — Record a receipt
  Pattern    : event
  Statement  : When PUR, SLS or SFR submits a receipt or a customer return, the system shall record one RECEIPT or RETURN_IN Stock move per line in the named warehouse at the line's unit cost.
  Traces     : US-INV-001, US-INV-016
  Entities   : ENT-INV-004, ENT-INV-003
  Rationale  : Goods coming in update the shared stock and its cost.
  Source     : POL-INV-001; module-registry AUTO (receipt cost supplied by PUR)
  Priority   : HIGH

#### AC-INV-056 — [REQ-INV-051]
  Given  : Variant V with on-hand 0 in W
  When   : PUR submits receipt GR-7 with 1 line of 10 of V at unit cost 25.0000
  Then   : 1 RECEIPT Stock move of 10 with unitCost 25.0000, moveValue 250.00 and sourceDocNo GR-7 is recorded, and V on-hand in W is 10

### REQ-INV-052 — Record an issue
  Pattern    : event
  Statement  : When PUR, SLS or SFR submits an issue or a return to supplier, the system shall record one ISSUE or RETURN_OUT Stock move per line in the named warehouse at the Variant's current average cost.
  Traces     : US-INV-001, US-INV-016
  Entities   : ENT-INV-004, ENT-INV-003, ENT-INV-013
  Rationale  : Goods leaving reduce the shared stock at a consistent cost.
  Source     : POL-INV-001, POL-INV-021
  Priority   : HIGH

#### AC-INV-057 — [REQ-INV-052]
  Given  : Variant V with on-hand 10 in W and averageUnitCost 25.0000
  When   : SLS submits POS sale P-31 issuing 4 of V in W
  Then   : 1 ISSUE Stock move of 4 with unitCost 25.0000 and moveValue 100.00 is recorded, and V on-hand in W is 6

#### AC-INV-058 — [REQ-INV-052]
  Given  : an issue line for a Variant whose Item is inactive in CAT
  When   : SLS submits the issue
  Then   : no Stock move is recorded and the message «متغيّر الصنف غير نشط.» / "The Variant is inactive." is returned (RULE-INV-018)

### REQ-INV-053 — Issue a bundle through its components
  Pattern    : optional
  Statement  : Where the bundles capability flag is on, the system shall issue a bundle Variant by recording one ISSUE Stock move per component Variant for the bundle quantity multiplied by the component quantity.
  Traces     : US-INV-020
  Entities   : ENT-INV-004
  Rationale  : No assembly step is needed and component stock stays true.
  Source     : POL-INV-014; INV-P06; CON-CAT-006
  Priority   : HIGH

#### AC-INV-059 — [REQ-INV-053]
  Given  : bundles on; Bundle B made of 2 × C1 and 1 × C2
  When   : SLS issues 3 of B from W
  Then   : 2 ISSUE Stock moves are recorded — C1 quantity 6 and C2 quantity 3, each with bundleVariantFk B — and no move is recorded for B itself

### REQ-INV-054 — Refuse a bundle without active components
  Pattern    : unwanted
  Statement  : If an issue or reservation names a bundle Variant that has no active component line, then the system shall refuse it.
  Traces     : US-INV-020
  Entities   : ENT-INV-004, ENT-INV-005
  Rationale  : A bundle with nothing to move would sell goods that never leave stock.
  Source     : RULE-INV-022
  Priority   : HIGH

#### AC-INV-060 — [REQ-INV-054]
  Given  : Bundle B whose component lines are all inactive in CAT
  When   : SLS issues 1 of B
  Then   : no Stock move is recorded and the message «الحزمة ليس لها مكوّنات نشطة.» / "The bundle has no active components." is returned

### REQ-INV-055 — Keep stock moves unchanged
  Pattern    : unwanted
  Statement  : If a user or a module attempts to change or delete a recorded stock move, then the system shall refuse the attempt.
  Traces     : US-INV-013
  Entities   : ENT-INV-004
  Rationale  : Every balance stays explainable; corrections go through a stock adjustment.
  Source     : RULE-INV-023; INV-P10
  Priority   : HIGH

#### AC-INV-061 — [REQ-INV-055]
  Given  : a recorded Stock move
  When   : a change to its quantity is submitted
  Then   : the move is unchanged and the message «لا تُعدَّل حركات المخزون. صحّح الرصيد بتسوية مخزون.» / "Stock moves cannot be changed. Correct the balance with a stock adjustment." is returned

### REQ-INV-056 — Record the source document on every move
  Pattern    : ubiquitous
  Statement  : The system shall record on every stock move the source document type, identifier and number that caused it.
  Traces     : US-INV-013
  Entities   : ENT-INV-004
  Rationale  : Any balance can be explained from its documents.
  Source     : US-INV-013
  Priority   : HIGH

#### AC-INV-062 — [REQ-INV-056]
  Given  : a posted Transfer TR-0005
  When   : its Stock moves are read
  Then   : each has sourceDocTypeCode INV_TRANSFER, sourceDocRef equal to the transferPk and sourceDocNo TR-0005

### REQ-INV-057 — Publish the stock-move event
  Pattern    : event
  Statement  : When a stock move commits, the system shall publish one stock-move event carrying the move's type, Variant, warehouse, lot, quantity, unit cost, value and source document.
  Traces     : US-INV-016
  Entities   : ENT-INV-004
  Rationale  : The accounts follow without manual entries (ACC posts from the event); SWR and INS react.
  Source     : module-registry AUTO (stock moves published as domain events, each with its unit cost); KB §3 D7
  Priority   : HIGH

#### AC-INV-063 — [REQ-INV-057]
  Given  : an issue of 4 at unit cost 25.0000
  When   : its transaction commits
  Then   : exactly 1 stock-move event with quantity 4, unitCost 25.0000 and value 100.00 is delivered after commit, and none is delivered when the transaction rolls back

### REQ-INV-058 — Record a repeated submission once
  Pattern    : unwanted
  Statement  : If a receipt, issue or reservation request repeats an idempotency key already recorded, then the system shall return the first result and record nothing new.
  Traces     : US-INV-019, US-INV-001
  Entities   : ENT-INV-004, ENT-INV-005
  Rationale  : An offline sale synchronised twice is never counted twice.
  Source     : RULE-INV-024; erp-core common idempotency
  Priority   : HIGH

#### AC-INV-064 — [REQ-INV-058]
  Given  : a POS sale already synchronised with idempotency key K1, which recorded 1 ISSUE of 2
  When   : SLS submits the same sale again with key K1
  Then   : the answer equals the first answer and the count of Stock moves with key K1 is still 1

### A4.6 — Valuation

### REQ-INV-059 — Recalculate the weighted-average cost on receipt
  Pattern    : event
  Statement  : When a stock move brings a quantity in at a unit cost, the system shall set the Variant's average unit cost across the tenant to the total value after the move divided by the total quantity after the move.
  Traces     : US-INV-016
  Entities   : ENT-INV-013, ENT-INV-004
  Rationale  : Stock is valued at the perpetual weighted-average cost of each Variant across the tenant.
  Source     : POL-INV-021; INV-P09; IAS 2 ¶25 (not live-verified)
  Priority   : HIGH

#### AC-INV-065 — [REQ-INV-059]
  Given  : Variant V with total quantity 10 at average 20.0000 (W1 6, W2 4)
  When   : a RECEIPT of 10 at unit cost 26.0000 is recorded in W1
  Then   : V has totalOnHandQty 20, averageUnitCost 23.0000 and stockValue 460.00

### REQ-INV-060 — Restart the average when total stock is not positive
  Pattern    : unwanted
  Statement  : If a quantity comes in at a unit cost while the Variant's total quantity across the tenant is zero or below, then the system shall set the average unit cost to that move's unit cost.
  Traces     : US-INV-016
  Entities   : ENT-INV-013
  Rationale  : An average over negative stock is meaningless; the newest known cost is the best estimate.
  Source     : DEFAULT D-12
  Priority   : HIGH

#### AC-INV-066 — [REQ-INV-060]
  Given  : Variant V with totalOnHandQty −3 and averageUnitCost 18.0000
  When   : a RECEIPT of 10 at unit cost 22.0000 is recorded
  Then   : V has totalOnHandQty 7 and averageUnitCost 22.0000

### REQ-INV-061 — Value outgoing moves at the current average
  Pattern    : ubiquitous
  Statement  : The system shall value every ISSUE, RETURN_OUT, TRANSFER_OUT, TRANSFER_IN and ADJUSTMENT_OUT move at the Variant's average unit cost at the time of the move.
  Traces     : US-INV-016
  Entities   : ENT-INV-004, ENT-INV-013
  Rationale  : Each sale earns against one consistent cost.
  Source     : POL-INV-021
  Priority   : HIGH

#### AC-INV-067 — [REQ-INV-061]
  Given  : Variant V with averageUnitCost 23.0000
  When   : an ADJUSTMENT_OUT of 2 is recorded
  Then   : the move has unitCost 23.0000 and moveValue 46.00, and V's averageUnitCost stays 23.0000

### REQ-INV-062 — Leave the value unchanged by a transfer
  Pattern    : event
  Statement  : When a transfer is posted, the system shall keep the Variant's average unit cost and total stock value unchanged.
  Traces     : US-INV-008, US-INV-016
  Entities   : ENT-INV-013, ENT-INV-006
  Rationale  : A move between warehouses does not change what the goods are worth.
  Source     : POL-INV-021; INV-P09 (transfers carry cost unchanged)
  Priority   : HIGH

#### AC-INV-068 — [REQ-INV-062]
  Given  : Variant V with totalOnHandQty 20, averageUnitCost 23.0000 and stockValue 460.00
  When   : a Transfer of 5 of V from W1 to W2 is posted
  Then   : V still has totalOnHandQty 20, averageUnitCost 23.0000 and stockValue 460.00

### REQ-INV-063 — Read the stock valuation
  Pattern    : event
  Statement  : When the Owner or another module requests the valuation of a Variant, the system shall return its total quantity, average unit cost and stock value.
  Traces     : US-INV-016
  Entities   : ENT-INV-013
  Rationale  : The Owner knows what stock is worth; SLS computes margin.
  Source     : module-registry (Stock valuation read by SLS, ACC, INS)
  Priority   : HIGH

#### AC-INV-069 — [REQ-INV-063]
  Given  : Variant V with totalOnHandQty 20 and averageUnitCost 23.0000
  When   : SLS requests the valuation of V
  Then   : the answer shows totalOnHandQty 20, averageUnitCost 23.0000 and stockValue 460.00

### REQ-INV-064 — Refuse an incoming quantity with no known cost
  Pattern    : unwanted
  Statement  : If a receipt line or an increase line carries no unit cost and the Variant has never been valued, then the system shall refuse the line.
  Traces     : US-INV-016
  Entities   : ENT-INV-004, ENT-INV-011, ENT-INV-013
  Rationale  : Stock never enters at an unknown cost.
  Source     : RULE-INV-025
  Priority   : HIGH

#### AC-INV-070 — [REQ-INV-064]
  Given  : Variant V with no Stock valuation record
  When   : SLS submits a RETURN_IN line of 1 of V without a unit cost
  Then   : no Stock move is recorded and the message «لا توجد تكلفة معروفة لهذا المتغيّر. أدخل تكلفة الوحدة.» / "This Variant has no known cost. Enter the unit cost." is returned

### A4.7 — Negative stock

### REQ-INV-065 — Save the negative-stock setting
  Pattern    : event
  Statement  : When the Owner saves the negative-stock setting, the system shall apply the saved value to every stock decrease submitted after the save.
  Traces     : US-INV-017
  Entities   : ENT-INV-004
  Rationale  : The Owner decides whether stock may go below zero.
  Source     : POL-INV-018; INV-P08; module-registry AUTO (cu setting)
  Priority   : HIGH

#### AC-INV-071 — [REQ-INV-065]
  Given  : INV_ALLOW_NEGATIVE_STOCK true and Variant V on-hand 0 in W
  When   : the Owner saves the setting as "not allowed" and SLS then submits an online issue of 1 of V in W
  Then   : the issue is refused with the RULE-INV-019 message

### REQ-INV-066 — Allow negative stock by default
  Pattern    : ubiquitous
  Statement  : The system shall treat negative stock as allowed for a tenant that has never saved the negative-stock setting.
  Traces     : US-INV-017
  Entities   : ENT-INV-004
  Rationale  : A new Owner can start selling before opening stock is loaded.
  Source     : POL-INV-018 rationale; INV-P08 (default allowed)
  Priority   : HIGH

#### AC-INV-072 — [REQ-INV-066]
  Given  : a new tenant that never saved INV_ALLOW_NEGATIVE_STOCK and Variant V on-hand 0 in W
  When   : SLS submits an online issue of 2 of V in W
  Then   : 1 ISSUE Stock move of 2 is recorded and V on-hand in W is −2

### REQ-INV-067 — Block an online decrease below zero when negative stock is disallowed
  Pattern    : unwanted
  Statement  : If an online issue, transfer or stock adjustment would take the on-hand quantity of a Variant in a warehouse below zero while the tenant disallows negative stock, then the system shall refuse it.
  Traces     : US-INV-017
  Entities   : ENT-INV-004, ENT-INV-003
  Rationale  : A tenant that wants strict balances gets them.
  Source     : POL-INV-018; RULE-INV-019
  Priority   : HIGH

#### AC-INV-073 — [REQ-INV-067]
  Given  : INV_ALLOW_NEGATIVE_STOCK false and Variant V on-hand 3 in W
  When   : SLS submits an online issue of 5 of V in W
  Then   : no Stock move is recorded and the message «الرصيد لا يكفي: المتاح في المستودع 3. الرصيد السالب غير مسموح.» / "Not enough stock: 3 on hand in this warehouse. Negative stock is not allowed." is returned

### REQ-INV-068 — Alert the Warehouse role on negative stock
  Pattern    : unwanted
  Statement  : If a stock move takes the on-hand quantity of a Variant in a warehouse from zero or above to below zero, then the system shall send one negative-stock notification to the users holding the Warehouse role.
  Traces     : US-INV-018
  Entities   : ENT-INV-004, ENT-INV-003
  Rationale  : A negative balance is allowed but never silent.
  Source     : POL-INV-019; INV-P08; module-registry (notif)
  Priority   : HIGH

#### AC-INV-074 — [REQ-INV-068]
  Given  : Variant V on-hand 1 in W and 2 users holding the Warehouse role
  When   : an issue of 3 of V in W is recorded and then another issue of 1
  Then   : each of the 2 users receives exactly 1 notification «رصيد سالب: {Variant} في {المستودع} = −2.» / "Negative stock: {Variant} in {Warehouse} = −2.", and the second issue sends none

### REQ-INV-069 — Accept a synchronised offline POS sale
  Pattern    : unwanted
  Statement  : If a synchronised offline point-of-sale sale takes the on-hand quantity of a Variant in a warehouse below zero, then the system shall record the sale's ISSUE moves whatever the negative-stock setting.
  Traces     : US-INV-019
  Entities   : ENT-INV-004
  Rationale  : The goods have already left; rejecting the sale would lose a real transaction.
  Source     : POL-INV-020; INV-P08
  Priority   : HIGH

#### AC-INV-075 — [REQ-INV-069]
  Given  : INV_ALLOW_NEGATIVE_STOCK false and Variant V on-hand 1 in W
  When   : SLS synchronises an offline POS sale of 3 of V in W with offlineFl true
  Then   : 1 ISSUE Stock move of 3 with offlineFl true is recorded, V on-hand in W is −2 and the REQ-INV-068 notification is sent

### A4.8 — Opening stock

### REQ-INV-070 — Post opening stock
  Pattern    : event
  Statement  : When the Owner posts opening stock, the system shall record one ADJUSTMENT_IN Stock move per line at the line's unit cost under a Stock adjustment with reason OPENING_STOCK.
  Traces     : US-INV-012
  Entities   : ENT-INV-010, ENT-INV-011, ENT-INV-004, ENT-INV-013
  Rationale  : Balances and stock value are right from the first day.
  Source     : US-INV-012; INV-P12 (mechanism chosen here — DEFAULT D-13)
  Priority   : HIGH

#### AC-INV-076 — [REQ-INV-070]
  Given  : Variant V with no stock and no valuation
  When   : the Owner posts opening stock with 1 line of 40 of V at unit cost 12.5000
  Then   : a POSTED Stock adjustment with reasonCode OPENING_STOCK exists, 1 ADJUSTMENT_IN move of 40 at 12.5000 is recorded, and V has averageUnitCost 12.5000 and stockValue 500.00

### REQ-INV-071 — Load many opening stock lines at once
  Pattern    : event
  Statement  : When the Owner submits an opening stock load of 1 to 1000 rows, the system shall validate every row and post all rows in one Stock adjustment.
  Traces     : US-INV-012
  Entities   : ENT-INV-010, ENT-INV-011
  Rationale  : Many Items are loaded in one step during setup.
  Source     : US-INV-012; KB §6.9 step 5, §6.10; RULE-INV-027
  Priority   : HIGH

#### AC-INV-077 — [REQ-INV-071]
  Given  : a load of 650 valid rows read from the opening stock sheet
  When   : the Owner submits it
  Then   : 1 POSTED Stock adjustment with 650 lines and 650 ADJUSTMENT_IN moves is recorded

#### AC-INV-078 — [REQ-INV-071]
  Given  : a load of 1001 rows
  When   : the Owner submits it
  Then   : nothing is posted and the message «الحد الأقصى 1000 سطر في التحميل الواحد.» / "At most 1000 rows per load." is returned (RULE-INV-027)

### REQ-INV-072 — Refuse a load with invalid rows
  Pattern    : unwanted
  Statement  : If any row of an opening stock load fails validation, then the system shall post no row and return each failing row number with its message.
  Traces     : US-INV-012
  Entities   : ENT-INV-011
  Rationale  : A partial load would leave balances half right; the Owner fixes the listed rows and resubmits.
  Source     : RULE-INV-028; KB §6.5 (no dead ends)
  Priority   : HIGH

#### AC-INV-079 — [REQ-INV-072]
  Given  : a load of 20 rows where row 7 names Variant code V-404 that does not exist
  When   : the Owner submits it
  Then   : nothing is posted and the answer lists row 7 with «لم يُعثر على متغيّر صنف نشط بهذا الرمز أو الباركود.» / "No active Variant has this code or barcode."

### REQ-INV-073 — Provide the opening stock sheet
  Pattern    : event
  Statement  : When the Owner requests the opening stock sheet, the system shall return a spreadsheet file with the columns Variant code or barcode, quantity, unit cost, lot number, expiry date and measure.
  Traces     : US-INV-012
  Entities   : ENT-INV-011
  Rationale  : The Owner fills a known layout in a spreadsheet program.
  Source     : KB §6.9 step 5 ("import items from Excel"); DEFAULT D-13
  Priority   : HIGH

#### AC-INV-080 — [REQ-INV-073]
  Given  : lotExpiry off and dualQuantity off
  When   : the Owner requests the opening stock sheet
  Then   : an xlsx file is returned with the 3 columns «رمز المتغيّر أو الباركود» / "Variant code or barcode", «الكمية» / "Quantity", «تكلفة الوحدة» / "Unit cost" (the lot, expiry and measure columns appear only with their flags on)

### REQ-INV-074 — Require a cost on every opening stock line
  Pattern    : unwanted
  Statement  : If an opening stock line carries no unit cost, then the system shall refuse the line.
  Traces     : US-INV-012
  Entities   : ENT-INV-011
  Rationale  : Opening stock sets the first average cost; it cannot be unknown.
  Source     : RULE-INV-026; POL-INV-021
  Priority   : HIGH

#### AC-INV-081 — [REQ-INV-074]
  Given  : an opening stock entry with 1 line of 10 of V and an empty unit cost
  When   : the Owner posts it
  Then   : nothing is posted and the message «أدخل تكلفة الوحدة للمخزون الافتتاحي.» / "Enter the unit cost of the opening stock." is shown on the line

### A4.9 — Move history and change history

### REQ-INV-075 — Search stock moves
  Pattern    : event
  Statement  : When a Warehouse user searches stock moves, the system shall return the moves matching Variant, warehouse, lot, move type, document number and date range, newest first, each with its source document type and number.
  Traces     : US-INV-013
  Entities   : ENT-INV-004
  Rationale  : Every move of a Variant can be seen with the document that caused it.
  Source     : US-INV-013
  Priority   : HIGH

#### AC-INV-082 — [REQ-INV-075]
  Given  : Variant V with 3 moves in W during October 2026 (RECEIPT GR-7, ISSUE P-31, ADJUSTMENT_OUT ADJ-0002)
  When   : a Warehouse user searches moves of V in W from 2026-10-01 to 2026-10-31
  Then   : 3 rows are returned newest first, showing document numbers ADJ-0002, P-31 and GR-7 with their document types

### REQ-INV-076 — Show the running balance
  Pattern    : state
  Statement  : While the move search is filtered to one Variant and one warehouse, the system shall show on each move the on-hand quantity after that move.
  Traces     : US-INV-013
  Entities   : ENT-INV-004
  Rationale  : A balance is explained line by line.
  Source     : US-INV-013
  Priority   : HIGH

#### AC-INV-083 — [REQ-INV-076]
  Given  : moves of V in W: RECEIPT 10, ISSUE 4, ADJUSTMENT_OUT 1
  When   : a Warehouse user filters the search to V and W
  Then   : the balance-after column shows 10, 6 and 5 for those moves

### REQ-INV-077 — Record the change history of stock documents
  Pattern    : event
  Statement  : When a Transfer, Stock count, Stock adjustment, Warehouse, Branch, Lot or Item stock settings record is created, changed, posted or cancelled, the system shall record an audit entry with the user, time and changed fields.
  Traces     : US-INV-013
  Entities   : ENT-INV-001, ENT-INV-002, ENT-INV-006, ENT-INV-008, ENT-INV-010, ENT-INV-012, ENT-INV-014
  Rationale  : Every change to a stock document is explained.
  Source     : module-registry DEPENDENCIES (audit: change history of stock documents)
  Priority   : HIGH

#### AC-INV-084 — [REQ-INV-077]
  Given  : a DRAFT Stock adjustment
  When   : a Warehouse user changes a line quantity from 2 to 3 and saves
  Then   : 1 audit entry exists for that adjustment with the user, the time and quantityChange 2 → 3

### A4.10 — Transfers

### REQ-INV-078 — Create a draft transfer
  Pattern    : event
  Statement  : When a Warehouse user saves a new transfer, the system shall create it in status DRAFT with a number from the series INV_TRANSFER.
  Traces     : US-INV-008
  Entities   : ENT-INV-006, ENT-INV-007
  Rationale  : A transfer is prepared before it moves stock.
  Source     : US-INV-008; RULE-INV-030
  Priority   : HIGH

#### AC-INV-085 — [REQ-INV-078]
  Given  : 2 active Warehouses W1 and W2
  When   : a Warehouse user saves a transfer from W1 to W2 with 1 line of 5 of V
  Then   : a Transfer with statusCode DRAFT and a docNo issued by series INV_TRANSFER exists, and no Stock move is recorded

### REQ-INV-079 — Update a draft transfer
  Pattern    : event
  Statement  : When a Warehouse user saves changes to a DRAFT transfer, the system shall replace its warehouses and lines with the saved values.
  Traces     : US-INV-008
  Entities   : ENT-INV-006, ENT-INV-007
  Rationale  : A prepared transfer can be corrected before posting.
  Source     : US-INV-008; KB §6.5 (undo for unposted operations)
  Priority   : HIGH

#### AC-INV-086 — [REQ-INV-079]
  Given  : a DRAFT Transfer with 1 line of 5 of V
  When   : the user changes the line to 7 and adds a line of 2 of V2
  Then   : the Transfer has 2 lines: 7 of V and 2 of V2

### REQ-INV-080 — Post a transfer
  Pattern    : event
  Statement  : When a Warehouse user posts a DRAFT transfer, the system shall record per line one TRANSFER_OUT move in the source warehouse and one TRANSFER_IN move in the destination warehouse at the Variant's average unit cost, and set the status to POSTED.
  Traces     : US-INV-008
  Entities   : ENT-INV-006, ENT-INV-007, ENT-INV-004
  Rationale  : Each warehouse balance shows where the goods really are.
  Source     : US-INV-008; POL-INV-002; INV-P09; DEFAULT D-07 (no in-transit state)
  Priority   : HIGH

#### AC-INV-087 — [REQ-INV-080]
  Given  : V on-hand 10 in W1 and 0 in W2, averageUnitCost 23.0000; a DRAFT Transfer W1 → W2 of 4 of V
  When   : the user posts it
  Then   : the Transfer has statusCode POSTED, a TRANSFER_OUT of 4 at 23.0000 is recorded in W1 and a TRANSFER_IN of 4 at 23.0000 in W2, and V on-hand is 6 in W1 and 4 in W2

#### AC-INV-088 — [REQ-INV-080]
  Given  : a DRAFT Transfer with no lines
  When   : the user posts it
  Then   : it stays DRAFT and the message «أضف سطرًا واحدًا على الأقل قبل الترحيل.» / "Add at least one line before posting." is shown (RULE-INV-031)

### REQ-INV-081 — Cancel a draft transfer
  Pattern    : event
  Statement  : When a Warehouse user cancels a DRAFT transfer, the system shall set its status to CANCELLED and record no stock move.
  Traces     : US-INV-008
  Entities   : ENT-INV-006
  Rationale  : An unposted operation can be undone.
  Source     : KB §6.5
  Priority   : HIGH

#### AC-INV-089 — [REQ-INV-081]
  Given  : a DRAFT Transfer
  When   : the user cancels it
  Then   : its statusCode is CANCELLED and the count of Stock moves with its sourceDocRef is 0

### REQ-INV-082 — Refuse a transfer within one warehouse
  Pattern    : unwanted
  Statement  : If a transfer names the same warehouse as source and destination, then the system shall refuse it.
  Traces     : US-INV-008
  Entities   : ENT-INV-006
  Rationale  : A move inside one warehouse changes nothing.
  Source     : RULE-INV-016
  Priority   : HIGH

#### AC-INV-090 — [REQ-INV-082]
  Given  : 2 active Warehouses
  When   : a user saves a transfer from W1 to W1
  Then   : nothing is saved and the message «التحويل يحتاج إلى مستودعين نشطين مختلفين.» / "A transfer needs two different active warehouses." is shown

### REQ-INV-083 — Refuse a transfer beyond the source balance when negative stock is disallowed
  Pattern    : unwanted
  Statement  : If posting a transfer would take the source warehouse's on-hand quantity of a line's Variant below zero while the tenant disallows negative stock, then the system shall refuse the posting.
  Traces     : US-INV-008
  Entities   : ENT-INV-006, ENT-INV-003
  Rationale  : Strict balances apply to transfers as to sales.
  Source     : POL-INV-018; RULE-INV-019
  Priority   : HIGH

#### AC-INV-091 — [REQ-INV-083]
  Given  : INV_ALLOW_NEGATIVE_STOCK false; V on-hand 3 in W1; a DRAFT Transfer W1 → W2 of 5 of V
  When   : the user posts it
  Then   : it stays DRAFT and the RULE-INV-019 message with on-hand 3 is shown

### REQ-INV-084 — Carry the lot on a transfer
  Pattern    : optional
  Statement  : Where the lotExpiry capability flag is on, the system shall move a lot-tracked transfer line under its named lot so that the lot keeps its expiry date in the destination warehouse.
  Traces     : US-INV-008, US-INV-021
  Entities   : ENT-INV-007, ENT-INV-012, ENT-INV-003
  Rationale  : Lot and expiry follow the goods.
  Source     : POL-INV-006; RULE-INV-037
  Priority   : HIGH

#### AC-INV-092 — [REQ-INV-084]
  Given  : lotExpiry on; lot-tracked V with Lot L1 (expiry 2027-03-01) on-hand 8 in W1
  When   : a Transfer W1 → W2 of 3 of V in L1 is posted
  Then   : the Stock balance of V in W2 for L1 shows on-hand 3, and L1 keeps expiryDate 2027-03-01

#### AC-INV-093 — [REQ-INV-084]
  Given  : lotExpiry on; a transfer line of lot-tracked V with no lot
  When   : the user saves the transfer
  Then   : nothing is saved and the message «اختر التشغيلة لهذا الصنف.» / "Choose the lot for this Item." is shown (RULE-INV-037)

### REQ-INV-085 — Keep a posted or cancelled transfer unchanged
  Pattern    : unwanted
  Statement  : If a user changes or cancels a transfer whose status is POSTED or CANCELLED, then the system shall refuse the change.
  Traces     : US-INV-008
  Entities   : ENT-INV-006
  Rationale  : A posted document is history; corrections use a new document.
  Source     : RULE-INV-029
  Priority   : HIGH

#### AC-INV-094 — [REQ-INV-085]
  Given  : a POSTED Transfer
  When   : a user submits a change to its line quantity
  Then   : the Transfer is unchanged and the message «المستند مُرحَّل أو ملغى ولا يمكن تعديله.» / "The document is posted or cancelled and cannot be changed." is returned

### REQ-INV-086 — Search and read transfers
  Pattern    : event
  Statement  : When a Warehouse user searches transfers, the system shall return the transfers matching number, date range, source warehouse, destination warehouse and document status, newest first.
  Traces     : US-INV-008
  Entities   : ENT-INV-006
  Rationale  : Transfers are found and reviewed.
  Source     : US-INV-008
  Priority   : HIGH

#### AC-INV-095 — [REQ-INV-086]
  Given  : 3 Transfers, 2 POSTED and 1 DRAFT
  When   : a user searches with document status POSTED
  Then   : the 2 POSTED Transfers are returned newest first

### A4.11 — Stock counts

### REQ-INV-087 — Start a stock count
  Pattern    : event
  Statement  : When a Warehouse user starts a stock count for a warehouse, the system shall create it in status DRAFT with a number from the series INV_COUNT.
  Traces     : US-INV-014
  Entities   : ENT-INV-008
  Rationale  : A count is recorded before it changes stock.
  Source     : US-INV-014; RULE-INV-030
  Priority   : HIGH

#### AC-INV-096 — [REQ-INV-087]
  Given  : a tenant with 1 active Warehouse W
  When   : a Warehouse user starts a stock count
  Then   : a Stock count with statusCode DRAFT, warehouseFk W and a docNo issued by series INV_COUNT exists

### REQ-INV-088 — Record a counted line
  Pattern    : event
  Statement  : When a Warehouse user enters a barcode or picks a Variant on a DRAFT count with a counted quantity, the system shall add the quantity to that Variant's line, creating the line when it does not exist.
  Traces     : US-INV-014
  Entities   : ENT-INV-009
  Rationale  : Scanning the same goods twice on the shelf adds up instead of duplicating lines.
  Source     : US-INV-014; CON-CAT-010 (barcode lookup); DEFAULT D-14
  Priority   : HIGH

#### AC-INV-097 — [REQ-INV-088]
  Given  : a DRAFT Stock count with no line for V, and barcode 6221000000017 identifying V
  When   : the user scans 6221000000017 with quantity 1, then again with quantity 2
  Then   : the count has 1 line for V with countedQty 3

#### AC-INV-098 — [REQ-INV-088]
  Given  : a DRAFT Stock count
  When   : the user enters a counted quantity of −1
  Then   : no line changes and the message «الكمية المعدودة لا تكون سالبة.» / "The counted quantity cannot be negative." is shown (RULE-INV-047)

### REQ-INV-089 — Change or remove a counted line
  Pattern    : event
  Statement  : When a Warehouse user changes the counted quantity of a line on a DRAFT count or removes the line, the system shall save the new quantity or delete the line.
  Traces     : US-INV-014
  Entities   : ENT-INV-009
  Rationale  : A miscount is corrected before posting.
  Source     : US-INV-014; KB §6.5
  Priority   : HIGH

#### AC-INV-099 — [REQ-INV-089]
  Given  : a DRAFT count with a line of V countedQty 3
  When   : the user sets the quantity to 5, then removes a line of V2
  Then   : the line of V has countedQty 5 and the count has no line of V2

### REQ-INV-090 — Count from a phone's web browser
  Pattern    : ubiquitous
  Statement  : The system shall let a Warehouse user start a count, record lines and post the count in a web browser whose viewport is 360 CSS pixels wide, without horizontal scrolling and without a native app.
  Traces     : US-INV-014
  Entities   : ENT-INV-008, ENT-INV-009
  Rationale  : Counting happens where the goods are.
  Source     : POL-INV-012; KB §6.7
  Priority   : HIGH

#### AC-INV-100 — [REQ-INV-090]
  Given  : a phone web browser with a 360 × 640 CSS-pixel viewport and no installed app
  When   : a Warehouse user starts a count, scans 3 barcodes and posts the count
  Then   : every step completes on that viewport and the page width never exceeds 360 CSS pixels

### REQ-INV-091 — Report an unknown barcode on a count
  Pattern    : unwanted
  Statement  : If a barcode entered on a count identifies no active Variant, then the system shall add no line and show that the barcode is unknown.
  Traces     : US-INV-014
  Entities   : ENT-INV-009
  Rationale  : A scan never adds the wrong goods; the user can pick the Variant instead.
  Source     : RULE-INV-032; CON-CAT-010 errors
  Priority   : HIGH

#### AC-INV-101 — [REQ-INV-091]
  Given  : a DRAFT count and a barcode 999 that CAT does not know
  When   : the user scans 999
  Then   : no line is added and the message «الباركود غير معروف. اختر متغيّر الصنف يدويًا.» / "Unknown barcode. Pick the Variant by hand." is shown

### REQ-INV-092 — Post a stock count
  Pattern    : event
  Statement  : When a Warehouse user posts a DRAFT count, the system shall record each line's system quantity and difference against the on-hand quantity at posting, create and post one Stock adjustment with reason COUNT_DIFFERENCE for the non-zero differences, and set the count status to POSTED.
  Traces     : US-INV-014, US-INV-015
  Entities   : ENT-INV-008, ENT-INV-009, ENT-INV-010, ENT-INV-011
  Rationale  : A count difference posts through an adjustment document, so every change is explained.
  Source     : module-registry AUTO ("a count difference posts through an adjustment document"); DEFAULT D-15
  Priority   : HIGH

#### AC-INV-102 — [REQ-INV-092]
  Given  : V on-hand 10 and V2 on-hand 4 in W; a DRAFT count with V countedQty 8 and V2 countedQty 4
  When   : the user posts the count
  Then   : the count is POSTED with V systemQty 10 differenceQty −2 and V2 differenceQty 0; 1 POSTED Stock adjustment with reasonCode COUNT_DIFFERENCE and 1 line (V, −2) exists; V on-hand is 8

### REQ-INV-093 — Refuse posting an empty count
  Pattern    : unwanted
  Statement  : If a user posts a count that has no line, then the system shall refuse the posting.
  Traces     : US-INV-014
  Entities   : ENT-INV-008
  Rationale  : An empty count changes nothing and is likely a mistake.
  Source     : RULE-INV-031
  Priority   : HIGH

#### AC-INV-103 — [REQ-INV-093]
  Given  : a DRAFT count with no line
  When   : the user posts it
  Then   : it stays DRAFT and the message «أضف سطرًا واحدًا على الأقل قبل الترحيل.» / "Add at least one line before posting." is shown

### REQ-INV-094 — Cancel a draft count
  Pattern    : event
  Statement  : When a Warehouse user cancels a DRAFT count, the system shall set its status to CANCELLED and change no stock.
  Traces     : US-INV-014
  Entities   : ENT-INV-008
  Rationale  : An unposted operation can be undone.
  Source     : KB §6.5
  Priority   : HIGH

#### AC-INV-104 — [REQ-INV-094]
  Given  : a DRAFT count with 3 lines
  When   : the user cancels it
  Then   : its statusCode is CANCELLED and no Stock adjustment references it

### REQ-INV-095 — Search and read stock counts
  Pattern    : event
  Statement  : When a Warehouse user searches stock counts, the system shall return the counts matching number, date range, warehouse and document status, newest first.
  Traces     : US-INV-014
  Entities   : ENT-INV-008
  Rationale  : Counts are found and reviewed.
  Source     : US-INV-014
  Priority   : HIGH

#### AC-INV-105 — [REQ-INV-095]
  Given  : 2 DRAFT counts and 1 POSTED count
  When   : a user searches with document status DRAFT
  Then   : the 2 DRAFT counts are returned newest first

### REQ-INV-096 — Keep a posted or cancelled count unchanged
  Pattern    : unwanted
  Statement  : If a user changes a line of a count whose status is POSTED or CANCELLED, then the system shall refuse the change.
  Traces     : US-INV-014
  Entities   : ENT-INV-008, ENT-INV-009
  Rationale  : A posted count is history.
  Source     : RULE-INV-029
  Priority   : HIGH

#### AC-INV-106 — [REQ-INV-096]
  Given  : a POSTED count
  When   : a user scans a barcode on it
  Then   : no line changes and the message «المستند مُرحَّل أو ملغى ولا يمكن تعديله.» / "The document is posted or cancelled and cannot be changed." is shown

### A4.12 — Stock adjustments

### REQ-INV-097 — Create a draft adjustment
  Pattern    : event
  Statement  : When a Warehouse user saves a new stock adjustment, the system shall create it in status DRAFT with a number from the series INV_ADJUSTMENT.
  Traces     : US-INV-015
  Entities   : ENT-INV-010, ENT-INV-011
  Rationale  : An adjustment is prepared before it changes stock.
  Source     : US-INV-015; RULE-INV-030
  Priority   : HIGH

#### AC-INV-107 — [REQ-INV-097]
  Given  : an active Warehouse W
  When   : a Warehouse user saves an adjustment with reason DAMAGE and 1 line of V −2
  Then   : a Stock adjustment with statusCode DRAFT and a docNo issued by series INV_ADJUSTMENT exists, and no Stock move is recorded

### REQ-INV-098 — Update a draft adjustment
  Pattern    : event
  Statement  : When a Warehouse user saves changes to a DRAFT adjustment, the system shall replace its reason, note and lines with the saved values.
  Traces     : US-INV-015
  Entities   : ENT-INV-010, ENT-INV-011
  Rationale  : A prepared adjustment can be corrected before posting.
  Source     : KB §6.5
  Priority   : HIGH

#### AC-INV-108 — [REQ-INV-098]
  Given  : a DRAFT adjustment with reason DAMAGE and 1 line of V −2
  When   : the user changes the line to −3 and saves
  Then   : the adjustment has 1 line of V −3

#### AC-INV-109 — [REQ-INV-098]
  Given  : a DRAFT adjustment with a line of V −2
  When   : the user adds a second line of V −1 with no lot
  Then   : nothing is saved and the message «هذا المتغيّر مذكور في سطر آخر بالتشغيلة نفسها.» / "This Variant is already on another line with the same lot." is shown (RULE-INV-048)

### REQ-INV-099 — Post an adjustment
  Pattern    : event
  Statement  : When a Warehouse user posts a DRAFT adjustment, the system shall record one ADJUSTMENT_IN move per positive line and one ADJUSTMENT_OUT move per negative line, and set the status to POSTED.
  Traces     : US-INV-015
  Entities   : ENT-INV-010, ENT-INV-011, ENT-INV-004
  Rationale  : Balances match what is physically there.
  Source     : US-INV-015
  Priority   : HIGH

#### AC-INV-110 — [REQ-INV-099]
  Given  : V on-hand 10 in W at averageUnitCost 23.0000; a DRAFT adjustment with reason DAMAGE and 1 line of V −2
  When   : the user posts it
  Then   : the adjustment is POSTED, 1 ADJUSTMENT_OUT of 2 at 23.0000 is recorded and V on-hand in W is 8

#### AC-INV-111 — [REQ-INV-099]
  Given  : a DRAFT adjustment with 1 line whose quantityChange is 0 and whose measureQtyChange is empty
  When   : the user posts it
  Then   : it stays DRAFT and the message «أدخل تغيّرًا في الكمية أو القياس لا يساوي صفرًا.» / "Enter a non-zero change of quantity or measure." is shown (RULE-INV-034)

### REQ-INV-100 — Cancel a draft adjustment
  Pattern    : event
  Statement  : When a Warehouse user cancels a DRAFT adjustment, the system shall set its status to CANCELLED and record no stock move.
  Traces     : US-INV-015
  Entities   : ENT-INV-010
  Rationale  : An unposted operation can be undone.
  Source     : KB §6.5
  Priority   : HIGH

#### AC-INV-112 — [REQ-INV-100]
  Given  : a DRAFT adjustment
  When   : the user cancels it
  Then   : its statusCode is CANCELLED and the count of Stock moves with its sourceDocRef is 0

### REQ-INV-101 — Require a reason on every adjustment
  Pattern    : unwanted
  Statement  : If a stock adjustment is saved without an active adjustment reason, then the system shall refuse it.
  Traces     : US-INV-015
  Entities   : ENT-INV-010
  Rationale  : Every change of stock is explained.
  Source     : RULE-INV-033; registry LOOKUPS OWNED (inv.adjustment-reason)
  Priority   : HIGH

#### AC-INV-113 — [REQ-INV-101]
  Given  : an adjustment entry with lines and no reason
  When   : the user saves it
  Then   : nothing is saved and the message «اختر سبب التسوية.» / "Choose the adjustment reason." is shown

### REQ-INV-102 — Cost an increase line
  Pattern    : unwanted
  Statement  : If a positive adjustment line carries no unit cost, then the system shall value it at the Variant's current average unit cost.
  Traces     : US-INV-015
  Entities   : ENT-INV-011, ENT-INV-013
  Rationale  : Found goods enter at the known cost; RULE-INV-025 refuses the line when no cost is known.
  Source     : POL-INV-021; RULE-INV-025
  Priority   : HIGH

#### AC-INV-114 — [REQ-INV-102]
  Given  : V with averageUnitCost 23.0000; a DRAFT adjustment with reason COUNT_DIFFERENCE and 1 line of V +2 with no unit cost
  When   : the user posts it
  Then   : 1 ADJUSTMENT_IN of 2 at 23.0000 with value 46.00 is recorded and V's averageUnitCost stays 23.0000

### REQ-INV-103 — Keep a posted or cancelled adjustment unchanged
  Pattern    : unwanted
  Statement  : If a user changes or cancels an adjustment whose status is POSTED or CANCELLED, then the system shall refuse the change.
  Traces     : US-INV-015
  Entities   : ENT-INV-010
  Rationale  : A posted document is history; a new adjustment corrects it.
  Source     : RULE-INV-029
  Priority   : HIGH

#### AC-INV-115 — [REQ-INV-103]
  Given  : a POSTED adjustment
  When   : a user submits a change to its reason
  Then   : the adjustment is unchanged and the message «المستند مُرحَّل أو ملغى ولا يمكن تعديله.» / "The document is posted or cancelled and cannot be changed." is returned

### REQ-INV-104 — Search and read adjustments
  Pattern    : event
  Statement  : When a Warehouse user searches stock adjustments, the system shall return the adjustments matching number, date range, warehouse, reason and document status, newest first.
  Traces     : US-INV-015
  Entities   : ENT-INV-010
  Rationale  : Adjustments are found and reviewed.
  Source     : US-INV-015
  Priority   : HIGH

#### AC-INV-116 — [REQ-INV-104]
  Given  : 2 adjustments with reason DAMAGE and 1 with reason OPENING_STOCK
  When   : a user searches with reason DAMAGE
  Then   : the 2 DAMAGE adjustments are returned newest first

### A4.13 — Lots and expiry

### REQ-INV-105 — Capture lot and expiry on receipt
  Pattern    : optional
  Statement  : Where the lotExpiry capability flag is on, the system shall require a lot number and an expiry date on every receipt line and increase line of a lot-tracked Item.
  Traces     : US-INV-021
  Entities   : ENT-INV-004, ENT-INV-011, ENT-INV-012, ENT-INV-014
  Rationale  : The tenant knows which lot it holds and when it expires.
  Source     : POL-INV-006; RULE-INV-035
  Priority   : MEDIUM

#### AC-INV-117 — [REQ-INV-105]
  Given  : lotExpiry on; lot-tracked Variant V
  When   : PUR submits a receipt line of 10 of V with lot number L-77 and expiry 2027-02-28
  Then   : 1 RECEIPT move of 10 with lotFk L-77 is recorded and the Stock balance of V for L-77 shows on-hand 10

#### AC-INV-118 — [REQ-INV-105]
  Given  : lotExpiry on; lot-tracked Variant V
  When   : PUR submits a receipt line of 10 of V with no lot number
  Then   : no Stock move is recorded and the message «أدخل رقم التشغيلة وتاريخ الصلاحية لهذا الصنف.» / "Enter the lot number and expiry date for this Item." is returned

### REQ-INV-106 — Create or reuse the lot on receipt
  Pattern    : event
  Statement  : When a receipt or increase line names a lot number, the system shall use the Variant's existing Lot with that number or create a new active Lot with that number and expiry date.
  Traces     : US-INV-021
  Entities   : ENT-INV-012
  Rationale  : A lot received twice stays one lot.
  Source     : POL-INV-006
  Priority   : MEDIUM

#### AC-INV-119 — [REQ-INV-106]
  Given  : Variant V with Lot L-77 expiry 2027-02-28
  When   : a second receipt of 5 of V names L-77 with expiry 2027-02-28
  Then   : no new Lot is created and the L-77 balance rises by 5

### REQ-INV-107 — Refuse a lot number with a different expiry
  Pattern    : unwanted
  Statement  : If a receipt or increase line names an existing lot number of the Variant with an expiry date different from the lot's, then the system shall refuse the line.
  Traces     : US-INV-021
  Entities   : ENT-INV-012
  Rationale  : One lot has one expiry date.
  Source     : RULE-INV-036
  Priority   : MEDIUM

#### AC-INV-120 — [REQ-INV-107]
  Given  : Variant V with Lot L-77 expiry 2027-02-28
  When   : a receipt names L-77 with expiry 2027-03-31
  Then   : no Stock move is recorded and the message «التشغيلة L-77 مسجّلة بتاريخ صلاحية 2027-02-28.» / "Lot L-77 is recorded with expiry date 2027-02-28." is returned

### REQ-INV-108 — Correct the expiry date of a lot
  Pattern    : event
  Statement  : When the Owner corrects the expiry date of a lot, the system shall save the new expiry date and apply it to every later availability, FEFO and near-expiry calculation.
  Traces     : US-INV-021
  Entities   : ENT-INV-012
  Rationale  : A typing mistake on receipt is fixed without moving stock.
  Source     : DEFAULT D-16; REQ-INV-077 (audit)
  Priority   : MEDIUM

#### AC-INV-121 — [REQ-INV-108]
  Given  : Lot L-77 with expiry 2027-02-28
  When   : the Owner sets its expiry to 2027-02-20
  Then   : L-77 has expiryDate 2027-02-20 and 1 audit entry records 2027-02-28 → 2027-02-20

### REQ-INV-109 — Search and read lots
  Pattern    : event
  Statement  : When a Warehouse user searches lots, the system shall return the lots matching Variant, lot number, warehouse and expiry date range with their on-hand quantity, ordered by expiry date.
  Traces     : US-INV-021
  Entities   : ENT-INV-012, ENT-INV-003
  Rationale  : The tenant knows which lots it holds.
  Source     : US-INV-021
  Priority   : MEDIUM

#### AC-INV-122 — [REQ-INV-109]
  Given  : Variant V with lots L1 (expiry 2027-01-10, on-hand 4) and L2 (expiry 2026-12-01, on-hand 6)
  When   : a user searches lots of V
  Then   : 2 rows are returned in the order L2, L1 with on-hand 6 and 4

### REQ-INV-110 — Issue the nearest expiry first
  Pattern    : optional
  Statement  : Where the lotExpiry capability flag is on, the system shall draw a sale issue of a lot-tracked Variant from its unexpired lots in the warehouse in ascending order of expiry date.
  Traces     : US-INV-022
  Entities   : ENT-INV-004, ENT-INV-012, ENT-INV-003
  Rationale  : Fewer goods expire on the shelf.
  Source     : POL-INV-007
  Priority   : MEDIUM

#### AC-INV-123 — [REQ-INV-110]
  Given  : lotExpiry on; in W, V has L1 (expiry 2027-01-10, on-hand 4) and L2 (expiry 2026-12-01, on-hand 6); today 2026-11-01
  When   : SLS issues 8 of V from W for a sale
  Then   : 2 ISSUE moves are recorded: 6 from L2, then 2 from L1

### REQ-INV-111 — Post a lot shortfall without a lot
  Pattern    : unwanted
  Statement  : If the unexpired lots of a lot-tracked Variant in a warehouse cannot cover an accepted sale issue, then the system shall record the uncovered quantity as an ISSUE move without a lot.
  Traces     : US-INV-022
  Entities   : ENT-INV-004, ENT-INV-003
  Rationale  : Allowed negative stock and offline sales still have a balance to land on; the next count corrects it.
  Source     : POL-INV-018, POL-INV-020; DEFAULT D-17
  Priority   : MEDIUM

#### AC-INV-124 — [REQ-INV-111]
  Given  : lotExpiry on; negative stock allowed; in W, V has only L1 on-hand 2
  When   : SLS issues 5 of V from W for a sale
  Then   : 1 ISSUE of 2 from L1 and 1 ISSUE of 3 with no lot are recorded, and V on-hand without a lot in W is −3

### REQ-INV-112 — Issue stock without a lot first
  Pattern    : event
  Statement  : When a sale issue draws a lot-tracked Variant that has positive on-hand quantity without a lot in the warehouse, the system shall issue that quantity before any lot.
  Traces     : US-INV-022
  Entities   : ENT-INV-004, ENT-INV-003
  Rationale  : Stock held before lot tracking was switched on is the oldest stock.
  Source     : DEFAULT D-18
  Priority   : MEDIUM

#### AC-INV-125 — [REQ-INV-112]
  Given  : lotExpiry on; in W, V has 3 on-hand without a lot and L2 on-hand 6
  When   : SLS issues 4 of V from W for a sale
  Then   : 1 ISSUE of 3 with no lot and 1 ISSUE of 1 from L2 are recorded

### REQ-INV-113 — Keep expired lots out of every sale
  Pattern    : unwanted
  Statement  : If a lot's expiry date is before today, then the system shall exclude that lot from every sale issue and from FEFO selection.
  Traces     : US-INV-024
  Entities   : ENT-INV-012, ENT-INV-004
  Rationale  : Expired goods are never sold.
  Source     : POL-INV-022; RULE-INV-038
  Priority   : MEDIUM

#### AC-INV-126 — [REQ-INV-113]
  Given  : today 2026-11-01; in W, V has L0 (expiry 2026-10-30, on-hand 5) and L2 (expiry 2026-12-01, on-hand 6)
  When   : SLS issues 3 of V from W for a sale
  Then   : 1 ISSUE of 3 from L2 is recorded and L0 stays at on-hand 5

### REQ-INV-114 — Let an expired lot leave stock only by adjustment
  Pattern    : unwanted
  Statement  : If an issue or a return to supplier names an expired lot, then the system shall refuse it.
  Traces     : US-INV-024
  Entities   : ENT-INV-012, ENT-INV-004
  Rationale  : An expired lot leaves stock only through a stock adjustment.
  Source     : INV-P10; RULE-INV-039
  Priority   : MEDIUM

#### AC-INV-127 — [REQ-INV-114]
  Given  : Lot L0 of V expired on 2026-10-30
  When   : PUR submits a return to supplier of 2 of V from L0
  Then   : no Stock move is recorded and the message «التشغيلة منتهية الصلاحية. اشطبها بتسوية مخزون.» / "The lot has expired. Write it off with a stock adjustment." is returned

#### AC-INV-128 — [REQ-INV-114]
  Given  : Lot L0 of V expired, on-hand 5 in W
  When   : a Warehouse user posts an adjustment with reason EXPIRY_WRITE_OFF and 1 line of V in L0 −5
  Then   : 1 ADJUSTMENT_OUT of 5 from L0 is recorded and L0 on-hand in W is 0

### REQ-INV-115 — Keep expired lots visible
  Pattern    : state
  Statement  : While an expired lot has non-zero on-hand quantity, the system shall show it in the stock balance, lot and near-expiry screens marked as expired.
  Traces     : US-INV-024
  Entities   : ENT-INV-012, ENT-INV-003
  Rationale  : Expired goods are still accounted for until written off.
  Source     : INV-P10 (stays in stock, visible in near-expiry reporting)
  Priority   : MEDIUM

#### AC-INV-129 — [REQ-INV-115]
  Given  : Lot L0 of V expired, on-hand 5 in W
  When   : a Warehouse user opens the near-expiry view
  Then   : L0 is listed with on-hand 5 and the mark «منتهية الصلاحية» / "Expired"

### REQ-INV-116 — Report stock near expiry
  Pattern    : optional
  Statement  : Where the lotExpiry capability flag is on, the system shall list the lots with non-zero on-hand quantity whose expiry date falls within the near-expiry horizon from today, ordered by expiry date.
  Traces     : US-INV-023
  Entities   : ENT-INV-012, ENT-INV-003
  Rationale  : The tenant acts before stock is lost.
  Source     : POL-INV-008
  Priority   : MEDIUM

#### AC-INV-130 — [REQ-INV-116]
  Given  : lotExpiry on; INV_NEAR_EXPIRY_DAYS 30; today 2026-11-01; lots L2 (expiry 2026-11-20, on-hand 6), L3 (expiry 2027-01-15, on-hand 4)
  When   : a Warehouse user opens the near-expiry view
  Then   : L2 is listed with 19 days left and L3 is not listed

### REQ-INV-117 — Notify lots entering the near-expiry horizon
  Pattern    : event
  Statement  : When the daily near-expiry check runs for a tenant with lotExpiry on, the system shall send one notification to the users holding the Warehouse role for each lot with non-zero on-hand quantity that has entered the horizon and has not been notified before.
  Traces     : US-INV-023
  Entities   : ENT-INV-012, ENT-INV-003
  Rationale  : The Warehouse role is told, not only shown.
  Source     : POL-INV-008; module-registry (notif: near-expiry alerts to the Warehouse role); DEFAULT D-19
  Priority   : MEDIUM

#### AC-INV-131 — [REQ-INV-117]
  Given  : lotExpiry on; INV_NEAR_EXPIRY_DAYS 30; Lot L2 of V expiry 2026-11-20, on-hand 6, nearExpiryNotifiedAt empty
  When   : the daily check runs on 2026-11-01 and again on 2026-11-02
  Then   : each Warehouse-role user receives exactly 1 notification «تشغيلة قريبة الانتهاء: {Variant} — L2 تنتهي في 2026-11-20 (الكمية 6).» / "Lot near expiry: {Variant} — L2 expires on 2026-11-20 (quantity 6).", and L2 has nearExpiryNotifiedAt set on 2026-11-01

### REQ-INV-118 — Save the near-expiry horizon
  Pattern    : event
  Statement  : When the Owner saves a near-expiry horizon of 1 to 365 whole days, the system shall use it for the near-expiry list and the daily check from the next calculation.
  Traces     : US-INV-023
  Entities   : ENT-INV-012
  Rationale  : The business chooses its horizon.
  Source     : POL-INV-008; module-registry (cu: near-expiry horizon); DEFAULT D-04 (30 days when never saved)
  Priority   : MEDIUM

#### AC-INV-132 — [REQ-INV-118]
  Given  : INV_NEAR_EXPIRY_DAYS never saved
  When   : the Owner opens the inventory settings
  Then   : the horizon shows 30 days; after the Owner saves 60, the near-expiry list includes lots expiring within 60 days

### REQ-INV-119 — Refuse a horizon outside the allowed range
  Pattern    : unwanted
  Statement  : If the Owner saves a near-expiry horizon that is not a whole number from 1 to 365, then the system shall keep the saved horizon unchanged.
  Traces     : US-INV-023
  Entities   : ENT-INV-012
  Rationale  : A zero or negative horizon would hide every warning.
  Source     : DEFAULT D-04
  Priority   : MEDIUM

#### AC-INV-133 — [REQ-INV-119]
  Given  : INV_NEAR_EXPIRY_DAYS 30
  When   : the Owner saves 0
  Then   : the horizon stays 30 and the message «أدخل عدد أيام من 1 إلى 365.» / "Enter a number of days from 1 to 365." is shown

### A4.14 — Dual quantity

### REQ-INV-120 — Record the measure on every move of a dual-quantity Item
  Pattern    : optional
  Statement  : Where the dualQuantity capability flag is on, the system shall record on every stock move of a dual-quantity Item its measure quantity in the Item's measure unit beside its count quantity.
  Traces     : US-INV-025
  Entities   : ENT-INV-004, ENT-INV-014
  Rationale  : The tenant knows how many pieces and how much weight or length it holds.
  Source     : POL-INV-009
  Priority   : LOW

#### AC-INV-134 — [REQ-INV-120]
  Given  : dualQuantity on; Item "Cotton fabric" dual-quantity with measure unit metre
  When   : PUR receives 3 rolls measuring 120.500 metres
  Then   : 1 RECEIPT move with quantity 3 and measureQty 120.500 is recorded

### REQ-INV-121 — Show the measure in balances
  Pattern    : optional
  Statement  : Where the dualQuantity capability flag is on, the system shall show the on-hand measure quantity and its unit beside the on-hand quantity of every dual-quantity Item in balances and availability answers.
  Traces     : US-INV-025
  Entities   : ENT-INV-003
  Rationale  : Both quantities are visible where stock is checked.
  Source     : POL-INV-009
  Priority   : LOW

#### AC-INV-135 — [REQ-INV-121]
  Given  : dualQuantity on; "Cotton fabric" on-hand 3 rolls and 120.500 metres in W
  When   : a user searches balances for it
  Then   : the row shows on-hand 3 and measure 120.500 metres

### REQ-INV-122 — Refuse a dual-quantity move without its measure
  Pattern    : unwanted
  Statement  : If a receipt, issue, transfer line or count line of a dual-quantity Item carries no measure quantity while the dualQuantity capability flag is on, then the system shall refuse it.
  Traces     : US-INV-025
  Entities   : ENT-INV-004, ENT-INV-007, ENT-INV-009
  Rationale  : Both quantities stay complete.
  Source     : RULE-INV-040
  Priority   : LOW

#### AC-INV-136 — [REQ-INV-122]
  Given  : dualQuantity on; dual-quantity "Cotton fabric"
  When   : SLS issues 1 roll with no measure
  Then   : no Stock move is recorded and the message «أدخل القياس (الوزن أو الطول) لهذا الصنف.» / "Enter the measure (weight or length) for this Item." is returned

### REQ-INV-123 — Adjust the measure alone
  Pattern    : optional
  Statement  : Where the dualQuantity capability flag is on, the system shall post an adjustment line of a dual-quantity Item whose count change is zero and whose measure change is non-zero as a measure-only move.
  Traces     : US-INV-025
  Entities   : ENT-INV-011, ENT-INV-004
  Rationale  : Weight lost or length cut off is corrected without changing the piece count.
  Source     : POL-INV-009; RULE-INV-034
  Priority   : LOW

#### AC-INV-137 — [REQ-INV-123]
  Given  : dualQuantity on; "Cotton fabric" on-hand 3 rolls and 120.500 metres in W
  When   : a user posts an adjustment line with quantityChange 0 and measureQtyChange −0.500
  Then   : on-hand stays 3 rolls and the measure is 120.000 metres

### A4.15 — Item stock settings and capability flags

### REQ-INV-124 — Mark an Item as tracked by lot
  Pattern    : complex
  Statement  : Where the lotExpiry capability flag is on, when the Owner marks a STANDARD Item as tracked by lot, the system shall save its Item stock settings with lotTrackedFl true.
  Traces     : US-INV-021
  Entities   : ENT-INV-014
  Rationale  : Lot capture applies only to the Items that need it.
  Source     : POL-INV-006 ("an item tracked by lot"); DEFAULT D-03
  Priority   : MEDIUM

#### AC-INV-138 — [REQ-INV-124]
  Given  : lotExpiry on; Item "Milk 1L" with no Item stock settings
  When   : the Owner marks it as tracked by lot
  Then   : Item stock settings exist for it with lotTrackedFl true, dualQuantityFl false and key equal to the Item's code

#### AC-INV-139 — [REQ-INV-124]
  Given  : lotExpiry on; a BUNDLE Item
  When   : the Owner marks it as tracked by lot
  Then   : nothing is saved and the message «الحزمة لا تحمل مخزونًا؛ تُضبط إعدادات المخزون على مكوّناتها.» / "A bundle holds no stock; set stock settings on its components." is shown (RULE-INV-045)

### REQ-INV-125 — Mark an Item as dual-quantity
  Pattern    : complex
  Statement  : Where the dualQuantity capability flag is on, when the Owner marks a STANDARD Item as dual-quantity with a measure unit, the system shall save its Item stock settings with dualQuantityFl true and that measure unit.
  Traces     : US-INV-025
  Entities   : ENT-INV-014
  Rationale  : The second quantity applies only to the Items that need it.
  Source     : POL-INV-009; DEFAULT D-03
  Priority   : LOW

#### AC-INV-140 — [REQ-INV-125]
  Given  : dualQuantity on; Item "Steel bar" with no Item stock settings
  When   : the Owner marks it as dual-quantity with measure unit KG
  Then   : Item stock settings exist for it with dualQuantityFl true and measureUomCode KG

#### AC-INV-141 — [REQ-INV-125]
  Given  : dualQuantity on
  When   : the Owner marks "Steel bar" as dual-quantity with no measure unit
  Then   : nothing is saved and the message «اختر وحدة القياس الثانية (وزن أو طول).» / "Choose the measure unit (weight or length)." is shown (RULE-INV-041)

### REQ-INV-126 — Refuse clearing lot tracking while lots hold stock
  Pattern    : unwanted
  Statement  : If the Owner clears lot tracking on an Item while any lot of its Variants has non-zero on-hand quantity, then the system shall refuse the change.
  Traces     : US-INV-026
  Entities   : ENT-INV-014, ENT-INV-003, ENT-INV-012
  Rationale  : Lot history is never lost.
  Source     : POL-INV-010; RULE-INV-042
  Priority   : MEDIUM

#### AC-INV-142 — [REQ-INV-126]
  Given  : lot-tracked "Milk 1L" with Lot L2 on-hand 6
  When   : the Owner clears its lot tracking
  Then   : lotTrackedFl stays true and the message «لا يمكن إلغاء تتبع التشغيلة: توجد تشغيلات فيها مخزون (1). اشطبها أو اصرفها أولًا.» / "Lot tracking cannot be cleared: lots still hold stock (1). Write them off or issue them first." is shown

### REQ-INV-127 — Refuse clearing dual quantity while a measure is held
  Pattern    : unwanted
  Statement  : If the Owner clears dual quantity on an Item while any Stock balance of its Variants has a non-zero on-hand measure, then the system shall refuse the change.
  Traces     : US-INV-026
  Entities   : ENT-INV-014, ENT-INV-003
  Rationale  : Measure history is never lost.
  Source     : POL-INV-010; RULE-INV-043
  Priority   : LOW

#### AC-INV-143 — [REQ-INV-127]
  Given  : dual-quantity "Cotton fabric" with on-hand measure 120.000 metres in W
  When   : the Owner clears its dual quantity
  Then   : dualQuantityFl stays true and the message «لا يمكن إلغاء الكمية المزدوجة: يوجد مخزون بقياس غير صفري. سوِّ القياس إلى صفر أولًا.» / "Dual quantity cannot be cleared: stock still holds a non-zero measure. Adjust the measure to zero first." is shown

### REQ-INV-128 — Refuse Item stock settings while their flag is off
  Pattern    : unwanted
  Statement  : If the Owner sets lot tracking while the lotExpiry capability flag is off, or dual quantity while the dualQuantity capability flag is off, then the system shall refuse the change.
  Traces     : US-INV-027
  Entities   : ENT-INV-014
  Rationale  : Behaviour follows the capability flags only.
  Source     : POL-INV-011; RULE-INV-044
  Priority   : HIGH

#### AC-INV-144 — [REQ-INV-128]
  Given  : lotExpiry off
  When   : the Owner sends a request marking "Milk 1L" as tracked by lot
  Then   : nothing is saved and the message «فعِّل مفتاح القدرة «التشغيلة والصلاحية» أولًا.» / "Switch on the lot and expiry capability flag first." is returned

### REQ-INV-129 — Read Item stock settings
  Pattern    : event
  Statement  : When a user or another module requests the stock settings of an Item, the system shall return its lotTrackedFl, dualQuantityFl and measure unit, or false, false and empty when the Item has no record.
  Traces     : US-INV-021, US-INV-025
  Entities   : ENT-INV-014
  Rationale  : PUR and SLS know which lines need a lot or a measure before they submit.
  Source     : POL-INV-006, POL-INV-009
  Priority   : MEDIUM

#### AC-INV-145 — [REQ-INV-129]
  Given  : Item "Rice 5kg" with no Item stock settings
  When   : PUR requests its stock settings
  Then   : the answer is lotTrackedFl false, dualQuantityFl false and an empty measure unit

### REQ-INV-130 — Answer the capability turn-off check
  Pattern    : event
  Statement  : When the capability-flag owner asks whether lotExpiry or dualQuantity can be turned off, the system shall return whether it is allowed, the count of dependent Items and at most 20 of them with code and name.
  Traces     : US-INV-026
  Entities   : ENT-INV-014
  Rationale  : The flag owner blocks a turn-off that would orphan stock data and shows what depends on it.
  Source     : POL-INV-010; DP §5 G3; contract-cat CON-CAT-017 (same obligation for variants and bundles)
  Priority   : MEDIUM

#### AC-INV-146 — [REQ-INV-130]
  Given  : no Item stock settings with dualQuantityFl true
  When   : the flag owner asks whether dualQuantity can be turned off
  Then   : the answer is allowed true, dependent count 0 and an empty list

### REQ-INV-131 — Refuse a turn-off while data depends on it
  Pattern    : unwanted
  Statement  : If the turn-off check finds an Item stock settings record with lotTrackedFl true for lotExpiry or with dualQuantityFl true for dualQuantity, then the system shall answer not allowed with a message that names the next step.
  Traces     : US-INV-026
  Entities   : ENT-INV-014
  Rationale  : History is never lost and the Owner always knows the next step.
  Source     : POL-INV-010; RULE-INV-046; KB §6.5
  Priority   : MEDIUM

#### AC-INV-147 — [REQ-INV-131]
  Given  : 3 Items with lotTrackedFl true
  When   : the flag owner asks whether lotExpiry can be turned off
  Then   : the answer is allowed false, dependent count 3, the 3 Items, and the message «لا يمكن إيقاف «التشغيلة والصلاحية»: 3 أصناف تُتتبَّع بالتشغيلة. ألغِ تتبع التشغيلة لها أولًا.» / "Lot and expiry cannot be switched off: 3 Items are tracked by lot. Clear their lot tracking first."

### REQ-INV-132 — Read capability flags through FeatureFlags only
  Pattern    : ubiquitous
  Statement  : The system shall decide every flag-dependent inventory behaviour from the lotExpiry, dualQuantity and bundles capability flags read through the FeatureFlags API and never from the tenant's declared activity or activity preset.
  Traces     : US-INV-027
  Entities   : ENT-INV-014
  Rationale  : One codebase serves every activity.
  Source     : POL-INV-011; KB §3 D2, §7; DP §5 G1
  Priority   : HIGH

#### AC-INV-148 — [REQ-INV-132]
  Given  : the INV source code
  When   : the architecture test suite runs
  Then   : 0 INV classes read the activity or the activity preset, and every flag read goes through the FeatureFlags API

### REQ-INV-133 — Keep behaviour when only the activity preset changes
  Pattern    : unwanted
  Statement  : If the tenant's activity preset changes while the lotExpiry, dualQuantity and bundles flag values stay the same, then the system shall keep every inventory behaviour unchanged.
  Traces     : US-INV-027
  Entities   : ENT-INV-014
  Rationale  : Capabilities change without re-setup; the activity name has no effect.
  Source     : POL-INV-011; KB §6.2
  Priority   : HIGH

#### AC-INV-149 — [REQ-INV-133]
  Given  : a tenant with activity "Grocery & food", lotExpiry on, and lot-tracked Variant V
  When   : the activity changes to "Cleaning supplies, feed" with lotExpiry still on, and PUR then receives V
  Then   : the receipt still requires a lot number and expiry date, exactly as before the change

### REQ-INV-134 — Hide lot surfaces while lotExpiry is off
  Pattern    : optional
  Statement  : Where the lotExpiry capability flag is off, the system shall hide the lot fields, the lot screen, the near-expiry view and the lot-tracking control of Item stock settings.
  Traces     : US-INV-027, US-INV-021
  Entities   : ENT-INV-012, ENT-INV-014
  Rationale  : The interface shows a flagged feature only when its flag is on.
  Source     : DP §5 G2; KB §4.1, §6.3
  Priority   : HIGH

#### AC-INV-150 — [REQ-INV-134]
  Given  : lotExpiry off
  When   : a Warehouse user opens the inventory menu and the adjustment entry
  Then   : the menu shows no lot or near-expiry entry and the adjustment lines show no lot number or expiry column

### REQ-INV-135 — Hide measure surfaces while dualQuantity is off
  Pattern    : optional
  Statement  : Where the dualQuantity capability flag is off, the system shall hide the measure fields and the dual-quantity control of Item stock settings.
  Traces     : US-INV-027, US-INV-025
  Entities   : ENT-INV-003, ENT-INV-014
  Rationale  : The interface shows a flagged feature only when its flag is on.
  Source     : DP §5 G2
  Priority   : HIGH

#### AC-INV-151 — [REQ-INV-135]
  Given  : dualQuantity off
  When   : a user opens the stock balance screen
  Then   : the result shows no measure column

### A4.16 — Role presets and access

### REQ-INV-136 — Grant the Warehouse role preset its inventory access
  Pattern    : event
  Statement  : When a tenant is provisioned, the system shall give the Warehouse role preset the INV page actions that B4 assigns to the Warehouse role.
  Traces     : US-INV-028
  Entities   : ENT-INV-001
  Rationale  : The people who run stock reach the inventory functions without role design.
  Source     : POL-INV-013; KB §3 D8; module-registry (sec: Warehouse role preset)
  Priority   : HIGH

#### AC-INV-152 — [REQ-INV-136]
  Given  : a newly provisioned tenant and a user holding only the Warehouse role
  When   : the user opens the inventory menu
  Then   : the entries for stock balances, stock moves, counts, adjustments, lots (lotExpiry on) and transfers (2 or more active warehouses) are shown, and the entries for opening stock, stock valuation and inventory settings are not

### REQ-INV-137 — Grant the Owner and Seller/Cashier presets their inventory access
  Pattern    : event
  Statement  : When a tenant is provisioned, the system shall give the Owner and Seller/Cashier role presets the INV page actions that B4 assigns to them.
  Traces     : US-INV-028, US-INV-002
  Entities   : ENT-INV-003
  Rationale  : Every preset reaches exactly what its role needs.
  Source     : KB §3 D8; POL-INV-013
  Priority   : HIGH

#### AC-INV-153 — [REQ-INV-137]
  Given  : a newly provisioned tenant and a user holding only the Seller/Cashier role
  When   : the user opens the inventory menu
  Then   : only the stock balance entry is shown, and the balance screen offers no create, update or cancel action

### REQ-INV-138 — Refuse an operation without the page permission
  Pattern    : unwanted
  Statement  : If a user calls an INV operation without the page action it needs, then the system shall refuse the call through the platform authorisation layer.
  Traces     : US-INV-028
  Entities   : ENT-INV-010
  Rationale  : Access follows the role grants; no hand-written checks.
  Source     : profile security_model; erp-core sec
  Priority   : HIGH

#### AC-INV-154 — [REQ-INV-138]
  Given  : a user holding only the Seller/Cashier role
  When   : the user submits a stock adjustment
  Then   : nothing is saved and the platform message «ليست لديك صلاحية لهذا الإجراء.» / "You do not have permission for this action." is returned

## A5 — Business rules

### RULE-INV-001 — Warehouse code is system-generated
  Scope      : ENT-INV-001
  Trigger    : on create; on update
  Statement  : The system shall assign the warehouse code from the number series INV_WAREHOUSE on first save and prevent any change to it afterwards.
  Message    : ar: رمز المستودع يُنشأ تلقائيًا ولا يمكن تغييره. · en: The warehouse code is generated automatically and cannot be changed.
  Traces     : REQ-INV-003, REQ-INV-004
  Data source: ENT-INV-001.code
  Source     : NUMBERING profile rule; DEFAULT D-20

### RULE-INV-002 — Warehouse Arabic name is required and unique
  Scope      : ENT-INV-001
  Trigger    : on create; on update
  Statement  : The system shall require nameAr and prevent saving when another warehouse of the tenant has the same nameAr.
  Message    : ar: يوجد مستودع آخر بهذا الاسم. · en: Another warehouse already has this name.
  Traces     : REQ-INV-003, REQ-INV-004
  Data source: ENT-INV-001.nameAr
  Source     : DP §8 P-09 (Arabic mandatory); domain best practice

### RULE-INV-003 — The default warehouse cannot be deactivated
  Scope      : ENT-INV-001
  Trigger    : on deactivate
  Statement  : The system shall prevent deactivation when the warehouse has isDefaultFl true.
  Message    : ar: لا يمكن إيقاف المستودع الافتراضي. اجعل مستودعًا آخر افتراضيًا أولًا. · en: The default warehouse cannot be deactivated. Set another warehouse as the default first.
  Traces     : REQ-INV-006
  Data source: ENT-INV-001.isDefaultFl
  Source     : POL-INV-003; DEFAULT D-20

### RULE-INV-004 — A warehouse holding stock or reservations cannot be deactivated
  Scope      : ENT-INV-001
  Trigger    : on deactivate
  Statement  : The system shall prevent deactivation when any Stock balance of the warehouse has non-zero onHandQty or any ACTIVE Reservation names the warehouse.
  Message    : ar: لا يمكن إيقاف مستودع فيه مخزون أو حجوزات قائمة. انقل المخزون أو سوِّه أولًا. · en: A warehouse that holds stock or open reservations cannot be deactivated. Transfer or adjust the stock first.
  Traces     : REQ-INV-006
  Data source: ENT-INV-003.warehouseFk, ENT-INV-003.onHandQty, ENT-INV-005.warehouseFk, ENT-INV-005.statusCode
  Source     : ARCH-5 (prevent); domain best practice

### RULE-INV-005 — Effect of a warehouse deactivation on consumers
  Scope      : ENT-INV-001
  Trigger    : on deactivate
  Statement  : The system shall keep a deactivated warehouse readable by its identifier, exclude it from the active warehouse list and every new selection, and notify consumers through the inventory master-change event without changing their records.
  Message    : ar: المستودع غير نشط. · en: The warehouse is inactive.
  Traces     : REQ-INV-005, REQ-INV-034
  Data source: ENT-INV-001.isActiveFl
  Source     : ARCH-5 (notify, never cascade); contract-cat deactivation promise (same pattern)
  Test-Hint  : PUR, SLS and SFR documents that already cite the warehouse still read it.

### RULE-INV-006 — Exactly one active default warehouse
  Scope      : ENT-INV-001
  Trigger    : on set as default; on create of the first warehouse
  Statement  : The system shall require the default warehouse to be active and keep exactly one warehouse of the tenant with isDefaultFl true.
  Message    : ar: المستودع الافتراضي يجب أن يكون نشطًا. · en: The default warehouse must be active.
  Traces     : REQ-INV-008
  Data source: ENT-INV-001.isDefaultFl, ENT-INV-001.isActiveFl
  Source     : POL-INV-003; DP §5 G8

### RULE-INV-007 — Stock operations use active warehouses
  Scope      : ENT-INV-004, ENT-INV-005, ENT-INV-006, ENT-INV-008, ENT-INV-010
  Trigger    : on submit of a receipt, issue or reservation; on save of a transfer, count or adjustment
  Statement  : The system shall prevent a stock document or cross-module stock request that names a warehouse whose isActiveFl is false.
  Message    : ar: المستودع غير نشط. · en: The warehouse is inactive.
  Traces     : REQ-INV-018
  Data source: ENT-INV-001.isActiveFl
  Source     : RULE-INV-005

### RULE-INV-008 — A warehouse is required when several are active
  Scope      : ENT-INV-004, ENT-INV-005, ENT-INV-008, ENT-INV-010
  Trigger    : on submit; on save
  Statement  : The system shall require a warehouse on a stock document or cross-module stock request when the tenant has two or more active warehouses, and assign the default warehouse when it has exactly one.
  Message    : ar: اختر المستودع. · en: Choose the warehouse.
  Traces     : REQ-INV-017, REQ-INV-013
  Data source: ENT-INV-001.isActiveFl, ENT-INV-001.isDefaultFl
  Source     : POL-INV-002, POL-INV-004

### RULE-INV-009 — A warehouse belongs to an active branch
  Scope      : ENT-INV-001
  Trigger    : on create; on update
  Statement  : The system shall require branchFk to reference an active branch and assign the default branch when none is given.
  Message    : ar: الفرع المختار غير نشط. · en: The chosen branch is inactive.
  Traces     : REQ-INV-003, REQ-INV-004, REQ-INV-031
  Data source: ENT-INV-001.branchFk, ENT-INV-002.isActiveFl, ENT-INV-002.isDefaultFl
  Source     : DEFAULT D-21

### RULE-INV-010 — Branch code is system-generated
  Scope      : ENT-INV-002
  Trigger    : on create; on update
  Statement  : The system shall assign the branch code from the number series INV_BRANCH on first save and prevent any change to it afterwards.
  Message    : ar: رمز الفرع يُنشأ تلقائيًا ولا يمكن تغييره. · en: The branch code is generated automatically and cannot be changed.
  Traces     : REQ-INV-022, REQ-INV-023
  Data source: ENT-INV-002.code
  Source     : NUMBERING profile rule; DEFAULT D-20

### RULE-INV-011 — Branch Arabic name is required and unique
  Scope      : ENT-INV-002
  Trigger    : on create; on update
  Statement  : The system shall require nameAr and prevent saving when another branch of the tenant has the same nameAr.
  Message    : ar: يوجد فرع آخر بهذا الاسم. · en: Another branch already has this name.
  Traces     : REQ-INV-022, REQ-INV-023
  Data source: ENT-INV-002.nameAr
  Source     : DP §8 P-09; domain best practice

### RULE-INV-012 — The default branch cannot be deactivated
  Scope      : ENT-INV-002
  Trigger    : on deactivate
  Statement  : The system shall prevent deactivation when the branch has isDefaultFl true.
  Message    : ar: لا يمكن إيقاف الفرع الافتراضي. اجعل فرعًا آخر افتراضيًا أولًا. · en: The default branch cannot be deactivated. Set another branch as the default first.
  Traces     : REQ-INV-025
  Data source: ENT-INV-002.isDefaultFl
  Source     : POL-INV-015

### RULE-INV-013 — A branch with active warehouses cannot be deactivated
  Scope      : ENT-INV-002
  Trigger    : on deactivate
  Statement  : The system shall prevent deactivation when an active warehouse has branchFk equal to the branch.
  Message    : ar: لا يمكن إيقاف فرع تتبعه مستودعات نشطة. انقل المستودعات إلى فرع آخر أو أوقفها أولًا. · en: A branch with active warehouses cannot be deactivated. Move the warehouses to another branch or deactivate them first.
  Traces     : REQ-INV-025
  Data source: ENT-INV-001.branchFk, ENT-INV-001.isActiveFl
  Source     : ARCH-5 (prevent); DEFAULT D-21

### RULE-INV-014 — Effect of a branch deactivation on consumers
  Scope      : ENT-INV-002
  Trigger    : on deactivate
  Statement  : The system shall keep a deactivated branch readable by its identifier, exclude it from the active branch list and every new selection, and notify consumers through the inventory master-change event without changing their records.
  Message    : ar: الفرع غير نشط. · en: The branch is inactive.
  Traces     : REQ-INV-024, REQ-INV-034
  Data source: ENT-INV-002.isActiveFl
  Source     : ARCH-5 (notify, never cascade)

### RULE-INV-015 — Exactly one active default branch
  Scope      : ENT-INV-002
  Trigger    : on set as default
  Statement  : The system shall require the default branch to be active and keep exactly one branch of the tenant with isDefaultFl true.
  Message    : ar: الفرع الافتراضي يجب أن يكون نشطًا. · en: The default branch must be active.
  Traces     : REQ-INV-027
  Data source: ENT-INV-002.isDefaultFl, ENT-INV-002.isActiveFl
  Source     : POL-INV-015; DP §5 G8

### RULE-INV-016 — A transfer needs two different active warehouses
  Scope      : ENT-INV-006
  Trigger    : on save; on post
  Statement  : The system shall prevent a transfer whose source and destination warehouses are the same, or either of which is inactive.
  Message    : ar: التحويل يحتاج إلى مستودعين نشطين مختلفين. · en: A transfer needs two different active warehouses.
  Traces     : REQ-INV-014, REQ-INV-082
  Data source: ENT-INV-006.fromWarehouseFk, ENT-INV-006.toWarehouseFk, ENT-INV-001.isActiveFl
  Source     : POL-INV-004, POL-INV-005

### RULE-INV-017 — Quantities are positive with at most 3 decimals
  Scope      : ENT-INV-004, ENT-INV-005, ENT-INV-007
  Trigger    : on submit; on save
  Statement  : The system shall require every receipt, issue, reservation and transfer quantity to be greater than zero with at most 3 decimal places.
  Message    : ar: أدخل كمية أكبر من صفر بثلاث خانات عشرية على الأكثر. · en: Enter a quantity greater than zero with at most 3 decimal places.
  Traces     : REQ-INV-043, REQ-INV-051, REQ-INV-052, REQ-INV-078
  Data source: ENT-INV-004.quantity, ENT-INV-005.originalQty, ENT-INV-007.quantity
  Source     : DEFAULT D-06; CON-CAT-006 (3 decimals)

### RULE-INV-018 — Stock lines name an active Variant of a STANDARD Item
  Scope      : ENT-INV-004, ENT-INV-005, ENT-INV-007, ENT-INV-009, ENT-INV-011
  Trigger    : on submit; on save
  Statement  : The system shall prevent a stock line whose Variant or Item is inactive in CAT, and a transfer, count or adjustment line whose Variant belongs to a BUNDLE Item.
  Message    : ar: متغيّر الصنف غير نشط. · en: The Variant is inactive.
  Traces     : REQ-INV-043, REQ-INV-051, REQ-INV-052, REQ-INV-078, REQ-INV-088, REQ-INV-097, REQ-INV-070
  Data source: ENT-INV-004.variantFk, ENT-INV-005.variantFk, ENT-INV-007.variantFk, ENT-INV-009.variantFk, ENT-INV-011.variantFk
  Source     : CON-CAT-007, CON-CAT-008 (isActiveFl, itemKindCode); POL-INV-014 (a bundle holds no stock)
  Test-Hint  : the Variant's active state is read from CAT at the moment of the request.

### RULE-INV-019 — No online decrease below zero while negative stock is disallowed
  Scope      : ENT-INV-004, ENT-INV-006, ENT-INV-010
  Trigger    : on submit of an issue; on post of a transfer or adjustment
  Statement  : The system shall prevent an online stock decrease that would take the on-hand quantity of a Variant in a warehouse below zero when the cu setting INV_ALLOW_NEGATIVE_STOCK is false, and shall never apply this check to a move with offlineFl true.
  Message    : ar: الرصيد لا يكفي: المتاح في المستودع {onHand}. الرصيد السالب غير مسموح. · en: Not enough stock: {onHand} on hand in this warehouse. Negative stock is not allowed.
  Traces     : REQ-INV-067, REQ-INV-083, REQ-INV-069
  Data source: ENT-INV-003.onHandQty, ENT-INV-004.offlineFl
  Source     : POL-INV-018, POL-INV-020; INV-P08

### RULE-INV-020 — No reservation beyond availability while negative stock is disallowed
  Scope      : ENT-INV-005
  Trigger    : on create of a reservation
  Statement  : The system shall prevent a reservation whose quantity exceeds the available quantity when the cu setting INV_ALLOW_NEGATIVE_STOCK is false, and record the uncovered part as shortfallQty when it is true.
  Message    : ar: الكمية المتاحة لا تكفي للحجز. المتاح: {available}. · en: Not enough available stock to reserve. Available: {available}.
  Traces     : REQ-INV-044, REQ-INV-045
  Data source: ENT-INV-003.onHandQty, ENT-INV-005.reservedQty, ENT-INV-005.shortfallQty
  Source     : POL-INV-001, POL-INV-018; DEFAULT D-11

### RULE-INV-021 — Only an active reservation is released or consumed
  Scope      : ENT-INV-005
  Trigger    : on release; on issue that references a reservation
  Statement  : The system shall prevent a release or a consumption of a reservation whose statusCode is not ACTIVE.
  Message    : ar: الحجز مغلق ولا يمكن استخدامه. · en: The reservation is closed and cannot be used.
  Traces     : REQ-INV-048, REQ-INV-046, REQ-INV-047
  Data source: ENT-INV-005.statusCode, ENT-INV-004.reservationFk
  Source     : A7 reservation lifecycle

### RULE-INV-022 — A bundle needs active components
  Scope      : ENT-INV-004, ENT-INV-005
  Trigger    : on submit of an issue or reservation of a bundle Variant
  Statement  : The system shall prevent the issue or reservation of a bundle Variant that has no active component line in CAT.
  Message    : ar: الحزمة ليس لها مكوّنات نشطة. · en: The bundle has no active components.
  Traces     : REQ-INV-054
  Data source: ENT-INV-004.bundleVariantFk, ENT-INV-005.bundleVariantFk
  Source     : POL-INV-014; CON-CAT-006, CON-CAT-007 (component lines)

### RULE-INV-023 — Stock moves are never changed or deleted
  Scope      : ENT-INV-004
  Trigger    : on update; on delete
  Statement  : The system shall prevent every change and every deletion of a recorded stock move.
  Message    : ar: لا تُعدَّل حركات المخزون. صحّح الرصيد بتسوية مخزون. · en: Stock moves cannot be changed. Correct the balance with a stock adjustment.
  Traces     : REQ-INV-055
  Data source: ENT-INV-004.stockMovePk
  Source     : INV-P10; domain best practice (immutable stock ledger)

### RULE-INV-024 — A repeated submission is recorded once
  Scope      : ENT-INV-004, ENT-INV-005
  Trigger    : on submit of a receipt, issue or reservation
  Statement  : The system shall require an idempotency key on every cross-module receipt, issue and reservation request and return the first result without recording anything when the key was already recorded.
  Message    : ar: سبق تسجيل هذا الطلب؛ أُعيدت النتيجة الأولى. · en: This request was already recorded; the first result is returned.
  Traces     : REQ-INV-058
  Data source: ENT-INV-004.idempotencyKey, ENT-INV-005.idempotencyKey
  Source     : POL-INV-020; erp-core common (idempotency)

### RULE-INV-025 — Incoming stock has a known cost
  Scope      : ENT-INV-004, ENT-INV-011
  Trigger    : on submit of a receipt or customer return; on post of an adjustment
  Statement  : The system shall require unitCost ≥ 0 with at most 4 decimals on an incoming line, use the current averageUnitCost when the line has none, and prevent the line when it has none and the Variant has no Stock valuation.
  Message    : ar: لا توجد تكلفة معروفة لهذا المتغيّر. أدخل تكلفة الوحدة. · en: This Variant has no known cost. Enter the unit cost.
  Traces     : REQ-INV-064, REQ-INV-102
  Data source: ENT-INV-004.unitCost, ENT-INV-011.unitCost, ENT-INV-013.averageUnitCost
  Source     : POL-INV-021

### RULE-INV-026 — Opening stock lines carry a cost
  Scope      : ENT-INV-011
  Trigger    : on post of an adjustment with reason OPENING_STOCK
  Statement  : The system shall require unitCost ≥ 0 and quantityChange > 0 on every line of an adjustment whose reasonCode is OPENING_STOCK.
  Message    : ar: أدخل تكلفة الوحدة للمخزون الافتتاحي. · en: Enter the unit cost of the opening stock.
  Traces     : REQ-INV-074, REQ-INV-070
  Data source: ENT-INV-011.unitCost, ENT-INV-011.quantityChange, ENT-INV-010.reasonCode
  Source     : POL-INV-021; US-INV-012

### RULE-INV-027 — An opening stock load holds at most 1000 rows
  Scope      : ENT-INV-011
  Trigger    : on submit of an opening stock load
  Statement  : The system shall prevent an opening stock load with zero rows or more than 1000 rows.
  Message    : ar: الحد الأقصى 1000 سطر في التحميل الواحد. · en: At most 1000 rows per load.
  Traces     : REQ-INV-071
  Data source: ENT-INV-011.rowNo
  Source     : DEFAULT D-13

### RULE-INV-028 — A load row resolves to one active Variant
  Scope      : ENT-INV-011
  Trigger    : on submit of an opening stock load
  Statement  : The system shall require the Variant code or barcode of every load row to identify exactly one active Variant of a STANDARD Item and report the row number when it does not.
  Message    : ar: لم يُعثر على متغيّر صنف نشط بهذا الرمز أو الباركود. · en: No active Variant has this code or barcode.
  Traces     : REQ-INV-072
  Data source: ENT-INV-011.variantFk, ENT-INV-011.rowNo
  Source     : CON-CAT-008, CON-CAT-010

### RULE-INV-029 — Posted and cancelled documents are read-only
  Scope      : ENT-INV-006, ENT-INV-007, ENT-INV-008, ENT-INV-009, ENT-INV-010, ENT-INV-011
  Trigger    : on update; on cancel; on line change
  Statement  : The system shall prevent every change to a transfer, count or adjustment, and to its lines, when its statusCode is POSTED or CANCELLED.
  Message    : ar: المستند مُرحَّل أو ملغى ولا يمكن تعديله. · en: The document is posted or cancelled and cannot be changed.
  Traces     : REQ-INV-085, REQ-INV-096, REQ-INV-103
  Data source: ENT-INV-006.statusCode, ENT-INV-008.statusCode, ENT-INV-010.statusCode
  Source     : A7 document lifecycle

### RULE-INV-030 — Stock document numbers come from the number series
  Scope      : ENT-INV-006, ENT-INV-008, ENT-INV-010
  Trigger    : on create
  Statement  : The system shall assign docNo on first save from the series INV_TRANSFER, INV_COUNT or INV_ADJUSTMENT, unique per document type, and prevent any change to it afterwards.
  Message    : ar: رقم المستند يُنشأ تلقائيًا ولا يمكن تغييره. · en: The document number is generated automatically and cannot be changed.
  Traces     : REQ-INV-078, REQ-INV-087, REQ-INV-097
  Data source: ENT-INV-006.docNo, ENT-INV-008.docNo, ENT-INV-010.docNo
  Source     : NUMBERING profile rule; module-registry (sequence: transfer, count and adjustment numbers)

### RULE-INV-031 — A document needs at least one line to post
  Scope      : ENT-INV-006, ENT-INV-008, ENT-INV-010
  Trigger    : on post
  Statement  : The system shall prevent posting a transfer, count or adjustment that has no line.
  Message    : ar: أضف سطرًا واحدًا على الأقل قبل الترحيل. · en: Add at least one line before posting.
  Traces     : REQ-INV-080, REQ-INV-093, REQ-INV-099
  Data source: ENT-INV-007.transferFk, ENT-INV-009.stockCountFk, ENT-INV-011.stockAdjustmentFk
  Source     : domain best practice

### RULE-INV-032 — A scanned barcode identifies an active Variant
  Scope      : ENT-INV-009
  Trigger    : on scan
  Statement  : The system shall prevent adding a count line from a barcode that identifies no active Variant.
  Message    : ar: الباركود غير معروف. اختر متغيّر الصنف يدويًا. · en: Unknown barcode. Pick the Variant by hand.
  Traces     : REQ-INV-091
  Data source: ENT-INV-009.variantFk
  Source     : CON-CAT-010 (not found)

### RULE-INV-033 — An adjustment has an active reason
  Scope      : ENT-INV-010
  Trigger    : on save; on post
  Statement  : The system shall require reasonCode to be an active value of INV_ADJUSTMENT_REASON.
  Message    : ar: اختر سبب التسوية. · en: Choose the adjustment reason.
  Traces     : REQ-INV-101
  Data source: ENT-INV-010.reasonCode
  Source     : US-INV-015; registry LOOKUPS OWNED

### RULE-INV-034 — An adjustment line changes something
  Scope      : ENT-INV-011
  Trigger    : on save; on post
  Statement  : The system shall prevent an adjustment line whose quantityChange is zero unless its Item is dual-quantity and its measureQtyChange is non-zero.
  Message    : ar: أدخل تغيّرًا في الكمية أو القياس لا يساوي صفرًا. · en: Enter a non-zero change of quantity or measure.
  Traces     : REQ-INV-099, REQ-INV-123
  Data source: ENT-INV-011.quantityChange, ENT-INV-011.measureQtyChange, ENT-INV-014.dualQuantityFl
  Source     : POL-INV-009

### RULE-INV-035 — Lot and expiry on incoming lines of lot-tracked Items
  Scope      : ENT-INV-004, ENT-INV-011, ENT-INV-012
  Trigger    : on submit of a receipt; on post of an increase line
  Statement  : The system shall require a lot number and an expiry date on every incoming line of an Item whose lotTrackedFl is true while the lotExpiry capability flag is on.
  Message    : ar: أدخل رقم التشغيلة وتاريخ الصلاحية لهذا الصنف. · en: Enter the lot number and expiry date for this Item.
  Traces     : REQ-INV-105
  Data source: ENT-INV-014.lotTrackedFl, ENT-INV-011.lotNo, ENT-INV-011.lotExpiryDate, ENT-INV-012.expiryDate
  Source     : POL-INV-006

### RULE-INV-036 — One lot number per Variant with one expiry
  Scope      : ENT-INV-012
  Trigger    : on create of a lot; on incoming line naming an existing lot
  Statement  : The system shall keep lot numbers unique per Variant and prevent an incoming line that names an existing lot number with a different expiry date.
  Message    : ar: التشغيلة {lot} مسجّلة بتاريخ صلاحية {expiry}. · en: Lot {lot} is recorded with expiry date {expiry}.
  Traces     : REQ-INV-107, REQ-INV-106
  Data source: ENT-INV-012.variantFk, ENT-INV-012.code, ENT-INV-012.expiryDate
  Source     : POL-INV-006; domain best practice

### RULE-INV-037 — Lines of lot-tracked Items name their lot
  Scope      : ENT-INV-007, ENT-INV-009, ENT-INV-011
  Trigger    : on save
  Statement  : The system shall require a lot of the line's Variant on every transfer, count and decrease adjustment line of an Item whose lotTrackedFl is true while the lotExpiry capability flag is on.
  Message    : ar: اختر التشغيلة لهذا الصنف. · en: Choose the lot for this Item.
  Traces     : REQ-INV-084, REQ-INV-099
  Data source: ENT-INV-014.lotTrackedFl, ENT-INV-007.lotFk, ENT-INV-009.lotFk, ENT-INV-011.lotFk
  Source     : POL-INV-006, POL-INV-007

### RULE-INV-038 — Expired lots are excluded from sale
  Scope      : ENT-INV-012, ENT-INV-004
  Trigger    : on submit of a sale issue; on FEFO selection; on availability read
  Statement  : The system shall exclude from sale issue, FEFO selection and available quantity every lot whose expiryDate is before today in the tenant's time zone.
  Message    : ar: التشغيلة منتهية الصلاحية ولا تُباع. · en: The lot has expired and cannot be sold.
  Traces     : REQ-INV-113, REQ-INV-038, REQ-INV-110
  Data source: ENT-INV-012.expiryDate
  Source     : POL-INV-022; INV-P10
  Test-Hint  : a lot expiring today is still sellable today.

### RULE-INV-039 — An expired lot leaves stock only by adjustment
  Scope      : ENT-INV-004
  Trigger    : on submit of an issue or return to supplier
  Statement  : The system shall prevent an ISSUE or RETURN_OUT move that names a lot whose expiryDate is before today.
  Message    : ar: التشغيلة منتهية الصلاحية. اشطبها بتسوية مخزون. · en: The lot has expired. Write it off with a stock adjustment.
  Traces     : REQ-INV-114
  Data source: ENT-INV-012.expiryDate, ENT-INV-004.moveTypeCode, ENT-INV-004.lotFk
  Source     : INV-P10

### RULE-INV-040 — Dual-quantity lines carry their measure
  Scope      : ENT-INV-004, ENT-INV-007, ENT-INV-009
  Trigger    : on submit; on save
  Statement  : The system shall require a measure quantity greater than zero on every receipt, issue and transfer line, and a counted measure on every count line, of an Item whose dualQuantityFl is true while the dualQuantity capability flag is on.
  Message    : ar: أدخل القياس (الوزن أو الطول) لهذا الصنف. · en: Enter the measure (weight or length) for this Item.
  Traces     : REQ-INV-122, REQ-INV-120
  Data source: ENT-INV-014.dualQuantityFl, ENT-INV-004.measureQty, ENT-INV-007.measureQty, ENT-INV-009.countedMeasureQty
  Source     : POL-INV-009

### RULE-INV-041 — A dual-quantity Item has a measure unit
  Scope      : ENT-INV-014
  Trigger    : on save
  Statement  : The system shall require measureUomCode when dualQuantityFl is true.
  Message    : ar: اختر وحدة القياس الثانية (وزن أو طول). · en: Choose the measure unit (weight or length).
  Traces     : REQ-INV-125
  Data source: ENT-INV-014.dualQuantityFl, ENT-INV-014.measureUomCode
  Source     : POL-INV-009

### RULE-INV-042 — Lot tracking is cleared only when no lot holds stock
  Scope      : ENT-INV-014
  Trigger    : on update (lotTrackedFl true → false)
  Statement  : The system shall prevent clearing lotTrackedFl while any Stock balance with a lot of the Item's Variants has non-zero onHandQty.
  Message    : ar: لا يمكن إلغاء تتبع التشغيلة: توجد تشغيلات فيها مخزون ({count}). اشطبها أو اصرفها أولًا. · en: Lot tracking cannot be cleared: lots still hold stock ({count}). Write them off or issue them first.
  Traces     : REQ-INV-126
  Data source: ENT-INV-014.lotTrackedFl, ENT-INV-003.lotFk, ENT-INV-003.onHandQty
  Source     : POL-INV-010

### RULE-INV-043 — Dual quantity is cleared only when no measure is held
  Scope      : ENT-INV-014
  Trigger    : on update (dualQuantityFl true → false)
  Statement  : The system shall prevent clearing dualQuantityFl while any Stock balance of the Item's Variants has non-zero onHandMeasureQty.
  Message    : ar: لا يمكن إلغاء الكمية المزدوجة: يوجد مخزون بقياس غير صفري. سوِّ القياس إلى صفر أولًا. · en: Dual quantity cannot be cleared: stock still holds a non-zero measure. Adjust the measure to zero first.
  Traces     : REQ-INV-127
  Data source: ENT-INV-014.dualQuantityFl, ENT-INV-003.onHandMeasureQty
  Source     : POL-INV-010

### RULE-INV-044 — Item stock settings follow their capability flag
  Scope      : ENT-INV-014
  Trigger    : on create; on update
  Statement  : The system shall prevent setting lotTrackedFl true while the lotExpiry capability flag is off and setting dualQuantityFl true while the dualQuantity capability flag is off.
  Message    : ar: فعِّل مفتاح القدرة الخاص بهذا الإعداد أولًا. · en: Switch on the capability flag for this setting first.
  Traces     : REQ-INV-128, REQ-INV-124, REQ-INV-125
  Data source: ENT-INV-014.lotTrackedFl, ENT-INV-014.dualQuantityFl
  Source     : POL-INV-011; DP §5 G1

### RULE-INV-045 — Item stock settings belong to STANDARD Items
  Scope      : ENT-INV-014
  Trigger    : on create
  Statement  : The system shall prevent Item stock settings for an Item whose itemKindCode is BUNDLE and keep at most one record per Item.
  Message    : ar: الحزمة لا تحمل مخزونًا؛ تُضبط إعدادات المخزون على مكوّناتها. · en: A bundle holds no stock; set stock settings on its components.
  Traces     : REQ-INV-124, REQ-INV-125
  Data source: ENT-INV-014.itemFk
  Source     : POL-INV-014; CON-CAT-001 (itemKindCode)

### RULE-INV-046 — A capability turn-off waits for dependent Items
  Scope      : ENT-INV-014
  Trigger    : on capability turn-off check
  Statement  : The system shall answer not allowed for lotExpiry while any Item stock settings record has lotTrackedFl true, and for dualQuantity while any has dualQuantityFl true.
  Message    : ar: لا يمكن إيقاف «{flag}»: {count} أصناف تعتمد عليه. ألغِ الإعداد لها أولًا. · en: {flag} cannot be switched off: {count} Items depend on it. Clear the setting on them first.
  Traces     : REQ-INV-131, REQ-INV-130
  Data source: ENT-INV-014.lotTrackedFl, ENT-INV-014.dualQuantityFl, ENT-INV-014.itemFk
  Source     : POL-INV-010; DP §5 G3

### RULE-INV-047 — A counted quantity is not negative
  Scope      : ENT-INV-009
  Trigger    : on save of a count line
  Statement  : The system shall require countedQty ≥ 0 with at most 3 decimals.
  Message    : ar: الكمية المعدودة لا تكون سالبة. · en: The counted quantity cannot be negative.
  Traces     : REQ-INV-088, REQ-INV-089
  Data source: ENT-INV-009.countedQty
  Source     : domain best practice

### RULE-INV-048 — One line per Variant and lot on a transfer or adjustment
  Scope      : ENT-INV-007, ENT-INV-011
  Trigger    : on save
  Statement  : The system shall prevent two lines with the same Variant and the same lot on one transfer or one adjustment.
  Message    : ar: هذا المتغيّر مذكور في سطر آخر بالتشغيلة نفسها. · en: This Variant is already on another line with the same lot.
  Traces     : REQ-INV-078, REQ-INV-079, REQ-INV-097, REQ-INV-098
  Data source: ENT-INV-007.variantFk, ENT-INV-007.lotFk, ENT-INV-011.variantFk, ENT-INV-011.lotNo
  Source     : domain best practice

## A6 — Lookups

All values are loaded at runtime from the core lookup module (`mdl`); no enumeration is hard-coded in an API or field specification (profile LOOKUPS rule).

```yaml name=lookups
lookups:
  - {key: INV_ADJUSTMENT_REASON, seeded: [COUNT_DIFFERENCE, DAMAGE, EXPIRY_WRITE_OFF, OPENING_STOCK, OTHER], open: true, values: [COUNT_DIFFERENCE, DAMAGE, EXPIRY_WRITE_OFF, OPENING_STOCK, OTHER], fields: [reasonCode], entity: ENT-INV-010, control: lookup, owner: INV, source: "module-registry LOOKUPS OWNED inv.adjustment-reason; US-INV-015 (count difference, damage, expired-lot write-off); US-INV-012 (opening stock); DEFAULT D-22"}
  - {key: INV_MOVE_TYPE, seeded: [RECEIPT, RETURN_IN, ISSUE, RETURN_OUT, TRANSFER_OUT, TRANSFER_IN, ADJUSTMENT_IN, ADJUSTMENT_OUT], open: false, values: [RECEIPT, RETURN_IN, ISSUE, RETURN_OUT, TRANSFER_OUT, TRANSFER_IN, ADJUSTMENT_IN, ADJUSTMENT_OUT], fields: [moveTypeCode], entity: ENT-INV-004, control: lookup, owner: INV, source: "REQ-INV-051, REQ-INV-052, REQ-INV-080, REQ-INV-099"}
  - {key: INV_DOC_STATUS, seeded: [DRAFT, POSTED, CANCELLED], open: false, values: [DRAFT, POSTED, CANCELLED], fields: [statusCode], entity: "ENT-INV-006, ENT-INV-008, ENT-INV-010", control: lookup, owner: INV, source: "A7 document lifecycle; KB §6.5 (undo for unposted operations)"}
  - {key: INV_RESERVATION_STATUS, seeded: [ACTIVE, CONSUMED, RELEASED], open: false, values: [ACTIVE, CONSUMED, RELEASED], fields: [statusCode], entity: ENT-INV-005, control: lookup, owner: INV, source: "A7 reservation lifecycle; US-INV-003"}
  - {key: INV_SOURCE_DOC_TYPE, seeded: [INV_TRANSFER, INV_COUNT, INV_ADJUSTMENT, PURCHASE_RECEIPT, PURCHASE_RETURN, SALES_INVOICE, POS_SALE, SALES_RETURN, SALES_ORDER, STOREFRONT_ORDER], open: true, values: [INV_TRANSFER, INV_COUNT, INV_ADJUSTMENT, PURCHASE_RECEIPT, PURCHASE_RETURN, SALES_INVOICE, POS_SALE, SALES_RETURN, SALES_ORDER, STOREFRONT_ORDER], fields: [sourceDocTypeCode], entity: "ENT-INV-004, ENT-INV-005", control: lookup, owner: INV, source: "REQ-INV-056; calling modules add their document types as data"}
  - {key: INV_CHANGE_KIND, seeded: [CREATED, UPDATED, DEACTIVATED, REACTIVATED, DEFAULT_CHANGED], open: false, values: [CREATED, UPDATED, DEACTIVATED, REACTIVATED, DEFAULT_CHANGED], fields: [], entity: "ENT-INV-001, ENT-INV-002", control: event field, owner: INV, source: "REQ-INV-034 (inventory master-change event)"}
```

Labels per language:

| Key | Code | ar | en |
|---|---|---|---|
| INV_ADJUSTMENT_REASON | COUNT_DIFFERENCE | فرق جرد | Count difference |
| | DAMAGE | تلف | Damage |
| | EXPIRY_WRITE_OFF | شطب تشغيلة منتهية | Expired lot write-off |
| | OPENING_STOCK | مخزون افتتاحي | Opening stock |
| | OTHER | أخرى | Other |
| INV_MOVE_TYPE | RECEIPT | استلام | Receipt |
| | RETURN_IN | مرتجع وارد | Customer return |
| | ISSUE | صرف | Issue |
| | RETURN_OUT | مرتجع إلى المورّد | Return to supplier |
| | TRANSFER_OUT | تحويل صادر | Transfer out |
| | TRANSFER_IN | تحويل وارد | Transfer in |
| | ADJUSTMENT_IN | تسوية بالزيادة | Adjustment in |
| | ADJUSTMENT_OUT | تسوية بالنقص | Adjustment out |
| INV_DOC_STATUS | DRAFT | مسودة | Draft |
| | POSTED | مُرحَّل | Posted |
| | CANCELLED | ملغى | Cancelled |
| INV_RESERVATION_STATUS | ACTIVE | قائم | Active |
| | CONSUMED | مُستهلك | Consumed |
| | RELEASED | مُحرَّر | Released |
| INV_SOURCE_DOC_TYPE | INV_TRANSFER | تحويل مخزني | Stock transfer |
| | INV_COUNT | جرد مخزون | Stock count |
| | INV_ADJUSTMENT | تسوية مخزون | Stock adjustment |
| | PURCHASE_RECEIPT | استلام بضاعة | Goods receipt |
| | PURCHASE_RETURN | مرتجع مشتريات | Purchase return |
| | SALES_INVOICE | فاتورة مبيعات | Sales invoice |
| | POS_SALE | بيع نقطة البيع | POS sale |
| | SALES_RETURN | مرتجع مبيعات | Sales return |
| | SALES_ORDER | أمر بيع | Sales order |
| | STOREFRONT_ORDER | طلب المتجر الإلكتروني | Storefront order |
| INV_CHANGE_KIND | CREATED / UPDATED / DEACTIVATED / REACTIVATED / DEFAULT_CHANGED | أُنشئ / عُدِّل / أُوقف / أُعيد تفعيله / أصبح افتراضيًا | Created / Updated / Deactivated / Reactivated / Set as default |

Rationale: each closed set above is fixed by a requirement of this SRS; INV_ADJUSTMENT_REASON and INV_SOURCE_DOC_TYPE are open because tenants add reasons and calling modules add document types as data. COUNT_DIFFERENCE, OPENING_STOCK and EXPIRY_WRITE_OFF are used by REQ-INV-092, REQ-INV-070 and REQ-INV-114 and stay active.

**Lookups consumed:**

| Key | Owner | Field |
|---|---|---|
| Unit of measure (CAT unit-of-measure key, CAT SRS A6) | CAT | ENT-INV-014.measureUomCode |

**Settings consumed (given `cu`, read through SettingsApi — not lookups):** `INV_ALLOW_NEGATIVE_STOCK` (flag, default true), `INV_NEAR_EXPIRY_DAYS` (whole days 1–365, default 30) — DEFAULT D-04.

## A7 — Status lifecycle

**Transfer (ENT-INV-006), Stock count (ENT-INV-008), Stock adjustment (ENT-INV-010)** — `INV_DOC_STATUS`:

```
DRAFT ──post──▶ POSTED        (RULE-INV-031, RULE-INV-019; transfer RULE-INV-016)
  │
  └──cancel──▶ CANCELLED      (POSTED and CANCELLED are final — RULE-INV-029)
```

**Reservation (ENT-INV-005)** — `INV_RESERVATION_STATUS`:

```
ACTIVE ──issue (remainder > 0)──▶ ACTIVE
ACTIVE ──issue (remainder = 0)──▶ CONSUMED     (RULE-INV-021)
ACTIVE ──release──────────────▶ RELEASED      (RULE-INV-021)
CONSUMED, RELEASED: final
```

No module-specific approval flow: no story asks for one (Approvals belong to APR, KB §3 D6).

## A8 — Module dependencies

```yaml name=module-dependencies
consumes:
  - {module: CAT, entity: ENT-CAT-001, type: HARD-FK}
  - {module: CAT, entity: ENT-CAT-002, type: HARD-FK}
  - {module: CAT, entity: ENT-CAT-007, type: SOFT-READ}
  - {module: CAT, entity: ENT-CAT-009, type: SOFT-READ}
```

| Consumed | Why | Through |
|---|---|---|
| ENT-CAT-001 Item | Item stock settings key on the Item; itemKindCode (STANDARD / BUNDLE) and baseUomCode | CON-CAT-001, CON-CAT-007 |
| ENT-CAT-002 Variant | Stock, moves, reservations, lots and valuation key on the Variant (INV-P05); active state | CON-CAT-002, CON-CAT-008 |
| ENT-CAT-007 Bundle component | Bundle availability, reservation and issue explode to components (POL-INV-014) | CON-CAT-006, CON-CAT-007 |
| ENT-CAT-009 Barcode | Count scanning and opening-stock rows resolve a barcode to its Variant | CON-CAT-005, CON-CAT-010 |

INV consumes no other business module. PUR, SLS and SFR call INV (edges PUR → INV, SLS → INV, SFR → INV); ACC, SWR and INS receive the stock-move event. INV never calls them, so source documents are held as SOFT references with no FK (ENT-INV-004.sourceDocRef). CAT's catalogue-change event (CON-CAT-018) is not consumed in v1: INV reads CAT state at request time (DEFAULT D-23).

**Operations INV offers to other modules (in-process; the P1.5 contract promises them):**

| Operation | Inputs | Outputs | RULEs | Traces (REQ) | Callers |
|---|---|---|---|---|---|
| read warehouse | warehousePk | Warehouse | — | REQ-INV-009 | PUR, SLS, SFR, INS |
| list active warehouses | — | active Warehouses + active count | — | REQ-INV-019 | PUR, SLS, SFR |
| read branch | branchPk | Branch | — | REQ-INV-028 | PUR, SLS, STL, TAX, INS |
| list active branches | — | active Branches + active count | — | REQ-INV-033 | PUR, SLS, STL, TAX |
| read availability | variantPk, warehousePk (optional) | on-hand, reserved, available, measure per warehouse and total | RULE-INV-038 | REQ-INV-040, REQ-INV-041, REQ-INV-042, REQ-INV-121 | SLS, SFR, INS |
| reserve | variantPk, warehousePk, quantity, source document, idempotency key | Reservation(s) with shortfall | RULE-INV-007, 008, 017, 018, 020, 022, 024 | REQ-INV-043, REQ-INV-044, REQ-INV-045, REQ-INV-049 | SLS, SFR |
| release reservation | reservationPk | Reservation | RULE-INV-021 | REQ-INV-046 | SLS, SFR |
| reservations of a document | source document type + identifier | Reservations | — | REQ-INV-050 | SLS, SFR |
| receive stock | warehouse, move type RECEIPT / RETURN_IN, lines {variantPk, quantity, unitCost, lot number, expiry date, measure}, source document, idempotency key | Stock moves | RULE-INV-007, 008, 017, 018, 024, 025, 035, 036, 040 | REQ-INV-051, REQ-INV-105, REQ-INV-106, REQ-INV-120 | PUR, SLS |
| issue stock | warehouse, move type ISSUE / RETURN_OUT, sale flag, offline flag, lines {variantPk, quantity, lot (non-sale), measure, reservationPk}, source document, idempotency key | Stock moves | RULE-INV-007, 008, 017, 018, 019, 021, 022, 024, 038, 039, 040 | REQ-INV-052, REQ-INV-053, REQ-INV-069, REQ-INV-110, REQ-INV-111 | PUR, SLS, SFR |
| read valuation | variantPk | total quantity, average unit cost, stock value | — | REQ-INV-063 | SLS, INS |
| read lot | lotPk | Lot | — | REQ-INV-109 | PUR, SLS |
| read Item stock settings | itemPk | lotTrackedFl, dualQuantityFl, measure unit | — | REQ-INV-129 | PUR, SLS |
| capability turn-off check | flag `lotExpiry` / `dualQuantity` | allowed, dependent count, at most 20 Items, messages | RULE-INV-046 | REQ-INV-130, REQ-INV-131 | flag owner (P-02) |
| stock-move event | — | one event per committed move | — | REQ-INV-057 | ACC, SWR, INS |
| inventory master-change event | — | one event per committed Warehouse / Branch change | — | REQ-INV-034 | PUR, SLS, SFR, STL, TAX, INS |

| External service | Purpose | Integration kind |
|---|---|---|
| — | INV uses no external service | — |

## A9 — Given usage

```yaml name=given-usage
rows:
  - {given: erp-core, module: modules.cu, via: com.erp.cu.crossmodule.SettingsApi, purpose: "read the lotExpiry, dualQuantity and bundles capability flags behind the FeatureFlags API (REQ-INV-132), and read and write the INV_ALLOW_NEGATIVE_STOCK and INV_NEAR_EXPIRY_DAYS settings (REQ-INV-065, REQ-INV-118)"}
  - {given: erp-core, module: modules.sequence, via: com.erp.sequence.crossmodule.NumberSeriesApi, purpose: "issue the codes INV_WAREHOUSE and INV_BRANCH and the document numbers INV_TRANSFER, INV_COUNT and INV_ADJUSTMENT (RULE-INV-001, RULE-INV-010, RULE-INV-030)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.OutboxPublisher, purpose: "record the stock-move event and the inventory master-change event in the change transaction and deliver them after commit (REQ-INV-057, REQ-INV-034)"}
  - {given: erp-core, module: modules.audit, via: com.erp.audit.crossmodule.AuditApi, purpose: "record the change history of stock documents, warehouses, branches, lots and Item stock settings (REQ-INV-077)"}
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "load the values of the INV_* lookup keys and the CAT unit-of-measure key at runtime (A6)"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantProvisioningContributor, purpose: "seed the default branch and the default warehouse of each new tenant (REQ-INV-020, REQ-INV-001)"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantRunner, purpose: "run the daily near-expiry check once per tenant (REQ-INV-117)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.permission.PermissionContributor, purpose: "register the INV page codes so the security module derives their VIEW/CREATE/UPDATE/DELETE permissions, and the grants of the Owner, Seller/Cashier and Warehouse role presets (REQ-INV-136, REQ-INV-137)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.crossmodule.SecUserDirectoryApi, purpose: "find the users holding the Warehouse role as recipients of the negative-stock and near-expiry notifications (REQ-INV-068, REQ-INV-117)"}
  - {given: erp-core, module: modules.notif, via: com.erp.notif.crossmodule.NotificationDispatchApi, purpose: "send the negative-stock and near-expiry notifications (REQ-INV-068, REQ-INV-117)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.ReportProvider, purpose: "register the report INV_OPENING_STOCK_SHEET (REQ-INV-073)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.crossmodule.PrintApi, purpose: "produce the xlsx opening stock sheet (REQ-INV-073)"}
```

No ENT of this SRS re-creates a given's entity: settings stay in `cu` (AppConfiguration), lookup values in `mdl`, numbers in `sequence`, users and roles in `sec`, the tenant in `tenant` [C:C-GIVEN-1].

# PART B — SCREEN REQUIREMENTS

Every screen reads the capability flags and the active warehouse and branch counts on load (REQ-INV-010 … 012, REQ-INV-029, 030, REQ-INV-134, 135). Field definitions, rules and lookup values are those of PART A.

## SCR-REQ-INV-001 — المستودعات / Warehouses

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | Add, maintain, deactivate and reactivate warehouses and choose the default |
| Entities | ENT-INV-001 |
| Operations | search, list, create, read, update, deactivate, reactivate, set as default |
| Users | Owner (maintain); Warehouse (view) |
| Navigation | Inventory → Setup → Warehouses; from: inventory home; to: SCR-REQ-INV-003 (balances of a warehouse) |
| Content shape | flat record |
| Traces | REQ-INV-003, REQ-INV-004, REQ-INV-005, REQ-INV-006, REQ-INV-007, REQ-INV-008, REQ-INV-009, REQ-INV-015, REQ-INV-030, REQ-INV-031 |
| Composite | Search + Entry — one screen requirement |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| code | code | ENT-INV-001.code |
| name | nameAr / nameEn | ENT-INV-001 |
| branch (hidden while one active branch — REQ-INV-030) | branch | ENT-INV-001.branchFk |
| active state (active only / all) | active | ENT-INV-001.isActiveFl |
| — | default | ENT-INV-001.isDefaultFl |

### B3 — Input
Fields: ENT-INV-001 nameAr (mandatory), nameEn, branchFk (hidden while one active branch — REQ-INV-029; default branch when empty — REQ-INV-031); code read-only. Mandatory fields outside Advanced: 1.

| Action | Operation | RULEs |
|---|---|---|
| Save | create / update (REQ-INV-003, REQ-INV-004) | RULE-INV-001, RULE-INV-002, RULE-INV-009 |
| Deactivate | deactivate (REQ-INV-005, REQ-INV-006) | RULE-INV-003, RULE-INV-004, RULE-INV-005 |
| Reactivate | reactivate (REQ-INV-007) | — |
| Set as default | set as default (REQ-INV-008) | RULE-INV-006 |

### B4 — Access
Page code `INV_WAREHOUSES` · VIEW: Owner, Warehouse · CREATE: Owner · UPDATE: Owner · DELETE (deactivate): Owner

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search warehouses | GET | /api/v1/inv/warehouses | code, name, branchPk, active, page | Page<Warehouse> | — | REQ-INV-009 |
| read warehouse | GET | /api/v1/inv/warehouses/{id} | id | Warehouse | — | REQ-INV-009 |
| create warehouse | POST | /api/v1/inv/warehouses | nameAr, nameEn, branchPk | Warehouse | RULE-INV-001, 002, 009 | REQ-INV-003, REQ-INV-031 |
| update warehouse | PUT | /api/v1/inv/warehouses/{id} | nameAr, nameEn, branchPk | Warehouse | RULE-INV-001, 002, 009 | REQ-INV-004 |
| deactivate warehouse | DELETE | /api/v1/inv/warehouses/{id} | id | confirmation | RULE-INV-003, 004, 005 | REQ-INV-005, REQ-INV-006 |
| reactivate warehouse | PATCH | /api/v1/inv/warehouses/{id}/reactivate | id | Warehouse | — | REQ-INV-007 |
| set as default warehouse | PATCH | /api/v1/inv/warehouses/{id}/default | id | Warehouse | RULE-INV-006 | REQ-INV-008 |
| inventory context (active warehouse and branch counts, flags) | GET | /api/v1/inv/context | — | counts, flags | — | REQ-INV-010, REQ-INV-015, REQ-INV-029, REQ-INV-032, REQ-INV-134 |

## SCR-REQ-INV-002 — الفروع / Branches

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | Add, maintain, deactivate and reactivate branches and choose the default |
| Entities | ENT-INV-002 |
| Operations | search, list, create, read, update, deactivate, reactivate, set as default |
| Users | Owner (maintain); Warehouse (view) |
| Navigation | Inventory → Setup → Branches; from: inventory home; to: SCR-REQ-INV-001 (warehouses of a branch) |
| Content shape | flat record |
| Traces | REQ-INV-022, REQ-INV-023, REQ-INV-024, REQ-INV-025, REQ-INV-026, REQ-INV-027, REQ-INV-028, REQ-INV-032 |
| Composite | Search + Entry — one screen requirement |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| code | code | ENT-INV-002.code |
| name | nameAr / nameEn | ENT-INV-002 |
| active state | active | ENT-INV-002.isActiveFl |
| — | default | ENT-INV-002.isDefaultFl |

### B3 — Input
Fields: ENT-INV-002 nameAr (mandatory), nameEn; code read-only. Mandatory fields outside Advanced: 1.

| Action | Operation | RULEs |
|---|---|---|
| Save | create / update (REQ-INV-022, REQ-INV-023) | RULE-INV-010, RULE-INV-011 |
| Deactivate | deactivate (REQ-INV-024, REQ-INV-025) | RULE-INV-012, RULE-INV-013, RULE-INV-014 |
| Reactivate | reactivate (REQ-INV-026) | — |
| Set as default | set as default (REQ-INV-027) | RULE-INV-015 |

### B4 — Access
Page code `INV_BRANCHES` · VIEW: Owner, Warehouse · CREATE: Owner · UPDATE: Owner · DELETE (deactivate): Owner

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search branches | GET | /api/v1/inv/branches | code, name, active, page | Page<Branch> | — | REQ-INV-028 |
| read branch | GET | /api/v1/inv/branches/{id} | id | Branch | — | REQ-INV-028 |
| create branch | POST | /api/v1/inv/branches | nameAr, nameEn | Branch | RULE-INV-010, 011 | REQ-INV-022 |
| update branch | PUT | /api/v1/inv/branches/{id} | nameAr, nameEn | Branch | RULE-INV-010, 011 | REQ-INV-023 |
| deactivate branch | DELETE | /api/v1/inv/branches/{id} | id | confirmation | RULE-INV-012, 013, 014 | REQ-INV-024, REQ-INV-025 |
| reactivate branch | PATCH | /api/v1/inv/branches/{id}/reactivate | id | Branch | — | REQ-INV-026 |
| set as default branch | PATCH | /api/v1/inv/branches/{id}/default | id | Branch | RULE-INV-015 | REQ-INV-027 |

## SCR-REQ-INV-003 — أرصدة المخزون / Stock balances

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | See on-hand, reserved and available stock per Variant and warehouse, with lot, expiry and measure where their flags are on |
| Entities | ENT-INV-003, ENT-INV-005, ENT-INV-012 |
| Operations | search, list, read |
| Users | Owner, Warehouse, Seller/Cashier |
| Navigation | Inventory → Stock balances; from: inventory home, SCR-REQ-INV-001; to: SCR-REQ-INV-004 (moves of a row) |
| Content shape | flat record (inquiry list) |
| Traces | REQ-INV-035, REQ-INV-038, REQ-INV-039, REQ-INV-041, REQ-INV-042, REQ-INV-115, REQ-INV-121, REQ-INV-012 |
| Composite | Search + read-only result — one screen requirement |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| Variant (code, name or barcode) | Variant code, Variant name | ENT-INV-003.variantFk (CON-CAT-008, CON-CAT-010) |
| Item | Item name | via Variant (CON-CAT-007) |
| warehouse (hidden while one — REQ-INV-012) | warehouse | ENT-INV-003.warehouseFk |
| lot (lotExpiry on) | lot number, expiry date, expired mark | ENT-INV-003.lotFk, ENT-INV-012.expiryDate (REQ-INV-115) |
| available only (on-hand ≠ 0) | on-hand, reserved, available | ENT-INV-003.onHandQty; derived (REQ-INV-038) |
| — | measure and unit (dualQuantity on) | ENT-INV-003.onHandMeasureQty (REQ-INV-121) |

Applies RULE-INV-038 to the available column. Bundle Variants show the derived availability of REQ-INV-042. No cost or value column (DEFAULT D-26).

### B3 — Input
Not applicable — read-only inquiry.

### B4 — Access
Page code `INV_STOCK_BALANCES` · VIEW: Owner, Warehouse, Seller/Cashier · CREATE / UPDATE / DELETE: not applicable

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search balances | GET | /api/v1/inv/stock-balances | variantPk or term, itemPk, warehousePk, lotPk, nonZero, page | Page<Stock balance row> | RULE-INV-038 | REQ-INV-039, REQ-INV-041, REQ-INV-115, REQ-INV-121 |
| read availability of a Variant | GET | /api/v1/inv/stock-balances/availability/{variantId} | variantId, warehousePk | availability per warehouse and total | RULE-INV-038 | REQ-INV-038, REQ-INV-040, REQ-INV-042 |

## SCR-REQ-INV-004 — سجل حركات المخزون / Stock move history

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | Explain any balance through every move and the document that caused it |
| Entities | ENT-INV-004 |
| Operations | search, list, read |
| Users | Owner, Warehouse |
| Navigation | Inventory → Stock moves; from: SCR-REQ-INV-003 (row), SCR-REQ-INV-005, 006, 007 (document); to: — |
| Content shape | other (journal of moves with running balance) |
| Traces | REQ-INV-075, REQ-INV-076, REQ-INV-056, REQ-INV-055, REQ-INV-016 |
| Composite | Search + read-only result — one screen requirement |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| Variant | Variant | ENT-INV-004.variantFk |
| warehouse (hidden while one — REQ-INV-012) | warehouse | ENT-INV-004.warehouseFk |
| lot (lotExpiry on) | lot | ENT-INV-004.lotFk |
| move type (INV_MOVE_TYPE) | move type | ENT-INV-004.moveTypeCode |
| document number | document type, document number | ENT-INV-004.sourceDocTypeCode, sourceDocNo |
| date from / to | move date | ENT-INV-004.docDate |
| — | quantity, measure (dualQuantity on), balance after (one Variant and one warehouse — REQ-INV-076) | ENT-INV-004.quantity, measureQty, balanceAfterQty |

### B3 — Input
Not applicable — moves are read-only (REQ-INV-055, RULE-INV-023).

### B4 — Access
Page code `INV_STOCK_MOVES` · VIEW: Owner, Warehouse · CREATE / UPDATE / DELETE: not applicable

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search moves | GET | /api/v1/inv/stock-moves | variantPk, warehousePk, lotPk, moveTypeCode, docNo, dateFrom, dateTo, page | Page<Stock move> | — | REQ-INV-075, REQ-INV-076 |
| read move | GET | /api/v1/inv/stock-moves/{id} | id | Stock move | — | REQ-INV-056 |

## SCR-REQ-INV-005 — التحويلات بين المستودعات / Transfers

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | Move stock between warehouses at unchanged cost |
| Entities | ENT-INV-006, ENT-INV-007 |
| Operations | search, list, create, read, update, post, cancel |
| Users | Owner, Warehouse |
| Navigation | Inventory → Transfers (shown only with 2 or more active warehouses — REQ-INV-011, REQ-INV-015); from: inventory home; to: SCR-REQ-INV-004 (moves of the transfer) |
| Content shape | header + repeating lines |
| Traces | REQ-INV-078, REQ-INV-079, REQ-INV-080, REQ-INV-081, REQ-INV-082, REQ-INV-083, REQ-INV-084, REQ-INV-085, REQ-INV-086, REQ-INV-062, REQ-INV-014 |
| Composite | Search + Entry (header + lines) — one screen requirement |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| number | number | ENT-INV-006.docNo |
| date from / to | date | ENT-INV-006.docDate |
| from warehouse | from warehouse | ENT-INV-006.fromWarehouseFk |
| to warehouse | to warehouse | ENT-INV-006.toWarehouseFk |
| document status (INV_DOC_STATUS) | document status | ENT-INV-006.statusCode |

### B3 — Input
Header: fromWarehouseFk (mandatory), toWarehouseFk (mandatory), docDate (defaulted), noteText (Advanced). Lines (ENT-INV-007): variantFk (mandatory, by barcode or pick), quantity (mandatory), lotFk (lotExpiry on, lot-tracked Items), measureQty (dualQuantity on, dual-quantity Items). Mandatory fields outside Advanced: 4.

| Action | Operation | RULEs |
|---|---|---|
| Save | create / update (REQ-INV-078, REQ-INV-079) | RULE-INV-016, RULE-INV-017, RULE-INV-018, RULE-INV-030, RULE-INV-037, RULE-INV-040, RULE-INV-048 |
| Post | post (REQ-INV-080, REQ-INV-083, REQ-INV-084) | RULE-INV-016, RULE-INV-019, RULE-INV-029, RULE-INV-031 |
| Cancel | cancel (REQ-INV-081) | RULE-INV-029 |

### B4 — Access
Page code `INV_TRANSFERS` · VIEW: Owner, Warehouse · CREATE: Owner, Warehouse · UPDATE (save, post): Owner, Warehouse · DELETE (cancel): Owner, Warehouse

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search transfers | GET | /api/v1/inv/transfers | docNo, dateFrom, dateTo, fromWarehousePk, toWarehousePk, statusCode, page | Page<Transfer> | — | REQ-INV-086 |
| read transfer | GET | /api/v1/inv/transfers/{id} | id | Transfer with lines | — | REQ-INV-086 |
| create transfer | POST | /api/v1/inv/transfers | header, lines | Transfer | RULE-INV-016, 017, 018, 030, 037, 040, 048 | REQ-INV-078, REQ-INV-014, REQ-INV-082 |
| update transfer | PUT | /api/v1/inv/transfers/{id} | header, lines | Transfer | RULE-INV-016, 017, 018, 029, 037, 040, 048 | REQ-INV-079, REQ-INV-085 |
| post transfer | PATCH | /api/v1/inv/transfers/{id}/post | id | Transfer | RULE-INV-016, 019, 029, 031 | REQ-INV-080, REQ-INV-083, REQ-INV-084, REQ-INV-062 |
| cancel transfer | DELETE | /api/v1/inv/transfers/{id} | id | confirmation | RULE-INV-029 | REQ-INV-081 |

## SCR-REQ-INV-006 — جرد المخزون / Stock counts

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | Count stock on the shelf from a phone's web browser and post the differences |
| Entities | ENT-INV-008, ENT-INV-009, ENT-INV-010 |
| Operations | search, list, create, read, update (lines), post, cancel |
| Users | Owner, Warehouse |
| Navigation | Inventory → Stock counts; from: inventory home; to: SCR-REQ-INV-007 (resulting adjustment) |
| Content shape | header + repeating lines (scan list) |
| Traces | REQ-INV-087, REQ-INV-088, REQ-INV-089, REQ-INV-090, REQ-INV-091, REQ-INV-092, REQ-INV-093, REQ-INV-094, REQ-INV-095, REQ-INV-096 |
| Composite | Search + Entry (header + lines) — one screen requirement; mobile-first (REQ-INV-090) |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| number | number | ENT-INV-008.docNo |
| date from / to | date | ENT-INV-008.docDate |
| warehouse (hidden while one — REQ-INV-012) | warehouse | ENT-INV-008.warehouseFk |
| document status | document status | ENT-INV-008.statusCode |

### B3 — Input
Header: warehouseFk (mandatory; hidden and defaulted while one — REQ-INV-013), noteText (Advanced). Lines (ENT-INV-009): barcode or Variant (mandatory), countedQty (mandatory), lotFk (lotExpiry on), countedMeasureQty (dualQuantity on); systemQty and differenceQty shown only after posting (DEFAULT D-15). Mandatory fields outside Advanced: 3.

| Action | Operation | RULEs |
|---|---|---|
| Start count | create (REQ-INV-087) | RULE-INV-007, RULE-INV-008, RULE-INV-030 |
| Scan / add line | update lines (REQ-INV-088, REQ-INV-091) | RULE-INV-018, RULE-INV-032, RULE-INV-037, RULE-INV-040, RULE-INV-047, RULE-INV-029 |
| Change / remove line | update lines (REQ-INV-089) | RULE-INV-047, RULE-INV-029 |
| Post | post (REQ-INV-092, REQ-INV-093) | RULE-INV-031, RULE-INV-029, RULE-INV-019 |
| Cancel | cancel (REQ-INV-094) | RULE-INV-029 |

### B4 — Access
Page code `INV_COUNTS` · VIEW: Owner, Warehouse · CREATE: Owner, Warehouse · UPDATE (lines, post): Owner, Warehouse · DELETE (cancel): Owner, Warehouse

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search counts | GET | /api/v1/inv/stock-counts | docNo, dateFrom, dateTo, warehousePk, statusCode, page | Page<Stock count> | — | REQ-INV-095 |
| read count | GET | /api/v1/inv/stock-counts/{id} | id | Stock count with lines | — | REQ-INV-095 |
| create count | POST | /api/v1/inv/stock-counts | warehousePk, noteText | Stock count | RULE-INV-007, 008, 030 | REQ-INV-087 |
| add counted quantity | POST | /api/v1/inv/stock-counts/{id}/lines | barcode or variantPk, countedQty, lotPk, countedMeasureQty | Stock count line | RULE-INV-018, 029, 032, 037, 040, 047 | REQ-INV-088, REQ-INV-091, REQ-INV-096 |
| change line | PUT | /api/v1/inv/stock-counts/{id}/lines/{lineId} | countedQty, countedMeasureQty | Stock count line | RULE-INV-029, 047 | REQ-INV-089 |
| remove line | DELETE | /api/v1/inv/stock-counts/{id}/lines/{lineId} | lineId | confirmation | RULE-INV-029 | REQ-INV-089 |
| post count | PATCH | /api/v1/inv/stock-counts/{id}/post | id | Stock count + Stock adjustment | RULE-INV-019, 029, 031 | REQ-INV-092, REQ-INV-093 |
| cancel count | DELETE | /api/v1/inv/stock-counts/{id} | id | confirmation | RULE-INV-029 | REQ-INV-094 |

## SCR-REQ-INV-007 — تسويات المخزون / Stock adjustments

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | Correct stock with a stated reason, including the write-off of expired lots |
| Entities | ENT-INV-010, ENT-INV-011 |
| Operations | search, list, create, read, update, post, cancel |
| Users | Owner, Warehouse |
| Navigation | Inventory → Stock adjustments; from: inventory home, SCR-REQ-INV-006, SCR-REQ-INV-008 (write off a lot); to: SCR-REQ-INV-004 |
| Content shape | header + repeating lines with totals (line value) |
| Traces | REQ-INV-097, REQ-INV-098, REQ-INV-099, REQ-INV-100, REQ-INV-101, REQ-INV-102, REQ-INV-103, REQ-INV-104, REQ-INV-114, REQ-INV-123, REQ-INV-067 |
| Composite | Search + Entry (header + lines) — one screen requirement |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| number | number | ENT-INV-010.docNo |
| date from / to | date | ENT-INV-010.docDate |
| warehouse (hidden while one) | warehouse | ENT-INV-010.warehouseFk |
| reason (INV_ADJUSTMENT_REASON) | reason | ENT-INV-010.reasonCode |
| document status | document status | ENT-INV-010.statusCode |

### B3 — Input
Header: reasonCode (mandatory), warehouseFk (hidden and defaulted while one), noteText (Advanced). Lines (ENT-INV-011): variantFk (mandatory), quantityChange (mandatory), unitCost (increase lines, Advanced unless no average exists), lotNo / lotExpiryDate (lotExpiry on), measureQtyChange (dualQuantity on). Mandatory fields outside Advanced: 3.

| Action | Operation | RULEs |
|---|---|---|
| Save | create / update (REQ-INV-097, REQ-INV-098) | RULE-INV-007, RULE-INV-008, RULE-INV-018, RULE-INV-030, RULE-INV-033, RULE-INV-034, RULE-INV-037, RULE-INV-048 |
| Post | post (REQ-INV-099, REQ-INV-102, REQ-INV-114, REQ-INV-123) | RULE-INV-019, RULE-INV-025, RULE-INV-029, RULE-INV-031, RULE-INV-034, RULE-INV-035, RULE-INV-036 |
| Cancel | cancel (REQ-INV-100) | RULE-INV-029 |

### B4 — Access
Page code `INV_ADJUSTMENTS` · VIEW: Owner, Warehouse · CREATE: Owner, Warehouse · UPDATE (save, post): Owner, Warehouse · DELETE (cancel): Owner, Warehouse

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search adjustments | GET | /api/v1/inv/stock-adjustments | docNo, dateFrom, dateTo, warehousePk, reasonCode, statusCode, page | Page<Stock adjustment> | — | REQ-INV-104 |
| read adjustment | GET | /api/v1/inv/stock-adjustments/{id} | id | Stock adjustment with lines | — | REQ-INV-104 |
| create adjustment | POST | /api/v1/inv/stock-adjustments | header, lines | Stock adjustment | RULE-INV-007, 008, 018, 030, 033, 034, 037, 048 | REQ-INV-097, REQ-INV-101 |
| update adjustment | PUT | /api/v1/inv/stock-adjustments/{id} | header, lines | Stock adjustment | RULE-INV-018, 029, 033, 034, 037, 048 | REQ-INV-098, REQ-INV-103 |
| post adjustment | PATCH | /api/v1/inv/stock-adjustments/{id}/post | id | Stock adjustment | RULE-INV-019, 025, 029, 031, 034, 035, 036 | REQ-INV-099, REQ-INV-102, REQ-INV-114, REQ-INV-123, REQ-INV-067 |
| cancel adjustment | DELETE | /api/v1/inv/stock-adjustments/{id} | id | confirmation | RULE-INV-029 | REQ-INV-100 |

## SCR-REQ-INV-008 — التشغيلات وقرب انتهاء الصلاحية / Lots and near expiry

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | See the lots held, which are near expiry or expired, and correct a lot's expiry date |
| Entities | ENT-INV-012, ENT-INV-003 |
| Operations | search, list, read, update (expiry correction), custom (near-expiry view) |
| Users | Owner (update), Warehouse (view) |
| Navigation | Inventory → Lots (shown only with lotExpiry on — REQ-INV-134); from: inventory home, near-expiry notification; to: SCR-REQ-INV-007 (write off an expired lot), SCR-REQ-INV-003 |
| Content shape | flat record (list with a near-expiry view) |
| Traces | REQ-INV-108, REQ-INV-109, REQ-INV-115, REQ-INV-116, REQ-INV-117 |
| Composite | Search + Entry — one screen requirement |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| Variant | Variant | ENT-INV-012.variantFk |
| lot number | lot number | ENT-INV-012.code |
| warehouse (hidden while one) | warehouse, on-hand | ENT-INV-003.warehouseFk, onHandQty |
| expiry date from / to | expiry date, days left, expired mark | ENT-INV-012.expiryDate |
| near-expiry only (within the horizon — REQ-INV-116) | — | ENT-INV-012.expiryDate, cu INV_NEAR_EXPIRY_DAYS |

Applies RULE-INV-038 (expired mark).

### B3 — Input
Fields: ENT-INV-012 expiryDate (mandatory, Owner), nameAr, nameEn (Advanced); code and Variant read-only. Mandatory fields outside Advanced: 1.

| Action | Operation | RULEs |
|---|---|---|
| Save expiry | update (REQ-INV-108) | RULE-INV-036 |
| Write off | navigates to SCR-REQ-INV-007 with reason EXPIRY_WRITE_OFF (REQ-INV-114) | RULE-INV-039 |

### B4 — Access
Page code `INV_LOTS` · VIEW: Owner, Warehouse · UPDATE: Owner · CREATE / DELETE: not applicable (lots are created by receipts and increases)

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search lots | GET | /api/v1/inv/lots | variantPk, code, warehousePk, expiryFrom, expiryTo, nearExpiryOnly, page | Page<Lot with on-hand> | RULE-INV-038 | REQ-INV-109, REQ-INV-115, REQ-INV-116 |
| read lot | GET | /api/v1/inv/lots/{id} | id | Lot | — | REQ-INV-109 |
| correct lot expiry | PATCH | /api/v1/inv/lots/{id} | expiryDate, nameAr, nameEn | Lot | RULE-INV-036 | REQ-INV-108 |

## SCR-REQ-INV-009 — إعدادات مخزون الصنف / Item stock settings

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | Mark Items as tracked by lot or as dual-quantity |
| Entities | ENT-INV-014 |
| Operations | search, list, create, read, update |
| Users | Owner (maintain), Warehouse (view) |
| Navigation | Inventory → Setup → Item stock settings (shown only with lotExpiry or dualQuantity on — REQ-INV-134, REQ-INV-135); from: inventory home; to: — |
| Content shape | flat record |
| Traces | REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-128, REQ-INV-129 |
| Composite | Search + Entry — one screen requirement |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| Item (code or name) | Item's code, Item's name | ENT-INV-014.itemFk (CON-CAT-007) |
| tracked by lot (lotExpiry on) | tracked by lot | ENT-INV-014.lotTrackedFl |
| dual quantity (dualQuantity on) | dual quantity, measure unit | ENT-INV-014.dualQuantityFl, measureUomCode |

### B3 — Input
Fields: ENT-INV-014 itemFk (mandatory), lotTrackedFl (lotExpiry on), dualQuantityFl and measureUomCode (dualQuantity on; measure unit mandatory when dual). Mandatory fields outside Advanced: 2.

| Action | Operation | RULEs |
|---|---|---|
| Save | create / update (REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-128) | RULE-INV-041, RULE-INV-042, RULE-INV-043, RULE-INV-044, RULE-INV-045 |

### B4 — Access
Page code `INV_ITEM_STOCK_SETTINGS` · VIEW: Owner, Warehouse · CREATE: Owner · UPDATE: Owner · DELETE: not applicable

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search Item stock settings | GET | /api/v1/inv/item-stock-settings | itemPk or term, lotTracked, dualQuantity, page | Page<Item stock settings> | — | REQ-INV-129 |
| read Item stock settings | GET | /api/v1/inv/item-stock-settings/by-item/{itemId} | itemId | Item stock settings | — | REQ-INV-129 |
| save Item stock settings | PUT | /api/v1/inv/item-stock-settings/by-item/{itemId} | lotTrackedFl, dualQuantityFl, measureUomCode | Item stock settings | RULE-INV-041, 042, 043, 044, 045 | REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-128 |

## SCR-REQ-INV-010 — المخزون الافتتاحي / Opening stock

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | Load the quantities and cost of stock already held, one line at a time or up to 1000 rows from the opening stock sheet |
| Entities | ENT-INV-010, ENT-INV-011 |
| Operations | create (post), custom (download sheet, bulk load) |
| Users | Owner |
| Navigation | Inventory → Setup → Opening stock; from: setup wizard step 5, inventory home; to: SCR-REQ-INV-007 (the posted OPENING_STOCK adjustment) |
| Content shape | header + repeating lines with totals |
| Traces | REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-073, REQ-INV-074 |
| Composite | Entry + bulk load — one screen requirement |

### B2 — Search / list
Not applicable — posted loads are found on SCR-REQ-INV-007 with reason OPENING_STOCK.

### B3 — Input
Header: warehouseFk (hidden and defaulted while one). Lines (ENT-INV-011): Variant code or barcode (mandatory), quantityChange (mandatory, > 0), unitCost (mandatory), lotNo / lotExpiryDate (lotExpiry on), measureQtyChange (dualQuantity on). The browser reads the filled sheet and submits its rows (DEFAULT D-13). Mandatory fields outside Advanced: 3.

| Action | Operation | RULEs |
|---|---|---|
| Download sheet | custom (REQ-INV-073) | — |
| Post / Load | create and post (REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-074) | RULE-INV-018, RULE-INV-025, RULE-INV-026, RULE-INV-027, RULE-INV-028, RULE-INV-035, RULE-INV-036, RULE-INV-040 |

### B4 — Access
Page code `INV_OPENING_STOCK` · VIEW: Owner · CREATE: Owner · UPDATE / DELETE: not applicable

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| download opening stock sheet | GET | /api/v1/inv/opening-stock/sheet | — | xlsx file | — | REQ-INV-073 |
| post opening stock | POST | /api/v1/inv/opening-stock | warehousePk, rows (1–1000) | Stock adjustment, or row errors | RULE-INV-018, 025, 026, 027, 028, 035, 036, 040 | REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-074 |

## SCR-REQ-INV-011 — إعدادات المخزون / Inventory settings

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | Choose whether negative stock is allowed and the near-expiry horizon |
| Entities | — (given `cu` settings INV_ALLOW_NEGATIVE_STOCK, INV_NEAR_EXPIRY_DAYS) |
| Operations | read, update |
| Users | Owner |
| Navigation | Inventory → Setup → Inventory settings; from: inventory home; to: — |
| Content shape | flat record |
| Traces | REQ-INV-065, REQ-INV-066, REQ-INV-118, REQ-INV-119 |
| Composite | Entry — one screen requirement |

### B2 — Search / list
Not applicable.

### B3 — Input
Fields: «السماح بالرصيد السالب» / "Allow negative stock" (flag, default on — REQ-INV-066); «أفق قرب انتهاء الصلاحية (أيام)» / "Near-expiry horizon (days)" (whole number 1–365, default 30; shown only with lotExpiry on). Mandatory fields outside Advanced: 0 (both defaulted).

| Action | Operation | RULEs |
|---|---|---|
| Save | update (REQ-INV-065, REQ-INV-118, REQ-INV-119) | — (validation of REQ-INV-119; DEFAULT D-04) |

### B4 — Access
Page code `INV_SETTINGS` · VIEW: Owner · UPDATE: Owner · CREATE / DELETE: not applicable

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| read inventory settings | GET | /api/v1/inv/settings | — | settings | — | REQ-INV-066, REQ-INV-118 |
| update inventory settings | PUT | /api/v1/inv/settings | allowNegativeStock, nearExpiryDays | settings | — | REQ-INV-065, REQ-INV-118, REQ-INV-119 |

## SCR-REQ-INV-012 — تقييم المخزون / Stock valuation

### B1 — Definition
| Item | Value |
|---|---|
| Purpose | See what stock is worth: total quantity, weighted-average cost and value per Variant |
| Entities | ENT-INV-013 |
| Operations | search, list, read |
| Users | Owner |
| Navigation | Inventory → Stock valuation; from: inventory home; to: SCR-REQ-INV-004 (moves of a Variant) |
| Content shape | flat record (inquiry list with a total value) |
| Traces | REQ-INV-063, REQ-INV-059, REQ-INV-061 |
| Composite | Search + read-only result — one screen requirement |

### B2 — Search / list
| Filter | Result column | Source |
|---|---|---|
| Variant (code, name or barcode) | Variant code, Variant name | ENT-INV-013.variantFk |
| Item | Item name | via Variant |
| non-zero only | total quantity, average unit cost, stock value; grand total of stock value | ENT-INV-013.totalOnHandQty, averageUnitCost, stockValue |

### B3 — Input
Not applicable — read-only inquiry.

### B4 — Access
Page code `INV_STOCK_VALUATION` · VIEW: Owner · CREATE / UPDATE / DELETE: not applicable

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search valuation | GET | /api/v1/inv/stock-valuation | variantPk or term, itemPk, nonZero, page | Page<Stock valuation> + total value | — | REQ-INV-063 |
| read valuation of a Variant | GET | /api/v1/inv/stock-valuation/{variantId} | variantId | Stock valuation | — | REQ-INV-063 |

# STANDALONE

## Traceability matrix

Every story → ≥ 1 REQ; every REQ → ≥ 1 AC; every RULE → ≥ 1 REQ; every SCR-REQ → ≥ 1 REQ. "— (cross-module)" marks a story served only by provisioning, cross-module operations or notifications (A8), with no screen of its own.

| P0.5 | REQ | AC | RULE | ENT | SCR-REQ |
|---|---|---|---|---|---|
| US-INV-001 | REQ-INV-035, REQ-INV-036, REQ-INV-037, REQ-INV-040, REQ-INV-051, REQ-INV-052, REQ-INV-058 | AC-INV-040, AC-INV-041, AC-INV-042, AC-INV-045, AC-INV-056, AC-INV-057, AC-INV-058, AC-INV-064 | RULE-INV-017, RULE-INV-018, RULE-INV-024 | ENT-INV-003, ENT-INV-004, ENT-INV-005, ENT-INV-013 | SCR-REQ-INV-003 |
| US-INV-002 | REQ-INV-036, REQ-INV-038, REQ-INV-039, REQ-INV-040, REQ-INV-041, REQ-INV-137 | AC-INV-041, AC-INV-043, AC-INV-044, AC-INV-045, AC-INV-046, AC-INV-153 | RULE-INV-038 | ENT-INV-003, ENT-INV-004, ENT-INV-005, ENT-INV-012 | SCR-REQ-INV-003 |
| US-INV-003 | REQ-INV-038, REQ-INV-043, REQ-INV-044, REQ-INV-045, REQ-INV-046, REQ-INV-047, REQ-INV-048, REQ-INV-050 | AC-INV-043, AC-INV-048, AC-INV-049, AC-INV-050, AC-INV-051, AC-INV-052, AC-INV-053, AC-INV-055 | RULE-INV-017, RULE-INV-018, RULE-INV-020, RULE-INV-021, RULE-INV-038 | ENT-INV-003, ENT-INV-004, ENT-INV-005, ENT-INV-012 | SCR-REQ-INV-003 |
| US-INV-004 | REQ-INV-001, REQ-INV-002 | AC-INV-001, AC-INV-002 | — | ENT-INV-001, ENT-INV-002 | — (cross-module) |
| US-INV-005 | REQ-INV-013, REQ-INV-016, REQ-INV-017, REQ-INV-018 | AC-INV-016, AC-INV-019, AC-INV-020, AC-INV-021 | RULE-INV-007, RULE-INV-008 | ENT-INV-001, ENT-INV-004, ENT-INV-005, ENT-INV-008, ENT-INV-010 | SCR-REQ-INV-004 |
| US-INV-006 | REQ-INV-008, REQ-INV-010, REQ-INV-011, REQ-INV-012, REQ-INV-013, REQ-INV-014, REQ-INV-019 | AC-INV-010, AC-INV-011, AC-INV-013, AC-INV-014, AC-INV-015, AC-INV-016, AC-INV-017, AC-INV-022 | RULE-INV-006, RULE-INV-008, RULE-INV-016 | ENT-INV-001, ENT-INV-003, ENT-INV-004, ENT-INV-005, ENT-INV-006, ENT-INV-008, ENT-INV-010, ENT-INV-012 | SCR-REQ-INV-001, SCR-REQ-INV-003, SCR-REQ-INV-005 |
| US-INV-007 | REQ-INV-003, REQ-INV-004, REQ-INV-005, REQ-INV-006, REQ-INV-007, REQ-INV-008, REQ-INV-009, REQ-INV-015, REQ-INV-018, REQ-INV-034 | AC-INV-003, AC-INV-004, AC-INV-005, AC-INV-006, AC-INV-007, AC-INV-008, AC-INV-009, AC-INV-010, AC-INV-011, AC-INV-012, AC-INV-018, AC-INV-021, AC-INV-039 | RULE-INV-001, RULE-INV-002, RULE-INV-003, RULE-INV-004, RULE-INV-005, RULE-INV-006, RULE-INV-007, RULE-INV-009, RULE-INV-014 | ENT-INV-001, ENT-INV-002, ENT-INV-003, ENT-INV-005 | SCR-REQ-INV-001 |
| US-INV-008 | REQ-INV-062, REQ-INV-078, REQ-INV-079, REQ-INV-080, REQ-INV-081, REQ-INV-082, REQ-INV-083, REQ-INV-084, REQ-INV-085, REQ-INV-086 | AC-INV-068, AC-INV-085, AC-INV-086, AC-INV-087, AC-INV-088, AC-INV-089, AC-INV-090, AC-INV-091, AC-INV-092, AC-INV-093, AC-INV-094, AC-INV-095 | RULE-INV-016, RULE-INV-017, RULE-INV-018, RULE-INV-019, RULE-INV-029, RULE-INV-030, RULE-INV-031, RULE-INV-037, RULE-INV-048 | ENT-INV-003, ENT-INV-004, ENT-INV-006, ENT-INV-007, ENT-INV-012, ENT-INV-013 | SCR-REQ-INV-005 |
| US-INV-009 | REQ-INV-020, REQ-INV-021 | AC-INV-023, AC-INV-024 | — | ENT-INV-002 | — (cross-module) |
| US-INV-010 | REQ-INV-029, REQ-INV-030, REQ-INV-031, REQ-INV-033 | AC-INV-034, AC-INV-035, AC-INV-036, AC-INV-038 | RULE-INV-009 | ENT-INV-001, ENT-INV-002 | SCR-REQ-INV-001 |
| US-INV-011 | REQ-INV-022, REQ-INV-023, REQ-INV-024, REQ-INV-025, REQ-INV-026, REQ-INV-027, REQ-INV-028, REQ-INV-032, REQ-INV-034 | AC-INV-025, AC-INV-026, AC-INV-027, AC-INV-028, AC-INV-029, AC-INV-030, AC-INV-031, AC-INV-032, AC-INV-033, AC-INV-037, AC-INV-039 | RULE-INV-005, RULE-INV-010, RULE-INV-011, RULE-INV-012, RULE-INV-013, RULE-INV-014, RULE-INV-015 | ENT-INV-001, ENT-INV-002 | SCR-REQ-INV-002 |
| US-INV-012 | REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-073, REQ-INV-074 | AC-INV-076, AC-INV-077, AC-INV-078, AC-INV-079, AC-INV-080, AC-INV-081 | RULE-INV-018, RULE-INV-026, RULE-INV-027, RULE-INV-028 | ENT-INV-004, ENT-INV-010, ENT-INV-011, ENT-INV-013 | SCR-REQ-INV-010 |
| US-INV-013 | REQ-INV-055, REQ-INV-056, REQ-INV-075, REQ-INV-076, REQ-INV-077 | AC-INV-061, AC-INV-062, AC-INV-082, AC-INV-083, AC-INV-084 | RULE-INV-023 | ENT-INV-001, ENT-INV-002, ENT-INV-004, ENT-INV-006, ENT-INV-008, ENT-INV-010, ENT-INV-012, ENT-INV-014 | SCR-REQ-INV-004 |
| US-INV-014 | REQ-INV-087, REQ-INV-088, REQ-INV-089, REQ-INV-090, REQ-INV-091, REQ-INV-092, REQ-INV-093, REQ-INV-094, REQ-INV-095, REQ-INV-096 | AC-INV-096, AC-INV-097, AC-INV-098, AC-INV-099, AC-INV-100, AC-INV-101, AC-INV-102, AC-INV-103, AC-INV-104, AC-INV-105, AC-INV-106 | RULE-INV-018, RULE-INV-029, RULE-INV-030, RULE-INV-031, RULE-INV-032, RULE-INV-047 | ENT-INV-008, ENT-INV-009, ENT-INV-010, ENT-INV-011 | SCR-REQ-INV-006 |
| US-INV-015 | REQ-INV-092, REQ-INV-097, REQ-INV-098, REQ-INV-099, REQ-INV-100, REQ-INV-101, REQ-INV-102, REQ-INV-103, REQ-INV-104 | AC-INV-102, AC-INV-107, AC-INV-108, AC-INV-109, AC-INV-110, AC-INV-111, AC-INV-112, AC-INV-113, AC-INV-114, AC-INV-115, AC-INV-116 | RULE-INV-018, RULE-INV-025, RULE-INV-029, RULE-INV-030, RULE-INV-031, RULE-INV-033, RULE-INV-034, RULE-INV-037, RULE-INV-048 | ENT-INV-004, ENT-INV-008, ENT-INV-009, ENT-INV-010, ENT-INV-011, ENT-INV-013 | SCR-REQ-INV-006, SCR-REQ-INV-007 |
| US-INV-016 | REQ-INV-051, REQ-INV-052, REQ-INV-057, REQ-INV-059, REQ-INV-060, REQ-INV-061, REQ-INV-062, REQ-INV-063, REQ-INV-064 | AC-INV-056, AC-INV-057, AC-INV-058, AC-INV-063, AC-INV-065, AC-INV-066, AC-INV-067, AC-INV-068, AC-INV-069, AC-INV-070 | RULE-INV-017, RULE-INV-018, RULE-INV-025 | ENT-INV-003, ENT-INV-004, ENT-INV-006, ENT-INV-011, ENT-INV-013 | SCR-REQ-INV-005, SCR-REQ-INV-012 |
| US-INV-017 | REQ-INV-065, REQ-INV-066, REQ-INV-067 | AC-INV-071, AC-INV-072, AC-INV-073 | RULE-INV-019 | ENT-INV-003, ENT-INV-004 | SCR-REQ-INV-007, SCR-REQ-INV-011 |
| US-INV-018 | REQ-INV-068 | AC-INV-074 | — | ENT-INV-003, ENT-INV-004 | — (cross-module) |
| US-INV-019 | REQ-INV-058, REQ-INV-069 | AC-INV-064, AC-INV-075 | RULE-INV-019, RULE-INV-024 | ENT-INV-004, ENT-INV-005 | — (cross-module) |
| US-INV-020 | REQ-INV-042, REQ-INV-049, REQ-INV-053, REQ-INV-054 | AC-INV-047, AC-INV-054, AC-INV-059, AC-INV-060 | RULE-INV-022 | ENT-INV-003, ENT-INV-004, ENT-INV-005 | SCR-REQ-INV-003 |
| US-INV-021 | REQ-INV-084, REQ-INV-105, REQ-INV-106, REQ-INV-107, REQ-INV-108, REQ-INV-109, REQ-INV-124, REQ-INV-129, REQ-INV-134 | AC-INV-092, AC-INV-093, AC-INV-117, AC-INV-118, AC-INV-119, AC-INV-120, AC-INV-121, AC-INV-122, AC-INV-138, AC-INV-139, AC-INV-145, AC-INV-150 | RULE-INV-035, RULE-INV-036, RULE-INV-037, RULE-INV-044, RULE-INV-045 | ENT-INV-003, ENT-INV-004, ENT-INV-007, ENT-INV-011, ENT-INV-012, ENT-INV-014 | SCR-REQ-INV-005, SCR-REQ-INV-008, SCR-REQ-INV-009 |
| US-INV-022 | REQ-INV-110, REQ-INV-111, REQ-INV-112 | AC-INV-123, AC-INV-124, AC-INV-125 | RULE-INV-038 | ENT-INV-003, ENT-INV-004, ENT-INV-012 | — (cross-module) |
| US-INV-023 | REQ-INV-116, REQ-INV-117, REQ-INV-118, REQ-INV-119 | AC-INV-130, AC-INV-131, AC-INV-132, AC-INV-133 | — | ENT-INV-003, ENT-INV-012 | SCR-REQ-INV-008, SCR-REQ-INV-011 |
| US-INV-024 | REQ-INV-113, REQ-INV-114, REQ-INV-115 | AC-INV-126, AC-INV-127, AC-INV-128, AC-INV-129 | RULE-INV-038, RULE-INV-039 | ENT-INV-003, ENT-INV-004, ENT-INV-012 | SCR-REQ-INV-003, SCR-REQ-INV-007, SCR-REQ-INV-008 |
| US-INV-025 | REQ-INV-120, REQ-INV-121, REQ-INV-122, REQ-INV-123, REQ-INV-125, REQ-INV-129, REQ-INV-135 | AC-INV-134, AC-INV-135, AC-INV-136, AC-INV-137, AC-INV-140, AC-INV-141, AC-INV-145, AC-INV-151 | RULE-INV-034, RULE-INV-040, RULE-INV-041, RULE-INV-044, RULE-INV-045 | ENT-INV-003, ENT-INV-004, ENT-INV-007, ENT-INV-009, ENT-INV-011, ENT-INV-014 | SCR-REQ-INV-003, SCR-REQ-INV-007, SCR-REQ-INV-009 |
| US-INV-026 | REQ-INV-126, REQ-INV-127, REQ-INV-130, REQ-INV-131 | AC-INV-142, AC-INV-143, AC-INV-146, AC-INV-147 | RULE-INV-042, RULE-INV-043, RULE-INV-046 | ENT-INV-003, ENT-INV-012, ENT-INV-014 | SCR-REQ-INV-009 |
| US-INV-027 | REQ-INV-128, REQ-INV-132, REQ-INV-133, REQ-INV-134, REQ-INV-135 | AC-INV-144, AC-INV-148, AC-INV-149, AC-INV-150, AC-INV-151 | RULE-INV-044 | ENT-INV-003, ENT-INV-012, ENT-INV-014 | SCR-REQ-INV-009 |
| US-INV-028 | REQ-INV-136, REQ-INV-137, REQ-INV-138 | AC-INV-152, AC-INV-153, AC-INV-154 | — | ENT-INV-001, ENT-INV-003, ENT-INV-010 | — (cross-module) |

### Capability flag × activity cells (CM-7)

Inventory varies by three flags only (POL-INV-011). Each INV cell of the brief §4.2 matrix traces to the Where-pattern requirements of its flag; `variants`, `serialTracking` and `b2bCredit` change no INV behaviour (stock keys on the Variant in both states — INV-P05; serials are SWR's; credit is PRC/STL's).

| Flag | Activities with the flag (brief §4.2: ✓ required · ○ optional) | REQ (flag on) | REQ (flag off) |
|---|---|---|---|
| `lotExpiry` | Grocery & food ✓ · Cleaning supplies, feed ✓ · Produce / meat by weight ✓ | REQ-INV-084, REQ-INV-105, REQ-INV-110, REQ-INV-116, REQ-INV-117, REQ-INV-124 | REQ-INV-128, REQ-INV-134 |
| `dualQuantity` | Grocery & food ○ · Fabrics & furnishings ✓ (metre) · Building & steel (row 11) ✓ · Produce / meat by weight ✓ | REQ-INV-120, REQ-INV-121, REQ-INV-123, REQ-INV-125 | REQ-INV-128, REQ-INV-135 |
| `bundles` | Electronics & appliances ✓ · Household, cosmetics, stationery, toys, accessories ✓ · Power tools & hardware ✓ · Furniture & lighting ✓ | REQ-INV-042, REQ-INV-049, REQ-INV-053 | REQ-INV-132 (no bundle Item exists while off — CAT POL-CAT-005 blocks the turn-off) |

Every activity without a mark in these three columns runs the flag-off requirements; each flag is tested in both states (DP §5 G2).

## Decisions applied

No ADR was raised by this stage: every choice below is non-breaking, settled by steps 1–6 of the resolution order and recorded here as a DEFAULT. The ADR stream of INV continues at ADR-INV-014. Decisions INV-P05 … INV-P13 (P0, P0.5) are applied as-is and not re-opened.

| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| D-01 | Transactional standard fields: `docNo` only on numbered documents (Transfer, Stock count, Stock adjustment); `statusCode` only where A6 declares a status lookup; line entities take docNo / docDate / statusCode from their header | profile entity_defaults; §3.3 NUMBERING | Add the fields to every transactional entity if the profile requires it literally |
| D-02 | Lot is a master entity (registry kind); its `code` is the lot number written on the goods, `nameAr` / `nameEn` optional and displaying the code when empty | module-registry ENTITIES OWNED; CM-1 | Generate lot numbers from a series |
| D-03 | Item stock settings (ENT-INV-014) is INV-owned: CAT declares no lot or dual-quantity marker (contract-cat CON-CAT-001, CON-CAT-008), so INV holds lotTrackedFl, dualQuantityFl and the measure unit per STANDARD Item | POL-INV-006 ("an item tracked by lot"), POL-INV-009; KB §4.1 | Move the markers to CAT in a later CAT version and consume them |
| D-04 | Inventory settings are given `cu` settings `INV_ALLOW_NEGATIVE_STOCK` (default true) and `INV_NEAR_EXPIRY_DAYS` (default 30, whole days 1–365), written through the INV settings operation, which validates the range. No RULE carries the range because no INV-declared field holds the value (C5.12); REQ-INV-119 and AC-INV-133 carry the message | module-registry AUTO (cu settings); INV-P08 | Register an INV config entity (registry IF WRONG) |
| D-05 | Quantities are in the Item's base unit; calling modules convert their units before calling INV, so INV does not consume ENT-CAT-008 Item unit | module-registry AUTO (stock in base unit); CON-CAT-004 | Accept a unit on each line and convert through CON-CAT-009 |
| D-06 | Precision: quantity and measure 3 decimals, unit and average cost 4 decimals, value 2 decimals in the tenant base currency; half-up rounding | CON-CAT-006 (3-decimal quantities); domain best practice | Change the places per tenant currency |
| D-07 | A transfer posts its out and in legs at once; no in-transit state in v1 | domain best practice; KB §6.1 (simplicity) | Add a DISPATCHED state and an in-transit balance |
| D-08 | Provisioning seeds are idempotent: a second run creates nothing | erp-core TenantProvisioningContributor practice | — |
| D-09 | Moves of one Variant in one warehouse are applied one after another, so parallel channels never lose an update | POL-INV-001; domain best practice | — |
| D-10 | A Variant that never moved answers zero quantities, not an error | domain best practice | — |
| D-11 | A reservation beyond availability is refused while negative stock is disallowed and accepted with a recorded shortfall while it is allowed; SLS and SFR decide what to do with the shortfall | POL-INV-001, POL-INV-018; INV-P08 | Refuse every reservation beyond availability |
| D-12 | When total quantity is zero or below, the next incoming cost becomes the average | IAS 2 ¶25 practice (not live-verified) | Keep the previous average |
| D-13 | Opening stock is a Stock adjustment with reason OPENING_STOCK. The bulk load takes 1–1000 rows that the browser reads from the xlsx opening stock sheet; all rows post or none. INV parses no uploaded file, which avoids the open CORE-GAP-file-document-metadata-read raised by CAT | INV-P12 (P1 chooses the mechanism); KB §6.9; contract-cat §4 (file rows) | Server-side import through the given file module once the core publishes the read |
| D-14 | Repeated scans of the same Variant (and lot) on a count add to one line | domain best practice (mobile counting) | One line per scan |
| D-15 | A count posts differences against on-hand at posting; only counted Variants are adjusted; system quantity is not shown while counting | module-registry AUTO; domain best practice | Snapshot quantities at count start; full-warehouse counts that zero uncounted Variants |
| D-16 | The Owner may correct a lot's expiry date; the change is audited | domain best practice | Fix the expiry date after first receipt |
| D-17 | A sale issue that unexpired lots cannot cover posts the rest without a lot (negative) | POL-INV-018, POL-INV-020 | Refuse the uncovered part online |
| D-18 | Stock without a lot (held before lot tracking was switched on) is issued before any lot | domain best practice | Treat it as the latest expiry |
| D-19 | The near-expiry check runs once a day per tenant and notifies each lot once (nearExpiryNotifiedAt) | POL-INV-008; module-registry (notif) | Notify on every run while in the horizon |
| D-20 | Warehouse and Branch codes come from series INV_WAREHOUSE and INV_BRANCH; the default warehouse and branch cannot be deactivated | NUMBERING rule; KB §6.1 (≤ 4 mandatory fields) | Owner-entered codes |
| D-21 | Every warehouse belongs to a branch (branchFk, default branch when empty) | DP §5 G8 (both dimensions from day one) | Let branchFk stay empty |
| D-22 | The registry key `inv.adjustment-reason` is written INV_ADJUSTMENT_REASON (lookup key grammar); seeded COUNT_DIFFERENCE, DAMAGE, EXPIRY_WRITE_OFF, OPENING_STOCK, OTHER; tenants add values | module-registry LOOKUPS OWNED (no values named by the user); US-INV-015 examples | Seed no value beyond the three the system uses |
| D-23 | PUR, SLS and SFR submit receipts, issues and reservations by calling INV operations; INV subscribes to none of their events, keeping the DAG downward-only. INV reads CAT state at request time and does not consume CON-CAT-018 in v1 | DP §5 G5; dependency-graph (PUR/SLS/SFR → INV); module-registry DEPENDENCIES (events) | Subscribe to their events through the given events module |
| D-24 | Reservations do not expire automatically in v1; the owning module releases them | module-registry AUTO (SLS, SFR own the order lifecycle) | Add a reservation expiry time |
| D-25 | The negative-stock alert is sent once when a Variant × warehouse balance crosses from zero or above to below zero | POL-INV-019 | Alert on every issue while negative |
| D-26 | Costs and values are shown only on the Stock valuation screen (Owner); stock balances show quantities only, so the Seller/Cashier sees no cost | KB §3 D8 (role presets); domain best practice | Show cost on balances to the Warehouse role |
| D-27 | When `variants` is off, callers pass the Item's default Variant (CON-CAT-002); INV always keys on a Variant | INV-P05; CON-CAT-002 | — |

## Access summary

Aggregate of B4 (B4 is the source). One SEC_PAGES row per screen requirement; the security module derives `PERM_<PAGE_CODE>_<ACTION>`.

| SCR-REQ | Page code | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|---|
| SCR-REQ-INV-001 | INV_WAREHOUSES | Owner, Warehouse | Owner | Owner | Owner |
| SCR-REQ-INV-002 | INV_BRANCHES | Owner, Warehouse | Owner | Owner | Owner |
| SCR-REQ-INV-003 | INV_STOCK_BALANCES | Owner, Warehouse, Seller/Cashier | — | — | — |
| SCR-REQ-INV-004 | INV_STOCK_MOVES | Owner, Warehouse | — | — | — |
| SCR-REQ-INV-005 | INV_TRANSFERS | Owner, Warehouse | Owner, Warehouse | Owner, Warehouse | Owner, Warehouse |
| SCR-REQ-INV-006 | INV_COUNTS | Owner, Warehouse | Owner, Warehouse | Owner, Warehouse | Owner, Warehouse |
| SCR-REQ-INV-007 | INV_ADJUSTMENTS | Owner, Warehouse | Owner, Warehouse | Owner, Warehouse | Owner, Warehouse |
| SCR-REQ-INV-008 | INV_LOTS | Owner, Warehouse | — | Owner | — |
| SCR-REQ-INV-009 | INV_ITEM_STOCK_SETTINGS | Owner, Warehouse | Owner | Owner | — |
| SCR-REQ-INV-010 | INV_OPENING_STOCK | Owner | Owner | — | — |
| SCR-REQ-INV-011 | INV_SETTINGS | Owner | — | Owner | — |
| SCR-REQ-INV-012 | INV_STOCK_VALUATION | Owner | — | — | — |

Cross-module operations (A8) are in-process calls: the calling module authorises its own caller, as CAT's contract states for its operations; tenancy applies to every call.
══════════════════════════════════════════════════════════════════

