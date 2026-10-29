# ADR-PTY-009 — Seed one walk-in customer (عميل نقدي) per tenant; POS sales without a named customer use it
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: 697f754b2899
traces      : POL-PTY-009

## Decision
Accepted (option B), recorded as POL-PTY-009. This rests on KB §6.8 and §6.10 only. The Saudi simplified-invoice claim was not researched (NO-RESEARCH) and is not relied on.
Guards:
- The walk-in customer never receives a credit limit or credit terms. A credit sale needs a named customer.
- The walk-in customer cannot be deleted.
- A storefront guest is not the walk-in customer.
- Seeding runs through the given TenantProvisioningContributor.
traces: POL-PTY-009, CAND-PTY-001

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md
