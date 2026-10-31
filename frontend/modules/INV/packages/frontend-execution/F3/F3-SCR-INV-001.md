<!-- source: PHASE:F3 / SUB:F3-SCR-INV-001 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-004, API-INV-001, API-INV-003, API-INV-004, REQ-INV-003, REQ-INV-004, REQ-INV-009, REQ-INV-029, REQ-INV-031, SCR-INV-001 -->
<!-- SUB:F3-SCR-INV-001:START traces=SCR-INV-001,REQ-INV-003,REQ-INV-004,REQ-INV-009,REQ-INV-029,REQ-INV-031,AC-INV-004,API-INV-001,API-INV-003,API-INV-004 -->
### F3 · SCR-INV-001 — Warehouses
- `warehouseSearchSchema`: `code` ≤ 20, `name` ≤ 100, `active` ∈ {ACTIVE_ONLY, ALL}.
- `warehouseFormSchema`:
  - `nameAr`: required, 1–100; empty → «اسم المستودع بالعربية مطلوب.» / "The warehouse name (Arabic) is required.".
  - `nameEn`: ≤ 100, optional.
  - `branchPk`: optional; required on update, and the record's value is kept when the field is hidden (REQ-INV-029, REQ-INV-031).
  - `code`: display only, never part of the form value (RULE-INV-001).
- Name uniqueness (RULE-INV-002) is a server check, shown inline on `nameAr`.
<!-- SUB:F3-SCR-INV-001:END -->
