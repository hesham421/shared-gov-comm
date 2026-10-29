<!-- source: PHASE:F4 / SUB:F4-SCR-CAT-003 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-041, API-CAT-054, API-CAT-055, REQ-CAT-011, REQ-CAT-012, REQ-CAT-013, REQ-CAT-014, REQ-CAT-016, REQ-CAT-042, REQ-CAT-043, REQ-CAT-044, REQ-CAT-045, REQ-CAT-046, REQ-CAT-047, REQ-CAT-048, REQ-CAT-054, REQ-CAT-055, REQ-CAT-137, REQ-CAT-138, SCR-CAT-003, UXD-CAT-002 -->
<!-- SUB:F4-SCR-CAT-003:START traces=REQ-CAT-137,REQ-CAT-138,API-CAT-054,API-CAT-055,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-041,UXD-CAT-002,SCR-CAT-003 -->
### F4 · SCR-CAT-003 — Variant attributes

#### VARIANT-ATTRIBUTES-SCREEN — SCR-CAT-003            traces=REQ-CAT-014,REQ-CAT-016,REQ-CAT-048,UXD-CAT-002,API-CAT-041,API-CAT-026
- **Guard**: `/cat/variant-attributes` needs PERM_CAT_VARIANT_ATTRIBUTES_VIEW. While `variants` is off, the route renders the RULE-CAT-008 state (text with «variants», next action "Catalogue capability settings" is the platform settings' entry, not a CAT route) and its menu entry is absent (REQ-CAT-014).
- **Facade**: ATTRIBUTE-FACADE only.
- **Cross-module**: `variants` flag → UXD-CAT-002.
- **Components**: `VariantAttributesPage` (filter bar, list), `AttributeEntryDrawer` (`?attribute=`), and `AttributeValuesDrawer` (`&values=open`), the second level.
- **Composition**: container drawer. The values are a summary row in the entry drawer (active count + first 5 names + "Edit values"). `AttributeValuesDrawer` is a sibling of the entry drawer, opened from the search params; its lines scroll with its body. Confirm returns the set to the entry's form state, and Cancel discards the draft.
- **Saves**: ONE per entry drawer (`ATTRIBUTE-SAVE`). It owns the attribute call, then the value calls, ordered: the value calls are sent only after the attribute call succeeded, and they are skipped when the value set is unchanged. Attribute Deactivate and Reactivate (`ATTRIBUTE-REACTIVATE`) are record commands; a value line's Reactivate is a pending line state sent by the Save (ADR-CAT-069).
- **Navigation out**: after Save, when `returnTo` is set → back to that Items Entry route, which re-reads the attribute options.
<!-- SUB:F4-SCR-CAT-003:END -->
