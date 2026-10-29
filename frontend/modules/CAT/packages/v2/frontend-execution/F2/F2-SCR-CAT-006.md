<!-- source: PHASE:F2 / SUB:F2-SCR-CAT-006 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-045, API-CAT-051, API-CAT-052, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023, REQ-CAT-092, REQ-CAT-093, REQ-CAT-094, REQ-CAT-095, REQ-CAT-098, REQ-CAT-120, REQ-CAT-121, REQ-CAT-122, REQ-CAT-123, REQ-CAT-124, REQ-CAT-134, SCR-CAT-006, UXD-CAT-001 -->
<!-- SUB:F2-SCR-CAT-006:START traces=API-CAT-052,REQ-CAT-134,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-045,API-CAT-051,UXD-CAT-001,SCR-CAT-006 -->
### F2 · SCR-CAT-006 — Catalogue settings

```yaml name=screen-hooks
screen: SCR-CAT-006
hooks:
  - {hook: SETTINGS-QUERY, kind: read, api: [API-CAT-045], cache_key: "[cat, settings]", errors: "CAT-403 → unauthorized · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: TURN-OFF-CHECK-QUERY, kind: read, api: [API-CAT-051], cache_key: "[cat, capability-checks, turn-off, flag]", errors: "CAT-400-REQUEST-INVALID → inline on the flag selector · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CAT_UOM-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_UOM]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAT_EINVOICE_UNIT-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_EINVOICE_UNIT]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAT_SCALE_VALUE_TYPE-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_SCALE_VALUE_TYPE]", errors: "select disabled, generic", loading: NONE}
  - {hook: SETTINGS-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized", loading: NONE}
  - {hook: LAYOUT-SAVE, kind: mutation, api: [API-CAT-032, API-CAT-033], errors: "see LAYOUT-SAVE below", loading: LOCAL, invalidation: "[cat, settings]"}
  - {hook: LAYOUT-DEACTIVATE, kind: mutation, api: [API-CAT-034], errors: "generic", loading: LOCAL, invalidation: "[cat, settings]"}
  - {hook: UNIT-CODE-SAVE, kind: mutation, api: [API-CAT-035, API-CAT-036], errors: "see UNIT-CODE-SAVE below", loading: LOCAL, invalidation: "[cat, settings] · [cat, variants, detail]"}
  - {hook: UNIT-CODE-DEACTIVATE, kind: mutation, api: [API-CAT-037], errors: "generic", loading: LOCAL, invalidation: "[cat, settings] · [cat, variants, detail]"}
  - {hook: SETTINGS-FACADE, kind: facade}
```

#### SETTINGS-QUERY — API-CAT-045            traces=API-CAT-045,REQ-CAT-095
Both lists in full, each entry with its active state. Each drawer edits a list entry (there is no read-by-id operation for these records), taking `version` from the list. A Unit e-invoice code row is labelled by the CAT_UOM lookup of its `key`, and by `valueAr` / `valueEn` when present.

#### TURN-OFF-CHECK-QUERY — API-CAT-051            traces=API-CAT-051,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023
Read run on "Check" with `flag`. Allowed → "allowed-yes" copy. Not allowed → the envelope's success code `CAT-200-VARIANTS-IN-USE` / `CAT-200-BUNDLES-IN-USE` with RULE-CAT-010 / RULE-CAT-011 message (the server fills {count}), `dependentCount`, and up to 20 `dependentItems` (code, nameAr). Each one links to `/cat/items/:itemPk`.

#### LAYOUT-SAVE — API-CAT-032, API-CAT-033            traces=API-CAT-032,API-CAT-033,REQ-CAT-092,REQ-CAT-093,REQ-CAT-098
Mutation, the layout drawer's one Save. Errors:
- `CAT-422-SCALE-LAYOUT-INVALID` → inline on key, scaleCodeLength and valueDecimals; text: RULE-CAT-034 message
- `CAT-409-BARCODE-SCALE-PREFIX` (create: existing barcodes start with the prefix) → inline on key; text: RULE-CAT-032 message
- `CAT-422-LOOKUP-VALUE-INACTIVE` → inline on embeddedValueTypeCode; text: RULE-CAT-006 message
- `CAT-409-STALE-VERSION` → drawer banner + Reload; text: catalogue CAT-409-STALE-VERSION

#### UNIT-CODE-SAVE — API-CAT-035, API-CAT-036            traces=API-CAT-035,API-CAT-036,REQ-CAT-120,REQ-CAT-121,REQ-CAT-124
Mutation, the unit code drawer's one Save. Errors:
- `CAT-409-UNIT-EINVOICE-DUPLICATE` → inline on key; text: RULE-CAT-043 message, with "Open the existing code" → `?unitCode=<pk>` (the active entry with that key, from SETTINGS-QUERY)
- `CAT-422-LOOKUP-VALUE-INACTIVE` → inline on key or einvoiceUnitCode; text: RULE-CAT-006 message
- `CAT-409-STALE-VERSION` → drawer banner + Reload

#### LAYOUT-DEACTIVATE / UNIT-CODE-DEACTIVATE — API-CAT-034, API-CAT-037            traces=API-CAT-034,API-CAT-037,REQ-CAT-094,REQ-CAT-122
Record commands in their drawers, after a confirmation.

#### SETTINGS-SCREEN-INIT — SCR-CAT-006
Permission read PERM_CAT_SETTINGS_VIEW / CREATE / UPDATE / DELETE; lookups CAT_UOM, CAT_EINVOICE_UNIT, CAT_SCALE_VALUE_TYPE; entity by id: the list entry. A new layout starts with scaleCodeLength 5, WEIGHT, decimals 3.

#### SETTINGS-FACADE — SCR-CAT-006
It owns: the open drawer (`?layout=` / `?unitCode=`), the chosen check flag (`?check=`), the drawer forms, derived loading. Operations: `saveLayout()`, `deactivateLayout()`, `saveUnitCode()`, `deactivateUnitCode()`, `runCheck(flag)`.
<!-- SUB:F2-SCR-CAT-006:END -->
