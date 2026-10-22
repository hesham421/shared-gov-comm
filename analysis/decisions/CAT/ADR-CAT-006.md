# ADR-CAT-006 — CAT owns bundle composition and INV/SAL explode it
Status      : RESOLVED-IN-DIALOGUE
Stage       : domain-profile        Module: CAT        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-22T14:34:22+00:00
Dialogue-key: b4dbee2b6acc
traces      : —

## Decision
Accept P-06. Catalog has no stock dependency in the DAG, and the storefront can show a bundle without reading inventory. Bundle stock is derived from components. Nesting is a field-level rule handed to the CAT requirements stage.
traces: KB §4.1, §5; G5

Source      : governance-shared\analysis\modules\CAT\_state\briefs\domain-profile-round2.response2.md
