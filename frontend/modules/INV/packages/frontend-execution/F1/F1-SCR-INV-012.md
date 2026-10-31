<!-- source: PHASE:F1 / SUB:F1-SCR-INV-012 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-008, API-INV-050, API-INV-051, REQ-INV-059, REQ-INV-061, REQ-INV-063, SCR-INV-012, UXD-INV-011 -->
<!-- SUB:F1-SCR-INV-012:START traces=SCR-INV-012,UXD-INV-011,REQ-INV-059,REQ-INV-061,REQ-INV-063,API-INV-008,API-INV-050,API-INV-051 -->
### F1 · SCR-INV-012 — Stock valuation
- Schemas: `StockValuation`, `StockValuationEnvelope`, `ValuationPageEnvelope` (with `grandTotalValue`).
- `ValuationFilter {variantPk?, term?, itemPk?, nonZero, page, size}`.
- `ValuationDetailNav = variantPk`, which comes from the `variant` search param.
<!-- SUB:F1-SCR-INV-012:END -->
