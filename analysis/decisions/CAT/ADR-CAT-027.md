# ADR-CAT-027 — CAT owns the item tax class and the e-invoice item code; TAX owns rates and rules, and TAX reads CAT
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: f59c7496f14f
traces      : POL-CAT-006

## Decision
traces: OI-11, CAND-TAX-001, CAND-CAT-002
Accept A. Option B would be an upward edge from tier 1 to tier 4. The Item carries a tax class from the CAT-owned lookup. The Variant carries the item code (type and value), defaulted from the Item. Unit values carry the e-invoice unit code. TAX maps class × country to rate and category. I add one rule so POL-CAT-006 holds: CAT never blocks item creation for lack of an e-invoice code. TAX decides at submission what its country adapter needs. The ETA and ZATCA specifics are recalled and `NO-RESEARCH`; they affect lookup values only, not ownership.
Sources: KB §2, §5 #8; ETA SDK and ZATCA XML standard (`NO-RESEARCH`).

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
