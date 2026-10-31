# ADR-INV-055 — Releasing a reservation is idempotent; only a CONSUMED reservation is refused
Status      : ACCEPTED
Stage       : gate analysis (revise 5, G4)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : CON-INV-012, RULE-INV-021, REQ-INV-046, REQ-INV-048, AC-INV-051, AC-INV-053, ENT-INV-005, DBF-INV-074, DBF-INV-080, TC-INV-012, TC-INV-293

## Context
`releaseReservation(reservationPk)` (CON-INV-012) takes no idempotency key, unlike reserve, receive and issue
(RULE-INV-024). RULE-INV-021 refused every release of a reservation that is not ACTIVE. A caller (SLS cancelling an
order, SFR abandoning a checkout) that releases, loses the answer to a timeout and retries therefore received
INV-409-RESERVATION-CLOSED for a release that had succeeded, and saw a completed release as a failure.

Options: (a) add an idempotency key to CON-INV-012 — a signature change every caller must adopt; (b) make the release
itself idempotent: the target state of a release is RELEASED, so a reservation already RELEASED is the answer.

## Decision
Option (b).
1. A release of an ACTIVE reservation sets statusCode RELEASED, reservedQty 0 and closedAt = now (unchanged).
2. A release of a reservation whose statusCode is already RELEASED returns it unchanged — statusCode RELEASED,
   reservedQty 0 and its first closedAt — writes nothing, publishes nothing and raises no error.
3. A release of a CONSUMED reservation is refused with INV-409-RESERVATION-CLOSED (RULE-INV-021): its goods have left,
   and answering "released" would misstate what happened.
4. The consumption side does not change: an issue that references a reservation that is not ACTIVE (RELEASED or
   CONSUMED) is still refused (RULE-INV-021).
5. The release keeps its lock (R-4, FOR UPDATE), so two concurrent releases serialise: the first releases, the second
   finds RELEASED and returns it unchanged.

## Consequences
- Contract: CON-INV-012 gains the promise; its error "reservation closed" now applies to a CONSUMED reservation only.
  ADDITIVE — fewer refusals, no signature change; no consumer is built yet.
- SRS: RULE-INV-021 restated; REQ-INV-046 and REQ-INV-048 restated; AC-INV-053 now states the CONSUMED case.
- Backend plan: §1.3 RULE-INV-021, ENT-INV-005 state machine, `ReservationDomain.release()`, §5.1 row of
  INV-409-RESERVATION-CLOSED, SVC-API-INT "Release".
- Test: TC-INV-012 (CONSUMED release refused, issue on RELEASED refused) and TC-INV-293 (a retried release answers the
  RELEASED reservation with its first closedAt, availability rises once).

## Status
ACCEPTED — non-breaking.
