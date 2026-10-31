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
| REQ | 138 | REQ-INV-001 … REQ-INV-138 | 138 |
| AC | 154 | AC-INV-001 … AC-INV-154 | 154 |
| ENT | 14 | ENT-INV-001 … ENT-INV-014 | 14 |
| RULE | 48 | RULE-INV-001 … RULE-INV-048 | 48 |
| SCR-REQ | 12 | SCR-REQ-INV-001 … SCR-REQ-INV-012 | 12 |

Last sequence per atom: (REQ: 138, AC: 154, ENT: 14, RULE: 48, SCR-REQ: 12). Each sequence continues from the next number after its last sequence.

Coverage: 28 / 28 stories covered by ≥ 1 REQ; 138 / 138 REQ carry ≥ 1 AC; 48 / 48 RULE trace to ≥ 1 REQ; 12 / 12 SCR-REQ trace to ≥ 1 REQ.

### Decisions

| Kind | Ids | Status |
|---|---|---|
| ADR | none raised by P1 (next free ADR-INV-014) | — |
| DEFAULT | D-01 … D-27 (SRS "Decisions applied") | applied |
| BLOCKED | none | — |

### Event

"P1 completed: INV v1 — REQ 138 · AC 154 · ENT 14 · RULE 48 · SCR-REQ 12 · ADR 0"
══════════════════════════════════════════════════════════════════
