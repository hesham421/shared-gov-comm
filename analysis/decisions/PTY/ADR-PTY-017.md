# ADR-PTY-017 — Open exposure through contributors; credit check outcomes; flag switch-off answer

Status  : ACCEPTED (non-breaking) · Stage : P1 · Module : PTY v1 · Date : 2026-10-29

## Context
US-PTY-014 needs the Customer's open exposure before a credit sale. Receivables (STL) and open orders (SLS) live in modules that depend on PTY; the PRD cites the module-registry EXTENSION POINT "Customer credit exposure". US-PTY-013 needs `b2bCredit` switch-off to be blocked while credit data exists, but the owner of the capability-flag switch is still OPEN (registry OI-05, P-02).

## Decision
- PTY declares the credit exposure contribution: every downstream module that holds exposure registers as a contributor and reports a Customer's open exposure; PTY sums them, and uses 0 when none is registered (so PTY is usable before STL ships).
- A contributor that does not answer within 3 seconds makes the result EXPOSURE_UNAVAILABLE (never WITHIN_LIMIT on incomplete data).
- An empty credit limit means no credit allowance: the result is NO_CREDIT (the walk-in Customer always gets NO_CREDIT).
- Results come from lookup CREDIT_CHECK_RESULT: WITHIN_LIMIT, OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE, NOT_APPLICABLE (flag off). Every non-WITHIN result carries the tenant's credit-overrun action and whether an approved request is required.
- PTY answers the flag owner's switch-off question with the number of Customers carrying a credit limit or payment terms; a count above 0 refuses the switch-off with a message and a next action (open the Customer credit list filtered to those Customers).

## Consequences
- STL and SLS each implement one contributor in their own SRS; their XM edges point to PTY.
- The 3-second limit is a default; P3.1 may tune it without changing the outcome rule.
- Whichever module P-02 assigns as the flag owner calls PTY's dependency answer.

## Traces
US-PTY-011, US-PTY-012, US-PTY-013, US-PTY-014 · ENT-PTY-002 · REQ-PTY-068, REQ-PTY-074 … REQ-PTY-085 · RULE-PTY-024
