# ADR-INV-030 — "Never valued" means no valuation row; how the row is created
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : RULE-INV-025, REQ-INV-059, REQ-INV-060, REQ-INV-061, REQ-INV-064, REQ-INV-066, ENT-INV-013, QR-INV-019, QR-INV-023, QR-INV-024

## Context
RULE-INV-025 refuses a costless incoming line when "the Variant has no Stock valuation". Negative stock is
allowed by default (REQ-INV-066), so a Variant can be issued before it was ever received; its outgoing moves
need a unit cost (UNIT_COST is NOT NULL) although no average exists. Creating a valuation row with average 0
on that issue would let later costless receipts enter at 0, which RULE-INV-025 forbids.

## Decision
- "Never valued" ≡ no INV_STOCK_VALUATION row for the Variant.
- An outgoing move (or transfer leg) of a never-valued Variant is recorded at unit cost 0 and value 0 and
  creates no valuation row.
- The first incoming valued move creates the row with TOTAL_ON_HAND_QTY = the Variant's on-hand over all
  warehouses after the move (QR-INV-019, which may reflect earlier negative issues), AVERAGE_UNIT_COST = the
  move's unit cost (consistent with REQ-INV-060), STOCK_VALUE = total × average, DOC_DATE = the move date.

## Consequences
- RULE-INV-025 keeps its meaning; the valuation total always equals the on-hand sum once the row exists.
- Issues before the first valued receipt carry cost 0 in the stock-move event; the ledger owner sees them
  as such.

## Status
ACCEPTED — non-breaking.
