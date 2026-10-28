## REGISTRY — P3.1 — CAT v2

### ID ranges
| Kind | Range | Count | Next free |
|---|---|---|---|
| API | API-CAT-001 … API-CAT-051 | 51 | API-CAT-052 |
| QR | QR-CAT-001 … QR-CAT-071 | 71 | QR-CAT-072 |

Both streams start at 001: the current state held no backend plan for CAT. No ID of another stage is minted here.

### Endpoints
| API | verb | path | entity · operation | permission | honours |
|---|---|---|---|---|---|
| API-CAT-001 | POST | /api/v1/cat/items | ENT-CAT-001 · CREATE | PERM_CAT_ITEMS_CREATE | CON-CAT-001, CON-CAT-002, CON-CAT-006, CON-CAT-018 |
| API-CAT-002 | GET | /api/v1/cat/items/{id} | ENT-CAT-001 · VIEW | PERM_CAT_ITEMS_VIEW | CON-CAT-007 |
| API-CAT-003 | PUT | /api/v1/cat/items/{id} | ENT-CAT-001 · UPDATE | PERM_CAT_ITEMS_UPDATE | CON-CAT-001, CON-CAT-018 |
| API-CAT-004 | DELETE | /api/v1/cat/items/{id} | ENT-CAT-001 · DELETE | PERM_CAT_ITEMS_DELETE | CON-CAT-001, CON-CAT-018 |
| API-CAT-005 | PATCH | /api/v1/cat/items/{id} | ENT-CAT-001 · UPDATE | PERM_CAT_ITEMS_UPDATE | CON-CAT-001, CON-CAT-018 |
| API-CAT-006 | POST | /api/v1/cat/items/{id}/variants | ENT-CAT-002 · CREATE | PERM_CAT_ITEMS_CREATE | CON-CAT-002, CON-CAT-018 |
| API-CAT-007 | GET | /api/v1/cat/variants/{id} | ENT-CAT-002 · VIEW | PERM_CAT_ITEMS_VIEW | CON-CAT-008 |
| API-CAT-008 | PUT | /api/v1/cat/variants/{id} | ENT-CAT-002 · UPDATE | PERM_CAT_ITEMS_UPDATE | CON-CAT-002, CON-CAT-018 |
| API-CAT-009 | DELETE | /api/v1/cat/variants/{id} | ENT-CAT-002 · DELETE | PERM_CAT_ITEMS_DELETE | CON-CAT-002, CON-CAT-018 |
| API-CAT-010 | POST | /api/v1/cat/items/{id}/units | ENT-CAT-008 · CREATE | PERM_CAT_ITEMS_CREATE | CON-CAT-004, CON-CAT-018 |
| API-CAT-011 | GET | /api/v1/cat/item-units/{id} | ENT-CAT-008 · VIEW | PERM_CAT_ITEMS_VIEW | CON-CAT-009 |
| API-CAT-012 | PUT | /api/v1/cat/item-units/{id} | ENT-CAT-008 · UPDATE | PERM_CAT_ITEMS_UPDATE | CON-CAT-004, CON-CAT-018 |
| API-CAT-013 | DELETE | /api/v1/cat/item-units/{id} | ENT-CAT-008 · DELETE | PERM_CAT_ITEMS_DELETE | CON-CAT-004, CON-CAT-018 |
| API-CAT-014 | POST | /api/v1/cat/variants/{id}/barcodes | ENT-CAT-009 · CREATE | PERM_CAT_ITEMS_CREATE | CON-CAT-005, CON-CAT-018 |
| API-CAT-015 | POST | /api/v1/cat/variants/{id}/barcodes/internal | ENT-CAT-009 · CREATE | PERM_CAT_ITEMS_CREATE | CON-CAT-005, CON-CAT-018 |
| API-CAT-016 | DELETE | /api/v1/cat/barcodes/{id} | ENT-CAT-009 · DELETE | PERM_CAT_ITEMS_DELETE | CON-CAT-005, CON-CAT-018 |
| API-CAT-017 | PUT | /api/v1/cat/items/{id}/bundle-components | ENT-CAT-007 · CREATE, UPDATE, DELETE | PERM_CAT_ITEMS_UPDATE | CON-CAT-006, CON-CAT-018 |
| API-CAT-018 | POST | /api/v1/cat/items/{id}/media | ENT-CAT-010 · CREATE | PERM_CAT_ITEMS_CREATE | CON-CAT-018 |
| API-CAT-019 | PUT | /api/v1/cat/media/{id} | ENT-CAT-010 · UPDATE | PERM_CAT_ITEMS_UPDATE | CON-CAT-018 |
| API-CAT-020 | DELETE | /api/v1/cat/media/{id} | ENT-CAT-010 · DELETE | PERM_CAT_ITEMS_DELETE | CON-CAT-018 |
| API-CAT-021 | POST | /api/v1/cat/categories | ENT-CAT-006 · CREATE | PERM_CAT_CATEGORIES_CREATE | CON-CAT-003 |
| API-CAT-022 | GET | /api/v1/cat/categories/{id} | ENT-CAT-006 · VIEW | PERM_CAT_CATEGORIES_VIEW | CON-CAT-011 |
| API-CAT-023 | PUT | /api/v1/cat/categories/{id} | ENT-CAT-006 · UPDATE | PERM_CAT_CATEGORIES_UPDATE | CON-CAT-003 |
| API-CAT-024 | DELETE | /api/v1/cat/categories/{id} | ENT-CAT-006 · DELETE | PERM_CAT_CATEGORIES_DELETE | CON-CAT-003 |
| API-CAT-025 | POST | /api/v1/cat/variant-attributes | ENT-CAT-003 · CREATE | PERM_CAT_VARIANT_ATTRIBUTES_CREATE | — |
| API-CAT-026 | GET | /api/v1/cat/variant-attributes/{id} | ENT-CAT-003 · VIEW | PERM_CAT_VARIANT_ATTRIBUTES_VIEW | — |
| API-CAT-027 | PUT | /api/v1/cat/variant-attributes/{id} | ENT-CAT-003 · UPDATE | PERM_CAT_VARIANT_ATTRIBUTES_UPDATE | — |
| API-CAT-028 | DELETE | /api/v1/cat/variant-attributes/{id} | ENT-CAT-003 · DELETE | PERM_CAT_VARIANT_ATTRIBUTES_DELETE | — |
| API-CAT-029 | POST | /api/v1/cat/variant-attributes/{id}/values | ENT-CAT-004 · CREATE | PERM_CAT_VARIANT_ATTRIBUTES_CREATE | — |
| API-CAT-030 | PUT | /api/v1/cat/variant-attribute-values/{id} | ENT-CAT-004 · UPDATE | PERM_CAT_VARIANT_ATTRIBUTES_UPDATE | — |
| API-CAT-031 | DELETE | /api/v1/cat/variant-attribute-values/{id} | ENT-CAT-004 · DELETE | PERM_CAT_VARIANT_ATTRIBUTES_DELETE | — |
| API-CAT-032 | POST | /api/v1/cat/scale-label-layouts | ENT-CAT-011 · CREATE | PERM_CAT_SETTINGS_CREATE | — |
| API-CAT-033 | PUT | /api/v1/cat/scale-label-layouts/{id} | ENT-CAT-011 · UPDATE | PERM_CAT_SETTINGS_UPDATE | — |
| API-CAT-034 | DELETE | /api/v1/cat/scale-label-layouts/{id} | ENT-CAT-011 · DELETE | PERM_CAT_SETTINGS_DELETE | — |
| API-CAT-035 | POST | /api/v1/cat/unit-einvoice-codes | ENT-CAT-012 · CREATE | PERM_CAT_SETTINGS_CREATE | — |
| API-CAT-036 | PUT | /api/v1/cat/unit-einvoice-codes/{id} | ENT-CAT-012 · UPDATE | PERM_CAT_SETTINGS_UPDATE | — |
| API-CAT-037 | DELETE | /api/v1/cat/unit-einvoice-codes/{id} | ENT-CAT-012 · DELETE | PERM_CAT_SETTINGS_DELETE | — |
| API-CAT-038 | GET | /api/v1/cat/items | ENT-CAT-001 · VIEW | PERM_CAT_ITEMS_VIEW | — |
| API-CAT-039 | GET | /api/v1/cat/categories | ENT-CAT-006 · VIEW | PERM_CAT_CATEGORIES_VIEW | CON-CAT-012 |
| API-CAT-040 | GET | /api/v1/cat/categories/{id}/items | ENT-CAT-006 · VIEW | PERM_CAT_CATEGORIES_VIEW | CON-CAT-013 |
| API-CAT-041 | GET | /api/v1/cat/variant-attributes | ENT-CAT-003 · VIEW | PERM_CAT_VARIANT_ATTRIBUTES_VIEW | — |
| API-CAT-042 | GET | /api/v1/cat/variants | ENT-CAT-002 · VIEW | PERM_CAT_BARCODE_LABELS_VIEW | — |
| API-CAT-043 | GET | /api/v1/cat/barcodes/lookup | ENT-CAT-009 · VIEW | PERM_CAT_ITEMS_VIEW | CON-CAT-010 |
| API-CAT-044 | GET | /api/v1/cat/variants/{id}/media | ENT-CAT-010 · VIEW | PERM_CAT_ITEMS_VIEW | CON-CAT-015 |
| API-CAT-045 | GET | /api/v1/cat/settings | ENT-CAT-011 · VIEW | PERM_CAT_SETTINGS_VIEW | — |
| API-CAT-046 | GET | /api/v1/cat/storefront-items | ENT-CAT-001 · VIEW | PERM_CAT_ITEMS_VIEW | CON-CAT-014 |
| API-CAT-047 | GET | /api/v1/cat/catalogue-search | ENT-CAT-001 · VIEW | PERM_CAT_ITEMS_VIEW (absent → empty result) | CON-CAT-016 |
| API-CAT-048 | GET | /api/v1/cat/item-imports/sheet | ENT-CAT-001 · VIEW | PERM_CAT_ITEM_IMPORT_VIEW | — |
| API-CAT-049 | POST | /api/v1/cat/item-imports | ENT-CAT-001 · CREATE | PERM_CAT_ITEM_IMPORT_CREATE | CON-CAT-001, CON-CAT-002, CON-CAT-005, CON-CAT-018 |
| API-CAT-050 | POST | /api/v1/cat/barcode-labels | ENT-CAT-002 · CREATE | PERM_CAT_BARCODE_LABELS_CREATE | — |
| API-CAT-051 | GET | /api/v1/cat/capability-checks/turn-off | ENT-CAT-001 · VIEW | PERM_CAT_SETTINGS_VIEW | CON-CAT-017 |

Contract coverage: CON-CAT-001 … CON-CAT-018 — 18 of 18 honoured (entity items also in the PHASE DATA-DOM entity blocks; the in-process interface `com.erp.cat.crossmodule.CatalogueReadApi` carries the eleven operation items).

### Query catalogue
QR-CAT-001 … 010 → ENT-CAT-001 · 011 … 017 → ENT-CAT-002 · 018 … 021 → ENT-CAT-005 · 022 … 026 → ENT-CAT-003 · 027 … 031 → ENT-CAT-004 · 032 … 038 → ENT-CAT-006 · 039 … 042 → ENT-CAT-007 · 043 … 046, 071 → ENT-CAT-008 · 047 … 053 → ENT-CAT-009 · 054 … 058 → ENT-CAT-010 · 059 … 064 → ENT-CAT-011 · 065 … 070 → ENT-CAT-012. Every QR is reached by ≥ 1 API and every API with a database operation cites its QRs (API-CAT-048 has none). Native / join / lock deviations: ADR-CAT-047.

### Entities / tables bound
12 of 12 entities bound to their db-script tables (CAT_ITEM, CAT_VARIANT, CAT_VAR_ATTR, CAT_VAR_ATTR_VALUE, CAT_VAR_ATTR_ASSIGN, CAT_CATEGORY, CAT_BUNDLE_COMP, CAT_ITEM_UNIT, CAT_BARCODE, CAT_ITEM_MEDIA, CAT_SCALE_LABEL_LAYOUT, CAT_UNIT_EINVOICE_CODE); 161 of 161 DBF in the DB Alignment Manifest; 0 PENDING DB. Lookups reused: the 10 CAT-owned keys of SRS A6 (CAT_ITEM_KIND, CAT_PUBLISH_STATE, CAT_BRAND, CAT_UOM, CAT_TAX_CLASS, CAT_EINVOICE_ITEM_CODE_TYPE, CAT_EINVOICE_UNIT, CAT_BARCODE_TYPE, CAT_SCALE_VALUE_TYPE, CAT_CHANGE_KIND); new keys: none.

### Integration
XM: none — the register is `records: []`; no edge minted; `INT-C` (the last phase) carries no block; no integration package.

### Catalog
58 codes: 52 RULE rows (one per RULE-CAT-001 … 052; 6 of them are 2xx success notices — RULE-CAT-010, 011, 012, 039, 045, 048) + 6 PLATFORM-STD rows (ADR-CAT-044; the 413 and 415 rows left with the multipart surface — ADR-CAT-056). Rules without a message: none. No RULE is DEFERRED.

### API document
api-spec-cat.yaml — OpenAPI 3.1.0, validated · operations 51 = API blocks 51 · paths 33 · catalog rows answered 58 = catalog rows 58 · no multipart request body (ADR-CAT-056) · bearer JWT security on every operation · paged operations (API-CAT-038, 040, 042, 046) carry page / size.

### Security
6 pages (CAT_ITEMS, CAT_CATEGORIES, CAT_VARIANT_ATTRIBUTES, CAT_ITEM_IMPORT, CAT_BARCODE_LABELS, CAT_SETTINGS) · 20 permission names `PERM_<PAGE_CODE>_<ACTION>` derived by the given `sec` module · preset-role grants per SRS B4 (ADR-CAT-046).

### ALIGN
Verdict as stamped by the orchestrator from `gov.py analyze` (self-check block placeholder: findings 0, clean true). Findings fixed during authoring: four RULE effects (041 on deactivation, 039 on upload / removal, 001 / 032 inside import rows) moved out of `Validations` because they raise no error at those endpoints.

### ADRs
| ADR | subject | status |
|---|---|---|
| decisions/CAT/ADR-CAT-044 | platform-standard error rows | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-045 | endpoint surface beyond SRS B5; in-process CatalogueReadApi | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-046 | security bootstrap, preset-role grants, tenant copies of seeds | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-047 | repository deviations and concurrency guards | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-048 | endpoint shapes, derived properties, success-path messages (§4 amended by ADR-CAT-056, 057) | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-055 | given CORE_TENANT FK is a permitted given reference (db-script; supersedes ADR-CAT-054) | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-056 | files through the given FILE endpoints; API-CAT-018 / 049 take fileDocumentRef; retention | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-057 | import sheet = report CAT_ITEM_IMPORT_SHEET through PrintApi | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-058 | tenant provisioning of CAT seeds | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-059 | ACTIVE-state invariants on reactivation; channel reads see only active Items | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-060 | Item unit used by active barcodes stays active | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-061 | CatalogueReadApi → unsecured read service; facades carry @PreAuthorize | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-062 | Arabic normalisation, trigram indexes, escaped LIKE | ACCEPTED (non-breaking) |

Next free ADR after the gate-analysis revise: ADR-CAT-063. None BLOCKED.

### Traceability
REQ covered by ≥ 1 API: 134 / 134 · orphan REQ: none · every API traces ≥ 1 REQ and ≥ 1 DBF · every PHASE / SUB / atom carries traces=.

### Event
"P3.1 completed: CAT v2 — API 51 · QR 71 · error codes 58 · XM 0 · ADR 5 (044–048) + gate-analysis revise 055–062, none BLOCKED"
