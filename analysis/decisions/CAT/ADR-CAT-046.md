# ADR-CAT-046 — Security bootstrap (pages, permissions, preset-role grants) and tenant copies of CAT seeds

Status : ACCEPTED (non-breaking) · Stage : P3.1 · Module : CAT v2 · Date : 2026-10-28

## Context
SRS B4 declares six page codes and the actions each preset role holds on them (Owner, Seller/cashier, Warehouse staff). The SRS Access summary says the given `sec` module derives permission names from the page codes and that CAT seeds none. The given-usage row for `sec` names `com.erp.sec.permission.PermissionContributor`. Registering a page and deriving a permission is not a grant: without the role grants every endpoint answers 403 to every caller. The db-script seeds lookup values, number series and Unit e-invoice codes for the PLATFORM tenant (TENANT_ID 1) and states that "new tenants receive copies through the given provisioning contributors"; no given-usage row names such a contributor.

## Decision
1. `CatPermissionContributor` (implements the given `PermissionContributor`) registers the six page codes CAT_ITEMS, CAT_CATEGORIES, CAT_VARIANT_ATTRIBUTES, CAT_ITEM_IMPORT, CAT_BARCODE_LABELS and CAT_SETTINGS, each with the actions SRS B4 uses. The given `sec` module derives the names `PERM_<PAGE_CODE>_<ACTION>` (e.g. `PERM_CAT_ITEMS_VIEW`).
2. The default grants of the three preset roles are exactly SRS B4. They are produced by the contributor's default-grant declaration where the given `PermissionContributor` offers one; otherwise by a CAT application migration (`V1000+`) that inserts the grant rows for the preset roles into the given's role-permission data. The executor confirms which of the two the given 1.6.0 supports from the `erp-core-usage` skill before coding.
3. Copies of the CAT seeds for tenants other than PLATFORM: amended by ADR-CAT-058. The given copies the CAT lookup types and values (`mdl`) and the three number series (`sequence`) at provisioning. `CatTenantProvisioningContributor` (given `TenantProvisioningContributor`, a given-usage row of SRS A9) copies the 8 Unit e-invoice codes.

## Consequences
- The `bootstrap` block of the plan names every page permission with its grant and its producer.
- Custom roles receive CAT actions through the given `sec` screens only.

## Traces
REQ-CAT-127 · REQ-CAT-001 · REQ-CAT-073 · REQ-CAT-074 · REQ-CAT-032 · REQ-CAT-095
