# ADR-CAT-022 — Edge kinds follow domain-profile §6, plus the explicit edges TAX → CAT and APR → INV, PUR, SLS, STL
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 656cb7f0d1ef
traces      : —

## Decision
traces: OI-17
Accept. I re-checked the tier rule on the 14 edges, and no lower tier depends on a higher one. The tiers equal the longest-path levels: CAT and PTY 1; PRC and INV 2; PUR and SLS 3; STL, TAX, SWR and SHP 4; ACC, APR and SFR 5; INS 6. TAX → CAT is a declared read of the item tax class and e-invoice item code. APR → INV, PUR, SLS, STL makes "any document module" concrete. TAX, ACC and SWR produce no approvable document of their own.
Sources: DP §6; KB §5; `xm.tier_rule`.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
