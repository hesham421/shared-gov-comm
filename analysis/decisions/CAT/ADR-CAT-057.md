# ADR-CAT-057 — The item import sheet is a report of the given report module

Status : ACCEPTED (non-breaking) · Stage : P3.1 (gate-analysis revise, G3) · Module : CAT v2 · Date : 2026-10-28
Amends : ADR-CAT-048 §4 (import sheet)

## Context
`API-CAT-048` built the xlsx import sheet in CAT ("build the workbook in memory → stream it"). The given library forbids a module-owned export path:
- checklist F7: "a CSV / XLSX / PDF writer" is a rejection;
- checklist C10: "Every list export / tabular report is a `ReportProvider` bean; printed documents go through `PrintApi` + `PrintResponses`";
- `modules.report`, "Do not rebuild": "A CSV / XLSX / PDF writer".

The given report module emits xlsx. Its exports are `format=csv|json|pdf|xlsx|docx|html`, and its printed formats are available when JasperReports is on the classpath, which is already the case for `CAT_BARCODE_LABEL`. So there is no core gap, and no `CORE-GAP-xlsx-template` is raised.

## Decision
1. `CatItemImportSheetReportProvider` (implements the given `com.erp.report.ReportProvider`) registers the report `CAT_ITEM_IMPORT_SHEET` for module CAT at application start. Its columns are the 11 import columns of REQ-CAT-036, each titled in ar and en. It has no parameters and returns no row: the sheet is a header row.
2. `API-CAT-048` renders the document through the given `PrintApi`:
   - template `reports/cat/item_import_sheet` (with its `_ar` variant);
   - `PrintFormat.XLSX`;
   - data = the column definitions of the report;
   - `fileName("item-import-sheet")`;
   - permission `PERM_CAT_ITEM_IMPORT_VIEW`.
   The endpoint returns the result through `PrintResponses.toResponseEntity`. CAT holds no workbook writer.
3. The report also gets the given's automatic report authority `CAT:REPORT:CAT_ITEM_IMPORT_SHEET`. The CAT screen does not use the given export endpoint; the authority is listed in the bootstrap block for completeness.

## Consequences
- The given-usage tables of the backend plan name `ReportProvider` for both `CAT_BARCODE_LABEL` and `CAT_ITEM_IMPORT_SHEET`. The bootstrap block names the report and its producer.
- The response media type of `API-CAT-048` is unchanged (xlsx), so the frontend binding and TC-CAT-082 / 164 are unchanged.

## Traces
REQ-CAT-036 · API-CAT-048
