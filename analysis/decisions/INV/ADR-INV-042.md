# ADR-INV-042 — REST services check INV authority; InventoryApi delegates to unchecked internal engines
Status      : ACCEPTED
Stage       : gate analysis (revise 1, G3)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : ADR-INV-024, ADR-PTY-039, ADR-CAT-061, CORE-GAP-in-process-caller-authority, CON-INV-006 … CON-INV-019, REQ-INV-040, REQ-INV-043, REQ-INV-051, REQ-INV-052, REQ-INV-136, REQ-INV-137, REQ-INV-138

## Context
The plan put `@PreAuthorize` on the controller methods only, and said `InventoryApi` "reuses the services of the
REST endpoints" and "checks no INV permission". Both cannot hold once checklist C13 is applied: if the services
carry `@PreAuthorize`, a POS sale that SLS posts through `issueStock` or `readAvailability` is refused, because a
Seller/Cashier holds only PERM_INV_STOCK_BALANCES_VIEW; if they do not, C13 is broken. PTY settled the same
question the other way (ADR-PTY-039: in-process callers must hold the PTY authority). INV had not settled it.

## Decision
1. **REST-facing services** (package `com.commerce.inv.service`) carry
   `@PreAuthorize("hasAuthority(T(com.commerce.inv.permission.InvPermissions).PERM_…)")` on every public method,
   with INV's own constants only and the permission of the endpoint they serve. The controller carries the same
   annotation, so a refusal comes before the body is read.
2. **Internal engines** (package `com.commerce.inv.engine`): `StockPostingEngine`, `AvailabilityEngine`,
   `ReservationEngine`, `InventoryReadEngine`. They hold the posting, availability, reservation and read logic,
   carry no permission check and have no REST path. A REST service calls an engine after its own check.
3. **`InventoryApi`** (package `com.commerce.inv.crossmodule`) delegates to the engines only; it never calls a
   REST-facing service. All fourteen methods bypass the INV permission check:

   | Method | CON | Engine | Why the caller holds no INV authority |
   |---|---|---|---|
   | readWarehouse, listActiveWarehouses, readBranch, listActiveBranches | 006 … 009 | InventoryReadEngine | consuming modules show warehouse and branch selectors to their own users |
   | readAvailability | 010 | AvailabilityEngine | SLS / SFR show availability to a cashier or a storefront shopper |
   | reserve, releaseReservation, reservationsOfDocument | 011 … 013 | ReservationEngine | an order or a checkout reserves for a user with no INV write permission |
   | receiveStock, issueStock | 014, 015 | StockPostingEngine (+ ReservationEngine) | a POS cashier, a purchase clerk or a system job posts stock; none holds an INV write permission by design (SRS B4) |
   | readValuation, readLot, readItemStockSettings, canTurnOff | 016 … 019 | InventoryReadEngine | SLS margin, PUR receipt checks, the flag owner's turn-off check |

   The calling module authorises its own caller against its own permissions before it calls INV (SRS access
   summary — the rule CAT's contract states, ADR-CAT-061). Tenancy applies to every call.
4. **Boundary.** An ArchUnit rule checks that `com.commerce.inv.engine` is used only by `service` and
   `crossmodule`, never by `controller`, and that other modules import only `com.commerce.inv.crossmodule`.

## Difference from ADR-PTY-039
PTY requires the in-process caller to hold the PTY authority, because PTY's published operations are reads and
customer resolution made by users (a cashier) who hold PTY permissions in their preset. INV's published operations
are stock postings and reservations made for users who, by the SRS role presets, hold no INV write permission:
requiring the authority would force granting INV write permissions to the Seller/Cashier preset and would open the
INV back-office screens to it. Both modules depend on the same open core question.

## CORE gap
The core publishes no mechanism for a caller-authorised in-process call (CORE-GAP-in-process-caller-authority,
raised by PTY, OPEN; CAT's CORE-GAP-in-process-authorization is the same family). When the core answers, INV adopts
its mechanism on the engines' entry points without changing any `InventoryApi` signature.

## Consequences
- Checklist C13 holds for every REST-facing service method.
- `InventoryApi` callers are not refused for lacking an INV permission.
- No permission name, endpoint or contract signature changes.

## Status
ACCEPTED — non-breaking.
