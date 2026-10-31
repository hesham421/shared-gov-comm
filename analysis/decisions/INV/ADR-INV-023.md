# ADR-INV-023 — Cross-module foreign keys reference the target's primary key column only
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : XM-INV-001 … XM-INV-011, CON-CAT-001, CON-CAT-002, db-script D-P2-14

## Context
The db-script declares the eleven HARD-FK columns column-only and leaves the constraint to the INT-C blocks;
its D-P2-14 expected the constraints to be composite with TENANT_ID. The CAT contract promises a BIGINT
identifier for Item and Variant but, by design, no table, column list or tenant-scoped unique key. A composite
FK needs a UNIQUE (TENANT_ID, <pk>) on the target that nothing promises.

## Decision
Each ddl_patch is a single-column foreign key from this module's column to the target primary key:
`REFERENCES CAT_VARIANT (VARIANT_PK)` for the ten Variant columns and `REFERENCES CAT_ITEM (ITEM_PK)` for
INV_ITEM_STOCK_SETTINGS.ITEM_FK. The table and key names are derived from the shared naming rule
(`<MOD>_` + entity in UPPER_SNAKE_CASE, PK `<entity>_PK`). Before running a patch the executor checks both
names against the delivered target migration and runs the orphan pre-check in the block's config line.
Tenant correctness of the reference is ensured by reading the Variant / Item through the tenant-scoped
in-process reads (XM-INV-014, XM-INV-015) before every write.

## Consequences
- Every patch can be applied to any delivered target without a target change.
- If the target later publishes a tenant-scoped unique key, a later version may replace the patches with
  composite FKs (a new ddl_patch per edge).

## Status
ACCEPTED — non-breaking.
