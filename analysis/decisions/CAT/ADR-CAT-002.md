# ADR-CAT-002 — A new level-0 module `SET` owns FeatureFlags, presets and the setup wizard
Status      : RESOLVED-IN-DIALOGUE
Stage       : domain-profile        Module: CAT        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-22T14:34:22+00:00
Dialogue-key: 704d65c188d6
traces      : —

## Decision
Amend P-02 option A. KB §7 allows preset reads only in settings, so there must be exactly one owner. I named it `SET` (context `configuration`). It persists through given `cu` and depends on no business module, so the fixed DAG order and delivery tiers are unchanged. I rejected putting it in Catalog (wrong context, and CAT would become everyone's dependency).
traces: KB §4.1, §6.9, §7; G1, G2, G3; block 4 row 15

Source      : governance-shared\analysis\modules\CAT\_state\briefs\domain-profile-round2.response2.md
