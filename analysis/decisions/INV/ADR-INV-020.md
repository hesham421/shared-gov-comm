# ADR-INV-020 — The SRS D-13 and D-23 override cells name no given interface (C-GIVEN-2 cleared on the SRS side)
Status      : ACCEPTED
Stage       : P2 (repair round 1 of the re-run)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : ADR-INV-018, ADR-INV-019, SRS D-13, SRS D-23, REQ-INV-043, REQ-INV-051, REQ-INV-052, REQ-INV-070, REQ-INV-071, REQ-INV-073

## Context
`gov.py analyze` refused P2 again with the same two MAJOR C-GIVEN-2 (given-via-extension) findings on
artifact `srs`. The SRS names `OutboxSubscriber` (erp-core:modules.events) and `FilePrivateStoreApi`
(erp-core:modules.file), and no `given-usage` row declares either use. ADR-INV-018 chose the finding's
"stop using it" branch. ADR-INV-019 stopped the pass because the fix belongs in the P1 SRS, which P2 did not
own. This repair brief authorises an in-place edit of `analysis/modules/INV/P1/srs-inv.md` for these
findings, which is the unblock condition ADR-INV-019 names.

The finding offers two fixes. One is to add the `given-usage` rows to the SRS and repeat them in the
contract. The other is to stop using the interfaces.

## Decision
Stop using the interfaces, as ADR-INV-018 decided. INV v1 uses neither interface: both names appear only
in the "Override / status" column of SRS "Decisions applied", which lists the alternative that was not
chosen. A `given-usage` row would declare a use that does not exist. For the subscriber, it would also
invite an upward dependency on PUR, SLS and SFR that the DAG forbids.

The two cells are reworded with no change of meaning, using the wording that ADR-INV-018 and ADR-INV-019 gave:
- D-13 "Override / status": `Server-side import through FilePrivateStoreApi when the core publishes the read` →
  `Server-side import through the given file module once the core publishes the read`
- D-23 "Override / status": `Subscribe to their events through OutboxSubscriber` →
  `Subscribe to their events through the given events module`

The `given-usage` blocks of the SRS (A9) and the contract (§2.5) are unchanged and stay identical. The
contract names neither interface.

## Consequences
- The two C-GIVEN-2 findings are fixed on the SRS side. No P1 file names either interface.
- The unblock condition of ADR-INV-019 now holds once `_state/current-srs.md` is regenerated from the
  edited SRS. ADR-INV-019 is not rewritten under its id; its resolution (BLOCKED → ACCEPTED) is left to the
  human who owns it.
- No DBF, XM, table, column, constraint, seed row, REQ, RULE, ENT or contract item changes.
- If either alternative is adopted later, that is an SRS change. It adds the matching `given-usage` row to
  the SRS and repeats it in the contract in the same change.

## Status
ACCEPTED — non-breaking.
