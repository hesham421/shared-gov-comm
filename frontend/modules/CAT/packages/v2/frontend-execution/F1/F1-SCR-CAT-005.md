<!-- source: PHASE:F1 / SUB:F1-SCR-CAT-005 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-007, API-CAT-042, API-CAT-050, API-CAT-056, REQ-CAT-101, REQ-CAT-102, REQ-CAT-103, REQ-CAT-104, REQ-CAT-148, SCR-CAT-005 -->
<!-- SUB:F1-SCR-CAT-005:START traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-148,API-CAT-007,API-CAT-042,API-CAT-050,API-CAT-056,SCR-CAT-005 -->
### F1 · SCR-CAT-005 — Barcode labels

Bound schemas: `VariantSearchRow` page (API-CAT-042) · `VariantDetail` (API-CAT-007, for the seeded line) · `LabelRequest` (API-CAT-050) → PDF binary · `LabelInternalBarcodeRequest` (empty body — the base unit) → `BarcodeView` (API-CAT-056, the missing internal barcode of a flagged line — ADR-CAT-078).

UI types:
- `VariantSearchFilters = { text?, page, size }` (search params of the picker).
- `LabelLine = { variantPk, itemPk, code, nameAr, itemNameAr, barcode: string | null, copies, flagged?: 'no-barcode' | 'not-found' }`. It is projected from `VariantSearchRow` (`firstBarcodeValue`, which the server already narrows to the label barcode) or from `VariantDetail` (the label barcode: the first active `BarcodeView` by `createdAt` whose `barcodeTypeCode` is GTIN or INTERNAL and whose `uomCode` equals `VariantDetail.baseUomCode` — ADR-CAT-079). A SCALE code or a barcode bound to an Item unit never fills `barcode`.
- `LabelForm = { lines: LabelLine[] }`. The request sends only `{ variantPk, copies }` per line.
<!-- SUB:F1-SCR-CAT-005:END -->
