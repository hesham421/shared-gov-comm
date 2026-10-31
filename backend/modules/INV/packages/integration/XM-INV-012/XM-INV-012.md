<!-- source: PHASE:INT-C / XM:XM-INV-012 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-042, REQ-INV-049, REQ-INV-053, REQ-INV-054 -->
<!-- XM:XM-INV-012:START traces=REQ-INV-042,REQ-INV-049,REQ-INV-053,REQ-INV-054 -->
### XM-INV-012 — Bundle component read
target     : CAT · ENT-CAT-007
type       : SOFT-READ
contract   : contract-cat.md#CON-CAT-006
requires   : CAT:DELIVERED
do         :
  ddl_patch : none — SOFT-READ, no column and no constraint
  adapter   : `com.commerce.inv.integration.cat.CatalogBundleComponentAdapter implements BundleComponentPort` — injects CAT's published in-process crossmodule API (the bean exposing CON-CAT-007 / CON-CAT-008). `List<BundleComponent> activeComponents(long bundleVariantPk)`: getVariant(bundleVariantPk) (CON-CAT-008) → itemFk; getItem(itemFk) (CON-CAT-007) → itemKindCode; not BUNDLE → empty list with `isBundle = false`; BUNDLE → its Bundle component lines mapped to {componentVariantPk = componentVariantFk, quantity (3 dp, in the component Item's base unit — CON-CAT-006), componentBaseUomCode}; a line whose component Variant reads inactive through getVariant is dropped; CAT "not found" → INV-404-VARIANT-NOT-FOUND. The callers multiply the bundle quantity by `quantity` (REQ-INV-042 availability, 049 reservation, 053 issue) and raise INV-422-BUNDLE-NO-COMPONENTS on an empty list of a bundle (RULE-INV-022)
  config    : Spring `@Component` in package com.commerce.inv.integration.cat, the only `BundleComponentPort` bean; in-process call inside the caller's transaction — no HTTP client, no base path, no timeout
tests      : AC-INV-047, AC-INV-054, AC-INV-059, AC-INV-060
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-012:END -->
