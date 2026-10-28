I added the missing `FilePrivateStoreApi` row to the `given-usage` block in both the SRS and the contract. I haven't re-run `gov.py analyze`, so the C-GIVEN-2 finding is fixed on paper but not yet confirmed by the gate.

The backend plan only names `FilePrivateStoreApi` in its CORE-GAP table. There it is the possible way to read the import file's type, size and bytes, which is still an open question to erp-core. So the new row says exactly that and nothing more. CAT stores no file through it, because the client uploads through the FILE endpoints. The read gets connected once the core publishes it.

**Changes:**
- **`analysis/modules/CAT/v2/P1/srs-cat.md`, A9 `given-usage`:** added the row `{erp-core · modules.file · com.erp.file.crossmodule.FilePrivateStoreApi · purpose}`, right after the `FileDocumentLookupApi` row. The purpose cites SCR-REQ-CAT-004, RULE-CAT-044, API-CAT-049, ADR-CAT-056, ADR-CAT-076 and CORE-GAP-file-document-metadata-read.
- **`analysis/modules/CAT/v2/P1_5/contract-cat.md`, §4 `given-usage`:** added the same row in the same position. A check confirmed both blocks now have the same 12 rows in the same order.

**Not changed:**
- `db-script-cat.md` and `registry-db-cat.md`: the finding doesn't touch any table, DBF or XM.
- No new ADR. The fix wasn't a real choice: the plan can't be edited in this stage, so the use had to be declared, and ADR-CAT-076 already covers it. The next free ADR is still ADR-CAT-077.
- No ids were renumbered and no question was raised.