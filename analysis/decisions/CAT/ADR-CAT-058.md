# ADR-CAT-058 — CAT seeds reach every new tenant through the given provisioning hook

Status : ACCEPTED (non-breaking) · Stage : P1 / P2 / P3.1 (gate-analysis revise, G5) · Module : CAT v2 · Date : 2026-10-28
Amends : ADR-CAT-046 §3

## Context
db-script BLOCK 8 seeds the CAT reference rows for the PLATFORM tenant only (`TENANT_ID = 1`). It said other tenants "receive copies through the given provisioning contributors". ADR-CAT-046 §3 deferred back to the db-script, and no given-usage row named a hook. Without the series, the first Item save of a new tenant fails with `SEQUENCE_NOT_CONFIGURED` (D-P2-14). The test precondition S0 assumes these rows exist in every test tenant.

The given erp-core 1.6.0 documents the hook and two copies it makes itself:
- `modules.tenant`: tenant provisioning (`POST /api/v1/platform/tenants`) with the `com.erp.tenant.TenantProvisioningContributor` SPI — "When the application seeds reference data every tenant needs: a `TenantProvisioningContributor`";
- `modules.mdl`: lookup types and values are "tenant-scoped and copied from `PLATFORM` at provisioning" (the erp-core tenancy decision of its `tenant` module);
- `modules.sequence`: series "are copied to new tenants with the counter at 1".

## Decision
1. The given copies the 10 CAT lookup types with their 34 seeded values (`mdl`) and the 3 number series CAT_ITEM, CAT_VARIANT and CAT_INTERNAL_BARCODE (`sequence`, counter at 1) from PLATFORM into each new tenant. CAT does not copy them again.
2. `CatTenantProvisioningContributor` (implements the given `TenantProvisioningContributor`) copies CAT's own seed rows, the 8 Unit e-invoice codes of D-12, from the source tenant to the new tenant, with an explicit `TENANT_ID`:
   `INSERT INTO CAT_UNIT_EINVOICE_CODE (KEY, VALUE_AR, VALUE_EN, EINVOICE_UNIT_CODE, IS_ACTIVE_FL, TENANT_ID, CREATED_BY, CREATED_AT) SELECT KEY, VALUE_AR, VALUE_EN, EINVOICE_UNIT_CODE, TRUE, :tenantId, 'system', now() FROM CAT_UNIT_EINVOICE_CODE WHERE TENANT_ID = :sourceTenantId AND IS_ACTIVE_FL`.
   The identity PK fills itself.
3. SRS A9 (given-usage) and the contract §4 gain the row `erp-core · modules.tenant · com.erp.tenant.TenantProvisioningContributor`. The backend plan's Given usage and bootstrap block name the contributor.
4. No core gap exists, so no `CORE-GAP-tenant-seed-provisioning` is raised.

## Consequences
- REQ-CAT-134 / AC-CAT-141 state the behaviour, and TC-CAT-193 provisions a second tenant and creates an Item in it.
- D-P2-14 no longer depends on the PLATFORM seed alone.

## Traces
REQ-CAT-134 · REQ-CAT-032 · REQ-CAT-033 · REQ-CAT-120 · ENT-CAT-012
