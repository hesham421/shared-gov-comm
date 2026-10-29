<!-- source: PHASE:F1 / SUB:F1-SCR-CAT-003 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-041, API-CAT-054, API-CAT-055, REQ-CAT-011, REQ-CAT-012, REQ-CAT-013, REQ-CAT-014, REQ-CAT-016, REQ-CAT-042, REQ-CAT-043, REQ-CAT-044, REQ-CAT-045, REQ-CAT-046, REQ-CAT-047, REQ-CAT-048, REQ-CAT-054, REQ-CAT-055, REQ-CAT-137, REQ-CAT-138, SCR-CAT-003, UXD-CAT-002 -->
<!-- SUB:F1-SCR-CAT-003:START traces=REQ-CAT-137,REQ-CAT-138,API-CAT-054,API-CAT-055,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-041,UXD-CAT-002,SCR-CAT-003 -->
### F1 · SCR-CAT-003 — Variant attributes

Bound schemas: `VariantAttributeView` with `values: VariantAttributeValueView[]` (API-CAT-041, API-CAT-026) · `VariantAttributeCreateRequest` / `VariantAttributeUpdateRequest` (API-CAT-025 / 027) · attribute deactivation (API-CAT-028) · `VariantAttributeStateRequest` `{isActiveFl: true}` (API-CAT-054, reactivate) · `VariantAttributeValueCreateRequest` / `VariantAttributeValueUpdateRequest` (API-CAT-029 / 030) · value deactivation (API-CAT-031) · `VariantAttributeValueStateRequest` `{isActiveFl: true}` (API-CAT-055, reactivate).

UI types:
- `AttributeListFilters = { text?, isActiveFl? }`.
- `AttributeRouteState = { attribute?: variantAttributePk | 'new', values?: 'open', returnTo?: string }`.
- `AttributeForm`. Header: `code` (create only), `nameAr`, `nameEn`, `sortOrder`, `version` (update). Values: `values[]` (`VariantAttributeValueView` + `pending`, or a new `{ code, nameAr, nameEn?, sortOrder }`).
<!-- SUB:F1-SCR-CAT-003:END -->
