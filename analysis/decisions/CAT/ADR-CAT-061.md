# ADR-CAT-061 — CatalogueReadApi calls an unsecured read service; the calling module authorizes its own caller

Status : ACCEPTED (non-breaking) · Stage : P3.1 (gate-analysis revise, G13) · Module : CAT v2 · Date : 2026-10-28
Supersedes : ADR-CAT-045 §1 and §4 (the rest of ADR-CAT-045 stands)

## Context
ADR-CAT-045 §1 made every `CatalogueReadApi` method delegate to "the same service method as the HTTP endpoint". Checklist C13 requires every service method to carry `@PreAuthorize(… PERM_…)` that references its own module only. So the in-process methods would check `PERM_CAT_ITEMS_VIEW` and similar.

The consumers of `CatalogueReadApi` hold no CAT permission:
- storefront shoppers in the CUSTOMER realm (through SFR);
- SLS cashiers on the sales screen;
- INV, PUR and TAX jobs.

The in-process surface was therefore either unusable or, if it skipped the check, unspecified.

## Decision
1. Each read is split in two:
   - **Controller-side facade** (`Cat<Resource>QueryFacade`, one per HTTP read). It carries `@PreAuthorize("hasAuthority(T(com.erp.cat.permission.CatPermissions).PERM_…)")` with its screen's permission, and calls the read service.
   - **Read service** (`CatCatalogueReadService`). It carries no `@PreAuthorize` and holds the read logic once: queries, derivations and the tenant filter. It is package-visible to the facades and to `CatalogueReadApi` only.
2. `CatalogueReadApi` (`com.erp.cat.crossmodule`) calls the read service directly. The **calling module authorizes its own caller** against its own permissions before it calls CAT. For example, SFR authorizes the shopper on its storefront screen, and SLS authorizes the cashier on its sales screen. CAT trusts an in-process call; tenancy still applies through the given tenant filter.
3. **One exception.** `searchCatalogue` keeps the permission check inside the read service: it answers an empty list when the current principal lacks `PERM_CAT_ITEMS_VIEW` (REQ-CAT-127). The global search host is the shell, which holds no catalogue permission of its own, and the result is still filtered per user.
4. Mutations are unchanged. Every mutating service method keeps its `@PreAuthorize` (C13), and no mutation is published in-process.

## Consequences
- PHASE CORE "Module structure" and the "In-process contract surface" table of the backend plan name the facade and the read service.
- Contract §0 states that the calling module authorizes its own caller.
- `ModuleBoundaryRules` keeps consumers on `com.erp.cat.crossmodule` only. The read service is not public API.

## Traces
CON-CAT-007 · CON-CAT-008 · CON-CAT-009 · CON-CAT-010 · CON-CAT-011 · CON-CAT-012 · CON-CAT-013 · CON-CAT-014 · CON-CAT-015 · CON-CAT-016 · CON-CAT-017 · REQ-CAT-001 · REQ-CAT-115 · REQ-CAT-127
