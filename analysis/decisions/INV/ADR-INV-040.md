# ADR-INV-040 — INV v1's XM edges to CAT are planned against delivered CAT v2 (resolution events acknowledged)
Status      : ACCEPTED
Stage       : gate analysis (feedback)        Module: INV        Version: v1
Lane        : operator · under Hesham's standing authorisation
Decided     : 2026-10-31
traces      : INV>CAT, XM-INV-001 … XM-INV-015, contract-cat (CAT v2) CON-CAT-001, CON-CAT-002, CON-CAT-005, CON-CAT-006, CON-CAT-007, CON-CAT-008, CON-CAT-010, CON-CAT-016

## Context
`gov.py graph` raised 16 resolution events into INV's manifest (`status.xm_events`), trigger `delivered`,
target CAT v2. These are the module edge INV>CAT and the 15 edges XM-INV-001 … XM-INV-015. An OPEN event
blocks INV's analysis gate until it is answered. The question each one asks: "your edge targets CAT, and
CAT is now delivered as v2. Does your plan hold against what was delivered?"

## Decision
Acknowledge all 16 events. No plan change is needed.
- The published CAT contract (`platform/contracts/contract-cat.md`) is CAT v2 (`Version : v2`, CON 18).
  It was published before INV's P1.5 to P4 ran, so every INV stage read the v2 contract.
- INV's backend execution plan (P3.1) cites only CAT v2 contract items. Every item INV relies on, confirmed one by
  one against `platform/contracts/contract-cat.md` (Version v2) at gate-analysis revise 1 (G19):

  | Item | What INV uses | Where | Confirmed in v2 |
  |---|---|---|---|
  | CON-CAT-001 Item | `itemPk` (BIGINT) as the target of ITEM_FK; itemKindCode, baseUomCode | XM-INV-011, XM-INV-015 | ✓ entity promise, identifier BIGINT |
  | CON-CAT-002 Variant | `variantPk` (BIGINT) as the target of the ten VARIANT_FK columns | XM-INV-001 … 010, XM-INV-014 | ✓ entity promise, identifier BIGINT |
  | CON-CAT-005 Barcode | one barcode identifies one Variant | XM-INV-013 | ✓ entity promise |
  | CON-CAT-006 Bundle component | component Variant, quantity in the component's base unit | XM-INV-012 | ✓ entity promise |
  | CON-CAT-007 getItem | itemKindCode, isActiveFl, code, names, Variants, Bundle component lines | XM-INV-012, 014, 015 | ✓ signature `getItem(itemPk) → Item {…}`, error not found |
  | CON-CAT-008 getVariant | itemFk, code, names, isActiveFl | XM-INV-012, 014 | ✓ signature `getVariant(variantPk) → Variant {…}`, error not found |
  | CON-CAT-010 lookupBarcode | barcode → variantPk; not found / scale label not readable | XM-INV-013 | ✓ signature `lookupBarcode(value) → {variantPk, itemPk, uomCode, …}` |
  | CON-CAT-016 searchCatalogue | Item term search, at most 20 Items | XM-INV-015 | ✓ signature `searchCatalogue(term) → ≤ 20 Items`; term under 2 characters → error, answered as an empty list by the adapter |

  (The first issue of this ADR named only CON-CAT-001, 002, 005 and 006; the adapters also rely on 007, 008, 010
  and 016, which were in v2 from the start, so the acknowledgement holds unchanged.)
- INV's analysis is clean at the gate (`analyze gate:analysis` reports 0 critical · 0 major) against that
  contract.

## Consequences
- The 16 events are recorded as ANSWERED with this ADR (`gov.py answer-feedback`). They are not waived.
- If CAT publishes a v3 that changes a cited contract item, `gov.py graph` raises new events against v3,
  and those events are owed again.
