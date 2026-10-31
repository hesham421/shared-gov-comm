<!-- source: PHASE:F2 / SUB:F2-SCR-INV-003 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-043, AC-INV-044, AC-INV-045, AC-INV-047, API-INV-001, API-INV-008, API-INV-016, API-INV-017, API-INV-040, REQ-INV-012, REQ-INV-035, REQ-INV-038, REQ-INV-039, REQ-INV-040, REQ-INV-041, REQ-INV-042, REQ-INV-113, REQ-INV-115, REQ-INV-121, REQ-INV-135, REQ-INV-137, SCR-INV-003, UXD-INV-001, UXD-INV-002, UXD-INV-003 -->
<!-- SUB:F2-SCR-INV-003:START traces=SCR-INV-003,UXD-INV-001,UXD-INV-002,UXD-INV-003,REQ-INV-012,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-113,REQ-INV-115,REQ-INV-121,REQ-INV-135,REQ-INV-137,AC-INV-043,AC-INV-044,AC-INV-045,AC-INV-047,API-INV-001,API-INV-008,API-INV-016,API-INV-017,API-INV-040 -->
### F2 · SCR-INV-003 — Stock balances

```yaml name=screen-hooks
screen: SCR-INV-003
hooks:
  - {hook: BALANCES-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: BALANCES-QUERY, kind: read, api: [API-INV-016], cache_key: "['inv','stock-balances',filter{variantPk,term,itemPk,warehousePk,lotPk,nonZero,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: AVAILABILITY-QUERY, kind: read, api: [API-INV-017], cache_key: "['inv','availability',variantPk,warehousePk]", errors: "BUNDLE-NO-COMPONENTS → summary message; VARIANT-NOT-FOUND / WAREHOUSE-NOT-FOUND → summary message", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active}]", errors: "INV-403 → warehouse filter not offered (labels come from the rows)", loading: LOCAL}
  - {hook: LOTS-LOOKUP, kind: lookup, api: [API-INV-040], cache_key: "['inv','lookup','lots',{variantPk,warehousePk}]", errors: "INV-403 → lot filter not offered", loading: LOCAL}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','variant-search',term] · ['cat','barcode',barcode]", errors: "not found → 'no results' in the picker", loading: LOCAL}
  - {hook: CAT-ITEM-LOOKUP, kind: lookup, cache_key: "['cat','item-search',term] · ['cat','item',itemPk]", errors: "not found → name left empty", loading: LOCAL}
  - {hook: CAT-UOM-LOOKUP, kind: lookup, cache_key: "['cat','uom']", errors: "unknown code → the code is shown", loading: NONE}
  - {hook: BALANCES-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| BALANCES-SCREEN-INIT | Permission read: `PERM_INV_STOCK_BALANCES_VIEW`, plus `PERM_INV_WAREHOUSES_VIEW` and `PERM_INV_LOTS_VIEW` to decide whether the warehouse and lot filter lookups run (ADR-INV-036 — labels never depend on them). Context: `activeWarehouseCount` (filter and column hidden while it is 1 — REQ-INV-012), `lotExpiryFl` (lot filter and columns), `dualQuantityFl` (measure column — REQ-INV-135) |
| BALANCES-QUERY | read. Each row carries the Variant code and name, the warehouse code and names (the warehouse column's label for every role — ADR-INV-036), lot code, expiry, expired mark (REQ-INV-115), on-hand, reserved and available (REQ-INV-038, REQ-INV-113) and the measure. Reserved and available are figures of the Variant × warehouse pair, repeated by the server on every lot row of that pair (QR-INV-015); the screen shows them once per pair (F4 below). A Variant with no record answers zeros (REQ-INV-041) and the screen shows them as such |
| AVAILABILITY-QUERY | read, enabled only when the filter names one Variant. It shows per-warehouse rows (labelled from their `warehouseCode` / names) and total availability (REQ-INV-040); for a bundle Variant this is the derived availability (REQ-INV-042). INV-422-BUNDLE-NO-COMPONENTS → message in the summary, text: RULE-INV-022 message (SRS) · `INV-404-*` → summary message, text: catalogue |
| WAREHOUSES-LOOKUP | fills the warehouse filter only (the rows label themselves). Without `PERM_INV_WAREHOUSES_VIEW` it does not run and the filter is not offered (ADR-INV-036) |
| LOTS-LOOKUP | lot filter options, scoped by the chosen Variant; run only with `PERM_INV_LOTS_VIEW` |
| CAT-VARIANT-LOOKUP | UXD-INV-001: the Variant filter. Choosing a Variant sets `variantPk`. A scanned barcode goes to `term` unchanged |
| CAT-ITEM-LOOKUP | UXD-INV-002: the Item filter sets `itemPk`. The Item name column reads `['cat','variant',variantPk]` → `itemPk` → `['cat','item',itemPk]`, deduplicated per page |
| CAT-UOM-LOOKUP | UXD-INV-003: labels `measureUomCode` beside the measure (REQ-INV-121) |
| BALANCES-FACADE | Owns the rows from query data, the filter (with page and size) mirrored to the URL, the summary data and derived loading. Operations: `setFilter`, `openMoves(row)`, which navigates to SCR-INV-004 with `variantPk`, `warehousePk` and `lotPk`. No mutation |
<!-- SUB:F2-SCR-INV-003:END -->
