I applied all 15 findings to the CAT v2 artifacts. Analyze now reports C10.8 = 11 and C10.9 = 9 subjects examined, with no findings from either, so G9 is met. Nothing is committed and I did not run `publish`.

**Analyze result:** it still reports BLOCKED, with 0 critical, 2 major and 4 minor findings. None of them is a defect in the artifacts:
- **2 major, C9.23 (publication-fresh):** the published copies of `api-spec-cat.yaml` and `contract-cat.md` are out of date because the sources changed. Running `gov.py publish` fixes this; the gate step normally runs it.
- **4 minor, C5.13:** the words "could" and «مختلف» in AC-013, 036, 072 and 102. They sit inside the quoted rule messages, and your brief already lists them as false positives.
- **10 clauses that examined nothing (C6.x, C7.x):** these were already confirmed as empty by nature.

**New IDs**

| Kind | IDs |
|---|---|
| Requirement | REQ-CAT-135 |
| Acceptance criteria | AC-CAT-143 … 152 |
| Rule | RULE-CAT-053, code CAT-422-PARENT-INACTIVE |
| API | API-CAT-052 `GET /api/v1/cat/lookups/{key}` |
| Query | QR-CAT-072 |
| Test cases | backend TC-CAT-197 … 207; frontend TC-CAT-208, 209 |
| Decisions | ADR-CAT-063 … 067 (all ACCEPTED), plus `CORE-GAP-in-process-authorization.md` |

**How each finding was applied**
- **G1 (capability flags):** RULE-CAT-008 now lists exactly the refused operations. API-CAT-026, 028, 031 and 041 no longer check the flag, and the frontend no longer handles that refusal on the attribute reads. AC-143 / TC-197 cover reading and deactivating a Bundle while `bundles` is off.
- **G2 (inactive parents):** a new rule refuses child writes under an inactive Item or Variant, in the 10 endpoints named. ADR-063 records the choice that deactivating a Variant also deactivates its barcodes, which frees the barcode value for reuse. Covered by AC-144/145 and TC-198/199, and on screen by TC-209.
- **G3 (scale layouts):** ADR-064 makes the scale code length fixed after creation. TC-107 no longer sends it, TC-048 checks that barcodes stay stored after a layout is deactivated, and TC-206 covers a decimals change.
- **G4:** the turn-off check result now carries messageAr and messageEn.
- **G5 (lookups):** API-CAT-052 serves CAT's 10 lookup keys through `OwnedLookups`. All 10 lookup hooks and UXD-CAT-001 are rebound to it, and ADR-050 is amended by ADR-065. I also removed the 10-key enum from the `{key}` path parameter. With it, an unknown key would fail as a 400, so CAT-404-LOOKUP-KEY-UNKNOWN could never be returned. The keys are now listed in the parameter's description instead.
- **G6:** the 6 platform-standard error rows have ar/en text, and ADR-044 no longer has the 413/415 rows.
- **G7:** the core gap is written up, ADR-061 is now conditional on the core's answer (ADR-066), and PHASE CORE names the gap.
- **G8:** six ACs and TCs for parallel requests (TC-200 … 205), each naming the guard and the unit that owns it.
- **G9:** the brand host values are listed in the SRS lookups block. The Host data lines of TC-019, 036, 037, 068, 137 and 139 now name the mdl calls: `POST /api/v1/mdl/lookup-types/search`, `POST …/{id}/values` and `DELETE /api/v1/mdl/lookup-values/{id}`.
- **G10–G12:** `itemFk` in CON-008 and CON-009; the `required` list on API-CAT-017 lines; and a frontend rule that a jump to another page shows only with that page's VIEW permission and its flag on (Print labels, Import, Open Variant attributes), tested by TC-208.
- **G13:** the four counter-cases were added as steps of TC-006, 007, 015 and 051.
- **G14:** the COMMIT is removed and the seed columns are checked against erp-core 1.6.0. The database privilege that pg_trgm needs is stated as a deployment precondition.
- **G15:** every intra-module foreign key is now composite with TENANT_ID (ADR-067).

**Other changes beyond the brief**
- **Platform findings:** finding 3 is marked resolved. Its cause was in CAT's own SRS and test plans, not in the parser. I added finding 4, OPEN: the erp-core manifest publishes no interface for `OwnedLookups`, so the API-CAT-052 usage row has to name `MdlLookupApi` instead.
- **ADR-CAT-053:** I added a note saying its premise ("no named add-value call exists") no longer holds. Its seeded substitutes stay as they are.

The next free ADR number is ADR-CAT-068.