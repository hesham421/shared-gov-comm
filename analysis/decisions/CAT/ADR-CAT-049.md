# ADR-CAT-049 — Items screen: one Save that owns ordered calls; record commands outside the form

Status : ACCEPTED (non-breaking) · Stage : P3.2 · Module : CAT v2 · Date : 2026-10-28

## Context
SRS SCR-REQ-CAT-001 (B3) lists one Save for the Item (create REQ-CAT-024 / update REQ-CAT-004) and separate actions for each child line: add / change / deactivate Item unit, add GTIN / generate internal / add scale barcode, deactivate barcode, generate Variants, deactivate Variant, save the Variant e-invoice override, save Bundle components, upload / update / remove image. The API document has one operation per child action, and each one commits its line on its own (API-CAT-006, 008 – 010, 012 – 020). The profile convention `screen_composition` allows one save per screen: an inline control that carries its own save, beside a screen Save, makes the user believe both were saved. The SRS does not say whether a child action submits on its own or with the Item.

## Decision
1. The Items screen is a route page (`/cat/items/new`, `/cat/items/:itemPk`). Its child collections (Variants, Item units, barcodes, Bundle components, images) sit inline in panes under the header, as the `page` container prescribes. One line is added or changed in a one-line drawer. Confirming the drawer returns the line to the page's form state and saves nothing.
2. Two pickers over unbounded sets open as second-level views, siblings of the page: the Variant generation picker (attribute values) and the Bundle component picker (Variants of active STANDARD Items). Each one returns its selection to the form state and saves nothing.
3. The page has ONE Save. It owns the calls below, sent in this order. Each call is sent only after the one before it succeeded, and a part is skipped when it is unchanged:
   1. create `API-CAT-001` (carries `bundleComponents`, an empty array for a STANDARD Item), or update `API-CAT-003`;
   2. replace the Bundle component set `API-CAT-017` (only on update of a BUNDLE Item, only when the set changed);
   3. pending deactivations: barcodes `API-CAT-016`, images `API-CAT-020`, Variants `API-CAT-009`, Item units `API-CAT-013`. These go first so that re-adding a unit (RULE-CAT-028 next action) and the limits (RULE-CAT-014, RULE-CAT-038) see the reduced set;
   4. Item unit adds `API-CAT-010` and flag changes `API-CAT-012`. They go before barcodes, because a barcode unit must be an active unit (RULE-CAT-031);
   5. Variant generation `API-CAT-006`;
   6. Variant e-invoice overrides `API-CAT-008`;
   7. barcode adds `API-CAT-014` and internal barcode requests `API-CAT-015`, on saved Variants only;
   8. image uploads `API-CAT-018`, then image changes `API-CAT-019`.
4. On the first rejected call the sequence stops. Calls that already succeeded stay committed, because each one is its own transaction on the server. The screen re-reads the Item (`API-CAT-002`) and keeps the unsent and rejected lines as pending, with the rejection shown inline on the line that failed. A new Item that failed after step 1 continues on its new route `/cat/items/:itemPk`. The next Save resumes from the remaining pending lines.
5. Record commands are not form saves: Deactivate / Reactivate / Publish / Unpublish (`API-CAT-004`, `API-CAT-005`). Each runs at once after a confirmation, and only while the form has no pending change. With pending changes, its button is disabled and shows the "save first" hint.
6. Barcodes can be added only to Variants that are already saved. A Variant created by a pending generation takes its barcodes after Save.

## Consequences
- Every child action of SRS B3 is still offered, with its RULEs. Only the moment of sending changes, so no REQ is affected.
- The plan (F2 `ITEM-SAVE`, F4 Composition and Saves) states this order. The implementer does not choose another one.
- The bilingual texts of the partial-save notice and the "save first" hint are UX copy fixed in the ui-ux-spec. They are not catalogue messages.

## Traces
REQ-CAT-004 · REQ-CAT-024 · REQ-CAT-049 · REQ-CAT-056 · REQ-CAT-057 · REQ-CAT-077 · REQ-CAT-083 · REQ-CAT-084 · REQ-CAT-105 · REQ-CAT-118 · SCR-CAT-001
