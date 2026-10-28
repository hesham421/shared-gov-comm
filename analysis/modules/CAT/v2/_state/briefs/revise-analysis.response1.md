I applied all four findings by editing the source files in place, as the earlier revises did, instead of pasting complete-file `<<<FILE>>>` blocks; the plans run to thousands of lines. Nothing is committed and I haven't run `gov.py analyze`. My own checks: `api-spec-cat.yaml` still parses with 55 operations, and every fenced YAML block in the SRS, contract, db-script and both plans parses. I didn't run the rewritten SQL function against a real PostgreSQL.

**G1 — Category change events (MAJOR, new ADR-CAT-077).** I took the recommended option.
- **What changed:** REQ-CAT-008, RULE-CAT-049, CON-CAT-018 and the backend plan's event list now include ENT-CAT-006 (Category).
- **Payload:** a Category event has `recordPk` set to the categoryPk and an empty `itemPk`. A request that changes nothing, such as deactivating an already inactive Category, publishes no event.
- **Endpoints:** API-CAT-021, 023, 024 and 053 now publish CREATED, UPDATED, DEACTIVATED and REACTIVATED events. Each is marked `Honours: CON-CAT-018`, traces REQ-CAT-008, and lists CAT-500-CHANGE-NOT-SAVED in the plan, the error catalog and the api-spec.
- **Contract:** the ADR records this as additive. The payload fields are unchanged and no consumer cites CON-CAT-018 yet.
- **Frontend:** the Categories save, deactivate and reactivate actions now show CAT-500-CHANGE-NOT-SAVED in a banner with the RULE-CAT-049 message.
- **Tests:** new AC-CAT-165 and TC-CAT-224, which checks one event per create, update, deactivation and reactivation, and none on a repeated deactivation.

**G2 — Arabic normaliser (MAJOR).**
- **Database:** `CAT_NORMALIZE_AR` now removes `E'[\\u064B-\\u065F\\u0670\\u0640]'`, so Arabic-Indic digits are kept.
- **Java side:** RULE-CAT-003 in the SRS and backend plan now names that exact set. The backend plan also names the service normaliser that must remove the same set; I called it `CatArabicNormalizer`, since nothing in the artifacts named it before.
- **Test:** new TC-CAT-225 checks that «علبة ٢٥» and «علبة ٣٠» are both created and «أحمر» / «احمر» still collide with CAT-409-ITEM-NAME-DUPLICATE. It also compares the database and Java normalisers on the same inputs. That comparison is a white-box step.

**G3 — import rows and scale prefixes.**
- RULE-CAT-045 now lists RULE-CAT-032, and RULE-CAT-032 now also applies on import rows.
- New AC-CAT-166 and TC-CAT-226 cover GTIN 2000000000008 while a layout with prefix 20 is active. TC-CAT-226 joins the import tests that wait on the open file-reading gap in erp-core.

**G4 — label request.** `LabelRequest.lines.items` now has `required: [variantPk, copies]`. I edited that schema directly rather than regenerating the whole api-spec; the only other api-spec changes are the G1 additions.

**Bookkeeping:** the AC count is now 166, the SRS cites 20 ADRs, and the next free ADR is 078 in the SRS, its registry, the contract, the db-script and the registries. The backend test plan now has 161 TCs.

**Files changed:**
- New: `analysis/decisions/CAT/ADR-CAT-077.md`
- P0: `business-policies-cat.md` (stamp only)
- P1: `srs-cat.md`, `registry-srs-cat.md`
- P1.5: `contract-cat.md`
- P2: `db-script-cat.md`, `registry-db-cat.md`
- P3.1: `backend-execution-plan-cat.md`, `api-spec-cat.yaml`, `registry-exec-be-cat.md`
- P3.2: `frontend-execution-plan-cat.md`, `registry-exec-fe-cat.md`
- P4: `backend-test-plan-cat.md`, `frontend-test-plan-cat.md`

**Not changed:** `platform-summary.md`, `module-registry-cat.md`, `prd-cat.md`, `flow-diagram-cat.md` and `ui-ux-spec-cat.md` needed no edits. `platform/contracts/contract-cat.md`, `change-manifest.md` and the `_state/current-*` copies are outside the writable list, so they need regenerating.