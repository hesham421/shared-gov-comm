```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": { "unambiguous": 1, "verifiable": 2, "complete": 2, "consistent": 1, "singular": 3, "feasible": 2, "traceable": 3 },
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
    {"finding": "C5.13 srs:452 AC-CAT-013 'could'", "confirmed": false, "note": "False positive. 'could' is inside the quoted English message of RULE-CAT-009 (\"could not be checked\"), not in the criterion itself."},
    {"finding": "C5.13 srs:770 AC-CAT-036 'could'", "confirmed": false, "note": "False positive. 'could' is inside the quoted RULE-CAT-005 message (\"could not be issued\")."},
    {"finding": "C5.13 srs:1290 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive. The word is part of the quoted RULE-CAT-024 message text («رمزًا أو اسمًا مختلفًا»); the AC's Then is exact."},
    {"finding": "C5.13 srs:1730 AC-CAT-102 'could'", "confirmed": false, "note": "False positive. 'could' is inside the quoted RULE-CAT-035 message (\"could not be read\")."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "consistent",
     "problem": "API-CAT-047 contradicts itself. Its Layers line puts `CatSearchQueryFacade.searchCatalogue` behind `@PreAuthorize` on the screen VIEW permission. Its Validations and Security lines, REQ-CAT-127, AC-CAT-129, TC-CAT-124, ADR-CAT-061 §3 and the api-spec (which has no 403 response) all require a caller without PERM_CAT_ITEMS_VIEW to get 200 with an empty list. With the annotation in place, Spring Security answers 403 before the empty-result logic runs. The HTTP path and the in-process `CatalogueReadApi.searchCatalogue` path would then behave differently.",
     "fix": "In API-CAT-047 Layers and in the CatalogueReadApi table, state that `CatSearchQueryFacade.searchCatalogue` carries only `@PreAuthorize(\"isAuthenticated()\")`. `CatCatalogueReadService.searchCatalogue` checks PERM_CAT_ITEMS_VIEW in code and returns an empty list when the caller lacks it. Add this exception to the PHASE SEC-BE paragraph that already names API-CAT-047.",
     "adr": false},
    {"id": "G2", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "concurrency (backend row)",
     "problem": "API-CAT-024's Concurrency line credits API-CAT-049 with reading the Category FOR SHARE, but API-CAT-049 does not. It resolves category codes in the lock-free validation pass (QR-CAT-032, no lock). Its write pass re-reads only the scale layouts under the scale-prefix lock. A Category deactivated between the two passes can still receive imported active Items. Each such Item then points to an inactive Category, which breaks RULE-CAT-026 and invariant 2 of SRS A7 (ADR-CAT-059).",
     "fix": "Add a step at the start of the API-CAT-049 write pass: re-read every Category referenced by a valid row with QR-CAT-032 FOR SHARE, and reject each row whose Category is now inactive with RULE-CAT-026 (column category, inside CAT-200-IMPORT-ROW-REJECTED) before its batch is inserted. Add QR-CAT-032 (FOR SHARE) to the Repository line and the lock order (scale-prefix lock, then Category rows) to the Concurrency line. Add API-CAT-049 to QR-CAT-032's FOR SHARE locking note.",
     "adr": false},
    {"id": "G3", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "unambiguous",
     "problem": "API-CAT-041 and QR-CAT-026 apply `isActiveFl` to attributes only (DBF-CAT-040); the second read of QR-CAT-026 returns every value. TC-CAT-090 nevertheless asserts that `GET /variant-attributes?isActiveFl=true` omits the inactive value XS. The frontend ATTRIBUTE-OPTIONS-QUERY also promises 'active values'. SCR-CAT-003 needs inactive values to stay visible, or Reactivate of a value (API-CAT-055) cannot be reached. As written, TC-CAT-090 fails against the plan, and two implementers would build the filter differently.",
     "fix": "In API-CAT-041, QR-CAT-026 and the api-spec parameter description, state that `isActiveFl` filters both the attributes and their values, and that omitting it returns all of both. In F2, state that the generation picker sends isActiveFl=true and that the SCR-CAT-003 list and values editor send no filter.",
     "adr": false},
    {"id": "G4", "severity": "MINOR", "section": "tests", "artifact": "backend-test-plan", "line": null,
     "clause": "verifiable",
     "problem": "ADR-CAT-071 and API-CAT-049 commit to a 120 s timeout with a whole-import rollback: nothing stored, no events, code gaps left, lock released, CAT-500-CHANGE-NOT-SAVED. They also set a 60 s budget for 5,000 rows. No REQ, AC or TC covers this behaviour.",
     "fix": "Add one REQ with the unwanted pattern and one AC: an import that exceeds its timeout is rolled back whole, stores 0 Items, Variants, barcodes and events, and answers CAT-500-CHANGE-NOT-SAVED. Add one backend TC in SVC-API-INT that injects a delayed write pass, in the style of TC-CAT-004.",
     "adr": false},
    {"id": "G5", "severity": "MINOR", "section": "backend", "artifact": "contract", "line": null,
     "clause": "traceable (stale upstream headers)",
     "problem": "Several header lines were not refreshed after the gate-analysis revises. The contract says 'REQ 127'. The db-script says 'REQ 127 · RULE 49' and 'next free ADR sequence is 068', and so does registry-db. The current registries give REQ 138, RULE 53 and next free ADR 072. The rule that allowed this: a revise re-renders only the artifacts it edits.",
     "fix": "Refresh the Inputs, Counts and next-id lines of the contract, db-script and registry-db from the current registries. Add a step to the revise checklist: re-stamp the header counts of every downstream artifact from the registries.",
     "adr": false},
    {"id": "G6", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "complete",
     "problem": "The analysis never says what happens to an Item's barcodes when the Item is deactivated. ADR-CAT-063 cascades barcodes only on Variant deactivation. After an Item is deactivated, its Variants' barcodes stop resolving (ADR-CAT-059) but stay active, so they keep blocking their values under RULE-CAT-029. Keeping them is probably intended, since the Item can be reactivated, but no artifact says so.",
     "fix": "State in REQ-CAT-005 and SRS A7 that deactivating an Item leaves its barcodes active, so it can be reactivated with them, and that the owner releases a GTIN with API-CAT-016, which stays allowed on an inactive Item. Extend the TC-CAT-188 expected result: the same GTIN added to another Variant is refused with CAT-409-BARCODE-DUPLICATE.",
     "adr": false}
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-044", "status_ok": true, "note": "Six PLATFORM-STD rows, each with ar and en text; the 413 and 415 rows were removed with the multipart surface."},
    {"id": "ADR-CAT-047", "status_ok": true, "note": "The tree lock covers every Category operation. API-CAT-049 does not take the Category FOR SHARE that API-CAT-024 credits it with (G2)."},
    {"id": "ADR-CAT-053", "status_ok": true, "note": "Unbound texts are recorded as a P3.2 plan gap. The note on the add-value call is now historical."},
    {"id": "ADR-CAT-055", "status_ok": true, "note": "The FK to the given CORE_TENANT matches checklist C1. The C6.15 exemption is an OPEN platform finding."},
    {"id": "ADR-CAT-056", "status_ok": true, "note": "No multipart endpoint, as checklist F4 and C7 require."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "Conditional on the core via ADR-CAT-066 and the CORE-GAP. Its searchCatalogue exception conflicts with the facade annotation in API-CAT-047 (G1)."},
    {"id": "ADR-CAT-063", "status_ok": true, "note": "Narrowed correctly by ADR-CAT-068. Barcode handling on Item deactivation is unstated (G6)."},
    {"id": "ADR-CAT-068", "status_ok": true, "note": "Removes the three reactivation dead ends; the SRS, both plans and both test plans follow it."},
    {"id": "ADR-CAT-069", "status_ok": true, "note": "Reactivation endpoints, guards and locks agree across the SRS, plan, api-spec and tests."},
    {"id": "ADR-CAT-070", "status_ok": true, "note": "The checklist C5/F3 deviation is justified and raised as a CORE-GAP, as F9 requires."},
    {"id": "ADR-CAT-071", "status_ok": true, "note": "The time budget and timeout behaviour have no AC or TC (G4)."}
  ],
  "verdict": "REVISE"
}
```

DECISION: Analyze findings
All four C5.13 hits are false positives: each word sits inside a quoted RULE message, not in the criterion. Confirmed from round 1.

DECISION: G1 stays MAJOR
API-CAT-047 Layers does carry the VIEW `@PreAuthorize` on its facade. That contradicts REQ-CAT-127 and the api-spec, which has no 403 response.

DECISION: G2 stays MAJOR
QR-CAT-032 in API-CAT-049 is listed as a plain FIND_ONE in the lock-free validation pass. The FOR SHARE that API-CAT-024 cites does not exist there.

DECISION: G3 stays MAJOR
I considered a client-side filter on `VariantAttributeValueView.isActiveFl`, which would fix the picker. It does not fix TC-CAT-090: that test asserts the server-side omission, and as written it fails against QR-CAT-026.

DECISION: Scores and verdict
Scores are kept as in round 1. The three MAJOR findings each have a fix the analysis lane can apply, and none is CRITICAL or repeats an earlier round, so the verdict is REVISE.

<!-- CONVERGED -->