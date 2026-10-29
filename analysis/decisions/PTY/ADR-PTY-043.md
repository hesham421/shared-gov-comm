# ADR-PTY-043 — The Party named by PTY-409-EMAIL-TAKEN is found through the role searches by email

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise (G1, G20) · Module : PTY v1 · Date : 2026-10-29
Supersedes : ADR-PTY-034 §9 (the PENDING interim)

## Context
RULE-PTY-002's message offers an action that opens the Party already holding the email (AC-PTY-013). `ErrorResponse` carries `code`, `messageAr` and `messageEn` only. The frontend's interim (ADR-PTY-034 §9) searched Customers by the entered email, which misses a Party that holds only the Supplier role.

There were two ways to close the gap:
1. Add `conflictingPartyId` and `conflictingPartyCode` to the error body.
2. Give the Supplier search an email filter.

## Decision
Option 2. Option 1 would need fields that the given's envelope does not carry. The envelope is the given's (`LocalizedException → {code, messageAr, messageEn}`, `GlobalExceptionHandler`), and erp-core conventions § Error codes rule 3 forbids a module to build its own response envelope.

1. API-PTY-028 (search Suppliers) gains the query filter `email` (DBF-PTY-003) and the result column `email`. The filter is "contains" without letter case, like every text filter of the search contract and like the existing email filter of API-PTY-026. The SRS SCR-REQ-PTY-002 B2 list gains ENT-PTY-001.email, so its filters and result columns stay equal.
2. RULE-PTY-002 compares email without letter case and surrounding spaces among **active** Parties, and the Party that answers the refusal is active. A search with `email=<entered email>&isActive=true` on its role therefore returns that Party's role. Among the rows it returns, exactly one has an email equal to the entered one once both are trimmed and lower-cased.
3. The frontend action "Open that party" «فتح ذلك الطرف» runs two reads and keeps only the row whose normalised email equals the entered one:
   - It reads API-PTY-026 with `email=<entered email>&isActive=true`. That row, if found, opens `/pty/customers/:customerPk`. This includes the walk-in Customer, which is a Customer like any other.
   - If no such Customer is found and the user holds PERM_PTY_SUPPLIER_VIEW, it reads API-PTY-028 with the same filter. That row, if found, opens `/pty/suppliers/:supplierPk`.
   - If neither is found, which happens when the user may not see the role the Party holds, the action is not offered, and the message still names the Party code.
4. The error body does not change. ADR-PTY-034 §9 is removed and points here.

## Consequences
- api-spec-pty.yaml: API-PTY-028 gains the `email` parameter and `SupplierRow.email`.
- Backend: QR-PTY-020 gains the email filter.
- Frontend: CUSTOMER-SAVE and SUPPLIER-SAVE route the RULE-PTY-002 action through the two reads, and F4 navigation follows.
- Tests: TC-PTY-123 asserts the Customer path and a new frontend TC asserts the Supplier-only path. A new backend TC covers the email filter of API-PTY-028 (AC-PTY-136).

## Traces
RULE-PTY-002 · REQ-PTY-010, REQ-PTY-018 · AC-PTY-013, AC-PTY-136 · API-PTY-026, API-PTY-028 · QR-PTY-020 · SCR-PTY-001, SCR-PTY-002
