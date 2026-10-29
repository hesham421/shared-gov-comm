# ADR-INV-001 — Profile codes SLS, SWR and SFR are binding; the aliases SAL, SRW and STF are retired; one bounded context per module
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:34:05+00:00
Dialogue-key: da0b675238b9
traces      : —

## Decision
Accept option A. `profile.vocabulary.module_prefixes` is the durable record, the engine's detection table uses SLS, SWR and SFR, and registry rule E2 mints IDs only from IN PROFILE codes. The domain-profile §4 and §7 text still uses the aliases and calls the codes "PROPOSED". Correcting that is a hand edit of the profile, outside this stage's three files. These artifacts use profile codes only. Contexts follow DP §7.2.
traces: OI-01, OI-04

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0-round2.response2.md
