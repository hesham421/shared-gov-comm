# ADR-PTY-006 — PTY owns customer credit limit and payment terms; STL contributes open exposure through a PTY extension point; credit limit is an attribute of Customer
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: 5cc9f41f24a6
traces      : POL-PTY-003, POL-PTY-004, POL-PTY-005

## Decision
Accepted (option C). Option A forces SLS (tier 3) to read STL (tier 4). Option B has the same upward edge through events. With C, STL depends on PTY, which the graph already says, and PTY knows no consumer.
Amendments:
- The credit limit is an attribute of Customer, not an entity. v1 has one limit per customer in one currency, and it has no lifecycle of its own. Payment term is an entity, because customers and suppliers reuse named terms. CAND-PTY-004 resolves to Payment term, and CAND-STL-003 is withdrawn.
- A contributor answers one thing: the customer's open exposure. PTY evaluates exposure plus the new amount against the limit and returns a verdict. SLS and SFR enforce it.
- A customer with no limit set is not checked.
- If `b2bCredit` is off, no limit exists and the check is inert.
traces: OI-16, CAND-PTY-004, CAND-STL-003, POL-PTY-003, POL-PTY-004, POL-PTY-005

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md
