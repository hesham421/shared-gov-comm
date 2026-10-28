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
- The native queries are QR-CAT-006, QR-CAT-008, QR-CAT-009 and QR-CAT-037, and — since the fifth gate-analysis revise — the advisory locks QR-CAT-073 and QR-CAT-074 (see Locks). Each one bypasses the given tenant filter, so each repository method carries `@TenantUnscoped(reason = "native SQL — TENANT_ID bound explicitly", adr = "ADR-CAT-047")` (`com.erp.tenant.TenantUnscoped`).
- Each of the first four writes `TENANT_ID = :tenant` explicitly on every table it reads (the two locks read no table and name TENANT_ID as their first lock key):
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
- **Amended at the third gate-analysis revise (G4).** Category deactivation (API-CAT-024) also takes the tree lock, before it locks the row and counts the active children (QR-CAT-038), and Category reactivation (API-CAT-053) takes it before it reads the parent. Before this, API-CAT-024 locked only the Category row, while API-CAT-021 and 023 read the parent unlocked under the tree lock: a sub-category create or move could commit an active child under a parent that a parallel deactivation was making inactive, which RULE-CAT-023 forbids. With every Category create, move, deactivation and reactivation behind the one tree lock, exactly one of a parallel "create child" and "deactivate parent" succeeds (AC-CAT-153, TC-CAT-211). Lock order: tree lock → Category row.
- GTIN / INTERNAL barcode adds and scale-label-layout creates take `pg_advisory_xact_lock` on (TENANT_ID, 'CAT_SCALE_PREFIX') before RULE-CAT-032 is checked.
- **Amended at the fifth gate-analysis revise (G4) — exact key form of the two advisory locks.** PostgreSQL 16 offers `pg_advisory_xact_lock(bigint)` and `pg_advisory_xact_lock(int, int)` only; the earlier wording "(TENANT_ID, 'CAT_CATEGORY_TREE')" matched neither. The two locks are now catalogued native statements:
  - QR-CAT-073 "Category tree lock": `SELECT pg_advisory_xact_lock(CAST(:tenant AS integer), hashtext('CAT_CATEGORY_TREE'))` — taken by API-CAT-021, 023, 024 and 053;
  - QR-CAT-074 "Scale-prefix lock": `SELECT pg_advisory_xact_lock(CAST(:tenant AS integer), hashtext('CAT_SCALE_PREFIX'))` — taken by API-CAT-014, 015, 032 and, once per import, 049.
  `:tenant` is TENANT_ID, bound from `TenantContext.require()`; CORE_TENANT ids are issued from 1 and stay below 2^31, so the cast to `integer` never overflows. Both repository methods (`CatCategoryRepository.lockTree`, `CatScaleLabelLayoutRepository.lockScalePrefix`) carry `@TenantUnscoped(reason = "advisory lock keyed by TENANT_ID", adr = "ADR-CAT-047")` (erp-core checklist C3). The native queries of this ADR are therefore six: QR-CAT-006, 008, 009, 037, 073 and 074.
- **Amended at the fifth gate-analysis revise (G2).** Creating a Variant attribute value (API-CAT-029) reads its attribute `FOR SHARE` (QR-CAT-022), as value reactivation (API-CAT-055) already did, so an active value is never committed under an attribute that a parallel deactivation (API-CAT-028, `FOR UPDATE`) makes inactive.
- Every "unique among active records" rule is finally held by the db-script's partial unique indexes; a unique violation is translated to the RULE's catalog row (never to 500).
- Codes and internal barcode digits come from the given `NumberSeriesApi`, whose allocation is atomic; UQ_CAT_ITEM_CODE / UQ_CAT_VARIANT_CODE / UQ_CAT_BARCODE_VALUE back it.

## Consequences
- The implementer rewrites each QR with the project's query strategy but keeps the lock each QR names.
- Lock order is always Item → Variant → line rows, to avoid deadlocks.

## Traces
REQ-CAT-049 · REQ-CAT-051 · REQ-CAT-052 · REQ-CAT-053 · REQ-CAT-062 · REQ-CAT-067 · REQ-CAT-068 · REQ-CAT-069 · REQ-CAT-071 · REQ-CAT-136 · REQ-CAT-087 · REQ-CAT-091 · REQ-CAT-106 · REQ-CAT-110 · REQ-CAT-111 · REQ-CAT-021 · REQ-CAT-125
