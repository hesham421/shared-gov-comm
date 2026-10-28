# ADR-CAT-067 — Every intra-module foreign key is composite with TENANT_ID

Status : ACCEPTED (non-breaking) · Stage : P2 (gate-analysis round 2, G15) · Module : CAT v2 · Date : 2026-10-28

## Context
Only two intra-module FKs carried TENANT_ID:
- `FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE`;
- `FK_CAT_ITEM_MEDIA_CAT_VARIANT`.

D-P2-12 nevertheless presented same-tenant integrity as structural. The other eleven FKs referenced the parent PK alone, so the database would accept a child of tenant A that points to a parent of tenant B. Only the given tenant filter prevented that, and the four native queries bypass it (`@TenantUnscoped`, ADR-CAT-047).

The two options were:
- (a) make every FK tenant-composite;
- (b) reword D-P2-12 to say the other FKs rely on the tenant filter.

## Decision
Option (a). It is the stronger choice: integrity holds whatever path writes the row (service, native query, data fix, import), and it follows the pattern the two existing composite FKs already use.
1. Each parent table gains a `(TENANT_ID, <PK>)` UNIQUE as the FK target: UQ_CAT_CATEGORY_PK, UQ_CAT_VAR_ATTR_PK, UQ_CAT_ITEM_PK and UQ_CAT_VARIANT_PK. CAT_VAR_ATTR_VALUE and CAT_VARIANT keep their existing same-parent UNIQUEs.
2. All 13 intra-module FKs of db-script BLOCK 5d are `FOREIGN KEY (TENANT_ID, <FK>) REFERENCES <PARENT> (TENANT_ID, <PK>)`, keeping their names. A NULL optional FK column (`PARENT_CATEGORY_FK`, `CATEGORY_FK`, `VARIANT_FK` of media) skips the check under MATCH SIMPLE, as before.
3. JPA mapping is unchanged. TENANT_ID is the child's own `@TenantId` column, filled by the given, so the composite FK is a database guarantee only.

## Consequences
- D-P2-12 now states what the DDL does. The backend plan's FIELDS tables list the new UNIQUEs on the four PK rows.
- No DBF, no column and no XM changes.

## Traces
DBF-CAT-015 · DBF-CAT-028 · DBF-CAT-053 · DBF-CAT-062 · DBF-CAT-063 · DBF-CAT-077 · DBF-CAT-088 · DBF-CAT-089 · DBF-CAT-102 · DBF-CAT-114 · DBF-CAT-128 · ADR-CAT-047 · ADR-CAT-055
