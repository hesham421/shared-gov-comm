<!-- source: PHASE:INT-C / XM:XM-INV-015 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-039, REQ-INV-124, REQ-INV-126, REQ-INV-127, REQ-INV-129 -->
<!-- XM:XM-INV-015:START traces=REQ-INV-039,REQ-INV-124,REQ-INV-126,REQ-INV-127,REQ-INV-129 -->
### XM-INV-015 — Item state read (minted at P3.1)
target     : CAT · ENT-CAT-001
type       : SOFT-READ
contract   : contract-cat.md#CON-CAT-001
requires   : CAT:DELIVERED
do         :
  ddl_patch : none — SOFT-READ, no column and no constraint
  adapter   : `com.commerce.inv.integration.cat.CatalogItemAdapter implements ItemReadPort` — injects CAT's published in-process crossmodule API (the bean exposing CON-CAT-007 / CON-CAT-016). `ItemRef readItem(long itemPk)`: getItem(itemPk) (CON-CAT-007) → {itemPk, code, nameAr, nameEn, itemKindCode, isActiveFl, variantPks = its Variants' identifiers}; CAT "not found" → INV-404-ITEM-NOT-FOUND. `List<Long> searchItems(String term)`: searchCatalogue(term) (CON-CAT-016) → at most 20 itemPks (term under 2 characters → empty). Used by RULE-INV-045 (BUNDLE refused), the settings key / names (API-INV-044, 045), RULE-INV-042 / 043 (the Item's Variants for QR-INV-045 / 046), the Item filters of API-INV-016, 043, 050
  config    : Spring `@Component` in package com.commerce.inv.integration.cat, the only `ItemReadPort` bean; in-process — no HTTP client, no base path, no timeout
tests      : AC-INV-139, AC-INV-142, AC-INV-143, AC-INV-145
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-015:END -->
