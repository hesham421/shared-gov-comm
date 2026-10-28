# ADR-CAT-072 — The default Variant moves with the Item's active Variants

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 (gate-analysis round 6, G3) · Module : CAT v2 · Date : 2026-10-28

## Context
CON-CAT-002 promises consumers that every Item has a default Variant (`isDefaultFl` true). Stock and price consumers fall back to it. Two operations could leave an active Item with no active default Variant:
- API-CAT-009 allows deactivating the default Variant whenever another Variant of the Item is active, and nothing moved the flag.
- Variant generation on an Item with 0 active Variants (ADR-CAT-068, the repair of RULE-CAT-050) created every new Variant with `isDefaultFl` false, so the reactivated Item's only default was its inactive former Variant.

QR-CAT-013 ("default first") then listed an arbitrary Variant first, and `UQ_CAT_VARIANT_DEFAULT (TENANT_ID, ITEM_FK) WHERE IS_DEFAULT_FL` allowed the one default row to be inactive.

Options:
- (a) the default moves to an active Variant whenever the default leaves the active set;
- (b) the default stays where it is, and consumers fall back to "the active Variant with the lowest code" when the default is inactive.

## Decision
Option (a), the default moves:
1. **Deactivating the default Variant** (API-CAT-009) while another Variant of the Item is active moves `isDefaultFl`, in the same transaction and under the Item lock (QR-CAT-010), to the remaining active Variant with the lowest code. The former default's flag is cleared first, then the new default's flag is set (QR-CAT-014), so the unique index is satisfied after each statement. The new default records one catalogue-change event UPDATED.
2. **Deactivating the last active Variant** of an inactive Item (allowed — RULE-CAT-015 guards only an active Item) leaves the flag on that inactive Variant: there is no active Variant to move to.
3. **Generating Variants for an Item with 0 active Variants** (API-CAT-006) gives `isDefaultFl` to the first Variant created (lowest new code) and clears it on the inactive former default, in the same transaction, under the Item lock. Generation on an Item that has active Variants leaves the default where it is.
4. **The index stays as it is.** `UQ_CAT_VARIANT_DEFAULT` keeps at most one row with `IS_DEFAULT_FL` per Item, active or inactive. The service clears the old row before it sets the new one, so the database needs no change and no partial index on `IS_ACTIVE_FL`.
5. **The invariant.** An active Item always has exactly one default Variant, and it is active (SRS A7 invariant 5). CON-CAT-002 is amended to "an active Item always has exactly one active default Variant".

## Consequences
- SRS: REQ-CAT-025 statement, AC-CAT-163 and AC-CAT-164, A7 invariant 5, ENT-CAT-002.isDefaultFl.
- Contract: CON-CAT-002 wording (the identifier, key columns and BIGINT type are unchanged, so the change is non-breaking).
- db-script: the comments of `IS_DEFAULT_FL` and `UQ_CAT_VARIANT_DEFAULT` only.
- Backend plan: API-CAT-006 and API-CAT-009 orchestration, QR-CAT-013 and QR-CAT-014 callers, ENT-CAT-002 state machine.
- Tests: TC-CAT-221 (move on deactivation), TC-CAT-222 (move on generation); TC-CAT-185 and TC-CAT-200 assert the default too.

## Traces
REQ-CAT-025 · REQ-CAT-049 · REQ-CAT-050 · REQ-CAT-130 · AC-CAT-163 · AC-CAT-164 · CON-CAT-002 · API-CAT-006 · API-CAT-009 · QR-CAT-013 · QR-CAT-014 · DBF-CAT-024 · ADR-CAT-068
