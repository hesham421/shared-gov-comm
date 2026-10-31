I've fixed both C-GIVEN-2 findings in the SRS by rewording two table cells so they no longer name either interface. I haven't re-run `gov.py analyze`, so the gate result isn't confirmed yet.

**Before re-running the check:** `_state/current-srs.md` still has the old wording. It is generated from the SRS, so it needs to be regenerated first. If `analyze` reads that copy, the findings will appear again.

**How I fixed it:** INV v1 doesn't use `OutboxSubscriber` or `FilePrivateStoreApi`. Both names appeared only in the "Override / status" column of the SRS decisions table, which lists the options that were *not* chosen. Adding `given-usage` rows would declare uses that don't exist, so I removed the names instead. This is what ADR-INV-018 decided and what ADR-INV-019 set as its unblock condition.

**Changes:**
- **`analysis/modules/INV/P1/srs-inv.md`**: two cells in "Decisions applied" reworded, with the same meaning:
  - D-13: "Server-side import through FilePrivateStoreApi when the core publishes the read" → "Server-side import through the given file module once the core publishes the read"
  - D-23: "Subscribe to their events through OutboxSubscriber" → "Subscribe to their events through the given events module"
  - No P1 file names either interface now. The `given-usage` blocks in the SRS and the contract are unchanged and still match, and the contract needed no edit.
- **`analysis/decisions/INV/ADR-INV-020.md`** (new, ACCEPTED): records the choice to stop naming the interfaces rather than add rows, and that this meets ADR-INV-019's unblock condition.
- **`P2/db-script-inv.md`**: decision records updated. The header now counts 7 ADRs (ADR-INV-014 … ADR-INV-020), §5.2 gains ADR-INV-020 in full, the BLOCKED note and the registry decisions line say the unblock condition is met, and the next free ADR is ADR-INV-021. No DBF, XM or SQL changed.
- **`P2/registry-db-inv.md`**: the Decisions line now lists ADR-INV-020, says ADR-INV-019's unblock condition is met, and gives the next free ADR as ADR-INV-021.

**Decision for you:** I left ADR-INV-019 as BLOCKED, because an existing ADR is never rewritten under its id and the human owner resolves it. Its condition is now met, so once the findings clear you can set it to ACCEPTED. The P2 "Event" line still reads "BLOCKED by ADR-INV-019" until that happens.