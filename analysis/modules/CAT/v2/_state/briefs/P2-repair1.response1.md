I removed the 12 tenant foreign-key constraints that `gov.py analyze` flagged under C6.15. I haven't re-run `gov.py analyze`, so I don't know yet whether it now passes.

- **`db-script-cat.md`:**
  - The 12 `ALTER TABLE … FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID)` statements (lines 738–749) are gone. Their section now carries a one-line comment saying `TENANT_ID` is column-only.
  - I also updated the header counts to "tenant FK 0" and rewrote D-P2-03 to match.
  - I changed the stale "0 ADR / next free 044" wording to say ADR-CAT-054 was raised and the next free ADR is 055.
- **`registry-db-cat.md`:** the "Tenant constraint … (12 FKs)" line now says `TENANT_ID` is column-only.
- **`analysis/decisions/CAT/ADR-CAT-054.md`:** new ADR, status ACCEPTED. It records that `CORE_TENANT` belongs to the given erp-core, so the reference is column-only and the given's tenancy layer enforces tenant validity.

I didn't create an XM entry for the tenant, because the SRS declares no cross-module dependency (`consumes: []`) and an XM with no SRS origin would itself be a finding. All DBF ids, columns and counts are unchanged.

I did not edit `change-manifest.md`; the brief limits edits to the two P2 files, and nothing in it changes. The new ADR file sits outside that two-file list, which the brief's ADR rule calls for. Don't delete it.