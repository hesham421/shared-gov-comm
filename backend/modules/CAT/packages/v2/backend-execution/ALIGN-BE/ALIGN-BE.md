<!-- source: PHASE:ALIGN-BE -->
<!-- traces: REQ-CAT-001, REQ-CAT-002, REQ-CAT-127 -->
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
| RULE-CAT-005 | API-CAT-001, API-CAT-006, API-CAT-015, API-CAT-049, API-CAT-056 | CAT-500-CODE-NOT-ISSUED |
| RULE-CAT-006 | API-CAT-001, API-CAT-003, API-CAT-005, API-CAT-008, API-CAT-010, API-CAT-014, API-CAT-015, API-CAT-032, API-CAT-033, API-CAT-035, API-CAT-036, API-CAT-056 | CAT-422-LOOKUP-VALUE-INACTIVE |
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
| RULE-CAT-029 | API-CAT-014, API-CAT-015, API-CAT-056 | CAT-409-BARCODE-DUPLICATE |
| RULE-CAT-030 | API-CAT-014 | CAT-422-GTIN-INVALID |
| RULE-CAT-031 | API-CAT-014, API-CAT-015, API-CAT-056 | CAT-422-BARCODE-UNIT-INVALID |
| RULE-CAT-032 | API-CAT-014, API-CAT-015, API-CAT-032, API-CAT-056 | CAT-409-BARCODE-SCALE-PREFIX |
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
| RULE-CAT-049 | API-CAT-001, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-023, API-CAT-024, API-CAT-049, API-CAT-053, API-CAT-056 | CAT-500-CHANGE-NOT-SAVED |
| RULE-CAT-050 | API-CAT-005 | CAT-422-ITEM-NO-ACTIVE-VARIANT |
| RULE-CAT-051 | API-CAT-013 | CAT-409-ITEM-UNIT-HAS-BARCODES |
| RULE-CAT-052 | API-CAT-006 | CAT-422-VARIANT-ATTRIBUTE-MISSING |
| RULE-CAT-053 | API-CAT-003, API-CAT-008, API-CAT-010, API-CAT-012, API-CAT-014, API-CAT-015, API-CAT-018, API-CAT-019, API-CAT-056 | CAT-422-PARENT-INACTIVE |

### Coverage — XM → requires → tests
none — the XM register is empty; no edge, no `requires`, no edge test.
<!-- PHASE:ALIGN-BE:END -->
