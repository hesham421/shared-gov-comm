# ADR-CAT-048 — Endpoint shapes, derived response properties and success-path messages

Status : ACCEPTED (non-breaking) · Stage : P3.1 · Module : CAT v2 · Date : 2026-10-28

## Context
SRS B5 states verbs, paths and inputs but leaves some shapes open: one PATCH for reactivate / publish / unpublish; a new Bundle that must carry its components on its first save (AC-CAT-058, AC-CAT-061); an import that returns a result; two binary documents (import sheet, label document); response properties that have no column (derived Variant names — D-15, D-P2-08; the English display fallback — REQ-CAT-029; effective e-invoice data — REQ-CAT-119; the e-invoice unit of a unit — CON-CAT-008/009; the embedded scale value — REQ-CAT-097); and several RULEs whose message is shown on success (RULE-CAT-010, 011, 012, 039, 045, 048).

## Decision
1. `PATCH /api/v1/cat/items/{id}` takes exactly one of `isActiveFl: true` (reactivate) or `publishStateCode` (a CAT_PUBLISH_STATE code). Deactivation stays `DELETE`.
2. `POST /api/v1/cat/items` carries `bundleComponents` when `itemKindCode` is BUNDLE; `PUT /api/v1/cat/items/{id}/bundle-components` replaces the active lines of an existing Bundle.
3. The import is synchronous: a validation pass over every row with no write, then one transaction that creates the valid rows; a number-series failure rolls the whole import back (RULE-CAT-005).
4. The import sheet (xlsx) and the label document (PDF) are both rendered by the given `PrintApi` and returned through `PrintResponses`. They are binary bodies, not wrapped in `ApiResponse<T>`; their errors use the normal envelope.
   - Import sheet: report `CAT_ITEM_IMPORT_SHEET`, registered by `CatItemImportSheetReportProvider` (amended by ADR-CAT-057).
   - Label document: report `CAT_BARCODE_LABEL`, registered by `CatBarcodeLabelReportProvider`.
   - Neither CAT endpoint takes a multipart body. Image uploads (`API-CAT-018`) and import files (`API-CAT-049`) go through the given FILE endpoints, and CAT receives `fileDocumentRef` in a JSON body (amended by ADR-CAT-056).
5. Derived response properties, computed by the service at read and never stored: Variant `nameAr` / `nameEn`, `displayNameEn`, `effectiveEinvoiceCodeTypeCode`, `effectiveEinvoiceCode`, `einvoiceUnitCode`, `embeddedValue`, `componentVariantCode`, `itemNameAr`, `firstBarcodeValue`. The manifest lists them with DBF "— (derived)".
6. A RULE whose message is shown on success is a catalog row with the 2xx status of its endpoint; the envelope carries its code and messages next to the data.
7. Search sorting is the one the SRS fixes (Items by nameAr, Variants by Item nameAr then code, trees by sortOrder); no sort parameter is offered.

## Consequences
- The API document marks binary responses with their media types.
- The frontend reads success messages from the envelope; it never composes them.

## Traces
REQ-CAT-006 · REQ-CAT-113 · REQ-CAT-114 · REQ-CAT-056 · REQ-CAT-059 · REQ-CAT-036 · REQ-CAT-038 · REQ-CAT-039 · REQ-CAT-102 · REQ-CAT-029 · REQ-CAT-119 · REQ-CAT-097 · REQ-CAT-019 · REQ-CAT-021 · REQ-CAT-106
