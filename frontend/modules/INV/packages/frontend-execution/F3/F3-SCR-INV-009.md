<!-- source: PHASE:F3 / SUB:F3-SCR-INV-009 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-141, API-INV-043, API-INV-045, REQ-INV-124, REQ-INV-125, REQ-INV-128, REQ-INV-129, SCR-INV-009, UXD-INV-009, UXD-INV-010 -->
<!-- SUB:F3-SCR-INV-009:START traces=SCR-INV-009,UXD-INV-009,UXD-INV-010,REQ-INV-124,REQ-INV-125,REQ-INV-128,REQ-INV-129,AC-INV-141,API-INV-043,API-INV-045 -->
### F3 · SCR-INV-009 — Item stock settings
- `itemSettingsSearchSchema`: `itemPk` (from UXD-INV-009) or `term` ≤ 100, `lotTracked`, `dualQuantity`.
- `itemSettingsFormSchema`:
  - `item`: required on a new record; empty → «اختر الصنف.» / "Choose the Item.".
  - `lotTrackedFl`: boolean, sent `false` while lotExpiry is off and the control is hidden (REQ-INV-128).
  - `dualQuantityFl`: boolean, likewise.
  - `measureUomCode`: required when `dualQuantityFl` is true, text: RULE-INV-041 message (SRS) (AC-INV-141).
  - `version`: nullable.
<!-- SUB:F3-SCR-INV-009:END -->
