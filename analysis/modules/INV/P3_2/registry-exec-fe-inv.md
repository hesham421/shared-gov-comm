## REGISTRY — P3.2 — INV v1
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Stage : P3.2 (Frontend — UX Design + Execution Plan)   Profile : commerce
Source : analysis/modules/INV/P3_2/flow-diagram-inv.md · ui-ux-spec-inv.md · frontend-execution-plan-inv.md · Date : 2026-10-31
Bound  : api-spec-inv.yaml (OpenAPI 3.1.0 — 52 operations)
══════════════════════════════════════════════════════════════════

### ID ranges

| Atom | Count | Range | Last sequence | Next free |
|---|---|---|---|---|
| SCR | 12 | SCR-INV-001 … SCR-INV-012 | 12 | SCR-INV-013 |
| UXD | 11 | UXD-INV-001 … UXD-INV-011 | 11 | UXD-INV-012 |

SCR ids : SCR-INV-001, SCR-INV-002, SCR-INV-003, SCR-INV-004, SCR-INV-005, SCR-INV-006, SCR-INV-007, SCR-INV-008, SCR-INV-009, SCR-INV-010, SCR-INV-011, SCR-INV-012

UXD ids : UXD-INV-001, UXD-INV-002, UXD-INV-003, UXD-INV-004, UXD-INV-005, UXD-INV-006, UXD-INV-007, UXD-INV-008, UXD-INV-009, UXD-INV-010, UXD-INV-011

### Screens

| SCR | Name (ar / en) | SCR-REQ | Owning ENT | Container | Permissions |
|---|---|---|---|---|---|
| SCR-INV-001 | المستودعات / Warehouses | SCR-REQ-INV-001 | ENT-INV-001 | drawer | PERM_INV_WAREHOUSES_VIEW / _CREATE / _UPDATE / _DELETE |
| SCR-INV-002 | الفروع / Branches | SCR-REQ-INV-002 | ENT-INV-002 | drawer | PERM_INV_BRANCHES_VIEW / _CREATE / _UPDATE / _DELETE |
| SCR-INV-003 | أرصدة المخزون / Stock balances | SCR-REQ-INV-003 | ENT-INV-003 | page | PERM_INV_STOCK_BALANCES_VIEW |
| SCR-INV-004 | سجل حركات المخزون / Stock move history | SCR-REQ-INV-004 | ENT-INV-004 | page | PERM_INV_STOCK_MOVES_VIEW |
| SCR-INV-005 | التحويلات بين المستودعات / Transfers | SCR-REQ-INV-005 | ENT-INV-006 (+ ENT-INV-007) | page | PERM_INV_TRANSFERS_VIEW / _CREATE / _UPDATE / _DELETE |
| SCR-INV-006 | جرد المخزون / Stock counts | SCR-REQ-INV-006 | ENT-INV-008 (+ ENT-INV-009) | page | PERM_INV_COUNTS_VIEW / _CREATE / _UPDATE / _DELETE |
| SCR-INV-007 | تسويات المخزون / Stock adjustments | SCR-REQ-INV-007 | ENT-INV-010 (+ ENT-INV-011) | page | PERM_INV_ADJUSTMENTS_VIEW / _CREATE / _UPDATE / _DELETE |
| SCR-INV-008 | التشغيلات وقرب انتهاء الصلاحية / Lots and near expiry | SCR-REQ-INV-008 | ENT-INV-012 | drawer | PERM_INV_LOTS_VIEW / _UPDATE |
| SCR-INV-009 | إعدادات مخزون الصنف / Item stock settings | SCR-REQ-INV-009 | ENT-INV-014 | drawer | PERM_INV_ITEM_STOCK_SETTINGS_VIEW / _CREATE / _UPDATE |
| SCR-INV-010 | المخزون الافتتاحي / Opening stock | SCR-REQ-INV-010 | ENT-INV-010 (+ ENT-INV-011) | page | PERM_INV_OPENING_STOCK_VIEW / _CREATE |
| SCR-INV-011 | إعدادات المخزون / Inventory settings | SCR-REQ-INV-011 | — (given `cu` settings) | page | PERM_INV_SETTINGS_VIEW / _UPDATE |
| SCR-INV-012 | تقييم المخزون / Stock valuation | SCR-REQ-INV-012 | ENT-INV-013 | page | PERM_INV_STOCK_VALUATION_VIEW |

Each screen has one F-block in each of F1, F2, F3 and F4 (`SUB:<PHASE>-SCR-INV-nnn`): 48 SUBs. SEC-FE and
ALIGN-FE cover all 12.

### UXD index

| UXD | Screen | Field | Owner module · read used (contract) | Traces |
|---|---|---|---|---|
| UXD-INV-001 | SCR-INV-003 | Variant filter | CAT · search, barcode lookup (CON-CAT-016, CON-CAT-010) | REQ-INV-039, AC-INV-044 |
| UXD-INV-002 | SCR-INV-003 | Item filter, Item name column | CAT · search, read Variant, read Item (CON-CAT-016, CON-CAT-008, CON-CAT-007) | REQ-INV-039, AC-INV-044 |
| UXD-INV-003 | SCR-INV-003 | measure unit label | CAT · unit-of-measure lookup (SRS A6 consumed) | REQ-INV-121, AC-INV-135 |
| UXD-INV-004 | SCR-INV-004 | Variant filter | CAT · search, barcode lookup (CON-CAT-016, CON-CAT-010) | REQ-INV-075, AC-INV-082 |
| UXD-INV-005 | SCR-INV-005 | line Variant (barcode, pick, label, Item, unit) | CAT · CON-CAT-010, 016, 008, 007 | REQ-INV-078, AC-INV-085, REQ-INV-084, AC-INV-093 |
| UXD-INV-006 | SCR-INV-006 | count line Variant (barcode, pick by hand, label, Item, unit) | CAT · CON-CAT-010, 016, 008, 007 | REQ-INV-088, AC-INV-097, REQ-INV-091, AC-INV-101 |
| UXD-INV-007 | SCR-INV-007 | line Variant (barcode, pick, label, Item, unit) | CAT · CON-CAT-010, 016, 008, 007 | REQ-INV-097, AC-INV-107, REQ-INV-099, AC-INV-110 |
| UXD-INV-008 | SCR-INV-008 | Variant filter, Variant label of lot rows | CAT · CON-CAT-016, 010, 008 | REQ-INV-109, AC-INV-122 |
| UXD-INV-009 | SCR-INV-009 | Item pick | CAT · search, read Item (CON-CAT-016, CON-CAT-007) | REQ-INV-124, AC-INV-138, REQ-INV-125, AC-INV-140 |
| UXD-INV-010 | SCR-INV-009 | measure unit options and label | CAT · unit-of-measure lookup (SRS A6 consumed) | REQ-INV-125, AC-INV-140, AC-INV-141 |
| UXD-INV-011 | SCR-INV-012 | Variant filter, Item filter | CAT · search, barcode lookup (CON-CAT-016, CON-CAT-010) | REQ-INV-063, AC-INV-069 |

All 11 are cited by F-blocks of F1–F4. They name the CAT contract only; CAT's API id and path resolve in CAT's
artifacts (ADR-INV-033).

### API coverage

Documented endpoints used: **52 / 52** (API-INV-001 … API-INV-052 — API-INV-052 serves INV's lookup keys, added at gate-analysis revise 2). Unused: none. The operations coverage table
is in ALIGN-FE.
Reads outside the document, none of them a `read` hook (ADR-INV-033):
- CAT reads, through the UXDs;
- the session permissions, from the given `sec`.
Values absent from the document: none. `StockBalanceRow` and `Availability.perWarehouse` carry `warehouseCode`,
`warehouseNameAr` and `warehouseNameEn`, so every role reads the warehouse label from the row (ADR-INV-036, closed at
gate-analysis revise 1). Lot and document labels are on the rows too (gate-analysis revise 3, G6): `lotCode` on transfer,
count and adjustment lines and on stock moves, `adjustmentDocNo` on a stock count, `sourceCountDocNo` on a stock
adjustment; the bundle label of a move goes through UXD-INV-004.

Runtime codes bound to a RULE in `api-surface`: 41 (INV-422-DOC-DATE-FUTURE → RULE-INV-051 added at gate-analysis revise 2, INV-409-IDEMPOTENCY-KEY-REUSED → RULE-INV-050 at revise 3). A line-level refusal is placed on the line `details[0]` names (ADR-INV-046). Codes routed with the catalogue text and no RULE:
- INV-400, INV-401, INV-403, INV-500;
- the `INV-404-*` not-found codes (each with INV's own text since gate-analysis revise 3) and INV-409-STALE-VERSION (ADR-INV-021 amended, ADR-INV-032);
- INV-422-HORIZON-RANGE (DEFAULT D-04);
- INV-422-LOAD-ROWS-INVALID (wrapper of row failures).

### ALIGN

The verdict is stamped by the orchestrator from `gov.py analyze` (the `self-check` block in ALIGN-FE). Findings
fixed at gate-analysis revise 1: G13 (warehouse label from the row on SCR-INV-003), G15 (the jumps from SCR-INV-005
and SCR-INV-007 to SCR-INV-004 seed `sourceDocTypeCode` + `sourceDocRef`), G12 (opening stock rows by barcode only).

### ADRs

| ADR | Title | Status |
|---|---|---|
| ADR-INV-033 | Reads a screen needs that are not operations of api-spec-inv.yaml | ACCEPTED |
| ADR-INV-034 | The stock count entry commits line by line | ACCEPTED |
| ADR-INV-035 | The browser reads the opening stock sheet | ACCEPTED |
| ADR-INV-036 | Warehouse and lot labels on the stock balance screen for a Seller/Cashier | ACCEPTED (open item closed at gate-analysis revise 1 — the label is on the row) |

Cited from P3.1 at gate-analysis revise 2: ADR-INV-046 (line refusals in `details`), ADR-INV-047 (OPENING_STOCK not offered on SCR-INV-007); ADR-INV-033 amended (INV lookup values through API-INV-052).

Cited at gate-analysis revise 3: ADR-INV-051 («بدون تشغيلة» / "Without a lot" on transfer, count and decrease lines), ADR-INV-053 (one idempotency key per opening stock load); ADR-INV-021 amended.

Cited at gate-analysis revise 4: ADR-INV-054 (system measure of a posted dual-quantity count line, SCR-INV-006); the SCR-INV-005 guard now hides only the menu entry and the create action while one warehouse is active (G7).

Filed under analysis/decisions/INV/. BLOCKED: none. Next free ADR: ADR-INV-057 (module-wide).

### Traceability

REQ covered by ≥ 1 SCR / F-block: **107 / 141** (REQ-INV-139 on SCR-INV-005 … 007, REQ-INV-140 on SCR-INV-010 — gate-analysis revise 3). Orphan REQ: **none**.

The other 34 REQs have no SRS screen. In the SRS traceability their stories are "— (cross-module)", or the REQ
is a backend effect with no screen requirement. They are served by P3.1 (InventoryApi, events, provisioning,
scheduled check, audit), and the frontend has nothing to build for them:
- provisioning seeds: REQ-INV-001, 002, 020, 021;
- in-process operations for other modules (InventoryApi): REQ-INV-019, 033, 043 … 054, 058;
- events: REQ-INV-034, 057;
- balance and move internals: REQ-INV-036, 037;
- valuation internals: REQ-INV-060;
- notifications: REQ-INV-068;
- offline POS acceptance: REQ-INV-069;
- audit: REQ-INV-077;
- FEFO issue: REQ-INV-110, 111, 112;
- measure recording on cross-module moves: REQ-INV-120;
- capability turn-off check (flag owner): REQ-INV-130, 131;
- a lot received or loaded already past its expiry, accepted and expired from creation (a posting effect — the screens show it through the existing expired mark): REQ-INV-141.

Stories in flows: 23 / 28. US-INV-004, 009, 018, 019 and 022 have no SRS screen and no flow (reconciliation B1).

### Event

"P3.2 completed: INV v1 — SCR 12, UXD 11, F-blocks 48 (F1–F4) + SEC-FE + ALIGN-FE, API 51/51 bound, ADR-INV-033 … 036" · "gate-analysis revise 2: API 52/52 bound (lookups through API-INV-052), line refusals routed by details[0].lineRef" · "gate-analysis revise 3: labels from the rows, Without a lot option, opening stock idempotency key" · "gate-analysis revise 5: reserved and available shown once per Variant × warehouse pair on SCR-INV-003"
══════════════════════════════════════════════════════════════════
