## REGISTRY — P2 — CAT v2

### Tables
| Table | ENT id | Kind | DBF range |
|---|---|---|---|
| CAT_ITEM | ENT-CAT-001 | master | DBF-CAT-001 … DBF-CAT-021 |
| CAT_VARIANT | ENT-CAT-002 | master | DBF-CAT-022 … DBF-CAT-034 |
| CAT_VAR_ATTR | ENT-CAT-003 | lookup | DBF-CAT-035 … DBF-CAT-046 |
| CAT_VAR_ATTR_VALUE | ENT-CAT-004 | lookup | DBF-CAT-047 … DBF-CAT-059 |
| CAT_VAR_ATTR_ASSIGN | ENT-CAT-005 | master (dependent) | DBF-CAT-060 … DBF-CAT-070 |
| CAT_CATEGORY | ENT-CAT-006 | master | DBF-CAT-071 … DBF-CAT-083 |
| CAT_BUNDLE_COMP | ENT-CAT-007 | master (dependent) | DBF-CAT-084 … DBF-CAT-095 |
| CAT_ITEM_UNIT | ENT-CAT-008 | master (dependent) | DBF-CAT-096 … DBF-CAT-108 |
| CAT_BARCODE | ENT-CAT-009 | master (dependent) | DBF-CAT-109 … DBF-CAT-120 |
| CAT_ITEM_MEDIA | ENT-CAT-010 | master (dependent) | DBF-CAT-121 … DBF-CAT-135 |
| CAT_SCALE_LABEL_LAYOUT | ENT-CAT-011 | config | DBF-CAT-136 … DBF-CAT-149 |
| CAT_UNIT_EINVOICE_CODE | ENT-CAT-012 | config | DBF-CAT-150 … DBF-CAT-161 |

Tenant: every table carries a column-only TENANT_ID (given CORE_TENANT, no FK constraint, ADR-CAT-054) and an index led by TENANT_ID.

Given tables read, never created: MDL_LOOKUP_TYPE and MDL_LOOKUP_VALUE (`erp-core:modules.mdl`, seeded only), CORE_NUMBER_SERIES (`erp-core:modules.sequence`, seeded only), FILE_DOCUMENT (`erp-core:modules.file`, soft read through `CAT_ITEM_MEDIA.FILE_DOCUMENT_REF`) and CORE_TENANT (`erp-core:modules.tenant`, through `TENANT_ID`).

### XM index
```yaml name=xm-register
records: []
```
None. SRS A8 `consumes: []`. CAT is first in the fixed module order and has no outbound cross-module edge.

### Lookups
| Key | Seeded values | Owner of the key | Table owner |
|---|---|---|---|
| CAT_ITEM_KIND | 2 | CAT | erp-core mdl (MDL_LOOKUP_TYPE / MDL_LOOKUP_VALUE) |
| CAT_PUBLISH_STATE | 2 | CAT | erp-core mdl |
| CAT_BRAND | 0 (open, tenant-maintained) | CAT | erp-core mdl |
| CAT_UOM | 8 | CAT | erp-core mdl |
| CAT_TAX_CLASS | 3 | CAT | erp-core mdl |
| CAT_EINVOICE_ITEM_CODE_TYPE | 2 | CAT | erp-core mdl |
| CAT_EINVOICE_UNIT | 8 | CAT | erp-core mdl |
| CAT_BARCODE_TYPE | 3 | CAT | erp-core mdl |
| CAT_SCALE_VALUE_TYPE | 2 | CAT | erp-core mdl |
| CAT_CHANGE_KIND | 4 | CAT | erp-core mdl |

Total: 10 keys, 34 seeded values.

Other seeds:
- Number series CAT_ITEM, CAT_VARIANT and CAT_INTERNAL_BARCODE go into CORE_NUMBER_SERIES (owner: erp-core sequence).
- 8 Unit e-invoice codes go into CAT_UNIT_EINVOICE_CODE (owner: CAT).

### Sequences
- Last DBF: DBF-CAT-161 (next free sequence 162)
- Last XM: none (next free sequence 001)
- No database sequence is created (every PK uses the identity clause)

### Decisions
DEFAULTs D-P2-01 … D-P2-18 (db-script §5). No ADR, none BLOCKED. The next free ADR sequence is unchanged (044).

### Event
"P2 completed: CAT v2 — 12 tables, 161 DBF, 0 XM"

### Cascade
None by hand. `gov.py graph` derives the edges that target CAT and raises their resolution events (shared/XM-PROTOCOL.md §5). No consumer edge exists yet.

