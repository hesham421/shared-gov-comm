# CONTRACT — Inventory / المخزون (INV) — outbound promise
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Profile : commerce   Stage : P1.5
Inputs : srs-inv.md (P1 pass 1, round 1), registry-srs (P1 — INV v1)
Counts : CON 21 (ENTITY 5 · OPERATION 16) · ADR 0 (next free ADR-INV-014)
Status : DRAFT — P1.5 pass 1, round 1 · prepared by claude:opus
══════════════════════════════════════════════════════════════════

## 0 — Reading this contract

- This is what other modules may depend on before INV is built: the SHARED entities they
  reference and the operations and events they call or receive. It promises; it never
  implements. The INV backend plan (P3.1) chooses paths and verbs and states
  `Honours: CON-INV-…` on the block that implements each item.
- An item with a `Signature` line is an operation (or a published event); an item without one
  is an entity promise.
- **Identifiers.** Every INV entity identifier is a `BIGINT` identity key assigned by the
  database (stack.db.pk_generation = identity). A consumer's HARD-FK column to an INV entity is
  `BIGINT` — copied verbatim, no other identifier type exists in v1.
- **Not promised (private):** Stock move (ENT-INV-004 — published only as the event of
  CON-INV-020), Reservation (ENT-INV-005 — reached only through CON-INV-011 … 013, 015),
  Transfer and its lines (ENT-INV-006, 007), Stock count and its lines (ENT-INV-008, 009),
  Stock adjustment and its lines (ENT-INV-010, 011), Item stock settings (ENT-INV-014 — read
  only through CON-INV-018). No table, column list beyond key columns, or endpoint path is
  promised.
- **Conventions binding every operation (from the SRS):**
  - Calls are in-process (`crossmodule` APIs); the calling module authorises its own caller;
    tenancy applies to every call (SRS Access summary).
  - Quantities are in the Item's base unit (CON-CAT-001 `baseUomCode`); the caller converts
    other units first (SRS D-05). Quantity and measure: 3 decimals; unit and average cost:
    4 decimals; value: 2 decimals in the tenant base currency (SRS D-06).
  - A warehouse input is optional while the tenant has exactly one active warehouse (the
    default is applied) and required when two or more are active (RULE-INV-008, REQ-INV-013,
    REQ-INV-017).
  - When `variants` is off the caller passes the Item's default Variant (CON-CAT-002); INV
    always keys on a Variant (SRS D-27).
  - Every receipt, issue and reservation request carries a caller-supplied idempotency key
    (text, ≤ 64); a key repeated with the same request returns the first result and records
    nothing new (RULE-INV-024, REQ-INV-058) — this is not an error. A key repeated with a different
    request is refused, so a caller never mistakes a new request for one already recorded
    (RULE-INV-050).
  - Source documents are passed as type (INV_SOURCE_DOC_TYPE — open lookup; calling modules add
    their document types as data), identifier (BIGINT), number (text ≤ 30) and optional line
    identifier (BIGINT). INV keeps them as SOFT references with no FK (ENT-INV-004.sourceDocRef).
  - Errors are stated by meaning; each carries its RULE's Arabic and English message (SRS A5).
  - Capability flags (`lotExpiry`, `dualQuantity`, `bundles`) are read by INV through the
    FeatureFlags API; behaviour marked "flag on" applies only while the flag is on (REQ-INV-132).

## 1 — ENTITY items

### CON-INV-001 — المستودع / Warehouse (ar / en)

Entity    : ENT-INV-001 — key columns: `code` (business key, series INV_WAREHOUSE, read-only, text 20), `warehousePk` (identifier a consumer stores) · identifier type: BIGINT
Consumers : PUR, SLS, SFR, INS (HARD-FK `BIGINT` → warehousePk)
Promise   : a deactivated Warehouse stays readable by its identifier and is excluded from every
            new selection; consumers are notified through CON-INV-021 and their records are never
            changed (notify, never cascade — RULE-INV-005). Exactly one active default Warehouse
            exists per tenant from provisioning on (REQ-INV-001, RULE-INV-006).
Traces    : ENT-INV-001, REQ-INV-001, REQ-INV-005, REQ-INV-009, REQ-INV-019

### CON-INV-002 — الفرع / Branch (ar / en)

Entity    : ENT-INV-002 — key columns: `code` (business key, series INV_BRANCH, read-only, text 20), `branchPk` (identifier a consumer stores) · identifier type: BIGINT
Consumers : PUR, SLS, STL, TAX, INS (HARD-FK `BIGINT` → branchPk)
Promise   : a deactivated Branch stays readable by its identifier and is excluded from every new
            selection; consumers are notified through CON-INV-021 and their records are never
            changed (RULE-INV-014). Exactly one active default Branch exists per tenant from
            provisioning on (REQ-INV-020, RULE-INV-015). Every Warehouse belongs to a Branch
            (ENT-INV-001.branchFk).
Traces    : ENT-INV-002, REQ-INV-020, REQ-INV-024, REQ-INV-028, REQ-INV-033

### CON-INV-003 — التشغيلة / Lot (ar / en)

Entity    : ENT-INV-012 — key columns: `variantFk` + `code` (business key — the lot number written on the goods, unique per Variant, text 40), `lotPk` (identifier a consumer stores) · identifier type: BIGINT
Consumers : PUR (receipt), SLS (FEFO issue) — flag `lotExpiry` on
Promise   : one lot number of a Variant has one expiry date (RULE-INV-036); a Lot is created by
            INV on the first receipt or increase that names it (REQ-INV-106) — consumers never
            create a Lot directly. A Lot whose expiry date is before today (tenant time zone) is
            never sold (RULE-INV-038).
Traces    : ENT-INV-012, REQ-INV-106, REQ-INV-109, REQ-INV-113

### CON-INV-004 — رصيد المخزون / Stock balance (ar / en)

Entity    : ENT-INV-003 — key columns: `variantFk` + `warehouseFk` + `lotFk` (business key — one record per Variant × warehouse × lot, lot empty for stock without a lot), `stockBalancePk` (identifier) · identifier type: BIGINT
Consumers : SLS, SFR, INS (read through CON-INV-010; no consumer stores stockBalancePk)
Promise   : one stock for every channel (REQ-INV-035); maintained only by stock moves, never
            written by another module. Reserved and available quantities are derived at read time
            (REQ-INV-038).
Traces    : ENT-INV-003, REQ-INV-035, REQ-INV-038, REQ-INV-040

### CON-INV-005 — تقييم المخزون / Stock valuation (ar / en)

Entity    : ENT-INV-013 — key columns: `variantFk` (business key — one record per Variant across the tenant), `stockValuationPk` (identifier) · identifier type: BIGINT
Consumers : SLS (margin), INS (read through CON-INV-016); ACC receives costs through CON-INV-020
Promise   : perpetual weighted-average cost per Variant across the tenant (REQ-INV-059 …
            REQ-INV-062); maintained only by stock moves.
Traces    : ENT-INV-013, REQ-INV-059, REQ-INV-063

## 2 — OPERATION items

### CON-INV-006 — قراءة مستودع / Read a warehouse (ar / en)
Signature : readWarehouse(warehousePk: BIGINT) → Warehouse {warehousePk, code, nameAr, nameEn, branchPk, isDefaultFl, isActiveFl} · errors: not found
Entity    : ENT-INV-001
Notes     : returns inactive Warehouses too (RULE-INV-005).
Traces    : REQ-INV-009, REQ-INV-005

### CON-INV-007 — المستودعات النشطة / List the active warehouses (ar / en)
Signature : listActiveWarehouses() → {activeCount, warehouses: [{warehousePk, code, nameAr, nameEn, branchPk, isDefaultFl}]} · errors: none
Entity    : ENT-INV-001
Notes     : consumers hide their own warehouse selector while activeCount is 1 (POL-INV-004).
Traces    : REQ-INV-019

### CON-INV-008 — قراءة فرع / Read a branch (ar / en)
Signature : readBranch(branchPk: BIGINT) → Branch {branchPk, code, nameAr, nameEn, isDefaultFl, isActiveFl} · errors: not found
Entity    : ENT-INV-002
Notes     : returns inactive Branches too (RULE-INV-014).
Traces    : REQ-INV-028, REQ-INV-024

### CON-INV-009 — الفروع النشطة / List the active branches (ar / en)
Signature : listActiveBranches() → {activeCount, branches: [{branchPk, code, nameAr, nameEn, isDefaultFl}]} · errors: none
Entity    : ENT-INV-002
Notes     : consumers hide their own branch selector while activeCount is 1 (POL-INV-016).
Traces    : REQ-INV-033

### CON-INV-010 — توفّر متغيّر الصنف / Read the availability of a Variant (ar / en)
Signature : readAvailability(variantPk: BIGINT, warehousePk?: BIGINT) → {perWarehouse: [{warehousePk, onHandQty, reservedQty, availableQty, onHandMeasureQty?, measureUomCode?}], total: {onHandQty, reservedQty, availableQty, onHandMeasureQty?}} · errors: Variant not found
Entity    : ENT-INV-003
Notes     : per active warehouse and in total. Available = on-hand outside expired lots minus
            ACTIVE reservations (RULE-INV-038). A Variant that never moved answers zeros, not an
            error (REQ-INV-041). Flag `bundles` on: a bundle Variant answers the derived
            availability of its components (REQ-INV-042). Flag `dualQuantity` on: measure and its
            unit for dual-quantity Items (REQ-INV-121).
Traces    : REQ-INV-040, REQ-INV-041, REQ-INV-042, REQ-INV-121

### CON-INV-011 — حجز مخزون / Reserve stock (ar / en)
Signature : reserve(variantPk: BIGINT, warehousePk?: BIGINT, quantity, sourceDoc {typeCode, ref, no, lineRef?}, idempotencyKey) → {reservations: [{reservationPk: BIGINT, variantPk, warehousePk, statusCode, originalQty, reservedQty, shortfallQty, bundleVariantPk?}]} · errors: warehouse required, warehouse inactive, Variant inactive, invalid quantity, not enough available (negative stock disallowed), bundle without active components, idempotency key already used for a different request
Entity    : ENT-INV-003
Notes     : while negative stock is allowed (tenant default) a request beyond availability is
            accepted with its shortfall recorded; the caller decides what to do with it
            (REQ-INV-045, SRS D-11). Flag `bundles` on: a bundle is reserved as one Reservation per
            component (REQ-INV-049). The caller keeps reservationPk as a SOFT reference (no FK) to
            release or consume it; the Reservation itself is private. Reservations do not expire
            automatically in v1 — the owning module releases them (SRS D-24).
            RULE-INV-007, 008, 017, 018, 020, 022, 024, 050.
Traces    : REQ-INV-043, REQ-INV-044, REQ-INV-045, REQ-INV-049

### CON-INV-012 — تحرير حجز / Release a reservation (ar / en)
Signature : releaseReservation(reservationPk: BIGINT) → {reservationPk, statusCode RELEASED, reservedQty 0, closedAt} · errors: not found, reservation closed
Entity    : ENT-INV-003
Notes     : only an ACTIVE reservation is released (RULE-INV-021); availability rises at once.
Traces    : REQ-INV-046, REQ-INV-048

### CON-INV-013 — حجوزات مستند / Read the reservations of a source document (ar / en)
Signature : reservationsOfDocument(sourceDocTypeCode, sourceDocRef: BIGINT) → [{reservationPk, variantPk, warehousePk, statusCode, reservedQty, shortfallQty, bundleVariantPk?}] · errors: none (empty list when none)
Entity    : ENT-INV-003
Traces    : REQ-INV-050

### CON-INV-014 — استلام مخزون / Receive stock (ar / en)
Signature : receiveStock(warehousePk?: BIGINT, moveTypeCode RECEIPT | RETURN_IN, lines: [{variantPk, quantity, unitCost?, lotNo?, lotExpiryDate?, measureQty?, sourceLineRef?}], sourceDoc {typeCode, ref, no}, idempotencyKey) → {moves: [{stockMovePk: BIGINT, variantPk, warehousePk, lotPk?, quantity, measureQty?, unitCost, moveValue}]} · errors: warehouse required, warehouse inactive, Variant inactive or of a BUNDLE Item, invalid quantity, no known cost, lot number and expiry required, lot recorded with a different expiry, measure required, idempotency key already used for a different request
Entity    : ENT-INV-003
Notes     : one move per line at the line's unit cost; a line without a cost takes the current
            average and is refused when the Variant was never valued (RULE-INV-025). Each receipt
            recalculates the weighted-average cost (REQ-INV-059). All lines are recorded or none.
            Flag `lotExpiry` on: lot-tracked Items require lot number and expiry; the Lot is reused
            or created (REQ-INV-105, REQ-INV-106). Flag `dualQuantity` on: dual-quantity Items
            require the measure (REQ-INV-120). Whether an Item needs a lot or a measure is read
            beforehand through CON-INV-018. A bundle Variant is never received or returned in: the
            line is refused as "of a BUNDLE Item" (RULE-INV-018). RULE-INV-007, 008, 017, 018, 024,
            025, 035, 036, 040, 050.
Traces    : REQ-INV-051, REQ-INV-064, REQ-INV-105, REQ-INV-106, REQ-INV-107, REQ-INV-120

### CON-INV-015 — صرف مخزون / Issue stock (ar / en)
Signature : issueStock(warehousePk?: BIGINT, moveTypeCode ISSUE | RETURN_OUT, saleFl, offlineFl, lines: [{variantPk, quantity, lotPk? (non-sale only), measureQty?, reservationPk?, sourceLineRef?}], sourceDoc {typeCode, ref, no}, idempotencyKey) → {moves: [{stockMovePk: BIGINT, variantPk, warehousePk, lotPk?, quantity, measureQty?, unitCost, moveValue, bundleVariantPk?}]} · errors: warehouse required, warehouse inactive, Variant inactive, invalid quantity, not enough stock (online, negative stock disallowed), reservation closed, reservation not of this line (Variant, warehouse or source document differs), bundle without active components, lot expired, measure required, idempotency key already used for a different request
Entity    : ENT-INV-003
Notes     : moves are valued at the Variant's current average cost (REQ-INV-061). An issue that
            references a reservation consumes it (REQ-INV-047). A synchronised offline POS sale
            (offlineFl true) is always recorded, whatever the negative-stock setting, even when its
            warehouse, Variant or Item became inactive after the sale, and in the default warehouse
            when it names none; INV then tells the Warehouse role which record was inactive
            (REQ-INV-069, ADR-INV-052) — "warehouse required", "warehouse inactive" and "Variant
            inactive" are never answered to an offline sale. Flag `bundles` on: a bundle is issued as one move per component and
            none for the bundle itself (REQ-INV-053). Flag `lotExpiry` on: a sale issue draws stock
            without a lot first, then unexpired lots by nearest expiry, and posts any uncovered
            quantity without a lot (REQ-INV-110 … 113); a non-sale issue or return to supplier
            naming an expired lot is refused (REQ-INV-114). One answer may therefore carry several
            moves per line. All lines are recorded or none.
            A referenced reservation must be of the line's Variant and warehouse and of the request's source
            document (RULE-INV-049). A repeated idempotency key submitted concurrently waits for the first
            submission and returns its answer.
            While negative stock is disallowed, a line that names a lot is also refused when that lot's
            on-hand in the warehouse does not cover it (RULE-INV-019).
            RULE-INV-007, 008, 017, 018, 019, 021, 022, 024, 038, 039, 040, 049, 050.
Traces    : REQ-INV-052, REQ-INV-053, REQ-INV-067, REQ-INV-069, REQ-INV-110, REQ-INV-111, REQ-INV-114

### CON-INV-016 — قراءة التقييم / Read the valuation of a Variant (ar / en)
Signature : readValuation(variantPk: BIGINT) → {variantPk, totalOnHandQty, averageUnitCost, stockValue} · errors: Variant not found
Entity    : ENT-INV-013
Notes     : a Variant never valued answers zero quantity and value with no average (consistent
            with REQ-INV-041; the caller treats a missing average as "no known cost").
Traces    : REQ-INV-063

### CON-INV-017 — قراءة تشغيلة / Read a lot (ar / en)
Signature : readLot(lotPk: BIGINT) → Lot {lotPk, variantPk, code, nameAr, nameEn, expiryDate, expiredFl, isActiveFl} · errors: not found
Entity    : ENT-INV-012
Traces    : REQ-INV-109

### CON-INV-018 — إعدادات مخزون الصنف / Read the stock settings of an Item (ar / en)
Signature : readItemStockSettings(itemPk: BIGINT) → {lotTrackedFl, dualQuantityFl, measureUomCode?} · errors: none (false, false, empty when the Item has no record)
Entity    : ENT-INV-014 (read-only answer; the entity itself stays private)
Traces    : REQ-INV-129

### CON-INV-019 — فحص إيقاف مفتاح القدرة / Capability turn-off check (ar / en)
Signature : canTurnOff(flag lotExpiry | dualQuantity) → {allowedFl, dependentCount, dependents: [{itemPk, code, nameAr, nameEn}] (at most 20), messageAr?, messageEn?} · errors: unknown flag
Entity    : ENT-INV-014 (read-only answer)
Notes     : called by the capability-flag owner (P-02); not allowed while any Item depends on the
            flag, with a message naming the next step (RULE-INV-046).
Traces    : REQ-INV-130, REQ-INV-131

### CON-INV-020 — حدث حركة المخزون / Stock-move event (ar / en)
Signature : event StockMoveRecorded {stockMovePk: BIGINT, docDate, moveTypeCode, variantPk, warehousePk, lotPk?, quantity, measureQty?, unitCost, moveValue, currencyCode, sourceDocTypeCode, sourceDocRef, sourceDocNo, sourceLineRef?, bundleVariantPk?, offlineFl} → delivered once per committed move, after commit · errors: none (never delivered when the transaction rolls back)
Entity    : ENT-INV-013 (the cost it carries); the move itself stays private
Notes     : consumers ACC (posting), SWR, INS; stockMovePk lets a consumer process each event
            once. Moves are never changed or deleted (RULE-INV-023), so no correction event exists —
            a correction arrives as a new ADJUSTMENT_IN / ADJUSTMENT_OUT move. `currencyCode` (ISO
            4217, the tenant base currency of unitCost and moveValue) is ADDITIVE in v1 (ADR-INV-015).
Traces    : REQ-INV-057, REQ-INV-055

### CON-INV-021 — حدث تغيّر بيانات المخزون الأساسية / Inventory master-change event (ar / en)
Signature : event InventoryMasterChanged {entity ENT-INV-001 | ENT-INV-002, identifier: BIGINT, changeKindCode (INV_CHANGE_KIND: CREATED, UPDATED, DEACTIVATED, REACTIVATED, DEFAULT_CHANGED)} → delivered once per committed Warehouse or Branch change, after commit · errors: none (never delivered on rollback)
Entity    : ENT-INV-001, ENT-INV-002
Notes     : consumers PUR, SLS, SFR, STL, TAX, INS refresh selectors and active counts; the event
            notifies and never cascades (ARCH-5).
Traces    : REQ-INV-034

## 2.5 — Given usage (repeated from the SRS)

```yaml name=given-usage
rows:
  - {given: erp-core, module: modules.cu, via: com.erp.cu.crossmodule.SettingsApi, purpose: "read the lotExpiry, dualQuantity and bundles capability flags behind the FeatureFlags API (REQ-INV-132), read and write the INV_ALLOW_NEGATIVE_STOCK and INV_NEAR_EXPIRY_DAYS settings (REQ-INV-065, REQ-INV-118), and read the INV_MEASURE_UOM_KEY setting that names the consumed unit-of-measure lookup key (REQ-INV-125)"}
  - {given: erp-core, module: modules.sequence, via: com.erp.sequence.crossmodule.NumberSeriesApi, purpose: "issue the codes INV_WAREHOUSE and INV_BRANCH and the document numbers INV_TRANSFER, INV_COUNT and INV_ADJUSTMENT (RULE-INV-001, RULE-INV-010, RULE-INV-030)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.OutboxPublisher, purpose: "record the stock-move event and the inventory master-change event in the change transaction and deliver them after commit (REQ-INV-057, REQ-INV-034)"}
  - {given: erp-core, module: modules.audit, via: com.erp.audit.crossmodule.AuditApi, purpose: "record the change history of stock documents, warehouses, branches, lots and Item stock settings (REQ-INV-077)"}
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "load the values of the INV_* lookup keys and the CAT unit-of-measure key at runtime, and serve INV's six lookup keys to INV's screens through the common helper OwnedLookups.read over this interface (A6)"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantProvisioningContributor, purpose: "seed the default branch and the default warehouse of each new tenant (REQ-INV-020, REQ-INV-001)"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantRunner, purpose: "run the daily near-expiry check once per tenant (REQ-INV-117)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.permission.PermissionContributor, purpose: "register the INV page codes so the security module derives their VIEW/CREATE/UPDATE/DELETE permissions, and the grants of the Owner, Seller/Cashier and Warehouse role presets (REQ-INV-136, REQ-INV-137)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.crossmodule.SecUserDirectoryApi, purpose: "find the users holding the Warehouse role as recipients of the negative-stock and near-expiry notifications (REQ-INV-068, REQ-INV-117)"}
  - {given: erp-core, module: modules.notif, via: com.erp.notif.crossmodule.NotificationDispatchApi, purpose: "send the negative-stock and near-expiry notifications (REQ-INV-068, REQ-INV-117)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.ReportProvider, purpose: "register the report INV_OPENING_STOCK_SHEET (REQ-INV-073)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.crossmodule.PrintApi, purpose: "produce the xlsx opening stock sheet (REQ-INV-073)"}
```

The contract re-creates no given's entity: settings stay in `cu`, lookup values in `mdl`,
numbers in `sequence`, users and roles in `sec`, the tenant in `tenant`.

## 3 — Stability

- v1 is the first contract of INV; every item above is ADDITIVE. The gate-analysis revisions
  added, still ADDITIVE: the error meaning "idempotency key already used for a different
  request" on CON-INV-011, 014 and 015, the field `currencyCode` of CON-INV-020, and the stated
  refusal of a bundle receipt (CON-INV-014) and of a lot decrease beyond the lot (CON-INV-015).
  Gate-analysis revise 3 widened, still ADDITIVE, the offline promise of CON-INV-015: an offline sale
  is recorded although a record it names became inactive (fewer refusals, never more).
- Items may be added freely. Changing or removing an item a consumer depends on — an identifier
  type, a key column, a signature input or output, an error meaning, an event field — happens
  only in a BREAKING version with an ADR; `gov.py graph` then raises a resolution event on every
  consumer edge that cites it.
- Lookup values of the open keys INV_SOURCE_DOC_TYPE and INV_ADJUSTMENT_REASON grow as data and
  are not a contract change.

## 4 — Decisions applied (no ADR raised)

Every choice here is settled by the SRS and is non-breaking; no ADR was raised and the ADR stream
continues at ADR-INV-014. No question was raised.

| # | Decision | Source |
|---|---|---|
| C-1 | ENTITY items are the SRS SHARED entities (ENT-INV-001, 002, 003, 012, 013). No other module's A8 `module-dependencies` block consumes INV yet; the consumers named are those of the SRS cross-module column and the registry. | SRS A3, A8; engine §1 |
| C-2 | OPERATION items are exactly the SRS A8 "Operations INV offers to other modules" table, one item per row, including its two published events. | SRS A8 |
| C-3 | Reservation and Stock move stay private: consumers hold `reservationPk` and `stockMovePk` only as SOFT references (no FK), returned by CON-INV-011 / 014 / 015 and carried by CON-INV-020. | SRS A3 ownership; D-23 |
| C-4 | Item stock settings stay private; its content is promised only as the read answers of CON-INV-018 and CON-INV-019. | SRS ENT-INV-014 ownership |
| C-5 | A Variant never valued answers zeros in CON-INV-016 instead of "not found", consistent with REQ-INV-041. | SRS D-10 |

## 5 — Self-check

- [x] Every SHARED entity has an ENTITY item; no other module's A8 block consumes INV yet.
- [x] Every operation item has a `Signature` line and names no path or verb.
- [x] Every item traces to an ENT and a REQ; no private entity is promised.
- [x] No question raised; CON-INV-001 … CON-INV-021 continuous.
- [x] §2.5 carries the SRS's `given-usage` rows verbatim, same order.
══════════════════════════════════════════════════════════════════
