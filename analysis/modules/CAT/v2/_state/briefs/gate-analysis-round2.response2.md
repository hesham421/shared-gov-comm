I checked the erp-core checklist and the mdl and conventions docs to confirm G5, G6 and G7. Merged scorecard:

```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": { "unambiguous": 2, "verifiable": 2, "complete": 1, "consistent": 2, "singular": 3, "feasible": 2, "traceable": 2 },
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
    {"finding": "C5.13 AC-CAT-013 'could'", "confirmed": false, "note": "False positive. The word is inside the verbatim RULE-CAT-009 message, not the Given/When/Then wording."},
    {"finding": "C5.13 AC-CAT-036 'could'", "confirmed": false, "note": "False positive. The word is inside the verbatim RULE-CAT-005 message."},
    {"finding": "C5.13 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive. The word is inside the verbatim RULE-CAT-024 message."},
    {"finding": "C5.13 AC-CAT-102 'could'", "confirmed": false, "note": "False positive. The word is inside the verbatim RULE-CAT-035 message."},
    {"finding": "coverage: C6.3, C6.10, C6.12, C6.15, C7.24, C7.26, C7.27, C7.28, C7.5, C7.5b examined nothing", "confirmed": true, "note": "Empty by nature. The XM register is empty, there is no patch or foreign scatter, and the CORE_TENANT FK is a permitted given reference (ADR-CAT-055)."},
    {"finding": "coverage: C10.8 and C10.9 examined nothing", "confirmed": false, "note": "Not empty by nature. About 10 TCs carry Host data and Test data lines, so the checks are failing to find their subject (OPEN platform finding 3; G9)."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "complete",
     "problem": "RULE-CAT-008's trigger reads 'any create, update or deactivate' of the flag-bound scope. Two problems follow. (1) Only API-CAT-001, 006, 017 and 025–031 check the flag. API-CAT-003, 004, 005 and 009 on a BUNDLE Item or a Variant do not, and their Errors lines omit CAT-422-CAPABILITY-OFF. (2) API-CAT-028 and 031 refuse deactivating attributes or values while `variants` is off, so data that depends on the flag cannot be cleaned up. Platform finding 2 is OPEN, so a flag can already be off while that data exists. The rule's text and the endpoints disagree, and the clean-up path is unspecified.",
     "fix": "Rewrite RULE-CAT-008's trigger to list exactly the refused operations: create a BUNDLE Item, generate Variants, save Bundle components, and create or update attributes and values. State that reading, deactivating and reactivating existing records stay allowed while the flag is off, and drop the flag check from API-CAT-028 and 031. Add one AC and one TC: with the flag off, an existing Bundle can be read and deactivated.",
     "adr": false},
    {"id": "G2", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "complete",
     "problem": "Writes to the children of an inactive parent are unspecified. Unspecified cases: PUT on an inactive Item, units, barcodes, images, generation and Bundle components on an inactive Item, and a barcode on a deactivated Variant. Also, API-CAT-009 deactivates a Variant's assignments but not its barcodes. Those barcodes stay active, block RULE-CAT-029 uniqueness and can never be looked up. One missing rule shows up in about 10 endpoints, so this is a pattern.",
     "fix": "Add a rule to SRS A5: an inactive parent refuses child writes (code CAT-422-PARENT-INACTIVE, text in ar and en). Apply it under the existing Item lock in API-CAT-003, 006, 008, 010, 012, 014, 015, 017, 018 and 019. Choose, and record in the ADR, whether deactivating a Variant also deactivates its barcodes. The recommended choice is yes, which releases the values for RULE-CAT-029. Update the Error Catalog and api-spec, and add an AC and a TC.",
     "adr": true},
    {"id": "G3", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "complete",
     "problem": "API-CAT-033 lets a layout's scaleCodeLength, embeddedValueTypeCode and valueDecimals change, and API-CAT-034 deactivates a layout. Active SCALE barcodes were validated against the old length (RULE-CAT-033). After a change they stop decoding or decode wrongly, and no statement covers it.",
     "fix": "Fix scaleCodeLength after creation, as key already is. State that changing valueDecimals or the value type only changes how the embedded value is read. State that deactivating a layout leaves SCALE barcodes stored but not decodable, as REQ-CAT-094 does. Add one TC.",
     "adr": true},
    {"id": "G4", "severity": "MAJOR", "section": "backend", "artifact": "contract", "line": null, "clause": "consistent",
     "problem": "CON-CAT-017 promises 'message ar/en' in the turn-off check result. TurnOffCheckResult, which the in-process CatalogueReadApi.checkCapabilityTurnOff also returns, has no message fields. The text exists only as the HTTP envelope code. The flag writer of platform finding 2 is told to show the returned message and cannot do so in-process.",
     "fix": "Add messageAr and messageEn to TurnOffCheckResult in the DOC phase and in api-spec. The service fills them with the RULE-CAT-010 or RULE-CAT-011 text and {count}.",
     "adr": false},
    {"id": "G5", "severity": "MAJOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null, "clause": "erp-core checklist C6: \"The module serves only its own lookup keys through `OwnedLookups.read(...)`\"",
     "problem": "erp-core mdl.md requires the owning module to 'serve its own keys to the UI through `OwnedLookups` (never a pass-through)' (conventions §Lookups rule 4). CAT owns 10 keys, but no API-CAT operation serves them. ADR-CAT-050 and UXD-CAT-001 have the frontend read them through 'the given's lookup client', and the backend plan names no OwnedLookups. This one gap runs across the backend plan, api-spec, ui-ux-spec and frontend plan.",
     "fix": "Add the operation GET /api/v1/cat/lookups/{key}. Its service calls OwnedLookups.read(key, the 10 CAT keys, CAT-404-LOOKUP-KEY-UNKNOWN, MdlLookupApi::readActiveValuesByKey) and is guarded by any CAT page VIEW. Add the error row, a given-usage row for the common owned-lookups capability, and the api-spec operation. Rebind UXD-CAT-001 and the `*-LOOKUP` hooks to it, and amend ADR-CAT-050 §1.",
     "adr": true},
    {"id": "G6", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "erp-core checklist C11: \"every code has an EN and an AR message\"",
     "problem": "The six PLATFORM-STD catalog rows (CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR) have no ar or en text. They are CAT-prefixed codes, so CatErrorCodes needs messages.properties and messages_ar.properties entries for them. ADR-CAT-044's table also still lists the 413 and 415 rows that ADR-CAT-056 removed.",
     "fix": "Write ar and en text for the six rows in the Error Catalog and the message-bundle rule. Alternatively, reuse the core's own codes and remove the CAT- rows, stating that in an ADR-CAT-044 successor. Delete the 413 and 415 rows from that table either way.",
     "adr": false},
    {"id": "G7", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "erp-core checklist C13: \"every service method has `@PreAuthorize(\"hasAuthority(T(…<Module>Permissions).PERM_…)\")` referencing its own module only\"",
     "problem": "By design (ADR-CAT-061), CatCatalogueReadService's methods carry no @PreAuthorize. Conventions §Security rule 2 restates C13 with no exception. Checklist F9 forbids working around a core capability instead of writing CORE-GAP-<topic>.md.",
     "fix": "Raise CORE-GAP-in-process-authorization.md, which asks how a crossmodule read API is authorized for callers that hold no permission of the owning module. Make ADR-CAT-061 conditional on the core's answer, and name the CORE-GAP in PHASE CORE.",
     "adr": true},
    {"id": "G8", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null, "clause": "complete",
     "problem": "The plan names a concurrency guard for six rules, but none of them has a test case. Uncovered rules: RULE-CAT-015, 014, 039, 003, 029 and 021. Only stale-version (TC-CAT-192) is tested.",
     "fix": "Add one AC for parallel requests to each of REQ-CAT-053, 052, 106, 007, 087 and 062. Add matching TCs in the form 'two simultaneous requests, exactly one succeeds', each naming the owning unit.",
     "adr": false},
    {"id": "G9", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null, "clause": "traceable",
     "problem": "C10.8 and C10.9 examine 0 subjects. The Host data lines of TC-CAT-019, 036, 037, 068, 137 and 139 name no verb or path; they say the value is maintained by 'MdlLookupApi; no add-value verb/path'. The repairs of ADR-CAT-052 §3 and ADR-CAT-053 are therefore unproven. Platform finding 3 says the gate cannot APPROVE until both clauses report a non-zero count.",
     "fix": "Take the given mdl add-value and deactivate-value verb and path from erp-core-usage reference/modules/mdl.md and api-docs, and put them in each Host data line in the form the C10.8 parser reads. Re-run analyze until C10.8 and C10.9 are non-zero.",
     "adr": false},
    {"id": "G10", "severity": "MINOR", "section": "backend", "artifact": "contract", "line": null, "clause": "consistent",
     "problem": "The contract signatures of CON-CAT-008 and 009 name itemPk, but VariantDetail and ItemUnitView expose itemFk.",
     "fix": "Use one name in both places.",
     "adr": false},
    {"id": "G11", "severity": "MINOR", "section": "backend", "artifact": "api-spec", "line": null, "clause": "consistent",
     "problem": "In API-CAT-017 the plan marks a line's componentVariantFk and quantity as required. The api-spec items of `lines` have no required list.",
     "fix": "Add required: [componentVariantFk, quantity].",
     "adr": false},
    {"id": "G12", "severity": "MINOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null, "clause": "complete",
     "problem": "The Items screen shows the jumps 'Print labels', 'Import' and 'Open Variant attributes' whatever the user's permissions. The target pages need another page's VIEW, and Variant attributes also needs `variants` on. A Seller/cashier who follows one of them lands on the unauthorized view.",
     "fix": "Add a SEC-FE rule: a jump to another page is shown only when the user holds that page's VIEW, and its flag is on when it has one. List the three jumps.",
     "adr": false},
    {"id": "G13", "severity": "MINOR", "section": "tests", "artifact": "backend-test-plan", "line": null, "clause": "verifiable",
     "problem": "Four counter-cases are stated in the analysis but have no TC: flag off on API-CAT-006, flag off on API-CAT-017, RULE-CAT-004 on API-CAT-008, and RULE-CAT-035 with a wrong check digit.",
     "fix": "Add them as TCs, or as steps of TC-CAT-006, 007, 015 and 051.",
     "adr": false},
    {"id": "G14", "severity": "MINOR", "section": "backend", "artifact": "db-script", "line": null, "clause": "feasible",
     "problem": "BLOCK 8 ends with an explicit COMMIT inside a Flyway migration. It inserts into given tables using column names D-P2-13 says are unverified. CREATE EXTENSION pg_trgm needs a database privilege that is not stated as a precondition.",
     "fix": "Remove the COMMIT. Check the MDL_LOOKUP_* and CORE_NUMBER_SERIES columns and the PLATFORM tenant id against the erp-core 1.6.0 migrations and record the result. State the pg_trgm privilege as a deployment precondition.",
     "adr": false},
    {"id": "G15", "severity": "MINOR", "section": "backend", "artifact": "db-script", "line": null, "clause": "consistent",
     "problem": "Only two intra-module FKs are tenant-composite. D-P2-12 presents same-tenant integrity as structural, but the other FKs do not enforce it.",
     "fix": "Make every intra-module FK tenant-composite, or reword D-P2-12 to say those FKs rely on the given tenant filter.",
     "adr": true}
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-025", "status_ok": true, "note": "Correctly marked superseded by ADR-CAT-039 and D-04."},
    {"id": "ADR-CAT-026", "status_ok": true, "note": "Correctly marked superseded by ADR-CAT-042."},
    {"id": "ADR-CAT-028", "status_ok": true, "note": "Correctly marked superseded by RULE-CAT-001."},
    {"id": "ADR-CAT-044", "status_ok": true, "note": "Its table still lists the 413 and 415 rows, and no row has ar or en text (G6)."},
    {"id": "ADR-CAT-045", "status_ok": true, "note": "Partly superseded by ADR-CAT-061, as stated."},
    {"id": "ADR-CAT-050", "status_ok": true, "note": "§1 conflicts with mdl's OwnedLookups rule for UXD-CAT-001 (G5)."},
    {"id": "ADR-CAT-053", "status_ok": true, "note": "Its 'next free ADR is 054' line is stale."},
    {"id": "ADR-CAT-054", "status_ok": true, "note": "Correctly marked superseded by ADR-CAT-055."},
    {"id": "ADR-CAT-055", "status_ok": true, "note": "Matches checklist C1. Platform finding 1 is OPEN and owned by factory/gov.py, which is correct."},
    {"id": "ADR-CAT-056", "status_ok": true, "note": "Follows checklist C7 and F4."},
    {"id": "ADR-CAT-057", "status_ok": true, "note": "Follows checklist C10 and F7."},
    {"id": "ADR-CAT-058", "status_ok": true, "note": "Matches the conventions (MdlTenantProvisioningContributor and the sequence copy)."},
    {"id": "ADR-CAT-059", "status_ok": true, "note": "Sound, but not yet applied to child writes (G2)."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "Conflicts with C13 and conventions §Security rule 2. It needs CORE-GAP-in-process-authorization (G7)."}
  ],
  "verdict": "REVISE"
}
```

DECISION: G5 raised to frontend scope with adr=true. erp-core mdl.md and conventions rule 4 require the owning module to serve its own keys to the UI through OwnedLookups. So the defect is the UI binding of ADR-CAT-050/UXD-CAT-001, not only a missing given-usage row.

DECISION: G7 resolved by a CORE-GAP ADR, not by repackaging. Conventions §Security rule 2 restates C13 with no exemption, so moving the logic to another package would itself be a workaround under F9.

DECISION: G1 fix is to narrow RULE-CAT-008, not to add the flag check to more endpoints. Clean-up of data that depends on the flag must stay possible while the flag is off. The flag checks on attribute and value deactivation (API-CAT-028, 031) are therefore dropped.

DECISION: G2 also covers barcodes of deactivated Variants. Releasing them with the Variant is the recommended choice, recorded in the ADR.

DECISION: traceable lowered from 3 to 2. C10.8/C10.9 examined nothing and platform finding 3 is OPEN, so the host-data trace is not proven mechanically.

DECISION: all other round-1 scores, extra checks, analyze confirmations and the G3, G4, G6, G8–G15 findings are confirmed. Verdict: REVISE.

<!-- CONVERGED -->