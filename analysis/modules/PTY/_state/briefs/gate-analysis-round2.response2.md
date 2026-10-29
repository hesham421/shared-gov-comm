DECISION: Given-checklist findings G2–G10 confirmed against the checklist file
I read `.claude/skills/erp-core-usage@1.6.0/templates/checklist.md`. The lines quoted in G2 (C1, C5), G3 (C3, F2), G4 (C19, F11), G5 (C13), G6 (C8), G7 (C12), G8 (C18, C20), G9 (C4, C17) and G10 (F6) match it word for word. Each one is a MAJOR finding. The checklist's own header names the way out for a real core gap: an ADR named `CORE-GAP-<topic>.md`. That is the right route for G10 and for the anonymous-shopper part of G5.

DECISION: Two checklist violations missed in round 1 are added
- **G21 (C9):** document numbers must come from a `CORE_NUMBER_SERIES` row seeded in `V1000+`. The db-script's rule D-P2-07 writes no such row. ADR-PTY-029 registers the series at tenant provisioning instead.
- **G22 (C16):** the credit fields and the override decisions must be visible to a reviewer, but no entity is declared `@Audited`. Audit is hand-written through `AuditApi`.
- **C2 is folded into G7.** The plan never states that entities extend `AuditableEntity`.

DECISION: Round-1 scores, extra checks, analyze confirmations and ADR reviews are upheld
- complete, consistent and feasible stay at 1.
- CM-8 stays FAIL. The approved policy says "currency dimension exists in the model".
- The six C5.13/C5.14 findings are confirmed as false positives.
- C10.8 and C10.9 did not have empty input by nature; TC-PTY-018 and TC-PTY-020 have host data.
- The verdict stays REVISE. There is no CRITICAL finding and no BLOCKED ADR, and every MAJOR finding has a fix the analysis lane can apply.

```json
{
  "gate": "analysis",
  "module": "PTY",
  "version": 1,
  "scores": { "unambiguous": 2, "verifiable": 2, "complete": 1, "consistent": 1, "singular": 2, "feasible": 1, "traceable": 2 },
  "extra_checks": [
    {"id": "CM-1", "result": "PASS"}, {"id": "CM-2", "result": "PASS"}, {"id": "CM-3", "result": "PASS"},
    {"id": "CM-4", "result": "PASS"}, {"id": "CM-5", "result": "PASS"}, {"id": "CM-6", "result": "PASS"},
    {"id": "CM-7", "result": "PASS"}, {"id": "CM-8", "result": "FAIL"}, {"id": "CM-9", "result": "PASS"},
    {"id": "CM-10", "result": "PASS"}
  ],
  "analyze_confirmed": [
    {"finding": "C5.13 AC-PTY-123 (srs:1824)", "confirmed": false, "note": "False positive: 'could' appears in the quoted RULE-PTY-030 message, not in the AC's own wording."},
    {"finding": "C5.14 AC-PTY-011", "confirmed": false, "note": "False positive: the outcome can be counted (an identifier and a code are returned, and TC-PTY-061 asserts both)."},
    {"finding": "C5.14 AC-PTY-040", "confirmed": false, "note": "False positive: isDefaultFl true is a boolean that a test can check and fail."},
    {"finding": "C5.14 AC-PTY-076", "confirmed": false, "note": "False positive: NO_CREDIT belongs to the closed set CREDIT_CHECK_RESULT."},
    {"finding": "C5.14 AC-PTY-079", "confirmed": false, "note": "False positive: the absence of two named members can be checked (TC-PTY-031)."},
    {"finding": "C5.14 AC-PTY-083", "confirmed": false, "note": "False positive: NOT_APPLICABLE belongs to a closed set."},
    {"finding": "Examined nothing: C6.3, C6.10, C6.12, C6.15, C7.5, C7.5b, C7.24, C7.26, C7.27, C7.28", "confirmed": true, "note": "Empty by nature: the XM register is empty, so there are no edges, no consumed contracts and no patches."},
    {"finding": "Examined nothing: C10.8, C10.9", "confirmed": false, "note": "Not empty by nature: TC-PTY-018 and TC-PTY-020 carry host data (G19)."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "P3 seam: AC-PTY-013 / REQ-PTY-010 vs api-spec ErrorResponse",
     "problem": "AC-PTY-013 requires an action that opens the conflicting Party, but ErrorResponse has no structured field for that Party's code. The frontend's interim (ADR-PTY-034 §9) searches Customers by email, which fails when the Party is Supplier-only or is the walk-in Party.",
     "fix": "Return the conflicting Party's identifier and code (conflictingPartyId and conflictingPartyCode) with PTY-409-EMAIL-TAKEN from API-PTY-001, 003, 005, 006, 008 and 010, or add an email filter to API-PTY-028. Regenerate api-spec, drop the interim in F2/F4 of SCR-PTY-001 and SCR-PTY-002, and update TC-PTY-123.", "adr": true},
    {"id": "G2", "severity": "MAJOR", "section": "backend", "artifact": "db-script", "line": null, "clause": "given checklist C1: \"Every table has TENANT_ID BIGINT NOT NULL, FK_<TABLE>_TENANT → CORE_TENANT(ID), an index led by TENANT_ID, every UNIQUE composite with TENANT_ID\" (also C5 \"TENANT_ID = 1\")",
     "problem": "None of the five tables has TENANT_ID, a tenant FK or a tenant-led index. Every UNIQUE constraint and unique index works across all tenants. The BLOCK 8 lookup seed has no TENANT_ID = 1. ADR-PTY-021 leaves all of this to the given, which the checklist does not allow.",
     "fix": "Supersede ADR-PTY-021. Add TENANT_ID with its FK and a leading index to all five tables, make every UNIQUE and unique index (including partial and expression ones) composite with TENANT_ID, seed the lookups with TENANT_ID = 1, and align the DBF matrix and the QRC.", "adr": true},
    {"id": "G3", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "given checklist C3 / F2: \"Every native query … names TENANT_ID and its method carries @TenantUnscoped(reason, adr)\"",
     "problem": "The native queries QR-PTY-011 and QR-PTY-037 do not name TENANT_ID and carry no @TenantUnscoped. The other QR logical specs omit the tenant predicate as well.",
     "fix": "Add TENANT_ID = :tenantId to every QR logical spec. Annotate QR-PTY-011 and QR-PTY-037 with @TenantUnscoped(reason, adr = ADR-PTY-027).", "adr": false},
    {"id": "G4", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "given checklist F11 / C19: \"Business events another module consumes are OutboxPublisher.publish(\"<code>.<Name>\", payload) inside the writing transaction\"",
     "problem": "PartyRoleDeactivatedEvent (CON-PTY-021) is published to consuming modules through DomainEventPublisher, which F11 forbids.",
     "fix": "Publish with OutboxPublisher.publish(\"pty.PartyRoleDeactivated\", payload) inside the deactivation transaction. Update the CORE phase, the given-usage rows in SRS A9 and contract Part 4, and TC-PTY-072. State that subscribers must be idempotent on OutboxMessage.id().", "adr": true},
    {"id": "G5", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "given checklist C13: \"every service method has @PreAuthorize(\"hasAuthority(T(…<Module>Permissions).PERM_…)\") referencing its own module only\"",
     "problem": "@PreAuthorize sits only on the controllers, and ADR-PTY-025 exempts the in-process PtyPartyApi path from any PTY permission check.",
     "fix": "Put @PreAuthorize with PtyPermissions constants on every service method. Revise ADR-PTY-025 so the in-process caller's context must hold the authority. If the anonymous storefront shopper has no mechanism in the given, raise a CORE-GAP ADR.", "adr": true},
    {"id": "G6", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "given checklist C8: \"Every message is NotificationDispatchApi.dispatch(DispatchCommand) with a NOTIF_TEMPLATE code seeded in V1000+\"",
     "problem": "API-PTY-019 sends the approver notification with inline text and no NOTIF_TEMPLATE code.",
     "fix": "Seed the template PTY_CREDIT_OVERRIDE_REQUESTED (placeholders {docNo} and {name}) in V1000+, add it to the bootstrap block, and dispatch a DispatchCommand that uses it.", "adr": false},
    {"id": "G7", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "given checklist C12: \"Business rules live on the entity's Domain companion (create() / from()), the service orchestrates\" (and C2 \"Every entity extends AuditableEntity and declares no tenant field\")",
     "problem": "The business rules sit in services and validators, no Domain companion exists, and the plan does not state that entities extend AuditableEntity.",
     "fix": "Add a *Domain companion per entity and move the owner layer of RULE-PTY-001 … 033 to it. State that every entity extends com.erp.common.domain.AuditableEntity.", "adr": false},
    {"id": "G8", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "given checklist C18 / C20: \"The module ships META-INF/erp/modules/<code>.yaml\"; \"ModuleBoundaryRules… runs in the module's tests and the module has its own CI job\"",
     "problem": "The plan has no module manifest, no boundary-rules test and no CI job for the module.",
     "fix": "Add META-INF/erp/modules/pty.yaml (code, package, the five pages as permission modules, api-paths /api/v1/pty/**, no dependencies), the ModuleBoundaryRules test and a module CI job, each with a package acceptance.", "adr": false},
    {"id": "G9", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "given checklist C4 / C17: async work on erpCoreEventExecutor or TenantContext.runAs; \"@TransactionalEventListener(phase = AFTER_COMMIT, fallbackExecution = true) for every listener\"",
     "problem": "After-commit work runs through TransactionSynchronization.afterCommit and never re-binds the tenant. The invitation-acceptance handler declares no listener.",
     "fix": "Use @TransactionalEventListener(AFTER_COMMIT, fallbackExecution = true) with plain-value events, run that work on erpCoreEventExecutor or under TenantContext.runAs, and declare how the acceptance handler subscribes.", "adr": false},
    {"id": "G10", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "given checklist F6: \"A module counter for document numbers: … MAX( + 1\"",
     "problem": "QR-PTY-024 assigns the user-visible Address code as MAX + 1.",
     "fix": "Record an ADR (CORE-GAP-ordinal-within-parent) stating that an ordinal inside one Party is not a document number, with the Party lock as the guard, or draw the code from NumberSeriesApi.", "adr": true},
    {"id": "G11", "severity": "MAJOR", "section": "backend", "artifact": "db-script", "line": null, "clause": "CM-8 / upstream wins (GOVERNANCE-CORE §1)",
     "problem": "ADR-PTY-020 removes the currency column, but the approved business policies state that 'the currency dimension exists in the model, hidden while its count is 1'.",
     "fix": "Add CURRENCY_CODE (NOT NULL, defaulting to the tenant base currency) to PTY_CUSTOMER and PTY_CREDIT_OVERRIDE_REQUEST, and carry it through the DBF matrix, the API schemas and the credit check. The UI keeps it hidden while there is one currency. Supersede ADR-PTY-020.", "adr": true},
    {"id": "G12", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null, "clause": "P1/P4 pattern: inactive Customer or Party on writes and credit operations",
     "problem": "No statement covers these cases: a credit check, request creation or approval use for an inactive Customer; address writes on an inactive Party; PENDING or APPROVED requests that outlive their Customer's deactivation or the flag being switched off. The same gap appears in four or more endpoints.",
     "fix": "Add one EARS unwanted-behaviour REQ and extend RULE-PTY-005 to cover credit check and override create/use. State that credit clear, history and reads stay allowed. Define what happens to pending requests, then add catalog codes, api-spec responses and TCs.", "adr": true},
    {"id": "G13", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null, "clause": "P2 closure: duplicate or cross-document override requests",
     "problem": "Two PENDING requests can exist for the same (customerFk, sourceDocumentRef). Use does not compare usedDocumentRef with sourceDocumentRef.",
     "fix": "Add a partial unique index plus a 409 code for one open request per (customerFk, sourceDocumentRef), and decide in an ADR whether use must match the source sale.", "adr": true},
    {"id": "G14", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "P2 closure: address permission for a Party holding both roles",
     "problem": "The plan does not say whether one UPDATE permission suffices when the Party holds both roles.",
     "fix": "State that either matching UPDATE permission suffices, and add a TC.", "adr": false},
    {"id": "G15", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null, "clause": "P1 silent path: audit failure on override decisions",
     "problem": "Nothing states what happens when the audit write of an approve or reject decision (REQ-PTY-107) fails.",
     "fix": "Extend RULE-PTY-030 so the decision rolls back and is refused, and add an AC and a TC.", "adr": false},
    {"id": "G16", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null, "clause": "P1: stated concurrency guards (ADR-PTY-026) without test cases",
     "problem": "None of the guards has a parallel test: approve vs approve or cancel, double use of one approval, the walk-in race, the email race, the Party-lock serialisation, or audit chaining of credit edits.",
     "fix": "Add one barrier-synchronised TC per guard that asserts exactly one success and the loser's catalog code.", "adr": false},
    {"id": "G17", "severity": "MAJOR", "section": "tests", "artifact": "frontend-test-plan", "line": null, "clause": "P1: frontend statements without test cases",
     "problem": "Several frontend behaviours have no TC: the ordered save and partial failure, commands disabled while the form is dirty and the leave prompt, the flag switched off mid-session, the empty, error and not-found states, the flag guard on SCR-003/004/005, and the Warehouse read-only view.",
     "fix": "Add TCs under the matching F2, F4 and SEC-FE packages.", "adr": false},
    {"id": "G18", "severity": "MINOR", "section": "tests", "artifact": "frontend-test-plan", "line": null, "clause": "P3 seam: TC-PTY-138 cannot reach its precondition on the delivered backend (F-N4)",
     "problem": "Seller/Cashier S2 cannot load another user's request on the real backend, so the case passes only against the mock.",
     "fix": "Make the actor an Owner holding VIEW, UPDATE and DELETE who cancels S1's request, and drop F-N4.", "adr": false},
    {"id": "G19", "severity": "MINOR", "section": "tests", "artifact": "backend-test-plan", "line": null, "clause": "C10.8 / C10.9 examined nothing; N4",
     "problem": "TC-PTY-020 depends on an add-value call whose verb and path are unknown, so the executor cannot run it as written.",
     "fix": "Seed VIP as inactive in the test fixture, or deactivate a seeded value instead. Find out why C10.8 and C10.9 found no subjects.", "adr": false},
    {"id": "G20", "severity": "MINOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null, "clause": "ADR status integrity",
     "problem": "ADR-PTY-034 is ACCEPTED while its §9 is still PENDING.",
     "fix": "Once G1 is fixed, remove §9. Until then, split §9 into an open ADR owned by the backend plan.", "adr": false},
    {"id": "G21", "severity": "MAJOR", "section": "backend", "artifact": "db-script", "line": null, "clause": "given checklist C9: \"Every document number is NumberSeriesApi.next(code) from a CORE_NUMBER_SERIES row seeded in V1000+, stored in a column unique with TENANT_ID\"",
     "problem": "D-P2-07 seeds no CORE_NUMBER_SERIES rows, and ADR-PTY-029 registers PTY_PARTY and PTY_CREDIT_OVERRIDE at provisioning instead. CODE and DOC_NO are not unique together with TENANT_ID.",
     "fix": "Seed both series in the V1000+ migration, make UQ_PTY_PARTY_CODE and UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO (TENANT_ID, …), and revise ADR-PTY-029.", "adr": true},
    {"id": "G22", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "given checklist C16: \"Entities whose changes a reviewer must see carry @Audited(entityType = \"<TABLE>\", ignore = {…})\"",
     "problem": "Credit-limit, payment-term and override-decision changes must be reviewable (REQ-PTY-106, REQ-PTY-107), but no entity is @Audited. The audit is written by hand through AuditApi.",
     "fix": "Declare @Audited on PtyCustomerEntity (for the credit fields; ignore the others) and on PtyCreditOverrideRequestEntity. Keep AuditApi only where C16 does not cover the need, and keep RULE-PTY-030 rollback semantics.", "adr": false}
  ],
  "adrs_reviewed": [
    {"id": "ADR-PTY-001…013", "status_ok": true, "note": "Dialogue decisions; status and traces are correct."},
    {"id": "ADR-PTY-014…017", "status_ok": true, "note": "Applied downstream."},
    {"id": "ADR-PTY-018", "status_ok": true, "note": "Filed verbatim by ADR-PTY-030."},
    {"id": "ADR-PTY-019", "status_ok": true, "note": "Consistent with how createdBy is used."},
    {"id": "ADR-PTY-020", "status_ok": false, "note": "Contradicts the approved scope exception and CM-8 (G11); supersede it."},
    {"id": "ADR-PTY-021", "status_ok": false, "note": "Contradicts given checklist C1 (G2); supersede it."},
    {"id": "ADR-PTY-022", "status_ok": false, "note": "Column list not checked against the given DDL, and no TENANT_ID = 1 (G2)."},
    {"id": "ADR-PTY-023", "status_ok": true, "note": "Third bullet is superseded by ADR-PTY-030; mark it so."},
    {"id": "ADR-PTY-024", "status_ok": true, "note": "Correct."},
    {"id": "ADR-PTY-025", "status_ok": false, "note": "The in-process no-check paragraph conflicts with C13 (G5)."},
    {"id": "ADR-PTY-026", "status_ok": true, "note": "Sound, but untested (G16)."},
    {"id": "ADR-PTY-027", "status_ok": true, "note": "Valid; it must be cited as the adr of @TenantUnscoped (G3)."},
    {"id": "ADR-PTY-028", "status_ok": true, "note": "Correct."},
    {"id": "ADR-PTY-029", "status_ok": false, "note": "Provisioning-time series registration conflicts with C9 (G21), and the notification template is missing (G6)."},
    {"id": "ADR-PTY-030…032", "status_ok": true, "note": "Correct."},
    {"id": "ADR-PTY-033", "status_ok": true, "note": "Reconciles B3 with AC-PTY-082 without breaking either."},
    {"id": "ADR-PTY-034", "status_ok": false, "note": "ACCEPTED with §9 PENDING (G1, G20)."},
    {"id": "Platform findings", "status_ok": false, "note": "The registry category was not supplied and could not be read. No BLOCKED ADR. ADR-PTY-021 and ADR-PTY-022 ('the given's documented integration', the unchecked mdl column list) are module-scoped workers saying 'not mine to settle'. These, and the G5 anonymous-shopper gap, should be recorded as platform findings owned by erp-core (CORE-GAP) if the library does not provide them."}
  ],
  "verdict": "REVISE"
}
```

<!-- CONVERGED -->