<!-- source: PHASE:F2 / SUB:F2-SCR-CAT-005 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-007, API-CAT-042, API-CAT-050, API-CAT-056, REQ-CAT-101, REQ-CAT-102, REQ-CAT-103, REQ-CAT-104, REQ-CAT-148, SCR-CAT-005 -->
<!-- SUB:F2-SCR-CAT-005:START traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-148,API-CAT-007,API-CAT-042,API-CAT-050,API-CAT-056,SCR-CAT-005 -->
### F2 · SCR-CAT-005 — Barcode labels

```yaml name=screen-hooks
screen: SCR-CAT-005
hooks:
  - {hook: VARIANT-SEARCH-QUERY, kind: read, api: [API-CAT-042], cache_key: "[cat, variants, search, {text, page, size}]", errors: "CAT-403 → unauthorized · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: SEEDED-VARIANT-QUERY, kind: read, api: [API-CAT-007], cache_key: "[cat, variants, detail, variantPk]", errors: "CAT-404-NOT-FOUND / CAT-403-FORBIDDEN → seed dropped with a notice (ADR-CAT-051)", loading: LOCAL, invalidation: "—"}
  - {hook: LABEL-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized", loading: NONE}
  - {hook: LABEL-PRINT, kind: mutation, api: [API-CAT-050], errors: "see LABEL-PRINT below", loading: LOCAL, invalidation: "—"}
  - {hook: LABEL-INTERNAL-BARCODE, kind: mutation, api: [API-CAT-056], errors: "see LABEL-INTERNAL-BARCODE below", loading: LOCAL, invalidation: "[cat, variants, search] · [cat, variants, detail, variantPk] · [cat, items, detail]"}
  - {hook: LABEL-FACADE, kind: facade}
```

#### VARIANT-SEARCH-QUERY — API-CAT-042            traces=API-CAT-042,REQ-CAT-101
Feeds the Variant picker: active Variants by code, name or barcode, paged. Each row carries `itemNameAr` and `firstBarcodeValue`, the label barcode — the earliest-created active GTIN or INTERNAL barcode bound to the Item's base unit (empty when none — ADR-CAT-079).

#### SEEDED-VARIANT-QUERY — API-CAT-007            traces=API-CAT-007,REQ-CAT-101
Runs once when the route carries `?variantPk=`. It adds that one Variant as a line, with its label barcode from `barcodes[]`: the earliest-created active barcode of type GTIN or INTERNAL whose `uomCode` equals `baseUomCode` (ADR-CAT-079).

#### LABEL-PRINT — API-CAT-050            traces=API-CAT-050,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104
Mutation, the screen's one submit. It sends `{lines: [{variantPk, copies}]}`, and the PDF body opens in a new tab. Errors:
- `CAT-422-LABEL-COPIES-OUT-OF-RANGE` → inline on the copies of the offending lines and on the total; text: RULE-CAT-036 message
- `CAT-422-LABEL-VARIANT-NO-BARCODE` → form banner; text: RULE-CAT-037 message (the server fills {count}). Lines whose `barcode` is empty are flagged `no-barcode`, each with "Generate internal barcode / إنشاء باركود داخلي" (LABEL-INTERNAL-BARCODE — shown with PERM_CAT_BARCODE_LABELS_CREATE, ADR-CAT-078) and, for a user holding PERM_CAT_ITEMS_VIEW, "Open item" → `/cat/items/:itemPk` (ADR-CAT-051 §5)
- `CAT-404-NOT-FOUND` → lines no longer found are flagged `not-found`; generic text

#### LABEL-INTERNAL-BARCODE — API-CAT-056            traces=API-CAT-056,REQ-CAT-148,REQ-CAT-104
Mutation, a record command on one line flagged `no-barcode`, run at once after a confirmation (it is not a second submit of the form). It sends an empty body, so the barcode is bound to the Item's base unit, and the server runs the rules and locks of the Items screen's internal-barcode operation (ADR-CAT-078). On success the line takes the returned `barcodeValue`, its flag is cleared, and Print can run. Errors (the line keeps its flag and shows the text):
- `CAT-422-PARENT-INACTIVE` → on the line; text: RULE-CAT-053 message (the Variant or its Item was deactivated meanwhile)
- `CAT-422-BARCODE-UNIT-INVALID` → on the line; text: RULE-CAT-031 message
- `CAT-422-LOOKUP-VALUE-INACTIVE` → on the line; text: RULE-CAT-006 message
- `CAT-409-BARCODE-SCALE-PREFIX` → on the line; text: RULE-CAT-032 message
- `CAT-409-BARCODE-DUPLICATE` → on the line; text: RULE-CAT-029 message (the server fills {code})
- `CAT-500-CODE-NOT-ISSUED` → on the line with Try again; text: RULE-CAT-005 message
- `CAT-500-CHANGE-NOT-SAVED` → on the line with Try again; text: RULE-CAT-049 message
- `CAT-404-NOT-FOUND` → the line is flagged `not-found`; generic text
- other → as §3.0

#### LABEL-SCREEN-INIT — SCR-CAT-005
Permission read PERM_CAT_BARCODE_LABELS_VIEW / CREATE; no lookups; the seed from `?variantPk=`.

#### LABEL-FACADE — SCR-CAT-005
It owns: the lines (form state), the picker filters (URL, `?picker=variants&text=`), the derived total and the pre-flagged lines without a barcode. Operations: `addVariants(rows)` (duplicates merged, copies 1), `removeLine()`, `generateInternalBarcode(line)` (LABEL-INTERNAL-BARCODE, on a `no-barcode` line), `print()`.
<!-- SUB:F2-SCR-CAT-005:END -->
