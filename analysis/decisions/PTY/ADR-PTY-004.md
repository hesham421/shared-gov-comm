# ADR-PTY-004 — One Party identity per counterparty, with Customer and Supplier roles
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: 0debf096c475
traces      : POL-PTY-001, POL-PTY-006

## Decision
Accepted (option B). A wholesale counterparty often both buys and sells. The tax identity and addresses are then held once. The glossary definitions of Customer and Supplier both start from "a party", so the model matches the steering language.
Amendments:
- The UI keeps two entry points, "Customer" and "Supplier", so the four-mandatory-input limit is unaffected. Adding the other role to an existing record is an action in place [KB §6.4].
- No AR/AP netting in v1. The model only keeps that possible.
- The comparison with SAP Business Partner, Odoo `res.partner` and Dynamics is general knowledge and was not researched live (NO-RESEARCH). The decision rests on the glossary and KB §1, not on it.
traces: CAND-PTY-001, CAND-PTY-002, CAND-PTY-003, POL-PTY-001, POL-PTY-006

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md
