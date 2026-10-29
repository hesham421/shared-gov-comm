# ADR-PTY-039 — Every service method checks its own PTY authority; in-process callers hold it too

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise (G5, G14) · Module : PTY v1 · Date : 2026-10-29
Revises : ADR-PTY-025 (in-process callers; address permission) · Depends on : CORE-GAP-in-process-caller-authority (OPEN)

## Context
The P3.1 plan carried `@PreAuthorize` on the controllers only. ADR-PTY-025 let an in-process caller of `PtyPartyApi` run under its own module's checks, with no PTY permission. Checklist C13 requires `@PreAuthorize("hasAuthority(T(…PtyPermissions).PERM_…)")` on every service method, referencing PTY's own constants. The SRS also leaves open which permission an Address write needs when the Party holds both roles.

## Decision
1. **One contributor, one constant class.** `com.erp.pty.permission.PtyPermissions implements PermissionContributor` declares permission module PTY with its five screens and the 16 `PERM_PTY_*` constants. Every `@PreAuthorize` references those constants and no other module's.
2. **Service methods.** Every public method of a `Pty*Service` carries the authority of the endpoint it serves (backend plan SEC-BE). The controller carries the same annotation, so a refusal happens before the body is read. `PtyPartyApi` delegates to the same service methods, so **an in-process caller's security context must hold the PTY authority** — ADR-PTY-025's exemption is withdrawn. The cashier on a sale holds PERM_PTY_CUSTOMER_VIEW and PERM_PTY_CREDIT_OVERRIDE_CREATE (preset Seller/Cashier), which covers CON-PTY-009, 013, 015, 016 and 018.
3. **The global search.** API-PTY-027 must answer an empty list, not a refusal, to a user without VIEW (REQ-PTY-043). It carries `@PreAuthorize("isAuthenticated()")` and checks `PtyPermissions.PERM_PTY_CUSTOMER_VIEW` in code through `SecurityContextHelper`. This is the one method whose PTY authority is checked in code rather than in the annotation, and the backend plan names it.
4. **Address writes (G14).** API-PTY-011, 013, 014 and 015 carry `@PreAuthorize("hasAnyAuthority(T(…PtyPermissions).PERM_PTY_CUSTOMER_UPDATE, T(…PtyPermissions).PERM_PTY_SUPPLIER_UPDATE)")`. The service then requires the caller to hold the UPDATE of a role the Party holds. When the Party holds both roles, **either UPDATE is enough**. A caller whose UPDATE belongs to a role the Party does not hold gets PTY-403-FORBIDDEN. Reads (API-PTY-012, 029) follow the same rule with VIEW.
5. **Callers with no PTY authority.** Three kinds of caller hold no PTY authority:
   - the anonymous storefront shopper of guest checkout (CON-PTY-014);
   - a CUSTOMER-realm shopper reading its own addresses;
   - system jobs of consuming modules.
   The core publishes no mechanism for them. CORE-GAP-in-process-caller-authority asks for one. Until it is answered, those calls are refused like any other caller without the authority. Every one of these callers belongs to a module of wave 2 or later (SFR) or to a later tier, so no v1 caller is blocked. When the core answers, a delta adopts its mechanism without changing any `PtyPartyApi` signature.

## Consequences
- The SEC-BE phase lists the authority of every service method. The permission matrix is unchanged: no permission name is added.
- The SRS B4 statement "Address create, update and deactivation require UPDATE on this page" holds for each screen. Decision 4 adds the case where the Party holds both roles.
- New backend TCs cover a caller holding only PERM_PTY_SUPPLIER_UPDATE writing an Address of a two-role Party, and an in-process call without the authority.

## Traces
REQ-PTY-030, REQ-PTY-031, REQ-PTY-033, REQ-PTY-035, REQ-PTY-043, REQ-PTY-050 · CON-PTY-009 … CON-PTY-020, CON-PTY-023, CON-PTY-024 · API-PTY-001 … API-PTY-037
