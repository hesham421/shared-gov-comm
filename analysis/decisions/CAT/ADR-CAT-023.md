# ADR-CAT-023 — Every Item has at least one Variant, and stock, price, barcode and e-invoice item code key on the Variant
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: d690457d3adb
traces      : —

## Decision
traces: OI-07, CAND-CAT-001, CAND-CAT-002
Accept A. While `variants` is off, one default Variant exists. Turning the flag on needs no migration, which B would break (KB §6.2, §6.8). I amend the shared-by lists: Variant is also read by TAX (e-invoice item code) and SWR (serial attaches to a Variant). Precedent from Shopify and Odoo is `NO-RESEARCH` (recalled, not fetched). The decision rests on KB §6.2 and §6.8.
Sources: KB §4.1, §6.2, §6.8.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
