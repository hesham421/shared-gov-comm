<!-- source: PHASE:SVC-API / SUB:SVC-API-SEARCH -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-INV-001, DBF-INV-002, DBF-INV-003, DBF-INV-004, DBF-INV-005, DBF-INV-006, DBF-INV-007, DBF-INV-014, DBF-INV-015, DBF-INV-016, DBF-INV-017, DBF-INV-018, DBF-INV-019, DBF-INV-027, DBF-INV-028, DBF-INV-030, DBF-INV-031, DBF-INV-032, DBF-INV-040, DBF-INV-041, DBF-INV-042, DBF-INV-043, DBF-INV-046, DBF-INV-047, DBF-INV-048, DBF-INV-049, DBF-INV-053, DBF-INV-054, DBF-INV-055, DBF-INV-058, DBF-INV-068, DBF-INV-080, DBF-INV-087, DBF-INV-090, DBF-INV-091, DBF-INV-092, DBF-INV-093, DBF-INV-094, DBF-INV-115, DBF-INV-118, DBF-INV-120, DBF-INV-121, DBF-INV-122, DBF-INV-143, DBF-INV-144, DBF-INV-147, DBF-INV-149, DBF-INV-150, DBF-INV-151, DBF-INV-160, DBF-INV-161, DBF-INV-162, DBF-INV-163, DBF-INV-164, DBF-INV-176, DBF-INV-177, DBF-INV-179, DBF-INV-180, DBF-INV-181, DBF-INV-192, DBF-INV-193, DBF-INV-194, DBF-INV-196, DBF-INV-200, DBF-INV-206, DBF-INV-207, DBF-INV-208, DBF-INV-209, REQ-INV-001, REQ-INV-002, REQ-INV-005, REQ-INV-009, REQ-INV-010, REQ-INV-011, REQ-INV-012, REQ-INV-015, REQ-INV-016, REQ-INV-019, REQ-INV-020, REQ-INV-021, REQ-INV-024, REQ-INV-028, REQ-INV-029, REQ-INV-030, REQ-INV-032, REQ-INV-033, REQ-INV-035, REQ-INV-038, REQ-INV-039, REQ-INV-040, REQ-INV-041, REQ-INV-042, REQ-INV-044, REQ-INV-054, REQ-INV-055, REQ-INV-056, REQ-INV-059, REQ-INV-060, REQ-INV-061, REQ-INV-063, REQ-INV-066, REQ-INV-073, REQ-INV-075, REQ-INV-076, REQ-INV-086, REQ-INV-090, REQ-INV-095, REQ-INV-101, REQ-INV-104, REQ-INV-109, REQ-INV-110, REQ-INV-111, REQ-INV-112, REQ-INV-113, REQ-INV-115, REQ-INV-116, REQ-INV-117, REQ-INV-118, REQ-INV-121, REQ-INV-129, REQ-INV-130, REQ-INV-131, REQ-INV-132, REQ-INV-133, REQ-INV-134, REQ-INV-135, REQ-INV-136, REQ-INV-137 -->
<!-- SUB:SVC-API-SEARCH:START traces=REQ-INV-009,REQ-INV-010,REQ-INV-028,REQ-INV-039,REQ-INV-040,REQ-INV-063,REQ-INV-075,REQ-INV-086,REQ-INV-095,REQ-INV-104,REQ-INV-109,REQ-INV-116,REQ-INV-129,REQ-INV-073,REQ-INV-066,DBF-INV-004,DBF-INV-016,DBF-INV-027,DBF-INV-041,DBF-INV-092,DBF-INV-120,DBF-INV-149,DBF-INV-177,DBF-INV-192,DBF-INV-209 -->
<!-- API:API-INV-001:START traces=REQ-INV-009,REQ-INV-019,REQ-INV-030,DBF-INV-002,DBF-INV-003,DBF-INV-004,DBF-INV-005,DBF-INV-006,DBF-INV-007 -->
### API-INV-001 — search, list warehouses
Entity       : ENT-INV-001 · operation VIEW · screen operations: search, list
Endpoint     : /api/v1/inv/warehouses   verb: GET
Layers       : WarehouseController.search → WarehouseService.search
Request      : query code (DBF-INV-004, LIKE), name (DBF-INV-005 / DBF-INV-006, LIKE), branchPk (DBF-INV-003, EXACT; the screen hides it while one branch is active — REQ-INV-030), active (ACTIVE_ONLY | ALL on DBF-INV-007, default ACTIVE_ONLY), page, size · sort fixed: code
Response     : 200 · ApiResponse<Page<WarehouseDto>> · paginated (warehousePk, code, nameAr, nameEn, branchPk, isDefaultFl DBF-INV-002, isActiveFl, version, audit)
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-001 → map; an empty page is success
Repository   : QR-INV-001 · FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-001 · PERM_INV_WAREHOUSES_VIEW
Localization : nameAr / nameEn returned; English display falls back to nameAr in the client
<!-- API:API-INV-001:END -->

<!-- API:API-INV-002:START traces=REQ-INV-009,REQ-INV-005,DBF-INV-001 -->
### API-INV-002 — read warehouse
Entity       : ENT-INV-001 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/warehouses/{id}   verb: GET
Layers       : WarehouseController.read → WarehouseService.read
Request      : path id (DBF-INV-001)
Response     : 200 · ApiResponse<WarehouseDto> — inactive warehouses are returned too (RULE-INV-005)
Validations  : none
Errors       : INV-404-WAREHOUSE-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-002
Repository   : QR-INV-002 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-001 · PERM_INV_WAREHOUSES_VIEW
Localization : ar + en
<!-- API:API-INV-002:END -->

<!-- API:API-INV-008:START traces=REQ-INV-001,REQ-INV-002,REQ-INV-010,REQ-INV-011,REQ-INV-012,REQ-INV-015,REQ-INV-020,REQ-INV-021,REQ-INV-029,REQ-INV-030,REQ-INV-032,REQ-INV-132,REQ-INV-133,REQ-INV-134,REQ-INV-135,REQ-INV-136,REQ-INV-137,DBF-INV-002,DBF-INV-007,DBF-INV-015,DBF-INV-019 -->
### API-INV-008 — read inventory context
Entity       : ENT-INV-001, ENT-INV-002 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/context   verb: GET
Layers       : InventoryContextController.read → InventoryContextService.read
Request      : none
Response     : 200 · ApiResponse<InventoryContextDto> {activeWarehouseCount, activeBranchCount, lotExpiryFl, dualQuantityFl, bundlesFl} — every screen reads it on load to hide or show the warehouse / branch selectors, filters and columns, the transfer entry and the flag-bound fields (REQ-INV-010 … 012, 015, 029, 030, 032, 134, 135); the counts reflect the one default branch and warehouse provisioning seeds (REQ-INV-001, 002, 020, 021)
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: QR-INV-006 (active warehouses, DBF-INV-007), QR-INV-013 (active branches, DBF-INV-019) → InvFeatureFlags (lotExpiry, dualQuantity, bundles through SettingsApi — never the activity preset, REQ-INV-132, 133)
Repository   : QR-INV-006, QR-INV-013 · COUNT · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : any INV page VIEW — PERM_INV_WAREHOUSES_VIEW, PERM_INV_BRANCHES_VIEW, PERM_INV_STOCK_BALANCES_VIEW, PERM_INV_STOCK_MOVES_VIEW, PERM_INV_TRANSFERS_VIEW, PERM_INV_COUNTS_VIEW, PERM_INV_ADJUSTMENTS_VIEW, PERM_INV_LOTS_VIEW, PERM_INV_ITEM_STOCK_SETTINGS_VIEW, PERM_INV_OPENING_STOCK_VIEW, PERM_INV_SETTINGS_VIEW, PERM_INV_STOCK_VALUATION_VIEW (the menu of each role preset follows from the same grants — REQ-INV-136, 137)
Localization : no text
<!-- API:API-INV-008:END -->

<!-- API:API-INV-009:START traces=REQ-INV-028,REQ-INV-033,DBF-INV-015,DBF-INV-016,DBF-INV-017,DBF-INV-018,DBF-INV-019 -->
### API-INV-009 — search, list branches
Entity       : ENT-INV-002 · operation VIEW · screen operations: search, list
Endpoint     : /api/v1/inv/branches   verb: GET
Layers       : BranchController.search → BranchService.search
Request      : query code (DBF-INV-016, LIKE), name (DBF-INV-017 / DBF-INV-018, LIKE), active (ACTIVE_ONLY | ALL on DBF-INV-019), page, size · sort fixed: code
Response     : 200 · ApiResponse<Page<BranchDto>> · paginated (branchPk, code, nameAr, nameEn, isDefaultFl DBF-INV-015, isActiveFl, version, audit)
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-008
Repository   : QR-INV-008 · FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-002 · PERM_INV_BRANCHES_VIEW
Localization : ar + en
<!-- API:API-INV-009:END -->

<!-- API:API-INV-010:START traces=REQ-INV-028,REQ-INV-024,DBF-INV-014 -->
### API-INV-010 — read branch
Entity       : ENT-INV-002 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/branches/{id}   verb: GET
Layers       : BranchController.read → BranchService.read
Request      : path id (DBF-INV-014)
Response     : 200 · ApiResponse<BranchDto> — inactive included (RULE-INV-014)
Validations  : none
Errors       : INV-404-BRANCH-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-009
Repository   : QR-INV-009 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-002 · PERM_INV_BRANCHES_VIEW
Localization : ar + en
<!-- API:API-INV-010:END -->

<!-- API:API-INV-016:START traces=REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-041,REQ-INV-042,REQ-INV-113,REQ-INV-115,REQ-INV-121,REQ-INV-012,DBF-INV-027,DBF-INV-028,DBF-INV-030,DBF-INV-031,DBF-INV-032,DBF-INV-068,DBF-INV-177 -->
### API-INV-016 — search, list stock balances
Entity       : ENT-INV-003, ENT-INV-005, ENT-INV-012 · operation VIEW · screen operations: search, list
Endpoint     : /api/v1/inv/stock-balances   verb: GET
Layers       : StockBalanceController.search → StockQueryService.searchBalances
Request      : query variantPk (DBF-INV-030), term (a barcode), itemPk, warehousePk (DBF-INV-031), lotPk (DBF-INV-032), nonZero (on-hand DBF-INV-027 ≠ 0), page, size · sort fixed: Variant, warehouse, expiry
Response     : 200 · ApiResponse<Page<StockBalanceRowDto>> · paginated {variantPk, variantCode, variantNameAr, variantNameEn, warehousePk, warehouseCode DBF-INV-004, warehouseNameAr DBF-INV-005, warehouseNameEn DBF-INV-006 (the label every role can read — ADR-INV-036), lotPk, lotCode, expiryDate DBF-INV-177, expiredFl, onHandQty, reservedQty (from DBF-INV-068), availableQty, onHandMeasureQty DBF-INV-028, measureUomCode} — no cost or value column (SRS D-26)
Validations  : none (RULE-INV-038 applied to the available column, not raised)
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: Variant set — variantPk; term → integrate (XM-INV-013); itemPk → integrate (XM-INV-015) → QR-INV-015 (reserved per QR-INV-026) → a filter naming one Variant with no balance → zeros per active warehouse from QR-INV-016 (REQ-INV-041) → a bundle Variant with bundles on → the derived row of REQ-INV-042 (as API-INV-017) → Variant codes / names integrate (XM-INV-014) per page → measureUomCode per Item QR-INV-043 (dualQuantity on)
Repository   : QR-INV-015, QR-INV-016, QR-INV-026, QR-INV-043 · NATIVE · join required — ADR-INV-025 · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-003 · PERM_INV_STOCK_BALANCES_VIEW (Owner, Warehouse, Seller/Cashier)
Localization : lot expired mark label «منتهية الصلاحية» / "Expired" rendered by the client from expiredFl
<!-- API:API-INV-016:END -->

<!-- API:API-INV-017:START traces=REQ-INV-038,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-044,REQ-INV-054,REQ-INV-113,REQ-INV-121,DBF-INV-027,DBF-INV-028,DBF-INV-030,DBF-INV-031,DBF-INV-068,DBF-INV-080,DBF-INV-177 -->
### API-INV-017 — read availability of a Variant
Entity       : ENT-INV-003, ENT-INV-005, ENT-INV-012 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/stock-balances/availability/{variantId}   verb: GET
Layers       : StockBalanceController.availability → StockQueryService.availability (`@PreAuthorize` PERM_INV_STOCK_BALANCES_VIEW) → AvailabilityEngine.availability (the engine also serves the in-process readAvailability — CON-INV-010, ADR-INV-042)
Request      : path variantId (DBF-INV-030) · query warehousePk (DBF-INV-031, optional)
Response     : 200 · ApiResponse<AvailabilityDto> {variantPk, perWarehouse[] {warehousePk, warehouseCode DBF-INV-004, warehouseNameAr DBF-INV-005, warehouseNameEn DBF-INV-006, onHandQty, reservedQty, availableQty, onHandMeasureQty, measureUomCode}, total {onHandQty, reservedQty, availableQty, onHandMeasureQty}} — the warehouse label is on the row, so a Seller/Cashier reads "W1 / W2" with no warehouse permission (ADR-INV-036)
Validations  : RULE-INV-022 → INV-422-BUNDLE-NO-COMPONENTS; RULE-INV-038 applied (expired lots excluded from available); the same figures decide a reservation (RULE-INV-020, REQ-INV-044 — SVC-API-INT)
Errors       : INV-404-VARIANT-NOT-FOUND, INV-404-WAREHOUSE-NOT-FOUND, INV-422-BUNDLE-NO-COMPONENTS, INV-400, INV-401, INV-403, INV-500
Orchestration: Variant integrate (XM-INV-014) → warehousePk given → QR-INV-002 … (404) → bundles on and the Variant's Item is a bundle → components integrate (XM-INV-012) (none active → RULE-INV-022); per component QR-INV-016 + QR-INV-026; bundle available per warehouse = smallest whole of component available ÷ component quantity, bundle on-hand = smallest whole of component on-hand ÷ quantity, reserved 0 (components carry their own reservations) — REQ-INV-042 → otherwise QR-INV-016 (on-hand, sellable = outside expired lots) and QR-INV-026 (reserved), available = sellable − reserved; no rows → zeros (REQ-INV-041) → measure unit QR-INV-043 (dualQuantity on)
Repository   : QR-INV-002, QR-INV-016, QR-INV-026, QR-INV-043 · NATIVE · join required — ADR-INV-025 · READ_ONLY
Concurrency  : NONE — read only (a reservation re-reads under its own lock)
Security     : SCR-REQ-INV-003 · PERM_INV_STOCK_BALANCES_VIEW
Localization : ar + en
<!-- API:API-INV-017:END -->

<!-- API:API-INV-018:START traces=REQ-INV-075,REQ-INV-076,REQ-INV-016,REQ-INV-056,REQ-INV-110,REQ-INV-111,REQ-INV-112,DBF-INV-041,DBF-INV-042,DBF-INV-043,DBF-INV-046,DBF-INV-047,DBF-INV-048,DBF-INV-049,DBF-INV-053,DBF-INV-054,DBF-INV-055,DBF-INV-058 -->
### API-INV-018 — search, list stock moves
Entity       : ENT-INV-004 · operation VIEW · screen operations: search, list
Endpoint     : /api/v1/inv/stock-moves   verb: GET
Layers       : StockMoveController.search → StockQueryService.searchMoves
Request      : query variantPk (DBF-INV-053), warehousePk (DBF-INV-054), lotPk (DBF-INV-055), moveTypeCode (DBF-INV-041, a code of INV_MOVE_TYPE), docNo (DBF-INV-049), sourceDocTypeCode (DBF-INV-047, a code of INV_SOURCE_DOC_TYPE) and sourceDocRef (DBF-INV-048) — together they identify one source document exactly (the jumps from SCR-INV-005 and SCR-INV-007; SRS SCR-REQ-INV-004 B2), dateFrom, dateTo (DBF-INV-058), page, size · sort fixed: newest first
Response     : 200 · ApiResponse<Page<StockMoveDto>> · paginated — each move with lotCode (label of lotPk), sourceDocTypeCode DBF-INV-047 and sourceDocNo, quantity DBF-INV-042, measureQty DBF-INV-043, balanceAfterQty DBF-INV-046 (displayed when filtered to one Variant and one warehouse — REQ-INV-076; that view is ordered by posting order, newest posted first, so a backdated document posted later sits where its running balance belongs — QR-INV-020); the FEFO split, lot-less shortfall and lot-less-first issues of REQ-INV-110 … 112 are visible here as separate moves
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-020 → lot codes of the page QR-INV-039 (batched — CORE labels) → Variant codes / names integrate (XM-INV-014) per page
Repository   : QR-INV-020, QR-INV-039 · FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-004 · PERM_INV_STOCK_MOVES_VIEW
Localization : ar + en
<!-- API:API-INV-018:END -->

<!-- API:API-INV-019:START traces=REQ-INV-056,REQ-INV-055,DBF-INV-040 -->
### API-INV-019 — read stock move
Entity       : ENT-INV-004 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/stock-moves/{id}   verb: GET
Layers       : StockMoveController.read → StockQueryService.readMove
Request      : path id (DBF-INV-040)
Response     : 200 · ApiResponse<StockMoveDto> — moves are read-only: no update or delete route exists (RULE-INV-023)
Validations  : none
Errors       : INV-404-STOCK-MOVE-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-021 → lot code QR-INV-039 (CORE labels)
Repository   : QR-INV-021, QR-INV-039 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-004 · PERM_INV_STOCK_MOVES_VIEW
Localization : ar + en
<!-- API:API-INV-019:END -->

<!-- API:API-INV-020:START traces=REQ-INV-086,DBF-INV-090,DBF-INV-091,DBF-INV-092,DBF-INV-093,DBF-INV-094 -->
### API-INV-020 — search, list transfers
Entity       : ENT-INV-006, ENT-INV-007 · operation VIEW · screen operations: search, list
Endpoint     : /api/v1/inv/transfers   verb: GET
Layers       : TransferController.search → TransferService.search
Request      : query docNo (DBF-INV-092), dateFrom, dateTo (DBF-INV-093), fromWarehousePk (DBF-INV-090), toWarehousePk (DBF-INV-091), statusCode (DBF-INV-094), page, size · sort fixed: newest first
Response     : 200 · ApiResponse<Page<TransferDto>> · paginated (header fields, no lines)
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-027
Repository   : QR-INV-027 · FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-005 · PERM_INV_TRANSFERS_VIEW
Localization : ar + en
<!-- API:API-INV-020:END -->

<!-- API:API-INV-021:START traces=REQ-INV-086,DBF-INV-087 -->
### API-INV-021 — read transfer
Entity       : ENT-INV-006, ENT-INV-007 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/transfers/{id}   verb: GET
Layers       : TransferController.read → TransferService.read
Request      : path id (DBF-INV-087)
Response     : 200 · ApiResponse<TransferDto> (with lines, each with lotCode)
Validations  : none
Errors       : INV-404-TRANSFER-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-028 → lot codes of the lines QR-INV-039 (batched — CORE labels)
Repository   : QR-INV-028, QR-INV-039 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-005 · PERM_INV_TRANSFERS_VIEW
Localization : ar + en
<!-- API:API-INV-021:END -->

<!-- API:API-INV-026:START traces=REQ-INV-095,DBF-INV-118,DBF-INV-120,DBF-INV-121,DBF-INV-122 -->
### API-INV-026 — search, list stock counts
Entity       : ENT-INV-008, ENT-INV-009, ENT-INV-010 · operation VIEW · screen operations: search, list
Endpoint     : /api/v1/inv/stock-counts   verb: GET
Layers       : StockCountController.search → StockCountService.search
Request      : query docNo (DBF-INV-120), dateFrom, dateTo (DBF-INV-121), warehousePk (DBF-INV-118), statusCode (DBF-INV-122), page, size · sort fixed: newest first
Response     : 200 · ApiResponse<Page<StockCountDto>> · paginated (header fields)
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-030
Repository   : QR-INV-030 · FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-006 · PERM_INV_COUNTS_VIEW
Localization : ar + en
<!-- API:API-INV-026:END -->

<!-- API:API-INV-027:START traces=REQ-INV-095,REQ-INV-090,DBF-INV-115 -->
### API-INV-027 — read stock count
Entity       : ENT-INV-008, ENT-INV-009, ENT-INV-010 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/stock-counts/{id}   verb: GET
Layers       : StockCountController.read → StockCountService.read
Request      : path id (DBF-INV-115)
Response     : 200 · ApiResponse<StockCountDto> (with lines, each with lotCode; systemQty / differenceQty only once POSTED — SRS D-15 — and, on a dual-quantity line, systemMeasureQty = countedMeasureQty − the measureQtyChange of the resulting adjustment's line for that Variant and lot, or countedMeasureQty when the line produced none — ADR-INV-054; adjustmentPk and adjustmentDocNo of the resulting adjustment)
Validations  : none
Errors       : INV-404-STOCK-COUNT-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-031 → lot codes of the lines QR-INV-039 (batched), adjustment number and lines QR-INV-036 (labels — CORE; the lines give the derived systemMeasureQty — ADR-INV-054)
Repository   : QR-INV-031, QR-INV-039, QR-INV-036 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-006 · PERM_INV_COUNTS_VIEW
Localization : ar + en
<!-- API:API-INV-027:END -->

<!-- API:API-INV-034:START traces=REQ-INV-104,DBF-INV-144,DBF-INV-147,DBF-INV-149,DBF-INV-150,DBF-INV-151 -->
### API-INV-034 — search, list stock adjustments
Entity       : ENT-INV-010, ENT-INV-011 · operation VIEW · screen operations: search, list
Endpoint     : /api/v1/inv/stock-adjustments   verb: GET
Layers       : StockAdjustmentController.search → StockAdjustmentService.search
Request      : query docNo (DBF-INV-149), dateFrom, dateTo (DBF-INV-150), warehousePk (DBF-INV-147), reasonCode (DBF-INV-144), statusCode (DBF-INV-151), page, size · sort fixed: newest first
Response     : 200 · ApiResponse<Page<StockAdjustmentDto>> · paginated (header fields)
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-035
Repository   : QR-INV-035 · FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-007 · PERM_INV_ADJUSTMENTS_VIEW
Localization : ar + en
<!-- API:API-INV-034:END -->

<!-- API:API-INV-035:START traces=REQ-INV-104,DBF-INV-143 -->
### API-INV-035 — read stock adjustment
Entity       : ENT-INV-010, ENT-INV-011 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/stock-adjustments/{id}   verb: GET
Layers       : StockAdjustmentController.read → StockAdjustmentService.read
Request      : path id (DBF-INV-143)
Response     : 200 · ApiResponse<StockAdjustmentDto> (with lines ordered by rowNo, each with lotCode; sourceCountPk with sourceCountDocNo)
Validations  : none
Errors       : INV-404-STOCK-ADJUSTMENT-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-036 → lot codes of the lines QR-INV-039 (batched), source count number QR-INV-031 (labels — CORE)
Repository   : QR-INV-036, QR-INV-039, QR-INV-031 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-007 · PERM_INV_ADJUSTMENTS_VIEW
Localization : ar + en
<!-- API:API-INV-035:END -->

<!-- API:API-INV-040:START traces=REQ-INV-109,REQ-INV-115,REQ-INV-116,REQ-INV-117,DBF-INV-027,DBF-INV-031,DBF-INV-176,DBF-INV-177,DBF-INV-179,DBF-INV-180,DBF-INV-181 -->
### API-INV-040 — search, list, custom (near-expiry view) — search lots
Entity       : ENT-INV-012, ENT-INV-003 · operation VIEW · screen operations: search, list, custom (near-expiry view)
Endpoint     : /api/v1/inv/lots   verb: GET
Layers       : LotController.search → LotService.search
Request      : query variantPk (DBF-INV-180), code (DBF-INV-181), warehousePk (DBF-INV-031), expiryFrom, expiryTo (DBF-INV-177), nearExpiryOnly (within today + INV_NEAR_EXPIRY_DAYS, non-zero on-hand — REQ-INV-116), page, size · sort fixed: expiry date
Response     : 200 · ApiResponse<Page<LotRowDto>> · paginated {lotPk, variantPk, code, nameAr, nameEn, expiryDate, daysLeft, expiredFl, warehousePk, onHandQty (DBF-INV-027), nearExpiryNotifiedAt DBF-INV-179} — expired lots with stock stay listed and marked (REQ-INV-115)
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: horizon from InvSettings (INV_NEAR_EXPIRY_DAYS) → QR-INV-038 → Variant names integrate (XM-INV-014) per page; the daily job of CORE (REQ-INV-117) reads the same query
Repository   : QR-INV-038 · NATIVE · join required — ADR-INV-025 · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-008 · PERM_INV_LOTS_VIEW
Localization : ar + en
<!-- API:API-INV-040:END -->

<!-- API:API-INV-041:START traces=REQ-INV-109,DBF-INV-176 -->
### API-INV-041 — read lot
Entity       : ENT-INV-012, ENT-INV-003 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/lots/{id}   verb: GET
Layers       : LotController.read → LotService.read
Request      : path id (DBF-INV-176)
Response     : 200 · ApiResponse<LotDto> (with expiredFl)
Validations  : none
Errors       : INV-404-LOT-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-039
Repository   : QR-INV-039 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-008 · PERM_INV_LOTS_VIEW
Localization : ar + en
<!-- API:API-INV-041:END -->

<!-- API:API-INV-043:START traces=REQ-INV-129,REQ-INV-130,REQ-INV-131,DBF-INV-206,DBF-INV-207,DBF-INV-208,DBF-INV-209 -->
### API-INV-043 — search, list Item stock settings
Entity       : ENT-INV-014 · operation VIEW · screen operations: search, list
Endpoint     : /api/v1/inv/item-stock-settings   verb: GET
Layers       : ItemStockSettingsController.search → ItemStockSettingsService.search
Request      : query itemPk (DBF-INV-209), term (Item code or name — integrate (XM-INV-015)), lotTracked (DBF-INV-206), dualQuantity (DBF-INV-207), page, size · sort fixed: key (Item code)
Response     : 200 · ApiResponse<Page<ItemStockSettingsDto>> · paginated (with Item code and names from integrate (XM-INV-015), measureUomCode DBF-INV-208)
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: term → integrate (XM-INV-015) → QR-INV-042; the same query, filtered on one flag, answers the in-process turn-off check (REQ-INV-130, 131 — SVC-API-INT)
Repository   : QR-INV-042 · FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-009 · PERM_INV_ITEM_STOCK_SETTINGS_VIEW
Localization : ar + en
<!-- API:API-INV-043:END -->

<!-- API:API-INV-044:START traces=REQ-INV-129,DBF-INV-209 -->
### API-INV-044 — read Item stock settings
Entity       : ENT-INV-014 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/item-stock-settings/by-item/{itemId}   verb: GET
Layers       : ItemStockSettingsController.read → ItemStockSettingsService.readByItem (`@PreAuthorize` PERM_INV_ITEM_STOCK_SETTINGS_VIEW) → InventoryReadEngine.itemStockSettings (the engine also serves the in-process readItemStockSettings — CON-INV-018, ADR-INV-042)
Request      : path itemId (DBF-INV-209)
Response     : 200 · ApiResponse<ItemStockSettingsDto> — no record → lotTrackedFl false, dualQuantityFl false, measureUomCode empty (REQ-INV-129)
Validations  : none
Errors       : INV-404-ITEM-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: Item exists integrate (XM-INV-015) → QR-INV-043
Repository   : QR-INV-043 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-009 · PERM_INV_ITEM_STOCK_SETTINGS_VIEW
Localization : ar + en
<!-- API:API-INV-044:END -->

<!-- API:API-INV-046:START traces=REQ-INV-073,DBF-INV-160,DBF-INV-161,DBF-INV-162,DBF-INV-163,DBF-INV-164 -->
### API-INV-046 — custom (download sheet, bulk load) — download opening stock sheet
Entity       : ENT-INV-010, ENT-INV-011 · operation VIEW · screen operations: custom (download sheet, bulk load)
Endpoint     : /api/v1/inv/opening-stock/sheet   verb: GET
Layers       : OpeningStockController.sheet → OpeningStockService.sheet
Request      : none
Response     : 200 · `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet` binary (a file — not wrapped in ApiResponse) — columns «الباركود» / "Barcode" (barcode only in v1 — ADR-INV-035), «الكمية» / "Quantity" (→ DBF-INV-162), «تكلفة الوحدة» / "Unit cost" (→ DBF-INV-164); lot number and expiry date (→ DBF-INV-160, DBF-INV-161) only with lotExpiry on; measure (→ DBF-INV-163) only with dualQuantity on (AC-INV-080)
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: InvFeatureFlags → PrintApi renders report INV_OPENING_STOCK_SHEET (registered through ReportProvider) with the column set → stream the xlsx
Repository   : none — no database read
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-010 · PERM_INV_OPENING_STOCK_VIEW
Localization : column headers ar + en
<!-- API:API-INV-046:END -->

<!-- API:API-INV-048:START traces=REQ-INV-066,REQ-INV-118,DBF-INV-027,DBF-INV-177 -->
### API-INV-048 — read inventory settings
Entity       : ENT-INV-004, ENT-INV-012 (given cu settings governing them — no ENT of their own) · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/settings   verb: GET
Layers       : InventorySettingsController.read → InventorySettingsService.read
Request      : none
Response     : 200 · ApiResponse<InventorySettingsDto> {allowNegativeStockFl (never saved → true — REQ-INV-066), nearExpiryDays (never saved → 30 — REQ-INV-118)}
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: SettingsApi reads INV_ALLOW_NEGATIVE_STOCK and INV_NEAR_EXPIRY_DAYS (the platform defaults seeded by db-script BLOCK 8e apply until the tenant saves); they govern the on-hand check (DBF-INV-027) and the horizon over expiry dates (DBF-INV-177)
Repository   : none — given cu (SettingsApi)
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-011 · PERM_INV_SETTINGS_VIEW
Localization : no text
<!-- API:API-INV-048:END -->

<!-- API:API-INV-050:START traces=REQ-INV-063,REQ-INV-059,REQ-INV-061,DBF-INV-192,DBF-INV-193,DBF-INV-194,DBF-INV-196,DBF-INV-200 -->
### API-INV-050 — search, list stock valuation
Entity       : ENT-INV-013 · operation VIEW · screen operations: search, list
Endpoint     : /api/v1/inv/stock-valuation   verb: GET
Layers       : ValuationController.search → ValuationService.search
Request      : query variantPk (DBF-INV-196), term (a barcode — integrate (XM-INV-013)), itemPk (integrate (XM-INV-015)), nonZero (DBF-INV-192 ≠ 0), page, size · sort fixed: Variant
Response     : 200 · ApiResponse<ValuationPageDto> · paginated — Page<StockValuationDto> {variantPk, variantCode, variantNameAr, variantNameEn, totalOnHandQty, averageUnitCost DBF-INV-193, stockValue DBF-INV-194, currencyCode DBF-INV-200, lastMoveAt} plus grandTotalValue over the whole filter
Validations  : none
Errors       : INV-400, INV-401, INV-403, INV-500
Orchestration: Variant set → QR-INV-025 → Variant names integrate (XM-INV-014) per page
Repository   : QR-INV-025 · FIND_BY_CRITERIA + AGGREGATE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-012 · PERM_INV_STOCK_VALUATION_VIEW (Owner only — SRS D-26)
Localization : ar + en
<!-- API:API-INV-050:END -->

<!-- API:API-INV-051:START traces=REQ-INV-063,REQ-INV-060,DBF-INV-192,DBF-INV-193,DBF-INV-194,DBF-INV-196 -->
### API-INV-051 — read stock valuation of a Variant
Entity       : ENT-INV-013 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/stock-valuation/{variantId}   verb: GET
Layers       : ValuationController.read → ValuationService.readByVariant (`@PreAuthorize` PERM_INV_STOCK_VALUATION_VIEW) → InventoryReadEngine.valuation (the engine also serves the in-process readValuation — CON-INV-016, ADR-INV-042)
Request      : path variantId (DBF-INV-196)
Response     : 200 · ApiResponse<StockValuationDto> — never valued → totalOnHandQty 0, stockValue 0, averageUnitCost null (contract C-5)
Validations  : none
Errors       : INV-404-VARIANT-NOT-FOUND, INV-400, INV-401, INV-403, INV-500
Orchestration: Variant integrate (XM-INV-014) → QR-INV-023
Repository   : QR-INV-023 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — read only
Security     : SCR-REQ-INV-012 · PERM_INV_STOCK_VALUATION_VIEW
Localization : ar + en
<!-- API:API-INV-051:END -->

<!-- API:API-INV-052:START traces=REQ-INV-101,REQ-INV-075,REQ-INV-086,DBF-INV-041,DBF-INV-047,DBF-INV-094,DBF-INV-144 -->
### API-INV-052 — read an INV lookup key (InvLookups)
Entity       : ENT-INV-010, ENT-INV-004, ENT-INV-006 · operation VIEW · screen operations: read
Endpoint     : /api/v1/inv/lookups/{key}   verb: GET
Layers       : InventoryLookupController.read → InventoryLookupService.read (`@PreAuthorize` any INV page VIEW) → `InvLookups.read`
Request      : path key — one of the six keys INV owns: INV_ADJUSTMENT_REASON (codes of DBF-INV-144), INV_MOVE_TYPE (DBF-INV-041), INV_DOC_STATUS (DBF-INV-094, 122, 151), INV_RESERVATION_STATUS (DBF-INV-080), INV_SOURCE_DOC_TYPE (DBF-INV-047, 070), INV_CHANGE_KIND (event field)
Response     : 200 · ApiResponse<List<LookupValueDto>> {code, nameAr, nameEn, sortOrder} — the active values of the key ordered by sortOrder; an open key includes the values the tenant added · not paginated
Validations  : none raised by a RULE; a key INV does not own → INV-404-LOOKUP-KEY-UNKNOWN (ADR-INV-033)
Errors       : INV-404-LOOKUP-KEY-UNKNOWN, INV-400, INV-401, INV-403, INV-500
Orchestration: key in the six owned keys, else INV-404-LOOKUP-KEY-UNKNOWN → `OwnedLookups.read(key, mdlLookupApi::readActiveValuesByKey)` (CORE "InvLookups") → map; no other module's key is ever served (CAT's unit-of-measure key included)
Repository   : none — given mdl (MdlLookupApi, through the common helper OwnedLookups)
Concurrency  : NONE — read only
Security     : any INV page VIEW — the twelve VIEW constants, `hasAnyAuthority(…)` as API-INV-008
Localization : nameAr / nameEn per value
<!-- API:API-INV-052:END -->
<!-- SUB:SVC-API-SEARCH:END -->
