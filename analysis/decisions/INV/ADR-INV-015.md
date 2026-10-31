# ADR-INV-015 — Currency code on every monetary INV row (profile check CM-8)
Status      : ACCEPTED
Stage       : P2 (Database)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : ENT-INV-004, ENT-INV-007, ENT-INV-011, ENT-INV-013, REQ-INV-057, REQ-INV-059, REQ-INV-061, REQ-INV-062, REQ-INV-063, REQ-INV-070, REQ-INV-080, REQ-INV-099, SRS A2 (Currency), SRS D-06

Filed from db-script-inv.md §5.2, where the decision is recorded in full; the text is unchanged.

## Context
Profile check CM-8 (MAJOR) requires every monetary row to carry its currency, "the dimension exists from day
one even while the tenant has one". INV holds amounts on Stock move (unitCost, moveValue), Transfer line
(unitCost), Stock adjustment line (unitCost, lineValue) and Stock valuation (averageUnitCost, stockValue).
SRS A2 and D-06 fix every value "in the tenant base currency" and declare multi-currency an advanced tier,
but A3 holds no currency field.

## Decision
`CURRENCY_CODE VARCHAR(3) NOT NULL` with `CHECK (CURRENCY_CODE ~ '^[A-Z]{3}$')` (ISO 4217) on INV_STOCK_MOVE
(DBF-INV-061), INV_TRANSFER_LINE (DBF-INV-110), INV_STOCK_ADJUSTMENT_LINE (DBF-INV-171) and
INV_STOCK_VALUATION (DBF-INV-200). The service writes the tenant base currency read through the given `cu`
SettingsApi (SRS A9 row 1); there is no column default because the base currency is a tenant setting, not a
constant. INV holds no price, so the price-list part of CM-8 does not apply.

## Consequences
- No SRS meaning changes: every amount stays in the base currency, now stated on the row.
- The stock-move event (CON-INV-020) may add the code as an ADDITIVE field in P3.1.
- When multi-currency arrives, valuation per currency is an additive change.
- A later SRS revision lists `currencyCode` among the fields of the four entities.

## Status
ACCEPTED — non-breaking.
