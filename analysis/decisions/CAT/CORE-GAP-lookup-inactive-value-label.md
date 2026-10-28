# CORE-GAP-lookup-inactive-value-label — Can a module read a lookup value by key and code, active or not?

Status : OPEN (asked of erp-core 1.6.0) · Raised by : CAT v2 (gate-analysis round 7, G1) · Date : 2026-10-28
Decision that depends on it : ADR-CAT-073

## The gap
`MdlLookupApi` publishes `readActiveValuesByKey(key)` only. A module that keeps a lookup code on its records after the value is deactivated (CAT REQ-CAT-076: a brand or unit retired later stays on existing Items) has no way to read that value's ar / en label. Every screen that renders the record can therefore show only the raw code.

## What CAT does meanwhile (ADR-CAT-073)
API-CAT-052 serves the active values of a CAT key. A code that is not among them renders as the raw code on every CAT screen.

## Question to the core
Can `MdlLookupApi` offer `readValuesByKeyAndCodes(key, codes)` — the values of one key with the given codes, each with `labelAr`, `labelEn`, `sortOrder` and `isActive`, whatever their state — within the caller's tenant?

## Impact when answered
CAT adds an optional `codes` query parameter to API-CAT-052 and `isActiveFl` to `LookupOption`. The screens label every code they render. No schema or existing operation changes.

## Traces
REQ-CAT-076 · UXD-CAT-001 · API-CAT-052 · ADR-CAT-073
