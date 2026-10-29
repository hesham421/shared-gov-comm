# ADR-PTY-038 — The party-role-deactivated event travels on the given outbox

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise (G4) · Module : PTY v1 · Date : 2026-10-29

## Context
CON-PTY-021 promises consuming modules a party-role-deactivated event. The P3.1 plan published it through `DomainEventPublisher`. The erp-core checklist does not allow that: F11 forbids a business event to another module through `DomainEventPublisher`, and C19 requires `OutboxPublisher.publish("<code>.<Name>", payload)` inside the writing transaction, with subscribers that are idempotent on `OutboxMessage.id()`. An in-process event cannot be replayed to a module enabled later.

## Decision
1. API-PTY-004 and API-PTY-009 call `OutboxPublisher.publish("pty.PartyRoleDeactivated", payload)` inside the deactivation transaction, after the role's update. The payload is plain values: `{role: CUSTOMER | SUPPLIER, identifier: customerPk or supplierPk, code: Party code}`. A rollback publishes nothing. The given delivers to every subscriber of an entitled module after commit, at least once.
2. A deactivation of a role that is already inactive publishes nothing (idempotent, as before).
3. Every subscriber is an `OutboxSubscriber` whose `eventTypes()` holds `pty.PartyRoleDeactivated`, and it must be idempotent on `OutboxMessage.id()`. The contract (CON-PTY-021) states this, and the subscribing modules implement it.
4. The module manifest lists `events.publishes: [pty.PartyRoleDeactivated]`. PTY creates no event table and no relay.
5. PTY keeps `DomainEventPublisher` only for its own in-process after-commit events, which only PTY's own listeners consume (backend plan CORE). No other module consumes them.

## Consequences
- The payload of CON-PTY-021 does not change. The delivery channel changes and the type name is fixed as `pty.PartyRoleDeactivated`. No consumer has been built, so the change breaks nobody.
- TC-PTY-072 observes the event at the given `OutboxPublisher`.

## Traces
CON-PTY-021 · REQ-PTY-023 · RULE-PTY-006 · API-PTY-004, API-PTY-009
