# Flow diagram — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Stage : P3.2 Part A (UX design)   Profile : commerce
Inputs : srs-inv (PART B screen requirements, A4 REQ/AC), prd-inv (US priority), registry-srs
Screens: SCR-INV-001 … SCR-INV-012 (one per SCR-REQ-INV-001 … 012 — see ui-ux-spec-inv.md)
══════════════════════════════════════════════════════════════════

Every inventory screen reads the inventory context (active warehouse and branch counts, capability flags —
API-INV-008) on load. The menu and every flow below show a warehouse, branch, lot or measure step only when the
SRS lets it be shown (REQ-INV-010 … 012, REQ-INV-029, REQ-INV-030, REQ-INV-134, REQ-INV-135). Menu entries
follow the role grants (REQ-INV-136, REQ-INV-137). Priorities come from the PRD (INV-P11).

## Navigation map

| From | To | Seeded filter (identifies one record or one set) |
|---|---|---|
| Inventory menu | every SCR | — |
| SCR-INV-001 Warehouses (row) | SCR-INV-003 Stock balances | `warehousePk` |
| SCR-INV-002 Branches (row) | SCR-INV-001 Warehouses | `branchPk` |
| SCR-INV-003 Stock balances (row) | SCR-INV-004 Stock move history | `variantPk` + `warehousePk` (+ `lotPk` when the row has a lot) |
| SCR-INV-005 Transfers (posted entry) | SCR-INV-004 Stock move history | `sourceDocTypeCode=INV_TRANSFER` + `sourceDocRef=<transferPk>` (one document exactly — a number alone could match another module's document) |
| SCR-INV-006 Stock counts (posted entry) | SCR-INV-007 Stock adjustments | `adjustmentPk` of the count (entry route `/:id`) |
| SCR-INV-007 Stock adjustments (posted entry) | SCR-INV-004 Stock move history | `sourceDocTypeCode=INV_ADJUSTMENT` + `sourceDocRef=<stockAdjustmentPk>` (one document exactly) |
| SCR-INV-008 Lots (expired row) | SCR-INV-007 Stock adjustments (new) | `reason=EXPIRY_WRITE_OFF` + `lotPk` + `warehousePk` |
| SCR-INV-008 Lots (row) | SCR-INV-003 Stock balances | `lotPk` (+ `warehousePk`) |
| SCR-INV-010 Opening stock (after posting) | SCR-INV-007 Stock adjustments | `stockAdjustmentPk` of the posted OPENING_STOCK adjustment |
| SCR-INV-012 Stock valuation (row) | SCR-INV-004 Stock move history | `variantPk` |
| Near-expiry notification | SCR-INV-008 Lots | `nearExpiryOnly=true` |

## Flows

```
FLOW — Maintain warehouses                       traces=US-INV-007,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,SCR-INV-001
Screens   : SCR-INV-001
Sequence  : Inventory menu → Setup → Warehouses (search) → entry (new | edit) → Save → search; row action Deactivate | Reactivate | Set as default → search
Trigger   : the Owner adds a location, renames one, takes one out of use or changes the default
Priority  : HIGH
```

```
FLOW — Warehouse default branch while one branch  traces=US-INV-010,REQ-INV-029,REQ-INV-030,REQ-INV-031,SCR-INV-001
Screens   : SCR-INV-001
Sequence  : Warehouses (no branch filter, no branch column) → entry (no branch field) → Save (default branch applied) → search
Trigger   : the Owner adds a warehouse while the tenant has one active branch
Priority  : HIGH
```

```
FLOW — Second warehouse reveals the warehouse dimension  traces=US-INV-007,US-INV-006,REQ-INV-010,REQ-INV-011,REQ-INV-012,REQ-INV-015,SCR-INV-001,SCR-INV-005
Screens   : SCR-INV-001, SCR-INV-005
Sequence  : Warehouses → entry (new) → Save → inventory context re-read → menu shows Transfers → Transfers (search)
Trigger   : the active warehouse count rises from one to two
Priority  : HIGH
```

```
FLOW — Balances of a warehouse                   traces=US-INV-007,US-INV-002,REQ-INV-009,REQ-INV-039,SCR-INV-001,SCR-INV-003
Screens   : SCR-INV-001, SCR-INV-003
Sequence  : Warehouses (row) → Stock balances filtered by that warehouse
Trigger   : the Owner or a Warehouse user wants to see what a location holds
Priority  : HIGH
```

```
FLOW — Maintain branches                         traces=US-INV-011,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,SCR-INV-002
Screens   : SCR-INV-002
Sequence  : Inventory menu → Setup → Branches (search) → entry (new | edit) → Save → search; row action Deactivate | Reactivate | Set as default → search
Trigger   : the Owner opens another outlet, renames one, takes one out of use or changes the default
Priority  : HIGH
```

```
FLOW — Warehouses of a branch                    traces=US-INV-011,US-INV-010,REQ-INV-028,REQ-INV-009,REQ-INV-032,SCR-INV-002,SCR-INV-001
Screens   : SCR-INV-002, SCR-INV-001
Sequence  : Branches (row) → Warehouses filtered by that branch
Trigger   : the Owner checks which warehouses belong to a branch (for example before deactivating it — RULE-INV-013)
Priority  : HIGH
```

```
FLOW — Check stock balances and availability     traces=US-INV-002,US-INV-001,US-INV-003,US-INV-020,US-INV-024,US-INV-025,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-115,REQ-INV-121,SCR-INV-003
Screens   : SCR-INV-003
Sequence  : Inventory menu → Stock balances (search) → filter by Variant → per-warehouse and total availability shown → exit
Trigger   : a Seller/Cashier promises goods; a Warehouse user prepares supply
Priority  : HIGH
```

```
FLOW — Explain a balance                         traces=US-INV-013,US-INV-005,REQ-INV-075,REQ-INV-076,REQ-INV-056,SCR-INV-003,SCR-INV-004
Screens   : SCR-INV-003, SCR-INV-004
Sequence  : Stock balances (row) → Stock move history filtered to that Variant and warehouse (balance after each move) → move detail → exit
Trigger   : a balance does not match the shelf
Priority  : HIGH
```

```
FLOW — Transfer stock between warehouses         traces=US-INV-008,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,SCR-INV-005,SCR-INV-004
Screens   : SCR-INV-005, SCR-INV-004
Sequence  : Inventory menu → Transfers (search) → entry (new) → add lines → Save (DRAFT) → Post → Stock move history filtered by the transfer (document type and identifier) | Cancel (DRAFT) → search
Trigger   : goods are moved from one warehouse to another (shown only with two or more active warehouses)
Priority  : HIGH
```

```
FLOW — Count stock from a phone                  traces=US-INV-014,US-INV-015,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,SCR-INV-006,SCR-INV-007
Screens   : SCR-INV-006, SCR-INV-007
Sequence  : Inventory menu → Stock counts (search) → Start count → scan barcodes / pick Variants → change or remove a line → Post → resulting Stock adjustment (entry) | Cancel → search
Trigger   : a Warehouse user counts the shelf with a phone's web browser (360 CSS-pixel viewport)
Priority  : HIGH
```

```
FLOW — Adjust stock with a reason                traces=US-INV-015,US-INV-017,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-067,SCR-INV-007,SCR-INV-004
Screens   : SCR-INV-007, SCR-INV-004
Sequence  : Inventory menu → Stock adjustments (search) → entry (new) → reason + lines → Save (DRAFT) → Post → Stock move history filtered by the adjustment (document type and identifier) | Cancel (DRAFT) → search
Trigger   : damage, a count difference or another reason
Priority  : HIGH
```

```
FLOW — Load opening stock                        traces=US-INV-012,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,SCR-INV-010,SCR-INV-007
Screens   : SCR-INV-010, SCR-INV-007
Sequence  : Setup wizard step 5 | Inventory menu → Setup → Opening stock → download sheet → fill → load sheet (rows shown) | enter lines → Post → posted OPENING_STOCK Stock adjustment (entry)
Trigger   : the Owner starts on the platform with stock already on hand
Priority  : HIGH
```

```
FLOW — Choose inventory settings                 traces=US-INV-017,US-INV-023,REQ-INV-065,REQ-INV-066,REQ-INV-118,REQ-INV-119,SCR-INV-011
Screens   : SCR-INV-011
Sequence  : Inventory menu → Setup → Inventory settings → Save → exit
Trigger   : the Owner decides whether negative stock is allowed, or sets the near-expiry horizon
Priority  : HIGH
```

```
FLOW — See lots and near expiry, correct an expiry date  traces=US-INV-021,US-INV-023,US-INV-024,REQ-INV-108,REQ-INV-109,REQ-INV-115,REQ-INV-116,SCR-INV-008
Screens   : SCR-INV-008
Sequence  : Inventory menu → Lots (search | near-expiry only) → entry (Owner) → Save expiry → search
Trigger   : the Warehouse role reviews lots; the Owner fixes a typing mistake made on receipt (shown only with lotExpiry on)
Priority  : MEDIUM
```

```
FLOW — Near-expiry notification                  traces=US-INV-023,REQ-INV-117,REQ-INV-116,SCR-INV-008
Screens   : SCR-INV-008
Sequence  : notification → Lots (near-expiry only) → exit
Trigger   : the daily near-expiry check notifies a Warehouse-role user
Priority  : MEDIUM
```

```
FLOW — Write off an expired lot                  traces=US-INV-024,REQ-INV-114,REQ-INV-115,SCR-INV-008,SCR-INV-007
Screens   : SCR-INV-008, SCR-INV-007
Sequence  : Lots (expired row) → Stock adjustment entry (new, reason Expired lot write-off, line seeded with the lot and its on-hand) → Save → Post → exit
Trigger   : an expired lot still holds stock
Priority  : MEDIUM
```

```
FLOW — Balances of a lot                         traces=US-INV-021,REQ-INV-109,REQ-INV-039,SCR-INV-008,SCR-INV-003
Screens   : SCR-INV-008, SCR-INV-003
Sequence  : Lots (row) → Stock balances filtered by that lot
Trigger   : a Warehouse user wants to know where a lot is held
Priority  : MEDIUM
```

```
FLOW — Set Item stock settings                   traces=US-INV-021,US-INV-025,US-INV-026,US-INV-027,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,SCR-INV-009
Screens   : SCR-INV-009
Sequence  : Inventory menu → Setup → Item stock settings (search) → entry (new | edit) → Save → search
Trigger   : the Owner marks an Item as tracked by lot or as dual-quantity (shown only with lotExpiry or dualQuantity on)
Priority  : MEDIUM
```

```
FLOW — Stock valuation                           traces=US-INV-016,REQ-INV-063,REQ-INV-059,REQ-INV-061,SCR-INV-012,SCR-INV-004
Screens   : SCR-INV-012, SCR-INV-004
Sequence  : Inventory menu → Stock valuation (search, grand total) → Variant detail → Stock move history filtered by that Variant
Trigger   : the Owner wants to know what stock is worth
Priority  : HIGH
```

```
FLOW — Inventory menu by role                    traces=US-INV-028,US-INV-006,REQ-INV-136,REQ-INV-137,REQ-INV-011,REQ-INV-134,SCR-INV-003
Screens   : SCR-INV-003
Sequence  : sign-in → Inventory menu (entries per role grant, capability flag and active warehouse count) → Stock balances (the only entry of a Seller/Cashier)
Trigger   : a user holding a role preset opens the inventory menu
Priority  : HIGH
```

## Stories without a flow

These stories have no SRS screen. The SRS traceability marks them "— (cross-module)": they are served by
provisioning, cross-module operations or notifications. No navigation is invented for them (reconciliation B1).

| US | Served by | Visible on a screen as |
|---|---|---|
| US-INV-004 | REQ-INV-001, REQ-INV-002 (provisioning) | the seeded default warehouse in SCR-INV-001 |
| US-INV-009 | REQ-INV-020, REQ-INV-021 (provisioning) | the seeded default branch in SCR-INV-002 |
| US-INV-018 | REQ-INV-068 (notification) | — |
| US-INV-019 | REQ-INV-058, REQ-INV-069 (cross-module issue) | offline-sale moves in SCR-INV-004 |
| US-INV-022 | REQ-INV-110 … REQ-INV-112 (cross-module issue) | the lot of each ISSUE move in SCR-INV-004 |
