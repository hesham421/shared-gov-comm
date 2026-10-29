DECISION: Previous-round scores confirmed
Every attribute stays at 2, and traceable stays at 3. Traceability holds at 148/148 REQ, 174/174 AC, 56/56 API and 53/53 RULE. The open MAJORs can be fixed inside the analysis lane, so no attribute falls below 2.

DECISION: All four C5.13 analyze findings are false positives
I checked each flagged word. Every one sits inside a quoted RULE message (RULE-CAT-009, 005, 024 and 035), not inside the criterion's own measure. The ten clauses that examined nothing are empty by nature: the module has no cross-module (XM) records and no patch or foreign subjects.

DECISION: G1 stays MAJOR and becomes more specific
On API-CAT-017 the failure is certain, not just possible. Hibernate always flushes inserts before updates, whatever order the service calls them in. So when a PUT drops a line and adds back the same componentVariantFk without a bundleComponentPk, the insert reaches the database before the deactivation. It hits UQ_CAT_BUNDLE_COMP_VARIANT, and a valid request is refused as RULE-CAT-019. API-CAT-019 and API-CAT-020 have the same exposure on UQ_CAT_ITEM_MEDIA_PRIMARY when updates are reordered. The fix is one rule at the level of ADR-CAT-047 and PHASE CORE: save and flush the row that clears the unique value before saving the row that sets it.

DECISION: G3 stays MAJOR and gets a concrete producer gap
The bootstrap row says CAT_ITEM_IMAGE is "registered for the PLATFORM tenant at deployment". No db-script BLOCK 8 statement seeds FILE_CATEGORY, and no supported mechanism exists yet (CORE-GAP question 3). So the image path has no producer, but it is not flagged as core-blocked the way the import is.

DECISION: G4 is widened into a pattern finding (probe P4)
The defaults problem has the same cause as a second one. CAT seeds closed, behaviour-driving keys into mdl, where a tenant can edit them: CAT_ITEM_KIND, CAT_PUBLISH_STATE, CAT_BARCODE_TYPE, CAT_SCALE_VALUE_TYPE and CAT_EINVOICE_ITEM_CODE_TYPE. The service also hard-codes some of their codes (PUBLISHED, BUNDLE, GTIN, INTERNAL, SCALE in QR-CAT-007/009/016/047/053). A tenant that deactivates PUBLISHED or GTIN breaks publish or GTIN entry through RULE-CAT-006. The fix belongs in RULE-CAT-006 and needs an ADR.

DECISION: G2, G5, G6 and G7 confirmed as MINOR
API-CAT-004's only TCs are 001, 188, 190 and 197, so the Bundle refusal branch is untested. G5, G6 and G7 each check out against the artifacts as stated.

DECISION: Given checklist re-read line by line
No new violation:
- C3 and F2: all six native queries name TENANT_ID and carry @TenantUnscoped.
- C5 / F3 and C13: the deviations are filed as CORE-GAPs under F9.
- C6, C7, C9, C10, C11, C15, C16, C18, C19 and C20 hold.
- C14 holds: the capability flags are platform keys, not CAT_* settings.

DECISION: Verdict REVISE
G1 and G3 are MAJOR and fixable in the backend and test plans. There is no CRITICAL finding, no BLOCKED ADR and no extra check failing above MINOR.

```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": { "unambiguous": 2, "verifiable": 2, "complete": 2, "consistent": 2, "singular": 2, "feasible": 2, "traceable": 3 },
  "extra_checks": [
    {"id": "CM-1", "result": "PASS"}, {"id": "CM-2", "result": "PASS"}, {"id": "CM-3", "result": "PASS"},
    {"id": "CM-4", "result": "PASS"}, {"id": "CM-5", "result": "PASS"}, {"id": "CM-6", "result": "PASS"},
    {"id": "CM-7", "result": "PASS"}, {"id": "CM-8", "result": "PASS"}, {"id": "CM-9", "result": "PASS"},
    {"id": "CM-10", "result": "PASS"}
  ],
  "analyze_confirmed": [
    {"finding": "C5.13@srs:464 AC-CAT-013 'could'", "confirmed": false, "note": "False positive: the word is inside the verbatim RULE-CAT-009 message."},
    {"finding": "C5.13@srs:782 AC-CAT-036 'could'", "confirmed": false, "note": "False positive: the word is inside the verbatim RULE-CAT-005 message."},
    {"finding": "C5.13@srs:1317 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive: the word is inside the verbatim RULE-CAT-024 message."},
    {"finding": "C5.13@srs:1767 AC-CAT-102 'could'", "confirmed": false, "note": "False positive: the word is inside the verbatim RULE-CAT-035 message."},
    {"finding": "coverage: C6.10, C6.12, C6.15, C6.3, C7.24, C7.26, C7.27, C7.28, C7.5, C7.5b examined nothing", "confirmed": true, "note": "Empty by nature: the XM register is empty and there are no patch or foreign-scatter subjects. C6.15 is also covered by OPEN platform finding 1."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "feasible / consistent (ADR-CAT-047 guards; UQ_CAT_BUNDLE_COMP_VARIANT, UQ_CAT_ITEM_MEDIA_PRIMARY)",
     "problem": "Only the default-Variant move (API-CAT-006, 009; ADR-CAT-072) states 'clear, flush, set'. API-CAT-017 persists new lines, updates kept lines and then deactivates dropped lines. Hibernate flushes inserts before updates whatever the call order, so a PUT that drops a line and adds back the same componentVariantFk without a bundleComponentPk always inserts first. It violates UQ_CAT_BUNDLE_COMP_VARIANT, and a valid request is refused as RULE-CAT-019. API-CAT-019 ('clear the former primary first', no flush) and API-CAT-020 (clear the removed primary, then promote another) can reach UQ_CAT_ITEM_MEDIA_PRIMARY out of order when updates are reordered.",
     "fix": "Add one rule to PHASE CORE and amend ADR-CAT-047: a write that clears a value covered by a partial unique index saves and flushes the clearing row before saving the row that sets it. Apply it in API-CAT-017 (deactivate dropped lines, flush, update kept lines, insert new lines), API-CAT-019 (clear the former primary, flush, set the new one) and API-CAT-020 (clear the removed row, flush, promote). Name the flush in QR-CAT-041 and QR-CAT-057 as QR-CAT-075 already does.",
     "adr": false},
    {"id": "G2", "severity": "MINOR", "section": "tests", "artifact": "backend-test-plan", "line": null,
     "clause": "verifiable (probe P1: counter-case with a statement and no test)",
     "problem": "API-CAT-004 refuses CAT-409-VARIANT-IN-ACTIVE-BUNDLE when a Variant of the Item is a component of an active Bundle (RULE-CAT-021, REQ-CAT-062 'or the Item'). Its only TCs are 001, 188, 190 and 197, and TC-CAT-001 excludes this case. The G1 orderings are also untested.",
     "fix": "Add a TC that deactivates an Item whose Variant is a component of an active Bundle: 409 with {count}, and the Item stays active. Add a TC that replaces a Bundle line with the same component Variant, without bundleComponentPk, in one PUT: 200, exactly one active line. Extend TC-CAT-054 and TC-CAT-057 to assert that the primary swap and the promotion succeed under UQ_CAT_ITEM_MEDIA_PRIMARY.",
     "adr": false},
    {"id": "G3", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "feasible (undeclared assumption at the seam with the core)",
     "problem": "The bootstrap row says FILE category CAT_ITEM_IMAGE is 'registered for the PLATFORM tenant at deployment' and copied by CatTenantProvisioningContributor. No db-script BLOCK 8 statement seeds FILE_CATEGORY, and CORE-GAP-file-document-metadata-read question 3 still asks how a module registers one. API-CAT-018 and TC-CAT-055, 056, 113, 146, 181 and 218 therefore depend on a producer that does not exist. Only the import TCs are declared core-blocked.",
     "fix": "Choose one, and state it in PHASE CORE 'Core gaps' and in the bootstrap row: (a) seed FILE_CATEGORY CAT_ITEM_IMAGE in db-script BLOCK 8 with columns verified against erp-core 1.6.0, as D-P2-13 does for mdl and sequence, and have CatTenantProvisioningContributor copy it; or (b) declare the image path a delivery dependency on question 3. Under (b), list the image TCs in the test plan's 'Open ADRs' as blocked by the core gap, on the same terms as the import TCs.",
     "adr": false},
    {"id": "G4", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "complete (probe P4: system-semantic codes stored as tenant-editable mdl values)",
     "problem": "The service relies on mdl codes a tenant can deactivate: (a) the defaults PIECE and STANDARD of open keys (D-09; API-CAT-001, 049), which RULE-CAT-006 then refuses for values the caller never sent; (b) the closed keys CAT_ITEM_KIND, CAT_PUBLISH_STATE, CAT_BARCODE_TYPE, CAT_SCALE_VALUE_TYPE and CAT_EINVOICE_ITEM_CODE_TYPE, whose codes are hard-coded in QR-CAT-007, 009, 016, 047, 053 and in the services. Deactivating PUBLISHED or GTIN in mdl breaks publish or GTIN entry through RULE-CAT-006.",
     "fix": "Amend RULE-CAT-006 and record an ADR. Closed keys (open: false in SRS A6) are checked against their fixed seeded set, not against mdl active state. A default of an open key that is no longer active is not applied: the field becomes required, and RULE-CAT-006 names it. The screens pre-fill only an active default.",
     "adr": true},
    {"id": "G5", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "feasible (concurrency side effect)",
     "problem": "API-CAT-049 holds the tenant-wide scale-prefix lock (QR-CAT-074) for up to 120 s. Every GTIN, INTERNAL and label-screen barcode add (API-CAT-014, 015, 056) also waits on it. The plan mentions only layout creates.",
     "fix": "State the full effect in API-CAT-049 Concurrency and ADR-CAT-071. Either take the lock only around the write-pass GTIN re-check and insert, or bound the wait with lock_timeout, returning a retryable CAT-500 code with a next action.",
     "adr": false},
    {"id": "G6", "severity": "MINOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null,
     "clause": "complete (seam with RULE-CAT-015)",
     "problem": "ITEM-SAVE sends Variant deactivations (step 3) before generation (step 5). Deactivating an active Item's only active Variant while a generation is pending in the same Save gets CAT-409-LAST-ACTIVE-VARIANT. The plan does not say so.",
     "fix": "State in F2 ITEM-SAVE that, when a generation is pending, a Variant deactivation that would remove the last active Variant is sent after step 5. Otherwise the line shows the 'save-first' hint.",
     "adr": false},
    {"id": "G7", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "consistent (RULE-CAT-031 vs REQ-CAT-097 / API-CAT-043)",
     "problem": "RULE-CAT-031 lets a SCALE barcode be bound to any active Item unit, but scale decoding returns the embedded value 'in the Item's base unit'. A SCALE barcode on CARTON returns a value whose unit contradicts its own uomCode.",
     "fix": "Recommended: restrict SCALE barcodes to the Item's base unit (RULE-CAT-033), and add the refusal to API-CAT-014 with a TC. The SRS owns the choice.",
     "adr": true}
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-047", "status_ok": true, "note": "ACCEPTED. Needs the clear-flush-set amendment of G1."},
    {"id": "ADR-CAT-055", "status_ok": true, "note": "The tenant FK is a permitted given reference (checklist C1). Platform finding 1 stays OPEN with the factory."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "Conditional through ADR-CAT-066 and CORE-GAP-in-process-authorization (checklist F9)."},
    {"id": "ADR-CAT-070", "status_ok": true, "note": "The C5 / F3 deviation is justified and filed as a CORE-GAP."},
    {"id": "ADR-CAT-071", "status_ok": true, "note": "See G5."},
    {"id": "ADR-CAT-072", "status_ok": true, "note": "Sound. G1 generalises its ordering rule to the other unique-index writes."},
    {"id": "ADR-CAT-074", "status_ok": true, "note": "Consistent with the six native queries."},
    {"id": "ADR-CAT-076", "status_ok": true, "note": "Right direction. G3: the category registration has no producer yet."},
    {"id": "ADR-CAT-078", "status_ok": true, "note": "Covered by TC-CAT-231 and TC-CAT-232."},
    {"id": "ADR-CAT-079", "status_ok": true, "note": "Covered by TC-CAT-233 and TC-CAT-234."},
    {"id": "ADR-CAT-080", "status_ok": true, "note": "Consistent everywhere. G7 is the related SCALE unit gap."},
    {"id": "ADR-CAT-038 … 046, 048 … 060, 062 … 069, 073, 075, 077", "status_ok": true, "note": "ACCEPTED, none BLOCKED, honoured downstream. ADR-CAT-025, 026, 028 and 054 are correctly marked SUPERSEDED."},
    {"id": "CORE-GAP × 5 / platform findings 1–11", "status_ok": true, "note": "Each is a real gap outside the module, with evidence and a named owner. G3 adds evidence to the file-metadata gap (question 3)."}
  ],
  "verdict": "REVISE"
}
```

<!-- CONVERGED -->