<!-- source: PHASE:ALIGN-BE -->
<!-- traces: DBF-INV-001, DBF-INV-219, REQ-INV-001, REQ-INV-138 -->
<!-- PHASE:ALIGN-BE:START traces=REQ-INV-001,REQ-INV-138,DBF-INV-001,DBF-INV-219 -->
## ALIGN-BE — alignment self-check

Each row's mark is the `gov.py analyze` result for its backing check, copied by the orchestrator from the
report; this stage does not adjudicate them.

```
ALIGN — INV v1
row               backing check        assertion                                                                 mark
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF   (report)
COVERED           orphans              every REQ is covered by ≥1 API or DBF                                      (report)
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares   (report)
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads                      (report)
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not (report)
QRC (§5)          orphans              every catalogued query is reached by ≥1 API                                (report)
API (R3)          code-format          every catalog code is an instance of the declared format with a platform status (report)
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-inv.yaml and every operation one block (report)
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation with its status and code     (report)
DOCUMENT VALID    api-spec-valid       api-spec-inv.yaml validates against OPENAPI 3.1.0 and reaches every required item (report)
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from    (report)
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered (report)
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase                         (report)
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's registry (report)
BOOTSTRAP         bootstrap-complete   every lookup key and every permission has a row naming who produces it     (report)
LOOKUPS (C6)      checklist C6         InvLookups serves exactly INV's six owned keys through OwnedLookups.read (API-INV-052) and no other key  (report)
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and permission (report)
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why not (report)
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/INV/        (report)
PATHS             paths-resolve        every path the generated manifest and execution state emit resolves        (report)
COVERAGE          (the report)         the clauses the analyze report lists as having examined nothing            (report)
```

```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C7.24
```

**Coverage — XM → requires → tests**

| XM | Type | requires | tests |
|---|---|---|---|
| XM-INV-001 … XM-INV-010 | HARD-FK (Variant columns) | target DELIVERED | AC ids of each block |
| XM-INV-011 | HARD-FK (Item column) | target DELIVERED | AC-INV-138, AC-INV-140, AC-INV-145 |
| XM-INV-012 | SOFT-READ (bundle components) | target DELIVERED | AC-INV-047, AC-INV-054, AC-INV-059, AC-INV-060 |
| XM-INV-013 | SOFT-READ (barcode) | target DELIVERED | AC-INV-079, AC-INV-097, AC-INV-101 |
| XM-INV-014 | SOFT-READ (Variant read, minted) | target DELIVERED | AC-INV-058, AC-INV-079, AC-INV-085, AC-INV-107 |
| XM-INV-015 | SOFT-READ (Item read, minted) | target DELIVERED | AC-INV-139, AC-INV-142, AC-INV-143, AC-INV-145 |

**Coverage — RULE → API → code**: the Error Catalog rows (§5) carry each RULE with its endpoints; the effect rules
RULE-INV-005, 014, 038 and the answers of RULE-INV-024, 046 raise no code (§1.3).

**Coverage — ENT → QR**: each entity block's REPOSITORY OPS line (DATA-DOM).
<!-- PHASE:ALIGN-BE:END -->
