# ADR-CAT-025 — CAT owns the bundle composition; an Item is a bundle when it has composition lines; INV and SLS explode it to components; no pre-assembled kits
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 9c9c07fbe978
traces      : —

## Decision
traces: OI-09, CAND-CAT-005, CAND-INV-008
Accept A and withdraw CAND-INV-008. Assembling kits into stock is manufacturing, which D1 excludes. I amend round 1's "Item of the bundle kind". That would need an item-kind value list whose values drive behaviour, and the profile rule forbids hardcoded enums. Presence of composition lines plus the `bundles` flag defines a bundle instead. The bundle holds no stock. Bundle pricing is PRC's concern.
Sources: KB §3 D1, §4.1; profile LOV rule.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
