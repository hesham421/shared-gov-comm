# ADR-INV-022 — The consumed unit-of-measure lookup key is configured, not hardcoded
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : ENT-INV-014, DBF-INV-208, RULE-INV-041, REQ-INV-125, API-INV-045

## Context
ENT-INV-014.measureUomCode stores a code of the unit-of-measure lookup owned by the Item owner module
(SRS A6 "Lookups consumed"). The SRS names the key only as "CAT unit-of-measure key"; it does not spell the
key string. The binding rule forbids inventing it, and lookup values must be loaded at runtime from mdl.

## Decision
The service validates measureUomCode as an active value of the key named by the configuration property
`inv.lookup.measure-uom-key`, through `MdlLookupApi`. The deployment sets the property from the key owner's
published registry. The key string is PENDING in this plan; no value is hardcoded in INV. While the
property is unset, a non-empty measureUomCode is refused with INV-422-MEASURE-UNIT-REQUIRED.

## Consequences
- RULE-INV-041 (and the CHECK CHK_INV_ITEM_STOCK_SETTINGS_MEASURE_UOM_CODE) is enforced from day one; the
  value check becomes complete once the property is set.
- If the key owner later publishes the key in its contract, this ADR is superseded by a plan update that
  names it.

## Status
ACCEPTED — non-breaking.
