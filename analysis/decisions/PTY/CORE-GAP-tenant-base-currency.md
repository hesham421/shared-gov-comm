# CORE-GAP-tenant-base-currency — Which given interface answers a tenant's base currency?

Status : OPEN (asked of erp-core 1.6.0) · Raised by : PTY v1 (gate-analysis revise, G11) · Date : 2026-10-29
Decision that depends on it : ADR-PTY-037

## The gap
Every credit amount in PTY carries the currency it is expressed in (CURRENCY_CODE, ADR-PTY-037), and that is the tenant's base currency while the tenant has one currency. erp-core 1.6.0 has no published read for it:
- `gl` keeps the currencies a tenant's ledger accepts (`GL_TENANT_CURRENCY`, minor units from ISO 4217). Its published interfaces (`GlAccountLookupApi`, `GlPostingApi`, `GlEntryQueryApi`) expose neither that list nor a base currency.
- `cu` resolves settings by key, but the core reserves no key for a base currency.
- `tenant` (`TenantLookupApi`, branding, public app-config) carries no currency.

## What PTY does meanwhile (ADR-PTY-037)
PTY reads its own setting `PTY_BASE_CURRENCY_CODE` through `SettingsApi` (checklist C14). The tenant sets it at setup. While it is unset, PTY writes `XXX` (ISO 4217 "no currency involved"). No v1 computation converts between currencies, because the scope exception keeps one currency per tenant.

## Question to the core
Which of these, or another, is the core's pattern?
1. A published read on the ledger currencies, for example `GlCurrencyLookupApi.baseCurrency()`.
2. A core-reserved settings key, for example `CORE_BASE_CURRENCY`, that every module reads through `SettingsApi`.
3. A base currency on the tenant record, read through `TenantLookupApi`.

## Impact when answered
PTY replaces one read in `PtyCurrency.baseCurrency()`. One migration re-stamps `XXX` rows with the tenant's base currency. No endpoint and no contract item changes.

## Traces
ADR-PTY-037 · DBF-PTY-078, DBF-PTY-079 · CON-PTY-002, CON-PTY-005, CON-PTY-015
