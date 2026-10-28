DECISION: G1 confirmed as MAJOR. API-CAT-015 requires PERM_CAT_ITEMS_CREATE, and SRS B4 grants that permission to the Owner only. Warehouse staff can print labels, but they cannot follow the RULE-CAT-037 next action ("generate an internal barcode").

DECISION: G2 confirmed as MAJOR. The four guards are stated in the plan's Concurrency lines (QR-CAT-074, ADR-CAT-060, QR-CAT-031 FOR SHARE, QR-CAT-032 FOR SHARE), but none of them has a parallel-request TC.

DECISION: G3 and G4 confirmed as MINOR.

DECISION: G5 is replaced by G6, which applies the P4 pattern. Updates of inactive records are handled inconsistently. API-CAT-033 and 036 refuse them. API-CAT-012, 019, 023, 027 and 030 say nothing. API-CAT-019 shows the harm: QR-CAT-055 loads a media row whether it is active or not, so a PUT on a removed image with isPrimaryFl true clears the active primary. That breaks RULE-CAT-039. Raised to MAJOR.

DECISION: New G7 (MINOR). The frontend-test-plan COVERAGE counts are stale ("60 / 164"), but the SRS has 166 ACs. Because of this, consistent drops from 3 to 2.

DECISION: §3.6 checklist read line by line (C1–C22, F1–F12). No line is violated:
- F3 (CAT_VAR_ATTR) and F9 are covered by CORE-GAP files (ADR-CAT-070, 076, 066, 071, 073).
- The sortOrder `MAX+1` in QR-CAT-024, 029 and 034 is a display order, not a document number, so F6 does not apply.
- The literals in QR-CAT-007, 009, 052 and 053 are closed system lookups that carry behaviour, not user-maintained lists.

```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": { "unambiguous": 2, "verifiable": 2, "complete": 2, "consistent": 2, "singular": 3, "feasible": 2, "traceable": 3 },
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
    {"finding": "C5.13@srs:462 AC-CAT-013 'could'", "confirmed": false, "note": "False positive. The word sits inside the verbatim RULE-CAT-009 message («could not be checked») that the Then quotes."},
    {"finding": "C5.13@srs:780 AC-CAT-036 'could'", "confirmed": false, "note": "False positive. The word sits inside the verbatim RULE-CAT-005 message."},
    {"finding": "C5.13@srs:1305 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive. The word sits inside the verbatim RULE-CAT-024 message."},
    {"finding": "C5.13@srs:1745 AC-CAT-102 'could'", "confirmed": false, "note": "False positive. The word sits inside the verbatim RULE-CAT-035 message. Every clause that examined nothing is empty by nature: there is no XM and no cross-module id."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null, "clause": "complete",
     "problem": "The next action of RULE-CAT-037 («Generate an internal barcode for them, then print») cannot be taken by the role that prints labels. Warehouse staff hold PERM_CAT_BARCODE_LABELS_CREATE, but API-CAT-015 requires PERM_CAT_ITEMS_CREATE, which only the Owner holds (SRS B4). The «Open item» jump on SCR-CAT-005 opens a read-only Item, so the role reaches a dead end (KB §6.5).",
     "fix": "Add a «generate internal barcode» action for flagged lines on SCR-CAT-005. Guard it with PERM_CAT_BARCODE_LABELS_CREATE and reuse the API-CAT-015 service logic and its locks. Add the REQ, AC, API block, api-spec operation and TC for it. Record the choice in an ADR. The alternative is to make the RULE-CAT-037 next action depend on the role («ask the owner»), but that is weaker.", "adr": true},
    {"id": "G2", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null, "clause": "verifiable",
     "problem": "Four concurrency guards stated in the backend plan have no parallel-request test case: (a) a barcode add racing a scale-layout create, under QR-CAT-074; (b) an Item unit deactivation racing a barcode add, under the Item lock of ADR-CAT-060; (c) a Variant generation racing a value deactivation, under the QR-CAT-031 FOR SHARE read; (d) an Item save into a Category racing that Category's deactivation, under the QR-CAT-032 FOR SHARE read.",
     "fix": "Add AC-CAT-167 … 170 in the style of AC-CAT-149 … 153, with one parallel TC each. Each TC shows exactly one request succeeding, names the refusal code, and names the guard and the unit that owns it. Update the «Concurrency guards» coverage row.", "adr": false},
    {"id": "G3", "severity": "MINOR", "section": "backend", "artifact": "api-spec", "line": null, "clause": "complete",
     "problem": "Bundle `quantity` (NUMERIC(18,3)) and `conversionFactor` (NUMERIC(18,6)) have no upper bound or scale limit in the document. An oversized value overflows the column and comes back as CAT-500-SERVER-ERROR.",
     "fix": "In API-CAT-001, 010 and 017, add `exclusiveMinimum: 0`, a `maximum` that fits the column precision, and `multipleOf` for the scale. Restate RULE-CAT-018 and RULE-CAT-027 with the bound. Answer CAT-400-REQUEST-INVALID above the bound. Add boundary rows to TC-CAT-027 and TC-CAT-039.", "adr": false},
    {"id": "G4", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "unambiguous",
     "problem": "API-CAT-003 does not say whether RULE-CAT-003 runs on the categoryFk-only repair path of an inactive Item. If it does, a name already taken by another Item blocks the repair of invariant 2.",
     "fix": "In API-CAT-003 Validations, state that on this path only RULE-CAT-026 and the version check run, and that RULE-CAT-003 is checked again at reactivation.", "adr": false},
    {"id": "G6", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "consistent",
     "problem": "Pattern (P4): an update of an inactive record has no one rule. API-CAT-033 and 036 refuse it with CAT-400-REQUEST-INVALID. API-CAT-012 (Item unit), 019 (media), 023 (Category), 027 (attribute) and 030 (value) do not say. The harmful case is API-CAT-019: QR-CAT-055 loads a removed image, and PUT with isPrimaryFl true clears the active primary first, leaving the Item's active images without a primary. UQ_CAT_ITEM_MEDIA_PRIMARY covers active rows only, so the database does not catch it, and RULE-CAT-039 is broken. This replaces the earlier G5.",
     "fix": "Add one rule to the PHASE CORE API conventions: a PUT on a record whose own isActiveFl is false answers CAT-400-REQUEST-INVALID and writes nothing. The repair path is reactivation (API-CAT-005, 053 … 055) or, for a removed image, a new upload. Cite the rule in API-CAT-012, 019, 023, 027 and 030, and add it to the CAT-400-REQUEST-INVALID trigger list. Add rows to TC-CAT-223 for each endpoint, including the removed-image case with an assertion that the active primary is unchanged.", "adr": false},
    {"id": "G7", "severity": "MINOR", "section": "tests", "artifact": "frontend-test-plan", "line": null, "clause": "consistent",
     "problem": "The COVERAGE section says «60 / 164 by module TCs», but the SRS and the backend plan count 166 ACs (AC-CAT-165 and 166 were added at the ninth revise). The header Delta says no TC changed, but the count was not re-stamped. This is the same issue as platform finding 8.",
     "fix": "Re-stamp the COVERAGE rows of the frontend test plan from registry-srs (AC 166; this track 60, the other 106 on the backend).", "adr": false}
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-045", "status_ok": true, "note": "Status reads ACCEPTED while §1 and §4 are superseded by ADR-CAT-061. The header note records this, which is acceptable."},
    {"id": "ADR-CAT-054", "status_ok": true, "note": "SUPERSEDED by ADR-CAT-055. No downstream artifact follows it."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "Conditional on CORE-GAP-in-process-authorization (ADR-CAT-066). It is not BLOCKED, and the contract §0 and the plan state the condition."},
    {"id": "ADR-CAT-070", "status_ok": true, "note": "It justifies the C5/F3 deviation and raises a CORE-GAP, as F9 requires."},
    {"id": "ADR-CAT-074", "status_ok": true, "note": "It amends ADR-CAT-071. The plan and TC-CAT-217 follow it."},
    {"id": "ADR-CAT-076", "status_ok": true, "note": "It follows the published FileDocumentLookupApi. The import's delivery dependency is recorded as OPEN platform finding 11."},
    {"id": "ADR-CAT-077", "status_ok": true, "note": "Reflected in the SRS, the contract, the plan, the api-spec, the frontend plan and TC-CAT-224."},
    {"id": "ADR-CAT-052", "status_ok": true, "note": "Its counts are superseded, and its own «Notes added later» says so. The 11 OPEN platform findings are genuinely outside CAT, and each names an owner and evidence."}
  ],
  "verdict": "REVISE"
}
```
<!-- CONVERGED -->