<!-- source: PHASE:F3 / SUB:F3-SCR-CAT-006 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-045, API-CAT-051, API-CAT-052, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023, REQ-CAT-092, REQ-CAT-093, REQ-CAT-094, REQ-CAT-095, REQ-CAT-098, REQ-CAT-120, REQ-CAT-121, REQ-CAT-122, REQ-CAT-123, REQ-CAT-124, REQ-CAT-134, SCR-CAT-006, UXD-CAT-001 -->
<!-- SUB:F3-SCR-CAT-006:START traces=API-CAT-052,REQ-CAT-134,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-045,API-CAT-051,UXD-CAT-001,SCR-CAT-006 -->
### F3 · SCR-CAT-006 — Catalogue settings

Layout drawer:

| Field | Validator | Message |
|---|---|---|
| key (create) | two digits, `02` or `20`–`29` · not the key of another active layout (from SETTINGS-QUERY) | RULE-CAT-034 |
| scaleCodeLength (create) | integer 4–6 (default 5); read-only after creation (ADR-CAT-064) | RULE-CAT-034 |
| embeddedValueTypeCode | required · an active option (default WEIGHT) | RULE-CAT-006 |
| valueDecimals | integer 0–3 (default 3) | RULE-CAT-034 |
| valueAr, valueEn | input `maxLength` 200 | — |

Unit e-invoice code drawer:

| Field | Validator | Message |
|---|---|---|
| key (create) | required · an active CAT_UOM option · no active Unit e-invoice code with that key (from SETTINGS-QUERY) | RULE-CAT-006 · RULE-CAT-043 |
| einvoiceUnitCode | required · an active CAT_EINVOICE_UNIT option | RULE-CAT-006 |

Turn-off check: the flag is required and is one of `variants`, `bundles` (a two-option segmented control, so no message).
<!-- SUB:F3-SCR-CAT-006:END -->
