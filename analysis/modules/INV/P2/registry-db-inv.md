## REGISTRY — P2 — INV v1

Source artifact : analysis/modules/INV/P2/db-script-inv.md · Date : 2026-10-31 · Profile : commerce · Dialect : postgresql16 · Schema prefix : none · PK generation : identity · Tenancy : TENANT_ID + FK_<TABLE>_TENANT + tenant-led index on every table, every UNIQUE with TENANT_ID, intra-module FKs composite with TENANT_ID (ADR-INV-014) · Migration : V1002__inv_inventory.sql

### Tables
| Table | ENT id | Kind | DBF range | DBF count |
|---|---|---|---|---|
| INV_WAREHOUSE | ENT-INV-001 | master | DBF-INV-001 … DBF-INV-013 | 13 |
| INV_BRANCH | ENT-INV-002 | master | DBF-INV-014 … DBF-INV-025 | 12 |
| INV_STOCK_BALANCE | ENT-INV-003 | transactional | DBF-INV-026 … DBF-INV-039 | 14 |
| INV_STOCK_MOVE | ENT-INV-004 | transactional | DBF-INV-040 … DBF-INV-065, DBF-INV-220 | 27 |
| INV_RESERVATION | ENT-INV-005 | transactional | DBF-INV-066 … DBF-INV-086, DBF-INV-221 | 22 |
| INV_TRANSFER | ENT-INV-006 | transactional | DBF-INV-087 … DBF-INV-100 | 14 |
| INV_TRANSFER_LINE | ENT-INV-007 | transactional (line) | DBF-INV-101 … DBF-INV-114 | 14 |
| INV_STOCK_COUNT | ENT-INV-008 | transactional | DBF-INV-115 … DBF-INV-128 | 14 |
| INV_STOCK_COUNT_LINE | ENT-INV-009 | transactional (line) | DBF-INV-129 … DBF-INV-142 | 14 |
| INV_STOCK_ADJUSTMENT | ENT-INV-010 | transactional | DBF-INV-143 … DBF-INV-157 | 15 |
| INV_STOCK_ADJUSTMENT_LINE | ENT-INV-011 | transactional (line) | DBF-INV-158 … DBF-INV-175 | 18 |
| INV_LOT | ENT-INV-012 | master | DBF-INV-176 … DBF-INV-190 | 15 |
| INV_STOCK_VALUATION | ENT-INV-013 | transactional | DBF-INV-191 … DBF-INV-204 | 14 |
| INV_ITEM_STOCK_SETTINGS | ENT-INV-014 | config | DBF-INV-205 … DBF-INV-219 | 15 |

Intra-module FKs (composite with TENANT_ID) : FK_INV_WAREHOUSE_INV_BRANCH · FK_INV_STOCK_BALANCE_INV_WAREHOUSE · FK_INV_STOCK_BALANCE_INV_LOT · FK_INV_RESERVATION_INV_WAREHOUSE · FK_INV_STOCK_MOVE_INV_WAREHOUSE · FK_INV_STOCK_MOVE_INV_LOT · FK_INV_STOCK_MOVE_INV_RESERVATION · FK_INV_TRANSFER_INV_WAREHOUSE_1 · FK_INV_TRANSFER_INV_WAREHOUSE_2 · FK_INV_TRANSFER_LINE_INV_TRANSFER · FK_INV_TRANSFER_LINE_INV_LOT · FK_INV_STOCK_COUNT_INV_WAREHOUSE · FK_INV_STOCK_COUNT_INV_STOCK_ADJUSTMENT · FK_INV_STOCK_COUNT_LINE_INV_STOCK_COUNT · FK_INV_STOCK_COUNT_LINE_INV_LOT · FK_INV_STOCK_ADJUSTMENT_INV_WAREHOUSE · FK_INV_STOCK_ADJUSTMENT_INV_STOCK_COUNT · FK_INV_STOCK_ADJUSTMENT_LINE_INV_STOCK_ADJUSTMENT · FK_INV_STOCK_ADJUSTMENT_LINE_INV_LOT (19)
Tenant FKs : FK_<TABLE>_TENANT on all 14 tables → CORE_TENANT(ID)
Cross-module columns (column only, no constraint — XM-INV-001 … XM-INV-011) : INV_STOCK_BALANCE.VARIANT_FK · INV_STOCK_MOVE.VARIANT_FK · INV_STOCK_MOVE.BUNDLE_VARIANT_FK · INV_RESERVATION.VARIANT_FK · INV_RESERVATION.BUNDLE_VARIANT_FK · INV_TRANSFER_LINE.VARIANT_FK · INV_STOCK_COUNT_LINE.VARIANT_FK · INV_STOCK_ADJUSTMENT_LINE.VARIANT_FK · INV_LOT.VARIANT_FK · INV_STOCK_VALUATION.VARIANT_FK (CAT Variant, BIGINT) · INV_ITEM_STOCK_SETTINGS.ITEM_FK (CAT Item, BIGINT)
Given tables not re-created : MDL_LOOKUP_TYPE / MDL_LOOKUP_VALUE (seeded only), CORE_NUMBER_SERIES (seeded only — INV_WAREHOUSE, INV_BRANCH, INV_TRANSFER, INV_COUNT, INV_ADJUSTMENT), NOTIF_TEMPLATE (seeded only — INV_NEGATIVE_STOCK, INV_NEAR_EXPIRY), CU_APP_CONFIGURATION (platform defaults seeded only — INV_ALLOW_NEGATIVE_STOCK, INV_NEAR_EXPIRY_DAYS, INV_MEASURE_UOM_KEY), SEC_USER / SEC_ROLE, CORE_AUDIT_EVENT, CORE_TENANT (FK target).

DBF ids : DBF-INV-001, DBF-INV-002, DBF-INV-003, DBF-INV-004, DBF-INV-005, DBF-INV-006, DBF-INV-007, DBF-INV-008, DBF-INV-009, DBF-INV-010, DBF-INV-011, DBF-INV-012, DBF-INV-013, DBF-INV-014, DBF-INV-015, DBF-INV-016, DBF-INV-017, DBF-INV-018, DBF-INV-019, DBF-INV-020, DBF-INV-021, DBF-INV-022, DBF-INV-023, DBF-INV-024, DBF-INV-025, DBF-INV-026, DBF-INV-027, DBF-INV-028, DBF-INV-029, DBF-INV-030, DBF-INV-031, DBF-INV-032, DBF-INV-033, DBF-INV-034, DBF-INV-035, DBF-INV-036, DBF-INV-037, DBF-INV-038, DBF-INV-039, DBF-INV-040, DBF-INV-041, DBF-INV-042, DBF-INV-043, DBF-INV-044, DBF-INV-045, DBF-INV-046, DBF-INV-047, DBF-INV-048, DBF-INV-049, DBF-INV-050, DBF-INV-051, DBF-INV-052, DBF-INV-053, DBF-INV-054, DBF-INV-055, DBF-INV-056, DBF-INV-057, DBF-INV-058, DBF-INV-059, DBF-INV-060, DBF-INV-061, DBF-INV-062, DBF-INV-063, DBF-INV-064, DBF-INV-065, DBF-INV-066, DBF-INV-067, DBF-INV-068, DBF-INV-069, DBF-INV-070, DBF-INV-071, DBF-INV-072, DBF-INV-073, DBF-INV-074, DBF-INV-075, DBF-INV-076, DBF-INV-077, DBF-INV-078, DBF-INV-079, DBF-INV-080, DBF-INV-081, DBF-INV-082, DBF-INV-083, DBF-INV-084, DBF-INV-085, DBF-INV-086, DBF-INV-087, DBF-INV-088, DBF-INV-089, DBF-INV-090, DBF-INV-091, DBF-INV-092, DBF-INV-093, DBF-INV-094, DBF-INV-095, DBF-INV-096, DBF-INV-097, DBF-INV-098, DBF-INV-099, DBF-INV-100, DBF-INV-101, DBF-INV-102, DBF-INV-103, DBF-INV-104, DBF-INV-105, DBF-INV-106, DBF-INV-107, DBF-INV-108, DBF-INV-109, DBF-INV-110, DBF-INV-111, DBF-INV-112, DBF-INV-113, DBF-INV-114, DBF-INV-115, DBF-INV-116, DBF-INV-117, DBF-INV-118, DBF-INV-119, DBF-INV-120, DBF-INV-121, DBF-INV-122, DBF-INV-123, DBF-INV-124, DBF-INV-125, DBF-INV-126, DBF-INV-127, DBF-INV-128, DBF-INV-129, DBF-INV-130, DBF-INV-131, DBF-INV-132, DBF-INV-133, DBF-INV-134, DBF-INV-135, DBF-INV-136, DBF-INV-137, DBF-INV-138, DBF-INV-139, DBF-INV-140, DBF-INV-141, DBF-INV-142, DBF-INV-143, DBF-INV-144, DBF-INV-145, DBF-INV-146, DBF-INV-147, DBF-INV-148, DBF-INV-149, DBF-INV-150, DBF-INV-151, DBF-INV-152, DBF-INV-153, DBF-INV-154, DBF-INV-155, DBF-INV-156, DBF-INV-157, DBF-INV-158, DBF-INV-159, DBF-INV-160, DBF-INV-161, DBF-INV-162, DBF-INV-163, DBF-INV-164, DBF-INV-165, DBF-INV-166, DBF-INV-167, DBF-INV-168, DBF-INV-169, DBF-INV-170, DBF-INV-171, DBF-INV-172, DBF-INV-173, DBF-INV-174, DBF-INV-175, DBF-INV-176, DBF-INV-177, DBF-INV-178, DBF-INV-179, DBF-INV-180, DBF-INV-181, DBF-INV-182, DBF-INV-183, DBF-INV-184, DBF-INV-185, DBF-INV-186, DBF-INV-187, DBF-INV-188, DBF-INV-189, DBF-INV-190, DBF-INV-191, DBF-INV-192, DBF-INV-193, DBF-INV-194, DBF-INV-195, DBF-INV-196, DBF-INV-197, DBF-INV-198, DBF-INV-199, DBF-INV-200, DBF-INV-201, DBF-INV-202, DBF-INV-203, DBF-INV-204, DBF-INV-205, DBF-INV-206, DBF-INV-207, DBF-INV-208, DBF-INV-209, DBF-INV-210, DBF-INV-211, DBF-INV-212, DBF-INV-213, DBF-INV-214, DBF-INV-215, DBF-INV-216, DBF-INV-217, DBF-INV-218, DBF-INV-219, DBF-INV-220, DBF-INV-221

### XM index

```yaml name=xm-register
records:
  - {id: XM-INV-001, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-035, REQ-INV-039, REQ-INV-040], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_STOCK_BALANCE · VARIANT_FK (DBF-INV-030)"}
  - {id: XM-INV-002, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-051, REQ-INV-052, REQ-INV-075], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_STOCK_MOVE · VARIANT_FK (DBF-INV-053)"}
  - {id: XM-INV-003, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-053], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_STOCK_MOVE · BUNDLE_VARIANT_FK (DBF-INV-056)"}
  - {id: XM-INV-004, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-043, REQ-INV-050], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_RESERVATION · VARIANT_FK (DBF-INV-076)"}
  - {id: XM-INV-005, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-049], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_RESERVATION · BUNDLE_VARIANT_FK (DBF-INV-078)"}
  - {id: XM-INV-006, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-078, REQ-INV-080], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_TRANSFER_LINE · VARIANT_FK (DBF-INV-106)"}
  - {id: XM-INV-007, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-088, REQ-INV-092], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_STOCK_COUNT_LINE · VARIANT_FK (DBF-INV-135)"}
  - {id: XM-INV-008, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-070, REQ-INV-072, REQ-INV-097], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_STOCK_ADJUSTMENT_LINE · VARIANT_FK (DBF-INV-167)"}
  - {id: XM-INV-009, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-106, REQ-INV-109], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_LOT · VARIANT_FK (DBF-INV-180)"}
  - {id: XM-INV-010, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-059, REQ-INV-063], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "INV_STOCK_VALUATION · VARIANT_FK (DBF-INV-196)"}
  - {id: XM-INV-011, type: HARD-FK, target_module: CAT, target_entity: ENT-CAT-001, traces: [REQ-INV-124, REQ-INV-125, REQ-INV-129], state: CONTRACTED, contract_ref: "CON-CAT-001", column: "INV_ITEM_STOCK_SETTINGS · ITEM_FK (DBF-INV-209)"}
  - {id: XM-INV-012, type: SOFT-READ, target_module: CAT, target_entity: ENT-CAT-007, traces: [REQ-INV-042, REQ-INV-049, REQ-INV-053, REQ-INV-054], state: CONTRACTED, contract_ref: "CON-CAT-006", column: "no column · the bundle availability, reservation and issue services read the Bundle component lines of the bundle Variant's Item through CAT getItem (CON-CAT-007)"}
  - {id: XM-INV-013, type: SOFT-READ, target_module: CAT, target_entity: ENT-CAT-009, traces: [REQ-INV-072, REQ-INV-088, REQ-INV-091], state: CONTRACTED, contract_ref: "CON-CAT-005", column: "no column · the count-line and opening-stock services resolve a scanned or loaded barcode to its Variant through CAT lookupBarcode (CON-CAT-010)"}
  - {id: XM-INV-014, type: SOFT-READ, target_module: CAT, target_entity: ENT-CAT-002, traces: [REQ-INV-052, REQ-INV-072, REQ-INV-078, REQ-INV-097], state: CONTRACTED, contract_ref: "CON-CAT-002", column: "no column · RULE-INV-018 / 028 checks and Variant names read through CAT getVariant (CON-CAT-008) and getItem (CON-CAT-007)"}
  - {id: XM-INV-015, type: SOFT-READ, target_module: CAT, target_entity: ENT-CAT-001, traces: [REQ-INV-039, REQ-INV-124, REQ-INV-126, REQ-INV-127, REQ-INV-129], state: CONTRACTED, contract_ref: "CON-CAT-001", column: "no column · Item kind, code, names and Variants read through CAT getItem (CON-CAT-007); Item term search through searchCatalogue (CON-CAT-016)"}
```

15 edges INV → CAT (11 HARD-FK, 4 SOFT-READ), all CONTRACTED against contract-cat (CAT v2): 13 registered at P2, and XM-INV-014 and XM-INV-015 (SOFT-READ, Variant and Item state reads), minted at P3.1 (ADR-INV-031) and back-registered here and in the db-script register exactly as the backend plan's `xm-minted` block states them (state CONTRACTED, contract_ref CON-CAT-002 / CON-CAT-001, no column). Every record comes from an SRS A8 `module-dependencies` row (ENT-CAT-001, ENT-CAT-002 HARD-FK; ENT-CAT-007, ENT-CAT-009 SOFT-READ). Edges into INV (PUR, SLS, SFR, STL, TAX, INS → CON-INV-001 … CON-INV-021) are minted by those consumers' P2 stages and derived by `gov.py graph`.

### Lookups
| Key | Seeded values | Count | Key owner | Table owner |
|---|---|---|---|---|
| INV_ADJUSTMENT_REASON | COUNT_DIFFERENCE, DAMAGE, EXPIRY_WRITE_OFF, OPENING_STOCK, OTHER | 5 (open) | INV | given `erp-core:modules.mdl` (MDL_LOOKUP_TYPE, MDL_LOOKUP_VALUE) |
| INV_MOVE_TYPE | RECEIPT, RETURN_IN, ISSUE, RETURN_OUT, TRANSFER_OUT, TRANSFER_IN, ADJUSTMENT_IN, ADJUSTMENT_OUT | 8 (closed) | INV | given `erp-core:modules.mdl` |
| INV_DOC_STATUS | DRAFT, POSTED, CANCELLED | 3 (closed) | INV | given `erp-core:modules.mdl` |
| INV_RESERVATION_STATUS | ACTIVE, CONSUMED, RELEASED | 3 (closed) | INV | given `erp-core:modules.mdl` |
| INV_SOURCE_DOC_TYPE | INV_TRANSFER, INV_COUNT, INV_ADJUSTMENT, PURCHASE_RECEIPT, PURCHASE_RETURN, SALES_INVOICE, POS_SALE, SALES_RETURN, SALES_ORDER, STOREFRONT_ORDER | 10 (open) | INV | given `erp-core:modules.mdl` |
| INV_CHANGE_KIND | CREATED, UPDATED, DEACTIVATED, REACTIVATED, DEFAULT_CHANGED | 5 (closed, event field) | INV | given `erp-core:modules.mdl` |

Consumed (not seeded here) : CAT unit-of-measure lookup → INV_ITEM_STOCK_SETTINGS.MEASURE_UOM_CODE (owner CAT); its key is the setting INV_MEASURE_UOM_KEY (ADR-INV-043).

Total seeded : 6 keys · 34 values (BLOCK 8a / 8b, TENANT_ID = 1) · number series 5 (BLOCK 8c: INV_WAREHOUSE `WH-`, INV_BRANCH `BR-`, INV_TRANSFER `TR-`, INV_COUNT `CNT-`, INV_ADJUSTMENT `ADJ-`, each + 4 digits, never reset) · notification templates 2 (BLOCK 8d: INV_NEGATIVE_STOCK, INV_NEAR_EXPIRY) · settings platform defaults 3 (BLOCK 8e: INV_ALLOW_NEGATIVE_STOCK = true, INV_NEAR_EXPIRY_DAYS = 30, INV_MEASURE_UOM_KEY = CAT_UOM).

### Sequences
Last DBF : DBF-INV-221 (next free DBF-INV-222 — DBF-INV-220, 221 added at gate-analysis revise 2, ADR-INV-048)
Triggers : TRG_INV_STOCK_MOVE_IMMUTABLE (BEFORE UPDATE OR DELETE ON INV_STOCK_MOVE) with its routine INV_STOCK_MOVE_IMMUTABLE_FN (ADR-INV-049)
Last XM  : XM-INV-015 (next free XM-INV-016 — XM-INV-014, 015 minted at P3.1)
Database sequences : none (identity PKs; business numbers from the given number series)

### Decisions
ADR-INV-014 (platform columns TENANT_ID and VERSION), ADR-INV-015 (currency code on monetary rows, CM-8), ADR-INV-016 (physical types and precision), ADR-INV-017 (measure-only adjustment move with quantity 0), ADR-INV-018 (no given events subscriber and no given file private store in v1 — C-GIVEN-2, repair round 1) — all ACCEPTED, non-breaking; ADR-INV-019 (P2 cannot clear the C-GIVEN-2 findings raised on P1 SRS text — repair round 2) — raised BLOCKED, now ACCEPTED (resolved by ADR-INV-020); ADR-INV-020 (SRS D-13 / D-23 override cells reworded so they name no given interface — C-GIVEN-2 cleared on the SRS side) — ACCEPTED, non-breaking. All seven are recorded in full in db-script-inv.md §5.2. ADR-INV-014 … 020 are also filed under `analysis/decisions/INV/` (014 … 017 filed at the P3.1 repair round 1). BLOCKED : none — P2 is complete. Next free ADR after P2 : ADR-INV-021. P2 deltas at gate-analysis revise 2 : ADR-INV-048 (REQUEST_HASH with the idempotency key), ADR-INV-049 (stock-move immutability trigger) — ACCEPTED, non-breaking. Module-wide next free : ADR-INV-051.
DEFAULTs : D-P2-01 … D-P2-14.
Applied from earlier stages : SRS D-01, D-02, D-03, D-04, D-06, D-07, D-13, D-14, D-19, D-22, D-23, D-24; contract C-3.

### Event
"P2 completed: INV v1 — 14 tables, 219 DBF, 15 XM (13 + 2 minted at P3.1) — ADR-INV-019 resolved by ADR-INV-020" · "gate-analysis revise 2: 221 DBF (REQUEST_HASH), 1 trigger — ADR-INV-048, ADR-INV-049"

### Cascade
None by hand — `gov.py graph` derives the edges targeting INV and raises their resolution events (shared/XM-PROTOCOL.md §5).
