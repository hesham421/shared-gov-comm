# ADR-PTY-044 — The given-usage rows name only seams the given publishes

Status  : ACCEPTED (non-breaking) · Stage : P2 repair round 1 (C-GIVEN-2) · Module : PTY v1 · Date : 2026-10-29

## Context
`gov.py analyze` C-GIVEN-2 (given-via-extension) refused three rows of the SRS A9 `given-usage` block, which the contract Part 4 repeats word for word. A use is supported only if it goes through an extension point, interface or api that the given's manifest declares for that module.

| Row | Named `via` | What the given's module publishes |
|---|---|---|
| modules.tenant | `com.erp.tenant.TenantContext` | extension points `TenantExportContributor`, `TenantProvisioningContributor`; interfaces `TenantPolicy`, `TenantRunner`, `crossmodule.TenantLookupApi`; api `docs/api-docs/tenant/index.md` |
| modules.common | `com.erp.common.domain.AuditableEntity` | nothing |
| modules.module | `META-INF/erp/modules/pty.yaml` | extension point `HomeCardProvider`; interfaces `crossmodule.HomeCardApi`, `crossmodule.ModuleCatalogApi`, `crossmodule.ModuleEntitlementApi`; api `docs/api-docs/module/index.md` |

## Decision
1. **Tenant re-binding.** PTY's after-commit work (invitation issue, approver notification) runs its tenant-scoped body through the published interface `com.erp.tenant.TenantRunner`, not through the internal `TenantContext`. The row's `via` becomes `com.erp.tenant.TenantRunner`.
2. **Module manifest.** The manifest file `META-INF/erp/modules/pty.yaml` is PTY's own artifact. The given's seam for it is the module api it documents, so the row's `via` becomes `docs/api-docs/module/index.md` and the purpose names the file. This follows the CAT precedent, where a file-based use is routed through `docs/api-docs/file/index.md`.
3. **Entity base class.** `erp-core:modules.common` publishes no extension point, interface or api. Inheriting its entity base class is a build dependency of PTY's code. It is not a runtime use through a published seam, so the row is removed. The platform columns that the base class maps stay as they are in the model: tenantId, version and the audit fields (SRS A3, ADR-PTY-035, DBF-PTY-068 … DBF-PTY-077). The SRS A9 notes this below the block.
4. The SRS A9 and the contract Part 4 keep the same rows, in the same order.

## Consequences
- SRS A9 and contract Part 4: the tenant and module rows are reworded and the common row is removed. 15 rows become 14.
- Database (P2): no change. Every column stays, including TENANT_ID and VERSION.
- Backend plan (P3.1): wherever it says `TenantContext.runAs`, the next pass should read `TenantRunner`. This is a wording change only; the behaviour does not change.
- No CON item, REQ, AC, RULE or ENT changes.

## Traces
C-GIVEN-2 · ADR-PTY-035 · REQ-PTY-051, REQ-PTY-059, REQ-PTY-093 · ENT-PTY-001 … ENT-PTY-005
