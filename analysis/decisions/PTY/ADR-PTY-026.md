# ADR-PTY-026 — Concurrency guards; Address default on a type change

Status  : ACCEPTED (non-breaking) · Stage : P3.1 · Module : PTY v1 · Date : 2026-10-29

## Context
Several PTY writes decide on a value they have just read:
- the next Address ordinal;
- whether a new Address is the first of its type;
- whether a role is the Party's last active role;
- whether a Party already holds a role;
- the status of a Credit override request;
- whether the walk-in Customer exists;
- whether a guest's email already belongs to a Party.

Two simultaneous requests can both read and both pass. The db-script has no version column, so optimistic locking is not available. The SRS also does not say what happens to the default flag when an Address's type is changed.

## Decision
- **The Party row is the mutex** for everything scoped to one Party: role add, active-state derivation, Address ordinal, and default switching. QR-PTY-001 runs `SELECT … FOR UPDATE`. Locks are always taken Party first, then the child row, so no two transactions can deadlock on them.
- **Unique constraints and indexes of the db-script are the backstop** and are translated to catalog errors:
  - UQ_PTY_PARTY_EMAIL → PTY-409-EMAIL-TAKEN, naming the code of the Party that holds the email;
  - UQ_PTY_CUSTOMER_PARTY_FK and UQ_PTY_SUPPLIER_PARTY_FK → PTY-409-ROLE-HELD;
  - UQ_PTY_ADDRESS_PARTY_CODE and UQ_PTY_ADDRESS_IS_DEFAULT_FL → PTY-500-INTERNAL, which cannot happen under the Party lock.
- **Credit override transitions:** the row is locked (QR-PTY-033) and updated by compare-and-set on STATUS_CODE (QR-PTY-034). If the update changes no row, the request is re-read and RULE-PTY-026 (decisions) or RULE-PTY-028 (use) is returned.
- **Credit edits** lock the Customer row (QR-PTY-006), so every audit event's old value is the value the previous edit committed.
- **Walk-in Customer:**
  - creation runs in a REQUIRES_NEW transaction;
  - a violation of UQ_PTY_CUSTOMER_IS_WALK_IN_FL rolls back that transaction, including the Party row it created;
  - the existing walk-in Customer is then re-read and returned.
- **Guest resolution:**
  - Creation runs in a REQUIRES_NEW transaction.
  - If UQ_PTY_PARTY_EMAIL is violated, the matching is retried once, and the order attaches to the Party that won.
  - Mobile is not unique (SRS D-03), so two simultaneous mobile-only guests may create two Parties. This is the outcome REQ-PTY-061 already prescribes for an ambiguous mobile.
- **Business numbers** (Party code, docNo) are allocated atomically by the given NumberSeriesApi, backstopped by their unique constraints.
- **Address type change on update** (API-PTY-013):
  - If the Address was the default of its old type, it stops being the default. The most recently updated remaining active Address of the old type becomes the default, as when a default is deactivated (RULE-PTY-017).
  - In its new type, the Address becomes the default only if that type has no active Address.
- **Setting a default on an inactive Address** is refused as PTY-404-ADDRESS-NOT-FOUND, because an inactive Address cannot be the active default.

## Consequences
- Writes to one Party are serialised. This is acceptable, because a Party is edited by one user at a time in practice.
- No version column is needed. If one is added in a later version, the locks above may be replaced by optimistic checks without changing the API.

## Traces
QR-PTY-001, 004, 006, 008, 021, 024, 028, 029, 030, 033, 034, 036, 037 · API-PTY-001, 003 … 006, 008 … 011, 013 … 015, 017, 021 … 023, 033, 034, 036 · REQ-PTY-027, REQ-PTY-028, REQ-PTY-034, REQ-PTY-036, REQ-PTY-046, REQ-PTY-061, REQ-PTY-098, REQ-PTY-099 · RULE-PTY-002, RULE-PTY-009, RULE-PTY-014, RULE-PTY-017, RULE-PTY-026, RULE-PTY-028
