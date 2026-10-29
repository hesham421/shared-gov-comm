# CORE-GAP-in-process-caller-authority — How does a caller with no staff permission reach a module's service?

Status : OPEN (asked of erp-core 1.6.0) · Raised by : PTY v1 (gate-analysis revise, G5) · Date : 2026-10-29
Decision that depends on it : ADR-PTY-039 · Related : decisions/CAT/CORE-GAP-in-process-authorization.md (CAT v2, same family)

## The gap
Checklist C13 puts `@PreAuthorize("hasAuthority(T(…PtyPermissions).PERM_…)")` on every service method of PTY, and ADR-PTY-039 requires in-process callers to hold that authority too. Three kinds of caller of PTY's published surface (`com.erp.pty.crossmodule.PtyPartyApi`) hold no PTY staff permission:
1. **The anonymous storefront shopper.** At guest checkout SFR calls `resolveGuestCustomer` (CON-PTY-014, REQ-PTY-050). The shopper has no account yet, so it has no realm and no authority.
2. **A CUSTOMER-realm shopper.** A token carries `ROLE_CUSTOMER` plus the permissions of the account's CUSTOMER-realm roles (erp-core `sec`). A PTY staff permission granted to every customer would also open the PTY back-office screens to them.
3. **System jobs of consuming modules** (STL, PUR, TAX). They read a Customer or a Supplier with no user at all.

## What PTY does meanwhile (ADR-PTY-039)
Every service method checks its PTY authority, and such calls are refused. No v1 caller is blocked, because these callers belong to modules built after PTY (SFR in wave 2; STL, PUR and TAX in later tiers). PTY grants no authority of its own and builds no bypass (checklist F5, F9).

## Question to the core
Which of these, or another, is the core's pattern?
1. A core authority for in-process calls, for example `CROSSMODULE_CALL`, which the calling module's code path is granted and which a `crossmodule` service accepts beside its own permission.
2. A core helper that runs a call as a module's system principal on behalf of an anonymous or CUSTOMER-realm user (for example `SystemPrincipal.runAsModule("SFR", …)`), with an audit row.
3. A CUSTOMER-realm permission set that a module may declare, for example `PERM_PTY_GUEST_RESOLVE` in realm CUSTOMER, which sec attaches to anonymous checkout sessions.

## Impact when answered
PTY changes only the `@PreAuthorize` expression of the affected service methods:
- `GuestCustomerService.resolve`;
- `AddressService.listActive` and `AddressService.read`;
- `CustomerService.read` and `SupplierService.read`.
No `PtyPartyApi` signature and no endpoint changes.

## Traces
ADR-PTY-039 · CON-PTY-009, CON-PTY-010, CON-PTY-011, CON-PTY-012, CON-PTY-014 · REQ-PTY-050 … REQ-PTY-063
