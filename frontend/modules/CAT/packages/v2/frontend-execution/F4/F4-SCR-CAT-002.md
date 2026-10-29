<!-- source: PHASE:F4 / SUB:F4-SCR-CAT-002 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-039, API-CAT-040, API-CAT-053, REQ-CAT-064, REQ-CAT-065, REQ-CAT-066, REQ-CAT-067, REQ-CAT-068, REQ-CAT-069, REQ-CAT-070, REQ-CAT-071, REQ-CAT-072, REQ-CAT-136, SCR-CAT-002 -->
<!-- SUB:F4-SCR-CAT-002:START traces=REQ-CAT-136,API-CAT-053,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-039,API-CAT-040,SCR-CAT-002 -->
### F4 · SCR-CAT-002 — Categories

#### CATEGORIES-SCREEN — SCR-CAT-002            traces=REQ-CAT-064,REQ-CAT-067,REQ-CAT-068,API-CAT-039,API-CAT-022,API-CAT-040
- **Guard**: `/cat/categories` needs PERM_CAT_CATEGORIES_VIEW. "New category" needs CREATE, Save on an existing node needs UPDATE, and Deactivate needs DELETE.
- **Facade**: CATEGORY-FACADE only.
- **Cross-module**: none.
- **Components**: `CategoriesPage` (filter bar, `CategoryTree`), `CategoryEntryDrawer` (`?node=`), and `CategoryItemsDrawer` (`&items=open`), the second level.
- **Composition**: container drawer. The Category record has no child collection. "Items of this category" is a summary row in the entry drawer (count + 3 names + "Show") that opens `CategoryItemsDrawer`. That drawer is a sibling of the entry drawer, opened from the search params; it is read-only and returns nothing.
- **Saves**: ONE per drawer (`CATEGORY-SAVE`). Deactivate (active node) and Reactivate (inactive node — `CATEGORY-REACTIVATE`) are record commands.
- **Navigation out**: a row of `CategoryItemsDrawer` → `/cat/items/:itemPk`; "Items directly in this category / الأصناف المباشرة في هذه الفئة" → `/cat/items?categoryFk=<categoryPk>`. The jump is labelled apart from "Items of this category": the drawer's count covers the active sub-tree (API-CAT-040), while the Items search filters on the exact `categoryFk` (API-CAT-038), so the two lists differ for a Category with sub-categories. No API change; widening API-CAT-038 to the sub-tree is left to a later delta.
<!-- SUB:F4-SCR-CAT-002:END -->
