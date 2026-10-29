# ADR-PTY-037 — A currency code on every credit amount, hidden while the tenant has one currency

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise (G11, CM-8) · Module : PTY v1 · Date : 2026-10-29
Supersedes : ADR-PTY-020 · Depends on : CORE-GAP-tenant-base-currency (OPEN)

## Context
ADR-PTY-020 left the credit amounts without a currency column and cited the scope exception "multi-currency party balances". The same approved scope exception says: "the currency dimension exists in the model, hidden while its count is 1". Upstream wins (GOVERNANCE-CORE §1), so the dimension has to exist in the model now (profile check CM-8). The currency stays hidden in the UI.

## Decision
1. `CURRENCY_CODE VARCHAR(3) NOT NULL` (an ISO 4217 code) is added in two places:
   - on PTY_CUSTOMER (DBF-PTY-078, ENT-PTY-002.currencyCode) — the currency of creditLimit and of the Customer's open exposure;
   - on PTY_CREDIT_OVERRIDE_REQUEST (DBF-PTY-079, ENT-PTY-005.currencyCode) — the currency of saleAmount, creditLimitAtRequest and openExposure.
   It is a system field and is never accepted from a caller in v1.
2. **Value.** The column holds the tenant base currency at the time the row is created. A request copies the currency of its Customer. erp-core 1.6.0 publishes no read of a tenant's base currency: the ledger currencies in `GL_TENANT_CURRENCY` have no published interface, and neither `cu` nor `tenant` names one. Until CORE-GAP-tenant-base-currency is answered, PTY reads it through `SettingsApi.getOrDefault("PTY_BASE_CURRENCY_CODE", String.class, "XXX")`. `PTY_BASE_CURRENCY_CODE` is a PTY setting (checklist C14), set per tenant at setup through `/api/v1/common/configurations`. While the tenant has not set it, rows are written with `XXX`, the ISO 4217 code for "no currency involved". v1 holds one currency per tenant (the scope exception), so no computation in v1 reads the column to convert.
3. **Credit check and requests.** Amounts are compared in the Customer's currency. The credit check response, the credit standing, the credit list and the request responses carry `currencyCode`. A consumer converts its sale amount into that currency before a check, a request or a use, as before.
4. **UI.** The frontend does not show `currencyCode` while the tenant has one currency. Amounts are formatted in that currency.

## Consequences
- The DBF matrix, the API schemas `Customer`, `CustomerCredit`, `CustomerCreditRow`, `CreditCheckResult`, `CreditOverrideRequest` and `CreditOverrideRow` all gain `currencyCode`, and so do contract items CON-PTY-002, 005, 009, 015, 017 and 022. All of these are additive.
- When the core answers the gap, a delta reads the given source instead of the setting, and one migration re-stamps `XXX` rows with the tenant's base currency. When multi-currency ships (wave 4), a request may then carry a currency other than its Customer's, through a new rule.

## Traces
ENT-PTY-002.currencyCode, ENT-PTY-005.currencyCode · DBF-PTY-078, DBF-PTY-079 · REQ-PTY-064, REQ-PTY-079, REQ-PTY-085, REQ-PTY-091 · API-PTY-002, 016, 017, 019, 020, 030, 032, 035
