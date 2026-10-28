# ADR-CAT-059 — Every transition into ACTIVE re-establishes the Item invariants; channel reads see only active Items

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 (gate-analysis revise, G6) · Module : CAT v2 · Date : 2026-10-28

## Context
The catalogue guards were written per trigger, not per state invariant. While an Item is inactive, its children can drift into states an active Item may never be in:
- RULE-CAT-015 guards only an active Item, so an inactive Item can lose every active Variant and then be reactivated with none;
- RULE-CAT-025 lets a Category go inactive while its Items are inactive, and reactivation did not re-check the Category;
- a reactivated BUNDLE may hold components that were deactivated meanwhile (RULE-CAT-021 guards only active Bundles);
- the barcode lookup (QR-CAT-052), the Variant search for labels (QR-CAT-016) and the label read (QR-CAT-017) filtered on the Variant only, so a barcode of an inactive Item still resolved at the point of sale, against REQ-CAT-005 ("leaves every channel").

## Decision
1. **One invariant, stated once (SRS A7).** An ACTIVE Item has:
   - at least one active Variant;
   - an empty categoryFk, or one that points to an active Category;
   - when its kind is BUNDLE, only active component lines whose Variants are active and belong to active STANDARD Items;
   - an Arabic name that no other active Item carries.
   Every transition into ACTIVE (create, import, reactivate) re-establishes it.
2. **Reactivation (`API-CAT-005`, REQ-CAT-006, REQ-CAT-130)** checks, under the Item lock, in this order:
   - RULE-CAT-050 (new, `CAT-422-ITEM-NO-ACTIVE-VARIANT`);
   - RULE-CAT-026 (`CAT-422-CATEGORY-INACTIVE`, its trigger extended to reactivation);
   - RULE-CAT-019 (`CAT-422-BUNDLE-COMPONENT-INVALID`, its trigger extended to reactivation);
   - RULE-CAT-003 (`CAT-409-ITEM-NAME-DUPLICATE`).
   The first failure refuses the reactivation and nothing changes.
3. **Channel reads exclude inactive Items.** The barcode lookup (RULE-CAT-047, REQ-CAT-086) and the Variant search and label reads (REQ-CAT-101, REQ-CAT-102) match only an active Variant of an active Item. A Variant of an inactive Item is "not found" there. The read by identifier (REQ-CAT-002, REQ-CAT-050) still returns it, so earlier documents resolve (RULE-CAT-048).
4. The publish guard (RULE-CAT-041) is unchanged. Publishing already requires an active Item with an active Variant.

## Consequences
- New RULE-CAT-050, REQ-CAT-130 and AC-CAT-132 … 136; TC-CAT-185 … 189.
- `API-CAT-005` reads QR-CAT-015, 032, 039 and 017 (FOR SHARE) under the Item lock. QR-CAT-016, 017 and 052 add `i.IS_ACTIVE_FL`.

## Traces
REQ-CAT-005 · REQ-CAT-006 · REQ-CAT-130 · REQ-CAT-086 · REQ-CAT-101 · RULE-CAT-015 · RULE-CAT-019 · RULE-CAT-025 · RULE-CAT-026 · RULE-CAT-047 · RULE-CAT-048 · RULE-CAT-050
