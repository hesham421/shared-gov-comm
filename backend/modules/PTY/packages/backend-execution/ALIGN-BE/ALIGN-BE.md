<!-- source: PHASE:ALIGN-BE -->
<!-- traces: REQ-PTY-001, REQ-PTY-024, REQ-PTY-109 -->
<!-- PHASE:ALIGN-BE:START traces=REQ-PTY-001,REQ-PTY-024,REQ-PTY-109 -->
## PHASE 6 — ALIGN-BE

The prose half of the alignment check. Each mark is the `gov.py analyze` result for the named check, copied by the orchestrator; the author does not adjudicate a row the machine decides.

```
ALIGN — PTY v1
row               backing check        assertion                                                                                     mark
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF        (from the analyze report)
COVERED           orphans              every REQ is covered by ≥1 API or DBF                                                          (from the analyze report)
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares for it                (from the analyze report)
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads                                         (from the analyze report)
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not                    (from the analyze report)
QRC (§5)          orphans              every catalogued query is reached by ≥1 API                                                   (from the analyze report)
API (R3)          code-format          every catalog code is an instance of the declared format and carries a status the platform can emit (from the analyze report)
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-pty.yaml and every operation one block           (from the analyze report)
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation of api-spec-pty.yaml with its status and code   (from the analyze report)
DOCUMENT VALID    api-spec-valid       api-spec-pty.yaml validates against OPENAPI 3.1.0 and reaches every required item             (from the analyze report)
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from, or is deferred        (from the analyze report)
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered               (from the analyze report)
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase, and nothing else names its target         (from the analyze report)
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's own registry               (from the analyze report)
BOOTSTRAP         bootstrap-complete   every lookup key and every permission has a row naming who produces it                        (from the analyze report)
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission (from the analyze report)
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why it is not      (from the analyze report)
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/PTY/                           (from the analyze report)
PATHS             paths-resolve        every path the generated manifest and execution state emit resolves to something that exists  (from the analyze report)
COVERAGE          (the report)         the clauses the analyze report lists as having examined nothing — verbatim, or `none`         (from the analyze report)
```

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

**Coverage — ENT / DBF → phases → QR → XM**

| ENT | DBF | Phases | QR | XM |
|---|---|---|---|---|
| ENT-PTY-001 | DBF-PTY-001 … 012, 068, 073 | DATA-DOM-MASTER, SVC-API-CRUD / SEARCH / INT | QR-PTY-001, 002, 003, 004, 021, 036, 037, 038 | — |
| ENT-PTY-002 | DBF-PTY-013 … 024, 069, 074, 078 | DATA-DOM-MASTER, SVC-API-CRUD / SEARCH / INT | QR-PTY-005 … 014 | — |
| ENT-PTY-003 | DBF-PTY-025 … 032, 070, 075 | DATA-DOM-MASTER, SVC-API-CRUD / SEARCH | QR-PTY-015 … 020 | — |
| ENT-PTY-004 | DBF-PTY-033 … 049, 071, 076 | DATA-DOM-MASTER, SVC-API-CRUD / SEARCH / INT | QR-PTY-022 … 030 | — |
| ENT-PTY-005 | DBF-PTY-050 … 067, 072, 077, 079 | DATA-DOM-TRANSACTIONAL, SVC-API-CRUD / SEARCH / INT | QR-PTY-031 … 035, 039, 040 | — |

**Coverage — RULE → API → catalog code**

| RULE | API | Code |
|---|---|---|
| RULE-PTY-001 | 001, 003, 006, 008, 034 | PTY-422-NAME-REQUIRED |
| RULE-PTY-002 | 001, 003, 005, 006, 008, 010 | PTY-409-EMAIL-TAKEN |
| RULE-PTY-003 | 001, 003, 006, 008, 034 | PTY-422-EMAIL-FORMAT |
| RULE-PTY-004 | 001, 003, 006, 008, 034 | PTY-422-MOBILE-FORMAT |
| RULE-PTY-005 | 002, 007, 011, 013, 015, 017, 019, 035, 036 | PTY-422-ROLE-INACTIVE |
| RULE-PTY-006 | 004, 009, 014 | confirmation message (no error) |
| RULE-PTY-007 | 003, 008 | PTY-422-CODE-READ-ONLY |
| RULE-PTY-008 | 006, 008, 017 | PTY-422-PAYMENT-TERMS-RANGE |
| RULE-PTY-009 | 001, 006 | PTY-409-ROLE-HELD |
| RULE-PTY-010 | 004 | PTY-422-WALK-IN-DEACTIVATE |
| RULE-PTY-011 | 017 | PTY-422-WALK-IN-CREDIT |
| RULE-PTY-012 | 006 | PTY-422-WALK-IN-SUPPLIER |
| RULE-PTY-013 | 034 (matching filter), invitation subscriber | subscriber outcome REFUSED, recorded through AuditApi (no error code) |
| RULE-PTY-014 | 033 | none — idempotent ensure, UQ_PTY_CUSTOMER_IS_WALK_IN_FL |
| RULE-PTY-015 | 011, 013, 034 | PTY-422-ADDRESS-LINE-REQUIRED |
| RULE-PTY-016 | 011, 013 | PTY-422-ADDRESS-TYPE-INVALID |
| RULE-PTY-017 | 011, 013, 014, 015 | confirmation message (no error) |
| RULE-PTY-018 | 001, 003 | PTY-422-CUSTOMER-GROUP-INVALID |
| RULE-PTY-019 | 034 | PTY-422-GUEST-CONTACT-REQUIRED |
| RULE-PTY-020 | invitation subscriber | subscriber outcome REFUSED, recorded through AuditApi (no error code) |
| RULE-PTY-021 | 034 | PTY-422-GUEST-NOT-COMPLETED |
| RULE-PTY-022 | 017 | PTY-422-CREDIT-LIMIT-NEGATIVE |
| RULE-PTY-023 | 016, 017, 018, 019, 021, 022, 030, 031, 036 | PTY-422-CREDIT-NOT-ENABLED |
| RULE-PTY-024 | 037 | answer message (no error) |
| RULE-PTY-025 | 019 | PTY-422-OVERRIDE-NOT-NEEDED |
| RULE-PTY-026 | 021, 022, 023 | PTY-409-REQUEST-NOT-PENDING |
| RULE-PTY-027 | 022 | PTY-422-REJECTION-REASON-REQUIRED |
| RULE-PTY-028 | 036 | PTY-422-APPROVAL-NOT-COVERING |
| RULE-PTY-029 | 023 | PTY-403-NOT-REQUESTER |
| RULE-PTY-030 | 017, 018, 021, 022 | PTY-500-AUDIT-NOT-RECORDED |
| RULE-PTY-031 | 021, 022, 036 | PTY-422-DOC-NO-READ-ONLY |
| RULE-PTY-032 | 019 | PTY-422-SALE-AMOUNT-NOT-POSITIVE |
| RULE-PTY-033 | 019 | PTY-422-WALK-IN-NO-CREDIT-SALE |
| RULE-PTY-034 | 019 | PTY-409-REQUEST-OPEN-FOR-SALE |
| RULE-PTY-035 | 019 | PTY-422-OVERRIDE-NOT-REQUIRED |

**Coverage — XM → requires → tests**: none (0 edges).
<!-- PHASE:ALIGN-BE:END -->
