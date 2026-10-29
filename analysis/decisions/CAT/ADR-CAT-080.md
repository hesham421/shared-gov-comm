# ADR-CAT-080 — A Bundle component quantity is in the base unit of the component Item

Status : ACCEPTED (non-breaking) · Stage : P1 / P1.5 / P2 / P3.1 / P3.2 / P4 (gate-analysis round 11, G2) · Module : CAT v2 · Date : 2026-10-29

## Context
- ENT-CAT-007.quantity (DBF-CAT-085, NUMERIC(18,3)) states no unit. INV and SLS split a sold Bundle into component movements through CON-CAT-006.
- A component Item may carry Item units with a conversion factor (a carton of 12). Two implementers can read «quantity 1» as one piece or as one carton.

Options:
- (a) the quantity is always in the base unit (baseUomCode) of the component Variant's Item, and every read returns that unit;
- (b) add a `uomCode` per line, chosen among the component Item's base unit and active Item units;
- (c) leave the unit to each consumer.

## Decision
Option (a).
- Option (b) adds a column, a rule and a conversion in every consumer for a need no story states; INV keeps stock in the base unit (D-14).
- Option (c) is the ambiguity the finding records.

1. **Meaning.** `quantity` is the number of base units of the component Variant's Item in one Bundle. The base unit is fixed after the Item's first save (RULE-CAT-007), so a stored quantity never changes meaning.
2. **Derived property.** `componentBaseUomCode` (String) — the component Item's baseUomCode, derived at read (QR-CAT-039 joins CAT_ITEM), returned on `BundleComponentView` with the existing `componentVariantCode`. No column is added; the db-script only comments DBF-CAT-085.
3. **Screen.** The Bundle components pane shows the unit beside each quantity, labelled by CAT_UOM.
4. Amends ADR-CAT-048 §5 (derived properties) by one row; non-breaking: a field is added to a response, no request changes.

## Consequences
- SRS: ENT-CAT-007.quantity, REQ-CAT-056, REQ-CAT-058, AC-CAT-060; new AC-CAT-174.
- Contract: CON-CAT-006 statement; CON-CAT-007 component line fields.
- db-script: comment of CAT_BUNDLE_COMP.QUANTITY.
- Backend plan: ENT-CAT-007 block, derived-property manifest row, DTO BundleComponentView, QR-CAT-039, API-CAT-001 / 002 / 017 notes. api-spec: `BundleComponentView.componentBaseUomCode`.
- Frontend plan: F1 bound schema and `bundleLines`, Bundle components pane; ui-ux-spec pane table.
- Tests: TC-CAT-235; TC-CAT-095 and frontend TC-CAT-179 extended.

## Traces
ENT-CAT-007 · DBF-CAT-085 · REQ-CAT-056 · REQ-CAT-058 · AC-CAT-060 · AC-CAT-174 · CON-CAT-006 · CON-CAT-007 · RULE-CAT-007 · RULE-CAT-018 · API-CAT-002 · API-CAT-017 · QR-CAT-039 · TC-CAT-235
