# ADR-PTY-001 — Profile codes SLS, SWR and SFR are binding; SAL, SRW and STF are retired aliases
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: 6d27a8d39737
traces      : —

## Decision
Accepted. `profile.vocabulary.module_prefixes` is the durable record, and the project registry forbids minting IDs with the aliases (§2.2, E2). The engine's detection table also uses SLS, SWR and SFR. Option B would need a profile change and a re-mint for no gain.
Amendment: the bounded-context ids (`sales`, `after-sales`, `storefront`) stay as in the domain-profile §7.2, because a context id does not change with its code. The domain-profile still prints the aliases in §4, §7.1 and §7.3. Its next hand re-run replaces them. Until then every artifact uses the profile codes.
traces: OI-01, OI-04

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md
