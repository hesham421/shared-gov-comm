<!-- source: PHASE:SEC-FE -->
<!-- traces: API-CAT-001, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-009, API-CAT-010, API-CAT-018, API-CAT-047, API-CAT-049, API-CAT-050, API-CAT-056, REQ-CAT-014, REQ-CAT-015, REQ-CAT-016, REQ-CAT-127, REQ-CAT-131, REQ-CAT-148, SCR-CAT-001, SCR-CAT-002, SCR-CAT-003, SCR-CAT-004, SCR-CAT-005, SCR-CAT-006 -->
<!-- PHASE:SEC-FE:START traces=REQ-CAT-131,REQ-CAT-148,API-CAT-056,SCR-CAT-001,SCR-CAT-002,SCR-CAT-003,SCR-CAT-004,SCR-CAT-005,SCR-CAT-006,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-127,API-CAT-001,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-009,API-CAT-010,API-CAT-018,API-CAT-047,API-CAT-049,API-CAT-050 -->
## SEC-FE

Permission names are the backend registry's (`PERM_<PAGE_CODE>_<ACTION>`, derived by the given `sec` module from the six CAT page codes). None is redeclared here. The user's permission set is platform session context (ADR-CAT-050 §Consequences). Rules for every screen:
- **Navigation**: a route without its VIEW permission redirects to the unauthorized view, and its menu entry is hidden.
- **Actions**: an action whose permission is missing is hidden. Fields the user may not change are read-only.
- **Forbidden responses**: a CAT-403-FORBIDDEN response is shown as the localized catalogue message from the envelope. On an auxiliary read it hides the affordance that read feeds (ADR-CAT-051 §4).

| SCR | Guard (no VIEW → unauthorized) | no CREATE → hidden | no UPDATE → read-only / hidden | no DELETE → hidden |
|---|---|---|---|---|
| SCR-CAT-001 | PERM_CAT_ITEMS_VIEW | "New item", "Add unit", "Add barcode", "Generate internal barcode", "Generate variants", "Add image" (`API-CAT-001`, 010, 014, 015, 006, 018) | header fields read-only, Save hidden on an existing Item; unit flags, Variant override, image changes, Bundle components, Reactivate / Publish / Unpublish hidden (`API-CAT-003`, 012, 008, 019, 017, 005) | Deactivate on the Item, Variant, unit, barcode and image lines (`API-CAT-004`, 009, 013, 016, 020) |
| SCR-CAT-002 | PERM_CAT_CATEGORIES_VIEW | "New category", "New sub-category" | entry fields read-only, Save and Reactivate hidden (`API-CAT-053`) | Deactivate |
| SCR-CAT-003 | PERM_CAT_VARIANT_ATTRIBUTES_VIEW (+ `variants` on) | "New attribute", "Add value" | entry and value fields read-only, Save and Reactivate (attribute, value line) hidden (`API-CAT-054`, 055) | Deactivate (attribute, value line) |
| SCR-CAT-004 | PERM_CAT_ITEM_IMPORT_VIEW | "Import" (`API-CAT-049`); "Download import sheet" stays | — (no UPDATE action) | — (no DELETE action) |
| SCR-CAT-005 | PERM_CAT_BARCODE_LABELS_VIEW | "Print" (`API-CAT-050`), "Generate internal barcode" on a flagged line (`API-CAT-056`) | — | — |
| SCR-CAT-006 | PERM_CAT_SETTINGS_VIEW | "New layout", "New unit code" | drawer fields read-only, Save hidden | Deactivate (layout, unit code) |

Further rules:
- **Mixed permissions inside the Items Save**: a call is never queued for an action the user cannot take, because the affordance is hidden. A 403 on a queued call stops the sequence like any rejection (ADR-CAT-049 §4).
- **Capability flags are not permissions**: flag-bound affordances are absent while their flag is off (REQ-CAT-014, REQ-CAT-015). The server still refuses a flag-off call (`CAT-422-CAPABILITY-OFF`, REQ-CAT-016), and that refusal is routed as F2 states.
- **Jumps to other pages**: a jump to another CAT page is shown only when the user holds that page's VIEW permission and, when that page is bound to a capability flag, only while the flag is on. On SCR-CAT-001 this governs "Print labels" (PERM_CAT_BARCODE_LABELS_VIEW), "Import" (PERM_CAT_ITEM_IMPORT_VIEW) and "Open Variant attributes" (PERM_CAT_VARIANT_ATTRIBUTES_VIEW and `variants` on); the same rule governs "Open item" and the dependent-Item links (PERM_CAT_ITEMS_VIEW) and "Items directly in this category" (PERM_CAT_ITEMS_VIEW) on the other screens. A user who lacks the target page's VIEW never reaches the unauthorized view through a CAT jump.
- **Global search**: `catalogueSearchProvider` registers only for a user holding PERM_CAT_ITEMS_VIEW. The server returns no Item results without it (REQ-CAT-127).
- **Tokens**: the bearer token is attached by the platform HTTP client. CAT code never reads or stores it. A CAT-401-UNAUTHENTICATED response goes to login and returns to the same route afterwards.
<!-- PHASE:SEC-FE:END -->
