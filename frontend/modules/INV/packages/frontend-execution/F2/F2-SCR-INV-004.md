<!-- source: PHASE:F2 / SUB:F2-SCR-INV-004 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-082, AC-INV-083, API-INV-001, API-INV-008, API-INV-018, API-INV-019, API-INV-040, API-INV-052, REQ-INV-016, REQ-INV-055, REQ-INV-056, REQ-INV-075, REQ-INV-076, SCR-INV-004, UXD-INV-004 -->
<!-- SUB:F2-SCR-INV-004:START traces=SCR-INV-004,UXD-INV-004,REQ-INV-016,REQ-INV-055,REQ-INV-056,REQ-INV-075,REQ-INV-076,AC-INV-082,AC-INV-083,API-INV-001,API-INV-008,API-INV-018,API-INV-019,API-INV-040,API-INV-052 -->
### F2 · SCR-INV-004 — Stock move history

```yaml name=screen-hooks
screen: SCR-INV-004
hooks:
  - {hook: MOVES-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: MOVES-QUERY, kind: read, api: [API-INV-018], cache_key: "['inv','stock-moves',filter{variantPk,warehousePk,lotPk,moveTypeCode,docNo,sourceDocTypeCode,sourceDocRef,dateFrom,dateTo,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: MOVE-QUERY, kind: read, api: [API-INV-019], cache_key: "['inv','stock-move',stockMovePk]", errors: "INV-404-STOCK-MOVE-NOT-FOUND → message, detail closes", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active:'ALL'}]", errors: "generic", loading: LOCAL}
  - {hook: LOTS-LOOKUP, kind: lookup, api: [API-INV-040], cache_key: "['inv','lookup','lots',{variantPk,warehousePk}]", errors: "generic", loading: LOCAL}
  - {hook: LOOKUP-INV_MOVE_TYPE, kind: lookup, api: [API-INV-052], cache_key: "['inv','lookup-key','INV_MOVE_TYPE']", loading: NONE}
  - {hook: LOOKUP-INV_SOURCE_DOC_TYPE, kind: lookup, api: [API-INV-052], cache_key: "['inv','lookup-key','INV_SOURCE_DOC_TYPE']", loading: NONE}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','variant-search',term]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: MOVES-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| MOVES-SCREEN-INIT | Permission read: `PERM_INV_STOCK_MOVES_VIEW`. Context: warehouse count (REQ-INV-012) and flags. Lookups: move type and document type |
| MOVES-QUERY | read, newest first (REQ-INV-075). `balanceAfterQty` is shown only while the filter names one Variant and one warehouse (REQ-INV-076); in that view the server orders the rows by posting order, newest posted first, and the screen keeps the server's order, so a backdated document posted later sits where its running balance belongs. A jump from a posted transfer or adjustment filters by `sourceDocTypeCode` + `sourceDocRef`, so only that document's moves are listed, never another module's document with the same number. Moves are read-only; no mutation exists (REQ-INV-055) |
| MOVE-QUERY | read of the detail drawer opened by `move=<id>`: source document type, identifier and number (REQ-INV-056), offline flag and bundle |
| WAREHOUSES-LOOKUP / LOTS-LOOKUP | labels: warehouse with `active=ALL`; the lot filter options scoped by the chosen Variant and warehouse — the lot label of each row is the row's `lotCode`, never a lookup (CORE "Labels on every row") |
| CAT-VARIANT-LOOKUP | UXD-INV-004: the Variant filter, and the label of the move detail's `bundleVariantPk` through `['cat','variant',variantPk]`. Row Variant labels come from `StockMove.variantCode / variantNameAr / variantNameEn` |
| MOVES-FACADE | Owns the rows, the filter (with page and size), the `move` navigation param and derived `showRunningBalance`. No mutation |
<!-- SUB:F2-SCR-INV-004:END -->
