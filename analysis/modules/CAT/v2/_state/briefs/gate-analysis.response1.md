```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": { "unambiguous": 2, "verifiable": 2, "complete": 2, "consistent": 3, "singular": 3, "feasible": 2, "traceable": 3 },
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
    {"finding": "C5.13@srs:462 AC-CAT-013 'could'", "confirmed": false, "note": "False positive. The word is inside the verbatim RULE-CAT-009 message («could not be checked») quoted in the Then. It is not authored wording."},
    {"finding": "C5.13@srs:780 AC-CAT-036 'could'", "confirmed": false, "note": "False positive. It sits inside the verbatim RULE-CAT-005 message («could not be issued»)."},
    {"finding": "C5.13@srs:1305 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive. It sits inside the verbatim RULE-CAT-024 message («استخدم رمزًا أو اسمًا مختلفًا»)."},
    {"finding": "C5.13@srs:1745 AC-CAT-102 'could'", "confirmed": false, "note": "False positive. It sits inside the verbatim RULE-CAT-035 message («could not be read»). The ten clauses that examined nothing are empty by nature (no XM, no cross-module id)."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null, "clause": "complete",
     "problem": "The next action of RULE-CAT-037 («Generate an internal barcode for them, then print») cannot be done by the role that prints labels. Warehouse staff hold PERM_CAT_BARCODE_LABELS_CREATE, but API-CAT-015 needs PERM_CAT_ITEMS_CREATE, which only the Owner holds (SRS B4). The «Open item» jump on SCR-CAT-005 therefore lands on a read-only Item. This is a dead end for that role (KB §6.5).",
     "fix": "Add a «generate internal barcode» action to the Barcode labels page, for the flagged lines. Guard it with PERM_CAT_BARCODE_LABELS_CREATE, and let it reuse the API-CAT-015 service logic and its locks. Add the REQ, AC, API block, api-spec operation and TC for it. The alternative is to reword RULE-CAT-037's message and the UI to say «ask the owner». The better practice is the first option. Record the choice in an ADR.", "adr": true},
    {"id": "G2", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null, "clause": "verifiable",
     "problem": "Four concurrency guards named in the backend plan have a statement and no parallel-request test case: (a) a barcode add against a scale-layout create under the QR-CAT-074 prefix lock; (b) an Item unit deactivation against a barcode add, with the Item lock per ADR-CAT-060; (c) Variant generation against a value deactivation, with the FOR SHARE read of QR-CAT-031; (d) an Item save into a Category against that Category's deactivation, with the FOR SHARE read of QR-CAT-032. Only 7 guards are covered.",
     "fix": "Add four ACs (AC-CAT-167 … 170) in the style of AC-CAT-149 … 153, with one parallel TC each. Each TC states that exactly one request succeeds, names the refusal code, and names the guard and its owning unit. Update the coverage row «Concurrency guards».", "adr": false},
    {"id": "G3", "severity": "MINOR", "section": "backend", "artifact": "api-spec", "line": null, "clause": "complete",
     "problem": "Bundle `quantity` (NUMERIC(18,3)) and Item unit `conversionFactor` (NUMERIC(18,6)) carry no upper bound or scale limit in the document. An oversized value overflows the column and surfaces as CAT-500-SERVER-ERROR, although the plan says a constraint violation never surfaces as 500.",
     "fix": "Add `exclusiveMinimum: 0`, a `maximum` (999999999999999 for quantity, 999999999999 for the factor) and `multipleOf` to the schemas in API-CAT-001, 010 and 017. Add «above zero and at most …» to the RULE-CAT-018 and RULE-CAT-027 statements. The service answers CAT-400-REQUEST-INVALID above the maximum. Add a boundary row to TC-CAT-027 and TC-CAT-039.", "adr": false},
    {"id": "G4", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "unambiguous",
     "problem": "API-CAT-003 lists RULE-CAT-003 for every update. On an inactive Item, the categoryFk-only repair (ADR-CAT-068) does not say whether that check runs. If it does, a name taken by another active Item (invariant 4) blocks the repair of invariant 2.",
     "fix": "State in API-CAT-003 Validations that on the categoryFk-only path of an inactive Item only RULE-CAT-026 and the version check run. RULE-CAT-003 is re-checked at reactivation.", "adr": false},
    {"id": "G5", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "complete",
     "problem": "The update of an inactive Category (API-CAT-023), Variant attribute (027) or value (030) is unspecified. Examples are a move under another parent, or a rename that clashes with an active name. RULE-CAT-023 and RULE-CAT-016 are written for active records.",
     "fix": "State that the update of an inactive record is allowed but runs the same rules as on create, including an active parent. Add one row to TC-CAT-213 and TC-CAT-215.", "adr": false}
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-045", "status_ok": true, "note": "Status reads ACCEPTED while §1 and §4 are superseded by ADR-CAT-061. The header note says so, which is acceptable."},
    {"id": "ADR-CAT-054", "status_ok": true, "note": "SUPERSEDED by ADR-CAT-055. No downstream artifact follows the removal of the tenant FKs."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "ACCEPTED, conditional on CORE-GAP-in-process-authorization (ADR-CAT-066). It is not BLOCKED, and the condition is stated in the contract and the plan."},
    {"id": "ADR-CAT-070", "status_ok": true, "note": "It justifies the C5 / F3 deviation and raises a CORE-GAP as checklist F9 requires."},
    {"id": "ADR-CAT-074", "status_ok": true, "note": "It amends ADR-CAT-071. The plan and test plan follow it, and the 60 s figure is stated as a measured target."},
    {"id": "ADR-CAT-076", "status_ok": true, "note": "It follows the core's published API. The import's delivery dependency is recorded as OPEN platform finding 11 (CORE-GAP-file-document-metadata-read), and API-CAT-049 cannot be delivered until the core answers it."},
    {"id": "ADR-CAT-077", "status_ok": true, "note": "The Category events appear in the SRS, the contract, the plan, the api-spec and TC-CAT-224."},
    {"id": "ADR-CAT-052", "status_ok": true, "note": "It carries superseded counts, noted in its own «Notes added later». The 11 OPEN platform findings are legitimately outside CAT, and each names an owner and evidence."}
  ],
  "verdict": "REVISE"
}
```