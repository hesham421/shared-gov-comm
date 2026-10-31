<!-- source: PHASE:DATA-DOM / SUB:DATA-DOM-TRANSACTIONAL -->
<!-- context: DATA-DOM-HEADER.md — phase-level preamble -->
<!-- traces: DBF-INV-026, DBF-INV-040, DBF-INV-066, DBF-INV-087, DBF-INV-101, DBF-INV-115, DBF-INV-129, DBF-INV-143, DBF-INV-158, DBF-INV-191, REQ-INV-035, REQ-INV-036, REQ-INV-038, REQ-INV-043, REQ-INV-047, REQ-INV-055, REQ-INV-059, REQ-INV-078, REQ-INV-080, REQ-INV-087, REQ-INV-092, REQ-INV-097, REQ-INV-099 -->
<!-- SUB:DATA-DOM-TRANSACTIONAL:START traces=REQ-INV-035,REQ-INV-036,REQ-INV-038,REQ-INV-043,REQ-INV-047,REQ-INV-055,REQ-INV-059,REQ-INV-078,REQ-INV-080,REQ-INV-087,REQ-INV-092,REQ-INV-097,REQ-INV-099,DBF-INV-026,DBF-INV-040,DBF-INV-066,DBF-INV-087,DBF-INV-101,DBF-INV-115,DBF-INV-129,DBF-INV-143,DBF-INV-158,DBF-INV-191 -->
### ENT-INV-003 — Stock balance / رصيد المخزون      kind: transactional
Honours        : CON-INV-004 (one row per Variant × warehouse × lot; written only by stock moves; reserved / available derived)
BINDINGS       : table INV_STOCK_BALANCE · PK DBF-INV-026 (PK_INV_STOCK_BALANCE) · identity · fields DBF-INV-026 … DBF-INV-039
BUSINESS CODE  : none
DEFAULT FIELDS : transactional → docDate DBF-INV-033 (date of the first move); no docNo / statusCode (SRS D-01); audit DBF-INV-036 … 039
DTO MEMBERSHIP : no request (never written by a user) · response StockBalanceRow: variantPk, Variant code / names (XM-INV-014), warehousePk, lotPk, lot code, expiryDate, expiredFl, onHandQty, reservedQty, availableQty (derived), onHandMeasureQty, measureUomCode (dualQuantity on)
LOOKUP FIELDS  : none
CONSTRAINTS    : UQ_INV_STOCK_BALANCE_VAR_WH_LOT (NULLS NOT DISTINCT), FK_INV_STOCK_BALANCE_INV_WAREHOUSE, FK_INV_STOCK_BALANCE_INV_LOT
DOMAIN RULES   : RULE-INV-004, 019, 038, 042, 043 read it; maintained only by `StockPostingEngine` (SVC-API-CRUD) through `StockBalanceDomain.apply` in the move's transaction
STATE MACHINE  : none
CROSS-MODULE   : XM-INV-001
OPERATIONS     : VIEW
REPOSITORY OPS : QR-INV-015 … QR-INV-019, QR-INV-045, QR-INV-046

### ENT-INV-004 — Stock move / حركة المخزون      kind: transactional
BINDINGS       : table INV_STOCK_MOVE · PK DBF-INV-040 (PK_INV_STOCK_MOVE) · identity · fields DBF-INV-040 … DBF-INV-065, DBF-INV-220 (requestHash — ADR-INV-048) · entity `@Immutable` (ADR-INV-049)
BUSINESS CODE  : none — identified by its source document number (DBF-INV-049)
DEFAULT FIELDS : transactional → docDate DBF-INV-058; audit DBF-INV-062 … 065 (UPDATED_* equal CREATED_* — never updated)
DTO MEMBERSHIP : no request · response every field + currencyCode + lotCode (label of lotPk — CORE "Labels on every row")
LOOKUP FIELDS  : moveTypeCode DBF-INV-041 key INV_MOVE_TYPE · sourceDocTypeCode DBF-INV-047 key INV_SOURCE_DOC_TYPE
CONSTRAINTS    : CHK_INV_STOCK_MOVE_MOVE_TYPE_CODE, CHK_INV_STOCK_MOVE_QUANTITY (quantity 0 only on a measure-only adjustment move — ADR-INV-017), CHK_INV_STOCK_MOVE_MEASURE_QTY, CHK_INV_STOCK_MOVE_UNIT_COST, CHK_INV_STOCK_MOVE_CURRENCY_CODE, FK_INV_STOCK_MOVE_INV_WAREHOUSE, FK_INV_STOCK_MOVE_INV_LOT, FK_INV_STOCK_MOVE_INV_RESERVATION · trigger TRG_INV_STOCK_MOVE_IMMUTABLE (BEFORE UPDATE OR DELETE — ADR-INV-049)
DOMAIN RULES   : RULE-INV-007, 008 (a warehouse on every move — REQ-INV-016), 017, 018, 019, 023 (insert-only repository, `@Immutable` entity, database trigger), 024, 025, 039, 040, 050 (app — request hash with the idempotency key)
STATE MACHINE  : none — immutable
CROSS-MODULE   : XM-INV-002, XM-INV-003
OPERATIONS     : VIEW
REPOSITORY OPS : QR-INV-020 … QR-INV-022

### ENT-INV-005 — Reservation / حجز المخزون      kind: transactional
BINDINGS       : table INV_RESERVATION · PK DBF-INV-066 (PK_INV_RESERVATION) · identity · fields DBF-INV-066 … DBF-INV-086, DBF-INV-221 (requestHash — ADR-INV-048)
BUSINESS CODE  : none — identified by its source document
DEFAULT FIELDS : transactional → docDate DBF-INV-079, statusCode DBF-INV-080; audit DBF-INV-083 … 086
DTO MEMBERSHIP : no REST request (created, released and consumed only in-process — SVC-API-INT) · reserved quantity appears in balance and availability reads
LOOKUP FIELDS  : statusCode DBF-INV-080 key INV_RESERVATION_STATUS · sourceDocTypeCode DBF-INV-070 key INV_SOURCE_DOC_TYPE
CONSTRAINTS    : CHK_INV_RESERVATION_STATUS_CODE, CHK_INV_RESERVATION_ORIGINAL_QTY, CHK_INV_RESERVATION_RESERVED_QTY, CHK_INV_RESERVATION_SHORTFALL_QTY, CHK_INV_RESERVATION_CLOSED_AT, FK_INV_RESERVATION_INV_WAREHOUSE
DOMAIN RULES   : RULE-INV-007, 008, 017, 018, 020, 021 (domain), 022, 024, 049 (app — compares the reservation with the issue line and its source document), 050 (app — request hash with the idempotency key)
STATE MACHINE  : statusCode DBF-INV-080 · ACTIVE (initial) → issue with remainder > 0 → ACTIVE · issue with remainder 0 → CONSUMED · release → RELEASED · release of a RELEASED reservation → returned unchanged, idempotent (ADR-INV-055) · CONSUMED and RELEASED terminal (CLOSED_AT DBF-INV-074 set) · invalid transition (release of CONSUMED, consumption of RELEASED or CONSUMED) → RULE-INV-021 (INV-409-RESERVATION-CLOSED)
CROSS-MODULE   : XM-INV-004, XM-INV-005
OPERATIONS     : VIEW
REPOSITORY OPS : QR-INV-026 (in-process intents: SVC-API-INT)

### ENT-INV-006 — Transfer / تحويل مخزني      kind: transactional
BINDINGS       : table INV_TRANSFER · PK DBF-INV-087 (PK_INV_TRANSFER) · identity · fields DBF-INV-087 … DBF-INV-100
BUSINESS CODE  : docNo DBF-INV-092 · `{PREFIX}-{SEQ:4}` prefix TR (e.g. TR-0005) · UQ_INV_TRANSFER_DOC_NO · NumberSeriesApi series INV_TRANSFER, read-only
DEFAULT FIELDS : transactional → docNo DBF-INV-092, docDate DBF-INV-093 (today by default), statusCode DBF-INV-094; audit DBF-INV-097 … 100
DTO MEMBERSHIP : create docDate, fromWarehousePk, toWarehousePk, noteText, lines · update + version · excluded transferPk, docNo, statusCode, postedAt, audit · response all + lines
LOOKUP FIELDS  : statusCode DBF-INV-094 key INV_DOC_STATUS
CONSTRAINTS    : UQ_INV_TRANSFER_DOC_NO, CHK_INV_TRANSFER_STATUS_CODE, CHK_INV_TRANSFER_TO_WAREHOUSE_FK, CHK_INV_TRANSFER_POSTED_AT, FK_INV_TRANSFER_INV_WAREHOUSE_1, FK_INV_TRANSFER_INV_WAREHOUSE_2
DOMAIN RULES   : RULE-INV-016 (CHK_INV_TRANSFER_TO_WAREHOUSE_FK + app), 019, 029, 030, 031, 051 (domain — docDate not after today)
STATE MACHINE  : statusCode DBF-INV-094 · DRAFT (initial) → post (Warehouse user / Owner) → POSTED · DRAFT → cancel → CANCELLED · POSTED, CANCELLED terminal · invalid → RULE-INV-029 (INV-409-DOCUMENT-CLOSED)
CROSS-MODULE   : none
OPERATIONS     : VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS : QR-INV-027 … QR-INV-029

### ENT-INV-007 — Transfer line / سطر التحويل      kind: transactional (line)
BINDINGS       : table INV_TRANSFER_LINE · PK DBF-INV-101 (PK_INV_TRANSFER_LINE) · identity · fields DBF-INV-101 … DBF-INV-114
BUSINESS CODE  : none — docNo, docDate, statusCode from the header (SRS D-01)
DTO MEMBERSHIP : request variantPk, lotPk, quantity, measureQty · excluded unitCost (set at posting), currencyCode · response all + lotCode (label of lotPk)
LOOKUP FIELDS  : none
CONSTRAINTS    : UQ_INV_TRANSFER_LINE_TRF_VAR_LOT, CHK_INV_TRANSFER_LINE_QUANTITY, CHK_INV_TRANSFER_LINE_MEASURE_QTY, CHK_INV_TRANSFER_LINE_UNIT_COST, CHK_INV_TRANSFER_LINE_CURRENCY_CODE, FK_INV_TRANSFER_LINE_INV_TRANSFER, FK_INV_TRANSFER_LINE_INV_LOT
DOMAIN RULES   : RULE-INV-017, 018, 029, 037, 040, 048
CROSS-MODULE   : XM-INV-006
OPERATIONS     : VIEW, CREATE, UPDATE
REPOSITORY OPS : QR-INV-028, QR-INV-029

### ENT-INV-008 — Stock count / جرد المخزون      kind: transactional
BINDINGS       : table INV_STOCK_COUNT · PK DBF-INV-115 (PK_INV_STOCK_COUNT) · identity · fields DBF-INV-115 … DBF-INV-128
BUSINESS CODE  : docNo DBF-INV-120 · `{PREFIX}-{SEQ:4}` prefix CNT · UQ_INV_STOCK_COUNT_DOC_NO · NumberSeriesApi series INV_COUNT, read-only
DEFAULT FIELDS : transactional → docNo DBF-INV-120, docDate DBF-INV-121, statusCode DBF-INV-122; audit DBF-INV-125 … 128
DTO MEMBERSHIP : create warehousePk, docDate, noteText · excluded stockCountPk, docNo, statusCode, adjustmentFk, postedAt · response all + adjustmentDocNo (label of adjustmentFk) + lines (systemQty / differenceQty after posting — SRS D-15)
LOOKUP FIELDS  : statusCode DBF-INV-122 key INV_DOC_STATUS
CONSTRAINTS    : UQ_INV_STOCK_COUNT_DOC_NO, CHK_INV_STOCK_COUNT_STATUS_CODE, CHK_INV_STOCK_COUNT_POSTED_AT, FK_INV_STOCK_COUNT_INV_WAREHOUSE, FK_INV_STOCK_COUNT_INV_STOCK_ADJUSTMENT
DOMAIN RULES   : RULE-INV-007, 008, 019, 029, 030, 031, 051 (domain — docDate not after today)
STATE MACHINE  : DRAFT → post → POSTED (creates and posts one COUNT_DIFFERENCE adjustment) · DRAFT → cancel → CANCELLED · terminal POSTED, CANCELLED · invalid → RULE-INV-029
CROSS-MODULE   : none
OPERATIONS     : VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS : QR-INV-030 … QR-INV-032

### ENT-INV-009 — Stock count line / سطر الجرد      kind: transactional (line)
BINDINGS       : table INV_STOCK_COUNT_LINE · PK DBF-INV-129 (PK_INV_STOCK_COUNT_LINE) · identity · fields DBF-INV-129 … DBF-INV-142
DTO MEMBERSHIP : add barcode or variantPk, lotPk, countedQty, countedMeasureQty · change countedQty, countedMeasureQty, version · excluded systemQty, differenceQty · response all + lotCode (label of lotFk)
CONSTRAINTS    : UQ_INV_STOCK_COUNT_LINE_CNT_VAR_LOT, CHK_INV_STOCK_COUNT_LINE_COUNTED_QTY, CHK_INV_STOCK_COUNT_LINE_COUNTED_MEASURE_QTY, FK_INV_STOCK_COUNT_LINE_INV_STOCK_COUNT, FK_INV_STOCK_COUNT_LINE_INV_LOT
DOMAIN RULES   : RULE-INV-018, 029, 032, 037, 040, 047
CROSS-MODULE   : XM-INV-007
OPERATIONS     : VIEW, UPDATE
REPOSITORY OPS : QR-INV-031, QR-INV-033, QR-INV-034

### ENT-INV-010 — Stock adjustment / تسوية المخزون      kind: transactional
BINDINGS       : table INV_STOCK_ADJUSTMENT · PK DBF-INV-143 (PK_INV_STOCK_ADJUSTMENT) · identity · fields DBF-INV-143 … DBF-INV-157
BUSINESS CODE  : docNo DBF-INV-149 · `{PREFIX}-{SEQ:4}` prefix ADJ (e.g. ADJ-0002) · UQ_INV_STOCK_ADJUSTMENT_DOC_NO · NumberSeriesApi series INV_ADJUSTMENT, read-only
DEFAULT FIELDS : transactional → docNo DBF-INV-149, docDate DBF-INV-150, statusCode DBF-INV-151; audit DBF-INV-154 … 157
DTO MEMBERSHIP : create warehousePk, docDate, reasonCode, noteText, lines · update reasonCode, noteText, lines, version · excluded docNo, statusCode, sourceCountFk, postedAt · response all + sourceCountDocNo (label of sourceCountFk) + lines
LOOKUP FIELDS  : reasonCode DBF-INV-144 key INV_ADJUSTMENT_REASON (open; MdlLookupApi active check) · statusCode DBF-INV-151 key INV_DOC_STATUS
CONSTRAINTS    : UQ_INV_STOCK_ADJUSTMENT_DOC_NO, CHK_INV_STOCK_ADJUSTMENT_STATUS_CODE, CHK_INV_STOCK_ADJUSTMENT_POSTED_AT, FK_INV_STOCK_ADJUSTMENT_INV_WAREHOUSE, FK_INV_STOCK_ADJUSTMENT_INV_STOCK_COUNT
DOMAIN RULES   : RULE-INV-007, 008, 019, 025, 026, 029, 030, 031, 033 (app — skipped for a system-generated adjustment, OPENING_STOCK refused on the screen — ADR-INV-047), 034, 035, 036, 040, 051 (domain — docDate not after today)
STATE MACHINE  : DRAFT → post → POSTED · DRAFT → cancel → CANCELLED · terminal POSTED, CANCELLED · invalid → RULE-INV-029 · opening stock and count differences are created and posted in one transaction
CROSS-MODULE   : none
OPERATIONS     : VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS : QR-INV-035 … QR-INV-037

### ENT-INV-011 — Stock adjustment line / سطر التسوية      kind: transactional (line)
BINDINGS       : table INV_STOCK_ADJUSTMENT_LINE · PK DBF-INV-158 (PK_INV_STOCK_ADJUSTMENT_LINE) · identity · fields DBF-INV-158 … DBF-INV-175
DTO MEMBERSHIP : request rowNo (optional on screen lines — assigned 1..n in order), variantPk, lotNo, lotExpiryDate, quantityChange, measureQtyChange, unitCost · excluded lotFk (resolved at posting), lineValue, currencyCode · response all + lotCode (label of lotFk)
CONSTRAINTS    : UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_VAR_LOT, UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_ROW, CHK_INV_STOCK_ADJUSTMENT_LINE_UNIT_COST, CHK_INV_STOCK_ADJUSTMENT_LINE_ROW_NO, CHK_INV_STOCK_ADJUSTMENT_LINE_CURRENCY_CODE, FK_INV_STOCK_ADJUSTMENT_LINE_INV_STOCK_ADJUSTMENT, FK_INV_STOCK_ADJUSTMENT_LINE_INV_LOT
DOMAIN RULES   : RULE-INV-018, 025, 026, 027, 028, 029, 034, 035, 036, 037, 040 (a non-zero quantity change of a dual-quantity Item carries a measure change), 048
CROSS-MODULE   : XM-INV-008
OPERATIONS     : VIEW, CREATE, UPDATE
REPOSITORY OPS : QR-INV-036, QR-INV-037

### ENT-INV-013 — Stock valuation / تقييم المخزون      kind: transactional
Honours        : CON-INV-005 (one row per Variant across the tenant; perpetual weighted average; written only by stock moves)
BINDINGS       : table INV_STOCK_VALUATION · PK DBF-INV-191 (PK_INV_STOCK_VALUATION) · identity · fields DBF-INV-191 … DBF-INV-204
DEFAULT FIELDS : transactional → docDate DBF-INV-197 (first valued move); audit DBF-INV-201 … 204
DTO MEMBERSHIP : no request · response variantPk, Variant code / names (XM-INV-014), totalOnHandQty, averageUnitCost, stockValue, currencyCode, lastMoveAt
CONSTRAINTS    : UQ_INV_STOCK_VALUATION_VARIANT_FK, CHK_INV_STOCK_VALUATION_AVERAGE_UNIT_COST, CHK_INV_STOCK_VALUATION_CURRENCY_CODE
DOMAIN RULES   : REQ-INV-059 … 062 (posting engine), RULE-INV-025 (absent = never valued, ADR-INV-030)
CROSS-MODULE   : XM-INV-010
OPERATIONS     : VIEW
REPOSITORY OPS : QR-INV-023 … QR-INV-025
<!-- SUB:DATA-DOM-TRANSACTIONAL:END -->
