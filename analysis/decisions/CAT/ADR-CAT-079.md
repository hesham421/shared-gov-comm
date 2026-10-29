# ADR-CAT-079 — A label shows only an active GTIN or INTERNAL barcode in the Item's base unit

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 / P3.2 / P4 (gate-analysis round 11, G1) · Module : CAT v2 · Date : 2026-10-29

## Context
- REQ-CAT-101, REQ-CAT-102, QR-CAT-016 and QR-CAT-047 took a Variant's earliest-created active barcode as its label barcode, whatever its type or unit. RULE-CAT-037 counted any active barcode.
- A Variant carries 0..n barcodes, each of type GTIN, INTERNAL or SCALE and each bound to one unit (D-05, RULE-CAT-031). Two counter-cases had no statement and no test:
  1. the only active barcode is a SCALE code of 4 to 6 digits (RULE-CAT-033). Printed on its own, it is not a scannable label: a scale code is read only inside a 13-digit scale label (RULE-CAT-035);
  2. the earliest barcode is bound to CARTON (an Item unit). A piece label then scans as a carton (REQ-CAT-086 returns the barcode's unit).

Options:
- (a) the label barcode is the earliest-created active barcode of type GTIN or INTERNAL whose uomCode equals the Item's baseUomCode; RULE-CAT-037 counts only such barcodes;
- (b) let the user pick the barcode per label line;
- (c) print every active barcode of the Variant.

## Decision
Option (a).
- Option (b) adds a choice to every line for a case the system can decide, and still needs a default that is (a).
- Option (c) prints codes that do not scan as one piece.

1. **Label barcode.** The earliest-created (createdAt, then barcodePk) active barcode of the Variant whose barcodeTypeCode is GTIN or INTERNAL and whose uomCode equals the Item's baseUomCode.
2. **Reads.** QR-CAT-016 returns it as `firstBarcodeValue` of `VariantSearchRow` (the name is kept, its meaning narrowed; the API document states it); QR-CAT-047 gains a label filter (type and base unit) used by API-CAT-050 only — the Item and Variant reads still return every active barcode.
3. **RULE-CAT-037.** It counts only such barcodes. A Variant without one is flagged and gets the existing next action, API-CAT-056, which generates an INTERNAL barcode in the base unit (ADR-CAT-078). The message names the base unit: ar «لا يوجد باركود نشط بالوحدة الأساسية لـ {count} متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع.» · en "{count} variants have no active barcode in the base unit. Generate an internal barcode for them, then print."
4. **Search by barcode.** The text filter of QR-CAT-016 still matches any active barcode, so a Variant can be found by its scale code or carton barcode; only the label barcode shown and printed is narrowed.

## Consequences
- SRS: REQ-CAT-101, 102, 104, 148, RULE-CAT-037 (statement, message, data source), AC-CAT-103, 106; new AC-CAT-172 (only a SCALE code) and AC-CAT-173 (earlier CARTON barcode, later PIECE barcode).
- Backend plan: RULE-CAT-037 under ENT-CAT-009, API-CAT-042 and API-CAT-050, QR-CAT-016 and QR-CAT-047, error catalog row CAT-422-LABEL-VARIANT-NO-BARCODE. api-spec: `VariantSearchRow.firstBarcodeValue` description, x-traces of API-CAT-042 and 050.
- Frontend plan: `LabelLine.barcode` projection (SEEDED-VARIANT-QUERY filters `VariantDetail.barcodes` by type and `baseUomCode`); ui-ux-spec copy of the flag.
- Tests: TC-CAT-233 (only a SCALE code), TC-CAT-234 (CARTON then PIECE); TC-CAT-053, 111, 231 and frontend TC-CAT-167, 170 re-worded.

## Traces
REQ-CAT-101 · REQ-CAT-102 · REQ-CAT-104 · REQ-CAT-148 · AC-CAT-103 · AC-CAT-106 · AC-CAT-172 · AC-CAT-173 · RULE-CAT-037 · RULE-CAT-031 · RULE-CAT-033 · API-CAT-042 · API-CAT-050 · API-CAT-056 · QR-CAT-016 · QR-CAT-047 · TC-CAT-233 · TC-CAT-234
