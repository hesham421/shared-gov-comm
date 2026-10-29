# ADR-PTY-032 — The XM total counts the XM ids the db-script names

Status  : ACCEPTED (non-breaking) · Stage : P3.1 (repair round 1) · Module : PTY v1 · Date : 2026-10-29

## Context
The plan's `totals` block must state `XM` as "the number of distinct `XM-*` ids in `db-script`" (engine §3; profile `declared_totals`, analyze `count-agrees`). The db-script's XM register is `records: []`, and no edge exists (SRS A8 `consumes: []`). Its §6 registry content does name one XM id, `XM-PTY-001`, as the *next free* XM id. `gov.py analyze` counts that id, so it refused `XM: 0` (C7.18, MAJOR). This stage cannot edit the db-script, where the wording would otherwise be fixed.

## Decision
- `totals` states `XM: 1`, the number of distinct `XM-*` ids the db-script carries, as the totals rule defines it.
- The plan says once, directly below the block, that this one id is the next-free pointer and that there are 0 integration edges. The header and the registry describe edges as "integration edges 0" or "XM register empty" rather than as an `XM` count, so no second statement of the total disagrees with the block.
- Nothing else changes: the register has no record, `INT-C` carries no block, and this stage mints no XM.

## Consequences
- A reader of `totals` alone sees 1. The note under the block and this ADR say it is a named id, not an edge.
- The db-script owner (P2) can avoid this in a later version by not naming the next-free XM id when the register is empty. The total then returns to 0 without any change to this plan's meaning.

## Traces
backend-execution-plan-pty.md (`totals`, Index) · registry-exec-be-pty.md (Integration, Event) · db-script §3 (`xm-register`), §6 · registry-db (Sequences)
