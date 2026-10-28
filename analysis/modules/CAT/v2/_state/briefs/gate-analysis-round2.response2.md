```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": { "unambiguous": 2, "verifiable": 2, "complete": 1, "consistent": 1, "singular": 2, "feasible": 1, "traceable": 2 },
  "extra_checks": [
    {"id": "CM-1", "result": "PASS"}, {"id": "CM-2", "result": "PASS"}, {"id": "CM-3", "result": "PASS"},
    {"id": "CM-4", "result": "PASS"}, {"id": "CM-5", "result": "PASS"}, {"id": "CM-6", "result": "PASS"},
    {"id": "CM-7", "result": "PASS"}, {"id": "CM-8", "result": "PASS"}, {"id": "CM-9", "result": "PASS"},
    {"id": "CM-10", "result": "PASS"}
  ],
  "analyze_confirmed": [
    {"finding": "C5.13 AC-CAT-010 'fast'", "confirmed": true, "note": "Real. The word is part of the test value 'Fast charger 25W', but the clause rightly asks for literal values to be quoted so the scanner skips them. Fix: quote the value consistently, or use 'Charger 25W'."},
    {"finding": "C5.13 AC-CAT-013 'could'", "confirmed": false, "note": "False positive on inspection: AC-CAT-013's Then is 'no record is saved' plus the verbatim RULE-CAT-009 message, and the word 'could' sits inside that message. Same class as AC-CAT-072 and AC-CAT-102."},
    {"finding": "C5.13 AC-CAT-036 'could'", "confirmed": false, "note": "False positive: inside the verbatim RULE-CAT-005 message ('A new code could not be issued'). The Then names 0 Items and 0 Variants, which is measurable."},
    {"finding": "C5.13 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive: inside the verbatim RULE-CAT-024 message."},
    {"finding": "C5.13 AC-CAT-102 'could'", "confirmed": false, "note": "False positive: inside the verbatim RULE-CAT-035 message."},
    {"finding": "C5.14 AC-CAT-006", "confirmed": false, "note": "False positive: 'isActiveFl is true and publishStateCode is UNPUBLISHED' names two enumerable outcomes, and a test can fail it. The heuristic misses enumerated lookup codes."},
    {"finding": "C5.14 AC-CAT-067", "confirmed": false, "note": "False positive: an empty parentCategoryFk and categoryFk PH are checkable values. Adding a count would make it stronger but is not needed."},
    {"finding": "C5.14 AC-CAT-110", "confirmed": true, "note": "Weak but checkable. Add 'and exactly 1 active image of the Item has isPrimaryFl true' so the invariant itself is asserted."},
    {"finding": "Coverage: 12 clauses examined nothing", "confirmed": true, "note": "Empty by nature: C6.3, C6.10, C6.12, C6.15 (no XM), C7.5, C7.5b, C7.24, C7.26, C7.27, C7.28 (no XM, no patch, no foreign scatter). NOT empty by nature: C10.8 and C10.9. At least 10 TCs carry Host data and Test data lines (TC-CAT-019, 036, 037, 068, 137, 139, 063, 120–122, 175), so the parser is not finding its subject. This is a tooling defect (G15)."}
  ],
  "findings": [
    {"id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "db-script", "line": null,
     "clause": "\"C1 Every table has TENANT_ID BIGINT NOT NULL, FK_<TABLE>_TENANT → CORE_TENANT(ID), an index led by TENANT_ID, every UNIQUE composite with TENANT_ID\"",
     "problem": "REPEATED. ADR-CAT-054 removed all 12 FK_<TABLE>_TENANT constraints so that analyze C6.15 would pass. Given checklist C1 requires them. CORE_TENANT is a given table, not a business-module XM. The analysis followed its own mechanical clause instead of the library's line.",
     "fix": "Restore FK_<TABLE>_TENANT → CORE_TENANT(ID) on all 12 tables. Supersede ADR-CAT-054 with an ADR that records the given tenant table as a permitted given reference, not an XM. Update D-P2-03 and registry-db. File an OPEN platform finding (owner: factory / gov.py) saying C6.15 must exempt given tables named by checklist C1.", "adr": true},
    {"id": "G2", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "\"F4 ... or a multipart endpoint of the module\" / \"C7 Every byte goes through FilePrivateStoreApi / FileImageStoreApi / the FILE endpoints\"",
     "problem": "REPEATED. API-CAT-018 (image upload) and API-CAT-049 (item import) are multipart endpoints of CAT (multipart/form-data in api-spec), and ADR-CAT-048 fixes that shape.",
     "fix": "Make each a two-step flow. The client uploads through the given FILE endpoint and receives FILE_DOCUMENT.ID. API-CAT-018 and API-CAT-049 then take JSON carrying fileDocumentRef, and the service reads type, size and bytes through FileDocumentLookupApi. Update the plan, api-spec, ADR-048, F2 ITEM-SAVE step 8, IMPORT-SUBMIT, and TC-CAT-018, 055, 056, 113, 146, 165 and 166.", "adr": true},
    {"id": "G3", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "\"F7 A second export path: a CSV / XLSX / PDF writer\" / \"C10 Every list export / tabular report is a ReportProvider bean\"",
     "problem": "REPEATED. API-CAT-048 builds the xlsx import sheet in the module ('build the workbook in memory → stream it').",
     "fix": "Register the import sheet as a ReportProvider report (CAT_ITEM_IMPORT_SHEET) delivered through PrintApi and PrintResponses, the same way CAT_BARCODE_LABEL is. If the given cannot emit xlsx, raise CORE-GAP-xlsx-template and file an OPEN platform finding owned by erp-core report. Update API-CAT-048, Given usage and the bootstrap block.", "adr": true},
    {"id": "G4", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "\"C3 Every native query / JdbcTemplate statement names TENANT_ID and its method carries @TenantUnscoped(reason, adr)\" / \"F2\"",
     "problem": "REPEATED. QR-CAT-006, 008, 009 and 037 are native queries or recursive CTEs. The catalog writes the tenant filter 'once here and implied below', and no @TenantUnscoped is stated.",
     "fix": "Write TENANT_ID = :tenant explicitly in every native spec, including every sub-select and CTE branch (CAT_CATEGORY up/down, CAT_BARCODE, CAT_VARIANT). State @TenantUnscoped(reason, ADR-CAT-047) on each native method, and amend ADR-047.", "adr": false},
    {"id": "G5", "severity": "MAJOR", "section": "backend", "artifact": "db-script", "line": null,
     "clause": "P2 closure: record absent in tenants other than PLATFORM (lookups, number series, Unit e-invoice codes)",
     "problem": "REPEATED. BLOCK 8 seeds TENANT_ID = 1 only. The db-script defers other tenants to 'given provisioning contributors', ADR-046 §3 defers back to the db-script, and no Given usage row names a hook. D-P2-14 admits that the first save fails with SEQUENCE_NOT_CONFIGURED without the series. S0 assumes these rows exist in every test tenant.",
     "fix": "Name the given tenant-provisioning hook in SRS A9 Given usage and in the bootstrap block, with a CAT contributor that copies the 3 series and the 8 Unit e-invoice codes into each new tenant (and the lookup values, if mdl is not shared from PLATFORM). Add a TC that provisions a second tenant and creates an Item. If erp-core 1.6.0 has no hook, write CORE-GAP-tenant-seed-provisioning and an OPEN platform finding owned by erp-core tenant.", "adr": true},
    {"id": "G6", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "P1 silent path / P4 pattern: transitions into ACTIVE and reads of children do not re-establish Item invariants (REQ-CAT-006, 086, 101; RULE-CAT-015, 019, 026, 048)",
     "problem": "REPEATED. (a) RULE-CAT-015 guards only an active Item, so an inactive Item can lose every active Variant and then be reactivated with 0. (b) Reactivation does not re-check the Category, which RULE-CAT-025 lets go inactive while its Items are inactive. (c) A reactivated BUNDLE may hold inactive components. (d) QR-CAT-052 (lookup), QR-CAT-016 (label search) and QR-CAT-017 filter only v.IS_ACTIVE_FL, so a barcode of an inactive Item still resolves at the point of sale, against REQ-CAT-005 'leaves every channel'. This is one pattern: guards are written per trigger, not per state invariant.",
     "fix": "State once in A7 that every transition into ACTIVE re-establishes the Item invariants. Add to REQ-CAT-006 and API-CAT-005 (reactivate) checks for ≥ 1 active Variant, an active or empty Category, and active Bundle components, reusing CAT-422-PUBLISH-BLOCKED, CAT-422-CATEGORY-INACTIVE and CAT-422-BUNDLE-COMPONENT-INVALID or new codes. Add 'active Item' to REQ-CAT-086 / RULE-CAT-047 (QR-CAT-052) and REQ-CAT-101 (QR-CAT-016, 017). Add the ACs and TCs.", "adr": true},
    {"id": "G7", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "P2 closure: withdrawing a record that something depends on (RULE-CAT-031 against REQ-CAT-079)",
     "problem": "REPEATED. RULE-CAT-031 binds a barcode's unit to an active Item unit at create only. API-CAT-013 never checks active barcodes in that unit, so a CARTON barcode keeps resolving after CARTON is deactivated.",
     "fix": "Refuse deactivation of an Item unit that still has active barcodes. Add a new RULE and catalog code CAT-409-ITEM-UNIT-HAS-BARCODES with {count} and the next action 'deactivate those barcodes first'. Add the AC, the TC and the frontend routing.", "adr": true},
    {"id": "G8", "severity": "MAJOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "P1 counter-case of RULE-CAT-013 'exactly one value of each attribute used by the Item'",
     "problem": "A generation whose selections omit an attribute the Item already uses creates Variants without a value for that attribute. RULE-CAT-012 fills only newly used attributes, and API-CAT-006 checks only the limits.",
     "fix": "Add to RULE-CAT-013 and API-CAT-006 that every attribute already used by the Item must appear in selections with ≥ 1 value, otherwise CAT-422-VARIANT-ATTRIBUTE-MISSING (a new code). Mirror the check in the F3 generation validator. Add the AC and TC.", "adr": false},
    {"id": "G9", "severity": "MAJOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null,
     "clause": "P3 read binding: every foreign id a screen renders resolves to a label",
     "problem": "REPEATED. CATEGORY-OPTIONS-QUERY reads active Categories only, but the Items list with isActiveFl=false and the Entry of an inactive Item can render a categoryFk of an inactive Category (reachable through RULE-CAT-025). That label does not resolve.",
     "fix": "Label categoryFk from a read that includes inactive nodes (API-CAT-039 without the filter, or API-CAT-022 for one Item). Keep the active-only read for the selection control. State this in F2 and add a TC.", "adr": false},
    {"id": "G10", "severity": "MAJOR", "section": "tests", "artifact": "backend-test-plan", "line": null,
     "clause": "SEC-BE / CM-4 enforcement, tenant isolation (contract §0) and the optimistic lock are stated but untested",
     "problem": "REPEATED. No TC proves a Seller/cashier gets 403 on a mutation, that another tenant's id reads as 404, or that a stale version yields CAT-409-STALE-VERSION. The 'no AC states them' excuse does not hold: SEC-BE, contract §0 and ADR-044 state these behaviours explicitly.",
     "fix": "Add TCs for (1) a Seller/cashier refused on API-CAT-001, 003 and 004; (2) another tenant's itemPk reads and writes as 404; (3) a PUT with a stale version → 409 CAT-409-STALE-VERSION. Add a frontend permission-hidden TC for SCR-CAT-001 (Seller/cashier sees no Save or Deactivate). If a TC must derive from an AC, add the ACs to the SRS.", "adr": false},
    {"id": "G11", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "\"C18 The module ships META-INF/erp/modules/<code>.yaml\" / \"C20 ModuleBoundaryRules ... own CI job\" / \"C16 @Audited\" / \"C19 OutboxPublisher.publish(\"<code>.<Name>\", payload)\" / \"C15 V1000+ migrations\"",
     "problem": "REPEATED, with additions. The plan has no module manifest, no boundary test or CI job, and no @Audited list. The event is named 'CatalogueChanged' instead of '<code>.<Name>'. The db-script names no V1000+ migration file. The library requires proof for each.",
     "fix": "Add to PHASE CORE: the manifest (code cat, api-paths /api/v1/cat, permission-modules, dependencies none), a ModuleBoundaryRules test and CI line, @Audited(entityType = \"<TABLE>\") on ENT-CAT-001 … 012, and the event name 'cat.CatalogueChanged'. Name the db-script file V1000__cat_catalog.sql.", "adr": false},
    {"id": "G12", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "\"C12 Business rules live on the entity's Domain companion (create() / from()), the service orchestrates\"",
     "problem": "PHASE CORE states that every RULE is enforced in the service layer, and no Domain companion is planned.",
     "fix": "Move single-record rules to <Entity>Domain.create() / from(), for example RULE-CAT-001, 002, 004, 007, 022, 027 (format), 028, 030, 034 and 042. The service keeps orchestration, locks, queries and the cross-record rules. Name the companions in each entity block.", "adr": false},
    {"id": "G13", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "\"C13 ... every service method has @PreAuthorize(... PERM_…) referencing its own module only\" against ADR-CAT-045 CatalogueReadApi",
     "problem": "REPEATED. CatalogueReadApi delegates to the same secured service methods as HTTP (PERM_CAT_ITEMS_VIEW), but its consumers (SFR shoppers in the CUSTOMER realm, SLS, INV, PUR, TAX) hold no CAT permission. The in-process surface is either unusable or, if it bypasses the check, unspecified.",
     "fix": "Split each read into a controller-side @PreAuthorize facade and an unsecured read service that CatalogueReadApi also calls. State in ADR-045's successor that the calling module authorizes its own caller, and keep searchCatalogue as the one method that checks PERM_CAT_ITEMS_VIEW.", "adr": true},
    {"id": "G14", "severity": "MAJOR", "section": "backend", "artifact": "business-policies", "line": null,
     "clause": "Platform finding not recorded (§7): POL-CAT-005 against the OPEN flag owner (ADR-CAT-041, ADR-CAT-017)",
     "problem": "POL-CAT-005 says the turn-off shall be refused, but CAT offers only an advisory check, and no flag writer is obliged to call it. No OPEN platform-finding row exists.",
     "fix": "Record an OPEN platform finding (owner: erp-core cu / platform shell): a write of 'variants' or 'bundles' to off must call CatalogueReadApi.checkCapabilityTurnOff and refuse when allowed = false. Cite POL-CAT-005, CON-CAT-017 and REQ-CAT-022/023 as evidence. Add the obligation to contract §0.", "adr": false},
    {"id": "G15", "severity": "MINOR", "section": "tests", "artifact": "frontend-test-plan", "line": null,
     "clause": "C10.8 tc-data / C10.9 tc-consistency examined nothing",
     "problem": "Both clauses report 0 subjects although ≥ 10 TCs carry Host data lines, so the ADR-053 repair is unproven mechanically. Contra round 1, no value is demanded in opposite states: BRAND*_137 and OLDBRAND are distinct values in separate tenants (ADR-052 §4). The defect is the check's, not the plan's.",
     "fix": "File an OPEN platform finding (owner: gov.py) that the Host data parser finds no subject. Re-run analyze until C10.8 and C10.9 report a non-zero count before APPROVE.", "adr": false},
    {"id": "G16", "severity": "MAJOR", "section": "backend", "artifact": "api-spec", "line": null,
     "clause": "C7.29 api-spec-agree / consistent: plan Request against the OpenAPI document (API-CAT-001)",
     "problem": "The plan marks only nameAr as required. api-spec requires [nameAr, bundleComponents], and ADR-051 §6 works around this by sending []. A consumer that sends only nameAr (REQ-CAT-024, AC-CAT-025) gets 400.",
     "fix": "Make bundleComponents optional with default [] in api-spec; RULE-CAT-018 applies only when itemKindCode is BUNDLE. Drop ADR-051 §6's 'send []'. Re-run C7.29 so it compares required lists.", "adr": false},
    {"id": "G17", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "feasible: API-CAT-018 file lifecycle",
     "problem": "The file is stored before the media row. A later refusal or rollback leaves an orphan public file, and removal never deletes it.",
     "fix": "Resolved by G2 (the upload moves to the FILE endpoint and CAT holds only the reference). State the retention policy for files of deactivated media in the G2 ADR.", "adr": false},
    {"id": "G18", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
     "clause": "feasible: search performance and Arabic text",
     "problem": "ILIKE %text% over NAME_AR, NAME_EN and CODE has btree indexes only, and LIKE metacharacters are not escaped. Uniqueness and search ignore Arabic letter variants (أ/ا, ة/ه, diacritics).",
     "fix": "Add pg_trgm GIN indexes and escape '%' and '_' in QR-CAT-005, 008 and 016. Record an ADR on Arabic normalisation for RULE-CAT-003 and for search.", "adr": true},
    {"id": "G19", "severity": "MINOR", "section": "backend", "artifact": "business-policies", "line": null,
     "clause": "traceable (ADR traces)",
     "problem": "ADR-CAT-015 and ADR-028 trace POL-CAT-010 and POL-CAT-011, which do not exist (ADR-031).",
     "fix": "Remove POL-CAT-010 and POL-CAT-011 from the traces lines of ADR-015 and ADR-028.", "adr": false},
    {"id": "G20", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null,
     "clause": "singular / unambiguous: AC id headers",
     "problem": "The SRS carries the headings '#### AC-CAT-017 — reserved' and '#### AC-CAT-053 — reserved' as well as the real AC-CAT-017 and AC-CAT-053, so one id has two headings. A parser keyed on AC headings may bind the wrong block.",
     "fix": "Delete the two 'reserved' placeholder blocks, and drop the closing note about them.", "adr": false}
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-025", "status_ok": true, "note": "SUPERSEDED by ADR-039 / D-04 is correct."},
    {"id": "ADR-CAT-026", "status_ok": true, "note": "SUPERSEDED by ADR-042 is correct."},
    {"id": "ADR-CAT-028", "status_ok": true, "note": "SUPERSEDED by D-07 is correct, but its traces name the non-existent POL-CAT-010 (G19)."},
    {"id": "ADR-CAT-039", "status_ok": true, "note": "CM-1 passes on the strength of this ADR."},
    {"id": "ADR-CAT-041", "status_ok": true, "note": "Accepted. The flag owner is OPEN and no platform finding is recorded (G14)."},
    {"id": "ADR-CAT-044", "status_ok": true, "note": "Accepted. Its rows are untested (G10)."},
    {"id": "ADR-CAT-045", "status_ok": false, "note": "CatalogueReadApi has no authorization model (G13)."},
    {"id": "ADR-CAT-046", "status_ok": false, "note": "§3 is circular with the db-script, so seed provisioning is unowned (G5)."},
    {"id": "ADR-CAT-047", "status_ok": false, "note": "The locks are sound, but native queries lack explicit TENANT_ID and @TenantUnscoped (G4)."},
    {"id": "ADR-CAT-048", "status_ok": false, "note": "§3 and §4 fix a module-owned multipart endpoint and an xlsx writer (G2, G3)."},
    {"id": "ADR-CAT-051", "status_ok": false, "note": "§6 'send [] for bundleComponents' works around a spec inconsistency (G16)."},
    {"id": "ADR-CAT-052", "status_ok": true, "note": "Accepted."},
    {"id": "ADR-CAT-053", "status_ok": true, "note": "Accepted. The substituted seeded values keep each Given true, and the eight unbound texts are a recorded P3.2 gap."},
    {"id": "ADR-CAT-054", "status_ok": false, "note": "Contradicts given checklist C1 (G1). Supersede it."}
  ],
  "verdict": "ESCALATE"
}
```

DECISION: Repeated findings force ESCALATE
G1–G7, G9–G11 and G13 were raised in the earlier REVISE round. Commit e06c0a3 "REVISE applied" left them unchanged in the current state; ADR-CAT-054 even reversed G1. The rule in QUALITY-RUBRIC §4 is that a repeated finding means ESCALATE. I confirmed the checklist lines C1, C3, C7, C10, C12, C13 and F4/F7 against `.claude/skills/erp-core-usage@1.6.0/templates/checklist.md`.

DECISION: Analyze minors re-judged
AC-CAT-013, 036, 072 and 102 are false positives, because the flagged word sits inside a verbatim RULE message. AC-CAT-006 and 067 are false positives, because enumerated lookup codes and empty FKs are checkable. AC-CAT-010 and 110 are confirmed.

DECISION: G15 downgraded to MINOR and recast
Round 1's claim that host values are "demanded in opposite states" does not hold: these are distinct values in isolated tenants. What remains is a gov.py parser defect, which belongs in a platform finding.

DECISION: G11 extended
I added checklist C19 (event name `cat.CatalogueChanged`) and C15 (the V1000+ migration file name) to the manifest, CI and @Audited gap.

DECISION: G20 added
Two "reserved" AC headings duplicate real AC ids.

DECISION: Scores kept
complete 1 rests on G5–G8 and G10, consistent 1 on G1 and G16, and feasible 1 on G2, G3 and G13, because plans that break library conventions cannot be built on erp-core.

<!-- CONVERGED -->