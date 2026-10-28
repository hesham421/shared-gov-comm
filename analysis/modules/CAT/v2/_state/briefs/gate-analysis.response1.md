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
    "singular": 3,
    "feasible": 1,
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
    {"finding": "C5.13 srs:455 AC-CAT-013 'could'", "confirmed": false, "note": "False positive. 'could' sits inside the verbatim RULE-CAT-009 English message ('could not be checked'), not in the measurable clause."},
    {"finding": "C5.13 srs:773 AC-CAT-036 'could'", "confirmed": false, "note": "False positive. It is the verbatim RULE-CAT-005 message ('could not be issued')."},
    {"finding": "C5.13 srs:1293 AC-CAT-072 'مختلف'", "confirmed": false, "note": "False positive. It is the verbatim RULE-CAT-024 message ('استخدم رمزًا أو اسمًا مختلفًا'). The measure is the 409 CAT-409-CATEGORY-DUPLICATE."},
    {"finding": "C5.13 srs:1733 AC-CAT-102 'could'", "confirmed": false, "note": "False positive. It is the verbatim RULE-CAT-035 message ('could not be read')."}
  ],
  "findings": [
    {
      "id": "G1",
      "severity": "MAJOR",
      "section": "backend",
      "artifact": "backend-execution-plan",
      "line": null,
      "clause": "erp-core checklist C7: \"Every byte goes through FilePrivateStoreApi / FileImageStoreApi / the FILE endpoints; the module keeps FILE_DOCUMENT.ID columns and asks FileDocumentLookupApi for URLs\" · F9 (workaround instead of CORE-GAP)",
      "problem": "PHASE CORE 'Given usage', API-CAT-018 (RULE-CAT-038) and API-CAT-049 (RULE-CAT-044) have CAT read through FileDocumentLookupApi the 'type, size, storage (public image storage or private)' of a fileDocumentRef, and the 'bytes of an import file'. The erp-core conventions §Files rule 3 and file.md publish only isAvailable, publicUrl and publicUrls on that interface. No metadata or bytes read exists. So RULE-CAT-038 (JPG/PNG/WEBP, ≤ 5 MB, public storage only) and RULE-CAT-044 (xlsx, ≤ 10 MB, header and row count) rest on an unpublished call. Reading the import bytes is also not one of the sanctioned byte paths (FilePrivateStoreApi / FileImageStoreApi store bytes; they do not read them back). This gap is not recorded as a CORE-GAP. ADR-CAT-056, TC-CAT-056, 146, 166 and 218 and the CON given-usage rows all assume the missing call, so the plan is not implementable as written. It is a given-checklist violation (MAJOR, forces REVISE).",
      "fix": "1) Raise decisions/CAT/CORE-GAP-file-document-metadata-read.md. The question: can FileDocumentLookupApi return a document's content type, size, visibility/storage and, for a private document, a bounded read of its bytes, within the caller's tenant? 2) Raise a new ADR (amending ADR-CAT-056). Until the core answers, type, size and public-storage enforcement for images move to the FILE category of the image use (allowPublic = true, per-tenant limits, allowed types), enforced by the given FILE endpoint at upload. CAT then checks only isAvailable on the fileDocumentRef and the 20-record count. RULE-CAT-038 and RULE-CAT-044 keep their messages; document which part is enforced by the FILE category until the gap is answered. Import file bytes are marked a core dependency. 3) Correct the two FileDocumentLookupApi given-usage rows in SRS A9, contract §4 and the backend plan PHASE CORE to list only the published methods, and add the core-gap row to 'Core gaps' and the index. 4) Add the finding to business-policies PLATFORM FINDINGS (owner erp-core `modules.file`). 5) Re-word TC-CAT-056, 146, 166 and 218 so the type, size and storage refusals come from the FILE category and the CAT endpoint is asserted only for what CAT itself decides.",
      "adr": true
    },
    {
      "id": "G2",
      "severity": "MAJOR",
      "section": "backend",
      "artifact": "backend-execution-plan",
      "line": null,
      "clause": "rubric: complete (P1 silent path; P4 pattern at rule level)",
      "problem": "No statement covers a request body that is well-typed but internally inconsistent or points at a foreign record. The same silent path recurs at four endpoints. (a) API-CAT-006: the same variantAttributeFk twice in `selections`, a duplicate id in `variantAttributeValueFks`, or a value that does not belong to its attribute. The composite FK FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE and UQ_CAT_VAR_ATTR_ASSIGN_ATTR would then fire, and a constraint violation with no catalog row maps to CAT-500. (b) API-CAT-001: a non-empty `bundleComponents` with itemKindCode STANDARD. (c) API-CAT-017: a line whose bundleComponentPk belongs to another Bundle or tenant, or whose componentVariantFk differs from the stored one (DBF-CAT-089 is read-only after create). (d) API-CAT-014/015: a uomCode or barcodeType combination already handled, but not an INTERNAL type on the plain endpoint beyond the one sentence. The plan states only per-field unwanted behaviour, so each implementer will pick a different answer (silently ignore, 500, or save). No TC covers any of these.",
      "fix": "State one rule in PHASE CORE 'API conventions': a request whose fields contradict each other or name a record that is not part of the addressed parent (an attribute listed twice, a value not of its attribute, duplicate value ids, bundleComponents on a STANDARD Item, a bundleComponentPk outside the Bundle, a changed componentVariantFk on a kept line) is refused with CAT-400-REQUEST-INVALID before any RULE runs, and writes nothing; and any db constraint that backs a RULE is translated to that RULE's row, never to CAT-500. Cite the rule in the Validations lines of API-CAT-001, 006, 014, 015 and 017. Add one parameterised backend TC (row per case, owned by SVC-API-CRUD) asserting 400 and no write. Update the `CAT-400-REQUEST-INVALID` trigger text in the Error Catalog.",
      "adr": false
    }
  ],
  "adrs_reviewed": [
    {"id": "ADR-CAT-056", "status_ok": false, "note": "ACCEPTED, but rests on unpublished FileDocumentLookupApi methods (type, size, storage, bytes). See G1; it must be amended and a CORE-GAP raised."},
    {"id": "ADR-CAT-055", "status_ok": true, "note": "Consistent with checklist C1 and conventions Migrations rule 5. Platform finding 1 is a factory matter and is correctly filed."},
    {"id": "ADR-CAT-061", "status_ok": true, "note": "Conditional on ADR-CAT-066 and the OPEN CORE-GAP-in-process-authorization. Recorded as a CORE-GAP as F9 requires, so it is not a hidden workaround."},
    {"id": "ADR-CAT-066", "status_ok": true, "note": "Correctly raises the gap. Platform finding 5 owns it."},
    {"id": "ADR-CAT-070", "status_ok": true, "note": "The C5/F3 deviation is justified (tenant-created types, nested values, composite FK) and raised as CORE-GAP-tenant-lookup-types."},
    {"id": "ADR-CAT-071", "status_ok": true, "note": "Amended by ADR-CAT-074. Import budget and rollback are coherent. The 60 s figure is a measured target, and the 120 s limit is the guarantee."},
    {"id": "ADR-CAT-073", "status_ok": true, "note": "Interim raw-code rendering is consistent across API-CAT-052, F1, UXD-CAT-001 and TC-CAT-139/177. CORE-GAP raised."},
    {"id": "ADR-CAT-068", "status_ok": true, "note": "The three repairs are applied consistently in SRS A7, API-CAT-003/006/017, F3 and TC-CAT-185/186/187/210/212."},
    {"id": "ADR-CAT-072", "status_ok": true, "note": "The default-Variant move is consistent across the contract, the plan (QR-CAT-075) and TC-CAT-221/222."},
    {"id": "ADR-CAT-054", "status_ok": true, "note": "SUPERSEDED by ADR-CAT-055. Status is right."},
    {"id": "ADR-CAT-025", "status_ok": true, "note": "SUPERSEDED. Status is right (bundle kind is an Item kind, fixed after first save)."},
    {"id": "ADR-CAT-026", "status_ok": true, "note": "SUPERSEDED by ADR-CAT-042. Status is right."},
    {"id": "ADR-CAT-028", "status_ok": true, "note": "SUPERSEDED by RULE-CAT-001/D-07. Status is right."},
    {"id": "ADR-CAT-039", "status_ok": true, "note": "Dependent masters (assignment, Bundle component, Item unit) carry no code or names. CM-1 passes only through this ADR (OPEN platform finding 9). Accepted as long as the factory has not changed CM-1."}
  ],
  "verdict": "REVISE"
}
```