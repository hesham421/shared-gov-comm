# ADR-PTY-005 — sec` owns the CUSTOMER-realm account; PTY owns the commercial Customer; a guest whose contact matches an existing Customer is linked by invitation
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: d681a9b99992
traces      : POL-PTY-001, POL-PTY-002

## Decision
Accepted. Credentials, login and invitations are given `sec` (customer-accounts, customer-invitations). The Customer links to 0..1 account by SOFT-READ.
Precisions:
- A guest with no match becomes a new Customer, and the account is offered through `sec`.
- A guest with a match has the order attached to the existing Customer immediately, so the order is not held. The account link completes when the shopper accepts the invitation.
- Auto-linking on an unverified contact would hand a stranger the credit and price list of someone else's customer record. That is the reason for the invitation.
- POL-PTY-002 stays as the stated need (KB §3 D5). The mechanism lives here and in the registry.
traces: OI-13, CAND-PTY-001, POL-PTY-001, POL-PTY-002

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md
