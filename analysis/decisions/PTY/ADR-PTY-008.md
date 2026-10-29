# ADR-PTY-008 — Price lists follow customer groups; PRC reads PTY's customer-group lookup (SOFT-READ edge PRC → PTY)
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: b44ad66dacd2
traces      : —

## Decision
Accepted with a narrower scope. The `customer-group` lookup type is owned by PTY and held in `mdl`. A price list is "for a channel or customer group" [DP §7.1]. PRC reads that lookup, and only that. Reading a lookup is a read from PTY, so the edge is declared. PRC never reads a Customer. SLS and SFR pass the customer's group to the price resolution. PRC stays tier 2, and no lower tier depends on a higher one.
traces: CAND-PRC-001, OI-17

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md
