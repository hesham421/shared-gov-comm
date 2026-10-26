# ADR-CAT-013 — Sales & POS, SerialWarranty and Storefront use the profile codes SLS, SWR and SFR; the aliases SAL, SRW and STF are retired
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: ec10031a4681
traces      : —

## Decision
traces: OI-01, OI-04
Accept option A. `profile.vocabulary.module_prefixes` already holds all 14 codes, and the engine's detection table uses SLS, SWR and SFR. Option B would change the profile and break C3.7 until it was done. I amend one point: no ID is ever minted with an alias, and the domain-profile spellings (§4, §7.1–7.3) are corrected at its hand re-run, which OI-02 already requires. The other 11 codes lose the label "PROPOSED" because the registry (§2.2) marks them IN PROFILE.
Sources: registry §2.2; engine §2.1.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
