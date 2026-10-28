I applied all nine findings by editing the source files in place, the same way as the last revise. I didn't paste full-file `<<<FILE>>>` blocks because the plans are thousands of lines long. Nothing is committed and I haven't run `gov.py analyze`. My checks were that `api-spec-cat.yaml` still parses (55 operations), the query count agrees in all three places (75), and the backend test count and its package rows agree (157).

**MAJOR**
- **G1 (TC-CAT-199, AC-CAT-145):** step 2 and the AC now expect the inactive Variant to come back with no active barcode. Steps 3 and 4 still prove the barcode value is released.
- **G2 (name taken while inactive):**
  - REQ-CAT-130 now names RULE-CAT-003, and RULE-CAT-003 traces back to it.
  - New AC-CAT-162 and backend TC-CAT-219 (in SVC-API-CRUD). The test is refused with `ITM-000031`, then the other Item is deactivated and the reactivation succeeds.
  - On the frontend, the reactivation banner gets an "Open the existing item" action, covered by TC-CAT-220.
  - API-CAT-005 names the repair.
- **G3 (default Variant moves), new ADR-CAT-072:**
  - Deactivating the default Variant (API-CAT-009) moves the flag to the active Variant with the lowest code.
  - Generating Variants for an Item with no active Variant (API-CAT-006) gives the flag to the first new Variant.
  - I kept the existing unique index on the default flag rather than making it partial. A new query, QR-CAT-075, clears the old default before QR-CAT-014 sets the new one, so the only db-script change is to comments.
  - Updated REQ-CAT-025, A7 (new invariant 5), the `isDefaultFl` field note and the CON-CAT-002 wording.
  - New AC-CAT-163 and 164, tested by TC-CAT-221 and 222. TC-CAT-185 and TC-CAT-200 now also check the default.

**MINOR**
- **G4:** the Categories jump is relabelled "Items directly in this category / الأصناف المباشرة في هذه الفئة" in the UI/UX spec, the flow diagram and the frontend plan. No API change.
- **G5:** ADR-CAT-051 has an "Amended" note: 55 operations, 53 used, API-CAT-011 and 046 unused.
- **G6:** added platform finding #9 in `business-policies-cat.md`, asking the factory to cover dependent masters in the profile's entity defaults. ADR-CAT-039 stays.
- **G7:**
  - API-CAT-049 rejects a GTIN repeated in the import file on the later row. Its `{code}` is the code the earlier row's Item received, filled in after the write pass.
  - API-CAT-015 draws one more number from the internal-barcode series before answering CAT-409-BARCODE-DUPLICATE.
  - I also aligned RULE-CAT-029 in the SRS and the plan with the in-file check.
- **G8:** AC-CAT-096 and TC-CAT-048 now scan 2001230012342.
- **G9:** API-CAT-033 refuses an update to an inactive layout with CAT-400-REQUEST-INVALID. The scale-layout and unit e-invoice code state machines say an inactive record is replaced by a new one under the same key.

**Bookkeeping:** I updated the header counts and next-ID lines in every downstream file to AC 164, QR 75, next free ADR 073, and test IDs 219–222.

**Changed files:**
- `analysis/decisions/CAT/ADR-CAT-072.md` (new) and `ADR-CAT-051.md`
- P0: `business-policies-cat.md`
- P1: `srs-cat.md`, `registry-srs-cat.md`
- P1.5: `contract-cat.md`
- P2: `db-script-cat.md`, `registry-db-cat.md`
- P3.1: `backend-execution-plan-cat.md`, `api-spec-cat.yaml`, `registry-exec-be-cat.md`
- P3.2: `flow-diagram-cat.md`, `ui-ux-spec-cat.md`, `frontend-execution-plan-cat.md`, `registry-exec-fe-cat.md`
- P4: `backend-test-plan-cat.md`, `frontend-test-plan-cat.md`

**Not changed:**
- `platform/contracts/contract-cat.md` and `change-manifest.md` were outside the writable list. The published contract copy still has the old CON-CAT-002 wording until it is regenerated.