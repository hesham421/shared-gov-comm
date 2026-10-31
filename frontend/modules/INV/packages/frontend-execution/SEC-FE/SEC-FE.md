<!-- source: PHASE:SEC-FE -->
<!-- traces: AC-INV-014, AC-INV-018, AC-INV-150, AC-INV-152, AC-INV-153, AC-INV-154, API-INV-008, REQ-INV-011, REQ-INV-015, REQ-INV-132, REQ-INV-133, REQ-INV-134, REQ-INV-135, REQ-INV-136, REQ-INV-137, REQ-INV-138, SCR-INV-001, SCR-INV-002, SCR-INV-003, SCR-INV-004, SCR-INV-005, SCR-INV-006, SCR-INV-007, SCR-INV-008, SCR-INV-009, SCR-INV-010, SCR-INV-011, SCR-INV-012 -->
<!-- PHASE:SEC-FE:START traces=SCR-INV-001,SCR-INV-002,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-006,SCR-INV-007,SCR-INV-008,SCR-INV-009,SCR-INV-010,SCR-INV-011,SCR-INV-012,REQ-INV-011,REQ-INV-015,REQ-INV-132,REQ-INV-133,REQ-INV-134,REQ-INV-135,REQ-INV-136,REQ-INV-137,REQ-INV-138,AC-INV-014,AC-INV-018,AC-INV-150,AC-INV-152,AC-INV-153,AC-INV-154,API-INV-008 -->
## SEC-FE

**RF5, frontend half.** Permission names are the backend registry's (`PERM_<PAGE_CODE>_<ACTION>`, registry-exec-be
"Endpoints"), read from the session (ADR-INV-033) and not redeclared. The server is the authority: every INV
operation is authorized by the platform layer (REQ-INV-138). The UI only hides what the user cannot do:
- **Navigation guard**: no `VIEW` → the route renders the unauthorized view, and the menu entry is absent.
- **Forbidden responses**: INV-403 → message, text: catalogue INV-403 «ليست لديك صلاحية لهذا الإجراء.» /
  "You do not have permission for this action." (AC-INV-154). It never shows a raw status.
- **Flags**: capability flags come from the context (API-INV-008), which reads FeatureFlags on the server. No
  frontend code reads the activity or the activity preset (REQ-INV-132, REQ-INV-133). A lint rule in the module
  forbids importing the activity preset.

| SCR | Guard (no VIEW → unauthorized) | no CREATE | no UPDATE | no DELETE | Flag / count gate |
|---|---|---|---|---|---|
| SCR-INV-001 | `PERM_INV_WAREHOUSES_VIEW` | "New" hidden | entry read-only; Reactivate and Set as default hidden | Deactivate hidden | branch field, filter and column hidden while one active branch |
| SCR-INV-002 | `PERM_INV_BRANCHES_VIEW` | "New" hidden | entry read-only; Reactivate and Set as default hidden | Deactivate hidden | — |
| SCR-INV-003 | `PERM_INV_STOCK_BALANCES_VIEW` | not applicable | not applicable | not applicable | warehouse filter and column hidden while one (AC-INV-015); lot hidden with lotExpiry off; measure hidden with dualQuantity off |
| SCR-INV-004 | `PERM_INV_STOCK_MOVES_VIEW` | not applicable | not applicable | not applicable | as SCR-INV-003 |
| SCR-INV-005 | `PERM_INV_TRANSFERS_VIEW` | "New" hidden | entry read-only; Save and Post hidden | Cancel hidden | menu entry and "New" (`/new`) only with two or more active warehouses (AC-INV-014, AC-INV-018); search, read and the moves link stay reachable by deep link |
| SCR-INV-006 | `PERM_INV_COUNTS_VIEW` | "Start count" hidden | scan bar, line drawer and Post hidden (read-only) | Cancel hidden | warehouse selector hidden while one |
| SCR-INV-007 | `PERM_INV_ADJUSTMENTS_VIEW` | "New" hidden | entry read-only; Save and Post hidden | Cancel hidden | warehouse hidden while one; lot hidden with lotExpiry off (AC-INV-150) |
| SCR-INV-008 | `PERM_INV_LOTS_VIEW` | not applicable | entry read-only (Save expiry hidden) | not applicable | menu entry and route only with lotExpiry on (AC-INV-150) |
| SCR-INV-009 | `PERM_INV_ITEM_STOCK_SETTINGS_VIEW` | "New" hidden | entry read-only | not applicable | route with lotExpiry or dualQuantity on; each control with its flag |
| SCR-INV-010 | `PERM_INV_OPENING_STOCK_VIEW` | Post and Load sheet hidden (download stays) | not applicable | not applicable | lot, expiry and measure columns with their flags |
| SCR-INV-011 | `PERM_INV_SETTINGS_VIEW` | not applicable | form read-only | not applicable | horizon with lotExpiry on |
| SCR-INV-012 | `PERM_INV_STOCK_VALUATION_VIEW` | not applicable | not applicable | not applicable | — |

**Inventory menu.** Each entry is shown when the user holds the screen's `VIEW` and its gate passes. With the
seeded role presets (REQ-INV-136, REQ-INV-137) this gives the following:
- Warehouse role: stock balances, stock moves, counts, adjustments, lots (lotExpiry on) and transfers (two or more
  active warehouses). Opening stock, stock valuation and inventory settings are not shown (AC-INV-152).
- Seller/Cashier: stock balances only (AC-INV-153).
- Owner: every entry its gates allow.
The menu re-reads INV-CONTEXT after any warehouse or branch change, so a second warehouse reveals the transfer
entry with no re-setup (REQ-INV-015).
<!-- PHASE:SEC-FE:END -->
