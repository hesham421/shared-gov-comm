# ADR-INV-025 — Native aggregate searches; Variant term and Item filters through in-process reads
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : QR-INV-015, QR-INV-016, QR-INV-038, REQ-INV-038, REQ-INV-039, REQ-INV-109, REQ-INV-116, API-INV-016, API-INV-017, API-INV-040, API-INV-050

## Context
Reserved and available quantities are derived (SRS A3): available = on-hand outside expired lots minus
ACTIVE reservations. The lot screen needs on-hand per lot and warehouse. Both need joins of INV_STOCK_BALANCE
with INV_LOT and INV_RESERVATION, and a join needs an ADR. The balance and valuation screens filter by
"Variant (code, name or barcode)" and by Item; the Variant and Item records belong to another module, which
publishes a barcode lookup, Item reads and an Item search, but no Variant search by code or name.

## Decision
- QR-INV-015, 016 and 038 are native aggregate queries with LEFT JOINs to INV_LOT and an aggregated
  sub-select of ACTIVE INV_RESERVATION rows; all are read-only and paged where they list.
- The `term` filter is resolved as a barcode through XM-INV-013; `itemPk` and Item terms resolve to Variant /
  Item identifiers through XM-INV-015; Variant display names are read per page through XM-INV-014. Picking a
  Variant by code or name is done in the frontend with the owner module's picker, which yields variantPk.

## Consequences
- No view or routine is added to the database (db-script BLOCK 9 / 10 stay empty).
- Search by Variant code text on the balance screen works through the picker, not through free text.

## Status
ACCEPTED — non-breaking.
