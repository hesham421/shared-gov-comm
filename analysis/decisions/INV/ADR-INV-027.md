# ADR-INV-027 — Opening stock is created and posted in one call, all rows or none
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-073, REQ-INV-074, RULE-INV-027, RULE-INV-028, SRS D-13, API-INV-046, API-INV-047

## Context
SRS D-13: opening stock is a Stock adjustment with reason OPENING_STOCK; the browser reads the xlsx sheet
and submits 1–1000 rows; all rows post or none; failing rows are reported with their row number. A row names
"Variant code or barcode". The Variant owner publishes a barcode lookup but no lookup by Variant code.

## Decision
- POST /api/v1/inv/opening-stock creates one OPENING_STOCK adjustment and posts it in the same transaction
  (201 with the posted adjustment).
- A row identifies its Variant by `variantPk` (the browser resolves a code through the owner's picker or reads)
  or by `barcode` (resolved through XM-INV-013).
- Every row is validated before anything is written. Any failure answers 422 INV-422-LOAD-ROWS-INVALID
  (rule REQ-INV-072) with `details[]` = {rowNo, code, messageAr, messageEn} per failing row, each code one of
  the catalog's row codes. A load with 0 or more than 1000 rows answers INV-422-LOAD-ROWS.
- The sheet (API-INV-046) is report INV_OPENING_STOCK_SHEET rendered by PrintApi, with the lot, expiry and
  measure columns only when their flags are on.

## Consequences
- A resubmitted identical load creates a second adjustment; the screen prevents double submission.
- No uploaded file is stored or parsed by INV (ADR-INV-018 stands).

## Status
ACCEPTED — non-breaking.
