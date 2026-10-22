# ADR-CAT-011 — STF owns the host-to-tenant resolver and given `tenant` owns the context
Status      : RESOLVED-IN-DIALOGUE
Stage       : domain-profile        Module: CAT        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-22T14:34:22+00:00
Dialogue-key: fde5f2aff348
traces      : —

## Decision
Accept P-11. The resolver feeds TenantContext and returns the default tenant in `single` mode.
traces: KB §3 D4; G4, G10

Source      : governance-shared\analysis\modules\CAT\_state\briefs\domain-profile-round2.response2.md
