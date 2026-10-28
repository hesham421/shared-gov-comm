```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": { "unambiguous": 3, "verifiable": 3, "complete": 2, "consistent": 3, "singular": 3, "feasible": 3, "traceable": 3 },
  "extra_checks": [
    {"id": "CM-1", "result": "PASS"},
    {"id": "CM-2", "result": "PASS"},
    {"id": "CM-3", "result": "PASS"},
    {"id": "CM-4", "result": "PASS"},
    {"id": "CM-5", "result": "PASS"},
    {"id": "CM-6", "result": "PASS"},
    {"id": "CM-7", "result": "PASS"},
    {"id": "CM-8", "result": "PASS"},
    {"id": "CM-9", "result": "PASS"},
    {"id": "CM-10", "result": "PASS"}
  ],
  "analyze_confirmed": [
    {"finding": "C5.13 AC-CAT-013 'could'", "confirmed": false, "note": "False positive. 'could' is inside the quoted RULE-CAT-009 message («could not be checked»), not in the Then clause measure."},
    {"finding": "C5.13 AC-CAT-036 'could'", "confirmed": false, "note": "False positive. It sits inside the quoted RULE-CAT-005 message («could not be issued»)."},
    {"finding": "C5.13 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive. It sits inside the quoted RULE-CAT-024 message («use a different code or name»). The Then clause is exact: nothing is created, and the message shown is the one quoted."},
    {"finding": "C5.13 AC-CAT-102 'could'", "confirmed": false, "note": "False positive. It sits inside the quoted RULE-CAT-035 message («could not be read»)."}
  ],
  "findings": [
    {"id": "G1", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "complete (P2 closure: who may create a record, and what it depends on)",
     "problem": "RULE-CAT-038 and API-CAT-018 check only the type, size and availability of fileDocumentRef through FileDocumentLookupApi. Nothing requires the FILE document to be in public image storage. An image uploaded to private storage is accepted, then cannot be shown by the storefront (UXD-CAT-003 and CON-CAT-015 assume a public file). The same ref can also be attached to several media rows. Neither case has a statement or a test case.",
     "fix": "Add to RULE-CAT-038 and to the API-CAT-018 Validations: the FILE document must be in the public image storage of the caller's tenant, otherwise CAT-422-IMAGE-NOT-ACCEPTED. Add one TC to the backend plan for an image uploaded to private storage (SVC-API-CRUD). State that attaching one ref to two media rows is allowed, since removal never deletes the file (ADR-CAT-056 §4).",
     "adr": false},
    {"id": "G2", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "concurrency (engine template: a named guard for every decision that is then written)",
     "problem": "API-CAT-029 (create Variant attribute value) loads the attribute with QR-CAT-022 and takes no lock, while API-CAT-028 (deactivate attribute) locks it FOR UPDATE. A create that read the attribute as active can commit an active value under an attribute that is deactivated meanwhile. API-CAT-055 already reads the attribute FOR SHARE for the same reason, and the Concurrency line of API-CAT-029 names only the unique indexes. The Category pair was fixed in round 3 (ADR-CAT-047); this pair was not.",
     "fix": "In API-CAT-029, read the attribute FOR SHARE (QR-CAT-022 locking: FOR SHARE on value create as well as on value reactivation). Add this to the Concurrency line and to QR-CAT-022. The race has no TC of its own; it can be covered by an extra parallel-request case in a later delta.",
     "adr": false},
    {"id": "G3", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "complete (P1 inversion: empty and edge input)",
     "problem": "The plan does not say what API-CAT-006 does with an empty `selections` array, or with a selection that has an empty `variantAttributeValueFks`. A generation can only raise RULE-CAT-052 for attributes already in use, so on an Item with no attributes an empty request reaches no rule. TC-CAT-184 covers only a request that omits a used attribute.",
     "fix": "Add to the API-CAT-006 Validations: an empty `selections`, or an attribute with no value, answers CAT-400-REQUEST-INVALID, with `minItems: 1` in the schema for both arrays. Add the empty-selection case to TC-CAT-184 as a second row.",
     "adr": false}
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-044", "status_ok": true, "note": "The six PLATFORM-STD rows have ar and en texts; the 413 and 415 rows are gone with the multipart surface."},
    {"id": "ADR-CAT-047", "status_ok": true, "note": "The tenant tree lock now covers Category create, move, deactivation and reactivation, and the native queries are annotated @TenantUnscoped. See G2 for the attribute-value pair, which is not yet locked."},
    {"id": "ADR-CAT-054", "status_ok": true, "note": "SUPERSEDED by ADR-CAT-055. Correct: checklist C1 requires FK_<TABLE>_TENANT."},
    {"id": "ADR-CAT-056", "status_ok": true, "note": "Two-step upload with fileDocumentRef. Storage-category check is missing (G1)."},
    {"id": "ADR-CAT-059", "status_ok": true, "note": "ACTIVE-state invariants are re-established on reactivation, and channel reads see only active Items."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "Conditional on CORE-GAP-in-process-authorization (ADR-CAT-066). The OPEN platform finding 5 matches."},
    {"id": "ADR-CAT-063", "status_ok": true, "note": "Amended by ADR-CAT-068; the narrowing is carried through the SRS, plan, API document and tests."},
    {"id": "ADR-CAT-065", "status_ok": true, "note": "API-CAT-052 uses OwnedLookups. The manifest gap is OPEN platform finding 4."},
    {"id": "ADR-CAT-066", "status_ok": true, "note": "The core-gap is recorded and named in the plan."},
    {"id": "ADR-CAT-068", "status_ok": true, "note": "The three repairs are consistent across the SRS, plan, API document, frontend and TC-CAT-185, 186, 187, 210 and 212."},
    {"id": "ADR-CAT-069", "status_ok": true, "note": "Reactivation of Categories, Variant attributes and values has an API, a screen command and a TC."},
    {"id": "ADR-CAT-070", "status_ok": true, "note": "The C5/F3 deviation is justified, and CORE-GAP-tenant-lookup-types is OPEN."},
    {"id": "ADR-CAT-071", "status_ok": true, "note": "Time budget, 120-second rollback and batches are stated, with TC-CAT-217. CORE-GAP-number-series-block-allocation is OPEN."},
    {"id": "ADR-CAT-001…043 (P0/P0.5/P1)", "status_ok": true, "note": "ADR-CAT-025, 026 and 028 are correctly marked SUPERSEDED by ADR-CAT-039, 042 and the SRS. No ADR is BLOCKED."}
  ],
  "verdict": "APPROVE"
}
```