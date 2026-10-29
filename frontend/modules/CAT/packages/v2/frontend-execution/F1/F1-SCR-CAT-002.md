<!-- source: PHASE:F1 / SUB:F1-SCR-CAT-002 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-039, API-CAT-040, API-CAT-053, REQ-CAT-064, REQ-CAT-065, REQ-CAT-066, REQ-CAT-067, REQ-CAT-068, REQ-CAT-069, REQ-CAT-070, REQ-CAT-071, REQ-CAT-072, REQ-CAT-136, SCR-CAT-002 -->
<!-- SUB:F1-SCR-CAT-002:START traces=REQ-CAT-136,API-CAT-053,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-039,API-CAT-040,SCR-CAT-002 -->
### F1 · SCR-CAT-002 — Categories

Bound schemas: `CategoryNode` tree (API-CAT-039) · `CategoryView` (API-CAT-022) · `CategoryCreateRequest` (API-CAT-021) · `CategoryUpdateRequest` (API-CAT-023) · deactivate response `CategoryView` (API-CAT-024) · `CategoryStateRequest` `{isActiveFl: true}` → `CategoryView` (API-CAT-053, reactivate) · `ItemSummary` page (API-CAT-040).

UI types:
- `CategoryTreeFilters = { text?, isActiveFl? }` (URL search params).
- `CategoryRouteState = { node?: categoryPk | 'new', parent?: categoryPk, items?: 'open' }`.
- `CategoryForm` = `CategoryCreateRequest` on create; `CategoryUpdateRequest` (no `code`) + `version` on update.
- `CategoryItemsFilters = { categoryPk, page, size }`.
<!-- SUB:F1-SCR-CAT-002:END -->
