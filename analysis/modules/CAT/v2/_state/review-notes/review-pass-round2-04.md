# C10.8 / C10.9 reported nothing, and that hides a contradiction (G21 becomes MAJOR)

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T08:55:57+00:00

ADR-CAT-053 dropped BAG and H87 because "no named add-value call exists". Yet TC-CAT-037, 068, 137 and 139 still create CAT_BRAND values through that same unnamed mdl maintenance call. Either these TCs break C10.8, or ADR-CAT-053's reasoning is wrong. The previous gate's G12 asked for a CORE-GAP ADR, as checklist line F9 requires. Round 1 dropped that request, and I restore it in G15.
