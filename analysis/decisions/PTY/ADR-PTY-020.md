# ADR-PTY-020 — No currency column on PTY amounts (profile check CM-8)

Status  : ACCEPTED (non-breaking — a MINOR deviation from CM-8 justified by the approved scope exception) · Stage : P2 · Module : PTY v1 · Date : 2026-10-29
Filed   : verbatim from the db-script §5.2 (analysis/modules/PTY/P2/db-script-pty.md) by the P3.1 repair, ADR-PTY-030

## Context
Profile check CM-8 asks every monetary row to carry its currency. PTY holds amounts on ENT-PTY-002 (creditLimit) and ENT-PTY-005 (saleAmount, creditLimitAtRequest, openExposure). The approved SRS states a business-policies SCOPE EXCEPTION — "multi-currency party balances" — and types each amount as "tenant base currency"; no currency field exists in SRS A3. NO-COLUMN-INVENTION forbids a column with no SRS origin.

## Decision
No currency column. The amounts are thresholds and approval amounts expressed in the tenant base currency (held by the given `cu`), as the SRS and the contract's §0 rules ("Currency: every amount is in the tenant's base currency") promise. PTY carries no stock-bearing row and no price, so the warehouse and price-list parts of CM-8 do not apply.

## Consequences
Consumers convert a foreign-currency document to base currency before a credit check (CON-PTY-015) and a request (CON-PTY-016). Lifting the scope exception in a later version adds a currency column per amount as an ADDITIVE migration with its own SRS field.

## Traces
ENT-PTY-002.creditLimit, ENT-PTY-005.saleAmount; DBF-PTY-015, 051, 052, 053; REQ-PTY-064, REQ-PTY-079, REQ-PTY-091.
