# ADR-INV-035 — The browser reads the opening stock sheet
Status      : ACCEPTED
Stage       : P3.2 (Frontend — UX Design + Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : SCR-INV-010, REQ-INV-071, REQ-INV-072, REQ-INV-073, AC-INV-077, AC-INV-078, AC-INV-079, AC-INV-080, API-INV-046, API-INV-047, ADR-INV-027

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
2. The text of the "Variant code or barcode" cell is sent **unchanged in `barcode`**, and `variantPk` stays null.
   The backend resolves the text to one active Variant (RULE-INV-028, INV-422-LOAD-ROW-VARIANT, reported with its
   `rowNo`). The browser does not resolve rows through CAT.
3. Columns are matched by their header text in either language (ar or en), as the sheet (AC-INV-080) defines them.
   `rowNo` is the sheet row number that the Owner sees.
4. The browser checks the row count (1–1000, RULE-INV-027) before sending, using the RULE-INV-027 message. It sends
   all rows in one call (ADR-INV-027). Row failures in `details` are listed by `rowNo` with their messages.

## Consequences
- One new frontend dependency, scoped to SCR-INV-010.
- If P3.1 meant `barcode` to carry barcodes only, this is the open point. The backend plan's RULE-INV-028 check
  already covers "code or barcode", so this stage changes no operation.

## Status
ACCEPTED — non-breaking.
