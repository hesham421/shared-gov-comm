```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": {
    "unambiguous": 2,
    "verifiable": 2,
    "complete": 2,
    "consistent": 2,
    "singular": 2,
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
    {"finding": "C5.13 srs:453 AC-CAT-013 'could'", "confirmed": false, "note": "False positive. The word comes from the verbatim RULE-CAT-009 message. The AC's own measure is exact: no record is saved and the message is shown."},
    {"finding": "C5.13 srs:771 AC-CAT-036 'could'", "confirmed": false, "note": "False positive. The word comes from the verbatim RULE-CAT-005 message. The AC measures counts: 0 Items and 0 Variants."},
    {"finding": "C5.13 srs:1291 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive. The word comes from the verbatim RULE-CAT-024 message."},
    {"finding": "C5.13 srs:1731 AC-CAT-102 'could'", "confirmed": false, "note": "False positive. The word comes from the verbatim RULE-CAT-035 message."},
    {"finding": "10 clauses examined nothing (C6.3, C6.10, C6.12, C6.15, C7.5, C7.5b, C7.24, C7.26, C7.27, C7.28)", "confirmed": true, "note": "Empty by nature. The XM register is `records: []`, there is no consumes edge and there are no patches. C6.15 is empty because the only constraint on a given table, FK_*_TENANT, is not an XM. Platform finding 1 already asks for the explicit exemption."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null,
     "clause": "verifiable / P3 seam: TC-CAT-199, AC-CAT-145 vs API-CAT-007, QR-CAT-047",
     "problem": "Confirmed. TC-CAT-199 step 2 and AC-CAT-145 expect a read of the deactivated Variant V1 to return its barcode 6223000000014 with isActiveFl false. API-CAT-007 reads 'active barcodes (QR-CAT-047)', and QR-CAT-047 filters on IS_ACTIVE_FL, so no read exposes an inactive barcode. The cascade of ADR-CAT-063 cannot be observed at that step as written, and the TC asserts a response the API document never produces.",
     "fix": "Recommended: change AC-CAT-145 Then and TC-CAT-199 step 2 to 'V1 is returned with isActiveFl false and with no active barcode'. The release of the value stays proven by steps 3 and 4. Alternative: state in API-CAT-007 that an inactive Variant returns all its barcodes, using a QR-CAT-047 variant without the active filter, and update the VariantDetail description.",
     "adr": false},
    {"id": "G2", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null,
     "clause": "P1 counter-case with a statement and no test case: A7 invariant 4 / RULE-CAT-003 'on reactivation' / API-CAT-005 CAT-409-ITEM-NAME-DUPLICATE",
     "problem": "Confirmed. The reactivation guard on name uniqueness is stated in A7 invariant 4, in RULE-CAT-003 (trigger 'on reactivation'), in the Validations of API-CAT-005 and in its error list. REQ-CAT-130 and AC-CAT-132…134 cover only invariants 1 to 3. No AC or TC reactivates an Item whose normalised name another active Item took meanwhile. The RULE/code → TC index lists RULE-CAT-003 only for create (003, 203) and import (020).",
     "fix": "Extend REQ-CAT-130 to name RULE-CAT-003, and add AC-CAT-162: an inactive Item «كوب زجاج», then another active Item «كوب زجاج» ITM-…; reactivating the first is refused with the RULE-CAT-003 message naming the other Item's code, and the first Item stays inactive. Add backend TC-CAT-219 in the SVC-API-CRUD package. On the frontend, route CAT-409-ITEM-NAME-DUPLICATE in ITEM-STATE-COMMAND (already routed) and cover it with a frontend TC.",
     "adr": false},
    {"id": "G3", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "P2 closure (record withdrawn): isDefaultFl after the default Variant leaves the active set; CON-CAT-002 promise",
     "problem": "Confirmed. CON-CAT-002 promises consumers 'Every Item has at least one Variant, its default Variant (isDefaultFl true)'. API-CAT-009 allows deactivating the default Variant whenever another Variant is active. Generation on an Item with 0 active Variants (ADR-CAT-068, TC-CAT-185, TC-CAT-212) creates Variants with isDefaultFl false. UQ_CAT_VARIANT_DEFAULT is not partial on IS_ACTIVE_FL. As a result, an active Item can have no active default Variant, and nothing defines which Variant stock and price consumers fall back to. QR-CAT-013 'default first' then lists an arbitrary Variant first.",
     "fix": "Best practice, and option (a) of round 1: the default moves. State it in REQ-CAT-025 and as a sentence of A7. When the default Variant is deactivated (API-CAT-009), isDefaultFl moves in the same transaction, under the Item lock, to the remaining active Variant with the lowest code. When generation runs on an Item with 0 active Variants, the first created Variant takes isDefaultFl and the inactive former default loses it. Make UQ_CAT_VARIANT_DEFAULT partial on IS_DEFAULT_FL AND IS_ACTIVE_FL, or keep it as it is and clear the flag on the old row. Amend CON-CAT-002 to 'an active Item always has exactly one active default Variant'. Add an AC and a TC for each of the two moves. Record the choice in a new ADR.",
     "adr": true},
    {"id": "G4", "severity": "MINOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null,
     "clause": "P3 seam: cross-screen jump identifies one record set exactly",
     "problem": "Confirmed. 'Open in Items' goes to /cat/items?categoryFk=<pk>. API-CAT-038 filters on the exact CATEGORY_FK, while the drawer it comes from counts the active sub-tree (API-CAT-040, QR-CAT-006). For a Category with sub-categories, the two lists differ.",
     "fix": "Relabel the jump 'Items directly in this category / الأصناف المباشرة في هذه الفئة' in the ui-ux-spec and in F4 SCR-CAT-002. This needs no API change. Widening API-CAT-038 is left to a later delta.",
     "adr": false},
    {"id": "G5", "severity": "MINOR", "section": "frontend", "artifact": "ADR-CAT-051", "line": null,
     "clause": "consistent",
     "problem": "Confirmed. ADR-CAT-051 still says 51 operations and '2 of 51' unused. The plan, its registry and the API document hold 55 operations, 53 of them used.",
     "fix": "Add an 'Amended' note to ADR-CAT-051 with the current counts (55 operations, 53 used, API-CAT-011 and 046 unused), as ADR-CAT-052 did.",
     "adr": false},
    {"id": "G6", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "CM-1 / ADR-CAT-039",
     "problem": "Confirmed as a note. ENT-CAT-005, 007 and 008 are kind master but carry none of code, nameAr or nameEn. CM-1 passes only through the reading in ADR-CAT-039.",
     "fix": "Keep ADR-CAT-039. File a platform finding to the factory: the profile's entity_defaults should state that a dependent master takes its parent's identity defaults, so that CM-1 can be checked mechanically.",
     "adr": false},
    {"id": "G7", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "P2 closure: duplicates inside one import file; internal barcode clash",
     "problem": "Confirmed. API-CAT-049 checks barcode uniqueness against an in-file set, but it fills the {code} of RULE-CAT-029 only for a clash with a stored barcode. It never says what {code} holds when two rows of the file carry the same GTIN. API-CAT-015 answers CAT-409-BARCODE-DUPLICATE when a generated 04… value equals an existing GTIN, and states no retry.",
     "fix": "In API-CAT-049, state that a GTIN repeated in the file is rejected on the later row with the RULE-CAT-029 message, where {code} is the code the write pass gave the earlier row's Item (composed after the write pass, as is done for RULE-CAT-003). In API-CAT-015, state that on a clash one more number is drawn from CAT_INTERNAL_BARCODE before CAT-409-BARCODE-DUPLICATE is answered.",
     "adr": false},
    {"id": "G8", "severity": "MINOR", "section": "tests", "artifact": "srs", "line": null,
     "clause": "unambiguous",
     "problem": "Confirmed. AC-CAT-096 and TC-CAT-048 scan 2000123012346. Its embedded scale code is 00123, but the Variant in the precondition carries SCALE 01230. The miss would happen even with an active layout, so the case does not isolate the effect of deactivation.",
     "fix": "Change AC-CAT-096 and TC-CAT-048 to scan 2001230012342: prefix 20, code 01230, valid check digit, the value of AC-CAT-099. Only the inactive layout then explains the miss.",
     "adr": false},
    {"id": "G9", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "P2 closure: writes on an inactive config record",
     "problem": "New. API-CAT-036 refuses an update of an inactive Unit e-invoice code (CAT-400-REQUEST-INVALID). API-CAT-033 does not say whether an inactive scale label layout can be updated. Neither config entity has a reactivation. A wrong deactivation of a layout is only fixed by creating a new layout under the same key (UQ_CAT_SCALE_LABEL_LAYOUT_KEY is partial, so this works), but no statement says so.",
     "fix": "In API-CAT-033, state 'the layout must be active (otherwise CAT-400-REQUEST-INVALID)', to match API-CAT-036. Add to the ENT-CAT-011 and ENT-CAT-012 state machines: 'not reactivated; a new record is created under the same key, which the partial unique index allows'.",
     "adr": false}
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-044", "status_ok": true, "note": "Six PLATFORM-STD rows, each with ar and en texts and a bundle entry (C11). The 413 and 415 rows were removed with the multipart surface."},
    {"id": "ADR-CAT-045", "status_ok": true, "note": "§1 and §4 are superseded by ADR-CAT-061, as the status line states."},
    {"id": "ADR-CAT-047", "status_ok": true, "note": "Six native queries carry @TenantUnscoped. The advisory locks use the valid (int,int) signature. Lock order is consistent: tree → row, and Item → Variant → lines."},
    {"id": "ADR-CAT-051", "status_ok": true, "note": "The decision holds, but the counts are stale (G5)."},
    {"id": "ADR-CAT-053", "status_ok": true, "note": "The seeded substitutes keep each Given true. The note added at round 2 correctly says restoring BAG/H87 is optional."},
    {"id": "ADR-CAT-054", "status_ok": true, "note": "SUPERSEDED by ADR-CAT-055. The status is correct."},
    {"id": "ADR-CAT-055", "status_ok": true, "note": "Matches checklist C1 and conventions rule 5. The platform finding is filed with its owner named."},
    {"id": "ADR-CAT-056", "status_ok": true, "note": "No multipart endpoint (F4, C7). Retention is delegated to FILE."},
    {"id": "ADR-CAT-057", "status_ok": true, "note": "ReportProvider and PrintApi (F7, C10). No core gap is needed."},
    {"id": "ADR-CAT-059", "status_ok": true, "note": "Invariants 1–3 are tested. Invariant 4 is not (G2)."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "Conditional through ADR-CAT-066, with CORE-GAP-in-process-authorization raised (F9)."},
    {"id": "ADR-CAT-063", "status_ok": true, "note": "Amended by ADR-CAT-068. The barcode cascade is observable only indirectly (G1)."},
    {"id": "ADR-CAT-064", "status_ok": true, "note": "Length fixed after creation. Covered by TC-CAT-206."},
    {"id": "ADR-CAT-065", "status_ok": true, "note": "OwnedLookups with a fixed key set (C6). The manifest gap is an OPEN platform finding (row 4)."},
    {"id": "ADR-CAT-066", "status_ok": true, "note": "Makes ADR-CAT-061 conditional, as F9 requires."},
    {"id": "ADR-CAT-067", "status_ok": true, "note": "All 13 intra-module FKs are tenant-composite, with UNIQUE targets in place."},
    {"id": "ADR-CAT-068", "status_ok": true, "note": "Closes the dead ends. It creates the default-Variant gap of G3."},
    {"id": "ADR-CAT-069", "status_ok": true, "note": "Reactivation with guards and the tree lock. Covered by TC-CAT-213 to 216."},
    {"id": "ADR-CAT-070", "status_ok": true, "note": "Justified C5/F3 deviation. CORE-GAP-tenant-lookup-types is raised."},
    {"id": "ADR-CAT-071", "status_ok": true, "note": "Budget, timeout and whole-import rollback. Covered by TC-CAT-217. CORE-GAP-number-series-block-allocation is raised."}
  ],
  "verdict": "REVISE"
}
```

DECISION: G1 confirmed (MAJOR). The fix is to align AC-CAT-145 and TC-CAT-199 step 2 with the active-only barcode read. Widening the API is not the fix.
DECISION: G2 confirmed (MAJOR). Add an AC and TCs for the RULE-CAT-003 refusal on reactivation.
DECISION: G3 confirmed (MAJOR, adr: true). The default flag moves to an active Variant, and CON-CAT-002 is amended accordingly.
DECISION: G4–G8 confirmed as MINOR. G4 is fixed by relabelling, not by an API change.
DECISION: G9 added (MINOR). Inactive config records: refuse updates, and recreate under the same key instead of reactivating.
DECISION: The four C5.13 findings are false positives (verbatim RULE messages). The 10 empty clauses are empty by nature.
DECISION: All scores are kept and all CM checks pass. The verdict is REVISE, because MAJOR findings with lane-applicable fixes remain.

<!-- CONVERGED -->