# BACKEND EXECUTION PLAN — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module : CAT   Version : v2   Profile : commerce   Stage : P3.1   Track : backend · plan exec
Inputs : srs (P1 — REQ 139 · AC 164 · ENT 12 · RULE 53 · SCR-REQ 6), db-script (P2 — 12 tables · DBF 161 · XM 0), registry-srs, registry-db, contract (P1.5 — CON 18)
Governance : FULL (db-script present) · dialect postgresql16 · framework Spring Boot (Java 25) on erp-core 1.6.0
Counts : API 55 · QR 75 · error codes 60 · XM 0 · ADR raised 5 at P3.1 (ADR-CAT-044 … ADR-CAT-048), ADR-CAT-055 … ADR-CAT-062 at the gate-analysis revise, ADR-CAT-063 … ADR-CAT-067 at the second, ADR-CAT-068 … ADR-CAT-071 at the third and ADR-CAT-072 at the sixth gate-analysis revise, all ACCEPTED non-breaking · CORE-GAP 3 (in-process authorization, tenant lookup types, number-series block allocation)
Delta  : CS-CAT-001 (ADDITIVE). The current state holds no backend plan for CAT, so v2 writes it in full; the API and QR streams start at 001.
Status : DRAFT — P3.1 pass 1 · no question raised · nothing BLOCKED
══════════════════════════════════════════════════════════════════

Reading order for the implementer: index → DB Alignment Manifest → ADRs → phases in profile order (CORE → DATA-DOM → SVC-API → DOC → SEC-BE → ALIGN-BE → INT-C) → Query Reference Catalog → Error Catalog.

## EXECUTION PLAN INDEX — CAT v2 — backend-execution-plan-cat.md

Profile: commerce · dialect: postgresql16 · framework: Spring Boot (Java 25), erp-core 1.6.0 given
Open ADRs: 0 BLOCKED — decisions/CAT/ (this stage raised ADR-CAT-044 … ADR-CAT-048; the gate-analysis revise added ADR-CAT-055 … ADR-CAT-062; the second revise added ADR-CAT-063 … ADR-CAT-067; the third revise added ADR-CAT-068 … ADR-CAT-071; the sixth revise added ADR-CAT-072; all ACCEPTED). Open core gaps: decisions/CAT/CORE-GAP-in-process-authorization.md (ADR-CAT-061 is conditional on its answer — ADR-CAT-066), decisions/CAT/CORE-GAP-tenant-lookup-types.md (ADR-CAT-070), decisions/CAT/CORE-GAP-number-series-block-allocation.md (ADR-CAT-071)

### Entity registry
| ENT | name (en / ar) | kind | table | business code | operations |
|---|---|---|---|---|---|
| ENT-CAT-001 | Item / صنف | master | CAT_ITEM | code DBF-CAT-002 · series CAT_ITEM · ITM-000001 · UQ_CAT_ITEM_CODE | VIEW, CREATE, UPDATE, DELETE |
| ENT-CAT-002 | Variant / متغيّر الصنف | master | CAT_VARIANT | code DBF-CAT-023 · series CAT_VARIANT · VAR-000001 · UQ_CAT_VARIANT_CODE | VIEW, CREATE, UPDATE, DELETE |
| ENT-CAT-003 | Variant attribute / خاصية المتغيّر | lookup | CAT_VAR_ATTR | — (owner-entered code) | VIEW, CREATE, UPDATE, DELETE |
| ENT-CAT-004 | Variant attribute value / قيمة خاصية المتغيّر | lookup | CAT_VAR_ATTR_VALUE | — (owner-entered code) | VIEW, CREATE, UPDATE, DELETE |
| ENT-CAT-005 | Variant attribute assignment / إسناد قيمة خاصية للمتغيّر | master (dependent) | CAT_VAR_ATTR_ASSIGN | — | VIEW, CREATE, DELETE |
| ENT-CAT-006 | Category / فئة | master | CAT_CATEGORY | — (owner-entered code) | VIEW, CREATE, UPDATE, DELETE |
| ENT-CAT-007 | Bundle component / مكوّن الحزمة | master (dependent) | CAT_BUNDLE_COMP | — | VIEW, CREATE, UPDATE, DELETE |
| ENT-CAT-008 | Item unit / وحدة الصنف | master (dependent) | CAT_ITEM_UNIT | — | VIEW, CREATE, UPDATE, DELETE |
| ENT-CAT-009 | Barcode / باركود | master (dependent) | CAT_BARCODE | — (internal digits: series CAT_INTERNAL_BARCODE) | VIEW, CREATE, DELETE |
| ENT-CAT-010 | Item media / وسائط الصنف | master (dependent) | CAT_ITEM_MEDIA | — | VIEW, CREATE, UPDATE, DELETE |
| ENT-CAT-011 | Scale label layout / إعداد ملصق الميزان | config | CAT_SCALE_LABEL_LAYOUT | — | VIEW, CREATE, UPDATE, DELETE |
| ENT-CAT-012 | Unit e-invoice code / رمز الوحدة في الفاتورة الإلكترونية | config | CAT_UNIT_EINVOICE_CODE | — | VIEW, CREATE, UPDATE, DELETE |

### Field registry
The FIELDS table of each entity block (PHASE DATA-DOM) is the field registry: DBF │ property │ column │ type │ null │ read-only │ constraint │ labels. The DB Alignment Manifest below binds every DBF to its plan property and type.

### API registry
| API | operation | verb | path | REQ traced |
|---|---|---|---|---|
| API-CAT-001 | create Item | POST | /api/v1/cat/items | 007 … 035, 056, 059, 060, 072 … 075, 117, 123, 131, 134 |
| API-CAT-002 | read Item | GET | /api/v1/cat/items/{id} | 001, 002, 010, 029, 058, 119, 132 |
| API-CAT-003 | update Item | PUT | /api/v1/cat/items/{id} | 004, 007, 009, 030, 031, 034, 063, 072, 073, 075, 076, 082, 117, 123, 131, 132, 133, 135 |
| API-CAT-004 | deactivate Item | DELETE | /api/v1/cat/items/{id} | 005, 062, 131 |
| API-CAT-005 | reactivate / publish / unpublish Item | PATCH | /api/v1/cat/items/{id} | 006, 113, 114, 116, 130 |
| API-CAT-006 | generate Variants | POST | /api/v1/cat/items/{id}/variants | 011 … 019, 025, 033, 035, 049, 051, 052, 061, 129, 135 |
| API-CAT-007 | read Variant | GET | /api/v1/cat/variants/{id} | 001, 029, 050, 119, 122 |
| API-CAT-008 | update Variant e-invoice override | PUT | /api/v1/cat/variants/{id} | 034, 118, 123, 135 |
| API-CAT-009 | deactivate Variant (with its assignments and barcodes) | DELETE | /api/v1/cat/variants/{id} | 025, 050, 053, 062 |
| API-CAT-010 | add Item unit | POST | /api/v1/cat/items/{id}/units | 074, 075, 077, 080, 135 |
| API-CAT-011 | read Item unit | GET | /api/v1/cat/item-units/{id} | 079, 122 |
| API-CAT-012 | update Item unit | PUT | /api/v1/cat/item-units/{id} | 078, 081, 135 |
| API-CAT-013 | deactivate Item unit | DELETE | /api/v1/cat/item-units/{id} | 079, 128 |
| API-CAT-014 | add barcode (GTIN / scale) | POST | /api/v1/cat/variants/{id}/barcodes | 074, 075, 083, 087, 088, 089, 091, 096, 099, 135 |
| API-CAT-015 | generate internal barcode | POST | /api/v1/cat/variants/{id}/barcodes/internal | 035, 084, 089, 091, 135 |
| API-CAT-016 | deactivate barcode | DELETE | /api/v1/cat/barcodes/{id} | 085 |
| API-CAT-017 | save Bundle components | PUT | /api/v1/cat/items/{id}/bundle-components | 013, 015, 016, 020, 056, 057, 059, 060, 135 |
| API-CAT-018 | upload image | POST | /api/v1/cat/items/{id}/media | 105, 108, 110, 112, 135 |
| API-CAT-019 | update image | PUT | /api/v1/cat/media/{id} | 106, 112, 135 |
| API-CAT-020 | remove image | DELETE | /api/v1/cat/media/{id} | 107, 111 |
| API-CAT-021 | create Category | POST | /api/v1/cat/categories | 064, 069, 070 |
| API-CAT-022 | read Category | GET | /api/v1/cat/categories/{id} | 066, 067 |
| API-CAT-023 | update Category | PUT | /api/v1/cat/categories/{id} | 065, 069, 070 |
| API-CAT-024 | deactivate Category | DELETE | /api/v1/cat/categories/{id} | 066, 071 |
| API-CAT-025 | create Variant attribute | POST | /api/v1/cat/variant-attributes | 012, 014, 016, 042, 055 |
| API-CAT-026 | read Variant attribute | GET | /api/v1/cat/variant-attributes/{id} | 048 |
| API-CAT-027 | update Variant attribute | PUT | /api/v1/cat/variant-attributes/{id} | 043, 055 |
| API-CAT-028 | deactivate Variant attribute | DELETE | /api/v1/cat/variant-attributes/{id} | 044, 054 |
| API-CAT-029 | create Variant attribute value | POST | /api/v1/cat/variant-attributes/{id}/values | 016, 045, 055 |
| API-CAT-030 | update Variant attribute value | PUT | /api/v1/cat/variant-attribute-values/{id} | 046, 055 |
| API-CAT-031 | deactivate Variant attribute value | DELETE | /api/v1/cat/variant-attribute-values/{id} | 047, 054 |
| API-CAT-032 | create scale label layout | POST | /api/v1/cat/scale-label-layouts | 075, 091, 092, 098 |
| API-CAT-033 | update scale label layout | PUT | /api/v1/cat/scale-label-layouts/{id} | 093, 098 |
| API-CAT-034 | deactivate scale label layout | DELETE | /api/v1/cat/scale-label-layouts/{id} | 094 |
| API-CAT-035 | create Unit e-invoice code | POST | /api/v1/cat/unit-einvoice-codes | 075, 120, 124 |
| API-CAT-036 | update Unit e-invoice code | PUT | /api/v1/cat/unit-einvoice-codes/{id} | 075, 121 |
| API-CAT-037 | deactivate Unit e-invoice code | DELETE | /api/v1/cat/unit-einvoice-codes/{id} | 122 |
| API-CAT-038 | search Items | GET | /api/v1/cat/items | 003, 014, 015 |
| API-CAT-039 | list Category tree | GET | /api/v1/cat/categories | 067 |
| API-CAT-040 | Items of a Category | GET | /api/v1/cat/categories/{id}/items | 068 |
| API-CAT-041 | list Variant attributes with values | GET | /api/v1/cat/variant-attributes | 014, 048 |
| API-CAT-042 | search Variants | GET | /api/v1/cat/variants | 101 |
| API-CAT-043 | barcode and scale label lookup | GET | /api/v1/cat/barcodes/lookup | 086, 090, 094, 097, 100 |
| API-CAT-044 | images of a Variant | GET | /api/v1/cat/variants/{id}/media | 109 |
| API-CAT-045 | read catalogue settings | GET | /api/v1/cat/settings | 095, 134 |
| API-CAT-046 | storefront catalogue read | GET | /api/v1/cat/storefront-items | 115 |
| API-CAT-047 | catalogue search | GET | /api/v1/cat/catalogue-search | 125, 126, 127 |
| API-CAT-048 | download import sheet | GET | /api/v1/cat/item-imports/sheet | 036 |
| API-CAT-049 | import items | POST | /api/v1/cat/item-imports | 037 … 041, 139 |
| API-CAT-050 | print barcode labels | POST | /api/v1/cat/barcode-labels | 102, 103, 104 |
| API-CAT-051 | capability turn-off check | GET | /api/v1/cat/capability-checks/turn-off | 021, 022, 023 |
| API-CAT-052 | active values of a CAT lookup key | GET | /api/v1/cat/lookups/{key} | 073, 074, 076 |
| API-CAT-053 | reactivate Category | PATCH | /api/v1/cat/categories/{id} | 136 |
| API-CAT-054 | reactivate Variant attribute | PATCH | /api/v1/cat/variant-attributes/{id} | 137 |
| API-CAT-055 | reactivate Variant attribute value | PATCH | /api/v1/cat/variant-attribute-values/{id} | 138 |

### Rule registry
Every RULE has a message in ar and en in the SRS; the Error Catalog (§ Error Catalog) carries it character for character. ✓ = both languages present.

| RULE | name | scope | owner ENT (full text) | ar/en |
|---|---|---|---|---|
| RULE-CAT-001 | Arabic name required | create · update · import row | ENT-CAT-001 | ✓ |
| RULE-CAT-002 | English name length | create · update · import row | ENT-CAT-001 | ✓ |
| RULE-CAT-003 | One active Item per name | create · update · reactivation · import row | ENT-CAT-001 | ✓ |
| RULE-CAT-004 | Codes are system-assigned | create · update | ENT-CAT-001 | ✓ |
| RULE-CAT-005 | No record without a code | create | ENT-CAT-001 | ✓ |
| RULE-CAT-006 | Lookup-backed fields hold active values | create · update · import row | ENT-CAT-001 | ✓ |
| RULE-CAT-007 | Base unit fixed after first save | update | ENT-CAT-001 | ✓ |
| RULE-CAT-008 | Flag-bound operation refused while its flag is off | BUNDLE Item create · Variant generation · Bundle component save · Variant attribute / value create and update | ENT-CAT-001 | ✓ |
| RULE-CAT-009 | Unknown flag value refuses the operation | the operations RULE-CAT-008 binds to a flag | ENT-CAT-001 | ✓ |
| RULE-CAT-010 | Variants cannot be turned off while in use | turn-off check | ENT-CAT-005 | ✓ |
| RULE-CAT-011 | Bundles cannot be turned off while in use | turn-off check | ENT-CAT-001 | ✓ |
| RULE-CAT-012 | Existing Variants keep their identity | Variant generation | ENT-CAT-002 | ✓ |
| RULE-CAT-013 | One value per attribute and unique combinations | Variant generation | ENT-CAT-005 | ✓ |
| RULE-CAT-014 | Variant limits per Item | Variant generation | ENT-CAT-002 | ✓ |
| RULE-CAT-015 | Last active Variant stays active | Variant deactivation | ENT-CAT-002 | ✓ |
| RULE-CAT-016 | Attribute and value codes and names unique | create · update · reactivation | ENT-CAT-003 | ✓ |
| RULE-CAT-017 | Attribute or value in use stays active | deactivation | ENT-CAT-003 | ✓ |
| RULE-CAT-018 | Bundle has components with quantities | Bundle save | ENT-CAT-007 | ✓ |
| RULE-CAT-019 | Valid Bundle components | Bundle save · line change · Bundle reactivation | ENT-CAT-007 | ✓ |
| RULE-CAT-020 | A Bundle has one Variant | Variant generation | ENT-CAT-002 | ✓ |
| RULE-CAT-021 | Component of an active Bundle stays active | Variant / Item deactivation | ENT-CAT-007 | ✓ |
| RULE-CAT-022 | Item kind fixed after first save | update | ENT-CAT-001 | ✓ |
| RULE-CAT-023 | Category hierarchy | create · update · reactivation | ENT-CAT-006 | ✓ |
| RULE-CAT-024 | Category code and sibling name unique | create · update · reactivation | ENT-CAT-006 | ✓ |
| RULE-CAT-025 | Category in use stays active | deactivation | ENT-CAT-006 | ✓ |
| RULE-CAT-026 | Item Category active | create · update · import row · reactivation | ENT-CAT-001 | ✓ |
| RULE-CAT-027 | Valid Item unit | create | ENT-CAT-008 | ✓ |
| RULE-CAT-028 | Conversion factor fixed after saving | update | ENT-CAT-008 | ✓ |
| RULE-CAT-029 | Barcode unique among active barcodes | create · import row | ENT-CAT-009 | ✓ |
| RULE-CAT-030 | GTIN format | create · import row | ENT-CAT-009 | ✓ |
| RULE-CAT-031 | Barcode unit belongs to the Item | create | ENT-CAT-009 | ✓ |
| RULE-CAT-032 | Barcodes avoid scale prefixes | barcode create · layout create | ENT-CAT-009 | ✓ |
| RULE-CAT-033 | Scale code length | create (SCALE) | ENT-CAT-009 | ✓ |
| RULE-CAT-034 | Valid scale label layout | create · update | ENT-CAT-011 | ✓ |
| RULE-CAT-035 | Scale label decoding | barcode lookup | ENT-CAT-009 | ✓ |
| RULE-CAT-036 | Label copy limits | label request | ENT-CAT-002 | ✓ |
| RULE-CAT-037 | Labelled Variant carries a barcode | label request | ENT-CAT-009 | ✓ |
| RULE-CAT-038 | Image file and count | upload | ENT-CAT-010 | ✓ |
| RULE-CAT-039 | Exactly one main image | upload · update · removal | ENT-CAT-010 | ✓ |
| RULE-CAT-040 | Image Variant belongs to the Item | upload · update | ENT-CAT-010 | ✓ |
| RULE-CAT-041 | Publish state follows the Item | publish · Item deactivation | ENT-CAT-001 | ✓ |
| RULE-CAT-042 | E-invoice code pair and format | create · update · import row | ENT-CAT-001 | ✓ |
| RULE-CAT-043 | One e-invoice unit per unit of measure | create | ENT-CAT-012 | ✓ |
| RULE-CAT-044 | Import file acceptance | import submission | ENT-CAT-001 | ✓ |
| RULE-CAT-045 | Import row validation | import row | ENT-CAT-001 | ✓ |
| RULE-CAT-046 | Search term minimum | catalogue search | ENT-CAT-001 | ✓ |
| RULE-CAT-047 | Barcode not found | barcode lookup | ENT-CAT-009 | ✓ |
| RULE-CAT-048 | Deactivation keeps records for consumers (ARCH-5) | deactivation | ENT-CAT-001 | ✓ |
| RULE-CAT-049 | Change events only after commit | commit / rollback | ENT-CAT-001 | ✓ |
| RULE-CAT-050 | A reactivated Item has an active Variant | reactivation | ENT-CAT-001 | ✓ |
| RULE-CAT-051 | Item unit used by active barcodes stays active | Item unit deactivation | ENT-CAT-008 | ✓ |
| RULE-CAT-052 | Generation names every attribute the Item uses | Variant generation | ENT-CAT-005 | ✓ |
| RULE-CAT-053 | An inactive parent refuses child writes | Item update other than a categoryFk-only change · Item unit, image, barcode and e-invoice override writes under an inactive Item or Variant (Variant generation and Bundle components stay allowed — ADR-CAT-068) | ENT-CAT-001 | ✓ |

No RULE is DEFERRED: every `Data source` of SRS A5 resolves to an ENT field bound to a DBF (db-script §2), except RULE-CAT-036 and RULE-CAT-044, whose remaining inputs are request inputs (copy counts, the submitted file) that the rule itself names as such.

### Screen registry
| screen | type | ENT | page code · permission names |
|---|---|---|---|
| SCR-REQ-CAT-001 Items / الأصناف | Search + Entry | ENT-CAT-001 (+ 002, 005, 007, 008, 009, 010) | CAT_ITEMS · PERM_CAT_ITEMS_VIEW, PERM_CAT_ITEMS_CREATE, PERM_CAT_ITEMS_UPDATE, PERM_CAT_ITEMS_DELETE |
| SCR-REQ-CAT-002 Categories / الفئات | Master + Detail (tree) | ENT-CAT-006 | CAT_CATEGORIES · PERM_CAT_CATEGORIES_VIEW, PERM_CAT_CATEGORIES_CREATE, PERM_CAT_CATEGORIES_UPDATE, PERM_CAT_CATEGORIES_DELETE |
| SCR-REQ-CAT-003 Variant attributes / خصائص المتغيّرات | Master + Detail | ENT-CAT-003, ENT-CAT-004 | CAT_VARIANT_ATTRIBUTES · PERM_CAT_VARIANT_ATTRIBUTES_VIEW, PERM_CAT_VARIANT_ATTRIBUTES_CREATE, PERM_CAT_VARIANT_ATTRIBUTES_UPDATE, PERM_CAT_VARIANT_ATTRIBUTES_DELETE |
| SCR-REQ-CAT-004 Item import / استيراد الأصناف | Wizard step | ENT-CAT-001 | CAT_ITEM_IMPORT · PERM_CAT_ITEM_IMPORT_VIEW, PERM_CAT_ITEM_IMPORT_CREATE |
| SCR-REQ-CAT-005 Barcode labels / ملصقات الباركود | Search + Entry | ENT-CAT-002 | CAT_BARCODE_LABELS · PERM_CAT_BARCODE_LABELS_VIEW, PERM_CAT_BARCODE_LABELS_CREATE |
| SCR-REQ-CAT-006 Catalogue settings / إعدادات الكتالوج | Master + Detail per list | ENT-CAT-011, ENT-CAT-012 | CAT_SETTINGS · PERM_CAT_SETTINGS_VIEW, PERM_CAT_SETTINGS_CREATE, PERM_CAT_SETTINGS_UPDATE, PERM_CAT_SETTINGS_DELETE |

### QRC summary
| QR | operation | phase | ENT |
|---|---|---|---|
| QR-CAT-001 | FIND_ONE — Item by identifier | DATA-DOM | ENT-CAT-001 |
| QR-CAT-002 | EXISTS — Active Item with the same normalised Arabic name | DATA-DOM | ENT-CAT-001 |
| QR-CAT-003 | SAVE — Insert Item | DATA-DOM | ENT-CAT-001 |
| QR-CAT-004 | UPDATE — Update Item | DATA-DOM | ENT-CAT-001 |
| QR-CAT-005 | FIND_BY_CRITERIA — Search Items | DATA-DOM | ENT-CAT-001 |
| QR-CAT-006 | NATIVE — Active Items of a Category sub-tree | DATA-DOM | ENT-CAT-001 |
| QR-CAT-007 | FIND_BY_CRITERIA — Storefront Items | DATA-DOM | ENT-CAT-001 |
| QR-CAT-008 | NATIVE — Catalogue search, top 20 | DATA-DOM | ENT-CAT-001 |
| QR-CAT-009 | AGGREGATE — Capability turn-off dependants | DATA-DOM | ENT-CAT-001 |
| QR-CAT-010 | FIND_ONE — Lock the Item row | DATA-DOM | ENT-CAT-001 |
| QR-CAT-011 | SAVE — Insert Variant | DATA-DOM | ENT-CAT-002 |
| QR-CAT-012 | FIND_ONE — Variant by identifier | DATA-DOM | ENT-CAT-002 |
| QR-CAT-013 | FIND_ALL — Active Variants of an Item | DATA-DOM | ENT-CAT-002 |
| QR-CAT-014 | UPDATE — Update Variant | DATA-DOM | ENT-CAT-002 |
| QR-CAT-015 | COUNT — Count active Variants of an Item | DATA-DOM | ENT-CAT-002 |
| QR-CAT-016 | FIND_BY_CRITERIA — Search Variants for labels | DATA-DOM | ENT-CAT-002 |
| QR-CAT-017 | FIND_ALL — Variants (with their Item) by identifier list | DATA-DOM | ENT-CAT-002 |
| QR-CAT-018 | SAVE — Insert assignment | DATA-DOM | ENT-CAT-005 |
| QR-CAT-019 | FIND_ALL — Assignments with attribute and value names | DATA-DOM | ENT-CAT-005 |
| QR-CAT-020 | UPDATE — Deactivate the assignments of a Variant | DATA-DOM | ENT-CAT-005 |
| QR-CAT-021 | COUNT — Usage of an attribute or value on active Variants | DATA-DOM | ENT-CAT-005 |
| QR-CAT-022 | FIND_ONE — Variant attribute by identifier | DATA-DOM | ENT-CAT-003 |
| QR-CAT-023 | EXISTS — Attribute code / active name taken | DATA-DOM | ENT-CAT-003 |
| QR-CAT-024 | SAVE — Insert Variant attribute | DATA-DOM | ENT-CAT-003 |
| QR-CAT-025 | UPDATE — Update Variant attribute | DATA-DOM | ENT-CAT-003 |
| QR-CAT-026 | FIND_ALL — Attributes with their values, ordered | DATA-DOM | ENT-CAT-003 |
| QR-CAT-027 | FIND_ONE — Variant attribute value by identifier | DATA-DOM | ENT-CAT-004 |
| QR-CAT-028 | EXISTS — Value code / active name taken within the attribute | DATA-DOM | ENT-CAT-004 |
| QR-CAT-029 | SAVE — Insert Variant attribute value | DATA-DOM | ENT-CAT-004 |
| QR-CAT-030 | UPDATE — Update Variant attribute value | DATA-DOM | ENT-CAT-004 |
| QR-CAT-031 | FIND_ALL — Selected values for generation | DATA-DOM | ENT-CAT-004 |
| QR-CAT-032 | FIND_ONE — Category by identifier (or code) | DATA-DOM | ENT-CAT-006 |
| QR-CAT-033 | EXISTS — Category code / sibling name taken | DATA-DOM | ENT-CAT-006 |
| QR-CAT-034 | SAVE — Insert Category | DATA-DOM | ENT-CAT-006 |
| QR-CAT-035 | UPDATE — Update Category | DATA-DOM | ENT-CAT-006 |
| QR-CAT-036 | FIND_ALL — All Categories for the tree | DATA-DOM | ENT-CAT-006 |
| QR-CAT-037 | NATIVE — Ancestors and descendants of a Category | DATA-DOM | ENT-CAT-006 |
| QR-CAT-038 | COUNT — Active children and Items of a Category | DATA-DOM | ENT-CAT-006 |
| QR-CAT-039 | FIND_ALL — Active lines of a Bundle | DATA-DOM | ENT-CAT-007 |
| QR-CAT-040 | SAVE — Insert Bundle line | DATA-DOM | ENT-CAT-007 |
| QR-CAT-041 | UPDATE — Update Bundle line | DATA-DOM | ENT-CAT-007 |
| QR-CAT-042 | COUNT — Active Bundles holding a Variant (or any Variant of an Item) | DATA-DOM | ENT-CAT-007 |
| QR-CAT-043 | FIND_ONE — Item unit by identifier | DATA-DOM | ENT-CAT-008 |
| QR-CAT-044 | FIND_ALL — Active units of an Item | DATA-DOM | ENT-CAT-008 |
| QR-CAT-045 | SAVE — Insert Item unit | DATA-DOM | ENT-CAT-008 |
| QR-CAT-046 | UPDATE — Update Item unit | DATA-DOM | ENT-CAT-008 |
| QR-CAT-047 | FIND_ALL — Active barcodes of Variants | DATA-DOM | ENT-CAT-009 |
| QR-CAT-048 | EXISTS — Active barcode value taken | DATA-DOM | ENT-CAT-009 |
| QR-CAT-049 | SAVE — Insert barcode | DATA-DOM | ENT-CAT-009 |
| QR-CAT-050 | FIND_ONE — Barcode by identifier | DATA-DOM | ENT-CAT-009 |
| QR-CAT-051 | UPDATE — Deactivate barcode | DATA-DOM | ENT-CAT-009 |
| QR-CAT-052 | FIND_ONE — Lookup an active barcode | DATA-DOM | ENT-CAT-009 |
| QR-CAT-053 | EXISTS — Active GTIN / INTERNAL barcode beginning with a prefix | DATA-DOM | ENT-CAT-009 |
| QR-CAT-054 | FIND_ALL — Active media of an Item | DATA-DOM | ENT-CAT-010 |
| QR-CAT-055 | FIND_ONE — Media by identifier | DATA-DOM | ENT-CAT-010 |
| QR-CAT-056 | SAVE — Insert media | DATA-DOM | ENT-CAT-010 |
| QR-CAT-057 | UPDATE — Update media | DATA-DOM | ENT-CAT-010 |
| QR-CAT-058 | FIND_ALL — Images of a Variant with fallback | DATA-DOM | ENT-CAT-010 |
| QR-CAT-059 | FIND_ALL — Active scale label layouts | DATA-DOM | ENT-CAT-011 |
| QR-CAT-060 | FIND_ONE — Layout by identifier | DATA-DOM | ENT-CAT-011 |
| QR-CAT-061 | EXISTS — Active layout key taken | DATA-DOM | ENT-CAT-011 |
| QR-CAT-062 | SAVE — Insert layout | DATA-DOM | ENT-CAT-011 |
| QR-CAT-063 | UPDATE — Update layout | DATA-DOM | ENT-CAT-011 |
| QR-CAT-064 | FIND_ALL — All layouts | DATA-DOM | ENT-CAT-011 |
| QR-CAT-065 | FIND_ONE — Unit e-invoice code by identifier | DATA-DOM | ENT-CAT-012 |
| QR-CAT-066 | EXISTS — Active row for a unit taken | DATA-DOM | ENT-CAT-012 |
| QR-CAT-067 | SAVE — Insert Unit e-invoice code | DATA-DOM | ENT-CAT-012 |
| QR-CAT-068 | UPDATE — Update Unit e-invoice code | DATA-DOM | ENT-CAT-012 |
| QR-CAT-069 | FIND_ALL — All Unit e-invoice codes | DATA-DOM | ENT-CAT-012 |
| QR-CAT-070 | FIND_ALL — Active e-invoice units of units of measure | DATA-DOM | ENT-CAT-012 |
| QR-CAT-071 | COUNT — Active barcodes of an Item bound to a unit | DATA-DOM | ENT-CAT-008 |
| QR-CAT-072 | UPDATE — Deactivate the active barcodes of a Variant | DATA-DOM | ENT-CAT-009 |
| QR-CAT-073 | NATIVE — Category tree lock | DATA-DOM | ENT-CAT-006 |
| QR-CAT-074 | NATIVE — Scale-prefix lock | DATA-DOM | ENT-CAT-011 |
| QR-CAT-075 | UPDATE — Clear the default flag of an Item's Variants | DATA-DOM | ENT-CAT-002 |

### Other dimensions
| dimension | value |
|---|---|
| DB ALIGNMENT | see the manifest below — ALIGNED ✓ · issues: 0 |
| INTEGRATION | 0 edges — the XM register is empty (`records: []`, SRS A8 `consumes: []`); `INT-C`, the last phase, carries no block |
| GIVEN | 11 uses — the SRS A9 `given-usage` rows (erp-core); each is reached only through the `via` it names (PHASE CORE, "Given usage") |
| SECURITY | 6 screens × 3 roles (Owner, Seller/cashier, Warehouse staff) — PHASE SEC-BE |

```yaml name=totals
DBF: 161
XM: 0
API: 55
QR: 75
```

## DB ALIGNMENT MANIFEST — CAT v2

Columns, DB types and SRS references are looked up from the db-script by the DBF id; this manifest only binds each DBF to the plan's property and type. No column crosses modules, so the XM column is "—" on every row. A required column no endpoint writes carries its reason (system-generated · derived · seeded · DEFERRED · core-provided).

| DBF | ENT | plan property | plan type | XM | status |
|---|---|---|---|---|---|
| DBF-CAT-001 | ENT-CAT-001 | itemPk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-002 | ENT-CAT-001 | code | String | — | ✓ |
| DBF-CAT-003 | ENT-CAT-001 | nameAr | String | — | ✓ |
| DBF-CAT-004 | ENT-CAT-001 | nameEn | String | — | ✓ |
| DBF-CAT-005 | ENT-CAT-001 | itemKindCode | String | — | ✓ |
| DBF-CAT-006 | ENT-CAT-001 | baseUomCode | String | — | ✓ |
| DBF-CAT-007 | ENT-CAT-001 | brandCode | String | — | ✓ |
| DBF-CAT-008 | ENT-CAT-001 | taxClassCode | String | — | ✓ |
| DBF-CAT-009 | ENT-CAT-001 | einvoiceCodeTypeCode | String | — | ✓ |
| DBF-CAT-010 | ENT-CAT-001 | einvoiceCode | String | — | ✓ |
| DBF-CAT-011 | ENT-CAT-001 | descriptionAr | String | — | ✓ |
| DBF-CAT-012 | ENT-CAT-001 | descriptionEn | String | — | ✓ |
| DBF-CAT-013 | ENT-CAT-001 | publishStateCode | String | — | ✓ |
| DBF-CAT-014 | ENT-CAT-001 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-015 | ENT-CAT-001 | categoryFk | Long | — | ✓ |
| DBF-CAT-016 | ENT-CAT-001 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-017 | ENT-CAT-001 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-018 | ENT-CAT-001 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-019 | ENT-CAT-001 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-020 | ENT-CAT-001 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-021 | ENT-CAT-001 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-022 | ENT-CAT-002 | variantPk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-023 | ENT-CAT-002 | code | String | — | ✓ |
| DBF-CAT-024 | ENT-CAT-002 | isDefaultFl | Boolean | — | ✓ |
| DBF-CAT-025 | ENT-CAT-002 | einvoiceCodeTypeCode | String | — | ✓ |
| DBF-CAT-026 | ENT-CAT-002 | einvoiceCode | String | — | ✓ |
| DBF-CAT-027 | ENT-CAT-002 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-028 | ENT-CAT-002 | itemFk | Long | — | ✓ |
| DBF-CAT-029 | ENT-CAT-002 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-030 | ENT-CAT-002 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-031 | ENT-CAT-002 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-032 | ENT-CAT-002 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-033 | ENT-CAT-002 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-034 | ENT-CAT-002 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-035 | ENT-CAT-003 | variantAttributePk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-036 | ENT-CAT-003 | code | String | — | ✓ |
| DBF-CAT-037 | ENT-CAT-003 | nameAr | String | — | ✓ |
| DBF-CAT-038 | ENT-CAT-003 | nameEn | String | — | ✓ |
| DBF-CAT-039 | ENT-CAT-003 | sortOrder | Integer | — | ✓ |
| DBF-CAT-040 | ENT-CAT-003 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-041 | ENT-CAT-003 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-042 | ENT-CAT-003 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-043 | ENT-CAT-003 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-044 | ENT-CAT-003 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-045 | ENT-CAT-003 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-046 | ENT-CAT-003 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-047 | ENT-CAT-004 | variantAttributeValuePk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-048 | ENT-CAT-004 | code | String | — | ✓ |
| DBF-CAT-049 | ENT-CAT-004 | nameAr | String | — | ✓ |
| DBF-CAT-050 | ENT-CAT-004 | nameEn | String | — | ✓ |
| DBF-CAT-051 | ENT-CAT-004 | sortOrder | Integer | — | ✓ |
| DBF-CAT-052 | ENT-CAT-004 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-053 | ENT-CAT-004 | variantAttributeFk | Long | — | ✓ |
| DBF-CAT-054 | ENT-CAT-004 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-055 | ENT-CAT-004 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-056 | ENT-CAT-004 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-057 | ENT-CAT-004 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-058 | ENT-CAT-004 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-059 | ENT-CAT-004 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-060 | ENT-CAT-005 | variantAttributeAssignmentPk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-061 | ENT-CAT-005 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-062 | ENT-CAT-005 | variantFk | Long | — | ✓ |
| DBF-CAT-063 | ENT-CAT-005 | variantAttributeFk | Long | — | ✓ |
| DBF-CAT-064 | ENT-CAT-005 | variantAttributeValueFk | Long | — | ✓ |
| DBF-CAT-065 | ENT-CAT-005 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-066 | ENT-CAT-005 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-067 | ENT-CAT-005 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-068 | ENT-CAT-005 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-069 | ENT-CAT-005 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-070 | ENT-CAT-005 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-071 | ENT-CAT-006 | categoryPk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-072 | ENT-CAT-006 | code | String | — | ✓ |
| DBF-CAT-073 | ENT-CAT-006 | nameAr | String | — | ✓ |
| DBF-CAT-074 | ENT-CAT-006 | nameEn | String | — | ✓ |
| DBF-CAT-075 | ENT-CAT-006 | sortOrder | Integer | — | ✓ |
| DBF-CAT-076 | ENT-CAT-006 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-077 | ENT-CAT-006 | parentCategoryFk | Long | — | ✓ |
| DBF-CAT-078 | ENT-CAT-006 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-079 | ENT-CAT-006 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-080 | ENT-CAT-006 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-081 | ENT-CAT-006 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-082 | ENT-CAT-006 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-083 | ENT-CAT-006 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-084 | ENT-CAT-007 | bundleComponentPk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-085 | ENT-CAT-007 | quantity | BigDecimal | — | ✓ |
| DBF-CAT-086 | ENT-CAT-007 | sortOrder | Integer | — | ✓ |
| DBF-CAT-087 | ENT-CAT-007 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-088 | ENT-CAT-007 | bundleItemFk | Long | — | ✓ |
| DBF-CAT-089 | ENT-CAT-007 | componentVariantFk | Long | — | ✓ |
| DBF-CAT-090 | ENT-CAT-007 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-091 | ENT-CAT-007 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-092 | ENT-CAT-007 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-093 | ENT-CAT-007 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-094 | ENT-CAT-007 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-095 | ENT-CAT-007 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-096 | ENT-CAT-008 | itemUnitPk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-097 | ENT-CAT-008 | uomCode | String | — | ✓ |
| DBF-CAT-098 | ENT-CAT-008 | conversionFactor | BigDecimal | — | ✓ |
| DBF-CAT-099 | ENT-CAT-008 | isPurchaseFl | Boolean | — | ✓ |
| DBF-CAT-100 | ENT-CAT-008 | isSalesFl | Boolean | — | ✓ |
| DBF-CAT-101 | ENT-CAT-008 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-102 | ENT-CAT-008 | itemFk | Long | — | ✓ |
| DBF-CAT-103 | ENT-CAT-008 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-104 | ENT-CAT-008 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-105 | ENT-CAT-008 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-106 | ENT-CAT-008 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-107 | ENT-CAT-008 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-108 | ENT-CAT-008 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-109 | ENT-CAT-009 | barcodePk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-110 | ENT-CAT-009 | barcodeValue | String | — | ✓ |
| DBF-CAT-111 | ENT-CAT-009 | barcodeTypeCode | String | — | ✓ |
| DBF-CAT-112 | ENT-CAT-009 | uomCode | String | — | ✓ |
| DBF-CAT-113 | ENT-CAT-009 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-114 | ENT-CAT-009 | variantFk | Long | — | ✓ |
| DBF-CAT-115 | ENT-CAT-009 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-116 | ENT-CAT-009 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-117 | ENT-CAT-009 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-118 | ENT-CAT-009 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-119 | ENT-CAT-009 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-120 | ENT-CAT-009 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-121 | ENT-CAT-010 | itemMediaPk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-122 | ENT-CAT-010 | fileDocumentRef | Long | — | ✓ |
| DBF-CAT-123 | ENT-CAT-010 | nameAr | String | — | ✓ |
| DBF-CAT-124 | ENT-CAT-010 | nameEn | String | — | ✓ |
| DBF-CAT-125 | ENT-CAT-010 | sortOrder | Integer | — | ✓ |
| DBF-CAT-126 | ENT-CAT-010 | isPrimaryFl | Boolean | — | ✓ |
| DBF-CAT-127 | ENT-CAT-010 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-128 | ENT-CAT-010 | itemFk | Long | — | ✓ |
| DBF-CAT-129 | ENT-CAT-010 | variantFk | Long | — | ✓ |
| DBF-CAT-130 | ENT-CAT-010 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-131 | ENT-CAT-010 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-132 | ENT-CAT-010 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-133 | ENT-CAT-010 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-134 | ENT-CAT-010 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-135 | ENT-CAT-010 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-136 | ENT-CAT-011 | scaleLabelLayoutPk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-137 | ENT-CAT-011 | key | String | — | ✓ |
| DBF-CAT-138 | ENT-CAT-011 | valueAr | String | — | ✓ |
| DBF-CAT-139 | ENT-CAT-011 | valueEn | String | — | ✓ |
| DBF-CAT-140 | ENT-CAT-011 | scaleCodeLength | Integer | — | ✓ |
| DBF-CAT-141 | ENT-CAT-011 | embeddedValueTypeCode | String | — | ✓ |
| DBF-CAT-142 | ENT-CAT-011 | valueDecimals | Integer | — | ✓ |
| DBF-CAT-143 | ENT-CAT-011 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-144 | ENT-CAT-011 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-145 | ENT-CAT-011 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-146 | ENT-CAT-011 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-147 | ENT-CAT-011 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-148 | ENT-CAT-011 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-149 | ENT-CAT-011 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-150 | ENT-CAT-012 | unitEinvoiceCodePk | Long | — | ✓ system-generated (identity) |
| DBF-CAT-151 | ENT-CAT-012 | key | String | — | ✓ |
| DBF-CAT-152 | ENT-CAT-012 | valueAr | String | — | ✓ derived (copied from the CAT_UOM label at save — D-P2-09) |
| DBF-CAT-153 | ENT-CAT-012 | valueEn | String | — | ✓ derived (copied from the CAT_UOM label at save — D-P2-09) |
| DBF-CAT-154 | ENT-CAT-012 | einvoiceUnitCode | String | — | ✓ |
| DBF-CAT-155 | ENT-CAT-012 | isActiveFl | Boolean | — | ✓ |
| DBF-CAT-156 | ENT-CAT-012 | tenantId | Long | — | ✓ core-provided (given tenancy) |
| DBF-CAT-157 | ENT-CAT-012 | version | Long | — | ✓ core-provided (given AuditableEntity optimistic lock; PUT bodies echo it) |
| DBF-CAT-158 | ENT-CAT-012 | createdBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-159 | ENT-CAT-012 | createdAt | OffsetDateTime | — | ✓ system-generated (given audit) |
| DBF-CAT-160 | ENT-CAT-012 | updatedBy | String | — | ✓ system-generated (given audit) |
| DBF-CAT-161 | ENT-CAT-012 | updatedAt | OffsetDateTime | — | ✓ system-generated (given audit) |

Derived / computed properties (no DBF — ADR-CAT-048):

| DBF | ENT | plan property | plan type | XM | status |
|---|---|---|---|---|---|
| — (derived) | ENT-CAT-002 | nameAr | String | — | ✓ derived — Item nameAr + " / " + value nameAr in attribute sortOrder (D-15, D-P2-08) — ADR-CAT-048 |
| — (derived) | ENT-CAT-002 | nameEn | String | — | ✓ derived — same composition from nameEn, nameAr where nameEn is empty — ADR-CAT-048 |
| — (derived) | ENT-CAT-001 | displayNameEn | String | — | ✓ derived — nameEn, else nameAr (REQ-CAT-029) — ADR-CAT-048 |
| — (derived) | ENT-CAT-002 | effectiveEinvoiceCodeTypeCode, effectiveEinvoiceCode | String | — | ✓ derived — Variant override, else Item value (REQ-CAT-119) — ADR-CAT-048 |
| — (derived) | ENT-CAT-002, ENT-CAT-008 | einvoiceUnitCode | String | — | ✓ derived — active CAT_UNIT_EINVOICE_CODE of the unit, else empty (REQ-CAT-122) — ADR-CAT-048 |
| — (derived) | ENT-CAT-009 | embeddedValue, embeddedValueTypeCode | BigDecimal, String | — | ✓ derived — decoded from a scale label (REQ-CAT-097) — ADR-CAT-048 |
| — (derived) | ENT-CAT-007 | componentVariantCode | String | — | ✓ derived — code of the component Variant (REQ-CAT-058) — ADR-CAT-048 |

## ADRs this plan depends on
| ADR | decision | status |
|---|---|---|
| ADR-CAT-038 … ADR-CAT-043 | P1 decisions (open items, dependent kinds, tax class, capability flags, config entities, catalogue search) | ACCEPTED |
| ADR-CAT-044 | platform-standard error rows (ar / en texts written and the 413 / 415 rows removed at the second gate-analysis revise) | ACCEPTED (non-breaking) |
| ADR-CAT-045 | endpoint surface beyond SRS B5; in-process `CatalogueReadApi` | ACCEPTED (non-breaking) |
| ADR-CAT-046 | security bootstrap and preset-role grants; tenant copies of seeds | ACCEPTED (non-breaking) |
| ADR-CAT-047 | repository deviations (native queries, joins) and concurrency guards (Category deactivation and reactivation take the tree lock — amended at the third gate-analysis revise; the exact key form of the two advisory locks, QR-CAT-073 / 074 — amended at the fifth gate-analysis revise) | ACCEPTED (non-breaking) |
| ADR-CAT-048 | endpoint shapes, derived properties, success-path messages (§4 amended by ADR-CAT-056, ADR-CAT-057) | ACCEPTED (non-breaking) |
| ADR-CAT-055 | the given CORE_TENANT is a permitted FK reference, not an XM (db-script BLOCK 5e) | ACCEPTED (non-breaking) |
| ADR-CAT-056 | uploaded files go through the given FILE endpoints; API-CAT-018 / 049 take fileDocumentRef; file retention | ACCEPTED (non-breaking) |
| ADR-CAT-057 | the item import sheet is report CAT_ITEM_IMPORT_SHEET rendered through PrintApi | ACCEPTED (non-breaking) |
| ADR-CAT-058 | CAT seeds reach every new tenant through the given provisioning hook | ACCEPTED (non-breaking) |
| ADR-CAT-059 | transitions into ACTIVE re-establish the Item invariants; channel reads see only active Items | ACCEPTED (non-breaking) |
| ADR-CAT-060 | an Item unit used by active barcodes stays active | ACCEPTED (non-breaking) |
| ADR-CAT-061 | CatalogueReadApi calls an unsecured read service; the caller module authorizes its own caller — conditional on the core's answer to CORE-GAP-in-process-authorization (ADR-CAT-066) | ACCEPTED (non-breaking) |
| ADR-CAT-062 | Arabic normalisation, trigram indexes, escaped LIKE | ACCEPTED (non-breaking) |
| ADR-CAT-063 | an inactive parent refuses child writes (RULE-CAT-053); deactivating a Variant deactivates its barcodes | ACCEPTED (non-breaking) |
| ADR-CAT-064 | scale code length fixed after creation; type and decimals only change how the embedded value is read; a deactivated layout leaves its SCALE barcodes stored | ACCEPTED (non-breaking) |
| ADR-CAT-065 | CAT serves its own lookup keys through OwnedLookups (API-CAT-052); amends ADR-CAT-050 §1 and §2 | ACCEPTED (non-breaking) |
| ADR-CAT-066 | in-process authorization raised as CORE-GAP-in-process-authorization.md; ADR-CAT-061 made conditional on the core's answer | ACCEPTED (non-breaking) |
| ADR-CAT-067 | every intra-module FK is composite with TENANT_ID (db-script D-P2-12) | ACCEPTED (non-breaking) |
| ADR-CAT-068 | three repair operations stay allowed on an inactive Item (API-CAT-003 categoryFk-only, API-CAT-006, API-CAT-017); amends ADR-CAT-063 | ACCEPTED (non-breaking) |
| ADR-CAT-069 | Categories, Variant attributes and values can be reactivated (API-CAT-053 … 055) | ACCEPTED (non-breaking) |
| ADR-CAT-070 | Variant attributes and values stay CAT tables, not mdl lookup types (checklist C5 / F3); CORE-GAP-tenant-lookup-types.md | ACCEPTED (non-breaking) |
| ADR-CAT-071 | item import time budget, batched code allocation, one scale-prefix lock per import; CORE-GAP-number-series-block-allocation.md | ACCEPTED (non-breaking) |
| ADR-CAT-072 | the default Variant moves: on deactivation of the default (API-CAT-009) to the remaining active Variant with the lowest code, on generation for an Item with 0 active Variants (API-CAT-006) to the first Variant created; UQ_CAT_VARIANT_DEFAULT kept, the former default cleared first | ACCEPTED (non-breaking) |

<!-- PHASE:CORE:START traces=REQ-CAT-131,REQ-CAT-132,REQ-CAT-134,REQ-CAT-001,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-032,REQ-CAT-033,REQ-CAT-073,REQ-CAT-074,REQ-CAT-076,REQ-CAT-029 -->
## PHASE CORE — architecture policies (R1)

### Module structure
- Package root `com.erp.cat`. Layers:
  - `api`: the REST controllers `Cat<Resource>Controller`, and the read facades `Cat<Resource>QueryFacade`. A facade carries the screen's `@PreAuthorize` and calls the read service (ADR-CAT-061). The one exception is `CatSearchQueryFacade.searchCatalogue` (API-CAT-047), which carries only `@PreAuthorize("isAuthenticated()")`; its read service checks PERM_CAT_ITEMS_VIEW in code and answers an empty list without it (REQ-CAT-127).
  - `service`: `Cat<Resource>Service` (transactional mutations, each method with `@PreAuthorize("hasAuthority(T(com.erp.cat.permission.CatPermissions).PERM_…)")` of its own page — checklist C13) and `CatCatalogueReadService` (the reads, written once, with no `@PreAuthorize`; package-visible to the facades and to `CatalogueReadApi`). The read service's missing `@PreAuthorize` is an exception to C13 that the conventions do not grant: it is raised as `CORE-GAP-in-process-authorization.md` and holds only until the core answers (ADR-CAT-066 — see "Core gaps" below).
  - `domain`: the entities, one per table, each with its Domain companion `<Entity>Domain`.
  - `repository`: `Cat<Entity>Repository`.
  - `crossmodule`: the published in-process interface `com.erp.cat.crossmodule.CatalogueReadApi` (ADR-CAT-045, ADR-CAT-061).
  - `permission`: `CatPermissions` (`com.erp.cat.permission.CatPermissions implements PermissionContributor`) — the one contributor of checklist C13, and the class every `@PreAuthorize` references. No other name is used for it.
  - `report`: `CatBarcodeLabelReportProvider` and `CatItemImportSheetReportProvider`.
  - `tenant`: `CatTenantProvisioningContributor`.
- **Rule placement (checklist C12).** A rule that needs only the record being written, and the parent values passed to it, lives on that entity's Domain companion (`<Entity>Domain.create(…)` for a new record, `<Entity>Domain.from(stored, request)` for a change). Each entity block names its companion and its rules: for example RULE-CAT-001, 002, 004, 007, 022, 027 (format and base-unit difference), 028, 030, 034 and 042. The service orchestrates: it holds the transaction, the locks, the queries, the given calls and every cross-record rule (uniqueness, counts, hierarchy, flags, lookups, state invariants). Controllers hold no rule. A db-script constraint that backs a rule is translated to that rule's catalog row.
- Every entity extends the given `AuditableEntity` (TENANT_ID, VERSION, audit columns — D-P2-02). Tenancy is resolved by the given core; every JPQL, derived and search-builder query runs under the given tenant filter (TENANT_ID = current tenant). The six native queries (QR-CAT-006, 008, 009, 037, and the two advisory locks QR-CAT-073 and 074) name `TENANT_ID` (`TENANT_ID = :tenant` on every table they read, or `:tenant` as the first lock key) and carry `@TenantUnscoped(reason, adr = "ADR-CAT-047")` on their repository methods (checklist C3). A record of another tenant reads and writes as not found (CAT-404-NOT-FOUND — REQ-CAT-132).
- One transaction per request: READ_WRITE for mutations, READ_ONLY for reads. Delete semantics are `soft`: a DELETE endpoint flips the active flag (suffix `Fl`) — nothing is ever physically deleted (RULE-CAT-048).
- Workflow engine: **forbidden** — no CAT entity has an approval flow (SRS A7).

### Type mapping (postgresql16 → Java)
| DB type | Java type | note |
|---|---|---|
| BIGINT | Long | PK columns use the identity strategy `GENERATED ALWAYS AS IDENTITY` (db-script header); FK and TENANT_ID / VERSION columns are plain Long |
| VARCHAR(n) | String | length ≤ n validated before persistence |
| BOOLEAN | Boolean | flag properties end with `Fl` |
| INTEGER | Integer | sortOrder, scaleCodeLength, valueDecimals |
| NUMERIC(18,3) / NUMERIC(18,6) | BigDecimal | scale 3 (quantity) / 6 (conversionFactor) |
| TIMESTAMPTZ | OffsetDateTime | audit columns as the given AuditableEntity maps them |

### API conventions
- Base path `/api/v1/{module}/{resource}` with module `cat`; verbs POST = create, GET = read, PUT = update, DELETE = deactivate (soft), PATCH = partial.
- Success responses are wrapped in `ApiResponse<T>`; lists that page return `Page<T>` with the query parameters `page` and `size`; an empty search result is success with empty content.
- Runtime error-code format: `{MOD}-{http}[-{SLUG}]` — for this module `CAT-<http>-<SLUG>`. Every Error Catalog row is an instance of it. Errors travel as `LocalizedException → {code, messageAr, messageEn}`; a RULE whose message is shown on success travels in the success envelope with its 2xx code (ADR-CAT-048). Every catalog code — the RULE rows and the PLATFORM-STD rows alike — is a `public static final String` of `CatErrorCodes` with an entry in `messages.properties` (en) and `messages_ar.properties` (ar) whose text is the catalog row's `messages` (checklist C11).
- Authentication: bearer JWT (given `sec`); every endpoint checks its `PERM_<PAGE_CODE>_<ACTION>` before processing (PHASE SEC-BE).
- Search contract: filters and sort are those of each screen's SRS B2 list, not widened — Items sorted by nameAr ascending (REQ-CAT-003), Category tree and Variant attributes by sortOrder (REQ-CAT-067, REQ-CAT-048), Variants by Item nameAr then code; no client sort parameter.

### Lookups
All LOV values are loaded at runtime from the core lookup module (`mdl`) through `com.erp.mdl.crossmodule.MdlLookupApi`; no enum of codes is hard-coded in an API or a field spec. The backend stores and returns codes; the frontend shows their labels from API-CAT-052. CAT serves its own keys to the screens through API-CAT-052 (`GET /api/v1/cat/lookups/{key}`): `CatLookupService.read(key)` returns `OwnedLookups.read(key, CAT_LOOKUP_KEYS, CatErrorCodes.LOOKUP_KEY_UNKNOWN, mdlLookupApi::readActiveValuesByKey)` mapped to `LookupOption`, where `CAT_LOOKUP_KEYS` holds the 10 keys listed below; any other key answers CAT-404-LOOKUP-KEY-UNKNOWN and is never passed through to mdl (checklist C6, ADR-CAT-065). A changed lookup-backed field must hold an active value of its key (RULE-CAT-006); an unchanged value that was deactivated later is kept (REQ-CAT-076). Keys (SRS A6, seeded by db-script BLOCK 8a/8b): CAT_ITEM_KIND (itemKindCode), CAT_PUBLISH_STATE (publishStateCode), CAT_BRAND (brandCode), CAT_UOM (baseUomCode, uomCode, Unit e-invoice key), CAT_TAX_CLASS (taxClassCode), CAT_EINVOICE_ITEM_CODE_TYPE (einvoiceCodeTypeCode), CAT_EINVOICE_UNIT (einvoiceUnitCode), CAT_BARCODE_TYPE (barcodeTypeCode), CAT_SCALE_VALUE_TYPE (embeddedValueTypeCode), CAT_CHANGE_KIND (event payload changeKind). The SRS defaults (STANDARD, PIECE, STANDARD, UNPUBLISHED — D-09; WEIGHT, 5, 3 for layouts) are applied by the service (D-P2-05), not by the database.

### Numbering
Document and business numbers come from the core sequence module through `com.erp.sequence.crossmodule.NumberSeriesApi`; CAT generates none itself. Series (seeded by db-script BLOCK 8c): CAT_ITEM → Item code `ITM-` + 6 digits (DBF-CAT-002), CAT_VARIANT → Variant code `VAR-` + 6 digits (DBF-CAT-023), CAT_INTERNAL_BARCODE → the 10 middle digits of an internal barcode (DBF-CAT-110: `04` + 10 digits + GS1 check digit). Allocation is atomic in the given; a failure to issue raises RULE-CAT-005 and nothing is stored (REQ-CAT-035).

### Capability flags
`CatCapabilityFlags` reads the flags `variants` and `bundles` through the FeatureFlags API backed by `com.erp.cu.crossmodule.SettingsApi` (ADR-CAT-041), on every request that depends on them — never cached across requests, so a change applies from the next request (REQ-CAT-012). Logic never reads the tenant's activity or preset (REQ-CAT-011). A read that returns no value refuses the operation with RULE-CAT-009; a flag that is off refuses it with RULE-CAT-008. Only the operations RULE-CAT-008 lists read a flag — creating a BUNDLE Item, generating Variants, saving Bundle components, creating or updating Variant attributes and values (API-CAT-001 for BUNDLE, 006, 017, 025, 027, 029, 030). Reads, deactivations and reactivations of records that already exist never read a flag, so data that depends on a flag that is off can still be cleaned up.

### Catalogue-change events
Every committed change to ENT-CAT-001, 002, 005, 007, 008, 009 or 010 records one event `OutboxPublisher.publish("cat.CatalogueChanged", payload)` with payload {entity (ENT id), recordPk, itemPk, changeKind ∈ CAT_CHANGE_KIND} inside the change transaction (event type `<code>.<Name>` — checklist C19); the given outbox delivers it only after commit, so a rollback publishes nothing (RULE-CAT-049, REQ-CAT-008, REQ-CAT-009). The event carries no field values (CON-CAT-018).

### Languages
Every named entity carries nameAr (mandatory, ≥ 1 Arabic letter) and nameEn (optional); config entities carry valueAr / valueEn. Every message is returned in ar and en. Where nameEn is empty, the English display returns nameAr (`displayNameEn`, REQ-CAT-029).

### Given usage
| given · module | via | used by |
|---|---|---|
| erp-core · modules.mdl | com.erp.mdl.crossmodule.MdlLookupApi | RULE-CAT-006 checks; lookup keys above |
| erp-core · modules.mdl, through common `owned-lookups` | com.erp.mdl.crossmodule.MdlLookupApi, read inside `com.erp.common.lookup.OwnedLookups.read(...)` | API-CAT-052 serves the 10 CAT keys only (ADR-CAT-065); the core manifest publishes no interface for `owned-lookups`, so the row names the mdl interface the call goes through |
| erp-core · modules.cu | com.erp.cu.crossmodule.SettingsApi | `CatCapabilityFlags` (RULE-CAT-008, RULE-CAT-009) |
| erp-core · modules.sequence | com.erp.sequence.crossmodule.NumberSeriesApi | Item / Variant codes, internal barcode digits |
| erp-core · modules.file | docs/api-docs/file/index.md (the given FILE upload endpoints) | the client uploads images (public image storage) and import files (private storage) before API-CAT-018 / API-CAT-049; CAT has no multipart endpoint and stores no byte (ADR-CAT-056) → DBF-CAT-122 |
| erp-core · modules.file | com.erp.file.crossmodule.FileDocumentLookupApi | type, size, storage (public image storage or private) and availability of a `fileDocumentRef` (RULE-CAT-038, RULE-CAT-044), bytes of an import file, public URL of an image |
| erp-core · modules.events | com.erp.events.OutboxPublisher | catalogue-change events |
| erp-core · modules.report | com.erp.report.crossmodule.PrintApi | label document (API-CAT-050, PDF) and item import sheet (API-CAT-048, XLSX), both returned through `PrintResponses.toResponseEntity` |
| erp-core · modules.report | com.erp.report.ReportProvider | `CatBarcodeLabelReportProvider` registers report CAT_BARCODE_LABEL; `CatItemImportSheetReportProvider` registers report CAT_ITEM_IMPORT_SHEET (ADR-CAT-057) |
| erp-core · modules.sec | com.erp.sec.permission.PermissionContributor | `CatPermissions` registers the six page codes |
| erp-core · modules.tenant | com.erp.tenant.TenantProvisioningContributor | `CatTenantProvisioningContributor` copies the 8 Unit e-invoice codes into each new tenant; the given copies the CAT lookups and number series (ADR-CAT-058, REQ-CAT-134) |

No CAT class re-creates an entity or a table the given owns; an operation on a given entity is a call through its `via`.

### Module manifest, boundary, audit and migration (erp-core checklist C15, C16, C18, C20)
- **Manifest (C18).** The CAT jar ships `src/main/resources/META-INF/erp/modules/cat.yaml`:
  - `code: cat`, `version: 2.0.0`, `name` (ar «الكتالوج» / en "Catalog"), `package: com.erp.cat`;
  - `depends: []`, `optional-depends: []` (no business-module dependency — SRS A8);
  - `permission-modules: [CAT]` — the `PermissionModule` CatPermissions declares (menu «الكتالوج / Catalog»; also the `OWNER_MODULE_CODE` of the CAT lookup types, db-script BLOCK 8a), whose `PermissionScreen`s are the six page codes CAT_ITEMS, CAT_CATEGORIES, CAT_VARIANT_ATTRIBUTES, CAT_ITEM_IMPORT, CAT_BARCODE_LABELS, CAT_SETTINGS;
  - `api-paths: [/api/v1/cat]`, `settings: []`;
  - `events.publishes: [cat.CatalogueChanged]`, `events.consumes: []`;
  - `migrations: [V1000__cat_catalog.sql]`;
  - `reports: [CAT_BARCODE_LABEL, CAT_ITEM_IMPORT_SHEET]`;
  - no `home-cards`.
  Every API-CAT path sits under `/api/v1/cat`.
- **Boundary (C20).** `CatModuleBoundaryTest` runs `ModuleBoundaryRules.modulesDependOnlyOnTheirDeclaredDependencies()` in the module's tests. The CI workflow gains the job `module-cat` (template `module-matrix.yml`), which builds and tests the CAT module on its own. Consumers may import `com.erp.cat.crossmodule` only.
- **Audit (C16).** Every entity carries `@Audited(entityType = "<TABLE>")`, named per entity block: ENT-CAT-001 `CAT_ITEM` … ENT-CAT-012 `CAT_UNIT_EINVOICE_CODE`. No field is excluded, because no CAT column is sensitive.
- **Migration (C15).** The db-script is the single file `V1000__cat_catalog.sql` in `classpath:db/migration`. A later change is a new `V1001+` file. Its BLOCK 8 seeds the given tables with the columns of erp-core 1.6.0 and holds no transaction statement (db-script D-P2-13). Deployment precondition: the Flyway role may CREATE on the database for `pg_trgm`, or a DBA installs the extension first.

### Core gaps (erp-core checklist F9)
| CORE-GAP | Question to the core | What CAT does until the answer | ADR |
|---|---|---|---|
| decisions/CAT/CORE-GAP-in-process-authorization.md | How is a cross-module read API (`com.erp.<mod>.crossmodule.*`) authorized when its caller holds no permission of the owning module — a storefront shopper of SFR, a cashier of SLS, a job of INV — given that checklist C13 requires `@PreAuthorize` on every service method? | `CatCatalogueReadService` carries no `@PreAuthorize`; the HTTP facades keep the screen permission; the calling module authorizes its own caller (ADR-CAT-061). When the core answers (for example a core caller authority for in-process reads, or a documented C13 exception), CAT adopts it in a delta without changing any `CatalogueReadApi` signature | ADR-CAT-066 |
| decisions/CAT/CORE-GAP-tenant-lookup-types.md | Can a tenant user create a lookup **type** at runtime (not only values of a type a module seeded), with values nested under it and a stable identifier another table may reference? | Variant attributes and their values stay in CAT_VAR_ATTR / CAT_VAR_ATTR_VALUE (checklist C5 / F3 deviation, justified): the tenant defines the attributes, each value belongs to one attribute, and CAT_VAR_ATTR_ASSIGN references the pair by composite FK (FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE). When the core answers, a delta moves them to mdl only if it keeps that integrity | ADR-CAT-070 |
| decisions/CAT/CORE-GAP-number-series-block-allocation.md | Can `NumberSeriesApi` allocate a block of n consecutive numbers in one call? | The item import (API-CAT-049) draws its codes per batch of 500 rows, one `next` call per code (each in its own transaction, as the given defines it), before the batch is inserted. When the core answers, one call per batch replaces the n calls, with no change to the endpoint | ADR-CAT-071 |
<!-- PHASE:CORE:END -->

<!-- PHASE:DATA-DOM:START traces=REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-016,DBF-CAT-017,DBF-CAT-018,DBF-CAT-019,DBF-CAT-020,DBF-CAT-021,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-025,DBF-CAT-026,DBF-CAT-027,DBF-CAT-028,DBF-CAT-029,DBF-CAT-030,DBF-CAT-031,DBF-CAT-032,DBF-CAT-033,DBF-CAT-034,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-040,DBF-CAT-041,DBF-CAT-042,DBF-CAT-043,DBF-CAT-044,DBF-CAT-045,DBF-CAT-046,DBF-CAT-047,DBF-CAT-048,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053,DBF-CAT-054,DBF-CAT-055,DBF-CAT-056,DBF-CAT-057,DBF-CAT-058,DBF-CAT-059,DBF-CAT-060,DBF-CAT-061,DBF-CAT-062,DBF-CAT-063,DBF-CAT-064,DBF-CAT-065,DBF-CAT-066,DBF-CAT-067,DBF-CAT-068,DBF-CAT-069,DBF-CAT-070,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077,DBF-CAT-078,DBF-CAT-079,DBF-CAT-080,DBF-CAT-081,DBF-CAT-082,DBF-CAT-083,DBF-CAT-084,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089,DBF-CAT-090,DBF-CAT-091,DBF-CAT-092,DBF-CAT-093,DBF-CAT-094,DBF-CAT-095,DBF-CAT-096,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102,DBF-CAT-103,DBF-CAT-104,DBF-CAT-105,DBF-CAT-106,DBF-CAT-107,DBF-CAT-108,DBF-CAT-109,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-115,DBF-CAT-116,DBF-CAT-117,DBF-CAT-118,DBF-CAT-119,DBF-CAT-120,DBF-CAT-121,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129,DBF-CAT-130,DBF-CAT-131,DBF-CAT-132,DBF-CAT-133,DBF-CAT-134,DBF-CAT-135,DBF-CAT-136,DBF-CAT-137,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-144,DBF-CAT-145,DBF-CAT-146,DBF-CAT-147,DBF-CAT-148,DBF-CAT-149,DBF-CAT-150,DBF-CAT-151,DBF-CAT-152,DBF-CAT-153,DBF-CAT-154,DBF-CAT-155,DBF-CAT-156,DBF-CAT-157,DBF-CAT-158,DBF-CAT-159,DBF-CAT-160,DBF-CAT-161,REQ-CAT-049,REQ-CAT-094,REQ-CAT-122 -->
## PHASE DATA-DOM — data and domain (R2)

Twelve entities, split by kind (engine self-check): SUB DATA-DOM-MASTER holds the master entities and their dependents (ENT-CAT-001, 002, 005, 006, 007, 008, 009, 010); SUB DATA-DOM-LOOKUP holds the tenant reference data — the lookup entities ENT-CAT-003, 004 and the config entities ENT-CAT-011, 012 (no transactional entity exists, so DATA-DOM-TRANSACTIONAL is not opened). Each RULE is written in full once, under the entity that owns it (Rule registry, column "owner ENT"); its message, in ar and en, is the Error Catalog row its code names. Every FIELDS row is bound from the db-script by its DBF id.

<!-- SUB:DATA-DOM-MASTER:START traces=DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-016,DBF-CAT-017,DBF-CAT-018,DBF-CAT-019,DBF-CAT-020,DBF-CAT-021,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-025,DBF-CAT-026,DBF-CAT-027,DBF-CAT-028,DBF-CAT-029,DBF-CAT-030,DBF-CAT-031,DBF-CAT-032,DBF-CAT-033,DBF-CAT-034,DBF-CAT-060,DBF-CAT-061,DBF-CAT-062,DBF-CAT-063,DBF-CAT-064,DBF-CAT-065,DBF-CAT-066,DBF-CAT-067,DBF-CAT-068,DBF-CAT-069,DBF-CAT-070,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077,DBF-CAT-078,DBF-CAT-079,DBF-CAT-080,DBF-CAT-081,DBF-CAT-082,DBF-CAT-083,DBF-CAT-084,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089,DBF-CAT-090,DBF-CAT-091,DBF-CAT-092,DBF-CAT-093,DBF-CAT-094,DBF-CAT-095,DBF-CAT-096,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102,DBF-CAT-103,DBF-CAT-104,DBF-CAT-105,DBF-CAT-106,DBF-CAT-107,DBF-CAT-108,DBF-CAT-109,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-115,DBF-CAT-116,DBF-CAT-117,DBF-CAT-118,DBF-CAT-119,DBF-CAT-120,DBF-CAT-121,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129,DBF-CAT-130,DBF-CAT-131,DBF-CAT-132,DBF-CAT-133,DBF-CAT-134,DBF-CAT-135,REQ-CAT-049 -->
### SUB DATA-DOM-MASTER

### ENT-CAT-001 — Item / صنف      kind: master
BINDINGS       table CAT_ITEM · PK ITEM_PK (DBF-CAT-001) · PK generation `identity` → `ITEM_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_ITEM · db-script CAT v2 (P2)
BUSINESS CODE  code · CODE (DBF-CAT-002) · format `ITM-` + 6 digits (series CAT_ITEM, pattern `{PREFIX}-{SEQ:6}`) · uniqueness UQ_CAT_ITEM_CODE (TENANT_ID, CODE) · generation NumberSeriesApi on first save · never in a create/update body, always in the response
DEFAULT FIELDS master → nameAr, nameEn, code, isActiveFl, createdBy, createdAt, updatedBy, updatedAt (all bound below) + the given's tenantId, version
#### FIELDS ENT-CAT-001 — CAT_ITEM
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-001 | itemPk | ITEM_PK | BIGINT | NOT NULL | yes | PK_CAT_ITEM, UQ_CAT_ITEM_PK | المعرّف / Identifier |
| DBF-CAT-002 | code | CODE | VARCHAR(20) | NOT NULL | yes (system) | UQ_CAT_ITEM_CODE | الرمز / Code |
| DBF-CAT-003 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | CHK_CAT_ITEM_NAME_AR, UQ_CAT_ITEM_NAME_AR | الاسم بالعربية / Arabic name |
| DBF-CAT-004 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | الاسم بالإنجليزية / English name |
| DBF-CAT-005 | itemKindCode | ITEM_KIND_CODE | VARCHAR(50) | NOT NULL | after create | — | نوع الصنف / Item kind |
| DBF-CAT-006 | baseUomCode | BASE_UOM_CODE | VARCHAR(50) | NOT NULL | after create | — | وحدة القياس الأساسية / Base unit of measure |
| DBF-CAT-007 | brandCode | BRAND_CODE | VARCHAR(50) | NULL | no | — | العلامة التجارية / Brand |
| DBF-CAT-008 | taxClassCode | TAX_CLASS_CODE | VARCHAR(50) | NOT NULL | no | — | التصنيف الضريبي / Tax class |
| DBF-CAT-009 | einvoiceCodeTypeCode | EINVOICE_CODE_TYPE_CODE | VARCHAR(50) | NULL | no | CHK_CAT_ITEM_EINVOICE_CODE | نوع رمز الفاتورة الإلكترونية / E-invoice code type |
| DBF-CAT-010 | einvoiceCode | EINVOICE_CODE | VARCHAR(100) | NULL | no | CHK_CAT_ITEM_EINVOICE_CODE | رمز الفاتورة الإلكترونية / E-invoice code |
| DBF-CAT-011 | descriptionAr | DESCRIPTION_AR | VARCHAR(2000) | NULL | no | — | الوصف بالعربية / Arabic description |
| DBF-CAT-012 | descriptionEn | DESCRIPTION_EN | VARCHAR(2000) | NULL | no | — | الوصف بالإنجليزية / English description |
| DBF-CAT-013 | publishStateCode | PUBLISH_STATE_CODE | VARCHAR(50) | NOT NULL | yes (PATCH only) | — | حالة النشر / Publish state |
| DBF-CAT-014 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE/PATCH only) | — | نشط / Active |
| DBF-CAT-015 | categoryFk | CATEGORY_FK | BIGINT | NULL | no | FK_CAT_ITEM_CAT_CATEGORY | الفئة / Category |
| DBF-CAT-016 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-017 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-018 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-019 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-020 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-021 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request excludes itemPk, code (a supplied code is rejected — RULE-CAT-004), publishStateCode (set UNPUBLISHED), isActiveFl (set true), tenantId, version, audit · update-request excludes itemPk, publishStateCode, isActiveFl, tenantId, audit; it carries version (optimistic lock) and may echo code / itemKindCode / baseUomCode, which are rejected when they differ from the stored value (RULE-CAT-004, 022, 007) · response ItemDetail / ItemSummary include itemPk, code, every stored field, version, audit, displayNameEn (derived)
LOOKUP FIELDS  itemKindCode · ITEM_KIND_CODE (DBF-CAT-005) · CAT_ITEM_KIND · baseUomCode · BASE_UOM_CODE (DBF-CAT-006) · CAT_UOM · brandCode · BRAND_CODE (DBF-CAT-007) · CAT_BRAND · taxClassCode · TAX_CLASS_CODE (DBF-CAT-008) · CAT_TAX_CLASS · einvoiceCodeTypeCode · EINVOICE_CODE_TYPE_CODE (DBF-CAT-009) · CAT_EINVOICE_ITEM_CODE_TYPE · publishStateCode · PUBLISH_STATE_CODE (DBF-CAT-013) · CAT_PUBLISH_STATE — each stores the code, values read through MdlLookupApi (frontend: API-CAT-052 for the key — ADR-CAT-065)
DOMAIN RULES
- RULE-CAT-001 — Arabic name required · scope ENT-CAT-001, 003, 004, 006, 010 · trigger on create · on update · on import row · statement: The system shall require a nameAr of 1 to 200 characters that contains at least one Arabic letter when a record of scope is saved. · DB CHK_CAT_ITEM_NAME_AR, CHK_CAT_VAR_ATTR_NAME_AR, CHK_CAT_VAR_ATTR_VALUE_NAME_AR, CHK_CAT_CATEGORY_NAME_AR, CHK_CAT_ITEM_MEDIA_NAME_AR (Arabic letter) + domain (presence, length) · layer domain (ItemDomain, VariantAttributeDomain, VariantAttributeValueDomain, CategoryDomain, ItemMediaDomain) · message CAT-422-NAME-AR-INVALID
- RULE-CAT-002 — English name length · scope ENT-CAT-001, 003, 004, 006, 010 · trigger on create · on update · on import row · statement: The system shall prevent saving a nameEn longer than 200 characters. · DB column length 200 + domain · layer domain (the five companions of RULE-CAT-001) · message CAT-422-NAME-EN-TOO-LONG
- RULE-CAT-003 — One active Item per name · scope ENT-CAT-001 · trigger on create · on update · on reactivation · on import row · statement: The system shall prevent saving or reactivating an Item when its nameAr, after Arabic normalisation (tashkeel and tatweel removed; أ إ آ ٱ read as ا, ة as ه, ى as ي; Latin letters lower-cased; leading, trailing and repeated spaces removed — ADR-CAT-062), equals the normalised nameAr of another active Item of the tenant or of an earlier valid row of the same import file. · DB UQ_CAT_ITEM_NAME_AR (partial, on CAT_NORMALIZE_AR(NAME_AR)) + service (QR-CAT-002; same-file rows in memory) · layer service · message CAT-409-ITEM-NAME-DUPLICATE ({code} = the code QR-CAT-002 returns for the other active Item, or, for an earlier row of the same import file, the code the import gave to that row's Item — the next action)
- RULE-CAT-004 — Codes are system-assigned · scope ENT-CAT-001, 002 · trigger on create · on update · statement: The system shall assign the code of an Item from series CAT_ITEM and of a Variant from series CAT_VARIANT on first save, and prevent any request from supplying or changing a code (NUMBERING: document numbers come from the core sequence module). · DB UQ_CAT_ITEM_CODE, UQ_CAT_VARIANT_CODE + domain (ItemDomain / VariantDomain reject a supplied or changed code; the service allocates it) · layer domain · message CAT-422-CODE-SYSTEM-ASSIGNED
- RULE-CAT-005 — No record without a code · scope ENT-CAT-001, 002 · trigger on create · statement: The system shall prevent storing an Item or a Variant when the core number series returns no code. · DB — · layer service (transaction rolled back) · message CAT-500-CODE-NOT-ISSUED
- RULE-CAT-006 — Lookup-backed fields hold active values · scope ENT-CAT-001, 002, 008, 009, 011, 012 · trigger on create · on update · on import row · statement: The system shall require every changed lookup-backed field to hold an active value of its lookup key (A6), and shall keep an unchanged value that was deactivated later. · DB — (D-P2-05) · layer service via MdlLookupApi · message CAT-422-LOOKUP-VALUE-INACTIVE
- RULE-CAT-007 — Base unit fixed after first save · scope ENT-CAT-001 · trigger on update · statement: The system shall prevent changing baseUomCode of a saved Item. · DB — · layer domain (ItemDomain.from) · message CAT-422-BASE-UOM-FIXED
- RULE-CAT-008 — Flag-bound operation refused while its flag is off · scope ENT-CAT-001, 003, 004, 005, 007 · trigger on create of an Item of kind BUNDLE (API-CAT-001) · on Variant generation (API-CAT-006) · on Bundle component save (API-CAT-017) · on create or update of a Variant attribute (API-CAT-025, 027) · on create or update of a Variant attribute value (API-CAT-029, 030) · statement: The system shall prevent creating an Item of kind BUNDLE and saving Bundle components while the `bundles` flag is off, and generating Variants and creating or updating Variant attributes and Variant attribute values while the `variants` flag is off, reading the flag through the FeatureFlags API. Reading, deactivating and reactivating records that already exist stay allowed while a flag is off (API-CAT-002, 004, 005, 009, 026, 028, 031, 041 read no flag), so data that depends on the flag can be cleaned up. · DB — · layer service (`CatCapabilityFlags`) · message CAT-422-CAPABILITY-OFF ({flag} = `variants` or `bundles`)
- RULE-CAT-009 — Unknown flag value refuses the operation · scope ENT-CAT-001, 003, 004, 005, 007 · trigger on any operation RULE-CAT-008 refuses while its flag is off · statement: The system shall prevent an operation that RULE-CAT-008 binds to a flag when the FeatureFlags read of that flag returns no value. · DB — · layer service · message CAT-500-CAPABILITY-UNREADABLE
- RULE-CAT-011 — Bundles cannot be turned off while in use · scope ENT-CAT-001 · trigger on turn-off check of `bundles` · statement: The system shall answer "not allowed" when an active Item of kind BUNDLE exists. · DB — · layer service (QR-CAT-009) · message CAT-200-BUNDLES-IN-USE ({count})
- RULE-CAT-022 — Item kind fixed after first save · scope ENT-CAT-001 · trigger on update · statement: The system shall prevent changing itemKindCode of a saved Item. · DB — · layer domain (ItemDomain.from) · message CAT-422-ITEM-KIND-FIXED
- RULE-CAT-026 — Item Category active · scope ENT-CAT-001 · trigger on create · on update · on import row · on reactivation · statement: The system shall require a changed categoryFk of an Item, and the categoryFk of an Item being reactivated, to be empty or to point to an active Category. · DB FK_CAT_ITEM_CAT_CATEGORY (existence) + service (active, QR-CAT-032) · layer service · message CAT-422-CATEGORY-INACTIVE
- RULE-CAT-041 — Publish state follows the Item · scope ENT-CAT-001 · trigger on publish · on Item deactivation · statement: The system shall prevent publishing an inactive Item or an Item with no active Variant, and set publishStateCode to UNPUBLISHED when an Item is deactivated. · DB — · layer service · message CAT-422-PUBLISH-BLOCKED
- RULE-CAT-042 — E-invoice code pair and format · scope ENT-CAT-001, 002 · trigger on create · on update · on import row · statement: The system shall require einvoiceCodeTypeCode and einvoiceCode to be both present or both empty, a GS1 code of 8 to 14 digits, and an EGS code that starts with "EG-" and has at most 100 characters. · DB CHK_CAT_ITEM_EINVOICE_CODE, CHK_CAT_VARIANT_EINVOICE_CODE (pair) + domain (format) · layer domain (ItemDomain, VariantDomain) · message CAT-422-EINVOICE-CODE-INVALID
- RULE-CAT-044 — Import file acceptance · scope ENT-CAT-001 · trigger on import submission · statement: The system shall require an xlsx file of at most 10 MB with at most 5,000 data rows and a header row equal to the import sheet's 11 columns. · DB — · layer service · message CAT-422-IMPORT-FILE-REJECTED
- RULE-CAT-045 — Import row validation · scope ENT-CAT-001, 009 · trigger on import row · statement: The system shall validate each data row against RULE-CAT-001, RULE-CAT-002, RULE-CAT-003, RULE-CAT-006, RULE-CAT-026, RULE-CAT-029, RULE-CAT-030 and RULE-CAT-042, skip every row that fails, and report its row number, column and reason. · DB — · layer service · message CAT-200-IMPORT-ROW-REJECTED ({row}, {column}, {reason} = the failed rule's message)
- RULE-CAT-046 — Search term minimum · scope ENT-CAT-001 · trigger on catalogue search · statement: The system shall require a search term of at least 2 characters before searching. · DB — · layer service · message CAT-400-SEARCH-TERM-TOO-SHORT
- RULE-CAT-048 — Deactivation keeps records for consumers (ARCH-5) · scope ENT-CAT-001, 002, 006, 007, 008, 009 · trigger on deactivation · statement: The system shall deactivate a SHARED (owner) record by setting isActiveFl to false, never by deletion, keep it readable by identifier for consuming modules, exclude it from new selections and publish a catalogue-change event with change kind DEACTIVATED. · DB — (no DELETE statement exists) · layer service · message CAT-200-DEACTIVATED
- RULE-CAT-049 — Change events only after commit · scope ENT-CAT-001, 002, 005, 007, 008, 009, 010 · trigger on commit or rollback of a catalogue change · statement: The system shall record each catalogue-change event in the same transaction as the change and deliver it only after commit. · DB — · layer service via OutboxPublisher · message CAT-500-CHANGE-NOT-SAVED
- RULE-CAT-050 — A reactivated Item has an active Variant · scope ENT-CAT-001, 002 · trigger on reactivation · statement: The system shall prevent reactivating an Item that has no active Variant. · DB — · layer service (QR-CAT-015 under the Item lock) · message CAT-422-ITEM-NO-ACTIVE-VARIANT
- RULE-CAT-053 — An inactive parent refuses child writes · scope ENT-CAT-001, 002, 008, 009, 010 · trigger on Item update that changes more than categoryFk (API-CAT-003) · on Variant e-invoice override (008) · on Item unit create or update (010, 012) · on barcode create (014, 015) · on image create or update (018, 019) · statement: The system shall prevent changing any field of an inactive Item other than categoryFk, creating or changing an Item unit or image of an inactive Item, and adding a barcode to, or setting the e-invoice override of, a Variant that is inactive or belongs to an inactive Item. Three repair operations stay allowed on an inactive Item, each under its own rules: Variant generation (API-CAT-006 — RULE-CAT-008, 012, 013, 014, 020, 052), an update whose only change is categoryFk (API-CAT-003 — RULE-CAT-026) and the replacement of the Bundle components (API-CAT-017 — RULE-CAT-008, 018, 019) — ADR-CAT-068. Deactivating a child record stays allowed. · DB — · layer service (the Item's isActiveFl DBF-CAT-014, and for 008 / 014 / 015 the Variant's isActiveFl DBF-CAT-027, read under the Item lock QR-CAT-010, so a parallel deactivation of the Item cannot interleave — ADR-CAT-063) · message CAT-422-PARENT-INACTIVE
STATE MACHINE  status columns IS_ACTIVE_FL (DBF-CAT-014) × PUBLISH_STATE_CODE (DBF-CAT-013) · values ACTIVE·UNPUBLISHED, ACTIVE·PUBLISHED, INACTIVE·UNPUBLISHED · initial ACTIVE·UNPUBLISHED (create) · transitions: publish ACTIVE·UNPUBLISHED → ACTIVE·PUBLISHED (owner, API-CAT-005, RULE-CAT-041); unpublish ACTIVE·PUBLISHED → ACTIVE·UNPUBLISHED (owner, API-CAT-005); deactivate ACTIVE·* → INACTIVE·UNPUBLISHED (owner, API-CAT-004, RULE-CAT-021, 041, 048); reactivate INACTIVE·UNPUBLISHED → ACTIVE·UNPUBLISHED (owner, API-CAT-005, stays UNPUBLISHED; guarded by the ACTIVE-state invariants of SRS A7 — RULE-CAT-050, 026, 019, 003, ADR-CAT-059; each broken invariant has a repair allowed while INACTIVE — Variant generation API-CAT-006, a categoryFk-only update API-CAT-003, Bundle components API-CAT-017, or the Category's own reactivation API-CAT-053 — ADR-CAT-068) · terminal none · invalid transition (publish while inactive / without active Variant) → RULE-CAT-041
CROSS-MODULE   none — no XM id touches this entity (read by other modules through `CatalogueReadApi`, PHASE SVC-API)
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         ItemDomain.create(request, defaults) · ItemDomain.from(stored, request) — RULE-CAT-001, 002, 004 (code supplied or changed), 007, 022, 042 (pair and format); the service orchestrates RULE-CAT-003, 005, 006, 008, 009, 019, 021, 026, 041, 048, 050, 053 and the locks
AUDIT          @Audited(entityType = "CAT_ITEM")
REPOSITORY OPS QR-CAT-001, QR-CAT-002, QR-CAT-003, QR-CAT-004, QR-CAT-005, QR-CAT-006, QR-CAT-007, QR-CAT-008, QR-CAT-009, QR-CAT-010
Honours: CON-CAT-001

### ENT-CAT-002 — Variant / متغيّر الصنف      kind: master
BINDINGS       table CAT_VARIANT · PK VARIANT_PK (DBF-CAT-022) · PK generation `identity` → `VARIANT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_VARIANT
BUSINESS CODE  code · CODE (DBF-CAT-023) · format `VAR-` + 6 digits (series CAT_VARIANT) · uniqueness UQ_CAT_VARIANT_CODE (TENANT_ID, CODE) · generation NumberSeriesApi on first save · never in a body, always in the response
DEFAULT FIELDS master → code, isActiveFl, audit stored; nameAr / nameEn are derived at read, not stored (D-15, D-P2-08)
#### FIELDS ENT-CAT-002 — CAT_VARIANT
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-022 | variantPk | VARIANT_PK | BIGINT | NOT NULL | yes | PK_CAT_VARIANT, UQ_CAT_VARIANT_PK, UQ_CAT_VARIANT_PK_ITEM | المعرّف / Identifier |
| DBF-CAT-023 | code | CODE | VARCHAR(20) | NOT NULL | yes (system) | UQ_CAT_VARIANT_CODE | الرمز / Code |
| DBF-CAT-024 | isDefaultFl | IS_DEFAULT_FL | BOOLEAN | NOT NULL | yes (system) | UQ_CAT_VARIANT_DEFAULT | المتغيّر الأساسي / Default variant |
| DBF-CAT-025 | einvoiceCodeTypeCode | EINVOICE_CODE_TYPE_CODE | VARCHAR(50) | NULL | no | CHK_CAT_VARIANT_EINVOICE_CODE | نوع رمز الفاتورة الإلكترونية / E-invoice code type |
| DBF-CAT-026 | einvoiceCode | EINVOICE_CODE | VARCHAR(100) | NULL | no | CHK_CAT_VARIANT_EINVOICE_CODE | رمز الفاتورة الإلكترونية / E-invoice code |
| DBF-CAT-027 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-028 | itemFk | ITEM_FK | BIGINT | NOT NULL | yes (system) | FK_CAT_VARIANT_CAT_ITEM, UQ_CAT_VARIANT_PK_ITEM | الصنف / Item |
| DBF-CAT-029 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-030 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-031 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-032 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-033 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-034 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP no create body (Variants are created with the Item — default — or by generation); update-request (override) carries einvoiceCodeTypeCode, einvoiceCode, version and may echo code (rejected when it differs — RULE-CAT-004) · response VariantSummary / VariantDetail include variantPk, itemFk, code, derived nameAr / nameEn, isDefaultFl, isActiveFl, stored override, effective e-invoice pair, taxClassCode and baseUomCode of the Item, einvoiceUnitCode, attribute values, barcodes, version
LOOKUP FIELDS  einvoiceCodeTypeCode · EINVOICE_CODE_TYPE_CODE (DBF-CAT-025) · CAT_EINVOICE_ITEM_CODE_TYPE
DOMAIN RULES   (RULE-CAT-004, 005, 006, 008, 041, 042, 048, 049 — full text under ENT-CAT-001)
- RULE-CAT-012 — Existing Variants keep their identity · scope ENT-CAT-002, 005, 009 · trigger on Variant generation · statement: The system shall assign to the Item's existing active Variants the first selected value of every attribute they do not yet carry, keeping their variantPk, code and barcodes, before it creates Variants for the remaining combinations. · DB — · layer service · message CAT-201-EXISTING-VARIANTS-KEPT (success notice)
- RULE-CAT-014 — Variant limits per Item · scope ENT-CAT-002, 005 · trigger on Variant generation · statement: The system shall prevent a generation that would give an Item more than 3 Variant attributes or more than 300 active Variants. · DB — · layer service (under the Item lock, QR-CAT-010, QR-CAT-015) · message CAT-422-VARIANT-LIMIT-EXCEEDED
- RULE-CAT-015 — Last active Variant stays active · scope ENT-CAT-001, 002 · trigger on Variant deactivation · statement: The system shall prevent deactivating the last active Variant of an active Item. · DB — · layer service (under the Item lock) · message CAT-409-LAST-ACTIVE-VARIANT
- RULE-CAT-020 — A Bundle has one Variant · scope ENT-CAT-001, 002, 005 · trigger on Variant generation · statement: The system shall prevent generating Variants or assigning Variant attribute values for an Item of kind BUNDLE. · DB — · layer service · message CAT-422-BUNDLE-SINGLE-VARIANT
- RULE-CAT-036 — Label copy limits · scope ENT-CAT-002 · trigger on label request · statement: The system shall require 1 to 999 copies per Variant and at most 5,000 labels per request. · DB — (copy counts are request inputs) · layer service · message CAT-422-LABEL-COPIES-OUT-OF-RANGE
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-027) only — two states, deactivate via API-CAT-009 (RULE-CAT-015, 021, 048), which also deactivates the Variant's assignments (QR-CAT-020) and active barcodes (QR-CAT-072 — ADR-CAT-063); a Variant is reactivated only by regenerating its combination (REQ-CAT-049 creates a new one) — no other transition; an inactive Variant takes no new barcode and no e-invoice override (RULE-CAT-053) · DEFAULT FLAG isDefaultFl (DBF-CAT-024) is held by exactly one Variant of the Item and moves with the active Variants (REQ-CAT-025, ADR-CAT-072): deactivating the default while another Variant is active moves it, in the same transaction under the Item lock, to the remaining active Variant with the lowest code (API-CAT-009); generating Variants for an Item with 0 active Variants gives it to the first Variant created and clears it on the inactive former default (API-CAT-006); the former default's flag is always cleared before the new one is set (QR-CAT-014), so UQ_CAT_VARIANT_DEFAULT holds after each statement; an active Item therefore always has exactly one active default Variant (SRS A7 invariant 5, CON-CAT-002)
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         VariantDomain.create(item, code) · VariantDomain.from(stored, request) — RULE-CAT-004 (code supplied or changed), 042 (override pair and format); the service orchestrates RULE-CAT-012, 013, 014, 015, 020, 021, 036, 052
AUDIT          @Audited(entityType = "CAT_VARIANT")
REPOSITORY OPS QR-CAT-011, QR-CAT-012, QR-CAT-013, QR-CAT-014, QR-CAT-015, QR-CAT-016, QR-CAT-017, QR-CAT-075
Honours: CON-CAT-002

### ENT-CAT-005 — Variant attribute assignment / إسناد قيمة خاصية للمتغيّر      kind: master (dependent of ENT-CAT-002, ADR-CAT-039)
BINDINGS       table CAT_VAR_ATTR_ASSIGN · PK VARIANT_ATTRIBUTE_ASSIGNMENT_PK (DBF-CAT-060) · PK generation `identity` → `VARIANT_ATTRIBUTE_ASSIGNMENT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_VAR_ATTR_ASSIGN
BUSINESS CODE  none
DEFAULT FIELDS dependent master → isActiveFl + audit (no own code or name: it shows the attribute and value names)
#### FIELDS ENT-CAT-005 — CAT_VAR_ATTR_ASSIGN
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-060 | variantAttributeAssignmentPk | VARIANT_ATTRIBUTE_ASSIGNMENT_PK | BIGINT | NOT NULL | yes | PK_CAT_VAR_ATTR_ASSIGN | المعرّف / Identifier |
| DBF-CAT-061 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (follows the Variant) | — | نشط / Active |
| DBF-CAT-062 | variantFk | VARIANT_FK | BIGINT | NOT NULL | yes (system) | FK_CAT_VAR_ATTR_ASSIGN_CAT_VARIANT, UQ_CAT_VAR_ATTR_ASSIGN_ATTR | المتغيّر / Variant |
| DBF-CAT-063 | variantAttributeFk | VARIANT_ATTRIBUTE_FK | BIGINT | NOT NULL | yes (system) | FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR, UQ_CAT_VAR_ATTR_ASSIGN_ATTR | الخاصية / Attribute |
| DBF-CAT-064 | variantAttributeValueFk | VARIANT_ATTRIBUTE_VALUE_FK | BIGINT | NOT NULL | yes (system) | FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE | القيمة / Value |
| DBF-CAT-065 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-066 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-067 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-068 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-069 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-070 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP no own request: rows are written by Variant generation (API-CAT-006) and deactivated with their Variant (API-CAT-009) · response AttributeValueView inside every Variant view
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-008, 009, 012, 014, 020 — full text under ENT-CAT-001 / ENT-CAT-002)
- RULE-CAT-010 — Variants cannot be turned off while in use · scope ENT-CAT-001, 002, 005 · trigger on turn-off check of `variants` · statement: The system shall answer "not allowed" when an active Variant attribute assignment exists on an active Variant of an active Item. · DB — · layer service (QR-CAT-009) · message CAT-200-VARIANTS-IN-USE ({count})
- RULE-CAT-013 — One value per attribute and unique combinations · scope ENT-CAT-002, 005 · trigger on Variant generation · statement: The system shall require each active Variant of an Item to carry exactly one value of each attribute used by the Item, and prevent two active Variants of the same Item from carrying the same combination; a generation that omits an attribute the Item already uses is refused under RULE-CAT-052 first. · DB UQ_CAT_VAR_ATTR_ASSIGN_ATTR (one active value per attribute) + FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE (value of the same attribute) + service (unique combination, under the Item lock) · layer service · message CAT-409-VARIANT-COMBINATION-DUPLICATE
- RULE-CAT-052 — Generation names every attribute the Item uses · scope ENT-CAT-002, 005 · trigger on Variant generation · statement: The system shall require a generation request to select at least one active value of every attribute that the Item's active Variants already carry. · DB — · layer service (QR-CAT-019 under the Item lock, checked before RULE-CAT-013 and RULE-CAT-014) · message CAT-422-VARIANT-ATTRIBUTE-MISSING
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-061) follows its Variant
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, DELETE
DOMAIN         VariantAttributeAssignmentDomain.create(variant, attribute, value) — the value belongs to the attribute (RULE-CAT-013, single record); combinations, RULE-CAT-014 and RULE-CAT-052 are cross-record and stay in the service
AUDIT          @Audited(entityType = "CAT_VAR_ATTR_ASSIGN")
REPOSITORY OPS QR-CAT-018, QR-CAT-019, QR-CAT-020, QR-CAT-021

### ENT-CAT-006 — Category / فئة      kind: master
BINDINGS       table CAT_CATEGORY · PK CATEGORY_PK (DBF-CAT-071) · PK generation `identity` → `CATEGORY_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_CATEGORY
BUSINESS CODE  code · CODE (DBF-CAT-072) · owner-entered, 1–30 Latin letters, digits or hyphens (CHK_CAT_CATEGORY_CODE) · uniqueness UQ_CAT_CATEGORY_CODE (TENANT_ID, CODE) · fixed after creation (contract CON-CAT-003)
DEFAULT FIELDS master → nameAr, nameEn, code, isActiveFl + audit
#### FIELDS ENT-CAT-006 — CAT_CATEGORY
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-071 | categoryPk | CATEGORY_PK | BIGINT | NOT NULL | yes | PK_CAT_CATEGORY, UQ_CAT_CATEGORY_PK | المعرّف / Identifier |
| DBF-CAT-072 | code | CODE | VARCHAR(30) | NOT NULL | after create | UQ_CAT_CATEGORY_CODE, CHK_CAT_CATEGORY_CODE | الرمز / Code |
| DBF-CAT-073 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | UQ_CAT_CATEGORY_NAME_AR, CHK_CAT_CATEGORY_NAME_AR | الاسم بالعربية / Arabic name |
| DBF-CAT-074 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | الاسم بالإنجليزية / English name |
| DBF-CAT-075 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-076 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-077 | parentCategoryFk | PARENT_CATEGORY_FK | BIGINT | NULL | no | FK_CAT_CATEGORY_CAT_CATEGORY, CHK_CAT_CATEGORY_PARENT_CATEGORY_FK | الفئة الأم / Parent category |
| DBF-CAT-078 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-079 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-080 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-081 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-082 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-083 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: code, nameAr, nameEn, parentCategoryFk, sortOrder (default next under the parent) · update-request: nameAr, nameEn, parentCategoryFk, sortOrder, version (code excluded) · response CategoryView / CategoryNode
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-001, 002, 048 — full text under ENT-CAT-001)
- RULE-CAT-023 — Category hierarchy · scope ENT-CAT-006 · trigger on create · on update · on reactivation (API-CAT-053) · statement: The system shall require the parent Category to be active, to be neither the Category itself nor one of its sub-categories, and the resulting depth to be at most 5 levels; a Category being reactivated must have an empty or active parent. · DB CHK_CAT_CATEGORY_PARENT_CATEGORY_FK (not itself) + service (active, no cycle, depth — QR-CAT-037 under the tree lock) · layer service · message CAT-422-CATEGORY-PARENT-INVALID
- RULE-CAT-024 — Category code and sibling name unique · scope ENT-CAT-006 · trigger on create · on update · on reactivation (API-CAT-053) · statement: The system shall require a code of 1 to 30 Latin letters, digits or hyphens unique per tenant, and a nameAr unique among the active Categories of the same parent; a Category being reactivated keeps its code and must not carry the nameAr of another active Category of its parent. · DB UQ_CAT_CATEGORY_CODE, UQ_CAT_CATEGORY_NAME_AR, CHK_CAT_CATEGORY_CODE · layer service (QR-CAT-033) + constraint translation · message CAT-409-CATEGORY-DUPLICATE
- RULE-CAT-025 — Category in use stays active · scope ENT-CAT-006 · trigger on deactivation · statement: The system shall prevent deactivating a Category that has active sub-categories or active Items. · DB — · layer service (QR-CAT-038 under the Category lock) · message CAT-409-CATEGORY-IN-USE ({count})
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-076) only — deactivate via API-CAT-024 (RULE-CAT-025), reactivate via API-CAT-053 (RULE-CAT-023, 024 — ADR-CAT-069); the code is kept, so a mistaken deactivation never loses it
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         CategoryDomain.create(request) · CategoryDomain.from(stored, request) — RULE-CAT-001, 002, 024 (code format), 023 (not its own parent); active parent, cycle, depth, sibling names and RULE-CAT-025 in the service
AUDIT          @Audited(entityType = "CAT_CATEGORY")
REPOSITORY OPS QR-CAT-032, QR-CAT-033, QR-CAT-034, QR-CAT-035, QR-CAT-036, QR-CAT-037, QR-CAT-038, QR-CAT-073
Honours: CON-CAT-003

### ENT-CAT-007 — Bundle component / مكوّن الحزمة      kind: master (dependent of a BUNDLE Item, ADR-CAT-039)
BINDINGS       table CAT_BUNDLE_COMP · PK BUNDLE_COMPONENT_PK (DBF-CAT-084) · PK generation `identity` → `BUNDLE_COMPONENT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_BUNDLE_COMP
BUSINESS CODE  none — business key BUNDLE_ITEM_FK + COMPONENT_VARIANT_FK among active lines (UQ_CAT_BUNDLE_COMP_VARIANT)
DEFAULT FIELDS dependent master → isActiveFl + audit
#### FIELDS ENT-CAT-007 — CAT_BUNDLE_COMP
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-084 | bundleComponentPk | BUNDLE_COMPONENT_PK | BIGINT | NOT NULL | yes | PK_CAT_BUNDLE_COMP | المعرّف / Identifier |
| DBF-CAT-085 | quantity | QUANTITY | NUMERIC(18,3) | NOT NULL | no | CHK_CAT_BUNDLE_COMP_QUANTITY | الكمية / Quantity |
| DBF-CAT-086 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-087 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (line removal) | UQ_CAT_BUNDLE_COMP_VARIANT (partial) | نشط / Active |
| DBF-CAT-088 | bundleItemFk | BUNDLE_ITEM_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_BUNDLE_COMP_CAT_ITEM | الحزمة / Bundle |
| DBF-CAT-089 | componentVariantFk | COMPONENT_VARIANT_FK | BIGINT | NOT NULL | after create | FK_CAT_BUNDLE_COMP_CAT_VARIANT, UQ_CAT_BUNDLE_COMP_VARIANT | المتغيّر المكوِّن / Component variant |
| DBF-CAT-090 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-091 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-092 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-093 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-094 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-095 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP line-request (inside ItemCreateRequest and BundleComponentsRequest): componentVariantFk, quantity (default 1), sortOrder (default line order) · response BundleComponentView with componentVariantCode (derived)
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-008, 009, 048, 049 — full text under ENT-CAT-001)
- RULE-CAT-018 — Bundle has components with quantities · scope ENT-CAT-001, 007 · trigger on Bundle save · statement: The system shall require at least one active component line per BUNDLE Item, each with a quantity above zero and at most 3 decimals. · DB CHK_CAT_BUNDLE_COMP_QUANTITY + domain (BundleComponentDomain: quantity > 0, scale ≤ 3) + service (≥ 1 active line) · layer domain + service · message CAT-422-BUNDLE-COMPONENTS-REQUIRED
- RULE-CAT-019 — Valid Bundle components · scope ENT-CAT-002, 007 · trigger on Bundle save · on component line change · on reactivation of the Bundle · statement: The system shall require each component Variant to be active, to belong to an active STANDARD Item, and to appear once among the active lines of the Bundle. · DB UQ_CAT_BUNDLE_COMP_VARIANT + service (active, STANDARD — QR-CAT-017 FOR SHARE) · layer service · message CAT-422-BUNDLE-COMPONENT-INVALID
- RULE-CAT-021 — Component of an active Bundle stays active · scope ENT-CAT-001, 002, 007 · trigger on Variant deactivation · on Item deactivation · statement: The system shall prevent deactivating a Variant, or the Item that owns it, while the Variant is an active component of an active Bundle. · DB — · layer service (QR-CAT-042 under the Item lock) · message CAT-409-VARIANT-IN-ACTIVE-BUNDLE ({count})
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-087) only — a removed line is deactivated (API-CAT-017)
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         BundleComponentDomain.create(request) · BundleComponentDomain.from(stored, request) — RULE-CAT-018 (quantity > 0 with ≤ 3 decimals); line count, RULE-CAT-019 and RULE-CAT-021 in the service
AUDIT          @Audited(entityType = "CAT_BUNDLE_COMP")
REPOSITORY OPS QR-CAT-039, QR-CAT-040, QR-CAT-041, QR-CAT-042
Honours: CON-CAT-006

### ENT-CAT-008 — Item unit / وحدة الصنف      kind: master (dependent of ENT-CAT-001, ADR-CAT-039)
BINDINGS       table CAT_ITEM_UNIT · PK ITEM_UNIT_PK (DBF-CAT-096) · PK generation `identity` → `ITEM_UNIT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_ITEM_UNIT
BUSINESS CODE  none — business key ITEM_FK + UOM_CODE among active units (UQ_CAT_ITEM_UNIT_UOM)
DEFAULT FIELDS dependent master → isActiveFl + audit
#### FIELDS ENT-CAT-008 — CAT_ITEM_UNIT
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-096 | itemUnitPk | ITEM_UNIT_PK | BIGINT | NOT NULL | yes | PK_CAT_ITEM_UNIT | المعرّف / Identifier |
| DBF-CAT-097 | uomCode | UOM_CODE | VARCHAR(50) | NOT NULL | after create | UQ_CAT_ITEM_UNIT_UOM | وحدة القياس / Unit of measure |
| DBF-CAT-098 | conversionFactor | CONVERSION_FACTOR | NUMERIC(18,6) | NOT NULL | after create | CHK_CAT_ITEM_UNIT_CONVERSION_FACTOR | معامل التحويل / Conversion factor |
| DBF-CAT-099 | isPurchaseFl | IS_PURCHASE_FL | BOOLEAN | NOT NULL | no | — | للشراء / Used for purchasing |
| DBF-CAT-100 | isSalesFl | IS_SALES_FL | BOOLEAN | NOT NULL | no | — | للبيع / Used for selling |
| DBF-CAT-101 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-102 | itemFk | ITEM_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_ITEM_UNIT_CAT_ITEM | الصنف / Item |
| DBF-CAT-103 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-104 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-105 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-106 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-107 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-108 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: uomCode, conversionFactor, isPurchaseFl (default true), isSalesFl (default true) · update-request: isPurchaseFl, isSalesFl, version; may echo conversionFactor, rejected when it differs (RULE-CAT-028) · response ItemUnitView with einvoiceUnitCode (derived)
LOOKUP FIELDS  uomCode · UOM_CODE (DBF-CAT-097) · CAT_UOM
DOMAIN RULES   (RULE-CAT-006, 048, 049 — full text under ENT-CAT-001)
- RULE-CAT-027 — Valid Item unit · scope ENT-CAT-008 · trigger on create · statement: The system shall require an Item unit's uomCode to differ from the Item's baseUomCode and from the uomCode of its other active Item units, and its conversionFactor to be above zero with at most 6 decimals. · DB UQ_CAT_ITEM_UNIT_UOM, CHK_CAT_ITEM_UNIT_CONVERSION_FACTOR + domain (ItemUnitDomain.create: differs from the Item's base unit, factor > 0 with scale ≤ 6) + service (differs from the other active units) · layer domain + service · message CAT-422-ITEM-UNIT-INVALID
- RULE-CAT-028 — Conversion factor fixed after saving · scope ENT-CAT-008 · trigger on update · statement: The system shall prevent changing conversionFactor of a saved Item unit. · DB — · layer domain (ItemUnitDomain.from) · message CAT-422-CONVERSION-FACTOR-FIXED
- RULE-CAT-051 — Item unit used by active barcodes stays active · scope ENT-CAT-008, 009 · trigger on Item unit deactivation · statement: The system shall prevent deactivating an Item unit while active barcodes of the Variants of the same Item are bound to its uomCode. · DB — · layer service (QR-CAT-071 under the Item lock — ADR-CAT-060) · message CAT-409-ITEM-UNIT-HAS-BARCODES ({count})
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-101) only — deactivate via API-CAT-013 (RULE-CAT-051)
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         ItemUnitDomain.create(request, item) · ItemUnitDomain.from(stored, request) — RULE-CAT-027 (differs from the Item's baseUomCode; factor > 0 with ≤ 6 decimals), 028; other active units and RULE-CAT-051 in the service
AUDIT          @Audited(entityType = "CAT_ITEM_UNIT")
REPOSITORY OPS QR-CAT-043, QR-CAT-044, QR-CAT-045, QR-CAT-046, QR-CAT-071
Honours: CON-CAT-004

### ENT-CAT-009 — Barcode / باركود      kind: master (dependent of ENT-CAT-002, ADR-CAT-039)
BINDINGS       table CAT_BARCODE · PK BARCODE_PK (DBF-CAT-109) · PK generation `identity` → `BARCODE_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_BARCODE
BUSINESS CODE  barcodeValue · BARCODE_VALUE (DBF-CAT-110) serves as the record's code · GTIN / SCALE entered; INTERNAL = `04` + 10 digits of series CAT_INTERNAL_BARCODE + GS1 check digit (D-05) · uniqueness UQ_CAT_BARCODE_VALUE (TENANT_ID, BARCODE_VALUE) among active rows · read-only after save
DEFAULT FIELDS dependent master → isActiveFl + audit
#### FIELDS ENT-CAT-009 — CAT_BARCODE
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-109 | barcodePk | BARCODE_PK | BIGINT | NOT NULL | yes | PK_CAT_BARCODE | المعرّف / Identifier |
| DBF-CAT-110 | barcodeValue | BARCODE_VALUE | VARCHAR(48) | NOT NULL | after create | UQ_CAT_BARCODE_VALUE, CHK_CAT_BARCODE_BARCODE_VALUE | الباركود / Barcode |
| DBF-CAT-111 | barcodeTypeCode | BARCODE_TYPE_CODE | VARCHAR(50) | NOT NULL | after create | — | نوع الباركود / Barcode type |
| DBF-CAT-112 | uomCode | UOM_CODE | VARCHAR(50) | NOT NULL | after create | — | وحدة القياس / Unit of measure |
| DBF-CAT-113 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-114 | variantFk | VARIANT_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_BARCODE_CAT_VARIANT | المتغيّر / Variant |
| DBF-CAT-115 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-116 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-117 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-118 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-119 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-120 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: barcodeValue, barcodeTypeCode, uomCode (default the Item's baseUomCode) · internal-request: uomCode only · no update · response BarcodeView
LOOKUP FIELDS  barcodeTypeCode · BARCODE_TYPE_CODE (DBF-CAT-111) · CAT_BARCODE_TYPE · uomCode · UOM_CODE (DBF-CAT-112) · CAT_UOM
DOMAIN RULES   (RULE-CAT-006, 048, 049 — full text under ENT-CAT-001)
- RULE-CAT-029 — Barcode unique among active barcodes · scope ENT-CAT-009 · trigger on create · on import row · statement: The system shall prevent saving a barcode whose barcodeValue equals the barcodeValue of an active barcode of the tenant or of an earlier valid row of the same import file, with SCALE barcodes compared only with SCALE barcodes. · DB UQ_CAT_BARCODE_VALUE (D-P2-11: lengths keep SCALE apart) + service (QR-CAT-048 returns the owning Item code; same-file rows in memory) · layer service · message CAT-409-BARCODE-DUPLICATE ({code} = the code QR-CAT-048 returns for the Item that owns the active barcode, or, for an earlier row of the same import file, the code the import gave to that row's Item)
- RULE-CAT-030 — GTIN format · scope ENT-CAT-009 · trigger on create · on import row · statement: The system shall require a GTIN barcodeValue of 8, 12, 13 or 14 digits whose last digit is the GS1 check digit. · DB CHK_CAT_BARCODE_BARCODE_VALUE (digits) + domain (BarcodeDomain: length, GS1 check digit) · layer domain · message CAT-422-GTIN-INVALID
- RULE-CAT-031 — Barcode unit belongs to the Item · scope ENT-CAT-009 · trigger on create · statement: The system shall require a barcode's uomCode to equal the Item's baseUomCode or the uomCode of an active Item unit of the same Item. · DB — · layer service (QR-CAT-044) · message CAT-422-BARCODE-UNIT-INVALID
- RULE-CAT-032 — Barcodes avoid scale prefixes · scope ENT-CAT-009 · trigger on create (GTIN, INTERNAL) · on scale label layout create · statement: The system shall prevent a GTIN or INTERNAL barcodeValue that begins with the key of an active scale label layout. · DB — · layer service (QR-CAT-059 / QR-CAT-053 under the scale-prefix lock) · message CAT-409-BARCODE-SCALE-PREFIX ({prefix})
- RULE-CAT-033 — Scale code length · scope ENT-CAT-009 · trigger on create (SCALE) · statement: The system shall require a SCALE barcodeValue to consist of digits only, with a length equal to the scaleCodeLength of an active scale label layout. · DB CHK_CAT_BARCODE_BARCODE_VALUE + service (length, QR-CAT-059) · layer service · message CAT-422-SCALE-CODE-LENGTH ({length})
- RULE-CAT-035 — Scale label decoding · scope ENT-CAT-009, 011 · trigger on barcode lookup of a 13-digit value with an active layout prefix · statement: The system shall require a valid EAN-13 check digit and an embedded scale code equal to an active SCALE barcodeValue before returning a Variant. · DB — · layer service · message CAT-422-SCALE-LABEL-UNREADABLE
- RULE-CAT-037 — Labelled Variant carries a barcode · scope ENT-CAT-002, 009 · trigger on label request · statement: The system shall require every Variant of a label request to carry at least one active barcode. · DB — · layer service (QR-CAT-047) · message CAT-422-LABEL-VARIANT-NO-BARCODE ({count}; the Variants are listed)
- RULE-CAT-047 — Barcode not found · scope ENT-CAT-009 · trigger on barcode lookup · statement: The system shall return no Variant, with a next action, when no active barcode of an active Variant of an active Item and no active scale label layout matches the value (ADR-CAT-059). · DB — · layer service · message CAT-404-BARCODE-NOT-FOUND
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-113) only — deactivate via API-CAT-016, or together with its Variant via API-CAT-009 (QR-CAT-072 — ADR-CAT-063); an inactive barcode never matches a lookup and no longer holds its value for RULE-CAT-029
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, DELETE
DOMAIN         BarcodeDomain.create(request, item, internalDigits) — RULE-CAT-030 (GTIN length and GS1 check digit), the INTERNAL value (04 + digits + check digit), digits-only SCALE values; uniqueness, unit, scale prefixes and lengths (RULE-CAT-029, 031, 032, 033) in the service
AUDIT          @Audited(entityType = "CAT_BARCODE")
REPOSITORY OPS QR-CAT-047, QR-CAT-048, QR-CAT-049, QR-CAT-050, QR-CAT-051, QR-CAT-052, QR-CAT-053, QR-CAT-072
Honours: CON-CAT-005

### ENT-CAT-010 — Item media / وسائط الصنف      kind: master (dependent of ENT-CAT-001, ADR-CAT-039)
BINDINGS       table CAT_ITEM_MEDIA · PK ITEM_MEDIA_PK (DBF-CAT-121) · PK generation `identity` → `ITEM_MEDIA_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_ITEM_MEDIA
BUSINESS CODE  none — the file is a given FileDocument (FILE_DOCUMENT_REF, DBF-CAT-122, given soft read, no XM)
DEFAULT FIELDS dependent master → nameAr, nameEn, isActiveFl + audit
#### FIELDS ENT-CAT-010 — CAT_ITEM_MEDIA
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-121 | itemMediaPk | ITEM_MEDIA_PK | BIGINT | NOT NULL | yes | PK_CAT_ITEM_MEDIA | المعرّف / Identifier |
| DBF-CAT-122 | fileDocumentRef | FILE_DOCUMENT_REF | BIGINT | NOT NULL | yes (system) | — (given soft read) | ملف الصورة / Image file |
| DBF-CAT-123 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | CHK_CAT_ITEM_MEDIA_NAME_AR | وصف الصورة بالعربية / Arabic image description |
| DBF-CAT-124 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | وصف الصورة بالإنجليزية / English image description |
| DBF-CAT-125 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-126 | isPrimaryFl | IS_PRIMARY_FL | BOOLEAN | NOT NULL | no | UQ_CAT_ITEM_MEDIA_PRIMARY | الصورة الرئيسية / Main image |
| DBF-CAT-127 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-128 | itemFk | ITEM_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_ITEM_MEDIA_CAT_ITEM | الصنف / Item |
| DBF-CAT-129 | variantFk | VARIANT_FK | BIGINT | NULL | no | FK_CAT_ITEM_MEDIA_CAT_VARIANT | المتغيّر / Variant |
| DBF-CAT-130 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-131 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-132 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-133 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-134 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-135 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request (JSON, ItemMediaCreateRequest): fileDocumentRef (the FILE document the client uploaded through the given FILE endpoint — ADR-CAT-056), nameAr (default the Item's nameAr), nameEn, variantFk · update-request: sortOrder, isPrimaryFl, nameAr, nameEn, variantFk, version · response MediaView
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-001, 002, 049 — full text under ENT-CAT-001)
- RULE-CAT-038 — Image file and count · scope ENT-CAT-010 · trigger on upload · statement: The system shall require a JPG, PNG or WEBP file of at most 5 MB that is held in the public image storage of the caller's tenant, and at most 20 active media records per Item. A FILE document held in any other storage (for example the private storage) is refused, because the storefront could not show it (UXD-CAT-003, CON-CAT-015). One fileDocumentRef may be attached to several media records — of the same Item or of different Items — because removing an image never deletes its file (ADR-CAT-056 §4); the 20-record limit counts media records, not distinct files. · DB — · layer service (FileDocumentLookupApi type, size and storage; QR-CAT-054 count under the Item lock) · message CAT-422-IMAGE-NOT-ACCEPTED
- RULE-CAT-039 — Exactly one main image · scope ENT-CAT-010 · trigger on upload · on update · on removal · statement: The system shall keep exactly one active media record with isPrimaryFl true for every Item that has active media, by marking the first image primary, clearing the former primary when another is chosen, and promoting the lowest sortOrder when the primary is removed. · DB UQ_CAT_ITEM_MEDIA_PRIMARY (at most one) + service (exactly one, under the Item lock) · layer service · message CAT-200-PRIMARY-IMAGE-REPLACED (success notice)
- RULE-CAT-040 — Image Variant belongs to the Item · scope ENT-CAT-010 · trigger on upload · on update · statement: The system shall require a media record's variantFk, when set, to point to a Variant of the same Item. · DB FK_CAT_ITEM_MEDIA_CAT_VARIANT (composite TENANT_ID, VARIANT_FK, ITEM_FK) + service · layer service · message CAT-422-MEDIA-VARIANT-MISMATCH
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-127) only — removal deactivates (API-CAT-020)
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         ItemMediaDomain.create(request, item) · ItemMediaDomain.from(stored, request) — RULE-CAT-001, 002 (descriptions; nameAr defaults to the Item's nameAr); RULE-CAT-038, 039 and 040 in the service
AUDIT          @Audited(entityType = "CAT_ITEM_MEDIA")
REPOSITORY OPS QR-CAT-054, QR-CAT-055, QR-CAT-056, QR-CAT-057, QR-CAT-058
<!-- SUB:DATA-DOM-MASTER:END -->

<!-- SUB:DATA-DOM-LOOKUP:START traces=DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-040,DBF-CAT-041,DBF-CAT-042,DBF-CAT-043,DBF-CAT-044,DBF-CAT-045,DBF-CAT-046,DBF-CAT-047,DBF-CAT-048,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053,DBF-CAT-054,DBF-CAT-055,DBF-CAT-056,DBF-CAT-057,DBF-CAT-058,DBF-CAT-059,DBF-CAT-136,DBF-CAT-137,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-144,DBF-CAT-145,DBF-CAT-146,DBF-CAT-147,DBF-CAT-148,DBF-CAT-149,DBF-CAT-150,DBF-CAT-151,DBF-CAT-152,DBF-CAT-153,DBF-CAT-154,DBF-CAT-155,DBF-CAT-156,DBF-CAT-157,DBF-CAT-158,DBF-CAT-159,DBF-CAT-160,DBF-CAT-161,REQ-CAT-094,REQ-CAT-122 -->
### SUB DATA-DOM-LOOKUP

### ENT-CAT-003 — Variant attribute / خاصية المتغيّر      kind: lookup
BINDINGS       table CAT_VAR_ATTR · PK VARIANT_ATTRIBUTE_PK (DBF-CAT-035) · PK generation `identity` → `VARIANT_ATTRIBUTE_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_VAR_ATTR
BUSINESS CODE  code · CODE (DBF-CAT-036) · owner-entered, 1–30 Latin letters, digits or hyphens (CHK_CAT_VAR_ATTR_CODE) · uniqueness UQ_CAT_VAR_ATTR_CODE (TENANT_ID, CODE) · fixed after creation
DEFAULT FIELDS lookup → code, nameAr, nameEn, sortOrder, isActiveFl (+ the given's tenantId, version, audit — D-P2-02)
#### FIELDS ENT-CAT-003 — CAT_VAR_ATTR
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-035 | variantAttributePk | VARIANT_ATTRIBUTE_PK | BIGINT | NOT NULL | yes | PK_CAT_VAR_ATTR, UQ_CAT_VAR_ATTR_PK | المعرّف / Identifier |
| DBF-CAT-036 | code | CODE | VARCHAR(30) | NOT NULL | after create | UQ_CAT_VAR_ATTR_CODE, CHK_CAT_VAR_ATTR_CODE | الرمز / Code |
| DBF-CAT-037 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | UQ_CAT_VAR_ATTR_NAME_AR, CHK_CAT_VAR_ATTR_NAME_AR | الاسم بالعربية / Arabic name |
| DBF-CAT-038 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | الاسم بالإنجليزية / English name |
| DBF-CAT-039 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-040 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-041 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-042 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-043 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-044 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-045 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-046 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: code, nameAr, nameEn, sortOrder (default next) · update-request: nameAr, nameEn, sortOrder, version · response VariantAttributeView with its values
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-001, 002, 008 — full text under ENT-CAT-001)
- RULE-CAT-016 — Attribute and value codes and names unique · scope ENT-CAT-003, 004 · trigger on create · on update · on reactivation (API-CAT-054, 055) · statement: The system shall require a code of 1 to 30 Latin letters, digits or hyphens, unique per tenant for a Variant attribute and per attribute for a value, and a nameAr unique among the active records of the same scope; a record being reactivated keeps its code and must not carry the nameAr of another active record of its scope. · DB UQ_CAT_VAR_ATTR_CODE, UQ_CAT_VAR_ATTR_NAME_AR, UQ_CAT_VAR_ATTR_VALUE_CODE, UQ_CAT_VAR_ATTR_VALUE_NAME_AR, CHK_CAT_VAR_ATTR_CODE, CHK_CAT_VAR_ATTR_VALUE_CODE · layer service (QR-CAT-023 / QR-CAT-028) + constraint translation · message CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
- RULE-CAT-017 — Attribute or value in use stays active · scope ENT-CAT-003, 004 · trigger on deactivation · statement: The system shall prevent deactivating a Variant attribute or value that an active assignment of an active Variant carries. · DB — · layer service (QR-CAT-021 under the row lock) · message CAT-409-ATTRIBUTE-VALUE-IN-USE ({count})
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-040) only — deactivate via API-CAT-028 (RULE-CAT-017), reactivate via API-CAT-054 (RULE-CAT-016 — ADR-CAT-069); its values keep their own state
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         VariantAttributeDomain.create(request) · VariantAttributeDomain.from(stored, request) — RULE-CAT-001, 002, 016 (code format); uniqueness and RULE-CAT-017 in the service
AUDIT          @Audited(entityType = "CAT_VAR_ATTR")
REPOSITORY OPS QR-CAT-022, QR-CAT-023, QR-CAT-024, QR-CAT-025, QR-CAT-026

### ENT-CAT-004 — Variant attribute value / قيمة خاصية المتغيّر      kind: lookup
BINDINGS       table CAT_VAR_ATTR_VALUE · PK VARIANT_ATTRIBUTE_VALUE_PK (DBF-CAT-047) · PK generation `identity` → `VARIANT_ATTRIBUTE_VALUE_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_VAR_ATTR_VALUE
BUSINESS CODE  code · CODE (DBF-CAT-048) · owner-entered (CHK_CAT_VAR_ATTR_VALUE_CODE) · uniqueness UQ_CAT_VAR_ATTR_VALUE_CODE (TENANT_ID, VARIANT_ATTRIBUTE_FK, CODE) · fixed after creation
DEFAULT FIELDS lookup → code, nameAr, nameEn, sortOrder, isActiveFl (+ tenantId, version, audit)
#### FIELDS ENT-CAT-004 — CAT_VAR_ATTR_VALUE
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-047 | variantAttributeValuePk | VARIANT_ATTRIBUTE_VALUE_PK | BIGINT | NOT NULL | yes | PK_CAT_VAR_ATTR_VALUE, UQ_CAT_VAR_ATTR_VALUE_PK_ATTR | المعرّف / Identifier |
| DBF-CAT-048 | code | CODE | VARCHAR(30) | NOT NULL | after create | UQ_CAT_VAR_ATTR_VALUE_CODE, CHK_CAT_VAR_ATTR_VALUE_CODE | الرمز / Code |
| DBF-CAT-049 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | UQ_CAT_VAR_ATTR_VALUE_NAME_AR, CHK_CAT_VAR_ATTR_VALUE_NAME_AR | الاسم بالعربية / Arabic name |
| DBF-CAT-050 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | الاسم بالإنجليزية / English name |
| DBF-CAT-051 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-052 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-053 | variantAttributeFk | VARIANT_ATTRIBUTE_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_VAR_ATTR_VALUE_CAT_VAR_ATTR, UQ_CAT_VAR_ATTR_VALUE_PK_ATTR | الخاصية / Attribute |
| DBF-CAT-054 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-055 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-056 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-057 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-058 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-059 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: code, nameAr, nameEn, sortOrder (default next within the attribute); variantAttributeFk from the path · update-request: nameAr, nameEn, sortOrder, version · response VariantAttributeValueView
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-001, 002, 008 — under ENT-CAT-001; RULE-CAT-016, 017 — under ENT-CAT-003)
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-052) only — deactivate via API-CAT-031 (RULE-CAT-017), reactivate via API-CAT-055 under an active attribute (RULE-CAT-016 — ADR-CAT-069)
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         VariantAttributeValueDomain.create(request, attribute) · VariantAttributeValueDomain.from(stored, request) — RULE-CAT-001, 002, 016 (code format); uniqueness and RULE-CAT-017 in the service
AUDIT          @Audited(entityType = "CAT_VAR_ATTR_VALUE")
REPOSITORY OPS QR-CAT-027, QR-CAT-028, QR-CAT-029, QR-CAT-030, QR-CAT-031

### ENT-CAT-011 — Scale label layout / إعداد ملصق الميزان      kind: config
BINDINGS       table CAT_SCALE_LABEL_LAYOUT · PK SCALE_LABEL_LAYOUT_PK (DBF-CAT-136) · PK generation `identity` → `SCALE_LABEL_LAYOUT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_SCALE_LABEL_LAYOUT
BUSINESS CODE  key · KEY (DBF-CAT-137) · prefix 02 or 20–29 (CHK_CAT_SCALE_LABEL_LAYOUT_KEY) · uniqueness UQ_CAT_SCALE_LABEL_LAYOUT_KEY among active rows · fixed after creation
DEFAULT FIELDS config → key, valueAr, valueEn, isActiveFl (+ tenantId, version, audit)
#### FIELDS ENT-CAT-011 — CAT_SCALE_LABEL_LAYOUT
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-136 | scaleLabelLayoutPk | SCALE_LABEL_LAYOUT_PK | BIGINT | NOT NULL | yes | PK_CAT_SCALE_LABEL_LAYOUT | المعرّف / Identifier |
| DBF-CAT-137 | key | KEY | VARCHAR(2) | NOT NULL | after create | CHK_CAT_SCALE_LABEL_LAYOUT_KEY, UQ_CAT_SCALE_LABEL_LAYOUT_KEY | البادئة / Prefix |
| DBF-CAT-138 | valueAr | VALUE_AR | VARCHAR(200) | NULL | no | — | الوصف بالعربية / Arabic description |
| DBF-CAT-139 | valueEn | VALUE_EN | VARCHAR(200) | NULL | no | — | الوصف بالإنجليزية / English description |
| DBF-CAT-140 | scaleCodeLength | SCALE_CODE_LENGTH | INTEGER | NOT NULL | after create | CHK_CAT_SCALE_LABEL_LAYOUT_SCALE_CODE_LENGTH | طول رمز الميزان / Scale code length |
| DBF-CAT-141 | embeddedValueTypeCode | EMBEDDED_VALUE_TYPE_CODE | VARCHAR(50) | NOT NULL | no | — | نوع القيمة المضمّنة / Embedded value type |
| DBF-CAT-142 | valueDecimals | VALUE_DECIMALS | INTEGER | NOT NULL | no | CHK_CAT_SCALE_LABEL_LAYOUT_VALUE_DECIMALS | الأرقام العشرية / Decimals |
| DBF-CAT-143 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-144 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-145 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-146 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-147 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-148 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-149 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: key, scaleCodeLength (default 5), embeddedValueTypeCode (default WEIGHT), valueDecimals (default 3), valueAr (default «ملصق ميزان {key}»), valueEn (default "Scale label {key}") · update-request: everything except key and scaleCodeLength, + version (both fixed after creation — ADR-CAT-064) · response ScaleLabelLayoutView
LOOKUP FIELDS  embeddedValueTypeCode · EMBEDDED_VALUE_TYPE_CODE (DBF-CAT-141) · CAT_SCALE_VALUE_TYPE
DOMAIN RULES   (RULE-CAT-006 — under ENT-CAT-001; RULE-CAT-032, 035 — under ENT-CAT-009)
- RULE-CAT-034 — Valid scale label layout · scope ENT-CAT-011 · trigger on create · on update · statement: The system shall require a key of 02 or 20 to 29 that no other active layout uses, a scaleCodeLength of 4 to 6 and valueDecimals of 0 to 3. The key and the scaleCodeLength are set on create only; an update carries neither (ADR-CAT-064). · DB CHK_CAT_SCALE_LABEL_LAYOUT_KEY, CHK_CAT_SCALE_LABEL_LAYOUT_SCALE_CODE_LENGTH, CHK_CAT_SCALE_LABEL_LAYOUT_VALUE_DECIMALS, UQ_CAT_SCALE_LABEL_LAYOUT_KEY + domain (ScaleLabelLayoutDomain: key format, ranges, defaults) + service (key unused by another active layout) · layer domain + service · message CAT-422-SCALE-LAYOUT-INVALID
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-143) only — deactivate via API-CAT-034; an inactive layout takes no update (API-CAT-033 answers CAT-400-REQUEST-INVALID) and is not reactivated: a new layout is created under the same key, which the partial unique index UQ_CAT_SCALE_LABEL_LAYOUT_KEY (active rows only) allows; an inactive layout stops decoding (REQ-CAT-094), while the SCALE barcodes validated against it stay stored and active and decode again only while an active layout of the same scaleCodeLength exists (ADR-CAT-064). A change of embeddedValueTypeCode or valueDecimals (API-CAT-033) changes only how the embedded value of a later scanned label is read
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         ScaleLabelLayoutDomain.create(request) · ScaleLabelLayoutDomain.from(stored, request) — RULE-CAT-034 (key format, length 4–6, decimals 0–3, defaults 5 / WEIGHT / 3; `from` keeps the stored key and scaleCodeLength); key uniqueness and RULE-CAT-032 in the service
AUDIT          @Audited(entityType = "CAT_SCALE_LABEL_LAYOUT")
REPOSITORY OPS QR-CAT-059, QR-CAT-060, QR-CAT-061, QR-CAT-062, QR-CAT-063, QR-CAT-064, QR-CAT-074

### ENT-CAT-012 — Unit e-invoice code / رمز الوحدة في الفاتورة الإلكترونية      kind: config
BINDINGS       table CAT_UNIT_EINVOICE_CODE · PK UNIT_EINVOICE_CODE_PK (DBF-CAT-150) · PK generation `identity` → `UNIT_EINVOICE_CODE_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_UNIT_EINVOICE_CODE
BUSINESS CODE  key · KEY (DBF-CAT-151) · a CAT_UOM code · uniqueness UQ_CAT_UNIT_EINVOICE_CODE_KEY among active rows · fixed after creation
DEFAULT FIELDS config → key, valueAr, valueEn (copied from the CAT_UOM labels at save — D-P2-09), isActiveFl (+ tenantId, version, audit)
#### FIELDS ENT-CAT-012 — CAT_UNIT_EINVOICE_CODE
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-150 | unitEinvoiceCodePk | UNIT_EINVOICE_CODE_PK | BIGINT | NOT NULL | yes | PK_CAT_UNIT_EINVOICE_CODE | المعرّف / Identifier |
| DBF-CAT-151 | key | KEY | VARCHAR(50) | NOT NULL | after create | UQ_CAT_UNIT_EINVOICE_CODE_KEY | وحدة القياس / Unit of measure |
| DBF-CAT-152 | valueAr | VALUE_AR | VARCHAR(200) | NULL | yes (derived) | — | الاسم بالعربية / Arabic name |
| DBF-CAT-153 | valueEn | VALUE_EN | VARCHAR(200) | NULL | yes (derived) | — | الاسم بالإنجليزية / English name |
| DBF-CAT-154 | einvoiceUnitCode | EINVOICE_UNIT_CODE | VARCHAR(50) | NOT NULL | no | — | وحدة الفاتورة الإلكترونية / E-invoice unit |
| DBF-CAT-155 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-156 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-157 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-158 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-159 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-160 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-161 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: key, einvoiceUnitCode · update-request: einvoiceUnitCode, version · response UnitEinvoiceCodeView
LOOKUP FIELDS  key · KEY (DBF-CAT-151) · CAT_UOM · einvoiceUnitCode · EINVOICE_UNIT_CODE (DBF-CAT-154) · CAT_EINVOICE_UNIT
DOMAIN RULES   (RULE-CAT-006 — under ENT-CAT-001)
- RULE-CAT-043 — One e-invoice unit per unit of measure · scope ENT-CAT-012 · trigger on create · statement: The system shall prevent creating a Unit e-invoice code for a key that already has an active one. · DB UQ_CAT_UNIT_EINVOICE_CODE_KEY + service (QR-CAT-066) · layer service · message CAT-409-UNIT-EINVOICE-DUPLICATE
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-155) only — deactivate via API-CAT-037; reads then return an empty e-invoice unit (REQ-CAT-122); an inactive row takes no update (API-CAT-036 answers CAT-400-REQUEST-INVALID) and is not reactivated: a new record is created under the same key (API-CAT-035), which the partial unique index UQ_CAT_UNIT_EINVOICE_CODE_KEY (active rows only) allows
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         UnitEinvoiceCodeDomain.create(request, uomLabels) · UnitEinvoiceCodeDomain.from(stored, request) — valueAr / valueEn copied from the CAT_UOM labels (D-P2-09); RULE-CAT-006 and RULE-CAT-043 in the service
AUDIT          @Audited(entityType = "CAT_UNIT_EINVOICE_CODE")
REPOSITORY OPS QR-CAT-065, QR-CAT-066, QR-CAT-067, QR-CAT-068, QR-CAT-069, QR-CAT-070
<!-- SUB:DATA-DOM-LOOKUP:END -->
<!-- PHASE:DATA-DOM:END -->

<!-- PHASE:SVC-API:START traces=REQ-CAT-139,REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,REQ-CAT-135,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-017,DBF-CAT-020,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-025,DBF-CAT-026,DBF-CAT-027,DBF-CAT-028,DBF-CAT-030,DBF-CAT-033,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-040,DBF-CAT-042,DBF-CAT-047,DBF-CAT-048,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053,DBF-CAT-055,DBF-CAT-061,DBF-CAT-062,DBF-CAT-063,DBF-CAT-064,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077,DBF-CAT-079,DBF-CAT-084,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089,DBF-CAT-096,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102,DBF-CAT-104,DBF-CAT-109,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-118,DBF-CAT-121,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129,DBF-CAT-131,DBF-CAT-136,DBF-CAT-137,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-145,DBF-CAT-150,DBF-CAT-151,DBF-CAT-152,DBF-CAT-153,DBF-CAT-154,DBF-CAT-155,DBF-CAT-157,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-096,REQ-CAT-097,REQ-CAT-098,REQ-CAT-099,REQ-CAT-100,REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127 -->
## PHASE SVC-API — services and endpoints (R3)

55 endpoints (≥ 8), grouped: SUB SVC-API-CRUD (create / read-by-id / update / deactivate / reactivate, API-CAT-001 … 037 and API-CAT-053 … 055), SUB SVC-API-SEARCH (lists, searches, lookups, API-CAT-038 … 047 and API-CAT-052), SUB SVC-API-INT (custom operations — import, labels, capability check — and the in-process contract surface, API-CAT-048 … 051). Line grammar used by every block: `Request` names path / query / body fields as `name: type [required] [DBF-…]`; `Response` names status · envelope · DTO (DTOs in PHASE DOC); `Errors` lists Error Catalog codes; every RULE named in `Validations` has its catalog row. Every mutating endpoint records the catalogue-change event of its entity (PHASE CORE) — that is what `Honours: CON-CAT-018` on a block means.

<!-- SUB:SVC-API-CRUD:START traces=REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,REQ-CAT-135,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-017,DBF-CAT-020,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-025,DBF-CAT-026,DBF-CAT-027,DBF-CAT-028,DBF-CAT-030,DBF-CAT-033,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-040,DBF-CAT-042,DBF-CAT-047,DBF-CAT-048,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053,DBF-CAT-055,DBF-CAT-061,DBF-CAT-062,DBF-CAT-063,DBF-CAT-064,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077,DBF-CAT-079,DBF-CAT-084,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089,DBF-CAT-096,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102,DBF-CAT-104,DBF-CAT-109,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-121,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129,DBF-CAT-131,DBF-CAT-136,DBF-CAT-137,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-145,DBF-CAT-150,DBF-CAT-151,DBF-CAT-152,DBF-CAT-153,DBF-CAT-154,DBF-CAT-155,DBF-CAT-157,REQ-CAT-001,REQ-CAT-002,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-091,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-096,REQ-CAT-098,REQ-CAT-099,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124 -->
### SUB SVC-API-CRUD

<!-- API:API-CAT-001:START traces=REQ-CAT-131,REQ-CAT-134,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-011,REQ-CAT-013,REQ-CAT-015,REQ-CAT-016,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-056,REQ-CAT-059,REQ-CAT-060,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-117,REQ-CAT-123,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-023,DBF-CAT-024,DBF-CAT-027,DBF-CAT-028,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089 -->
### API-CAT-001 — create Item
Entity       : ENT-CAT-001, ENT-CAT-002, ENT-CAT-007 · operation CREATE · serves SCR-REQ-CAT-001 "create"
Honours: CON-CAT-001, CON-CAT-002, CON-CAT-006, CON-CAT-018
Endpoint     : /api/v1/cat/items   verb: POST
Layers       : CatItemController.create → CatItemService.create
Request      : body ItemCreateRequest {nameAr: string(200) required DBF-CAT-003; nameEn: string(200) DBF-CAT-004; itemKindCode: string(50) DBF-CAT-005; baseUomCode: string(50) DBF-CAT-006; taxClassCode: string(50) DBF-CAT-008; categoryFk: int64 DBF-CAT-015; brandCode: string(50) DBF-CAT-007; einvoiceCodeTypeCode: string(50) DBF-CAT-009; einvoiceCode: string(100) DBF-CAT-010; descriptionAr: string(2000) DBF-CAT-011; descriptionEn: string(2000) DBF-CAT-012; code: string(20); bundleComponents: array{componentVariantFk: int64 required DBF-CAT-089; quantity: decimal DBF-CAT-085; sortOrder: int32 DBF-CAT-086} optional, default []} · `bundleComponents` is optional and must hold ≥ 1 line only when itemKindCode is BUNDLE (RULE-CAT-018); a STANDARD Item is created from nameAr alone (REQ-CAT-024) · excluded system fields: itemPk, publishStateCode, isActiveFl, tenantId, version, audit
Response     : 201 · ApiResponse<ItemDetail> · paginated: no
Validations  : RULE-CAT-008 / RULE-CAT-009 (itemKindCode BUNDLE needs `bundles` on and readable) · RULE-CAT-001, RULE-CAT-002 (names) · RULE-CAT-004 (a supplied `code` is rejected) · RULE-CAT-006 (itemKindCode, baseUomCode, taxClassCode, brandCode, einvoiceCodeTypeCode active in their keys) · RULE-CAT-042 (e-invoice pair and format) · RULE-CAT-026 (categoryFk active) · RULE-CAT-003 (no other active Item with the normalised nameAr) · RULE-CAT-018, RULE-CAT-019 (kind BUNDLE: ≥ 1 line, quantity > 0 with ≤ 3 decimals, component Variants active, of STANDARD Items, listed once) · RULE-CAT-005 (series must issue) · RULE-CAT-049 (event only after commit)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ITEM-NAME-DUPLICATE, CAT-422-CODE-SYSTEM-ASSIGNED, CAT-500-CODE-NOT-ISSUED, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-BUNDLE-COMPONENTS-REQUIRED, CAT-422-BUNDLE-COMPONENT-INVALID, CAT-422-CATEGORY-INACTIVE, CAT-422-EINVOICE-CODE-INVALID, CAT-500-CHANGE-NOT-SAVED
Orchestration : load flags when itemKindCode is BUNDLE (CatCapabilityFlags) → apply defaults itemKindCode STANDARD (DBF-CAT-005), baseUomCode PIECE (DBF-CAT-006), taxClassCode STANDARD (DBF-CAT-008) → ItemDomain.create() checks names, code and the e-invoice pair (RULE-CAT-001, 002, 004, 042) → the service checks lookups (MdlLookupApi), Category (QR-CAT-032, FOR SHARE), name uniqueness (QR-CAT-002), BUNDLE lines (QR-CAT-017, FOR SHARE; each quantity through BundleComponentDomain.create()) → allocate the Item code from series CAT_ITEM (NumberSeriesApi) into DBF-CAT-002 → set publishStateCode UNPUBLISHED (DBF-CAT-013) and isActiveFl true (DBF-CAT-014) → persist the Item (QR-CAT-003, CAT_ITEM, identity ITEM_PK) → allocate the Variant code from series CAT_VARIANT into DBF-CAT-023 and persist the default Variant with isDefaultFl true (DBF-CAT-024), isActiveFl true (DBF-CAT-027), itemFk (DBF-CAT-028) (QR-CAT-011) → BUNDLE: persist each line with bundleItemFk (DBF-CAT-088), isActiveFl true (DBF-CAT-087), quantity default 1 (DBF-CAT-085), sortOrder default line order (DBF-CAT-086) (QR-CAT-040) → record cat.CatalogueChanged CREATED for the Item, the Variant and each line (OutboxPublisher) → return ItemDetail
Repository   : QR-CAT-032 FIND_ONE · QR-CAT-002 EXISTS · QR-CAT-017 FIND_ALL · QR-CAT-003 SAVE · QR-CAT-011 SAVE · QR-CAT-040 SAVE · join NONE · READ_WRITE
Concurrency  : codes — NumberSeriesApi allocates atomically, UQ_CAT_ITEM_CODE / UQ_CAT_VARIANT_CODE back it; name — two parallel creates both pass QR-CAT-002, the second insert hits UQ_CAT_ITEM_NAME_AR and is translated to CAT-409-ITEM-NAME-DUPLICATE; Category and component Variants/Items are read FOR SHARE, so a parallel deactivation (FOR UPDATE) waits or fails first (ADR-CAT-047)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : messages ar + en from the Error Catalog; nameAr mandatory, nameEn optional, displayNameEn falls back to nameAr
<!-- API:API-CAT-001:END -->

<!-- API:API-CAT-002:START traces=REQ-CAT-132,REQ-CAT-001,REQ-CAT-002,REQ-CAT-010,REQ-CAT-029,REQ-CAT-058,REQ-CAT-119,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-014,DBF-CAT-022,DBF-CAT-027,DBF-CAT-062,DBF-CAT-064,DBF-CAT-088,DBF-CAT-089,DBF-CAT-102,DBF-CAT-114,DBF-CAT-128 -->
### API-CAT-002 — read Item
Entity       : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-007
Endpoint     : /api/v1/cat/items/{id}   verb: GET
Layers       : CatItemController.get → CatItemQueryFacade.getItem (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getItem (also called by CatalogueReadApi.getItem, with no CAT permission check — ADR-CAT-061)
Request      : path {id: int64 required DBF-CAT-001}
Response     : 200 · ApiResponse<ItemDetail> · paginated: no
Validations  : none — an inactive Item is still returned with isActiveFl false; a committed change is visible to every read that starts after the commit (REQ-CAT-010); an itemPk of another tenant reads as CAT-404-NOT-FOUND (REQ-CAT-132)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the Item (QR-CAT-001) → its active Variants (QR-CAT-013) with their assignments and value names (QR-CAT-019) → derive Variant nameAr / nameEn, displayNameEn and the effective e-invoice pair → active Item units (QR-CAT-044) with their e-invoice units (QR-CAT-070) → active barcodes of the Variants (QR-CAT-047) → active Bundle lines with component codes (QR-CAT-039) → active media, primary first (QR-CAT-054) → return ItemDetail
Repository   : QR-CAT-001 FIND_ONE · QR-CAT-013 FIND_ALL · QR-CAT-019 FIND_ALL · QR-CAT-044 FIND_ALL · QR-CAT-070 FIND_ALL · QR-CAT-047 FIND_ALL · QR-CAT-039 FIND_ALL · QR-CAT-054 FIND_ALL · join ADR-CAT-047 (QR-CAT-019) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : names in ar and en; codes returned as stored, labels resolved by the frontend
<!-- API:API-CAT-002:END -->

<!-- API:API-CAT-003:START traces=REQ-CAT-135,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-004,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-030,REQ-CAT-031,REQ-CAT-034,REQ-CAT-063,REQ-CAT-072,REQ-CAT-073,REQ-CAT-075,REQ-CAT-076,REQ-CAT-082,REQ-CAT-117,REQ-CAT-123,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-015,DBF-CAT-017,DBF-CAT-020 -->
### API-CAT-003 — update Item
Entity       : ENT-CAT-001 · operation UPDATE · serves SCR-REQ-CAT-001 "update"
Honours: CON-CAT-001, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}   verb: PUT
Layers       : CatItemController.update → CatItemService.update
Request      : path {id: int64 required DBF-CAT-001} · body ItemUpdateRequest {nameAr: string(200) required DBF-CAT-003; nameEn: string(200) DBF-CAT-004; categoryFk: int64 DBF-CAT-015; brandCode: string(50) DBF-CAT-007; taxClassCode: string(50) required DBF-CAT-008; einvoiceCodeTypeCode: string(50) DBF-CAT-009; einvoiceCode: string(100) DBF-CAT-010; descriptionAr: string(2000) DBF-CAT-011; descriptionEn: string(2000) DBF-CAT-012; version: int64 required DBF-CAT-017; code: string(20); itemKindCode: string(50); baseUomCode: string(50)} · code / itemKindCode / baseUomCode are echoes only, rejected when they differ from the stored value · excluded system fields: itemPk, publishStateCode, isActiveFl, tenantId, audit
Response     : 200 · ApiResponse<ItemDetail> · paginated: no
Validations  : RULE-CAT-001, RULE-CAT-002 · RULE-CAT-004 (code changed) · RULE-CAT-022 (itemKindCode changed) · RULE-CAT-007 (baseUomCode changed) · RULE-CAT-006 (only changed lookup fields; an unchanged value deactivated later is kept — REQ-CAT-076) · RULE-CAT-042 · RULE-CAT-026 (changed categoryFk) · RULE-CAT-003 (excluding this Item) · RULE-CAT-049 · the echoed version must equal the stored one (CAT-409-STALE-VERSION — REQ-CAT-133) · an itemPk of another tenant answers CAT-404-NOT-FOUND and changes nothing (REQ-CAT-132) · RULE-CAT-053 (on an inactive Item the only change accepted is categoryFk: every other field of the body must equal the stored value, else CAT-422-PARENT-INACTIVE; a categoryFk-only change is the repair of RULE-CAT-026 and is still checked by RULE-CAT-026 and the version — REQ-CAT-135, ADR-CAT-063, ADR-CAT-068)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-422-CODE-SYSTEM-ASSIGNED, CAT-422-ITEM-KIND-FIXED, CAT-422-BASE-UOM-FIXED, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-EINVOICE-CODE-INVALID, CAT-422-CATEGORY-INACTIVE, CAT-409-ITEM-NAME-DUPLICATE, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) → when it is inactive (DBF-CAT-014): compare every body field with the stored value and refuse any change other than categoryFk (DBF-CAT-015) with RULE-CAT-053; a categoryFk-only change goes on to the Category check and the write, and records cat.CatalogueChanged UPDATED (ADR-CAT-068) → ItemDomain.from(stored, request) compares the echoed code / itemKindCode / baseUomCode (RULE-CAT-004, 022, 007) and checks names and the e-invoice pair (RULE-CAT-001, 002, 042) → the service validates the changed fields (MdlLookupApi; Category QR-CAT-032 FOR SHARE; name QR-CAT-002) → write nameAr, nameEn, categoryFk, brandCode, taxClassCode, einvoiceCodeTypeCode, einvoiceCode, descriptionAr, descriptionEn with the version check (QR-CAT-004; the platform sets updatedBy DBF-CAT-020) → record cat.CatalogueChanged UPDATED → return ItemDetail
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-032 FIND_ONE · QR-CAT-002 EXISTS · QR-CAT-004 UPDATE · join NONE · READ_WRITE
Concurrency  : lost update — the version check (optimistic lock on DBF-CAT-017) makes the second of two parallel updates fail with CAT-409-STALE-VERSION; name — UQ_CAT_ITEM_NAME_AR translated to CAT-409-ITEM-NAME-DUPLICATE; the Item lock orders the update against a parallel deactivation or reactivation, so RULE-CAT-053 and its categoryFk-only exception decide on the committed state
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-003:END -->

<!-- API:API-CAT-004:START traces=REQ-CAT-131,REQ-CAT-005,REQ-CAT-062,DBF-CAT-001,DBF-CAT-013,DBF-CAT-014,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089 -->
### API-CAT-004 — deactivate Item
Entity       : ENT-CAT-001 · operation DELETE · serves SCR-REQ-CAT-001 "deactivate"
Honours: CON-CAT-001, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}   verb: DELETE
Layers       : CatItemController.deactivate → CatItemService.deactivate
Request      : path {id: int64 required DBF-CAT-001}
Response     : 200 · ApiResponse<ItemSummary> · paginated: no
Validations  : RULE-CAT-021 (no Variant of this Item is an active component of an active Bundle) · RULE-CAT-048 (soft deactivation, success notice) · RULE-CAT-049; the publish-state effect of rule 041 (deactivation sets UNPUBLISHED) is applied in the orchestration and raises nothing · the Item's Variants and their barcodes are left active (REQ-CAT-005): while the Item is inactive its barcodes no longer resolve (RULE-CAT-047, ADR-CAT-059) but still hold their values under RULE-CAT-029, so the Item can be reactivated with them; the owner releases a GTIN with API-CAT-016, which stays allowed on an inactive Item
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-VARIANT-IN-ACTIVE-BUNDLE, CAT-200-DEACTIVATED, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) → count active Bundles holding any of its Variants (QR-CAT-042) → set isActiveFl false (DBF-CAT-014) and publishStateCode UNPUBLISHED (DBF-CAT-013) (QR-CAT-004); its Variants, assignments and barcodes are not touched → record cat.CatalogueChanged DEACTIVATED → return ItemSummary with CAT-200-DEACTIVATED; an already inactive Item returns the same success
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-042 COUNT · QR-CAT-004 UPDATE · join NONE · READ_WRITE
Concurrency  : the Item row lock (QR-CAT-010) is decided on before the Bundle count; a parallel Bundle save reads the component Items FOR SHARE (QR-CAT-017), so the two never both pass (ADR-CAT-047)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : success notice ar + en (CAT-200-DEACTIVATED)
<!-- API:API-CAT-004:END -->

<!-- API:API-CAT-005:START traces=REQ-CAT-130,REQ-CAT-006,REQ-CAT-113,REQ-CAT-114,REQ-CAT-116,DBF-CAT-001,DBF-CAT-003,DBF-CAT-013,DBF-CAT-014,DBF-CAT-027,DBF-CAT-028 -->
### API-CAT-005 — reactivate, publish or unpublish Item
Entity       : ENT-CAT-001 · operation UPDATE · serves SCR-REQ-CAT-001 "reactivate", "publish", "unpublish"
Honours: CON-CAT-001, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}   verb: PATCH
Layers       : CatItemController.changeState → CatItemService.reactivate / publish / unpublish
Request      : path {id: int64 required DBF-CAT-001} · body ItemStateRequest {isActiveFl: boolean DBF-CAT-014; publishStateCode: string(50) DBF-CAT-013} · exactly one of the two; isActiveFl accepts only true (deactivation is DELETE) — ADR-CAT-048 · schema: `isActiveFl` enum [true]; `oneOf` [required isActiveFl, required publishStateCode] with `minProperties: 1`, `maxProperties: 1` and no other property, so an empty body, a body with both fields or `isActiveFl: false` fails the document check (CAT-400-REQUEST-INVALID) before the service runs
Response     : 200 · ApiResponse<ItemDetail> · paginated: no
Validations  : reactivate — the ACTIVE-state invariants of SRS A7 are re-established (ADR-CAT-059), in this order: RULE-CAT-050 (≥ 1 active Variant), RULE-CAT-026 (categoryFk empty or an active Category), RULE-CAT-019 (kind BUNDLE: every active line names an active Variant of an active STANDARD Item), RULE-CAT-003 (the normalised name is still unique among active Items); each refusal names a repair that stays allowed on the inactive Item (ADR-CAT-068): RULE-CAT-050 → generate Variants (API-CAT-006), RULE-CAT-026 → change only categoryFk (API-CAT-003) or reactivate the Category (API-CAT-053), RULE-CAT-019 → replace the Bundle components (API-CAT-017), RULE-CAT-003 → rename or deactivate the other active Item, whose code the message names (A7 invariant 4; the Item's own name cannot change while it is inactive — AC-CAT-162) · publish — RULE-CAT-006 (publishStateCode an active CAT_PUBLISH_STATE value), RULE-CAT-041 (Item active and ≥ 1 active Variant) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ITEM-NAME-DUPLICATE, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-PUBLISH-BLOCKED, CAT-422-ITEM-NO-ACTIVE-VARIANT, CAT-422-CATEGORY-INACTIVE, CAT-422-BUNDLE-COMPONENT-INVALID, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) → reactivate: count its active Variants (QR-CAT-015), read its Category FOR SHARE (QR-CAT-032), for a BUNDLE read the active lines (QR-CAT-039) and their Variants and Items FOR SHARE (QR-CAT-017), check the name (QR-CAT-002), set isActiveFl true (DBF-CAT-014), keep publishStateCode UNPUBLISHED (DBF-CAT-013), record REACTIVATED → publish: count active Variants (QR-CAT-015), set PUBLISHED (DBF-CAT-013), record UPDATED → unpublish: set UNPUBLISHED (DBF-CAT-013), isActiveFl unchanged, record UPDATED → write (QR-CAT-004) → return ItemDetail
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-015 COUNT · QR-CAT-032 FIND_ONE (FOR SHARE) · QR-CAT-039 FIND_ALL · QR-CAT-017 FIND_ALL (FOR SHARE) · QR-CAT-002 EXISTS · QR-CAT-004 UPDATE · join ADR-CAT-047 (QR-CAT-017, QR-CAT-039) · READ_WRITE
Concurrency  : publish decides on "≥ 1 active Variant" under the Item lock; Variant deactivation (API-CAT-009) takes the same lock, so an Item is never published with no active Variant; reactivation decides on its Variants under the Item lock and reads its Category and its component Variants / Items FOR SHARE, so a parallel deactivation of any of them (FOR UPDATE) cannot interleave; reactivation name clash backed by UQ_CAT_ITEM_NAME_AR
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-005:END -->

<!-- API:API-CAT-006:START traces=REQ-CAT-135,REQ-CAT-129,REQ-CAT-025,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-033,REQ-CAT-035,REQ-CAT-049,REQ-CAT-051,REQ-CAT-052,REQ-CAT-061,DBF-CAT-005,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-027,DBF-CAT-028,DBF-CAT-061,DBF-CAT-062,DBF-CAT-063,DBF-CAT-064,DBF-CAT-114 -->
### API-CAT-006 — generate Variants
Entity       : ENT-CAT-002, ENT-CAT-005 · operation CREATE · serves SCR-REQ-CAT-001 "generate Variants"
Honours: CON-CAT-002, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}/variants   verb: POST
Layers       : CatVariantController.generate → CatVariantService.generate
Request      : path {id: int64 required DBF-CAT-001} · body VariantGenerationRequest {selections: array (minItems 1){variantAttributeFk: int64 required DBF-CAT-063; variantAttributeValueFks: array<int64> (minItems 1) required DBF-CAT-064}}
Response     : 201 · ApiResponse<VariantGenerationResult> · paginated: no
Validations  : an empty `selections`, or a selection whose `variantAttributeValueFks` is empty, answers CAT-400-REQUEST-INVALID before any rule runs (the document check of `minItems: 1` on both arrays, repeated by the service) — so a request that names no value never reaches RULE-CAT-052 or RULE-CAT-014, also on an Item with no attribute in use · RULE-CAT-008 / RULE-CAT-009 (`variants`) · allowed on an inactive Item: generation is the repair of RULE-CAT-050, so RULE-CAT-053 does not apply here and the rules below decide alone (REQ-CAT-135, ADR-CAT-068); on an inactive Item with 0 active Variants, RULE-CAT-052 requires no attribute and RULE-CAT-012 assigns nothing · RULE-CAT-020 (Item kind must be STANDARD) · selected attributes and values must be active (an inactive one is not offered — CAT-400-REQUEST-INVALID) · RULE-CAT-052 (every attribute the Item's active Variants already carry is in the selections with ≥ 1 value — checked first) · RULE-CAT-014 (≤ 3 attributes used, ≤ 300 active Variants after) · RULE-CAT-012 (existing Variants first take the first selected value of each newly used attribute — success notice) · RULE-CAT-013 (one value per attribute; a request that names exactly one combination already carried by an active Variant is rejected; in a multi-value selection existing combinations are skipped — REQ-CAT-049) · RULE-CAT-005 · RULE-CAT-049; new Variants carry no barcode (REQ-CAT-019) and the existing ones keep variantPk, code and barcodes (REQ-CAT-017) · default flag (REQ-CAT-025, ADR-CAT-072): when the Item has 0 active Variants, the first Variant created (the lowest new code) takes isDefaultFl and the inactive former default loses it; when the Item has active Variants, the default stays where it is
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-BUNDLE-SINGLE-VARIANT, CAT-422-VARIANT-ATTRIBUTE-MISSING, CAT-422-VARIANT-LIMIT-EXCEEDED, CAT-409-VARIANT-COMBINATION-DUPLICATE, CAT-201-EXISTING-VARIANTS-KEPT, CAT-500-CODE-NOT-ISSUED, CAT-500-CHANGE-NOT-SAVED
Orchestration : read `variants` → lock the Item (QR-CAT-010) and check itemKindCode (DBF-CAT-005); an inactive Item (DBF-CAT-014) is accepted and stays inactive — the owner reactivates it afterwards (ADR-CAT-068) → load the selected attributes and values (QR-CAT-026, QR-CAT-031 FOR SHARE) → load the active Variants (QR-CAT-013) and their assignments (QR-CAT-019) → check that every attribute the active Variants carry is selected with ≥ 1 value (RULE-CAT-052) → count (QR-CAT-015) and check the limits → for each existing active Variant persist an assignment of the first selected value of every newly used attribute: variantFk (DBF-CAT-062), variantAttributeFk (DBF-CAT-063), variantAttributeValueFk (DBF-CAT-064), isActiveFl true (DBF-CAT-061) (QR-CAT-018) → for each remaining combination allocate a code from CAT_VARIANT into DBF-CAT-023, persist the Variant with isDefaultFl false (DBF-CAT-024), isActiveFl true (DBF-CAT-027), itemFk (DBF-CAT-028) (QR-CAT-011) and its assignments (QR-CAT-018) → when the Item had 0 active Variants before this generation: clear isDefaultFl on the Item's former default (QR-CAT-075, DBF-CAT-024), then set isDefaultFl true on the first Variant created (QR-CAT-014) — in that order, so UQ_CAT_VARIANT_DEFAULT holds after each statement (ADR-CAT-072) → record cat.CatalogueChanged CREATED for every new Variant and assignment, and UPDATED for the former default whose flag was cleared → return the Item's Variants
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-026 FIND_ALL · QR-CAT-031 FIND_ALL (FOR SHARE) · QR-CAT-013 FIND_ALL · QR-CAT-019 FIND_ALL · QR-CAT-015 COUNT · QR-CAT-018 SAVE · QR-CAT-011 SAVE · QR-CAT-075 UPDATE · QR-CAT-014 UPDATE · join ADR-CAT-047 · READ_WRITE
Concurrency  : two generations on one Item would both pass the 300 limit and the combination check — the Item row lock (QR-CAT-010) serialises them, and orders a generation against a parallel reactivation of the same Item (API-CAT-005 counts the Variants under that lock); a parallel deactivation of a selected value waits on its FOR SHARE read; UQ_CAT_VAR_ATTR_ASSIGN_ATTR backs one value per attribute; codes are allocated atomically; the default-flag move is decided under the same Item lock, so a parallel Variant deactivation cannot leave two defaults or none
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : derived Variant names in ar and en; notice ar + en (CAT-201-EXISTING-VARIANTS-KEPT)
<!-- API:API-CAT-006:END -->

<!-- API:API-CAT-007:START traces=REQ-CAT-001,REQ-CAT-029,REQ-CAT-050,REQ-CAT-119,REQ-CAT-122,DBF-CAT-006,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-025,DBF-CAT-026,DBF-CAT-027,DBF-CAT-028,DBF-CAT-154 -->
### API-CAT-007 — read Variant
Entity       : ENT-CAT-002, ENT-CAT-005 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-008
Endpoint     : /api/v1/cat/variants/{id}   verb: GET
Layers       : CatVariantController.get → CatVariantQueryFacade.getVariant (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getVariant (also called by CatalogueReadApi.getVariant, with no CAT permission check — ADR-CAT-061) — ADR-CAT-045
Request      : path {id: int64 required DBF-CAT-022}
Response     : 200 · ApiResponse<VariantDetail> · paginated: no
Validations  : none — an inactive Variant is returned with isActiveFl false (REQ-CAT-050)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the Variant (QR-CAT-012) and its Item (QR-CAT-001) → assignments and value names (QR-CAT-019) → derived nameAr / nameEn → active barcodes (QR-CAT-047) → effective e-invoice pair (override DBF-CAT-025 / DBF-CAT-026, else Item DBF-CAT-009 / DBF-CAT-010), Item taxClassCode (DBF-CAT-008) and baseUomCode (DBF-CAT-006) → e-invoice unit of the base unit (QR-CAT-070; empty when none is active) → return VariantDetail
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-001 FIND_ONE · QR-CAT-019 FIND_ALL · QR-CAT-047 FIND_ALL · QR-CAT-070 FIND_ALL · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : derived names in ar and en
<!-- API:API-CAT-007:END -->

<!-- API:API-CAT-008:START traces=REQ-CAT-135,REQ-CAT-034,REQ-CAT-118,REQ-CAT-123,DBF-CAT-022,DBF-CAT-023,DBF-CAT-025,DBF-CAT-026,DBF-CAT-030,DBF-CAT-033 -->
### API-CAT-008 — update Variant e-invoice override
Entity       : ENT-CAT-002 · operation UPDATE · serves SCR-REQ-CAT-001 "e-invoice override"
Honours: CON-CAT-002, CON-CAT-018
Endpoint     : /api/v1/cat/variants/{id}   verb: PUT
Layers       : CatVariantController.update → CatVariantService.updateOverride
Request      : path {id: int64 required DBF-CAT-022} · body VariantOverrideRequest {einvoiceCodeTypeCode: string(50) DBF-CAT-025; einvoiceCode: string(100) DBF-CAT-026; version: int64 required DBF-CAT-030; code: string(20)} · code is an echo only, rejected when it differs
Response     : 200 · ApiResponse<VariantDetail> · paginated: no
Validations  : RULE-CAT-053 (the Variant and its Item must be active) · RULE-CAT-004 (code changed) · RULE-CAT-006 (einvoiceCodeTypeCode) · RULE-CAT-042 (both or neither; GS1 8–14 digits; EGS starts with "EG-", ≤ 100) · RULE-CAT-049; both empty removes the override
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CODE-SYSTEM-ASSIGNED, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-EINVOICE-CODE-INVALID, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the Variant (QR-CAT-012) → lock its Item (QR-CAT-010; itemFk DBF-CAT-028) and refuse an inactive Variant (DBF-CAT-027) or Item (DBF-CAT-014) — RULE-CAT-053 → validate → write einvoiceCodeTypeCode (DBF-CAT-025) and einvoiceCode (DBF-CAT-026) with the version check (QR-CAT-014; updatedBy DBF-CAT-033 by the platform) → record cat.CatalogueChanged UPDATED → return VariantDetail
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-014 UPDATE · join NONE · READ_WRITE
Concurrency  : lost update — optimistic version (DBF-CAT-030) → CAT-409-STALE-VERSION; the Item lock orders it against a parallel Variant or Item deactivation (RULE-CAT-053)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-008:END -->

<!-- API:API-CAT-009:START traces=REQ-CAT-025,REQ-CAT-050,REQ-CAT-053,REQ-CAT-062,DBF-CAT-022,DBF-CAT-024,DBF-CAT-027,DBF-CAT-028,DBF-CAT-014,DBF-CAT-061,DBF-CAT-062,DBF-CAT-089,DBF-CAT-113,DBF-CAT-114 -->
### API-CAT-009 — deactivate Variant
Entity       : ENT-CAT-002, ENT-CAT-005, ENT-CAT-009 · operation DELETE · serves SCR-REQ-CAT-001 "deactivate Variant"
Honours: CON-CAT-002, CON-CAT-018
Endpoint     : /api/v1/cat/variants/{id}   verb: DELETE
Layers       : CatVariantController.deactivate → CatVariantService.deactivate
Request      : path {id: int64 required DBF-CAT-022}
Response     : 200 · ApiResponse<VariantDetail> · paginated: no
Validations  : RULE-CAT-015 (not the last active Variant of an active Item) · RULE-CAT-021 (not an active component of an active Bundle) · RULE-CAT-048 · RULE-CAT-049; the Variant's active barcodes are deactivated with it, which releases their values for RULE-CAT-029 (REQ-CAT-050, ADR-CAT-063); allowed on an inactive Item, so its Variants can be cleaned up · default flag (REQ-CAT-025, ADR-CAT-072): when the Variant being deactivated carries isDefaultFl and another Variant of the Item is active, isDefaultFl moves, in the same transaction, to the remaining active Variant with the lowest code; when it was the Item's last active Variant (an inactive Item), the flag stays on it until a generation moves it (API-CAT-006)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-LAST-ACTIVE-VARIANT, CAT-409-VARIANT-IN-ACTIVE-BUNDLE, CAT-200-DEACTIVATED, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the Variant (QR-CAT-012) → lock its Item (QR-CAT-010; reads isActiveFl DBF-CAT-014) → count active Variants (QR-CAT-015) → count active Bundles holding it (QR-CAT-042) → set isActiveFl false (DBF-CAT-027) (QR-CAT-014) and its assignments' isActiveFl false (DBF-CAT-061) (QR-CAT-020) and its active barcodes' isActiveFl false (DBF-CAT-113, by variantFk DBF-CAT-114) (QR-CAT-072) → when the Variant carried isDefaultFl (DBF-CAT-024) and the Item keeps an active Variant: read the remaining active Variants (QR-CAT-013, ordered by code after the default), clear isDefaultFl on the deactivated Variant (QR-CAT-075), then set it on the remaining active Variant with the lowest code (QR-CAT-014) — in that order, so UQ_CAT_VARIANT_DEFAULT holds after each statement (ADR-CAT-072) → record cat.CatalogueChanged DEACTIVATED for the Variant, each assignment and each barcode, and UPDATED for the Variant that became the default → return VariantDetail with CAT-200-DEACTIVATED
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-015 COUNT · QR-CAT-042 COUNT · QR-CAT-014 UPDATE · QR-CAT-020 UPDATE · QR-CAT-072 UPDATE · QR-CAT-013 FIND_ALL · QR-CAT-075 UPDATE · join NONE · READ_WRITE
Concurrency  : two parallel deactivations of the last two Variants would each see two active — the Item row lock serialises them, so the second sees one and is refused; the default-flag move is decided under the same lock, so the surviving Variant ends as the one default (AC-CAT-146, ADR-CAT-072); a parallel Bundle save reads the Variant FOR SHARE
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : success notice ar + en (CAT-200-DEACTIVATED)
<!-- API:API-CAT-009:END -->

<!-- API:API-CAT-010:START traces=REQ-CAT-135,REQ-CAT-074,REQ-CAT-075,REQ-CAT-077,REQ-CAT-080,DBF-CAT-006,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102 -->
### API-CAT-010 — add Item unit
Entity       : ENT-CAT-008 · operation CREATE · serves SCR-REQ-CAT-001 "add"
Honours: CON-CAT-004, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}/units   verb: POST
Layers       : CatItemUnitController.create → CatItemUnitService.create
Request      : path {id: int64 required DBF-CAT-001} · body ItemUnitCreateRequest {uomCode: string(50) required DBF-CAT-097; conversionFactor: decimal required DBF-CAT-098; isPurchaseFl: boolean DBF-CAT-099; isSalesFl: boolean DBF-CAT-100}
Response     : 201 · ApiResponse<ItemUnitView> · paginated: no
Validations  : RULE-CAT-053 (the Item must be active) · RULE-CAT-006 (uomCode active in CAT_UOM) · RULE-CAT-027 (differs from the Item's baseUomCode DBF-CAT-006 and from its other active units; factor > 0 with ≤ 6 decimals) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-ITEM-UNIT-INVALID, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) and refuse an inactive one (DBF-CAT-014, RULE-CAT-053) → load its active units (QR-CAT-044) → validate → default isPurchaseFl / isSalesFl true (DBF-CAT-099 / DBF-CAT-100) → persist with itemFk (DBF-CAT-102) and isActiveFl true (DBF-CAT-101) (QR-CAT-045) → record cat.CatalogueChanged CREATED → return ItemUnitView
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-044 FIND_ALL · QR-CAT-045 SAVE · join NONE · READ_WRITE
Concurrency  : two parallel adds of the same unit both pass the check — UQ_CAT_ITEM_UNIT_UOM rejects the second, translated to CAT-422-ITEM-UNIT-INVALID; the Item lock orders them
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-010:END -->

<!-- API:API-CAT-011:START traces=REQ-CAT-079,REQ-CAT-122,DBF-CAT-096,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102,DBF-CAT-154 -->
### API-CAT-011 — read Item unit
Entity       : ENT-CAT-008 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-009
Endpoint     : /api/v1/cat/item-units/{id}   verb: GET
Layers       : CatItemUnitController.get → CatItemUnitQueryFacade.getItemUnit (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getItemUnit (also called by CatalogueReadApi.getItemUnit, with no CAT permission check — ADR-CAT-061) — ADR-CAT-045
Request      : path {id: int64 required DBF-CAT-096}
Response     : 200 · ApiResponse<ItemUnitView> · paginated: no
Validations  : none — an inactive unit is still returned (REQ-CAT-079)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the unit (QR-CAT-043) → its e-invoice unit (QR-CAT-070; empty when none is active) → return ItemUnitView
Repository   : QR-CAT-043 FIND_ONE · QR-CAT-070 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : codes returned as stored
<!-- API:API-CAT-011:END -->

<!-- API:API-CAT-012:START traces=REQ-CAT-135,REQ-CAT-078,REQ-CAT-081,DBF-CAT-096,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-104 -->
### API-CAT-012 — update Item unit
Entity       : ENT-CAT-008 · operation UPDATE · serves SCR-REQ-CAT-001 "update"
Honours: CON-CAT-004, CON-CAT-018
Endpoint     : /api/v1/cat/item-units/{id}   verb: PUT
Layers       : CatItemUnitController.update → CatItemUnitService.update
Request      : path {id: int64 required DBF-CAT-096} · body ItemUnitUpdateRequest {isPurchaseFl: boolean required DBF-CAT-099; isSalesFl: boolean required DBF-CAT-100; version: int64 required DBF-CAT-104; conversionFactor: decimal} · conversionFactor is an echo only, rejected when it differs
Response     : 200 · ApiResponse<ItemUnitView> · paginated: no
Validations  : RULE-CAT-053 (the Item must be active) · RULE-CAT-028 (conversionFactor fixed) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CONVERSION-FACTOR-FIXED, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : load (QR-CAT-043) → lock its Item (QR-CAT-010; itemFk DBF-CAT-102) and refuse an inactive one (RULE-CAT-053) → compare the echoed factor (DBF-CAT-098) → write isPurchaseFl (DBF-CAT-099), isSalesFl (DBF-CAT-100) with the version check (QR-CAT-046) → record cat.CatalogueChanged UPDATED → return ItemUnitView
Repository   : QR-CAT-043 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-046 UPDATE · join NONE · READ_WRITE
Concurrency  : lost update — optimistic version (DBF-CAT-104) → CAT-409-STALE-VERSION; the Item lock orders it against a parallel Item deactivation (RULE-CAT-053)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-012:END -->

<!-- API:API-CAT-013:START traces=REQ-CAT-128,REQ-CAT-079,DBF-CAT-096,DBF-CAT-101 -->
### API-CAT-013 — deactivate Item unit
Entity       : ENT-CAT-008 · operation DELETE · serves SCR-REQ-CAT-001 "deactivate Item unit"
Honours: CON-CAT-004, CON-CAT-018
Endpoint     : /api/v1/cat/item-units/{id}   verb: DELETE
Layers       : CatItemUnitController.deactivate → CatItemUnitService.deactivate
Request      : path {id: int64 required DBF-CAT-096}
Response     : 200 · ApiResponse<ItemUnitView> · paginated: no
Validations  : RULE-CAT-051 (no active barcode of the Item's Variants is bound to the unit's uomCode — ADR-CAT-060) · RULE-CAT-048 (soft, still readable by identifier) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ITEM-UNIT-HAS-BARCODES, CAT-200-DEACTIVATED, CAT-500-CHANGE-NOT-SAVED
Orchestration : load (QR-CAT-043) → lock its Item (QR-CAT-010; itemFk DBF-CAT-102) → count the active barcodes of the Item's Variants bound to the unit's uomCode (QR-CAT-071 on DBF-CAT-112, DBF-CAT-113) → set isActiveFl false (DBF-CAT-101) (QR-CAT-046) → record cat.CatalogueChanged DEACTIVATED → return ItemUnitView with CAT-200-DEACTIVATED; an already inactive unit returns the same success
Repository   : QR-CAT-043 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-071 COUNT · QR-CAT-046 UPDATE · join ADR-CAT-047 (QR-CAT-071) · READ_WRITE
Concurrency  : the barcode count is decided under the Item lock; barcode adds (API-CAT-014, 015) take the same Item lock before RULE-CAT-031 reads the active units, so a unit is never deactivated while a parallel add binds a barcode to it (ADR-CAT-060)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : success notice ar + en
<!-- API:API-CAT-013:END -->

<!-- API:API-CAT-014:START traces=REQ-CAT-135,REQ-CAT-074,REQ-CAT-075,REQ-CAT-083,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-091,REQ-CAT-096,REQ-CAT-099,DBF-CAT-006,DBF-CAT-022,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-137,DBF-CAT-140 -->
### API-CAT-014 — add barcode (GTIN or scale code)
Entity       : ENT-CAT-009 · operation CREATE · serves SCR-REQ-CAT-001 "add GTIN", "add scale barcode"
Honours: CON-CAT-005, CON-CAT-018
Endpoint     : /api/v1/cat/variants/{id}/barcodes   verb: POST
Layers       : CatBarcodeController.create → CatBarcodeService.create
Request      : path {id: int64 required DBF-CAT-022} · body BarcodeCreateRequest {barcodeValue: string(48) required DBF-CAT-110; barcodeTypeCode: string(50) required DBF-CAT-111; uomCode: string(50) DBF-CAT-112} · type INTERNAL is not accepted here (API-CAT-015 generates it) — CAT-400-REQUEST-INVALID
Response     : 201 · ApiResponse<BarcodeView> · paginated: no
Validations  : RULE-CAT-053 (the Variant and its Item must be active) · RULE-CAT-006 (barcodeTypeCode, uomCode) · RULE-CAT-031 (unit is the Item's base unit or an active Item unit) · GTIN: RULE-CAT-030 (8/12/13/14 digits, GS1 check digit), RULE-CAT-032 (does not begin with an active layout key) · SCALE: RULE-CAT-033 (digits only, length = scaleCodeLength of an active layout; offered only while ≥ 1 active layout exists — REQ-CAT-096) · RULE-CAT-029 (unique among active barcodes; message names the owning Item code) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-BARCODE-UNIT-INVALID, CAT-422-GTIN-INVALID, CAT-409-BARCODE-SCALE-PREFIX, CAT-422-SCALE-CODE-LENGTH, CAT-409-BARCODE-DUPLICATE, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the Variant (QR-CAT-012) and lock its Item (QR-CAT-010 — RULE-CAT-031 and RULE-CAT-051 decide under the same lock, ADR-CAT-060) → refuse an inactive Variant (DBF-CAT-027) or Item (DBF-CAT-014) — RULE-CAT-053 → default uomCode to the Item's baseUomCode (DBF-CAT-006 → DBF-CAT-112) → active units (QR-CAT-044) → take the scale-prefix lock (QR-CAT-074, advisory, ADR-CAT-047) → active layouts (QR-CAT-059: key DBF-CAT-137, scaleCodeLength DBF-CAT-140) → duplicate check (QR-CAT-048) → persist with variantFk (DBF-CAT-114) and isActiveFl true (DBF-CAT-113) (QR-CAT-049) → record cat.CatalogueChanged CREATED → return BarcodeView
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-044 FIND_ALL · QR-CAT-074 NATIVE (scale-prefix lock) · QR-CAT-059 FIND_ALL · QR-CAT-048 EXISTS · QR-CAT-049 SAVE · join ADR-CAT-047 (QR-CAT-048) · READ_WRITE
Concurrency  : duplicate value — UQ_CAT_BARCODE_VALUE rejects the second of two parallel adds (→ CAT-409-BARCODE-DUPLICATE); prefix clash — barcode adds and layout creates take the same advisory lock (QR-CAT-074: `pg_advisory_xact_lock(:tenant, hashtext('CAT_SCALE_PREFIX'))`), so a layout and a clashing barcode are never both saved
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-014:END -->

<!-- API:API-CAT-015:START traces=REQ-CAT-135,REQ-CAT-035,REQ-CAT-084,REQ-CAT-089,REQ-CAT-091,DBF-CAT-006,DBF-CAT-022,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114 -->
### API-CAT-015 — generate internal barcode
Entity       : ENT-CAT-009 · operation CREATE · serves SCR-REQ-CAT-001 "generate internal"
Honours: CON-CAT-005, CON-CAT-018
Endpoint     : /api/v1/cat/variants/{id}/barcodes/internal   verb: POST
Layers       : CatBarcodeController.generateInternal → CatBarcodeService.generateInternal
Request      : path {id: int64 required DBF-CAT-022} · body InternalBarcodeRequest {uomCode: string(50) DBF-CAT-112}
Response     : 201 · ApiResponse<BarcodeView> · paginated: no
Validations  : RULE-CAT-053 (the Variant and its Item must be active) · RULE-CAT-006 (uomCode) · RULE-CAT-031 · RULE-CAT-005 (series CAT_INTERNAL_BARCODE must issue) · RULE-CAT-032 · RULE-CAT-029 (a generated `04…` value can equal a GTIN entered earlier by hand: on that clash one more number is drawn from CAT_INTERNAL_BARCODE and the value is composed again; only when the second value also clashes is CAT-409-BARCODE-DUPLICATE answered, with {code} the Item that owns the second value) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-BARCODE-UNIT-INVALID, CAT-500-CODE-NOT-ISSUED, CAT-409-BARCODE-SCALE-PREFIX, CAT-409-BARCODE-DUPLICATE, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the Variant (QR-CAT-012) and lock its Item (QR-CAT-010 — RULE-CAT-031 and RULE-CAT-051 decide under the same lock, ADR-CAT-060) → refuse an inactive Variant (DBF-CAT-027) or Item (DBF-CAT-014) — RULE-CAT-053 → default uomCode (DBF-CAT-006 → DBF-CAT-112) and check it (QR-CAT-044) → allocate 10 digits from series CAT_INTERNAL_BARCODE (NumberSeriesApi) → barcodeValue = `04` + digits + GS1 check digit (DBF-CAT-110), barcodeTypeCode INTERNAL (DBF-CAT-111) → scale-prefix lock (QR-CAT-074) and active layouts (QR-CAT-059) → duplicate check (QR-CAT-048); when the value is taken, draw one more number from CAT_INTERNAL_BARCODE, compose the value again and repeat the duplicate check once; when that value is taken too, answer CAT-409-BARCODE-DUPLICATE (the numbers drawn stay as gaps in the series) → persist with variantFk (DBF-CAT-114), isActiveFl true (DBF-CAT-113) (QR-CAT-049) → record cat.CatalogueChanged CREATED → return BarcodeView
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-044 FIND_ALL · QR-CAT-074 NATIVE (scale-prefix lock) · QR-CAT-059 FIND_ALL · QR-CAT-048 EXISTS · QR-CAT-049 SAVE · join NONE · READ_WRITE
Concurrency  : the digits are allocated atomically by NumberSeriesApi, so two parallel requests never draw the same number; the one retry covers a clash with a GTIN entered by hand; UQ_CAT_BARCODE_VALUE backs uniqueness; the advisory scale-prefix lock (QR-CAT-074) orders it against layout creation
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-015:END -->

<!-- API:API-CAT-016:START traces=REQ-CAT-085,DBF-CAT-109,DBF-CAT-113 -->
### API-CAT-016 — deactivate barcode
Entity       : ENT-CAT-009 · operation DELETE · serves SCR-REQ-CAT-001 "deactivate barcode"
Honours: CON-CAT-005, CON-CAT-018
Endpoint     : /api/v1/cat/barcodes/{id}   verb: DELETE
Layers       : CatBarcodeController.deactivate → CatBarcodeService.deactivate
Request      : path {id: int64 required DBF-CAT-109}
Response     : 200 · ApiResponse<BarcodeView> · paginated: no
Validations  : RULE-CAT-048 (soft; an inactive barcode is excluded from lookups and releases its value for RULE-CAT-029 — REQ-CAT-085) · RULE-CAT-049 · allowed on a barcode of an inactive Variant or Item (a child deactivation, never refused by RULE-CAT-053), so the owner can release a GTIN of a deactivated Item (REQ-CAT-005)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-200-DEACTIVATED, CAT-500-CHANGE-NOT-SAVED
Orchestration : load (QR-CAT-050) → set isActiveFl false (DBF-CAT-113) (QR-CAT-051) → record cat.CatalogueChanged DEACTIVATED → return BarcodeView with CAT-200-DEACTIVATED
Repository   : QR-CAT-050 FIND_ONE · QR-CAT-051 UPDATE · join NONE · READ_WRITE
Concurrency  : NONE — a flag flip with no decision on other rows; repeated calls are idempotent
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : success notice ar + en
<!-- API:API-CAT-016:END -->

<!-- API:API-CAT-017:START traces=REQ-CAT-135,REQ-CAT-013,REQ-CAT-015,REQ-CAT-016,REQ-CAT-020,REQ-CAT-056,REQ-CAT-057,REQ-CAT-059,REQ-CAT-060,DBF-CAT-005,DBF-CAT-017,DBF-CAT-084,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089 -->
### API-CAT-017 — save Bundle components
Entity       : ENT-CAT-007 · operation CREATE, UPDATE, DELETE · serves SCR-REQ-CAT-001 "maintain Bundle components"
Honours: CON-CAT-006, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}/bundle-components   verb: PUT
Layers       : CatBundleController.replace → CatBundleService.replaceComponents
Request      : path {id: int64 required DBF-CAT-001} · body BundleComponentsRequest {lines: array{bundleComponentPk: int64 DBF-CAT-084; componentVariantFk: int64 required DBF-CAT-089; quantity: decimal required DBF-CAT-085; sortOrder: int32 DBF-CAT-086}; version: int64 required DBF-CAT-017} · the lines are the Bundle's complete new set of active lines
Response     : 200 · ApiResponse<List<BundleComponentView>> · paginated: no
Validations  : RULE-CAT-008 / RULE-CAT-009 (`bundles`) · allowed on an inactive Bundle: replacing the components is the repair of RULE-CAT-019, so RULE-CAT-053 does not apply here and the rules below decide alone (REQ-CAT-135, ADR-CAT-068) · the Item must be of kind BUNDLE (DBF-CAT-005; otherwise CAT-400-REQUEST-INVALID) · RULE-CAT-018 · RULE-CAT-019 · RULE-CAT-049; an active STANDARD Variant created while `bundles` was off is accepted unchanged (REQ-CAT-020)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-BUNDLE-COMPONENTS-REQUIRED, CAT-422-BUNDLE-COMPONENT-INVALID, CAT-500-CHANGE-NOT-SAVED
Orchestration : read `bundles` → lock the Item (QR-CAT-010) and check its version (DBF-CAT-017); an inactive Bundle (DBF-CAT-014) is accepted and stays inactive — the owner reactivates it afterwards (ADR-CAT-068) → load the component Variants and their Items FOR SHARE (QR-CAT-017) → load the active lines (QR-CAT-039) → new lines: persist with bundleItemFk (DBF-CAT-088), isActiveFl true (DBF-CAT-087), quantity (DBF-CAT-085), sortOrder default line order (DBF-CAT-086) (QR-CAT-040) → kept lines: update quantity / sortOrder (QR-CAT-041) → lines not in the set: isActiveFl false (DBF-CAT-087) (QR-CAT-041) → bump the Item version (QR-CAT-004) → record cat.CatalogueChanged CREATED / UPDATED / DEACTIVATED per line → return the active lines
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-017 FIND_ALL (FOR SHARE) · QR-CAT-039 FIND_ALL · QR-CAT-040 SAVE · QR-CAT-041 UPDATE · QR-CAT-004 UPDATE · join NONE · READ_WRITE
Concurrency  : component validity is decided on rows read FOR SHARE, so a parallel deactivation of a component Variant or Item (FOR UPDATE) cannot interleave; two parallel saves of one Bundle are serialised by the Item lock and the version check; UQ_CAT_BUNDLE_COMP_VARIANT backs "listed once"
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-017:END -->

<!-- API:API-CAT-018:START traces=REQ-CAT-135,REQ-CAT-105,REQ-CAT-108,REQ-CAT-110,REQ-CAT-112,DBF-CAT-003,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129 -->
### API-CAT-018 — upload image
Entity       : ENT-CAT-010 · operation CREATE · serves SCR-REQ-CAT-001 "add"
Honours: CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}/media   verb: POST
Layers       : CatMediaController.upload → CatMediaService.upload
Request      : path {id: int64 required DBF-CAT-001} · body ItemMediaCreateRequest {fileDocumentRef: int64 required DBF-CAT-122; nameAr: string(200) DBF-CAT-123; nameEn: string(200) DBF-CAT-124; variantFk: int64 DBF-CAT-129} · application/json; the client uploaded the bytes beforehand through the given FILE endpoint (public image storage), which returned fileDocumentRef (ADR-CAT-056)
Response     : 201 · ApiResponse<MediaView> · paginated: no
Validations  : fileDocumentRef names an available FILE document of the caller's tenant (otherwise CAT-404-NOT-FOUND) · RULE-CAT-053 (the Item must be active) · RULE-CAT-038 (JPG, PNG or WEBP and ≤ 5 MB, read through FileDocumentLookupApi; the FILE document must be in the public image storage of the caller's tenant, otherwise CAT-422-IMAGE-NOT-ACCEPTED — a file uploaded to the private storage could not be shown by the storefront; ≤ 20 active images per Item) · one fileDocumentRef may be attached to several media rows, of this Item or of others: CAT does not check that the ref is unused, because removing an image never deletes its file (ADR-CAT-056 §4) · RULE-CAT-040 (variantFk of the same Item) · RULE-CAT-001, RULE-CAT-002 (nameAr defaults to the Item's nameAr) · RULE-CAT-049; the main-image effect of rule 039 (the first active image becomes primary — REQ-CAT-108) is applied in the orchestration and raises nothing
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-IMAGE-NOT-ACCEPTED, CAT-422-MEDIA-VARIANT-MISMATCH, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) and refuse an inactive one (DBF-CAT-014, RULE-CAT-053) → its active media (QR-CAT-054: count, max sortOrder, primary present) → variantFk check (QR-CAT-012) → read the type, size, storage and availability of fileDocumentRef (FileDocumentLookupApi) and refuse a document outside the tenant's public image storage (RULE-CAT-038) → persist with fileDocumentRef (DBF-CAT-122), nameAr default the Item's nameAr (DBF-CAT-003 → DBF-CAT-123), sortOrder next (DBF-CAT-125), isPrimaryFl true when no active image exists (DBF-CAT-126), isActiveFl true (DBF-CAT-127), itemFk (DBF-CAT-128) (QR-CAT-056) → record cat.CatalogueChanged CREATED → return MediaView; on any rejection or rollback no media row is created and CAT stores no byte (an unattached file stays with the given FILE module under its retention — ADR-CAT-056 §4)
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-054 FIND_ALL · QR-CAT-012 FIND_ONE · QR-CAT-056 SAVE · join NONE · READ_WRITE
Concurrency  : the 20-image limit and "first image is primary" are decided under the Item lock; UQ_CAT_ITEM_MEDIA_PRIMARY backs at most one primary
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : image descriptions in ar and en
<!-- API:API-CAT-018:END -->

<!-- API:API-CAT-019:START traces=REQ-CAT-135,REQ-CAT-106,REQ-CAT-112,DBF-CAT-121,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-128,DBF-CAT-129,DBF-CAT-131 -->
### API-CAT-019 — update image
Entity       : ENT-CAT-010 · operation UPDATE · serves SCR-REQ-CAT-001 "update"
Honours: CON-CAT-018
Endpoint     : /api/v1/cat/media/{id}   verb: PUT
Layers       : CatMediaController.update → CatMediaService.update
Request      : path {id: int64 required DBF-CAT-121} · body ItemMediaUpdateRequest {nameAr: string(200) required DBF-CAT-123; nameEn: string(200) DBF-CAT-124; sortOrder: int32 required DBF-CAT-125; isPrimaryFl: boolean required DBF-CAT-126; variantFk: int64 DBF-CAT-129; version: int64 required DBF-CAT-131}
Response     : 200 · ApiResponse<MediaView> · paginated: no
Validations  : RULE-CAT-053 (the Item must be active) · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-040 · RULE-CAT-039 (setting isPrimaryFl clears the former primary — success notice; clearing it on the only primary leaves it primary) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-422-MEDIA-VARIANT-MISMATCH, CAT-422-PARENT-INACTIVE, CAT-200-PRIMARY-IMAGE-REPLACED, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the media (QR-CAT-055) → lock its Item (QR-CAT-010; itemFk DBF-CAT-128) and refuse an inactive one (RULE-CAT-053) → variantFk check (QR-CAT-012) → when isPrimaryFl turns true: clear the former primary first (QR-CAT-057) → write nameAr, nameEn, sortOrder, isPrimaryFl (DBF-CAT-126), variantFk with the version check (QR-CAT-057) → record cat.CatalogueChanged UPDATED → return MediaView (with CAT-200-PRIMARY-IMAGE-REPLACED when the primary moved)
Repository   : QR-CAT-055 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-012 FIND_ONE · QR-CAT-057 UPDATE · join NONE · READ_WRITE
Concurrency  : two parallel "make primary" requests are serialised by the Item lock; clearing before setting keeps UQ_CAT_ITEM_MEDIA_PRIMARY satisfied; version check against lost updates
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : notice ar + en (CAT-200-PRIMARY-IMAGE-REPLACED)
<!-- API:API-CAT-019:END -->

<!-- API:API-CAT-020:START traces=REQ-CAT-107,REQ-CAT-111,DBF-CAT-121,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128 -->
### API-CAT-020 — remove image
Entity       : ENT-CAT-010 · operation DELETE · serves SCR-REQ-CAT-001 "remove image"
Honours: CON-CAT-018
Endpoint     : /api/v1/cat/media/{id}   verb: DELETE
Layers       : CatMediaController.remove → CatMediaService.remove
Request      : path {id: int64 required DBF-CAT-121}
Response     : 200 · ApiResponse<MediaView> · paginated: no
Validations  : RULE-CAT-049; the main-image effect of rule 039 (removing the primary promotes the remaining active image with the lowest sortOrder — REQ-CAT-111) is applied in the orchestration and raises nothing
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-500-CHANGE-NOT-SAVED
Orchestration : load (QR-CAT-055) → lock its Item (QR-CAT-010) → set isActiveFl false (DBF-CAT-127) and isPrimaryFl false (DBF-CAT-126) (QR-CAT-057) → when it was primary: the remaining active image with the lowest sortOrder (QR-CAT-054, DBF-CAT-125) gets isPrimaryFl true (QR-CAT-057) → record cat.CatalogueChanged DEACTIVATED (and UPDATED for the promoted image) → return MediaView; FILE_DOCUMENT_REF is kept and CAT never deletes the FILE document — its purge after the tenant's retention period is the given FILE module's (ADR-CAT-056 §4)
Repository   : QR-CAT-055 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-054 FIND_ALL · QR-CAT-057 UPDATE · join NONE · READ_WRITE
Concurrency  : the promotion decision is taken under the Item lock, so two parallel removals never leave zero or two primaries
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-020:END -->

<!-- API:API-CAT-021:START traces=REQ-CAT-064,REQ-CAT-069,REQ-CAT-070,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077 -->
### API-CAT-021 — create Category
Entity       : ENT-CAT-006 · operation CREATE · serves SCR-REQ-CAT-002 "create"
Honours: CON-CAT-003
Endpoint     : /api/v1/cat/categories   verb: POST
Layers       : CatCategoryController.create → CatCategoryService.create
Request      : body CategoryCreateRequest {code: string(30) required DBF-CAT-072; nameAr: string(200) required DBF-CAT-073; nameEn: string(200) DBF-CAT-074; parentCategoryFk: int64 DBF-CAT-077; sortOrder: int32 DBF-CAT-075}
Response     : 201 · ApiResponse<CategoryView> · paginated: no
Validations  : RULE-CAT-001, RULE-CAT-002 · RULE-CAT-024 (code unique per tenant, 1–30 Latin letters / digits / hyphens; nameAr unique among active siblings) · RULE-CAT-023 (parent active; depth ≤ 5)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-CATEGORY-DUPLICATE, CAT-422-CATEGORY-PARENT-INVALID
Orchestration : take the tree lock (QR-CAT-073, advisory, ADR-CAT-047) → duplicate checks (QR-CAT-033) → parent (QR-CAT-032) and its depth (QR-CAT-037) → sortOrder default next under the parent (DBF-CAT-075) → persist with isActiveFl true (DBF-CAT-076) (QR-CAT-034) → return CategoryView
Repository   : QR-CAT-073 NATIVE (tree lock) · QR-CAT-033 EXISTS · QR-CAT-032 FIND_ONE · QR-CAT-037 NATIVE · QR-CAT-034 SAVE · join ADR-CAT-047 (recursive CTE) · READ_WRITE
Concurrency  : duplicate code / sibling name backed by UQ_CAT_CATEGORY_CODE and UQ_CAT_CATEGORY_NAME_AR (→ CAT-409-CATEGORY-DUPLICATE); depth and parent decisions under the tenant tree lock (QR-CAT-073)
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_CREATE
Localization : names in ar and en
<!-- API:API-CAT-021:END -->

<!-- API:API-CAT-022:START traces=REQ-CAT-066,REQ-CAT-067,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077 -->
### API-CAT-022 — read Category
Entity       : ENT-CAT-006 · operation VIEW · serves SCR-REQ-CAT-002 "read"
Honours: CON-CAT-011
Endpoint     : /api/v1/cat/categories/{id}   verb: GET
Layers       : CatCategoryController.get → CatCategoryQueryFacade.getCategory (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getCategory (also called by CatalogueReadApi.getCategory, with no CAT permission check — ADR-CAT-061)
Request      : path {id: int64 required DBF-CAT-071}
Response     : 200 · ApiResponse<CategoryView> · paginated: no
Validations  : none — an inactive Category is still returned (REQ-CAT-066)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load (QR-CAT-032) → return CategoryView
Repository   : QR-CAT-032 FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-022:END -->

<!-- API:API-CAT-023:START traces=REQ-CAT-065,REQ-CAT-069,REQ-CAT-070,DBF-CAT-071,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-077,DBF-CAT-079 -->
### API-CAT-023 — update Category
Entity       : ENT-CAT-006 · operation UPDATE · serves SCR-REQ-CAT-002 "update (move)"
Honours: CON-CAT-003
Endpoint     : /api/v1/cat/categories/{id}   verb: PUT
Layers       : CatCategoryController.update → CatCategoryService.update
Request      : path {id: int64 required DBF-CAT-071} · body CategoryUpdateRequest {nameAr: string(200) required DBF-CAT-073; nameEn: string(200) DBF-CAT-074; parentCategoryFk: int64 DBF-CAT-077; sortOrder: int32 required DBF-CAT-075; version: int64 required DBF-CAT-079} · code is fixed after creation and not accepted
Response     : 200 · ApiResponse<CategoryView> · paginated: no
Validations  : RULE-CAT-001, RULE-CAT-002 · RULE-CAT-024 (sibling name under the new parent) · RULE-CAT-023 (parent active, not itself, not one of its sub-categories; parent depth + height of the moved sub-tree ≤ 5); the Items keep their categoryFk (REQ-CAT-065)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-CATEGORY-DUPLICATE, CAT-422-CATEGORY-PARENT-INVALID
Orchestration : load (QR-CAT-032) → take the tree lock (QR-CAT-073) → duplicate checks (QR-CAT-033) → ancestors of the new parent and descendants of this Category (QR-CAT-037) → write nameAr, nameEn, parentCategoryFk (DBF-CAT-077), sortOrder with the version check (QR-CAT-035) → return CategoryView
Repository   : QR-CAT-032 FIND_ONE · QR-CAT-073 NATIVE (tree lock) · QR-CAT-033 EXISTS · QR-CAT-037 NATIVE · QR-CAT-035 UPDATE · join ADR-CAT-047 (recursive CTE) · READ_WRITE
Concurrency  : two parallel moves (A under B, B under A) would each pass the cycle check — the tenant tree lock (QR-CAT-073: `pg_advisory_xact_lock(:tenant, hashtext('CAT_CATEGORY_TREE'))`) serialises every create and move; version check against lost updates
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_UPDATE
Localization : names in ar and en
<!-- API:API-CAT-023:END -->

<!-- API:API-CAT-024:START traces=REQ-CAT-066,REQ-CAT-071,REQ-CAT-069,DBF-CAT-071,DBF-CAT-076,DBF-CAT-077,DBF-CAT-014,DBF-CAT-015 -->
### API-CAT-024 — deactivate Category
Entity       : ENT-CAT-006 · operation DELETE · serves SCR-REQ-CAT-002 "deactivate"
Honours: CON-CAT-003
Endpoint     : /api/v1/cat/categories/{id}   verb: DELETE
Layers       : CatCategoryController.deactivate → CatCategoryService.deactivate
Request      : path {id: int64 required DBF-CAT-071}
Response     : 200 · ApiResponse<CategoryView> · paginated: no
Validations  : RULE-CAT-025 (no active sub-category, no active Item) · RULE-CAT-048
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-CATEGORY-IN-USE, CAT-200-DEACTIVATED
Orchestration : take the tenant tree lock (QR-CAT-073 — the same lock as API-CAT-021, 023 and 053, ADR-CAT-047) → load and lock the Category (QR-CAT-032, FOR UPDATE) → count active sub-categories (DBF-CAT-077) and active Items (DBF-CAT-015, DBF-CAT-014) (QR-CAT-038) → set isActiveFl false (DBF-CAT-076) (QR-CAT-035) → return CategoryView with CAT-200-DEACTIVATED
Repository   : QR-CAT-073 NATIVE (tree lock) · QR-CAT-032 FIND_ONE (FOR UPDATE) · QR-CAT-038 COUNT · QR-CAT-035 UPDATE · join NONE · READ_WRITE
Concurrency  : the tree lock is taken before QR-CAT-038, so a parallel sub-category create or move (API-CAT-021, 023) or reactivation (API-CAT-053), which read the parent under the same lock, is serialised with the count: either the child commits first and the count refuses the deactivation (CAT-409-CATEGORY-IN-USE), or the deactivation commits first and the child sees an inactive parent (CAT-422-CATEGORY-PARENT-INVALID) — no active child is ever left under an inactive parent (RULE-CAT-023, AC-CAT-153); Item saves read the Category FOR SHARE (API-CAT-001, 003, 049), so an Item is never attached to a Category being deactivated
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_DELETE
Localization : success notice ar + en
<!-- API:API-CAT-024:END -->

<!-- API:API-CAT-025:START traces=REQ-CAT-012,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-055,DBF-CAT-036,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-040 -->
### API-CAT-025 — create Variant attribute
Entity       : ENT-CAT-003 · operation CREATE · serves SCR-REQ-CAT-003 "create"
Endpoint     : /api/v1/cat/variant-attributes   verb: POST
Layers       : CatVariantAttributeController.create → CatVariantAttributeService.create
Request      : body VariantAttributeCreateRequest {code: string(30) required DBF-CAT-036; nameAr: string(200) required DBF-CAT-037; nameEn: string(200) DBF-CAT-038; sortOrder: int32 DBF-CAT-039}
Response     : 201 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : RULE-CAT-008 / RULE-CAT-009 (`variants`, read on this request — REQ-CAT-012) · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-016
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : read `variants` → duplicate checks (QR-CAT-023) → sortOrder default next (DBF-CAT-039) → persist with isActiveFl true (DBF-CAT-040) (QR-CAT-024) → return VariantAttributeView
Repository   : QR-CAT-023 EXISTS · QR-CAT-024 SAVE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_VAR_ATTR_CODE / UQ_CAT_VAR_ATTR_NAME_AR reject the second of two parallel creates (→ CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE); a sortOrder tie is harmless (display order only)
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_CREATE
Localization : names in ar and en
<!-- API:API-CAT-025:END -->

<!-- API:API-CAT-026:START traces=REQ-CAT-048,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-039,DBF-CAT-040,DBF-CAT-047,DBF-CAT-053 -->
### API-CAT-026 — read Variant attribute
Entity       : ENT-CAT-003, ENT-CAT-004 · operation VIEW · serves SCR-REQ-CAT-003 "read"
Endpoint     : /api/v1/cat/variant-attributes/{id}   verb: GET
Layers       : CatVariantAttributeController.get → CatVariantAttributeService.get — ADR-CAT-045
Request      : path {id: int64 required DBF-CAT-035}
Response     : 200 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : none — a read stays allowed while `variants` is off (RULE-CAT-008), so existing attributes can be inspected and cleaned up
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load (QR-CAT-022) with its values by sortOrder (QR-CAT-026) → return VariantAttributeView
Repository   : QR-CAT-022 FIND_ONE · QR-CAT-026 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-026:END -->

<!-- API:API-CAT-027:START traces=REQ-CAT-043,REQ-CAT-055,DBF-CAT-035,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-042 -->
### API-CAT-027 — update Variant attribute
Entity       : ENT-CAT-003 · operation UPDATE · serves SCR-REQ-CAT-003 "update"
Endpoint     : /api/v1/cat/variant-attributes/{id}   verb: PUT
Layers       : CatVariantAttributeController.update → CatVariantAttributeService.update
Request      : path {id: int64 required DBF-CAT-035} · body VariantAttributeUpdateRequest {nameAr: string(200) required DBF-CAT-037; nameEn: string(200) DBF-CAT-038; sortOrder: int32 required DBF-CAT-039; version: int64 required DBF-CAT-042} · code fixed after creation
Response     : 200 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : RULE-CAT-008 / RULE-CAT-009 · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-016 (nameAr among active attributes); derived Variant names change with it (REQ-CAT-043)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : read `variants` → load (QR-CAT-022) → duplicate check (QR-CAT-023) → write nameAr, nameEn, sortOrder with the version check (QR-CAT-025) → return VariantAttributeView
Repository   : QR-CAT-022 FIND_ONE · QR-CAT-023 EXISTS · QR-CAT-025 UPDATE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_VAR_ATTR_NAME_AR backs the name; version check against lost updates
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_UPDATE
Localization : names in ar and en
<!-- API:API-CAT-027:END -->

<!-- API:API-CAT-028:START traces=REQ-CAT-044,REQ-CAT-054,DBF-CAT-035,DBF-CAT-040,DBF-CAT-061,DBF-CAT-063 -->
### API-CAT-028 — deactivate Variant attribute
Entity       : ENT-CAT-003 · operation DELETE · serves SCR-REQ-CAT-003 "deactivate (attribute and value)"
Endpoint     : /api/v1/cat/variant-attributes/{id}   verb: DELETE
Layers       : CatVariantAttributeController.deactivate → CatVariantAttributeService.deactivate
Request      : path {id: int64 required DBF-CAT-035}
Response     : 200 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : RULE-CAT-017 (no active assignment on an active Variant carries it); once inactive it is excluded from generation (REQ-CAT-044); allowed while `variants` is off, so attributes that depend on the flag can be cleaned up (RULE-CAT-008)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ATTRIBUTE-VALUE-IN-USE
Orchestration : load and lock (QR-CAT-022, FOR UPDATE) → count usage (QR-CAT-021 on DBF-CAT-063, DBF-CAT-061) → set isActiveFl false (DBF-CAT-040) (QR-CAT-025) → return VariantAttributeView
Repository   : QR-CAT-022 FIND_ONE (FOR UPDATE) · QR-CAT-021 COUNT · QR-CAT-025 UPDATE · join NONE · READ_WRITE
Concurrency  : generation reads the attribute FOR SHARE (QR-CAT-031 / QR-CAT-026), and a value create or reactivation reads it FOR SHARE (QR-CAT-022, API-CAT-029, 055), so neither a generation nor a new active value can commit under an attribute this deactivation makes inactive
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-028:END -->

<!-- API:API-CAT-029:START traces=REQ-CAT-016,REQ-CAT-045,REQ-CAT-055,DBF-CAT-035,DBF-CAT-048,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053 -->
### API-CAT-029 — create Variant attribute value
Entity       : ENT-CAT-004 · operation CREATE · serves SCR-REQ-CAT-003 "create"
Endpoint     : /api/v1/cat/variant-attributes/{id}/values   verb: POST
Layers       : CatVariantAttributeController.createValue → CatVariantAttributeService.createValue
Request      : path {id: int64 required DBF-CAT-035} · body VariantAttributeValueCreateRequest {code: string(30) required DBF-CAT-048; nameAr: string(200) required DBF-CAT-049; nameEn: string(200) DBF-CAT-050; sortOrder: int32 DBF-CAT-051}
Response     : 201 · ApiResponse<VariantAttributeValueView> · paginated: no
Validations  : RULE-CAT-008 / RULE-CAT-009 · the attribute must be active (otherwise CAT-400-REQUEST-INVALID) · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-016 (code per attribute, nameAr among the attribute's active values)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : read `variants` → load the attribute FOR SHARE (QR-CAT-022) and refuse an inactive one → duplicate checks (QR-CAT-028) → variantAttributeFk from the path (DBF-CAT-053), sortOrder default next within the attribute (DBF-CAT-051) → persist with isActiveFl true (DBF-CAT-052) (QR-CAT-029) → return VariantAttributeValueView
Repository   : QR-CAT-022 FIND_ONE (FOR SHARE) · QR-CAT-028 EXISTS · QR-CAT-029 SAVE · join NONE · READ_WRITE
Concurrency  : the attribute is read FOR SHARE (QR-CAT-022), as on value reactivation (API-CAT-055), so a parallel attribute deactivation (API-CAT-028, FOR UPDATE) waits until the create commits, or the create waits until the deactivation commits and then sees an inactive attribute (CAT-400-REQUEST-INVALID) — an active value is never committed under an attribute deactivated meanwhile; UQ_CAT_VAR_ATTR_VALUE_CODE / UQ_CAT_VAR_ATTR_VALUE_NAME_AR reject the second of two parallel creates
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_CREATE
Localization : names in ar and en
<!-- API:API-CAT-029:END -->

<!-- API:API-CAT-030:START traces=REQ-CAT-046,REQ-CAT-055,DBF-CAT-047,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-055 -->
### API-CAT-030 — update Variant attribute value
Entity       : ENT-CAT-004 · operation UPDATE · serves SCR-REQ-CAT-003 "update"
Endpoint     : /api/v1/cat/variant-attribute-values/{id}   verb: PUT
Layers       : CatVariantAttributeController.updateValue → CatVariantAttributeService.updateValue
Request      : path {id: int64 required DBF-CAT-047} · body VariantAttributeValueUpdateRequest {nameAr: string(200) required DBF-CAT-049; nameEn: string(200) DBF-CAT-050; sortOrder: int32 required DBF-CAT-051; version: int64 required DBF-CAT-055}
Response     : 200 · ApiResponse<VariantAttributeValueView> · paginated: no
Validations  : RULE-CAT-008 / RULE-CAT-009 · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-016; derived Variant names follow the new name (REQ-CAT-046)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : read `variants` → load (QR-CAT-027) → duplicate check (QR-CAT-028) → write nameAr, nameEn, sortOrder with the version check (QR-CAT-030) → return VariantAttributeValueView
Repository   : QR-CAT-027 FIND_ONE · QR-CAT-028 EXISTS · QR-CAT-030 UPDATE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_VAR_ATTR_VALUE_NAME_AR backs the name; version check against lost updates
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_UPDATE
Localization : names in ar and en
<!-- API:API-CAT-030:END -->

<!-- API:API-CAT-031:START traces=REQ-CAT-047,REQ-CAT-054,DBF-CAT-047,DBF-CAT-052,DBF-CAT-061,DBF-CAT-064 -->
### API-CAT-031 — deactivate Variant attribute value
Entity       : ENT-CAT-004 · operation DELETE · serves SCR-REQ-CAT-003 "deactivate (attribute and value)"
Endpoint     : /api/v1/cat/variant-attribute-values/{id}   verb: DELETE
Layers       : CatVariantAttributeController.deactivateValue → CatVariantAttributeService.deactivateValue
Request      : path {id: int64 required DBF-CAT-047}
Response     : 200 · ApiResponse<VariantAttributeValueView> · paginated: no
Validations  : RULE-CAT-017; once inactive it is excluded from generation (REQ-CAT-047); allowed while `variants` is off, so values that depend on the flag can be cleaned up (RULE-CAT-008)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ATTRIBUTE-VALUE-IN-USE
Orchestration : load and lock (QR-CAT-027, FOR UPDATE) → count usage (QR-CAT-021 on DBF-CAT-064, DBF-CAT-061) → set isActiveFl false (DBF-CAT-052) (QR-CAT-030) → return VariantAttributeValueView
Repository   : QR-CAT-027 FIND_ONE (FOR UPDATE) · QR-CAT-021 COUNT · QR-CAT-030 UPDATE · join NONE · READ_WRITE
Concurrency  : generation reads the value FOR SHARE (QR-CAT-031), so a generation and a deactivation never both pass
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-031:END -->

<!-- API:API-CAT-032:START traces=REQ-CAT-075,REQ-CAT-091,REQ-CAT-092,REQ-CAT-098,DBF-CAT-110,DBF-CAT-137,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143 -->
### API-CAT-032 — create scale label layout
Entity       : ENT-CAT-011 · operation CREATE · serves SCR-REQ-CAT-006 "create"
Endpoint     : /api/v1/cat/scale-label-layouts   verb: POST
Layers       : CatSettingsController.createLayout → CatSettingsService.createLayout
Request      : body ScaleLabelLayoutCreateRequest {key: string(2) required DBF-CAT-137; scaleCodeLength: int32 DBF-CAT-140; embeddedValueTypeCode: string(50) DBF-CAT-141; valueDecimals: int32 DBF-CAT-142; valueAr: string(200) DBF-CAT-138; valueEn: string(200) DBF-CAT-139}
Response     : 201 · ApiResponse<ScaleLabelLayoutView> · paginated: no
Validations  : RULE-CAT-006 (embeddedValueTypeCode) · RULE-CAT-034 (key 02 or 20–29 not used by another active layout; scaleCodeLength 4–6; valueDecimals 0–3) · RULE-CAT-032 (no active GTIN / INTERNAL barcode begins with the key — its "on scale label layout create" trigger)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-SCALE-LAYOUT-INVALID, CAT-409-BARCODE-SCALE-PREFIX
Orchestration : defaults scaleCodeLength 5 (DBF-CAT-140), embeddedValueTypeCode WEIGHT (DBF-CAT-141), valueDecimals 3 (DBF-CAT-142), valueAr «ملصق ميزان {key}» (DBF-CAT-138), valueEn "Scale label {key}" (DBF-CAT-139) → active key check (QR-CAT-061) → scale-prefix lock (QR-CAT-074) → barcodes beginning with the key (QR-CAT-053 on DBF-CAT-110) → persist with isActiveFl true (DBF-CAT-143) (QR-CAT-062) → return ScaleLabelLayoutView
Repository   : QR-CAT-061 EXISTS · QR-CAT-074 NATIVE (scale-prefix lock) · QR-CAT-053 EXISTS · QR-CAT-062 SAVE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_SCALE_LABEL_LAYOUT_KEY rejects a parallel duplicate key; the advisory scale-prefix lock (QR-CAT-074, shared with API-CAT-014 / 015 / 049) prevents a clashing barcode being saved meanwhile
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_CREATE
Localization : descriptions in ar and en
<!-- API:API-CAT-032:END -->

<!-- API:API-CAT-033:START traces=REQ-CAT-093,REQ-CAT-098,DBF-CAT-136,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-145 -->
### API-CAT-033 — update scale label layout
Entity       : ENT-CAT-011 · operation UPDATE · serves SCR-REQ-CAT-006 "update"
Endpoint     : /api/v1/cat/scale-label-layouts/{id}   verb: PUT
Layers       : CatSettingsController.updateLayout → CatSettingsService.updateLayout
Request      : path {id: int64 required DBF-CAT-136} · body ScaleLabelLayoutUpdateRequest {embeddedValueTypeCode: string(50) required DBF-CAT-141; valueDecimals: int32 required DBF-CAT-142; valueAr: string(200) DBF-CAT-138; valueEn: string(200) DBF-CAT-139; version: int64 required DBF-CAT-145} · key and scaleCodeLength (DBF-CAT-140) are fixed after creation and not accepted, because the layout's SCALE barcodes were validated against that length (RULE-CAT-033 — ADR-CAT-064)
Response     : 200 · ApiResponse<ScaleLabelLayoutView> · paginated: no
Validations  : the layout must be active (DBF-CAT-143; otherwise CAT-400-REQUEST-INVALID, as on API-CAT-036) — an inactive layout is not reactivated: the owner creates a new layout under the same key (API-CAT-032), which the partial UQ_CAT_SCALE_LABEL_LAYOUT_KEY allows · RULE-CAT-006 · RULE-CAT-034 (valueDecimals 0–3); a new embeddedValueTypeCode or valueDecimals changes only how the embedded value of a label scanned afterwards is read (API-CAT-043), and every SCALE barcode keeps resolving because its length cannot change (ADR-CAT-064)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-SCALE-LAYOUT-INVALID
Orchestration : load (QR-CAT-060) and refuse an inactive layout (DBF-CAT-143) with CAT-400-REQUEST-INVALID → validate → write embeddedValueTypeCode, valueDecimals, valueAr, valueEn with the version check (QR-CAT-063) → return ScaleLabelLayoutView
Repository   : QR-CAT-060 FIND_ONE · QR-CAT-063 UPDATE · join NONE · READ_WRITE
Concurrency  : version check against lost updates; no uniqueness is decided (key and scaleCodeLength are not updatable)
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_UPDATE
Localization : descriptions in ar and en
<!-- API:API-CAT-033:END -->

<!-- API:API-CAT-034:START traces=REQ-CAT-094,DBF-CAT-136,DBF-CAT-143 -->
### API-CAT-034 — deactivate scale label layout
Entity       : ENT-CAT-011 · operation DELETE · serves SCR-REQ-CAT-006 "deactivate (layouts and Unit e-invoice codes)"
Endpoint     : /api/v1/cat/scale-label-layouts/{id}   verb: DELETE
Layers       : CatSettingsController.deactivateLayout → CatSettingsService.deactivateLayout
Request      : path {id: int64 required DBF-CAT-136}
Response     : 200 · ApiResponse<ScaleLabelLayoutView> · paginated: no
Validations  : none beyond existence — values with its prefix stop being decoded (REQ-CAT-094); the SCALE barcodes validated against it stay stored and active, and decode again only while an active layout with the same scaleCodeLength exists (ADR-CAT-064)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load (QR-CAT-060) → set isActiveFl false (DBF-CAT-143) (QR-CAT-063) → return ScaleLabelLayoutView
Repository   : QR-CAT-060 FIND_ONE · QR-CAT-063 UPDATE · join NONE · READ_WRITE
Concurrency  : NONE — a flag flip with no decision on other rows
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-034:END -->

<!-- API:API-CAT-035:START traces=REQ-CAT-075,REQ-CAT-120,REQ-CAT-124,DBF-CAT-151,DBF-CAT-152,DBF-CAT-153,DBF-CAT-154,DBF-CAT-155 -->
### API-CAT-035 — create Unit e-invoice code
Entity       : ENT-CAT-012 · operation CREATE · serves SCR-REQ-CAT-006 "create"
Endpoint     : /api/v1/cat/unit-einvoice-codes   verb: POST
Layers       : CatSettingsController.createUnitEinvoiceCode → CatSettingsService.createUnitEinvoiceCode
Request      : body UnitEinvoiceCodeCreateRequest {key: string(50) required DBF-CAT-151; einvoiceUnitCode: string(50) required DBF-CAT-154}
Response     : 201 · ApiResponse<UnitEinvoiceCodeView> · paginated: no
Validations  : RULE-CAT-006 (key in CAT_UOM, einvoiceUnitCode in CAT_EINVOICE_UNIT) · RULE-CAT-043 (no active row for the key)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-409-UNIT-EINVOICE-DUPLICATE
Orchestration : validate (MdlLookupApi) → active-key check (QR-CAT-066) → copy the CAT_UOM labels of the key into valueAr (DBF-CAT-152) and valueEn (DBF-CAT-153) → persist with isActiveFl true (DBF-CAT-155) (QR-CAT-067) → return UnitEinvoiceCodeView
Repository   : QR-CAT-066 EXISTS · QR-CAT-067 SAVE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_UNIT_EINVOICE_CODE_KEY rejects the second of two parallel creates (→ CAT-409-UNIT-EINVOICE-DUPLICATE)
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_CREATE
Localization : valueAr / valueEn from the lookup labels
<!-- API:API-CAT-035:END -->

<!-- API:API-CAT-036:START traces=REQ-CAT-075,REQ-CAT-121,DBF-CAT-150,DBF-CAT-154,DBF-CAT-157 -->
### API-CAT-036 — update Unit e-invoice code
Entity       : ENT-CAT-012 · operation UPDATE · serves SCR-REQ-CAT-006 "update"
Endpoint     : /api/v1/cat/unit-einvoice-codes/{id}   verb: PUT
Layers       : CatSettingsController.updateUnitEinvoiceCode → CatSettingsService.updateUnitEinvoiceCode
Request      : path {id: int64 required DBF-CAT-150} · body UnitEinvoiceCodeUpdateRequest {einvoiceUnitCode: string(50) required DBF-CAT-154; version: int64 required DBF-CAT-157}
Response     : 200 · ApiResponse<UnitEinvoiceCodeView> · paginated: no
Validations  : the row must be active (REQ-CAT-121; otherwise CAT-400-REQUEST-INVALID) · RULE-CAT-006
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE
Orchestration : load (QR-CAT-065) → validate → write einvoiceUnitCode (DBF-CAT-154) with the version check (QR-CAT-068) → return UnitEinvoiceCodeView
Repository   : QR-CAT-065 FIND_ONE · QR-CAT-068 UPDATE · join NONE · READ_WRITE
Concurrency  : version check against lost updates
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-036:END -->

<!-- API:API-CAT-037:START traces=REQ-CAT-122,DBF-CAT-150,DBF-CAT-155 -->
### API-CAT-037 — deactivate Unit e-invoice code
Entity       : ENT-CAT-012 · operation DELETE · serves SCR-REQ-CAT-006 "deactivate (layouts and Unit e-invoice codes)"
Endpoint     : /api/v1/cat/unit-einvoice-codes/{id}   verb: DELETE
Layers       : CatSettingsController.deactivateUnitEinvoiceCode → CatSettingsService.deactivateUnitEinvoiceCode
Request      : path {id: int64 required DBF-CAT-150}
Response     : 200 · ApiResponse<UnitEinvoiceCodeView> · paginated: no
Validations  : none beyond existence — reads of the unit then return an empty e-invoice unit (REQ-CAT-122)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load (QR-CAT-065) → set isActiveFl false (DBF-CAT-155) (QR-CAT-068) → return UnitEinvoiceCodeView
Repository   : QR-CAT-065 FIND_ONE · QR-CAT-068 UPDATE · join NONE · READ_WRITE
Concurrency  : NONE — a flag flip with no decision on other rows
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-037:END -->

<!-- API:API-CAT-053:START traces=REQ-CAT-136,DBF-CAT-071,DBF-CAT-073,DBF-CAT-076,DBF-CAT-077,DBF-CAT-079 -->
### API-CAT-053 — reactivate Category
Entity       : ENT-CAT-006 · operation UPDATE · serves SCR-REQ-CAT-002 "reactivate"
Honours: CON-CAT-003
Endpoint     : /api/v1/cat/categories/{id}   verb: PATCH
Layers       : CatCategoryController.reactivate → CatCategoryService.reactivate
Request      : path {id: int64 required DBF-CAT-071} · body CategoryStateRequest {isActiveFl: boolean required DBF-CAT-076} · schema: `isActiveFl` enum [true], no other property (deactivation is DELETE — API-CAT-024)
Response     : 200 · ApiResponse<CategoryView> · paginated: no
Validations  : RULE-CAT-023 (the parent DBF-CAT-077 is empty or active; depth and cycle are unchanged by a reactivation) · RULE-CAT-024 (no other active Category of the same parent carries its nameAr; the code DBF-CAT-072 is kept — UQ_CAT_CATEGORY_CODE spans active and inactive rows, so it is still its own) · no capability flag is read · its sub-categories and Items are not reactivated with it · an already active Category returns the same success (REQ-CAT-136, ADR-CAT-069)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-CATEGORY-PARENT-INVALID, CAT-409-CATEGORY-DUPLICATE
Orchestration : take the tenant tree lock (QR-CAT-073) → load and lock the Category (QR-CAT-032, FOR UPDATE) → read its parent by parentCategoryFk (QR-CAT-032) and refuse an inactive one (RULE-CAT-023) → sibling name check on nameAr (DBF-CAT-073) under the same parent, excluding this Category (QR-CAT-033) → set isActiveFl true (DBF-CAT-076) with the version read under the lock (DBF-CAT-079) (QR-CAT-035) → return CategoryView
Repository   : QR-CAT-073 NATIVE (tree lock) · QR-CAT-032 FIND_ONE (FOR UPDATE) · QR-CAT-033 EXISTS · QR-CAT-035 UPDATE · join NONE · READ_WRITE
Concurrency  : the tree lock (QR-CAT-073) serialises it with a parallel deactivation of its parent (API-CAT-024) and with creates and moves (API-CAT-021, 023), so it never activates a child under a parent being deactivated; UQ_CAT_CATEGORY_NAME_AR backs the sibling name
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-053:END -->

<!-- API:API-CAT-054:START traces=REQ-CAT-137,DBF-CAT-035,DBF-CAT-037,DBF-CAT-040,DBF-CAT-042 -->
### API-CAT-054 — reactivate Variant attribute
Entity       : ENT-CAT-003 · operation UPDATE · serves SCR-REQ-CAT-003 "reactivate (attribute and value)"
Endpoint     : /api/v1/cat/variant-attributes/{id}   verb: PATCH
Layers       : CatVariantAttributeController.reactivate → CatVariantAttributeService.reactivate
Request      : path {id: int64 required DBF-CAT-035} · body VariantAttributeStateRequest {isActiveFl: boolean required DBF-CAT-040} · schema: `isActiveFl` enum [true], no other property (deactivation is DELETE — API-CAT-028)
Response     : 200 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : RULE-CAT-016 (no other active Variant attribute carries its nameAr DBF-CAT-037; the code DBF-CAT-036 is kept — UQ_CAT_VAR_ATTR_CODE spans active and inactive rows) · no capability flag is read, like every reactivation (RULE-CAT-008) · its values keep their own state · an already active attribute returns the same success (REQ-CAT-137, ADR-CAT-069)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : load and lock (QR-CAT-022, FOR UPDATE) → name check among the active attributes, excluding this one (QR-CAT-023 with code empty) → set isActiveFl true (DBF-CAT-040) with the version read under the lock (DBF-CAT-042) (QR-CAT-025) → return VariantAttributeView
Repository   : QR-CAT-022 FIND_ONE (FOR UPDATE) · QR-CAT-023 EXISTS · QR-CAT-025 UPDATE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_VAR_ATTR_NAME_AR rejects a parallel create or reactivation that takes the same nameAr (→ CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE)
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-054:END -->

<!-- API:API-CAT-055:START traces=REQ-CAT-138,DBF-CAT-047,DBF-CAT-049,DBF-CAT-052,DBF-CAT-053,DBF-CAT-055 -->
### API-CAT-055 — reactivate Variant attribute value
Entity       : ENT-CAT-004 · operation UPDATE · serves SCR-REQ-CAT-003 "reactivate (attribute and value)"
Endpoint     : /api/v1/cat/variant-attribute-values/{id}   verb: PATCH
Layers       : CatVariantAttributeController.reactivateValue → CatVariantAttributeService.reactivateValue
Request      : path {id: int64 required DBF-CAT-047} · body VariantAttributeValueStateRequest {isActiveFl: boolean required DBF-CAT-052} · schema: `isActiveFl` enum [true], no other property (deactivation is DELETE — API-CAT-031)
Response     : 200 · ApiResponse<VariantAttributeValueView> · paginated: no
Validations  : its attribute (DBF-CAT-053) must be active (otherwise CAT-400-REQUEST-INVALID, as on value create — API-CAT-029) · RULE-CAT-016 (no other active value of the same attribute carries its nameAr DBF-CAT-049; the code is kept — UQ_CAT_VAR_ATTR_VALUE_CODE spans active and inactive rows) · no capability flag is read (RULE-CAT-008) · an already active value returns the same success (REQ-CAT-138, ADR-CAT-069)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : load and lock the value (QR-CAT-027, FOR UPDATE) → read its attribute FOR SHARE (QR-CAT-022 by variantAttributeFk) and refuse an inactive one → name check among the attribute's active values, excluding this one (QR-CAT-028 with code empty) → set isActiveFl true (DBF-CAT-052) with the version read under the lock (DBF-CAT-055) (QR-CAT-030) → return VariantAttributeValueView
Repository   : QR-CAT-027 FIND_ONE (FOR UPDATE) · QR-CAT-022 FIND_ONE (FOR SHARE) · QR-CAT-028 EXISTS · QR-CAT-030 UPDATE · join NONE · READ_WRITE
Concurrency  : the attribute is read FOR SHARE, so a parallel attribute deactivation (API-CAT-028, FOR UPDATE) waits; UQ_CAT_VAR_ATTR_VALUE_NAME_AR backs the name
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-055:END -->
<!-- SUB:SVC-API-CRUD:END -->

<!-- SUB:SVC-API-SEARCH:START traces=REQ-CAT-134,DBF-CAT-052,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-007,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-022,DBF-CAT-023,DBF-CAT-027,DBF-CAT-028,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-039,DBF-CAT-040,DBF-CAT-047,DBF-CAT-051,DBF-CAT-053,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-118,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129,DBF-CAT-136,DBF-CAT-137,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-150,DBF-CAT-151,DBF-CAT-154,DBF-CAT-155,REQ-CAT-003,REQ-CAT-014,REQ-CAT-015,REQ-CAT-048,REQ-CAT-067,REQ-CAT-068,REQ-CAT-086,REQ-CAT-090,REQ-CAT-094,REQ-CAT-095,REQ-CAT-097,REQ-CAT-100,REQ-CAT-101,REQ-CAT-109,REQ-CAT-115,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127 -->
### SUB SVC-API-SEARCH

<!-- API:API-CAT-038:START traces=REQ-CAT-003,REQ-CAT-014,REQ-CAT-015,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-007,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-110,DBF-CAT-113 -->
### API-CAT-038 — search Items
Entity       : ENT-CAT-001 · operation VIEW · serves SCR-REQ-CAT-001 "search", "list"
Endpoint     : /api/v1/cat/items   verb: GET
Layers       : CatItemController.search → CatItemService.search
Request      : query {text: string(200); categoryFk: int64 DBF-CAT-015; brandCode: string(50) DBF-CAT-007; itemKindCode: string(50) DBF-CAT-005; publishStateCode: string(50) DBF-CAT-013; isActiveFl: boolean DBF-CAT-014; page: int32; size: int32} · text matches nameAr (DBF-CAT-003), nameEn (DBF-CAT-004), code (DBF-CAT-002) or an active barcode (DBF-CAT-110, DBF-CAT-113)
Response     : 200 · ApiResponse<Page<ItemSummary>> · paginated: yes
Validations  : none — filters are the SRS B2 list; the kind filter is shown by the frontend only while `bundles` is on (REQ-CAT-015) and Variant fields only while `variants` is on (REQ-CAT-014); sorted by nameAr ascending; an empty page is success
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : build the criteria → page query (QR-CAT-005) → return the page of ItemSummary
Repository   : QR-CAT-005 FIND_BY_CRITERIA · join NONE (barcode match by EXISTS sub-query) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : names in ar and en; displayNameEn fallback
<!-- API:API-CAT-038:END -->

<!-- API:API-CAT-039:START traces=REQ-CAT-067,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077 -->
### API-CAT-039 — list Category tree
Entity       : ENT-CAT-006 · operation VIEW · serves SCR-REQ-CAT-002 "list (tree)"
Honours: CON-CAT-012
Endpoint     : /api/v1/cat/categories   verb: GET
Layers       : CatCategoryController.tree → CatCategoryQueryFacade.getCategoryTree (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getCategoryTree (also called by CatalogueReadApi.getCategoryTree, with no CAT permission check — ADR-CAT-061)
Request      : query {text: string(200); isActiveFl: boolean DBF-CAT-076} · text matches code (DBF-CAT-072), nameAr (DBF-CAT-073), nameEn (DBF-CAT-074); a matching node is returned with its ancestors
Response     : 200 · ApiResponse<List<CategoryNode>> · paginated: no
Validations  : none — every Category under its parent (DBF-CAT-077), each level ordered by sortOrder (DBF-CAT-075)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : load the tenant's Categories (QR-CAT-036) → filter → assemble the tree in memory → return the root nodes
Repository   : QR-CAT-036 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-039:END -->

<!-- API:API-CAT-040:START traces=REQ-CAT-068,DBF-CAT-071,DBF-CAT-076,DBF-CAT-077,DBF-CAT-014,DBF-CAT-015 -->
### API-CAT-040 — Items of a Category
Entity       : ENT-CAT-006, ENT-CAT-001 · operation VIEW · serves SCR-REQ-CAT-002 "list Items of a Category"
Honours: CON-CAT-013
Endpoint     : /api/v1/cat/categories/{id}/items   verb: GET
Layers       : CatCategoryController.items → CatCategoryQueryFacade.listCategoryItems (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.listCategoryItems (also called by CatalogueReadApi.listCategoryItems, with no CAT permission check — ADR-CAT-061)
Request      : path {id: int64 required DBF-CAT-071} · query {page: int32; size: int32}
Response     : 200 · ApiResponse<Page<ItemSummary>> · paginated: yes
Validations  : none — active Items (DBF-CAT-014) of the Category and of its active sub-categories (DBF-CAT-076, DBF-CAT-077), sorted by nameAr
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the Category (QR-CAT-032) → page of Items over the active sub-tree (QR-CAT-006, on DBF-CAT-015) → return the page
Repository   : QR-CAT-032 FIND_ONE · QR-CAT-006 NATIVE · join ADR-CAT-047 (recursive CTE) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-040:END -->

<!-- API:API-CAT-041:START traces=REQ-CAT-014,REQ-CAT-048,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-039,DBF-CAT-040,DBF-CAT-047,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053 -->
### API-CAT-041 — list Variant attributes with values
Entity       : ENT-CAT-003, ENT-CAT-004 · operation VIEW · serves SCR-REQ-CAT-003 "list"
Endpoint     : /api/v1/cat/variant-attributes   verb: GET
Layers       : CatVariantAttributeController.list → CatVariantAttributeService.list
Request      : query {text: string(200); isActiveFl: boolean DBF-CAT-040, DBF-CAT-052} · text matches code (DBF-CAT-036) or nameAr (DBF-CAT-037) · `isActiveFl` filters both the attributes (DBF-CAT-040) and their values (DBF-CAT-052): an attribute is returned only when its isActiveFl equals the filter, and of each returned attribute only the values whose isActiveFl equals the filter; omitting it returns every attribute with every value, active and inactive
Response     : 200 · ApiResponse<List<VariantAttributeView>> · paginated: no
Validations  : none — a read stays allowed while `variants` is off (RULE-CAT-008); the menu entry is absent while off (REQ-CAT-014) · attributes by sortOrder (DBF-CAT-039), values by sortOrder (DBF-CAT-051) · callers: the Variant generation picker sends `isActiveFl=true` (active attributes with their active values — REQ-CAT-044, REQ-CAT-047); the SCR-CAT-003 list sends no filter by default, so inactive attributes and values stay visible and can be reactivated (API-CAT-054, 055)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : load attributes with values (QR-CAT-026, the `isActiveFl` filter applied to both reads) → return the list
Repository   : QR-CAT-026 FIND_ALL · join NONE (two ordered reads) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-041:END -->

<!-- API:API-CAT-042:START traces=REQ-CAT-101,DBF-CAT-003,DBF-CAT-022,DBF-CAT-023,DBF-CAT-027,DBF-CAT-028,DBF-CAT-110,DBF-CAT-113,DBF-CAT-118 -->
### API-CAT-042 — search Variants
Entity       : ENT-CAT-002, ENT-CAT-009 · operation VIEW · serves SCR-REQ-CAT-005 "search Variants"
Endpoint     : /api/v1/cat/variants   verb: GET
Layers       : CatVariantController.search → CatVariantService.search
Request      : query {text: string(200); page: int32; size: int32} · text matches the Variant code (DBF-CAT-023), the Item nameAr (DBF-CAT-003) or an active barcode (DBF-CAT-110)
Response     : 200 · ApiResponse<Page<VariantSearchRow>> · paginated: yes
Validations  : none — active Variants (DBF-CAT-027) of active Items (DBF-CAT-014) only (REQ-CAT-101, ADR-CAT-059), each with its Item nameAr and its earliest-created active barcode (DBF-CAT-118; empty when none); sorted by Item nameAr then code
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : page query (QR-CAT-016) → derived Variant names for the page (QR-CAT-019) → return the page
Repository   : QR-CAT-016 FIND_BY_CRITERIA · QR-CAT-019 FIND_ALL · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-005 Barcode labels · PERM_CAT_BARCODE_LABELS_VIEW
Localization : derived names in ar and en
<!-- API:API-CAT-042:END -->

<!-- API:API-CAT-043:START traces=REQ-CAT-086,REQ-CAT-090,REQ-CAT-094,REQ-CAT-097,REQ-CAT-100,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-137,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143 -->
### API-CAT-043 — barcode and scale label lookup
Entity       : ENT-CAT-009, ENT-CAT-002 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-010
Endpoint     : /api/v1/cat/barcodes/lookup   verb: GET
Layers       : CatBarcodeController.lookup → CatBarcodeQueryFacade.lookup (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.lookup (also called by CatalogueReadApi.lookupBarcode, with no CAT permission check — ADR-CAT-061)
Request      : query {value: string(48) required DBF-CAT-110}
Response     : 200 · ApiResponse<BarcodeLookupResult> · paginated: no
Validations  : scale label — a 13-digit value beginning with the key (DBF-CAT-137) of an active layout (DBF-CAT-143): RULE-CAT-035 (valid EAN-13 check digit; the scaleCodeLength (DBF-CAT-140) digits after the prefix equal an active SCALE barcode) · plain barcode — RULE-CAT-047 (no active barcode on an active Variant of an active Item and no active layout matches — REQ-CAT-086, ADR-CAT-059); a scale code resolves only to an active Variant of an active Item as well
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-422-SCALE-LABEL-UNREADABLE, CAT-404-BARCODE-NOT-FOUND
Orchestration : active layouts (QR-CAT-059) → when the value is a scale label: check digit, cut the scale code, find the active SCALE barcode (QR-CAT-052, barcodeTypeCode DBF-CAT-111 = SCALE), embedded value = the next 10 − scaleCodeLength digits ÷ 10^valueDecimals (DBF-CAT-142), typed by embeddedValueTypeCode (DBF-CAT-141), in the Item's base unit → otherwise exact active barcode (QR-CAT-052) → return Variant, Item and the barcode's uomCode (DBF-CAT-112)
Repository   : QR-CAT-059 FIND_ALL · QR-CAT-052 FIND_ONE · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : messages ar + en from the Error Catalog (next action in each)
<!-- API:API-CAT-043:END -->

<!-- API:API-CAT-044:START traces=REQ-CAT-109,DBF-CAT-022,DBF-CAT-028,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129 -->
### API-CAT-044 — images of a Variant
Entity       : ENT-CAT-010, ENT-CAT-002 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-015
Endpoint     : /api/v1/cat/variants/{id}/media   verb: GET
Layers       : CatMediaController.ofVariant → CatMediaQueryFacade.getVariantImages (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getVariantImages (also called by CatalogueReadApi.getVariantImages, with no CAT permission check — ADR-CAT-061)
Request      : path {id: int64 required DBF-CAT-022}
Response     : 200 · ApiResponse<List<MediaView>> · paginated: no
Validations  : none — the active media bound to the Variant (DBF-CAT-129), else the Item's active media bound to no Variant; primary (DBF-CAT-126) first, then sortOrder (DBF-CAT-125)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the Variant (QR-CAT-012) → images with fallback (QR-CAT-058) → return the list
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-058 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : image descriptions in ar and en
<!-- API:API-CAT-044:END -->

<!-- API:API-CAT-045:START traces=REQ-CAT-134,REQ-CAT-095,DBF-CAT-136,DBF-CAT-137,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-150,DBF-CAT-151,DBF-CAT-154,DBF-CAT-155 -->
### API-CAT-045 — read catalogue settings
Entity       : ENT-CAT-011, ENT-CAT-012 · operation VIEW · serves SCR-REQ-CAT-006 "read"
Endpoint     : /api/v1/cat/settings   verb: GET
Layers       : CatSettingsController.get → CatSettingsService.getSettings
Request      : none
Response     : 200 · ApiResponse<CatalogueSettings> · paginated: no
Validations  : none — every layout and every Unit e-invoice code, active or not, each with isActiveFl (DBF-CAT-143, DBF-CAT-155)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : load the layouts (QR-CAT-064) and the Unit e-invoice codes (QR-CAT-069) → return CatalogueSettings
Repository   : QR-CAT-064 FIND_ALL · QR-CAT-069 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_VIEW
Localization : descriptions in ar and en
<!-- API:API-CAT-045:END -->

<!-- API:API-CAT-046:START traces=REQ-CAT-115,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-013,DBF-CAT-014 -->
### API-CAT-046 — storefront catalogue read
Entity       : ENT-CAT-001 · operation VIEW · serves SCR-REQ-CAT-001 "list"
Honours: CON-CAT-014
Endpoint     : /api/v1/cat/storefront-items   verb: GET
Layers       : CatItemController.storefront → CatItemQueryFacade.listStorefrontItems (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.listStorefrontItems (also called by CatalogueReadApi.listStorefrontItems, with no CAT permission check — ADR-CAT-061) — ADR-CAT-045
Request      : query {page: int32; size: int32}
Response     : 200 · ApiResponse<Page<ItemSummary>> · paginated: yes
Validations  : none — only Items with isActiveFl true (DBF-CAT-014) and publishStateCode PUBLISHED (DBF-CAT-013), sorted by nameAr
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : page query (QR-CAT-007) → return the page
Repository   : QR-CAT-007 FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW (back-office preview; shoppers reach the data only through the in-process interface — ADR-CAT-045)
Localization : names in ar and en
<!-- API:API-CAT-046:END -->

<!-- API:API-CAT-047:START traces=REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-014,DBF-CAT-110,DBF-CAT-113 -->
### API-CAT-047 — catalogue search (global search)
Entity       : ENT-CAT-001, ENT-CAT-009 · operation VIEW · serves SCR-REQ-CAT-001 "search"
Honours: CON-CAT-016
Endpoint     : /api/v1/cat/catalogue-search   verb: GET
Layers       : CatSearchController.search → CatSearchQueryFacade.searchCatalogue (`@PreAuthorize("isAuthenticated()")` only — not the screen VIEW permission, so a caller without PERM_CAT_ITEMS_VIEW reaches the read service and is answered 200 with an empty list, never 403) → CatCatalogueReadService.searchCatalogue (checks PERM_CAT_ITEMS_VIEW of the current principal in code and returns an empty list when the caller lacks it; also called by CatalogueReadApi.searchCatalogue, so the HTTP path and the in-process path answer alike — ADR-CAT-061)
Request      : query {term: string(200) required}
Response     : 200 · ApiResponse<List<CatalogueSearchHit>> · paginated: no
Validations  : the caller's PERM_CAT_ITEMS_VIEW is checked first, in code by CatCatalogueReadService.searchCatalogue — without it the result is empty (REQ-CAT-127, ADR-CAT-045) · RULE-CAT-046 (term ≥ 2 characters) · ≤ 20 active Items (DBF-CAT-014) whose nameAr (DBF-CAT-003), nameEn (DBF-CAT-004), code (DBF-CAT-002) or active barcode (DBF-CAT-110, DBF-CAT-113) contains the term, exact code or barcode matches first
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-500-SERVER-ERROR, CAT-400-SEARCH-TERM-TOO-SHORT
Orchestration : permission check → term check → ranked search (QR-CAT-008) → return the hits
Repository   : QR-CAT-008 NATIVE · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW (absent → empty result, not 403)
Localization : message ar + en (CAT-400-SEARCH-TERM-TOO-SHORT)
<!-- API:API-CAT-047:END -->

<!-- API:API-CAT-052:START traces=REQ-CAT-073,REQ-CAT-074,REQ-CAT-076,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-013,DBF-CAT-025,DBF-CAT-097,DBF-CAT-111,DBF-CAT-112,DBF-CAT-141,DBF-CAT-151,DBF-CAT-154 -->
### API-CAT-052 — active values of a CAT lookup key
Entity       : ENT-CAT-001 · operation VIEW · serves SCR-REQ-CAT-001 "read" (the lookup-backed fields and filters) and SCR-REQ-CAT-006 "read"
Endpoint     : /api/v1/cat/lookups/{key}   verb: GET
Layers       : CatLookupController.read → CatLookupService.read (`@PreAuthorize("hasAnyAuthority(…)")` on the VIEW permission of any CAT page, own module only) → `com.erp.common.lookup.OwnedLookups.read` over `MdlLookupApi` — ADR-CAT-065
Request      : path {key: string(50) required} · one of the 10 CAT keys of SRS A6: CAT_ITEM_KIND, CAT_PUBLISH_STATE, CAT_BRAND, CAT_UOM, CAT_TAX_CLASS, CAT_EINVOICE_ITEM_CODE_TYPE, CAT_EINVOICE_UNIT, CAT_BARCODE_TYPE, CAT_SCALE_VALUE_TYPE, CAT_CHANGE_KIND
Response     : 200 · ApiResponse<List<LookupOption>> · paginated: no
Validations  : the key must be one of the 10 CAT keys (any other key, or one mdl answers 404 for, gives CAT-404-LOOKUP-KEY-UNKNOWN; never a pass-through — checklist C6) · only the active values, in mdl's sortOrder (REQ-CAT-073, REQ-CAT-074); a value deactivated later is no longer offered, while the records that hold it keep it (REQ-CAT-076)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-404-LOOKUP-KEY-UNKNOWN
Orchestration : OwnedLookups.read(key, CAT_LOOKUP_KEYS, CatErrorCodes.LOOKUP_KEY_UNKNOWN, mdlLookupApi::readActiveValuesByKey) → map each LookupOptionView to LookupOption {code, labelAr, labelEn, sortOrder} → return the list; no CAT table is read
Repository   : none — no database operation (the values are read through MdlLookupApi)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : the VIEW permission of any CAT page — PERM_CAT_ITEMS_VIEW, PERM_CAT_CATEGORIES_VIEW, PERM_CAT_VARIANT_ATTRIBUTES_VIEW, PERM_CAT_ITEM_IMPORT_VIEW, PERM_CAT_BARCODE_LABELS_VIEW or PERM_CAT_SETTINGS_VIEW (every CAT screen shows lookup-backed fields)
Localization : labels in ar and en as mdl stores them
<!-- API:API-CAT-052:END -->
<!-- SUB:SVC-API-SEARCH:END -->

<!-- SUB:SVC-API-INT:START traces=REQ-CAT-139,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-027,DBF-CAT-028,DBF-CAT-061,DBF-CAT-062,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-118,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104 -->
### SUB SVC-API-INT

<!-- API:API-CAT-048:START traces=REQ-CAT-036,DBF-CAT-003,DBF-CAT-004,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-015,DBF-CAT-110 -->
### API-CAT-048 — download import sheet
Entity       : ENT-CAT-001 · operation VIEW · serves SCR-REQ-CAT-004 "download import sheet"
Endpoint     : /api/v1/cat/item-imports/sheet   verb: GET
Layers       : CatItemImportController.sheet → CatItemImportService.renderSheet (report CAT_ITEM_IMPORT_SHEET through the given PrintApi — ADR-CAT-057)
Request      : none
Response     : 200 · binary application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Validations  : none — one sheet, header row of the 11 import columns, each labelled in ar and en: nameAr (DBF-CAT-003), nameEn (DBF-CAT-004), baseUomCode (DBF-CAT-006), category code (resolved to DBF-CAT-015), brandCode (DBF-CAT-007), taxClassCode (DBF-CAT-008), einvoiceCodeTypeCode (DBF-CAT-009), einvoiceCode (DBF-CAT-010), GTIN barcode (DBF-CAT-110), descriptionAr (DBF-CAT-011), descriptionEn (DBF-CAT-012)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : PrintApi.render(templatePath "cat/item_import_sheet" with its _ar variant, PrintFormat.XLSX, data = the 11 column definitions of report CAT_ITEM_IMPORT_SHEET registered by CatItemImportSheetReportProvider, locale, fileName "item-import-sheet", permission PERM_CAT_ITEM_IMPORT_VIEW) → PrintResponses.toResponseEntity; CAT holds no workbook writer (checklist C10, F7); no database operation
Repository   : none — no database operation
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-004 Item import · PERM_CAT_ITEM_IMPORT_VIEW
Localization : header labels in ar and en
<!-- API:API-CAT-048:END -->

<!-- API:API-CAT-049:START traces=REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-139,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-023,DBF-CAT-024,DBF-CAT-027,DBF-CAT-028,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114 -->
### API-CAT-049 — import items
Entity       : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009 · operation CREATE · serves SCR-REQ-CAT-004 "submit file (custom)", "read result"
Honours: CON-CAT-001, CON-CAT-002, CON-CAT-005, CON-CAT-018
Endpoint     : /api/v1/cat/item-imports   verb: POST
Layers       : CatItemImportController.submit → CatItemImportService.importItems
Request      : body ItemImportRequest {fileDocumentRef: int64 required} · application/json; the client uploaded the xlsx beforehand through the given FILE endpoint (private storage), which returned fileDocumentRef (ADR-CAT-056) · each data row maps to nameAr (DBF-CAT-003), nameEn (DBF-CAT-004), baseUomCode (DBF-CAT-006), category code → categoryFk (DBF-CAT-015), brandCode (DBF-CAT-007), taxClassCode (DBF-CAT-008), einvoiceCodeTypeCode (DBF-CAT-009), einvoiceCode (DBF-CAT-010), GTIN barcodeValue (DBF-CAT-110), descriptionAr (DBF-CAT-011), descriptionEn (DBF-CAT-012)
Response     : 200 · ApiResponse<ImportResult> · paginated: no
Validations  : fileDocumentRef names an available FILE document of the caller's tenant (otherwise CAT-404-NOT-FOUND) · RULE-CAT-044 (xlsx and ≤ 10 MB, read through FileDocumentLookupApi; ≤ 5,000 data rows and a header equal to the sheet — otherwise the whole file is rejected) · RULE-CAT-045 per row: the checks of rules 001, 002, 003 (also against earlier valid rows of the file), 006, 026, 029 (also against earlier valid rows of the file), 030 and 042 — plus the scale-prefix check of rule 032, whose trigger covers every GTIN create — each failure reported through CAT-200-IMPORT-ROW-REJECTED with the failed rule's message as {reason} · a failing row is skipped and reported (row, column, reason in ar and en); for a name repeated inside the file, the RULE-CAT-003 {code} of the later row is the code the write pass gave to the earlier row's Item, so the rejection reasons are composed after the write pass · a GTIN repeated inside the file is rejected on the later row, column GTIN barcode, with the RULE-CAT-029 message as {reason}, where {code} is the code the write pass gave to the earlier row's Item — composed after the write pass, as for RULE-CAT-003; a GTIN that equals a stored active barcode takes {code} from QR-CAT-048 (the owning Item) · RULE-CAT-005 · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-IMPORT-FILE-REJECTED, CAT-200-IMPORT-ROW-REJECTED, CAT-500-CODE-NOT-ISSUED, CAT-500-CHANGE-NOT-SAVED
Orchestration : read the file's type, size and bytes through FileDocumentLookupApi and accept it (RULE-CAT-044) → validation pass, no write: per row resolve the category code (QR-CAT-032), check names, lookups (MdlLookupApi), e-invoice pair, name uniqueness (QR-CAT-002 + in-file set), barcode uniqueness (QR-CAT-048 + in-file set: the first valid row that carries a GTIN keeps it, every later row that repeats it is marked rejected under RULE-CAT-029 and points to that first row), GTIN format and scale prefixes (QR-CAT-059) → write pass in one transaction: take the scale-prefix lock once, at its start, when the file holds a GTIN row (QR-CAT-074), re-read the active layouts under it (QR-CAT-059) and re-check the GTIN rows in memory → re-read every Category referenced by a valid row with QR-CAT-032 FOR SHARE (in categoryPk order, after the scale-prefix lock) and reject each row whose Category is now inactive with RULE-CAT-026 (column category, reported through CAT-200-IMPORT-ROW-REJECTED with the RULE-CAT-026 message as {reason}) before its batch is inserted, so no imported active Item points to an inactive Category (RULE-CAT-026, A7 invariant 2, ADR-CAT-059) → process the remaining valid rows in batches of 500: draw the batch's Item codes (CAT_ITEM) and Variant codes (CAT_VARIANT) from NumberSeriesApi before the batch is inserted — one `next` call per code, because erp-core 1.6.0 publishes no block allocation (CORE-GAP-number-series-block-allocation.md, ADR-CAT-071) — then insert the batch with JDBC batching (batch size 500) under one savepoint; on a unique violation the batch is rolled back to its savepoint and replayed row by row, each row with its own savepoint, reusing the codes already drawn → per row: Item with code from CAT_ITEM (DBF-CAT-002), itemKindCode STANDARD (DBF-CAT-005), defaults PIECE / STANDARD, publishStateCode UNPUBLISHED (DBF-CAT-013), isActiveFl true (DBF-CAT-014) (QR-CAT-003) → default Variant with code from CAT_VARIANT (DBF-CAT-023), isDefaultFl true (DBF-CAT-024), isActiveFl true (DBF-CAT-027), itemFk (DBF-CAT-028) (QR-CAT-011) → GTIN barcode with barcodeTypeCode GTIN (DBF-CAT-111), uomCode the base unit (DBF-CAT-112), variantFk (DBF-CAT-114), isActiveFl true (DBF-CAT-113) (QR-CAT-049) → record cat.CatalogueChanged CREATED per record → compose the in-file rejection reasons of RULE-CAT-003 and RULE-CAT-029 with the code the earlier row's Item received → return rows read, Items created, rows rejected and the rejections (ADR-CAT-048)
Repository   : QR-CAT-032 FIND_ONE (validation pass: no lock; write pass: FOR SHARE) · QR-CAT-002 EXISTS · QR-CAT-048 EXISTS · QR-CAT-059 FIND_ALL · QR-CAT-074 NATIVE (scale-prefix lock, once per import) · QR-CAT-003 SAVE · QR-CAT-011 SAVE · QR-CAT-049 SAVE · join NONE · READ_WRITE
Concurrency  : lock order — the scale-prefix lock (QR-CAT-074) first (when the file holds a GTIN row), then the referenced Category rows FOR SHARE (QR-CAT-032, in categoryPk order), both held until the commit; a Category deactivated between the two passes is caught by the write-pass re-read and its rows are rejected with RULE-CAT-026, and a deactivation that starts after the re-read (API-CAT-024, FOR UPDATE) waits until the import commits, so an imported active Item never points to an inactive Category; a name or barcode taken by a parallel request between the two passes hits UQ_CAT_ITEM_NAME_AR / UQ_CAT_BARCODE_VALUE; the batch is replayed row by row and the row's own savepoint is rolled back, so only that row is reported rejected (RULE-CAT-003 / RULE-CAT-029); a number-series failure rolls the whole import back (RULE-CAT-005); the scale-prefix lock is taken once per import, not per row, and is held until the commit, so a layout create (API-CAT-032) waits at most the import's time budget · Time budget (ADR-CAT-071): a file of 5,000 valid rows completes in at most 60 s on the reference deployment (validation pass ≤ 15 s, write pass ≤ 45 s); the service transaction carries `@Transactional(timeout = 120)` and the request timeout of the endpoint is 120 s. When 120 s is exceeded the transaction is rolled back as a whole: no Item, Variant or barcode is stored and no catalogue-change event is delivered (RULE-CAT-049), the codes already drawn stay as gaps in the series (the given's REQUIRES_NEW allocation), the scale-prefix lock and the Category share locks are released, and the answer is CAT-500-CHANGE-NOT-SAVED (REQ-CAT-139, AC-CAT-161); the owner imports the file again, split into smaller files
Security     : SCR-REQ-CAT-004 Item import · PERM_CAT_ITEM_IMPORT_CREATE
Localization : rejection reasons in ar and en (CAT-200-IMPORT-ROW-REJECTED wraps the failed rule's message)
<!-- API:API-CAT-049:END -->

<!-- API:API-CAT-050:START traces=REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,DBF-CAT-022,DBF-CAT-023,DBF-CAT-027,DBF-CAT-110,DBF-CAT-113,DBF-CAT-114,DBF-CAT-118 -->
### API-CAT-050 — print barcode labels
Entity       : ENT-CAT-002, ENT-CAT-009 · operation CREATE · serves SCR-REQ-CAT-005 "create label request (print)"
Endpoint     : /api/v1/cat/barcode-labels   verb: POST
Layers       : CatBarcodeLabelController.print → CatBarcodeLabelService.print
Request      : body LabelRequest {lines: array{variantPk: int64 required DBF-CAT-022; copies: int32 required}} · copies is a request input, not stored
Response     : 200 · binary application/pdf
Validations  : RULE-CAT-036 (1–999 copies per Variant, ≤ 5,000 labels in total) · RULE-CAT-037 (every Variant has an active barcode; the ones without are listed)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-LABEL-COPIES-OUT-OF-RANGE, CAT-422-LABEL-VARIANT-NO-BARCODE
Orchestration : check the counts → load the Variants (QR-CAT-017, no lock; only active Variants of active Items — an id that is missing, inactive or of an inactive Item answers CAT-404-NOT-FOUND, ADR-CAT-059) → earliest-created active barcode per Variant (QR-CAT-047, DBF-CAT-118) → derived nameAr (QR-CAT-019) → render report CAT_BARCODE_LABEL through PrintApi with nameAr, code (DBF-CAT-023) and barcode (DBF-CAT-110) per copy → return the document; nothing is written
Repository   : QR-CAT-017 FIND_ALL · QR-CAT-047 FIND_ALL · QR-CAT-019 FIND_ALL · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-005 Barcode labels · PERM_CAT_BARCODE_LABELS_CREATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-050:END -->

<!-- API:API-CAT-051:START traces=REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,DBF-CAT-002,DBF-CAT-003,DBF-CAT-005,DBF-CAT-014,DBF-CAT-027,DBF-CAT-028,DBF-CAT-061,DBF-CAT-062 -->
### API-CAT-051 — capability turn-off check
Entity       : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005 · operation VIEW · serves SCR-REQ-CAT-006 "turn-off check (custom)"
Honours: CON-CAT-017
Endpoint     : /api/v1/cat/capability-checks/turn-off   verb: GET
Layers       : CatCapabilityController.turnOffCheck → CatCapabilityQueryFacade.checkTurnOff (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.checkTurnOff (also called by CatalogueReadApi.checkCapabilityTurnOff, with no CAT permission check — ADR-CAT-061)
Request      : query {flag: string(20) required} · `variants` or `bundles` (capability flag names, not lookup values); any other value → CAT-400-REQUEST-INVALID
Response     : 200 · ApiResponse<TurnOffCheckResult> · paginated: no
Validations  : RULE-CAT-010 (`variants`: active assignments DBF-CAT-061 on active Variants DBF-CAT-027 of active Items DBF-CAT-014) · RULE-CAT-011 (`bundles`: active Items of kind BUNDLE, DBF-CAT-005) — when dependants exist the answer is allowed = false with the rule's message; the check never changes a flag
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-200-VARIANTS-IN-USE, CAT-200-BUNDLES-IN-USE
Orchestration : count the dependent Items and list at most 20 by code (DBF-CAT-002) and nameAr (DBF-CAT-003) (QR-CAT-009) → allowed = count is 0 → messageAr / messageEn = the RULE-CAT-010 (`variants`) or RULE-CAT-011 (`bundles`) message with {count} filled when not allowed, empty when allowed — the same text the envelope carries, so the in-process `CatalogueReadApi.checkCapabilityTurnOff` returns it too (CON-CAT-017) → return TurnOffCheckResult
Repository   : QR-CAT-009 AGGREGATE · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — the answer is advisory to the flag owner and writes nothing (ADR-CAT-041)
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_VIEW
Localization : messages ar + en (count filled in)
<!-- API:API-CAT-051:END -->

#### In-process contract surface — `com.erp.cat.crossmodule.CatalogueReadApi` (ADR-CAT-045)
Other modules reach CAT only through this injected interface (`conventions.module_interface: in_process`). Each method calls `CatCatalogueReadService`, the same read service the endpoint's facade calls, so both surfaces return the same data (ADR-CAT-061). The read service carries no `@PreAuthorize`: the **calling module authorizes its own caller** against its own permissions before it calls CAT (SFR a shopper, SLS a cashier). The HTTP facades keep the screen permission, except `CatSearchQueryFacade.searchCatalogue`, which carries only `@PreAuthorize("isAuthenticated()")`. `searchCatalogue` is the one method whose read service checks `PERM_CAT_ITEMS_VIEW` of the current principal, in code, and answers an empty list without it (REQ-CAT-127), on both surfaces. Tenancy applies to every call. No HTTP client exists between modules.

| method | contract item | same service as |
|---|---|---|
| getItem(itemPk) | CON-CAT-007 | API-CAT-002 |
| getVariant(variantPk) | CON-CAT-008 | API-CAT-007 |
| getItemUnit(itemUnitPk) | CON-CAT-009 | API-CAT-011 |
| lookupBarcode(value) | CON-CAT-010 | API-CAT-043 |
| getCategory(categoryPk) | CON-CAT-011 | API-CAT-022 |
| getCategoryTree(activeOnly) | CON-CAT-012 | API-CAT-039 |
| listCategoryItems(categoryPk, page, size) | CON-CAT-013 | API-CAT-040 |
| listStorefrontItems(page, size) | CON-CAT-014 | API-CAT-046 |
| getVariantImages(variantPk) | CON-CAT-015 | API-CAT-044 |
| searchCatalogue(term) | CON-CAT-016 | API-CAT-047 — `CatSearchQueryFacade.searchCatalogue` carries only `@PreAuthorize("isAuthenticated()")`; `CatCatalogueReadService.searchCatalogue` checks PERM_CAT_ITEMS_VIEW in code and returns an empty list when the caller lacks it, so both surfaces answer alike |
| checkCapabilityTurnOff(flag) | CON-CAT-017 | API-CAT-051 |
| event cat.CatalogueChanged | CON-CAT-018 | every mutating endpoint (PHASE CORE) |
<!-- SUB:SVC-API-INT:END -->
<!-- PHASE:SVC-API:END -->

<!-- PHASE:DOC:START traces=REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-029,REQ-CAT-039,REQ-CAT-058,REQ-CAT-095,REQ-CAT-097,REQ-CAT-101,REQ-CAT-119,REQ-CAT-125,REQ-CAT-021 -->
## PHASE DOC — API documentation and DTO catalogue

- The endpoints publish OpenAPI documentation at the given's api-docs location; `api-verify` holds it to `api-spec-cat.yaml`, which this stage derives from the API blocks and the Error Catalog (§ API document). Each operation carries its `API-CAT-*` id as `x-api-id`, its traces as `x-traces`, and every catalog code it can raise under `x-error-codes` of the response keyed by the row's status.
- Error body (every non-2xx response): `LocalizedException → {code, messageAr, messageEn}`. Success body: `ApiResponse<T>` with `data`, and — when a RULE's notice is shown on success — `code`, `messageAr`, `messageEn` (ADR-CAT-048). Paged bodies: `Page<T>` with `content`, `page`, `size`, `totalElements`, `totalPages`.
- Message bundles (checklist C11): one entry per Error Catalog code in `messages.properties` (en) and `messages_ar.properties` (ar), copied from the catalog — the 53 RULE rows and the 7 PLATFORM-STD rows alike, so no `CAT-` code falls back to the core bundle; placeholders `{flag}`, `{count}`, `{code}`, `{prefix}`, `{length}`, `{row}`, `{column}`, `{reason}` are filled by the service.

### Response DTOs (request bodies are written inline on each `Request` line)
Grammar: `- DTO <Name>: <field> <type>; …` with types int64, int32, string, boolean, decimal, date-time, array<…> and DTO names. Every stored field maps to the DBF of the same property (manifest); derived fields are the manifest's "— (derived)" rows.

- DTO ItemSummary: itemPk int64; code string; nameAr string; nameEn string; displayNameEn string; categoryFk int64; brandCode string; itemKindCode string; publishStateCode string; isActiveFl boolean
- DTO ItemDetail: itemPk int64; code string; nameAr string; nameEn string; displayNameEn string; itemKindCode string; baseUomCode string; categoryFk int64; brandCode string; taxClassCode string; einvoiceCodeTypeCode string; einvoiceCode string; descriptionAr string; descriptionEn string; publishStateCode string; isActiveFl boolean; version int64; createdBy string; createdAt date-time; updatedBy string; updatedAt date-time; variants array<VariantSummary>; itemUnits array<ItemUnitView>; bundleComponents array<BundleComponentView>; media array<MediaView>
- DTO VariantSummary: variantPk int64; code string; nameAr string; nameEn string; isDefaultFl boolean; isActiveFl boolean; einvoiceCodeTypeCode string; einvoiceCode string; effectiveEinvoiceCodeTypeCode string; effectiveEinvoiceCode string; version int64; attributeValues array<AttributeValueView>; barcodes array<BarcodeView>
- DTO VariantDetail: variantPk int64; itemFk int64; code string; nameAr string; nameEn string; isDefaultFl boolean; isActiveFl boolean; baseUomCode string; taxClassCode string; einvoiceCodeTypeCode string; einvoiceCode string; effectiveEinvoiceCodeTypeCode string; effectiveEinvoiceCode string; einvoiceUnitCode string; version int64; attributeValues array<AttributeValueView>; barcodes array<BarcodeView>
- DTO VariantGenerationResult: itemPk int64; variants array<VariantSummary>
- DTO AttributeValueView: variantAttributeFk int64; variantAttributeCode string; variantAttributeValueFk int64; valueCode string; valueNameAr string; valueNameEn string
- DTO BarcodeView: barcodePk int64; variantFk int64; barcodeValue string; barcodeTypeCode string; uomCode string; isActiveFl boolean; createdAt date-time
- DTO ItemUnitView: itemUnitPk int64; itemFk int64; uomCode string; conversionFactor decimal; isPurchaseFl boolean; isSalesFl boolean; isActiveFl boolean; einvoiceUnitCode string; version int64
- DTO BundleComponentView: bundleComponentPk int64; bundleItemFk int64; componentVariantFk int64; componentVariantCode string; quantity decimal; sortOrder int32; isActiveFl boolean
- DTO MediaView: itemMediaPk int64; itemFk int64; variantFk int64; fileDocumentRef int64; nameAr string; nameEn string; sortOrder int32; isPrimaryFl boolean; isActiveFl boolean; version int64
- DTO CategoryView: categoryPk int64; code string; nameAr string; nameEn string; parentCategoryFk int64; sortOrder int32; isActiveFl boolean; version int64
- DTO CategoryNode: categoryPk int64; code string; nameAr string; nameEn string; parentCategoryFk int64; sortOrder int32; isActiveFl boolean; children array<CategoryNode>
- DTO VariantAttributeView: variantAttributePk int64; code string; nameAr string; nameEn string; sortOrder int32; isActiveFl boolean; version int64; values array<VariantAttributeValueView>
- DTO VariantAttributeValueView: variantAttributeValuePk int64; variantAttributeFk int64; code string; nameAr string; nameEn string; sortOrder int32; isActiveFl boolean; version int64
- DTO VariantSearchRow: variantPk int64; code string; itemPk int64; itemNameAr string; nameAr string; nameEn string; firstBarcodeValue string
- DTO BarcodeLookupResult: variantPk int64; variantCode string; itemPk int64; itemCode string; itemNameAr string; uomCode string; embeddedValue decimal; embeddedValueTypeCode string
- DTO CatalogueSettings: scaleLabelLayouts array<ScaleLabelLayoutView>; unitEinvoiceCodes array<UnitEinvoiceCodeView>
- DTO ScaleLabelLayoutView: scaleLabelLayoutPk int64; key string; valueAr string; valueEn string; scaleCodeLength int32; embeddedValueTypeCode string; valueDecimals int32; isActiveFl boolean; version int64
- DTO UnitEinvoiceCodeView: unitEinvoiceCodePk int64; key string; valueAr string; valueEn string; einvoiceUnitCode string; isActiveFl boolean; version int64
- DTO CatalogueSearchHit: itemPk int64; code string; nameAr string; nameEn string
- DTO TurnOffCheckResult: flag string; allowed boolean; dependentCount int32; dependentItems array<DependentItem>; messageAr string; messageEn string
- DTO LookupOption: code string; labelAr string; labelEn string; sortOrder int32
- DTO DependentItem: itemPk int64; code string; nameAr string
- DTO ImportResult: rowsRead int32; itemsCreated int32; rowsRejected int32; rejectedRows array<ImportRowRejection>
- DTO ImportRowRejection: rowNumber int32; column string; code string; messageAr string; messageEn string
<!-- PHASE:DOC:END -->

<!-- PHASE:SEC-BE:START traces=REQ-CAT-131,REQ-CAT-001,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-127 -->
## PHASE SEC-BE — security, backend half (R5)

Every endpoint verifies its `PERM_<PAGE_CODE>_<ACTION>` before processing: mutations by `@PreAuthorize` on their service method, reads by `@PreAuthorize` on their controller-side facade (ADR-CAT-061). API-CAT-052 accepts the VIEW permission of any CAT page (`hasAnyAuthority`), because every CAT screen shows lookup-backed fields; it is listed under SCR-REQ-CAT-001 and SCR-REQ-CAT-006, the pages whose fields and filters read it. A caller without it receives CAT-403-FORBIDDEN and nothing changes (REQ-CAT-131). The one exception, API-CAT-047, answers an empty result instead of 403 (REQ-CAT-127): its facade `CatSearchQueryFacade.searchCatalogue` carries only `@PreAuthorize("isAuthenticated()")`, so Spring Security never answers 403 before the service runs, and `CatCatalogueReadService.searchCatalogue` checks PERM_CAT_ITEMS_VIEW in code and returns an empty list when the caller lacks it — the HTTP path and `CatalogueReadApi.searchCatalogue` behave the same. No service checks a role name; only permission names. A forbidden call maps through `LocalizedException → {code, messageAr, messageEn}` to CAT-403-FORBIDDEN. VIEW is the gateway: without it no other action of the page applies. Page registration: `CatPermissions` (given `PermissionContributor`) registers each page code with its name in ar / en and the parent menu «الكتالوج / Catalog»; the given `sec` module derives the permission names (no SQL seed of permission rows — SRS Access summary); preset-role grants per ADR-CAT-046.

### SCR-REQ-CAT-001 — Items · page CAT_ITEMS «الأصناف / Items»
VIEW (Owner, Seller/cashier, Warehouse staff): API-CAT-002, 007, 011, 038, 043, 044, 046, 047, 052 · CREATE (Owner): API-CAT-001, 006, 010, 014, 015, 018 · UPDATE (Owner): API-CAT-003, 005, 008, 012, 017, 019 · DELETE (Owner): API-CAT-004, 009, 013, 016, 020

### SCR-REQ-CAT-002 — Categories · page CAT_CATEGORIES «الفئات / Categories»
VIEW (Owner, Seller/cashier, Warehouse staff): API-CAT-022, 039, 040 · CREATE (Owner): API-CAT-021 · UPDATE (Owner): API-CAT-023, 053 (reactivation) · DELETE (Owner): API-CAT-024

### SCR-REQ-CAT-003 — Variant attributes · page CAT_VARIANT_ATTRIBUTES «خصائص المتغيّرات / Variant attributes»
VIEW (Owner, Seller/cashier, Warehouse staff): API-CAT-026, 041 · CREATE (Owner): API-CAT-025, 029 · UPDATE (Owner): API-CAT-027, 030, 054, 055 (reactivations) · DELETE (Owner): API-CAT-028, 031. The menu entry is hidden by the frontend while `variants` is off; the backend refuses the creates and updates (API-CAT-025, 027, 029, 030) with RULE-CAT-008 (REQ-CAT-016), and keeps the reads, deactivations and reactivations (API-CAT-026, 028, 031, 041, 054, 055) open so that existing attributes can be cleaned up or restored.

### SCR-REQ-CAT-004 — Item import · page CAT_ITEM_IMPORT «استيراد الأصناف / Item import»
VIEW (Owner): API-CAT-048 · CREATE (Owner): API-CAT-049 · UPDATE, DELETE not granted (no endpoint)

### SCR-REQ-CAT-005 — Barcode labels · page CAT_BARCODE_LABELS «ملصقات الباركود / Barcode labels»
VIEW (Owner, Warehouse staff): API-CAT-042 · CREATE (Owner, Warehouse staff): API-CAT-050 · UPDATE, DELETE not granted (no endpoint)

### SCR-REQ-CAT-006 — Catalogue settings · page CAT_SETTINGS «إعدادات الكتالوج / Catalogue settings»
VIEW (Owner): API-CAT-045, 051, 052 · CREATE (Owner): API-CAT-032, 035 · UPDATE (Owner): API-CAT-033, 036 · DELETE (Owner): API-CAT-034, 037

```yaml name=permission-matrix
rows:
  - screen: SCR-REQ-CAT-001 Items
    entity: ENT-CAT-001
    api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-038, API-CAT-043, API-CAT-044, API-CAT-046, API-CAT-047, API-CAT-052]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_ITEMS_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_ITEMS_CREATE}
      UPDATE: {granted: true, permission: PERM_CAT_ITEMS_UPDATE}
      DELETE: {granted: true, permission: PERM_CAT_ITEMS_DELETE}
  - screen: SCR-REQ-CAT-002 Categories
    entity: ENT-CAT-006
    api: [API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-039, API-CAT-040, API-CAT-053]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_CATEGORIES_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_CATEGORIES_CREATE}
      UPDATE: {granted: true, permission: PERM_CAT_CATEGORIES_UPDATE}
      DELETE: {granted: true, permission: PERM_CAT_CATEGORIES_DELETE}
  - screen: SCR-REQ-CAT-003 Variant attributes
    entity: ENT-CAT-003
    api: [API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-041, API-CAT-054, API-CAT-055]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_VARIANT_ATTRIBUTES_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_VARIANT_ATTRIBUTES_CREATE}
      UPDATE: {granted: true, permission: PERM_CAT_VARIANT_ATTRIBUTES_UPDATE}
      DELETE: {granted: true, permission: PERM_CAT_VARIANT_ATTRIBUTES_DELETE}
  - screen: SCR-REQ-CAT-004 Item import
    entity: ENT-CAT-001
    api: [API-CAT-048, API-CAT-049]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_ITEM_IMPORT_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_ITEM_IMPORT_CREATE}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-CAT-005 Barcode labels
    entity: ENT-CAT-002
    api: [API-CAT-042, API-CAT-050]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_BARCODE_LABELS_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_BARCODE_LABELS_CREATE}
      UPDATE: {granted: false}
      DELETE: {granted: false}
  - screen: SCR-REQ-CAT-006 Catalogue settings
    entity: ENT-CAT-011
    api: [API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-045, API-CAT-051, API-CAT-052]
    actions:
      VIEW: {granted: true, permission: PERM_CAT_SETTINGS_VIEW}
      CREATE: {granted: true, permission: PERM_CAT_SETTINGS_CREATE}
      UPDATE: {granted: true, permission: PERM_CAT_SETTINGS_UPDATE}
      DELETE: {granted: true, permission: PERM_CAT_SETTINGS_DELETE}
```

### Bootstrap data
The rows every first call reads. Lookup keys and number series live in given tables but are CAT's to seed (db-script BLOCK 8 — PLATFORM tenant; every other tenant at its provisioning per ADR-CAT-058). Permissions are not seeded by SQL: each is derived by the given `sec` module from the page CAT registers, and each carries its preset-role grant (SRS B4, ADR-CAT-046).

```yaml name=bootstrap
rows:
  - {kind: lookup key, name: "CAT_ITEM_KIND", source: "db-script BLOCK 8a/8b — mdl lookup type + values STANDARD, BUNDLE (PLATFORM tenant)"}
  - {kind: lookup key, name: "CAT_PUBLISH_STATE", source: "db-script BLOCK 8a/8b — values UNPUBLISHED, PUBLISHED"}
  - {kind: lookup key, name: "CAT_BRAND", source: "db-script BLOCK 8a — type only", note: "no seeded value; the tenant maintains its brands in mdl"}
  - {kind: lookup key, name: "CAT_UOM", source: "db-script BLOCK 8a/8b — 8 values PIECE … LITRE"}
  - {kind: lookup key, name: "CAT_TAX_CLASS", source: "db-script BLOCK 8a/8b — values STANDARD, ZERO_RATED, EXEMPT"}
  - {kind: lookup key, name: "CAT_EINVOICE_ITEM_CODE_TYPE", source: "db-script BLOCK 8a/8b — values GS1, EGS"}
  - {kind: lookup key, name: "CAT_EINVOICE_UNIT", source: "db-script BLOCK 8a/8b — 8 values EA … DZN"}
  - {kind: lookup key, name: "CAT_BARCODE_TYPE", source: "db-script BLOCK 8a/8b — values GTIN, INTERNAL, SCALE"}
  - {kind: lookup key, name: "CAT_SCALE_VALUE_TYPE", source: "db-script BLOCK 8a/8b — values WEIGHT, PRICE"}
  - {kind: lookup key, name: "CAT_CHANGE_KIND", source: "db-script BLOCK 8a/8b — values CREATED, UPDATED, DEACTIVATED, REACTIVATED"}
  - {kind: number series, name: "CAT_ITEM", source: "db-script BLOCK 8c — given sequence module data, ITM-{SEQ:6}"}
  - {kind: number series, name: "CAT_VARIANT", source: "db-script BLOCK 8c — VAR-{SEQ:6}"}
  - {kind: number series, name: "CAT_INTERNAL_BARCODE", source: "db-script BLOCK 8c — {SEQ:10}"}
  - {kind: seed rows, name: "CAT_UNIT_EINVOICE_CODE (8 rows, D-12)", source: "db-script BLOCK 8d (PLATFORM); each new tenant: CatTenantProvisioningContributor (given TenantProvisioningContributor) — ADR-CAT-058"}
  - {kind: report, name: "CAT_BARCODE_LABEL", source: "CatBarcodeLabelReportProvider (given ReportProvider) at application start"}
  - {kind: report, name: "CAT_ITEM_IMPORT_SHEET", source: "CatItemImportSheetReportProvider (given ReportProvider) at application start — ADR-CAT-057", note: "rendered as xlsx through PrintApi by API-CAT-048; the given derives the report authority CAT:REPORT:CAT_ITEM_IMPORT_SHEET"}
  - {kind: tenant seed, name: "CAT lookup types and values (10 keys, 34 values) in each new tenant", source: "given mdl copies them from PLATFORM at tenant provisioning (erp-core modules.mdl) — ADR-CAT-058"}
  - {kind: tenant seed, name: "number series CAT_ITEM, CAT_VARIANT, CAT_INTERNAL_BARCODE in each new tenant", source: "given sequence copies them at tenant provisioning with the counter at 1 — ADR-CAT-058"}
  - {kind: module manifest, name: "META-INF/erp/modules/cat.yaml", source: "the CAT jar; read by the given module catalog at application start (PHASE CORE)"}
  - {kind: page, name: "CAT_ITEMS", source: "CatPermissions (given PermissionContributor) at application start"}
  - {kind: page, name: "CAT_CATEGORIES", source: "CatPermissions at application start"}
  - {kind: page, name: "CAT_VARIANT_ATTRIBUTES", source: "CatPermissions at application start"}
  - {kind: page, name: "CAT_ITEM_IMPORT", source: "CatPermissions at application start"}
  - {kind: page, name: "CAT_BARCODE_LABELS", source: "CatPermissions at application start"}
  - {kind: page, name: "CAT_SETTINGS", source: "CatPermissions at application start"}
  - {kind: permission, name: "PERM_CAT_ITEMS_VIEW", source: "given sec derives it from page CAT_ITEMS", note: "role grant: Owner, Seller/cashier, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEMS_CREATE", source: "given sec derives it from page CAT_ITEMS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEMS_UPDATE", source: "given sec derives it from page CAT_ITEMS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEMS_DELETE", source: "given sec derives it from page CAT_ITEMS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_CATEGORIES_VIEW", source: "given sec derives it from page CAT_CATEGORIES", note: "role grant: Owner, Seller/cashier, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_CATEGORIES_CREATE", source: "given sec derives it from page CAT_CATEGORIES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_CATEGORIES_UPDATE", source: "given sec derives it from page CAT_CATEGORIES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_CATEGORIES_DELETE", source: "given sec derives it from page CAT_CATEGORIES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_VARIANT_ATTRIBUTES_VIEW", source: "given sec derives it from page CAT_VARIANT_ATTRIBUTES", note: "role grant: Owner, Seller/cashier, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_VARIANT_ATTRIBUTES_CREATE", source: "given sec derives it from page CAT_VARIANT_ATTRIBUTES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_VARIANT_ATTRIBUTES_UPDATE", source: "given sec derives it from page CAT_VARIANT_ATTRIBUTES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_VARIANT_ATTRIBUTES_DELETE", source: "given sec derives it from page CAT_VARIANT_ATTRIBUTES", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEM_IMPORT_VIEW", source: "given sec derives it from page CAT_ITEM_IMPORT", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_ITEM_IMPORT_CREATE", source: "given sec derives it from page CAT_ITEM_IMPORT", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_BARCODE_LABELS_VIEW", source: "given sec derives it from page CAT_BARCODE_LABELS", note: "role grant: Owner, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_BARCODE_LABELS_CREATE", source: "given sec derives it from page CAT_BARCODE_LABELS", note: "role grant: Owner, Warehouse staff (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_SETTINGS_VIEW", source: "given sec derives it from page CAT_SETTINGS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_SETTINGS_CREATE", source: "given sec derives it from page CAT_SETTINGS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_SETTINGS_UPDATE", source: "given sec derives it from page CAT_SETTINGS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: permission, name: "PERM_CAT_SETTINGS_DELETE", source: "given sec derives it from page CAT_SETTINGS", note: "role grant: Owner (ADR-CAT-046)"}
  - {kind: capability flag, name: "variants", source: "core settings read through SettingsApi (flag owner OPEN, P-02 — ADR-CAT-041)", note: "CAT never writes it; an unreadable value refuses the operation (RULE-CAT-009)"}
  - {kind: capability flag, name: "bundles", source: "core settings read through SettingsApi (flag owner OPEN, P-02 — ADR-CAT-041)", note: "CAT never writes it"}
```
The frontend stage references these permission names; it does not redeclare them.
<!-- PHASE:SEC-BE:END -->

<!-- PHASE:ALIGN-BE:START traces=REQ-CAT-001,REQ-CAT-002,REQ-CAT-127 -->
## PHASE ALIGN-BE — alignment self-check (R6)

Each mark is the `gov.py analyze` result for the backing check, copied by the orchestrator after the stage; until then every row reads "pending".

| row | backing check | assertion | mark |
|---|---|---|---|
| TRACEABILITY | traces | every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF | pending |
| COVERED | orphans | every REQ is covered by ≥ 1 API or DBF | pending |
| BINDING (§2A) | value-agreement | every DBF names the same physical column here as the db-script declares for it | pending |
| MANIFEST (§4) | count-agrees | every total this plan states equals the rows it heads | pending |
| WRITERS | required-writer | every required column is written by an endpoint, or the row states why not | pending |
| QRC (§5) | orphans | every catalogued query is reached by ≥ 1 API | pending |
| API (R3) | code-format | every catalog code is an instance of the declared format and carries a status the platform can emit | pending |
| API DOCUMENT | api-spec-agree | every API block is one operation of api-spec-cat.yaml and every operation one block, agreeing on method and path | pending |
| ERROR RESPONSES | api-spec-errors | every catalog row is answered by an operation of api-spec-cat.yaml with its status and code | pending |
| DOCUMENT VALID | api-spec-valid | api-spec-cat.yaml validates against OPENAPI 3.1.0 and reaches every required item | pending |
| RULE INPUTS | data-source | every RULE enforced at runtime names where the data it READS comes from, or is deferred | pending |
| CROSS-MODULE | registry-agree | every registered XM is placed here, and every XM minted here is back-registered | pending |
| INTEGRATION | xm-block-complete | every edge is one complete block of the last phase, and nothing else names its target | pending |
| FOREIGN IDS | xref-resolve | every id of another module cited here is defined in that module's own registry | pending |
| BOOTSTRAP | bootstrap-complete | every lookup key and every permission has a row naming who produces it | pending |
| SECURITY (R5) | operation-resolves | every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission | pending |
| DEMAND (SRS) | operation-resolves | every operation an SRS screen names is built by an API, or the plan states why it is not | pending |
| DECISIONS | refs-exist | every ADR this plan cites exists on disk in analysis/decisions/CAT/ | pending |
| PATHS | paths-resolve | every path the generated manifest and execution state emit resolves to something that exists | pending |
| COVERAGE | (the report) | the clauses the analyze report lists as having examined nothing — verbatim, or `none` | pending |

```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C7.24
- C7.26
- C7.27
- C7.28
- C7.5
- C7.5b
```

### Coverage — ENT / DBF → phase → QR → XM
| ENT | DBF range | DATA-DOM SUB | QR | XM |
|---|---|---|---|---|
| ENT-CAT-001 | DBF-CAT-001 … 021 | MASTER | QR-CAT-001 … 010 | — |
| ENT-CAT-002 | DBF-CAT-022 … 034 | MASTER | QR-CAT-011 … 017, 075 | — |
| ENT-CAT-003 | DBF-CAT-035 … 046 | LOOKUP | QR-CAT-022 … 026 | — |
| ENT-CAT-004 | DBF-CAT-047 … 059 | LOOKUP | QR-CAT-027 … 031 | — |
| ENT-CAT-005 | DBF-CAT-060 … 070 | MASTER | QR-CAT-018 … 021 | — |
| ENT-CAT-006 | DBF-CAT-071 … 083 | MASTER | QR-CAT-032 … 038, 073 | — |
| ENT-CAT-007 | DBF-CAT-084 … 095 | MASTER | QR-CAT-039 … 042 | — |
| ENT-CAT-008 | DBF-CAT-096 … 108 | MASTER | QR-CAT-043 … 046, 071 | — |
| ENT-CAT-009 | DBF-CAT-109 … 120 | MASTER | QR-CAT-047 … 053, 072 | — |
| ENT-CAT-010 | DBF-CAT-121 … 135 | MASTER | QR-CAT-054 … 058 | — |
| ENT-CAT-011 | DBF-CAT-136 … 149 | LOOKUP | QR-CAT-059 … 064, 074 | — |
| ENT-CAT-012 | DBF-CAT-150 … 161 | LOOKUP | QR-CAT-065 … 070 | — |

### Coverage — RULE → API → catalog code
| RULE | API | catalog code |
|---|---|---|
| RULE-CAT-001 | API-CAT-001, API-CAT-003, API-CAT-018, API-CAT-019, API-CAT-021, API-CAT-023, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030 | CAT-422-NAME-AR-INVALID |
| RULE-CAT-002 | API-CAT-001, API-CAT-003, API-CAT-018, API-CAT-019, API-CAT-021, API-CAT-023, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030 | CAT-422-NAME-EN-TOO-LONG |
| RULE-CAT-003 | API-CAT-001, API-CAT-003, API-CAT-005 | CAT-409-ITEM-NAME-DUPLICATE |
| RULE-CAT-004 | API-CAT-001, API-CAT-003, API-CAT-008 | CAT-422-CODE-SYSTEM-ASSIGNED |
| RULE-CAT-005 | API-CAT-001, API-CAT-006, API-CAT-015, API-CAT-049 | CAT-500-CODE-NOT-ISSUED |
| RULE-CAT-006 | API-CAT-001, API-CAT-003, API-CAT-005, API-CAT-008, API-CAT-010, API-CAT-014, API-CAT-015, API-CAT-032, API-CAT-033, API-CAT-035, API-CAT-036 | CAT-422-LOOKUP-VALUE-INACTIVE |
| RULE-CAT-007 | API-CAT-003 | CAT-422-BASE-UOM-FIXED |
| RULE-CAT-008 | API-CAT-001, API-CAT-006, API-CAT-017, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030 | CAT-422-CAPABILITY-OFF |
| RULE-CAT-009 | API-CAT-001, API-CAT-006, API-CAT-017, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030 | CAT-500-CAPABILITY-UNREADABLE |
| RULE-CAT-010 | API-CAT-051 | CAT-200-VARIANTS-IN-USE |
| RULE-CAT-011 | API-CAT-051 | CAT-200-BUNDLES-IN-USE |
| RULE-CAT-012 | API-CAT-006 | CAT-201-EXISTING-VARIANTS-KEPT |
| RULE-CAT-013 | API-CAT-006 | CAT-409-VARIANT-COMBINATION-DUPLICATE |
| RULE-CAT-014 | API-CAT-006 | CAT-422-VARIANT-LIMIT-EXCEEDED |
| RULE-CAT-015 | API-CAT-009 | CAT-409-LAST-ACTIVE-VARIANT |
| RULE-CAT-016 | API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030, API-CAT-054, API-CAT-055 | CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE |
| RULE-CAT-017 | API-CAT-028, API-CAT-031 | CAT-409-ATTRIBUTE-VALUE-IN-USE |
| RULE-CAT-018 | API-CAT-001, API-CAT-017 | CAT-422-BUNDLE-COMPONENTS-REQUIRED |
| RULE-CAT-019 | API-CAT-001, API-CAT-005, API-CAT-017 | CAT-422-BUNDLE-COMPONENT-INVALID |
| RULE-CAT-020 | API-CAT-006 | CAT-422-BUNDLE-SINGLE-VARIANT |
| RULE-CAT-021 | API-CAT-004, API-CAT-009 | CAT-409-VARIANT-IN-ACTIVE-BUNDLE |
| RULE-CAT-022 | API-CAT-003 | CAT-422-ITEM-KIND-FIXED |
| RULE-CAT-023 | API-CAT-021, API-CAT-023, API-CAT-053 | CAT-422-CATEGORY-PARENT-INVALID |
| RULE-CAT-024 | API-CAT-021, API-CAT-023, API-CAT-053 | CAT-409-CATEGORY-DUPLICATE |
| RULE-CAT-025 | API-CAT-024 | CAT-409-CATEGORY-IN-USE |
| RULE-CAT-026 | API-CAT-001, API-CAT-003, API-CAT-005 | CAT-422-CATEGORY-INACTIVE |
| RULE-CAT-027 | API-CAT-010 | CAT-422-ITEM-UNIT-INVALID |
| RULE-CAT-028 | API-CAT-012 | CAT-422-CONVERSION-FACTOR-FIXED |
| RULE-CAT-029 | API-CAT-014, API-CAT-015 | CAT-409-BARCODE-DUPLICATE |
| RULE-CAT-030 | API-CAT-014 | CAT-422-GTIN-INVALID |
| RULE-CAT-031 | API-CAT-014, API-CAT-015 | CAT-422-BARCODE-UNIT-INVALID |
| RULE-CAT-032 | API-CAT-014, API-CAT-015, API-CAT-032 | CAT-409-BARCODE-SCALE-PREFIX |
| RULE-CAT-033 | API-CAT-014 | CAT-422-SCALE-CODE-LENGTH |
| RULE-CAT-034 | API-CAT-032, API-CAT-033 | CAT-422-SCALE-LAYOUT-INVALID |
| RULE-CAT-035 | API-CAT-043 | CAT-422-SCALE-LABEL-UNREADABLE |
| RULE-CAT-036 | API-CAT-050 | CAT-422-LABEL-COPIES-OUT-OF-RANGE |
| RULE-CAT-037 | API-CAT-050 | CAT-422-LABEL-VARIANT-NO-BARCODE |
| RULE-CAT-038 | API-CAT-018 | CAT-422-IMAGE-NOT-ACCEPTED |
| RULE-CAT-039 | API-CAT-019 | CAT-200-PRIMARY-IMAGE-REPLACED |
| RULE-CAT-040 | API-CAT-018, API-CAT-019 | CAT-422-MEDIA-VARIANT-MISMATCH |
| RULE-CAT-041 | API-CAT-005 | CAT-422-PUBLISH-BLOCKED |
| RULE-CAT-042 | API-CAT-001, API-CAT-003, API-CAT-008 | CAT-422-EINVOICE-CODE-INVALID |
| RULE-CAT-043 | API-CAT-035 | CAT-409-UNIT-EINVOICE-DUPLICATE |
| RULE-CAT-044 | API-CAT-049 | CAT-422-IMPORT-FILE-REJECTED |
| RULE-CAT-045 | API-CAT-049 | CAT-200-IMPORT-ROW-REJECTED |
| RULE-CAT-046 | API-CAT-047 | CAT-400-SEARCH-TERM-TOO-SHORT |
| RULE-CAT-047 | API-CAT-043 | CAT-404-BARCODE-NOT-FOUND |
| RULE-CAT-048 | API-CAT-004, API-CAT-009, API-CAT-013, API-CAT-016, API-CAT-024 | CAT-200-DEACTIVATED |
| RULE-CAT-049 | API-CAT-001, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-049 | CAT-500-CHANGE-NOT-SAVED |
| RULE-CAT-050 | API-CAT-005 | CAT-422-ITEM-NO-ACTIVE-VARIANT |
| RULE-CAT-051 | API-CAT-013 | CAT-409-ITEM-UNIT-HAS-BARCODES |
| RULE-CAT-052 | API-CAT-006 | CAT-422-VARIANT-ATTRIBUTE-MISSING |
| RULE-CAT-053 | API-CAT-003, API-CAT-008, API-CAT-010, API-CAT-012, API-CAT-014, API-CAT-015, API-CAT-018, API-CAT-019 | CAT-422-PARENT-INACTIVE |

### Coverage — XM → requires → tests
none — the XM register is empty; no edge, no `requires`, no edge test.
<!-- PHASE:ALIGN-BE:END -->

<!-- PHASE:INT-C:START traces=REQ-CAT-001,REQ-CAT-008 -->
## PHASE INT-C — integration (R7, the last phase)

No edge. The db-script XM register is `records: []` (SRS A8 `consumes: []`: CAT is first in the fixed module order and depends on no business module), and nothing in this plan reads another business module's data, so no XM is minted. This phase therefore carries no `XM-*` block and the splitter emits no integration package. Inbound use of CAT by other modules is their own edge, through CAT's published contract (`CatalogueReadApi`, PHASE SVC-API; catalogue-change events, PHASE CORE).
<!-- PHASE:INT-C:END -->

## QUERY REFERENCE CATALOG (QR) — CAT v2

Logical specifications only — the implementer rewrites each with the real entity classes and the project's query strategy. Every JPQL, derived and search-builder query runs under the given tenant filter; its spec still shows `TENANT_ID = :tenant` where it reads several tables. The six native queries — QR-CAT-006, 008, 009 and 037, and the two advisory locks QR-CAT-073 (Category tree) and QR-CAT-074 (scale prefix) — bypass that filter. The first four write `TENANT_ID = :tenant` on every table they read: the outer query, every sub-select and `EXISTS`, and both branches of every recursive CTE. The two locks read no table; they name TENANT_ID as the first key of `pg_advisory_xact_lock(int, int)`, with `hashtext` of the lock name as the second. Their repository methods carry `@TenantUnscoped(reason, adr = "ADR-CAT-047")` (erp-core checklist C3, F2). `:tenant` is bound from `TenantContext.require()`. Text filters bind `:textEsc` / `:termEsc`, the term with `\`, `%` and `_` escaped, and match names through `CAT_NORMALIZE_AR` (ADR-CAT-062). Defaults per operation follow the engine table (FIND_ONE not found → CAT-404-NOT-FOUND; FIND_BY_CRITERIA empty → success; SAVE / UPDATE READ_WRITE with PK and audit system-set; EXISTS excludes the current PK on update). Non-default joins, native queries and locks: ADR-CAT-047.

QR-CAT-001 — Item by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : FIND_ONE
API          : API-CAT-002, API-CAT-007
Intent       : the Item a read or a dependent write starts from; inactive Items included
Logical spec : SELECT * FROM CAT_ITEM WHERE ITEM_PK = :id
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : ITEM_PK EXACT · Result shape : full entity · Null handling : optional columns returned as null

QR-CAT-002 — Active Item with the same normalised Arabic name
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : EXISTS
API          : API-CAT-001, API-CAT-003, API-CAT-005, API-CAT-049
Intent       : RULE-CAT-003 — another active Item of the tenant carries the name; returns its CODE, which fills the {code} of the RULE-CAT-003 message (the next action)
Logical spec : SELECT ITEM_PK, CODE FROM CAT_ITEM WHERE TENANT_ID = :tenant AND IS_ACTIVE_FL AND CAT_NORMALIZE_AR(NAME_AR) = CAT_NORMALIZE_AR(:nameAr) AND ITEM_PK <> COALESCE(:excludePk, -1) LIMIT 1 (JPQL `function('CAT_NORMALIZE_AR', …)` under the tenant filter; the same expression as UQ_CAT_ITEM_NAME_AR — ADR-CAT-062)
Join : NONE · Transaction : READ_ONLY · Locking : NONE — UQ_CAT_ITEM_NAME_AR decides a race · Pagination : NO · Filters : NAME_AR EXACT (normalised) · Result shape : projection ITEM_PK, CODE · Null handling : excludePk null on create

QR-CAT-003 — Insert Item
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : SAVE
API          : API-CAT-001, API-CAT-049
Intent       : store a new Item; ITEM_PK from the identity clause, CODE from series CAT_ITEM
Logical spec : INSERT INTO CAT_ITEM (CODE, NAME_AR, NAME_EN, ITEM_KIND_CODE, BASE_UOM_CODE, BRAND_CODE, TAX_CLASS_CODE, EINVOICE_CODE_TYPE_CODE, EINVOICE_CODE, DESCRIPTION_AR, DESCRIPTION_EN, PUBLISH_STATE_CODE, IS_ACTIVE_FL, CATEGORY_FK, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…) RETURNING ITEM_PK
Join : NONE · Transaction : READ_WRITE · Locking : NONE — code allocated atomically; UQ_CAT_ITEM_CODE, UQ_CAT_ITEM_NAME_AR · Pagination : NO · Filters : — · Result shape : full entity · Null handling : optional fields stored null

QR-CAT-004 — Update Item
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : UPDATE
API          : API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-017
Intent       : header fields, active flag, publish state or version bump, with the optimistic-lock check
Logical spec : UPDATE CAT_ITEM SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE ITEM_PK = :id AND VERSION = :version
Join : NONE · Transaction : READ_WRITE · Locking : optimistic VERSION; 0 rows → CAT-409-STALE-VERSION · Pagination : NO · Filters : ITEM_PK EXACT · Result shape : count · Null handling : CODE, ITEM_KIND_CODE, BASE_UOM_CODE never in the SET list

QR-CAT-005 — Search Items
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : FIND_BY_CRITERIA
API          : API-CAT-038
Intent       : back-office Item list with the SRS B2 filters, sorted by nameAr (REQ-CAT-003)
Logical spec : SELECT i.* FROM CAT_ITEM i WHERE i.TENANT_ID = :tenant AND (:text IS NULL OR CAT_NORMALIZE_AR(i.NAME_AR) LIKE '%' || CAT_NORMALIZE_AR(:textEsc) || '%' ESCAPE '\' OR CAT_NORMALIZE_AR(i.NAME_EN) LIKE '%' || CAT_NORMALIZE_AR(:textEsc) || '%' ESCAPE '\' OR i.CODE ILIKE '%' || :textEsc || '%' ESCAPE '\' OR EXISTS (SELECT 1 FROM CAT_BARCODE b JOIN CAT_VARIANT v ON v.VARIANT_PK = b.VARIANT_FK AND v.TENANT_ID = :tenant WHERE b.TENANT_ID = :tenant AND v.ITEM_FK = i.ITEM_PK AND b.IS_ACTIVE_FL AND b.BARCODE_VALUE = :text)) AND (:categoryFk IS NULL OR i.CATEGORY_FK = :categoryFk) AND (:brandCode IS NULL OR i.BRAND_CODE = :brandCode) AND (:itemKindCode IS NULL OR i.ITEM_KIND_CODE = :itemKindCode) AND (:publishStateCode IS NULL OR i.PUBLISH_STATE_CODE = :publishStateCode) AND (:isActiveFl IS NULL OR i.IS_ACTIVE_FL = :isActiveFl) ORDER BY i.NAME_AR, i.ITEM_PK LIMIT :size OFFSET :page*:size
Join : NONE (barcode by EXISTS) · Transaction : READ_ONLY · Locking : NONE · Pagination : YES (Page<T>) · Filters : text LIKE (escaped; names normalised; trigram indexes — ADR-CAT-062), CATEGORY_FK / BRAND_CODE / ITEM_KIND_CODE / PUBLISH_STATE_CODE / IS_ACTIVE_FL EXACT · Result shape : full entity · Null handling : null filter = not applied

QR-CAT-006 — Active Items of a Category sub-tree
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : NATIVE
API          : API-CAT-040
Intent       : REQ-CAT-068 — active Items of the Category and of its active sub-categories
Logical spec : WITH RECURSIVE t AS (SELECT CATEGORY_PK FROM CAT_CATEGORY WHERE TENANT_ID = :tenant AND CATEGORY_PK = :id UNION ALL SELECT c.CATEGORY_PK FROM CAT_CATEGORY c JOIN t ON c.PARENT_CATEGORY_FK = t.CATEGORY_PK WHERE c.TENANT_ID = :tenant AND c.IS_ACTIVE_FL) SELECT i.* FROM CAT_ITEM i JOIN t ON i.CATEGORY_FK = t.CATEGORY_PK WHERE i.TENANT_ID = :tenant AND i.IS_ACTIVE_FL ORDER BY i.NAME_AR, i.ITEM_PK LIMIT :size OFFSET :page*:size
Join : required — ADR-CAT-047 (recursive CTE) · Transaction : READ_ONLY · Locking : NONE · Pagination : YES (Page<T>) · Filters : sub-tree SET · Tenant : native — `TENANT_ID = :tenant` in both CTE branches and on CAT_ITEM; the method carries @TenantUnscoped(reason, adr = "ADR-CAT-047") · Result shape : full entity · Null handling : —

QR-CAT-007 — Storefront Items
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : FIND_BY_CRITERIA
API          : API-CAT-046
Intent       : REQ-CAT-115 — only active and published Items
Logical spec : SELECT * FROM CAT_ITEM WHERE IS_ACTIVE_FL AND PUBLISH_STATE_CODE = 'PUBLISHED' ORDER BY NAME_AR, ITEM_PK LIMIT :size OFFSET :page*:size
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : YES (Page<T>) · Filters : IS_ACTIVE_FL, PUBLISH_STATE_CODE EXACT · Result shape : full entity · Null handling : —

QR-CAT-008 — Catalogue search, top 20
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : NATIVE
API          : API-CAT-047
Intent       : REQ-CAT-125 — ≤ 20 active Items matching name, code or active barcode; exact code / barcode first
Logical spec : SELECT i.ITEM_PK, i.CODE, i.NAME_AR, i.NAME_EN, CASE WHEN i.CODE = :term OR EXISTS (SELECT 1 FROM CAT_BARCODE b JOIN CAT_VARIANT v ON v.VARIANT_PK = b.VARIANT_FK AND v.TENANT_ID = :tenant AND v.IS_ACTIVE_FL WHERE b.TENANT_ID = :tenant AND v.ITEM_FK = i.ITEM_PK AND b.IS_ACTIVE_FL AND b.BARCODE_VALUE = :term) THEN 0 ELSE 1 END AS RANK FROM CAT_ITEM i WHERE i.TENANT_ID = :tenant AND i.IS_ACTIVE_FL AND (CAT_NORMALIZE_AR(i.NAME_AR) LIKE '%' || CAT_NORMALIZE_AR(:termEsc) || '%' ESCAPE '\' OR CAT_NORMALIZE_AR(i.NAME_EN) LIKE '%' || CAT_NORMALIZE_AR(:termEsc) || '%' ESCAPE '\' OR i.CODE ILIKE '%' || :termEsc || '%' ESCAPE '\' OR EXISTS (SELECT 1 FROM CAT_BARCODE b2 JOIN CAT_VARIANT v2 ON v2.VARIANT_PK = b2.VARIANT_FK AND v2.TENANT_ID = :tenant AND v2.IS_ACTIVE_FL WHERE b2.TENANT_ID = :tenant AND v2.ITEM_FK = i.ITEM_PK AND b2.IS_ACTIVE_FL AND b2.BARCODE_VALUE LIKE '%' || :termEsc || '%' ESCAPE '\')) ORDER BY RANK, i.NAME_AR LIMIT 20
Join : required — ADR-CAT-047 (ranking across Item and barcode) · Transaction : READ_ONLY · Locking : NONE · Pagination : NO (fixed 20) · Filters : term LIKE (escaped; names normalised — ADR-CAT-062) · Tenant : native — `TENANT_ID = :tenant` on CAT_ITEM and on CAT_BARCODE / CAT_VARIANT inside both barcode sub-queries; the method carries @TenantUnscoped(reason, adr = "ADR-CAT-047") · Result shape : projection ITEM_PK, CODE, NAME_AR, NAME_EN · Null handling : NAME_EN null returned as null

QR-CAT-009 — Capability turn-off dependants
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : AGGREGATE
API          : API-CAT-051
Intent       : RULE-CAT-010 / RULE-CAT-011 — count and first 20 Items that block a turn-off
Logical spec : variants: SELECT DISTINCT i.ITEM_PK, i.CODE, i.NAME_AR FROM CAT_ITEM i JOIN CAT_VARIANT v ON v.ITEM_FK = i.ITEM_PK AND v.TENANT_ID = :tenant AND v.IS_ACTIVE_FL JOIN CAT_VAR_ATTR_ASSIGN a ON a.VARIANT_FK = v.VARIANT_PK AND a.TENANT_ID = :tenant AND a.IS_ACTIVE_FL WHERE i.TENANT_ID = :tenant AND i.IS_ACTIVE_FL · bundles: SELECT ITEM_PK, CODE, NAME_AR FROM CAT_ITEM WHERE TENANT_ID = :tenant AND IS_ACTIVE_FL AND ITEM_KIND_CODE = 'BUNDLE' — each with COUNT(*) OVER () and ORDER BY CODE LIMIT 20
Join : required — ADR-CAT-047 · Transaction : READ_ONLY · Locking : NONE (advisory answer) · Pagination : NO (first 20 + total) · Filters : flag SET · Tenant : native — `TENANT_ID = :tenant` on CAT_ITEM, CAT_VARIANT and CAT_VAR_ATTR_ASSIGN; the method carries @TenantUnscoped(reason, adr = "ADR-CAT-047") · Result shape : count + projection · Null handling : —

QR-CAT-010 — Lock the Item row
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : FIND_ONE
API          : API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020
Intent       : serialise every Item-scoped decision (update, Variants, lines, units, barcodes, media, publish, deactivate, reactivate) and the inactive-parent check of RULE-CAT-053
Logical spec : SELECT * FROM CAT_ITEM WHERE ITEM_PK = :id FOR UPDATE
Join : NONE · Transaction : READ_WRITE · Locking : FOR UPDATE — ADR-CAT-047; lock order Item → Variant → lines · Pagination : NO · Filters : ITEM_PK EXACT · Result shape : full entity · Null handling : —

QR-CAT-011 — Insert Variant
Phase        : DATA-DOM · Entity : ENT-CAT-002 · Operation : SAVE
API          : API-CAT-001, API-CAT-006, API-CAT-049
Intent       : default Variant of a new Item, or a generated Variant
Logical spec : INSERT INTO CAT_VARIANT (CODE, IS_DEFAULT_FL, IS_ACTIVE_FL, ITEM_FK, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…) RETURNING VARIANT_PK
Join : NONE · Transaction : READ_WRITE · Locking : NONE — UQ_CAT_VARIANT_CODE, UQ_CAT_VARIANT_DEFAULT · Pagination : NO · Filters : — · Result shape : full entity · Null handling : override columns null

QR-CAT-012 — Variant by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-002 · Operation : FIND_ONE
API          : API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-014, API-CAT-015, API-CAT-018, API-CAT-019, API-CAT-044
Intent       : the Variant a read or a dependent write starts from; inactive included
Logical spec : SELECT * FROM CAT_VARIANT WHERE VARIANT_PK = :id
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : VARIANT_PK EXACT · Result shape : full entity · Null handling : override columns may be null

QR-CAT-013 — Active Variants of an Item
Phase        : DATA-DOM · Entity : ENT-CAT-002 · Operation : FIND_ALL
API          : API-CAT-002, API-CAT-006, API-CAT-009
Intent       : the Item's sellable Variants, default first; on a default-flag move (API-CAT-009) the first non-default row is the remaining active Variant with the lowest code (ADR-CAT-072)
Logical spec : SELECT * FROM CAT_VARIANT WHERE ITEM_FK = :itemPk AND IS_ACTIVE_FL ORDER BY IS_DEFAULT_FL DESC, CODE
Join : NONE · Transaction : READ_ONLY · Locking : NONE (callers that write hold the Item lock) · Pagination : NO · Filters : ITEM_FK EXACT · Result shape : full entity · Null handling : —

QR-CAT-014 — Update Variant
Phase        : DATA-DOM · Entity : ENT-CAT-002 · Operation : UPDATE
API          : API-CAT-006, API-CAT-008, API-CAT-009
Intent       : e-invoice override, deactivation, or setting isDefaultFl on the Variant that becomes the default (ADR-CAT-072 — always after QR-CAT-075 has cleared the former default)
Logical spec : UPDATE CAT_VARIANT SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE VARIANT_PK = :id AND VERSION = :version
Join : NONE · Transaction : READ_WRITE · Locking : optimistic VERSION · Pagination : NO · Filters : VARIANT_PK EXACT · Result shape : count · Null handling : both override columns null removes the override

QR-CAT-015 — Count active Variants of an Item
Phase        : DATA-DOM · Entity : ENT-CAT-002 · Operation : COUNT
API          : API-CAT-005, API-CAT-006, API-CAT-009
Intent       : RULE-CAT-014 (≤ 300), RULE-CAT-015 (last active), RULE-CAT-041 (≥ 1 to publish)
Logical spec : SELECT COUNT(*) FROM CAT_VARIANT WHERE ITEM_FK = :itemPk AND IS_ACTIVE_FL
Join : NONE · Transaction : READ_ONLY inside the caller's READ_WRITE · Locking : decided under the Item lock (QR-CAT-010) · Pagination : NO · Filters : ITEM_FK EXACT · Result shape : count · Null handling : —

QR-CAT-016 — Search Variants for labels
Phase        : DATA-DOM · Entity : ENT-CAT-002 · Operation : FIND_BY_CRITERIA
API          : API-CAT-042
Intent       : REQ-CAT-101 — active Variants of active Items (ADR-CAT-059) with their Item nameAr and earliest-created active barcode
Logical spec : SELECT v.VARIANT_PK, v.CODE, i.ITEM_PK, i.NAME_AR, (SELECT b.BARCODE_VALUE FROM CAT_BARCODE b WHERE b.TENANT_ID = :tenant AND b.VARIANT_FK = v.VARIANT_PK AND b.IS_ACTIVE_FL ORDER BY b.CREATED_AT, b.BARCODE_PK LIMIT 1) AS FIRST_BARCODE FROM CAT_VARIANT v JOIN CAT_ITEM i ON i.ITEM_PK = v.ITEM_FK AND i.TENANT_ID = :tenant WHERE v.TENANT_ID = :tenant AND v.IS_ACTIVE_FL AND i.IS_ACTIVE_FL AND (:text IS NULL OR v.CODE ILIKE '%' || :textEsc || '%' ESCAPE '\' OR CAT_NORMALIZE_AR(i.NAME_AR) LIKE '%' || CAT_NORMALIZE_AR(:textEsc) || '%' ESCAPE '\' OR EXISTS (SELECT 1 FROM CAT_BARCODE b WHERE b.TENANT_ID = :tenant AND b.VARIANT_FK = v.VARIANT_PK AND b.IS_ACTIVE_FL AND b.BARCODE_VALUE LIKE '%' || :textEsc || '%' ESCAPE '\')) ORDER BY i.NAME_AR, v.CODE LIMIT :size OFFSET :page*:size
Join : required — ADR-CAT-047 (the result shows the Item name) · Transaction : READ_ONLY · Locking : NONE · Pagination : YES (Page<T>) · Filters : text LIKE (escaped; names normalised — ADR-CAT-062) · Result shape : projection · Null handling : FIRST_BARCODE null when none

QR-CAT-017 — Variants (with their Item) by identifier list
Phase        : DATA-DOM · Entity : ENT-CAT-002 · Operation : FIND_ALL
API          : API-CAT-001, API-CAT-005, API-CAT-017, API-CAT-050
Intent       : component validation (RULE-CAT-019, at Bundle save and at Bundle reactivation) and label requests (active Variants of active Items only — ADR-CAT-059)
Logical spec : SELECT v.*, i.ITEM_KIND_CODE, i.IS_ACTIVE_FL AS ITEM_ACTIVE FROM CAT_VARIANT v JOIN CAT_ITEM i ON i.ITEM_PK = v.ITEM_FK AND i.TENANT_ID = :tenant WHERE v.TENANT_ID = :tenant AND v.VARIANT_PK IN (:ids) [labels: AND v.IS_ACTIVE_FL AND i.IS_ACTIVE_FL] [FOR SHARE OF v, i]
Join : required — ADR-CAT-047 (component Item kind) · Transaction : READ_WRITE (Bundle save) / READ_ONLY (labels) · Locking : FOR SHARE when called by a Bundle save or a Bundle reactivation, none for labels — a parallel Variant or Item deactivation cannot interleave · Pagination : NO · Filters : VARIANT_PK SET · Result shape : full entity + 2 Item columns · Null handling : a missing id → CAT-404-NOT-FOUND

QR-CAT-018 — Insert assignment
Phase        : DATA-DOM · Entity : ENT-CAT-005 · Operation : SAVE
API          : API-CAT-006
Intent       : the value a Variant carries for one attribute
Logical spec : INSERT INTO CAT_VAR_ATTR_ASSIGN (IS_ACTIVE_FL, VARIANT_FK, VARIANT_ATTRIBUTE_FK, VARIANT_ATTRIBUTE_VALUE_FK, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (TRUE, …)
Join : NONE · Transaction : READ_WRITE · Locking : NONE — UQ_CAT_VAR_ATTR_ASSIGN_ATTR and FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE hold the rules · Pagination : NO · Filters : — · Result shape : full entity · Null handling : —

QR-CAT-019 — Assignments with attribute and value names
Phase        : DATA-DOM · Entity : ENT-CAT-005 · Operation : FIND_ALL
API          : API-CAT-002, API-CAT-006, API-CAT-007, API-CAT-042, API-CAT-050
Intent       : derived Variant names (D-15) and the attributes an Item uses
Logical spec : SELECT a.VARIANT_FK, a.VARIANT_ATTRIBUTE_FK, t.CODE, t.SORT_ORDER, a.VARIANT_ATTRIBUTE_VALUE_FK, x.CODE, x.NAME_AR, x.NAME_EN FROM CAT_VAR_ATTR_ASSIGN a JOIN CAT_VAR_ATTR t ON t.VARIANT_ATTRIBUTE_PK = a.VARIANT_ATTRIBUTE_FK JOIN CAT_VAR_ATTR_VALUE x ON x.VARIANT_ATTRIBUTE_VALUE_PK = a.VARIANT_ATTRIBUTE_VALUE_FK WHERE a.VARIANT_FK IN (:variantPks) AND a.IS_ACTIVE_FL ORDER BY a.VARIANT_FK, t.SORT_ORDER
Join : required — ADR-CAT-047 (names composed at read) · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : VARIANT_FK SET · Result shape : projection · Null handling : x.NAME_EN null → nameAr used

QR-CAT-020 — Deactivate the assignments of a Variant
Phase        : DATA-DOM · Entity : ENT-CAT-005 · Operation : UPDATE
API          : API-CAT-009
Intent       : assignments follow their Variant (ENT-CAT-005 isActiveFl)
Logical spec : UPDATE CAT_VAR_ATTR_ASSIGN SET IS_ACTIVE_FL = FALSE, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE VARIANT_FK = :variantPk AND IS_ACTIVE_FL
Join : NONE · Transaction : READ_WRITE · Locking : under the Item lock · Pagination : NO · Filters : VARIANT_FK EXACT · Result shape : count · Null handling : —

QR-CAT-021 — Usage of an attribute or value on active Variants
Phase        : DATA-DOM · Entity : ENT-CAT-005 · Operation : COUNT
API          : API-CAT-028, API-CAT-031
Intent       : RULE-CAT-017 — {count} for the message
Logical spec : SELECT COUNT(DISTINCT a.VARIANT_FK) FROM CAT_VAR_ATTR_ASSIGN a JOIN CAT_VARIANT v ON v.VARIANT_PK = a.VARIANT_FK WHERE a.IS_ACTIVE_FL AND v.IS_ACTIVE_FL AND (a.VARIANT_ATTRIBUTE_FK = :attrPk OR a.VARIANT_ATTRIBUTE_VALUE_FK = :valuePk)
Join : required — ADR-CAT-047 (active Variant) · Transaction : READ_ONLY inside READ_WRITE · Locking : the attribute / value row is locked FOR UPDATE first (QR-CAT-022 / QR-CAT-027) · Pagination : NO · Filters : one of the two keys · Result shape : count · Null handling : the unused key is null

QR-CAT-022 — Variant attribute by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-003 · Operation : FIND_ONE
API          : API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-054, API-CAT-055
Intent       : read, update, deactivate or reactivate (FOR UPDATE), parent of a new value, or parent of a value being reactivated (both FOR SHARE)
Logical spec : SELECT * FROM CAT_VAR_ATTR WHERE VARIANT_ATTRIBUTE_PK = :id [FOR UPDATE | FOR SHARE]
Join : NONE · Transaction : per caller · Locking : FOR UPDATE on deactivation and reactivation (API-CAT-028, 054), FOR SHARE when a value of it is created or reactivated (API-CAT-029, 055) — so an active value is never committed under an attribute a parallel deactivation makes inactive — else NONE · Pagination : NO · Filters : PK EXACT · Result shape : full entity · Null handling : —

QR-CAT-023 — Attribute code / active name taken
Phase        : DATA-DOM · Entity : ENT-CAT-003 · Operation : EXISTS
API          : API-CAT-025, API-CAT-027, API-CAT-054
Intent       : RULE-CAT-016 for attributes
Logical spec : SELECT 1 FROM CAT_VAR_ATTR WHERE (CODE = :code OR (IS_ACTIVE_FL AND NAME_AR = :nameAr)) AND VARIANT_ATTRIBUTE_PK <> COALESCE(:excludePk, -1) LIMIT 1
Join : NONE · Transaction : READ_ONLY · Locking : NONE — UQ_CAT_VAR_ATTR_CODE / UQ_CAT_VAR_ATTR_NAME_AR decide a race · Pagination : NO · Filters : CODE, NAME_AR EXACT · Result shape : count · Null handling : code null on update

QR-CAT-024 — Insert Variant attribute
Phase        : DATA-DOM · Entity : ENT-CAT-003 · Operation : SAVE
API          : API-CAT-025
Intent       : new attribute, sortOrder default next
Logical spec : INSERT INTO CAT_VAR_ATTR (CODE, NAME_AR, NAME_EN, SORT_ORDER, IS_ACTIVE_FL, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…, :sortOrder, TRUE, …) — an entity save (JPA), not native SQL; when the request carries no sortOrder, the service first reads `SELECT COALESCE(MAX(a.sortOrder), 0) + 1 FROM CatVariantAttribute a` as JPQL under the given tenant filter (so it reads the current tenant's rows only — checklist F2) and passes the result as :sortOrder
Join : NONE · Transaction : READ_WRITE · Locking : NONE (a sortOrder tie only affects display) · Pagination : NO · Filters : — · Result shape : full entity · Null handling : NAME_EN null allowed

QR-CAT-025 — Update Variant attribute
Phase        : DATA-DOM · Entity : ENT-CAT-003 · Operation : UPDATE
API          : API-CAT-027, API-CAT-028, API-CAT-054
Intent       : names, sortOrder or deactivation
Logical spec : UPDATE CAT_VAR_ATTR SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE VARIANT_ATTRIBUTE_PK = :id AND VERSION = :version
Join : NONE · Transaction : READ_WRITE · Locking : optimistic VERSION · Pagination : NO · Filters : PK EXACT · Result shape : count · Null handling : —

QR-CAT-026 — Attributes with their values, ordered
Phase        : DATA-DOM · Entity : ENT-CAT-003 · Operation : FIND_ALL
API          : API-CAT-006, API-CAT-026, API-CAT-041
Intent       : REQ-CAT-048 list; the attribute set offered to generation
Logical spec : SELECT * FROM CAT_VAR_ATTR WHERE [(:text matches CODE / NAME_AR)] [AND IS_ACTIVE_FL = :isActiveFl] [AND VARIANT_ATTRIBUTE_PK IN (:ids)] ORDER BY SORT_ORDER, CODE; then SELECT * FROM CAT_VAR_ATTR_VALUE WHERE VARIANT_ATTRIBUTE_FK IN (:attrPks) [AND IS_ACTIVE_FL = :isActiveFl] ORDER BY VARIANT_ATTRIBUTE_FK, SORT_ORDER, CODE — the one `:isActiveFl` filter applies to both reads, so `true` returns the active attributes with their active values only; when it is null, both reads return every row, active and inactive (API-CAT-026 always reads with it null)
Join : NONE (two ordered reads) · Transaction : READ_ONLY · Locking : FOR SHARE on the attribute rows when called by generation · Pagination : NO · Filters : text LIKE, IS_ACTIVE_FL EXACT on the attributes and on the values, ids SET · Result shape : full entities · Null handling : null filter = not applied (to either read)

QR-CAT-027 — Variant attribute value by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-004 · Operation : FIND_ONE
API          : API-CAT-030, API-CAT-031, API-CAT-055
Intent       : update, deactivate or reactivate (FOR UPDATE) a value
Logical spec : SELECT * FROM CAT_VAR_ATTR_VALUE WHERE VARIANT_ATTRIBUTE_VALUE_PK = :id [FOR UPDATE]
Join : NONE · Transaction : per caller · Locking : FOR UPDATE on deactivation and reactivation (API-CAT-031, 055) · Pagination : NO · Filters : PK EXACT · Result shape : full entity · Null handling : —

QR-CAT-028 — Value code / active name taken within the attribute
Phase        : DATA-DOM · Entity : ENT-CAT-004 · Operation : EXISTS
API          : API-CAT-029, API-CAT-030, API-CAT-055
Intent       : RULE-CAT-016 for values
Logical spec : SELECT 1 FROM CAT_VAR_ATTR_VALUE WHERE VARIANT_ATTRIBUTE_FK = :attrPk AND (CODE = :code OR (IS_ACTIVE_FL AND NAME_AR = :nameAr)) AND VARIANT_ATTRIBUTE_VALUE_PK <> COALESCE(:excludePk, -1) LIMIT 1
Join : NONE · Transaction : READ_ONLY · Locking : NONE — UQ_CAT_VAR_ATTR_VALUE_CODE / UQ_CAT_VAR_ATTR_VALUE_NAME_AR decide a race · Pagination : NO · Filters : EXACT · Result shape : count · Null handling : code null on update

QR-CAT-029 — Insert Variant attribute value
Phase        : DATA-DOM · Entity : ENT-CAT-004 · Operation : SAVE
API          : API-CAT-029
Intent       : new value, sortOrder default next within the attribute
Logical spec : INSERT INTO CAT_VAR_ATTR_VALUE (CODE, NAME_AR, NAME_EN, SORT_ORDER, IS_ACTIVE_FL, VARIANT_ATTRIBUTE_FK, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…, :sortOrder, TRUE, :attrPk, …) — an entity save (JPA), not native SQL; when the request carries no sortOrder, the service first reads `SELECT COALESCE(MAX(v.sortOrder), 0) + 1 FROM CatVariantAttributeValue v WHERE v.variantAttributeFk = :attrPk` as JPQL under the given tenant filter (checklist F2) and passes the result as :sortOrder
Join : NONE · Transaction : READ_WRITE · Locking : NONE · Pagination : NO · Filters : — · Result shape : full entity · Null handling : NAME_EN null allowed

QR-CAT-030 — Update Variant attribute value
Phase        : DATA-DOM · Entity : ENT-CAT-004 · Operation : UPDATE
API          : API-CAT-030, API-CAT-031, API-CAT-055
Intent       : names, sortOrder or deactivation
Logical spec : UPDATE CAT_VAR_ATTR_VALUE SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE VARIANT_ATTRIBUTE_VALUE_PK = :id AND VERSION = :version
Join : NONE · Transaction : READ_WRITE · Locking : optimistic VERSION · Pagination : NO · Filters : PK EXACT · Result shape : count · Null handling : —

QR-CAT-031 — Selected values for generation
Phase        : DATA-DOM · Entity : ENT-CAT-004 · Operation : FIND_ALL
API          : API-CAT-006
Intent       : the active values a generation request names, each of its attribute
Logical spec : SELECT * FROM CAT_VAR_ATTR_VALUE WHERE VARIANT_ATTRIBUTE_VALUE_PK IN (:ids) FOR SHARE
Join : NONE · Transaction : READ_WRITE · Locking : FOR SHARE — a parallel deactivation (FOR UPDATE) cannot pass meanwhile · Pagination : NO · Filters : PK SET · Result shape : full entity · Null handling : missing or inactive → CAT-400-REQUEST-INVALID

QR-CAT-032 — Category by identifier (or code)
Phase        : DATA-DOM · Entity : ENT-CAT-006 · Operation : FIND_ONE
API          : API-CAT-001, API-CAT-003, API-CAT-005, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-040, API-CAT-049, API-CAT-053
Intent       : read; parent of a create or of a reactivation; Category of an Item (active check, FOR SHARE — at save, at reactivation and in the import's write pass); deactivation and reactivation (FOR UPDATE); import resolves by code
Logical spec : SELECT * FROM CAT_CATEGORY WHERE CATEGORY_PK = :id (import: WHERE CODE = :code) [FOR SHARE | FOR UPDATE]
Join : NONE · Transaction : per caller · Locking : FOR SHARE when an Item is attached (API-CAT-001, 003, 005, and API-CAT-049 in its write pass, where every referenced Category is re-read FOR SHARE in categoryPk order after the scale-prefix lock — its validation pass reads with no lock), FOR UPDATE on deactivation and reactivation (API-CAT-024, 053, both after the tree lock) — ADR-CAT-047 · Pagination : NO · Filters : PK or CODE EXACT · Result shape : full entity · Null handling : —

QR-CAT-033 — Category code / sibling name taken
Phase        : DATA-DOM · Entity : ENT-CAT-006 · Operation : EXISTS
API          : API-CAT-021, API-CAT-023, API-CAT-053
Intent       : RULE-CAT-024
Logical spec : SELECT 1 FROM CAT_CATEGORY WHERE (CODE = :code OR (IS_ACTIVE_FL AND NAME_AR = :nameAr AND PARENT_CATEGORY_FK IS NOT DISTINCT FROM :parentPk)) AND CATEGORY_PK <> COALESCE(:excludePk, -1) LIMIT 1
Join : NONE · Transaction : READ_ONLY · Locking : under the tree lock; UQ_CAT_CATEGORY_CODE / UQ_CAT_CATEGORY_NAME_AR back it · Pagination : NO · Filters : EXACT · Result shape : count · Null handling : parent null = top level

QR-CAT-034 — Insert Category
Phase        : DATA-DOM · Entity : ENT-CAT-006 · Operation : SAVE
API          : API-CAT-021
Intent       : new Category, sortOrder default next under the parent
Logical spec : INSERT INTO CAT_CATEGORY (CODE, NAME_AR, NAME_EN, SORT_ORDER, IS_ACTIVE_FL, PARENT_CATEGORY_FK, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…, :sortOrder, TRUE, …) — an entity save (JPA), not native SQL; when the request carries no sortOrder, the service first reads `SELECT COALESCE(MAX(c.sortOrder), 0) + 1 FROM CatCategory c WHERE (c.parentCategoryFk = :parentPk OR (:parentPk IS NULL AND c.parentCategoryFk IS NULL))` as JPQL under the given tenant filter (checklist F2), inside the tree lock, and passes the result as :sortOrder
Join : NONE · Transaction : READ_WRITE · Locking : under the tree lock · Pagination : NO · Filters : — · Result shape : full entity · Null handling : parent null = top level

QR-CAT-035 — Update Category
Phase        : DATA-DOM · Entity : ENT-CAT-006 · Operation : UPDATE
API          : API-CAT-023, API-CAT-024, API-CAT-053
Intent       : names, parent, sortOrder or deactivation
Logical spec : UPDATE CAT_CATEGORY SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE CATEGORY_PK = :id AND VERSION = :version
Join : NONE · Transaction : READ_WRITE · Locking : optimistic VERSION (+ tree lock on a move) · Pagination : NO · Filters : PK EXACT · Result shape : count · Null handling : CODE never in the SET list

QR-CAT-036 — All Categories for the tree
Phase        : DATA-DOM · Entity : ENT-CAT-006 · Operation : FIND_ALL
API          : API-CAT-039
Intent       : REQ-CAT-067 — the tree is assembled in memory
Logical spec : SELECT * FROM CAT_CATEGORY [WHERE IS_ACTIVE_FL = :isActiveFl] ORDER BY PARENT_CATEGORY_FK NULLS FIRST, SORT_ORDER, CODE
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO (bounded per tenant) · Filters : IS_ACTIVE_FL EXACT; text applied in memory · Result shape : full entity · Null handling : null filter = all

QR-CAT-037 — Ancestors and descendants of a Category
Phase        : DATA-DOM · Entity : ENT-CAT-006 · Operation : NATIVE
API          : API-CAT-021, API-CAT-023
Intent       : RULE-CAT-023 — depth of the new parent and the moved sub-tree; cycle check
Logical spec : WITH RECURSIVE up AS (SELECT CATEGORY_PK, PARENT_CATEGORY_FK, 1 AS DEPTH FROM CAT_CATEGORY WHERE TENANT_ID = :tenant AND CATEGORY_PK = :parentPk UNION ALL SELECT c.CATEGORY_PK, c.PARENT_CATEGORY_FK, up.DEPTH + 1 FROM CAT_CATEGORY c JOIN up ON c.CATEGORY_PK = up.PARENT_CATEGORY_FK WHERE c.TENANT_ID = :tenant) SELECT MAX(DEPTH), bool_or(CATEGORY_PK = :id) FROM up; and WITH RECURSIVE down AS (SELECT CATEGORY_PK, 1 AS LEVEL FROM CAT_CATEGORY WHERE TENANT_ID = :tenant AND CATEGORY_PK = :id UNION ALL SELECT c.CATEGORY_PK, down.LEVEL + 1 FROM CAT_CATEGORY c JOIN down ON c.PARENT_CATEGORY_FK = down.CATEGORY_PK WHERE c.TENANT_ID = :tenant) SELECT MAX(LEVEL) FROM down
Join : required — ADR-CAT-047 (recursive CTE) · Transaction : READ_WRITE · Locking : the tree lock QR-CAT-073 taken before — ADR-CAT-047 · Tenant : native — `TENANT_ID = :tenant` in the anchor and the recursive member of both CTEs (up and down); the method carries @TenantUnscoped(reason, adr = "ADR-CAT-047") · Pagination : NO · Filters : PK EXACT · Result shape : depth, height, cycle flag · Null handling : parent null = depth 0

QR-CAT-038 — Active children and Items of a Category
Phase        : DATA-DOM · Entity : ENT-CAT-006 · Operation : COUNT
API          : API-CAT-024
Intent       : RULE-CAT-025 — {count}
Logical spec : SELECT (SELECT COUNT(*) FROM CAT_CATEGORY WHERE PARENT_CATEGORY_FK = :id AND IS_ACTIVE_FL) + (SELECT COUNT(*) FROM CAT_ITEM WHERE CATEGORY_FK = :id AND IS_ACTIVE_FL)
Join : NONE (two counts) · Transaction : READ_ONLY inside READ_WRITE · Locking : the tree lock QR-CAT-073 is taken first, then the Category row is locked FOR UPDATE (QR-CAT-032), so a parallel sub-category create, move or reactivation cannot commit between the count and the deactivation — ADR-CAT-047 · Pagination : NO · Filters : EXACT · Result shape : count · Null handling : —

QR-CAT-039 — Active lines of a Bundle
Phase        : DATA-DOM · Entity : ENT-CAT-007 · Operation : FIND_ALL
API          : API-CAT-002, API-CAT-005, API-CAT-017
Intent       : REQ-CAT-058 — lines with the component Variant code
Logical spec : SELECT c.*, v.CODE AS COMPONENT_VARIANT_CODE FROM CAT_BUNDLE_COMP c JOIN CAT_VARIANT v ON v.VARIANT_PK = c.COMPONENT_VARIANT_FK WHERE c.BUNDLE_ITEM_FK = :itemPk AND c.IS_ACTIVE_FL ORDER BY c.SORT_ORDER
Join : required — ADR-CAT-047 (component code) · Transaction : READ_ONLY · Locking : NONE (Bundle save holds the Item lock) · Pagination : NO · Filters : BUNDLE_ITEM_FK EXACT · Result shape : full entity + code · Null handling : —

QR-CAT-040 — Insert Bundle line
Phase        : DATA-DOM · Entity : ENT-CAT-007 · Operation : SAVE
API          : API-CAT-001, API-CAT-017
Intent       : one component Variant and its quantity
Logical spec : INSERT INTO CAT_BUNDLE_COMP (QUANTITY, SORT_ORDER, IS_ACTIVE_FL, BUNDLE_ITEM_FK, COMPONENT_VARIANT_FK, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…, TRUE, …)
Join : NONE · Transaction : READ_WRITE · Locking : NONE — UQ_CAT_BUNDLE_COMP_VARIANT, CHK_CAT_BUNDLE_COMP_QUANTITY · Pagination : NO · Filters : — · Result shape : full entity · Null handling : —

QR-CAT-041 — Update Bundle line
Phase        : DATA-DOM · Entity : ENT-CAT-007 · Operation : UPDATE
API          : API-CAT-017
Intent       : quantity / sortOrder change, or removal (deactivation)
Logical spec : UPDATE CAT_BUNDLE_COMP SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE BUNDLE_COMPONENT_PK = :id AND BUNDLE_ITEM_FK = :itemPk
Join : NONE · Transaction : READ_WRITE · Locking : under the Item lock · Pagination : NO · Filters : EXACT · Result shape : count · Null handling : —

QR-CAT-042 — Active Bundles holding a Variant (or any Variant of an Item)
Phase        : DATA-DOM · Entity : ENT-CAT-007 · Operation : COUNT
API          : API-CAT-004, API-CAT-009
Intent       : RULE-CAT-021 — {count}
Logical spec : SELECT COUNT(DISTINCT c.BUNDLE_ITEM_FK) FROM CAT_BUNDLE_COMP c JOIN CAT_ITEM b ON b.ITEM_PK = c.BUNDLE_ITEM_FK WHERE c.IS_ACTIVE_FL AND b.IS_ACTIVE_FL AND c.COMPONENT_VARIANT_FK IN (:variantPks)
Join : required — ADR-CAT-047 (active Bundle Item) · Transaction : READ_ONLY inside READ_WRITE · Locking : decided under the Item lock; Bundle saves read the component rows FOR SHARE · Pagination : NO · Filters : SET · Result shape : count · Null handling : —

QR-CAT-043 — Item unit by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-008 · Operation : FIND_ONE
API          : API-CAT-011, API-CAT-012, API-CAT-013
Intent       : read, update, deactivate; inactive included
Logical spec : SELECT * FROM CAT_ITEM_UNIT WHERE ITEM_UNIT_PK = :id
Join : NONE · Transaction : per caller · Locking : NONE · Pagination : NO · Filters : PK EXACT · Result shape : full entity · Null handling : —

QR-CAT-044 — Active units of an Item
Phase        : DATA-DOM · Entity : ENT-CAT-008 · Operation : FIND_ALL
API          : API-CAT-002, API-CAT-010, API-CAT-014, API-CAT-015
Intent       : Item read; RULE-CAT-027 duplicates; RULE-CAT-031 barcode unit
Logical spec : SELECT * FROM CAT_ITEM_UNIT WHERE ITEM_FK = :itemPk AND IS_ACTIVE_FL ORDER BY UOM_CODE
Join : NONE · Transaction : READ_ONLY · Locking : NONE (adds hold the Item lock; UQ_CAT_ITEM_UNIT_UOM backs it) · Pagination : NO · Filters : ITEM_FK EXACT · Result shape : full entity · Null handling : —

QR-CAT-045 — Insert Item unit
Phase        : DATA-DOM · Entity : ENT-CAT-008 · Operation : SAVE
API          : API-CAT-010
Intent       : new unit with its factor
Logical spec : INSERT INTO CAT_ITEM_UNIT (UOM_CODE, CONVERSION_FACTOR, IS_PURCHASE_FL, IS_SALES_FL, IS_ACTIVE_FL, ITEM_FK, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…, TRUE, …)
Join : NONE · Transaction : READ_WRITE · Locking : NONE — UQ_CAT_ITEM_UNIT_UOM, CHK_CAT_ITEM_UNIT_CONVERSION_FACTOR · Pagination : NO · Filters : — · Result shape : full entity · Null handling : —

QR-CAT-046 — Update Item unit
Phase        : DATA-DOM · Entity : ENT-CAT-008 · Operation : UPDATE
API          : API-CAT-012, API-CAT-013
Intent       : purchase / sales flags or deactivation
Logical spec : UPDATE CAT_ITEM_UNIT SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE ITEM_UNIT_PK = :id AND VERSION = :version
Join : NONE · Transaction : READ_WRITE · Locking : optimistic VERSION · Pagination : NO · Filters : PK EXACT · Result shape : count · Null handling : CONVERSION_FACTOR never in the SET list

QR-CAT-047 — Active barcodes of Variants
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : FIND_ALL
API          : API-CAT-002, API-CAT-007, API-CAT-050
Intent       : reads; RULE-CAT-037 and the earliest-created barcode of a label
Logical spec : SELECT * FROM CAT_BARCODE WHERE VARIANT_FK IN (:variantPks) AND IS_ACTIVE_FL ORDER BY VARIANT_FK, CREATED_AT, BARCODE_PK
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : VARIANT_FK SET · Result shape : full entity · Null handling : —

QR-CAT-048 — Active barcode value taken
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : EXISTS
API          : API-CAT-014, API-CAT-015, API-CAT-049
Intent       : RULE-CAT-029 — returns the owning Item code for {code}
Logical spec : SELECT i.CODE FROM CAT_BARCODE b JOIN CAT_VARIANT v ON v.VARIANT_PK = b.VARIANT_FK JOIN CAT_ITEM i ON i.ITEM_PK = v.ITEM_FK WHERE b.IS_ACTIVE_FL AND b.BARCODE_VALUE IN (:values) LIMIT 1
Join : required — ADR-CAT-047 (owning Item code in the message) · Transaction : READ_ONLY · Locking : NONE — UQ_CAT_BARCODE_VALUE decides a race · Pagination : NO · Filters : BARCODE_VALUE SET · Result shape : projection CODE · Null handling : none found → free

QR-CAT-049 — Insert barcode
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : SAVE
API          : API-CAT-014, API-CAT-015, API-CAT-049
Intent       : GTIN, SCALE or INTERNAL barcode of a Variant in one unit
Logical spec : INSERT INTO CAT_BARCODE (BARCODE_VALUE, BARCODE_TYPE_CODE, UOM_CODE, IS_ACTIVE_FL, VARIANT_FK, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…, TRUE, …)
Join : NONE · Transaction : READ_WRITE · Locking : NONE — UQ_CAT_BARCODE_VALUE, CHK_CAT_BARCODE_BARCODE_VALUE · Pagination : NO · Filters : — · Result shape : full entity · Null handling : —

QR-CAT-050 — Barcode by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : FIND_ONE
API          : API-CAT-016
Intent       : the barcode to deactivate
Logical spec : SELECT * FROM CAT_BARCODE WHERE BARCODE_PK = :id
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : PK EXACT · Result shape : full entity · Null handling : —

QR-CAT-051 — Deactivate barcode
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : UPDATE
API          : API-CAT-016
Intent       : soft deactivation (RULE-CAT-048)
Logical spec : UPDATE CAT_BARCODE SET IS_ACTIVE_FL = FALSE, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE BARCODE_PK = :id
Join : NONE · Transaction : READ_WRITE · Locking : NONE (idempotent flag flip) · Pagination : NO · Filters : PK EXACT · Result shape : count · Null handling : —

QR-CAT-052 — Lookup an active barcode
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : FIND_ONE
API          : API-CAT-043
Intent       : REQ-CAT-086 / REQ-CAT-097 — the active Variant of an active Item (ADR-CAT-059), that Item and the barcode unit
Logical spec : SELECT b.UOM_CODE, v.VARIANT_PK, v.CODE, i.ITEM_PK, i.CODE, i.NAME_AR, i.BASE_UOM_CODE FROM CAT_BARCODE b JOIN CAT_VARIANT v ON v.VARIANT_PK = b.VARIANT_FK AND v.IS_ACTIVE_FL JOIN CAT_ITEM i ON i.ITEM_PK = v.ITEM_FK AND i.IS_ACTIVE_FL WHERE b.IS_ACTIVE_FL AND b.BARCODE_VALUE = :value [AND b.BARCODE_TYPE_CODE = 'SCALE']
Join : required — ADR-CAT-047 · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : BARCODE_VALUE EXACT · Result shape : projection · Null handling : none → RULE-CAT-047 / RULE-CAT-035

QR-CAT-053 — Active GTIN / INTERNAL barcode beginning with a prefix
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : EXISTS
API          : API-CAT-032
Intent       : RULE-CAT-032 on layout create
Logical spec : SELECT 1 FROM CAT_BARCODE WHERE IS_ACTIVE_FL AND BARCODE_TYPE_CODE IN ('GTIN', 'INTERNAL') AND BARCODE_VALUE LIKE :key || '%' LIMIT 1
Join : NONE · Transaction : READ_ONLY inside READ_WRITE · Locking : advisory scale-prefix lock taken first — ADR-CAT-047 · Pagination : NO · Filters : prefix LIKE · Result shape : count · Null handling : —

QR-CAT-054 — Active media of an Item
Phase        : DATA-DOM · Entity : ENT-CAT-010 · Operation : FIND_ALL
API          : API-CAT-002, API-CAT-018, API-CAT-020
Intent       : reads; RULE-CAT-038 count; RULE-CAT-039 primary and promotion
Logical spec : SELECT * FROM CAT_ITEM_MEDIA WHERE ITEM_FK = :itemPk AND IS_ACTIVE_FL ORDER BY IS_PRIMARY_FL DESC, SORT_ORDER, ITEM_MEDIA_PK
Join : NONE · Transaction : READ_ONLY · Locking : writers hold the Item lock · Pagination : NO (≤ 20) · Filters : ITEM_FK EXACT · Result shape : full entity · Null handling : VARIANT_FK null = bound to no Variant

QR-CAT-055 — Media by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-010 · Operation : FIND_ONE
API          : API-CAT-019, API-CAT-020
Intent       : the media record to update or remove
Logical spec : SELECT * FROM CAT_ITEM_MEDIA WHERE ITEM_MEDIA_PK = :id
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : PK EXACT · Result shape : full entity · Null handling : —

QR-CAT-056 — Insert media
Phase        : DATA-DOM · Entity : ENT-CAT-010 · Operation : SAVE
API          : API-CAT-018
Intent       : one image pointing to the given FileDocument
Logical spec : INSERT INTO CAT_ITEM_MEDIA (FILE_DOCUMENT_REF, NAME_AR, NAME_EN, SORT_ORDER, IS_PRIMARY_FL, IS_ACTIVE_FL, ITEM_FK, VARIANT_FK, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…)
Join : NONE · Transaction : READ_WRITE · Locking : under the Item lock; UQ_CAT_ITEM_MEDIA_PRIMARY, FK_CAT_ITEM_MEDIA_CAT_VARIANT · Pagination : NO · Filters : — · Result shape : full entity · Null handling : VARIANT_FK null allowed

QR-CAT-057 — Update media
Phase        : DATA-DOM · Entity : ENT-CAT-010 · Operation : UPDATE
API          : API-CAT-019, API-CAT-020
Intent       : fields, primary flag (clear the former first), removal, promotion
Logical spec : UPDATE CAT_ITEM_MEDIA SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE ITEM_MEDIA_PK = :id [AND VERSION = :version]
Join : NONE · Transaction : READ_WRITE · Locking : under the Item lock; optimistic VERSION on owner edits · Pagination : NO · Filters : PK EXACT · Result shape : count · Null handling : —

QR-CAT-058 — Images of a Variant with fallback
Phase        : DATA-DOM · Entity : ENT-CAT-010 · Operation : FIND_ALL
API          : API-CAT-044
Intent       : REQ-CAT-109 — media bound to the Variant, else the Item's unbound media
Logical spec : SELECT * FROM CAT_ITEM_MEDIA WHERE ITEM_FK = :itemPk AND IS_ACTIVE_FL AND (VARIANT_FK = :variantPk OR (VARIANT_FK IS NULL AND NOT EXISTS (SELECT 1 FROM CAT_ITEM_MEDIA m WHERE m.VARIANT_FK = :variantPk AND m.IS_ACTIVE_FL))) ORDER BY IS_PRIMARY_FL DESC, SORT_ORDER
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : EXACT · Result shape : full entity · Null handling : —

QR-CAT-059 — Active scale label layouts
Phase        : DATA-DOM · Entity : ENT-CAT-011 · Operation : FIND_ALL
API          : API-CAT-014, API-CAT-015, API-CAT-043, API-CAT-049
Intent       : RULE-CAT-032 / 033 at barcode entry; decoding (RULE-CAT-035)
Logical spec : SELECT * FROM CAT_SCALE_LABEL_LAYOUT WHERE IS_ACTIVE_FL ORDER BY KEY
Join : NONE · Transaction : READ_ONLY · Locking : NONE (writers take the advisory scale-prefix lock) · Pagination : NO · Filters : IS_ACTIVE_FL · Result shape : full entity · Null handling : —

QR-CAT-060 — Layout by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-011 · Operation : FIND_ONE
API          : API-CAT-033, API-CAT-034
Intent       : update or deactivate
Logical spec : SELECT * FROM CAT_SCALE_LABEL_LAYOUT WHERE SCALE_LABEL_LAYOUT_PK = :id
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : PK EXACT · Result shape : full entity · Null handling : —

QR-CAT-061 — Active layout key taken
Phase        : DATA-DOM · Entity : ENT-CAT-011 · Operation : EXISTS
API          : API-CAT-032
Intent       : RULE-CAT-034 — prefix used by another active layout
Logical spec : SELECT 1 FROM CAT_SCALE_LABEL_LAYOUT WHERE IS_ACTIVE_FL AND KEY = :key LIMIT 1
Join : NONE · Transaction : READ_ONLY · Locking : NONE — UQ_CAT_SCALE_LABEL_LAYOUT_KEY decides a race · Pagination : NO · Filters : KEY EXACT · Result shape : count · Null handling : —

QR-CAT-062 — Insert layout
Phase        : DATA-DOM · Entity : ENT-CAT-011 · Operation : SAVE
API          : API-CAT-032
Intent       : new layout
Logical spec : INSERT INTO CAT_SCALE_LABEL_LAYOUT (KEY, VALUE_AR, VALUE_EN, SCALE_CODE_LENGTH, EMBEDDED_VALUE_TYPE_CODE, VALUE_DECIMALS, IS_ACTIVE_FL, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…, TRUE, …)
Join : NONE · Transaction : READ_WRITE · Locking : advisory scale-prefix lock held · Pagination : NO · Filters : — · Result shape : full entity · Null handling : descriptions defaulted by the service

QR-CAT-063 — Update layout
Phase        : DATA-DOM · Entity : ENT-CAT-011 · Operation : UPDATE
API          : API-CAT-033, API-CAT-034
Intent       : layout fields or deactivation
Logical spec : UPDATE CAT_SCALE_LABEL_LAYOUT SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE SCALE_LABEL_LAYOUT_PK = :id [AND VERSION = :version]
Join : NONE · Transaction : READ_WRITE · Locking : optimistic VERSION on edits · Pagination : NO · Filters : PK EXACT · Result shape : count · Null handling : KEY never in the SET list

QR-CAT-064 — All layouts
Phase        : DATA-DOM · Entity : ENT-CAT-011 · Operation : FIND_ALL
API          : API-CAT-045
Intent       : REQ-CAT-095 — every layout with its active state
Logical spec : SELECT * FROM CAT_SCALE_LABEL_LAYOUT ORDER BY KEY, SCALE_LABEL_LAYOUT_PK
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : — · Result shape : full entity · Null handling : —

QR-CAT-065 — Unit e-invoice code by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-012 · Operation : FIND_ONE
API          : API-CAT-036, API-CAT-037
Intent       : update or deactivate
Logical spec : SELECT * FROM CAT_UNIT_EINVOICE_CODE WHERE UNIT_EINVOICE_CODE_PK = :id
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : PK EXACT · Result shape : full entity · Null handling : —

QR-CAT-066 — Active row for a unit taken
Phase        : DATA-DOM · Entity : ENT-CAT-012 · Operation : EXISTS
API          : API-CAT-035
Intent       : RULE-CAT-043
Logical spec : SELECT 1 FROM CAT_UNIT_EINVOICE_CODE WHERE IS_ACTIVE_FL AND KEY = :key LIMIT 1
Join : NONE · Transaction : READ_ONLY · Locking : NONE — UQ_CAT_UNIT_EINVOICE_CODE_KEY decides a race · Pagination : NO · Filters : KEY EXACT · Result shape : count · Null handling : —

QR-CAT-067 — Insert Unit e-invoice code
Phase        : DATA-DOM · Entity : ENT-CAT-012 · Operation : SAVE
API          : API-CAT-035
Intent       : new mapping; names copied from the CAT_UOM labels
Logical spec : INSERT INTO CAT_UNIT_EINVOICE_CODE (KEY, VALUE_AR, VALUE_EN, EINVOICE_UNIT_CODE, IS_ACTIVE_FL, TENANT_ID, CREATED_BY, CREATED_AT) VALUES (…, TRUE, …)
Join : NONE · Transaction : READ_WRITE · Locking : NONE — UQ_CAT_UNIT_EINVOICE_CODE_KEY · Pagination : NO · Filters : — · Result shape : full entity · Null handling : —

QR-CAT-068 — Update Unit e-invoice code
Phase        : DATA-DOM · Entity : ENT-CAT-012 · Operation : UPDATE
API          : API-CAT-036, API-CAT-037
Intent       : the e-invoice unit or deactivation
Logical spec : UPDATE CAT_UNIT_EINVOICE_CODE SET <changed columns>, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE UNIT_EINVOICE_CODE_PK = :id [AND VERSION = :version]
Join : NONE · Transaction : READ_WRITE · Locking : optimistic VERSION on edits · Pagination : NO · Filters : PK EXACT · Result shape : count · Null handling : KEY never in the SET list

QR-CAT-069 — All Unit e-invoice codes
Phase        : DATA-DOM · Entity : ENT-CAT-012 · Operation : FIND_ALL
API          : API-CAT-045
Intent       : REQ-CAT-095 — every mapping with its active state
Logical spec : SELECT * FROM CAT_UNIT_EINVOICE_CODE ORDER BY KEY, UNIT_EINVOICE_CODE_PK
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : — · Result shape : full entity · Null handling : —

QR-CAT-070 — Active e-invoice units of units of measure
Phase        : DATA-DOM · Entity : ENT-CAT-012 · Operation : FIND_ALL
API          : API-CAT-002, API-CAT-007, API-CAT-011
Intent       : einvoiceUnitCode of a Variant's base unit or of Item units (REQ-CAT-122 — empty when none is active)
Logical spec : SELECT KEY, EINVOICE_UNIT_CODE FROM CAT_UNIT_EINVOICE_CODE WHERE IS_ACTIVE_FL AND KEY IN (:uomCodes)
Join : NONE · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : KEY SET · Result shape : projection · Null handling : missing key → empty einvoiceUnitCode

QR-CAT-071 — Active barcodes of an Item bound to a unit
Phase        : DATA-DOM · Entity : ENT-CAT-008 · Operation : COUNT
API          : API-CAT-013
Intent       : RULE-CAT-051 — the {count} of active barcodes of the Item's Variants bound to the unit being deactivated
Logical spec : SELECT COUNT(*) FROM CAT_BARCODE b JOIN CAT_VARIANT v ON v.VARIANT_PK = b.VARIANT_FK AND v.TENANT_ID = :tenant WHERE b.TENANT_ID = :tenant AND v.ITEM_FK = :itemPk AND b.UOM_CODE = :uomCode AND b.IS_ACTIVE_FL
Join : required — ADR-CAT-047 (a barcode reaches its Item through its Variant) · Transaction : READ_ONLY inside READ_WRITE · Locking : decided under the Item lock (QR-CAT-010); barcode adds take the same lock — ADR-CAT-060 · Pagination : NO · Filters : ITEM_FK, UOM_CODE EXACT · Result shape : count · Null handling : —

QR-CAT-072 — Deactivate the active barcodes of a Variant
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : UPDATE
API          : API-CAT-009
Intent       : the barcodes of a deactivated Variant follow it, so no barcode of an inactive Variant stays active and its value is released for RULE-CAT-029 (REQ-CAT-050, ADR-CAT-063)
Logical spec : UPDATE CAT_BARCODE SET IS_ACTIVE_FL = FALSE, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE VARIANT_FK = :variantPk AND IS_ACTIVE_FL RETURNING BARCODE_PK
Join : NONE · Transaction : READ_WRITE · Locking : under the Item lock (QR-CAT-010) · Pagination : NO · Filters : VARIANT_FK EXACT · Result shape : the barcodePks deactivated (one catalogue-change event each) · Null handling : —

QR-CAT-073 — Category tree lock
Phase        : DATA-DOM · Entity : ENT-CAT-006 · Operation : NATIVE
API          : API-CAT-021, API-CAT-023, API-CAT-024, API-CAT-053
Intent       : serialise every Category create, move, deactivation and reactivation of one tenant, so the parent, cycle, depth (RULE-CAT-023) and in-use (RULE-CAT-025) decisions are taken on a stable tree (ADR-CAT-047)
Logical spec : SELECT pg_advisory_xact_lock(CAST(:tenant AS integer), hashtext('CAT_CATEGORY_TREE')) — the `(int, int)` form of PostgreSQL 16 (only `(bigint)` and `(int, int)` exist); the first key is the tenant, the second the 32-bit hash of the lock name. `:tenant` is TENANT_ID, which is below 2^31 for every tenant of the given (CORE_TENANT ids are issued from 1), so the cast never overflows
Join : NONE · Transaction : READ_WRITE (transaction-scoped: released at commit or rollback) · Locking : advisory exclusive, taken first in its request — lock order tree lock → Category row (QR-CAT-032) · Tenant : native — the lock key names TENANT_ID as its first part, bound from `TenantContext.require()`, never from the request; the repository method `CatCategoryRepository.lockTree(int tenant)` carries `@TenantUnscoped(reason = "advisory lock keyed by TENANT_ID", adr = "ADR-CAT-047")` · Pagination : NO · Filters : — · Result shape : void (the call blocks until granted) · Null handling : —

QR-CAT-074 — Scale-prefix lock
Phase        : DATA-DOM · Entity : ENT-CAT-011 · Operation : NATIVE
API          : API-CAT-014, API-CAT-015, API-CAT-032, API-CAT-049
Intent       : serialise scale-label-layout creates with GTIN / INTERNAL barcode adds of one tenant, so a layout key and a barcode that begins with it are never both saved (RULE-CAT-032, ADR-CAT-047); the import takes it once per import (ADR-CAT-071)
Logical spec : SELECT pg_advisory_xact_lock(CAST(:tenant AS integer), hashtext('CAT_SCALE_PREFIX')) — the same `(int, int)` form as QR-CAT-073; the two names hash to different second keys, so the two locks never block each other
Join : NONE · Transaction : READ_WRITE (transaction-scoped: released at commit or rollback) · Locking : advisory exclusive; barcode adds take it after the Item lock (QR-CAT-010), the import takes it before the Category share locks (QR-CAT-032) · Tenant : native — the lock key names TENANT_ID as its first part, bound from `TenantContext.require()`, never from the request; the repository method `CatScaleLabelLayoutRepository.lockScalePrefix(int tenant)` carries `@TenantUnscoped(reason = "advisory lock keyed by TENANT_ID", adr = "ADR-CAT-047")` · Pagination : NO · Filters : — · Result shape : void (the call blocks until granted) · Null handling : —

QR-CAT-075 — Clear the default flag of an Item's Variants
Phase        : DATA-DOM · Entity : ENT-CAT-002 · Operation : UPDATE
API          : API-CAT-006, API-CAT-009
Intent       : the first half of a default-flag move (REQ-CAT-025, ADR-CAT-072): clear isDefaultFl on the Item's current default — active (API-CAT-009, the Variant being deactivated) or inactive (API-CAT-006, generation on an Item with 0 active Variants) — before QR-CAT-014 sets it on the new default, so UQ_CAT_VARIANT_DEFAULT holds after each statement
Logical spec : UPDATE CAT_VARIANT SET IS_DEFAULT_FL = FALSE, VERSION = VERSION + 1, UPDATED_BY = :user, UPDATED_AT = now() WHERE ITEM_FK = :itemPk AND IS_DEFAULT_FL RETURNING VARIANT_PK (a JPQL bulk update under the given tenant filter)
Join : NONE · Transaction : READ_WRITE · Locking : under the Item lock (QR-CAT-010), so no parallel move or deactivation interleaves · Pagination : NO · Filters : ITEM_FK EXACT · Result shape : the variantPk cleared (at most one — UQ_CAT_VARIANT_DEFAULT; one catalogue-change event UPDATED) · Null handling : —

## ERROR CATALOG — CAT v2

Envelope `LocalizedException → {code, messageAr, messageEn}`; codes follow `{MOD}-{http}[-{SLUG}]`; statuses are those the platform emits. Messages are copied character for character from the SRS. The SRS gives no text for the PLATFORM-STD rows, so their ar / en text is written here; every row, RULE or PLATFORM-STD, has both messages and both bundle entries (checklist C11, ADR-CAT-044, ADR-CAT-065). Rows with a 2xx status are success notices carried in `ApiResponse<T>` (ADR-CAT-048).

```yaml name=error-catalog
rows:
  - {code: "CAT-422-NAME-AR-INVALID", rule: RULE-CAT-001, api: [API-CAT-001, API-CAT-003, API-CAT-018, API-CAT-019, API-CAT-021, API-CAT-023, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030], http: 422, trigger: "nameAr missing, longer than 200 characters or without an Arabic letter", messages: {ar: "أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.", en: "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."}}
  - {code: "CAT-422-NAME-EN-TOO-LONG", rule: RULE-CAT-002, api: [API-CAT-001, API-CAT-003, API-CAT-018, API-CAT-019, API-CAT-021, API-CAT-023, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030], http: 422, trigger: "nameEn longer than 200 characters", messages: {ar: "الاسم بالإنجليزية لا يتجاوز 200 حرف.", en: "The English name can have at most 200 characters."}}
  - {code: "CAT-409-ITEM-NAME-DUPLICATE", rule: RULE-CAT-003, api: [API-CAT-001, API-CAT-003, API-CAT-005], http: 409, trigger: "another active Item of the tenant has the same normalised nameAr ({code} = that Item's code, from QR-CAT-002)", messages: {ar: "يوجد صنف نشط بالاسم نفسه: {code}. افتح الصنف الموجود أو غيّر الاسم.", en: "An active item already has this name: {code}. Open the existing item or change the name."}}
  - {code: "CAT-422-CODE-SYSTEM-ASSIGNED", rule: RULE-CAT-004, api: [API-CAT-001, API-CAT-003, API-CAT-008], http: 422, trigger: "a request supplies or changes the code of an Item or a Variant", messages: {ar: "يُعيَّن الرمز تلقائيًا ولا يمكن إدخاله أو تعديله.", en: "The code is assigned automatically and cannot be entered or changed."}}
  - {code: "CAT-500-CODE-NOT-ISSUED", rule: RULE-CAT-005, api: [API-CAT-001, API-CAT-006, API-CAT-015, API-CAT-049], http: 500, trigger: "NumberSeriesApi returns no code for CAT_ITEM, CAT_VARIANT or CAT_INTERNAL_BARCODE; nothing is stored", messages: {ar: "تعذّر إصدار رمز جديد الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.", en: "A new code could not be issued, and nothing was saved. Try again in a moment."}}
  - {code: "CAT-422-LOOKUP-VALUE-INACTIVE", rule: RULE-CAT-006, api: [API-CAT-001, API-CAT-003, API-CAT-005, API-CAT-008, API-CAT-010, API-CAT-014, API-CAT-015, API-CAT-032, API-CAT-033, API-CAT-035, API-CAT-036], http: 422, trigger: "a changed lookup-backed field holds a code that is not an active value of its key", messages: {ar: "القيمة المختارة غير متاحة. اختر قيمة نشطة من القائمة.", en: "The selected value is not available. Choose an active value from the list."}}
  - {code: "CAT-422-BASE-UOM-FIXED", rule: RULE-CAT-007, api: [API-CAT-003], http: 422, trigger: "an update changes baseUomCode of a saved Item", messages: {ar: "لا يمكن تغيير وحدة القياس الأساسية بعد حفظ الصنف. أضف وحدة أخرى للصنف بدلًا من ذلك.", en: "The base unit cannot be changed after the item is saved. Add another unit to the item instead."}}
  - {code: "CAT-422-CAPABILITY-OFF", rule: RULE-CAT-008, api: [API-CAT-001, API-CAT-006, API-CAT-017, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030], http: 422, trigger: "creating or updating a Variant attribute or value, or generating Variants, while `variants` is off; creating a BUNDLE Item or saving Bundle components while `bundles` is off ({flag}); a read, deactivation or reactivation is never refused", messages: {ar: "هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «{flag}» من الإعدادات لاستخدامها.", en: "This function is not switched on for your business. Turn on the «{flag}» capability flag in settings to use it."}}
  - {code: "CAT-500-CAPABILITY-UNREADABLE", rule: RULE-CAT-009, api: [API-CAT-001, API-CAT-006, API-CAT-017, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030], http: 500, trigger: "the FeatureFlags read returns no value for the flag an operation of RULE-CAT-008 depends on", messages: {ar: "تعذّر التحقق من إعدادات القدرات الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.", en: "Capability settings could not be checked, and nothing was saved. Try again in a moment."}}
  - {code: "CAT-200-VARIANTS-IN-USE", rule: RULE-CAT-010, api: [API-CAT-051], http: 200, trigger: "turn-off check of `variants` while active assignments exist on active Variants of active Items (allowed = false; {count})", messages: {ar: "لا يمكن إيقاف المتغيّرات لأن {count} صنفًا يستخدمها. أوقف متغيّرات هذه الأصناف أو أبقِ المتغيّرات مفعّلة.", en: "Variants cannot be turned off because {count} items use them. Deactivate the variants of these items or keep variants on."}}
  - {code: "CAT-200-BUNDLES-IN-USE", rule: RULE-CAT-011, api: [API-CAT-051], http: 200, trigger: "turn-off check of `bundles` while active BUNDLE Items exist (allowed = false; {count})", messages: {ar: "لا يمكن إيقاف الحزم لوجود {count} حزمة نشطة. أوقف هذه الحزم أو أبقِ الحزم مفعّلة.", en: "Bundles cannot be turned off because {count} active bundles exist. Deactivate these bundles or keep bundles on."}}
  - {code: "CAT-201-EXISTING-VARIANTS-KEPT", rule: RULE-CAT-012, api: [API-CAT-006], http: 201, trigger: "success notice: existing Variants took the first selected values and kept their codes and barcodes", messages: {ar: "احتفظت المتغيّرات الحالية بأرقامها وباركوداتها وأُسندت إليها القيم الأولى المختارة.", en: "Existing variants kept their codes and barcodes and took the first selected values."}}
  - {code: "CAT-409-VARIANT-COMBINATION-DUPLICATE", rule: RULE-CAT-013, api: [API-CAT-006], http: 409, trigger: "the requested combination is already carried by another active Variant of the Item", messages: {ar: "هذه التركيبة من القيم موجودة في متغيّر آخر لهذا الصنف.", en: "This combination of values already exists on another variant of this item."}}
  - {code: "CAT-422-VARIANT-LIMIT-EXCEEDED", rule: RULE-CAT-014, api: [API-CAT-006], http: 422, trigger: "generation would give the Item more than 3 attributes or 300 active Variants", messages: {ar: "الحد الأقصى 3 خصائص و300 متغيّر نشط للصنف الواحد.", en: "An item can have at most 3 attributes and 300 active variants."}}
  - {code: "CAT-409-LAST-ACTIVE-VARIANT", rule: RULE-CAT-015, api: [API-CAT-009], http: 409, trigger: "deactivating the last active Variant of an active Item", messages: {ar: "لا يمكن إيقاف آخر متغيّر نشط لصنف نشط. أوقف الصنف نفسه بدلًا من ذلك.", en: "The last active variant of an active item cannot be deactivated. Deactivate the item instead."}}
  - {code: "CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE", rule: RULE-CAT-016, api: [API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030, API-CAT-054, API-CAT-055], http: 409, trigger: "attribute / value code taken in its scope, nameAr taken among active records (also when the record is reactivated), or code not 1–30 Latin letters, digits or hyphens", messages: {ar: "الرمز أو الاسم مستخدم بالفعل. استخدم رمزًا (حتى 30 حرفًا لاتينيًا أو رقمًا أو شرطة) واسمًا مختلفين.", en: "The code or name is already used. Use a different code (up to 30 Latin letters, digits or hyphens) and name."}}
  - {code: "CAT-409-ATTRIBUTE-VALUE-IN-USE", rule: RULE-CAT-017, api: [API-CAT-028, API-CAT-031], http: 409, trigger: "deactivating an attribute or value that an active assignment of an active Variant carries ({count})", messages: {ar: "هذه الخاصية أو القيمة مستخدمة في {count} متغيّرًا نشطًا. أوقف هذه المتغيّرات أولًا.", en: "This attribute or value is used by {count} active variants. Deactivate those variants first."}}
  - {code: "CAT-422-BUNDLE-COMPONENTS-REQUIRED", rule: RULE-CAT-018, api: [API-CAT-001, API-CAT-017], http: 422, trigger: "a BUNDLE saved with no active line, or a quantity not above zero / with more than 3 decimals", messages: {ar: "أضف مكوّنًا واحدًا على الأقل بكمية أكبر من صفر (حتى 3 أرقام عشرية).", en: "Add at least one component with a quantity above zero (up to 3 decimals)."}}
  - {code: "CAT-422-BUNDLE-COMPONENT-INVALID", rule: RULE-CAT-019, api: [API-CAT-001, API-CAT-005, API-CAT-017], http: 422, trigger: "at Bundle save or Bundle reactivation, a component Variant is inactive or of an inactive Item, belongs to a BUNDLE Item, or is listed twice", messages: {ar: "المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها.", en: "A component must be an active variant of an item that is not a bundle, listed once per bundle."}}
  - {code: "CAT-422-BUNDLE-SINGLE-VARIANT", rule: RULE-CAT-020, api: [API-CAT-006], http: 422, trigger: "Variant generation or attribute assignment for an Item of kind BUNDLE", messages: {ar: "الحزمة لها متغيّر واحد فقط ولا تقبل خصائص المتغيّرات.", en: "A bundle has one variant only and takes no variant attributes."}}
  - {code: "CAT-409-VARIANT-IN-ACTIVE-BUNDLE", rule: RULE-CAT-021, api: [API-CAT-004, API-CAT-009], http: 409, trigger: "deactivating a Variant, or its Item, while it is an active component of an active Bundle ({count})", messages: {ar: "هذا المتغيّر مكوّن في {count} حزمة نشطة. أزله من هذه الحزم أو أوقفها أولًا.", en: "This variant is a component of {count} active bundles. Remove it from those bundles or deactivate them first."}}
  - {code: "CAT-422-ITEM-KIND-FIXED", rule: RULE-CAT-022, api: [API-CAT-003], http: 422, trigger: "an update changes itemKindCode of a saved Item", messages: {ar: "لا يمكن تغيير نوع الصنف بعد حفظه. أنشئ صنفًا جديدًا بالنوع المطلوب.", en: "The item kind cannot be changed after saving. Create a new item of the required kind."}}
  - {code: "CAT-422-CATEGORY-PARENT-INVALID", rule: RULE-CAT-023, api: [API-CAT-021, API-CAT-023, API-CAT-053], http: 422, trigger: "parent inactive (also when the Category is reactivated), the Category itself or one of its sub-categories, or depth above 5", messages: {ar: "الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.", en: "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels."}}
  - {code: "CAT-409-CATEGORY-DUPLICATE", rule: RULE-CAT-024, api: [API-CAT-021, API-CAT-023, API-CAT-053], http: 409, trigger: "Category code taken, nameAr taken among active siblings (also when the Category is reactivated), or code not 1–30 Latin letters, digits or hyphens", messages: {ar: "يوجد رمز الفئة نفسه، أو الاسم نفسه تحت الفئة الأم نفسها. استخدم رمزًا أو اسمًا مختلفًا.", en: "The category code already exists, or the name already exists under the same parent. Use a different code or name."}}
  - {code: "CAT-409-CATEGORY-IN-USE", rule: RULE-CAT-025, api: [API-CAT-024], http: 409, trigger: "deactivating a Category with active sub-categories or active Items ({count})", messages: {ar: "لا يمكن إيقاف الفئة لوجود {count} فئة فرعية أو صنف نشط فيها. انقلها أو أوقفها أولًا.", en: "The category cannot be deactivated while it holds {count} active sub-categories or items. Move or deactivate them first."}}
  - {code: "CAT-422-CATEGORY-INACTIVE", rule: RULE-CAT-026, api: [API-CAT-001, API-CAT-003, API-CAT-005], http: 422, trigger: "a changed categoryFk, or the categoryFk of an Item being reactivated, points to an inactive Category", messages: {ar: "الفئة المختارة غير نشطة. اختر فئة نشطة.", en: "The selected category is inactive. Choose an active category."}}
  - {code: "CAT-422-ITEM-UNIT-INVALID", rule: RULE-CAT-027, api: [API-CAT-010], http: 422, trigger: "unit equals the base unit or another active unit of the Item, or factor not above zero / more than 6 decimals", messages: {ar: "الوحدة يجب أن تختلف عن الوحدة الأساسية وألا تتكرر، ومعامل التحويل أكبر من صفر (حتى 6 أرقام عشرية).", en: "The unit must differ from the base unit and appear once, with a conversion factor above zero (up to 6 decimals)."}}
  - {code: "CAT-422-CONVERSION-FACTOR-FIXED", rule: RULE-CAT-028, api: [API-CAT-012], http: 422, trigger: "an update changes conversionFactor of a saved Item unit", messages: {ar: "لا يمكن تعديل معامل التحويل بعد الحفظ. أوقف هذه الوحدة وأضفها من جديد بالمعامل الصحيح.", en: "The conversion factor cannot be changed after saving. Deactivate this unit and add it again with the correct factor."}}
  - {code: "CAT-409-BARCODE-DUPLICATE", rule: RULE-CAT-029, api: [API-CAT-014, API-CAT-015], http: 409, trigger: "barcodeValue equals an active barcode of the tenant ({code} = owning Item code)", messages: {ar: "هذا الباركود مستخدم في الصنف {code}. افتح ذلك الصنف أو استخدم باركودًا آخر.", en: "This barcode is already used by item {code}. Open that item or use another barcode."}}
  - {code: "CAT-422-GTIN-INVALID", rule: RULE-CAT-030, api: [API-CAT-014], http: 422, trigger: "GTIN not 8, 12, 13 or 14 digits, or wrong GS1 check digit", messages: {ar: "باركود GTIN يجب أن يتكون من 8 أو 12 أو 13 أو 14 رقمًا برقم تحقق صحيح.", en: "A GTIN barcode must have 8, 12, 13 or 14 digits with a valid check digit."}}
  - {code: "CAT-422-BARCODE-UNIT-INVALID", rule: RULE-CAT-031, api: [API-CAT-014, API-CAT-015], http: 422, trigger: "barcode unit is neither the Item's base unit nor an active Item unit", messages: {ar: "وحدة الباركود يجب أن تكون الوحدة الأساسية للصنف أو إحدى وحداته النشطة.", en: "The barcode unit must be the item's base unit or one of its active units."}}
  - {code: "CAT-409-BARCODE-SCALE-PREFIX", rule: RULE-CAT-032, api: [API-CAT-014, API-CAT-015, API-CAT-032], http: 409, trigger: "a GTIN / INTERNAL barcode begins with an active layout key, or a new layout key begins an active GTIN / INTERNAL barcode ({prefix})", messages: {ar: "الباركود يبدأ ببادئة ملصق ميزان نشط ({prefix}). استخدم باركودًا آخر أو عدّل إعداد الميزان.", en: "The barcode starts with the prefix of an active scale label layout ({prefix}). Use another barcode or change the scale settings."}}
  - {code: "CAT-422-SCALE-CODE-LENGTH", rule: RULE-CAT-033, api: [API-CAT-014], http: 422, trigger: "SCALE barcode not all digits, of a length no active layout uses, or no active layout exists ({length})", messages: {ar: "رمز الميزان يجب أن يتكون من {length} أرقام، ويلزم وجود إعداد ملصق ميزان نشط.", en: "The scale code must have {length} digits, and an active scale label layout is required."}}
  - {code: "CAT-422-SCALE-LAYOUT-INVALID", rule: RULE-CAT-034, api: [API-CAT-032, API-CAT-033], http: 422, trigger: "prefix outside 02 / 20–29 or used by another active layout, scaleCodeLength outside 4–6, valueDecimals outside 0–3", messages: {ar: "البادئة رقمان (02 أو من 20 إلى 29) غير مستخدمة في إعداد نشط آخر، وطول رمز الميزان من 4 إلى 6، والأرقام العشرية من 0 إلى 3.", en: "The prefix is two digits (02 or 20 to 29) not used by another active layout, the scale code length is 4 to 6, and the decimals are 0 to 3."}}
  - {code: "CAT-422-SCALE-LABEL-UNREADABLE", rule: RULE-CAT-035, api: [API-CAT-043], http: 422, trigger: "a value with an active layout prefix has a wrong check digit or an unknown scale code", messages: {ar: "تعذّر قراءة ملصق الميزان: رقم التحقق غير صحيح أو رمز الميزان غير معرَّف. اطلب الصنف بالاسم أو أضف رمز الميزان إليه.", en: "The scale label could not be read: the check digit is wrong or the scale code is not defined. Find the item by name or add the scale code to it."}}
  - {code: "CAT-422-LABEL-COPIES-OUT-OF-RANGE", rule: RULE-CAT-036, api: [API-CAT-050], http: 422, trigger: "fewer than 1 or more than 999 copies of a Variant, or more than 5,000 labels", messages: {ar: "عدد النسخ لكل متغيّر من 1 إلى 999، وإجمالي الملصقات لا يتجاوز 5000.", en: "Copies per variant are 1 to 999, and the total cannot exceed 5,000 labels."}}
  - {code: "CAT-422-LABEL-VARIANT-NO-BARCODE", rule: RULE-CAT-037, api: [API-CAT-050], http: 422, trigger: "a label request lists Variants without an active barcode ({count}; the Variants are listed)", messages: {ar: "لا يوجد باركود نشط لـ {count} متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع.", en: "{count} variants have no active barcode. Generate an internal barcode for them, then print."}}
  - {code: "CAT-422-IMAGE-NOT-ACCEPTED", rule: RULE-CAT-038, api: [API-CAT-018], http: 422, trigger: "image not JPG / PNG / WEBP, above 5 MB, not held in the public image storage of the caller's tenant (for example uploaded to the private storage), or a 21st active image", messages: {ar: "الصورة يجب أن تكون JPG أو PNG أو WEBP بحجم لا يتجاوز 5 ميجابايت، وبحد أقصى 20 صورة نشطة للصنف.", en: "The image must be JPG, PNG or WEBP of at most 5 MB, with at most 20 active images per item."}}
  - {code: "CAT-200-PRIMARY-IMAGE-REPLACED", rule: RULE-CAT-039, api: [API-CAT-019], http: 200, trigger: "success notice: the selected image became the main image in place of the former one", messages: {ar: "للصنف صورة رئيسية واحدة فقط؛ تم تعيين الصورة المختارة رئيسية بدل السابقة.", en: "An item has exactly one main image; the selected image replaced the previous main image."}}
  - {code: "CAT-422-MEDIA-VARIANT-MISMATCH", rule: RULE-CAT-040, api: [API-CAT-018, API-CAT-019], http: 422, trigger: "variantFk of a media record belongs to another Item", messages: {ar: "المتغيّر المختار لا ينتمي إلى هذا الصنف.", en: "The selected variant does not belong to this item."}}
  - {code: "CAT-422-PUBLISH-BLOCKED", rule: RULE-CAT-041, api: [API-CAT-005], http: 422, trigger: "publishing an inactive Item or an Item with no active Variant", messages: {ar: "لا يمكن نشر صنف غير نشط أو بلا متغيّر نشط. فعّل الصنف أو أحد متغيّراته أولًا.", en: "An inactive item, or one with no active variant, cannot be published. Activate the item or one of its variants first."}}
  - {code: "CAT-422-EINVOICE-CODE-INVALID", rule: RULE-CAT-042, api: [API-CAT-001, API-CAT-003, API-CAT-008], http: 422, trigger: "only one of type and code, a GS1 code not 8–14 digits, or an EGS code not starting with EG- / above 100 characters", messages: {ar: "أدخل نوع رمز الفاتورة الإلكترونية والرمز معًا: GS1 من 8 إلى 14 رقمًا، وEGS يبدأ بـ EG- ولا يتجاوز 100 حرف.", en: "Enter the e-invoice code type and code together: GS1 is 8 to 14 digits, EGS starts with EG- and has at most 100 characters."}}
  - {code: "CAT-409-UNIT-EINVOICE-DUPLICATE", rule: RULE-CAT-043, api: [API-CAT-035], http: 409, trigger: "a Unit e-invoice code is created for a unit that already has an active one", messages: {ar: "لهذه الوحدة رمز فاتورة إلكترونية نشط بالفعل. عدّله بدلًا من إضافة رمز جديد.", en: "This unit already has an active e-invoice unit. Change it instead of adding a new one."}}
  - {code: "CAT-422-IMPORT-FILE-REJECTED", rule: RULE-CAT-044, api: [API-CAT-049], http: 422, trigger: "import file not xlsx, above 10 MB, above 5,000 data rows, or with a different header row; no Item is created", messages: {ar: "الملف غير مقبول: يجب أن يكون بصيغة xlsx بحجم لا يتجاوز 10 ميجابايت وحتى 5000 صف، وبعناوين ورقة الاستيراد نفسها. نزّل ورقة الاستيراد واستخدمها.", en: "The file was not accepted: it must be xlsx, at most 10 MB and 5,000 rows, with the import sheet headers. Download the import sheet and use it."}}
  - {code: "CAT-200-IMPORT-ROW-REJECTED", rule: RULE-CAT-045, api: [API-CAT-049], http: 200, trigger: "per rejected row inside a completed import result ({row}, {column}, {reason} = the failed rule's message)", messages: {ar: "الصف {row}، العمود {column}: {reason}. صحّح الصف وأعد استيراده.", en: "Row {row}, column {column}: {reason}. Correct the row and import it again."}}
  - {code: "CAT-400-SEARCH-TERM-TOO-SHORT", rule: RULE-CAT-046, api: [API-CAT-047], http: 400, trigger: "catalogue search term shorter than 2 characters; no results", messages: {ar: "اكتب حرفين على الأقل للبحث.", en: "Type at least 2 characters to search."}}
  - {code: "CAT-404-BARCODE-NOT-FOUND", rule: RULE-CAT-047, api: [API-CAT-043], http: 404, trigger: "no active barcode of an active Variant of an active Item and no active scale label layout matches the looked-up value", messages: {ar: "لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف.", en: "No item has this barcode. Search by name or add the barcode to the item."}}
  - {code: "CAT-200-DEACTIVATED", rule: RULE-CAT-048, api: [API-CAT-004, API-CAT-009, API-CAT-013, API-CAT-016, API-CAT-024], http: 200, trigger: "success notice after a soft deactivation of a SHARED record", messages: {ar: "تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة.", en: "Deactivated. The record stays available to earlier documents and no longer appears in new selections."}}
  - {code: "CAT-500-CHANGE-NOT-SAVED", rule: RULE-CAT-049, api: [API-CAT-001, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-049], http: 500, trigger: "the commit of a catalogue change fails and the transaction rolls back; no event is delivered", messages: {ar: "تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة.", en: "The change was not saved and no change notice was sent. Try again."}}
  - {code: "CAT-422-ITEM-NO-ACTIVE-VARIANT", rule: RULE-CAT-050, api: [API-CAT-005], http: 422, trigger: "reactivating an Item that has no active Variant", messages: {ar: "لا يمكن إعادة تفعيل صنف بلا متغيّر نشط. أنشئ متغيّرًا له أو أنشئ صنفًا جديدًا.", en: "An item with no active variant cannot be reactivated. Generate a variant for it, or create a new item."}}
  - {code: "CAT-409-ITEM-UNIT-HAS-BARCODES", rule: RULE-CAT-051, api: [API-CAT-013], http: 409, trigger: "deactivating an Item unit while active barcodes of the Item's Variants are bound to it ({count})", messages: {ar: "هذه الوحدة مستخدمة في {count} باركود نشط. أوقف هذه الباركودات أولًا.", en: "This unit is used by {count} active barcodes. Deactivate those barcodes first."}}
  - {code: "CAT-422-VARIANT-ATTRIBUTE-MISSING", rule: RULE-CAT-052, api: [API-CAT-006], http: 422, trigger: "a generation request omits an attribute the Item's active Variants already carry, or selects no value for it", messages: {ar: "اختر قيمة واحدة على الأقل لكل خاصية يستخدمها الصنف بالفعل.", en: "Select at least one value for every attribute the item already uses."}}
  - {code: "CAT-422-PARENT-INACTIVE", rule: RULE-CAT-053, api: [API-CAT-003, API-CAT-008, API-CAT-010, API-CAT-012, API-CAT-014, API-CAT-015, API-CAT-018, API-CAT-019], http: 422, trigger: "an update of an inactive Item that changes any field other than categoryFk, or an Item unit or image create / update under an inactive Item, or a barcode / e-invoice override on a Variant that is inactive or of an inactive Item; child deactivations and the three repairs of ADR-CAT-068 (Variant generation API-CAT-006, a categoryFk-only update API-CAT-003, Bundle components API-CAT-017) are never refused by it", messages: {ar: "هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا.", en: "This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant."}}
  - {code: "CAT-400-REQUEST-INVALID", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055], http: 400, trigger: "malformed body or parameter, wrong type or format, an empty array where at least one entry is required (Variant generation selections or values), a value not offered (inactive attribute / value, unknown flag name, wrong item kind for the endpoint), an update of an inactive scale label layout or Unit e-invoice code", messages: {ar: "الطلب غير صالح. راجع الحقول المحددة ثم أعد المحاولة.", en: "The request is not valid. Check the highlighted fields and try again."}, adr: "ADR-CAT-044"}
  - {code: "CAT-401-UNAUTHENTICATED", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055], http: 401, trigger: "no valid bearer token", messages: {ar: "انتهت جلستك أو لم تسجّل الدخول. سجّل الدخول ثم أعد المحاولة.", en: "You are not signed in or your session has ended. Sign in, then try again."}, adr: "ADR-CAT-044"}
  - {code: "CAT-403-FORBIDDEN", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055], http: 403, trigger: "the caller lacks the endpoint's PERM_<PAGE_CODE>_<ACTION>", messages: {ar: "ليس لديك صلاحية لتنفيذ هذه العملية. اطلب الصلاحية من مالك النشاط.", en: "You do not have permission to perform this operation. Ask the business owner for access."}, adr: "ADR-CAT-044"}
  - {code: "CAT-404-NOT-FOUND", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-033, API-CAT-034, API-CAT-036, API-CAT-037, API-CAT-040, API-CAT-044, API-CAT-049, API-CAT-050, API-CAT-053, API-CAT-054, API-CAT-055], http: 404, trigger: "an identifier (a path id, a body reference or a fileDocumentRef) names no record or available file of the caller's tenant", messages: {ar: "السجل المطلوب غير موجود. حدّث القائمة ثم أعد المحاولة.", en: "The requested record was not found. Refresh the list and try again."}, adr: "ADR-CAT-044"}
  - {code: "CAT-409-STALE-VERSION", rule: PLATFORM-STD, api: [API-CAT-003, API-CAT-008, API-CAT-012, API-CAT-017, API-CAT-019, API-CAT-023, API-CAT-027, API-CAT-030, API-CAT-033, API-CAT-036], http: 409, trigger: "the echoed version differs from the stored one (optimistic lock)", messages: {ar: "عدّل مستخدم آخر هذا السجل في الأثناء. أعد تحميله ثم حاول مجددًا.", en: "This record was changed by someone else in the meantime. Reload it and try again."}, adr: "ADR-CAT-044"}
  - {code: "CAT-500-SERVER-ERROR", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055], http: 500, trigger: "unexpected failure; the transaction rolls back and no event is delivered", messages: {ar: "حدث خطأ غير متوقع ولم يُحفظ شيء. حاول مرة أخرى لاحقًا.", en: "An unexpected error occurred, and nothing was saved. Try again later."}, adr: "ADR-CAT-044"}
  - {code: "CAT-404-LOOKUP-KEY-UNKNOWN", rule: PLATFORM-STD, api: [API-CAT-052], http: 404, trigger: "the requested key is not one of the 10 CAT lookup keys, or mdl answers 404 for it (OwnedLookups translates mdl's 404 to this code)", messages: {ar: "هذه القائمة ليست من قوائم الكتالوج. اختر إحدى قوائم الكتالوج.", en: "This list is not one of the catalogue lists. Choose a catalogue list."}, adr: "ADR-CAT-065"}
```

## API DOCUMENT
`api-spec-cat.yaml` (OpenAPI 3.1.0) is derived from the API blocks and this catalog in the same run: one operation per `API-*` block (path and method from its `Endpoint` line, `x-api-id`, `x-traces`), request parameters and bodies from its `Request` line, the success response from its `Response` line, and one response per catalog row the block's `Errors` line names, keyed by the row's status with the row's code under `x-error-codes`; bearer JWT security on every operation; paged operations carry `page` and `size`.
