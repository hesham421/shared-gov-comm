# ADR-CAT-069 — Categories, Variant attributes and Variant attribute values can be reactivated

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 / P3.2 / P4 (gate-analysis round 3, G10) · Module : CAT v2 · Date : 2026-10-28

## Context
A Category, a Variant attribute and a Variant attribute value could be deactivated (API-CAT-024, 028, 031) but never reactivated. Their codes are unique across active and inactive rows: UQ_CAT_CATEGORY_CODE, UQ_CAT_VAR_ATTR_CODE and UQ_CAT_VAR_ATTR_VALUE_CODE are not partial. A mistaken deactivation therefore lost the record and its code for good, and a deactivated tree node could never come back. It also left one of the three reactivation dead ends of ADR-CAT-068 (an Item whose Category went inactive) with a single way out.

## Decision
1. **Three new operations**, each `PATCH {isActiveFl: true}` on the record's own path — the same shape as the Item reactivation (API-CAT-005):
   - API-CAT-053 reactivates a Category (REQ-CAT-136);
   - API-CAT-054 reactivates a Variant attribute (REQ-CAT-137);
   - API-CAT-055 reactivates a Variant attribute value (REQ-CAT-138).
   The body schema accepts only `isActiveFl: true` (enum [true], no other property). Deactivation stays DELETE.
2. **Guards — the same rules as on create:**
   - Category: RULE-CAT-023 (the parent is empty or active) and RULE-CAT-024 (no active sibling carries its nameAr). Depth and cycle cannot change, because the parent does not change.
   - Variant attribute: RULE-CAT-016 (no other active attribute carries its nameAr).
   - Variant attribute value: its attribute must be active (CAT-400-REQUEST-INVALID, as on value create) and RULE-CAT-016 (no other active value of the attribute carries its nameAr).
   The code needs no check: it is still the record's own, since the unique constraint spans inactive rows.
3. **No cascade.** A Category's sub-categories and Items, and an attribute's values, keep their own state.
4. **No capability flag is read.** Like every reactivation (RULE-CAT-008), attribute and value reactivation stay allowed while `variants` is off.
5. **Concurrency.** Category reactivation takes the tenant tree lock, like every Category create, move and deactivation (ADR-CAT-047), so it never activates a child under a parent being deactivated. Attribute and value reactivation lock their row; a value reads its attribute FOR SHARE. The partial unique indexes on nameAr back the name rules.
6. **Permissions.** Reactivation is an UPDATE: PERM_CAT_CATEGORIES_UPDATE, PERM_CAT_VARIANT_ATTRIBUTES_UPDATE.
7. **No new code.** The refusals reuse CAT-422-CATEGORY-PARENT-INVALID, CAT-409-CATEGORY-DUPLICATE and CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE; their catalog rows list the new endpoints.

## Consequences
- New REQ-CAT-136, 137, 138; AC-CAT-157 … 160; API-CAT-053 … 055; TC-CAT-213 … 216. RULE-CAT-016, 023 and 024 gain the trigger "on reactivation". The QRs are reused (QR-CAT-022, 023, 025, 027, 028, 030, 032, 033, 035).
- The frontend adds a Reactivate command to the Category and attribute drawers and a reactivate action per inactive value line.

## Traces
REQ-CAT-136 · REQ-CAT-137 · REQ-CAT-138 · RULE-CAT-016 · RULE-CAT-023 · RULE-CAT-024 · API-CAT-053 · API-CAT-054 · API-CAT-055
