<!-- source: PHASE:SVC-API / SUB:SVC-API-CRUD -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-INV-001, DBF-INV-002, DBF-INV-003, DBF-INV-004, DBF-INV-005, DBF-INV-006, DBF-INV-007, DBF-INV-009, DBF-INV-014, DBF-INV-015, DBF-INV-016, DBF-INV-017, DBF-INV-018, DBF-INV-019, DBF-INV-021, DBF-INV-027, DBF-INV-028, DBF-INV-029, DBF-INV-030, DBF-INV-031, DBF-INV-033, DBF-INV-041, DBF-INV-042, DBF-INV-044, DBF-INV-045, DBF-INV-046, DBF-INV-047, DBF-INV-048, DBF-INV-049, DBF-INV-051, DBF-INV-052, DBF-INV-053, DBF-INV-054, DBF-INV-058, DBF-INV-061, DBF-INV-077, DBF-INV-087, DBF-INV-088, DBF-INV-089, DBF-INV-090, DBF-INV-091, DBF-INV-092, DBF-INV-093, DBF-INV-094, DBF-INV-096, DBF-INV-102, DBF-INV-103, DBF-INV-104, DBF-INV-105, DBF-INV-106, DBF-INV-107, DBF-INV-110, DBF-INV-115, DBF-INV-116, DBF-INV-117, DBF-INV-118, DBF-INV-119, DBF-INV-120, DBF-INV-121, DBF-INV-122, DBF-INV-124, DBF-INV-129, DBF-INV-130, DBF-INV-131, DBF-INV-132, DBF-INV-133, DBF-INV-134, DBF-INV-135, DBF-INV-136, DBF-INV-138, DBF-INV-143, DBF-INV-144, DBF-INV-145, DBF-INV-146, DBF-INV-147, DBF-INV-148, DBF-INV-149, DBF-INV-150, DBF-INV-151, DBF-INV-153, DBF-INV-159, DBF-INV-160, DBF-INV-161, DBF-INV-162, DBF-INV-163, DBF-INV-164, DBF-INV-165, DBF-INV-166, DBF-INV-167, DBF-INV-168, DBF-INV-171, DBF-INV-176, DBF-INV-177, DBF-INV-178, DBF-INV-180, DBF-INV-181, DBF-INV-182, DBF-INV-183, DBF-INV-184, DBF-INV-186, DBF-INV-192, DBF-INV-193, DBF-INV-194, DBF-INV-195, DBF-INV-196, DBF-INV-197, DBF-INV-200, DBF-INV-206, DBF-INV-207, DBF-INV-208, DBF-INV-209, DBF-INV-210, DBF-INV-211, DBF-INV-212, DBF-INV-213, DBF-INV-215, DBF-INV-220, REQ-INV-003, REQ-INV-004, REQ-INV-005, REQ-INV-006, REQ-INV-007, REQ-INV-008, REQ-INV-010, REQ-INV-013, REQ-INV-014, REQ-INV-017, REQ-INV-022, REQ-INV-023, REQ-INV-024, REQ-INV-025, REQ-INV-026, REQ-INV-027, REQ-INV-031, REQ-INV-034, REQ-INV-036, REQ-INV-037, REQ-INV-057, REQ-INV-059, REQ-INV-060, REQ-INV-061, REQ-INV-062, REQ-INV-064, REQ-INV-065, REQ-INV-067, REQ-INV-068, REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-074, REQ-INV-077, REQ-INV-078, REQ-INV-079, REQ-INV-080, REQ-INV-081, REQ-INV-082, REQ-INV-083, REQ-INV-084, REQ-INV-085, REQ-INV-087, REQ-INV-088, REQ-INV-089, REQ-INV-090, REQ-INV-091, REQ-INV-092, REQ-INV-093, REQ-INV-094, REQ-INV-096, REQ-INV-097, REQ-INV-098, REQ-INV-099, REQ-INV-100, REQ-INV-101, REQ-INV-102, REQ-INV-103, REQ-INV-105, REQ-INV-106, REQ-INV-107, REQ-INV-108, REQ-INV-114, REQ-INV-118, REQ-INV-119, REQ-INV-122, REQ-INV-123, REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-128, REQ-INV-138, REQ-INV-139, REQ-INV-140, REQ-INV-141 -->
<!-- SUB:SVC-API-CRUD:START traces=REQ-INV-139,REQ-INV-140,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-008,REQ-INV-022,REQ-INV-036,REQ-INV-059,REQ-INV-078,REQ-INV-080,REQ-INV-087,REQ-INV-092,REQ-INV-097,REQ-INV-099,REQ-INV-108,REQ-INV-124,REQ-INV-070,REQ-INV-065,DBF-INV-005,DBF-INV-017,DBF-INV-027,DBF-INV-042,DBF-INV-092,DBF-INV-120,DBF-INV-149,DBF-INV-177,DBF-INV-206 -->
### Stock posting engine (`com.commerce.inv.engine.StockPostingEngine`)

Used by API-INV-024, 032, 038, 047 and by the in-process receive / issue (SVC-API-INT). Input: a document
context {sourceDocTypeCode, sourceDocRef, sourceDocNo, docDate, offlineFl, idempotencyKey, countGeneratedFl}
and move requests {moveTypeCode, variantPk, warehousePk, lotPk?, quantity, measureQty?, unitCost?,
bundleVariantPk?, reservationPk?, sourceLineRef?}. All in the caller's transaction — a refusal rolls back every
line. The engine carries no `@PreAuthorize`: a REST service calls it after its own check, `InventoryApi` calls
it on behalf of a caller the calling module has authorised (ADR-INV-042).

0. **Validate again on post** (ADR-INV-045) — a DRAFT was validated when it was saved, but the warehouse, the
   Variant or the Item stock settings may have changed since. For an INV document (transfer, count, adjustment,
   opening stock) the engine re-runs, after the locks of step 1, on the header and on every line:
   RULE-INV-007 (the warehouse, or both transfer warehouses, active — INV-422-WAREHOUSE-INACTIVE; a transfer
   answers RULE-INV-016's INV-422-TRANSFER-WAREHOUSES), RULE-INV-008 (a warehouse is named — always true for a
   saved document, kept for the in-process requests), RULE-INV-016 (transfer: two different active
   warehouses), RULE-INV-018 (Variant and Item active, STANDARD — integrate (XM-INV-014) at post time),
   RULE-INV-037 (a line of an Item that is lot-tracked now names a lot of its Variant, or names no lot and stays within the
   Variant's on-hand held without a lot in the line's warehouse — QR-INV-019 lot-less mode (`LOT_FK IS NULL`), read after
   the lot-less row is locked in step 1, REQ-INV-139, ADR-INV-051) and RULE-INV-040 (a line
   of an Item that is dual-quantity now carries its measure — for an adjustment line, a non-zero quantity change
   carries a measure change). The in-process receive / issue run the same checks in their resolve step; an offline issue (offlineFl true) skips
   RULE-INV-007, the two-warehouse refusal of RULE-INV-008 and RULE-INV-018 (ADR-INV-052). The
   lines are checked in stored order (transfer and count lines by identifier, adjustment lines by rowNo); the first
   failing line refuses the document with its code, and the answer's `details` names it — `{lineRef, lineRefKind:
   LINE_PK, rowNo (adjustments), code, messageAr, messageEn}` (ADR-INV-046). **Count posting** (countGeneratedFl):
   step 0 runs on the count and its lines; the COUNT_DIFFERENCE adjustment lines the posting generates are not
   re-checked by the adjustment-line part of RULE-INV-040 (each carries its count line's measure difference, and
   its count line already carried the counted measure), nor by RULE-INV-035 or RULE-INV-036 (each names the counted
   lot by lotFk — REQ-INV-092).
1. **Lock** — warehouse(s) `FOR SHARE` (QR-INV-002); the Item stock settings records of the document's Items
   `FOR SHARE` in ascending itemFk order (QR-INV-043), so a concurrent flag clear (API-INV-045, `FOR UPDATE`)
   either finishes first or waits; then the balance rows (QR-INV-017): **first** the lot-less row (variant,
   warehouse, NULL) of every Variant × warehouse pair the document touches, created when absent, in ascending
   (variant, warehouse) order, **then** the lot rows in ascending (variant, warehouse, lot) order — every
   reserve, issue, transfer leg and adjustment of a pair, with or without a lot, serialises on the pair's
   lot-less row (ADR-INV-026); then the lots themselves `FOR SHARE` in ascending lotPk order (QR-INV-039) — the
   lots the lines name and, for a sale issue, every FEFO candidate lot — so an expiry correction (API-INV-042,
   `FOR UPDATE`) cannot change an expiry the issue or the FEFO selection is using; then the valuation rows
   `FOR UPDATE` in ascending variant order (QR-INV-023).
2. **Negative stock** (RULE-INV-019) — for each Variant × warehouse the document decreases, when offlineFl is
   false and INV_ALLOW_NEGATIVE_STOCK is false: on-hand over all lots (QR-INV-019) minus the document's total
   decrease < 0 → INV-409-NEGATIVE-STOCK with {onHand}. **Lot check:** for each lot the document decreases in a
   warehouse, the lot row's on-hand (already locked by step 1) minus the document's decrease of that lot < 0 →
   INV-409-NEGATIVE-STOCK with {onHand} = that lot's on-hand, even when the warehouse total covers the decrease, so
   no lot balance goes below zero while negative stock is disallowed (RULE-INV-019). Offline moves are never
   checked (REQ-INV-069).
3. **Unit cost** — RECEIPT, RETURN_IN, ADJUSTMENT_IN with a line cost → that cost (≥ 0, at most 4 dp; more than
   4 dp → INV-422-NO-KNOWN-COST, RULE-INV-025 — never rounded silently); without one → the valuation average
   (REQ-INV-102); no valuation → INV-422-NO-KNOWN-COST (RULE-INV-025), **except** an ADJUSTMENT_IN of an
   adjustment generated by a count posting (countGeneratedFl, sourceCountFk set): it is valued at 0 and creates
   no valuation row, so a count difference can always be posted (ADR-INV-030 as extended — a count line has
   no cost field). ISSUE, RETURN_OUT,
   ADJUSTMENT_OUT and both transfer legs → the average at the time of the move (REQ-INV-061); 0 when never
   valued (ADR-INV-030). Measure-only adjustment move: quantity 0, measure = |measure change|, average cost,
   value 0 (ADR-INV-017). **Lot of an increase line** — an ADJUSTMENT_IN of an adjustment generated by a count
   posting (countGeneratedFl) uses the counted lot it carries in lotFk (DBF-INV-168), an existing lot with its stored
   expiry: it is resolved by lotFk only — its lotNo (DBF-INV-160) holds the counted lot's code (DBF-INV-181) solely so
   that UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_VAR_LOT keeps two lots of one Variant on distinct lines, and is never used to
   look a lot up; no lot is created, and RULE-INV-035 and RULE-INV-036 are not applied to it (REQ-INV-092); a lot-less
   counted line keeps lotNo NULL and posts without a lot. A decrease line of a count-generated adjustment is resolved by
   lotFk the same way. Every other increase line of a lot-tracked Item resolves or creates its lot from lotNo /
   lotExpiryDate under RULE-INV-035 and RULE-INV-036 (API-INV-038, 047). An incoming line whose lotExpiryDate is already
   before today is accepted and its new lot is expired from creation (RULE-INV-038, REQ-INV-141, ADR-INV-056).
4. **Balance** — QR-INV-018: ON_HAND ± quantity, measure ± (dual-quantity Items), last move now
   (DBF-INV-027, DBF-INV-028, DBF-INV-029); a created row carries variantFk, warehouseFk, lotFk and docDate =
   move date (DBF-INV-030, DBF-INV-031, DBF-INV-032, DBF-INV-033).
5. **Move** — balanceAfterQty = QR-INV-019 (Variant × warehouse, all lots) after step 4; moveValue = quantity ×
   unit cost (2 dp); insert QR-INV-022 writing DBF-INV-041, 042, 043, 044, 045, 046, 047, 048, 049, 050, 051,
   052, 053, 054, 055, 056, 057, 058 and currencyCode DBF-INV-061 (tenant base currency).
6. **Valuation** (REQ-INV-059 … 062) — incoming valued move: total before ≤ 0 → average = unit cost
   (REQ-INV-060), else average = (total × average + qty × cost) / (total + qty) rounded to 4 dp; total += qty.
   Outgoing: total −= qty, average unchanged. A transfer's TRANSFER_OUT and TRANSFER_IN at the same average
   leave total, average and value unchanged (REQ-INV-062). value = total × average (2 dp); last move now. A
   never-valued Variant: the first incoming valued move creates the row (QR-INV-024) with total = QR-INV-019
   over all warehouses after the move, average = unit cost, docDate = move date, currencyCode = base currency;
   an outgoing move, and a count-difference ADJUSTMENT_IN valued at 0, create nothing (ADR-INV-030). The
   arithmetic is `StockValuationDomain`'s. Writes DBF-INV-192, 193, 194, 195, 196, 197, 200.
7. **Lot activity** — registered for after commit, never run under the posting locks: for every lot the document
   moved, a short transaction of its own recomputes isActiveFl DBF-INV-184 from the committed on-hand over all
   warehouses (QR-INV-019 by lot): false at 0, true otherwise (QR-INV-041). The recompute that starts last sees
   every committed move of the lot, so two documents emptying one lot in two warehouses at once still leave it
   inactive, and no posting transaction ever waits for another warehouse's balance or lot lock.
8. **Alerts** — a Variant × warehouse on-hand crossing from ≥ 0 to < 0 queues the negative-stock alert, sent
   after commit (REQ-INV-068, SRS D-25).
9. **Event** — one `inv.StockMoveRecorded` per inserted move through OutboxPublisher (CON-INV-020).

<!-- API:API-INV-003:START traces=REQ-INV-003,REQ-INV-031,REQ-INV-034,DBF-INV-002,DBF-INV-003,DBF-INV-004,DBF-INV-005,DBF-INV-006,DBF-INV-007 -->
### API-INV-003 — create warehouse
Entity       : ENT-INV-001 · operation CREATE · screen operations: create
Endpoint     : /api/v1/inv/warehouses   verb: POST
Layers       : WarehouseController.create → WarehouseService.create
Request      : body WarehouseCreateRequest {nameAr DBF-INV-005 (required, ≤ 100, non-blank), nameEn DBF-INV-006 (optional, ≤ 100), branchPk DBF-INV-003 (optional — the default branch when absent)} · excluded warehousePk, code, isDefaultFl, isActiveFl, version, audit
Response     : 201 · ApiResponse<WarehouseDto> · not paginated
Validations  : RULE-INV-002 → INV-409-WAREHOUSE-NAME-TAKEN · RULE-INV-009 → INV-422-BRANCH-INACTIVE (statements §1.3)
Errors       : INV-409-WAREHOUSE-NAME-TAKEN, INV-422-BRANCH-INACTIVE, INV-404-BRANCH-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: load branch — branchPk given → QR-INV-009 FOR SHARE; absent → default branch QR-INV-012 (REQ-INV-031) → validate RULE-INV-009, RULE-INV-002 (QR-INV-003) → code DBF-INV-004 from NumberSeriesApi (INV_WAREHOUSE) → isDefaultFl DBF-INV-002 = false, isActiveFl DBF-INV-007 = true → persist QR-INV-004 (INV_WAREHOUSE, identity) → AuditApi → OutboxPublisher InventoryMasterChanged CREATED (REQ-INV-034)
Repository   : QR-INV-009, QR-INV-012, QR-INV-003, QR-INV-004 · join NONE · READ_WRITE
Concurrency  : code allocated atomically by NumberSeriesApi; two simultaneous saves of one name — the second hits UQ_INV_WAREHOUSE_NAME_AR → INV-409-WAREHOUSE-NAME-TAKEN; the branch is read FOR SHARE so a concurrent branch deactivation waits and then sees the active warehouse (RULE-INV-013)
Security     : SCR-REQ-INV-001 · PERM_INV_WAREHOUSES_CREATE
Localization : nameAr required, nameEn optional; every message ar + en
<!-- API:API-INV-003:END -->

<!-- API:API-INV-004:START traces=REQ-INV-004,REQ-INV-034,DBF-INV-001,DBF-INV-003,DBF-INV-004,DBF-INV-005,DBF-INV-006,DBF-INV-009 -->
### API-INV-004 — update warehouse
Entity       : ENT-INV-001 · operation UPDATE · screen operations: update
Endpoint     : /api/v1/inv/warehouses/{id}   verb: PUT
Layers       : WarehouseController.update → WarehouseService.update
Request      : path id (DBF-INV-001) · body WarehouseUpdateRequest {nameAr DBF-INV-005 (required), nameEn DBF-INV-006, branchPk DBF-INV-003 (required), version DBF-INV-009 (required), code DBF-INV-004 (optional echo — must equal the stored code)} · excluded isDefaultFl, isActiveFl, audit
Response     : 200 · ApiResponse<WarehouseDto>
Validations  : RULE-INV-001 → INV-422-WAREHOUSE-CODE-READONLY · RULE-INV-002 → INV-409-WAREHOUSE-NAME-TAKEN · RULE-INV-009 → INV-422-BRANCH-INACTIVE
Errors       : INV-404-WAREHOUSE-NOT-FOUND, INV-422-WAREHOUSE-CODE-READONLY, INV-409-WAREHOUSE-NAME-TAKEN, INV-404-BRANCH-NOT-FOUND, INV-422-BRANCH-INACTIVE, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-002 FOR UPDATE → version check → branch QR-INV-009 FOR SHARE → validate (QR-INV-003) → set nameAr, nameEn, branchFk → persist QR-INV-004 → AuditApi → InventoryMasterChanged UPDATED
Repository   : QR-INV-002, QR-INV-009, QR-INV-003, QR-INV-004 · join NONE · READ_WRITE
Concurrency  : optimistic VERSION — the slower of two editors gets INV-409-STALE-VERSION; name race closed by UQ_INV_WAREHOUSE_NAME_AR
Security     : SCR-REQ-INV-001 · PERM_INV_WAREHOUSES_UPDATE
Localization : ar + en
<!-- API:API-INV-004:END -->

<!-- API:API-INV-005:START traces=REQ-INV-005,REQ-INV-006,REQ-INV-034,DBF-INV-001,DBF-INV-002,DBF-INV-007,DBF-INV-031,DBF-INV-077 -->
### API-INV-005 — deactivate warehouse
Entity       : ENT-INV-001 · operation DELETE · screen operations: deactivate
Endpoint     : /api/v1/inv/warehouses/{id}   verb: DELETE
Layers       : WarehouseController.deactivate → WarehouseService.deactivate
Request      : path id (DBF-INV-001) · no body
Response     : 200 · ApiResponse<WarehouseDto> (the deactivated record — soft delete, `soft` semantics)
Validations  : RULE-INV-003 → INV-409-WAREHOUSE-DEFAULT · RULE-INV-004 → INV-409-WAREHOUSE-IN-USE · RULE-INV-005 effect (readable, excluded, notified)
Errors       : INV-404-WAREHOUSE-NOT-FOUND, INV-409-WAREHOUSE-DEFAULT, INV-409-WAREHOUSE-IN-USE, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-002 FOR UPDATE → already inactive → return it unchanged (no event) → RULE-INV-003 on isDefaultFl DBF-INV-002 → RULE-INV-004 (QR-INV-007) → isActiveFl DBF-INV-007 = false → persist QR-INV-004 → AuditApi → InventoryMasterChanged DEACTIVATED (exactly one, after commit — AC-INV-039)
Repository   : QR-INV-002, QR-INV-007, QR-INV-004 · join NONE · READ_WRITE
Concurrency  : the warehouse row is locked FOR UPDATE before the usage probe; every posting path and every DRAFT save reads it FOR SHARE, so no move and no new DRAFT can land between the probe and the flag change (ADR-INV-045)
Security     : SCR-REQ-INV-001 · PERM_INV_WAREHOUSES_DELETE
Localization : ar + en
<!-- API:API-INV-005:END -->

<!-- API:API-INV-006:START traces=REQ-INV-007,REQ-INV-034,DBF-INV-001,DBF-INV-007,DBF-INV-003,DBF-INV-009 -->
### API-INV-006 — reactivate warehouse
Entity       : ENT-INV-001 · operation UPDATE · screen operations: reactivate
Endpoint     : /api/v1/inv/warehouses/{id}/reactivate   verb: PATCH
Layers       : WarehouseController.reactivate → WarehouseService.reactivate
Request      : path id (DBF-INV-001) · body VersionedActionRequest {version DBF-INV-009 (required)} (ADR-INV-032)
Response     : 200 · ApiResponse<WarehouseDto>
Validations  : RULE-INV-009 → INV-422-BRANCH-INACTIVE (its branch must be active)
Errors       : INV-404-WAREHOUSE-NOT-FOUND, INV-422-BRANCH-INACTIVE, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-002 FOR UPDATE → version check (DBF-INV-009) → already active → return unchanged → branch DBF-INV-003 via QR-INV-009 FOR SHARE active → isActiveFl DBF-INV-007 = true → persist QR-INV-004 → AuditApi → InventoryMasterChanged REACTIVATED
Repository   : QR-INV-002, QR-INV-009, QR-INV-004 · join NONE · READ_WRITE
Concurrency  : row lock FOR UPDATE; idempotent
Security     : SCR-REQ-INV-001 · PERM_INV_WAREHOUSES_UPDATE
Localization : ar + en
<!-- API:API-INV-006:END -->

<!-- API:API-INV-007:START traces=REQ-INV-008,REQ-INV-034,DBF-INV-001,DBF-INV-002,DBF-INV-007,DBF-INV-009 -->
### API-INV-007 — set as default warehouse
Entity       : ENT-INV-001 · operation UPDATE · screen operations: set as default
Endpoint     : /api/v1/inv/warehouses/{id}/default   verb: PATCH
Layers       : WarehouseController.setDefault → WarehouseService.setDefault
Request      : path id (DBF-INV-001) · body VersionedActionRequest {version DBF-INV-009 (required) — the target's} (ADR-INV-032)
Response     : 200 · ApiResponse<WarehouseDto>
Validations  : RULE-INV-006 → INV-409-WAREHOUSE-DEFAULT-INACTIVE
Errors       : INV-404-WAREHOUSE-NOT-FOUND, INV-409-WAREHOUSE-DEFAULT-INACTIVE, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: take the tenant's default-switch advisory lock `pg_advisory_xact_lock(hashtextextended(:tenantId || ':INV_WAREHOUSE_DEFAULT', 0))` (ADR-INV-050) → lock the current default QR-INV-005 FOR UPDATE, then the target QR-INV-002 FOR UPDATE → target version check (DBF-INV-009) → target already default → return → target isActiveFl DBF-INV-007 true else RULE-INV-006 → clear isDefaultFl DBF-INV-002 on the previous default, then set it on the target, one transaction (QR-INV-004 twice, clear first so UQ_INV_WAREHOUSE_IS_DEFAULT_FL holds) → AuditApi → InventoryMasterChanged DEFAULT_CHANGED for the target
Repository   : QR-INV-005, QR-INV-002, QR-INV-004 · join NONE · READ_WRITE
Concurrency  : every switch takes the advisory lock on the stable key `<tenantId>:INV_WAREHOUSE_DEFAULT` before it reads the default — two switches serialise, and the second reads the default the first committed (locking only the old default row would hand the second no row once the first cleared it — ADR-INV-050); UQ_INV_WAREHOUSE_IS_DEFAULT_FL makes a second default impossible
Security     : SCR-REQ-INV-001 · PERM_INV_WAREHOUSES_UPDATE
Localization : ar + en
<!-- API:API-INV-007:END -->

<!-- API:API-INV-011:START traces=REQ-INV-022,REQ-INV-034,DBF-INV-015,DBF-INV-016,DBF-INV-017,DBF-INV-018,DBF-INV-019 -->
### API-INV-011 — create branch
Entity       : ENT-INV-002 · operation CREATE · screen operations: create
Endpoint     : /api/v1/inv/branches   verb: POST
Layers       : BranchController.create → BranchService.create
Request      : body BranchCreateRequest {nameAr DBF-INV-017 (required, ≤ 100, non-blank), nameEn DBF-INV-018 (optional)} · excluded branchPk, code, isDefaultFl, isActiveFl, version, audit
Response     : 201 · ApiResponse<BranchDto>
Validations  : RULE-INV-011 → INV-409-BRANCH-NAME-TAKEN · RULE-INV-010 (code system-generated)
Errors       : INV-409-BRANCH-NAME-TAKEN, INV-400, INV-401, INV-403, INV-500
Orchestration: validate RULE-INV-011 (QR-INV-010) → code DBF-INV-016 from NumberSeriesApi (INV_BRANCH) → isDefaultFl DBF-INV-015 = false, isActiveFl DBF-INV-019 = true → persist QR-INV-011 → AuditApi → InventoryMasterChanged CREATED
Repository   : QR-INV-010, QR-INV-011 · join NONE · READ_WRITE
Concurrency  : atomic code allocation; name race closed by UQ_INV_BRANCH_NAME_AR
Security     : SCR-REQ-INV-002 · PERM_INV_BRANCHES_CREATE
Localization : ar + en
<!-- API:API-INV-011:END -->

<!-- API:API-INV-012:START traces=REQ-INV-023,REQ-INV-034,DBF-INV-014,DBF-INV-016,DBF-INV-017,DBF-INV-018,DBF-INV-021 -->
### API-INV-012 — update branch
Entity       : ENT-INV-002 · operation UPDATE · screen operations: update
Endpoint     : /api/v1/inv/branches/{id}   verb: PUT
Layers       : BranchController.update → BranchService.update
Request      : path id (DBF-INV-014) · body BranchUpdateRequest {nameAr DBF-INV-017 (required), nameEn DBF-INV-018, version DBF-INV-021 (required), code DBF-INV-016 (optional echo)}
Response     : 200 · ApiResponse<BranchDto>
Validations  : RULE-INV-010 → INV-422-BRANCH-CODE-READONLY · RULE-INV-011 → INV-409-BRANCH-NAME-TAKEN
Errors       : INV-404-BRANCH-NOT-FOUND, INV-422-BRANCH-CODE-READONLY, INV-409-BRANCH-NAME-TAKEN, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-009 FOR UPDATE → version → validate (QR-INV-010) → set nameAr, nameEn → persist QR-INV-011 → AuditApi → InventoryMasterChanged UPDATED
Repository   : QR-INV-009, QR-INV-010, QR-INV-011 · join NONE · READ_WRITE
Concurrency  : optimistic VERSION; UQ_INV_BRANCH_NAME_AR
Security     : SCR-REQ-INV-002 · PERM_INV_BRANCHES_UPDATE
Localization : ar + en
<!-- API:API-INV-012:END -->

<!-- API:API-INV-013:START traces=REQ-INV-024,REQ-INV-025,REQ-INV-034,DBF-INV-014,DBF-INV-015,DBF-INV-019,DBF-INV-003 -->
### API-INV-013 — deactivate branch
Entity       : ENT-INV-002 · operation DELETE · screen operations: deactivate
Endpoint     : /api/v1/inv/branches/{id}   verb: DELETE
Layers       : BranchController.deactivate → BranchService.deactivate
Request      : path id (DBF-INV-014) · no body
Response     : 200 · ApiResponse<BranchDto> (soft delete)
Validations  : RULE-INV-012 → INV-409-BRANCH-DEFAULT · RULE-INV-013 → INV-409-BRANCH-IN-USE · RULE-INV-014 effect
Errors       : INV-404-BRANCH-NOT-FOUND, INV-409-BRANCH-DEFAULT, INV-409-BRANCH-IN-USE, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-009 FOR UPDATE → already inactive → return → RULE-INV-012 on DBF-INV-015 → RULE-INV-013 (QR-INV-014) → isActiveFl DBF-INV-019 = false → persist QR-INV-011 → AuditApi → InventoryMasterChanged DEACTIVATED
Repository   : QR-INV-009, QR-INV-014, QR-INV-011 · join NONE · READ_WRITE
Concurrency  : branch row locked FOR UPDATE before the probe; warehouse saves read it FOR SHARE
Security     : SCR-REQ-INV-002 · PERM_INV_BRANCHES_DELETE
Localization : ar + en
<!-- API:API-INV-013:END -->

<!-- API:API-INV-014:START traces=REQ-INV-026,REQ-INV-034,DBF-INV-014,DBF-INV-019,DBF-INV-021 -->
### API-INV-014 — reactivate branch
Entity       : ENT-INV-002 · operation UPDATE · screen operations: reactivate
Endpoint     : /api/v1/inv/branches/{id}/reactivate   verb: PATCH
Layers       : BranchController.reactivate → BranchService.reactivate
Request      : path id (DBF-INV-014) · body VersionedActionRequest {version DBF-INV-021 (required)} (ADR-INV-032)
Response     : 200 · ApiResponse<BranchDto>
Validations  : none
Errors       : INV-404-BRANCH-NOT-FOUND, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-009 FOR UPDATE → version check (DBF-INV-021) → isActiveFl DBF-INV-019 = true → persist QR-INV-011 → AuditApi → InventoryMasterChanged REACTIVATED (none when already active)
Repository   : QR-INV-009, QR-INV-011 · join NONE · READ_WRITE
Concurrency  : row lock; idempotent
Security     : SCR-REQ-INV-002 · PERM_INV_BRANCHES_UPDATE
Localization : ar + en
<!-- API:API-INV-014:END -->

<!-- API:API-INV-015:START traces=REQ-INV-027,REQ-INV-034,DBF-INV-014,DBF-INV-015,DBF-INV-019,DBF-INV-021 -->
### API-INV-015 — set as default branch
Entity       : ENT-INV-002 · operation UPDATE · screen operations: set as default
Endpoint     : /api/v1/inv/branches/{id}/default   verb: PATCH
Layers       : BranchController.setDefault → BranchService.setDefault
Request      : path id (DBF-INV-014) · body VersionedActionRequest {version DBF-INV-021 (required) — the target's} (ADR-INV-032)
Response     : 200 · ApiResponse<BranchDto>
Validations  : RULE-INV-015 → INV-409-BRANCH-DEFAULT-INACTIVE
Errors       : INV-404-BRANCH-NOT-FOUND, INV-409-BRANCH-DEFAULT-INACTIVE, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: take the tenant's default-switch advisory lock `pg_advisory_xact_lock(hashtextextended(:tenantId || ':INV_BRANCH_DEFAULT', 0))` (ADR-INV-050) → lock current default QR-INV-012 FOR UPDATE, then target QR-INV-009 FOR UPDATE → target version check (DBF-INV-021) → target active (DBF-INV-019) else RULE-INV-015 → clear isDefaultFl DBF-INV-015 on the previous default, set on the target (QR-INV-011 twice) → AuditApi → InventoryMasterChanged DEFAULT_CHANGED
Repository   : QR-INV-012, QR-INV-009, QR-INV-011 · join NONE · READ_WRITE
Concurrency  : the advisory lock on `<tenantId>:INV_BRANCH_DEFAULT` serialises two switches before either reads the default, so the second reads the default the first committed (ADR-INV-050); UQ_INV_BRANCH_IS_DEFAULT_FL
Security     : SCR-REQ-INV-002 · PERM_INV_BRANCHES_UPDATE
Localization : ar + en
<!-- API:API-INV-015:END -->

<!-- API:API-INV-022:START traces=REQ-INV-139,REQ-INV-078,REQ-INV-014,REQ-INV-082,REQ-INV-084,REQ-INV-122,DBF-INV-088,DBF-INV-090,DBF-INV-091,DBF-INV-092,DBF-INV-093,DBF-INV-094,DBF-INV-102,DBF-INV-103,DBF-INV-105,DBF-INV-106,DBF-INV-107,DBF-INV-110 -->
### API-INV-022 — create transfer
Entity       : ENT-INV-006, ENT-INV-007 · operation CREATE · screen operations: create
Endpoint     : /api/v1/inv/transfers   verb: POST
Layers       : TransferController.create → TransferService.create
Request      : body TransferCreateRequest {docDate DBF-INV-093 (optional — today), fromWarehousePk DBF-INV-090 (required), toWarehousePk DBF-INV-091 (required), noteText DBF-INV-088 (≤ 500), lines[] {variantPk DBF-INV-106 (required), lotPk DBF-INV-107, quantity DBF-INV-102 (required), measureQty DBF-INV-103}} · excluded transferPk, docNo, statusCode, postedAt, unitCost, currencyCode, audit
Response     : 201 · ApiResponse<TransferDto> (with lines)
Validations  : RULE-INV-016 → INV-422-TRANSFER-WAREHOUSES (also fewer than two active warehouses, REQ-INV-014) · RULE-INV-017 → INV-422-QUANTITY-INVALID (quantity > 0 and ≤ 3 dp; measure ≤ 3 dp) · RULE-INV-018 → INV-422-VARIANT-INACTIVE · RULE-INV-037 → INV-422-LOT-REQUIRED (a line without a lot is accepted up to the Variant's lot-less on-hand in the source warehouse — REQ-INV-139) · RULE-INV-040 → INV-422-MEASURE-REQUIRED · RULE-INV-048 → INV-422-DUPLICATE-LINE · RULE-INV-051 → INV-422-DOC-DATE-FUTURE (docDate after today) · RULE-INV-030 (docNo assigned) — line rules held by `TransferDomain` / `TransferLineDomain`; a line refusal names its line in `details` (`REQUEST_POSITION` — ADR-INV-046)
Errors       : INV-404-WAREHOUSE-NOT-FOUND, INV-422-TRANSFER-WAREHOUSES, INV-422-QUANTITY-INVALID, INV-404-VARIANT-NOT-FOUND, INV-422-VARIANT-INACTIVE, INV-404-LOT-NOT-FOUND, INV-422-LOT-REQUIRED, INV-422-MEASURE-REQUIRED, INV-422-DUPLICATE-LINE, INV-422-DOC-DATE-FUTURE, INV-400, INV-401, INV-403, INV-500
Orchestration: load warehouses QR-INV-002 ×2 FOR SHARE (a concurrent deactivation waits — ADR-INV-045) and the active count QR-INV-006 → RULE-INV-016 → per line: Variant integrate (XM-INV-014) — inactive / BUNDLE → RULE-INV-018; Item settings QR-INV-043 — lot-tracked with lotExpiry on → a lot of the Variant (QR-INV-039), or no lot within the lot-less on-hand of the source warehouse (QR-INV-019 lot-less mode, `LOT_FK IS NULL` — REQ-INV-139); dual-quantity with dualQuantity on → measure > 0 → duplicates in the request (RULE-INV-048) → docNo DBF-INV-092 from NumberSeriesApi (INV_TRANSFER), statusCode DBF-INV-094 = DRAFT, transferFk DBF-INV-105 on each line, currencyCode DBF-INV-110 = base currency → persist QR-INV-029 → AuditApi; no stock move (AC-INV-085)
Repository   : QR-INV-002, QR-INV-006, QR-INV-019, QR-INV-039, QR-INV-043, QR-INV-029 · join NONE · READ_WRITE
Concurrency  : docNo allocated atomically; duplicate lines also stopped by UQ_INV_TRANSFER_LINE_TRF_VAR_LOT
Security     : SCR-REQ-INV-005 · PERM_INV_TRANSFERS_CREATE
Localization : ar + en
<!-- API:API-INV-022:END -->

<!-- API:API-INV-023:START traces=REQ-INV-139,REQ-INV-079,REQ-INV-085,REQ-INV-082,REQ-INV-084,REQ-INV-122,DBF-INV-087,DBF-INV-088,DBF-INV-090,DBF-INV-091,DBF-INV-093,DBF-INV-096,DBF-INV-102,DBF-INV-103,DBF-INV-105,DBF-INV-106,DBF-INV-107,DBF-INV-110 -->
### API-INV-023 — update transfer
Entity       : ENT-INV-006, ENT-INV-007 · operation UPDATE · screen operations: update
Endpoint     : /api/v1/inv/transfers/{id}   verb: PUT
Layers       : TransferController.update → TransferService.update
Request      : path id (DBF-INV-087) · body TransferUpdateRequest {docDate DBF-INV-093, fromWarehousePk DBF-INV-090, toWarehousePk DBF-INV-091, noteText DBF-INV-088, version DBF-INV-096 (required), docNo (optional echo), lines[] {transferLinePk (kept lines), variantPk DBF-INV-106, lotPk DBF-INV-107, quantity DBF-INV-102, measureQty DBF-INV-103}} — the saved lines replace the stored ones (REQ-INV-079)
Response     : 200 · ApiResponse<TransferDto>
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED · RULE-INV-030 → INV-422-DOC-NO-READONLY · RULE-INV-016, 017, 018, 037, 040, 048, 051 as API-INV-022; a line refusal names its line in `details` — `LINE_PK` for a kept line, `REQUEST_POSITION` for a new one (ADR-INV-046)
Errors       : INV-404-TRANSFER-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-422-DOC-NO-READONLY, INV-404-WAREHOUSE-NOT-FOUND, INV-422-TRANSFER-WAREHOUSES, INV-422-QUANTITY-INVALID, INV-404-VARIANT-NOT-FOUND, INV-422-VARIANT-INACTIVE, INV-404-LOT-NOT-FOUND, INV-422-LOT-REQUIRED, INV-422-MEASURE-REQUIRED, INV-422-DUPLICATE-LINE, INV-422-DOC-DATE-FUTURE, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-028 FOR UPDATE → statusCode DRAFT else RULE-INV-029 → version → validations as API-INV-022 (QR-INV-002 FOR SHARE, QR-INV-006, integrate (XM-INV-014), QR-INV-043, QR-INV-039, QR-INV-019 lot-less mode `LOT_FK IS NULL` on the source warehouse for a line without a lot) → replace header fields and lines (transferFk DBF-INV-105, currencyCode DBF-INV-110 on new lines) → persist QR-INV-029 → AuditApi
Repository   : QR-INV-028, QR-INV-002, QR-INV-006, QR-INV-019, QR-INV-039, QR-INV-043, QR-INV-029 · join NONE · READ_WRITE
Concurrency  : header locked FOR UPDATE and VERSION checked — a concurrent post makes this update fail with INV-409-DOCUMENT-CLOSED
Security     : SCR-REQ-INV-005 · PERM_INV_TRANSFERS_UPDATE
Localization : ar + en
<!-- API:API-INV-023:END -->

<!-- API:API-INV-024:START traces=REQ-INV-139,REQ-INV-080,REQ-INV-083,REQ-INV-084,REQ-INV-062,REQ-INV-014,REQ-INV-068,REQ-INV-036,REQ-INV-037,REQ-INV-057,REQ-INV-061,DBF-INV-089,DBF-INV-094,DBF-INV-096,DBF-INV-104,DBF-INV-027,DBF-INV-029,DBF-INV-030,DBF-INV-031,DBF-INV-033,DBF-INV-041,DBF-INV-042,DBF-INV-044,DBF-INV-045,DBF-INV-046,DBF-INV-047,DBF-INV-048,DBF-INV-049,DBF-INV-051,DBF-INV-052,DBF-INV-220,DBF-INV-053,DBF-INV-054,DBF-INV-058,DBF-INV-061,DBF-INV-192,DBF-INV-194,DBF-INV-195 -->
### API-INV-024 — post transfer
Entity       : ENT-INV-006, ENT-INV-007 · operation UPDATE · screen operations: post
Endpoint     : /api/v1/inv/transfers/{id}/post   verb: PATCH
Layers       : TransferController.post → TransferService.post → StockPostingEngine.post
Request      : path id (DBF-INV-087) · body VersionedActionRequest {version DBF-INV-096 (required)} (ADR-INV-032)
Response     : 200 · ApiResponse<TransferDto> (statusCode POSTED, line unit costs)
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED · RULE-INV-031 → INV-422-NO-LINES · re-checked on post by engine step 0 (ADR-INV-045): RULE-INV-016 → INV-422-TRANSFER-WAREHOUSES (either warehouse deactivated since the save), RULE-INV-018 → INV-422-VARIANT-INACTIVE, RULE-INV-037 → INV-422-LOT-REQUIRED (also a line without a lot above the lot-less on-hand of the source warehouse at posting — REQ-INV-139), RULE-INV-040 → INV-422-MEASURE-REQUIRED · RULE-INV-019 → INV-409-NEGATIVE-STOCK (Variant × warehouse, and the lot the line names) — a line refusal names its line in `details` (`LINE_PK` = transferLinePk, ADR-INV-046)
Errors       : INV-404-TRANSFER-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-422-NO-LINES, INV-422-TRANSFER-WAREHOUSES, INV-422-VARIANT-INACTIVE, INV-422-LOT-REQUIRED, INV-422-MEASURE-REQUIRED, INV-409-NEGATIVE-STOCK, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-028 FOR UPDATE → DRAFT → version check (DBF-INV-096) → ≥ 1 line → warehouses QR-INV-002 FOR SHARE, active count QR-INV-006 → posting engine (steps 0 … 9), per line: TRANSFER_OUT from the source and TRANSFER_IN to the destination, same lot (the lot keeps its expiry — REQ-INV-084), both at the average (REQ-INV-061); moves write moveTypeCode DBF-INV-041, quantity DBF-INV-042, unitCost DBF-INV-044, moveValue DBF-INV-045, balanceAfterQty DBF-INV-046, sourceDocTypeCode DBF-INV-047 = INV_TRANSFER, sourceDocRef DBF-INV-048 = transferPk, sourceDocNo DBF-INV-049 = docNo, offlineFl DBF-INV-051 = false, idempotencyKey DBF-INV-052 = INV_TRANSFER:{transferPk}, requestHash DBF-INV-220 = SHA-256 of that key (ADR-INV-048), variantFk DBF-INV-053, warehouseFk DBF-INV-054, docDate DBF-INV-058 = transfer docDate, currencyCode DBF-INV-061; balances DBF-INV-027, DBF-INV-029, DBF-INV-030, DBF-INV-031, DBF-INV-033; valuation DBF-INV-192, DBF-INV-194, DBF-INV-195 (total and value unchanged — REQ-INV-062) → line unitCost DBF-INV-104 = average → statusCode DBF-INV-094 = POSTED, postedAt DBF-INV-089 → persist QR-INV-029 → AuditApi → StockMoveRecorded per move; negative-crossing alert after commit
Repository   : QR-INV-028, QR-INV-002, QR-INV-006, QR-INV-043, QR-INV-017, QR-INV-018, QR-INV-019, QR-INV-022, QR-INV-023, QR-INV-024, QR-INV-029 · join NONE · READ_WRITE
Concurrency  : header FOR UPDATE — a second post, or a cancel racing the post, waits, then sees POSTED / CANCELLED (INV-409-DOCUMENT-CLOSED); lot-less rows then lot rows locked in key order before the RULE-INV-019 read, so two posts draining one balance cannot both pass the check; Item stock settings read FOR SHARE (engine step 1)
Security     : SCR-REQ-INV-005 · PERM_INV_TRANSFERS_UPDATE
Localization : ar + en
<!-- API:API-INV-024:END -->

<!-- API:API-INV-025:START traces=REQ-INV-081,REQ-INV-085,DBF-INV-087,DBF-INV-094 -->
### API-INV-025 — cancel transfer
Entity       : ENT-INV-006 · operation DELETE · screen operations: cancel
Endpoint     : /api/v1/inv/transfers/{id}   verb: DELETE
Layers       : TransferController.cancel → TransferService.cancel
Request      : path id (DBF-INV-087) · no body
Response     : 200 · ApiResponse<TransferDto> (statusCode CANCELLED)
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Errors       : INV-404-TRANSFER-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-028 FOR UPDATE → DRAFT → statusCode DBF-INV-094 = CANCELLED → persist QR-INV-029 → AuditApi; no stock move (AC-INV-089)
Repository   : QR-INV-028, QR-INV-029 · join NONE · READ_WRITE
Concurrency  : header FOR UPDATE — a cancel racing a post: whichever locks second sees the final status and is refused
Security     : SCR-REQ-INV-005 · PERM_INV_TRANSFERS_DELETE
Localization : ar + en
<!-- API:API-INV-025:END -->

<!-- API:API-INV-028:START traces=REQ-INV-087,REQ-INV-013,REQ-INV-017,REQ-INV-090,DBF-INV-116,DBF-INV-118,DBF-INV-120,DBF-INV-121,DBF-INV-122 -->
### API-INV-028 — create stock count
Entity       : ENT-INV-008 · operation CREATE · screen operations: create
Endpoint     : /api/v1/inv/stock-counts   verb: POST
Layers       : StockCountController.create → StockCountService.create
Request      : body StockCountCreateRequest {warehousePk DBF-INV-118 (optional while one warehouse is active), docDate DBF-INV-121 (optional — today), noteText DBF-INV-116}
Response     : 201 · ApiResponse<StockCountDto>
Validations  : RULE-INV-007 → INV-422-WAREHOUSE-INACTIVE · RULE-INV-008 → INV-422-WAREHOUSE-REQUIRED · RULE-INV-051 → INV-422-DOC-DATE-FUTURE · RULE-INV-030 (docNo assigned)
Errors       : INV-404-WAREHOUSE-NOT-FOUND, INV-422-WAREHOUSE-INACTIVE, INV-422-WAREHOUSE-REQUIRED, INV-422-DOC-DATE-FUTURE, INV-400, INV-401, INV-403, INV-500
Orchestration: warehouse — given → QR-INV-002 FOR SHARE (active); absent → QR-INV-006 = 1 → default QR-INV-005 (REQ-INV-013), ≥ 2 → RULE-INV-008 → docNo DBF-INV-120 from NumberSeriesApi (INV_COUNT), statusCode DBF-INV-122 = DRAFT, docDate DBF-INV-121 → persist QR-INV-032 → AuditApi
Repository   : QR-INV-002, QR-INV-005, QR-INV-006, QR-INV-032 · join NONE · READ_WRITE
Concurrency  : docNo allocated atomically
Security     : SCR-REQ-INV-006 · PERM_INV_COUNTS_CREATE
Localization : ar + en (mobile viewport 360 px — REQ-INV-090 is a frontend obligation; the payloads stay small)
<!-- API:API-INV-028:END -->

<!-- API:API-INV-029:START traces=REQ-INV-139,REQ-INV-088,REQ-INV-091,REQ-INV-096,REQ-INV-090,REQ-INV-122,DBF-INV-130,DBF-INV-131,DBF-INV-134,DBF-INV-135,DBF-INV-136 -->
### API-INV-029 — update (lines) — add counted quantity
Entity       : ENT-INV-008, ENT-INV-009, ENT-INV-010 · operation UPDATE · screen operations: update (lines)
Endpoint     : /api/v1/inv/stock-counts/{id}/lines   verb: POST
Layers       : StockCountController.addLine → StockCountService.addCountedQuantity
Request      : path id · body StockCountLineAddRequest {barcode (text) or variantPk DBF-INV-135 (one required), lotPk DBF-INV-136, countedQty DBF-INV-130 (required, ≥ 0), countedMeasureQty DBF-INV-131}
Response     : 200 · ApiResponse<StockCountLineDto> (the created line, or the existing line with its quantity increased)
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED · RULE-INV-032 → INV-422-BARCODE-UNKNOWN · RULE-INV-018 → INV-422-VARIANT-INACTIVE · RULE-INV-037 → INV-422-LOT-REQUIRED (a line without a lot is accepted while its counted quantity is not above the Variant's lot-less on-hand in the count's warehouse — REQ-INV-139) · RULE-INV-040 → INV-422-MEASURE-REQUIRED · RULE-INV-047 → INV-422-COUNTED-NEGATIVE — a line refusal carries `details` with `REQUEST_POSITION` 1 (ADR-INV-046)
Errors       : INV-404-STOCK-COUNT-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-422-BARCODE-UNKNOWN, INV-404-VARIANT-NOT-FOUND, INV-422-VARIANT-INACTIVE, INV-404-LOT-NOT-FOUND, INV-422-LOT-REQUIRED, INV-422-MEASURE-REQUIRED, INV-422-COUNTED-NEGATIVE, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-031 FOR UPDATE → DRAFT → barcode → integrate (XM-INV-013) (unknown → RULE-INV-032) → Variant integrate (XM-INV-014) → Item settings QR-INV-043 (lot / measure) → lot of the Variant QR-INV-039, or, for a lot-tracked line without a lot, the counted total (existing line plus this scan) not above the lot-less on-hand of the count's warehouse (QR-INV-019 lot-less mode, `LOT_FK IS NULL` — REQ-INV-139) → RULE-INV-047 → existing line QR-INV-033 → add countedQty DBF-INV-130 and countedMeasureQty DBF-INV-131 to it, or insert with stockCountFk DBF-INV-134, variantFk DBF-INV-135, lotFk DBF-INV-136 → persist QR-INV-034 → AuditApi
Repository   : QR-INV-031, QR-INV-033, QR-INV-034, QR-INV-019, QR-INV-039, QR-INV-043 · join NONE · READ_WRITE
Concurrency  : header locked FOR UPDATE — two scans of the same Variant serialise, the second adds to the first's line; UQ_INV_STOCK_COUNT_LINE_CNT_VAR_LOT backs it
Security     : SCR-REQ-INV-006 · PERM_INV_COUNTS_UPDATE
Localization : ar + en
<!-- API:API-INV-029:END -->

<!-- API:API-INV-030:START traces=REQ-INV-089,REQ-INV-096,DBF-INV-129,DBF-INV-130,DBF-INV-131,DBF-INV-138 -->
### API-INV-030 — update (lines) — change counted line
Entity       : ENT-INV-008, ENT-INV-009 · operation UPDATE · screen operations: update (lines)
Endpoint     : /api/v1/inv/stock-counts/{id}/lines/{lineId}   verb: PUT
Layers       : StockCountController.changeLine → StockCountService.changeLine
Request      : path id, lineId (DBF-INV-129) · body StockCountLineUpdateRequest {countedQty DBF-INV-130 (required), countedMeasureQty DBF-INV-131, version DBF-INV-138 (required)}
Response     : 200 · ApiResponse<StockCountLineDto>
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED · RULE-INV-047 → INV-422-COUNTED-NEGATIVE · RULE-INV-040 → INV-422-MEASURE-REQUIRED — a line refusal carries `details` with `LINE_PK` = lineId (ADR-INV-046)
Errors       : INV-404-STOCK-COUNT-NOT-FOUND, INV-404-STOCK-COUNT-LINE-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-422-COUNTED-NEGATIVE, INV-422-MEASURE-REQUIRED, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-031 FOR UPDATE → DRAFT → line of this count → version → Item settings QR-INV-043 → set countedQty DBF-INV-130, countedMeasureQty DBF-INV-131 → persist QR-INV-034 → AuditApi
Repository   : QR-INV-031, QR-INV-034, QR-INV-043 · join NONE · READ_WRITE
Concurrency  : header lock + line VERSION
Security     : SCR-REQ-INV-006 · PERM_INV_COUNTS_UPDATE
Localization : ar + en
<!-- API:API-INV-030:END -->

<!-- API:API-INV-031:START traces=REQ-INV-089,REQ-INV-096,DBF-INV-129,DBF-INV-134 -->
### API-INV-031 — update (lines) — remove counted line
Entity       : ENT-INV-008, ENT-INV-009 · operation UPDATE · screen operations: update (lines)
Endpoint     : /api/v1/inv/stock-counts/{id}/lines/{lineId}   verb: DELETE
Layers       : StockCountController.removeLine → StockCountService.removeLine
Request      : path id, lineId (DBF-INV-129) · no body
Response     : 200 · ApiResponse<StockCountDto> (the count without the line)
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Errors       : INV-404-STOCK-COUNT-NOT-FOUND, INV-404-STOCK-COUNT-LINE-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-031 FOR UPDATE → DRAFT → line belongs to the count (stockCountFk DBF-INV-134) → delete QR-INV-034 → AuditApi
Repository   : QR-INV-031, QR-INV-034 · join NONE · READ_WRITE
Concurrency  : header lock
Security     : SCR-REQ-INV-006 · PERM_INV_COUNTS_UPDATE
Localization : ar + en
<!-- API:API-INV-031:END -->

<!-- API:API-INV-032:START traces=REQ-INV-139,REQ-INV-092,REQ-INV-093,REQ-INV-090,REQ-INV-068,DBF-INV-117,DBF-INV-119,DBF-INV-122,DBF-INV-124,DBF-INV-028,DBF-INV-131,DBF-INV-132,DBF-INV-133,DBF-INV-144,DBF-INV-146,DBF-INV-147,DBF-INV-148,DBF-INV-149,DBF-INV-150,DBF-INV-151,DBF-INV-159,DBF-INV-160,DBF-INV-162,DBF-INV-163,DBF-INV-166,DBF-INV-167,DBF-INV-168,DBF-INV-171 -->
### API-INV-032 — post stock count
Entity       : ENT-INV-008, ENT-INV-009, ENT-INV-010 · operation UPDATE · screen operations: post
Endpoint     : /api/v1/inv/stock-counts/{id}/post   verb: PATCH
Layers       : StockCountController.post → StockCountService.post → StockAdjustmentService.createAndPost → StockPostingEngine.post
Request      : path id (DBF-INV-115) · body VersionedActionRequest {version DBF-INV-124 (required)} (ADR-INV-032)
Response     : 200 · ApiResponse<StockCountPostResult> {stockCount (with systemQty / differenceQty and, on a dual-quantity line, the derived systemMeasureQty — ADR-INV-054), stockAdjustment (or null when nothing differs)}
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED · RULE-INV-031 → INV-422-NO-LINES · re-checked on post by engine step 0 (ADR-INV-045): RULE-INV-007 → INV-422-WAREHOUSE-INACTIVE, RULE-INV-018 → INV-422-VARIANT-INACTIVE, RULE-INV-037 → INV-422-LOT-REQUIRED (also a lot-less line counted above the lot-less on-hand at posting — REQ-INV-139; a lot-less line's systemQty is that on-hand, so its difference is never positive), RULE-INV-040 → INV-422-MEASURE-REQUIRED · RULE-INV-019 → INV-409-NEGATIVE-STOCK · RULE-INV-025 is not raised: an increase of a never-valued Variant enters at cost 0 and creates no valuation row (ADR-INV-030 as extended), because a count line carries no cost · RULE-INV-033 is not checked: the system writes COUNT_DIFFERENCE itself, so a tenant that deactivated it still posts counts (ADR-INV-047) · RULE-INV-035 and RULE-INV-036 are not applied to a generated increase line, so INV-422-LOT-EXPIRY-REQUIRED and INV-422-LOT-EXPIRY-MISMATCH are never raised here: a positive difference on a counted lot line enters under that line's lotFk — an existing lot with its stored expiry — and no lot is resolved by lotNo or created (REQ-INV-092) · the adjustment-line part of RULE-INV-040 is not applied to a generated line: it carries its count line's measure difference, and the count line already carried the counted measure (step 0) · a line refusal names the count line in `details` (`LINE_PK` = stockCountLinePk, ADR-INV-046)
Errors       : INV-404-STOCK-COUNT-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-422-NO-LINES, INV-422-WAREHOUSE-INACTIVE, INV-422-VARIANT-INACTIVE, INV-422-LOT-REQUIRED, INV-422-MEASURE-REQUIRED, INV-409-NEGATIVE-STOCK, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-031 FOR UPDATE → DRAFT → version check (DBF-INV-124) → ≥ 1 line → warehouse QR-INV-002 FOR SHARE, Item stock settings QR-INV-043 FOR SHARE → engine step 0 on the count and its lines → lock each pair's lot-less balance row, then each line's lot row (QR-INV-017) → systemQty DBF-INV-132 = on-hand, differenceQty DBF-INV-133 = counted − system (QR-INV-034); **measure rule** — while dualQuantity is on, for a line of a dual-quantity Item: measure difference = countedMeasureQty DBF-INV-131 − COALESCE(ON_HAND_MEASURE_QTY DBF-INV-028, 0) of the same balance row, read under the same balance lock; the system measure is not stored — it is derived on read as countedMeasureQty − the generated line's measureQtyChange (ADR-INV-054, SRS D-15) → one adjustment line per count line whose differenceQty ≠ 0 or whose measure difference ≠ 0: create adjustment (QR-INV-037) reasonCode DBF-INV-144 = COUNT_DIFFERENCE, warehouseFk DBF-INV-147 = the count's, sourceCountFk DBF-INV-148, docNo DBF-INV-149 (INV_ADJUSTMENT), docDate DBF-INV-150, lines rowNo DBF-INV-159, quantityChange DBF-INV-162 = difference, measureQtyChange DBF-INV-163 = the measure difference (null when it is 0 or the Item is not dual-quantity), stockAdjustmentFk DBF-INV-166, variantFk DBF-INV-167, lotFk DBF-INV-168 = the count line's lotFk, lotNo DBF-INV-160 = the code DBF-INV-181 of that counted lot (read with the lot FOR SHARE, QR-INV-039 — engine step 1) and NULL for a count line without a lot, lotExpiryDate DBF-INV-161 left empty, currencyCode DBF-INV-171 — the code in lotNo keeps UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_VAR_LOT satisfied when one Variant has several counted lots, or a lot-less line beside lot lines, and a lot-less lotNo NULL stays unique per Variant because UQ_INV_STOCK_COUNT_LINE_CNT_VAR_LOT allows one lot-less count line per Variant (gate-analysis revise 5, G1) → post it through the posting engine with countGeneratedFl and no reason check (ADR-INV-047) (ADJUSTMENT_IN / ADJUSTMENT_OUT at the average; every generated line is resolved by its lotFk, never by its lotNo — an increase line uses the lot's stored expiry, with no RULE-INV-035 / 036 check and no lot created — engine step 3; a line with differenceQty 0 and a non-zero measure difference is a measure-only move, quantity 0, measure |difference|, value 0, ADJUSTMENT_IN when the difference is positive and ADJUSTMENT_OUT when negative — ADR-INV-017, RULE-INV-034 satisfied by the non-zero measure change; a line with both non-zero moves both; an ADJUSTMENT_IN of a never-valued Variant at 0 with no valuation row — ADR-INV-030; sourceDocTypeCode INV_ADJUSTMENT) → adjustment statusCode DBF-INV-151 = POSTED, postedAt DBF-INV-146 → count statusCode DBF-INV-122 = POSTED, postedAt DBF-INV-117, adjustmentFk DBF-INV-119 → persist QR-INV-032 → AuditApi → events
Repository   : QR-INV-031, QR-INV-002, QR-INV-043, QR-INV-017, QR-INV-039, QR-INV-018, QR-INV-019, QR-INV-022, QR-INV-023, QR-INV-024, QR-INV-034, QR-INV-037, QR-INV-032 · join NONE · READ_WRITE
Concurrency  : header FOR UPDATE; system quantities read under the balance locks the adjustment then changes, so a sale between "read system" and "post difference" is impossible
Security     : SCR-REQ-INV-006 · PERM_INV_COUNTS_UPDATE
Localization : ar + en
<!-- API:API-INV-032:END -->

<!-- API:API-INV-033:START traces=REQ-INV-094,REQ-INV-096,DBF-INV-115,DBF-INV-122 -->
### API-INV-033 — cancel stock count
Entity       : ENT-INV-008 · operation DELETE · screen operations: cancel
Endpoint     : /api/v1/inv/stock-counts/{id}   verb: DELETE
Layers       : StockCountController.cancel → StockCountService.cancel
Request      : path id (DBF-INV-115) · no body
Response     : 200 · ApiResponse<StockCountDto> (statusCode CANCELLED)
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Errors       : INV-404-STOCK-COUNT-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-031 FOR UPDATE → DRAFT → statusCode DBF-INV-122 = CANCELLED → persist QR-INV-032 → AuditApi; no stock changes (AC-INV-104)
Repository   : QR-INV-031, QR-INV-032 · join NONE · READ_WRITE
Concurrency  : header lock
Security     : SCR-REQ-INV-006 · PERM_INV_COUNTS_DELETE
Localization : ar + en
<!-- API:API-INV-033:END -->

<!-- API:API-INV-036:START traces=REQ-INV-139,REQ-INV-097,REQ-INV-101,REQ-INV-010,REQ-INV-013,REQ-INV-138,DBF-INV-144,DBF-INV-145,DBF-INV-147,DBF-INV-149,DBF-INV-150,DBF-INV-151,DBF-INV-159,DBF-INV-160,DBF-INV-161,DBF-INV-162,DBF-INV-163,DBF-INV-164,DBF-INV-166,DBF-INV-167,DBF-INV-171 -->
### API-INV-036 — create stock adjustment
Entity       : ENT-INV-010, ENT-INV-011 · operation CREATE · screen operations: create
Endpoint     : /api/v1/inv/stock-adjustments   verb: POST
Layers       : StockAdjustmentController.create → StockAdjustmentService.create
Request      : body StockAdjustmentCreateRequest {warehousePk DBF-INV-147 (optional while one is active), docDate DBF-INV-150, reasonCode DBF-INV-144 (required), noteText DBF-INV-145, lines[] {rowNo DBF-INV-159 (optional — 1..n in order), variantPk DBF-INV-167, lotNo DBF-INV-160, lotExpiryDate DBF-INV-161, quantityChange DBF-INV-162, measureQtyChange DBF-INV-163, unitCost DBF-INV-164}}
Response     : 201 · ApiResponse<StockAdjustmentDto>
Validations  : RULE-INV-007 → INV-422-WAREHOUSE-INACTIVE · RULE-INV-008 → INV-422-WAREHOUSE-REQUIRED · RULE-INV-051 → INV-422-DOC-DATE-FUTURE · RULE-INV-033 → INV-422-REASON-REQUIRED (also OPENING_STOCK, which only API-INV-047 writes — ADR-INV-047) · RULE-INV-018 → INV-422-VARIANT-INACTIVE · RULE-INV-017 → INV-422-QUANTITY-INVALID (quantityChange and measureQtyChange ≤ 3 dp) · RULE-INV-025 → INV-422-NO-KNOWN-COST (a unitCost with more than 4 dp; the "no known cost" part is checked at post) · RULE-INV-034 → INV-422-ZERO-CHANGE · RULE-INV-040 → INV-422-MEASURE-REQUIRED (a non-zero quantityChange of a dual-quantity Item without a measure change, dualQuantity on) · RULE-INV-037 → INV-422-LOT-REQUIRED (decrease lines of lot-tracked Items; a decrease without a lot is accepted up to the Variant's lot-less on-hand in the warehouse — REQ-INV-139) · RULE-INV-048 → INV-422-DUPLICATE-LINE · RULE-INV-030 (docNo assigned) — line rules held by `StockAdjustmentDomain` / `StockAdjustmentLineDomain`; a line refusal names its line in `details` (`REQUEST_POSITION` and rowNo — ADR-INV-046)
Errors       : INV-404-WAREHOUSE-NOT-FOUND, INV-422-WAREHOUSE-INACTIVE, INV-422-WAREHOUSE-REQUIRED, INV-422-DOC-DATE-FUTURE, INV-422-REASON-REQUIRED, INV-404-VARIANT-NOT-FOUND, INV-422-VARIANT-INACTIVE, INV-422-QUANTITY-INVALID, INV-422-NO-KNOWN-COST, INV-422-ZERO-CHANGE, INV-422-MEASURE-REQUIRED, INV-422-LOT-REQUIRED, INV-422-DUPLICATE-LINE, INV-400, INV-401, INV-403, INV-500
Orchestration: warehouse as API-INV-028 (QR-INV-002 FOR SHARE, QR-INV-006, QR-INV-005) → docDate not after today (RULE-INV-051) → reasonCode active in INV_ADJUSTMENT_REASON (MdlLookupApi) and not OPENING_STOCK → per line: Variant integrate (XM-INV-014), Item settings QR-INV-043; a decrease without a lot of a lot-tracked Item bounded by the lot-less on-hand of the adjustment's warehouse (QR-INV-019 lot-less mode, `LOT_FK IS NULL` — REQ-INV-139) → docNo DBF-INV-149 from NumberSeriesApi (INV_ADJUSTMENT), statusCode DBF-INV-151 = DRAFT, stockAdjustmentFk DBF-INV-166, currencyCode DBF-INV-171 → persist QR-INV-037 → AuditApi; no stock move (AC-INV-107)
Repository   : QR-INV-002, QR-INV-005, QR-INV-006, QR-INV-019, QR-INV-043, QR-INV-037 · join NONE · READ_WRITE
Concurrency  : docNo atomic; UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_VAR_LOT, UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_ROW
Security     : SCR-REQ-INV-007 · PERM_INV_ADJUSTMENTS_CREATE (a Seller/Cashier is refused with INV-403 — AC-INV-154)
Localization : ar + en
<!-- API:API-INV-036:END -->

<!-- API:API-INV-037:START traces=REQ-INV-139,REQ-INV-098,REQ-INV-103,DBF-INV-143,DBF-INV-144,DBF-INV-145,DBF-INV-153,DBF-INV-159,DBF-INV-160,DBF-INV-161,DBF-INV-162,DBF-INV-163,DBF-INV-164,DBF-INV-166,DBF-INV-167,DBF-INV-171 -->
### API-INV-037 — update stock adjustment
Entity       : ENT-INV-010, ENT-INV-011 · operation UPDATE · screen operations: update
Endpoint     : /api/v1/inv/stock-adjustments/{id}   verb: PUT
Layers       : StockAdjustmentController.update → StockAdjustmentService.update
Request      : path id (DBF-INV-143) · body StockAdjustmentUpdateRequest {reasonCode DBF-INV-144, noteText DBF-INV-145, version DBF-INV-153 (required), docNo (optional echo), lines[] as API-INV-036 (rowNo DBF-INV-159, variantPk DBF-INV-167, lotNo DBF-INV-160, lotExpiryDate DBF-INV-161, quantityChange DBF-INV-162, measureQtyChange DBF-INV-163, unitCost DBF-INV-164)} — reason, note and lines replace the stored ones (REQ-INV-098)
Response     : 200 · ApiResponse<StockAdjustmentDto>
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED · RULE-INV-030 → INV-422-DOC-NO-READONLY · RULE-INV-033 (OPENING_STOCK refused), 018, 017, 025 (4 dp), 034, 040, 037, 048 as API-INV-036; a line refusal names its line in `details` (`LINE_PK` for a kept line, `REQUEST_POSITION` for a new one, with rowNo — ADR-INV-046)
Errors       : INV-404-STOCK-ADJUSTMENT-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-422-DOC-NO-READONLY, INV-422-REASON-REQUIRED, INV-404-VARIANT-NOT-FOUND, INV-422-VARIANT-INACTIVE, INV-422-QUANTITY-INVALID, INV-422-NO-KNOWN-COST, INV-422-ZERO-CHANGE, INV-422-MEASURE-REQUIRED, INV-422-LOT-REQUIRED, INV-422-DUPLICATE-LINE, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-036 FOR UPDATE → DRAFT → version → validations (MdlLookupApi, integrate (XM-INV-014), QR-INV-043, QR-INV-019 lot-less mode `LOT_FK IS NULL` on the adjustment's warehouse for a decrease without a lot) → replace reason, note, lines (stockAdjustmentFk DBF-INV-166, currencyCode DBF-INV-171) → persist QR-INV-037 → AuditApi (line change 2 → 3 recorded — AC-INV-084)
Repository   : QR-INV-036, QR-INV-019, QR-INV-043, QR-INV-037 · join NONE · READ_WRITE
Concurrency  : header lock + VERSION
Security     : SCR-REQ-INV-007 · PERM_INV_ADJUSTMENTS_UPDATE
Localization : ar + en
<!-- API:API-INV-037:END -->

<!-- API:API-INV-038:START traces=REQ-INV-139,REQ-INV-141,REQ-INV-099,REQ-INV-102,REQ-INV-114,REQ-INV-123,REQ-INV-067,REQ-INV-068,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-036,REQ-INV-057,REQ-INV-059,REQ-INV-060,REQ-INV-061,DBF-INV-146,DBF-INV-151,DBF-INV-153,DBF-INV-165,DBF-INV-168,DBF-INV-177,DBF-INV-178,DBF-INV-180,DBF-INV-181,DBF-INV-184,DBF-INV-027,DBF-INV-029,DBF-INV-030,DBF-INV-031,DBF-INV-033,DBF-INV-041,DBF-INV-042,DBF-INV-044,DBF-INV-045,DBF-INV-046,DBF-INV-047,DBF-INV-048,DBF-INV-049,DBF-INV-051,DBF-INV-052,DBF-INV-220,DBF-INV-053,DBF-INV-054,DBF-INV-058,DBF-INV-061,DBF-INV-192,DBF-INV-193,DBF-INV-194,DBF-INV-195,DBF-INV-196,DBF-INV-197,DBF-INV-200 -->
### API-INV-038 — post stock adjustment
Entity       : ENT-INV-010, ENT-INV-011 · operation UPDATE · screen operations: post
Endpoint     : /api/v1/inv/stock-adjustments/{id}/post   verb: PATCH
Layers       : StockAdjustmentController.post → StockAdjustmentService.post → StockPostingEngine.post
Request      : path id (DBF-INV-143) · body VersionedActionRequest {version DBF-INV-153 (required)} (ADR-INV-032)
Response     : 200 · ApiResponse<StockAdjustmentDto> (POSTED, line lots and values)
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED · RULE-INV-031 → INV-422-NO-LINES · RULE-INV-033 → INV-422-REASON-REQUIRED (also a stored OPENING_STOCK — ADR-INV-047) · RULE-INV-034 → INV-422-ZERO-CHANGE · re-checked on post by engine step 0 (ADR-INV-045): RULE-INV-007 → INV-422-WAREHOUSE-INACTIVE, RULE-INV-018 → INV-422-VARIANT-INACTIVE, RULE-INV-037 → INV-422-LOT-REQUIRED, RULE-INV-040 → INV-422-MEASURE-REQUIRED (a non-zero quantity change of a dual-quantity Item without a measure change) · RULE-INV-019 → INV-409-NEGATIVE-STOCK · RULE-INV-025 → INV-422-NO-KNOWN-COST · RULE-INV-026 → INV-422-OPENING-COST-REQUIRED · RULE-INV-035 → INV-422-LOT-EXPIRY-REQUIRED · RULE-INV-036 → INV-422-LOT-EXPIRY-MISMATCH · RULE-INV-037 → INV-422-LOT-REQUIRED (a decrease naming no existing lot, or naming none beyond the lot-less on-hand at posting — REQ-INV-139) — a line refusal names its line in `details` (`LINE_PK` = stockAdjustmentLinePk, with rowNo — ADR-INV-046)
Errors       : INV-404-STOCK-ADJUSTMENT-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-422-NO-LINES, INV-422-REASON-REQUIRED, INV-422-ZERO-CHANGE, INV-422-WAREHOUSE-INACTIVE, INV-422-VARIANT-INACTIVE, INV-422-MEASURE-REQUIRED, INV-409-NEGATIVE-STOCK, INV-422-NO-KNOWN-COST, INV-422-OPENING-COST-REQUIRED, INV-422-LOT-EXPIRY-REQUIRED, INV-422-LOT-EXPIRY-MISMATCH, INV-422-LOT-REQUIRED, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-036 FOR UPDATE → DRAFT → version check (DBF-INV-153) → ≥ 1 line → reason still active and not OPENING_STOCK (MdlLookupApi — RULE-INV-033) → engine step 0 on the header and lines → OPENING_STOCK → RULE-INV-026 on every line → per line (Item settings QR-INV-043 FOR SHARE): increase with lotNo → QR-INV-040; existing lot → expiry equal else RULE-INV-036; new → lotExpiryDate required (RULE-INV-035) — an expiry date already before today is accepted and the lot is expired from creation (REQ-INV-141, ADR-INV-056) — and lot created QR-INV-041 (expiryDate DBF-INV-177, firstReceivedAt DBF-INV-178, variantFk DBF-INV-180, code DBF-INV-181, isActiveFl DBF-INV-184); lot-tracked increase without lotNo while lotExpiry is on → RULE-INV-035; decrease with lotNo → existing lot else RULE-INV-037 → lotFk DBF-INV-168 → warehouse QR-INV-002 FOR SHARE → posting engine: positive → ADJUSTMENT_IN at the line cost or the average (REQ-INV-102), negative → ADJUSTMENT_OUT at the average, measure-only → ADR-INV-017; an expired lot may leave by this path (REQ-INV-114, EXPIRY_WRITE_OFF); moves DBF-INV-041, DBF-INV-042, DBF-INV-044, DBF-INV-045, DBF-INV-046, DBF-INV-047 (INV_ADJUSTMENT), DBF-INV-048, DBF-INV-049, DBF-INV-051, DBF-INV-052 (= INV_ADJUSTMENT:{stockAdjustmentPk}), requestHash DBF-INV-220 (= SHA-256 of that key, ADR-INV-048), DBF-INV-053, DBF-INV-054, DBF-INV-058, DBF-INV-061; balances DBF-INV-027, DBF-INV-029, DBF-INV-030, DBF-INV-031, DBF-INV-033; valuation DBF-INV-192, DBF-INV-193, DBF-INV-194, DBF-INV-195, DBF-INV-196, DBF-INV-197, DBF-INV-200 → lineValue DBF-INV-165 = |quantity| × unit cost → statusCode DBF-INV-151 = POSTED, postedAt DBF-INV-146 → persist QR-INV-037 → AuditApi → events
Repository   : QR-INV-036, QR-INV-002, QR-INV-040, QR-INV-041, QR-INV-043, QR-INV-017, QR-INV-018, QR-INV-019, QR-INV-022, QR-INV-023, QR-INV-024, QR-INV-037 · join NONE · READ_WRITE
Concurrency  : header FOR UPDATE; lots resolved FOR UPDATE with insert-if-absent on UQ_INV_LOT_VAR_CODE (two posts creating one lot number collapse to one lot); balance and valuation locks per the engine
Security     : SCR-REQ-INV-007 · PERM_INV_ADJUSTMENTS_UPDATE
Localization : ar + en
<!-- API:API-INV-038:END -->

<!-- API:API-INV-039:START traces=REQ-INV-100,REQ-INV-103,DBF-INV-143,DBF-INV-151 -->
### API-INV-039 — cancel stock adjustment
Entity       : ENT-INV-010 · operation DELETE · screen operations: cancel
Endpoint     : /api/v1/inv/stock-adjustments/{id}   verb: DELETE
Layers       : StockAdjustmentController.cancel → StockAdjustmentService.cancel
Request      : path id (DBF-INV-143) · no body
Response     : 200 · ApiResponse<StockAdjustmentDto> (CANCELLED)
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED
Errors       : INV-404-STOCK-ADJUSTMENT-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-036 FOR UPDATE → DRAFT → statusCode DBF-INV-151 = CANCELLED → persist QR-INV-037 → AuditApi; no stock move (AC-INV-112)
Repository   : QR-INV-036, QR-INV-037 · join NONE · READ_WRITE
Concurrency  : header lock
Security     : SCR-REQ-INV-007 · PERM_INV_ADJUSTMENTS_DELETE
Localization : ar + en
<!-- API:API-INV-039:END -->

<!-- API:API-INV-042:START traces=REQ-INV-108,REQ-INV-077,DBF-INV-176,DBF-INV-177,DBF-INV-182,DBF-INV-183,DBF-INV-186 -->
### API-INV-042 — update (expiry correction) of a lot
Entity       : ENT-INV-012, ENT-INV-003 · operation UPDATE · screen operations: update (expiry correction)
Endpoint     : /api/v1/inv/lots/{id}   verb: PATCH
Layers       : LotController.correct → LotService.correct
Request      : path id (DBF-INV-176) · body LotUpdateRequest {expiryDate DBF-INV-177 (required, a date), nameAr DBF-INV-182, nameEn DBF-INV-183, version DBF-INV-186 (required)}
Response     : 200 · ApiResponse<LotDto>
Validations  : none raised — RULE-INV-036 keeps one expiry per lot and the correction replaces it (SRS D-16)
Errors       : INV-404-LOT-NOT-FOUND, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-039 FOR UPDATE (waits for any posting or issue holding the lot FOR SHARE) → version → set expiryDate DBF-INV-177, nameAr DBF-INV-182, nameEn DBF-INV-183 → persist QR-INV-041 → AuditApi records old → new expiry (AC-INV-121); later availability, FEFO and near-expiry reads use the new date
Repository   : QR-INV-039, QR-INV-041 · join NONE · READ_WRITE
Concurrency  : the lot is locked FOR UPDATE, then its VERSION is checked. A sale or FEFO selection reads the lots it draws from FOR SHARE (engine step 1), so a correction to a past date and a sale of the same lot serialise: the sale either completes before the correction or, after it, sees the lot expired and skips it (RULE-INV-038)
Security     : SCR-REQ-INV-008 · PERM_INV_LOTS_UPDATE
Localization : ar + en
<!-- API:API-INV-042:END -->

<!-- API:API-INV-045:START traces=REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,DBF-INV-206,DBF-INV-207,DBF-INV-208,DBF-INV-209,DBF-INV-210,DBF-INV-211,DBF-INV-212,DBF-INV-213,DBF-INV-215 -->
### API-INV-045 — create, update Item stock settings
Entity       : ENT-INV-014 · operation CREATE, UPDATE · screen operations: create, update
Endpoint     : /api/v1/inv/item-stock-settings/by-item/{itemId}   verb: PUT
Layers       : ItemStockSettingsController.save → ItemStockSettingsService.save
Request      : path itemId (→ itemFk DBF-INV-209) · body ItemStockSettingsRequest {lotTrackedFl DBF-INV-206 (required), dualQuantityFl DBF-INV-207 (required), measureUomCode DBF-INV-208 (required when dual), version DBF-INV-215 (required when the record exists)}
Response     : 200 · ApiResponse<ItemStockSettingsDto>
Validations  : RULE-INV-044 → INV-422-CAPABILITY-OFF · RULE-INV-045 → INV-422-BUNDLE-SETTINGS · RULE-INV-041 → INV-422-MEASURE-UNIT-REQUIRED (also a code that is not an active value of the unit-of-measure key named by INV_MEASURE_UOM_KEY, ADR-INV-043) · RULE-INV-042 → INV-409-LOT-TRACKING-IN-USE · RULE-INV-043 → INV-409-DUAL-QUANTITY-IN-USE
Errors       : INV-404-ITEM-NOT-FOUND, INV-422-CAPABILITY-OFF, INV-422-BUNDLE-SETTINGS, INV-422-MEASURE-UNIT-REQUIRED, INV-409-LOT-TRACKING-IN-USE, INV-409-DUAL-QUANTITY-IN-USE, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: Item integrate (XM-INV-015) — unknown → 404, BUNDLE → RULE-INV-045 → QR-INV-043 FOR UPDATE (absent → CREATE permission, present → UPDATE permission and version — ADR-INV-029) → a flag turned on → its capability flag on (InvFeatureFlags) else RULE-INV-044 → lotTrackedFl true → false → QR-INV-045 = 0 else RULE-INV-042 {count} → dualQuantityFl true → false → QR-INV-046 false else RULE-INV-043 → RULE-INV-041 (`ItemStockSettingsDomain`; the code checked through MdlLookupApi against the key read from SettingsApi INV_MEASURE_UOM_KEY) → key DBF-INV-210 = Item code, valueAr DBF-INV-211 / valueEn DBF-INV-212 = tracking summary, isActiveFl DBF-INV-213 = true → persist QR-INV-044 → AuditApi
Repository   : QR-INV-043, QR-INV-045, QR-INV-046, QR-INV-044 · join NONE · READ_WRITE
Concurrency  : record FOR UPDATE; two first saves of one Item collide on UQ_INV_ITEM_STOCK_SETTINGS_ITEM_FK — the loser re-reads and applies as an update; clearing a flag reads the balances inside the same transaction, and a receipt of a lot racing the clear is serialised because engine step 1 reads the same record FOR SHARE (QR-INV-043) — whichever comes second sees the other's result (ADR-INV-026)
Security     : SCR-REQ-INV-009 · PERM_INV_ITEM_STOCK_SETTINGS_CREATE (no record yet) / PERM_INV_ITEM_STOCK_SETTINGS_UPDATE (record exists)
Localization : ar + en
<!-- API:API-INV-045:END -->

<!-- API:API-INV-047:START traces=REQ-INV-140,REQ-INV-141,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-074,REQ-INV-064,REQ-INV-105,REQ-INV-106,REQ-INV-059,DBF-INV-144,DBF-INV-147,DBF-INV-149,DBF-INV-150,DBF-INV-151,DBF-INV-159,DBF-INV-160,DBF-INV-161,DBF-INV-162,DBF-INV-163,DBF-INV-164,DBF-INV-166,DBF-INV-167,DBF-INV-171,DBF-INV-177,DBF-INV-178,DBF-INV-180,DBF-INV-181,DBF-INV-184,DBF-INV-027,DBF-INV-029,DBF-INV-030,DBF-INV-031,DBF-INV-033,DBF-INV-041,DBF-INV-042,DBF-INV-044,DBF-INV-045,DBF-INV-046,DBF-INV-047,DBF-INV-048,DBF-INV-049,DBF-INV-051,DBF-INV-052,DBF-INV-220,DBF-INV-053,DBF-INV-054,DBF-INV-058,DBF-INV-061,DBF-INV-192,DBF-INV-193,DBF-INV-194,DBF-INV-195,DBF-INV-196,DBF-INV-197,DBF-INV-200 -->
### API-INV-047 — create (post), custom (download sheet, bulk load) — post opening stock
Entity       : ENT-INV-010, ENT-INV-011 · operation CREATE · screen operations: create (post), custom (download sheet, bulk load)
Endpoint     : /api/v1/inv/opening-stock   verb: POST
Layers       : OpeningStockController.post → OpeningStockService.post → StockAdjustmentService.createAndPost → StockPostingEngine.post
Request      : body OpeningStockRequest {idempotencyKey (required, text 1–50, generated by the screen once per load and kept across its retries; not a column — the load's moves record `OPENING_STOCK:<idempotencyKey>` in DBF-INV-052, ADR-INV-053), warehousePk DBF-INV-147 (optional while one is active), docDate DBF-INV-150 (optional — today), rows[] (1–1000 — RULE-INV-027) {rowNo DBF-INV-159 (required), variantPk DBF-INV-167 or barcode (one required — ADR-INV-027), quantity → quantityChange DBF-INV-162 (> 0), unitCost DBF-INV-164 (required, ≥ 0, ≤ 4 dp), lotNo DBF-INV-160, lotExpiryDate DBF-INV-161, measureQty → measureQtyChange DBF-INV-163}}. **Variant resolution, stated once (ADR-INV-035):** `barcode` is resolved through XM-INV-013; a row with `variantPk` is read through XM-INV-014. CAT v2 publishes no exact Variant-code read, so v1 accepts no Variant code: the sheet column is «الباركود» / "Barcode" (AC-INV-080), and a text that is not an active barcode fails its row with INV-422-LOAD-ROW-VARIANT. The Variant-code read is a platform finding owned by CAT (business-policies-inv PLATFORM FINDINGS); when CAT publishes it, an INV delta adds the fallback "barcode not found → exact Variant code" here and in XM-INV-013 / 014
Response     : 201 · ApiResponse<StockAdjustmentDto> (POSTED, reasonCode OPENING_STOCK)
Validations  : RULE-INV-027 → INV-422-LOAD-ROWS · per row (all rows checked, all failures returned in details, nothing posted — REQ-INV-072 → INV-422-LOAD-ROWS-INVALID): RULE-INV-028 → INV-422-LOAD-ROW-VARIANT · RULE-INV-018 → INV-422-VARIANT-INACTIVE · RULE-INV-017 → INV-422-QUANTITY-INVALID (quantity and measure ≤ 3 dp) · RULE-INV-025 → INV-422-NO-KNOWN-COST (unitCost with more than 4 dp) · RULE-INV-026 → INV-422-OPENING-COST-REQUIRED · RULE-INV-035 → INV-422-LOT-EXPIRY-REQUIRED · RULE-INV-036 → INV-422-LOT-EXPIRY-MISMATCH · a lotExpiryDate already before today is not refused: the row posts and its new lot is expired from creation (RULE-INV-038, REQ-INV-141, ADR-INV-056) · RULE-INV-040 → INV-422-MEASURE-REQUIRED · RULE-INV-048 → INV-422-DUPLICATE-LINE · RULE-INV-007 / 008 on the warehouse · RULE-INV-051 → INV-422-DOC-DATE-FUTURE (docDate after today) · RULE-INV-033 is not checked: the system writes OPENING_STOCK itself (ADR-INV-047) · RULE-INV-024 → the first answer (201, the first load's adjustment, nothing recorded) for a key already recorded with the same load hash · RULE-INV-050 → INV-409-IDEMPOTENCY-KEY-REUSED for a different hash (ADR-INV-053)
Errors       : INV-404-WAREHOUSE-NOT-FOUND, INV-409-IDEMPOTENCY-KEY-REUSED, INV-422-WAREHOUSE-INACTIVE, INV-422-WAREHOUSE-REQUIRED, INV-422-DOC-DATE-FUTURE, INV-422-LOAD-ROWS, INV-422-LOAD-ROWS-INVALID, INV-422-LOAD-ROW-VARIANT, INV-422-VARIANT-INACTIVE, INV-422-QUANTITY-INVALID, INV-422-NO-KNOWN-COST, INV-422-OPENING-COST-REQUIRED, INV-422-LOT-EXPIRY-REQUIRED, INV-422-LOT-EXPIRY-MISMATCH, INV-422-MEASURE-REQUIRED, INV-422-DUPLICATE-LINE, INV-400, INV-401, INV-403, INV-500
Orchestration: advisory lock `pg_advisory_xact_lock(hashtextextended(:tenantId || ':OPENING_STOCK:' || :idempotencyKey, 0))` (native, tenant in the hash — ADR-INV-044, ADR-INV-053) → moves with IDEMPOTENCY_KEY `OPENING_STOCK:<idempotencyKey>` (intent R-6) → found with the same REQUEST_HASH → answer 201 with the adjustment their sourceDocRef names (QR-INV-036), nothing recorded (RULE-INV-024); found with another hash → INV-409-IDEMPOTENCY-KEY-REUSED (RULE-INV-050) → RULE-INV-027 → warehouse as API-INV-028 (QR-INV-002, QR-INV-006, QR-INV-005) → resolve every row: barcode → integrate (XM-INV-013), Variant → integrate (XM-INV-014) (active, STANDARD), Item settings QR-INV-043, lot by (Variant, lotNo) QR-INV-040 → any failure → INV-422-LOAD-ROWS-INVALID with details → create adjustment (QR-INV-037) reasonCode DBF-INV-144 = OPENING_STOCK, warehouseFk DBF-INV-147, docNo DBF-INV-149 (INV_ADJUSTMENT), docDate DBF-INV-150, lines rowNo DBF-INV-159, quantityChange DBF-INV-162, unitCost DBF-INV-164, lotNo DBF-INV-160, lotExpiryDate DBF-INV-161, measureQtyChange DBF-INV-163, stockAdjustmentFk DBF-INV-166, variantFk DBF-INV-167, currencyCode DBF-INV-171 → lots created QR-INV-041 (DBF-INV-177, DBF-INV-178, DBF-INV-180, DBF-INV-181, DBF-INV-184) → posting engine: one ADJUSTMENT_IN per row at the row's cost (moves DBF-INV-041, DBF-INV-042, DBF-INV-044, DBF-INV-045, DBF-INV-046, DBF-INV-047, DBF-INV-048, DBF-INV-049, DBF-INV-051, DBF-INV-052 (= OPENING_STOCK:{idempotencyKey} — ADR-INV-053), requestHash DBF-INV-220 (= SHA-256 of the load's canonical form taken over the request as submitted — warehousePk and docDate as sent, absent when absent, never the default warehouse or today; the rows in rowNo order with their fields as sent, no trim, no barcode lookup — ADR-INV-053), DBF-INV-053, DBF-INV-054, DBF-INV-058, DBF-INV-061; balances DBF-INV-027, DBF-INV-029, DBF-INV-030, DBF-INV-031, DBF-INV-033; valuation DBF-INV-192, DBF-INV-193, DBF-INV-194, DBF-INV-195, DBF-INV-196, DBF-INV-197, DBF-INV-200) → statusCode DBF-INV-151 = POSTED → AuditApi → events
Repository   : QR-INV-036 (the first load's adjustment), QR-INV-002, QR-INV-005, QR-INV-006, QR-INV-043, QR-INV-040, QR-INV-041, QR-INV-037, QR-INV-017, QR-INV-018, QR-INV-019, QR-INV-022, QR-INV-023, QR-INV-024 · join NONE · READ_WRITE (one transaction, batched inserts)
Concurrency  : the load's idempotency key is guarded by the advisory lock of ADR-INV-044, taken before the key lookup, so a double submit or a retry after a timeout waits for the first and answers its 201 — one adjustment, one set of moves (ADR-INV-053); up to 1000 balance rows locked in key order (engine)
Security     : SCR-REQ-INV-010 · PERM_INV_OPENING_STOCK_CREATE
Localization : ar + en; each row error carries messageAr and messageEn
<!-- API:API-INV-047:END -->

<!-- API:API-INV-049:START traces=REQ-INV-065,REQ-INV-118,REQ-INV-119,DBF-INV-027,DBF-INV-177 -->
### API-INV-049 — update inventory settings
Entity       : ENT-INV-004, ENT-INV-012 (the settings govern their moves and expiry — given cu settings, no ENT of their own) · operation UPDATE · screen operations: update
Endpoint     : /api/v1/inv/settings   verb: PUT
Layers       : InventorySettingsController.update → InventorySettingsService.update
Request      : body InventorySettingsRequest {allowNegativeStockFl (required), nearExpiryDays (required, whole 1–365)} — written to the given cu settings INV_ALLOW_NEGATIVE_STOCK and INV_NEAR_EXPIRY_DAYS; no INV column (they govern the on-hand check of DBF-INV-027 and the horizon over DBF-INV-177)
Response     : 200 · ApiResponse<InventorySettingsDto>
Validations  : REQ-INV-119 → INV-422-HORIZON-RANGE (no RULE — SRS D-04, ADR-INV-028)
Errors       : INV-422-HORIZON-RANGE, INV-400, INV-401, INV-403, INV-500
Orchestration: validate the range → SettingsApi writes both tenant values → every later decrease reads the new INV_ALLOW_NEGATIVE_STOCK (REQ-INV-065), the next near-expiry read uses the new horizon (REQ-INV-118)
Repository   : none — given cu (SettingsApi)
Concurrency  : NONE — last write wins on a tenant setting; no allocation, no read-then-write
Security     : SCR-REQ-INV-011 · PERM_INV_SETTINGS_UPDATE
Localization : ar + en
<!-- API:API-INV-049:END -->
<!-- SUB:SVC-API-CRUD:END -->
