All 23 findings are applied, except G20, which asks for no plan change. I edited the files in place rather than returning `<<<FILE>>>` blocks, because the full files are too large to repeat back. `git diff` shows every change.

**What I checked, and what I couldn't:** every `yaml name=…` block in the changed files parses. The API document parses with 51 operations. Every Error Catalog row is answered by its operations in `api-spec-inv.yaml`, and the reverse also holds (0 mismatches). The SRS and contract `given-usage` blocks are still identical. I could not run `gov.py analyze`: there is no Python here, and I don't have read access to `governance-tools`. So the analyze result is still to be confirmed, including whether `refs-exist` accepts the ADR-PTY-039, ADR-PTY-047 and ADR-CAT-061 citations the reviewer asked for.

**Choices I made where a finding offered options:**
- **G3 (who checks permissions):** the REST services carry `@PreAuthorize` on every method. `InventoryApi` goes to a new internal package, `com.commerce.inv.engine`, which has no permission check. ADR-INV-042 lists all 14 methods that skip the check and why, and states how this differs from ADR-PTY-039.
- **G4 (unit-of-measure key):** it is now the setting `INV_MEASURE_UOM_KEY`, seeded with `CAT_UOM`. That is the key CAT's own SRS uses; CAT's contract doesn't publish one. A platform finding asks CAT to publish it (ADR-INV-043).
- **G11 (count dead end):** when a count raises a Variant that has never been valued, the increase is valued at 0 and creates no valuation row. ADR-INV-030 is extended.
- **G12 (opening stock sheet):** I took the reviewer's fallback. CAT v2 is already delivered without a lookup by Variant code, so the sheet column is now "Barcode" only. This changes AC-INV-079, AC-INV-080, REQ-INV-073 and the RULE-INV-028 message, which is now «لا يطابق هذا السطر متغيّر صنف نشطًا.» / "This row matches no active Variant.". A platform finding asks CAT for the lookup by code.

**New ids:**
- RULE-INV-049 (an issue can only consume its own reservation), with the in-process code `INV-422-RESERVATION-MISMATCH`.
- ADR-INV-041 … 045. The next free ADR is ADR-INV-046.
- Backend test cases TC-INV-242 … 261 (concurrency, checks on post, decimal limits, tenancy of native queries, the module manifest and boundary rules).
- Frontend test cases TC-INV-262 and 263 (G13 and G15 on the screen).
- A PLATFORM FINDINGS section in `business-policies-inv.md` with three rows. Row 3 cites PTY's existing finding about the CAT package rename.

I regenerated the traceability index tables with a small script and checked its output.

**Left for you or the orchestrator:**
- **G20** asks the parser owner why C10.7–9 and C7.24 found nothing. That needs someone with the analyzer.
- **`platform/contracts/contract-inv.md`** (the published contract) was not in my writable set. It still lacks the CON-INV-015 change: one added error meaning (reservation not of this line) and a line about concurrent repeated keys. It needs republishing.