<!-- source: PHASE:F3 / SUB:F3-SCR-INV-002 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-026, API-INV-009, API-INV-011, API-INV-012, REQ-INV-022, REQ-INV-023, REQ-INV-028, SCR-INV-002 -->
<!-- SUB:F3-SCR-INV-002:START traces=SCR-INV-002,REQ-INV-022,REQ-INV-023,REQ-INV-028,AC-INV-026,API-INV-009,API-INV-011,API-INV-012 -->
### F3 · SCR-INV-002 — Branches
- `branchSearchSchema`: `code` ≤ 20, `name` ≤ 100, `active`.
- `branchFormSchema`:
  - `nameAr`: required, 1–100; empty → «اسم الفرع بالعربية مطلوب.» / "The branch name (Arabic) is required.".
  - `nameEn`: ≤ 100.
  - `code`: display only (RULE-INV-010).
- Name uniqueness (RULE-INV-011) is a server check.
<!-- SUB:F3-SCR-INV-002:END -->
