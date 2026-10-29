# ADR-PTY-014 — Party identity with Customer and Supplier roles; credit terms on the Customer

Status  : ACCEPTED (non-breaking) · Stage : P1 · Module : PTY v1 · Date : 2026-10-29

## Context
The project registry lists Customer, Supplier, Address and "Credit limit & terms" as PTY candidates (CAND-PTY-001 … 004) and leaves the owner of credit limits and terms open (OI-16: Party vs Settlements). The PRD cites module-registry decision 4 ("one Party identity record per counterparty … adding the other role is an action in place") and decision 6 (credit limit and terms per customer, with the "Customer credit exposure" extension point). The profile's master defaults (nameAr, nameEn, code, isActiveFl) would be duplicated if Customer and Supplier each held them.

## Decision
- ENT-PTY-001 Party (master, SHARED owner) holds names, code, mobile, email, tax registration number and active state once.
- ENT-PTY-002 Customer and ENT-PTY-003 Supplier (master, SHARED owner) are role records linked to the Party (at most one of each per Party); their standard name/code fields are presented from the Party, not held twice. The Party is inactive when no role is active.
- Credit limit and payment terms are fields of the Customer (not a separate entity); PTY owns them, resolving OI-16 for v1. Settlements reports open exposure into PTY (ADR-PTY-017).
- Payment terms are a whole number of days (0–365), not a lookup, so that Settlements can compute due dates without a lookup attribute. Supplier payment terms are flag-independent (ADR-PTY-012).
- ENT-PTY-004 Address is SHARED (owner) with SLS, PUR, SFR as the module registry states, overriding the project registry's earlier PRIVATE candidate.

## Consequences
- Consumers reference Customer or Supplier identifiers (HARD-FK) and read names through them.
- A trader who is both customer and supplier is one Party; contact changes appear in both roles.
- Payment terms codes (e.g. NET_30) are not offered in v1; a later version can add a lookup without changing the stored days.

## Traces
US-PTY-001, US-PTY-003, US-PTY-004, US-PTY-005, US-PTY-011 · ENT-PTY-001, ENT-PTY-002, ENT-PTY-003, ENT-PTY-004 · REQ-PTY-004, REQ-PTY-016, REQ-PTY-024 … REQ-PTY-029, REQ-PTY-064, REQ-PTY-065 · RULE-PTY-008, RULE-PTY-009
