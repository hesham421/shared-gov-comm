# ADR-CAT-008 — A Variant carries tax-classification codes tagged by scheme, with schemes owned by TAX
Status      : RESOLVED-IN-DIALOGUE
Stage       : domain-profile        Module: CAT        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-22T14:34:22+00:00
Dialogue-key: 4d603a9614d0
traces      : —

## Decision
Amend P-08. Round 1 named ETA GS1/EGS directly, but that is unverified, so the decision is scheme-agnostic: CAT stores the codes, and TAX defines the schemes as `mdl` lookups and validates them through a crossmodule API. This avoids the reverse TAX→CAT dependency the opus proposal rejected. Verification of per-country requirements is handed to the TAX stage.
traces: KB §2, §5 #8; G5, G15; block 9 (NO-RESEARCH)

Source      : governance-shared\analysis\modules\CAT\_state\briefs\domain-profile-round2.response2.md
