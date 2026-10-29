# ADR-INV-010 — An expired lot is excluded from issue for sale and leaves stock only through a stock adjustment
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:34:05+00:00
Dialogue-key: f9a805f433a4
traces      : POL-INV-007, POL-INV-008, POL-INV-022

## Decision
Accept option A. Selling expired goods is prohibited in Egypt and KSA *(not live-verified)*. Scope amendment: the exclusion covers sale issues (SLS, POS, SFR) and FEFO selection. The lot stays in stock and visible in near-expiry reporting until written off, and transfers are not barred. It binds POL-INV-022.
traces: POL-INV-022, POL-INV-007, POL-INV-008

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0-round2.response2.md
