# ADR-PTY-041 — Inactive Customers and Parties on credit operations and Address writes; open requests on deactivation and while the flag is off

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise (G12) · Module : PTY v1 · Date : 2026-10-29

## Context
RULE-PTY-005 refused an inactive Customer or Supplier only when a consumer read it for a new document. The SRS said nothing about the other cases:
- a credit check, a credit limit save, or a Credit override request created or used for an inactive Customer;
- an Address written on an inactive Party;
- PENDING or APPROVED requests that outlive their Customer's deactivation, or that meet a `b2bCredit` flag switched off.

## Decision
1. **Refused for an inactive Customer** (RULE-PTY-005, extended; REQ-PTY-110): a credit check (API-PTY-035), a credit limit or payment terms save (API-PTY-017), and the creation (API-PTY-019) or use (API-PTY-036) of a Credit override request. The refusal is PTY-422-ROLE-INACTIVE with the Customer's Party code.
2. **Refused for an inactive Party** (a Party with no active role, DBF-PTY-008 false): an Address create, update or set-default (API-PTY-011, 013, 015). The refusal is PTY-422-ROLE-INACTIVE with the Party code. Address deactivation stays allowed (clean-up).
3. **Stay allowed for an inactive Customer**: reads (API-PTY-002 without forNewDocument, 016, 020, 030, 032), credit history (API-PTY-031), Clear credit (API-PTY-018), and the cancellation of a request. A deactivated Customer can therefore still be cleaned up. In particular, Clear credit still lowers the `b2bCredit` switch-off count.
4. **Open requests on Customer deactivation** (REQ-PTY-111): API-PTY-004 sets every PENDING or APPROVED request of the Customer to CANCELLED in the same transaction. Two system transitions are added to the A7 lifecycle: PENDING → CANCELLED and APPROVED → CANCELLED, both "on deactivation of the Customer". An approval never outlives its Customer's deactivation. Reactivating the Customer does not restore a cancelled request; a new one is raised.
5. **While `b2bCredit` is off** (RULE-PTY-023, extended; REQ-PTY-112): approve, reject and use are refused with PTY-422-CREDIT-NOT-ENABLED. The requests keep their status and resume when the flag is turned on. Cancel and the reads stay allowed. A sale made while the flag is off needs no approval (the credit check answers NOT_APPLICABLE, REQ-PTY-074).
6. RULE-PTY-005's message is unchanged and names the record's code. No new catalog code is needed: the existing rows gain endpoints in the Error Catalog and in api-spec-pty.yaml.

## Consequences
- SRS: REQ-PTY-110, REQ-PTY-111 and REQ-PTY-112 are added with their ACs. RULE-PTY-005 and RULE-PTY-023 are extended, and A7 gains the two system transitions.
- Contract: CON-PTY-005 gains the lifecycle transitions. CON-PTY-015, 016 and 018 gain the inactive refusal, and CON-PTY-018 gains the credit-not-enabled refusal. These are additive error meanings, and no consumer has been built yet.
- Backend plan: API-PTY-004 adds QR-PTY-039 to its orchestration. Address writes check DBF-PTY-008 under the Party lock.

## Traces
RULE-PTY-005, RULE-PTY-023, RULE-PTY-026 · REQ-PTY-003, REQ-PTY-110, REQ-PTY-111, REQ-PTY-112 · API-PTY-004, 011, 013, 015, 017, 019, 021, 022, 035, 036
