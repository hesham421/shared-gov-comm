```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": {
    "unambiguous": 3,
    "verifiable": 3,
    "complete": 2,
    "consistent": 2,
    "singular": 3,
    "feasible": 2,
    "traceable": 3
  },
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
    {"finding": "C5.13@srs:451 AC-CAT-013", "confirmed": false, "note": "False positive. 'could' sits inside the quoted RULE-CAT-009 message text, not in the criterion's own wording."},
    {"finding": "C5.13@srs:769 AC-CAT-036", "confirmed": false, "note": "False positive. 'could' is part of the quoted RULE-CAT-005 message."},
    {"finding": "C5.13@srs:1289 AC-CAT-072", "confirmed": false, "note": "False positive. 'مختلف' is part of the quoted RULE-CAT-024 message."},
    {"finding": "C5.13@srs:1724 AC-CAT-102", "confirmed": false, "note": "False positive. 'could' is part of the quoted RULE-CAT-035 message."}
  ],
  "findings": [
    {
      "id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null,
      "clause": "complete (RULE-CAT-053 / REQ-CAT-135, REQ-CAT-130, A7 ACTIVE-state invariants)",
      "problem": "RULE-CAT-053 refuses every write under an inactive Item. RULE-CAT-050, 026 and 019 refuse reactivation until the invariants hold again. That leaves three states the user can never leave, and the messages promise a way out that does not exist. (1) An inactive Item with 0 active Variants (AC-CAT-132, TC-CAT-185): the message says 'generate a variant', but generation is refused on an inactive Item, and a Variant cannot be reactivated. (2) An inactive Item whose Category went inactive (AC-CAT-133, TC-CAT-186): changing categoryFk needs a PUT, which is refused on an inactive Item, and a Category has no reactivate operation (isActiveFl is 'DELETE only'). (3) An inactive Bundle with an invalid component (AC-CAT-134, TC-CAT-187): API-CAT-017 is refused on an inactive Bundle. This is one defect repeated in three places. The rule that allowed it is RULE-CAT-053, which has no carve-out for operations that repair an ACTIVE-state invariant.",
      "fix": "Amend RULE-CAT-053 and REQ-CAT-135 with a stated exemption: on an inactive Item, three operations stay allowed because they are the only way back to ACTIVE. They are Variant generation (API-CAT-006), changing categoryFk alone (API-CAT-003), and replacing Bundle components (API-CAT-017). Each is still refused if it would break another invariant. Update the SRS A7 text, the Orchestration and Validations lines of API-CAT-003, 006 and 017, and the CAT-422-PARENT-INACTIVE trigger text. Alternatively, add Category reactivation and Variant reactivation, but the exemption is smaller. Record the choice in a new ADR.",
      "adr": true
    },
    {
      "id": "G2", "severity": "MAJOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null,
      "clause": "consistent (F3 SCR-CAT-001 inactive-Item read-only; ui-ux-spec SCR-CAT-001)",
      "problem": "F3 and the UX spec hide every add trigger on an inactive Item, including 'Generate variants', 'Add component' and the Category field. After G1 these are the repair paths the user needs. The REQ-CAT-130 refusal messages tell the user to do exactly what the screen forbids.",
      "fix": "In F3, F4 and the UX spec, keep three affordances on an inactive Item's Entry. They are 'Generate variants' (shown while `variants` is on), the Category field (editable), and the Bundle components pane (editable for a BUNDLE). All other fields and add triggers stay read-only. Route the refusal banners of ITEM-STATE-COMMAND to these affordances.",
      "adr": false
    },
    {
      "id": "G3", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null,
      "clause": "verifiable (P1 counter-case; TC-CAT-185, 186, 187)",
      "problem": "TC-CAT-185, 186 and 187 assert the refusals but not the recovery. After G1 each refusal needs a second step that proves the Item can return to ACTIVE.",
      "fix": "Extend TC-CAT-185, 186 and 187 with a recovery sequence, run on an inactive Item. 185: generate a Variant, then reactivate. 186: change categoryFk to an active Category, then reactivate. 187: replace the Bundle components, then reactivate. Add a frontend TC that the Entry of an inactive Item still offers those three affordances.",
      "adr": false
    },
    {
      "id": "G4", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
      "clause": "concurrency (API-CAT-021, 023, 024; ADR-CAT-047)",
      "problem": "API-CAT-024 deactivates a Category under the Category row lock, counts active sub-categories, then sets inactive. API-CAT-021 creates a sub-category under the tenant tree lock and reads the parent without a lock (QR-CAT-032 is FOR SHARE only 'when an Item is attached'). The two use different locks, so they can interleave. The create sees the parent active, the deactivation counts 0 children, and both commit. The result is an active sub-category under an inactive parent, which RULE-CAT-023 forbids. API-CAT-024's Concurrency line names the tree lock for creates and moves, but its own steps never take it.",
      "fix": "Make API-CAT-024 take the same advisory tree lock (TENANT_ID, 'CAT_CATEGORY_TREE') before it counts, or read the parent FOR SHARE in API-CAT-021 and API-CAT-023. Use the tree lock, which is simpler. State it in the Orchestration and Concurrency lines and in ADR-CAT-047. Add an AC and a parallel-request TC (create a sub-category and deactivate its parent): exactly one succeeds.",
      "adr": false
    },
    {
      "id": "G5", "severity": "MINOR", "section": "backend", "artifact": "module-registry", "line": null,
      "clause": "platform findings (section 7)",
      "problem": "CORE-GAP-in-process-authorization is an obligation on erp-core. ADR-CAT-061 depends on it, but it is not recorded as an OPEN row in the platform-findings category. Only the ADR folder holds it, so the owner has no row to act on.",
      "fix": "Add an OPEN platform finding. Owner: erp-core. Evidence: checklist C13 and F9, ADR-CAT-061, ADR-CAT-066 and the CORE-GAP file. Name the three options that file proposes.",
      "adr": false
    },
    {
      "id": "G6", "severity": "MINOR", "section": "tests", "artifact": "backend-test-plan", "line": null,
      "clause": "consistent",
      "problem": "The backend test plan header lists the frontend TC range as 125–182 and 194–196. The frontend plan also owns TC-CAT-208 and 209. ADR-CAT-052 still quotes the pre-revise counts '124 of 129 ACs' and '52 ACs', while the SRS now has 152.",
      "fix": "Add TC-CAT-208 and 209 to the backend header's frontend range. Add a note to ADR-CAT-052 that its AC counts are superseded by the revised totals (146 backend, 152 combined).",
      "adr": false
    },
    {
      "id": "G7", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
      "clause": "feasible (API-CAT-049)",
      "problem": "The import is synchronous: a validation pass over up to 5,000 rows, then a write pass with a savepoint per row, one code allocation per Item and Variant, and one barcode and name check per row, all in one transaction. No time budget, batch size or request timeout is stated, and a long transaction holds the scale-prefix lock for GTIN rows.",
      "fix": "State a target duration and the request timeout for 5,000 rows. Allocate codes in a batch. Take the scale-prefix lock once per import, not once per row. Name the expected behaviour if the timeout is exceeded.",
      "adr": false
    },
    {
      "id": "G8", "severity": "MINOR", "section": "tests", "artifact": "backend-test-plan", "line": null,
      "clause": "verifiable (P1: a counter-case with a statement but no test)",
      "problem": "RULE-CAT-053 is enforced on 10 endpoints (API-CAT-003, 006, 008, 010, 012, 014, 015, 017, 018, 019). Only TC-CAT-198 (unit add) and the frontend TC-CAT-209 cover it. The other endpoints are delivered without that check being tested.",
      "fix": "Add one parameterised TC that calls each of the listed endpoints on an inactive Item or Variant and expects CAT-422-PARENT-INACTIVE. Also assert that the child deactivations still succeed.",
      "adr": false
    },
    {
      "id": "G9", "severity": "MINOR", "section": "backend", "artifact": "api-spec", "line": null,
      "clause": "consistent (C7.29–C7.31; API-CAT-005)",
      "problem": "API-CAT-005 states that exactly one of isActiveFl or publishStateCode is sent, and that isActiveFl accepts only true. The ItemStateRequest schema encodes neither rule, so the mock server accepts both fields, an empty body, or isActiveFl false.",
      "fix": "In the plan, mark `isActiveFl` as enum [true] and add `oneOf` or `minProperties: 1, maxProperties: 1` to ItemStateRequest. Regenerate the document from the plan.",
      "adr": false
    }
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-025", "status_ok": true, "note": "SUPERSEDED by ADR-CAT-039 and SRS D-04. Consistent."},
    {"id": "ADR-CAT-026", "status_ok": true, "note": "SUPERSEDED by ADR-CAT-042. Consistent."},
    {"id": "ADR-CAT-028", "status_ok": true, "note": "SUPERSEDED by RULE-CAT-001. Consistent."},
    {"id": "ADR-CAT-044", "status_ok": true, "note": "Six PLATFORM-STD rows with ar and en text, amended for checklist C11."},
    {"id": "ADR-CAT-045", "status_ok": true, "note": "Partly superseded by ADR-CAT-061. The header says so. Sections 2, 3 and 5 stand."},
    {"id": "ADR-CAT-047", "status_ok": false, "note": "The lock set is incomplete for Category deactivate against sub-category create (G4). Amend it."},
    {"id": "ADR-CAT-052", "status_ok": true, "note": "Counts are stale after the revise (G6). Add a supersession note."},
    {"id": "ADR-CAT-054", "status_ok": true, "note": "SUPERSEDED by ADR-CAT-055. Consistent."},
    {"id": "ADR-CAT-055", "status_ok": true, "note": "Matches checklist C1. The factory exemption is an OPEN platform finding, legitimately owned by gov.py."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "Conditional on the core's answer. The checklist's own route for a gap is an ADR plus a CORE-GAP file, which exists. The platform-finding row is missing (G5)."},
    {"id": "ADR-CAT-063", "status_ok": false, "note": "The rule it introduces creates the dead ends of G1. Amend it with the repair-operation exemption."},
    {"id": "ADR-CAT-064", "status_ok": true, "note": "Consistent with SRS, plan, api-spec and TC-CAT-206."},
    {"id": "ADR-CAT-065", "status_ok": true, "note": "Follows checklist C6. API-CAT-052 and the given-usage row are consistent with the platform finding 4."},
    {"id": "ADR-CAT-066", "status_ok": true, "note": "The core gap is OPEN, not BLOCKED. This does not force ESCALATE."},
    {"id": "ADR-CAT-067", "status_ok": true, "note": "All 13 intra-module FKs in the DDL are tenant-composite. Matches D-P2-12."}
  ],
  "verdict": "REVISE"
}
```