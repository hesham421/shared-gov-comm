# ADR-INV-019 — P2 cannot clear the C-GIVEN-2 findings raised on the P1 SRS text; the pass stops
Status      : ACCEPTED (unblocked 2026-10-31)
Stage       : P2 (repair round 2)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : ADR-INV-018, SRS D-13, SRS D-23, REQ-INV-043, REQ-INV-051, REQ-INV-052, REQ-INV-070, REQ-INV-071, REQ-INV-073

## Context
`gov.py analyze` refused P2 twice (round 1 and repair round 1) with the same two MAJOR C-GIVEN-2
(given-via-extension) findings on artifact `srs`:
- `srs` names `OutboxSubscriber` (erp-core:modules.events) with no `given-usage` row;
- `srs` names `FilePrivateStoreApi` (erp-core:modules.file) with no `given-usage` row.

Repair round 1 took the finding's "stop using it" branch (ADR-INV-018, ACCEPTED). INV v1 uses neither
interface: the names sit only in the "Override / status" column of SRS "Decisions applied", rows D-23 and
D-13. That column lists the alternative that was **not** chosen. ADR-INV-018 gave the replacement wording for
the two cells, but the cells are in the SRS (`analysis/modules/INV/P1/srs-inv.md`, carried into
`_state/current-srs.md`). P2 does not own the SRS and its write set is the two P2 files. Neither P2 file
names either interface. The check reads the SRS text only, so no edit to a P2 artifact can change its result.
A further P2 repair round would produce the same refusal.

Both of the finding's remedies need a P1 change:
- "add the row" — the SRS `given-usage` block and the contract `given-usage` block (P1, P1.5);
- "stop using it" — reword the two SRS override cells (P1).

## Decision
Stop the P2 pass as BLOCKED rather than run another repair round that cannot succeed.
- ADR-INV-018 stands: INV v1 declares no use of either interface. Adding `given-usage` rows would declare
  uses that do not exist, and for the subscriber it would invite an upward dependency that the DAG forbids.
- The P2 artifacts are complete and are kept as written. DBF-INV-001 … DBF-INV-219, XM-INV-001 …
  XM-INV-013, the SQL script, the seed blocks and ADR-INV-014 … 018 are unchanged. Only the decision
  records cite this ADR.
- This is not a structural or meaning conflict with the SRS. It is BLOCKED because the stage's exit
  condition depends on an artifact the stage does not own.

## Unblock condition
Reword the two cells in the P1 SRS through a P1 repair or an authorised edit of
`analysis/modules/INV/P1/srs-inv.md`, then regenerate `_state/current-srs.md`. The meaning of the cells
stays the same:
- D-23 "Override / status": `Subscribe to their events through OutboxSubscriber` →
  `Subscribe to their events through the given events module`
- D-13 "Override / status": `Server-side import through FilePrivateStoreApi when the core publishes the read` →
  `Server-side import through the given file module once the core publishes the read`

Then re-run `gov.py analyze` for P2. No P2 file needs to change for the findings to clear. When they clear,
this ADR becomes ACCEPTED (unblocked) and P2 proceeds to P3.1.

## Consequences
- P2 does not pass its gate until the unblock condition holds. P3.1 does not start.
- No DBF, XM, table, column, constraint or seed row changes, now or when the unblock condition holds.
- The contract (P1.5) needs no change under the "stop using it" branch, because neither name appears in it.

## Status
ACCEPTED — resolved by ADR-INV-020 (2026-10-31). The ADR was raised BLOCKED in P2 repair round 2, because the
stage's exit condition depended on a P1 artifact outside the P2 write set. ADR-INV-020 reworded the two SRS cells
with no change of meaning, the unblock condition holds, and P2 is complete (see Resolution).

## Resolution (2026-10-31, under Hesham's standing authorisation)
The unblock condition holds. Factory fix `149c8d5` lets a stage repair edit the earlier file a finding
names. The re-run P2 repair reworded the two SRS cells exactly as stated above, with no change of meaning
(ADR-INV-020). `gov.py analyze stage:P2` now reports 0 critical · 0 major · 0 minor. No P2 artifact
changed for the unblock. Status: ACCEPTED, so P2 proceeds to P3.1.
