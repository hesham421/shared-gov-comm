# ADR-CAT-063 — An inactive parent refuses child writes; deactivating a Variant deactivates its barcodes

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 (gate-analysis round 2, G2) · Module : CAT v2 · Date : 2026-10-28

## Context
The plan did not say what happens when a child record is written under an inactive parent. About ten endpoints were affected, so the gap was a missing rule, not one missing check:
- `PUT` on an inactive Item (API-CAT-003);
- Item units (API-CAT-010, 012), barcodes (API-CAT-014, 015), images (API-CAT-018, 019), Variant generation (API-CAT-006) and Bundle components (API-CAT-017) on an inactive Item;
- a barcode or an e-invoice override on a deactivated Variant (API-CAT-014, 015, 008).

A second gap sat in API-CAT-009. It deactivated a Variant's attribute assignments but not its barcodes. Those barcodes stayed active, so they kept blocking their value under RULE-CAT-029, yet no lookup could ever reach them (RULE-CAT-047 matches only active Variants of active Items — ADR-CAT-059).

Writes under an inactive parent create data that no channel can use and that the ACTIVE-state invariants (SRS A7) never checked.

## Decision
1. **New RULE-CAT-053 / REQ-CAT-135.** An inactive parent refuses child writes, with code `CAT-422-PARENT-INACTIVE`:
   - ar «هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا.»
   - en "This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant."
2. **What it refuses.** The rule refuses:
   - an update of an inactive Item;
   - the creation or change of a Variant, assignment, Bundle component line, Item unit or image of an inactive Item;
   - a new barcode or an e-invoice override on a Variant that is inactive or belongs to an inactive Item.
   
   **What it allows.** Deactivating a child record, so an inactive Item can always be cleaned up (TC-CAT-185 and TC-CAT-187 depend on this).
3. **Where it is checked.** In API-CAT-003, 006, 008, 010, 012, 014, 015, 017, 018 and 019, under the Item row lock (QR-CAT-010). Three endpoints gain the lock for this: API-CAT-003, 008 and 012. A parallel deactivation therefore cannot interleave with the check.
4. **Barcode cascade — the choice.** Deactivating a Variant also deactivates its active barcodes (new QR-CAT-072), with one catalogue-change event each. This releases their values for RULE-CAT-029: the GTIN of a discontinued Variant can be put on its successor.
   - It differs from ADR-CAT-060, which refuses to deactivate an Item unit that barcodes use. There the barcode stays meaningful after the change. Here the barcode can never resolve again, so keeping it active only blocks its value.
   - Refusing instead would make a Variant with barcodes impossible to deactivate without clearing its barcodes by hand first. That is the same result with more steps.
5. **Frontend.** The Entry of an inactive Item is read-only except Reactivate and the line deactivations. ITEM-SAVE routes `CAT-422-PARENT-INACTIVE` to the form banner with the RULE-CAT-053 text.

## Consequences
- New REQ-CAT-135, RULE-CAT-053, AC-CAT-144 and AC-CAT-145, QR-CAT-072, TC-CAT-198 and TC-CAT-199. The Error Catalog grows by one row.
- The api-spec answers `CAT-422-PARENT-INACTIVE` on the ten endpoints. API-CAT-009 traces DBF-CAT-113 and DBF-CAT-114.

## Traces
REQ-CAT-050 · REQ-CAT-135 · RULE-CAT-029 · RULE-CAT-047 · RULE-CAT-053 · API-CAT-003 · API-CAT-006 · API-CAT-008 · API-CAT-009 · API-CAT-010 · API-CAT-012 · API-CAT-014 · API-CAT-015 · API-CAT-017 · API-CAT-018 · API-CAT-019
