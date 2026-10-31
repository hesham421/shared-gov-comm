<!-- source: PHASE:F2 / SUB:F2-SCR-INV-009 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-139, AC-INV-141, AC-INV-142, AC-INV-143, AC-INV-144, API-INV-008, API-INV-043, API-INV-044, API-INV-045, REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-128, REQ-INV-129, REQ-INV-134, REQ-INV-135, SCR-INV-009, UXD-INV-009, UXD-INV-010 -->
<!-- SUB:F2-SCR-INV-009:START traces=SCR-INV-009,UXD-INV-009,UXD-INV-010,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,AC-INV-139,AC-INV-141,AC-INV-142,AC-INV-143,AC-INV-144,API-INV-008,API-INV-043,API-INV-044,API-INV-045 -->
### F2 · SCR-INV-009 — Item stock settings

```yaml name=screen-hooks
screen: SCR-INV-009
hooks:
  - {hook: ITEM-SETTINGS-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: ITEM-SETTINGS-SEARCH-QUERY, kind: read, api: [API-INV-043], cache_key: "['inv','item-settings-search',filter{itemPk,term,lotTracked,dualQuantity,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: ITEM-TRACKING-LOOKUP, kind: read, api: [API-INV-044], cache_key: "['inv','item-settings',itemPk]", errors: "INV-404-ITEM-NOT-FOUND → message, drawer closes", loading: LOCAL, invalidation: "—"}
  - {hook: ITEM-SETTINGS-SAVE, kind: mutation, api: [API-INV-045], errors: "LOT-TRACKING-IN-USE / DUAL-QUANTITY-IN-USE / BUNDLE-SETTINGS / MEASURE-UNIT-REQUIRED → inline; CAPABILITY-OFF → message; STALE-VERSION → reload", invalidation: "['inv','item-settings-search'], ['inv','item-settings',itemPk]"}
  - {hook: CAT-ITEM-LOOKUP, kind: lookup, cache_key: "['cat','item-search',term] · ['cat','item',itemPk]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: CAT-UOM-LOOKUP, kind: lookup, cache_key: "['cat','uom']", loading: NONE}
  - {hook: ITEM-SETTINGS-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| ITEM-SETTINGS-SCREEN-INIT | Permission read: VIEW / CREATE / UPDATE of `INV_ITEM_STOCK_SETTINGS`. Context: `lotExpiryFl`, `dualQuantityFl`. With both off the screen is not reachable; each control shows only with its flag (REQ-INV-134, REQ-INV-135). Entity by id: ITEM-TRACKING-LOOKUP on `entry=<itemPk>` |
| ITEM-SETTINGS-SEARCH-QUERY | read. Rows carry the Item's code and names from the INV answer |
| ITEM-TRACKING-LOOKUP | read of one Item's settings for the entry (REQ-INV-129); the same shared key the line drawers use |
| ITEM-SETTINGS-SAVE | mutation `{lotTrackedFl, dualQuantityFl, measureUomCode, version | null}`; idempotent create-or-update (ADR-INV-029). INV-409-LOT-TRACKING-IN-USE → inline on lotTrackedFl, text: RULE-INV-042 message (SRS) (the server text carries the count) · INV-409-DUAL-QUANTITY-IN-USE → inline on dualQuantityFl, text: RULE-INV-043 message (SRS) · INV-422-BUNDLE-SETTINGS → inline on the Item, text: RULE-INV-045 message (SRS) · INV-422-MEASURE-UNIT-REQUIRED → inline on measureUomCode, text: RULE-INV-041 message (SRS) · INV-422-CAPABILITY-OFF → message, text: RULE-INV-044 message (SRS) (the catalogue text) · INV-409-STALE-VERSION → reload · INV-404-ITEM-NOT-FOUND → catalogue |
| CAT-ITEM-LOOKUP | UXD-INV-009: the Item pick of a new record. A BUNDLE Item is shown with its kind; the server refuses it (RULE-INV-045) |
| CAT-UOM-LOOKUP | UXD-INV-010: measureUomCode options and labels |
| ITEM-SETTINGS-FACADE | Owns the rows, the filter with page and size, the `entry` param, form state and derived loading. Operations: `save()`, `openEntry(itemPk | 'new')`, `closeEntry()` |
<!-- SUB:F2-SCR-INV-009:END -->
