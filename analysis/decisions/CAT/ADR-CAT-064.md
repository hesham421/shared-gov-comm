# ADR-CAT-064 — A scale label layout keeps its scale code length; type and decimals change only how the value is read

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 (gate-analysis round 2, G3) · Module : CAT v2 · Date : 2026-10-28

## Context
API-CAT-033 let an owner change a layout's scaleCodeLength, embeddedValueTypeCode and valueDecimals, and API-CAT-034 deactivated a layout. Every active SCALE barcode was validated against a layout's length when it was created (RULE-CAT-033). After a length change, its labels stop decoding, or decode to the wrong code and value. No statement covered this.

## Decision
1. **The length is fixed.** scaleCodeLength is fixed after creation, as the key already is. `ScaleLabelLayoutUpdateRequest` no longer carries it, and `ScaleLabelLayoutDomain.from` keeps the stored value. A scale re-set to another length is a new layout under a new key.
2. **Type and decimals change only the reading.** A change of embeddedValueTypeCode or valueDecimals changes only how the embedded value of a label scanned afterwards is read: the same digits, a new unit (weight or price) or a new decimal point. Every SCALE barcode keeps resolving, because its code length cannot change (REQ-CAT-093, AC-CAT-152).
3. **Deactivation keeps the barcodes.** Deactivating a layout leaves its SCALE barcodes stored and active. They are not decodable from a scale label while no active layout with the same scaleCodeLength exists, and they decode again when one does (REQ-CAT-094).

## Consequences
- SRS A3 (ENT-CAT-011), REQ-CAT-093, REQ-CAT-094 and RULE-CAT-034 say so. The backend plan's ENT-CAT-011 block, API-CAT-033, API-CAT-034 and the api-spec drop scaleCodeLength from the update.
- On the frontend, the layout drawer shows scaleCodeLength read-only after creation.
- New AC-CAT-152 and TC-CAT-206.

## Traces
REQ-CAT-093 · REQ-CAT-094 · RULE-CAT-033 · RULE-CAT-034 · RULE-CAT-035 · API-CAT-033 · API-CAT-034 · API-CAT-043
