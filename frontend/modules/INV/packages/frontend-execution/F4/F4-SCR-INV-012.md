<!-- source: PHASE:F4 / SUB:F4-SCR-INV-012 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-065, AC-INV-069, API-INV-008, API-INV-050, API-INV-051, REQ-INV-059, REQ-INV-061, REQ-INV-063, SCR-INV-012, UXD-INV-011 -->
<!-- SUB:F4-SCR-INV-012:START traces=SCR-INV-012,UXD-INV-011,REQ-INV-059,REQ-INV-061,REQ-INV-063,AC-INV-065,AC-INV-069,API-INV-008,API-INV-050,API-INV-051 -->
### VALUATION-SCREEN — SCR-INV-012
- **Guard**: `PERM_INV_STOCK_VALUATION_VIEW` (Owner).
- **Facade**: VALUATION-FACADE.
- **Cross-module**: Variant and Item filters → UXD-INV-011.
- **Composition**: container page. `ValuationDetailDrawer` is a sibling opened by `?variant`; it is read-only (→ none). Submits: none.
- **Saves**: none (read-only inquiry).
- **Components**:
  - `ValuationSearchForm`.
  - `ValuationResultTable`, with the grand total row from `grandTotalValue`.
  - `ValuationDetailDrawer`, with the link "Stock moves" to `/inventory/stock-moves?variantPk=<id>`.
<!-- SUB:F4-SCR-INV-012:END -->
