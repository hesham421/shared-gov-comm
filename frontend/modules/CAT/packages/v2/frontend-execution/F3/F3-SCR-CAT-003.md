<!-- source: PHASE:F3 / SUB:F3-SCR-CAT-003 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-041, API-CAT-054, API-CAT-055, REQ-CAT-011, REQ-CAT-012, REQ-CAT-013, REQ-CAT-014, REQ-CAT-016, REQ-CAT-042, REQ-CAT-043, REQ-CAT-044, REQ-CAT-045, REQ-CAT-046, REQ-CAT-047, REQ-CAT-048, REQ-CAT-054, REQ-CAT-055, REQ-CAT-137, REQ-CAT-138, SCR-CAT-003, UXD-CAT-002 -->
<!-- SUB:F3-SCR-CAT-003:START traces=REQ-CAT-137,REQ-CAT-138,API-CAT-054,API-CAT-055,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-041,UXD-CAT-002,SCR-CAT-003 -->
### F3 · SCR-CAT-003 — Variant attributes

| Field | Validator | Message |
|---|---|---|
| attribute code (create) | required · 1–30 Latin letters, digits or hyphens | RULE-CAT-016 |
| attribute nameAr | required · 1–200 · ≥ 1 Arabic letter | RULE-CAT-001 |
| attribute nameEn | ≤ 200 | RULE-CAT-002 |
| value code (new line) | required · 1–30 Latin letters, digits or hyphens · not repeated among this attribute's lines | RULE-CAT-016 |
| value nameAr | required · 1–200 · ≥ 1 Arabic letter · not repeated among this attribute's active lines | RULE-CAT-001 · RULE-CAT-016 |
| value nameEn | ≤ 200 | RULE-CAT-002 |
| sortOrder (attribute and value) | integer ≥ 0 (input `min`) · default next | — |

The values editor validates its draft set before Confirm returns it. The entry's Save validates the whole form again.
<!-- SUB:F3-SCR-CAT-003:END -->
