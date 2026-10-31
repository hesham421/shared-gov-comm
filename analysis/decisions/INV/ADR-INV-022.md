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
The service validates measureUomCode as an active value of the consumed unit-of-measure key, through
`MdlLookupApi`; no key string is hardcoded in INV code. The key is read at runtime from the SettingsApi setting
INV_MEASURE_UOM_KEY (ADR-INV-043). (The original decision read it from a deployment property and left the key
PENDING; that property is withdrawn by ADR-INV-043, because it was a second settings mechanism and left
REQ-INV-125 failing until it was set.)

## Consequences
- RULE-INV-041 (and the CHECK CHK_INV_ITEM_STOCK_SETTINGS_MEASURE_UOM_CODE) is enforced from day one.
- The key's platform default and the platform finding to CAT are in ADR-INV-043.

## Status
ACCEPTED — non-breaking. Revised by ADR-INV-043 (gate-analysis revise 1, G4).
