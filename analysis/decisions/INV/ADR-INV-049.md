# ADR-INV-049 — Stock-move immutability is enforced in the database too (P2 delta)
Status      : ACCEPTED
Stage       : gate analysis (revise 2, G12) — delta to P2        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : RULE-INV-023, REQ-INV-055, ENT-INV-004, DBF-INV-040, D-P2-06, QR-INV-022

## Context
RULE-INV-023 (a stock move is never changed or deleted) was enforced only by the application: the repository
exposes no update or delete method (D-P2-06 left the trigger block empty). Direct SQL, a migration script or a
JPA dirty check on a loaded move could still change the stock ledger, and every balance and valuation is derived
from it.

## Decision
1. **Database.** db-script BLOCK 10 adds the function `INV_STOCK_MOVE_IMMUTABLE_FN()` and BLOCK 6 the trigger
   `TRG_INV_STOCK_MOVE_IMMUTABLE` — `BEFORE UPDATE OR DELETE ON INV_STOCK_MOVE FOR EACH ROW` — which raises
   `SQLSTATE 'P0001'` with the message `INV-409-STOCK-MOVE-IMMUTABLE`. Inserts are untouched.
2. **Application.** The `StockMove` entity is `@org.hibernate.annotations.Immutable`, so Hibernate never issues
   an UPDATE for it; the repository still exposes no update or delete method. CORE translates a database error
   whose message is `INV-409-STOCK-MOVE-IMMUTABLE` into that catalogue code (§5.1).
3. D-P2-06 is narrowed: the stock-move ledger is the one place where a trigger guards a rule; the other
   read-only rules stay service behaviour.

## Consequences
- A tenant deletion that wants to purge moves must drop the trigger in its own maintenance migration; INV v1
  deletes no tenant data.
- TC-INV-015 also asserts that a direct SQL UPDATE and DELETE of a move fail.
- No DBF, XM or contract change.

## Status
ACCEPTED — non-breaking.
