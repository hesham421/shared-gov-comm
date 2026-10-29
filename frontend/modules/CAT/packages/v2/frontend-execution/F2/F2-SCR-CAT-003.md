<!-- source: PHASE:F2 / SUB:F2-SCR-CAT-003 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-041, API-CAT-054, API-CAT-055, REQ-CAT-011, REQ-CAT-012, REQ-CAT-013, REQ-CAT-014, REQ-CAT-016, REQ-CAT-042, REQ-CAT-043, REQ-CAT-044, REQ-CAT-045, REQ-CAT-046, REQ-CAT-047, REQ-CAT-048, REQ-CAT-054, REQ-CAT-055, REQ-CAT-137, REQ-CAT-138, SCR-CAT-003, UXD-CAT-002 -->
<!-- SUB:F2-SCR-CAT-003:START traces=REQ-CAT-137,REQ-CAT-138,API-CAT-054,API-CAT-055,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-041,UXD-CAT-002,SCR-CAT-003 -->
### F2 · SCR-CAT-003 — Variant attributes

```yaml name=screen-hooks
screen: SCR-CAT-003
hooks:
  - {hook: ATTRIBUTE-LIST-QUERY, kind: read, api: [API-CAT-041], cache_key: "[cat, variant-attributes, list, {text, isActiveFl}]", errors: "CAT-403 → unauthorized · other → generic (the read is not flag-bound; while `variants` is off the screen sends nothing — UXD-CAT-002)", loading: LOCAL, invalidation: "—"}
  - {hook: ATTRIBUTE-QUERY, kind: read, api: [API-CAT-026], cache_key: "[cat, variant-attributes, detail, variantAttributePk]", errors: "CAT-404-NOT-FOUND → drawer closes, not-found notice · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CAPABILITY-FLAGS-INIT, kind: init, cache_key: "[platform, app-config]", errors: "unreadable → screen state with RULE-CAT-009 text", loading: NONE}
  - {hook: ATTRIBUTE-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized · variants off → RULE-CAT-008 state", loading: NONE}
  - {hook: ATTRIBUTE-SAVE, kind: mutation, api: [API-CAT-025, API-CAT-027, API-CAT-031, API-CAT-055, API-CAT-029, API-CAT-030], errors: "see ATTRIBUTE-SAVE below", loading: LOCAL, invalidation: "[cat, variant-attributes, list] · [cat, variant-attributes, detail, variantAttributePk] · [cat, items, detail] · [cat, variants, detail]"}
  - {hook: ATTRIBUTE-DEACTIVATE, kind: mutation, api: [API-CAT-028], errors: "CAT-409-ATTRIBUTE-VALUE-IN-USE → drawer banner, text RULE-CAT-017 message", loading: LOCAL, invalidation: "[cat, variant-attributes, list] · [cat, variant-attributes, detail, variantAttributePk]"}
  - {hook: ATTRIBUTE-REACTIVATE, kind: mutation, api: [API-CAT-054], errors: "CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE → drawer banner and inline on nameAr, text RULE-CAT-016 message · other → as §3.0", loading: LOCAL, invalidation: "[cat, variant-attributes, list] · [cat, variant-attributes, detail, variantAttributePk]"}
  - {hook: ATTRIBUTE-FACADE, kind: facade}
```

#### ATTRIBUTE-LIST-QUERY — API-CAT-041            traces=API-CAT-041,REQ-CAT-048,REQ-CAT-014
The list by sortOrder, each attribute with its values. It sends no `isActiveFl` filter by default (the Active filter starts empty), so `API-CAT-041` returns every attribute with every value, active and inactive, and inactive ones stay visible and can be reactivated (API-CAT-054, 055). When the user sets the Active filter, it filters the values as well as the attributes. Enabled only while `variants` is on (UXD-CAT-002); while it is off, the screen shows RULE-CAT-008's text and sends nothing.

#### ATTRIBUTE-QUERY — API-CAT-026            traces=API-CAT-026,REQ-CAT-048
Entity by id for the entry drawer: the attribute's `version` and its values with their `version`s seed the form. `API-CAT-026` takes no filter and returns every value of the attribute, active and inactive, so the values editor sends no filter and shows inactive values with their Reactivate action (REQ-CAT-138).

#### ATTRIBUTE-SAVE — API-CAT-025 … API-CAT-031, API-CAT-055            traces=API-CAT-025,API-CAT-027,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-055,REQ-CAT-042,REQ-CAT-043,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-054,REQ-CAT-055,REQ-CAT-138
Mutation, the drawer's ONE Save. The calls are ORDERED, and each is sent only after the previous succeeded:
1. `API-CAT-025` (new attribute) or `API-CAT-027` (dirty header, with `version`).
2. Value deactivations `API-CAT-031`. They go first, so that a nameAr freed among the active values can be reused (RULE-CAT-016).
3. Value reactivations `API-CAT-055` (lines marked `reactivate`, ADR-CAT-069), after the deactivations for the same reason.
4. Value creates `API-CAT-029` under the attribute's pk returned by step 1, or the existing pk.
5. Value changes `API-CAT-030` (with `version`).

The value calls are not sent at all when the value set is unchanged. At the first rejection the sequence stops: the attribute is re-read, the unsent values stay pending, the failed line is marked, and the "partial-save" copy is shown when an earlier call succeeded.

Errors:
- `CAT-422-NAME-AR-INVALID` → inline on nameAr (attribute or value line); text: RULE-CAT-001 message
- `CAT-422-NAME-EN-TOO-LONG` → inline on nameEn; text: RULE-CAT-002 message
- `CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE` → inline on code and nameAr of the attribute or value line; text: RULE-CAT-016 message
- `CAT-409-ATTRIBUTE-VALUE-IN-USE` → on the value line; text: RULE-CAT-017 message (the server fills {count})
- `CAT-422-CAPABILITY-OFF` → drawer banner, and the flags are re-read; text: RULE-CAT-008 message
- `CAT-500-CAPABILITY-UNREADABLE` → drawer banner; text: RULE-CAT-009 message
- `CAT-409-STALE-VERSION` → drawer banner + Reload; text: catalogue CAT-409-STALE-VERSION

Invalidation includes the Item reads, because derived Variant names change with the value names (AC-CAT-045, AC-CAT-048).

#### ATTRIBUTE-DEACTIVATE — API-CAT-028            traces=API-CAT-028,REQ-CAT-044,REQ-CAT-054
Record command in the drawer, after a confirmation, only while the drawer has no pending change.

#### ATTRIBUTE-REACTIVATE — API-CAT-054            traces=API-CAT-054,REQ-CAT-137
Record command in the drawer of an inactive attribute, after a confirmation, only while the drawer has no pending change; it sends `{isActiveFl: true}`. Its values keep their own state: an inactive value line offers "Reactivate", which marks it `reactivate` for the next Save (ATTRIBUTE-SAVE step 3 — ADR-CAT-069).

#### ATTRIBUTE-SCREEN-INIT — SCR-CAT-003
Permission read PERM_CAT_VARIANT_ATTRIBUTES_VIEW / CREATE / UPDATE / DELETE; the `variants` flag (UXD-CAT-002); no lookups; entity by id: ATTRIBUTE-QUERY. `returnTo` is kept to go back to the Items Entry after Save.

#### ATTRIBUTE-FACADE — SCR-CAT-003
It owns: the list filters (URL); the open attribute (`?attribute=`) and `values=open`; the drawer form with the value lines; derived loading. Operations: `save()`, `deactivate()`, `reactivate()`, `applyValues(set)` (from the second level, form state only).
<!-- SUB:F2-SCR-CAT-003:END -->
