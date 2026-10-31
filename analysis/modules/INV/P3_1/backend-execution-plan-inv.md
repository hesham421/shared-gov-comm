# BACKEND EXECUTION PLAN — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Profile : commerce   Stage : P3.1 (Backend Execution Plan) · pass 1 · round 1 · gate-analysis revise 4
Track  : backend · plan exec · lane analysis · prepared by claude:opus
Inputs : srs (REQ 140 · AC 159 · ENT 14 · RULE 51 · SCR-REQ 12) · db-script (14 tables · DBF 221 · XM 15 — 13 from P2 + the 2 minted here and back-registered, V1002__inv_inventory.sql) · registry-srs · registry-db · contract (CON-INV-001 … CON-INV-021) · contracts (the published contracts of the modules this one reads — reached here only through XM ids)
Governance : FULL (db-script present — every binding resolved by DBF / constraint name)
Produces : backend-execution-plan-inv.md · registry-exec-be-inv.md · api-spec-inv.yaml (OpenAPI 3.1.0, derived from §R3 blocks and the Error Catalog)
Owns ids : API-INV-001 … API-INV-052 · QR-INV-001 … QR-INV-046 (first sequences of the module; API-INV-052 added at gate-analysis revise 2)
ADRs raised : ADR-INV-021 … ADR-INV-032 (P3.1), ADR-INV-041 … ADR-INV-045 (gate-analysis revise 1) and ADR-INV-046 … ADR-INV-050 (gate-analysis revise 2) and ADR-INV-051 … ADR-INV-053 (gate-analysis revise 3) and ADR-INV-054 (gate-analysis revise 4) — all ACCEPTED, non-breaking; filed under analysis/decisions/INV/ · next free ADR-INV-055
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
Open ADRs: 0 BLOCKED raised here (ADR-INV-021 … ADR-INV-032, ADR-INV-041 … ADR-INV-054 ACCEPTED) — decisions/INV/
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
| API-INV-038 | post stock adjustment | PATCH | /api/v1/inv/stock-adjustments/{id}/post | REQ-INV-099, REQ-INV-102, REQ-INV-114, REQ-INV-123 |
| API-INV-039 | cancel stock adjustment | DELETE | /api/v1/inv/stock-adjustments/{id} | REQ-INV-100 |
| API-INV-040 | search, list lots (custom near-expiry view) | GET | /api/v1/inv/lots | REQ-INV-109, REQ-INV-116 |
| API-INV-041 | read lot | GET | /api/v1/inv/lots/{id} | REQ-INV-109 |
| API-INV-042 | update (expiry correction) of a lot | PATCH | /api/v1/inv/lots/{id} | REQ-INV-108 |
| API-INV-043 | search, list Item stock settings | GET | /api/v1/inv/item-stock-settings | REQ-INV-129 |
| API-INV-044 | read Item stock settings | GET | /api/v1/inv/item-stock-settings/by-item/{itemId} | REQ-INV-129 |
| API-INV-045 | create, update Item stock settings | PUT | /api/v1/inv/item-stock-settings/by-item/{itemId} | REQ-INV-124 … REQ-INV-128 |
| API-INV-046 | download opening stock sheet | GET | /api/v1/inv/opening-stock/sheet | REQ-INV-073 |
| API-INV-047 | post opening stock | POST | /api/v1/inv/opening-stock | REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-074, REQ-INV-140 |
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
| RULE-INV-021 | ENT-INV-005 | on release; on issue that references a reservation | The system shall prevent a release or a consumption of a reservation whose statusCode is not ACTIVE. | INV-409-RESERVATION-CLOSED (in-process, §5.1) |
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
| RULE-INV-038 | ENT-INV-012, 004 | on submit of a sale issue; on FEFO selection; on availability read | The system shall exclude from sale issue, FEFO selection and available quantity every lot whose expiryDate is before today in the tenant's time zone. | effect rule — exclusion, raises no error; message «التشغيلة منتهية الصلاحية ولا تُباع.» / "The lot has expired and cannot be sold." is the expired-lot label (expiredFl) of balance and lot reads |
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
Logical spec  : SELECT COALESCE(SUM(ON_HAND_QTY),0) FROM INV_STOCK_BALANCE WHERE VARIANT_FK = :v [AND WAREHOUSE_FK = :w] [AND LOT_FK = :l]
Join · Tx · Locking · Paging : NONE · READ_WRITE (inside the posting transaction) · the pair's lot-less row and the touched lot rows are locked by QR-INV-017; untouched lots of the pair cannot change concurrently because every move of the pair locks the pair's lot-less row first, and the sum is taken after all rows of the document are locked · NO
Filters · Result · Nulls : — · scalar · none → 0

QR-INV-020 — Stock move search
Phase · API · Entity · Op : SVC-API · API-INV-018 · ENT-INV-004 · FIND_BY_CRITERIA
Intent        : moves matching Variant, warehouse, lot, move type, document number, source document (type and identifier) and date range, newest first, with source document type and number (REQ-INV-075, 076); the source document filter identifies one document exactly, so the jumps from a posted transfer or adjustment show only its moves (SRS SCR-REQ-INV-004 B2)
Logical spec  : SELECT * FROM INV_STOCK_MOVE WHERE [VARIANT_FK = :variantPk] [AND WAREHOUSE_FK = :warehousePk] [AND LOT_FK = :lotPk] [AND MOVE_TYPE_CODE = :moveTypeCode] [AND SOURCE_DOC_NO = :docNo] [AND SOURCE_DOC_TYPE_CODE = :sourceDocTypeCode] [AND SOURCE_DOC_REF = :sourceDocRef] [AND DOC_DATE BETWEEN :dateFrom AND :dateTo] ORDER BY DOC_DATE DESC, STOCK_MOVE_PK DESC [page/size]
Join · Tx · Locking · Paging : NONE · READ_ONLY · NONE · YES (Page<T>) — IDX_INV_STOCK_MOVE_SRC_TYPE_REF serves the source document filter
Filters · Result · Nulls : variantPk, warehousePk, lotPk, moveTypeCode, docNo, sourceDocTypeCode, sourceDocRef EXACT; DATE_RANGE · full entity (BALANCE_AFTER_QTY shown only when filtered to one Variant and one warehouse) · LOT_FK, MEASURE_QTY nullable

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
Intent        : create a lot on first receipt or increase; correct its expiry / descriptions; recompute its active flag from its stock after commit (engine step 7 — in its own short transaction, holding no posting lock); stamp NEAR_EXPIRY_NOTIFIED_AT (daily job)
Logical spec  : INSERT INTO INV_LOT (TENANT_ID, VARIANT_FK, CODE, EXPIRY_DATE, FIRST_RECEIVED_AT, IS_ACTIVE_FL, VERSION, …audit) VALUES (:tenantId, …, 0, …) ON CONFLICT ON CONSTRAINT UQ_INV_LOT_VAR_CODE DO NOTHING · UPDATE INV_LOT SET EXPIRY_DATE / NAME_AR / NAME_EN / IS_ACTIVE_FL / NEAR_EXPIRY_NOTIFIED_AT = … WHERE LOT_PK = :id [AND VERSION = :version] (the update is JPA, tenant-filtered by `@TenantId`)
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
| INV-409-RESERVATION-CLOSED | RULE-INV-021 | releaseReservation (CON-INV-012), issueStock with a reservation (CON-INV-015) | الحجز مغلق ولا يمكن استخدامه. | The reservation is closed and cannot be used. |
| INV-409-STOCK-MOVE-IMMUTABLE | RULE-INV-023 | any attempt through the repository to update or delete a move (the repository exposes no such method; the guard throws) | لا تُعدَّل حركات المخزون. صحّح الرصيد بتسوية مخزون. | Stock moves cannot be changed. Correct the balance with a stock adjustment. |
| INV-422-LOT-EXPIRED | RULE-INV-039 | issueStock (CON-INV-015) — a non-sale ISSUE or RETURN_OUT naming an expired lot | التشغيلة منتهية الصلاحية. اشطبها بتسوية مخزون. | The lot has expired. Write it off with a stock adjustment. |
| INV-422-RESERVATION-MISMATCH | RULE-INV-049 | issueStock with a reservation (CON-INV-015) — the reservation's variantFk or warehouseFk differs from the line's, or its sourceDocTypeCode / sourceDocRef differs from the request's source document | الحجز لا يخص هذا السطر: المتغيّر أو المستودع أو المستند مختلف. | The reservation does not belong to this line: the Variant, warehouse or document differs. |

The in-process operations also raise, with the same codes and texts as the catalog: INV-422-WAREHOUSE-INACTIVE,
INV-422-WAREHOUSE-REQUIRED, INV-422-QUANTITY-INVALID, INV-422-VARIANT-INACTIVE, INV-422-BUNDLE-NO-COMPONENTS,
INV-409-NEGATIVE-STOCK, INV-422-NO-KNOWN-COST, INV-422-LOT-EXPIRY-REQUIRED, INV-422-LOT-EXPIRY-MISMATCH,
INV-422-MEASURE-REQUIRED, INV-404-WAREHOUSE-NOT-FOUND, INV-404-VARIANT-NOT-FOUND, INV-404-LOT-NOT-FOUND, and
INV-409-IDEMPOTENCY-KEY-REUSED (receiveStock, issueStock, reserve — a key repeated with a different request hash,
ADR-INV-048; an HTTP catalog row since API-INV-047 raises it too — ADR-INV-053).

<!-- PHASE:CORE:START traces=REQ-INV-016,REQ-INV-034,REQ-INV-037,REQ-INV-057,REQ-INV-058,REQ-INV-077,REQ-INV-132,REQ-INV-133 -->
## CORE — architecture policies

**Module layout (Spring Boot, Java 25, on the given erp-core).** Package root `com.commerce.inv`, the
application namespace of every business module (platform summary decision 11; `com.erp.*` is the
given's namespace, checklist F8). The folders follow the checklist:

| Package (`com.commerce.inv.…`) | Holds |
|---|---|
| `entity` | JPA entities extending the given `AuditableEntity` |
| `domain` | one `<Entity>Domain` companion per entity (below) — the rules that need no other aggregate and the A7 transitions (checklist C12, `domain/*Domain.java`) |
| `repository` | Spring Data repositories, one per entity |
| `service` | transactional REST-facing services — orchestrate, lock and persist; every public method carries `@PreAuthorize` (SEC-BE, ADR-INV-042) |
| `engine` | the internal engines shared by the services and `InventoryApi` — `StockPostingEngine`, `AvailabilityEngine`, `ReservationEngine`, `InventoryReadEngine`; no permission check, no REST path (ADR-INV-042) |
| `controller` | REST controllers under `/api/v1/inv` |
| `crossmodule` | the in-process `InventoryApi` other modules inject (CON-INV-006 … 021) — the only package other modules may import |
| `port` | this module's own interfaces for data owned elsewhere — implemented only by INT-C packages |
| `integration.cat` | the INT-C adapters of the CAT edges (XM-INV-012 … 015) |
| `permission` | `InvPermissions implements PermissionContributor` — page codes, `PERM_INV_*` constants, preset grants |
| `exception` | `InvErrorCodes` — the runtime codes of §5 / §5.1 as constants |
| `report` | `InvReports implements ReportProvider` — report INV_OPENING_STOCK_SHEET |
| `tenant` | `InvTenantProvisioning implements TenantProvisioningContributor`; `InvNearExpiryJob` (TenantRunner) |
| `config` | `InvFeatureFlags`, `InvSettings`, `InvNotifier` |
| `lookup` | `InvLookups` — serves INV's own six lookup keys through `OwnedLookups.read` (below; API-INV-052) |

Layers: Controller → Service → (Domain companion, Engine, Repository). A controller holds no business rule; a
repository holds no rule; a service orchestrates — it loads, locks, calls the companion and the engine, and
persists; services call other services only inside the module.

**Domain companions (`<Entity>Domain`, package `com.commerce.inv.domain`).** Each entity has a companion with a static
`create(…)` (a new valid instance) and `from(entity)` (the domain view of a stored one, whose methods apply the
transitions). The companion holds every rule that needs no other aggregate, the A7 transitions and the
default / active invariants; the service never re-states them. A rule that needs another aggregate (a
repository probe, a CAT read, a balance) stays in the service or the engine (§1.3 names which).

| Companion | Rules and transitions it holds |
|---|---|
| `WarehouseDomain` | RULE-INV-001 (code set once, change refused), 002 (nameAr required, non-blank — uniqueness stays a repository probe + constraint), 003 and 006 (default / active invariants: the default cannot be deactivated, a default must be active), deactivate / reactivate / setDefault / clearDefault |
| `BranchDomain` | RULE-INV-010, 011 (nameAr required, non-blank), 012, 015, deactivate / reactivate / setDefault / clearDefault |
| `StockBalanceDomain` | apply(quantity, measure) — the only writer of onHandQty / onHandMeasureQty; returns whether the pair crossed below zero (REQ-INV-068) |
| `StockMoveDomain` | create only (RULE-INV-023 — no mutator exists; the `StockMove` entity is `@org.hibernate.annotations.Immutable` and the table carries TRG_INV_STOCK_MOVE_IMMUTABLE — ADR-INV-049), RULE-INV-017 (quantity > 0, or the measure-only form of ADR-INV-017), moveValue = quantity × unitCost |
| `ReservationDomain` | RULE-INV-017, the A7 reservation transitions consume(quantity) / release() with RULE-INV-021 (only ACTIVE), shortfall |
| `TransferDomain` | RULE-INV-016 (source ≠ destination — the active check stays in the service), 017, 029, 030, 048 on its lines, the A7 transitions post() / cancel() |
| `TransferLineDomain` | RULE-INV-017 on quantity and measure |
| `StockCountDomain` | RULE-INV-029, 030, 047 on its lines, addCounted(variant, lot, qty) (REQ-INV-088), the A7 transitions post() / cancel() |
| `StockCountLineDomain` | RULE-INV-047 |
| `StockAdjustmentDomain` | RULE-INV-017, 029, 030, 034, 048 on its lines, rowNo assignment, the A7 transitions post() / cancel() |
| `StockAdjustmentLineDomain` | RULE-INV-017, 034 (with the Item's dual-quantity flag passed in), RULE-INV-025's 4-decimal part |
| `LotDomain` | RULE-INV-036 (expiry equality against the stored expiry), activate / deactivate from on-hand, correctExpiry (REQ-INV-108) |
| `StockValuationDomain` | the weighted-average arithmetic of REQ-INV-059 … 062 and ADR-INV-030 (restart when total ≤ 0, 4-dp average, 2-dp value) |
| `ItemStockSettingsDomain` | RULE-INV-041, the system-filled key / tracking summary (ADR-INV-029) |

**Module manifest (`META-INF/erp/modules/inv.yaml`, checklist C18).**
```
code: INV
package: com.commerce.inv
api-paths: [/api/v1/inv/**]
permission-modules: [INV]
depends-on: [CAT]
home-cards: []
```
Every endpoint of §1.2 is under `/api/v1/inv/**`; the only permission module the INV code references is `INV`.

**Module boundary (checklist C20).** The module's test suite runs the given
`ModuleBoundaryRules.modulesDependOnlyOnTheirDeclaredDependencies()` (ArchUnit), so INV imports only its own
packages, the given's `com.erp.*` crossmodule / extension packages it declares in §1.5, and
`com.commerce.cat.crossmodule` (its one declared dependency; CAT v2 still declares `com.erp.cat`, and its rename
to `com.commerce.cat` is the open platform finding of business-policies-pty PLATFORM FINDINGS row 1, cited in
business-policies-inv — the rule names CAT's crossmodule package as CAT delivers it). Its own test `InvModuleBoundaryTest` adds that no
other module imports `com.commerce.inv` outside `com.commerce.inv.crossmodule`, and that nothing in
`com.commerce.inv` reads the activity preset (REQ-INV-132). The module has its own CI job `inv-backend`
(build, unit and integration tests, the two boundary rules, `api-verify` against `api-spec-inv.yaml`).

**Type mapping (postgresql16 → Java)** — column types only; the PK-generation clause is not a type (every PK is
`GENERATED ALWAYS AS IDENTITY`, mapped `@GeneratedValue(strategy = IDENTITY)`):

| DB type (db-script) | Java type | Notes |
|---|---|---|
| BIGINT (PK, FK, reference) | Long | identifiers and soft references |
| VARCHAR(n) | String | `@Size(max = n)` on request fields |
| BOOLEAN | Boolean | flags end with `Fl` |
| NUMERIC(18,3) | BigDecimal | quantities and measures, scale 3, HALF_UP |
| NUMERIC(18,4) | BigDecimal | unit and average cost, scale 4, HALF_UP |
| NUMERIC(18,2) | BigDecimal | values, scale 2, HALF_UP (SRS D-06) |
| TIMESTAMPTZ | OffsetDateTime | audit, posted / closed / last-move times |
| DATE | LocalDate | business dates and expiry (ADR-INV-016) |
| INTEGER | Integer | row number |

**Runtime error-code format:** `{MOD}-{http}[-{SLUG}]` — MOD = `INV`, http = the response status, SLUG = upper
kebab case. Every Error Catalog row (§5, §5.1) is an instance of it. Errors are raised as the given
`LocalizedException → {code, messageAr, messageEn}`; one class `com.commerce.inv.exception.InvErrorCodes`
(checklist C11, `<Module>ErrorCodes`) holds the codes as constants, and the EN and AR text of every code of §5 and
§5.1 — the four generic codes INV-400, INV-401, INV-500 and INV-409-STALE-VERSION included (ADR-INV-021 as amended at
gate-analysis revise 4) — is copied verbatim into the module's message bundles `messages.properties` (EN) and
`messages_ar.properties` (AR), the names checklist C11 uses. A
`DataIntegrityViolationException` on a named constraint is translated by constraint name: UQ_INV_WAREHOUSE_NAME_AR
→ INV-409-WAREHOUSE-NAME-TAKEN, UQ_INV_BRANCH_NAME_AR → INV-409-BRANCH-NAME-TAKEN,
UQ_INV_TRANSFER_LINE_TRF_VAR_LOT and UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_VAR_LOT → INV-422-DUPLICATE-LINE; an
`OptimisticLockException` → INV-409-STALE-VERSION; a database error whose message is
INV-409-STOCK-MOVE-IMMUTABLE (raised by TRG_INV_STOCK_MOVE_IMMUTABLE) → INV-409-STOCK-MOVE-IMMUTABLE (ADR-INV-049); any
other → INV-500 (ADR-INV-021).

**Lookups.** All LOV values are runtime-loaded from the core lookup module (mdl); no enum is hard-coded in an API
or a field specification. Stored values are codes. A code written by a request is validated as an active value
of its key through `MdlLookupApi` (INV_ADJUSTMENT_REASON for reasonCode; for measureUomCode, the consumed
unit-of-measure key named by the SettingsApi setting INV_MEASURE_UOM_KEY, platform default `CAT_UOM` —
ADR-INV-043, which revises ADR-INV-022; no second settings mechanism such as a `@Value` property is used). Codes the service itself writes (INV_MOVE_TYPE, INV_DOC_STATUS,
INV_RESERVATION_STATUS, INV_SOURCE_DOC_TYPE for INV documents, INV_CHANGE_KIND) are the seeded values of SRS A6,
held as string constants of the behaviour they select; INV_MOVE_TYPE, INV_DOC_STATUS and INV_RESERVATION_STATUS
are also CHECK-constrained in the db-script. A code the system writes itself — COUNT_DIFFERENCE on a count's
adjustment, OPENING_STOCK on an opening stock load — is not checked against the tenant's active values (ADR-INV-047).
The backend returns codes only.

**InvLookups (erp-core checklist C6).** The component `com.commerce.inv.lookup.InvLookups` serves exactly the six
keys INV owns — INV_ADJUSTMENT_REASON, INV_MOVE_TYPE, INV_DOC_STATUS, INV_RESERVATION_STATUS, INV_SOURCE_DOC_TYPE
and INV_CHANGE_KIND — through the given common helper `OwnedLookups.read(key, mdlLookupApi::readActiveValuesByKey)`,
and no other key: a key outside that set answers INV-404-LOOKUP-KEY-UNKNOWN. It is the source of every INV lookup
label and option on INV's screens (API-INV-052; the frontend lookup hooks of ADR-INV-033); a key another module
owns — CAT's unit-of-measure key included — is never served by INV.

**Numbering.** Document numbers come from the core sequence module; never generated in a business module.
`NumberSeriesApi` issues, on first save: warehouse `code` (series INV_WAREHOUSE), branch `code` (INV_BRANCH),
transfer `docNo` (INV_TRANSFER), count `docNo` (INV_COUNT), adjustment `docNo` (INV_ADJUSTMENT) — pattern
`{PREFIX}-{SEQ:4}`, prefixes WH, BR, TR, CNT, ADJ, never reset (db-script BLOCK 8c). The allocation is atomic in
the given, so two simultaneous saves never receive the same number; the tenant-led UNIQUE constraints
(UQ_INV_WAREHOUSE_CODE, UQ_INV_BRANCH_CODE, UQ_INV_TRANSFER_DOC_NO, UQ_INV_STOCK_COUNT_DOC_NO,
UQ_INV_STOCK_ADJUSTMENT_DOC_NO) are the backstop.

**Workflow engine:** forbidden. Document lifecycles (SRS A7) are explicit service transitions.

**Search contract.** Each search endpoint filters only on its screen's SRS B2 filter list, sorts on the fixed
order its SRS requirement states (code; newest first; expiry date), pages with `Page<T>` (`page`, `size`;
size ≤ 100, default 20) and answers an empty page as success. No client-chosen sort field is accepted.

**Labels on every row (gate-analysis revise 3, G6).** Every identifier a screen renders carries its display label in
the same API row, so no screen resolves an INV identifier through a capped or scoped lookup: a lot identifier carries
`lotCode` (transfer, count and adjustment lines, stock moves, balance rows), a stock count carries `adjustmentDocNo`,
a stock adjustment carries `sourceCountDocNo`, balance and availability rows carry the warehouse code and names
(ADR-INV-036). The service reads the labels of a page in one batched read by identifier (QR-INV-039 for lots,
QR-INV-036 / QR-INV-031 for documents). Two families keep their own resolution and are named here so the rule has no
silent exception: warehouse and branch identifiers on the master and document screens are labelled by the shared
warehouse and branch lookups (every row of the tenant, `active=ALL`, with a read by identifier for one off the page),
and CAT identifiers (variantPk, bundleVariantPk, itemPk) by the cross-module UXD reads.

**Languages.** Every named record carries nameAr and nameEn (nameEn optional, English display falls back to
nameAr — names of lots fall back to their code); every error carries messageAr and messageEn.

**Tenancy.** Every entity extends the given `AuditableEntity` (TENANT_ID with `@TenantId`, VERSION as
`@Version`, audit columns filled by the given auditing) — no JPA code of INV reads or writes a tenant
identifier, version or audit value (ADR-INV-014). `@TenantId` does not filter native SQL: every NATIVE query of
§4, the advisory locks of ADR-INV-044 and ADR-INV-050 and any native form of R-1 … R-6 writes `TENANT_ID = :tenantId` on each
table and join and TENANT_ID in each INSERT column list, with the current tenant taken from the given tenant
context; each such repository method carries `@TenantUnscoped(reason, adr = "ADR-INV-041")`, and a repository
test runs every one under two tenants and asserts no row of the other tenant is read or written (ADR-INV-041).

**Capability flags and settings.** `InvFeatureFlags` (service) reads `lotExpiry`, `dualQuantity`, `bundles` through
`SettingsApi` behind the FeatureFlags API — never the activity or its preset (REQ-INV-132, REQ-INV-133; an
ArchUnit rule fails the build on any read of the activity preset in `com.commerce.inv`). `InvSettings` reads and
writes INV_ALLOW_NEGATIVE_STOCK (default true, REQ-INV-066) and INV_NEAR_EXPIRY_DAYS (default 30) through
`SettingsApi`, and reads INV_MEASURE_UOM_KEY (platform default `CAT_UOM`, never written by a tenant —
ADR-INV-043); every operator-changeable value is a SettingsApi key `INV_<NAME>` (checklist C14); the tenant base currency written to every CURRENCY_CODE column (DBF-INV-061, 110, 171, 200) is
read through `SettingsApi` as well (ADR-INV-015). "Today" is the current date in the tenant's time zone,
read once per request from the given tenant context (`modules.tenant` — the same context that supplies `:tenantId`,
ADR-INV-041) and used by every date rule: expiry and FEFO (RULE-INV-038, 039), the near-expiry horizon
(REQ-INV-116, 117) and the document-date bound (RULE-INV-051 — a docDate after today answers
INV-422-DOC-DATE-FUTURE). A lot expiring today is still sellable.

**Concurrency (ADR-INV-026, ADR-INV-044).** Cross-module receive, issue and reserve first take the
transaction-scoped advisory lock of their idempotency key (`pg_advisory_xact_lock` on a hash of tenant and key —
ADR-INV-044), then look the key up. An issue then locks every reservation it references `FOR UPDATE` in ascending
reservationPk — after the idempotency lock and before the pair's balance rows — whatever the line order, so two
issues of one source document naming the same reservations in opposite order cannot deadlock (ADR-INV-026 as
amended at gate-analysis revise 4). Stock-changing transactions read the warehouse `FOR SHARE` and the Item stock
settings records of the document's Items `FOR SHARE` in ascending itemFk order; they then lock, after an
insert-if-absent, first the lot-less balance row of every Variant × warehouse pair the document touches in
ascending (variantFk, warehouseFk), then its lot rows in ascending (variantFk, warehouseFk, lotFk), then the
valuation rows in ascending variantFk order. Document headers are locked `FOR UPDATE` before any status check.
Lots: an issue and a FEFO selection read the lots they draw from `FOR SHARE` after the balance rows, in ascending
lotPk order; an expiry correction locks the lot `FOR UPDATE` (ADR-INV-026). Masters use optimistic VERSION for edits
and `FOR UPDATE` for deactivation; a default switch first takes the tenant's default-switch advisory lock
(`pg_advisory_xact_lock(hashtextextended(:tenantId || ':INV_WAREHOUSE_DEFAULT' | ':INV_BRANCH_DEFAULT', 0))`),
then reads the current default — ADR-INV-050.

**Idempotency of internal documents.** IDEMPOTENCY_KEY (NOT NULL) of a move recorded by an INV document (the opening
stock load excepted, below) is
`<sourceDocTypeCode>:<sourceDocRef>` (e.g. `INV_TRANSFER:42`), SOURCE_DOC_TYPE_CODE / SOURCE_DOC_REF /
SOURCE_DOC_NO are the document's type code, identifier and number (db-script D-P2-05, REQ-INV-056); REQUEST_HASH is the
SHA-256 of that key (ADR-INV-048). A cross-module request records the hash of its canonical request with its key, and
a repeated key with a different hash answers INV-409-IDEMPOTENCY-KEY-REUSED (RULE-INV-050, SVC-API-INT). An opening
stock load (API-INV-047) carries a client idempotency key: its moves record `OPENING_STOCK:<idempotencyKey>` and the
SHA-256 of the load's canonical form, behind the advisory lock of ADR-INV-044, so a double submit or a retry posts once
(RULE-INV-024, RULE-INV-050 — ADR-INV-053). **The hash is taken over the request as submitted** — of a cross-module
request and of an opening stock load alike: absent fields stay absent, and no default (today's docDate, the default
warehouse), trim or lookup (barcode → Variant) is applied before hashing, so a load retried after midnight or after
the default warehouse changes is still the same request (ADR-INV-048, ADR-INV-053, gate-analysis revise 4).

**Events (OutboxPublisher).** Recorded in the change transaction, delivered after commit, never on rollback:
`inv.StockMoveRecorded` — one per inserted move (CON-INV-020, REQ-INV-057); `inv.InventoryMasterChanged` — one
per committed Warehouse or Branch create / update / deactivate / reactivate / default change, with
changeKindCode from INV_CHANGE_KIND (CON-INV-021, REQ-INV-034).

**Audit (AuditApi).** Every create, change, post and cancel of a Transfer, Stock count, Stock adjustment,
Warehouse, Branch, Lot or Item stock settings record writes one audit entry with user, time and changed fields
(REQ-INV-077, checklist C16). The seven audited entities carry the given annotation with their table:
`@Audited(entityType = "INV_WAREHOUSE")`, `@Audited(entityType = "INV_BRANCH")`,
`@Audited(entityType = "INV_TRANSFER")`, `@Audited(entityType = "INV_STOCK_COUNT")`,
`@Audited(entityType = "INV_STOCK_ADJUSTMENT")`, `@Audited(entityType = "INV_LOT")` and
`@Audited(entityType = "INV_ITEM_STOCK_SETTINGS")`; their line entities are audited as part of their header's
entry. Post and cancel add an explicit business action entry through `AuditApi`.

**Notifications.** `InvNotifier` collects alerts during a transaction and sends them after commit:
negative-stock (template INV_NEGATIVE_STOCK, variables variant / warehouse / onHand — REQ-INV-068, sent when a
Variant × warehouse on-hand crosses from ≥ 0 to < 0) and near-expiry (template INV_NEAR_EXPIRY, variables
variant / lot / expiryDate / quantity — REQ-INV-117) and offline sale on an inactive record (template
INV_OFFLINE_SALE_INACTIVE, variables document / record — REQ-INV-069, ADR-INV-052, sent when an offline sale was
recorded against an inactive warehouse, Variant or Item). Recipients: users holding the Warehouse role, resolved
through `SecUserDirectoryApi`; dispatch through `NotificationDispatchApi`.

**Provisioning (TenantProvisioningContributor → `InvTenantProvisioning`).** For a new tenant, inside the
provisioning transaction: if the tenant has no Branch (QR-INV-013 = 0 and no inactive one), insert «الفرع
الرئيسي» / "Main branch" default active with a code from INV_BRANCH (QR-INV-011); if it has no Warehouse,
insert «المستودع الرئيسي» / "Main warehouse" default active under the default branch with a code from
INV_WAREHOUSE (QR-INV-004). A second run creates nothing (REQ-INV-001, 002, 020, 021; SRS D-08).

**Daily near-expiry job (TenantRunner → `InvNearExpiryJob`).** Once a day per tenant, when lotExpiry is on: read
the lots with non-zero on-hand, expiry within today + INV_NEAR_EXPIRY_DAYS and NEAR_EXPIRY_NOTIFIED_AT empty
(QR-INV-038, job mode — one row per lot, on-hand summed over every warehouse), send one notification per lot with that
total as {quantity}, then stamp NEAR_EXPIRY_NOTIFIED_AT (DBF-INV-179) through QR-INV-041
(REQ-INV-117, SRS D-19).

**Ports to data owned elsewhere** (interfaces of this module; the implementation of each is its INT-C package,
injected; until it is delivered the dependent check is unavailable and the executor follows that block's
`if_not_met`):

| Port (com.commerce.inv.port) | Reads | Edge |
|---|---|---|
| `VariantReadPort` | Variant active state, its Item, Item kind and active state, code and names, by identifier (batch) | XM-INV-014 |
| `ItemReadPort` | Item kind, code, names, active state and the identifiers of its Variants | XM-INV-015 |
| `BundleComponentPort` | active component lines of a bundle Variant (component Variant, quantity in the component's base unit) | XM-INV-012 |
| `BarcodePort` | the Variant a barcode identifies | XM-INV-013 |
<!-- PHASE:CORE:END -->

<!-- PHASE:DATA-DOM:START traces=REQ-INV-001,REQ-INV-003,REQ-INV-020,REQ-INV-022,REQ-INV-035,REQ-INV-038,REQ-INV-043,REQ-INV-051,REQ-INV-059,REQ-INV-078,REQ-INV-087,REQ-INV-097,REQ-INV-106,REQ-INV-124,DBF-INV-001,DBF-INV-014,DBF-INV-026,DBF-INV-040,DBF-INV-066,DBF-INV-087,DBF-INV-101,DBF-INV-115,DBF-INV-129,DBF-INV-143,DBF-INV-158,DBF-INV-176,DBF-INV-191,DBF-INV-205 -->
## DATA-DOM — data and domain

Every entity: table and columns bound by DBF id (properties and types in §3), PK `GENERATED ALWAYS AS IDENTITY`
from db-script INV v1 (`V1002__inv_inventory.sql`), audit / tenant / version from `AuditableEntity`. Each entity
has its `<Entity>Domain` companion in package `com.commerce.inv.domain` (CORE): the DOMAIN RULES line marks a rule the companion holds with "(domain)";
"(app)" marks one the service or the engine checks because it reads another aggregate. Full RULE text: §1.3;
codes and messages: §5. DTO rule for every entity: PK, business code / docNo, audit fields,
version (except on update requests, where it is the optimistic-lock token), tenant and every system-set column
are excluded from create requests and present in every response.

<!-- SUB:DATA-DOM-MASTER:START traces=REQ-INV-001,REQ-INV-003,REQ-INV-008,REQ-INV-020,REQ-INV-022,REQ-INV-027,REQ-INV-106,REQ-INV-108,DBF-INV-001,DBF-INV-014,DBF-INV-176 -->
### ENT-INV-001 — Warehouse / المستودع      kind: master
Honours        : CON-INV-001 (identifier BIGINT warehousePk; deactivated stays readable; one active default)
BINDINGS       : table INV_WAREHOUSE · PK DBF-INV-001 (PK_INV_WAREHOUSE) · identity · fields DBF-INV-001 … DBF-INV-013
BUSINESS CODE  : code DBF-INV-004 · `{PREFIX}-{SEQ:4}` prefix WH (e.g. WH-0002) · UQ_INV_WAREHOUSE_CODE · NumberSeriesApi series INV_WAREHOUSE on first save, read-only
DEFAULT FIELDS : master → nameAr DBF-INV-005, nameEn DBF-INV-006, code DBF-INV-004, isActiveFl DBF-INV-007, audit DBF-INV-010 … 013
DTO MEMBERSHIP : create nameAr, nameEn, branchPk (→ DBF-INV-003) · update + version · excluded warehousePk, code, isDefaultFl, isActiveFl, audit · response all
LOOKUP FIELDS  : none
CONSTRAINTS    : UQ_INV_WAREHOUSE_NAME_AR, UQ_INV_WAREHOUSE_TENANT_PK, UQ_INV_WAREHOUSE_IS_DEFAULT_FL (unique index), CHK_INV_WAREHOUSE_IS_DEFAULT_FL, CHK_INV_WAREHOUSE_NAME_AR, FK_INV_WAREHOUSE_INV_BRANCH
DOMAIN RULES   : RULE-INV-001 (domain — code set once, excluded from requests, change refused), 002 (domain: required, non-blank; app: uniqueness by UQ_INV_WAREHOUSE_NAME_AR + QR-INV-003), 003 (domain), 004 (app, QR-INV-007 — stock, ACTIVE reservations and DRAFT documents), 005 (effect: readable, excluded from active lists, master-change event), 006 (domain + UQ_INV_WAREHOUSE_IS_DEFAULT_FL + CHK_INV_WAREHOUSE_IS_DEFAULT_FL), 007 (app, every stock document on save and on post), 009 (app + FK_INV_WAREHOUSE_INV_BRANCH) — scope CREATE / UPDATE / DELETE as §1.3
STATE MACHINE  : none — isActiveFl flag (deactivate / reactivate); isDefaultFl moves in one transaction
CROSS-MODULE   : none
OPERATIONS     : VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS : QR-INV-001 … QR-INV-007

### ENT-INV-002 — Branch / الفرع      kind: master
Honours        : CON-INV-002 (identifier BIGINT branchPk; deactivated stays readable; one active default)
BINDINGS       : table INV_BRANCH · PK DBF-INV-014 (PK_INV_BRANCH) · identity · fields DBF-INV-014 … DBF-INV-025
BUSINESS CODE  : code DBF-INV-016 · `{PREFIX}-{SEQ:4}` prefix BR (e.g. BR-0002) · UQ_INV_BRANCH_CODE · NumberSeriesApi series INV_BRANCH, read-only
DEFAULT FIELDS : master → nameAr DBF-INV-017, nameEn DBF-INV-018, code DBF-INV-016, isActiveFl DBF-INV-019, audit DBF-INV-022 … 025
DTO MEMBERSHIP : create nameAr, nameEn · update + version · excluded branchPk, code, isDefaultFl, isActiveFl, audit · response all
LOOKUP FIELDS  : none
CONSTRAINTS    : UQ_INV_BRANCH_NAME_AR, UQ_INV_BRANCH_TENANT_PK, UQ_INV_BRANCH_IS_DEFAULT_FL (unique index), CHK_INV_BRANCH_IS_DEFAULT_FL, CHK_INV_BRANCH_NAME_AR
DOMAIN RULES   : RULE-INV-010 (app), 011 (UQ_INV_BRANCH_NAME_AR + QR-INV-010), 012 (app), 013 (app, QR-INV-014), 014 (effect), 015 (UQ_INV_BRANCH_IS_DEFAULT_FL + CHK_INV_BRANCH_IS_DEFAULT_FL + app)
STATE MACHINE  : none — isActiveFl flag
CROSS-MODULE   : none
OPERATIONS     : VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS : QR-INV-008 … QR-INV-014

### ENT-INV-012 — Lot / التشغيلة      kind: master
Honours        : CON-INV-003 (variantFk + code business key; one expiry per lot; created by INV only; expired never sold)
BINDINGS       : table INV_LOT · PK DBF-INV-176 (PK_INV_LOT) · identity · fields DBF-INV-176 … DBF-INV-190
BUSINESS CODE  : code DBF-INV-181 — the lot number written on the goods, entered on the incoming line, unique per Variant (UQ_INV_LOT_VAR_CODE), never generated
DEFAULT FIELDS : master → nameAr DBF-INV-182, nameEn DBF-INV-183 (optional, display code when empty — SRS D-02), code DBF-INV-181, isActiveFl DBF-INV-184, audit DBF-INV-187 … 190
DTO MEMBERSHIP : no create request (lots are created by incoming lines) · update (API-INV-042) expiryDate, nameAr, nameEn, version · response all + expiredFl, daysLeft (derived)
LOOKUP FIELDS  : none
CONSTRAINTS    : UQ_INV_LOT_VAR_CODE, UQ_INV_LOT_TENANT_PK, CHK_INV_LOT_CODE
DOMAIN RULES   : RULE-INV-035, 036 (UQ_INV_LOT_VAR_CODE + app), 038 (effect, derived expiredFl), 039 (in-process issue)
STATE MACHINE  : none — isActiveFl is recomputed after the commit of every move of the lot, in its own short transaction: false while the lot holds no stock in any warehouse (QR-INV-019 by lot), true otherwise (engine step 7)
CROSS-MODULE   : XM-INV-009
OPERATIONS     : VIEW, UPDATE
REPOSITORY OPS : QR-INV-038 … QR-INV-041
<!-- SUB:DATA-DOM-MASTER:END -->

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
STATE MACHINE  : statusCode DBF-INV-080 · ACTIVE (initial) → issue with remainder > 0 → ACTIVE · issue with remainder 0 → CONSUMED · release → RELEASED · CONSUMED and RELEASED terminal (CLOSED_AT DBF-INV-074 set) · invalid transition → RULE-INV-021 (INV-409-RESERVATION-CLOSED)
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
<!-- PHASE:DATA-DOM:END -->

<!-- PHASE:SVC-API:START traces=REQ-INV-139,REQ-INV-140,REQ-INV-003,REQ-INV-009,REQ-INV-022,REQ-INV-028,REQ-INV-039,REQ-INV-040,REQ-INV-043,REQ-INV-051,REQ-INV-052,REQ-INV-063,REQ-INV-070,REQ-INV-075,REQ-INV-078,REQ-INV-080,REQ-INV-087,REQ-INV-092,REQ-INV-097,REQ-INV-099,REQ-INV-108,REQ-INV-109,REQ-INV-124,REQ-INV-129,REQ-INV-138,DBF-INV-001,DBF-INV-014,DBF-INV-027,DBF-INV-040,DBF-INV-087,DBF-INV-115,DBF-INV-143,DBF-INV-176,DBF-INV-192,DBF-INV-205 -->
## SVC-API — services and endpoints

52 endpoints (≥ 8) — grouped CRUD (writes), SEARCH (reads), INT (the in-process surface other modules call).
Every endpoint: base path `/api/v1/inv/{resource}`; success wrapped in `ApiResponse<T>`; lists in `Page<T>`;
errors through `LocalizedException → {code, messageAr, messageEn}`; the permission checked before any
processing — `@PreAuthorize("hasAuthority(T(com.commerce.inv.permission.InvPermissions).PERM_…)")` on the
controller method **and** on every public method of the REST-facing service it calls, INV's own constants only,
never a role name (checklist C13, ADR-INV-042).
Services (REST-facing, package `service`): `WarehouseService`, `BranchService`, `StockQueryService`,
`TransferService`, `StockCountService`, `StockAdjustmentService`, `OpeningStockService`, `LotService`,
`ItemStockSettingsService`, `ValuationService`, `InventorySettingsService`, `InventoryContextService`.
Engines (internal, package `engine`, no permission check, no REST path — ADR-INV-042): `StockPostingEngine`
(the posting engine below), `AvailabilityEngine` (availability and bundle derivation), `ReservationEngine`
(reserve, release, consume), `InventoryReadEngine` (the reads `InventoryApi` serves). A REST service reaches an
engine only after its own `@PreAuthorize` has passed.

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
   Variant's on-hand held without a lot in the line's warehouse — read from the lot-less row locked in step 1, REQ-INV-139,
   ADR-INV-051) and RULE-INV-040 (a line
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
   expiry: no lot is resolved by lotNo, none is created, and RULE-INV-035 and RULE-INV-036 are not applied to it
   (REQ-INV-092); a lot-less counted line posts without a lot. Every other increase line of a lot-tracked Item
   resolves or creates its lot from lotNo / lotExpiryDate under RULE-INV-035 and RULE-INV-036 (API-INV-038, 047).
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
Orchestration: load warehouses QR-INV-002 ×2 FOR SHARE (a concurrent deactivation waits — ADR-INV-045) and the active count QR-INV-006 → RULE-INV-016 → per line: Variant integrate (XM-INV-014) — inactive / BUNDLE → RULE-INV-018; Item settings QR-INV-043 — lot-tracked with lotExpiry on → a lot of the Variant (QR-INV-039), or no lot within the lot-less on-hand of the source warehouse (QR-INV-019 with LOT_FK NULL — REQ-INV-139); dual-quantity with dualQuantity on → measure > 0 → duplicates in the request (RULE-INV-048) → docNo DBF-INV-092 from NumberSeriesApi (INV_TRANSFER), statusCode DBF-INV-094 = DRAFT, transferFk DBF-INV-105 on each line, currencyCode DBF-INV-110 = base currency → persist QR-INV-029 → AuditApi; no stock move (AC-INV-085)
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
Orchestration: load QR-INV-028 FOR UPDATE → statusCode DRAFT else RULE-INV-029 → version → validations as API-INV-022 (QR-INV-002 FOR SHARE, QR-INV-006, integrate (XM-INV-014), QR-INV-043, QR-INV-039) → replace header fields and lines (transferFk DBF-INV-105, currencyCode DBF-INV-110 on new lines) → persist QR-INV-029 → AuditApi
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
Orchestration: load QR-INV-031 FOR UPDATE → DRAFT → barcode → integrate (XM-INV-013) (unknown → RULE-INV-032) → Variant integrate (XM-INV-014) → Item settings QR-INV-043 (lot / measure) → lot of the Variant QR-INV-039 → RULE-INV-047 → existing line QR-INV-033 → add countedQty DBF-INV-130 and countedMeasureQty DBF-INV-131 to it, or insert with stockCountFk DBF-INV-134, variantFk DBF-INV-135, lotFk DBF-INV-136 → persist QR-INV-034 → AuditApi
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

<!-- API:API-INV-032:START traces=REQ-INV-139,REQ-INV-092,REQ-INV-093,REQ-INV-090,REQ-INV-068,DBF-INV-117,DBF-INV-119,DBF-INV-122,DBF-INV-124,DBF-INV-028,DBF-INV-131,DBF-INV-132,DBF-INV-133,DBF-INV-144,DBF-INV-146,DBF-INV-147,DBF-INV-148,DBF-INV-149,DBF-INV-150,DBF-INV-151,DBF-INV-159,DBF-INV-162,DBF-INV-163,DBF-INV-166,DBF-INV-167,DBF-INV-168,DBF-INV-171 -->
### API-INV-032 — post stock count
Entity       : ENT-INV-008, ENT-INV-009, ENT-INV-010 · operation UPDATE · screen operations: post
Endpoint     : /api/v1/inv/stock-counts/{id}/post   verb: PATCH
Layers       : StockCountController.post → StockCountService.post → StockAdjustmentService.createAndPost → StockPostingEngine.post
Request      : path id (DBF-INV-115) · body VersionedActionRequest {version DBF-INV-124 (required)} (ADR-INV-032)
Response     : 200 · ApiResponse<StockCountPostResult> {stockCount (with systemQty / differenceQty and, on a dual-quantity line, the derived systemMeasureQty — ADR-INV-054), stockAdjustment (or null when nothing differs)}
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED · RULE-INV-031 → INV-422-NO-LINES · re-checked on post by engine step 0 (ADR-INV-045): RULE-INV-007 → INV-422-WAREHOUSE-INACTIVE, RULE-INV-018 → INV-422-VARIANT-INACTIVE, RULE-INV-037 → INV-422-LOT-REQUIRED (also a lot-less line counted above the lot-less on-hand at posting — REQ-INV-139; a lot-less line's systemQty is that on-hand, so its difference is never positive), RULE-INV-040 → INV-422-MEASURE-REQUIRED · RULE-INV-019 → INV-409-NEGATIVE-STOCK · RULE-INV-025 is not raised: an increase of a never-valued Variant enters at cost 0 and creates no valuation row (ADR-INV-030 as extended), because a count line carries no cost · RULE-INV-033 is not checked: the system writes COUNT_DIFFERENCE itself, so a tenant that deactivated it still posts counts (ADR-INV-047) · RULE-INV-035 and RULE-INV-036 are not applied to a generated increase line, so INV-422-LOT-EXPIRY-REQUIRED and INV-422-LOT-EXPIRY-MISMATCH are never raised here: a positive difference on a counted lot line enters under that line's lotFk — an existing lot with its stored expiry — and no lot is resolved by lotNo or created (REQ-INV-092) · the adjustment-line part of RULE-INV-040 is not applied to a generated line: it carries its count line's measure difference, and the count line already carried the counted measure (step 0) · a line refusal names the count line in `details` (`LINE_PK` = stockCountLinePk, ADR-INV-046)
Errors       : INV-404-STOCK-COUNT-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-422-NO-LINES, INV-422-WAREHOUSE-INACTIVE, INV-422-VARIANT-INACTIVE, INV-422-LOT-REQUIRED, INV-422-MEASURE-REQUIRED, INV-409-NEGATIVE-STOCK, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-031 FOR UPDATE → DRAFT → version check (DBF-INV-124) → ≥ 1 line → warehouse QR-INV-002 FOR SHARE, Item stock settings QR-INV-043 FOR SHARE → engine step 0 on the count and its lines → lock each pair's lot-less balance row, then each line's lot row (QR-INV-017) → systemQty DBF-INV-132 = on-hand, differenceQty DBF-INV-133 = counted − system (QR-INV-034); **measure rule** — while dualQuantity is on, for a line of a dual-quantity Item: measure difference = countedMeasureQty DBF-INV-131 − COALESCE(ON_HAND_MEASURE_QTY DBF-INV-028, 0) of the same balance row, read under the same balance lock; the system measure is not stored — it is derived on read as countedMeasureQty − the generated line's measureQtyChange (ADR-INV-054, SRS D-15) → one adjustment line per count line whose differenceQty ≠ 0 or whose measure difference ≠ 0: create adjustment (QR-INV-037) reasonCode DBF-INV-144 = COUNT_DIFFERENCE, warehouseFk DBF-INV-147 = the count's, sourceCountFk DBF-INV-148, docNo DBF-INV-149 (INV_ADJUSTMENT), docDate DBF-INV-150, lines rowNo DBF-INV-159, quantityChange DBF-INV-162 = difference, measureQtyChange DBF-INV-163 = the measure difference (null when it is 0 or the Item is not dual-quantity), stockAdjustmentFk DBF-INV-166, variantFk DBF-INV-167, lotFk DBF-INV-168 = the count line's lotFk (lotNo and lotExpiryDate stay empty), currencyCode DBF-INV-171 → post it through the posting engine with countGeneratedFl and no reason check (ADR-INV-047) (ADJUSTMENT_IN / ADJUSTMENT_OUT at the average; an increase line uses its lotFk with the lot's stored expiry, with no RULE-INV-035 / 036 check and no lot created — engine step 3; a line with differenceQty 0 and a non-zero measure difference is a measure-only move, quantity 0, measure |difference|, value 0, ADJUSTMENT_IN when the difference is positive and ADJUSTMENT_OUT when negative — ADR-INV-017, RULE-INV-034 satisfied by the non-zero measure change; a line with both non-zero moves both; an ADJUSTMENT_IN of a never-valued Variant at 0 with no valuation row — ADR-INV-030; sourceDocTypeCode INV_ADJUSTMENT) → adjustment statusCode DBF-INV-151 = POSTED, postedAt DBF-INV-146 → count statusCode DBF-INV-122 = POSTED, postedAt DBF-INV-117, adjustmentFk DBF-INV-119 → persist QR-INV-032 → AuditApi → events
Repository   : QR-INV-031, QR-INV-002, QR-INV-043, QR-INV-017, QR-INV-018, QR-INV-019, QR-INV-022, QR-INV-023, QR-INV-024, QR-INV-034, QR-INV-037, QR-INV-032 · join NONE · READ_WRITE
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
Orchestration: warehouse as API-INV-028 (QR-INV-002 FOR SHARE, QR-INV-006, QR-INV-005) → docDate not after today (RULE-INV-051) → reasonCode active in INV_ADJUSTMENT_REASON (MdlLookupApi) and not OPENING_STOCK → per line: Variant integrate (XM-INV-014), Item settings QR-INV-043 → docNo DBF-INV-149 from NumberSeriesApi (INV_ADJUSTMENT), statusCode DBF-INV-151 = DRAFT, stockAdjustmentFk DBF-INV-166, currencyCode DBF-INV-171 → persist QR-INV-037 → AuditApi; no stock move (AC-INV-107)
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
Orchestration: load QR-INV-036 FOR UPDATE → DRAFT → version → validations (MdlLookupApi, integrate (XM-INV-014), QR-INV-043) → replace reason, note, lines (stockAdjustmentFk DBF-INV-166, currencyCode DBF-INV-171) → persist QR-INV-037 → AuditApi (line change 2 → 3 recorded — AC-INV-084)
Repository   : QR-INV-036, QR-INV-019, QR-INV-043, QR-INV-037 · join NONE · READ_WRITE
Concurrency  : header lock + VERSION
Security     : SCR-REQ-INV-007 · PERM_INV_ADJUSTMENTS_UPDATE
Localization : ar + en
<!-- API:API-INV-037:END -->

<!-- API:API-INV-038:START traces=REQ-INV-139,REQ-INV-099,REQ-INV-102,REQ-INV-114,REQ-INV-123,REQ-INV-067,REQ-INV-068,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-036,REQ-INV-057,REQ-INV-059,REQ-INV-060,REQ-INV-061,DBF-INV-146,DBF-INV-151,DBF-INV-153,DBF-INV-165,DBF-INV-168,DBF-INV-177,DBF-INV-178,DBF-INV-180,DBF-INV-181,DBF-INV-184,DBF-INV-027,DBF-INV-029,DBF-INV-030,DBF-INV-031,DBF-INV-033,DBF-INV-041,DBF-INV-042,DBF-INV-044,DBF-INV-045,DBF-INV-046,DBF-INV-047,DBF-INV-048,DBF-INV-049,DBF-INV-051,DBF-INV-052,DBF-INV-220,DBF-INV-053,DBF-INV-054,DBF-INV-058,DBF-INV-061,DBF-INV-192,DBF-INV-193,DBF-INV-194,DBF-INV-195,DBF-INV-196,DBF-INV-197,DBF-INV-200 -->
### API-INV-038 — post stock adjustment
Entity       : ENT-INV-010, ENT-INV-011 · operation UPDATE · screen operations: post
Endpoint     : /api/v1/inv/stock-adjustments/{id}/post   verb: PATCH
Layers       : StockAdjustmentController.post → StockAdjustmentService.post → StockPostingEngine.post
Request      : path id (DBF-INV-143) · body VersionedActionRequest {version DBF-INV-153 (required)} (ADR-INV-032)
Response     : 200 · ApiResponse<StockAdjustmentDto> (POSTED, line lots and values)
Validations  : RULE-INV-029 → INV-409-DOCUMENT-CLOSED · RULE-INV-031 → INV-422-NO-LINES · RULE-INV-033 → INV-422-REASON-REQUIRED (also a stored OPENING_STOCK — ADR-INV-047) · RULE-INV-034 → INV-422-ZERO-CHANGE · re-checked on post by engine step 0 (ADR-INV-045): RULE-INV-007 → INV-422-WAREHOUSE-INACTIVE, RULE-INV-018 → INV-422-VARIANT-INACTIVE, RULE-INV-037 → INV-422-LOT-REQUIRED, RULE-INV-040 → INV-422-MEASURE-REQUIRED (a non-zero quantity change of a dual-quantity Item without a measure change) · RULE-INV-019 → INV-409-NEGATIVE-STOCK · RULE-INV-025 → INV-422-NO-KNOWN-COST · RULE-INV-026 → INV-422-OPENING-COST-REQUIRED · RULE-INV-035 → INV-422-LOT-EXPIRY-REQUIRED · RULE-INV-036 → INV-422-LOT-EXPIRY-MISMATCH · RULE-INV-037 → INV-422-LOT-REQUIRED (a decrease naming no existing lot, or naming none beyond the lot-less on-hand at posting — REQ-INV-139) — a line refusal names its line in `details` (`LINE_PK` = stockAdjustmentLinePk, with rowNo — ADR-INV-046)
Errors       : INV-404-STOCK-ADJUSTMENT-NOT-FOUND, INV-409-DOCUMENT-CLOSED, INV-422-NO-LINES, INV-422-REASON-REQUIRED, INV-422-ZERO-CHANGE, INV-422-WAREHOUSE-INACTIVE, INV-422-VARIANT-INACTIVE, INV-422-MEASURE-REQUIRED, INV-409-NEGATIVE-STOCK, INV-422-NO-KNOWN-COST, INV-422-OPENING-COST-REQUIRED, INV-422-LOT-EXPIRY-REQUIRED, INV-422-LOT-EXPIRY-MISMATCH, INV-422-LOT-REQUIRED, INV-409-STALE-VERSION, INV-400, INV-401, INV-403, INV-500
Orchestration: load QR-INV-036 FOR UPDATE → DRAFT → version check (DBF-INV-153) → ≥ 1 line → reason still active and not OPENING_STOCK (MdlLookupApi — RULE-INV-033) → engine step 0 on the header and lines → OPENING_STOCK → RULE-INV-026 on every line → per line (Item settings QR-INV-043 FOR SHARE): increase with lotNo → QR-INV-040; existing lot → expiry equal else RULE-INV-036; new → lotExpiryDate required (RULE-INV-035) and lot created QR-INV-041 (expiryDate DBF-INV-177, firstReceivedAt DBF-INV-178, variantFk DBF-INV-180, code DBF-INV-181, isActiveFl DBF-INV-184); lot-tracked increase without lotNo while lotExpiry is on → RULE-INV-035; decrease with lotNo → existing lot else RULE-INV-037 → lotFk DBF-INV-168 → warehouse QR-INV-002 FOR SHARE → posting engine: positive → ADJUSTMENT_IN at the line cost or the average (REQ-INV-102), negative → ADJUSTMENT_OUT at the average, measure-only → ADR-INV-017; an expired lot may leave by this path (REQ-INV-114, EXPIRY_WRITE_OFF); moves DBF-INV-041, DBF-INV-042, DBF-INV-044, DBF-INV-045, DBF-INV-046, DBF-INV-047 (INV_ADJUSTMENT), DBF-INV-048, DBF-INV-049, DBF-INV-051, DBF-INV-052 (= INV_ADJUSTMENT:{stockAdjustmentPk}), requestHash DBF-INV-220 (= SHA-256 of that key, ADR-INV-048), DBF-INV-053, DBF-INV-054, DBF-INV-058, DBF-INV-061; balances DBF-INV-027, DBF-INV-029, DBF-INV-030, DBF-INV-031, DBF-INV-033; valuation DBF-INV-192, DBF-INV-193, DBF-INV-194, DBF-INV-195, DBF-INV-196, DBF-INV-197, DBF-INV-200 → lineValue DBF-INV-165 = |quantity| × unit cost → statusCode DBF-INV-151 = POSTED, postedAt DBF-INV-146 → persist QR-INV-037 → AuditApi → events
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

<!-- API:API-INV-047:START traces=REQ-INV-140,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-074,REQ-INV-064,REQ-INV-105,REQ-INV-106,REQ-INV-059,DBF-INV-144,DBF-INV-147,DBF-INV-149,DBF-INV-150,DBF-INV-151,DBF-INV-159,DBF-INV-160,DBF-INV-161,DBF-INV-162,DBF-INV-163,DBF-INV-164,DBF-INV-166,DBF-INV-167,DBF-INV-171,DBF-INV-177,DBF-INV-178,DBF-INV-180,DBF-INV-181,DBF-INV-184,DBF-INV-027,DBF-INV-029,DBF-INV-030,DBF-INV-031,DBF-INV-033,DBF-INV-041,DBF-INV-042,DBF-INV-044,DBF-INV-045,DBF-INV-046,DBF-INV-047,DBF-INV-048,DBF-INV-049,DBF-INV-051,DBF-INV-052,DBF-INV-220,DBF-INV-053,DBF-INV-054,DBF-INV-058,DBF-INV-061,DBF-INV-192,DBF-INV-193,DBF-INV-194,DBF-INV-195,DBF-INV-196,DBF-INV-197,DBF-INV-200 -->
### API-INV-047 — create (post), custom (download sheet, bulk load) — post opening stock
Entity       : ENT-INV-010, ENT-INV-011 · operation CREATE · screen operations: create (post), custom (download sheet, bulk load)
Endpoint     : /api/v1/inv/opening-stock   verb: POST
Layers       : OpeningStockController.post → OpeningStockService.post → StockAdjustmentService.createAndPost → StockPostingEngine.post
Request      : body OpeningStockRequest {idempotencyKey (required, text 1–50, generated by the screen once per load and kept across its retries; not a column — the load's moves record `OPENING_STOCK:<idempotencyKey>` in DBF-INV-052, ADR-INV-053), warehousePk DBF-INV-147 (optional while one is active), docDate DBF-INV-150 (optional — today), rows[] (1–1000 — RULE-INV-027) {rowNo DBF-INV-159 (required), variantPk DBF-INV-167 or barcode (one required — ADR-INV-027), quantity → quantityChange DBF-INV-162 (> 0), unitCost DBF-INV-164 (required, ≥ 0, ≤ 4 dp), lotNo DBF-INV-160, lotExpiryDate DBF-INV-161, measureQty → measureQtyChange DBF-INV-163}}. **Variant resolution, stated once (ADR-INV-035):** `barcode` is resolved through XM-INV-013; a row with `variantPk` is read through XM-INV-014. CAT v2 publishes no exact Variant-code read, so v1 accepts no Variant code: the sheet column is «الباركود» / "Barcode" (AC-INV-080), and a text that is not an active barcode fails its row with INV-422-LOAD-ROW-VARIANT. The Variant-code read is a platform finding owned by CAT (business-policies-inv PLATFORM FINDINGS); when CAT publishes it, an INV delta adds the fallback "barcode not found → exact Variant code" here and in XM-INV-013 / 014
Response     : 201 · ApiResponse<StockAdjustmentDto> (POSTED, reasonCode OPENING_STOCK)
Validations  : RULE-INV-027 → INV-422-LOAD-ROWS · per row (all rows checked, all failures returned in details, nothing posted — REQ-INV-072 → INV-422-LOAD-ROWS-INVALID): RULE-INV-028 → INV-422-LOAD-ROW-VARIANT · RULE-INV-018 → INV-422-VARIANT-INACTIVE · RULE-INV-017 → INV-422-QUANTITY-INVALID (quantity and measure ≤ 3 dp) · RULE-INV-025 → INV-422-NO-KNOWN-COST (unitCost with more than 4 dp) · RULE-INV-026 → INV-422-OPENING-COST-REQUIRED · RULE-INV-035 → INV-422-LOT-EXPIRY-REQUIRED · RULE-INV-036 → INV-422-LOT-EXPIRY-MISMATCH · RULE-INV-040 → INV-422-MEASURE-REQUIRED · RULE-INV-048 → INV-422-DUPLICATE-LINE · RULE-INV-007 / 008 on the warehouse · RULE-INV-051 → INV-422-DOC-DATE-FUTURE (docDate after today) · RULE-INV-033 is not checked: the system writes OPENING_STOCK itself (ADR-INV-047) · RULE-INV-024 → the first answer (201, the first load's adjustment, nothing recorded) for a key already recorded with the same load hash · RULE-INV-050 → INV-409-IDEMPOTENCY-KEY-REUSED for a different hash (ADR-INV-053)
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
Response     : 200 · ApiResponse<Page<StockMoveDto>> · paginated — each move with lotCode (label of lotPk), sourceDocTypeCode DBF-INV-047 and sourceDocNo, quantity DBF-INV-042, measureQty DBF-INV-043, balanceAfterQty DBF-INV-046 (displayed when filtered to one Variant and one warehouse — REQ-INV-076); the FEFO split, lot-less shortfall and lot-less-first issues of REQ-INV-110 … 112 are visible here as separate moves
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

<!-- SUB:SVC-API-INT:START traces=REQ-INV-019,REQ-INV-033,REQ-INV-040,REQ-INV-043,REQ-INV-044,REQ-INV-045,REQ-INV-046,REQ-INV-047,REQ-INV-048,REQ-INV-049,REQ-INV-050,REQ-INV-051,REQ-INV-052,REQ-INV-053,REQ-INV-058,REQ-INV-063,REQ-INV-069,REQ-INV-109,REQ-INV-110,REQ-INV-111,REQ-INV-112,REQ-INV-113,REQ-INV-114,REQ-INV-129,REQ-INV-130,REQ-INV-131,REQ-INV-057,REQ-INV-034,DBF-INV-066,DBF-INV-067,DBF-INV-068,DBF-INV-069,DBF-INV-075,DBF-INV-080,DBF-INV-052 -->
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

**Release** (REQ-INV-046, 048). Reservation by PK FOR UPDATE (R-4) → not ACTIVE → INV-409-RESERVATION-CLOSED
(RULE-INV-021) → statusCode RELEASED, reservedQty 0, closedAt DBF-INV-074 = now (R-5).

**Receive** (REQ-INV-051, 059, 064, 105 … 107, 120). Idempotency: advisory lock, then moves with this key (R-6)
→ return them (or INV-409-IDEMPOTENCY-KEY-REUSED on a different request hash). Resolve (a bundle Variant is refused,
see "Common resolution") → per line: Item settings QR-INV-043 FOR SHARE — lot-tracked with lotExpiry on → lot number and expiry required
(RULE-INV-035), lot reused or created (QR-INV-040 / 041; different expiry → RULE-INV-036); dual-quantity with
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
<!-- PHASE:SVC-API:END -->

<!-- PHASE:DOC:START traces=REQ-INV-009,REQ-INV-039,REQ-INV-057,REQ-INV-075,REQ-INV-129,DBF-INV-001,DBF-INV-040 -->
## DOC — API documentation

- **The API document** is `api-spec-inv.yaml` (OpenAPI 3.1.0), derived in this stage from the 52 `API-*` blocks
  and the Error Catalog: one operation per block (`operationId` = the block's operation, `x-api-id` = its id,
  `x-traces` = its traces), the request / response schemas from its Request / Response lines (field types from
  the DBF types via CORE R1), one error response per catalog row naming the block (`x-error-codes`), paging
  `page` / `size` with `x-paginated: true`, bearer JWT on every operation. The five in-process-only codes of §5.1
  sit under the document-level `x-inprocess-error-codes`. The frontend binds to this document and mocks it; the
  test stage cites it.
- **Schema bounds (derivation rule, ADR-INV-038).** A bound that a catalogue code owns is **described, not
  enforced**, in the schema: it is stated in the property's `description` with its code, and carries no
  `minimum`, `exclusiveMinimum`, `maximum`, `minItems` or `maxItems`, so a validating server or mock never
  answers INV-400 before the rule's 422 code. This covers OpeningStockRequest `rows` (1–1000, INV-422-LOAD-ROWS),
  row `rowNo`, `quantity` and `unitCost` (INV-422-LOAD-ROWS-INVALID with the row's own code), TransferLineRequest
  `quantity` (INV-422-QUANTITY-INVALID), StockAdjustmentLineRequest `rowNo` and `unitCost`, StockCountLine
  `countedQty` and `countedMeasureQty` (INV-422-COUNTED-NEGATIVE) and InventorySettings `nearExpiryDays`
  (INV-422-HORIZON-RANGE). Type, format, `maxLength` of a text and the paging bounds of `page` / `size`, which no
  catalogue code owns, stay enforced (INV-400).
- **Packages and manifest.** The code lives under `com.commerce.inv` (CORE); the module manifest
  `META-INF/erp/modules/inv.yaml` declares the api-paths `/api/v1/inv/**` this document covers.
- **The delivered backend** publishes its api-docs with springdoc as the group `inv` (paths `/api/v1/inv/**`);
  each controller method carries `@Operation(operationId = …, summary = <block heading>)`, `@ApiResponse` per
  catalog status, and the `x-api-id` extension, so `api-verify` can hold the published document to
  `api-spec-inv.yaml` operation by operation.
- **In-process surface.** `InventoryApi` carries Javadoc per method naming the contract item it honours
  (CON-INV-006 … 019) and the error codes it can throw; the two events carry their payload description
  (CON-INV-020, 021).
- **Error texts.** The message bundles `messages.properties` (EN) and `messages_ar.properties` (AR) — checklist C11
  — key every code of §5 / §5.1, the four generic codes included, to its texts verbatim (ADR-INV-021); downstream
  documents cite codes, never the text.
<!-- PHASE:DOC:END -->

<!-- PHASE:SEC-BE:START traces=REQ-INV-136,REQ-INV-137,REQ-INV-138,DBF-INV-001,DBF-INV-014 -->
## SEC-BE — security (backend half)

Security model: one `SEC_PAGES` row per composite screen, registered by
`com.commerce.inv.permission.InvPermissions` (PermissionContributor, permission module `INV`); the security
module derives `PERM_<PAGE_CODE>_<ACTION>` for VIEW / CREATE / UPDATE / DELETE, VIEW being the gateway. Every
endpoint verifies its permission before any processing, and so does every public method of the REST-facing
service it calls: both carry
`@PreAuthorize("hasAuthority(T(com.commerce.inv.permission.InvPermissions).PERM_…)")` with the permission its
API block's Security line names (also listed in registry-exec-be "Endpoints"), INV's own constants only
(checklist C13). API-INV-008 carries
`hasAnyAuthority(…)` over the twelve VIEW constants, and so does API-INV-052 (the INV lookup keys every INV screen reads); API-INV-045 carries `hasAnyAuthority(…_CREATE, …_UPDATE)` and
the service requires CREATE when the record is absent and UPDATE when it exists (ADR-INV-029). A refusal answers
INV-403 through `LocalizedException → {code, messageAr, messageEn}` (REQ-INV-138). No service checks a role
name.

**In-process surface (ADR-INV-042).** `InventoryApi` (CON-INV-006 … 019) delegates to the engines of
`com.commerce.inv.engine`, which carry no permission check and have no REST path; it never calls a REST-facing
service. Every one of its fourteen methods therefore bypasses the INV permission check, because the calling
module authorises its own caller (SRS access summary; a POS cashier holds only PERM_INV_STOCK_BALANCES_VIEW).
It follows CORE-GAP-in-process-caller-authority when the core answers.

| Page code | Name ar / en | Parent | Screen |
|---|---|---|---|
| INV_WAREHOUSES | المستودعات / Warehouses | Inventory → Setup | SCR-REQ-INV-001 |
| INV_BRANCHES | الفروع / Branches | Inventory → Setup | SCR-REQ-INV-002 |
| INV_STOCK_BALANCES | أرصدة المخزون / Stock balances | Inventory | SCR-REQ-INV-003 |
| INV_STOCK_MOVES | سجل حركات المخزون / Stock move history | Inventory | SCR-REQ-INV-004 |
| INV_TRANSFERS | التحويلات بين المستودعات / Transfers | Inventory | SCR-REQ-INV-005 |
| INV_COUNTS | جرد المخزون / Stock counts | Inventory | SCR-REQ-INV-006 |
| INV_ADJUSTMENTS | تسويات المخزون / Stock adjustments | Inventory | SCR-REQ-INV-007 |
| INV_LOTS | التشغيلات وقرب انتهاء الصلاحية / Lots and near expiry | Inventory | SCR-REQ-INV-008 |
| INV_ITEM_STOCK_SETTINGS | إعدادات مخزون الصنف / Item stock settings | Inventory → Setup | SCR-REQ-INV-009 |
| INV_OPENING_STOCK | المخزون الافتتاحي / Opening stock | Inventory → Setup | SCR-REQ-INV-010 |
| INV_SETTINGS | إعدادات المخزون / Inventory settings | Inventory → Setup | SCR-REQ-INV-011 |
| INV_STOCK_VALUATION | تقييم المخزون / Stock valuation | Inventory | SCR-REQ-INV-012 |

```yaml name=permission-matrix
rows:
  - screen: SCR-REQ-INV-001 Warehouses (INV_WAREHOUSES)
    entity: ENT-INV-001
    api: [API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008]
    actions:
      VIEW: {granted: true, permission: PERM_INV_WAREHOUSES_VIEW}
      CREATE: {granted: true, permission: PERM_INV_WAREHOUSES_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_WAREHOUSES_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_WAREHOUSES_DELETE}
  - screen: SCR-REQ-INV-002 Branches (INV_BRANCHES)
    entity: ENT-INV-002
    api: [API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015]
    actions:
      VIEW: {granted: true, permission: PERM_INV_BRANCHES_VIEW}
      CREATE: {granted: true, permission: PERM_INV_BRANCHES_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_BRANCHES_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_BRANCHES_DELETE}
  - screen: SCR-REQ-INV-003 Stock balances (INV_STOCK_BALANCES)
    entity: ENT-INV-003
    api: [API-INV-016, API-INV-017, API-INV-008]
    actions:
      VIEW: {granted: true, permission: PERM_INV_STOCK_BALANCES_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-004 Stock move history (INV_STOCK_MOVES)
    entity: ENT-INV-004
    api: [API-INV-018, API-INV-019, API-INV-052]
    actions:
      VIEW: {granted: true, permission: PERM_INV_STOCK_MOVES_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-005 Transfers (INV_TRANSFERS)
    entity: ENT-INV-006
    api: [API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-052]
    actions:
      VIEW: {granted: true, permission: PERM_INV_TRANSFERS_VIEW}
      CREATE: {granted: true, permission: PERM_INV_TRANSFERS_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_TRANSFERS_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_TRANSFERS_DELETE}
  - screen: SCR-REQ-INV-006 Stock counts (INV_COUNTS)
    entity: ENT-INV-008
    api: [API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-052]
    actions:
      VIEW: {granted: true, permission: PERM_INV_COUNTS_VIEW}
      CREATE: {granted: true, permission: PERM_INV_COUNTS_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_COUNTS_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_COUNTS_DELETE}
  - screen: SCR-REQ-INV-007 Stock adjustments (INV_ADJUSTMENTS)
    entity: ENT-INV-010
    api: [API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-052]
    actions:
      VIEW: {granted: true, permission: PERM_INV_ADJUSTMENTS_VIEW}
      CREATE: {granted: true, permission: PERM_INV_ADJUSTMENTS_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_ADJUSTMENTS_UPDATE}
      DELETE: {granted: true, permission: PERM_INV_ADJUSTMENTS_DELETE}
  - screen: SCR-REQ-INV-008 Lots and near expiry (INV_LOTS)
    entity: ENT-INV-012
    api: [API-INV-040, API-INV-041, API-INV-042]
    actions:
      VIEW: {granted: true, permission: PERM_INV_LOTS_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: true, permission: PERM_INV_LOTS_UPDATE}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-009 Item stock settings (INV_ITEM_STOCK_SETTINGS)
    entity: ENT-INV-014
    api: [API-INV-043, API-INV-044, API-INV-045]
    actions:
      VIEW: {granted: true, permission: PERM_INV_ITEM_STOCK_SETTINGS_VIEW}
      CREATE: {granted: true, permission: PERM_INV_ITEM_STOCK_SETTINGS_CREATE}
      UPDATE: {granted: true, permission: PERM_INV_ITEM_STOCK_SETTINGS_UPDATE}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-010 Opening stock (INV_OPENING_STOCK)
    entity: ENT-INV-010
    api: [API-INV-046, API-INV-047]
    actions:
      VIEW: {granted: true, permission: PERM_INV_OPENING_STOCK_VIEW}
      CREATE: {granted: true, permission: PERM_INV_OPENING_STOCK_CREATE}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-011 Inventory settings (INV_SETTINGS)
    entity: ENT-INV-004
    api: [API-INV-048, API-INV-049]
    actions:
      VIEW: {granted: true, permission: PERM_INV_SETTINGS_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: true, permission: PERM_INV_SETTINGS_UPDATE}
      DELETE: {granted: false}
  - screen: SCR-REQ-INV-012 Stock valuation (INV_STOCK_VALUATION)
    entity: ENT-INV-013
    api: [API-INV-050, API-INV-051]
    actions:
      VIEW: {granted: true, permission: PERM_INV_STOCK_VALUATION_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: false}
      DELETE: {granted: false}
```

SCR-REQ-INV-011 has no entity of its own (given cu settings); its row names ENT-INV-004, whose moves the
negative-stock setting governs (REQ-INV-065). Role-preset grants (SRS B4 / access summary — REQ-INV-136, 137):

| Role preset | Permissions granted |
|---|---|
| Owner | all 32 permissions below |
| Warehouse | PERM_INV_WAREHOUSES_VIEW, PERM_INV_BRANCHES_VIEW, PERM_INV_STOCK_BALANCES_VIEW, PERM_INV_STOCK_MOVES_VIEW, PERM_INV_TRANSFERS_VIEW / _CREATE / _UPDATE / _DELETE, PERM_INV_COUNTS_VIEW / _CREATE / _UPDATE / _DELETE, PERM_INV_ADJUSTMENTS_VIEW / _CREATE / _UPDATE / _DELETE, PERM_INV_LOTS_VIEW, PERM_INV_ITEM_STOCK_SETTINGS_VIEW |
| Seller/Cashier | PERM_INV_STOCK_BALANCES_VIEW |

**Bootstrap data** — the rows that must exist before any endpoint can succeed, each with who produces it:

```yaml name=bootstrap
rows:
  - {kind: lookup key, name: "INV_ADJUSTMENT_REASON", source: "db-script BLOCK 8a/8b (V1002__inv_inventory.sql) into the given MDL_LOOKUP_TYPE / MDL_LOOKUP_VALUE for TENANT_ID 1; copied to each new tenant by the given mdl provisioning", note: "5 values, open; COUNT_DIFFERENCE and OPENING_STOCK are written by the system without the tenant-active check (ADR-INV-047); served to INV screens by InvLookups (API-INV-052)"}
  - {kind: lookup key, name: "INV_MOVE_TYPE", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "8 values, closed"}
  - {kind: lookup key, name: "INV_DOC_STATUS", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "3 values, closed"}
  - {kind: lookup key, name: "INV_RESERVATION_STATUS", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "3 values, closed"}
  - {kind: lookup key, name: "INV_SOURCE_DOC_TYPE", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "10 values, open — calling modules add their types as data"}
  - {kind: lookup key, name: "INV_CHANGE_KIND", source: "db-script BLOCK 8a/8b; given mdl provisioning per tenant", note: "5 values, event field"}
  - {kind: number series, name: "INV_WAREHOUSE", source: "db-script BLOCK 8c into the given CORE_NUMBER_SERIES; given sequence provisioning per tenant", note: "WH-{SEQ:4}"}
  - {kind: number series, name: "INV_BRANCH", source: "db-script BLOCK 8c; given sequence provisioning per tenant", note: "BR-{SEQ:4}"}
  - {kind: number series, name: "INV_TRANSFER", source: "db-script BLOCK 8c; given sequence provisioning per tenant", note: "TR-{SEQ:4}"}
  - {kind: number series, name: "INV_COUNT", source: "db-script BLOCK 8c; given sequence provisioning per tenant", note: "CNT-{SEQ:4}"}
  - {kind: number series, name: "INV_ADJUSTMENT", source: "db-script BLOCK 8c; given sequence provisioning per tenant", note: "ADJ-{SEQ:4}"}
  - {kind: notification template, name: "INV_NEGATIVE_STOCK", source: "db-script BLOCK 8d into the given NOTIF_TEMPLATE; given notif provisioning per tenant"}
  - {kind: notification template, name: "INV_NEAR_EXPIRY", source: "db-script BLOCK 8d; given notif provisioning per tenant"}
  - {kind: notification template, name: "INV_OFFLINE_SALE_INACTIVE", source: "db-script BLOCK 8d; given notif provisioning per tenant", note: "an offline sale recorded against an inactive warehouse, Variant or Item (ADR-INV-052)"}
  - {kind: setting, name: "INV_ALLOW_NEGATIVE_STOCK", source: "db-script BLOCK 8e platform default (true) in the given CU_APP_CONFIGURATION; tenants inherit until API-INV-049 saves an override"}
  - {kind: setting, name: "INV_NEAR_EXPIRY_DAYS", source: "db-script BLOCK 8e platform default (30); tenants inherit until API-INV-049 saves an override"}
  - {kind: setting, name: "INV_MEASURE_UOM_KEY", source: "db-script BLOCK 8e platform default (CAT_UOM, the key CAT SRS A6 declares); every tenant inherits it, no INV endpoint writes it (ADR-INV-043)", note: "the key of the consumed unit-of-measure lookup; CAT is asked to publish it in contract-cat"}
  - {kind: report, name: "INV_OPENING_STOCK_SHEET", source: "InvReports (ReportProvider) registers the report at start-up; rendered by PrintApi (API-INV-046)"}
  - {kind: master row, name: "default branch «الفرع الرئيسي» / Main branch", source: "InvTenantProvisioning (TenantProvisioningContributor) at tenant provisioning, idempotent (REQ-INV-020, 021)"}
  - {kind: master row, name: "default warehouse «المستودع الرئيسي» / Main warehouse", source: "InvTenantProvisioning (TenantProvisioningContributor) at tenant provisioning, under the default branch, idempotent (REQ-INV-001, 002)"}
  - {kind: permission, name: "PERM_INV_WAREHOUSES_VIEW", source: "InvPermissions (PermissionContributor) registers page INV_WAREHOUSES; the given sec derives the permission; the preset grant is seeded at tenant provisioning by the same contributor", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_WAREHOUSES_CREATE", source: "InvPermissions (PermissionContributor) — page INV_WAREHOUSES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_WAREHOUSES_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_WAREHOUSES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_WAREHOUSES_DELETE", source: "InvPermissions (PermissionContributor) — page INV_WAREHOUSES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_BRANCHES_VIEW", source: "InvPermissions (PermissionContributor) — page INV_BRANCHES; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_BRANCHES_CREATE", source: "InvPermissions (PermissionContributor) — page INV_BRANCHES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_BRANCHES_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_BRANCHES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_BRANCHES_DELETE", source: "InvPermissions (PermissionContributor) — page INV_BRANCHES; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_STOCK_BALANCES_VIEW", source: "InvPermissions (PermissionContributor) — page INV_STOCK_BALANCES; preset grant at provisioning", note: "role: Owner, Warehouse, Seller/Cashier"}
  - {kind: permission, name: "PERM_INV_STOCK_MOVES_VIEW", source: "InvPermissions (PermissionContributor) — page INV_STOCK_MOVES; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_TRANSFERS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_TRANSFERS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_TRANSFERS_CREATE", source: "InvPermissions (PermissionContributor) — page INV_TRANSFERS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_TRANSFERS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_TRANSFERS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_TRANSFERS_DELETE", source: "InvPermissions (PermissionContributor) — page INV_TRANSFERS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_COUNTS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_COUNTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_COUNTS_CREATE", source: "InvPermissions (PermissionContributor) — page INV_COUNTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_COUNTS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_COUNTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_COUNTS_DELETE", source: "InvPermissions (PermissionContributor) — page INV_COUNTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ADJUSTMENTS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_ADJUSTMENTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ADJUSTMENTS_CREATE", source: "InvPermissions (PermissionContributor) — page INV_ADJUSTMENTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ADJUSTMENTS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_ADJUSTMENTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ADJUSTMENTS_DELETE", source: "InvPermissions (PermissionContributor) — page INV_ADJUSTMENTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_LOTS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_LOTS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_LOTS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_LOTS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_ITEM_STOCK_SETTINGS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_ITEM_STOCK_SETTINGS; preset grant at provisioning", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_INV_ITEM_STOCK_SETTINGS_CREATE", source: "InvPermissions (PermissionContributor) — page INV_ITEM_STOCK_SETTINGS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_ITEM_STOCK_SETTINGS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_ITEM_STOCK_SETTINGS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_OPENING_STOCK_VIEW", source: "InvPermissions (PermissionContributor) — page INV_OPENING_STOCK; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_OPENING_STOCK_CREATE", source: "InvPermissions (PermissionContributor) — page INV_OPENING_STOCK; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_SETTINGS_VIEW", source: "InvPermissions (PermissionContributor) — page INV_SETTINGS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_SETTINGS_UPDATE", source: "InvPermissions (PermissionContributor) — page INV_SETTINGS; preset grant at provisioning", note: "role: Owner"}
  - {kind: permission, name: "PERM_INV_STOCK_VALUATION_VIEW", source: "InvPermissions (PermissionContributor) — page INV_STOCK_VALUATION; preset grant at provisioning", note: "role: Owner"}
```

The frontend references these permission names; it does not redeclare them.
<!-- PHASE:SEC-BE:END -->

<!-- PHASE:ALIGN-BE:START traces=REQ-INV-001,REQ-INV-138,DBF-INV-001,DBF-INV-219 -->
## ALIGN-BE — alignment self-check

Each row's mark is the `gov.py analyze` result for its backing check, copied by the orchestrator from the
report; this stage does not adjudicate them.

```
ALIGN — INV v1
row               backing check        assertion                                                                 mark
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF   (report)
COVERED           orphans              every REQ is covered by ≥1 API or DBF                                      (report)
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares   (report)
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads                      (report)
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not (report)
QRC (§5)          orphans              every catalogued query is reached by ≥1 API                                (report)
API (R3)          code-format          every catalog code is an instance of the declared format with a platform status (report)
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-inv.yaml and every operation one block (report)
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation with its status and code     (report)
DOCUMENT VALID    api-spec-valid       api-spec-inv.yaml validates against OPENAPI 3.1.0 and reaches every required item (report)
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from    (report)
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered (report)
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase                         (report)
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's registry (report)
BOOTSTRAP         bootstrap-complete   every lookup key and every permission has a row naming who produces it     (report)
LOOKUPS (C6)      checklist C6         InvLookups serves exactly INV's six owned keys through OwnedLookups.read (API-INV-052) and no other key  (report)
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and permission (report)
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why not (report)
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/INV/        (report)
PATHS             paths-resolve        every path the generated manifest and execution state emit resolves        (report)
COVERAGE          (the report)         the clauses the analyze report lists as having examined nothing            (report)
```

```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C7.24
```

**Coverage — XM → requires → tests**

| XM | Type | requires | tests |
|---|---|---|---|
| XM-INV-001 … XM-INV-010 | HARD-FK (Variant columns) | target DELIVERED | AC ids of each block |
| XM-INV-011 | HARD-FK (Item column) | target DELIVERED | AC-INV-138, AC-INV-140, AC-INV-145 |
| XM-INV-012 | SOFT-READ (bundle components) | target DELIVERED | AC-INV-047, AC-INV-054, AC-INV-059, AC-INV-060 |
| XM-INV-013 | SOFT-READ (barcode) | target DELIVERED | AC-INV-079, AC-INV-097, AC-INV-101 |
| XM-INV-014 | SOFT-READ (Variant read, minted) | target DELIVERED | AC-INV-058, AC-INV-079, AC-INV-085, AC-INV-107 |
| XM-INV-015 | SOFT-READ (Item read, minted) | target DELIVERED | AC-INV-139, AC-INV-142, AC-INV-143, AC-INV-145 |

**Coverage — RULE → API → code**: the Error Catalog rows (§5) carry each RULE with its endpoints; the effect rules
RULE-INV-005, 014, 038 and the answers of RULE-INV-024, 046 raise no code (§1.3).

**Coverage — ENT → QR**: each entity block's REPOSITORY OPS line (DATA-DOM).
<!-- PHASE:ALIGN-BE:END -->

<!-- PHASE:INT-C:START traces=REQ-INV-035,REQ-INV-039,REQ-INV-040,REQ-INV-042,REQ-INV-043,REQ-INV-049,REQ-INV-050,REQ-INV-051,REQ-INV-052,REQ-INV-053,REQ-INV-054,REQ-INV-059,REQ-INV-063,REQ-INV-070,REQ-INV-072,REQ-INV-075,REQ-INV-078,REQ-INV-080,REQ-INV-088,REQ-INV-091,REQ-INV-092,REQ-INV-097,REQ-INV-106,REQ-INV-109,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-129 -->
## INT-C — integration (one block per edge)

The one place this plan speaks about another module. Every edge targets CAT (the catalogue module, DAG #1 —
downward from INV, DAG #3). Access is in-process (conventions.module_interface: in_process): CAT's published
crossmodule API is an injected bean — there is no HTTP client, base path, timeout or network error path.
Foreign keys reference the target's primary-key column only (ADR-INV-023); the executor checks the target table
and key column against CAT's delivered migration before running a patch. Each block is one package
(`integration/XM-INV-0nn`); the adapters live in `com.commerce.inv.integration.cat`. They inject CAT's published
crossmodule bean by its type, as CAT delivers it: CAT v2 still declares it under `com.erp.cat.crossmodule`, and
the rename to `com.commerce.cat.crossmodule` is the open platform finding of business-policies-pty PLATFORM
FINDINGS row 1 (cited in business-policies-inv PLATFORM FINDINGS row 3) — when CAT applies it, only the adapters'
import lines change. The CAT contract items INV relies on are CON-CAT-001, 002, 005, 006, 007, 008, 010 and 016
(ADR-INV-040).

<!-- XM:XM-INV-001:START traces=REQ-INV-035,REQ-INV-039,REQ-INV-040 -->
### XM-INV-001 — Stock balance Variant foreign key on VARIANT_FK (DBF-INV-030)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_BALANCE ADD CONSTRAINT FK_INV_STOCK_BALANCE_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_001_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_BALANCE b WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = b.VARIANT_FK)` = 0
tests      : AC-INV-040, AC-INV-044, AC-INV-045
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-001:END -->

<!-- XM:XM-INV-002:START traces=REQ-INV-051,REQ-INV-052,REQ-INV-075 -->
### XM-INV-002 — Stock move Variant foreign key on VARIANT_FK (DBF-INV-053)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT FK_INV_STOCK_MOVE_CAT_VARIANT_1 FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_002_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_MOVE m WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = m.VARIANT_FK)` = 0
tests      : AC-INV-056, AC-INV-057, AC-INV-058, AC-INV-082
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-002:END -->

<!-- XM:XM-INV-003:START traces=REQ-INV-053 -->
### XM-INV-003 — Stock move bundle Variant foreign key on BUNDLE_VARIANT_FK (DBF-INV-056)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT FK_INV_STOCK_MOVE_CAT_VARIANT_2 FOREIGN KEY (BUNDLE_VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);   -- the column is nullable
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_003_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_MOVE m WHERE m.BUNDLE_VARIANT_FK IS NOT NULL AND NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = m.BUNDLE_VARIANT_FK)` = 0
tests      : AC-INV-059
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-003:END -->

<!-- XM:XM-INV-004:START traces=REQ-INV-043,REQ-INV-050 -->
### XM-INV-004 — Reservation Variant foreign key on VARIANT_FK (DBF-INV-076)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_RESERVATION ADD CONSTRAINT FK_INV_RESERVATION_CAT_VARIANT_1 FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_004_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_RESERVATION r WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = r.VARIANT_FK)` = 0
tests      : AC-INV-048, AC-INV-055
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-004:END -->

<!-- XM:XM-INV-005:START traces=REQ-INV-049 -->
### XM-INV-005 — Reservation bundle Variant foreign key on BUNDLE_VARIANT_FK (DBF-INV-078)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_RESERVATION ADD CONSTRAINT FK_INV_RESERVATION_CAT_VARIANT_2 FOREIGN KEY (BUNDLE_VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);   -- the column is nullable
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_005_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_RESERVATION r WHERE r.BUNDLE_VARIANT_FK IS NOT NULL AND NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = r.BUNDLE_VARIANT_FK)` = 0
tests      : AC-INV-054
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-005:END -->

<!-- XM:XM-INV-006:START traces=REQ-INV-078,REQ-INV-080 -->
### XM-INV-006 — Transfer line Variant foreign key on VARIANT_FK (DBF-INV-106)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT FK_INV_TRANSFER_LINE_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_006_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_TRANSFER_LINE t WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = t.VARIANT_FK)` = 0
tests      : AC-INV-085, AC-INV-087, AC-INV-088
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-006:END -->

<!-- XM:XM-INV-007:START traces=REQ-INV-088,REQ-INV-092 -->
### XM-INV-007 — Stock count line Variant foreign key on VARIANT_FK (DBF-INV-135)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_COUNT_LINE ADD CONSTRAINT FK_INV_STOCK_COUNT_LINE_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_007_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_COUNT_LINE c WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = c.VARIANT_FK)` = 0
tests      : AC-INV-097, AC-INV-098, AC-INV-102
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-007:END -->

<!-- XM:XM-INV-008:START traces=REQ-INV-070,REQ-INV-072,REQ-INV-097 -->
### XM-INV-008 — Stock adjustment line Variant foreign key on VARIANT_FK (DBF-INV-167)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT FK_INV_STOCK_ADJUSTMENT_LINE_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_008_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_ADJUSTMENT_LINE a WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = a.VARIANT_FK)` = 0
tests      : AC-INV-076, AC-INV-079, AC-INV-107
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-008:END -->

<!-- XM:XM-INV-009:START traces=REQ-INV-106,REQ-INV-109 -->
### XM-INV-009 — Lot Variant foreign key on VARIANT_FK (DBF-INV-180)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_LOT ADD CONSTRAINT FK_INV_LOT_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_009_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_LOT l WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = l.VARIANT_FK)` = 0
tests      : AC-INV-119, AC-INV-122
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-009:END -->

<!-- XM:XM-INV-010:START traces=REQ-INV-059,REQ-INV-063 -->
### XM-INV-010 — Stock valuation Variant foreign key on VARIANT_FK (DBF-INV-196)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_VALUATION ADD CONSTRAINT FK_INV_STOCK_VALUATION_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_010_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_VALUATION s WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = s.VARIANT_FK)` = 0
tests      : AC-INV-065, AC-INV-069
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-010:END -->

<!-- XM:XM-INV-011:START traces=REQ-INV-124,REQ-INV-125,REQ-INV-129 -->
### XM-INV-011 — Item stock settings Item foreign key on ITEM_FK (DBF-INV-209)
target     : CAT · ENT-CAT-001
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-001
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_ITEM_STOCK_SETTINGS ADD CONSTRAINT FK_INV_ITEM_STOCK_SETTINGS_CAT_ITEM FOREIGN KEY (ITEM_FK) REFERENCES CAT_ITEM (ITEM_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_011_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_ITEM_STOCK_SETTINGS s WHERE NOT EXISTS (SELECT 1 FROM CAT_ITEM i WHERE i.ITEM_PK = s.ITEM_FK)` = 0
tests      : AC-INV-138, AC-INV-140, AC-INV-145
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-011:END -->

<!-- XM:XM-INV-012:START traces=REQ-INV-042,REQ-INV-049,REQ-INV-053,REQ-INV-054 -->
### XM-INV-012 — Bundle component read
target     : CAT · ENT-CAT-007
type       : SOFT-READ
contract   : contract-cat.md#CON-CAT-006
requires   : CAT:DELIVERED
do         :
  ddl_patch : none — SOFT-READ, no column and no constraint
  adapter   : `com.commerce.inv.integration.cat.CatalogBundleComponentAdapter implements BundleComponentPort` — injects CAT's published in-process crossmodule API (the bean exposing CON-CAT-007 / CON-CAT-008). `List<BundleComponent> activeComponents(long bundleVariantPk)`: getVariant(bundleVariantPk) (CON-CAT-008) → itemFk; getItem(itemFk) (CON-CAT-007) → itemKindCode; not BUNDLE → empty list with `isBundle = false`; BUNDLE → its Bundle component lines mapped to {componentVariantPk = componentVariantFk, quantity (3 dp, in the component Item's base unit — CON-CAT-006), componentBaseUomCode}; a line whose component Variant reads inactive through getVariant is dropped; CAT "not found" → INV-404-VARIANT-NOT-FOUND. The callers multiply the bundle quantity by `quantity` (REQ-INV-042 availability, 049 reservation, 053 issue) and raise INV-422-BUNDLE-NO-COMPONENTS on an empty list of a bundle (RULE-INV-022)
  config    : Spring `@Component` in package com.commerce.inv.integration.cat, the only `BundleComponentPort` bean; in-process call inside the caller's transaction — no HTTP client, no base path, no timeout
tests      : AC-INV-047, AC-INV-054, AC-INV-059, AC-INV-060
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-012:END -->

<!-- XM:XM-INV-013:START traces=REQ-INV-072,REQ-INV-088,REQ-INV-091 -->
### XM-INV-013 — Barcode resolution
target     : CAT · ENT-CAT-009
type       : SOFT-READ
contract   : contract-cat.md#CON-CAT-005
requires   : CAT:DELIVERED
do         :
  ddl_patch : none — SOFT-READ, no column and no constraint
  adapter   : `com.commerce.inv.integration.cat.CatalogBarcodeAdapter implements BarcodePort` — injects CAT's published in-process crossmodule API (the bean exposing CON-CAT-010). `Optional<Long> variantOf(String barcode)`: lookupBarcode(barcode) (CON-CAT-010) → variantPk; CAT "not found" or "scale label not readable" → Optional.empty(); the embedded weight or price of a scale label is ignored. Callers: count line add → INV-422-BARCODE-UNKNOWN (RULE-INV-032, API-INV-029); opening stock row → INV-422-LOAD-ROW-VARIANT (RULE-INV-028, API-INV-047 — the row's `barcode` is resolved here only: CAT v2 publishes no exact Variant-code read, so v1 resolves no Variant code, and the sheet column is "Barcode" — ADR-INV-035; the code read is a platform finding owned by CAT); balance / valuation term filters → empty result (API-INV-016, 050)
  config    : Spring `@Component` in package com.commerce.inv.integration.cat, the only `BarcodePort` bean; in-process — no HTTP client, no base path, no timeout
tests      : AC-INV-079, AC-INV-097, AC-INV-101
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-013:END -->

<!-- XM:XM-INV-014:START traces=REQ-INV-052,REQ-INV-072,REQ-INV-078,REQ-INV-097 -->
### XM-INV-014 — Variant state read (minted at P3.1)
target     : CAT · ENT-CAT-002
type       : SOFT-READ
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : none — SOFT-READ, no column and no constraint
  adapter   : `com.commerce.inv.integration.cat.CatalogVariantAdapter implements VariantReadPort` — injects CAT's published in-process crossmodule API (the bean exposing CON-CAT-007 / CON-CAT-008). `VariantRef readVariant(long variantPk)`: getVariant(variantPk) (CON-CAT-008) → {variantPk, itemFk, code, nameAr, nameEn, isActiveFl}; getItem(itemFk) (CON-CAT-007) → {itemKindCode, isActiveFl, code, nameAr, nameEn}; returns {variantPk, itemPk, variantCode, variantNameAr, variantNameEn, variantActiveFl, itemActiveFl, itemKindCode, itemCode}; CAT "not found" → INV-404-VARIANT-NOT-FOUND. `Map<Long, VariantRef> readVariants(Set<Long>)` loops readVariant per page (≤ 100). Used by RULE-INV-018 (active Variant of a STANDARD Item — on save and again on post, engine step 0), RULE-INV-028 (an opening stock row given by `variantPk`, or the Variant a barcode resolved to through XM-INV-013; no Variant-code lookup exists in v1 — ADR-INV-035), the Variant display names of the search pages and the Item of a line (lot / dual-quantity settings)
  config    : Spring `@Component` in package com.commerce.inv.integration.cat, the only `VariantReadPort` bean; in-process — no HTTP client, no base path, no timeout; state read at request time (SRS D-23 — the catalogue-change event is not consumed in v1)
tests      : AC-INV-058, AC-INV-079, AC-INV-085, AC-INV-107
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-014:END -->

<!-- XM:XM-INV-015:START traces=REQ-INV-039,REQ-INV-124,REQ-INV-126,REQ-INV-127,REQ-INV-129 -->
### XM-INV-015 — Item state read (minted at P3.1)
target     : CAT · ENT-CAT-001
type       : SOFT-READ
contract   : contract-cat.md#CON-CAT-001
requires   : CAT:DELIVERED
do         :
  ddl_patch : none — SOFT-READ, no column and no constraint
  adapter   : `com.commerce.inv.integration.cat.CatalogItemAdapter implements ItemReadPort` — injects CAT's published in-process crossmodule API (the bean exposing CON-CAT-007 / CON-CAT-016). `ItemRef readItem(long itemPk)`: getItem(itemPk) (CON-CAT-007) → {itemPk, code, nameAr, nameEn, itemKindCode, isActiveFl, variantPks = its Variants' identifiers}; CAT "not found" → INV-404-ITEM-NOT-FOUND. `List<Long> searchItems(String term)`: searchCatalogue(term) (CON-CAT-016) → at most 20 itemPks (term under 2 characters → empty). Used by RULE-INV-045 (BUNDLE refused), the settings key / names (API-INV-044, 045), RULE-INV-042 / 043 (the Item's Variants for QR-INV-045 / 046), the Item filters of API-INV-016, 043, 050
  config    : Spring `@Component` in package com.commerce.inv.integration.cat, the only `ItemReadPort` bean; in-process — no HTTP client, no base path, no timeout
tests      : AC-INV-139, AC-INV-142, AC-INV-143, AC-INV-145
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-015:END -->
<!-- PHASE:INT-C:END -->

