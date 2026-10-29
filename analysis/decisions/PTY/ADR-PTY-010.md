# ADR-PTY-010 — Module edges are HARD-FK except ACC, APR, SFR and INS (SOFT-READ); party data reaches TAX and SHP on the document's party snapshot
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: 7201800806d1
traces      : —

## Decision
Accepted. KB §5 states the dependencies and DP §6 gives their kinds. A snapshot on the issued SLS or PUR document is needed anyway, so a legal document does not change when a party is edited. TAX reads the buyer data and SHP the delivery address from the document, so neither gets a PTY edge.
Note: APR depends on the document-producing modules of the graph (INV, PUR, SLS, STL), following "any document module" [KB §5 #11]. APR's own P0 run may extend that, and `gov.py` recomputes the tiers.
traces: OI-17

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md
