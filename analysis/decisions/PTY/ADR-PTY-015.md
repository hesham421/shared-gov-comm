# ADR-PTY-015 — Guest matching and conversion to a CUSTOMER-realm account

Status  : ACCEPTED (non-breaking) · Stage : P1 · Module : PTY v1 · Date : 2026-10-29

## Context
KB §3 D5 / DP §5 G9: the guest becomes a CUSTOMER-realm account after the first order. The PRD cites module-registry decision 5: a guest who is already a Customer is attached to that Customer and is linked "through a `sec` customer invitation, never by an unverified contact". The matching keys, the contact formats, how the account is created for a brand-new guest, and how ambiguous matches are handled were not stated.

## Decision
- Matching runs over active Parties only, excluding the walk-in Customer's Party: first by email (case-insensitive; email is unique among active Parties, D-03), then by mobile when exactly one active Party matches after normalisation (remove spaces and dashes, a leading "+" or "00").
- A match on a Party without a Customer adds a Customer (RETAIL). A match whose Customer is inactive is refused with a message that does not reveal the match. A mobile matching more than one Party creates a new Customer rather than guessing.
- Guest details never change a matched Party's names, contacts or addresses.
- Both for a new Customer and for a matched Customer without a linked user, PTY requests a CUSTOMER-realm invitation from given `sec` (SecCustomerInvitationApi) — for a matched Customer addressed to the contact recorded on the Party, not the one typed by the guest. `secUserId` is set only when `sec` reports the invitation accepted; one user links to one Customer.
- Contact formats: email = one "@", non-empty local part, domain with a "."; mobile = optional "+" and 7–15 digits (ITU-T E.164 maximum length).
- Guest details must carry an email or a mobile.

## Consequences
- No account is reached by typing someone else's contact; duplicates are avoided whenever the match is unambiguous.
- Shared family mobiles can still produce a second Customer; the Owner can merge manually in a later version.

## Traces
US-PTY-009, US-PTY-010 · ENT-PTY-001, ENT-PTY-002, ENT-PTY-004 · REQ-PTY-011, REQ-PTY-050 … REQ-PTY-063 · RULE-PTY-003, RULE-PTY-004, RULE-PTY-013, RULE-PTY-019, RULE-PTY-020, RULE-PTY-021
