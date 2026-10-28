# ADR-CAT-075 — Item reads carry the Category's code and names

Status : ACCEPTED (non-breaking) · Stage : P3.1 / P3.2 (gate-analysis round 7, G5) · Module : CAT v2 · Date : 2026-10-28 · Amends : ADR-CAT-048 §5, ADR-CAT-051 §4 and §5

## Context
`ItemSummary` and `ItemDetail` carried only `categoryFk`. The Items screen labelled the Category column and field from a second read of the Category tree (API-CAT-039), and ADR-CAT-051 §4 said the column shows "the code" when that read answers 403 to a custom role. No code was available: the Item reads hold only the identifier.

Options:
- (a) add derived `categoryCode`, `categoryNameAr` and `categoryNameEn` to the Item reads;
- (b) show "—" when the tree cannot be read.

## Decision
Option (a):
1. **Derived properties.** `ItemSummary` and `ItemDetail` carry `categoryCode`, `categoryNameAr` and `categoryNameEn` of the Category the Item points to, **active or inactive**, and empty when `categoryFk` is empty. They are computed at read, never stored, and listed under ADR-CAT-048 §5 with DBF "— (derived)".
2. **Source.** A left join to `CAT_CATEGORY` on `(TENANT_ID, CATEGORY_FK)` in the Item reads (QR-CAT-001, QR-CAT-005, QR-CAT-006, QR-CAT-007). Where the Item was loaded under its lock (QR-CAT-010), the service reads the Category by QR-CAT-032.
3. **Permission.** The labels travel with the Item read, under the Item read's own permission. A user who may not read the Category tree still sees the Category's name on the Items they may read.
4. **Frontend.** The Items screen labels the Category column, header and read-only field from these properties (`categoryNameAr`, else `categoryNameEn` in en with `categoryNameAr` as fallback). The separate whole-tree label read (CATEGORY-LABEL-QUERY) is dropped. A 403 on API-CAT-039 now hides only the Category selection control and filter.

## Consequences
- `api-spec-cat.yaml`: `ItemSummary`, `ItemDetail`.
- Backend plan: derived-properties table, DTO catalogue, QR-CAT-001 / 005 / 006 / 007.
- Frontend plan: F2 SCR-CAT-001 (CATEGORY-LABEL-QUERY removed, ITEM-SEARCH-QUERY / ITEM-QUERY label the Category).
- Tests: TC-CAT-195 (exercises the new properties).
- ADR-CAT-048, ADR-CAT-051: amended notes.

## Traces
REQ-CAT-002 · REQ-CAT-003 · REQ-CAT-066 · AC-CAT-142 · API-CAT-002 · API-CAT-038 · API-CAT-039 · API-CAT-040 · API-CAT-046 · QR-CAT-001 · QR-CAT-005 · QR-CAT-006 · QR-CAT-007 · SCR-CAT-001 · TC-CAT-195 · ADR-CAT-048 · ADR-CAT-051
