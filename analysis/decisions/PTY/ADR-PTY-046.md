# ADR-PTY-046 — Customer VIEW includes reading the Customer's credit values

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise 2 (G6) · Module : PTY v1 · Date : 2026-10-29

## Context
While `b2bCredit` is on, API-PTY-002 (read Customer) returns creditLimit, paymentTermDays and currencyCode to any holder of PERM_PTY_CUSTOMER_VIEW. SCR-REQ-PTY-003 B4 gates the Customer credit screen with PERM_PTY_CUSTOMER_CREDIT_VIEW. A custom role holding Customer VIEW but not Customer credit VIEW therefore reads the two credit values through the Customer read. No requirement settles whether it should.

## Decision
1. **Customer VIEW includes reading the two credit values and their currency** on the Customer read (API-PTY-002 and the in-process `PtyPartyApi.readCustomer`). Consumers rely on this: CON-PTY-009 promises the values to Sales & POS, Settlements and the Storefront, whose callers hold Customer VIEW (ADR-PTY-039), and SCR-PTY-001 shows them read-only (ADR-PTY-033).
2. **PERM_PTY_CUSTOMER_CREDIT_VIEW gates the credit work**, not the two values: the Customer credit screen, the credit standing with open exposure and available credit (API-PTY-016), the credit list (API-PTY-030) and the credit history (API-PTY-031).
3. **Editing stays gated** by PERM_PTY_CUSTOMER_CREDIT_UPDATE (API-PTY-017, 018).
4. REQ-PTY-002 names the three values in its statement (present only while the flag is on, REQ-PTY-071), and AC-PTY-138 fixes the behaviour for a role holding Customer VIEW only.

This is the best-practice choice: the values are part of the Customer the contract promises, the exposure (which depends on other modules' data) stays behind the credit permission, and no write is widened.

## Consequences
- No permission name and no endpoint change.
- A backend TC asserts that a Customer-VIEW-only role reads the values on API-PTY-002 and is refused on API-PTY-016 (TC-PTY-196).

## Traces
REQ-PTY-002, REQ-PTY-071, REQ-PTY-085 · AC-PTY-138 · API-PTY-002, API-PTY-016, API-PTY-030, API-PTY-031 · CON-PTY-009 · SCR-REQ-PTY-003 B4 · ADR-PTY-033, ADR-PTY-039
