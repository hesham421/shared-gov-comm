# CORE-GAP-in-process-authorization — How is a cross-module read API authorized for callers without a permission of the owning module?

Status : OPEN (asked of erp-core 1.6.0) · Raised by : CAT v2 (gate-analysis round 2, G7) · Date : 2026-10-28
Decision that depends on it : ADR-CAT-061 (conditional — ADR-CAT-066)

## The gap
erp-core requires every service method to carry `@PreAuthorize("hasAuthority(T(…<Module>Permissions).PERM_…)")` that references its own module only. The sources are checklist C13 and conventions § Auth and permissions, rule 2. They state no exception for the read services behind a module's published in-process interface (`com.erp.<mod>.crossmodule.*`).

A catalogue is read in-process by callers that hold **no** permission of the owning module:
- a storefront shopper in the CUSTOMER realm, through SFR;
- a cashier on the SLS sales screen;
- system jobs of INV, PUR and TAX, with no user at all.

If the read service checks `PERM_CAT_ITEMS_VIEW`, none of them can read the catalogue. If it checks nothing, C13 is broken. Granting every shopper and cashier a CAT permission would leak the CAT back-office screens to them.

## What CAT does meanwhile (ADR-CAT-061)
- `CatCatalogueReadService` holds the read logic once and carries no `@PreAuthorize`. It is package-visible to the HTTP facades and to `CatalogueReadApi` only.
- Each HTTP read goes through a facade that carries the screen's `@PreAuthorize`.
- The calling module authorizes its own caller against its own permissions before it calls `CatalogueReadApi`. Tenancy still applies through the given tenant filter.
- One exception: `searchCatalogue` checks `PERM_CAT_ITEMS_VIEW` of the current principal itself (REQ-CAT-127).

## Question to the core
Which of these, or another, is the core's pattern?
1. A core-provided caller authority (for example `CROSSMODULE_READ`) granted to in-process calls, which a `crossmodule` read service checks.
2. A documented exception to C13 for read-only services reached only through a `crossmodule` interface, enforced by `ModuleBoundaryRules`.
3. A core helper that runs an in-process read as a system principal on behalf of the calling module.

## Impact when answered
CAT changes only the annotation or the facade of `CatCatalogueReadService`. No `CatalogueReadApi` signature and no HTTP endpoint changes.

## Traces
CON-CAT-007 … CON-CAT-017 · ADR-CAT-061 · ADR-CAT-066
