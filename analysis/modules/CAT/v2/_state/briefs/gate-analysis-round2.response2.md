DECISION: Verdict is ESCALATE because findings repeat
The previous gate pass (`gate-analysis.result.json`) returned REVISE. These findings from that pass come back with no change in the artifacts:
- tenant FK and index (now G1)
- multipart endpoints (G2)
- the module's own xlsx writer (G3)
- native queries without TENANT_ID (G4)
- the C13 permission split (G5)
- rules not on a Domain companion (G13)
- inactive Variants still selectable (G10)
- the Category deactivation race (G14)
- unmarked superseded ADRs (G22)

D-P2-03 is still in the db-script, API-CAT-018 and API-CAT-049 are still multipart, and QR-CAT-006/008/009/037 still leave the tenant filter implied. The REVISE budget of 1 is used up. A repeated finding forces ESCALATE under QUALITY-RUBRIC §4, whether or not a CRITICAL finding exists. Round 1 reached ESCALATE without giving this reason; this decision records it.

DECISION: Checklist lines confirmed word for word
I read `.claude/skills/erp-core-usage@1.6.0/templates/checklist.md`. Lines C1, C3, C7, C10, C12, C13, C15–C20, F2, F4, F5, F7 and F9 exist as quoted. G1–G5 and G13 stay MAJOR, as §3.6 requires.

DECISION: Check-digit corrections confirmed (G8)
I recomputed the GS1 check digits:

| Value | Digits weighted 1/3/1/3… | Sum | Correct check digit | Correct value |
|---|---|---|---|---|
| 200123001234x | 2+0+0+3+2+9+0+0+1+6+3+12 | 38 | 2 | 2001230012342 |
| 040000000042x | | 22 | 8 | 0400000000428 |
| 200012301234x | | 34 | 6 | 2000123012346 |

These GTINs are valid as written: 6223000000014, 2000000000008, 16223000000011 and 6229999999995.

DECISION: C10.8 / C10.9 reported nothing, and that hides a contradiction (G21 becomes MAJOR)
ADR-CAT-053 dropped BAG and H87 because "no named add-value call exists". Yet TC-CAT-037, 068, 137 and 139 still create CAT_BRAND values through that same unnamed mdl maintenance call. Either these TCs break C10.8, or ADR-CAT-053's reasoning is wrong. The previous gate's G12 asked for a CORE-GAP ADR, as checklist line F9 requires. Round 1 dropped that request, and I restore it in G15.

DECISION: Round-1 scores and the remaining findings are accepted
Each finding was checked against the artifacts:
- G6 (reactivation gaps, extended to bundle cases)
- G7
- G9
- G10 (extended to writes: API-CAT-006, 010, 014, 015 and 018 never check that the parent is active)
- G11 (AC-CAT-040 truncates the {reason} text)
- G12
- G14
- G16–G20

```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": { "unambiguous": 2, "verifiable": 2, "complete": 1, "consistent": 1, "singular": 3, "feasible": 1, "traceable": 3 },
  "extra_checks": [
    {"id": "CM-1", "result": "PASS"}, {"id": "CM-2", "result": "PASS"}, {"id": "CM-3", "result": "PASS"}, {"id": "CM-4", "result": "PASS"}, {"id": "CM-5", "result": "PASS"},
    {"id": "CM-6", "result": "PASS"}, {"id": "CM-7", "result": "PASS"}, {"id": "CM-8", "result": "PASS"}, {"id": "CM-9", "result": "PASS"}, {"id": "CM-10", "result": "PASS"}
  ],
  "analyze_confirmed": [
    {"finding": "C5.13/srs:404 AC-CAT-010 'fast'", "confirmed": false, "note": "False positive. The word is part of the test data \"Fast charger 25W\"."},
    {"finding": "C5.13/srs:446 AC-CAT-013 'could'", "confirmed": false, "note": "False positive. The word is inside the quoted RULE-CAT-009 message."},
    {"finding": "C5.13/srs:762 AC-CAT-036 'could'", "confirmed": false, "note": "False positive. The word is inside the quoted RULE-CAT-005 message."},
    {"finding": "C5.13/srs:1260 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive. The word is inside the quoted RULE-CAT-024 message."},
    {"finding": "C5.13/srs:1680 AC-CAT-102 'could'", "confirmed": false, "note": "False positive. The word is inside the quoted RULE-CAT-035 message."},
    {"finding": "C5.14/srs:348 AC-CAT-006", "confirmed": false, "note": "False positive. The outcome is a boolean plus an enumerated code, and TC-CAT-002 asserts both."},
    {"finding": "C5.14/srs:1190 AC-CAT-067", "confirmed": false, "note": "False positive. The outcome is checkable, and TC-CAT-097 checks it."},
    {"finding": "C5.14/srs:1792 AC-CAT-110", "confirmed": false, "note": "False positive. The boolean outcome is asserted by TC-CAT-055."},
    {"finding": "coverage: C10.8 and C10.9 examined nothing", "confirmed": true, "note": "These clauses are not empty by nature. TC-CAT-037, 068, 137 and 139 carry Host data lines that create unseeded CAT_BRAND values through the mdl add-value call, which ADR-CAT-053 itself says has no verb or path. The checks enforced nothing on this run. See G21."},
    {"finding": "coverage: C6.3, C6.10, C6.12, C6.15, C7.5, C7.5b, C7.24, C7.26, C7.27, C7.28", "confirmed": true, "note": "Empty by nature: the XM register is empty, CAT is a root module, and there are no patches."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "db-script", "line": null, "clause": "\"C1 Every table has TENANT_ID BIGINT NOT NULL, FK_<TABLE>_TENANT → CORE_TENANT(ID), an index led by TENANT_ID, every UNIQUE composite with TENANT_ID\"", "problem": "D-P2-03 leaves out FK_<TABLE>_TENANT → CORE_TENANT on all 12 tables. CAT_ITEM and CAT_CATEGORY have no plain index led by TENANT_ID. The previous gate raised this, and it is unchanged.", "fix": "Reverse D-P2-03. In BLOCK 5d add FK_CAT_<TABLE>_TENANT → CORE_TENANT(ID) for all 12 tables. Add IDX_<TABLE>_TENANT_ID wherever no non-partial UNIQUE leads with TENANT_ID. Update registry-db and the manifest.", "adr": false},
    {"id": "G2", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "\"F4 ... or a multipart endpoint of the module\" / \"C7 Every byte goes through FilePrivateStoreApi / FileImageStoreApi / the FILE endpoints\"", "problem": "API-CAT-018 and API-CAT-049 are multipart endpoints of CAT itself. This repeats a finding of the previous gate.", "fix": "Upload the bytes through the given FILE endpoints. Change API-CAT-018 and API-CAT-049 to JSON bodies carrying fileDocumentRef. Drop the CAT-413 and CAT-415 rows. Update the api-spec, ADR-CAT-044, ADR-CAT-048 §4, ADR-CAT-049 step 8, F2/F4 of SCR-CAT-001 and SCR-CAT-004, and TC-CAT-056, 113, 146, 165 and 166.", "adr": true},
    {"id": "G3", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "\"F7 A second export path: a CSV / XLSX / PDF writer\" / \"C10 Every list export / tabular report is a ReportProvider bean\"", "problem": "API-CAT-048 builds the import xlsx in CatItemImportService.buildSheet. This repeats a finding of the previous gate.", "fix": "Produce the sheet from CatItemImportSheetReportProvider through the given report path. State whether API-CAT-048 is kept or retired. Align the api-spec, TC-CAT-082 and TC-CAT-164.", "adr": true},
    {"id": "G4", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "\"C3 Every native query / JdbcTemplate statement names TENANT_ID and its method carries @TenantUnscoped(reason, adr)\" / \"F2\"", "problem": "The native queries QR-CAT-006, 008, 009 and 037, and the advisory-lock keys, leave TENANT_ID implied. None carries @TenantUnscoped. This is one rule-level gap, and it repeats a finding of the previous gate.", "fix": "Write AND TENANT_ID = :tenant in every native spec and every recursive CTE branch. Add to ADR-CAT-047 that each native method carries @TenantUnscoped(reason, adr=\"ADR-CAT-047\") and that every advisory-lock key takes the tenant from TenantContext.", "adr": false},
    {"id": "G5", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "\"C13 ... every service method has @PreAuthorize(...PERM_…) referencing its own module only\"", "problem": "The same service methods serve both the PERM-guarded HTTP endpoints and CatalogueReadApi, whose callers (CUSTOMER-realm shoppers, INV, PUR, SLS, PRC) hold no CAT permissions. The plan does not say which way it goes. This repeats a finding of the previous gate.", "fix": "Put @PreAuthorize on the services behind HTTP. Add a CatalogueReadService for CatalogueReadApi with tenant-scoped reads and no PERM check, keeping the REQ-CAT-127 check inside searchCatalogue. State the split per method. Add a TC for a CUSTOMER-realm storefront read.", "adr": true},
    {"id": "G6", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null, "clause": "P1 silent path: reactivation (REQ-CAT-006, RULE-CAT-015/021/025/026, RULE-CAT-008)", "problem": "Nothing refuses reactivating an Item that has no active Variant, whose Category is inactive, that is a Bundle with an inactive component, or that is a BUNDLE while `bundles` is off.", "fix": "Add an unwanted REQ and RULE for reactivation, with the codes CAT-422-REACTIVATE-NO-VARIANT, CAT-422-CATEGORY-INACTIVE, CAT-422-BUNDLE-COMPONENT-INVALID and CAT-422-CAPABILITY-OFF. Check them under the Item lock in API-CAT-005. Add the api-spec rows and the TCs.", "adr": false},
    {"id": "G7", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null, "clause": "P2 closure: withdrawing a record something depends on (RULE-CAT-031, REQ-CAT-079)", "problem": "API-CAT-013 deactivates an Item unit while active barcodes are bound to it, so API-CAT-043 returns an inactive unit.", "fix": "Refuse with CAT-409-UNIT-IN-USE-BY-BARCODE ({count}) under the Item lock. Add a count QR, a REQ, an AC and a TC.", "adr": false},
    {"id": "G8", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null, "clause": "consistent / verifiable (RULE-CAT-035, D-05)", "problem": "Wrong check digits. AC-CAT-099 and TC-CAT-110 use 2001230012347; the correct digit is 2. AC-CAT-086 and TC-CAT-104 use 0400000000420; the correct digit is 8. A conforming implementation fails both TCs. AC-CAT-096 uses 2000123012347; the correct digit is 6.", "fix": "Correct the ACs, the TCs and every reference to 2001230012342, 0400000000428 and 2000123012346.", "adr": false},
    {"id": "G9", "severity": "MAJOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null, "clause": "P3 read binding: every foreign id a screen renders resolves to a label", "problem": "categoryFk is labelled from the active-only tree, so an inactive Category renders as an unresolved id.", "fix": "Read options from the active tree and labels from the unfiltered tree (or add categoryNameAr and categoryNameEn to ItemSummary and ItemDetail). Update F2 and add a TC.", "adr": false},
    {"id": "G10", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "P4 pattern: RULE-CAT-048 \"exclude from new selections\" does not reach the children of an inactive parent", "problem": "Variants of an inactive Item stay selectable through QR-CAT-016, 017 and 052. Writes under an inactive Item or Variant are accepted by API-CAT-006, 010, 014, 015 and 018. This repeats a finding of the previous gate.", "fix": "Define once in PHASE CORE that a selectable Variant has both its own and its Item's isActiveFl true. Refuse writes under an inactive parent with CAT-409-PARENT-INACTIVE through a new REQ and RULE. Apply this to the three QRs and the five writes. Add ACs and TCs.", "adr": false},
    {"id": "G11", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null, "clause": "consistent (AC-CAT-040 against RULE-CAT-045 / RULE-CAT-001)", "problem": "AC-CAT-040 and TC-CAT-084 assert a truncated {reason} in place of the full RULE-CAT-001 message that RULE-CAT-045 prescribes.", "fix": "Put the full RULE-CAT-001 message as the reason in AC-CAT-040 and TC-CAT-084.", "adr": false},
    {"id": "G12", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null, "clause": "P1 silent path: import row with an unknown Category code (RULE-CAT-026/045)", "problem": "An unknown category code on an import row matches no rule. Several import counter-cases have no TC.", "fix": "State in RULE-CAT-045 that an unknown or inactive code rejects the row for column category with the RULE-CAT-026 message. Add TCs for this case and for the check digit, an in-file duplicate barcode, the scale prefix and the e-invoice pair.", "adr": false},
    {"id": "G13", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "\"C12 Business rules live on the entity's Domain companion (create() / from()), the service orchestrates\"", "problem": "PHASE CORE places every RULE in the service layer. This repeats a finding of the previous gate.", "fix": "Move each RULE into an <Entity>Domain companion. The service keeps locks, lookups, number series and counts, and passes them in. Update PHASE CORE and each RULE's layer field.", "adr": false},
    {"id": "G14", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "concurrency: a named guard for every read-then-write decision (API-CAT-021/023 against API-CAT-024; API-CAT-049)", "problem": "Category create or move and Category deactivation do not share a guard, so an active child can end up under an inactive parent. The API-CAT-049 validation pass reads Categories outside the write transaction. This repeats a finding of the previous gate.", "fix": "Make API-CAT-024 take the tree lock, or read the parent FOR SHARE in API-CAT-021 and 023. Re-read the Category FOR SHARE in the API-CAT-049 write pass. Update ADR-CAT-047 and add a TC.", "adr": false},
    {"id": "G15", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "§7 platform findings / \"F9 A workaround for a missing core capability instead of CORE-GAP-<topic>.md\"", "problem": "Four gaps are left with nobody to settle them, and none is recorded as a platform finding or CORE-GAP. (1) The flag owner (P-02) is open, so the CON-CAT-017 caller does not exist and POL-CAT-005 is not delivered. (2) The global-search host is unowned. (3) The setup-wizard host is unowned. (4) The mdl add-value call has no verb or path, yet ADR-CAT-052 §3 and the TCs rely on it.", "fix": "Record platform findings (1)–(3) with evidence IDs and named owners. File CORE-GAP-lookup-value-maintenance.md for (4) and cite it from every Host data line.", "adr": true},
    {"id": "G16", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null, "clause": "P2 edge (RULE-CAT-033/034)", "problem": "API-CAT-033 can change scaleCodeLength while active SCALE barcodes of the old length exist.", "fix": "Refuse the change while such barcodes exist. Add a catalog row and a TC.", "adr": false},
    {"id": "G17", "severity": "MINOR", "section": "tests", "artifact": "backend-test-plan", "line": null, "clause": "P1: stated behaviour with no test case", "problem": "No TC covers: stale version, a cross-tenant read answered as 404, the 401 and 403 refusals, any ADR-CAT-047 concurrency guard, RULE-CAT-021 on Item deactivation, the depth limit of 5, or each (rule, endpoint) pair.", "fix": "Add these TCs, plus the TCs for G6, G7, G10, G12 and G14.", "adr": false},
    {"id": "G18", "severity": "MINOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null, "clause": "C10.7 msg-bound; F2 error-routing grammar", "problem": "Eight routed codes have no `text:` binding, so ADR-CAT-053 weakened eight frontend TCs to assert routing only.", "fix": "Rewrite the eight F2 rows as `text: RULE-CAT-0nn message` and restore the word assertions in TC-CAT-130, 143, 144, 145, 150, 157, 165 and 171.", "adr": false},
    {"id": "G19", "severity": "MINOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null, "clause": "consistent (Arabic-letter definition)", "problem": "The F3 validator accepts U+0600–U+06FF, but the DB CHECK uses [\\u0621-\\u064A].", "fix": "Define one Arabic-letter set in RULE-CAT-001 and use it in all three layers.", "adr": false},
    {"id": "G20", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null, "clause": "\"C15\" / \"C16\" / \"C18\" / \"C19\" / \"C20\"; QR VERSION = :version on DELETE", "problem": "The plan gives no packaging evidence for these lines, and the soft-delete QRs filter on VERSION although DELETE requests carry no version.", "fix": "Add a 'Module packaging' block to PHASE CORE. State that deactivation uses the version of the row read under lock.", "adr": false},
    {"id": "G21", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null, "clause": "C10.8 tc-data / C10.9 tc-consistency examined nothing; consistency with ADR-CAT-053", "problem": "Both checks examined 0 subjects. Meanwhile TC-CAT-037, 068, 137 and 139 keep Host data lines that use the same unnamed mdl call ADR-CAT-053 rejected for BAG and H87. The plans therefore contradict their own ADR.", "fix": "Fix the parser or the line format so that C10.8 and C10.9 examine these subjects. Either bind the four TCs to the CORE-GAP of G15(4) or replace the host values. Re-run the analysis.", "adr": false},
    {"id": "G22", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null, "clause": "§7 decisions: ADR status / \"F5 ... a settings table\"", "problem": "ADR-CAT-025, 026 and 028 are contradicted by later decisions but not marked. ADR-CAT-042 lacks a justification against F5. This repeats a finding of the previous gate.", "fix": "Mark ADR-CAT-025, 026 and 028 SUPERSEDED with back-references. Add to ADR-CAT-042 why the config tables are reference data and not a settings table.", "adr": true}
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-025", "status_ok": false, "note": "Superseded by D-04 and CAT_ITEM_KIND but not marked (G22)."},
    {"id": "ADR-CAT-026", "status_ok": false, "note": "Superseded by ADR-CAT-042 but not marked (G22)."},
    {"id": "ADR-CAT-028", "status_ok": false, "note": "Superseded by ADR-CAT-031 but not marked (G22)."},
    {"id": "ADR-CAT-038", "status_ok": true, "note": "The orchestrator must mark the open items as applied."},
    {"id": "ADR-CAT-039", "status_ok": true, "note": ""},
    {"id": "ADR-CAT-040", "status_ok": true, "note": ""},
    {"id": "ADR-CAT-041", "status_ok": false, "note": "The flag owner is open, so the turn-off check has no caller (G15)."},
    {"id": "ADR-CAT-042", "status_ok": false, "note": "Needs a justification against F5 (G22)."},
    {"id": "ADR-CAT-043", "status_ok": true, "note": "The search host needs a platform finding (G15)."},
    {"id": "ADR-CAT-044", "status_ok": false, "note": "The 413 and 415 rows go with G2."},
    {"id": "ADR-CAT-045", "status_ok": false, "note": "Does not settle the C13 split (G5)."},
    {"id": "ADR-CAT-046", "status_ok": true, "note": ""},
    {"id": "ADR-CAT-047", "status_ok": false, "note": "G4 and G14."},
    {"id": "ADR-CAT-048", "status_ok": false, "note": "§4 conflicts with F4 and F7 (G2, G3)."},
    {"id": "ADR-CAT-049", "status_ok": true, "note": "Step 8 changes with G2."},
    {"id": "ADR-CAT-050", "status_ok": true, "note": ""},
    {"id": "ADR-CAT-051", "status_ok": true, "note": "Add the unfiltered Category label read (G9)."},
    {"id": "ADR-CAT-052", "status_ok": false, "note": "§3 relies on an mdl add-value call that has no verb or path. It needs the CORE-GAP of G15(4)."},
    {"id": "ADR-CAT-053", "status_ok": false, "note": "Records a P3.2 plan gap as a P4 decision (G18). Its host-data reasoning is applied to BAG and H87 but not to the CAT_BRAND values (G21)."}
  ],
  "verdict": "ESCALATE"
}
```

<!-- CONVERGED -->