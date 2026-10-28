# ADR-CAT-026 — A Variant carries 0..n barcodes, each optionally tied to a unit; scale codes follow a tenant pattern held in `cu`; no new flag
Status      : SUPERSEDED — by ADR-CAT-042 and SRS D-05 (scale label layouts are CAT config entities, not a `cu` tenant pattern)
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 8f1376d165e2
traces      : —

## Decision
traces: OI-10, CAND-CAT-007
Accept, with one addition: wholesale trade scans carton barcodes, so a barcode may identify a specific unit of the Variant (via the item unit conversion). The scale-barcode pattern is a `cu` setting. The GS1 restricted-circulation range (prefixes 20–29) is recalled and not fetched, so it is `NO-RESEARCH`. P0.5 must verify it against the GS1 specification before it is used as a default. Weighed sale exists without `dualQuantity` (KB §4.2, grocery ○), so no flag is added.
Sources: KB §4.2; GS1 General Specifications (`NO-RESEARCH`).

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
