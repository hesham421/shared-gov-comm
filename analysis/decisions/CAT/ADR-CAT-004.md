# ADR-CAT-004 — Every Item has at least one Variant, and stock, price, barcode and tax-classification attach to the Variant
Status      : RESOLVED-IN-DIALOGUE
Stage       : domain-profile        Module: CAT        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-22T14:34:22+00:00
Dialogue-key: c44793bbd2a8
traces      : —

## Decision
Accept P-04, but narrowed the justification. No industry source could be fetched, so the decision rests on KB §6.2 and §6.8 (turning `variants` on needs no migration), not on a Shopify or Odoo comparison. It is recorded as rule G16.
traces: KB §4.1, §6.2, §6.8; G3, G8, G16

Source      : governance-shared\analysis\modules\CAT\_state\briefs\domain-profile-round2.response2.md
