# ADR-INV-044 — Concurrent requests with one idempotency key serialise on an advisory lock
Status      : ACCEPTED
Stage       : gate analysis (revise 1, G6)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : RULE-INV-024, REQ-INV-058, REQ-INV-069, ADR-INV-026, ADR-INV-041, D-P2-05, DBF-INV-052, DBF-INV-075, CON-INV-011, CON-INV-014, CON-INV-015

## Context
IDEMPOTENCY_KEY is indexed but not unique (D-P2-05): one cross-module request records several moves or
reservations under one key (multi-line documents, bundle explosion, FEFO split). Reserve, receive and issue look
the key up (intents R-2 / R-6) before any balance lock is taken. Two simultaneous submissions with the same key —
for example a POS sync retried by the client while the first attempt is still running — both find nothing and both
record. No guard was named.

## Decision
- Receive, issue and reserve begin with
  `SELECT pg_advisory_xact_lock(hashtextextended(:tenantId || ':' || :idempotencyKey, 0))`, then run the R-2 / R-6
  lookup. The second submission waits until the first commits (it then finds the first's rows and returns them,
  recording nothing) or rolls back (it then records normally).
- The tenant is part of the hashed text, so tenants never wait for one another; the statement is native and follows
  ADR-INV-041.
- The lock is transaction-scoped: it is released at commit or rollback, with no cleanup.
- A hash collision between two different keys only serialises two unrelated requests; it never merges them, because
  the lookup still compares the key itself.
- IDEMPOTENCY_KEY stays a non-unique index; no schema change.

## Consequences
- RULE-INV-024 holds under concurrency.
- The lock is taken before the balance locks of ADR-INV-026, in the same order for every request, so it adds no
  deadlock path.
- A backend test case submits two requests with one key in parallel and asserts one recording and one equal answer.

## Status
ACCEPTED — non-breaking.
