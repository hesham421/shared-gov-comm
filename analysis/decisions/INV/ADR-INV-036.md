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

## Decision
- Frontend, now: the warehouse column and the warehouse filter are labelled through the shared warehouse lookup
  (API-INV-001, `active=ALL`) when the user holds `PERM_INV_WAREHOUSES_VIEW` (Owner, Warehouse). For a user
  without it, the warehouse label is **PENDING ADR-INV-036**. The column shows the identifier, and the filter is
  not offered. The lot filter is offered only with `PERM_INV_LOTS_VIEW`. The lot column is always shown, from
  `lotCode`.
- Backend gap, named here: `StockBalanceRow` and `Availability.perWarehouse` should carry `warehouseCode`,
  `warehouseNameAr` and `warehouseNameEn`. The P3.1 delta adds them, and the frontend then reads the label from
  the row with no change to its hooks.

## Consequences
- Until that P3.1 delta, AC-INV-044 holds for the Owner and Warehouse roles. For a Seller/Cashier in a tenant with
  two or more active warehouses, it holds by identifier only. In a single-warehouse tenant the column is hidden
  anyway (REQ-INV-012).
- This stage grants no permission and invents no field.

## Status
ACCEPTED — non-breaking (open item for the P3.1 delta).
