```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": {
    "unambiguous": 2,
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
    {"finding": "C5.13 AC-CAT-013 'could'", "confirmed": false, "note": "False positive. The word is part of the quoted RULE-CAT-009 message ('could not be checked'). The Then clause is measurable: nothing is saved, and the message is given in full."},
    {"finding": "C5.13 AC-CAT-036 'could'", "confirmed": false, "note": "False positive. It is part of the quoted RULE-CAT-005 message ('could not be issued'). The measure is 0 Items and 0 Variants stored."},
    {"finding": "C5.13 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive. It sits inside the quoted RULE-CAT-024 message ('استخدم رمزًا أو اسمًا مختلفًا'). The measured outcome is that no Category is created."},
    {"finding": "C5.13 AC-CAT-102 'could'", "confirmed": false, "note": "False positive. It is part of the quoted RULE-CAT-035 message ('could not be read'). The measure is that no Variant is returned."},
    {"finding": "coverage: C6.10, C6.12, C6.15, C6.3, C7.24, C7.26, C7.27, C7.28, C7.5, C7.5b examined nothing", "confirmed": true, "note": "Empty by nature. The XM register is empty and CAT has no cross-module edge. C6.15 examined nothing only because the 12 given-tenant FKs are exempted implicitly (platform finding 1, OPEN with the factory)."}
  ],
  "findings": [
    {
      "id": "G1",
      "severity": "MAJOR",
      "section": "backend",
      "artifact": "backend-execution-plan",
      "line": null,
      "clause": "complete (P1 counter-case: a label printed with a barcode that does not identify the Variant in the labelled unit)",
      "problem": "REQ-CAT-102, QR-CAT-047 and QR-CAT-016 print and show the 'earliest-created active barcode' of a Variant whatever its type or unit. Counter-case 1: a Variant whose only active barcode is SCALE (a 4 to 6 digit code). RULE-CAT-037 counts it as 'carries a barcode', but the printed code can only be decoded inside a 13-digit scale label, so scanning the label finds nothing. Counter-case 2: the earliest barcode is bound to CARTON (uomCode CARTON). The printed piece label then sells a carton at the till. No statement or test covers either case.",
      "fix": "Do two things. (1) Choose the label barcode only among active GTIN and INTERNAL barcodes whose uomCode equals the Item's baseUomCode, earliest-created first. State this in REQ-CAT-102 and REQ-CAT-101, and in the QR-CAT-047 / QR-CAT-016 specs. (2) Make RULE-CAT-037 count only such barcodes, so a Variant with only a SCALE or non-base-unit barcode is flagged with the existing 'Generate internal barcode' next action (API-CAT-056). Add one AC (SRS) and one TC (backend plan) for each counter-case.",
      "adr": true
    },
    {
      "id": "G2",
      "severity": "MAJOR",
      "section": "backend",
      "artifact": "srs",
      "line": null,
      "clause": "unambiguous / complete (seam to INV and SLS through CON-CAT-006)",
      "problem": "ENT-CAT-007.quantity and CON-CAT-006 say the Bundle 'keeps no stock; consumers count stock on the component Variants', but the unit of the component quantity is not stated anywhere. It could be the component Item's base unit, any Item unit, or the unit of a barcode scanned. INV and SLS must explode the Bundle into component movements, so two implementers will read it two ways, and units with a conversion factor make the difference material.",
      "fix": "State in ENT-CAT-007, REQ-CAT-056 / REQ-CAT-058 and CON-CAT-006 that the quantity is always in the base unit of the component Variant's Item. Return the unit as a derived property `componentBaseUomCode` on BundleComponentView, DTO, api-spec and manifest. Show it next to the quantity in the Bundle component pane, and add one AC and one TC. Record the best-practice choice (base unit) as an ADR.",
      "adr": true
    },
    {
      "id": "G3",
      "severity": "MINOR",
      "section": "frontend",
      "artifact": "frontend-execution-plan",
      "line": null,
      "clause": "consistent (pattern: counts not re-stamped after a revise, platform finding 8)",
      "problem": "After API-CAT-056 was added, the plan header and the ALIGN-FE heading still say '55 operations' / 'Operations coverage (55 operations)'. The api-spec holds 56 operations, and the totals line already says '54 of 56'. The 'Amended' note of ADR-CAT-051 and the ui-ux-spec counts still say 55 operations with 53 used. The same defect (counts and next-id lines of an untouched artifact left stale) has recurred in several revises, so it is a rule-level defect, not four separate ones.",
      "fix": "Re-stamp the counts to 56 operations and 54 used in the frontend plan header, in the ALIGN-FE heading and in the ADR-CAT-051 amended note. At the rule level, platform finding 8 stays OPEN with the factory: a revise step must regenerate the count lines of every downstream artifact from the registries.",
      "adr": false
    }
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-044", "status_ok": true, "note": "Six PLATFORM-STD rows with ar and en text and bundle entries. The 413 and 415 rows were removed correctly."},
    {"id": "ADR-CAT-047", "status_ok": true, "note": "Six native statements, all with @TenantUnscoped. Lock order is stated. The advisory-lock key form (int,int) is valid for PostgreSQL 16."},
    {"id": "ADR-CAT-055", "status_ok": true, "note": "The tenant FK matches checklist C1. Platform finding 1 stays OPEN with the factory."},
    {"id": "ADR-CAT-056", "status_ok": true, "note": "No multipart endpoint, which satisfies checklist F4 and C7. Amended by ADR-CAT-076."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "An unsecured read service is an exception to checklist C13. It is correctly held conditional by ADR-CAT-066 and CORE-GAP-in-process-authorization."},
    {"id": "ADR-CAT-063", "status_ok": true, "note": "Narrowed by ADR-CAT-068. The repairs are consistent across the SRS, the plan and the tests."},
    {"id": "ADR-CAT-065", "status_ok": true, "note": "OwnedLookups serves the 10 CAT keys only. The given-usage row names MdlLookupApi because no interface for owned-lookups is published."},
    {"id": "ADR-CAT-070", "status_ok": true, "note": "Two tenant-defined tables deviate from checklist C5 and F3. The reason is justified and CORE-GAP-tenant-lookup-types is raised. This is accepted as a recorded deviation, not as a finding."},
    {"id": "ADR-CAT-072", "status_ok": true, "note": "The default Variant move is consistent across A7, the contract, API-CAT-006 and 009, QR-CAT-075, and TC-CAT-221 and 222."},
    {"id": "ADR-CAT-076", "status_ok": true, "note": "The FILE category enforces image type and size. The import's byte read is a delivery dependency on the CORE-GAP. The import TCs are marked core-dependent."},
    {"id": "ADR-CAT-077", "status_ok": true, "note": "Category events are added consistently to the SRS, the contract, the plan, the api-spec and TC-CAT-224."},
    {"id": "ADR-CAT-078", "status_ok": true, "note": "API-CAT-056 closes the dead end of RULE-CAT-037 for warehouse staff. G1 above refines which barcode the label uses."},
    {"id": "ADR-CAT-051", "status_ok": false, "note": "Its amended counts are stale: 55 operations and 53 used, against 56 and 54 (G3)."},
    {"id": "ADR-CAT-001..037", "status_ok": true, "note": "The P0 and P0.5 dialogue decisions. Superseded ones are marked, and none is BLOCKED."}
  ],
  "verdict": "REVISE"
}
```