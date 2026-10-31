<!-- source: PHASE:INT-C / XM:XM-INV-014 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-052, REQ-INV-072, REQ-INV-078, REQ-INV-097 -->
<!-- XM:XM-INV-014:START traces=REQ-INV-052,REQ-INV-072,REQ-INV-078,REQ-INV-097 -->
### XM-INV-014 — Variant state read (minted at P3.1)
target     : CAT · ENT-CAT-002
type       : SOFT-READ
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : none — SOFT-READ, no column and no constraint
  adapter   : `com.commerce.inv.integration.cat.CatalogVariantAdapter implements VariantReadPort` — injects CAT's published in-process crossmodule API (the bean exposing CON-CAT-007 / CON-CAT-008). `VariantRef readVariant(long variantPk)`: getVariant(variantPk) (CON-CAT-008) → {variantPk, itemFk, code, nameAr, nameEn, isActiveFl}; getItem(itemFk) (CON-CAT-007) → {itemKindCode, isActiveFl, code, nameAr, nameEn}; returns {variantPk, itemPk, variantCode, variantNameAr, variantNameEn, variantActiveFl, itemActiveFl, itemKindCode, itemCode}; CAT "not found" → INV-404-VARIANT-NOT-FOUND. `Map<Long, VariantRef> readVariants(Set<Long>)` loops readVariant per page (≤ 100). Used by RULE-INV-018 (active Variant of a STANDARD Item — on save and again on post, engine step 0), RULE-INV-028 (an opening stock row given by `variantPk`, or the Variant a barcode resolved to through XM-INV-013; no Variant-code lookup exists in v1 — ADR-INV-035), the Variant display names of the search pages and the Item of a line (lot / dual-quantity settings)
  config    : Spring `@Component` in package com.commerce.inv.integration.cat, the only `VariantReadPort` bean; in-process — no HTTP client, no base path, no timeout; state read at request time (SRS D-23 — the catalogue-change event is not consumed in v1)
tests      : AC-INV-058, AC-INV-079, AC-INV-085, AC-INV-107
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-014:END -->
