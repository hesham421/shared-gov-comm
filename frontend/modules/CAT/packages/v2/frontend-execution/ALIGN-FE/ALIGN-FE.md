<!-- source: PHASE:ALIGN-FE -->
<!-- traces: API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055, API-CAT-056, SCR-CAT-001, SCR-CAT-002, SCR-CAT-003, SCR-CAT-004, SCR-CAT-005, SCR-CAT-006, UXD-CAT-001, UXD-CAT-002, UXD-CAT-003 -->
<!-- PHASE:ALIGN-FE:START traces=API-CAT-056,API-CAT-053,API-CAT-054,API-CAT-055,API-CAT-052,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-038,API-CAT-039,API-CAT-040,API-CAT-041,API-CAT-042,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,API-CAT-048,API-CAT-049,API-CAT-050,API-CAT-051,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001,SCR-CAT-002,SCR-CAT-003,SCR-CAT-004,SCR-CAT-005,SCR-CAT-006 -->
## ALIGN-FE

The checks run against the plan itself, the API document and the SRS ceiling. The cross-artifact checks are `gov.py analyze`'s. Each row names the check that backs it. The result column is copied by the orchestrator from the analyze report; this author writes no mark of their own.

```
ALIGN — CAT v2
row           backing check       assertion                                                                                       result
SCREENS       orphans             every SCR is referenced by a plan block                                                         (stamped from the analyze report)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once                          (stamped from the analyze report)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it        (stamped from the analyze report)
READS         ux-reads-spec       every read a screen binds is an operation of api-spec-cat.yaml, and the plan names the document its mock server serves   (stamped from the analyze report)
UXD           orphans             every UXD is cited by a plan block — this is where a UX decision closes                         (stamped from the analyze report)
TRACES        traces              every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD       (stamped from the analyze report)
API           traces              every API this plan cites is an operation of api-spec-cat.yaml — never a line of the backend plan's prose   (stamped from the analyze report)
FOREIGN       xref-surface        every reference to another module's surface resolves in that module's own artifacts             (stamped from the analyze report)
REGISTRY      registry-agree      every UXD and SCR defined here is in the stage registry, and nothing else is                    (stamped from the analyze report)
LANGUAGES     languages           labels and messages in ar + en                                                                  (stamped from the analyze report)
MARKERS       markers             the parser reports no structural or semantic error for this track and plan                      (stamped from the analyze report)
DECISIONS     refs-exist          every ADR this plan cites exists on disk in analysis/decisions/CAT/                             (stamped from the analyze report)
COVERAGE      (the report)        the clauses the analyze report lists as having examined nothing — verbatim, or `none`           (stamped from the analyze report)
```

```yaml name=self-check
findings: 0
clean: true
```

Operations coverage (56 operations of `api-spec-cat.yaml`):

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| create Item | API-CAT-001 | SCR-CAT-001 Save (new) | `/cat/items/new` | ✓ |
| read Item | API-CAT-002 | SCR-CAT-001 open Entry; component picker expand | `/cat/items/:itemPk` | ✓ |
| update Item | API-CAT-003 | SCR-CAT-001 Save (header) | `/cat/items/:itemPk` | ✓ |
| deactivate Item | API-CAT-004 | SCR-CAT-001 Deactivate | `/cat/items/:itemPk` | ✓ |
| reactivate / publish / unpublish Item | API-CAT-005 | SCR-CAT-001 Reactivate · Publish · Unpublish | `/cat/items/:itemPk` | ✓ |
| generate Variants | API-CAT-006 | SCR-CAT-001 Save (pending generation) | `/cat/items/:itemPk?picker=variant-generation` | ✓ |
| read Variant | API-CAT-007 | SCR-CAT-001 component line label · SCR-CAT-005 seeded line | `/cat/items/:itemPk` · `/cat/barcode-labels?variantPk=` | ✓ |
| update Variant e-invoice override | API-CAT-008 | SCR-CAT-001 Save (override line) | `/cat/items/:itemPk?drawer=override:<variantPk>` | ✓ |
| deactivate Variant | API-CAT-009 | SCR-CAT-001 Save (Variant line marked) | `/cat/items/:itemPk` | ✓ |
| add Item unit | API-CAT-010 | SCR-CAT-001 Save (new unit line) | `/cat/items/:itemPk?drawer=unit:new` | ✓ |
| read Item unit | API-CAT-011 | — (read inside API-CAT-002; consumers' read — ADR-CAT-051) | — | ✗ ADR-CAT-051 |
| update Item unit | API-CAT-012 | SCR-CAT-001 Save (unit flags) | `/cat/items/:itemPk` | ✓ |
| deactivate Item unit | API-CAT-013 | SCR-CAT-001 Save (unit line marked) | `/cat/items/:itemPk` | ✓ |
| add barcode (GTIN or scale code) | API-CAT-014 | SCR-CAT-001 Save (new barcode line) | `/cat/items/:itemPk?drawer=barcode:new` | ✓ |
| generate internal barcode | API-CAT-015 | SCR-CAT-001 Save (internal barcode line) | `/cat/items/:itemPk?drawer=internal-barcode:new` | ✓ |
| deactivate barcode | API-CAT-016 | SCR-CAT-001 Save (barcode line marked) | `/cat/items/:itemPk` | ✓ |
| save Bundle components | API-CAT-017 | SCR-CAT-001 Save (component set changed) | `/cat/items/:itemPk?picker=bundle-components` | ✓ |
| upload image | API-CAT-018 | SCR-CAT-001 Save (new image line, after the given FILE upload) | `/cat/items/:itemPk?drawer=image:new` | ✓ |
| update image | API-CAT-019 | SCR-CAT-001 Save (image line changed) | `/cat/items/:itemPk?drawer=image:<itemMediaPk>` | ✓ |
| remove image | API-CAT-020 | SCR-CAT-001 Save (image line marked) | `/cat/items/:itemPk` | ✓ |
| create Category | API-CAT-021 | SCR-CAT-002 Save (new) | `/cat/categories?node=new` | ✓ |
| read Category | API-CAT-022 | SCR-CAT-002 open entry | `/cat/categories?node=<categoryPk>` | ✓ |
| update Category | API-CAT-023 | SCR-CAT-002 Save | `/cat/categories?node=<categoryPk>` | ✓ |
| deactivate Category | API-CAT-024 | SCR-CAT-002 Deactivate | `/cat/categories?node=<categoryPk>` | ✓ |
| reactivate Category | API-CAT-053 | SCR-CAT-002 Reactivate | `/cat/categories?node=<categoryPk>` | ✓ |
| create Variant attribute | API-CAT-025 | SCR-CAT-003 Save (new) | `/cat/variant-attributes?attribute=new` | ✓ |
| read Variant attribute | API-CAT-026 | SCR-CAT-003 open entry | `/cat/variant-attributes?attribute=<pk>` | ✓ |
| update Variant attribute | API-CAT-027 | SCR-CAT-003 Save | `/cat/variant-attributes?attribute=<pk>` | ✓ |
| deactivate Variant attribute | API-CAT-028 | SCR-CAT-003 Deactivate | `/cat/variant-attributes?attribute=<pk>` | ✓ |
| create Variant attribute value | API-CAT-029 | SCR-CAT-003 Save (new value line) | `/cat/variant-attributes?attribute=<pk>&values=open` | ✓ |
| update Variant attribute value | API-CAT-030 | SCR-CAT-003 Save (value line changed) | `/cat/variant-attributes?attribute=<pk>&values=open` | ✓ |
| deactivate Variant attribute value | API-CAT-031 | SCR-CAT-003 Save (value line marked) | `/cat/variant-attributes?attribute=<pk>&values=open` | ✓ |
| reactivate Variant attribute | API-CAT-054 | SCR-CAT-003 Reactivate | `/cat/variant-attributes?attribute=<pk>` | ✓ |
| reactivate Variant attribute value | API-CAT-055 | SCR-CAT-003 Save (value line marked reactivate) | `/cat/variant-attributes?attribute=<pk>&values=open` | ✓ |
| create scale label layout | API-CAT-032 | SCR-CAT-006 layout Save (new) | `/cat/settings?layout=new` | ✓ |
| update scale label layout | API-CAT-033 | SCR-CAT-006 layout Save | `/cat/settings?layout=<pk>` | ✓ |
| deactivate scale label layout | API-CAT-034 | SCR-CAT-006 layout Deactivate | `/cat/settings?layout=<pk>` | ✓ |
| create Unit e-invoice code | API-CAT-035 | SCR-CAT-006 unit code Save (new) | `/cat/settings?unitCode=new` | ✓ |
| update Unit e-invoice code | API-CAT-036 | SCR-CAT-006 unit code Save | `/cat/settings?unitCode=<pk>` | ✓ |
| deactivate Unit e-invoice code | API-CAT-037 | SCR-CAT-006 unit code Deactivate | `/cat/settings?unitCode=<pk>` | ✓ |
| search Items | API-CAT-038 | SCR-CAT-001 Search; component picker | `/cat/items` | ✓ |
| list Category tree | API-CAT-039 | SCR-CAT-002 tree; SCR-CAT-001 Category options | `/cat/categories` · `/cat/items` | ✓ |
| Items of a Category | API-CAT-040 | SCR-CAT-002 Items of this category | `/cat/categories?node=<pk>&items=open` | ✓ |
| list Variant attributes with values | API-CAT-041 | SCR-CAT-003 list; SCR-CAT-001 generation picker | `/cat/variant-attributes` · `/cat/items/:itemPk?picker=variant-generation` | ✓ |
| search Variants | API-CAT-042 | SCR-CAT-005 Variant picker | `/cat/barcode-labels?picker=variants` | ✓ |
| barcode and scale label lookup | API-CAT-043 | SCR-CAT-001 Find by barcode | `/cat/items` | ✓ |
| images of a Variant | API-CAT-044 | SCR-CAT-001 Variant row expand | `/cat/items/:itemPk` | ✓ |
| read catalogue settings | API-CAT-045 | SCR-CAT-006 lists; SCR-CAT-001 SCALE availability | `/cat/settings` · `/cat/items/:itemPk` | ✓ |
| storefront catalogue read | API-CAT-046 | — (SFR consumer — ADR-CAT-051) | — | ✗ ADR-CAT-051 |
| catalogue search (global search) | API-CAT-047 | SCR-CAT-001 exported global search provider → hit | `/cat/items/:itemPk` | ✓ |
| download import sheet | API-CAT-048 | SCR-CAT-004 Download import sheet | `/cat/item-import` | ✓ |
| import items | API-CAT-049 | SCR-CAT-004 Import (after the given FILE upload) | `/cat/item-import` | ✓ |
| print barcode labels | API-CAT-050 | SCR-CAT-005 Print | `/cat/barcode-labels` | ✓ |
| generate the missing internal barcode of a label line | API-CAT-056 | SCR-CAT-005 Generate internal barcode (flagged line) | `/cat/barcode-labels` | ✓ |
| capability turn-off check | API-CAT-051 | SCR-CAT-006 Check | `/cat/settings?check=<flag>` | ✓ |
| active values of a CAT lookup key | API-CAT-052 | every `*-LOOKUP` hook: SCR-CAT-001 lookup fields, filters and columns; SCR-CAT-006 drawers | `/cat/items` · `/cat/items/:itemPk` · `/cat/settings` | ✓ |

Totals: 54 of 56 operations have a screen action and a route. API-CAT-011 and API-CAT-046 have neither, by decision (ADR-CAT-051).
<!-- PHASE:ALIGN-FE:END -->
