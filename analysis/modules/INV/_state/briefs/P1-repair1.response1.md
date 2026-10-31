I fixed all 7 findings by editing the two P1 files in place. I haven't re-run `gov.py analyze`, so whether it now passes is still to be confirmed.

**EARS findings (C5.2) in `srs-inv.md`.** The checker rejects "While/Where …, if …, then the system shall …". Statements in the "If … while …, then the system shall …" form already pass elsewhere in this file, so I rewrote these four that way and changed their Pattern from `complex` to `unwanted`. Each one means the same as before.
- `REQ-INV-044`: "If a reservation request exceeds the available quantity while the tenant disallows negative stock, then the system shall refuse the reservation."
- `REQ-INV-045`: "If a reservation request exceeds the available quantity while the tenant allows negative stock, then the system shall create the Reservation with the uncovered quantity recorded as its shortfall."
- `REQ-INV-067`: "If an online issue, transfer or stock adjustment would take the on-hand quantity of a Variant in a warehouse below zero while the tenant disallows negative stock, then the system shall refuse it."
- `REQ-INV-083`: "If posting a transfer would take the source warehouse's on-hand quantity of a line's Variant below zero while the tenant disallows negative stock, then the system shall refuse the posting."

**Registry findings (C5.8) in `registry-srs-inv.md`.** The "Next free: …" sentence named ids that don't exist yet (`AC-INV-155`, `ENT-INV-015`, `RULE-INV-049`, plus `REQ-INV-139` and `SCR-REQ-INV-013`), and the checker read them as registered. I replaced it with "Each sequence continues from the next number after its last sequence." The last-sequence numbers (REQ 138, AC 154, ENT 14, RULE 48, SCR-REQ 12) stay as they were.

No ids were re-numbered and nothing else was changed. None of the fixes needed a design choice, so I didn't write an ADR.