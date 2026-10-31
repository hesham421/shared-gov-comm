<!-- source: PHASE:F2 / SUB:F2-SCR-INV-012 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-069, API-INV-008, API-INV-050, API-INV-051, REQ-INV-059, REQ-INV-061, REQ-INV-063, SCR-INV-012, UXD-INV-011 -->
<!-- SUB:F2-SCR-INV-012:START traces=SCR-INV-012,UXD-INV-011,REQ-INV-059,REQ-INV-061,REQ-INV-063,AC-INV-069,API-INV-008,API-INV-050,API-INV-051 -->
### F2 · SCR-INV-012 — Stock valuation

```yaml name=screen-hooks
screen: SCR-INV-012
hooks:
  - {hook: VALUATION-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: VALUATION-QUERY, kind: read, api: [API-INV-050], cache_key: "['inv','valuation',filter{variantPk,term,itemPk,nonZero,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: VALUATION-DETAIL-QUERY, kind: read, api: [API-INV-051], cache_key: "['inv','valuation','variant',variantPk]", errors: "INV-404-VARIANT-NOT-FOUND → message, detail closes", loading: LOCAL, invalidation: "—"}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','variant-search',term]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: CAT-ITEM-LOOKUP, kind: lookup, cache_key: "['cat','item-search',term]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: VALUATION-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| VALUATION-SCREEN-INIT | Permission read: `PERM_INV_STOCK_VALUATION_VIEW` |
| VALUATION-QUERY | read. Rows carry total quantity, weighted-average unit cost and stock value (REQ-INV-063, REQ-INV-059, REQ-INV-061). The page carries `grandTotalValue`, shown as the screen total |
| VALUATION-DETAIL-QUERY | read of the `variant=<id>` detail drawer, which adds the last move |
| CAT-VARIANT-LOOKUP / CAT-ITEM-LOOKUP | UXD-INV-011: the Variant and Item filters set `variantPk` / `itemPk`; a scanned barcode goes to `term` |
| VALUATION-FACADE | Owns the rows, the total, the filter with page and size, the `variant` param and derived loading. Operation: `openMoves(variantPk)`. No mutation |
<!-- SUB:F2-SCR-INV-012:END -->
