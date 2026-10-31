<!-- source: PHASE:SVC-API / SUB:SVC-API-INT -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-INV-052, DBF-INV-066, DBF-INV-067, DBF-INV-068, DBF-INV-069, DBF-INV-075, DBF-INV-080, REQ-INV-019, REQ-INV-033, REQ-INV-034, REQ-INV-040, REQ-INV-043, REQ-INV-044, REQ-INV-045, REQ-INV-046, REQ-INV-047, REQ-INV-048, REQ-INV-049, REQ-INV-050, REQ-INV-051, REQ-INV-052, REQ-INV-053, REQ-INV-057, REQ-INV-058, REQ-INV-063, REQ-INV-069, REQ-INV-109, REQ-INV-110, REQ-INV-111, REQ-INV-112, REQ-INV-113, REQ-INV-114, REQ-INV-129, REQ-INV-130, REQ-INV-131, REQ-INV-141 -->
<!-- SUB:SVC-API-INT:START traces=REQ-INV-141,REQ-INV-019,REQ-INV-033,REQ-INV-040,REQ-INV-043,REQ-INV-044,REQ-INV-045,REQ-INV-046,REQ-INV-047,REQ-INV-048,REQ-INV-049,REQ-INV-050,REQ-INV-051,REQ-INV-052,REQ-INV-053,REQ-INV-058,REQ-INV-063,REQ-INV-069,REQ-INV-109,REQ-INV-110,REQ-INV-111,REQ-INV-112,REQ-INV-113,REQ-INV-114,REQ-INV-129,REQ-INV-130,REQ-INV-131,REQ-INV-057,REQ-INV-034,DBF-INV-066,DBF-INV-067,DBF-INV-068,DBF-INV-069,DBF-INV-075,DBF-INV-080,DBF-INV-052 -->
### In-process surface — `InventoryApi` (com.commerce.inv.crossmodule)

One injected bean (conventions.module_interface: in_process — one deployable; no HTTP client, no base path, no
timeout, no network error path). Quantities are in the Item's base unit (SRS D-05); every receive / issue /
reserve carries an idempotency key (text ≤ 64) — a repeated key returns the first answer and records nothing
(RULE-INV-024, REQ-INV-058). Errors are `LocalizedException` with the codes of §5 / §5.1. No method has a REST
route (ADR-INV-024).

**Authority (ADR-INV-042).** `InventoryApi` does **not** call the REST-facing services. Every method delegates
to the internal engine package `com.commerce.inv.engine`, which carries no `@PreAuthorize` and has no REST
path; the REST services carry `@PreAuthorize` on every method and reach the same engines after their own check.
All fourteen methods of CON-INV-006 … 019 bypass the INV permission check, for one reason: the calling module
authorises its own caller against its own permissions before it calls INV (SRS access summary). A cashier selling at the POS holds only PERM_INV_STOCK_BALANCES_VIEW, so a
check on the INV services would refuse every POS issue and every reservation; a storefront shopper and the
system jobs of PUR / SLS hold no INV authority at all. Tenancy still applies to every call. INV's published
operations are stock postings made for users who by design hold no INV write permission. They follow CORE-GAP-in-process-caller-authority (OPEN) when the core
answers; no `InventoryApi` signature changes then. An ArchUnit rule of `InvModuleBoundaryTest` checks that
`com.commerce.inv.engine` is used only by `service` and `crossmodule`, never by `controller`.

| Method | Honours | Implementation |
|---|---|---|
| readWarehouse(warehousePk) | CON-INV-006 | InventoryReadEngine — QR-INV-002 → {warehousePk, code, nameAr, nameEn, branchPk, isDefaultFl, isActiveFl}; inactive returned |
| listActiveWarehouses() | CON-INV-007 | InventoryReadEngine — QR-INV-001 (active only, all rows) + QR-INV-006 → {activeCount, warehouses} |
| readBranch(branchPk) | CON-INV-008 | InventoryReadEngine — QR-INV-009 → Branch; inactive returned |
| listActiveBranches() | CON-INV-009 | InventoryReadEngine — QR-INV-008 (active only) + QR-INV-013 → {activeCount, branches} |
| readAvailability(variantPk, warehousePk?) | CON-INV-010 | AvailabilityEngine.availability (the engine behind API-INV-017) |
| reserve(variantPk, warehousePk?, quantity, sourceDoc, idempotencyKey) | CON-INV-011 | ReservationEngine — see "Reserve" below |
| releaseReservation(reservationPk) | CON-INV-012 | ReservationEngine — see "Release" below |
| reservationsOfDocument(sourceDocTypeCode, sourceDocRef) | CON-INV-013 | ReservationEngine — reservations of the document (intent R-3), empty list when none |
| receiveStock(warehousePk?, RECEIPT / RETURN_IN, lines, sourceDoc, idempotencyKey) | CON-INV-014 | StockPostingEngine — see "Receive" below |
| issueStock(warehousePk?, ISSUE / RETURN_OUT, saleFl, offlineFl, lines, sourceDoc, idempotencyKey) | CON-INV-015 | StockPostingEngine + ReservationEngine — see "Issue" below |
| readValuation(variantPk) | CON-INV-016 | InventoryReadEngine.valuation (the engine behind API-INV-051) |
| readLot(lotPk) | CON-INV-017 | InventoryReadEngine — QR-INV-039 + expiredFl |
| readItemStockSettings(itemPk) | CON-INV-018 | InventoryReadEngine.itemStockSettings (the engine behind API-INV-044) |
| canTurnOff(lotExpiry / dualQuantity) | CON-INV-019 | InventoryReadEngine — QR-INV-042 filtered on the flag: count and the first 20 (Item code / names through integrate (XM-INV-015)); count > 0 → allowedFl false with the RULE-INV-046 message, {flag} = «التشغيلة والصلاحية» / "Lot and expiry" or «الكمية المزدوجة» / "Dual quantity", {count} = the count; unknown flag → INV-400 |
| event inv.StockMoveRecorded | CON-INV-020 | posting engine step 9 — {stockMovePk, docDate, moveTypeCode, variantPk, warehousePk, lotPk?, quantity, measureQty?, unitCost, moveValue, currencyCode (ADDITIVE — ADR-INV-015), sourceDocTypeCode, sourceDocRef, sourceDocNo, sourceLineRef?, bundleVariantPk?, offlineFl}; a measure-only move carries quantity 0 (ADR-INV-017) |
| event inv.InventoryMasterChanged | CON-INV-021 | API-INV-003 … 007, 011 … 015 — {entity ENT-INV-001 / ENT-INV-002, identifier, changeKindCode} |

Honours      : CON-INV-006, CON-INV-007, CON-INV-008, CON-INV-009, CON-INV-010, CON-INV-011, CON-INV-012, CON-INV-013, CON-INV-014, CON-INV-015, CON-INV-016, CON-INV-017, CON-INV-018, CON-INV-019, CON-INV-020, CON-INV-021

**Idempotency guard (ADR-INV-044).** Receive, issue and reserve begin with
`SELECT pg_advisory_xact_lock(hashtextextended(:tenantId || ':' || :idempotencyKey, 0))` (native, tenant in the
hash — ADR-INV-041), **then** run the R-2 / R-6 lookup by key. The rows found carry REQUEST_HASH (DBF-INV-220, 221):
the hash of this request's canonical form — taken over the request as submitted: absent fields stay absent, and no default (warehouse, docDate), trim or lookup is applied before hashing (ADR-INV-048) — equal to it → the first answer, nothing recorded
(RULE-INV-024); different → INV-409-IDEMPOTENCY-KEY-REUSED (RULE-INV-050), nothing recorded. A new key records the
hash on every row the request writes. Two simultaneous submissions with one key (a POS
sync retried by the client) therefore serialise: the second waits until the first commits or rolls back, then
finds the first's reservations or moves and returns them, recording nothing. The lock is released with the
transaction; IDEMPOTENCY_KEY stays a non-unique index (D-P2-05), because one request records several rows under
one key.

**Common resolution.** Warehouse: given → QR-INV-002 FOR SHARE, active (RULE-INV-007); absent → QR-INV-006 = 1 →
default (QR-INV-005), ≥ 2 → INV-422-WAREHOUSE-REQUIRED (RULE-INV-008, REQ-INV-013, 017). **Offline sale** (offlineFl true — ADR-INV-052, REQ-INV-069): the active checks of
RULE-INV-007 and RULE-INV-018 are skipped, so an inactive warehouse, Variant or Item still receives the move, and a sale
naming no warehouse takes the default warehouse (QR-INV-005) whatever the active count (RULE-INV-008); each inactive
record skipped is queued for the after-commit notification INV_OFFLINE_SALE_INACTIVE {document = sourceDocNo, record =
the warehouse or Variant code and name} to the users holding the Warehouse role (InvNotifier). Quantity > 0, ≤ 3 dp,
measure ≤ 3 dp (RULE-INV-017). Variant active, STANDARD Item — integrate (XM-INV-014) (RULE-INV-018). Bundles: only an
**issue** or a **reserve** names a bundle Variant — with bundles on, its active component lines are read through
integrate (XM-INV-012); none → INV-422-BUNDLE-NO-COMPONENTS (RULE-INV-022); the request is exploded to the components
(bundle quantity × component quantity) with bundleVariantFk set (REQ-INV-049, 053). A **receive** (RECEIPT or
RETURN_IN) line naming a bundle Variant is refused with INV-422-VARIANT-INACTIVE (RULE-INV-018) and never exploded,
because a bundle holds no stock (POL-INV-014, CON-INV-014).

**Reserve** (REQ-INV-043 … 045, 049). Idempotency: advisory lock, then reservations with this key (R-2) → return
them. Resolve → per (component) Variant, in ascending (variant, warehouse) order: lock-or-create the pair's
lot-less balance row (QR-INV-017 — the serialisation point of every reservation, issue, transfer leg and
adjustment of that Variant × warehouse, lot moves included, because the posting engine locks the same row first —
ADR-INV-026) → available = QR-INV-016 sellable − QR-INV-026 reserved → quantity >
available: INV_ALLOW_NEGATIVE_STOCK false → INV-409-RESERVE-NOT-AVAILABLE {available} (RULE-INV-020); true →
shortfall = quantity − max(available, 0) → insert (R-1) originalQty DBF-INV-067 = reservedQty DBF-INV-068 =
quantity, shortfallQty DBF-INV-069, sourceDocTypeCode / Ref / No / LineRef DBF-INV-070 … 073, idempotencyKey
DBF-INV-075, variantFk DBF-INV-076, warehouseFk DBF-INV-077, bundleVariantFk DBF-INV-078, docDate DBF-INV-079 =
today, statusCode DBF-INV-080 = ACTIVE. Concurrency: two reservations of the last units serialise on the pair
row; the second sees the first's reservation in QR-INV-026.

**Release** (REQ-INV-046, 048). Reservation by PK FOR UPDATE (R-4) → already RELEASED → return it unchanged
(statusCode RELEASED, reservedQty 0, the first closedAt), nothing written, so a retry after a successful release is answered
as a success (ADR-INV-055) → CONSUMED → INV-409-RESERVATION-CLOSED (RULE-INV-021) → ACTIVE → statusCode RELEASED,
reservedQty 0, closedAt DBF-INV-074 = now (R-5).

**Receive** (REQ-INV-051, 059, 064, 105 … 107, 120, 141). Idempotency: advisory lock, then moves with this key (R-6)
→ return them (or INV-409-IDEMPOTENCY-KEY-REUSED on a different request hash). Resolve (a bundle Variant is refused,
see "Common resolution") → per line: Item settings QR-INV-043 FOR SHARE — lot-tracked with lotExpiry on → lot number and expiry required
(RULE-INV-035), lot reused or created (QR-INV-040 / 041; different expiry → RULE-INV-036); an expiry date already before
today is accepted — no refusal — and the lot holds the stock as expired from creation, excluded from sale, FEFO and
availability (RULE-INV-038, REQ-INV-141, ADR-INV-056); dual-quantity with
dualQuantity on → measure > 0 (RULE-INV-040); cost ≥ 0 or the average, none → RULE-INV-025 → posting engine
(RECEIPT / RETURN_IN; idempotencyKey DBF-INV-052 = the caller's key on every move). All lines or none.

**Issue** (REQ-INV-047, 052, 053, 067, 069, 110 … 114, 120). Idempotency: advisory lock, then R-6. Resolve and
explode → **lock the referenced reservations**: every distinct reservationPk the request's lines name is locked
FOR UPDATE (R-4) in ascending reservationPk, in one pass after the idempotency advisory lock and before the pair's
balance rows (engine step 1), whatever the line order — two issues of one source document naming the same
reservations in opposite line order therefore take them in the same order and cannot deadlock (ADR-INV-026 as
amended at gate-analysis revise 4) → per line: reservationPk given → the reservation already locked: not ACTIVE → RULE-INV-021; its
variantFk or warehouseFk differs from the line's (the component's, for an exploded bundle line), or its
sourceDocTypeCode / sourceDocRef differs from the request's source document → INV-422-RESERVATION-MISMATCH
(RULE-INV-049) — so an unrelated reservation is never consumed; reservedQty −= issued (floor 0),
0 → CONSUMED with closedAt (R-5), and the moves carry reservationFk → lot selection: a sale (saleFl) of a
lot-tracked Variant with lotExpiry on → stock without a lot first (REQ-INV-112), then unexpired lots by ascending
expiry (REQ-INV-110; the candidate lots read FOR SHARE after the balance rows, in ascending lotPk order, so an
expiry correction cannot change them mid-selection — engine step 1; lots with expiryDate < today skipped —
RULE-INV-038, REQ-INV-113), any remainder as a move
without a lot (REQ-INV-111); a non-sale line naming a lot whose expiry is before today → INV-422-LOT-EXPIRED
(RULE-INV-039, REQ-INV-114 — the lot read FOR SHARE) → measure (RULE-INV-040) → posting engine with offlineFl
(RULE-INV-019 — Variant × warehouse and the named lot — skipped when
offlineFl is true — REQ-INV-069; the negative-crossing alert still fires; with offlineFl true the active checks are skipped
as in "Common resolution" — ADR-INV-052). One answer may carry several moves per
line. All lines or none.

**In-process repository intents (not catalogued as QR — no API-* reaches them, ADR-INV-024).**
R-1 insert INV_RESERVATION (with REQUEST_HASH — ADR-INV-048); R-2 reservations by IDEMPOTENCY_KEY (IDX_INV_RESERVATION_IDEMPOTENCY_KEY);
R-3 reservations by SOURCE_DOC_TYPE_CODE + SOURCE_DOC_REF (IDX_INV_RESERVATION_SRC_TYPE_REF); R-4 reservation by
RESERVATION_PK FOR UPDATE; R-5 update reservation status / remaining / closed time with VERSION; R-6 moves by
IDEMPOTENCY_KEY (IDX_INV_STOCK_MOVE_IDEMPOTENCY_KEY). Each is READ_WRITE inside the caller's transaction except
R-2, R-3 and R-6 (READ_ONLY), with no join; R-2 and R-6 run after the advisory lock of ADR-INV-044. They are
JPA / derived queries, tenant-filtered by `@TenantId`; an implementer who writes any of them as native SQL writes
`TENANT_ID = :tenantId` in its WHERE clause (and TENANT_ID in R-1's INSERT column list) and marks the method
`@TenantUnscoped(reason, adr = "ADR-INV-041")`.
<!-- SUB:SVC-API-INT:END -->
