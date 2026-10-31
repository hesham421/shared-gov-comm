<!-- source: PHASE:F3 / SUB:F3-SCR-INV-004 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-018, REQ-INV-075, SCR-INV-004, UXD-INV-004 -->
<!-- SUB:F3-SCR-INV-004:START traces=SCR-INV-004,UXD-INV-004,REQ-INV-075,API-INV-018 -->
### F3 · SCR-INV-004 — Stock move history
- `moveSearchSchema`:
  - `variantPk`: from UXD-INV-004.
  - `warehousePk`, `lotPk`.
  - `moveTypeCode`: an INV_MOVE_TYPE option.
  - `docNo`: ≤ 30.
  - `sourceDocTypeCode` (an INV_SOURCE_DOC_TYPE option) and `sourceDocRef` (integer): both or neither; set by the document jumps only.
  - `dateFrom` ≤ `dateTo`, both ISO dates.
- No entry form (REQ-INV-055).
<!-- SUB:F3-SCR-INV-004:END -->
