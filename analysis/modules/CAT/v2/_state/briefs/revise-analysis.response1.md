I applied all four findings by editing the source files in place. I didn't paste full-file `<<<FILE>>>` blocks because the backend plan is thousands of lines long. Nothing is committed. I haven't run `gov.py analyze`. My only checks were that the API spec still parses as YAML and that the query count matches in all three places (74 detailed entries, 74 summary rows, `QR: 74` in the totals).

**G1 — images must be in public storage (MINOR)**
- RULE-CAT-038 and API-CAT-018 now require the file to be in the tenant's public image storage. Otherwise the request is refused with `CAT-422-IMAGE-NOT-ACCEPTED`.
- Both now also say that one file reference can back several image records, because removing an image never deletes its file (ADR-CAT-056 §4).
- I updated the matching wording in the error catalog trigger, the given-usage row and the API-CAT-018 description in `api-spec-cat.yaml`.
- New backend test TC-CAT-218 (package SVC-API-CRUD) uploads an image to private storage and expects 422 with nothing created. It then attaches one public file twice and expects two records, exactly one of them primary.

**G2 — lock on attribute value create (MINOR)**
- API-CAT-029 now reads the attribute `FOR SHARE` through QR-CAT-022 and names that guard on its Concurrency line.
- QR-CAT-022's locking line now covers value create as well as reactivation.
- I also updated API-CAT-028's Concurrency line to match.

**G3 — empty generation request (MINOR)**
- API-CAT-006 now rejects an empty `selections` list, or an attribute with no values, with `CAT-400-REQUEST-INVALID`.
- `minItems: 1` is on both arrays in the API spec. I also marked both fields of each selection as required, which the plan already said they were.
- TC-CAT-184 has a second row covering both empty cases, run against an Item with no attribute in use.

**G4 — advisory locks (MAJOR)**
- Two new native queries in the catalog:
  - **QR-CAT-073**, Category tree lock: `SELECT pg_advisory_xact_lock(CAST(:tenant AS integer), hashtext('CAT_CATEGORY_TREE'))`
  - **QR-CAT-074**, scale-prefix lock: the same form with `'CAT_SCALE_PREFIX'`
- `:tenant` comes from `TenantContext.require()`. Both repository methods carry `@TenantUnscoped(reason = "advisory lock keyed by TENANT_ID", adr = "ADR-CAT-047")`.
- QR-CAT-073 is cited by API-CAT-021, 023, 024 and 053; QR-CAT-074 by API-CAT-014, 015, 032 and 049.
- The old `(TENANT_ID, '…')` wording, which matched no PostgreSQL signature, is replaced everywhere.
- "Four native queries" is now "six" in PHASE CORE and the query catalog preface.
- I amended ADR-CAT-047 with the exact key form and noted the G2 lock there too.
- One assumption to check: casting the tenant ID to `integer` is only safe while tenant IDs stay below 2^31. The plan says the core issues them from 1, but I haven't confirmed that against erp-core.

**Bookkeeping**
- The plan header and totals now say 74 queries. QR-CAT-073 is listed under ENT-CAT-006 and QR-CAT-074 under ENT-CAT-011 in the summary, coverage table and repository lists.
- `registry-exec-be-cat.md`: the query range now ends at QR-CAT-074 (next free QR-CAT-075), plus a note on this revise.
- In the backend test plan I updated the delta note, the test counts (154 tests, 85 in the rule-scenarios group), the traceability tables and the note on which tests cover `CAT-400-REQUEST-INVALID`.
- I also changed the shared test-ID range in the header of `frontend-test-plan-cat.md` to include TC-CAT-218.
- No new ADR was raised; the next free ADR number stays 072.

The changes are in:
- `analysis/modules/CAT/v2/P3_1/backend-execution-plan-cat.md`
- `analysis/modules/CAT/v2/P3_1/api-spec-cat.yaml`
- `analysis/modules/CAT/v2/P3_1/registry-exec-be-cat.md`
- `analysis/modules/CAT/v2/P4/backend-test-plan-cat.md`
- `analysis/modules/CAT/v2/P4/frontend-test-plan-cat.md`
- `analysis/decisions/CAT/ADR-CAT-047.md`