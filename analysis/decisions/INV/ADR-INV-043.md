# ADR-INV-043 — The consumed unit-of-measure key is the setting INV_MEASURE_UOM_KEY, not a deployment property
Status      : ACCEPTED
Stage       : gate analysis (revise 1, G4)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
Revises     : ADR-INV-022
traces      : ENT-INV-014, DBF-INV-208, RULE-INV-041, REQ-INV-125, AC-INV-140, API-INV-045, SRS A6 (Lookups consumed), contract-cat v2

## Context
ADR-INV-022 read the key of the consumed unit-of-measure lookup from the deployment property
`inv.lookup.measure-uom-key` and left the key string PENDING. That is a second settings mechanism (`@Value`) beside
SettingsApi, which checklist C14 forbids for operator-changeable values. While the property was unset, every
measureUomCode was refused, so REQ-INV-125 and AC-INV-140 could not pass. contract-cat v2 does not name the key.
CAT's own SRS (v2, A6) declares it: `CAT_UOM` (open; seeded PIECE, CARTON, BOX, DOZEN, KILOGRAM, GRAM, METRE, LITRE).

## Decision
1. The key is the SettingsApi setting **INV_MEASURE_UOM_KEY**. db-script BLOCK 8e seeds its platform default
   `CAT_UOM`; every tenant inherits it, and no INV endpoint writes it (it is not a tenant choice).
2. `InvSettings` reads it; RULE-INV-041 validates measureUomCode through `MdlLookupApi` as an active value of
   that key. No INV code spells `CAT_UOM`.
3. The deployment property of ADR-INV-022 is withdrawn.
4. **Platform finding** (business-policies-inv PLATFORM FINDINGS row 1), owned by CAT: publish the
   unit-of-measure lookup key in contract-cat (SRS A6 "Lookups consumed"; contract-cat v2 names it nowhere). When
   CAT publishes it, the seed value is confirmed against the contract; if it differs, one seed update follows.

## Consequences
- REQ-INV-125 and AC-INV-140 pass from the first deployment.
- SRS A6 "Settings consumed", D-04, A9 `given-usage` (cu row) and the contract's `given-usage` name the setting.
- No column, endpoint or contract signature changes.

## Status
ACCEPTED — non-breaking.
