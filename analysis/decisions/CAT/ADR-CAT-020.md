# ADR-CAT-020 — SFR owns the storefront domain mapping and the host-to-tenant resolver, which feeds the given TenantContext
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 07c434fa0bd9
traces      : —

## Decision
traces: OI-14, CAND-SFR-002
Accept. The `tenant` entry lists no host-based resolution. The resolver reaches the context through `TenantLookupApi` and returns the default tenant in `single` mode. The brief fixes this as D4.
Sources: KB §3 D4; manifest `tenant`; G10.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
