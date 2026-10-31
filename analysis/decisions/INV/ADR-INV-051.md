# ADR-INV-051 — Stock held without a lot stays countable, transferable and adjustable after lot tracking is switched on
Status      : ACCEPTED
Stage       : gate analysis (revise 3, G2)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-139, REQ-INV-112, REQ-INV-124, RULE-INV-037, RULE-INV-044, AC-INV-155, AC-INV-156, ENT-INV-007, ENT-INV-009, ENT-INV-011, ENT-INV-003, SRS D-18

## Context
RULE-INV-044 lets the Owner switch lot tracking on for an Item that already holds stock without a lot (SRS D-18
says such stock exists). RULE-INV-037 then refuses every transfer, count and decrease-adjustment line of that
Item that names no lot. The lot-less balance could never again be counted, transferred or written off; only FEFO
sales (REQ-INV-112) would drain it. The Owner had no next step (KB §6.5).

Options considered: (a) refuse to switch tracking on while lot-less stock exists — blocks a legitimate change and
forces a write-off and re-receipt of real goods; (b) migrate the lot-less stock into a synthetic lot — invents an
expiry date; (c) accept a line without a lot up to the quantity held without a lot.

## Decision
Option (c). For an Item whose lotTrackedFl is true while lotExpiry is on, a transfer, count or decrease-adjustment
line that names no lot is accepted when its quantity is not above the on-hand quantity of the Item's Variant held
without a lot in the line's warehouse (the source warehouse for a transfer). It posts against the lot-less balance
row (variant, warehouse, NULL), which engine step 1 already locks first. Beyond that quantity — including when the
lot-less on-hand is zero or below — the line answers INV-422-LOT-REQUIRED as before.

- Transfer: TRANSFER_OUT from the source's lot-less row, TRANSFER_IN to the destination's lot-less row.
- Count: the line's system quantity is the lot-less on-hand; a counted quantity above it answers
  INV-422-LOT-REQUIRED (found goods of a lot-tracked Item enter under a lot), so the difference is never positive.
- Decrease adjustment: |quantityChange| ≤ lot-less on-hand.
- The check runs on save against the on-hand then, and again on post (engine step 0, ADR-INV-045) under the
  balance locks, so a concurrent sale cannot make a saved line exceed the lot-less stock at posting.
- Increases of a lot-tracked Item still need a lot and an expiry (RULE-INV-035) — no lot-less stock is created.

## Consequences
- SRS: new REQ-INV-139, AC-INV-155, AC-INV-156; RULE-INV-037 states the exception.
- Backend plan: engine step 0, API-INV-022 / 023 / 024 / 029 / 032 / 036 / 037 / 038 validations.
- Frontend: the lot select of a transfer, count or decrease line offers «بدون تشغيلة» / "Without a lot" while the
  Variant holds lot-less stock in the warehouse; the server enforces the quantity.
- Test: TC-INV-286 switches tracking on for an Item with 5 lot-less units, then counts, transfers and writes them off.

## Status
ACCEPTED — non-breaking (no consumer is built yet).
