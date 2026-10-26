# ADR-CAT-035 — Standard values of the lookup `einvoice-item-code-type` are GS1 and EGS
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0.5        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T14:56:26+00:00
Dialogue-key: c1ae5ebf6c78
traces      : POL-CAT-001, US-CAT-021

## Decision
Accepted. The registry delegated these values to P0.5 from the ETA source. The Egypt ETA e-invoice SDK `itemType` takes `GS1` or `EGS`. ZATCA's UBL carries a standard item identifier (GTIN) with no separate type list, and the Variant barcode already covers that identifier. The values are mdl lookup values, so they receive no ID.
traces: POL-CAT-001, US-CAT-021

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0.5-round2.response2.md
