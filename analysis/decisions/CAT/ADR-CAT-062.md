# ADR-CAT-062 — Arabic normalisation for name uniqueness and search; trigram indexes; escaped LIKE

Status : ACCEPTED (non-breaking) · Stage : P2 / P3.1 (gate-analysis revise, G18) · Module : CAT v2 · Date : 2026-10-28

## Context
- RULE-CAT-003 compared Item names after trimming and collapsing spaces only. Arabic letter variants defeat that comparison: «أحمر» and «احمر», «شنطة» and «شنطه», «ممتاز» and «مُمتاز». Two active Items with one name can therefore coexist.
- The Item search, the catalogue search and the Variant search ran `ILIKE '%text%'` over NAME_AR, NAME_EN, CODE and BARCODE_VALUE. Those columns have btree indexes only, which cannot serve a leading wildcard, so every search was a full scan.
- `%` and `_` typed by a user were read as wildcards.

## Decision
1. **One normalisation function** in the db-script (BLOCK 10): `CAT_NORMALIZE_AR(text) RETURNS text IMMUTABLE STRICT PARALLEL SAFE`. It:
   - removes the tashkeel marks U+064B–U+065F and U+0670, and the tatweel U+0640;
   - folds أ إ آ ٱ (U+0623, U+0625, U+0622, U+0671) to ا (U+0627);
   - folds ة (U+0629) to ه (U+0647);
   - folds ى (U+0649) to ي (U+064A);
   - lower-cases Latin letters;
   - trims and collapses whitespace.
   The service applies the same rules in Java for messages and in-file import checks.
2. **Uniqueness.** `UQ_CAT_ITEM_NAME_AR` is a partial unique index on `(TENANT_ID, CAT_NORMALIZE_AR(NAME_AR)) WHERE IS_ACTIVE_FL`. RULE-CAT-003 compares names after this normalisation. QR-CAT-002 compares `CAT_NORMALIZE_AR(NAME_AR) = CAT_NORMALIZE_AR(:nameAr)`. The stored name is unchanged (REQ-CAT-028).
3. **Search.** QR-CAT-005, 008 and 016 match `CAT_NORMALIZE_AR(column) LIKE '%' || CAT_NORMALIZE_AR(:text) || '%' ESCAPE '\'` on names, and `LIKE … ESCAPE '\'` on codes and barcodes. The service escapes `\`, `%` and `_` in the term before binding it. Exact code or barcode matches keep `=`.
4. **Indexes.** BLOCK 7 adds trigram GIN indexes with the PostgreSQL `pg_trgm` extension (`CREATE EXTENSION IF NOT EXISTS pg_trgm`, in the CAT migration; the deployment grants the right to create it):
   - `CAT_NORMALIZE_AR(NAME_AR)` and `CAT_NORMALIZE_AR(NAME_EN)` on CAT_ITEM;
   - `CODE` on CAT_ITEM and CAT_VARIANT;
   - `BARCODE_VALUE` on CAT_BARCODE.
   The btree indexes `IDX_CAT_ITEM_NAME_AR` / `IDX_CAT_ITEM_NAME_EN` stay for the nameAr sort.
5. Category, Variant attribute and value names keep exact-name uniqueness. Their lists are short, owner-maintained and shown side by side. A later delta may extend the function to them.

## Consequences
- RULE-CAT-003's statement names the normalisation. Its message is unchanged.
- No DBF, column or count changes; one function and five indexes are added.

## Traces
RULE-CAT-003 · REQ-CAT-003 · REQ-CAT-007 · REQ-CAT-101 · REQ-CAT-125 · DBF-CAT-002 · DBF-CAT-003 · DBF-CAT-004 · DBF-CAT-023 · DBF-CAT-110
