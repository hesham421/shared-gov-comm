All 20 findings are applied. Analyze still reports `BLOCKED`, though: 0 critical, 2 major, 4 minor. The 2 major are stale publications that the orchestrator's `gov.py publish` step clears. As with the last round, I edited the files in the repo directly rather than returning `<<<FILE>>>` blocks. Nothing is committed.

**What analyze shows now** (I ran `gov.py state` and `analyze --scope all`; both only rewrite `_state/`):
- **2 major, both C9.23 (publication-fresh):** the published copies of `api-spec-cat.yaml` and `contract-cat.md` are stale. They clear when `gov.py publish` runs. I didn't run it because it writes into `backend/` and `platform/`.
- **4 minor:** the 'could' / 'مختلف' hits on AC-CAT-013, 036, 072 and 102. The reviewer already confirmed these as false positives. AC-CAT-010 ('fast') and AC-CAT-110 are fixed.
- **Coverage is 100%** on every ratio: 134 REQs, 142 ACs, every AC has a TC.
- **C10.8 and C10.9 still examine 0 subjects.** This is the `gov.py` parser defect (G15), filed as platform finding 3. Per the finding, the gate shouldn't APPROVE until those clauses report a non-zero count.

**New decisions** (ADR-CAT-055 … 062, all ACCEPTED; the next free number is 063):
- **G1 (055):** the 12 tenant FKs to `CORE_TENANT` are back, and ADR-054 is marked superseded. With the FKs restored, C6.15 examines nothing, so it doesn't flag them today. It did refuse them in repair round 1, so platform finding 1 asks the factory to make the exemption explicit.
- **G2 / G17 (056):** images and import files are uploaded through the given FILE endpoints first. API-CAT-018 and 049 now take JSON with `fileDocumentRef`. The 413/415 error codes are removed, and the ADR states the file retention rules.
- **G3 (057):** the import sheet is report `CAT_ITEM_IMPORT_SHEET`, rendered as xlsx through `PrintApi`. The given report module already supports xlsx, so I raised no `CORE-GAP`.
- **G5 (058):** the given copies the lookups and number series to each new tenant itself. A new `CatTenantProvisioningContributor` copies the 8 Unit e-invoice codes. The hook exists in erp-core, so no `CORE-GAP` was needed.
- **G6 (059):** reactivation now re-checks the Item's state. A new rule (RULE-CAT-050) refuses an Item with no active Variant, and the existing Category and Bundle-component rules now also run on reactivation. Barcode lookup, Variant search and labels only match active Items.
- **G7 (060):** an Item unit that active barcodes use cannot be deactivated (RULE-CAT-051, new QR-CAT-071). I chose to refuse rather than deactivate the barcodes along with it, so printed labels never stop scanning silently.
- **G13 (061):** `CatalogueReadApi` calls a read service with no permission check, and the calling module authorizes its own user. The HTTP endpoints keep their permission checks.
- **G18 (062):** a `CAT_NORMALIZE_AR` function folds Arabic letter variants for name uniqueness and search. Search gets trigram indexes, and `%` and `_` are escaped in search terms.

**Other changes:**
- **G8:** I added a new rule, RULE-CAT-052, instead of giving RULE-013 a second code, because an SRS rule has one message.
- **New IDs:** REQ-CAT-128 … 134, AC-CAT-130 … 142, TC-CAT-183 … 196. The error catalog goes from 57 to 58 codes (3 new, 413/415 removed).
- **G4:** the four native queries name `TENANT_ID` in every branch and carry `@TenantUnscoped`. ADR-047 is amended.
- **Platform findings (G1, G14, G15):** recorded as OPEN rows in a new PLATFORM FINDINGS section of `business-policies-cat.md`. The project registry, where they belong, isn't in this pass's writable set.
- **Smaller fixes:** the G19 traces in ADR-015 and ADR-028, and G20, where the two "reserved" AC placeholders are deleted.