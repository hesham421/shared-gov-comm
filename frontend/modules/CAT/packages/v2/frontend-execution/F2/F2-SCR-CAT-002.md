<!-- source: PHASE:F2 / SUB:F2-SCR-CAT-002 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-039, API-CAT-040, API-CAT-053, REQ-CAT-008, REQ-CAT-064, REQ-CAT-065, REQ-CAT-066, REQ-CAT-067, REQ-CAT-068, REQ-CAT-069, REQ-CAT-070, REQ-CAT-071, REQ-CAT-072, REQ-CAT-136, SCR-CAT-002 -->
<!-- SUB:F2-SCR-CAT-002:START traces=REQ-CAT-008,REQ-CAT-136,API-CAT-053,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-039,API-CAT-040,SCR-CAT-002 -->
### F2 · SCR-CAT-002 — Categories

```yaml name=screen-hooks
screen: SCR-CAT-002
hooks:
  - {hook: CATEGORY-TREE-QUERY, kind: read, api: [API-CAT-039], cache_key: "[cat, categories, tree, {text, isActiveFl}]", errors: "CAT-401 → login · CAT-403 → unauthorized · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: PARENT-OPTIONS-QUERY, kind: read, api: [API-CAT-039], cache_key: "[cat, categories, tree, {isActiveFl: true}]", errors: "generic", loading: NONE, invalidation: "—"}
  - {hook: CATEGORY-QUERY, kind: read, api: [API-CAT-022], cache_key: "[cat, categories, detail, categoryPk]", errors: "CAT-404-NOT-FOUND → drawer closes, not-found notice · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CATEGORY-ITEMS-QUERY, kind: read, api: [API-CAT-040], cache_key: "[cat, categories, items, {categoryPk, page, size}]", errors: "CAT-404-NOT-FOUND → empty list · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CATEGORY-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized", loading: NONE}
  - {hook: CATEGORY-SAVE, kind: mutation, api: [API-CAT-021, API-CAT-023], errors: "see CATEGORY-SAVE below", loading: LOCAL, invalidation: "[cat, categories, tree] · [cat, categories, detail, categoryPk] · [cat, categories, items]"}
  - {hook: CATEGORY-DEACTIVATE, kind: mutation, api: [API-CAT-024], errors: "CAT-409-CATEGORY-IN-USE → drawer banner, text RULE-CAT-025 message, action opens Items of this category · CAT-500-CHANGE-NOT-SAVED → drawer banner with Try again, text RULE-CAT-049 message", loading: LOCAL, invalidation: "[cat, categories, tree] · [cat, categories, detail, categoryPk]"}
  - {hook: CATEGORY-REACTIVATE, kind: mutation, api: [API-CAT-053], errors: "see CATEGORY-REACTIVATE below", loading: LOCAL, invalidation: "[cat, categories, tree] · [cat, categories, detail, categoryPk] · [cat, categories, items]"}
  - {hook: CATEGORY-FACADE, kind: facade}
```

#### CATEGORY-TREE-QUERY / PARENT-OPTIONS-QUERY — API-CAT-039            traces=API-CAT-039,REQ-CAT-067,REQ-CAT-069
The tree view reads with the screen filters. The parent select reads active nodes only, and disables the node being edited and its descendants (computed from the `children` of the tree read — RULE-CAT-023). Depth is computed from the same tree to warn before save.

#### CATEGORY-QUERY — API-CAT-022            traces=API-CAT-022,REQ-CAT-066,REQ-CAT-067
Entity by id for the entry drawer (`?node=<categoryPk>`): it supplies the `version` and fresh values. A deactivated Category stays readable (REQ-CAT-066).

#### CATEGORY-ITEMS-QUERY — API-CAT-040            traces=API-CAT-040,REQ-CAT-068
Feeds the "Items of this category" summary row (total count + 3 names from page 0) and the second-level list (paged). A row jump seeds `/cat/items/:itemPk`, which identifies one Item.

#### CATEGORY-SAVE — API-CAT-021, API-CAT-023            traces=API-CAT-021,API-CAT-023,REQ-CAT-008,REQ-CAT-064,REQ-CAT-065,REQ-CAT-069,REQ-CAT-070
Mutation, the drawer's one Save: create (`API-CAT-021`) or update (`API-CAT-023`, with `version`, without `code`). Errors:
- `CAT-422-NAME-AR-INVALID` → inline on nameAr; text: RULE-CAT-001 message
- `CAT-422-NAME-EN-TOO-LONG` → inline on nameEn; text: RULE-CAT-002 message
- `CAT-422-CATEGORY-PARENT-INVALID` → inline on parentCategoryFk; text: RULE-CAT-023 message
- `CAT-409-CATEGORY-DUPLICATE` → inline on code (create) and nameAr; text: RULE-CAT-024 message
- `CAT-409-STALE-VERSION` → drawer banner + Reload; text: catalogue CAT-409-STALE-VERSION
- `CAT-500-CHANGE-NOT-SAVED` → drawer banner with Try again (the drawer keeps its values); text: RULE-CAT-049 message (ADR-CAT-077)
- other → as §3.0

#### CATEGORY-DEACTIVATE — API-CAT-024            traces=API-CAT-024,REQ-CAT-008,REQ-CAT-066,REQ-CAT-071
Record command in the drawer, after a confirmation. The success code `CAT-200-DEACTIVATED` → notice, text: RULE-CAT-048 message. `CAT-500-CHANGE-NOT-SAVED` → drawer banner with Try again, text: RULE-CAT-049 message (ADR-CAT-077).

#### CATEGORY-REACTIVATE — API-CAT-053            traces=API-CAT-053,REQ-CAT-008,REQ-CAT-136
Record command in the drawer of an inactive Category, after a confirmation, only while the drawer has no pending change; it sends `{isActiveFl: true}` (ADR-CAT-069). Errors:
- `CAT-422-CATEGORY-PARENT-INVALID` → drawer banner; text: RULE-CAT-023 message; action "Open the parent" → `?node=<parentCategoryFk>`, where the parent can be reactivated first
- `CAT-409-CATEGORY-DUPLICATE` → drawer banner and inline on nameAr; text: RULE-CAT-024 message (rename it first, then reactivate)
- `CAT-500-CHANGE-NOT-SAVED` → drawer banner with Try again; text: RULE-CAT-049 message (ADR-CAT-077)
- other → as §3.0

#### CATEGORY-SCREEN-INIT — SCR-CAT-002
Permission read PERM_CAT_CATEGORIES_VIEW / CREATE / UPDATE / DELETE; no lookups; entity by id: CATEGORY-QUERY. A new node takes `?parent=` as its parent.

#### CATEGORY-FACADE — SCR-CAT-002
It owns: the tree filters (URL); the open node (`?node=`) and `items=open`; the drawer form; the Items-of-category paging inside its filter object; derived loading. Operations: `save()`, `deactivate()`, `reactivate()`, `openItems()`, `openDirectItems()` ("Items directly in this category" → `/cat/items?categoryFk=<categoryPk>`; API-CAT-038 filters on the Category itself, not on its sub-categories, unlike the sub-tree count of CATEGORY-ITEMS-QUERY — so the jump carries its own label).
<!-- SUB:F2-SCR-CAT-002:END -->
