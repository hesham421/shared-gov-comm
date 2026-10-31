# ADR-INV-036 — Warehouse and lot labels on the stock balance screen for a Seller/Cashier
Status      : ACCEPTED
Stage       : P3.2 (Frontend — UX Design + Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : SCR-INV-003, REQ-INV-012, REQ-INV-039, REQ-INV-040, REQ-INV-137, AC-INV-044, AC-INV-153, API-INV-001, API-INV-016, API-INV-017, API-INV-040

## Context
SCR-INV-003 (Stock balances) is the only INV page a Seller/Cashier sees. B4 gives that role
`PERM_INV_STOCK_BALANCES_VIEW` only. The balance row (`StockBalanceRow`) and the availability answer
(`Availability.perWarehouse`) carry `warehousePk` but no warehouse code or name. Labelling that id or offering
the warehouse filter needs API-INV-001, which requires `PERM_INV_WAREHOUSES_VIEW`, and a Seller/Cashier does not
hold it. The lot filter has the same problem: its options come from API-INV-040, which requires
`PERM_INV_LOTS_VIEW`. The lot column itself is fine, because the row carries `lotCode`.

AC-INV-044 expects a Seller/Cashier to read rows "W1 … W2" by warehouse. Showing a bare identifier would fail it.

## Decision (revised at gate-analysis revise 1, G13 — the backend delta is applied)
- **Backend.** `StockBalanceRow` (API-INV-016) and `Availability.perWarehouse` (API-INV-017) carry
  `warehouseCode`, `warehouseNameAr` and `warehouseNameEn`, read by join in QR-INV-015 / QR-INV-016 (DBF-INV-004,
  005, 006). They are in the Response lines of the backend plan and in api-spec-inv.yaml.
- **Frontend.** The warehouse column of SCR-INV-003 and the per-warehouse rows of its availability summary read the
  label from the row, for every role; no warehouse lookup is needed to label them. The warehouse **filter** still
  needs its options from API-INV-001, so it is offered only to a user holding `PERM_INV_WAREHOUSES_VIEW` (Owner,
  Warehouse). The lot filter is offered only with `PERM_INV_LOTS_VIEW`; the lot column is always shown, from
  `lotCode`.
- No label is PENDING any more.

## Consequences
- AC-INV-044 holds for every role, the Seller/Cashier included ("W1 / W2" rows by name).
- No permission is granted; the label fields are derived reads of DBF columns, not new columns.

## Status
ACCEPTED — non-breaking. The open item of round 1 is closed.
