<!-- source: PHASE:F3 / SUB:F3-SCR-CAT-002 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-039, API-CAT-040, API-CAT-053, REQ-CAT-064, REQ-CAT-065, REQ-CAT-066, REQ-CAT-067, REQ-CAT-068, REQ-CAT-069, REQ-CAT-070, REQ-CAT-071, REQ-CAT-072, REQ-CAT-136, SCR-CAT-002 -->
<!-- SUB:F3-SCR-CAT-002:START traces=REQ-CAT-136,API-CAT-053,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-039,API-CAT-040,SCR-CAT-002 -->
### F3 · SCR-CAT-002 — Categories

| Field | Validator | Message |
|---|---|---|
| code (create) | required · 1–30 Latin letters, digits or hyphens | field hint "code-format" (below) · server duplicate: RULE-CAT-024 |

An inactive node opens with every field read-only and Save hidden; only Reactivate is offered, because the server refuses a PUT on an inactive Category (CAT-400-REQUEST-INVALID — backend plan, update of an inactive record).
| nameAr | required · 1–200 · ≥ 1 Arabic letter | RULE-CAT-001 |
| nameEn | ≤ 200 | RULE-CAT-002 |
| parentCategoryFk | optional · active · not the node itself nor one of its sub-categories · resulting depth ≤ 5 | RULE-CAT-023 |
| sortOrder | integer ≥ 0 (input `min`) · default next under the parent | — |

Copy "code-format": ar «من 1 إلى 30 حرفًا لاتينيًا أو رقمًا أو شرطة.» · en "1 to 30 Latin letters, digits or hyphens." (UX copy restating RULE-CAT-024's format; not a catalogue message).
<!-- SUB:F3-SCR-CAT-002:END -->
