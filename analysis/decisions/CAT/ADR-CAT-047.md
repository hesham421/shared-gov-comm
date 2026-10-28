# ADR-CAT-047 — Repository deviations and concurrency guards

Status : ACCEPTED (non-breaking) · Stage : P3.1 · Module : CAT v2 · Date : 2026-10-28

## Context
Several catalogue reads cross entities or walk a hierarchy (Category tree and sub-trees, the catalogue search ranking, the capability turn-off check, Variant search with the first barcode, barcode lookup, derived Variant names). Several writes decide on a value they then write (last active Variant, at most 300 Variants, unique combinations, exactly one main image, at most 20 images, Category cycles, barcode/scale-prefix clashes, components of active Bundles). The engine requires an ADR for every join, native query and non-default lock, and an answer for every read-then-write.

## Decision
**Native queries / joins**
- Category tree, ancestors, descendants and "Items of a Category" use PostgreSQL recursive CTEs on CAT_CATEGORY (QR-CAT-006, QR-CAT-037).
- The catalogue search (QR-CAT-008) and the turn-off check (QR-CAT-009) are native aggregate queries.
- Variant search (QR-CAT-016), barcode lookup (QR-CAT-052), derived Variant names (QR-CAT-019) and images of a Variant (QR-CAT-058) join CAT tables only. No lookup label is joined: the backend returns codes.

**Tenant scope of native queries** (amended at the gate-analysis revise, G4 — given checklist C3 / F2)
- The native queries are QR-CAT-006, QR-CAT-008, QR-CAT-009 and QR-CAT-037. Each one bypasses the given tenant filter, so each repository method carries `@TenantUnscoped(reason = "native SQL — TENANT_ID bound explicitly", adr = "ADR-CAT-047")` (`com.erp.tenant.TenantUnscoped`).
- Each of them writes `TENANT_ID = :tenant` explicitly on every table it reads:
  - every `FROM` and `JOIN` of the outer query;
  - every sub-select and every `EXISTS`;
  - both branches of every recursive CTE (`CAT_CATEGORY` up and down, the anchor and the recursive member);
  - `CAT_BARCODE` and `CAT_VARIANT` inside the barcode sub-queries.
- `:tenant` is bound from `TenantContext.require()`, never from the request.
- The other QRs run through the given tenant filter (JPQL, derived queries, search builders). They need no annotation. Their logical specs still show `TENANT_ID = :tenant` so the reader sees the scope.

**Locks** (all inside the request transaction, released at commit)
- Item-scoped mutations (generate Variants, deactivate Variant, publish, deactivate Item, Bundle components, media changes, Item unit add) first lock the CAT_ITEM row with `SELECT … FOR UPDATE` (QR-CAT-010). Every guard about "the Variants / images / units / lines of one Item" is decided under that lock.
- Component Variants of a Bundle, and their Items, are read `FOR SHARE` (QR-CAT-017); deactivating a Variant or an Item takes the Item lock and then counts active Bundles (QR-CAT-042), so a deactivation and a Bundle save never both pass.
- Variant attributes and values chosen for generation are read `FOR SHARE` (QR-CAT-031); their deactivation locks the row `FOR UPDATE` before counting usage (QR-CAT-021).
- Category moves and creates take `pg_advisory_xact_lock` on the key (TENANT_ID, 'CAT_CATEGORY_TREE') before the cycle and depth check (QR-CAT-037); Category deactivation locks the Category row and Items assigned to a Category read it `FOR SHARE`.
- GTIN / INTERNAL barcode adds and scale-label-layout creates take `pg_advisory_xact_lock` on (TENANT_ID, 'CAT_SCALE_PREFIX') before RULE-CAT-032 is checked.
- Every "unique among active records" rule is finally held by the db-script's partial unique indexes; a unique violation is translated to the RULE's catalog row (never to 500).
- Codes and internal barcode digits come from the given `NumberSeriesApi`, whose allocation is atomic; UQ_CAT_ITEM_CODE / UQ_CAT_VARIANT_CODE / UQ_CAT_BARCODE_VALUE back it.

## Consequences
- The implementer rewrites each QR with the project's query strategy but keeps the lock each QR names.
- Lock order is always Item → Variant → line rows, to avoid deadlocks.

## Traces
REQ-CAT-049 · REQ-CAT-051 · REQ-CAT-052 · REQ-CAT-053 · REQ-CAT-062 · REQ-CAT-067 · REQ-CAT-068 · REQ-CAT-069 · REQ-CAT-087 · REQ-CAT-091 · REQ-CAT-106 · REQ-CAT-110 · REQ-CAT-111 · REQ-CAT-021 · REQ-CAT-125
