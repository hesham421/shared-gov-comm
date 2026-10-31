<!-- source: PHASE:INT-C / XM:XM-INV-013 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-072, REQ-INV-088, REQ-INV-091 -->
<!-- XM:XM-INV-013:START traces=REQ-INV-072,REQ-INV-088,REQ-INV-091 -->
### XM-INV-013 — Barcode resolution
target     : CAT · ENT-CAT-009
type       : SOFT-READ
contract   : contract-cat.md#CON-CAT-005
requires   : CAT:DELIVERED
do         :
  ddl_patch : none — SOFT-READ, no column and no constraint
  adapter   : `com.commerce.inv.integration.cat.CatalogBarcodeAdapter implements BarcodePort` — injects CAT's published in-process crossmodule API (the bean exposing CON-CAT-010). `Optional<Long> variantOf(String barcode)`: lookupBarcode(barcode) (CON-CAT-010) → variantPk; CAT "not found" or "scale label not readable" → Optional.empty(); the embedded weight or price of a scale label is ignored. Callers: count line add → INV-422-BARCODE-UNKNOWN (RULE-INV-032, API-INV-029); opening stock row → INV-422-LOAD-ROW-VARIANT (RULE-INV-028, API-INV-047 — the row's `barcode` is resolved here only: CAT v2 publishes no exact Variant-code read, so v1 resolves no Variant code, and the sheet column is "Barcode" — ADR-INV-035; the code read is a platform finding owned by CAT); balance / valuation term filters → empty result (API-INV-016, 050)
  config    : Spring `@Component` in package com.commerce.inv.integration.cat, the only `BarcodePort` bean; in-process — no HTTP client, no base path, no timeout
tests      : AC-INV-079, AC-INV-097, AC-INV-101
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-013:END -->
