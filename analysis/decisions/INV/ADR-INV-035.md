# ADR-INV-035 — The browser reads the opening stock sheet
Status      : ACCEPTED
Stage       : P3.2 (Frontend — UX Design + Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : SCR-INV-010, REQ-INV-071, REQ-INV-072, REQ-INV-073, AC-INV-077, AC-INV-078, AC-INV-079, AC-INV-080, API-INV-046, API-INV-047, ADR-INV-027, XM-INV-013, XM-INV-014, RULE-INV-028, CON-CAT-010

## Context
DEFAULT D-13 (SRS): "the browser reads the filled sheet and submits its rows". INV parses no uploaded file.
API-INV-046 returns the xlsx sheet. API-INV-047 takes `rows` (1–1000), and each row has `rowNo`, `variantPk` or
`barcode`, `quantity`, `unitCost`, `lotNo`, `lotExpiryDate` and `measureQty`. Two points are left open:

1. `profile.stack.frontend` names no library that reads an xlsx file.
2. The sheet has one column, «رمز المتغيّر أو الباركود» / "Variant code or barcode" (AC-INV-080). The request
   has a `barcode` field and a `variantPk` field, and no Variant-code field.

## Decision
1. The opening stock screen reads the xlsx file with a spreadsheet-reading library added to the frontend stack.
   The library is loaded only in that screen's lazy chunk, and the screen is its only user. The implementer
   chooses a maintained library that reads xlsx client-side. Nothing is uploaded.
2. **The sheet names its Variant by barcode only** (revised at gate-analysis revise 1, G12). The column is
   «الباركود» / "Barcode" (AC-INV-080, REQ-INV-073). The cell text is sent unchanged in `barcode`, and `variantPk`
   stays null. The backend resolves it through CAT lookupBarcode (CON-CAT-010, XM-INV-013) to one active Variant of
   a STANDARD Item (RULE-INV-028, INV-422-LOAD-ROW-VARIANT «لا يطابق هذا السطر متغيّر صنف نشطًا.» / "This row
   matches no active Variant.", reported with its `rowNo`). The browser does not resolve rows through CAT.
   - *Why not "Variant code or barcode":* CAT v2 publishes no exact Variant-code read (contract-cat lists
     getVariant by identifier, lookupBarcode and searchCatalogue, which returns Items, not Variants). A sheet filled
     with codes would fail every row.
   - *Platform finding,* owned by CAT (business-policies-inv PLATFORM FINDINGS row 2): publish an exact
     Variant-code read. When CAT publishes it, an INV delta resolves `barcode` through CON-CAT-010 first and, when
     not found, through that read, and restores the "Variant code or barcode" column.
   - The same statement appears once in API-INV-047 and in XM-INV-013 / 014 of the backend plan.
3. Columns are matched by their header text in either language (ar or en), as the sheet (AC-INV-080) defines them.
   `rowNo` is the sheet row number that the Owner sees.
4. The browser checks the row count (1–1000, RULE-INV-027) before sending, using the RULE-INV-027 message. It sends
   all rows in one call (ADR-INV-027). Row failures in `details` are listed by `rowNo` with their messages.

## Consequences
- One new frontend dependency, scoped to SCR-INV-010.
- The open point of round 1 (whether `barcode` carries barcodes only) is closed: it does, in v1.

## Status
ACCEPTED — non-breaking.
