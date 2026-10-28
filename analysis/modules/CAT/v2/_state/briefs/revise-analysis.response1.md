I applied both MAJOR findings by editing the source files in place, as in the earlier revises, rather than pasting full-file `<<<FILE>>>` blocks; the plans are thousands of lines. Nothing is committed and I haven't run `gov.py analyze`. My own checks: `api-spec-cat.yaml` still parses with 55 operations, the diff is 164 lines added and 104 removed across 15 files, and nothing else in the artifacts still claims CAT reads a file's type or size.

**G1 — file metadata read (new ADR-CAT-076 and `CORE-GAP-file-document-metadata-read.md`)**
- **Images:** CAT registers a FILE category `CAT_ITEM_IMAGE` (public, PNG/JPEG/WEBP, 5 MB), added as a bootstrap row and copied into each new tenant by `CatTenantProvisioningContributor`. The FILE upload endpoint now refuses a wrong file before CAT is called.
- **What API-CAT-018 still checks:** availability, plus a public URL, and the 20-image count (RULE-CAT-038 keeps its code and message). The public-URL check is my own addition, using `publicUrl`, which erp-core publishes. Without it a privately stored file would pass `isAvailable` and be attached even though the storefront can't show it. Such a file now gets CAT-404-NOT-FOUND.
- **Import:** the byte read and the type/size check of API-CAT-049 are marked as waiting on the core gap. The endpoint, its codes and its tests are unchanged. The backend test plan says TC-CAT-017…020, 083, 084 and 217 run once the core answers.
- **Category registration:** erp-core doesn't document how a module registers a FILE category, so I added that as question 3 of the core gap.
- **Given-usage rows** in SRS A9, contract §4 and the backend plan now list only `isAvailable`, `publicUrl` and `publicUrls`. The gap is in the backend plan's core-gaps table and index, and platform finding 11 records it.
- **SRS:** RULE-CAT-038 and AC-CAT-112 had to change too, which the fix text didn't mention. AC-CAT-112 now covers the 7 MB JPG refused by the image category and a 21st image refused by RULE-CAT-038. No AC was added.
- **Tests:**
  - TC-CAT-056: the FILE category refuses a 7 MB JPG and a GIF.
  - TC-CAT-218: CAT's own checks — a file with no public URL or a missing reference gives 404, and the 21st image gives RULE-CAT-038.
  - TC-CAT-146: the client no longer checks type or size; the FILE refusal shows on the file input. It moved from package F3 to F2.
  - TC-CAT-166: keeps its client-side check, with a note that the server-side check waits on the core.

**G2 — contradictory requests**
- PHASE CORE "API conventions" has one new rule: a request whose fields contradict each other, or that names a record outside the addressed parent, gets CAT-400-REQUEST-INVALID before any RULE runs and writes nothing.
  - It covers the four groups the finding listed: API-CAT-006, the STANDARD-Item case on API-CAT-001, API-CAT-017, and API-CAT-014 (now beyond INTERNAL).
  - I also included two cases the finding didn't list: a component listed twice, and a client-chosen value on API-CAT-015.
- A second rule says any database constraint backing a RULE is reported as that RULE's error, never CAT-500.
- API-CAT-001, 006, 014, 015 and 017 cite the rule, and the CAT-400-REQUEST-INVALID trigger lists the cases.
- New TC-CAT-223 (SVC-API-CRUD) has 10 rows, each asserting 400, nothing written and no events. The backend test plan now has 158 TCs.

**Bookkeeping:** next free ADR restamped to 077 in the contract, db-script and all registries. The SRS ADR count is now 19, and the backend plan lists 5 core gaps.

**Files changed:**
- New: `ADR-CAT-076.md`, `CORE-GAP-file-document-metadata-read.md`
- Amended: `ADR-CAT-056.md` (note added to §2)
- P0: `business-policies-cat.md`
- P1: `srs-cat.md`, `registry-srs-cat.md`
- P1.5: `contract-cat.md`
- P2: `db-script-cat.md`, `registry-db-cat.md` (header stamps only)
- P3.1: `backend-execution-plan-cat.md`, `api-spec-cat.yaml`, `registry-exec-be-cat.md`
- P3.2: `frontend-execution-plan-cat.md`, `ui-ux-spec-cat.md`, `registry-exec-fe-cat.md`
- P4: `backend-test-plan-cat.md`, `frontend-test-plan-cat.md`

**Not changed:** `platform-summary.md`, `module-registry-cat.md`, `prd-cat.md` and `flow-diagram-cat.md` needed no edits. `platform/contracts/contract-cat.md` and the `_state/current-*` copies are outside the writable list, so they need regenerating.