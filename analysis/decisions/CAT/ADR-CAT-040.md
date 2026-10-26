# ADR-CAT-040 — Tax class held as a CAT-owned lookup

Status : ACCEPTED (non-breaking) · Stage : P1 · Module : CAT v2 · Date : 2026-10-26

## Context
US-CAT-021 asks to record each Item's tax class. The domain-profile P-08 (pending) recommends a tax-category reference owned by TAX (registry CAND-TAX-001, OPEN OI-11). However, CAT is first in the fixed module order and depends on no business module, and dependencies flow downward only [KB:raw-idea.md §5]. A CAT reference to a TAX entity would invert that order.

## Decision
The tax class is the lookup CAT_TAX_CLASS (in mdl, owned by CAT):
- seeded with STANDARD, ZERO_RATED and EXEMPT
- open, so the tenant can add values
- defaulted to STANDARD on new Items

The TAX module later maps each tax class to its rates and authority codes. CAT holds no rate.

## Consequences
- CAT stays free of business dependencies.
- TAX consumes CAT_TAX_CLASS read-only.
- If P-08 later decides otherwise, the TAX SRS takes ownership of the key, and CAT's taxClassCode is re-pointed in a delta.

## Traces
US-CAT-021 · ENT-CAT-001 · REQ-CAT-004, REQ-CAT-024, REQ-CAT-119 · RULE-CAT-006
