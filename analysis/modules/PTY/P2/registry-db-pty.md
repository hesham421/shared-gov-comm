## REGISTRY — P2 — PTY v1

Source artifact : analysis/modules/PTY/P2/db-script-pty.md · Date : 2026-10-29 · Profile : commerce · Dialect : postgresql16 · Schema prefix : none · PK generation : identity · Tenancy : TENANT_ID + FK_<TABLE>_TENANT + tenant-led index on every table, every UNIQUE with TENANT_ID (ADR-PTY-035) · Migration : V1001__pty_party.sql

### Tables
| Table | ENT id | Kind | DBF range | DBF count |
|---|---|---|---|---|
| PTY_PARTY | ENT-PTY-001 | master | DBF-PTY-001 … DBF-PTY-012, DBF-PTY-068, DBF-PTY-073 | 14 |
| PTY_CUSTOMER | ENT-PTY-002 | master | DBF-PTY-013 … DBF-PTY-024, DBF-PTY-069, DBF-PTY-074, DBF-PTY-078 | 15 |
| PTY_SUPPLIER | ENT-PTY-003 | master | DBF-PTY-025 … DBF-PTY-032, DBF-PTY-070, DBF-PTY-075 | 10 |
| PTY_ADDRESS | ENT-PTY-004 | master | DBF-PTY-033 … DBF-PTY-049, DBF-PTY-071, DBF-PTY-076 | 19 |
| PTY_CREDIT_OVERRIDE_REQUEST | ENT-PTY-005 | transactional | DBF-PTY-050 … DBF-PTY-067, DBF-PTY-072, DBF-PTY-077, DBF-PTY-079 | 21 |

Intra-module FKs (composite with TENANT_ID) : FK_PTY_CUSTOMER_PTY_PARTY · FK_PTY_SUPPLIER_PTY_PARTY · FK_PTY_ADDRESS_PTY_PARTY · FK_PTY_CREDIT_OVERRIDE_REQUEST_PTY_CUSTOMER
Tenant FKs : FK_PTY_PARTY_TENANT · FK_PTY_CUSTOMER_TENANT · FK_PTY_SUPPLIER_TENANT · FK_PTY_ADDRESS_TENANT · FK_PTY_CREDIT_OVERRIDE_REQUEST_TENANT → CORE_TENANT(ID)
Given tables not re-created : SEC_USER (secUserId, decidedBy — no constraint), MDL_LOOKUP_TYPE / MDL_LOOKUP_VALUE (seeded only), CORE_NUMBER_SERIES (seeded only — PTY_PARTY, PTY_CREDIT_OVERRIDE), NOTIF_TEMPLATE (seeded only — PTY_CREDIT_OVERRIDE_REQUESTED), CU_APP_CONFIGURATION, CORE_AUDIT_EVENT, CORE_TENANT (FK target).

DBF ids : DBF-PTY-001, DBF-PTY-002, DBF-PTY-003, DBF-PTY-004, DBF-PTY-005, DBF-PTY-006, DBF-PTY-007, DBF-PTY-008, DBF-PTY-009, DBF-PTY-010, DBF-PTY-011, DBF-PTY-012, DBF-PTY-013, DBF-PTY-014, DBF-PTY-015, DBF-PTY-016, DBF-PTY-017, DBF-PTY-018, DBF-PTY-019, DBF-PTY-020, DBF-PTY-021, DBF-PTY-022, DBF-PTY-023, DBF-PTY-024, DBF-PTY-025, DBF-PTY-026, DBF-PTY-027, DBF-PTY-028, DBF-PTY-029, DBF-PTY-030, DBF-PTY-031, DBF-PTY-032, DBF-PTY-033, DBF-PTY-034, DBF-PTY-035, DBF-PTY-036, DBF-PTY-037, DBF-PTY-038, DBF-PTY-039, DBF-PTY-040, DBF-PTY-041, DBF-PTY-042, DBF-PTY-043, DBF-PTY-044, DBF-PTY-045, DBF-PTY-046, DBF-PTY-047, DBF-PTY-048, DBF-PTY-049, DBF-PTY-050, DBF-PTY-051, DBF-PTY-052, DBF-PTY-053, DBF-PTY-054, DBF-PTY-055, DBF-PTY-056, DBF-PTY-057, DBF-PTY-058, DBF-PTY-059, DBF-PTY-060, DBF-PTY-061, DBF-PTY-062, DBF-PTY-063, DBF-PTY-064, DBF-PTY-065, DBF-PTY-066, DBF-PTY-067, DBF-PTY-068, DBF-PTY-069, DBF-PTY-070, DBF-PTY-071, DBF-PTY-072, DBF-PTY-073, DBF-PTY-074, DBF-PTY-075, DBF-PTY-076, DBF-PTY-077, DBF-PTY-078, DBF-PTY-079

### XM index

```yaml name=xm-register
records: []
```

None — SRS A8 `consumes: []`; PTY reaches only the given `erp-core`, which creates no XM edge. Edges into PTY are minted by its consumers (SLS, PUR, STL, TAX, SFR, PRC) and derived by `gov.py graph`.

### Lookups
| Key | Seeded values | Count | Key owner | Table owner |
|---|---|---|---|---|
| CUSTOMER_GROUP | RETAIL, WHOLESALE | 2 (open) | PTY | given `erp-core:modules.mdl` (MDL_LOOKUP_TYPE, MDL_LOOKUP_VALUE) |
| ADDRESS_TYPE | BILLING, DELIVERY | 2 (open) | PTY | given `erp-core:modules.mdl` |
| CREDIT_OVERRUN_ACTION | WARN, BLOCK_WITH_OVERRIDE | 2 (closed) | PTY | given `erp-core:modules.mdl` |
| CREDIT_CHECK_RESULT | WITHIN_LIMIT, OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE, NOT_APPLICABLE | 5 (closed) | PTY | given `erp-core:modules.mdl` |
| CREDIT_OVERRIDE_STATUS | PENDING, APPROVED, REJECTED, CANCELLED, USED | 5 (closed) | PTY | given `erp-core:modules.mdl` |

Total seeded : 5 keys · 16 values (BLOCK 8a / 8b, TENANT_ID = 1; column list ADR-PTY-035) · number series 2 (BLOCK 8c: PTY_PARTY `P-` + 6 digits, PTY_CREDIT_OVERRIDE `CO-` + 6 digits) · notification template 1 (BLOCK 8d: PTY_CREDIT_OVERRIDE_REQUESTED) — ADR-PTY-036.

### Sequences
Last DBF : DBF-PTY-079 (next free DBF-PTY-080)
Last XM  : none (next free XM-PTY-001)
Database sequences : none (identity PKs; business numbers from the given number series)

### Decisions
ADR-PTY-018 (physical precision), ADR-PTY-019 (audit principal columns), ADR-PTY-020 (no currency column — SUPERSEDED by ADR-PTY-037), ADR-PTY-021 (tenant scoping without a tenant column — SUPERSEDED by ADR-PTY-035), ADR-PTY-022 (lookup seed against given mdl tables — column list revised by ADR-PTY-035). Applied from the gate-analysis revise : ADR-PTY-035, 036, 037, 040, 042 — all ACCEPTED, non-breaking. Raised by the P2 repair round 1 : ADR-PTY-044 (given-usage rows name only published seams — SRS A9 / contract Part 4; no DB change), ACCEPTED, non-breaking. Raised by the gate-analysis revise 2 : ADR-PTY-045 … ADR-PTY-048 (request only under BLOCK_WITH_OVERRIDE, Customer VIEW and credit values, root namespace, OwnedLookups) — no DB change, ACCEPTED, non-breaking. BLOCKED : none. Next free ADR : ADR-PTY-049.
DEFAULTs : D-P2-01 … D-P2-12.
Applied from earlier stages : ADR-PTY-012, ADR-PTY-014, ADR-PTY-015, ADR-PTY-016, ADR-PTY-017.

### Event
"P2 completed: PTY v1 — 5 tables, 67 DBF, 0 XM" · "gate-analysis revise: PTY v1 — 5 tables, 79 DBF, 0 XM"

### Cascade
None by hand — `gov.py graph` derives the edges targeting PTY and raises their resolution events (shared/XM-PROTOCOL.md §5).
