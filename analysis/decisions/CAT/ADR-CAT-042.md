# ADR-CAT-042 — Scale label layouts and unit e-invoice codes as CAT config entities

Status : ACCEPTED (non-breaking) · Stage : P1 · Module : CAT v2 · Date : 2026-10-26

## Context
- US-CAT-017 (module-registry OI-10) calls the scale-barcode layout "a tenant setting".
- US-CAT-021 asks that units carry their e-invoice unit. Units of measure are mdl lookup values, and no given interface in the manifest is known to hold an extra attribute per lookup value.
- The rules that check scale codes and decode labels need a `Data source` that resolves to declared ENT fields (C5.12). A key-value setting in given `cu` would leave those rules without one.

## Decision
- Scale label layouts are the CAT config entity ENT-CAT-011, one per tenant prefix; this still counts as a tenant setting.
- The e-invoice unit of each unit of measure is the CAT config entity ENT-CAT-012, with lookup CAT_EINVOICE_UNIT.
- Both are maintained on the Catalogue settings screen (SCR-REQ-CAT-006).

## Consequences
- RULE-CAT-032 … RULE-CAT-035 and RULE-CAT-043 are enforceable, with declared data sources.
- Nothing provided by erp-core is rebuilt; the given `cu` keeps the capability flags only.

## Traces
US-CAT-017, US-CAT-021 · ENT-CAT-011, ENT-CAT-012 · REQ-CAT-092 … REQ-CAT-100, REQ-CAT-120 … REQ-CAT-124
