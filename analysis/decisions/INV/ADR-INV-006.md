# ADR-INV-006 — CAT owns the bundle composition; INV explodes a bundle into component stock moves; no bundle stock and no assembly
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:34:05+00:00
Dialogue-key: 804c950cac54
traces      : POL-INV-014

## Decision
Accept option A. Bundle availability is derived from component availability. Option B needs assembly orders, which is light manufacturing, excluded by D1 and KB §9. The policy is reworded to a single EARS pattern ("Where …, the system shall …"), because the round-1 draft combined `Where` with `when`. It binds POL-INV-014.
traces: OI-09, POL-INV-014

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0-round2.response2.md
