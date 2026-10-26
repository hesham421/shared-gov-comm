# ADR-CAT-024 — Category is a CAT-owned hierarchy; Brand and unit of measure are CAT-owned lookup types in the given `mdl`, with no entity ID
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: f9d73f2faf85
traces      : —

## Decision
traces: OI-08, CAND-CAT-004, CAND-CAT-008, CAND-CAT-009, CAND-OPEN-001, CAND-OPEN-002
Accept, with a changed reason. Round 1 said "mdl lists are flat", which the manifest does not state. The sound reason is that the profile defines Category as a hierarchical classification with its own consumers (browsing, reporting). The manifest lists `mdl` only as code lists and lookups. Brand and unit of measure are flat value lists, so they go to `mdl` as owned lookups (the `common` capability `owned-lookups`). That closes the four duplicate candidates without an entity ID.
Sources: DP §7.1; manifest `mdl`, `common`; profile LOV rule.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
