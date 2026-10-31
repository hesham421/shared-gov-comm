<!-- source: PHASE:F3 / SUB:F3-SCR-INV-005 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-090, AC-INV-093, API-INV-020, API-INV-022, API-INV-023, REQ-INV-014, REQ-INV-078, REQ-INV-079, REQ-INV-082, REQ-INV-084, REQ-INV-086, REQ-INV-122, REQ-INV-139, SCR-INV-005, UXD-INV-005 -->
<!-- SUB:F3-SCR-INV-005:START traces=SCR-INV-005,REQ-INV-139,UXD-INV-005,REQ-INV-014,REQ-INV-078,REQ-INV-079,REQ-INV-082,REQ-INV-084,REQ-INV-086,REQ-INV-122,AC-INV-090,AC-INV-093,API-INV-020,API-INV-022,API-INV-023 -->
### F3 · SCR-INV-005 — Transfers
- `transferSearchSchema`: `docNo` ≤ 30, date range, `fromWarehousePk`, `toWarehousePk`, `statusCode` (an INV_DOC_STATUS option).
- `transferFormSchema`:
  - `fromWarehousePk` and `toWarehousePk`: required. Equal values give a refinement on `toWarehousePk`, text: RULE-INV-016 message (SRS) (REQ-INV-082).
  - `docDate`: date, defaulted to today; a date after today → refinement, text: RULE-INV-051 message (SRS) (the server checks the tenant's today).
  - `noteText`: ≤ 500.
  - `lines`: array. Two lines with the same `variantPk` and `lotPk` give a refinement on the second line, text: RULE-INV-048 message (SRS).
- `transferLineSchema` (the one-line drawer):
  - `variant`: required; empty → «اختر متغيّر الصنف.» / "Choose the Variant.".
  - `quantity`: > 0 with ≤ 3 decimals, text: RULE-INV-017 message (SRS).
  - `lotPk`: required when `lotExpiryFl` and the Item is `lotTrackedFl`, text: RULE-INV-037 message (SRS) (AC-INV-093), unless the user chose «بدون تشغيلة» / "Without a lot"; the quantity bound (the lot-less stock of the from-warehouse) is the server's (REQ-INV-139, ADR-INV-051).
  - `measureQty`: > 0, required when `dualQuantityFl` and the Item is `dualQuantityFl`, text: RULE-INV-040 message (SRS) (REQ-INV-122); ≤ 3 decimals, text: RULE-INV-017 message (SRS).
  The tracking flags come from ITEM-TRACKING-LOOKUP. The drawer's confirm validates the line and returns it to the form state.
- Post asks for no input. "At least one line" (RULE-INV-031) is also checked before Post is offered.
<!-- SUB:F3-SCR-INV-005:END -->
