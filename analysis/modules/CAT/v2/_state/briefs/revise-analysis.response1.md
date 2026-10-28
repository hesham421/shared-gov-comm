I applied all eight findings by editing the source files in place, the same way as the last two revises. I didn't paste full-file `<<<FILE>>>` blocks because the plans run to thousands of lines. Nothing is committed and I haven't run `gov.py analyze`. My own checks passed:
- `api-spec-cat.yaml` still parses, with 55 operations.
- The SRS has 147 REQs, each with its own ACs.
- Each new REQ is traced by an endpoint, a frontend binding and at least one backend test.
- The test counts are unchanged: 157 backend and 65 frontend.

**MAJOR**
- **G1 (inactive lookup value label):**
  - New ADR-CAT-073 and `CORE-GAP-lookup-inactive-value-label.md`.
  - API-CAT-052 still returns active values only. Until the core answers, a value deactivated later shows as its raw code on every screen.
  - The frontend plan's `LookupOption` drops `isActive` and now matches the API document. UXD-CAT-001 is narrowed to match.
  - TC-CAT-139 and TC-CAT-177 use host brands whose labels differ from their codes (OLDBRAND and a new RETIRED_177, added to the SRS lookup values) and assert the code is shown.
  - Platform finding 10 records the gap.
- **G3 (import batching):** I took the recommended option, recorded as ADR-CAT-074, which amends ADR-CAT-071.
  - The import saves entities through QR-CAT-003, 011 and 049, keeping the savepoint per batch and the row-by-row replay. "JDBC batching" is gone, and there are still six native statements.
  - The 60 s figure is now a target, measured by a new undelayed run in TC-CAT-217. The 120 s limit stays the guarantee.
  - TC-CAT-217's fault point now reads "after the first 500 rows are saved".
- **G4 (bulk updates):** QR-CAT-020, 072 and 075 now load the rows as entities and save each one. Every change is audited and gets its own change event with its record id. QR-CAT-075 flushes between clearing the old default and setting the new one.

**MINOR**
- **G2 (request size and paging):**
  - Every paged read goes through the given `PageableBuilder` (size ≤ 200, page ≥ 0), and the native queries take their LIMIT and OFFSET from it.
  - The API document now caps the arrays: selections 3, values per selection 300, Bundle lines 100, label lines 5,000. It also bounds the page and size parameters.
  - API-CAT-006 checks the 300-Variant limit arithmetically before building any combination.
  - TC-CAT-184 has a row 3 where 4 attributes get CAT-400-REQUEST-INVALID.
- **G5 (Category label):** New ADR-CAT-075, with amendment notes on ADR-CAT-048 and ADR-CAT-051.
  - The Item read schemas now carry the Category's code and names, filled by a join in the read queries.
  - The separate Category-tree label read is dropped from the frontend plan.
  - TC-CAT-195 adds a step for a user who can't read the Category tree.
- **G6 (empty import cells):** API-CAT-049 now says an empty base unit or tax class cell takes PIECE / STANDARD, and a filled cell is checked against the active values and never overridden. I covered this by extending TC-CAT-083 rather than adding a new test.
- **G7 (bundled REQs):**
  - New REQ-CAT-140 and 141 are split from REQ-CAT-025 (one per default-Variant move).
  - New REQ-CAT-142, 143 and 144 are split from REQ-CAT-130 (one per reactivation refusal).
  - The fix text only named those two, but the problem also listed REQ-CAT-135, so I split it too. New REQ-CAT-145, 146 and 147 cover the three repairs allowed on an inactive Item.
  - Eight ACs are re-pointed, not renumbered. The rules, endpoints, API traces, frontend bindings and tests now point at the new REQs.
- **G8 (dependent master entities):** No CAT change while ADR-CAT-039 stands. Platform finding 9 stays OPEN and notes this round.

**Bookkeeping:** downstream headers now read REQ 147 · AC 164 · RULE 53 · next free ADR 076, in the contract, db-script and all registries.

**Files changed:**
- New: ADR-CAT-073, 074, 075 and `CORE-GAP-lookup-inactive-value-label.md`.
- Amended: ADR-CAT-048, 051 and 071.
- P0: `business-policies-cat.md`
- P1: `srs-cat.md`, `registry-srs-cat.md`
- P1.5: `contract-cat.md`
- P2: `db-script-cat.md`, `registry-db-cat.md`
- P3.1: `backend-execution-plan-cat.md`, `api-spec-cat.yaml`, `registry-exec-be-cat.md`
- P3.2: `flow-diagram-cat.md`, `ui-ux-spec-cat.md`, `frontend-execution-plan-cat.md`, `registry-exec-fe-cat.md`
- P4: `backend-test-plan-cat.md`, `frontend-test-plan-cat.md`

**Not changed:**
- `platform-summary.md`, `module-registry-cat.md` and `prd-cat.md` needed no edits.
- `platform/contracts/contract-cat.md`, `change-manifest.md` and the `_state/current-*` copies were outside the writable list, so they need regenerating.