## REGISTRY — P1 — INV v1
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Stage : P1 (SRS)   Profile : commerce
Source : srs-inv.md (P1 pass 1, round 1) — PRD approved 2026-10-29
══════════════════════════════════════════════════════════════════

### Entities

| ENT id | Name (en / ar) | Kind | Ownership | Status |
|---|---|---|---|---|
| ENT-INV-001 | Warehouse / المستودع | master | SHARED (owner) | REGISTERED |
| ENT-INV-002 | Branch / الفرع | master | SHARED (owner) | REGISTERED |
| ENT-INV-003 | Stock balance / رصيد المخزون | transactional | SHARED (owner) | REGISTERED |
| ENT-INV-004 | Stock move / حركة المخزون | transactional | PRIVATE | REGISTERED |
| ENT-INV-005 | Reservation / حجز المخزون | transactional | PRIVATE | REGISTERED |
| ENT-INV-006 | Transfer / تحويل مخزني | transactional | PRIVATE | REGISTERED |
| ENT-INV-007 | Transfer line / سطر التحويل | transactional | PRIVATE | REGISTERED |
| ENT-INV-008 | Stock count / جرد المخزون | transactional | PRIVATE | REGISTERED |
| ENT-INV-009 | Stock count line / سطر الجرد | transactional | PRIVATE | REGISTERED |
| ENT-INV-010 | Stock adjustment / تسوية المخزون | transactional | PRIVATE | REGISTERED |
| ENT-INV-011 | Stock adjustment line / سطر التسوية | transactional | PRIVATE | REGISTERED |
| ENT-INV-012 | Lot / التشغيلة | master | SHARED (owner) | REGISTERED |
| ENT-INV-013 | Stock valuation / تقييم المخزون | transactional | SHARED (owner) | REGISTERED |
| ENT-INV-014 | Item stock settings / إعدادات مخزون الصنف | config | PRIVATE | REGISTERED |

### Consumed (SRS A8 `module-dependencies` → gov.py graph)

| Module | Entity | Type |
|---|---|---|
| CAT | ENT-CAT-001 | HARD-FK |
| CAT | ENT-CAT-002 | HARD-FK |
| CAT | ENT-CAT-007 | SOFT-READ |
| CAT | ENT-CAT-009 | SOFT-READ |

Given (no XM edge — SRS A9 `given-usage`): erp-core modules.cu, modules.sequence, modules.events, modules.audit, modules.mdl, modules.tenant, modules.sec, modules.notif, modules.report.

### Lookups owned

| Key | ENT | Values count |
|---|---|---|
| INV_ADJUSTMENT_REASON | ENT-INV-010 | 5 (open) |
| INV_MOVE_TYPE | ENT-INV-004 | 8 |
| INV_DOC_STATUS | ENT-INV-006, ENT-INV-008, ENT-INV-010 | 3 |
| INV_RESERVATION_STATUS | ENT-INV-005 | 3 |
| INV_SOURCE_DOC_TYPE | ENT-INV-004, ENT-INV-005 | 10 (open) |
| INV_CHANGE_KIND | ENT-INV-001, ENT-INV-002 (event field) | 5 |

### Lookups consumed

| Key | Owner |
|---|---|
| Unit of measure (CAT unit-of-measure key) | CAT |

### Screens

| SCR-REQ id | Name (ar / en) | Page code |
|---|---|---|
| SCR-REQ-INV-001 | المستودعات / Warehouses | INV_WAREHOUSES |
| SCR-REQ-INV-002 | الفروع / Branches | INV_BRANCHES |
| SCR-REQ-INV-003 | أرصدة المخزون / Stock balances | INV_STOCK_BALANCES |
| SCR-REQ-INV-004 | سجل حركات المخزون / Stock move history | INV_STOCK_MOVES |
| SCR-REQ-INV-005 | التحويلات بين المستودعات / Transfers | INV_TRANSFERS |
| SCR-REQ-INV-006 | جرد المخزون / Stock counts | INV_COUNTS |
| SCR-REQ-INV-007 | تسويات المخزون / Stock adjustments | INV_ADJUSTMENTS |
| SCR-REQ-INV-008 | التشغيلات وقرب انتهاء الصلاحية / Lots and near expiry | INV_LOTS |
| SCR-REQ-INV-009 | إعدادات مخزون الصنف / Item stock settings | INV_ITEM_STOCK_SETTINGS |
| SCR-REQ-INV-010 | المخزون الافتتاحي / Opening stock | INV_OPENING_STOCK |
| SCR-REQ-INV-011 | إعدادات المخزون / Inventory settings | INV_SETTINGS |
| SCR-REQ-INV-012 | تقييم المخزون / Stock valuation | INV_STOCK_VALUATION |

### Requirements

| Atom | Count | Range | Last sequence |
|---|---|---|---|
| REQ | 140 | REQ-INV-001 … REQ-INV-140 | 140 |
| AC | 158 | AC-INV-001 … AC-INV-158 | 158 |
| ENT | 14 | ENT-INV-001 … ENT-INV-014 | 14 |
| RULE | 51 | RULE-INV-001 … RULE-INV-051 | 51 |
| SCR-REQ | 12 | SCR-REQ-INV-001 … SCR-REQ-INV-012 | 12 |

Last sequence per atom: (REQ: 140, AC: 158, ENT: 14, RULE: 51, SCR-REQ: 12). Each sequence continues from the next number after its last sequence.

Coverage: 28 / 28 stories covered by ≥ 1 REQ; 140 / 140 REQ carry ≥ 1 AC; 51 / 51 RULE trace to ≥ 1 REQ; 12 / 12 SCR-REQ trace to ≥ 1 REQ.

RULE-INV-049 (an issue consumes only its own reservation — REQ-INV-047, REQ-INV-048) was added at gate-analysis revise 1 (G9). The same revise extended the triggers of RULE-INV-004, 007, 008, 017, 018, 025, 028, 037, 040 and 047 in place, with no id change.

Gate-analysis revise 2 added RULE-INV-050 (a reused idempotency key carries the same request — REQ-INV-058, G9) and RULE-INV-051 (a stock document is not dated after today — REQ-INV-078, 079, 087, 097, 070, G14). It extended in place, with no id change, RULE-INV-018 (bundle receipts refused, G5), RULE-INV-019 (the lot-level check, G4), RULE-INV-033 (system-generated reasons not checked; OPENING_STOCK refused on the adjustment screen, G3, G18) and RULE-INV-040 with REQ-INV-122 (adjustment and opening stock lines, G6). It reworded AC-INV-104, 111, 144, 145, 147 and 149, added the field requestHash to ENT-INV-004 and ENT-INV-005, and stated the read and delete lifecycle of ENT-INV-007, 011, 013 and 014.

Gate-analysis revise 3 added REQ-INV-139 with AC-INV-155, 156 (stock held without a lot stays countable, transferable and adjustable after lot tracking is switched on — G2, ADR-INV-051), AC-INV-157 on REQ-INV-069 (a synchronised offline sale skips the active-state checks — G3, ADR-INV-052) and REQ-INV-140 with AC-INV-158 (an opening stock load carries an idempotency key — G4, ADR-INV-053). It extended in place, with no id change, RULE-INV-007 (scope without ENT-INV-006; offline exception — G3, G5), RULE-INV-008 and RULE-INV-018 (offline exception — G3), RULE-INV-016 (owns the inactive-warehouse refusal of a transfer — G5), RULE-INV-024 and RULE-INV-050 (opening stock load — G4) and RULE-INV-037 (lot-less exception — G2), and added the lifecycle clauses of REQ-INV-059, 079, 098 and 126 (C5.15).

### Decisions

| Kind | Ids | Status |
|---|---|---|
| ADR | none raised by P1 (next free ADR-INV-014) | — |
| DEFAULT | D-01 … D-27 (SRS "Decisions applied") | applied |
| BLOCKED | none | — |

### Event

"P1 completed: INV v1 — REQ 138 · AC 154 · ENT 14 · RULE 48 · SCR-REQ 12 · ADR 0" · "gate-analysis revise 1: RULE-INV-049 added (RULE 49)" · "gate-analysis revise 2: RULE-INV-050, RULE-INV-051 added (RULE 51)" · "gate-analysis revise 3: REQ-INV-139, 140 and AC-INV-155 … 158 added (REQ 140 · AC 158)"
══════════════════════════════════════════════════════════════════
