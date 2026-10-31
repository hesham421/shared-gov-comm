I've applied all of G1–G19 and the four `analyze_confirmed` notes to the INV v1 artifacts. My validation script passes: all 29 yaml blocks parse, the Error Catalog matches the API document in both directions, and DBF 221/221 and API 52/52 agree with the totals. I could not run the governance analyzer (`gov.py analyze`) because I was denied access to governance-tools, so the C-checks themselves have not been re-run.

**Delivery format:** the brief asks for complete FILE blocks, but these files are too large for that, so I edited them in place, as the previous revise did. The `self-check` blocks were left for the orchestrator.

**New ids (no renumbering, every sequence continued):**
- **Rules:** RULE-INV-050 (a reused idempotency key must carry the same request) and RULE-INV-051 (a stock document cannot be dated after today, in the tenant's time zone).
- **Database fields:** DBF-INV-220 and 221 (`REQUEST_HASH` on stock moves and reservations). Database change: a trigger, `TRG_INV_STOCK_MOVE_IMMUTABLE`, now refuses any update or delete of a stock move.
- **API:** API-INV-052, `GET /api/v1/inv/lookups/{key}`.
- **Error codes:** INV-409-IDEMPOTENCY-KEY-REUSED (in-process only), INV-422-DOC-DATE-FUTURE and INV-404-LOOKUP-KEY-UNKNOWN.
- **Decisions:** ADR-INV-046 … 050, all ACCEPTED; the next free number is ADR-INV-051. ADR-INV-026, 033 and 038 were amended.
- **Test cases:** TC-INV-264 … 284 in the backend plan and TC-INV-285 in the frontend plan.

**Choices I made, each recorded as an ADR:**
- **G2:** a refusal for one line names that line in `details` (`lineRef`, `lineRefKind`, `rowNo`) — ADR-046.
- **G3 and G18:** the system does not check reasons it writes itself (COUNT_DIFFERENCE, OPENING_STOCK). The adjustment screen refuses OPENING_STOCK and no longer offers it in the reason list — ADR-047.
- **G9:** a SHA-256 hash of each request is stored next to its idempotency key, so reusing a key for a different request is refused — ADR-048.
- **G12:** the immutability trigger plus `@Immutable` on the stock-move entity — ADR-049.
- **G19:** switching the default warehouse or branch takes a per-tenant advisory lock — ADR-050.
- **G7:** INV now serves its own six lookup keys through `OwnedLookups`, following the PTY precedent. Lookup values come from API-052 instead of the `mdl` service.
- **G13:** lots are locked FOR SHARE when posting and FOR UPDATE when an expiry is corrected. The lot's activity flag is recomputed after commit, so a share lock never has to be upgraded (which could deadlock).

**Test plans:**
- The backend plan now states that every HTTP catalogue row and every in-process row is asserted by at least one TC. A script checks this: 58/58 and 6/6.
- I regenerated both traceability indexes. The regenerated tables match the old ones exactly apart from the new TCs.
- I updated the coverage sections and the PHASE/SUB trace markers.
- In the frontend plan, 27 refusal texts are now written `ar «…» · en «…»`. Screen labels such as chips, statuses and lookup names keep the old `«…» / "…"` form.

**G16, things the analyzer owner should know:**
- **C10.8 / C10.9:** these checks only look at lookup values that are not seeded. INV seeds every value, so they have nothing to examine. TC-INV-265 gives them a subject: it makes COUNT_DIFFERENCE inactive in its own tenant through the `mdl` call. It is the only case that needs host data.
- **C7.24:** this check also finds nothing in PTY, even though every API block there has its Honours line. I added an explicit `Honours` line here, but the check probably needs its owner to look at it.
- **C7.23:** the B1 entity tables are now written as `Label : value` lines.

**Still to do outside the writable set:**
- `backend/modules/INV/packages/api-spec-inv.yaml` and `platform/contracts/contract-inv.md` still hold the revise-1 versions. They need republishing from the revised analysis copies (API-052, the new codes, `currencyCode` in CON-INV-020).
- Platform finding row 4 (in-process caller authority) stays OPEN and is tracked with PTY.