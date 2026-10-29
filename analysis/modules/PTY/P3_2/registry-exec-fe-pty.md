## REGISTRY — P3.2 — PTY v1

Source artifacts : analysis/modules/PTY/P3_2/flow-diagram-pty.md · ui-ux-spec-pty.md · frontend-execution-plan-pty.md · API document : api-spec-pty.yaml (P3.1) · Date : 2026-10-29 · Profile : commerce · Track : frontend · Plan : exec

### ID ranges
| Kind | Range | Count | Next free seq |
|---|---|---|---|
| SCR | SCR-PTY-001 … SCR-PTY-005 | 5 | 006 |
| UXD | UXD-PTY-001 … UXD-PTY-001 | 1 | 002 |

SCR ids : SCR-PTY-001, SCR-PTY-002, SCR-PTY-003, SCR-PTY-004, SCR-PTY-005

UXD ids : UXD-PTY-001

### Screens
| SCR | Name (en / ar) | SRS screen | Owning ENT | Container · composition | Permissions | Flag |
|---|---|---|---|---|---|---|
| SCR-PTY-001 | Customers / العملاء | SCR-REQ-PTY-001 | ENT-PTY-002 (with ENT-PTY-001, ENT-PTY-004) | page · Addresses inline · Quick register none · one save | PERM_PTY_CUSTOMER_VIEW, _CREATE, _UPDATE, _DELETE (+ PERM_PTY_SUPPLIER_CREATE for Make supplier) | — |
| SCR-PTY-002 | Suppliers / الموردون | SCR-REQ-PTY-002 | ENT-PTY-003 (with ENT-PTY-001, ENT-PTY-004) | page · Addresses inline · Quick register none · one save | PERM_PTY_SUPPLIER_VIEW, _CREATE, _UPDATE, _DELETE (+ PERM_PTY_CUSTOMER_CREATE for Make customer) | — |
| SCR-PTY-003 | Customer credit / ائتمان العملاء | SCR-REQ-PTY-003 | ENT-PTY-002 (with ENT-PTY-001) | page · Credit history inline · one save | PERM_PTY_CUSTOMER_CREDIT_VIEW, _UPDATE | `b2bCredit` |
| SCR-PTY-004 | Credit override requests / طلبات تجاوز حد الائتمان | SCR-REQ-PTY-004 | ENT-PTY-005 (with ENT-PTY-002) | page · Decision panel inline · one submit per sub-view | PERM_PTY_CREDIT_OVERRIDE_VIEW, _CREATE, _UPDATE, _DELETE | `b2bCredit` |
| SCR-PTY-005 | Credit control settings / إعدادات ضبط الائتمان | SCR-REQ-PTY-005 | ENT-PTY-002 (setting in given `cu`) | page · none · one save | PERM_PTY_CREDIT_SETTINGS_VIEW, _UPDATE | `b2bCredit` |

### UXD index
| UXD | Screen | Field | Owner module | Read used | Traces | Cited by |
|---|---|---|---|---|---|---|
| UXD-PTY-001 | SCR-PTY-004 | decidedBy — Decided by / صاحب القرار (user name) | erp-core `modules.sec` (given) | the given `sec` user read (USER-LABEL-LOOKUP, kind lookup; not an operation of api-spec-pty.yaml — ADR-PTY-034) | REQ-PTY-094, REQ-PTY-103, AC-PTY-106, AC-PTY-117 | F1-SCR-PTY-004, F2-SCR-PTY-004, F3-SCR-PTY-004, F4-SCR-PTY-004 |

### API coverage
Documented operations : 37 (API-PTY-001 … API-PTY-037).
Used by a PTY screen : 31 — API-PTY-001 … 011, 013 … 032.
Unused by this plan : 6 — API-PTY-012 (read Address — Sales & POS, Purchasing, Storefront), API-PTY-033 (walk-in Customer — Sales & POS), API-PTY-034 (guest Customer resolution — Storefront), API-PTY-035 (credit check — Sales & POS, Storefront), API-PTY-036 (use Credit override request — Sales & POS, Storefront), API-PTY-037 (`b2bCredit` switch-off dependency — the capability-flag owner, OI-05) · ADR-PTY-034.
Unmapped (REQ without operation / operation without REQ) : none. REQ-PTY-052 and REQ-PTY-054 are served in-process (ADR-PTY-025) with no frontend surface.
Mock : api-spec-pty.yaml · catalog codes routed : 36 (27 rule-bound with their RULE text, 9 PLATFORM-STD by the standard routing).
Reads outside the document (kinds lookup / init, ADR-PTY-034) : given `mdl` lookups (CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT, CREDIT_OVERRIDE_STATUS) · given application configuration (`b2bCredit`) · given `sec` session permissions · given `sec` user read (UXD-PTY-001).

### ALIGN
Verdict : as stamped by the orchestrator from `gov.py analyze` (the plan's `self-check` block is a placeholder) · findings fixed : none yet (round 1).

### ADRs
| ADR | Title | Status |
|---|---|---|
| decisions/PTY/ADR-PTY-033 | Credit values on the Customer screen — read-only, edited on Customer credit | ACCEPTED (non-breaking) |
| decisions/PTY/ADR-PTY-034 | Frontend binding decisions — given surfaces, consumer-only operations, in-place registration, one save with ordered calls, commands, cache deviations, pending party code | ACCEPTED (non-breaking) |

Cited, not raised here : ADR-PTY-012, ADR-PTY-024, ADR-PTY-025, ADR-PTY-031. BLOCKED : none. Next free ADR sequence : 035.

### Traceability
REQ covered by ≥ 1 SCR/F-block : 109 / 109 — 77 by the screen SUBs of F1 … F4 (SCR-PTY-001 … 005); 32 by the ALIGN-FE operations coverage only, because the SRS gives them no screen: REQ-PTY-002, 003, 019, 042 … 046, 050 … 063, 074 … 076, 079, 081 … 084, 097, 099 (operations for consuming modules, SRS A8.1 — bound in the API binding), of which REQ-PTY-052 and REQ-PTY-054 have no operation (in-process).
Orphan REQ : none.
SCR referenced by a plan block : 5 / 5 · UXD cited by a plan block : 1 / 1.

### Event
"P3.2 completed: PTY v1 — SCR 5 · UXD 1 · flows 20 · API used 31/37 (6 consumer-only) · ADR 2 (033–034), 0 BLOCKED"
