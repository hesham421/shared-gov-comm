Reviewed the whole PTY v1 analysis against the given `erp-core` 1.6.0 checklist. Verdict is REVISE, with three MAJOR findings (G1–G3) and two MINOR. G1 is a checklist violation, so it forces at least REVISE.

```json
{
  "gate": "analysis",
  "module": "PTY",
  "version": 1,
  "scores": { "unambiguous": 2, "verifiable": 2, "complete": 2, "consistent": 2, "singular": 3, "feasible": 2, "traceable": 3 },
  "extra_checks": [
    {"id": "CM-1", "result": "PASS"}, {"id": "CM-2", "result": "PASS"}, {"id": "CM-3", "result": "PASS"},
    {"id": "CM-4", "result": "PASS"}, {"id": "CM-5", "result": "PASS"}, {"id": "CM-6", "result": "PASS"},
    {"id": "CM-7", "result": "PASS"}, {"id": "CM-8", "result": "PASS"}, {"id": "CM-9", "result": "PASS"},
    {"id": "CM-10", "result": "PASS"}
  ],
  "analyze_confirmed": [
    {"finding": "C5.13 AC-PTY-123", "confirmed": false, "note": "False positive. 'could' is inside the quoted fixed RULE-PTY-030 message («could not be recorded»), not in the criterion's own wording."},
    {"finding": "C5.14 AC-PTY-011", "confirmed": true, "note": "Minor. The Then names an enumerable outcome (identifier and code returned), but it is weakly worded. Acceptable; TC-PTY-061 and TC-PTY-147 assert it."},
    {"finding": "C5.14 AC-PTY-040", "confirmed": false, "note": "False positive. isDefaultFl = true is an exact, failable value, asserted by TC-PTY-015."},
    {"finding": "C5.14 AC-PTY-076", "confirmed": false, "note": "False positive. NO_CREDIT is a closed-lookup code, asserted by TC-PTY-099."},
    {"finding": "C5.14 AC-PTY-079", "confirmed": false, "note": "False positive. The absence of two named members is checkable, asserted by TC-PTY-031."},
    {"finding": "C5.14 AC-PTY-083", "confirmed": false, "note": "False positive. NOT_APPLICABLE is a closed-lookup code, asserted by TC-PTY-035."},
    {"finding": "coverage: C6.10 C6.12 C6.15 C6.3 C7.5 C7.5b C7.26 C7.27 C7.28", "confirmed": true, "note": "Empty by nature. SRS A8 has consumes: [] and the XM register is empty, so these XM-edge clauses have no subject."},
    {"finding": "coverage: C7.24 contract-honoured", "confirmed": true, "note": "Empty by nature: it applies to a consumed contract, and PTY consumes none. Honours lines are covered by C13.5 (24 examined)."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "given checklist C11 / conventions §Error codes rule 3 ('never registers a second @ControllerAdvice for the shared codes; GlobalExceptionHandler owns VALIDATION_ERROR, ACCESS_DENIED, NOT_FOUND …')",
     "problem": "The Error Catalog defines PTY-400-VALIDATION, PTY-401-UNAUTHENTICATED, PTY-403-FORBIDDEN and PTY-500-INTERNAL as PTY codes. CORE and SEC-BE say an @PreAuthorize refusal 'maps through LocalizedException to PTY-403-FORBIDDEN'. The given handler answers these cases with its own shared codes (ACCESS_DENIED, VALIDATION_ERROR and so on). The module cannot make the PTY-prefixed codes appear without a second advice or catching the exception, both of which the checklist forbids. The api-spec ErrorResponse pattern '^PTY-', frontend STD routing and tests such as TC-PTY-113, 177 and 196 assert codes the platform will not emit.",
     "fix": "Keep as PTY catalog rows only the codes a PTY Domain or service throws: the rule rows, PTY-404-*-NOT-FOUND and PTY-404-LOOKUP-KEY-UNKNOWN. For 400, 401, 403 and 500, state that the given GlobalExceptionHandler answers with its own codes. Relax the ErrorResponse code pattern to allow them. Re-key the frontend STD routing and the affected TCs on those shared codes. Amend ADR-PTY-024 accordingly.",
     "adr": true},
    {"id": "G2", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "§7 platform findings / complete / verifiable",
     "problem": "REQ-PTY-076 says 'the system shall refuse the switch-off', and AC-PTY-085 asserts 'b2bCredit stays on'. PTY only answers the dependency question (API-PTY-037, TC-PTY-036). The module that enforces the refusal, and that must call API-PTY-037, is the capability-flag owner, which is still OPEN (OI-05). business-policies PLATFORM FINDINGS records nothing for it, so the AC cannot be met or tested inside PTY.",
     "fix": "Add an OPEN platform-findings row. Owner: the capability-flag owner (registry OI-05) or erp-core cu. Obligation: call API-PTY-037 or PtyPartyApi.b2bCreditSwitchOffDependency before turning b2bCredit off, and refuse with the RULE-PTY-024 message. Evidence: CON-PTY-019, AC-PTY-085. Reword the Then of AC-PTY-085 to PTY's observable result (dependentCustomerCount 4, switchOffAllowed false, the message and next action returned).",
     "adr": false},
    {"id": "G3", "severity": "MAJOR", "section": "backend", "artifact": "api-spec", "line": null,
     "clause": "complete (P1 counter-case) / feasible",
     "problem": "creditLimit, saleAmount, openExposure and the contributors' amounts are typed `number` with no maximum or scale. The columns are NUMERIC(18,3). ADR-PTY-018 says only that the backend rounds. A value of 10^15 or more, or one with more than 3 decimals, has no stated outcome. Overflow would surface as an undefined 500 or silent rounding of a credit value. No test case covers it.",
     "fix": "Add maximum 999999999999999.999 and scale at most 3 to creditLimit, saleAmount and the request schemas. State in API-PTY-017, 019, 035 and 036 that a larger value or a scale above 3 is refused with PTY-400-VALIDATION. Add one BOUNDARY TC per input kind, with a limit-value case and a one-above case.",
     "adr": false},
    {"id": "G4", "severity": "MINOR", "section": "backend", "artifact": "business-policies", "line": null,
     "clause": "§7 platform findings",
     "problem": "The three OPEN CORE-GAP files (in-process caller authority, tenant base currency, ordinal within parent) are questions to erp-core. They are not rows in the PLATFORM FINDINGS table, which lists only the namespace rule and OwnedLookups. The owner and evidence are in the ADR files but not in the findings register.",
     "fix": "Add three OPEN rows to PLATFORM FINDINGS. Owner: erp-core. Evidence: the CORE-GAP file and the ADR that depends on it.",
     "adr": false},
    {"id": "G5", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "complete (P2 closure: who may not)",
     "problem": "A user holding both PERM_PTY_CREDIT_OVERRIDE_CREATE and _UPDATE (the Owner preset) can approve a request they created. No rule or ADR states whether that is allowed, and RULE-PTY-029 restricts only cancel.",
     "fix": "Record the best-practice choice in an ADR, and state it as a rule with a catalog row and a TC if segregation is chosen. Otherwise state explicitly that self-approval by an Owner is permitted in v1.",
     "adr": true}
  ],
  "adrs_reviewed": [
    {"id": "ADR-PTY-024", "status_ok": false, "note": "Needs amending per G1: the platform codes for 400, 401, 403 and 500 belong to the given handler."},
    {"id": "ADR-PTY-039", "status_ok": true, "note": "ACCEPTED, follows checklist C13. The open CORE-GAP is correctly tracked, but see G4."},
    {"id": "ADR-PTY-045", "status_ok": true, "note": "Closes the WARN path. RULE-PTY-035, AC-PTY-137 and TC-PTY-191 are consistent."},
    {"id": "ADR-PTY-046", "status_ok": true, "note": "AC-PTY-138 and TC-PTY-196 are consistent, except for the 403 code, which falls under G1."},
    {"id": "ADR-PTY-047", "status_ok": true, "note": "Namespace com.commerce respects F8."},
    {"id": "ADR-PTY-048", "status_ok": true, "note": "OwnedLookups for API-PTY-038 satisfies C6. Platform finding row 2 is OPEN and legitimate."},
    {"id": "ADR-PTY-035", "status_ok": true, "note": "Satisfies C1 and C2."},
    {"id": "ADR-PTY-040", "status_ok": true, "note": "The F6 question is raised as a CORE-GAP and guarded by the Party lock."},
    {"id": "ADR-PTY-020", "status_ok": true, "note": "SUPERSEDED by ADR-PTY-037. Correct."},
    {"id": "ADR-PTY-021", "status_ok": true, "note": "SUPERSEDED by ADR-PTY-035. Correct."},
    {"id": "ADR-PTY-023", "status_ok": true, "note": "Its third bullet is superseded by ADR-PTY-030 for ADR-PTY-018 to 022. No downstream artifact ignores it."},
    {"id": "CORE-GAP-in-process-caller-authority", "status_ok": true, "note": "OPEN, not BLOCKED, so it does not force ESCALATE. No v1 caller is blocked."},
    {"id": "CORE-GAP-tenant-base-currency", "status_ok": true, "note": "OPEN, not BLOCKED."},
    {"id": "CORE-GAP-ordinal-within-parent", "status_ok": true, "note": "OPEN, not BLOCKED."}
  ],
  "verdict": "REVISE"
}
```