# ADR-CAT-073 — An inactive lookup value renders as its raw code until the core can label it

Status : ACCEPTED (non-breaking) · Stage : P3.1 / P3.2 / P4 (gate-analysis round 7, G1) · Module : CAT v2 · Date : 2026-10-28 · Raises : CORE-GAP-lookup-inactive-value-label.md

## Context
REQ-CAT-076 keeps a lookup code on the records that hold it after the value is deactivated in mdl. UXD-CAT-001 then asked the screens to show that value's label, and TC-CAT-139 expected the label of the deactivated brand OLDBRAND on its Items.

No source for that label exists:
- API-CAT-052 serves a CAT key through `OwnedLookups.read(…, MdlLookupApi::readActiveValuesByKey)`, which returns the **active** values only. Its `LookupOption` schema has no activity flag.
- erp-core 1.6.0 publishes only `MdlLookupApi.readActiveValuesByKey`. No call reads one value by key and code whatever its state.
- The frontend plan's F1 `LookupOption` declared an `isActive` field that the API document does not carry.

TC-CAT-139 passed only because OLDBRAND's code and label were the same text.

## Decision
1. **Interim rendering.** A lookup-backed field, column or badge whose code is not among the active values API-CAT-052 returns renders the **raw code**, unchanged, in both languages. Active values render their ar / en labels.
2. **API-CAT-052 is unchanged.** It returns the active values only. `LookupOption` stays `{code, labelAr, labelEn, sortOrder}`.
3. **Frontend.** F1 `LookupOption` drops `isActive`. Each `*-LOOKUP` hook labels a code from the active options, else shows the code itself.
4. **UXD-CAT-001** is narrowed: the active values of each key as options, their labels, and the raw code of a value that is no longer active.
5. **Tests.** TC-CAT-139 and TC-CAT-177 use a host CAT_BRAND value whose label differs from its code, deactivate it, and assert that the record shows the code, not the label.
6. **Core gap.** Raised as `CORE-GAP-lookup-inactive-value-label.md`: a read of lookup values by key and codes, active or not.
7. **When the core answers**, a later delta adds an optional `codes` query parameter to API-CAT-052 (returning those values whatever their state) and `isActiveFl` to `LookupOption`. The screens then label every code they render, and inactive values show their label. The change is additive to the API document.

## Consequences
- Backend plan: PHASE CORE "Lookups" and "Core gaps", API-CAT-052 Validations.
- `api-spec-cat.yaml`: the API-CAT-052 description states the interim.
- Frontend plan: F1 `LookupOption`, the F2 lookup rule. ui-ux-spec: conventions and UXD-CAT-001.
- Tests: TC-CAT-139, TC-CAT-177.
- business-policies: platform finding 10.

## Traces
REQ-CAT-073 · REQ-CAT-074 · REQ-CAT-076 · AC-CAT-078 · UXD-CAT-001 · API-CAT-052 · TC-CAT-139 · TC-CAT-177 · ADR-CAT-065 · CORE-GAP-lookup-inactive-value-label
