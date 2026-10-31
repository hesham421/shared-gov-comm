<!-- source: PHASE:DATA-DOM / SUB:DATA-DOM-LOOKUP -->
<!-- context: DATA-DOM-HEADER.md — phase-level preamble -->
<!-- traces: DBF-INV-041, DBF-INV-144, DBF-INV-205, DBF-INV-208, REQ-INV-101, REQ-INV-124, REQ-INV-125, REQ-INV-129 -->
<!-- SUB:DATA-DOM-LOOKUP:START traces=REQ-INV-101,REQ-INV-124,REQ-INV-125,REQ-INV-129,DBF-INV-041,DBF-INV-144,DBF-INV-205,DBF-INV-208 -->
### Lookup keys owned (SRS A6 — seeded by the db-script, read through MdlLookupApi)

| Key | Open | Column(s) | Seeded values |
|---|---|---|---|
| INV_ADJUSTMENT_REASON | open | DBF-INV-144 | COUNT_DIFFERENCE, DAMAGE, EXPIRY_WRITE_OFF, OPENING_STOCK, OTHER (COUNT_DIFFERENCE and OPENING_STOCK are written by the system without the tenant-active check — ADR-INV-047; OPENING_STOCK is refused on the adjustment screen) |
| INV_MOVE_TYPE | closed | DBF-INV-041 | RECEIPT, RETURN_IN, ISSUE, RETURN_OUT, TRANSFER_OUT, TRANSFER_IN, ADJUSTMENT_IN, ADJUSTMENT_OUT |
| INV_DOC_STATUS | closed | DBF-INV-094, DBF-INV-122, DBF-INV-151 | DRAFT, POSTED, CANCELLED |
| INV_RESERVATION_STATUS | closed | DBF-INV-080 | ACTIVE, CONSUMED, RELEASED |
| INV_SOURCE_DOC_TYPE | open | DBF-INV-047, DBF-INV-070 | INV_TRANSFER, INV_COUNT, INV_ADJUSTMENT, PURCHASE_RECEIPT, PURCHASE_RETURN, SALES_INVOICE, POS_SALE, SALES_RETURN, SALES_ORDER, STOREFRONT_ORDER |
| INV_CHANGE_KIND | closed | — (event field of InventoryMasterChanged) | CREATED, UPDATED, DEACTIVATED, REACTIVATED, DEFAULT_CHANGED |

Consumed: the unit-of-measure lookup key of the Item owner → DBF-INV-208; the key is the SettingsApi setting INV_MEASURE_UOM_KEY, platform default `CAT_UOM` (ADR-INV-043).

### ENT-INV-014 — Item stock settings / إعدادات مخزون الصنف      kind: config
BINDINGS       : table INV_ITEM_STOCK_SETTINGS · PK DBF-INV-205 (PK_INV_ITEM_STOCK_SETTINGS) · identity · fields DBF-INV-205 … DBF-INV-219
BUSINESS CODE  : key DBF-INV-210 = the Item's code (read through XM-INV-015), system-filled, read-only
DEFAULT FIELDS : config → key DBF-INV-210, valueAr DBF-INV-211, valueEn DBF-INV-212 (system-filled tracking summary — ADR-INV-029), isActiveFl DBF-INV-213; audit DBF-INV-216 … 219
DTO MEMBERSHIP : save (PUT by itemId) lotTrackedFl, dualQuantityFl, measureUomCode, version (when the record exists) · excluded key, valueAr, valueEn, isActiveFl · response all; an Item without a record answers false, false, empty (REQ-INV-129)
LOOKUP FIELDS  : measureUomCode DBF-INV-208 — the consumed unit-of-measure key named by INV_MEASURE_UOM_KEY (ADR-INV-043), stores the code
CONSTRAINTS    : UQ_INV_ITEM_STOCK_SETTINGS_ITEM_FK, CHK_INV_ITEM_STOCK_SETTINGS_MEASURE_UOM_CODE
DOMAIN RULES   : RULE-INV-041 (CHK_INV_ITEM_STOCK_SETTINGS_MEASURE_UOM_CODE + app), 042, 043, 044, 045 (UQ_INV_ITEM_STOCK_SETTINGS_ITEM_FK + app), 046 (in-process turn-off check)
STATE MACHINE  : none
CROSS-MODULE   : XM-INV-011
OPERATIONS     : VIEW, CREATE, UPDATE
REPOSITORY OPS : QR-INV-042 … QR-INV-046
<!-- SUB:DATA-DOM-LOOKUP:END -->
