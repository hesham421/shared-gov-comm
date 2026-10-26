# ADR-CAT-021 — PTY owns credit limit and terms; SLS, STL and SFR read them; PRC takes no PTY edge
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: e764db4078b4
traces      : —

## Decision
traces: OI-16, CAND-PTY-004, CAND-STL-003
Accept PTY as owner and withdraw CAND-STL-003. KB §5 #4 gives "credit" to Party. I correct a round-1 slip: it said PRC reads credit data, but PRC depends only on CAT, and a PRC → PTY edge is not in the DAG. Only modules that already depend on PTY read it: SLS (credit check), STL (exposure and ageing) and SFR (wholesale checkout). PRC reads the `b2bCredit` flag, which is a `cu` read and creates no module edge. How a customer is matched to a price list is settled in PRC's own P0 without an upward edge.
Sources: KB §4.1, §5 #4, §5 #7; DP §6.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
