<!-- source: PHASE:F2 / SUB:F2-SCR-INV-008 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-121, AC-INV-122, AC-INV-129, AC-INV-130, API-INV-001, API-INV-008, API-INV-040, API-INV-041, API-INV-042, REQ-INV-108, REQ-INV-109, REQ-INV-114, REQ-INV-115, REQ-INV-116, REQ-INV-117, REQ-INV-134, SCR-INV-008, UXD-INV-008 -->
<!-- SUB:F2-SCR-INV-008:START traces=SCR-INV-008,UXD-INV-008,REQ-INV-108,REQ-INV-109,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-134,AC-INV-121,AC-INV-122,AC-INV-129,AC-INV-130,API-INV-001,API-INV-008,API-INV-040,API-INV-041,API-INV-042 -->
### F2 · SCR-INV-008 — Lots and near expiry

```yaml name=screen-hooks
screen: SCR-INV-008
hooks:
  - {hook: LOTS-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: LOTS-QUERY, kind: read, api: [API-INV-040], cache_key: "['inv','lots',filter{variantPk,code,warehousePk,expiryFrom,expiryTo,nearExpiryOnly,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: LOT-QUERY, kind: read, api: [API-INV-041], cache_key: "['inv','lot',lotPk]", errors: "INV-404-LOT-NOT-FOUND → message, drawer closes", loading: LOCAL, invalidation: "—"}
  - {hook: LOT-CORRECT, kind: mutation, api: [API-INV-042], errors: "STALE-VERSION → reload; INV-404-LOT-NOT-FOUND → message", invalidation: "['inv','lots'], ['inv','lot',id], ['inv','lookup','lots'], ['inv','stock-balances'], ['inv','availability']"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active}]", errors: "generic", loading: LOCAL}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','variant-search',term] · ['cat','variant',variantPk]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: LOTS-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| LOTS-SCREEN-INIT | Permission read: `PERM_INV_LOTS_VIEW`, `PERM_INV_LOTS_UPDATE`. Context: `lotExpiryFl`; with it off the route answers "not available" and the menu hides it (REQ-INV-134). Warehouse count. Entity by id: LOT-QUERY when `entry=<id>` |
| LOTS-QUERY | read, ordered by expiry date (REQ-INV-109). `nearExpiryOnly` gives the near-expiry view (REQ-INV-116); rows carry `daysLeft`, `expiredFl` (REQ-INV-115) and `nearExpiryNotifiedAt` (REQ-INV-117) |
| LOT-CORRECT | mutation `{expiryDate, nameAr, nameEn, version}` (REQ-INV-108). INV-409-STALE-VERSION → reload · `INV-404-*` → catalogue · generic |
| CAT-VARIANT-LOOKUP | UXD-INV-008: the Variant filter, and labels of `LotRow.variantPk`, deduplicated per page |
| LOTS-FACADE | Owns the rows, the filter with page and size (`nearExpiryOnly` seeded by the notification link), the `entry` param and derived loading. Operations: `saveExpiry()`, `writeOff(row)` (navigates to SCR-INV-007 `/new?reason=EXPIRY_WRITE_OFF&lotPk&warehousePk`, offered for `expiredFl` rows with on-hand ≠ 0), `openBalances(row)` |
<!-- SUB:F2-SCR-INV-008:END -->
