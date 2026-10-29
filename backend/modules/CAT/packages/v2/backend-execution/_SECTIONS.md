<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# BACKEND EXECUTION PLAN — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module : CAT   Version : v2   Profile : commerce   Stage : P3.1   Track : backend · plan exec
Inputs : srs (P1 — REQ 148 · AC 174 · ENT 12 · RULE 53 · SCR-REQ 6), db-script (P2 — 12 tables · DBF 161 · XM 0), registry-srs, registry-db, contract (P1.5 — CON 18)
Governance : FULL (db-script present) · dialect postgresql16 · framework Spring Boot (Java 25) on erp-core 1.6.0
Counts : API 56 · QR 75 · error codes 60 · XM 0 · ADR raised 5 at P3.1 (ADR-CAT-044 … ADR-CAT-048), ADR-CAT-055 … ADR-CAT-062 at the gate-analysis revise, ADR-CAT-063 … ADR-CAT-067 at the second, ADR-CAT-068 … ADR-CAT-071 at the third, ADR-CAT-072 at the sixth and ADR-CAT-073 … ADR-CAT-075 at the seventh ADR-CAT-076 at the eighth, ADR-CAT-077 at the ninth, ADR-CAT-078 at the tenth and ADR-CAT-079, ADR-CAT-080 at the eleventh gate-analysis revise, all ACCEPTED non-breaking · CORE-GAP 5 (in-process authorization, tenant lookup types, number-series block allocation, lookup inactive value label, file document metadata read)
Delta  : CS-CAT-001 (ADDITIVE). The current state holds no backend plan for CAT, so v2 writes it in full; the API and QR streams start at 001.
Status : DRAFT — P3.1 pass 1 · no question raised · nothing BLOCKED
══════════════════════════════════════════════════════════════════

Reading order for the implementer: index → DB Alignment Manifest → ADRs → phases in profile order (CORE → DATA-DOM → SVC-API → DOC → SEC-BE → ALIGN-BE → INT-C) → Query Reference Catalog → Error Catalog.

## EXECUTION PLAN INDEX — CAT v2 — backend-execution-plan-cat.md

Profile: commerce · dialect: postgresql16 · framework: Spring Boot (Java 25), erp-core 1.6.0 given
Open ADRs: 0 BLOCKED — decisions/CAT/ (this stage raised ADR-CAT-044 … ADR-CAT-048; the gate-analysis revise added ADR-CAT-055 … ADR-CAT-062; the second revise added ADR-CAT-063 … ADR-CAT-067; the third revise added ADR-CAT-068 … ADR-CAT-071; the sixth revise added ADR-CAT-072; the seventh revise added ADR-CAT-073 … ADR-CAT-075; the eighth revise added ADR-CAT-076; the ninth revise added ADR-CAT-077; the tenth revise added ADR-CAT-078; the eleventh revise added ADR-CAT-079 and ADR-CAT-080; all ACCEPTED). Open core gaps: decisions/CAT/CORE-GAP-in-process-authorization.md (ADR-CAT-061 is conditional on its answer — ADR-CAT-066), decisions/CAT/CORE-GAP-tenant-lookup-types.md (ADR-CAT-070), decisions/CAT/CORE-GAP-number-series-block-allocation.md (ADR-CAT-071, ADR-CAT-074), decisions/CAT/CORE-GAP-lookup-inactive-value-label.md (ADR-CAT-073), decisions/CAT/CORE-GAP-file-document-metadata-read.md (ADR-CAT-076 — the byte read of API-CAT-049 waits on its answer)

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
| API-CAT-003 | update Item | PUT | /api/v1/cat/items/{id} | 004, 007, 009, 030, 031, 034, 063, 072, 073, 075, 076, 082, 117, 123, 131, 132, 133, 135, 146 |
| API-CAT-004 | deactivate Item | DELETE | /api/v1/cat/items/{id} | 005, 062, 131 |
| API-CAT-005 | reactivate / publish / unpublish Item | PATCH | /api/v1/cat/items/{id} | 006, 113, 114, 116, 130, 142, 143, 144 |
| API-CAT-006 | generate Variants | POST | /api/v1/cat/items/{id}/variants | 011 … 019, 033, 035, 049, 051, 052, 061, 129, 141, 145 |
| API-CAT-007 | read Variant | GET | /api/v1/cat/variants/{id} | 001, 029, 050, 119, 122 |
| API-CAT-008 | update Variant e-invoice override | PUT | /api/v1/cat/variants/{id} | 034, 118, 123, 135 |
| API-CAT-009 | deactivate Variant (with its assignments and barcodes) | DELETE | /api/v1/cat/variants/{id} | 050, 053, 062, 140 |
| API-CAT-010 | add Item unit | POST | /api/v1/cat/items/{id}/units | 074, 075, 077, 080, 135 |
| API-CAT-011 | read Item unit | GET | /api/v1/cat/item-units/{id} | 079, 122 |
| API-CAT-012 | update Item unit | PUT | /api/v1/cat/item-units/{id} | 078, 081, 135 |
| API-CAT-013 | deactivate Item unit | DELETE | /api/v1/cat/item-units/{id} | 079, 128 |
| API-CAT-014 | add barcode (GTIN / scale) | POST | /api/v1/cat/variants/{id}/barcodes | 074, 075, 083, 087, 088, 089, 091, 096, 099, 135 |
| API-CAT-015 | generate internal barcode | POST | /api/v1/cat/variants/{id}/barcodes/internal | 035, 084, 089, 091, 135 |
| API-CAT-016 | deactivate barcode | DELETE | /api/v1/cat/barcodes/{id} | 085 |
| API-CAT-017 | save Bundle components | PUT | /api/v1/cat/items/{id}/bundle-components | 013, 015, 016, 020, 056, 057, 059, 060, 147 |
| API-CAT-018 | upload image | POST | /api/v1/cat/items/{id}/media | 105, 108, 110, 112, 135 |
| API-CAT-019 | update image | PUT | /api/v1/cat/media/{id} | 106, 112, 135 |
| API-CAT-020 | remove image | DELETE | /api/v1/cat/media/{id} | 107, 111 |
| API-CAT-021 | create Category | POST | /api/v1/cat/categories | 008, 064, 069, 070 |
| API-CAT-022 | read Category | GET | /api/v1/cat/categories/{id} | 066, 067 |
| API-CAT-023 | update Category | PUT | /api/v1/cat/categories/{id} | 008, 065, 069, 070 |
| API-CAT-024 | deactivate Category | DELETE | /api/v1/cat/categories/{id} | 008, 066, 071 |
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
| API-CAT-053 | reactivate Category | PATCH | /api/v1/cat/categories/{id} | 008, 136 |
| API-CAT-054 | reactivate Variant attribute | PATCH | /api/v1/cat/variant-attributes/{id} | 137 |
| API-CAT-055 | reactivate Variant attribute value | PATCH | /api/v1/cat/variant-attribute-values/{id} | 138 |
| API-CAT-056 | generate the missing internal barcode of a label line | POST | /api/v1/cat/barcode-labels/variants/{id}/internal-barcode | 084, 104, 148 |

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
| RULE-CAT-032 | Barcodes avoid scale prefixes | barcode create · import row · layout create | ENT-CAT-009 | ✓ |
| RULE-CAT-033 | Scale code length | create (SCALE) | ENT-CAT-009 | ✓ |
| RULE-CAT-034 | Valid scale label layout | create · update | ENT-CAT-011 | ✓ |
| RULE-CAT-035 | Scale label decoding | barcode lookup | ENT-CAT-009 | ✓ |
| RULE-CAT-036 | Label copy limits | label request | ENT-CAT-002 | ✓ |
| RULE-CAT-037 | Labelled Variant carries a barcode in its base unit | label request | ENT-CAT-009 | ✓ |
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
API: 56
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
| — (derived) | ENT-CAT-001 | categoryCode, categoryNameAr, categoryNameEn | String | — | ✓ derived — code and names of the Category the Item points to, active or inactive; empty when categoryFk is empty; left join to CAT_CATEGORY in QR-CAT-001, 005, 006, 007, else QR-CAT-032 (REQ-CAT-066) — ADR-CAT-075, amending ADR-CAT-048 §5 |
| — (derived) | ENT-CAT-002 | effectiveEinvoiceCodeTypeCode, effectiveEinvoiceCode | String | — | ✓ derived — Variant override, else Item value (REQ-CAT-119) — ADR-CAT-048 |
| — (derived) | ENT-CAT-002, ENT-CAT-008 | einvoiceUnitCode | String | — | ✓ derived — active CAT_UNIT_EINVOICE_CODE of the unit, else empty (REQ-CAT-122) — ADR-CAT-048 |
| — (derived) | ENT-CAT-009 | embeddedValue, embeddedValueTypeCode | BigDecimal, String | — | ✓ derived — decoded from a scale label (REQ-CAT-097) — ADR-CAT-048 |
| — (derived) | ENT-CAT-007 | componentVariantCode | String | — | ✓ derived — code of the component Variant (REQ-CAT-058) — ADR-CAT-048 |
| — (derived) | ENT-CAT-007 | componentBaseUomCode | String | — | ✓ derived — baseUomCode (DBF-CAT-006) of the component Variant's Item, the unit of quantity (DBF-CAT-085); joined in QR-CAT-039 (REQ-CAT-056, REQ-CAT-058) — ADR-CAT-080, amending ADR-CAT-048 §5 |

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
| ADR-CAT-056 | uploaded files go through the given FILE endpoints; API-CAT-018 / 049 take fileDocumentRef; file retention (§2 amended by ADR-CAT-076) | ACCEPTED (non-breaking) |
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
| ADR-CAT-073 | an inactive lookup value renders as its raw code: API-CAT-052 serves active values only and erp-core 1.6.0 publishes no read of an inactive value; CORE-GAP-lookup-inactive-value-label.md raised | ACCEPTED (non-breaking) |
| ADR-CAT-074 | the item import saves entities through QR-CAT-003, 011, 049 (no JDBC batching, no seventh native statement); the 60 s figure is a measured target; amends ADR-CAT-071 | ACCEPTED (non-breaking) |
| ADR-CAT-075 | ItemSummary / ItemDetail carry the derived categoryCode, categoryNameAr, categoryNameEn; amends ADR-CAT-048 §5 | ACCEPTED (non-breaking) |
| ADR-CAT-076 | image type, size and public storage are enforced by the FILE category CAT_ITEM_IMAGE at upload; API-CAT-018 checks only availability, a public URL and the 20-record count; the import's byte read waits on CORE-GAP-file-document-metadata-read; amends ADR-CAT-056 §2 | ACCEPTED (non-breaking) |
| ADR-CAT-077 | Category changes publish catalogue-change events: ENT-CAT-006 joins RULE-CAT-049 and CON-CAT-018 (itemPk empty); API-CAT-021, 023, 024 and 053 record CREATED, UPDATED, DEACTIVATED and REACTIVATED | ACCEPTED (non-breaking) |
| ADR-CAT-078 | the barcode label screen generates the missing internal barcode of a line itself: API-CAT-056, guarded by PERM_CAT_BARCODE_LABELS_CREATE, shares the service body, rules and locks of API-CAT-015, so RULE-CAT-037's next action is reachable by warehouse staff | ACCEPTED (non-breaking) |
| ADR-CAT-079 | a label shows only the Variant's label barcode, the earliest-created active GTIN or INTERNAL barcode bound to the Item's base unit: QR-CAT-016 (firstBarcodeValue) and QR-CAT-047 (label filter, API-CAT-050) select it, and RULE-CAT-037 counts only such barcodes; a Variant without one gets the API-CAT-056 next action | ACCEPTED (non-breaking) |
| ADR-CAT-080 | a Bundle component quantity is always in the base unit of the component Variant's Item; BundleComponentView carries the derived componentBaseUomCode (QR-CAT-039); amends ADR-CAT-048 §5 | ACCEPTED (non-breaking) |















## QUERY REFERENCE CATALOG (QR) — CAT v2

Logical specifications only — the implementer rewrites each with the real entity classes and the project's query strategy. Every JPQL, derived and search-builder query runs under the given tenant filter; its spec still shows `TENANT_ID = :tenant` where it reads several tables. The six native queries — QR-CAT-006, 008, 009 and 037, and the two advisory locks QR-CAT-073 (Category tree) and QR-CAT-074 (scale prefix) — bypass that filter. The first four write `TENANT_ID = :tenant` on every table they read: the outer query, every sub-select and `EXISTS`, and both branches of every recursive CTE. The two locks read no table; they name TENANT_ID as the first key of `pg_advisory_xact_lock(int, int)`, with `hashtext` of the lock name as the second. Their repository methods carry `@TenantUnscoped(reason, adr = "ADR-CAT-047")` (erp-core checklist C3, F2). `:tenant` is bound from `TenantContext.require()`. Text filters bind `:textEsc` / `:termEsc`, the term with `\`, `%` and `_` escaped, and match names through `CAT_NORMALIZE_AR` (ADR-CAT-062). Defaults per operation follow the engine table (FIND_ONE not found → CAT-404-NOT-FOUND; FIND_BY_CRITERIA empty → success; SAVE / UPDATE READ_WRITE with PK and audit system-set; EXISTS excludes the current PK on update). Non-default joins, native queries and locks: ADR-CAT-047.

QR-CAT-001 — Item by identifier
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : FIND_ONE
API          : API-CAT-002, API-CAT-007
Intent       : the Item a read or a dependent write starts from; inactive Items included
Logical spec : SELECT i.*, c.CODE AS CATEGORY_CODE, c.NAME_AR AS CATEGORY_NAME_AR, c.NAME_EN AS CATEGORY_NAME_EN FROM CAT_ITEM i LEFT JOIN CAT_CATEGORY c ON c.TENANT_ID = i.TENANT_ID AND c.CATEGORY_PK = i.CATEGORY_FK WHERE i.ITEM_PK = :id
Join : LEFT JOIN CAT_CATEGORY for the derived categoryCode / categoryNameAr / categoryNameEn — ADR-CAT-075 (the Category is read active or inactive) · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : ITEM_PK EXACT · Result shape : full entity + 3 Category columns · Null handling : optional columns returned as null; no Category → the 3 Category columns null

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
Logical spec : SELECT i.*, c.CODE AS CATEGORY_CODE, c.NAME_AR AS CATEGORY_NAME_AR, c.NAME_EN AS CATEGORY_NAME_EN FROM CAT_ITEM i LEFT JOIN CAT_CATEGORY c ON c.TENANT_ID = i.TENANT_ID AND c.CATEGORY_PK = i.CATEGORY_FK WHERE i.TENANT_ID = :tenant AND (:text IS NULL OR CAT_NORMALIZE_AR(i.NAME_AR) LIKE '%' || CAT_NORMALIZE_AR(:textEsc) || '%' ESCAPE '\' OR CAT_NORMALIZE_AR(i.NAME_EN) LIKE '%' || CAT_NORMALIZE_AR(:textEsc) || '%' ESCAPE '\' OR i.CODE ILIKE '%' || :textEsc || '%' ESCAPE '\' OR EXISTS (SELECT 1 FROM CAT_BARCODE b JOIN CAT_VARIANT v ON v.VARIANT_PK = b.VARIANT_FK AND v.TENANT_ID = :tenant WHERE b.TENANT_ID = :tenant AND v.ITEM_FK = i.ITEM_PK AND b.IS_ACTIVE_FL AND b.BARCODE_VALUE = :text)) AND (:categoryFk IS NULL OR i.CATEGORY_FK = :categoryFk) AND (:brandCode IS NULL OR i.BRAND_CODE = :brandCode) AND (:itemKindCode IS NULL OR i.ITEM_KIND_CODE = :itemKindCode) AND (:publishStateCode IS NULL OR i.PUBLISH_STATE_CODE = :publishStateCode) AND (:isActiveFl IS NULL OR i.IS_ACTIVE_FL = :isActiveFl) ORDER BY i.NAME_AR, i.ITEM_PK LIMIT :pageSize OFFSET :offset (both taken from the Pageable that PageableBuilder built — size ≤ 200, page ≥ 0)
Join : LEFT JOIN CAT_CATEGORY for the derived Category label — ADR-CAT-075 (barcode by EXISTS) · Transaction : READ_ONLY · Locking : NONE · Pagination : YES (Page<T>) · Filters : text LIKE (escaped; names normalised; trigram indexes — ADR-CAT-062), CATEGORY_FK / BRAND_CODE / ITEM_KIND_CODE / PUBLISH_STATE_CODE / IS_ACTIVE_FL EXACT · Result shape : full entity + 3 Category columns · Null handling : null filter = not applied

QR-CAT-006 — Active Items of a Category sub-tree
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : NATIVE
API          : API-CAT-040
Intent       : REQ-CAT-068 — active Items of the Category and of its active sub-categories
Logical spec : WITH RECURSIVE t AS (SELECT CATEGORY_PK FROM CAT_CATEGORY WHERE TENANT_ID = :tenant AND CATEGORY_PK = :id UNION ALL SELECT c.CATEGORY_PK FROM CAT_CATEGORY c JOIN t ON c.PARENT_CATEGORY_FK = t.CATEGORY_PK WHERE c.TENANT_ID = :tenant AND c.IS_ACTIVE_FL) SELECT i.*, c.CODE AS CATEGORY_CODE, c.NAME_AR AS CATEGORY_NAME_AR, c.NAME_EN AS CATEGORY_NAME_EN FROM CAT_ITEM i JOIN t ON i.CATEGORY_FK = t.CATEGORY_PK LEFT JOIN CAT_CATEGORY c ON c.TENANT_ID = :tenant AND c.CATEGORY_PK = i.CATEGORY_FK WHERE i.TENANT_ID = :tenant AND i.IS_ACTIVE_FL ORDER BY i.NAME_AR, i.ITEM_PK LIMIT :pageSize OFFSET :offset (both taken from the Pageable that PageableBuilder built — size ≤ 200, page ≥ 0)
Join : required — ADR-CAT-047 (recursive CTE) · Transaction : READ_ONLY · Locking : NONE · Pagination : YES (Page<T>) · Filters : sub-tree SET · Tenant : native — `TENANT_ID = :tenant` in both CTE branches, on CAT_ITEM and on the joined CAT_CATEGORY (ADR-CAT-075); the method carries @TenantUnscoped(reason, adr = "ADR-CAT-047") · Result shape : full entity + 3 Category columns · Null handling : —

QR-CAT-007 — Storefront Items
Phase        : DATA-DOM · Entity : ENT-CAT-001 · Operation : FIND_BY_CRITERIA
API          : API-CAT-046
Intent       : REQ-CAT-115 — only active and published Items
Logical spec : SELECT i.*, c.CODE AS CATEGORY_CODE, c.NAME_AR AS CATEGORY_NAME_AR, c.NAME_EN AS CATEGORY_NAME_EN FROM CAT_ITEM i LEFT JOIN CAT_CATEGORY c ON c.TENANT_ID = i.TENANT_ID AND c.CATEGORY_PK = i.CATEGORY_FK WHERE i.IS_ACTIVE_FL AND i.PUBLISH_STATE_CODE = 'PUBLISHED' ORDER BY i.NAME_AR, i.ITEM_PK LIMIT :pageSize OFFSET :offset (both taken from the Pageable that PageableBuilder built — size ≤ 200, page ≥ 0)
Join : LEFT JOIN CAT_CATEGORY for the derived Category label — ADR-CAT-075 · Transaction : READ_ONLY · Locking : NONE · Pagination : YES (Page<T>) · Filters : IS_ACTIVE_FL, PUBLISH_STATE_CODE EXACT · Result shape : full entity + 3 Category columns · Null handling : —

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
API          : API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-056
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
API          : API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-014, API-CAT-015, API-CAT-018, API-CAT-019, API-CAT-044, API-CAT-056
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
Intent       : REQ-CAT-101 — active Variants of active Items (ADR-CAT-059) with their Item nameAr and their label barcode: the earliest-created active GTIN or INTERNAL barcode bound to the Item's base unit (ADR-CAT-079)
Logical spec : SELECT v.VARIANT_PK, v.CODE, i.ITEM_PK, i.NAME_AR, (SELECT b.BARCODE_VALUE FROM CAT_BARCODE b WHERE b.TENANT_ID = :tenant AND b.VARIANT_FK = v.VARIANT_PK AND b.IS_ACTIVE_FL AND b.BARCODE_TYPE_CODE IN ('GTIN', 'INTERNAL') AND b.UOM_CODE = i.BASE_UOM_CODE ORDER BY b.CREATED_AT, b.BARCODE_PK LIMIT 1) AS FIRST_BARCODE FROM CAT_VARIANT v JOIN CAT_ITEM i ON i.ITEM_PK = v.ITEM_FK AND i.TENANT_ID = :tenant WHERE v.TENANT_ID = :tenant AND v.IS_ACTIVE_FL AND i.IS_ACTIVE_FL AND (:text IS NULL OR v.CODE ILIKE '%' || :textEsc || '%' ESCAPE '\' OR CAT_NORMALIZE_AR(i.NAME_AR) LIKE '%' || CAT_NORMALIZE_AR(:textEsc) || '%' ESCAPE '\' OR EXISTS (SELECT 1 FROM CAT_BARCODE b WHERE b.TENANT_ID = :tenant AND b.VARIANT_FK = v.VARIANT_PK AND b.IS_ACTIVE_FL AND b.BARCODE_VALUE LIKE '%' || :textEsc || '%' ESCAPE '\')) ORDER BY i.NAME_AR, v.CODE LIMIT :pageSize OFFSET :offset (both taken from the Pageable that PageableBuilder built — size ≤ 200, page ≥ 0)
Join : required — ADR-CAT-047 (the result shows the Item name) · Transaction : READ_ONLY · Locking : NONE · Pagination : YES (Page<T>) · Filters : text LIKE (escaped; names normalised — ADR-CAT-062) · Result shape : projection · Null handling : FIRST_BARCODE null when the Variant has no active GTIN or INTERNAL barcode bound to the Item's base unit (a SCALE code or a carton barcode is not a label barcode — ADR-CAT-079); the text filter still matches any active barcode

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
Intent       : assignments follow their Variant (ENT-CAT-005 isActiveFl), each one recorded by the given audit (@Audited) and by its own catalogue-change event DEACTIVATED with its recordPk (RULE-CAT-049)
Logical spec : load the active assignments of the Variant as entities — SELECT a FROM CatVariantAttributeAssignment a WHERE a.variantFk = :variantPk AND a.isActiveFl (JPQL under the given tenant filter) — then, for each entity, set isActiveFl false and save it through the repository (one UPDATE … WHERE VARIANT_ATTRIBUTE_ASSIGNMENT_PK = :pk AND VERSION = :version per entity, with VERSION, UPDATED_BY and UPDATED_AT set by the given AuditableEntity); no JPQL bulk update, so the @Audited and AuditableEntity hooks run for every row
Join : NONE · Transaction : READ_WRITE · Locking : under the Item lock (QR-CAT-010), already held by API-CAT-009, plus each entity's optimistic VERSION · Pagination : NO (at most 3 rows — one per attribute) · Filters : VARIANT_FK EXACT · Result shape : the assignment entities deactivated (one catalogue-change event DEACTIVATED each, carrying its variantAttributeAssignmentPk) · Null handling : —

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
API          : API-CAT-001, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-040, API-CAT-049, API-CAT-053
Intent       : read; parent of a create or of a reactivation; Category of an Item (active check, FOR SHARE — at save, at reactivation and in the import's write pass); the derived Category label of the ItemSummary that API-CAT-004 returns (no lock — ADR-CAT-075); deactivation and reactivation (FOR UPDATE); import resolves by code
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
Intent       : REQ-CAT-058 — lines with the component Variant code and the unit of their quantity, the component Item's base unit (ADR-CAT-080)
Logical spec : SELECT c.*, v.CODE AS COMPONENT_VARIANT_CODE, ci.BASE_UOM_CODE AS COMPONENT_BASE_UOM_CODE FROM CAT_BUNDLE_COMP c JOIN CAT_VARIANT v ON v.VARIANT_PK = c.COMPONENT_VARIANT_FK JOIN CAT_ITEM ci ON ci.ITEM_PK = v.ITEM_FK WHERE c.BUNDLE_ITEM_FK = :itemPk AND c.IS_ACTIVE_FL ORDER BY c.SORT_ORDER
Join : required — ADR-CAT-047 (component code), ADR-CAT-080 (component Item base unit) · Transaction : READ_ONLY · Locking : NONE (Bundle save holds the Item lock) · Pagination : NO · Filters : BUNDLE_ITEM_FK EXACT · Result shape : full entity + code + base unit · Null handling : —

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
API          : API-CAT-002, API-CAT-010, API-CAT-014, API-CAT-015, API-CAT-056
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
Intent       : reads (every active barcode); with the label filter, RULE-CAT-037 and the label barcode of each Variant — the earliest-created active GTIN or INTERNAL barcode bound to the Item's base unit (ADR-CAT-079)
Logical spec : SELECT b.* FROM CAT_BARCODE b WHERE b.VARIANT_FK IN (:variantPks) AND b.IS_ACTIVE_FL [labels: AND b.BARCODE_TYPE_CODE IN ('GTIN', 'INTERNAL') AND b.UOM_CODE = (SELECT i.BASE_UOM_CODE FROM CAT_VARIANT v JOIN CAT_ITEM i ON i.ITEM_PK = v.ITEM_FK WHERE v.VARIANT_PK = b.VARIANT_FK)] ORDER BY b.VARIANT_FK, b.CREATED_AT, b.BARCODE_PK — the label filter is applied only by API-CAT-050, which takes the first row per Variant as its label barcode; API-CAT-002 and 007 read every active barcode
Join : NONE (the label filter reads the Item's base unit by sub-query — ADR-CAT-079) · Transaction : READ_ONLY · Locking : NONE · Pagination : NO · Filters : VARIANT_FK SET; labels: BARCODE_TYPE_CODE SET, UOM_CODE EXACT against the Item's BASE_UOM_CODE · Result shape : full entity · Null handling : labels: a Variant with no row has no label barcode (RULE-CAT-037)

QR-CAT-048 — Active barcode value taken
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : EXISTS
API          : API-CAT-014, API-CAT-015, API-CAT-049, API-CAT-056
Intent       : RULE-CAT-029 — returns the owning Item code for {code}
Logical spec : SELECT i.CODE FROM CAT_BARCODE b JOIN CAT_VARIANT v ON v.VARIANT_PK = b.VARIANT_FK JOIN CAT_ITEM i ON i.ITEM_PK = v.ITEM_FK WHERE b.IS_ACTIVE_FL AND b.BARCODE_VALUE IN (:values) LIMIT 1
Join : required — ADR-CAT-047 (owning Item code in the message) · Transaction : READ_ONLY · Locking : NONE — UQ_CAT_BARCODE_VALUE decides a race · Pagination : NO · Filters : BARCODE_VALUE SET · Result shape : projection CODE · Null handling : none found → free

QR-CAT-049 — Insert barcode
Phase        : DATA-DOM · Entity : ENT-CAT-009 · Operation : SAVE
API          : API-CAT-014, API-CAT-015, API-CAT-049, API-CAT-056
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
API          : API-CAT-014, API-CAT-015, API-CAT-043, API-CAT-049, API-CAT-056
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
Logical spec : load the active barcodes of the Variant as entities — SELECT b FROM CatBarcode b WHERE b.variantFk = :variantPk AND b.isActiveFl (JPQL under the given tenant filter) — then, for each entity, set isActiveFl false and save it through the repository (one UPDATE … WHERE BARCODE_PK = :pk AND VERSION = :version per entity, audit columns set by the given AuditableEntity); no bulk update and no RETURNING, so the @Audited and AuditableEntity hooks run for every row and every barcodePk is known
Join : NONE · Transaction : READ_WRITE · Locking : under the Item lock (QR-CAT-010), already held by API-CAT-009, plus each entity's optimistic VERSION · Pagination : NO · Filters : VARIANT_FK EXACT · Result shape : the barcode entities deactivated (one catalogue-change event DEACTIVATED each, carrying its barcodePk) · Null handling : —

QR-CAT-073 — Category tree lock
Phase        : DATA-DOM · Entity : ENT-CAT-006 · Operation : NATIVE
API          : API-CAT-021, API-CAT-023, API-CAT-024, API-CAT-053
Intent       : serialise every Category create, move, deactivation and reactivation of one tenant, so the parent, cycle, depth (RULE-CAT-023) and in-use (RULE-CAT-025) decisions are taken on a stable tree (ADR-CAT-047)
Logical spec : SELECT pg_advisory_xact_lock(CAST(:tenant AS integer), hashtext('CAT_CATEGORY_TREE')) — the `(int, int)` form of PostgreSQL 16 (only `(bigint)` and `(int, int)` exist); the first key is the tenant, the second the 32-bit hash of the lock name. `:tenant` is TENANT_ID, which is below 2^31 for every tenant of the given (CORE_TENANT ids are issued from 1), so the cast never overflows
Join : NONE · Transaction : READ_WRITE (transaction-scoped: released at commit or rollback) · Locking : advisory exclusive, taken first in its request — lock order tree lock → Category row (QR-CAT-032) · Tenant : native — the lock key names TENANT_ID as its first part, bound from `TenantContext.require()`, never from the request; the repository method `CatCategoryRepository.lockTree(int tenant)` carries `@TenantUnscoped(reason = "advisory lock keyed by TENANT_ID", adr = "ADR-CAT-047")` · Pagination : NO · Filters : — · Result shape : void (the call blocks until granted) · Null handling : —

QR-CAT-074 — Scale-prefix lock
Phase        : DATA-DOM · Entity : ENT-CAT-011 · Operation : NATIVE
API          : API-CAT-014, API-CAT-015, API-CAT-032, API-CAT-049, API-CAT-056
Intent       : serialise scale-label-layout creates with GTIN / INTERNAL barcode adds of one tenant, so a layout key and a barcode that begins with it are never both saved (RULE-CAT-032, ADR-CAT-047); the import takes it once per import (ADR-CAT-071)
Logical spec : SELECT pg_advisory_xact_lock(CAST(:tenant AS integer), hashtext('CAT_SCALE_PREFIX')) — the same `(int, int)` form as QR-CAT-073; the two names hash to different second keys, so the two locks never block each other
Join : NONE · Transaction : READ_WRITE (transaction-scoped: released at commit or rollback) · Locking : advisory exclusive; barcode adds take it after the Item lock (QR-CAT-010), the import takes it before the Category share locks (QR-CAT-032) · Tenant : native — the lock key names TENANT_ID as its first part, bound from `TenantContext.require()`, never from the request; the repository method `CatScaleLabelLayoutRepository.lockScalePrefix(int tenant)` carries `@TenantUnscoped(reason = "advisory lock keyed by TENANT_ID", adr = "ADR-CAT-047")` · Pagination : NO · Filters : — · Result shape : void (the call blocks until granted) · Null handling : —

QR-CAT-075 — Clear the default flag of an Item's Variants
Phase        : DATA-DOM · Entity : ENT-CAT-002 · Operation : UPDATE
API          : API-CAT-006, API-CAT-009
Intent       : the first half of a default-flag move (REQ-CAT-140, REQ-CAT-141, ADR-CAT-072): clear isDefaultFl on the Item's current default — active (API-CAT-009, the Variant being deactivated) or inactive (API-CAT-006, generation on an Item with 0 active Variants) — before QR-CAT-014 sets it on the new default, so UQ_CAT_VARIANT_DEFAULT holds after each statement
Logical spec : load the Item's current default Variant as an entity — SELECT v FROM CatVariant v WHERE v.itemFk = :itemPk AND v.isDefaultFl (JPQL under the given tenant filter; at most one row — UQ_CAT_VARIANT_DEFAULT), or reuse the entity the caller already loaded — set isDefaultFl false, save it through the repository and **flush** (one UPDATE … WHERE VARIANT_PK = :pk AND VERSION = :version, audit columns set by the given AuditableEntity); only after that flush does the caller set isDefaultFl true on the new default and save it (QR-CAT-014), so the database sees the clear before the set and UQ_CAT_VARIANT_DEFAULT holds after each statement; no bulk update and no RETURNING
Join : NONE · Transaction : READ_WRITE · Locking : under the Item lock (QR-CAT-010), so no parallel move or deactivation interleaves, plus the entity's optimistic VERSION · Pagination : NO · Filters : ITEM_FK EXACT · Result shape : the Variant entity whose flag was cleared (at most one; one catalogue-change event UPDATED, carrying its variantPk, unless the same Variant is being deactivated in the same request, in which case its one event is DEACTIVATED) · Null handling : no default row → nothing to clear

## ERROR CATALOG — CAT v2

Envelope `LocalizedException → {code, messageAr, messageEn}`; codes follow `{MOD}-{http}[-{SLUG}]`; statuses are those the platform emits. Messages are copied character for character from the SRS. The SRS gives no text for the PLATFORM-STD rows, so their ar / en text is written here; every row, RULE or PLATFORM-STD, has both messages and both bundle entries (checklist C11, ADR-CAT-044, ADR-CAT-065). Rows with a 2xx status are success notices carried in `ApiResponse<T>` (ADR-CAT-048).

```yaml name=error-catalog
rows:
  - {code: "CAT-422-NAME-AR-INVALID", rule: RULE-CAT-001, api: [API-CAT-001, API-CAT-003, API-CAT-018, API-CAT-019, API-CAT-021, API-CAT-023, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030], http: 422, trigger: "nameAr missing, longer than 200 characters or without an Arabic letter", messages: {ar: "أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.", en: "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."}}
  - {code: "CAT-422-NAME-EN-TOO-LONG", rule: RULE-CAT-002, api: [API-CAT-001, API-CAT-003, API-CAT-018, API-CAT-019, API-CAT-021, API-CAT-023, API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030], http: 422, trigger: "nameEn longer than 200 characters", messages: {ar: "الاسم بالإنجليزية لا يتجاوز 200 حرف.", en: "The English name can have at most 200 characters."}}
  - {code: "CAT-409-ITEM-NAME-DUPLICATE", rule: RULE-CAT-003, api: [API-CAT-001, API-CAT-003, API-CAT-005], http: 409, trigger: "another active Item of the tenant has the same normalised nameAr ({code} = that Item's code, from QR-CAT-002)", messages: {ar: "يوجد صنف نشط بالاسم نفسه: {code}. افتح الصنف الموجود أو غيّر الاسم.", en: "An active item already has this name: {code}. Open the existing item or change the name."}}
  - {code: "CAT-422-CODE-SYSTEM-ASSIGNED", rule: RULE-CAT-004, api: [API-CAT-001, API-CAT-003, API-CAT-008], http: 422, trigger: "a request supplies or changes the code of an Item or a Variant", messages: {ar: "يُعيَّن الرمز تلقائيًا ولا يمكن إدخاله أو تعديله.", en: "The code is assigned automatically and cannot be entered or changed."}}
  - {code: "CAT-500-CODE-NOT-ISSUED", rule: RULE-CAT-005, api: [API-CAT-001, API-CAT-006, API-CAT-015, API-CAT-049, API-CAT-056], http: 500, trigger: "NumberSeriesApi returns no code for CAT_ITEM, CAT_VARIANT or CAT_INTERNAL_BARCODE; nothing is stored", messages: {ar: "تعذّر إصدار رمز جديد الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.", en: "A new code could not be issued, and nothing was saved. Try again in a moment."}}
  - {code: "CAT-422-LOOKUP-VALUE-INACTIVE", rule: RULE-CAT-006, api: [API-CAT-001, API-CAT-003, API-CAT-005, API-CAT-008, API-CAT-010, API-CAT-014, API-CAT-015, API-CAT-032, API-CAT-033, API-CAT-035, API-CAT-036, API-CAT-056], http: 422, trigger: "a changed lookup-backed field holds a code that is not an active value of its key", messages: {ar: "القيمة المختارة غير متاحة. اختر قيمة نشطة من القائمة.", en: "The selected value is not available. Choose an active value from the list."}}
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
  - {code: "CAT-422-BUNDLE-COMPONENTS-REQUIRED", rule: RULE-CAT-018, api: [API-CAT-001, API-CAT-017], http: 422, trigger: "a BUNDLE saved with no active line; a quantity not above zero, above 999,999.999 or with more than 3 decimals is refused first by the document check (CAT-400-REQUEST-INVALID), and this row stays the domain backstop", messages: {ar: "أضف مكوّنًا واحدًا على الأقل بكمية أكبر من صفر (حتى 3 أرقام عشرية).", en: "Add at least one component with a quantity above zero (up to 3 decimals)."}}
  - {code: "CAT-422-BUNDLE-COMPONENT-INVALID", rule: RULE-CAT-019, api: [API-CAT-001, API-CAT-005, API-CAT-017], http: 422, trigger: "at Bundle save or Bundle reactivation, a component Variant is inactive or of an inactive Item, belongs to a BUNDLE Item, or is listed twice", messages: {ar: "المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها.", en: "A component must be an active variant of an item that is not a bundle, listed once per bundle."}}
  - {code: "CAT-422-BUNDLE-SINGLE-VARIANT", rule: RULE-CAT-020, api: [API-CAT-006], http: 422, trigger: "Variant generation or attribute assignment for an Item of kind BUNDLE", messages: {ar: "الحزمة لها متغيّر واحد فقط ولا تقبل خصائص المتغيّرات.", en: "A bundle has one variant only and takes no variant attributes."}}
  - {code: "CAT-409-VARIANT-IN-ACTIVE-BUNDLE", rule: RULE-CAT-021, api: [API-CAT-004, API-CAT-009], http: 409, trigger: "deactivating a Variant, or its Item, while it is an active component of an active Bundle ({count})", messages: {ar: "هذا المتغيّر مكوّن في {count} حزمة نشطة. أزله من هذه الحزم أو أوقفها أولًا.", en: "This variant is a component of {count} active bundles. Remove it from those bundles or deactivate them first."}}
  - {code: "CAT-422-ITEM-KIND-FIXED", rule: RULE-CAT-022, api: [API-CAT-003], http: 422, trigger: "an update changes itemKindCode of a saved Item", messages: {ar: "لا يمكن تغيير نوع الصنف بعد حفظه. أنشئ صنفًا جديدًا بالنوع المطلوب.", en: "The item kind cannot be changed after saving. Create a new item of the required kind."}}
  - {code: "CAT-422-CATEGORY-PARENT-INVALID", rule: RULE-CAT-023, api: [API-CAT-021, API-CAT-023, API-CAT-053], http: 422, trigger: "parent inactive (also when the Category is reactivated), the Category itself or one of its sub-categories, or depth above 5", messages: {ar: "الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.", en: "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels."}}
  - {code: "CAT-409-CATEGORY-DUPLICATE", rule: RULE-CAT-024, api: [API-CAT-021, API-CAT-023, API-CAT-053], http: 409, trigger: "Category code taken, nameAr taken among active siblings (also when the Category is reactivated), or code not 1–30 Latin letters, digits or hyphens", messages: {ar: "يوجد رمز الفئة نفسه، أو الاسم نفسه تحت الفئة الأم نفسها. استخدم رمزًا أو اسمًا مختلفًا.", en: "The category code already exists, or the name already exists under the same parent. Use a different code or name."}}
  - {code: "CAT-409-CATEGORY-IN-USE", rule: RULE-CAT-025, api: [API-CAT-024], http: 409, trigger: "deactivating a Category with active sub-categories or active Items ({count})", messages: {ar: "لا يمكن إيقاف الفئة لوجود {count} فئة فرعية أو صنف نشط فيها. انقلها أو أوقفها أولًا.", en: "The category cannot be deactivated while it holds {count} active sub-categories or items. Move or deactivate them first."}}
  - {code: "CAT-422-CATEGORY-INACTIVE", rule: RULE-CAT-026, api: [API-CAT-001, API-CAT-003, API-CAT-005], http: 422, trigger: "a changed categoryFk, or the categoryFk of an Item being reactivated, points to an inactive Category", messages: {ar: "الفئة المختارة غير نشطة. اختر فئة نشطة.", en: "The selected category is inactive. Choose an active category."}}
  - {code: "CAT-422-ITEM-UNIT-INVALID", rule: RULE-CAT-027, api: [API-CAT-010], http: 422, trigger: "unit equals the base unit or another active unit of the Item; a factor not above zero, above 999,999.999999 or with more than 6 decimals is refused first by the document check (CAT-400-REQUEST-INVALID), and this row stays the domain backstop", messages: {ar: "الوحدة يجب أن تختلف عن الوحدة الأساسية وألا تتكرر، ومعامل التحويل أكبر من صفر (حتى 6 أرقام عشرية).", en: "The unit must differ from the base unit and appear once, with a conversion factor above zero (up to 6 decimals)."}}
  - {code: "CAT-422-CONVERSION-FACTOR-FIXED", rule: RULE-CAT-028, api: [API-CAT-012], http: 422, trigger: "an update changes conversionFactor of a saved Item unit", messages: {ar: "لا يمكن تعديل معامل التحويل بعد الحفظ. أوقف هذه الوحدة وأضفها من جديد بالمعامل الصحيح.", en: "The conversion factor cannot be changed after saving. Deactivate this unit and add it again with the correct factor."}}
  - {code: "CAT-409-BARCODE-DUPLICATE", rule: RULE-CAT-029, api: [API-CAT-014, API-CAT-015, API-CAT-056], http: 409, trigger: "barcodeValue equals an active barcode of the tenant ({code} = owning Item code)", messages: {ar: "هذا الباركود مستخدم في الصنف {code}. افتح ذلك الصنف أو استخدم باركودًا آخر.", en: "This barcode is already used by item {code}. Open that item or use another barcode."}}
  - {code: "CAT-422-GTIN-INVALID", rule: RULE-CAT-030, api: [API-CAT-014], http: 422, trigger: "GTIN not 8, 12, 13 or 14 digits, or wrong GS1 check digit", messages: {ar: "باركود GTIN يجب أن يتكون من 8 أو 12 أو 13 أو 14 رقمًا برقم تحقق صحيح.", en: "A GTIN barcode must have 8, 12, 13 or 14 digits with a valid check digit."}}
  - {code: "CAT-422-BARCODE-UNIT-INVALID", rule: RULE-CAT-031, api: [API-CAT-014, API-CAT-015, API-CAT-056], http: 422, trigger: "barcode unit is neither the Item's base unit nor an active Item unit", messages: {ar: "وحدة الباركود يجب أن تكون الوحدة الأساسية للصنف أو إحدى وحداته النشطة.", en: "The barcode unit must be the item's base unit or one of its active units."}}
  - {code: "CAT-409-BARCODE-SCALE-PREFIX", rule: RULE-CAT-032, api: [API-CAT-014, API-CAT-015, API-CAT-032, API-CAT-056], http: 409, trigger: "a GTIN / INTERNAL barcode begins with an active layout key, or a new layout key begins an active GTIN / INTERNAL barcode ({prefix})", messages: {ar: "الباركود يبدأ ببادئة ملصق ميزان نشط ({prefix}). استخدم باركودًا آخر أو عدّل إعداد الميزان.", en: "The barcode starts with the prefix of an active scale label layout ({prefix}). Use another barcode or change the scale settings."}}
  - {code: "CAT-422-SCALE-CODE-LENGTH", rule: RULE-CAT-033, api: [API-CAT-014], http: 422, trigger: "SCALE barcode not all digits, of a length no active layout uses, or no active layout exists ({length})", messages: {ar: "رمز الميزان يجب أن يتكون من {length} أرقام، ويلزم وجود إعداد ملصق ميزان نشط.", en: "The scale code must have {length} digits, and an active scale label layout is required."}}
  - {code: "CAT-422-SCALE-LAYOUT-INVALID", rule: RULE-CAT-034, api: [API-CAT-032, API-CAT-033], http: 422, trigger: "prefix outside 02 / 20–29 or used by another active layout, scaleCodeLength outside 4–6, valueDecimals outside 0–3", messages: {ar: "البادئة رقمان (02 أو من 20 إلى 29) غير مستخدمة في إعداد نشط آخر، وطول رمز الميزان من 4 إلى 6، والأرقام العشرية من 0 إلى 3.", en: "The prefix is two digits (02 or 20 to 29) not used by another active layout, the scale code length is 4 to 6, and the decimals are 0 to 3."}}
  - {code: "CAT-422-SCALE-LABEL-UNREADABLE", rule: RULE-CAT-035, api: [API-CAT-043], http: 422, trigger: "a value with an active layout prefix has a wrong check digit or an unknown scale code", messages: {ar: "تعذّر قراءة ملصق الميزان: رقم التحقق غير صحيح أو رمز الميزان غير معرَّف. اطلب الصنف بالاسم أو أضف رمز الميزان إليه.", en: "The scale label could not be read: the check digit is wrong or the scale code is not defined. Find the item by name or add the scale code to it."}}
  - {code: "CAT-422-LABEL-COPIES-OUT-OF-RANGE", rule: RULE-CAT-036, api: [API-CAT-050], http: 422, trigger: "fewer than 1 or more than 999 copies of a Variant, or more than 5,000 labels", messages: {ar: "عدد النسخ لكل متغيّر من 1 إلى 999، وإجمالي الملصقات لا يتجاوز 5000.", en: "Copies per variant are 1 to 999, and the total cannot exceed 5,000 labels."}}
  - {code: "CAT-422-LABEL-VARIANT-NO-BARCODE", rule: RULE-CAT-037, api: [API-CAT-050], http: 422, trigger: "a label request lists Variants without an active GTIN or INTERNAL barcode bound to their Item's base unit — a SCALE code or a barcode bound to an Item unit does not count ({count}; the Variants are listed — ADR-CAT-079)", messages: {ar: "لا يوجد باركود نشط بالوحدة الأساسية لـ {count} متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع.", en: "{count} variants have no active barcode in the base unit. Generate an internal barcode for them, then print."}}
  - {code: "CAT-422-IMAGE-NOT-ACCEPTED", rule: RULE-CAT-038, api: [API-CAT-018], http: 422, trigger: "a 21st active image of the Item (the type, size and public-storage part of RULE-CAT-038 is refused at upload by the FILE category CAT_ITEM_IMAGE, in the FILE envelope; a reference that is not available or has no public URL answers CAT-404-NOT-FOUND — ADR-CAT-076)", messages: {ar: "الصورة يجب أن تكون JPG أو PNG أو WEBP بحجم لا يتجاوز 5 ميجابايت، وبحد أقصى 20 صورة نشطة للصنف.", en: "The image must be JPG, PNG or WEBP of at most 5 MB, with at most 20 active images per item."}}
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
  - {code: "CAT-500-CHANGE-NOT-SAVED", rule: RULE-CAT-049, api: [API-CAT-001, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-023, API-CAT-024, API-CAT-049, API-CAT-053, API-CAT-056], http: 500, trigger: "the commit of a catalogue change fails and the transaction rolls back; no event is delivered", messages: {ar: "تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة.", en: "The change was not saved and no change notice was sent. Try again."}}
  - {code: "CAT-422-ITEM-NO-ACTIVE-VARIANT", rule: RULE-CAT-050, api: [API-CAT-005], http: 422, trigger: "reactivating an Item that has no active Variant", messages: {ar: "لا يمكن إعادة تفعيل صنف بلا متغيّر نشط. أنشئ متغيّرًا له أو أنشئ صنفًا جديدًا.", en: "An item with no active variant cannot be reactivated. Generate a variant for it, or create a new item."}}
  - {code: "CAT-409-ITEM-UNIT-HAS-BARCODES", rule: RULE-CAT-051, api: [API-CAT-013], http: 409, trigger: "deactivating an Item unit while active barcodes of the Item's Variants are bound to it ({count})", messages: {ar: "هذه الوحدة مستخدمة في {count} باركود نشط. أوقف هذه الباركودات أولًا.", en: "This unit is used by {count} active barcodes. Deactivate those barcodes first."}}
  - {code: "CAT-422-VARIANT-ATTRIBUTE-MISSING", rule: RULE-CAT-052, api: [API-CAT-006], http: 422, trigger: "a generation request omits an attribute the Item's active Variants already carry, or selects no value for it", messages: {ar: "اختر قيمة واحدة على الأقل لكل خاصية يستخدمها الصنف بالفعل.", en: "Select at least one value for every attribute the item already uses."}}
  - {code: "CAT-422-PARENT-INACTIVE", rule: RULE-CAT-053, api: [API-CAT-003, API-CAT-008, API-CAT-010, API-CAT-012, API-CAT-014, API-CAT-015, API-CAT-018, API-CAT-019, API-CAT-056], http: 422, trigger: "an update of an inactive Item that changes any field other than categoryFk, or an Item unit or image create / update under an inactive Item, or a barcode / e-invoice override on a Variant that is inactive or of an inactive Item; child deactivations and the three repairs of ADR-CAT-068 (Variant generation API-CAT-006, a categoryFk-only update API-CAT-003, Bundle components API-CAT-017) are never refused by it", messages: {ar: "هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا.", en: "This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant."}}
  - {code: "CAT-400-REQUEST-INVALID", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055, API-CAT-056], http: 400, trigger: "malformed body or parameter, wrong type or format, an empty array where at least one entry is required (Variant generation selections or values), a value not offered (inactive attribute / value, unknown flag name, wrong item kind for the endpoint), an update of an inactive scale label layout or Unit e-invoice code; a request whose fields contradict each other or that names a record not part of the addressed parent, refused before any RULE runs and writing nothing (PHASE CORE, API conventions): the same attribute twice in Variant generation selections, the same value id twice, a value not of its selection's attribute (API-CAT-006), a non-empty bundleComponents on a STANDARD Item or a component Variant listed twice (API-CAT-001), a bundleComponentPk not an active line of the addressed Bundle, a kept line whose componentVariantFk changed, or a line listed twice (API-CAT-017), barcode type INTERNAL on API-CAT-014, any property other than uomCode on API-CAT-015 and API-CAT-056; a Bundle quantity or an Item unit conversion factor outside the document's bounds (API-CAT-001, 010, 017 — PHASE CORE, request numbers); a PUT on an inactive Item unit, removed image, inactive Category, Variant attribute or value (API-CAT-012, 019, 023, 027, 030 — PHASE CORE, update of an inactive record)", messages: {ar: "الطلب غير صالح. راجع الحقول المحددة ثم أعد المحاولة.", en: "The request is not valid. Check the highlighted fields and try again."}, adr: "ADR-CAT-044"}
  - {code: "CAT-401-UNAUTHENTICATED", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055, API-CAT-056], http: 401, trigger: "no valid bearer token", messages: {ar: "انتهت جلستك أو لم تسجّل الدخول. سجّل الدخول ثم أعد المحاولة.", en: "You are not signed in or your session has ended. Sign in, then try again."}, adr: "ADR-CAT-044"}
  - {code: "CAT-403-FORBIDDEN", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055, API-CAT-056], http: 403, trigger: "the caller lacks the endpoint's PERM_<PAGE_CODE>_<ACTION>", messages: {ar: "ليس لديك صلاحية لتنفيذ هذه العملية. اطلب الصلاحية من مالك النشاط.", en: "You do not have permission to perform this operation. Ask the business owner for access."}, adr: "ADR-CAT-044"}
  - {code: "CAT-404-NOT-FOUND", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-033, API-CAT-034, API-CAT-036, API-CAT-037, API-CAT-040, API-CAT-044, API-CAT-049, API-CAT-050, API-CAT-053, API-CAT-054, API-CAT-055, API-CAT-056], http: 404, trigger: "an identifier (a path id, a body reference or a fileDocumentRef) names no record or available file of the caller's tenant", messages: {ar: "السجل المطلوب غير موجود. حدّث القائمة ثم أعد المحاولة.", en: "The requested record was not found. Refresh the list and try again."}, adr: "ADR-CAT-044"}
  - {code: "CAT-409-STALE-VERSION", rule: PLATFORM-STD, api: [API-CAT-003, API-CAT-008, API-CAT-012, API-CAT-017, API-CAT-019, API-CAT-023, API-CAT-027, API-CAT-030, API-CAT-033, API-CAT-036], http: 409, trigger: "the echoed version differs from the stored one (optimistic lock)", messages: {ar: "عدّل مستخدم آخر هذا السجل في الأثناء. أعد تحميله ثم حاول مجددًا.", en: "This record was changed by someone else in the meantime. Reload it and try again."}, adr: "ADR-CAT-044"}
  - {code: "CAT-500-SERVER-ERROR", rule: PLATFORM-STD, api: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055, API-CAT-056], http: 500, trigger: "unexpected failure; the transaction rolls back and no event is delivered", messages: {ar: "حدث خطأ غير متوقع ولم يُحفظ شيء. حاول مرة أخرى لاحقًا.", en: "An unexpected error occurred, and nothing was saved. Try again later."}, adr: "ADR-CAT-044"}
  - {code: "CAT-404-LOOKUP-KEY-UNKNOWN", rule: PLATFORM-STD, api: [API-CAT-052], http: 404, trigger: "the requested key is not one of the 10 CAT lookup keys, or mdl answers 404 for it (OwnedLookups translates mdl's 404 to this code)", messages: {ar: "هذه القائمة ليست من قوائم الكتالوج. اختر إحدى قوائم الكتالوج.", en: "This list is not one of the catalogue lists. Choose a catalogue list."}, adr: "ADR-CAT-065"}
```

## API DOCUMENT
`api-spec-cat.yaml` (OpenAPI 3.1.0) is derived from the API blocks and this catalog in the same run: one operation per `API-*` block (path and method from its `Endpoint` line, `x-api-id`, `x-traces`), request parameters and bodies from its `Request` line, the success response from its `Response` line, and one response per catalog row the block's `Errors` line names, keyed by the row's status with the row's code under `x-error-codes`; bearer JWT security on every operation; paged operations carry `page` and `size`.
