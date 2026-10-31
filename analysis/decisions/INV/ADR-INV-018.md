# ADR-INV-018 — INV v1 uses no given events subscriber and no given file private store
Status      : ACCEPTED
Stage       : P2 (repair round 1)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : SRS D-13, SRS D-23, REQ-INV-043, REQ-INV-051, REQ-INV-052, REQ-INV-070, REQ-INV-071, REQ-INV-073

## Context
`gov.py analyze` (P2 round 1, C-GIVEN-2 given-via-extension) reported two MAJOR findings on the SRS:
it names `com.erp.events.OutboxSubscriber` (erp-core:modules.events) and
`com.erp.file.crossmodule.FilePrivateStoreApi` (erp-core:modules.file) and no `given-usage` row declares either use.

Both names appear only in the "Override / status" column of SRS "Decisions applied", i.e. as the
alternative that was **not** chosen:
- D-23 — chosen: PUR, SLS and SFR call INV operations; INV subscribes to none of their events (DAG stays
  downward-only). Override: "Subscribe to their events through OutboxSubscriber".
- D-13 — chosen: the browser reads the xlsx opening stock sheet and submits 1–1000 rows; INV parses no
  uploaded file. Override: "Server-side import through FilePrivateStoreApi when the core publishes the read".

No REQ, RULE, contract item, table, column, seed row or XM of INV v1 uses either interface.

## Decision
Take the finding's "stop using it" branch, not "add the row". INV v1 declares no use of either interface;
the `given-usage` blocks of the SRS and the contract are left unchanged, because a row would declare a use
that does not exist (and, for the subscriber, invite an upward dependency the DAG forbids).

The two SRS override cells are to be reworded at the next touch of P1 so that they describe the
alternative without naming the interface, with no meaning change:
- D-23 override → "Subscribe to their events through the given events module"
- D-13 override → "Server-side import through the given file module once the core publishes the read"

Adopting either alternative later is an SRS change that adds the matching `given-usage` row to the SRS
and repeats it in the contract in the same change.

## Consequences
- P2 is structurally unchanged: DBF-INV-001 … DBF-INV-219 and XM-INV-001 … XM-INV-013 stay as written.
- The P2 artifacts (db-script-inv.md §5.2 / §5.3, registry-db-inv.md Decisions) record this decision.
- The finding is raised on SRS text, which P2 does not write; it clears when the two P1 cells above are
  reworded.

## Status
ACCEPTED — non-breaking.
