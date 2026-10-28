# ADR-CAT-054 — Tenant reference is column-only

| Item | Value |
|---|---|
| Module | CAT |
| Version | v2 |
| Stage | P2 — Database (repair round 1) |
| Status | ACCEPTED (non-breaking) |
| Date | 2026-10-28 |
| Applies to | `analysis/modules/CAT/v2/P2/db-script-cat.md`, `registry-db-cat.md` |

## Context
`gov.py analyze` refused C6.15 (db-column-only) on 12 `FK_<TABLE>_TENANT` constraints referencing `CORE_TENANT`, a table of the given erp-core that this script does not create. A cross-module reference is column-only.

## Decision
`TENANT_ID` stays on every table, with its tenant-led index and tenant-composite UNIQUEs, but the 12 FK constraints are removed. Tenant validity is enforced by the given tenancy layer. No XM is minted: the tenant is a given, not a business-module edge (SRS A8 `consumes: []`).

## Consequences
Supersedes the FK part of D-P2-03. DBF ids, columns and counts are unchanged. Next free ADR is ADR-CAT-055.
