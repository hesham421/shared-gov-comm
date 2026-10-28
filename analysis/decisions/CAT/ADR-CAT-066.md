# ADR-CAT-066 — In-process authorization is a core gap; ADR-CAT-061 holds on condition

Status : ACCEPTED (non-breaking) · Stage : P3.1 (gate-analysis round 2, G7) · Module : CAT v2 · Date : 2026-10-28
Amends : ADR-CAT-061 (now conditional on the core's answer)

## Context
ADR-CAT-061 lets `CatCatalogueReadService` carry no `@PreAuthorize`, so that `CatalogueReadApi` can serve callers who hold no CAT permission (SFR shoppers, SLS cashiers, INV / PUR / TAX jobs). The core does not grant that exception:
- erp-core checklist C13 and conventions § Auth and permissions, rule 2, require `@PreAuthorize` on every service method, with no exception for an in-process read;
- checklist F9 forbids working around a missing core capability instead of raising `CORE-GAP-<topic>.md`.

## Decision
1. CAT raises `analysis/decisions/CAT/CORE-GAP-in-process-authorization.md`. It asks how a cross-module read API is authorized for callers that hold no permission of the owning module.
2. ADR-CAT-061 stays in force **on condition**. When the core answers, CAT adopts the answer in a delta, for example:
   - a core caller authority for in-process reads;
   - a documented C13 exception for `crossmodule` read services;
   - a core helper that runs the read as a system principal.
   
   No `CatalogueReadApi` signature changes; only the annotation or the facade changes.
3. The backend plan names the CORE-GAP in PHASE CORE ("Core gaps") and in the index. The contract §0 states the condition.

## Consequences
- Until the answer, `CatCatalogueReadService` has no `@PreAuthorize`, and the HTTP facades keep the screen permissions (unchanged).
- The CORE-GAP is OPEN. It is not a CAT question, and it blocks no CAT stage.

## Traces
CON-CAT-007 · CON-CAT-008 · CON-CAT-009 · CON-CAT-010 · CON-CAT-011 · CON-CAT-012 · CON-CAT-013 · CON-CAT-014 · CON-CAT-015 · CON-CAT-016 · CON-CAT-017 · ADR-CAT-061
