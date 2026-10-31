# ADR-INV-017 — A measure-only adjustment move carries quantity 0
Status      : ACCEPTED
Stage       : P2 (Database)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : ENT-INV-004, DBF-INV-042, DBF-INV-043, REQ-INV-123, REQ-INV-099, RULE-INV-017, RULE-INV-034, AC-INV-137

Filed from db-script-inv.md §5.2, where the decision is recorded in full; the text is unchanged.

## Context
SRS ENT-INV-004.quantity is typed "> 0" (RULE-INV-017 for receipts, issues, reservations and transfers),
while REQ-INV-123 posts an adjustment line of a dual-quantity Item whose count change is zero and whose
measure change is non-zero "as a measure-only move" (AC-INV-137: on-hand stays 3, measure 120.500 →
120.000). The move needs a quantity value.

## Decision
`CHK_INV_STOCK_MOVE_QUANTITY`: QUANTITY > 0, or QUANTITY = 0 only on an ADJUSTMENT_IN / ADJUSTMENT_OUT move
whose MEASURE_QTY is greater than 0; the direction of the measure follows the move type, as for the
quantity. Every other move keeps QUANTITY > 0, so RULE-INV-017 holds where it applies.

## Consequences
- A measure-only move has MOVE_VALUE 0 and leaves the average cost unchanged.
- ACC receives it through the stock-move event with quantity 0. P3.1 documents this on CON-INV-020 (no field
  changes).

## Status
ACCEPTED — non-breaking.
