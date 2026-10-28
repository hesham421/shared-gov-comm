```json
{
  "gate": "analysis",
  "module": "CAT",
  "version": 2,
  "scores": { "unambiguous": 2, "verifiable": 3, "complete": 2, "consistent": 2, "singular": 2, "feasible": 2, "traceable": 3 },
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
    {"finding": "C5.13 AC-CAT-013 'could' (srs line 454)", "confirmed": false, "note": "False positive. 'could' appears only inside the quoted RULE-CAT-009 message ('Capability settings could not be checked'), not in the AC's own wording."},
    {"finding": "C5.13 AC-CAT-036 'could' (srs line 782)", "confirmed": false, "note": "False positive. It is the quoted RULE-CAT-005 message ('A new code could not be issued')."},
    {"finding": "C5.13 AC-CAT-072 'مختلف' (srs line 1302)", "confirmed": false, "note": "False positive. It is the quoted RULE-CAT-024 message ('…استخدم رمزًا أو اسمًا مختلفًا'), a catalogue text copied verbatim."},
    {"finding": "C5.13 AC-CAT-102 'could' (srs line 1742)", "confirmed": false, "note": "False positive. It is the quoted RULE-CAT-035 message ('The scale label could not be read')."},
    {"finding": "10 clauses examined nothing (C6.3, C6.10, C6.12, C6.15, C7.5, C7.5b, C7.24, C7.26, C7.27, C7.28)", "confirmed": true, "note": "Each is empty by nature: the XM register is `records: []` and CAT consumes nothing. C6.15 is also covered by platform finding 1, which is OPEN and correctly owned by the factory."}
  ],
  "findings": [
    {
      "id": "G1", "severity": "MAJOR", "section": "backend", "artifact": "api-spec", "line": null,
      "clause": "P3 read-binding — does every foreign id a screen renders resolve to a label (REQ-CAT-076, UXD-CAT-001, rubric: consistent)",
      "problem": "API-CAT-052 returns only ACTIVE values, and the LookupOption schema has no activity flag. REQ-CAT-076 and UXD-CAT-001 require the screens to show the label of a value that was deactivated later (brand, unit, tax class) on records that still hold it, and TC-CAT-139 expects 'its label kept although inactive'. The frontend plan's F1 type `LookupOption = {code, labelAr, labelEn, isActive}` has an `isActive` field the API document does not carry. No read exists from which the UI can obtain such a label. OLDBRAND passes in TC-CAT-139 only because its code equals its name.",
      "fix": "Pick one and state it in P3.1, P3.2 and the TCs. (a) Add an optional `codes` query parameter (comma list, ≤ 50) to API-CAT-052. It returns the listed values with `isActiveFl`, active or not, and `LookupOption` gains `isActiveFl`; this needs `MdlLookupApi` or `OwnedLookups` to read by code, so record an erp-core platform finding if it cannot. (b) State that an inactive value renders as its raw code, drop `isActive` from F1, narrow UXD-CAT-001's 'Need', and change TC-CAT-139 and TC-CAT-177 to use a brand whose label differs from its code. Recommended: (a) with (b) as the documented fallback.",
      "adr": true
    },
    {
      "id": "G2", "severity": "MAJOR", "section": "backend", "artifact": "api-spec", "line": null,
      "clause": "P2 closure — maximum/edge of a record; P4 pattern (one rule, four-plus places)",
      "problem": "The API document and the plan set no upper bound on request-side sizes. `page`/`size` on API-CAT-038/040/042/046 have no minimum or maximum, so a huge or negative size has no stated outcome. `VariantGenerationRequest.selections` has no `maxItems` (3) and `variantAttributeValueFks` has no `maxItems`. RULE-CAT-014 limits only the result (≤ 300 active Variants), and the plan does not say the limit is checked BEFORE the Cartesian product is built. Three attributes of 1,000 values each would be 10^9 combinations. `BundleComponentsRequest.lines`, `ItemCreateRequest.bundleComponents` and `LabelRequest.lines` are also unbounded. No statement and no TC covers these.",
      "fix": "Add one shared convention to the plan (PHASE CORE, API conventions): size ∈ [1,100] and page ≥ 0, else CAT-400-REQUEST-INVALID. Add `maxItems` to the OpenAPI: selections 3, values per selection 300, Bundle lines 100 (or the D-10 equivalent), label lines 5000. In API-CAT-006, compute the product size arithmetically and refuse with RULE-CAT-014 before materialising any combination. Add one TC row to TC-CAT-184 (4 attributes, or 301 values, answers RULE-CAT-014/400 without a timeout) and one for size=0/101.",
      "adr": false
    },
    {
      "id": "G3", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
      "clause": "\"C3 Every native query / JdbcTemplate statement names TENANT_ID and its method carries @TenantUnscoped(reason, adr)\" (and F2)",
      "problem": "API-CAT-049 and ADR-CAT-071 insert each import batch 'with JDBC batching (batch size 500) under one savepoint, replayed row by row'. Every PK is `GENERATED ALWAYS AS IDENTITY`, and Hibernate silently disables insert batching for identity keys. So the batch is either not batched, or it is a JdbcTemplate/native statement. No QR is catalogued for such a statement, ADR-CAT-047 says there are exactly six native statements, and no @TenantUnscoped method or explicit TENANT_ID is declared. The 60 s budget rests on the batching that may not exist.",
      "fix": "State the mechanism. Recommended: entity saves through the repositories (QR-CAT-003/011/049), with the savepoint and row-by-row replay unchanged and the words 'JDBC batching' removed. Re-derive the 60 s / 120 s budget as roughly 3 inserts per row plus events, and mark it 'to be measured' with the reference-deployment test of TC-CAT-217. If batching is truly wanted, add a catalogued native QR (a seventh native statement) that names `TENANT_ID = :tenant` on every row, with `@TenantUnscoped(reason, adr=\"ADR-CAT-047\")`, and amend ADR-CAT-047 and ADR-CAT-071 accordingly.",
      "adr": true
    },
    {
      "id": "G4", "severity": "MAJOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
      "clause": "REQ-CAT-008 / RULE-CAT-049 (one event per committed change, carrying recordPk) vs QR-CAT-020, QR-CAT-072, QR-CAT-075; rubric: feasible, consistent",
      "problem": "Three bulk updates must feed per-record catalogue-change events: assignments (QR-CAT-020), barcodes (QR-CAT-072) and the default flag (QR-CAT-075). QR-CAT-020 returns a 'count' only, so the assignment pks for the DEACTIVATED events of API-CAT-009 are unknown. QR-CAT-072 and QR-CAT-075 specify `UPDATE … RETURNING` as 'a JPQL bulk update', and JPQL/HQL has no RETURNING. A bulk update also bypasses entity listeners and the audited-entity hooks of the given. The only native alternative needs C3 annotations, which are not declared.",
      "fix": "Since the Item lock is held, replace the three bulk statements with load-then-modify through the entities: select the active rows, set `isActiveFl`/`isDefaultFl` on the entities, and publish one event per entity. Keep the clear-before-set ordering and a flush between the two statements for QR-CAT-075, because of UQ_CAT_VARIANT_DEFAULT. Update the Result shape lines accordingly. If a bulk statement is kept, make it a declared native QR with RETURNING, an explicit tenant and @TenantUnscoped, and amend ADR-CAT-047.",
      "adr": false
    },
    {
      "id": "G5", "severity": "MINOR", "section": "frontend", "artifact": "frontend-execution-plan", "line": null,
      "clause": "P3 read-binding — does every foreign id it renders resolve to a label",
      "problem": "ADR-CAT-051 §4 and the CATEGORY-LABEL-QUERY hook say that when API-CAT-039 answers 403, the Category column shows 'the code'. ItemSummary and ItemDetail carry only `categoryFk` (an id), not the Category code, so no code is available. The user sees a raw identifier, or nothing.",
      "fix": "Add derived `categoryCode`, `categoryNameAr` and `categoryNameEn` to ItemSummary and ItemDetail (the list in ADR-CAT-048 §5). The Items screen then needs no Category read for labels, and the 403 branch disappears. Otherwise, state that the column shows '—' when the tree cannot be read.",
      "adr": true
    },
    {
      "id": "G6", "severity": "MINOR", "section": "backend", "artifact": "backend-execution-plan", "line": null,
      "clause": "unambiguous — P2 closure (empty value on an import row)",
      "problem": "API-CAT-049 maps each row to baseUomCode and taxClassCode. The same Orchestration line then says 'defaults PIECE / STANDARD' for the created Item. It does not say whether the default applies only to an empty cell or overrides a supplied value. It also does not say what a row with an empty baseUomCode does under RULE-CAT-006. Two implementers would read this differently.",
      "fix": "In API-CAT-049 state: an empty baseUomCode or taxClassCode cell takes PIECE / STANDARD (D-09). A non-empty cell is validated under RULE-CAT-006 and never overridden. Add one AC/TC row for 'empty cells take the defaults'.",
      "adr": false
    },
    {
      "id": "G7", "severity": "MINOR", "section": "backend", "artifact": "srs", "line": null,
      "clause": "singular (rubric); C5.x EARS",
      "problem": "Three requirements bundle several triggers and outcomes in one statement. REQ-CAT-025 covers create default, move on deactivation and move on generation. REQ-CAT-130 covers four refusals. REQ-CAT-135 covers five write triggers plus three exceptions. A single REQ id then carries many independent ACs and TCs, and traceability cannot show which behaviour a change touches.",
      "fix": "Split in a delta with new ids and no renumbering. Example: keep REQ-CAT-025 for the default Variant at creation, add REQ-CAT-140 'default moves on deactivation' and REQ-CAT-141 'default moves on generation'. Likewise add one REQ per RULE-CAT-050/026/019/003 refusal. Re-point the ACs and TCs accordingly. Low urgency, but do it in the same revise as G1–G4.",
      "adr": false
    },
    {
      "id": "G8", "severity": "MINOR", "section": "backend", "artifact": "business-policies", "line": null,
      "clause": "CM-1",
      "problem": "ENT-CAT-005, 007 and 008 are 'master (dependent)' yet carry none of code, nameAr or nameEn. ENT-CAT-009 has no nameAr, and ENT-CAT-010 has no code. CM-1 passes only through the reading of ADR-CAT-039, which no check can apply. This is already recorded as OPEN platform finding 9 (owner: factory).",
      "fix": "No CAT change is required while ADR-CAT-039 stands. The factory should add the dependent-master rule to the commerce `conventions.entity_defaults`. Keep platform finding 9 OPEN, and re-check CM-1 mechanically once the rule exists.",
      "adr": false
    }
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-044", "status_ok": true, "note": "Six PLATFORM-STD rows with ar/en text; 413/415 removed consistently in the plan, spec and registry."},
    {"id": "ADR-CAT-045", "status_ok": true, "note": "Partly superseded by ADR-CAT-061; the header says so."},
    {"id": "ADR-CAT-047", "status_ok": false, "note": "Lock design is sound. Its 'exactly six native queries' statement contradicts the import's JDBC batching and the RETURNING bulk updates (G3, G4); amend with the fix."},
    {"id": "ADR-CAT-050", "status_ok": true, "note": "UXD-CAT-001 is amended by ADR-CAT-065; flagged in the header. See G1 for the remaining label gap."},
    {"id": "ADR-CAT-055", "status_ok": true, "note": "Supersedes ADR-CAT-054, consistent with checklist C1; platform finding 1 correctly names the factory as owner."},
    {"id": "ADR-CAT-056", "status_ok": true, "note": "No multipart endpoint; satisfies checklist F4/C7."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "An unsecured read service departs from C13. The CORE-GAP exists and ADR-CAT-066 makes the decision conditional; F9 is respected. Needs no further finding."},
    {"id": "ADR-CAT-065", "status_ok": true, "note": "Serves own keys through OwnedLookups (C6); the active-only read creates the gap in G1."},
    {"id": "ADR-CAT-068", "status_ok": true, "note": "The three repairs close the dead ends; TCs 185–187, 210 and 212 exercise them."},
    {"id": "ADR-CAT-069", "status_ok": true, "note": "Reactivation of Category, attribute and value is consistent with the tree lock."},
    {"id": "ADR-CAT-070", "status_ok": true, "note": "The C5/F3 deviation is justified and CORE-GAP-tenant-lookup-types is filed."},
    {"id": "ADR-CAT-071", "status_ok": false, "note": "Time budget and rollback are fine, but 'JDBC batching' conflicts with identity keys and C3 (G3)."},
    {"id": "ADR-CAT-072", "status_ok": true, "note": "Default-Variant move is consistent across the SRS, contract, db-script comments and TC-CAT-221/222. The QR-CAT-075 mechanism is covered by G4."},
    {"id": "CORE-GAP-in-process-authorization", "status_ok": true, "note": "OPEN and owned by erp-core; ADR-CAT-066 correctly keeps ADR-CAT-061 conditional."},
    {"id": "CORE-GAP-number-series-block-allocation", "status_ok": true, "note": "Correct as a core gap; CAT's interim per-code allocation is within the stated budget."},
    {"id": "CORE-GAP-tenant-lookup-types", "status_ok": true, "note": "Genuine core gap, not a module gap filed as a platform finding."}
  ],
  "verdict": "REVISE"
}
```