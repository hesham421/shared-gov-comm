I've applied all six findings of the REVISE brief to the CAT v2 artifacts. I edited the files in place in the repo instead of sending them back as complete-file `<<<FILE>>>` blocks; the result is the same as the earlier "REVISE applied" commits. Every YAML block validates against its schema and the api-spec parses. I didn't run the governance tool (`gov.py`), so the gate itself hasn't been re-run on this.

**G1 – generate an internal barcode from the label screen** (ADR-CAT-078, ACCEPTED)
- I added a new endpoint, API-CAT-056 (`POST /api/v1/cat/barcode-labels/variants/{id}/internal-barcode`). It needs PERM_CAT_BARCODE_LABELS_CREATE and reuses the service, rules and locks of API-CAT-015.
- New ids: REQ-CAT-148, AC-CAT-171, backend test TC-CAT-231 and frontend test TC-CAT-232.
- The frontend plan has a new mutation hook (`LABEL-INTERNAL-BARCODE`) and a "Generate internal barcode" action on each flagged line of SCR-CAT-005. The UI spec and flow diagram are updated to match.
- The ADR records two rejected options: widening API-CAT-015, and showing a different next action depending on the user's role.

**G2 – parallel-request races**
- I added AC-CAT-167 to 170, one per race, each with a parallel-request test (TC-CAT-227 to 230).
- The "Concurrency guards" coverage row now reads 12 / 12.

**G3 – Bundle quantity and conversion factor bounds**
- In API-CAT-001 and 017, `quantity` now has `exclusiveMinimum: 0`, `maximum: 999999.999` and `multipleOf: 0.001`.
- In API-CAT-010, `conversionFactor` now has `exclusiveMinimum: 0`, `maximum: 999999.999999` and `multipleOf: 0.000001`.
- RULE-CAT-018 and RULE-CAT-027 are restated with these bounds. A value outside them answers CAT-400-REQUEST-INVALID before the rule runs.
- TC-CAT-027 now has 6 boundary rows and TC-CAT-039 has 5.
- No database change was needed; the bounds fit the existing columns.

**G4 – categoryFk-only repair in API-CAT-003**
- The text now says only RULE-CAT-026 and the version check run on that path, and that RULE-CAT-003 is checked again at reactivation.

**G6 – updating an inactive record**
- PHASE CORE has a new rule: a PUT on a record whose own `isActiveFl` is false answers CAT-400-REQUEST-INVALID and writes nothing.
- It's cited in API-CAT-012, 019, 023, 027 and 030, and in the CAT-400 trigger. Inactive Items and Variants are left out because RULE-CAT-053 already governs them.
- TC-CAT-223 has new rows 11 to 15. They include the removed-image case, which checks that the active primary image stays unchanged.

**G7 – frontend coverage re-stamp**
- The frontend test plan's coverage rows are re-stamped from registry-srs: AC 61 / 171 on this track and 171 / 171 across both plans, REQ 148 / 148.

**Knock-on updates**
- New totals: REQ 148, AC 171, API 56 (35 paths), 166 backend tests and 66 frontend tests. The next free ADR is 079.
- These are re-stamped in both registries, the business policies, the contract and the db-script. None of these files still shows an old count.

Four items in `git status` were already modified or untracked before I started, and I didn't touch them: `platform/contracts/contract-cat.md`, `_state/briefs/revise-analysis.md`, `backend/modules/CAT/` and `frontend/modules/CAT/`. Nothing is committed yet.