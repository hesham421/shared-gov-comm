<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# BACKEND EXECUTION PLAN — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Profile : commerce   Stage : P3.1 (Backend Execution Plan) · pass 1 · round 1 · gate-analysis revise 5
Track  : backend · plan exec · lane analysis · prepared by claude:opus
Inputs : srs (REQ 141 · AC 160 · ENT 14 · RULE 51 · SCR-REQ 12) · db-script (14 tables · DBF 221 · XM 15 — 13 from P2 + the 2 minted here and back-registered, V1002__inv_inventory.sql) · registry-srs · registry-db · contract (CON-INV-001 … CON-INV-021) · contracts (the published contracts of the modules this one reads — reached here only through XM ids)
Governance : FULL (db-script present — every binding resolved by DBF / constraint name)
Produces : backend-execution-plan-inv.md · registry-exec-be-inv.md · api-spec-inv.yaml (OpenAPI 3.1.0, derived from §R3 blocks and the Error Catalog)
Owns ids : API-INV-001 … API-INV-052 · QR-INV-001 … QR-INV-046 (first sequences of the module; API-INV-052 added at gate-analysis revise 2)
ADRs raised : ADR-INV-021 … ADR-INV-032 (P3.1), ADR-INV-041 … ADR-INV-045 (gate-analysis revise 1) and ADR-INV-046 … ADR-INV-050 (gate-analysis revise 2) and ADR-INV-051 … ADR-INV-053 (gate-analysis revise 3) and ADR-INV-054 (gate-analysis revise 4) and ADR-INV-055, ADR-INV-056 (gate-analysis revise 5) — all ACCEPTED, non-breaking; filed under analysis/decisions/INV/ · next free ADR-INV-057
XM minted here : XM-INV-014, XM-INV-015 (SOFT-READ — this stage's runtime checks are the first reads of the Variant and Item state; back-registration is the orchestrator's)
Questions : none
══════════════════════════════════════════════════════════════════

## 0 — How to read this plan

Reading order for the implementation agent: Plan Index (§1) → ADRs (§2) → DB Alignment Manifest (§3) →
Query Reference Catalog (§4) → Error Catalog (§5) → the seven phases in profile order (CORE, DATA-DOM,
SVC-API, DOC, SEC-BE, ALIGN-BE, INT-C). The QR entries are logical intent, never code to paste. Every column
is reached by its `DBF-*` id; its physical name, type, nullability and default are looked up in the db-script
(`dbf-matrix`) — this plan never restates them. Outside `INT-C`, another module is named only by this
module's own `XM-*` id.

## 1 — Plan Index

```
EXECUTION PLAN INDEX — INV v1 — backend-execution-plan-inv.md
Profile: commerce · dialect: postgresql16 · framework: Spring Boot (Java 25) on the given erp-core
Open ADRs: 0 BLOCKED raised here (ADR-INV-021 … ADR-INV-032, ADR-INV-041 … ADR-INV-056 ACCEPTED) — decisions/INV/
Root package: com.commerce.inv (platform summary decision 11 — never com.erp.*, checklist F8)
INTEGRATION  15 edges — 13 registered (XM-INV-001 … XM-INV-013) + 2 minted (XM-INV-014, XM-INV-015), one block each in INT-C
GIVEN        12 uses — the SRS A9 given-usage rows (erp-core 1.6.0); none added here
SECURITY     12 screens × 3 role presets (Owner, Warehouse, Seller/Cashier) · 32 permissions declared
DB ALIGNMENT see §3 — 221 / 221 DBF bound, issues: 0
```

```yaml name=totals
DBF: 221
XM: 15
API: 52
QR: 46
```

### 1.1 Entity registry

| ENT | Name (en / ar) | Kind | Table | Business code | Operations |
|---|---|---|---|---|---|
| ENT-INV-001 | Warehouse / المستودع | master | INV_WAREHOUSE | code DBF-INV-004 · series INV_WAREHOUSE · UQ_INV_WAREHOUSE_CODE | VIEW, CREATE, UPDATE, DELETE |
| ENT-INV-002 | Branch / الفرع | master | INV_BRANCH | code DBF-INV-016 · series INV_BRANCH · UQ_INV_BRANCH_CODE | VIEW, CREATE, UPDATE, DELETE |
| ENT-INV-003 | Stock balance / رصيد المخزون | transactional | INV_STOCK_BALANCE | — | VIEW |
| ENT-INV-004 | Stock move / حركة المخزون | transactional | INV_STOCK_MOVE | — (source document number) | VIEW |
| ENT-INV-005 | Reservation / حجز المخزون | transactional | INV_RESERVATION | — | VIEW |
| ENT-INV-006 | Transfer / تحويل مخزني | transactional | INV_TRANSFER | docNo DBF-INV-092 · series INV_TRANSFER · UQ_INV_TRANSFER_DOC_NO | VIEW, CREATE, UPDATE, DELETE |
| ENT-INV-007 | Transfer line / سطر التحويل | transactional (line) | INV_TRANSFER_LINE | — | VIEW, CREATE, UPDATE |
| ENT-INV-008 | Stock count / جرد المخزون | transactional | INV_STOCK_COUNT | docNo DBF-INV-120 · series INV_COUNT · UQ_INV_STOCK_COUNT_DOC_NO | VIEW, CREATE, UPDATE, DELETE |
| ENT-INV-009 | Stock count line / سطر الجرد | transactional (line) | INV_STOCK_COUNT_LINE | — | VIEW, UPDATE |
| ENT-INV-010 | Stock adjustment / تسوية المخزون | transactional | INV_STOCK_ADJUSTMENT | docNo DBF-INV-149 · series INV_ADJUSTMENT · UQ_INV_STOCK_ADJUSTMENT_DOC_NO | VIEW, CREATE, UPDATE, DELETE |
| ENT-INV-011 | Stock adjustment line / سطر التسوية | transactional (line) | INV_STOCK_ADJUSTMENT_LINE | — | VIEW, CREATE, UPDATE |
| ENT-INV-012 | Lot / التشغيلة | master | INV_LOT | code DBF-INV-181 (entered lot number) · UQ_INV_LOT_VAR_CODE | VIEW, UPDATE |
| ENT-INV-013 | Stock valuation / تقييم المخزون | transactional | INV_STOCK_VALUATION | — | VIEW |
| ENT-INV-014 | Item stock settings / إعدادات مخزون الصنف | config | INV_ITEM_STOCK_SETTINGS | key DBF-INV-210 (system-filled) · UQ_INV_ITEM_STOCK_SETTINGS_ITEM_FK | VIEW, CREATE, UPDATE |

### 1.2 API registry

| API | Operation | Verb | Path | Traces (REQ) |
|---|---|---|---|---|
| API-INV-001 | search, list warehouses | GET | /api/v1/inv/warehouses | REQ-INV-009, REQ-INV-019 |
| API-INV-002 | read warehouse | GET | /api/v1/inv/warehouses/{id} | REQ-INV-009 |
| API-INV-003 | create warehouse | POST | /api/v1/inv/warehouses | REQ-INV-003, REQ-INV-031 |
| API-INV-004 | update warehouse | PUT | /api/v1/inv/warehouses/{id} | REQ-INV-004 |
| API-INV-005 | deactivate warehouse | DELETE | /api/v1/inv/warehouses/{id} | REQ-INV-005, REQ-INV-006 |
| API-INV-006 | reactivate warehouse | PATCH | /api/v1/inv/warehouses/{id}/reactivate | REQ-INV-007 |
| API-INV-007 | set as default warehouse | PATCH | /api/v1/inv/warehouses/{id}/default | REQ-INV-008 |
| API-INV-008 | read inventory context | GET | /api/v1/inv/context | REQ-INV-010, REQ-INV-015, REQ-INV-029, REQ-INV-032, REQ-INV-134 |
| API-INV-009 | search, list branches | GET | /api/v1/inv/branches | REQ-INV-028, REQ-INV-033 |
| API-INV-010 | read branch | GET | /api/v1/inv/branches/{id} | REQ-INV-028 |
| API-INV-011 | create branch | POST | /api/v1/inv/branches | REQ-INV-022 |
| API-INV-012 | update branch | PUT | /api/v1/inv/branches/{id} | REQ-INV-023 |
| API-INV-013 | deactivate branch | DELETE | /api/v1/inv/branches/{id} | REQ-INV-024, REQ-INV-025 |
| API-INV-014 | reactivate branch | PATCH | /api/v1/inv/branches/{id}/reactivate | REQ-INV-026 |
| API-INV-015 | set as default branch | PATCH | /api/v1/inv/branches/{id}/default | REQ-INV-027 |
| API-INV-016 | search, list stock balances | GET | /api/v1/inv/stock-balances | REQ-INV-039, REQ-INV-041 |
| API-INV-017 | read availability of a Variant | GET | /api/v1/inv/stock-balances/availability/{variantId} | REQ-INV-038, REQ-INV-040, REQ-INV-042 |
| API-INV-018 | search, list stock moves | GET | /api/v1/inv/stock-moves | REQ-INV-075, REQ-INV-076 |
| API-INV-019 | read stock move | GET | /api/v1/inv/stock-moves/{id} | REQ-INV-056 |
| API-INV-020 | search, list transfers | GET | /api/v1/inv/transfers | REQ-INV-086 |
| API-INV-021 | read transfer | GET | /api/v1/inv/transfers/{id} | REQ-INV-086 |
| API-INV-022 | create transfer | POST | /api/v1/inv/transfers | REQ-INV-078, REQ-INV-014, REQ-INV-082 |
| API-INV-023 | update transfer | PUT | /api/v1/inv/transfers/{id} | REQ-INV-079, REQ-INV-085 |
| API-INV-024 | post transfer | PATCH | /api/v1/inv/transfers/{id}/post | REQ-INV-080, REQ-INV-083, REQ-INV-084 |
| API-INV-025 | cancel transfer | DELETE | /api/v1/inv/transfers/{id} | REQ-INV-081 |
| API-INV-026 | search, list stock counts | GET | /api/v1/inv/stock-counts | REQ-INV-095 |
| API-INV-027 | read stock count | GET | /api/v1/inv/stock-counts/{id} | REQ-INV-095 |
| API-INV-028 | create stock count | POST | /api/v1/inv/stock-counts | REQ-INV-087 |
| API-INV-029 | update (lines) — add counted quantity | POST | /api/v1/inv/stock-counts/{id}/lines | REQ-INV-088, REQ-INV-091 |
| API-INV-030 | update (lines) — change counted line | PUT | /api/v1/inv/stock-counts/{id}/lines/{lineId} | REQ-INV-089 |
| API-INV-031 | update (lines) — remove counted line | DELETE | /api/v1/inv/stock-counts/{id}/lines/{lineId} | REQ-INV-089 |
| API-INV-032 | post stock count | PATCH | /api/v1/inv/stock-counts/{id}/post | REQ-INV-092, REQ-INV-093 |
| API-INV-033 | cancel stock count | DELETE | /api/v1/inv/stock-counts/{id} | REQ-INV-094 |
| API-INV-034 | search, list stock adjustments | GET | /api/v1/inv/stock-adjustments | REQ-INV-104 |
| API-INV-035 | read stock adjustment | GET | /api/v1/inv/stock-adjustments/{id} | REQ-INV-104 |
| API-INV-036 | create stock adjustment | POST | /api/v1/inv/stock-adjustments | REQ-INV-097, REQ-INV-101 |
| API-INV-037 | update stock adjustment | PUT | /api/v1/inv/stock-adjustments/{id} | REQ-INV-098, REQ-INV-103 |
| API-INV-038 | post stock adjustment | PATCH | /api/v1/inv/stock-adjustments/{id}/post | REQ-INV-099, REQ-INV-102, REQ-INV-114, REQ-INV-123, REQ-INV-141 |
| API-INV-039 | cancel stock adjustment | DELETE | /api/v1/inv/stock-adjustments/{id} | REQ-INV-100 |
| API-INV-040 | search, list lots (custom near-expiry view) | GET | /api/v1/inv/lots | REQ-INV-109, REQ-INV-116 |
| API-INV-041 | read lot | GET | /api/v1/inv/lots/{id} | REQ-INV-109 |
| API-INV-042 | update (expiry correction) of a lot | PATCH | /api/v1/inv/lots/{id} | REQ-INV-108 |
| API-INV-043 | search, list Item stock settings | GET | /api/v1/inv/item-stock-settings | REQ-INV-129 |
| API-INV-044 | read Item stock settings | GET | /api/v1/inv/item-stock-settings/by-item/{itemId} | REQ-INV-129 |
| API-INV-045 | create, update Item stock settings | PUT | /api/v1/inv/item-stock-settings/by-item/{itemId} | REQ-INV-124 … REQ-INV-128 |
| API-INV-046 | download opening stock sheet | GET | /api/v1/inv/opening-stock/sheet | REQ-INV-073 |
| API-INV-047 | post opening stock | POST | /api/v1/inv/opening-stock | REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-074, REQ-INV-140, REQ-INV-141 |
| API-INV-048 | read inventory settings | GET | /api/v1/inv/settings | REQ-INV-066, REQ-INV-118 |
| API-INV-049 | update inventory settings | PUT | /api/v1/inv/settings | REQ-INV-065, REQ-INV-118, REQ-INV-119 |
| API-INV-050 | search, list stock valuation | GET | /api/v1/inv/stock-valuation | REQ-INV-063 |
| API-INV-051 | read stock valuation of a Variant | GET | /api/v1/inv/stock-valuation/{variantId} | REQ-INV-063 |
| API-INV-052 | read an INV lookup key (InvLookups — OwnedLookups) | GET | /api/v1/inv/lookups/{key} | REQ-INV-101, REQ-INV-075, REQ-INV-086 |

### 1.3 Rule registry (full statement and trigger; messages per language live once, in the Error Catalog §5)

| RULE | Scope | Trigger | Statement | Code (§5) |
|---|---|---|---|---|
| RULE-INV-001 | ENT-INV-001 | on create; on update | The system shall assign the warehouse code from the number series INV_WAREHOUSE on first save and prevent any change to it afterwards. | INV-422-WAREHOUSE-CODE-READONLY |
| RULE-INV-002 | ENT-INV-001 | on create; on update | The system shall require nameAr and prevent saving when another warehouse of the tenant has the same nameAr. | INV-409-WAREHOUSE-NAME-TAKEN |
| RULE-INV-003 | ENT-INV-001 | on deactivate | The system shall prevent deactivation when the warehouse has isDefaultFl true. | INV-409-WAREHOUSE-DEFAULT |
| RULE-INV-004 | ENT-INV-001 | on deactivate | The system shall prevent deactivation when any Stock balance of the warehouse has non-zero onHandQty, any ACTIVE Reservation names the warehouse, or any DRAFT Transfer, Stock count or Stock adjustment names the warehouse. | INV-409-WAREHOUSE-IN-USE |
| RULE-INV-005 | ENT-INV-001 | on deactivate | The system shall keep a deactivated warehouse readable by its identifier, exclude it from the active warehouse list and every new selection, and notify consumers through the inventory master-change event without changing their records. | effect rule — raises no error; message «المستودع غير نشط.» / "The warehouse is inactive." is the one RULE-INV-007 raises (INV-422-WAREHOUSE-INACTIVE) |
| RULE-INV-006 | ENT-INV-001 | on set as default; on create of the first warehouse | The system shall require the default warehouse to be active and keep exactly one warehouse of the tenant with isDefaultFl true. | INV-409-WAREHOUSE-DEFAULT-INACTIVE |
| RULE-INV-007 | ENT-INV-004, 005, 008, 010 | on submit of a receipt, issue or reservation (not on an issue with offlineFl true); on save of a count or adjustment; on post of a count or adjustment | The system shall prevent a stock count, stock adjustment, opening stock load or cross-module stock request that names a warehouse whose isActiveFl is false, except a synchronised offline sale (offlineFl true), which is recorded in the named warehouse (REQ-INV-069, ADR-INV-052). A transfer naming an inactive warehouse is refused by RULE-INV-016, not by this rule. | INV-422-WAREHOUSE-INACTIVE |
| RULE-INV-008 | ENT-INV-004, 005, 008, 010 | on submit; on save; on post | The system shall require a warehouse on a stock document or cross-module stock request when the tenant has two or more active warehouses, and assign the default warehouse when it has exactly one; a synchronised offline sale (offlineFl true) that names no warehouse is assigned the default warehouse whatever the number of active warehouses (ADR-INV-052). | INV-422-WAREHOUSE-REQUIRED |
| RULE-INV-009 | ENT-INV-001 | on create; on update | The system shall require branchFk to reference an active branch and assign the default branch when none is given. | INV-422-BRANCH-INACTIVE |
| RULE-INV-010 | ENT-INV-002 | on create; on update | The system shall assign the branch code from the number series INV_BRANCH on first save and prevent any change to it afterwards. | INV-422-BRANCH-CODE-READONLY |
| RULE-INV-011 | ENT-INV-002 | on create; on update | The system shall require nameAr and prevent saving when another branch of the tenant has the same nameAr. | INV-409-BRANCH-NAME-TAKEN |
| RULE-INV-012 | ENT-INV-002 | on deactivate | The system shall prevent deactivation when the branch has isDefaultFl true. | INV-409-BRANCH-DEFAULT |
| RULE-INV-013 | ENT-INV-002 | on deactivate | The system shall prevent deactivation when an active warehouse has branchFk equal to the branch. | INV-409-BRANCH-IN-USE |
| RULE-INV-014 | ENT-INV-002 | on deactivate | The system shall keep a deactivated branch readable by its identifier, exclude it from the active branch list and every new selection, and notify consumers through the inventory master-change event without changing their records. | effect rule — raises no error; message «الفرع غير نشط.» / "The branch is inactive." is shown as the inactive-state label; selecting an inactive branch raises RULE-INV-009 (INV-422-BRANCH-INACTIVE) |
| RULE-INV-015 | ENT-INV-002 | on set as default | The system shall require the default branch to be active and keep exactly one branch of the tenant with isDefaultFl true. | INV-409-BRANCH-DEFAULT-INACTIVE |
| RULE-INV-016 | ENT-INV-006 | on save; on post | The system shall prevent a transfer whose source and destination warehouses are the same, or either of which is inactive; this rule, not RULE-INV-007, owns the refusal of a transfer that names an inactive warehouse. | INV-422-TRANSFER-WAREHOUSES |
| RULE-INV-017 | ENT-INV-004, 005, 007, 011 | on submit; on save; on post | The system shall require every receipt, issue, reservation and transfer quantity to be greater than zero with at most 3 decimal places, and every measure quantity of a receipt, issue or transfer line and every quantity change and measure change of an adjustment or opening stock line to have at most 3 decimal places. | INV-422-QUANTITY-INVALID |
| RULE-INV-018 | ENT-INV-004, 005, 007, 009, 011 | on submit; on save; on post | The system shall prevent a stock line whose Variant or Item is inactive — except a line of a synchronised offline sale (offlineFl true), which is recorded (REQ-INV-069, ADR-INV-052) — and a receipt, customer return (RETURN_IN), transfer, count or adjustment line whose Variant belongs to a BUNDLE Item; only an issue or a reservation names a bundle Variant, moved through its components. | INV-422-VARIANT-INACTIVE |
| RULE-INV-019 | ENT-INV-004, 006, 010 | on submit of an issue; on post of a transfer or adjustment | The system shall prevent an online stock decrease that would take the on-hand quantity of a Variant in a warehouse below zero, or a decrease that names a lot and would take that lot's on-hand quantity in the warehouse below zero, when the cu setting INV_ALLOW_NEGATIVE_STOCK is false, and shall never apply this check to a move with offlineFl true. | INV-409-NEGATIVE-STOCK |
| RULE-INV-020 | ENT-INV-005 | on create of a reservation | The system shall prevent a reservation whose quantity exceeds the available quantity when the cu setting INV_ALLOW_NEGATIVE_STOCK is false, and record the uncovered part as shortfallQty when it is true. | INV-409-RESERVE-NOT-AVAILABLE (in-process, §5.1) |
| RULE-INV-021 | ENT-INV-005 | on release; on issue that references a reservation | The system shall prevent the release of a reservation whose statusCode is CONSUMED and the consumption of a reservation whose statusCode is not ACTIVE; the release of a reservation that is already RELEASED returns it unchanged, so a retried release is safe (ADR-INV-055). | INV-409-RESERVATION-CLOSED (in-process, §5.1) |
| RULE-INV-022 | ENT-INV-004, 005 | on submit of an issue or reservation of a bundle Variant | The system shall prevent the issue or reservation of a bundle Variant that has no active component line. | INV-422-BUNDLE-NO-COMPONENTS |
| RULE-INV-023 | ENT-INV-004 | on update; on delete | The system shall prevent every change and every deletion of a recorded stock move. | INV-409-STOCK-MOVE-IMMUTABLE (in-process, §5.1) |
| RULE-INV-024 | ENT-INV-004, 005, 010 | on submit of a receipt, issue or reservation; on submit of an opening stock load | The system shall require an idempotency key on every cross-module receipt, issue and reservation request and on every opening stock load (ADR-INV-053), and return the first result without recording anything when the key was already recorded. | not an error — the first answer is returned; message «سبق تسجيل هذا الطلب؛ أُعيدت النتيجة الأولى.» / "This request was already recorded; the first result is returned." is carried as the answer's informational message |
| RULE-INV-025 | ENT-INV-004, 011 | on submit of a receipt or customer return; on save and on post of an adjustment (the 4-decimal part on save, the no-known-cost part on post) | The system shall require unitCost ≥ 0 with at most 4 decimals on an incoming line and prevent a unitCost with more than 4 decimals, use the current averageUnitCost when the line has none, and prevent the line when it has none and the Variant has no Stock valuation — except an increase line of an adjustment generated by a stock count, which is valued at 0 when the Variant has no Stock valuation (ADR-INV-030). | INV-422-NO-KNOWN-COST |
| RULE-INV-026 | ENT-INV-011 | on post of an adjustment with reason OPENING_STOCK | The system shall require unitCost ≥ 0 and quantityChange > 0 on every line of an adjustment whose reasonCode is OPENING_STOCK. | INV-422-OPENING-COST-REQUIRED |
| RULE-INV-027 | ENT-INV-011 | on submit of an opening stock load | The system shall prevent an opening stock load with zero rows or more than 1000 rows. | INV-422-LOAD-ROWS |
| RULE-INV-028 | ENT-INV-011 | on submit of an opening stock load | The system shall require the barcode or the Variant identifier of every load row to identify exactly one active Variant of a STANDARD Item and report the row number when it does not. | INV-422-LOAD-ROW-VARIANT |
| RULE-INV-029 | ENT-INV-006 … 011 | on update; on cancel; on line change | The system shall prevent every change to a transfer, count or adjustment, and to its lines, when its statusCode is POSTED or CANCELLED. | INV-409-DOCUMENT-CLOSED |
| RULE-INV-030 | ENT-INV-006, 008, 010 | on create | The system shall assign docNo on first save from the series INV_TRANSFER, INV_COUNT or INV_ADJUSTMENT, unique per document type, and prevent any change to it afterwards. | INV-422-DOC-NO-READONLY |
| RULE-INV-031 | ENT-INV-006, 008, 010 | on post | The system shall prevent posting a transfer, count or adjustment that has no line. | INV-422-NO-LINES |
| RULE-INV-032 | ENT-INV-009 | on scan | The system shall prevent adding a count line from a barcode that identifies no active Variant. | INV-422-BARCODE-UNKNOWN |
| RULE-INV-033 | ENT-INV-010 | on save; on post | The system shall require reasonCode to be an active value of INV_ADJUSTMENT_REASON other than OPENING_STOCK on every adjustment saved or posted from the stock adjustment screen, and shall not check the reason of a system-generated adjustment (count posting, opening stock load), whose reason the system writes itself (ADR-INV-047). | INV-422-REASON-REQUIRED |
| RULE-INV-034 | ENT-INV-011 | on save; on post | The system shall prevent an adjustment line whose quantityChange is zero unless its Item is dual-quantity and its measureQtyChange is non-zero. | INV-422-ZERO-CHANGE |
| RULE-INV-035 | ENT-INV-004, 011, 012 | on submit of a receipt; on post of an increase line that is not generated by a stock count posting | The system shall require a lot number and an expiry date on every incoming line of an Item whose lotTrackedFl is true while the lotExpiry capability flag is on, except an increase line of an adjustment generated by a stock count posting (countGeneratedFl), which uses the counted line's lot — an existing lot with its stored expiry date — and needs neither (REQ-INV-092). | INV-422-LOT-EXPIRY-REQUIRED |
| RULE-INV-036 | ENT-INV-012 | on create of a lot; on incoming line naming an existing lot (not on an increase line generated by a stock count posting) | The system shall keep lot numbers unique per Variant and prevent an incoming line that names an existing lot number with a different expiry date; an increase line generated by a stock count posting names its lot by identifier (lotFk) and is not checked by this rule (REQ-INV-092). | INV-422-LOT-EXPIRY-MISMATCH |
| RULE-INV-037 | ENT-INV-007, 009, 011 | on save; on post | The system shall require a lot of the line's Variant on every transfer, count and decrease adjustment line of an Item whose lotTrackedFl is true while the lotExpiry capability flag is on, except a line that names no lot and whose quantity (counted quantity, transfer quantity or decrease) is not above the on-hand quantity of its Variant held without a lot in the line's warehouse, which posts against that stock without a lot (REQ-INV-139, ADR-INV-051). | INV-422-LOT-REQUIRED |
| RULE-INV-038 | ENT-INV-012, 004 | on submit of a sale issue; on FEFO selection; on availability read | The system shall exclude from sale issue, FEFO selection and available quantity every lot whose expiryDate is before today in the tenant's time zone; a lot that a receipt or increase creates with an expiry date already before today is accepted and is expired from its creation (REQ-INV-141, ADR-INV-056). | effect rule — exclusion, raises no error; message «التشغيلة منتهية الصلاحية ولا تُباع.» / "The lot has expired and cannot be sold." is the expired-lot label (expiredFl) of balance and lot reads |
| RULE-INV-039 | ENT-INV-004 | on submit of an issue or return to supplier | The system shall prevent an ISSUE or RETURN_OUT move that names a lot whose expiryDate is before today. | INV-422-LOT-EXPIRED (in-process, §5.1) |
| RULE-INV-040 | ENT-INV-004, 007, 009, 011 | on submit; on save; on post | The system shall require a measure quantity greater than zero on every receipt, issue and transfer line, a counted measure on every count line, and a non-zero measure change on every adjustment or opening stock line with a non-zero quantity change, of an Item whose dualQuantityFl is true while the dualQuantity capability flag is on; a line of an adjustment generated by a stock count posting carries its count line's measure difference (REQ-INV-092) and is not checked by the adjustment-line part. | INV-422-MEASURE-REQUIRED |
| RULE-INV-041 | ENT-INV-014 | on save | The system shall require measureUomCode when dualQuantityFl is true. | INV-422-MEASURE-UNIT-REQUIRED |
| RULE-INV-042 | ENT-INV-014 | on update (lotTrackedFl true → false) | The system shall prevent clearing lotTrackedFl while any Stock balance with a lot of the Item's Variants has non-zero onHandQty. | INV-409-LOT-TRACKING-IN-USE |
| RULE-INV-043 | ENT-INV-014 | on update (dualQuantityFl true → false) | The system shall prevent clearing dualQuantityFl while any Stock balance of the Item's Variants has non-zero onHandMeasureQty. | INV-409-DUAL-QUANTITY-IN-USE |
| RULE-INV-044 | ENT-INV-014 | on create; on update | The system shall prevent setting lotTrackedFl true while the lotExpiry capability flag is off and setting dualQuantityFl true while the dualQuantity capability flag is off. | INV-422-CAPABILITY-OFF |
| RULE-INV-045 | ENT-INV-014 | on create | The system shall prevent Item stock settings for an Item whose itemKindCode is BUNDLE and keep at most one record per Item. | INV-422-BUNDLE-SETTINGS |
| RULE-INV-046 | ENT-INV-014 | on capability turn-off check | The system shall answer not allowed for lotExpiry while any Item stock settings record has lotTrackedFl true, and for dualQuantity while any has dualQuantityFl true. | not an error — the in-process answer `allowedFl false` carries message «لا يمكن إيقاف «{flag}»: {count} أصناف تعتمد عليه. ألغِ الإعداد لها أولًا.» / "{flag} cannot be switched off: {count} Items depend on it. Clear the setting on them first." (SVC-API-INT, CON-INV-019) |
| RULE-INV-047 | ENT-INV-009 | on save of a count line | The system shall require countedQty ≥ 0 and countedMeasureQty ≥ 0, each with at most 3 decimals. | INV-422-COUNTED-NEGATIVE |
| RULE-INV-048 | ENT-INV-007, 011 | on save | The system shall prevent two lines with the same Variant and the same lot on one transfer or one adjustment. | INV-422-DUPLICATE-LINE |
| RULE-INV-049 | ENT-INV-004, 005 | on issue that references a reservation | The system shall prevent an issue line that references a reservation whose variantFk or warehouseFk differs from the line's, or whose sourceDocTypeCode or sourceDocRef differs from the issue's source document. | INV-422-RESERVATION-MISMATCH (in-process, §5.1) |
| RULE-INV-050 | ENT-INV-004, 005, 010 | on submit of a receipt, issue, reservation or opening stock load whose idempotency key is already recorded | The system shall record with every idempotency key a hash of the request it came with, and prevent a receipt, issue, reservation or opening stock load that repeats a recorded key with a different request hash. | INV-409-IDEMPOTENCY-KEY-REUSED (HTTP on API-INV-047, and in-process — ADR-INV-053) |
| RULE-INV-051 | ENT-INV-006, 008, 010 | on save of a transfer, count or adjustment; on submit of an opening stock load | The system shall prevent a transfer, stock count, stock adjustment or opening stock load whose docDate is after today in the tenant's time zone. | INV-422-DOC-DATE-FUTURE |

No RULE of this module has a `Data source` marked DEFERRED: every check reads a column the db-script declares (db-script §2, C6.9).

### 1.4 Screen registry

| Screen | Type | ENT | Page code | Permissions declared |
|---|---|---|---|---|
| SCR-REQ-INV-001 Warehouses | Search + Entry | ENT-INV-001 | INV_WAREHOUSES | PERM_INV_WAREHOUSES_VIEW, _CREATE, _UPDATE, _DELETE |
| SCR-REQ-INV-002 Branches | Search + Entry | ENT-INV-002 | INV_BRANCHES | PERM_INV_BRANCHES_VIEW, _CREATE, _UPDATE, _DELETE |
| SCR-REQ-INV-003 Stock balances | Search + read-only | ENT-INV-003, 005, 012 | INV_STOCK_BALANCES | PERM_INV_STOCK_BALANCES_VIEW |
| SCR-REQ-INV-004 Stock move history | Search + read-only | ENT-INV-004 | INV_STOCK_MOVES | PERM_INV_STOCK_MOVES_VIEW |
| SCR-REQ-INV-005 Transfers | Search + Entry (header + lines) | ENT-INV-006, 007 | INV_TRANSFERS | PERM_INV_TRANSFERS_VIEW, _CREATE, _UPDATE, _DELETE |
| SCR-REQ-INV-006 Stock counts | Search + Entry (header + lines) | ENT-INV-008, 009, 010 | INV_COUNTS | PERM_INV_COUNTS_VIEW, _CREATE, _UPDATE, _DELETE |
| SCR-REQ-INV-007 Stock adjustments | Search + Entry (header + lines) | ENT-INV-010, 011 | INV_ADJUSTMENTS | PERM_INV_ADJUSTMENTS_VIEW, _CREATE, _UPDATE, _DELETE |
| SCR-REQ-INV-008 Lots and near expiry | Search + Entry | ENT-INV-012, 003 | INV_LOTS | PERM_INV_LOTS_VIEW, _UPDATE |
| SCR-REQ-INV-009 Item stock settings | Search + Entry | ENT-INV-014 | INV_ITEM_STOCK_SETTINGS | PERM_INV_ITEM_STOCK_SETTINGS_VIEW, _CREATE, _UPDATE |
| SCR-REQ-INV-010 Opening stock | Entry + bulk load | ENT-INV-010, 011 | INV_OPENING_STOCK | PERM_INV_OPENING_STOCK_VIEW, _CREATE |
| SCR-REQ-INV-011 Inventory settings | Entry | — (given cu settings) | INV_SETTINGS | PERM_INV_SETTINGS_VIEW, _UPDATE |
| SCR-REQ-INV-012 Stock valuation | Search + read-only | ENT-INV-013 | INV_STOCK_VALUATION | PERM_INV_STOCK_VALUATION_VIEW |

Full permission names and grants: SEC-BE (`permission-matrix`, `bootstrap`).

### 1.5 Given usage (SRS A9 rows, cited — none added)

| via | Used for in this plan |
|---|---|
| com.erp.cu.crossmodule.SettingsApi | capability flags lotExpiry / dualQuantity / bundles behind FeatureFlags; INV_ALLOW_NEGATIVE_STOCK, INV_NEAR_EXPIRY_DAYS (API-INV-008, 048, 049; posting engine); INV_MEASURE_UOM_KEY, the key of the consumed unit-of-measure lookup (API-INV-045, ADR-INV-043); tenant base currency for CURRENCY_CODE columns |
| com.erp.sequence.crossmodule.NumberSeriesApi | INV_WAREHOUSE, INV_BRANCH codes; INV_TRANSFER, INV_COUNT, INV_ADJUSTMENT document numbers |
| com.erp.events.OutboxPublisher | StockMoveRecorded (CON-INV-020), InventoryMasterChanged (CON-INV-021) |
| com.erp.audit.crossmodule.AuditApi | change history of documents, warehouses, branches, lots, Item stock settings (REQ-INV-077) |
| com.erp.mdl.crossmodule.MdlLookupApi | validation of stored lookup codes (INV_* keys and the consumed unit-of-measure key); the values of INV's six keys served to INV's screens by `InvLookups` through the common helper `OwnedLookups.read` over this interface (API-INV-052, ADR-INV-033) |
| com.erp.tenant.TenantProvisioningContributor | default branch and default warehouse per new tenant |
| com.erp.tenant.TenantRunner | daily near-expiry check per tenant |
| com.erp.sec.permission.PermissionContributor | INV page codes and role-preset grants |
| com.erp.sec.crossmodule.SecUserDirectoryApi | Warehouse-role recipients of notifications |
| com.erp.notif.crossmodule.NotificationDispatchApi | INV_NEGATIVE_STOCK, INV_NEAR_EXPIRY notifications |
| com.erp.report.ReportProvider | report INV_OPENING_STOCK_SHEET |
| com.erp.report.crossmodule.PrintApi | xlsx opening stock sheet (API-INV-046) |

## 2 — ADRs raised by this stage

Each is filed in full under `analysis/decisions/INV/` (Context · Decision · Consequences · traces · Status). Summary:

| ADR | Decision | Status |
|---|---|---|
| ADR-INV-021 | Infrastructure errors are PLATFORM-STD catalog rows: INV-400 (malformed request), INV-401 (unauthenticated), INV-403 (forbidden — message of AC-INV-154), one not-found code per resource (INV-404-WAREHOUSE-NOT-FOUND … INV-404-LOT-NOT-FOUND, §5), INV-409-STALE-VERSION (optimistic lock on VERSION), INV-500 (server). Amended at gate-analysis revise 3 (G1): INV owns and §5 states the ar / en text of every module-specific code — INV-403, each per-resource not-found code (INV-404-WAREHOUSE-NOT-FOUND … INV-404-LOT-NOT-FOUND, §5), INV-404-LOOKUP-KEY-UNKNOWN and INV-422-LOAD-ROWS-INVALID. Amended again at gate-analysis revise 4 (G3): INV states the ar / en text of every code it raises, the four generic codes INV-400, INV-401, INV-500 and INV-409-STALE-VERSION included, because the platform holds no text under an INV-prefixed code; every text is copied from §5 into the bundles `messages.properties` and `messages_ar.properties` (checklist C11). | ACCEPTED (amended) |
| ADR-INV-022 | The consumed unit-of-measure lookup key (ENT-INV-014.measureUomCode) is not hardcoded in INV code; it is read at runtime and the code is validated through MdlLookupApi against it. Revised by ADR-INV-043: the key is the SettingsApi setting INV_MEASURE_UOM_KEY, not a deployment property. | ACCEPTED (revised by ADR-INV-043) |
| ADR-INV-023 | The cross-module foreign keys (INT-C) reference the target's primary key column only — single-column FKs — because the target's contract promises the identifier, not a tenant-scoped unique key; the target table and key column are derived from the shared naming rule and verified by the executor against the delivered target migration before the patch runs. | ACCEPTED |
| ADR-INV-024 | The contract's in-process operations (CON-INV-006 … 019) are one injected bean `InventoryApi` (package com.commerce.inv.crossmodule — platform summary decision 11), with no REST surface; their codes raised only in-process (RULE-INV-020, 021, 023, 039) are listed in §5.1, not in the HTTP catalog; their reservation-only repository intents are stated in SVC-API-INT, outside the QRC (no API-* reaches them). | ACCEPTED |
| ADR-INV-025 | Balance, availability and lot searches are native aggregate queries joining INV_STOCK_BALANCE with INV_LOT and INV_RESERVATION (derived reserved / available, expired exclusion); the Variant "term" filter resolves a barcode through XM-INV-013 and an Item filter through XM-INV-015; display names of Variants are read in-process per page through XM-INV-014. | ACCEPTED |
| ADR-INV-026 | Concurrency: balance and valuation rows are locked `FOR UPDATE` in ascending (variantFk, warehouseFk, lotFk) order after an insert-if-absent on their unique keys; posting paths read the warehouse `FOR SHARE`, deactivation takes it `FOR UPDATE`; default switches first take the tenant's default-switch advisory lock (amended by ADR-INV-050); an issue and a FEFO selection read their lots `FOR SHARE`, an expiry correction locks the lot `FOR UPDATE`; document headers are locked before status checks; numbers come atomically from NumberSeriesApi. Amended at gate-analysis revise 4 (G4): every reservation an issue references is locked `FOR UPDATE` in ascending reservationPk, after the idempotency advisory lock and before the pair's balance rows, so two issues naming the same reservations in opposite line order cannot deadlock. | ACCEPTED (amended at gate-analysis revise 2 and 4) |
| ADR-INV-027 | Opening stock (API-INV-047) creates and posts one OPENING_STOCK adjustment in one call; rows identify the Variant by `variantPk` or by `barcode` (resolved through XM-INV-013); all-or-nothing validation answers 422 INV-422-LOAD-ROWS-INVALID with one `details` entry per failing row (rowNo, code, messageAr, messageEn). A resubmitted identical load is retry-safe: it answers the first adjustment and records nothing (ADR-INV-053; consequence amended at gate-analysis revise 4, G6). | ACCEPTED (amended) |
| ADR-INV-028 | Inventory settings (API-INV-048/049) read and write the given cu settings INV_ALLOW_NEGATIVE_STOCK and INV_NEAR_EXPIRY_DAYS; the 1–365 range of REQ-INV-119 raises INV-422-HORIZON-RANGE (rule REQ-INV-119, no RULE — SRS D-04); the context endpoint (API-INV-008) returns active counts and the three capability flags. | ACCEPTED |
| ADR-INV-029 | Item stock settings are saved by an idempotent PUT keyed by itemId: CREATE permission when no record exists, UPDATE when one does; key = Item code, valueAr / valueEn = a system-filled tracking summary built from the two flags. | ACCEPTED |
| ADR-INV-030 | "Never valued" ≡ no INV_STOCK_VALUATION row. An outgoing move of a never-valued Variant is valued at 0 and creates no valuation row; the first incoming valued move creates it with TOTAL_ON_HAND_QTY = the Variant's on-hand sum after the move and the move's unit cost as average (REQ-INV-060). Extended (gate-analysis revise 1, G11): an ADJUSTMENT_IN of an adjustment generated by a count posting (sourceCountFk set) of a never-valued Variant is valued at 0 and creates no valuation row, so a count can always be posted. | ACCEPTED |
| ADR-INV-031 | Two SOFT-READ edges are minted (XM-INV-014 Variant read, XM-INV-015 Item read): RULE-INV-018 / 028 / 042 / 043 / 045 and the Item filter are the first runtime reads of that state, and the register held only structural edges for it. | ACCEPTED |
| ADR-INV-032 | The seven state-change PATCH endpoints (reactivate and set-default of a warehouse or a branch; post of a transfer, a count or an adjustment) take a typed body `VersionedActionRequest {version}` — the VERSION the caller read. A stale version answers INV-409-STALE-VERSION, so a record changed by another user since it was read (a draft edited, a default moved) is never acted on unseen. | ACCEPTED |
| ADR-INV-041 | Tenancy of native SQL: Hibernate `@TenantId` filters JPQL and derived queries only. Every NATIVE entry (QR-INV-015, 016, 017, 024, 038, 041, the advisory locks of ADR-INV-044 and ADR-INV-050, and any native form of R-1 … R-6) writes `TENANT_ID = :tenantId` on each table and each join, and TENANT_ID in every INSERT column list; `:tenantId` is the given tenant context's current id. Each such repository method carries `@TenantUnscoped(reason = "native SQL — tenant predicate written by hand", adr = "ADR-INV-041")`, and a repository test runs each one under two tenants. | ACCEPTED |
| ADR-INV-042 | Authority of the in-process surface: every REST-facing service method carries `@PreAuthorize(hasAuthority(T(com.commerce.inv.permission.InvPermissions).PERM_…))` with INV's own constants. `InventoryApi` (CON-INV-006 … 019) delegates to the internal engine package `com.commerce.inv.engine` (StockPostingEngine, AvailabilityEngine, ReservationEngine, InventoryReadEngine), which has no permission check and no REST path; the calling module authorises its own caller (SRS access summary). Depends on CORE-GAP-in-process-caller-authority. | ACCEPTED |
| ADR-INV-043 | The consumed unit-of-measure lookup key is the SettingsApi setting INV_MEASURE_UOM_KEY, platform default `CAT_UOM` (the key CAT SRS A6 declares), seeded by db-script BLOCK 8e and not tenant-editable; the deployment property of ADR-INV-022 is withdrawn. A platform finding asks CAT to publish the key in contract-cat. | ACCEPTED |
| ADR-INV-044 | Concurrent requests with one idempotency key: receive, issue and reserve take `pg_advisory_xact_lock(hashtextextended(<tenantId> || ':' || <idempotencyKey>, 0))` before the R-2 / R-6 lookup, so a retried submission waits for the first and then returns its answer. IDEMPOTENCY_KEY stays a non-unique index (D-P2-05). | ACCEPTED |
| ADR-INV-045 | A DRAFT is validated again when posted: engine step 0 re-runs RULE-INV-007, 008, 016, 018, 037 and 040 on every line; a warehouse named by a DRAFT transfer, count or adjustment cannot be deactivated (RULE-INV-004, INV-409-WAREHOUSE-IN-USE text). | ACCEPTED |
| ADR-INV-046 | A refusal raised by a rule on one line of a transfer, count or adjustment (API-INV-022, 023, 024, 029, 030, 032, 036, 037, 038) returns `details` with one entry for the first failing line: `{lineRef, lineRefKind, rowNo?, code, messageAr, messageEn}` — lineRef is the line's identifier, or its 1-based position in the request for an unsaved line; rowNo is the adjustment row number. (gate-analysis revise 2, G2) | ACCEPTED |
| ADR-INV-047 | A system-generated adjustment (count posting, opening stock load) does not check that its reason is tenant-active; the adjustment screen (API-INV-036, 037, 038) refuses OPENING_STOCK with INV-422-REASON-REQUIRED. (G3, G18) | ACCEPTED |
| ADR-INV-048 | The idempotency key is recorded with a SHA-256 hash of its request (REQUEST_HASH, DBF-INV-220, 221); the same key with a different hash answers INV-409-IDEMPOTENCY-KEY-REUSED (RULE-INV-050 — in-process, and on API-INV-047 (ADR-INV-053)). The hash is taken over the request as submitted: absent fields stay absent, and no default, trim or lookup is applied before hashing (gate-analysis revise 4, G5). (G9) | ACCEPTED (amended) |
| ADR-INV-049 | Stock-move immutability is also enforced by the database trigger TRG_INV_STOCK_MOVE_IMMUTABLE and the `@Immutable` entity; a database refusal is translated to INV-409-STOCK-MOVE-IMMUTABLE. (G12, P2 delta) | ACCEPTED |
| ADR-INV-050 | Default switches (API-INV-007, 015) serialise on a tenant advisory lock (`pg_advisory_xact_lock(hashtextextended(:tenantId || ':INV_WAREHOUSE_DEFAULT' | ':INV_BRANCH_DEFAULT', 0))`) taken before the current default is read. (G19) | ACCEPTED |
| ADR-INV-051 | A transfer, count or decrease-adjustment line of a lot-tracked Item that names no lot is accepted up to the Variant's on-hand held without a lot in the line's warehouse and posts against that lot-less row; beyond it, INV-422-LOT-REQUIRED. (gate-analysis revise 3, G2) | ACCEPTED |
| ADR-INV-052 | A synchronised offline sale skips the active checks of RULE-INV-007 and RULE-INV-018, takes the default warehouse when it names none, and notifies the Warehouse role of the inactive record (INV_OFFLINE_SALE_INACTIVE); extends ADR-INV-045. (G3) | ACCEPTED |
| ADR-INV-053 | API-INV-047 requires an idempotencyKey, guarded by the advisory lock and request hash of ADR-INV-044 / ADR-INV-048: the same load answers its first 201, a different one INV-409-IDEMPOTENCY-KEY-REUSED. The load hash is taken over the request as submitted — an absent warehousePk or docDate stays absent, so a retry after midnight or after the default warehouse changes is still the same load (gate-analysis revise 4, G5). (G4) | ACCEPTED (amended) |
| ADR-INV-054 | The system measure of a dual-quantity count line after posting is derived, not stored: countedMeasureQty minus the measureQtyChange of the count's adjustment line for that Variant and lot (countedMeasureQty when no line was created); the measure difference itself is read under the posting's balance lock and posted on the COUNT_DIFFERENCE line. No DBF is added. (gate-analysis revise 4, G8) | ACCEPTED |
| ADR-INV-055 | Releasing a reservation is idempotent: a release of a reservation that is already RELEASED returns it unchanged (status, reservedQty 0 and the first closedAt), and only a CONSUMED reservation is refused with INV-409-RESERVATION-CLOSED. Additive on CON-INV-012 — fewer refusals, no signature change. (gate-analysis revise 5, G4) | ACCEPTED |
| ADR-INV-056 | A receipt or increase line that names a lot whose expiry date is already before today is accepted, and the lot is expired from its creation: it holds stock, shows expiredFl true and is excluded from sale, FEFO and available quantity at once (RULE-INV-038); it leaves stock by an EXPIRY_WRITE_OFF adjustment or as the business decides. No refusal code is added. (gate-analysis revise 5, G8) | ACCEPTED |

Inherited and applied: ADR-INV-014 (TENANT_ID, VERSION), ADR-INV-015 (CURRENCY_CODE), ADR-INV-016 (types), ADR-INV-017 (measure-only move, quantity 0), ADR-INV-018 / ADR-INV-020 (no events subscriber, no server-side file parsing); SRS D-01 … D-27; db-script D-P2-01 … D-P2-14.

## 3 — DB Alignment Manifest

Columns per the engine: plan property and plan type only — the physical column, DB type, nullability and
default are looked up from the db-script by DBF id. The last column is the binding status and, for a
required column, its writer: the `API-*` whose Request or Orchestration line names it, or the stated reason
(system-generated · derived · seeded · DEFERRED · core-provided). Plan types per CORE R1 type mapping.

```
DB ALIGNMENT MANIFEST — INV v1
Legend  ✓ aligned · ✗ type mismatch (finding)   ·   writer = API that writes a required column, or the reason none does
```

| DBF | ENT | Plan property | Plan type | XM | Status · writer |
|---|---|---|---|---|---|
| DBF-INV-001 | ENT-INV-001 | warehousePk | Long | — | ✓ system-generated (identity) |
| DBF-INV-002 | ENT-INV-001 | isDefaultFl | Boolean | — | ✓ API-INV-003, API-INV-007 |
| DBF-INV-003 | ENT-INV-001 | branchFk | Long | — | ✓ API-INV-003, API-INV-004 |
| DBF-INV-004 | ENT-INV-001 | code | String | — | ✓ API-INV-003 (NumberSeriesApi) |
| DBF-INV-005 | ENT-INV-001 | nameAr | String | — | ✓ API-INV-003, API-INV-004 |
| DBF-INV-006 | ENT-INV-001 | nameEn | String | — | ✓ |
| DBF-INV-007 | ENT-INV-001 | isActiveFl | Boolean | — | ✓ API-INV-003, API-INV-005, API-INV-006 |
| DBF-INV-008 | ENT-INV-001 | tenantId | Long | — | ✓ core-provided (AuditableEntity) |
| DBF-INV-009 | ENT-INV-001 | version | Long | — | ✓ core-provided (AuditableEntity) |
| DBF-INV-010 | ENT-INV-001 | createdBy | String | — | ✓ core-provided |
| DBF-INV-011 | ENT-INV-001 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-012 | ENT-INV-001 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-013 | ENT-INV-001 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-014 | ENT-INV-002 | branchPk | Long | — | ✓ system-generated (identity) |
| DBF-INV-015 | ENT-INV-002 | isDefaultFl | Boolean | — | ✓ API-INV-011, API-INV-015 |
| DBF-INV-016 | ENT-INV-002 | code | String | — | ✓ API-INV-011 (NumberSeriesApi) |
| DBF-INV-017 | ENT-INV-002 | nameAr | String | — | ✓ API-INV-011, API-INV-012 |
| DBF-INV-018 | ENT-INV-002 | nameEn | String | — | ✓ |
| DBF-INV-019 | ENT-INV-002 | isActiveFl | Boolean | — | ✓ API-INV-011, API-INV-013, API-INV-014 |
| DBF-INV-020 | ENT-INV-002 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-021 | ENT-INV-002 | version | Long | — | ✓ core-provided |
| DBF-INV-022 | ENT-INV-002 | createdBy | String | — | ✓ core-provided |
| DBF-INV-023 | ENT-INV-002 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-024 | ENT-INV-002 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-025 | ENT-INV-002 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-026 | ENT-INV-003 | stockBalancePk | Long | — | ✓ system-generated (identity) |
| DBF-INV-027 | ENT-INV-003 | onHandQty | BigDecimal | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-028 | ENT-INV-003 | onHandMeasureQty | BigDecimal | — | ✓ |
| DBF-INV-029 | ENT-INV-003 | lastMoveAt | OffsetDateTime | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-030 | ENT-INV-003 | variantFk | Long | XM-INV-001 | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-031 | ENT-INV-003 | warehouseFk | Long | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-032 | ENT-INV-003 | lotFk | Long | — | ✓ |
| DBF-INV-033 | ENT-INV-003 | docDate | LocalDate | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-034 | ENT-INV-003 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-035 | ENT-INV-003 | version | Long | — | ✓ core-provided |
| DBF-INV-036 | ENT-INV-003 | createdBy | String | — | ✓ core-provided |
| DBF-INV-037 | ENT-INV-003 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-038 | ENT-INV-003 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-039 | ENT-INV-003 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-040 | ENT-INV-004 | stockMovePk | Long | — | ✓ system-generated (identity) |
| DBF-INV-041 | ENT-INV-004 | moveTypeCode | String | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-042 | ENT-INV-004 | quantity | BigDecimal | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-043 | ENT-INV-004 | measureQty | BigDecimal | — | ✓ |
| DBF-INV-044 | ENT-INV-004 | unitCost | BigDecimal | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-045 | ENT-INV-004 | moveValue | BigDecimal | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-046 | ENT-INV-004 | balanceAfterQty | BigDecimal | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-047 | ENT-INV-004 | sourceDocTypeCode | String | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-048 | ENT-INV-004 | sourceDocRef | Long | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-049 | ENT-INV-004 | sourceDocNo | String | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-050 | ENT-INV-004 | sourceLineRef | Long | — | ✓ |
| DBF-INV-051 | ENT-INV-004 | offlineFl | Boolean | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-052 | ENT-INV-004 | idempotencyKey | String | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-053 | ENT-INV-004 | variantFk | Long | XM-INV-002 | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-054 | ENT-INV-004 | warehouseFk | Long | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-055 | ENT-INV-004 | lotFk | Long | — | ✓ |
| DBF-INV-056 | ENT-INV-004 | bundleVariantFk | Long | XM-INV-003 | ✓ |
| DBF-INV-057 | ENT-INV-004 | reservationFk | Long | — | ✓ |
| DBF-INV-058 | ENT-INV-004 | docDate | LocalDate | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-059 | ENT-INV-004 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-060 | ENT-INV-004 | version | Long | — | ✓ core-provided |
| DBF-INV-061 | ENT-INV-004 | currencyCode | String | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-062 | ENT-INV-004 | createdBy | String | — | ✓ core-provided |
| DBF-INV-063 | ENT-INV-004 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-064 | ENT-INV-004 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-065 | ENT-INV-004 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-066 | ENT-INV-005 | reservationPk | Long | — | ✓ system-generated (identity) |
| DBF-INV-067 | ENT-INV-005 | originalQty | BigDecimal | — | ✓ system-generated (in-process reserve, ADR-INV-024) |
| DBF-INV-068 | ENT-INV-005 | reservedQty | BigDecimal | — | ✓ system-generated (in-process reserve / release / issue, ADR-INV-024) |
| DBF-INV-069 | ENT-INV-005 | shortfallQty | BigDecimal | — | ✓ system-generated (in-process reserve, ADR-INV-024) |
| DBF-INV-070 | ENT-INV-005 | sourceDocTypeCode | String | — | ✓ system-generated (in-process reserve, ADR-INV-024) |
| DBF-INV-071 | ENT-INV-005 | sourceDocRef | Long | — | ✓ system-generated (in-process reserve, ADR-INV-024) |
| DBF-INV-072 | ENT-INV-005 | sourceDocNo | String | — | ✓ system-generated (in-process reserve, ADR-INV-024) |
| DBF-INV-073 | ENT-INV-005 | sourceLineRef | Long | — | ✓ |
| DBF-INV-074 | ENT-INV-005 | closedAt | OffsetDateTime | — | ✓ |
| DBF-INV-075 | ENT-INV-005 | idempotencyKey | String | — | ✓ system-generated (in-process reserve, ADR-INV-024) |
| DBF-INV-076 | ENT-INV-005 | variantFk | Long | XM-INV-004 | ✓ system-generated (in-process reserve, ADR-INV-024) |
| DBF-INV-077 | ENT-INV-005 | warehouseFk | Long | — | ✓ system-generated (in-process reserve, ADR-INV-024) |
| DBF-INV-078 | ENT-INV-005 | bundleVariantFk | Long | XM-INV-005 | ✓ |
| DBF-INV-079 | ENT-INV-005 | docDate | LocalDate | — | ✓ system-generated (in-process reserve, ADR-INV-024) |
| DBF-INV-080 | ENT-INV-005 | statusCode | String | — | ✓ system-generated (in-process reserve / release / issue, ADR-INV-024) |
| DBF-INV-081 | ENT-INV-005 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-082 | ENT-INV-005 | version | Long | — | ✓ core-provided |
| DBF-INV-083 | ENT-INV-005 | createdBy | String | — | ✓ core-provided |
| DBF-INV-084 | ENT-INV-005 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-085 | ENT-INV-005 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-086 | ENT-INV-005 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-087 | ENT-INV-006 | transferPk | Long | — | ✓ system-generated (identity) |
| DBF-INV-088 | ENT-INV-006 | noteText | String | — | ✓ |
| DBF-INV-089 | ENT-INV-006 | postedAt | OffsetDateTime | — | ✓ |
| DBF-INV-090 | ENT-INV-006 | fromWarehouseFk | Long | — | ✓ API-INV-022, API-INV-023 |
| DBF-INV-091 | ENT-INV-006 | toWarehouseFk | Long | — | ✓ API-INV-022, API-INV-023 |
| DBF-INV-092 | ENT-INV-006 | docNo | String | — | ✓ API-INV-022 (NumberSeriesApi) |
| DBF-INV-093 | ENT-INV-006 | docDate | LocalDate | — | ✓ API-INV-022, API-INV-023 |
| DBF-INV-094 | ENT-INV-006 | statusCode | String | — | ✓ API-INV-022, API-INV-024, API-INV-025 |
| DBF-INV-095 | ENT-INV-006 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-096 | ENT-INV-006 | version | Long | — | ✓ core-provided |
| DBF-INV-097 | ENT-INV-006 | createdBy | String | — | ✓ core-provided |
| DBF-INV-098 | ENT-INV-006 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-099 | ENT-INV-006 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-100 | ENT-INV-006 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-101 | ENT-INV-007 | transferLinePk | Long | — | ✓ system-generated (identity) |
| DBF-INV-102 | ENT-INV-007 | quantity | BigDecimal | — | ✓ API-INV-022, API-INV-023 |
| DBF-INV-103 | ENT-INV-007 | measureQty | BigDecimal | — | ✓ |
| DBF-INV-104 | ENT-INV-007 | unitCost | BigDecimal | — | ✓ |
| DBF-INV-105 | ENT-INV-007 | transferFk | Long | — | ✓ API-INV-022, API-INV-023 |
| DBF-INV-106 | ENT-INV-007 | variantFk | Long | XM-INV-006 | ✓ API-INV-022, API-INV-023 |
| DBF-INV-107 | ENT-INV-007 | lotFk | Long | — | ✓ |
| DBF-INV-108 | ENT-INV-007 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-109 | ENT-INV-007 | version | Long | — | ✓ core-provided |
| DBF-INV-110 | ENT-INV-007 | currencyCode | String | — | ✓ API-INV-022, API-INV-023 |
| DBF-INV-111 | ENT-INV-007 | createdBy | String | — | ✓ core-provided |
| DBF-INV-112 | ENT-INV-007 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-113 | ENT-INV-007 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-114 | ENT-INV-007 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-115 | ENT-INV-008 | stockCountPk | Long | — | ✓ system-generated (identity) |
| DBF-INV-116 | ENT-INV-008 | noteText | String | — | ✓ |
| DBF-INV-117 | ENT-INV-008 | postedAt | OffsetDateTime | — | ✓ |
| DBF-INV-118 | ENT-INV-008 | warehouseFk | Long | — | ✓ API-INV-028 |
| DBF-INV-119 | ENT-INV-008 | adjustmentFk | Long | — | ✓ |
| DBF-INV-120 | ENT-INV-008 | docNo | String | — | ✓ API-INV-028 (NumberSeriesApi) |
| DBF-INV-121 | ENT-INV-008 | docDate | LocalDate | — | ✓ API-INV-028 |
| DBF-INV-122 | ENT-INV-008 | statusCode | String | — | ✓ API-INV-028, API-INV-032, API-INV-033 |
| DBF-INV-123 | ENT-INV-008 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-124 | ENT-INV-008 | version | Long | — | ✓ core-provided |
| DBF-INV-125 | ENT-INV-008 | createdBy | String | — | ✓ core-provided |
| DBF-INV-126 | ENT-INV-008 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-127 | ENT-INV-008 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-128 | ENT-INV-008 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-129 | ENT-INV-009 | stockCountLinePk | Long | — | ✓ system-generated (identity) |
| DBF-INV-130 | ENT-INV-009 | countedQty | BigDecimal | — | ✓ API-INV-029, API-INV-030 |
| DBF-INV-131 | ENT-INV-009 | countedMeasureQty | BigDecimal | — | ✓ |
| DBF-INV-132 | ENT-INV-009 | systemQty | BigDecimal | — | ✓ |
| DBF-INV-133 | ENT-INV-009 | differenceQty | BigDecimal | — | ✓ |
| DBF-INV-134 | ENT-INV-009 | stockCountFk | Long | — | ✓ API-INV-029 |
| DBF-INV-135 | ENT-INV-009 | variantFk | Long | XM-INV-007 | ✓ API-INV-029 |
| DBF-INV-136 | ENT-INV-009 | lotFk | Long | — | ✓ |
| DBF-INV-137 | ENT-INV-009 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-138 | ENT-INV-009 | version | Long | — | ✓ core-provided |
| DBF-INV-139 | ENT-INV-009 | createdBy | String | — | ✓ core-provided |
| DBF-INV-140 | ENT-INV-009 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-141 | ENT-INV-009 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-142 | ENT-INV-009 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-143 | ENT-INV-010 | stockAdjustmentPk | Long | — | ✓ system-generated (identity) |
| DBF-INV-144 | ENT-INV-010 | reasonCode | String | — | ✓ API-INV-036, API-INV-037, API-INV-047 |
| DBF-INV-145 | ENT-INV-010 | noteText | String | — | ✓ |
| DBF-INV-146 | ENT-INV-010 | postedAt | OffsetDateTime | — | ✓ |
| DBF-INV-147 | ENT-INV-010 | warehouseFk | Long | — | ✓ API-INV-036, API-INV-047 |
| DBF-INV-148 | ENT-INV-010 | sourceCountFk | Long | — | ✓ |
| DBF-INV-149 | ENT-INV-010 | docNo | String | — | ✓ API-INV-036, API-INV-047 (NumberSeriesApi) |
| DBF-INV-150 | ENT-INV-010 | docDate | LocalDate | — | ✓ API-INV-036, API-INV-047 |
| DBF-INV-151 | ENT-INV-010 | statusCode | String | — | ✓ API-INV-036, API-INV-038, API-INV-039 |
| DBF-INV-152 | ENT-INV-010 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-153 | ENT-INV-010 | version | Long | — | ✓ core-provided |
| DBF-INV-154 | ENT-INV-010 | createdBy | String | — | ✓ core-provided |
| DBF-INV-155 | ENT-INV-010 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-156 | ENT-INV-010 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-157 | ENT-INV-010 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-158 | ENT-INV-011 | stockAdjustmentLinePk | Long | — | ✓ system-generated (identity) |
| DBF-INV-159 | ENT-INV-011 | rowNo | Integer | — | ✓ API-INV-036, API-INV-037, API-INV-047 |
| DBF-INV-160 | ENT-INV-011 | lotNo | String | — | ✓ |
| DBF-INV-161 | ENT-INV-011 | lotExpiryDate | LocalDate | — | ✓ |
| DBF-INV-162 | ENT-INV-011 | quantityChange | BigDecimal | — | ✓ API-INV-036, API-INV-037, API-INV-047 |
| DBF-INV-163 | ENT-INV-011 | measureQtyChange | BigDecimal | — | ✓ |
| DBF-INV-164 | ENT-INV-011 | unitCost | BigDecimal | — | ✓ |
| DBF-INV-165 | ENT-INV-011 | lineValue | BigDecimal | — | ✓ |
| DBF-INV-166 | ENT-INV-011 | stockAdjustmentFk | Long | — | ✓ API-INV-036, API-INV-037, API-INV-047 |
| DBF-INV-167 | ENT-INV-011 | variantFk | Long | XM-INV-008 | ✓ API-INV-036, API-INV-037, API-INV-047 |
| DBF-INV-168 | ENT-INV-011 | lotFk | Long | — | ✓ |
| DBF-INV-169 | ENT-INV-011 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-170 | ENT-INV-011 | version | Long | — | ✓ core-provided |
| DBF-INV-171 | ENT-INV-011 | currencyCode | String | — | ✓ API-INV-036, API-INV-037, API-INV-047 |
| DBF-INV-172 | ENT-INV-011 | createdBy | String | — | ✓ core-provided |
| DBF-INV-173 | ENT-INV-011 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-174 | ENT-INV-011 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-175 | ENT-INV-011 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-176 | ENT-INV-012 | lotPk | Long | — | ✓ system-generated (identity) |
| DBF-INV-177 | ENT-INV-012 | expiryDate | LocalDate | — | ✓ API-INV-038, API-INV-042, API-INV-047 |
| DBF-INV-178 | ENT-INV-012 | firstReceivedAt | OffsetDateTime | — | ✓ API-INV-038, API-INV-047 |
| DBF-INV-179 | ENT-INV-012 | nearExpiryNotifiedAt | OffsetDateTime | — | ✓ |
| DBF-INV-180 | ENT-INV-012 | variantFk | Long | XM-INV-009 | ✓ API-INV-038, API-INV-047 |
| DBF-INV-181 | ENT-INV-012 | code | String | — | ✓ API-INV-038, API-INV-047 |
| DBF-INV-182 | ENT-INV-012 | nameAr | String | — | ✓ |
| DBF-INV-183 | ENT-INV-012 | nameEn | String | — | ✓ |
| DBF-INV-184 | ENT-INV-012 | isActiveFl | Boolean | — | ✓ API-INV-038, API-INV-047 |
| DBF-INV-185 | ENT-INV-012 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-186 | ENT-INV-012 | version | Long | — | ✓ core-provided |
| DBF-INV-187 | ENT-INV-012 | createdBy | String | — | ✓ core-provided |
| DBF-INV-188 | ENT-INV-012 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-189 | ENT-INV-012 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-190 | ENT-INV-012 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-191 | ENT-INV-013 | stockValuationPk | Long | — | ✓ system-generated (identity) |
| DBF-INV-192 | ENT-INV-013 | totalOnHandQty | BigDecimal | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-193 | ENT-INV-013 | averageUnitCost | BigDecimal | — | ✓ API-INV-038, API-INV-047 |
| DBF-INV-194 | ENT-INV-013 | stockValue | BigDecimal | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-195 | ENT-INV-013 | lastMoveAt | OffsetDateTime | — | ✓ API-INV-024, API-INV-038, API-INV-047 |
| DBF-INV-196 | ENT-INV-013 | variantFk | Long | XM-INV-010 | ✓ API-INV-038, API-INV-047 |
| DBF-INV-197 | ENT-INV-013 | docDate | LocalDate | — | ✓ API-INV-038, API-INV-047 |
| DBF-INV-198 | ENT-INV-013 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-199 | ENT-INV-013 | version | Long | — | ✓ core-provided |
| DBF-INV-200 | ENT-INV-013 | currencyCode | String | — | ✓ API-INV-038, API-INV-047 |
| DBF-INV-201 | ENT-INV-013 | createdBy | String | — | ✓ core-provided |
| DBF-INV-202 | ENT-INV-013 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-203 | ENT-INV-013 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-204 | ENT-INV-013 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-205 | ENT-INV-014 | itemStockSettingsPk | Long | — | ✓ system-generated (identity) |
| DBF-INV-206 | ENT-INV-014 | lotTrackedFl | Boolean | — | ✓ API-INV-045 |
| DBF-INV-207 | ENT-INV-014 | dualQuantityFl | Boolean | — | ✓ API-INV-045 |
| DBF-INV-208 | ENT-INV-014 | measureUomCode | String | — | ✓ |
| DBF-INV-209 | ENT-INV-014 | itemFk | Long | XM-INV-011 | ✓ API-INV-045 |
| DBF-INV-210 | ENT-INV-014 | key | String | — | ✓ API-INV-045 |
| DBF-INV-211 | ENT-INV-014 | valueAr | String | — | ✓ API-INV-045 |
| DBF-INV-212 | ENT-INV-014 | valueEn | String | — | ✓ API-INV-045 |
| DBF-INV-213 | ENT-INV-014 | isActiveFl | Boolean | — | ✓ API-INV-045 |
| DBF-INV-214 | ENT-INV-014 | tenantId | Long | — | ✓ core-provided |
| DBF-INV-215 | ENT-INV-014 | version | Long | — | ✓ core-provided |
| DBF-INV-216 | ENT-INV-014 | createdBy | String | — | ✓ core-provided |
| DBF-INV-217 | ENT-INV-014 | createdAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-218 | ENT-INV-014 | updatedBy | String | — | ✓ core-provided |
| DBF-INV-219 | ENT-INV-014 | updatedAt | OffsetDateTime | — | ✓ core-provided |
| DBF-INV-220 | ENT-INV-004 | requestHash | String | — | ✓ API-INV-024, API-INV-038, API-INV-047 (and the in-process receive / issue — ADR-INV-048) |
| DBF-INV-221 | ENT-INV-005 | requestHash | String | — | ✓ system-generated (in-process reserve, ADR-INV-024, ADR-INV-048) |

Derived, not held (no DBF): `reservedQty`, `availableQty` of a balance row (REQ-INV-038 — QR-INV-015/016), the warehouse label `warehouseCode` / `warehouseNameAr` / `warehouseNameEn` of a balance row and of an availability row (read by join from DBF-INV-004, 005, 006 — QR-INV-015/016, ADR-INV-036), `expiredFl` and `daysLeft` of a lot (RULE-INV-038, today in the tenant time zone), the bundle availability (REQ-INV-042), and the `systemMeasureQty` of a posted dual-quantity count line (DBF-INV-131 minus DBF-INV-163 of the count's adjustment line for that Variant and lot, read with QR-INV-036 — ADR-INV-054). No ADR is needed for the others: the SRS A3 declares them derived; the system measure is ADR-INV-054 (SRS D-15).

Labels, not held (CORE "Labels on every row", gate-analysis revise 3 G6): `lotCode` of a transfer, count or adjustment
line and of a stock move (DBF-INV-181 of the row's lot, read once per page through QR-INV-039 batched by identifier),
`adjustmentDocNo` of a stock count (DBF-INV-149 of its DBF-INV-119, QR-INV-036) and `sourceCountDocNo` of a stock
adjustment (DBF-INV-120 of its DBF-INV-148, QR-INV-031).

DB ALIGNMENT: 221 / 221 ✓ · issues: 0.

## 4 — Query Reference Catalog (QR)

Logical intent only — the implementer rewrites each entry with the mapped entity classes and the project's
query strategy (Spring Data JPA; native SQL only where the entry says NATIVE). Tenancy (ADR-INV-041):
- **JPQL and derived queries** (every entry not marked NATIVE) are tenant-scoped by the given
  (`AuditableEntity` / Hibernate `@TenantId`); their `TENANT_ID` predicate is added by Hibernate and is not
  repeated in their logical spec.
- **NATIVE entries** (QR-INV-015, 016, 017, 024, 038, 041) are not filtered by `@TenantId`. Their logical spec
  writes `TENANT_ID = :tenantId` on every table and every join predicate, and TENANT_ID in every INSERT column
  list; `:tenantId` is the current tenant of the given tenant context. Each such repository method carries
  `@TenantUnscoped(reason = "native SQL — tenant predicate written by hand", adr = "ADR-INV-041")`. The same
  holds for the advisory locks of ADR-INV-044 (idempotency key) and ADR-INV-050 (default switches) and for any native
  form of the in-process intents R-1 … R-6.

Ordering / paging: `Page<T>`; responses wrapped in `ApiResponse<T>`. Locking modes
follow ADR-INV-026. Every entry names the `API-*` that reaches it; every `API-*` with a DB operation cites its
entries on its Repository line.

```
QR-INV-001 — Warehouse search
Phase · API · Entity · Op : SVC-API · API-INV-001 · ENT-INV-001 · FIND_BY_CRITERIA
Intent        : the page of warehouses matching the search screen filters, ordered by code (REQ-INV-009)
Logical spec  : SELECT * FROM INV_WAREHOUSE WHERE [CODE ILIKE %:code%] [AND (NAME_AR ILIKE %:name% OR NAME_EN ILIKE %:name%)] [AND BRANCH_FK = :branchPk] [AND IS_ACTIVE_FL = TRUE when active = ACTIVE_ONLY] ORDER BY CODE [page/size]
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · YES (Page<T>)
Filters · Result · Nulls : code LIKE, name LIKE, branchPk EXACT, active SET · full entity · NAME_EN null → display NAME_AR

QR-INV-002 — Warehouse by identifier
Phase · API · Entity · Op : SVC-API · API-INV-002, 004, 005, 006, 007, 017, 022, 023, 024, 028, 032, 036, 038, 047 · ENT-INV-001 · FIND_ONE
Intent        : one warehouse by WAREHOUSE_PK, inactive included (RULE-INV-005); used as the active check of RULE-INV-007
Logical spec  : SELECT * FROM INV_WAREHOUSE WHERE WAREHOUSE_PK = :id
Join · Tx · Locking · Paging : NONE · READ_ONLY (reads) / READ_WRITE (in write paths) · FOR UPDATE in API-INV-004/005/006/007; FOR SHARE in posting paths (API-INV-024, 032, 038, 047) and in DRAFT saves (API-INV-022, 023, 028, 036) so a concurrent deactivation waits (ADR-INV-026, ADR-INV-045) · NO
Filters · Result · Nulls : PK EXACT · full entity · not found → INV-404-WAREHOUSE-NOT-FOUND

QR-INV-003 — Warehouse Arabic name taken
Phase · API · Entity · Op : SVC-API · API-INV-003, 004 · ENT-INV-001 · EXISTS
Intent        : another warehouse of the tenant already has this NAME_AR (RULE-INV-002)
Logical spec  : SELECT 1 FROM INV_WAREHOUSE WHERE NAME_AR = :nameAr [AND WAREHOUSE_PK <> :id on update]
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE — the race is closed by UQ_INV_WAREHOUSE_NAME_AR (violation → INV-409-WAREHOUSE-NAME-TAKEN) · NO
Filters · Result · Nulls : nameAr EXACT (after trim) · boolean · —

QR-INV-004 — Warehouse save
Phase · API · Entity · Op : SVC-API · API-INV-003, 004, 005, 006, 007 · ENT-INV-001 · SAVE / UPDATE
Intent        : insert a new warehouse or update name / branch / active / default flags; provisioning inserts the default warehouse through the same path
Logical spec  : INSERT INTO INV_WAREHOUSE (IS_DEFAULT_FL, BRANCH_FK, CODE, NAME_AR, NAME_EN, IS_ACTIVE_FL, …audit) VALUES (…) — PK by GENERATED ALWAYS AS IDENTITY · UPDATE INV_WAREHOUSE SET … WHERE WAREHOUSE_PK = :id AND VERSION = :version
Join · Tx · Locking · Paging : NONE · READ_WRITE · optimistic VERSION (stale → INV-409-STALE-VERSION) · NO
Filters · Result · Nulls : — · full entity · NAME_EN nullable

QR-INV-005 — Current default warehouse
Phase · API · Entity · Op : SVC-API · API-INV-007, 028, 036, 047 · ENT-INV-001 · FIND_ONE
Intent        : the tenant's default warehouse — the row to clear on a default switch (RULE-INV-006) and the warehouse applied while one is active (RULE-INV-008)
Logical spec  : SELECT * FROM INV_WAREHOUSE WHERE IS_DEFAULT_FL = TRUE
Join · Tx · Locking · Paging : NONE · READ_WRITE · FOR UPDATE in API-INV-007, read only after the tenant's default-switch advisory lock `SELECT pg_advisory_xact_lock(hashtextextended(:tenantId || ':INV_WAREHOUSE_DEFAULT', 0))` (native, tenant in the hash — ADR-INV-050, ADR-INV-041). Two switches serialise on that lock, which does not move, so the second reads the default the first committed; locking the default row alone would let the second find no row once the first cleared it. UQ_INV_WAREHOUSE_IS_DEFAULT_FL backs it · NO
Filters · Result · Nulls : — · full entity · none → provisioning defect, INV-500 (never a race: the advisory lock guarantees one committed default is read)

QR-INV-006 — Active warehouse count
Phase · API · Entity · Op : SVC-API · API-INV-008, 022, 023, 024, 028, 036, 047 · ENT-INV-001 · COUNT
Intent        : number of active warehouses — selector visibility (REQ-INV-010 … 015) and the default / required rule (RULE-INV-008, REQ-INV-014)
Logical spec  : SELECT COUNT(*) FROM INV_WAREHOUSE WHERE IS_ACTIVE_FL = TRUE
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · NO
Filters · Result · Nulls : — · count · —

QR-INV-007 — Warehouse in use
Phase · API · Entity · Op : SVC-API · API-INV-005 · ENT-INV-001 · EXISTS
Intent        : the warehouse holds non-zero stock, an ACTIVE reservation or a DRAFT transfer, count or adjustment (RULE-INV-004, ADR-INV-045)
Logical spec  : five single-table EXISTS probes, stopping at the first hit — INV_STOCK_BALANCE WHERE WAREHOUSE_FK = :id AND ON_HAND_QTY <> 0 · INV_RESERVATION WHERE WAREHOUSE_FK = :id AND STATUS_CODE = 'ACTIVE' · INV_TRANSFER WHERE (FROM_WAREHOUSE_FK = :id OR TO_WAREHOUSE_FK = :id) AND STATUS_CODE = 'DRAFT' · INV_STOCK_COUNT WHERE WAREHOUSE_FK = :id AND STATUS_CODE = 'DRAFT' · INV_STOCK_ADJUSTMENT WHERE WAREHOUSE_FK = :id AND STATUS_CODE = 'DRAFT'
Join · Tx · Locking · Paging : NONE (single-table JPQL probes) · READ_WRITE (runs after QR-INV-002 FOR UPDATE) · warehouse row locked first, so in-flight posts (FOR SHARE) finish before the probe; a DRAFT saved against the warehouse reads it with QR-INV-002 FOR SHARE (API-INV-022, 023, 028, 036), so it cannot slip in after the probe · NO
Filters · Result · Nulls : — · boolean · —

QR-INV-008 — Branch search
Phase · API · Entity · Op : SVC-API · API-INV-009 · ENT-INV-002 · FIND_BY_CRITERIA
Intent        : the page of branches matching code, name and active state, ordered by code (REQ-INV-028)
Logical spec  : SELECT * FROM INV_BRANCH WHERE [CODE ILIKE %:code%] [AND (NAME_AR ILIKE %:name% OR NAME_EN ILIKE %:name%)] [AND IS_ACTIVE_FL = TRUE when active = ACTIVE_ONLY] ORDER BY CODE [page/size]
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · YES (Page<T>)
Filters · Result · Nulls : code LIKE, name LIKE, active SET · full entity · NAME_EN null → display NAME_AR

QR-INV-009 — Branch by identifier
Phase · API · Entity · Op : SVC-API · API-INV-003, 004, 006, 010, 012, 013, 014, 015 · ENT-INV-002 · FIND_ONE
Intent        : one branch by BRANCH_PK, inactive included (RULE-INV-014); the active check of RULE-INV-009
Logical spec  : SELECT * FROM INV_BRANCH WHERE BRANCH_PK = :id
Join · Tx · Locking · Paging : NONE · READ_ONLY / READ_WRITE · FOR UPDATE in API-INV-012/013/014/015; FOR SHARE when a warehouse is saved against it (API-INV-003/004/006) so a concurrent branch deactivation waits · NO
Filters · Result · Nulls : PK EXACT · full entity · not found → INV-404-BRANCH-NOT-FOUND

QR-INV-010 — Branch Arabic name taken
Phase · API · Entity · Op : SVC-API · API-INV-011, 012 · ENT-INV-002 · EXISTS
Intent        : another branch has this NAME_AR (RULE-INV-011)
Logical spec  : SELECT 1 FROM INV_BRANCH WHERE NAME_AR = :nameAr [AND BRANCH_PK <> :id on update]
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE — race closed by UQ_INV_BRANCH_NAME_AR (→ INV-409-BRANCH-NAME-TAKEN) · NO
Filters · Result · Nulls : nameAr EXACT · boolean · —

QR-INV-011 — Branch save
Phase · API · Entity · Op : SVC-API · API-INV-011, 012, 013, 014, 015 · ENT-INV-002 · SAVE / UPDATE
Intent        : insert a branch or update names / active / default; provisioning inserts the default branch through the same path
Logical spec  : INSERT INTO INV_BRANCH (IS_DEFAULT_FL, CODE, NAME_AR, NAME_EN, IS_ACTIVE_FL, …audit) VALUES (…) · UPDATE INV_BRANCH SET … WHERE BRANCH_PK = :id AND VERSION = :version
Join · Tx · Locking · Paging : NONE · READ_WRITE · optimistic VERSION · NO
Filters · Result · Nulls : — · full entity · NAME_EN nullable

QR-INV-012 — Current default branch
Phase · API · Entity · Op : SVC-API · API-INV-003, 015 · ENT-INV-002 · FIND_ONE
Intent        : the default branch — assigned to a warehouse saved without one (RULE-INV-009, REQ-INV-031) and cleared on a default switch (RULE-INV-015)
Logical spec  : SELECT * FROM INV_BRANCH WHERE IS_DEFAULT_FL = TRUE
Join · Tx · Locking · Paging : NONE · READ_WRITE · FOR UPDATE in API-INV-015, read only after the tenant's default-switch advisory lock `SELECT pg_advisory_xact_lock(hashtextextended(:tenantId || ':INV_BRANCH_DEFAULT', 0))` (native — ADR-INV-050, ADR-INV-041), so two switches serialise and the second reads the default the first committed; UQ_INV_BRANCH_IS_DEFAULT_FL backs it · NO
Filters · Result · Nulls : — · full entity · —

QR-INV-013 — Active branch count
Phase · API · Entity · Op : SVC-API · API-INV-008 · ENT-INV-002 · COUNT
Intent        : number of active branches — branch selector visibility (REQ-INV-029, 030, 032)
Logical spec  : SELECT COUNT(*) FROM INV_BRANCH WHERE IS_ACTIVE_FL = TRUE
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · NO
Filters · Result · Nulls : — · count · —

QR-INV-014 — Branch in use by an active warehouse
Phase · API · Entity · Op : SVC-API · API-INV-013 · ENT-INV-002 · EXISTS
Intent        : an active warehouse belongs to the branch (RULE-INV-013)
Logical spec  : SELECT 1 FROM INV_WAREHOUSE WHERE BRANCH_FK = :id AND IS_ACTIVE_FL = TRUE LIMIT 1
Join · Tx · Locking · Paging : NONE · READ_WRITE (after QR-INV-009 FOR UPDATE) · branch row locked first; warehouse saves read it FOR SHARE · NO
Filters · Result · Nulls : — · boolean · —

QR-INV-015 — Stock balance search with reserved and available
Phase · API · Entity · Op : SVC-API · API-INV-016 · ENT-INV-003 · NATIVE (AGGREGATE)
Intent        : per Variant × warehouse × lot: on-hand, measure, reserved (ACTIVE reservations of the Variant × warehouse), available = on-hand outside expired lots − reserved (REQ-INV-038, RULE-INV-038), lot code / expiry / expired mark, and the warehouse code and names (ADR-INV-036)
Logical spec  : SELECT b.*, w.CODE, w.NAME_AR, w.NAME_EN, l.CODE, l.EXPIRY_DATE, (l.EXPIRY_DATE < :today) AS expired, r.reserved FROM INV_STOCK_BALANCE b JOIN INV_WAREHOUSE w ON w.TENANT_ID = :tenantId AND w.WAREHOUSE_PK = b.WAREHOUSE_FK LEFT JOIN INV_LOT l ON l.TENANT_ID = :tenantId AND l.LOT_PK = b.LOT_FK LEFT JOIN (SELECT VARIANT_FK, WAREHOUSE_FK, SUM(RESERVED_QTY) reserved FROM INV_RESERVATION WHERE TENANT_ID = :tenantId AND STATUS_CODE = 'ACTIVE' GROUP BY 1,2) r ON r.VARIANT_FK = b.VARIANT_FK AND r.WAREHOUSE_FK = b.WAREHOUSE_FK WHERE b.TENANT_ID = :tenantId [AND b.VARIANT_FK IN (:variantPks)] [AND b.WAREHOUSE_FK = :warehousePk] [AND b.LOT_FK = :lotPk] [AND b.ON_HAND_QTY <> 0 when nonZero] ORDER BY b.VARIANT_FK, b.WAREHOUSE_FK, l.EXPIRY_DATE NULLS FIRST [page/size]; available per Variant × warehouse = SUM(on-hand of non-expired rows) − reserved, reported on each row of that pair
Join · Tx · Locking · Paging : required — ADR-INV-025 (lot expiry, reservations and warehouse labels are separate tables); tenant predicate on every table and join — ADR-INV-041 (`@TenantUnscoped`) · READ_ONLY · NONE · YES (Page<T>)
Filters · Result · Nulls : variantPks SET (from variantPk, barcode term via integrate (XM-INV-013), itemPk via integrate (XM-INV-015)), warehousePk EXACT, lotPk EXACT, nonZero · projection StockBalanceRow (with warehouseCode, warehouseNameAr, warehouseNameEn) · LOT_FK null → stock without a lot, never expired; reserved null → 0; NAME_EN null → display NAME_AR

QR-INV-016 — Availability of a Variant per warehouse
Phase · API · Entity · Op : SVC-API · API-INV-016, 017 · ENT-INV-003 · NATIVE (AGGREGATE)
Intent        : on-hand, reserved and available per ACTIVE warehouse and in total for one Variant (REQ-INV-040); zeros for a Variant with no balance (REQ-INV-041); reused per component for a bundle (REQ-INV-042); the reservation and in-process issue paths read the same figures
Logical spec  : SELECT w.WAREHOUSE_PK, w.CODE, w.NAME_AR, w.NAME_EN, COALESCE(SUM(b.ON_HAND_QTY),0) on_hand, COALESCE(SUM(b.ON_HAND_QTY) FILTER (WHERE l.EXPIRY_DATE IS NULL OR l.EXPIRY_DATE >= :today),0) sellable, COALESCE(SUM(b.ON_HAND_MEASURE_QTY),0) measure FROM INV_WAREHOUSE w LEFT JOIN INV_STOCK_BALANCE b ON b.TENANT_ID = :tenantId AND b.WAREHOUSE_FK = w.WAREHOUSE_PK AND b.VARIANT_FK = :variantPk LEFT JOIN INV_LOT l ON l.TENANT_ID = :tenantId AND l.LOT_PK = b.LOT_FK WHERE w.TENANT_ID = :tenantId AND w.IS_ACTIVE_FL = TRUE [AND w.WAREHOUSE_PK = :warehousePk] GROUP BY w.WAREHOUSE_PK, w.CODE, w.NAME_AR, w.NAME_EN; reserved from QR-INV-026; available = sellable − reserved
Join · Tx · Locking · Paging : required — ADR-INV-025; tenant predicate on every table and join — ADR-INV-041 (`@TenantUnscoped`) · READ_ONLY · NONE (the reserve path re-reads under its own locks, SVC-API-INT) · NO
Filters · Result · Nulls : variantPk EXACT, warehousePk EXACT · projection Availability (perWarehouse rows with warehouseCode, warehouseNameAr, warehouseNameEn) · no rows → zeros

QR-INV-017 — Stock balance lock-or-create
Phase · API · Entity · Op : SVC-API · API-INV-024, 032, 038, 047 · ENT-INV-003 · NATIVE (insert-if-absent) + FIND_ONE
Intent        : obtain the one balance row of (Variant, warehouse, lot) locked for the move about to change it (REQ-INV-036, REQ-INV-037)
Logical spec  : INSERT INTO INV_STOCK_BALANCE (TENANT_ID, VARIANT_FK, WAREHOUSE_FK, LOT_FK, ON_HAND_QTY, LAST_MOVE_AT, DOC_DATE, VERSION, …audit) VALUES (:tenantId, :v, :w, :l, 0, now, :moveDate, 0, …) ON CONFLICT ON CONSTRAINT UQ_INV_STOCK_BALANCE_VAR_WH_LOT DO NOTHING; then SELECT * FROM INV_STOCK_BALANCE WHERE TENANT_ID = :tenantId AND VARIANT_FK = :v AND WAREHOUSE_FK = :w AND LOT_FK IS NOT DISTINCT FROM :l FOR UPDATE
Join · Tx · Locking · Paging : NONE; tenant predicate and TENANT_ID column written by hand — ADR-INV-041 (`@TenantUnscoped`) · READ_WRITE · FOR UPDATE. Lock order across the whole document (ADR-INV-026): first the lot-less row (variant, warehouse, NULL) of every Variant × warehouse pair the document touches, created when absent, in ascending (VARIANT_FK, WAREHOUSE_FK); then the lot rows in ascending (VARIANT_FK, WAREHOUSE_FK, LOT_FK). The lot-less row is the serialisation point of every reserve, issue, transfer leg and adjustment of the pair, with or without a lot — two parallel moves of the same pair serialise, neither update is lost · NO
Filters · Result · Nulls : key EXACT · full entity · LOT_FK null is one key value (NULLS NOT DISTINCT)

QR-INV-018 — Stock balance update
Phase · API · Entity · Op : SVC-API · API-INV-024, 032, 038, 047 · ENT-INV-003 · UPDATE
Intent        : apply a move to its balance in the move's transaction (REQ-INV-036)
Logical spec  : UPDATE INV_STOCK_BALANCE SET ON_HAND_QTY = ON_HAND_QTY ± :qty, ON_HAND_MEASURE_QTY = COALESCE(ON_HAND_MEASURE_QTY,0) ± :measure (dual-quantity only), LAST_MOVE_AT = :now WHERE STOCK_BALANCE_PK = :id
Join · Tx · Locking · Paging : NONE · READ_WRITE · row already locked by QR-INV-017 · NO
Filters · Result · Nulls : — · row count · ON_HAND_MEASURE_QTY stays null for Items that are not dual-quantity

QR-INV-019 — On-hand sum
Phase · API · Entity · Op : SVC-API · API-INV-022, 023, 024, 029, 032, 036, 037, 038, 047 · ENT-INV-003 · AGGREGATE
Intent        : on-hand of a Variant in a warehouse over all lots (BALANCE_AFTER_QTY, REQ-INV-076; the RULE-INV-019 check; the negative-crossing alert REQ-INV-068), of a Variant over all warehouses (valuation creation, ADR-INV-030) and of a lot over all warehouses (the lot active flag, recomputed after commit in its own short transaction — engine step 7), and the on-hand of a Variant in a warehouse held without a lot (`LOT_FK IS NULL`) that bounds a lot-less line of a lot-tracked Item on save (API-INV-022, 023, 029, 036, 037) and again on post (REQ-INV-139, ADR-INV-051)
Logical spec  : SELECT COALESCE(SUM(ON_HAND_QTY),0) FROM INV_STOCK_BALANCE WHERE VARIANT_FK = :v [AND WAREHOUSE_FK = :w] <lot mode>, where <lot mode> is exactly one of three — (a) **all lots**: no lot predicate; (b) **one lot**: `AND LOT_FK IS NOT DISTINCT FROM :l` with :l a lot identifier; (c) **lot-less**: `AND LOT_FK IS NULL`, no parameter. A nullable parameter is never compared with `=` (a null :l would match no row and read 0 — gate-analysis revise 5, G9). Mode per caller: all lots — BALANCE_AFTER_QTY (engine step 5), the Variant × warehouse part of RULE-INV-019 (engine step 2), the negative-crossing alert (step 8), valuation creation over all warehouses (step 6); one lot — the lot active flag over all warehouses (step 7, no warehouse predicate); lot-less — the bound of a line without a lot of a lot-tracked Item on save in API-INV-022, 023 (source warehouse), 029 (the count's warehouse), 036, 037 (the adjustment's warehouse), and on post in engine step 0 (RULE-INV-037), which takes the same lot-less mode inside the posting transaction after the pair's lot-less row is locked by QR-INV-017 (REQ-INV-139, ADR-INV-051)
Join · Tx · Locking · Paging : NONE · READ_WRITE (inside the posting transaction) · the pair's lot-less row and the touched lot rows are locked by QR-INV-017; untouched lots of the pair cannot change concurrently because every move of the pair locks the pair's lot-less row first, and the sum is taken after all rows of the document are locked · NO
Filters · Result · Nulls : variantPk EXACT, warehousePk EXACT, lot mode (all lots / one lot / lot-less) · scalar · none → 0

QR-INV-020 — Stock move search
Phase · API · Entity · Op : SVC-API · API-INV-018 · ENT-INV-004 · FIND_BY_CRITERIA
Intent        : moves matching Variant, warehouse, lot, move type, document number, source document (type and identifier) and date range, newest first, with source document type and number (REQ-INV-075, 076); the source document filter identifies one document exactly, so the jumps from a posted transfer or adjustment show only its moves (SRS SCR-REQ-INV-004 B2)
Logical spec  : SELECT * FROM INV_STOCK_MOVE WHERE [VARIANT_FK = :variantPk] [AND WAREHOUSE_FK = :warehousePk] [AND LOT_FK = :lotPk] [AND MOVE_TYPE_CODE = :moveTypeCode] [AND SOURCE_DOC_NO = :docNo] [AND SOURCE_DOC_TYPE_CODE = :sourceDocTypeCode] [AND SOURCE_DOC_REF = :sourceDocRef] [AND DOC_DATE BETWEEN :dateFrom AND :dateTo] ORDER BY <order> [page/size], where <order> is `STOCK_MOVE_PK DESC` while showRunningBalance holds (variantPk and warehousePk both given — the running balance BALANCE_AFTER_QTY follows posting order, and a backdated document posted later would otherwise appear out of sequence — REQ-INV-076, gate-analysis revise 5, G3), and `DOC_DATE DESC, STOCK_MOVE_PK DESC` otherwise
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · YES (Page<T>) — IDX_INV_STOCK_MOVE_SRC_TYPE_REF serves the source document filter; IDX_INV_STOCK_MOVE_TENANT (TENANT_ID, VARIANT_FK, WAREHOUSE_FK, …) serves the one-Variant, one-warehouse view
Filters · Result · Nulls : variantPk, warehousePk, lotPk, moveTypeCode, docNo, sourceDocTypeCode, sourceDocRef EXACT; DATE_RANGE · full entity (BALANCE_AFTER_QTY shown only when filtered to one Variant and one warehouse, then in posting order) · LOT_FK, MEASURE_QTY nullable

QR-INV-021 — Stock move by identifier
Phase · API · Entity · Op : SVC-API · API-INV-019 · ENT-INV-004 · FIND_ONE
Intent        : one move with its source document (REQ-INV-056)
Logical spec  : SELECT * FROM INV_STOCK_MOVE WHERE STOCK_MOVE_PK = :id
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · NO
Filters · Result · Nulls : PK EXACT · full entity · not found → INV-404-STOCK-MOVE-NOT-FOUND

QR-INV-022 — Stock move insert
Phase · API · Entity · Op : SVC-API · API-INV-024, 032, 038, 047 · ENT-INV-004 · SAVE (insert only)
Intent        : record one immutable ledger row (RULE-INV-023 — no UPDATE / DELETE path exists in the repository)
Logical spec  : INSERT INTO INV_STOCK_MOVE (MOVE_TYPE_CODE, QUANTITY, MEASURE_QTY, UNIT_COST, MOVE_VALUE, BALANCE_AFTER_QTY, SOURCE_DOC_TYPE_CODE, SOURCE_DOC_REF, SOURCE_DOC_NO, SOURCE_LINE_REF, OFFLINE_FL, IDEMPOTENCY_KEY, REQUEST_HASH, VARIANT_FK, WAREHOUSE_FK, LOT_FK, BUNDLE_VARIANT_FK, RESERVATION_FK, DOC_DATE, CURRENCY_CODE, …audit) VALUES (…); the entity is `@Immutable` and the table carries TRG_INV_STOCK_MOVE_IMMUTABLE, so an UPDATE or DELETE fails in the database too (ADR-INV-049)
Join · Tx · Locking · Paging : NONE · READ_WRITE · NONE (insert) · NO
Filters · Result · Nulls : — · stockMovePk · nullable per manifest

QR-INV-023 — Valuation of a Variant
Phase · API · Entity · Op : SVC-API · API-INV-024, 032, 038, 047, 051 · ENT-INV-013 · FIND_ONE
Intent        : the weighted-average record of one Variant (REQ-INV-059 … 063); absent = never valued (ADR-INV-030)
Logical spec  : SELECT * FROM INV_STOCK_VALUATION WHERE VARIANT_FK = :variantPk
Join · Tx · Locking · Paging : NONE · READ_ONLY (API-INV-051) / READ_WRITE (posting) · FOR UPDATE in posting paths, taken after the document's balance rows, in ascending VARIANT_FK order · NO
Filters · Result · Nulls : variantPk EXACT · full entity · absent → API-INV-051 answers zeros (contract C-5)

QR-INV-024 — Valuation save
Phase · API · Entity · Op : SVC-API · API-INV-024, 032, 038, 047 · ENT-INV-013 · NATIVE (insert-if-absent) / UPDATE
Intent        : create the valuation on the first valued incoming move, or update total, average, value and last move
Logical spec  : INSERT INTO INV_STOCK_VALUATION (TENANT_ID, VARIANT_FK, TOTAL_ON_HAND_QTY, AVERAGE_UNIT_COST, STOCK_VALUE, LAST_MOVE_AT, DOC_DATE, CURRENCY_CODE, VERSION, …audit) VALUES (:tenantId, …, 0, …) ON CONFLICT ON CONSTRAINT UQ_INV_STOCK_VALUATION_VARIANT_FK DO NOTHING, then re-read with QR-INV-023 FOR UPDATE · UPDATE INV_STOCK_VALUATION SET TOTAL_ON_HAND_QTY = :t, AVERAGE_UNIT_COST = :a, STOCK_VALUE = :v, LAST_MOVE_AT = :now WHERE TENANT_ID = :tenantId AND STOCK_VALUATION_PK = :id
Join · Tx · Locking · Paging : NONE; the insert-if-absent is native — tenant predicate and TENANT_ID column written by hand, ADR-INV-041 (`@TenantUnscoped`) · READ_WRITE · row locked (QR-INV-023) · NO
Filters · Result · Nulls : — · full entity · —

QR-INV-025 — Valuation search with grand total
Phase · API · Entity · Op : SVC-API · API-INV-050 · ENT-INV-013 · FIND_BY_CRITERIA + AGGREGATE
Intent        : the page of valuations and the grand total of stock value over the whole filter (SCR-REQ-INV-012)
Logical spec  : SELECT * FROM INV_STOCK_VALUATION WHERE [VARIANT_FK IN (:variantPks)] [AND TOTAL_ON_HAND_QTY <> 0 when nonZero] ORDER BY VARIANT_FK [page/size]; SELECT COALESCE(SUM(STOCK_VALUE),0) FROM INV_STOCK_VALUATION WHERE <same filter>
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · YES (Page<T>) + scalar
Filters · Result · Nulls : variantPks SET (variantPk, barcode term via integrate (XM-INV-013), itemPk via integrate (XM-INV-015)), nonZero · full entity + total · —

QR-INV-026 — Reserved quantity
Phase · API · Entity · Op : SVC-API · API-INV-016, 017 · ENT-INV-005 · AGGREGATE
Intent        : remaining quantity of ACTIVE reservations of a Variant per warehouse (REQ-INV-038)
Logical spec  : SELECT WAREHOUSE_FK, COALESCE(SUM(RESERVED_QTY),0) FROM INV_RESERVATION WHERE VARIANT_FK = :variantPk AND STATUS_CODE = 'ACTIVE' [AND WAREHOUSE_FK = :warehousePk] GROUP BY WAREHOUSE_FK
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · NO
Filters · Result · Nulls : variantPk, warehousePk EXACT · projection · none → 0

QR-INV-027 — Transfer search
Phase · API · Entity · Op : SVC-API · API-INV-020 · ENT-INV-006 · FIND_BY_CRITERIA
Intent        : transfers matching number, date range, source, destination and status, newest first (REQ-INV-086)
Logical spec  : SELECT * FROM INV_TRANSFER WHERE [DOC_NO ILIKE %:docNo%] [AND DOC_DATE BETWEEN :dateFrom AND :dateTo] [AND FROM_WAREHOUSE_FK = :fromWarehousePk] [AND TO_WAREHOUSE_FK = :toWarehousePk] [AND STATUS_CODE = :statusCode] ORDER BY DOC_DATE DESC, TRANSFER_PK DESC [page/size]
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · YES (Page<T>)
Filters · Result · Nulls : docNo LIKE, DATE_RANGE, warehouses EXACT, statusCode EXACT · header only · NOTE_TEXT, POSTED_AT nullable

QR-INV-028 — Transfer with lines
Phase · API · Entity · Op : SVC-API · API-INV-021, 023, 024, 025 · ENT-INV-006, ENT-INV-007 · FIND_ONE
Intent        : one transfer and its lines
Logical spec  : SELECT * FROM INV_TRANSFER WHERE TRANSFER_PK = :id; SELECT * FROM INV_TRANSFER_LINE WHERE TRANSFER_FK = :id ORDER BY TRANSFER_LINE_PK
Join · Tx · Locking · Paging : NONE (two single-table reads of one aggregate) · READ_ONLY / READ_WRITE · header FOR UPDATE in API-INV-023/024/025 — a second post or cancel waits and then sees POSTED / CANCELLED (INV-409-DOCUMENT-CLOSED) · NO
Filters · Result · Nulls : PK EXACT · aggregate · not found → INV-404-TRANSFER-NOT-FOUND

QR-INV-029 — Transfer save
Phase · API · Entity · Op : SVC-API · API-INV-022, 023, 024, 025 · ENT-INV-006, ENT-INV-007 · SAVE / UPDATE
Intent        : insert a DRAFT transfer with lines; replace header fields and lines of a DRAFT (REQ-INV-079); set status / posted time / line unit cost
Logical spec  : INSERT INTO INV_TRANSFER (…) / INSERT INTO INV_TRANSFER_LINE (…); UPDATE INV_TRANSFER SET … WHERE TRANSFER_PK = :id AND VERSION = :version; DELETE FROM INV_TRANSFER_LINE WHERE TRANSFER_FK = :id AND TRANSFER_LINE_PK NOT IN (:kept) then upsert kept lines
Join · Tx · Locking · Paging : NONE · READ_WRITE · header locked (QR-INV-028); UQ_INV_TRANSFER_LINE_TRF_VAR_LOT closes the duplicate-line race (→ INV-422-DUPLICATE-LINE) · NO
Filters · Result · Nulls : — · aggregate · —

QR-INV-030 — Stock count search
Phase · API · Entity · Op : SVC-API · API-INV-026 · ENT-INV-008 · FIND_BY_CRITERIA
Intent        : counts matching number, date range, warehouse and status, newest first (REQ-INV-095)
Logical spec  : SELECT * FROM INV_STOCK_COUNT WHERE [DOC_NO ILIKE %:docNo%] [AND DOC_DATE BETWEEN :dateFrom AND :dateTo] [AND WAREHOUSE_FK = :warehousePk] [AND STATUS_CODE = :statusCode] ORDER BY DOC_DATE DESC, STOCK_COUNT_PK DESC [page/size]
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · YES (Page<T>)
Filters · Result · Nulls : docNo LIKE, DATE_RANGE, warehousePk, statusCode EXACT · header only · —

QR-INV-031 — Stock count with lines
Phase · API · Entity · Op : SVC-API · API-INV-027, 029, 030, 031, 032, 033, 035 · ENT-INV-008, ENT-INV-009 · FIND_ONE
Intent        : one count and its lines
Logical spec  : SELECT * FROM INV_STOCK_COUNT WHERE STOCK_COUNT_PK = :id; SELECT * FROM INV_STOCK_COUNT_LINE WHERE STOCK_COUNT_FK = :id ORDER BY STOCK_COUNT_LINE_PK
Join · Tx · Locking · Paging : NONE · READ_ONLY / READ_WRITE · header FOR UPDATE in every line change, post and cancel — two scans of the same Variant serialise on the header, so the second adds to the line the first created (REQ-INV-088) · NO
Filters · Result · Nulls : PK EXACT · aggregate · not found → INV-404-STOCK-COUNT-NOT-FOUND

QR-INV-032 — Stock count header save
Phase · API · Entity · Op : SVC-API · API-INV-028, 032, 033 · ENT-INV-008 · SAVE / UPDATE
Intent        : insert a DRAFT count; set POSTED with posted time and resulting adjustment; set CANCELLED
Logical spec  : INSERT INTO INV_STOCK_COUNT (…); UPDATE INV_STOCK_COUNT SET STATUS_CODE = :s, POSTED_AT = :t, ADJUSTMENT_FK = :a WHERE STOCK_COUNT_PK = :id
Join · Tx · Locking · Paging : NONE · READ_WRITE · header locked (QR-INV-031) · NO
Filters · Result · Nulls : — · header · ADJUSTMENT_FK null when no difference

QR-INV-033 — Count line of a Variant and lot
Phase · API · Entity · Op : SVC-API · API-INV-029 · ENT-INV-009 · FIND_ONE
Intent        : the existing line of the Variant (and lot) on the count, to add a scanned quantity to it (SRS D-14)
Logical spec  : SELECT * FROM INV_STOCK_COUNT_LINE WHERE STOCK_COUNT_FK = :id AND VARIANT_FK = :v AND LOT_FK IS NOT DISTINCT FROM :l
Join · Tx · Locking · Paging : NONE · READ_WRITE · header locked (QR-INV-031); UQ_INV_STOCK_COUNT_LINE_CNT_VAR_LOT backs it · NO
Filters · Result · Nulls : key EXACT · line or none · —

QR-INV-034 — Count line save / delete
Phase · API · Entity · Op : SVC-API · API-INV-029, 030, 031, 032 · ENT-INV-009 · SAVE / UPDATE / DELETE
Intent        : create or increase a line, set a counted quantity, remove a DRAFT line, record system and difference quantities at posting
Logical spec  : INSERT INTO INV_STOCK_COUNT_LINE (…); UPDATE INV_STOCK_COUNT_LINE SET COUNTED_QTY = :q, COUNTED_MEASURE_QTY = :m [, SYSTEM_QTY, DIFFERENCE_QTY] WHERE STOCK_COUNT_LINE_PK = :id; DELETE FROM INV_STOCK_COUNT_LINE WHERE STOCK_COUNT_LINE_PK = :id AND STOCK_COUNT_FK = :countId
Join · Tx · Locking · Paging : NONE · READ_WRITE · header locked; optimistic VERSION on the line for API-INV-030 · NO
Filters · Result · Nulls : — · line · SYSTEM_QTY / DIFFERENCE_QTY null until posting

QR-INV-035 — Stock adjustment search
Phase · API · Entity · Op : SVC-API · API-INV-034 · ENT-INV-010 · FIND_BY_CRITERIA
Intent        : adjustments matching number, date range, warehouse, reason and status, newest first (REQ-INV-104)
Logical spec  : SELECT * FROM INV_STOCK_ADJUSTMENT WHERE [DOC_NO ILIKE %:docNo%] [AND DOC_DATE BETWEEN :dateFrom AND :dateTo] [AND WAREHOUSE_FK = :warehousePk] [AND REASON_CODE = :reasonCode] [AND STATUS_CODE = :statusCode] ORDER BY DOC_DATE DESC, STOCK_ADJUSTMENT_PK DESC [page/size]
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · YES (Page<T>)
Filters · Result · Nulls : docNo LIKE, DATE_RANGE, warehousePk, reasonCode, statusCode EXACT · header only · —

QR-INV-036 — Stock adjustment with lines
Phase · API · Entity · Op : SVC-API · API-INV-027, 035, 037, 038, 039, 047 · ENT-INV-010, ENT-INV-011 · FIND_ONE
Intent        : one adjustment and its lines ordered by row number
Logical spec  : SELECT * FROM INV_STOCK_ADJUSTMENT WHERE STOCK_ADJUSTMENT_PK = :id; SELECT * FROM INV_STOCK_ADJUSTMENT_LINE WHERE STOCK_ADJUSTMENT_FK = :id ORDER BY ROW_NO
Join · Tx · Locking · Paging : NONE · READ_ONLY / READ_WRITE · header FOR UPDATE in API-INV-037/038/039 · NO
Filters · Result · Nulls : PK EXACT · aggregate · not found → INV-404-STOCK-ADJUSTMENT-NOT-FOUND

QR-INV-037 — Stock adjustment save
Phase · API · Entity · Op : SVC-API · API-INV-032, 036, 037, 038, 039, 047 · ENT-INV-010, ENT-INV-011 · SAVE / UPDATE
Intent        : insert a DRAFT adjustment with lines (screen, count posting with COUNT_DIFFERENCE, opening stock with OPENING_STOCK); replace reason, note and lines of a DRAFT; set POSTED / CANCELLED, posted time, line lot and value
Logical spec  : INSERT INTO INV_STOCK_ADJUSTMENT (…) / INSERT INTO INV_STOCK_ADJUSTMENT_LINE (…) (batched, up to 1000 rows); UPDATE INV_STOCK_ADJUSTMENT SET … WHERE STOCK_ADJUSTMENT_PK = :id AND VERSION = :version; line replace as QR-INV-029
Join · Tx · Locking · Paging : NONE · READ_WRITE · header locked (QR-INV-036); UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_VAR_LOT and UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_ROW close duplicate races · NO
Filters · Result · Nulls : — · aggregate · —

QR-INV-038 — Lot search with on-hand
Phase · API · Entity · Op : SVC-API · API-INV-040 · ENT-INV-012, ENT-INV-003 · NATIVE (AGGREGATE)
Intent        : lots matching Variant, lot number, warehouse and expiry range with on-hand, ordered by expiry date (REQ-INV-109); near-expiry only = non-zero on-hand and expiry within today + horizon (REQ-INV-116); expired lots with stock stay listed and marked (REQ-INV-115); the daily near-expiry job reads the same query in job mode — "not yet notified", grouped by lot only, so a lot held in several warehouses is one row with its total on-hand (AC-INV-131, SRS D-19)
Logical spec  : SELECT l.*, b.WAREHOUSE_FK, SUM(b.ON_HAND_QTY) on_hand FROM INV_LOT l LEFT JOIN INV_STOCK_BALANCE b ON b.TENANT_ID = :tenantId AND b.LOT_FK = l.LOT_PK WHERE l.TENANT_ID = :tenantId [AND l.VARIANT_FK = :variantPk] [AND l.CODE ILIKE %:code%] [AND b.WAREHOUSE_FK = :warehousePk] [AND l.EXPIRY_DATE BETWEEN :expiryFrom AND :expiryTo] [AND l.EXPIRY_DATE <= :today + :horizonDays when nearExpiryOnly] [AND l.NEAR_EXPIRY_NOTIFIED_AT IS NULL for the job] GROUP BY l.LOT_PK, b.WAREHOUSE_FK [HAVING SUM(b.ON_HAND_QTY) <> 0 when nearExpiryOnly] ORDER BY l.EXPIRY_DATE, l.CODE [page/size]. Job mode: no warehouse column and no warehouse filter — SELECT l.*, SUM(b.ON_HAND_QTY) on_hand … WHERE … AND l.NEAR_EXPIRY_NOTIFIED_AT IS NULL AND l.EXPIRY_DATE <= :today + :horizonDays GROUP BY l.LOT_PK HAVING SUM(b.ON_HAND_QTY) <> 0 ORDER BY l.EXPIRY_DATE, l.CODE — one row per lot, its on-hand summed over every warehouse
Join · Tx · Locking · Paging : required — ADR-INV-025; tenant predicate on every table and join — ADR-INV-041 (`@TenantUnscoped`) · READ_ONLY · NONE · YES (Page<T>)
Filters · Result · Nulls : variantPk EXACT, code LIKE, warehousePk EXACT, expiry DATE_RANGE, nearExpiryOnly · projection LotRow (expiredFl = EXPIRY_DATE < today, daysLeft) · lot with no balance row → on-hand 0, warehouse null

QR-INV-039 — Lot by identifier
Phase · API · Entity · Op : SVC-API · API-INV-018, 019, 021, 022, 023, 024, 027, 029, 032, 035, 038, 041, 042 · ENT-INV-012 · FIND_ONE
Intent        : one lot; on lines, also the check that the lot belongs to the line's Variant (RULE-INV-037); read in one batch by identifier per page to label the lotPk of lines and moves (CORE "Labels on every row")
Logical spec  : SELECT * FROM INV_LOT WHERE LOT_PK = :id
Join · Tx · Locking · Paging : NONE · READ_ONLY / READ_WRITE · FOR UPDATE, then the optimistic VERSION check, in API-INV-042 (an expiry correction waits for any posting or issue that holds the lot, and they wait for it); FOR SHARE in the posting paths (API-INV-024, 032, 038 — the lots the document's lines name) and in the in-process issue (the lot a non-sale line names, and every FEFO candidate lot a sale draws from), taken after the document's balance rows, in ascending LOT_PK order (engine step 1, ADR-INV-026), so the expiry a sale or FEFO selection uses cannot change under it · NO
Filters · Result · Nulls : PK EXACT · full entity · not found → INV-404-LOT-NOT-FOUND

QR-INV-040 — Lot by Variant and number
Phase · API · Entity · Op : SVC-API · API-INV-038, 047 · ENT-INV-012 · FIND_ONE
Intent        : reuse an existing lot named by an incoming line, or learn it is new (REQ-INV-106, RULE-INV-036)
Logical spec  : SELECT * FROM INV_LOT WHERE VARIANT_FK = :v AND CODE = :lotNo FOR UPDATE
Join · Tx · Locking · Paging : NONE · READ_WRITE · FOR UPDATE; a lot created concurrently is caught by UQ_INV_LOT_VAR_CODE (QR-INV-041 insert-if-absent, then re-read) · NO
Filters · Result · Nulls : key EXACT · full entity or none · —

QR-INV-041 — Lot save
Phase · API · Entity · Op : SVC-API · API-INV-038, 042, 047 · ENT-INV-012 · NATIVE (insert-if-absent) / UPDATE
Intent        : create a lot on first receipt or increase; correct its expiry / descriptions; recompute its active flag from its stock after commit (engine step 7 — in its own short transaction, holding no posting lock); claim the lot for its near-expiry notification (daily job — the claim is the stamp, taken before anything is sent)
Logical spec  : INSERT INTO INV_LOT (TENANT_ID, VARIANT_FK, CODE, EXPIRY_DATE, FIRST_RECEIVED_AT, IS_ACTIVE_FL, VERSION, …audit) VALUES (:tenantId, …, 0, …) ON CONFLICT ON CONSTRAINT UQ_INV_LOT_VAR_CODE DO NOTHING · UPDATE INV_LOT SET EXPIRY_DATE / NAME_AR / NAME_EN / IS_ACTIVE_FL = … WHERE LOT_PK = :id [AND VERSION = :version] (the update is JPA, tenant-filtered by `@TenantId`) · **near-expiry claim** (daily job): UPDATE INV_LOT SET NEAR_EXPIRY_NOTIFIED_AT = :now, VERSION = VERSION + 1 WHERE LOT_PK = :id AND NEAR_EXPIRY_NOTIFIED_AT IS NULL — a JPQL bulk update restricted to the current tenant by `@TenantId` (written native, it carries `AND TENANT_ID = :tenantId` and `@TenantUnscoped`, ADR-INV-041); its row count decides: exactly 1 → this run owns the lot's notification, 0 → another run or an earlier day already claimed it (gate-analysis revise 5, G5)
Join · Tx · Locking · Paging : NONE; the insert-if-absent is native — tenant predicate and TENANT_ID column written by hand, ADR-INV-041 (`@TenantUnscoped`) · READ_WRITE · row locked FOR UPDATE by QR-INV-039 and optimistic VERSION (API-INV-042); the after-commit activity recompute updates the row alone and waits only for transactions that still hold the lot · NO
Filters · Result · Nulls : — · full entity · NAME_AR / NAME_EN null → display CODE

QR-INV-042 — Item stock settings search
Phase · API · Entity · Op : SVC-API · API-INV-043 · ENT-INV-014 · FIND_BY_CRITERIA
Intent        : settings records matching Item and flags (SCR-REQ-INV-009); also the dependents of the in-process turn-off check (RULE-INV-046)
Logical spec  : SELECT * FROM INV_ITEM_STOCK_SETTINGS WHERE [ITEM_FK IN (:itemPks)] [AND LOT_TRACKED_FL = :lotTracked] [AND DUAL_QUANTITY_FL = :dualQuantity] ORDER BY KEY [page/size]
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · YES (Page<T>)
Filters · Result · Nulls : itemPks SET, flags EXACT · full entity · MEASURE_UOM_CODE nullable

QR-INV-043 — Item stock settings of an Item
Phase · API · Entity · Op : SVC-API · API-INV-016, 017, 022, 023, 029, 030, 036, 037, 038, 044, 045, 047 · ENT-INV-014 · FIND_ONE
Intent        : whether an Item is lot-tracked / dual-quantity and its measure unit; no record = false, false, empty (REQ-INV-129)
Logical spec  : SELECT * FROM INV_ITEM_STOCK_SETTINGS WHERE ITEM_FK = :itemPk
Join · Tx · Locking · Paging : NONE · READ_ONLY / READ_WRITE · FOR UPDATE in API-INV-045; FOR SHARE in the posting paths (engine step 1 — API-INV-024, 032, 038, 047 and the in-process receive / issue), the records of the document's Items read in ascending ITEM_FK order, so a flag clear (FOR UPDATE) and a receipt of the same Item serialise (ADR-INV-026) · NO
Filters · Result · Nulls : itemPk EXACT · full entity or defaults · —

QR-INV-044 — Item stock settings save
Phase · API · Entity · Op : SVC-API · API-INV-045 · ENT-INV-014 · SAVE / UPDATE
Intent        : create the record of a STANDARD Item or update its flags and measure unit (REQ-INV-124, 125)
Logical spec  : INSERT INTO INV_ITEM_STOCK_SETTINGS (ITEM_FK, KEY, VALUE_AR, VALUE_EN, LOT_TRACKED_FL, DUAL_QUANTITY_FL, MEASURE_UOM_CODE, IS_ACTIVE_FL, …audit) VALUES (…) · UPDATE … WHERE ITEM_STOCK_SETTINGS_PK = :id AND VERSION = :version
Join · Tx · Locking · Paging : NONE · READ_WRITE · record locked (QR-INV-043); a concurrent first create is caught by UQ_INV_ITEM_STOCK_SETTINGS_ITEM_FK (→ re-read and update) · NO
Filters · Result · Nulls : — · full entity · —

QR-INV-045 — Lots of an Item still holding stock
Phase · API · Entity · Op : SVC-API · API-INV-045 · ENT-INV-003 · COUNT
Intent        : number of lots of the Item's Variants with non-zero on-hand (RULE-INV-042, message {count})
Logical spec  : SELECT COUNT(DISTINCT LOT_FK) FROM INV_STOCK_BALANCE WHERE VARIANT_FK IN (:variantPks) AND LOT_FK IS NOT NULL AND ON_HAND_QTY <> 0
Join · Tx · Locking · Paging : NONE · READ_WRITE (after QR-INV-043 FOR UPDATE) · NONE · NO
Filters · Result · Nulls : variantPks SET (via integrate (XM-INV-015)) · count · —

QR-INV-046 — Measure still held by an Item
Phase · API · Entity · Op : SVC-API · API-INV-045 · ENT-INV-003 · EXISTS
Intent        : a balance of the Item's Variants has non-zero measure (RULE-INV-043)
Logical spec  : SELECT 1 FROM INV_STOCK_BALANCE WHERE VARIANT_FK IN (:variantPks) AND COALESCE(ON_HAND_MEASURE_QTY,0) <> 0 LIMIT 1
Join · Tx · Locking · Paging : NONE · READ_WRITE · NONE · NO
Filters · Result · Nulls : variantPks SET (via integrate (XM-INV-015)) · boolean · null measure = 0
```

## 5 — Error Catalog

Envelope: `LocalizedException → {code, messageAr, messageEn}` (a `details` list is added by
API-INV-047 — one entry per failing row, ADR-INV-027 — and, for a refusal raised by a rule on one line, by API-INV-022,
023, 024, 029, 030, 032, 036, 037 and 038 — one entry `{lineRef, lineRefKind, rowNo?, code, messageAr, messageEn}`
for the first failing line, where lineRef is the line's identifier (`LINE_PK`) or its 1-based position in the
request (`REQUEST_POSITION`) and rowNo is an adjustment line's row number, ADR-INV-046). Runtime code format (CORE R1): `{MOD}-{http}[-{SLUG}]` with MOD = INV. Every
`http` value is one of 200, 201, 400, 401, 403, 404, 409, 413, 415, 422, 500. Messages are copied from the
SRS character for character. A module-specific code the SRS gives no text for — the not-found codes, the lookup-key
code, the load wrapper INV-422-LOAD-ROWS-INVALID and the four generic codes INV-400, INV-401, INV-500 and
INV-409-STALE-VERSION — carries INV's own ar / en text here, with its `adr`: every row of this catalogue has a text
in both languages, because the platform holds no text under an INV-prefixed code (ADR-INV-021, amended at
gate-analysis revise 3 and 4). The
cross-module edges are in-process (one deployable) — no row describes a network failure.

```yaml name=error-catalog
rows:
  - {code: "INV-422-WAREHOUSE-CODE-READONLY", rule: RULE-INV-001, api: [API-INV-004], http: 422, trigger: "an update request carries a code different from the stored warehouse code", messages: {ar: "رمز المستودع يُنشأ تلقائيًا ولا يمكن تغييره.", en: "The warehouse code is generated automatically and cannot be changed."}}
  - {code: "INV-409-WAREHOUSE-NAME-TAKEN", rule: RULE-INV-002, api: [API-INV-003, API-INV-004], http: 409, trigger: "another warehouse of the tenant has the same nameAr (QR-INV-003 or UQ_INV_WAREHOUSE_NAME_AR)", messages: {ar: "يوجد مستودع آخر بهذا الاسم.", en: "Another warehouse already has this name."}}
  - {code: "INV-409-WAREHOUSE-DEFAULT", rule: RULE-INV-003, api: [API-INV-005], http: 409, trigger: "deactivation of the warehouse whose isDefaultFl is true", messages: {ar: "لا يمكن إيقاف المستودع الافتراضي. اجعل مستودعًا آخر افتراضيًا أولًا.", en: "The default warehouse cannot be deactivated. Set another warehouse as the default first."}}
  - {code: "INV-409-WAREHOUSE-IN-USE", rule: RULE-INV-004, api: [API-INV-005], http: 409, trigger: "deactivation of a warehouse with a non-zero balance, an ACTIVE reservation or a DRAFT transfer, count or adjustment naming it (QR-INV-007, ADR-INV-045)", messages: {ar: "لا يمكن إيقاف مستودع فيه مخزون أو حجوزات قائمة. انقل المخزون أو سوِّه أولًا.", en: "A warehouse that holds stock or open reservations cannot be deactivated. Transfer or adjust the stock first."}}
  - {code: "INV-409-WAREHOUSE-DEFAULT-INACTIVE", rule: RULE-INV-006, api: [API-INV-007], http: 409, trigger: "set as default on an inactive warehouse", messages: {ar: "المستودع الافتراضي يجب أن يكون نشطًا.", en: "The default warehouse must be active."}}
  - {code: "INV-422-WAREHOUSE-INACTIVE", rule: RULE-INV-007, api: [API-INV-028, API-INV-032, API-INV-036, API-INV-038, API-INV-047], http: 422, trigger: "a count, adjustment or opening stock load, or an in-process receipt, issue or reservation, names a warehouse whose isActiveFl is false, on save or again on post (engine step 0, ADR-INV-045; also the inactive-warehouse message of RULE-INV-005); a transfer answers RULE-INV-016 instead, and an offline sale is never refused (ADR-INV-052)", messages: {ar: "المستودع غير نشط.", en: "The warehouse is inactive."}}
  - {code: "INV-422-WAREHOUSE-REQUIRED", rule: RULE-INV-008, api: [API-INV-028, API-INV-036, API-INV-047], http: 422, trigger: "no warehouse named while two or more warehouses are active", messages: {ar: "اختر المستودع.", en: "Choose the warehouse."}}
  - {code: "INV-422-BRANCH-INACTIVE", rule: RULE-INV-009, api: [API-INV-003, API-INV-004, API-INV-006], http: 422, trigger: "a warehouse is saved or reactivated against an inactive branch", messages: {ar: "الفرع المختار غير نشط.", en: "The chosen branch is inactive."}}
  - {code: "INV-422-BRANCH-CODE-READONLY", rule: RULE-INV-010, api: [API-INV-012], http: 422, trigger: "an update request carries a code different from the stored branch code", messages: {ar: "رمز الفرع يُنشأ تلقائيًا ولا يمكن تغييره.", en: "The branch code is generated automatically and cannot be changed."}}
  - {code: "INV-409-BRANCH-NAME-TAKEN", rule: RULE-INV-011, api: [API-INV-011, API-INV-012], http: 409, trigger: "another branch of the tenant has the same nameAr", messages: {ar: "يوجد فرع آخر بهذا الاسم.", en: "Another branch already has this name."}}
  - {code: "INV-409-BRANCH-DEFAULT", rule: RULE-INV-012, api: [API-INV-013], http: 409, trigger: "deactivation of the branch whose isDefaultFl is true", messages: {ar: "لا يمكن إيقاف الفرع الافتراضي. اجعل فرعًا آخر افتراضيًا أولًا.", en: "The default branch cannot be deactivated. Set another branch as the default first."}}
  - {code: "INV-409-BRANCH-IN-USE", rule: RULE-INV-013, api: [API-INV-013], http: 409, trigger: "deactivation of a branch that an active warehouse belongs to (QR-INV-014)", messages: {ar: "لا يمكن إيقاف فرع تتبعه مستودعات نشطة. انقل المستودعات إلى فرع آخر أو أوقفها أولًا.", en: "A branch with active warehouses cannot be deactivated. Move the warehouses to another branch or deactivate them first."}}
  - {code: "INV-409-BRANCH-DEFAULT-INACTIVE", rule: RULE-INV-015, api: [API-INV-015], http: 409, trigger: "set as default on an inactive branch", messages: {ar: "الفرع الافتراضي يجب أن يكون نشطًا.", en: "The default branch must be active."}}
  - {code: "INV-422-TRANSFER-WAREHOUSES", rule: RULE-INV-016, api: [API-INV-022, API-INV-023, API-INV-024], http: 422, trigger: "source equals destination, either warehouse is inactive, or fewer than two warehouses are active (REQ-INV-014)", messages: {ar: "التحويل يحتاج إلى مستودعين نشطين مختلفين.", en: "A transfer needs two different active warehouses."}}
  - {code: "INV-422-QUANTITY-INVALID", rule: RULE-INV-017, api: [API-INV-022, API-INV-023, API-INV-036, API-INV-037, API-INV-047], http: 422, trigger: "a transfer line quantity is not greater than zero or has more than 3 decimals; a transfer line measure, an adjustment line quantityChange or measureQtyChange, or an opening stock row quantity or measure has more than 3 decimals", messages: {ar: "أدخل كمية أكبر من صفر بثلاث خانات عشرية على الأكثر.", en: "Enter a quantity greater than zero with at most 3 decimal places."}}
  - {code: "INV-422-VARIANT-INACTIVE", rule: RULE-INV-018, api: [API-INV-022, API-INV-023, API-INV-024, API-INV-029, API-INV-032, API-INV-036, API-INV-037, API-INV-038, API-INV-047], http: 422, trigger: "the line's Variant or Item is inactive, or the Variant belongs to a BUNDLE Item on a transfer, count, adjustment or load line (and on an in-process receipt or customer return), read through integrate (XM-INV-014), on save or again on post (engine step 0, ADR-INV-045)", messages: {ar: "متغيّر الصنف غير نشط.", en: "The Variant is inactive."}}
  - {code: "INV-409-NEGATIVE-STOCK", rule: RULE-INV-019, api: [API-INV-024, API-INV-032, API-INV-038], http: 409, trigger: "an online decrease would take a Variant's on-hand in a warehouse below zero, or a decrease naming a lot would take that lot's on-hand in the warehouse below zero, while INV_ALLOW_NEGATIVE_STOCK is false; {onHand} = on-hand before the move (the lot's, for the lot check)", messages: {ar: "الرصيد لا يكفي: المتاح في المستودع {onHand}. الرصيد السالب غير مسموح.", en: "Not enough stock: {onHand} on hand in this warehouse. Negative stock is not allowed."}}
  - {code: "INV-422-BUNDLE-NO-COMPONENTS", rule: RULE-INV-022, api: [API-INV-017], http: 422, trigger: "availability of a bundle Variant that has no active component line (integrate (XM-INV-012)); the in-process reserve and issue raise the same code", messages: {ar: "الحزمة ليس لها مكوّنات نشطة.", en: "The bundle has no active components."}}
  - {code: "INV-422-NO-KNOWN-COST", rule: RULE-INV-025, api: [API-INV-036, API-INV-037, API-INV-038, API-INV-047], http: 422, trigger: "an increase line has no unit cost and the Variant has no Stock valuation (ADR-INV-030; an increase line of a count-generated adjustment is valued at 0 instead, so API-INV-032 never raises it), or a unit cost has more than 4 decimals", messages: {ar: "لا توجد تكلفة معروفة لهذا المتغيّر. أدخل تكلفة الوحدة.", en: "This Variant has no known cost. Enter the unit cost."}}
  - {code: "INV-422-OPENING-COST-REQUIRED", rule: RULE-INV-026, api: [API-INV-038, API-INV-047], http: 422, trigger: "a line of an OPENING_STOCK adjustment has no unit cost, a negative cost or a quantity change not above zero", messages: {ar: "أدخل تكلفة الوحدة للمخزون الافتتاحي.", en: "Enter the unit cost of the opening stock."}}
  - {code: "INV-422-LOAD-ROWS", rule: RULE-INV-027, api: [API-INV-047], http: 422, trigger: "an opening stock load with zero rows or more than 1000 rows", messages: {ar: "الحد الأقصى 1000 سطر في التحميل الواحد.", en: "At most 1000 rows per load."}}
  - {code: "INV-422-LOAD-ROW-VARIANT", rule: RULE-INV-028, api: [API-INV-047], http: 422, trigger: "a load row's barcode or Variant identifier identifies no active Variant of a STANDARD Item (row detail)", messages: {ar: "لا يطابق هذا السطر متغيّر صنف نشطًا.", en: "This row matches no active Variant."}}
  - {code: "INV-422-LOAD-ROWS-INVALID", rule: REQ-INV-072, api: [API-INV-047], http: 422, trigger: "one or more rows of an opening stock load fail validation; nothing is posted and details lists each failing rowNo with its own code and message", messages: {ar: "بعض أسطر التحميل غير صالحة؛ راجع الأسطر المذكورة.", en: "Some rows of the load are invalid; see the listed rows."}, adr: ADR-INV-027}
  - {code: "INV-409-DOCUMENT-CLOSED", rule: RULE-INV-029, api: [API-INV-023, API-INV-024, API-INV-025, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-037, API-INV-038, API-INV-039], http: 409, trigger: "change, post, cancel or line change of a transfer, count or adjustment whose statusCode is POSTED or CANCELLED", messages: {ar: "المستند مُرحَّل أو ملغى ولا يمكن تعديله.", en: "The document is posted or cancelled and cannot be changed."}}
  - {code: "INV-422-DOC-NO-READONLY", rule: RULE-INV-030, api: [API-INV-023, API-INV-037], http: 422, trigger: "an update request carries a docNo different from the stored one", messages: {ar: "رقم المستند يُنشأ تلقائيًا ولا يمكن تغييره.", en: "The document number is generated automatically and cannot be changed."}}
  - {code: "INV-422-DOC-DATE-FUTURE", rule: RULE-INV-051, api: [API-INV-022, API-INV-023, API-INV-028, API-INV-036, API-INV-047], http: 422, trigger: "a transfer, count, adjustment or opening stock load whose docDate is after today in the tenant time zone", messages: {ar: "تاريخ المستند لا يكون بعد اليوم.", en: "The document date cannot be after today."}}
  - {code: "INV-422-NO-LINES", rule: RULE-INV-031, api: [API-INV-024, API-INV-032, API-INV-038], http: 422, trigger: "posting a transfer, count or adjustment with no line", messages: {ar: "أضف سطرًا واحدًا على الأقل قبل الترحيل.", en: "Add at least one line before posting."}}
  - {code: "INV-422-BARCODE-UNKNOWN", rule: RULE-INV-032, api: [API-INV-029], http: 422, trigger: "a scanned barcode identifies no active Variant (integrate (XM-INV-013))", messages: {ar: "الباركود غير معروف. اختر متغيّر الصنف يدويًا.", en: "Unknown barcode. Pick the Variant by hand."}}
  - {code: "INV-422-REASON-REQUIRED", rule: RULE-INV-033, api: [API-INV-036, API-INV-037, API-INV-038], http: 422, trigger: "on the stock adjustment screen, reasonCode missing, not an active value of INV_ADJUSTMENT_REASON (MdlLookupApi), or OPENING_STOCK, which only the opening stock load writes; never raised for a system-generated adjustment (count posting, opening stock — ADR-INV-047)", messages: {ar: "اختر سبب التسوية.", en: "Choose the adjustment reason."}}
  - {code: "INV-422-ZERO-CHANGE", rule: RULE-INV-034, api: [API-INV-036, API-INV-037, API-INV-038], http: 422, trigger: "an adjustment line with quantityChange 0, unless the Item is dual-quantity and measureQtyChange is non-zero", messages: {ar: "أدخل تغيّرًا في الكمية أو القياس لا يساوي صفرًا.", en: "Enter a non-zero change of quantity or measure."}}
  - {code: "INV-422-LOT-EXPIRY-REQUIRED", rule: RULE-INV-035, api: [API-INV-038, API-INV-047], http: 422, trigger: "an incoming line of a lot-tracked Item without lot number or expiry date while lotExpiry is on", messages: {ar: "أدخل رقم التشغيلة وتاريخ الصلاحية لهذا الصنف.", en: "Enter the lot number and expiry date for this Item."}}
  - {code: "INV-422-LOT-EXPIRY-MISMATCH", rule: RULE-INV-036, api: [API-INV-038, API-INV-047], http: 422, trigger: "an incoming line names an existing lot of the Variant with a different expiry date; {lot}, {expiry} = the stored lot", messages: {ar: "التشغيلة {lot} مسجّلة بتاريخ صلاحية {expiry}.", en: "Lot {lot} is recorded with expiry date {expiry}."}}
  - {code: "INV-422-LOT-REQUIRED", rule: RULE-INV-037, api: [API-INV-022, API-INV-023, API-INV-024, API-INV-029, API-INV-032, API-INV-036, API-INV-037, API-INV-038], http: 422, trigger: "a transfer, count or decrease adjustment line of a lot-tracked Item names no lot of its Variant, and its quantity is above the Variant's on-hand held without a lot in the line's warehouse, while lotExpiry is on, on save or again on post (the Item became lot-tracked after the save — engine step 0, ADR-INV-045; the lot-less exception — REQ-INV-139, ADR-INV-051)", messages: {ar: "اختر التشغيلة لهذا الصنف.", en: "Choose the lot for this Item."}}
  - {code: "INV-422-MEASURE-REQUIRED", rule: RULE-INV-040, api: [API-INV-022, API-INV-023, API-INV-024, API-INV-029, API-INV-030, API-INV-032, API-INV-036, API-INV-037, API-INV-038, API-INV-047], http: 422, trigger: "while dualQuantity is on, a transfer or count line of a dual-quantity Item carries no measure, or an adjustment or load line of one has a non-zero quantity change and no measure change — on save or again on post (the Item became dual-quantity after the save — engine step 0, ADR-INV-045)", messages: {ar: "أدخل القياس (الوزن أو الطول) لهذا الصنف.", en: "Enter the measure (weight or length) for this Item."}}
  - {code: "INV-422-MEASURE-UNIT-REQUIRED", rule: RULE-INV-041, api: [API-INV-045], http: 422, trigger: "dualQuantityFl true without measureUomCode", messages: {ar: "اختر وحدة القياس الثانية (وزن أو طول).", en: "Choose the measure unit (weight or length)."}}
  - {code: "INV-409-LOT-TRACKING-IN-USE", rule: RULE-INV-042, api: [API-INV-045], http: 409, trigger: "clearing lotTrackedFl while lots of the Item's Variants hold stock; {count} = QR-INV-045", messages: {ar: "لا يمكن إلغاء تتبع التشغيلة: توجد تشغيلات فيها مخزون ({count}). اشطبها أو اصرفها أولًا.", en: "Lot tracking cannot be cleared: lots still hold stock ({count}). Write them off or issue them first."}}
  - {code: "INV-409-DUAL-QUANTITY-IN-USE", rule: RULE-INV-043, api: [API-INV-045], http: 409, trigger: "clearing dualQuantityFl while a balance of the Item's Variants holds a non-zero measure", messages: {ar: "لا يمكن إلغاء الكمية المزدوجة: يوجد مخزون بقياس غير صفري. سوِّ القياس إلى صفر أولًا.", en: "Dual quantity cannot be cleared: stock still holds a non-zero measure. Adjust the measure to zero first."}}
  - {code: "INV-422-CAPABILITY-OFF", rule: RULE-INV-044, api: [API-INV-045], http: 422, trigger: "lotTrackedFl set true while lotExpiry is off, or dualQuantityFl set true while dualQuantity is off", messages: {ar: "فعِّل مفتاح القدرة الخاص بهذا الإعداد أولًا.", en: "Switch on the capability flag for this setting first."}}
  - {code: "INV-422-BUNDLE-SETTINGS", rule: RULE-INV-045, api: [API-INV-045], http: 422, trigger: "Item stock settings for an Item whose itemKindCode is BUNDLE (integrate (XM-INV-015))", messages: {ar: "الحزمة لا تحمل مخزونًا؛ تُضبط إعدادات المخزون على مكوّناتها.", en: "A bundle holds no stock; set stock settings on its components."}}
  - {code: "INV-422-COUNTED-NEGATIVE", rule: RULE-INV-047, api: [API-INV-029, API-INV-030], http: 422, trigger: "countedQty or countedMeasureQty below zero or with more than 3 decimals", messages: {ar: "الكمية المعدودة لا تكون سالبة.", en: "The counted quantity cannot be negative."}}
  - {code: "INV-422-DUPLICATE-LINE", rule: RULE-INV-048, api: [API-INV-022, API-INV-023, API-INV-036, API-INV-037, API-INV-047], http: 422, trigger: "two lines with the same Variant and lot on one transfer, adjustment or load", messages: {ar: "هذا المتغيّر مذكور في سطر آخر بالتشغيلة نفسها.", en: "This Variant is already on another line with the same lot."}}
  - {code: "INV-422-HORIZON-RANGE", rule: REQ-INV-119, api: [API-INV-049], http: 422, trigger: "nearExpiryDays not a whole number from 1 to 365 (SRS D-04, no RULE)", messages: {ar: "أدخل عدد أيام من 1 إلى 365.", en: "Enter a number of days from 1 to 365."}, adr: ADR-INV-028}
  - {code: "INV-409-IDEMPOTENCY-KEY-REUSED", rule: RULE-INV-050, api: [API-INV-047], http: 409, trigger: "an opening stock load repeats an idempotency key already recorded with a different request hash (ADR-INV-053); the in-process receive, issue and reserve raise the same code (ADR-INV-048)", messages: {ar: "مفتاح عدم التكرار مستخدم لطلب آخر. استخدم مفتاحًا جديدًا لهذا الطلب.", en: "This idempotency key was already used for a different request. Use a new key for this request."}, adr: ADR-INV-053}
  - {code: "INV-404-WAREHOUSE-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-002, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-017, API-INV-022, API-INV-023, API-INV-028, API-INV-036, API-INV-047], http: 404, trigger: "a path or body warehouse identifier that does not exist in the tenant", messages: {ar: "المستودع غير موجود.", en: "The warehouse was not found."}, adr: ADR-INV-021}
  - {code: "INV-404-BRANCH-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-003, API-INV-004, API-INV-010, API-INV-012, API-INV-013, API-INV-014, API-INV-015], http: 404, trigger: "a path or body branch identifier that does not exist in the tenant", messages: {ar: "الفرع غير موجود.", en: "The branch was not found."}, adr: ADR-INV-021}
  - {code: "INV-404-VARIANT-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-017, API-INV-022, API-INV-023, API-INV-029, API-INV-036, API-INV-037, API-INV-051], http: 404, trigger: "a Variant identifier the owner's read does not know (integrate (XM-INV-014))", messages: {ar: "متغيّر الصنف غير موجود.", en: "The Variant was not found."}, adr: ADR-INV-021}
  - {code: "INV-404-ITEM-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-044, API-INV-045], http: 404, trigger: "an Item identifier the owner's read does not know (integrate (XM-INV-015))", messages: {ar: "الصنف غير موجود.", en: "The Item was not found."}, adr: ADR-INV-021}
  - {code: "INV-404-STOCK-MOVE-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-019], http: 404, trigger: "unknown stock move identifier", messages: {ar: "حركة المخزون غير موجودة.", en: "The stock move was not found."}, adr: ADR-INV-021}
  - {code: "INV-404-TRANSFER-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-021, API-INV-023, API-INV-024, API-INV-025], http: 404, trigger: "unknown transfer identifier", messages: {ar: "التحويل غير موجود.", en: "The transfer was not found."}, adr: ADR-INV-021}
  - {code: "INV-404-STOCK-COUNT-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-027, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033], http: 404, trigger: "unknown stock count identifier", messages: {ar: "الجرد غير موجود.", en: "The stock count was not found."}, adr: ADR-INV-021}
  - {code: "INV-404-STOCK-COUNT-LINE-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-030, API-INV-031], http: 404, trigger: "unknown line identifier, or a line of another count", messages: {ar: "سطر الجرد غير موجود في هذا الجرد.", en: "The count line was not found on this count."}, adr: ADR-INV-021}
  - {code: "INV-404-STOCK-ADJUSTMENT-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-035, API-INV-037, API-INV-038, API-INV-039], http: 404, trigger: "unknown stock adjustment identifier", messages: {ar: "التسوية غير موجودة.", en: "The stock adjustment was not found."}, adr: ADR-INV-021}
  - {code: "INV-404-LOT-NOT-FOUND", rule: PLATFORM-STD, api: [API-INV-022, API-INV-023, API-INV-029, API-INV-041, API-INV-042], http: 404, trigger: "unknown lot identifier, or a lot of another Variant than the line's", messages: {ar: "التشغيلة غير موجودة.", en: "The lot was not found."}, adr: ADR-INV-021}
  - {code: "INV-404-LOOKUP-KEY-UNKNOWN", rule: PLATFORM-STD, api: [API-INV-052], http: 404, trigger: "a lookup key that INV does not own (InvLookups serves INV_ADJUSTMENT_REASON, INV_MOVE_TYPE, INV_DOC_STATUS, INV_RESERVATION_STATUS, INV_SOURCE_DOC_TYPE and INV_CHANGE_KIND only)", messages: {ar: "مفتاح القائمة غير معروف.", en: "Unknown lookup key."}, adr: ADR-INV-033}
  - {code: "INV-409-STALE-VERSION", rule: PLATFORM-STD, api: [API-INV-004, API-INV-006, API-INV-007, API-INV-012, API-INV-014, API-INV-015, API-INV-023, API-INV-024, API-INV-030, API-INV-032, API-INV-037, API-INV-038, API-INV-042, API-INV-045], http: 409, trigger: "the request's version differs from the stored VERSION (optimistic lock of the given AuditableEntity)", messages: {ar: "تغيّر السجل منذ قراءته. أعد التحميل.", en: "The record changed since you read it. Reload."}, adr: ADR-INV-021}
  - {code: "INV-400", rule: PLATFORM-STD, api: [API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008, API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015, API-INV-016, API-INV-017, API-INV-018, API-INV-019, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-040, API-INV-041, API-INV-042, API-INV-043, API-INV-044, API-INV-045, API-INV-046, API-INV-047, API-INV-048, API-INV-049, API-INV-050, API-INV-051, API-INV-052], http: 400, trigger: "malformed request: unreadable body, wrong type, missing required field, unknown enum-free code format", messages: {ar: "طلب غير صالح.", en: "The request is invalid."}, adr: ADR-INV-021}
  - {code: "INV-401", rule: PLATFORM-STD, api: [API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008, API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015, API-INV-016, API-INV-017, API-INV-018, API-INV-019, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-040, API-INV-041, API-INV-042, API-INV-043, API-INV-044, API-INV-045, API-INV-046, API-INV-047, API-INV-048, API-INV-049, API-INV-050, API-INV-051, API-INV-052], http: 401, trigger: "no valid bearer token (the given security filter)", messages: {ar: "يلزم تسجيل الدخول.", en: "Authentication is required."}, adr: ADR-INV-021}
  - {code: "INV-403", rule: PLATFORM-STD, api: [API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008, API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015, API-INV-016, API-INV-017, API-INV-018, API-INV-019, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-040, API-INV-041, API-INV-042, API-INV-043, API-INV-044, API-INV-045, API-INV-046, API-INV-047, API-INV-048, API-INV-049, API-INV-050, API-INV-051, API-INV-052], http: 403, trigger: "the caller lacks the PERM_<PAGE_CODE>_<ACTION> the endpoint requires (REQ-INV-138)", messages: {ar: "ليست لديك صلاحية لهذا الإجراء.", en: "You do not have permission for this action."}, adr: ADR-INV-021}
  - {code: "INV-500", rule: PLATFORM-STD, api: [API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008, API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015, API-INV-016, API-INV-017, API-INV-018, API-INV-019, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-040, API-INV-041, API-INV-042, API-INV-043, API-INV-044, API-INV-045, API-INV-046, API-INV-047, API-INV-048, API-INV-049, API-INV-050, API-INV-051, API-INV-052], http: 500, trigger: "unexpected server failure", messages: {ar: "حدث خطأ غير متوقع. حاول مرة أخرى.", en: "An unexpected error occurred. Try again."}, adr: ADR-INV-021}
```

### 5.1 Codes raised in-process only (no HTTP operation — ADR-INV-024)

These codes are thrown as `LocalizedException` to the calling module through the in-process `InventoryApi`
(SVC-API-INT); no endpoint of this module can raise them, so the API document carries them only under its
document-level `x-inprocess-error-codes`. They follow the same format.

| Code | Rule | Raised by | ar | en |
|---|---|---|---|---|
| INV-409-RESERVE-NOT-AVAILABLE | RULE-INV-020 | reserve (CON-INV-011) — {available} = available before the request | الكمية المتاحة لا تكفي للحجز. المتاح: {available}. | Not enough available stock to reserve. Available: {available}. |
| INV-409-RESERVATION-CLOSED | RULE-INV-021 | releaseReservation of a CONSUMED reservation (CON-INV-012 — a RELEASED one is returned unchanged, ADR-INV-055), issueStock with a reservation that is not ACTIVE (CON-INV-015) | الحجز مغلق ولا يمكن استخدامه. | The reservation is closed and cannot be used. |
| INV-409-STOCK-MOVE-IMMUTABLE | RULE-INV-023 | any attempt through the repository to update or delete a move (the repository exposes no such method; the guard throws) | لا تُعدَّل حركات المخزون. صحّح الرصيد بتسوية مخزون. | Stock moves cannot be changed. Correct the balance with a stock adjustment. |
| INV-422-LOT-EXPIRED | RULE-INV-039 | issueStock (CON-INV-015) — a non-sale ISSUE or RETURN_OUT naming an expired lot | التشغيلة منتهية الصلاحية. اشطبها بتسوية مخزون. | The lot has expired. Write it off with a stock adjustment. |
| INV-422-RESERVATION-MISMATCH | RULE-INV-049 | issueStock with a reservation (CON-INV-015) — the reservation's variantFk or warehouseFk differs from the line's, or its sourceDocTypeCode / sourceDocRef differs from the request's source document | الحجز لا يخص هذا السطر: المتغيّر أو المستودع أو المستند مختلف. | The reservation does not belong to this line: the Variant, warehouse or document differs. |

The in-process operations also raise, with the same codes and texts as the catalog: INV-422-WAREHOUSE-INACTIVE,
INV-422-WAREHOUSE-REQUIRED, INV-422-QUANTITY-INVALID, INV-422-VARIANT-INACTIVE, INV-422-BUNDLE-NO-COMPONENTS,
INV-409-NEGATIVE-STOCK, INV-422-NO-KNOWN-COST, INV-422-LOT-EXPIRY-REQUIRED, INV-422-LOT-EXPIRY-MISMATCH,
INV-422-MEASURE-REQUIRED, INV-404-WAREHOUSE-NOT-FOUND, INV-404-VARIANT-NOT-FOUND, INV-404-LOT-NOT-FOUND, and
INV-409-IDEMPOTENCY-KEY-REUSED (receiveStock, issueStock, reserve — a key repeated with a different request hash,
ADR-INV-048; an HTTP catalog row since API-INV-047 raises it too — ADR-INV-053).
