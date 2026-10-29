# ADR-PTY-025 — Permissions the SRS leaves open; in-process callers; invitation-handler outcomes

Status  : ACCEPTED (non-breaking) — in-process callers and the Address permission revised by ADR-PTY-039; the invitation outcomes by the backend plan CORE (outbox subscriber) · Stage : P3.1 · Module : PTY v1 · Date : 2026-10-29

## Context
The SRS access matrices (B4) give permissions per screen and action. Several endpoints are not covered:
- the operations offered to consuming modules (SRS A8.1): walk-in Customer, guest resolution, credit check, use of an approval, the `b2bCredit` switch-off answer;
- reactivation;
- Address endpoints, which are shared by the Customers and Suppliers screens;
- clearing credit on a page that grants no DELETE;
- reading a single Credit override request.

The SRS also requires two outcomes of the CUSTOMER-realm invitation acceptance — RULE-PTY-013 and RULE-PTY-020 — to be "reported". That acceptance arrives in-process from the given sec module, not over HTTP. The platform is one deployable (`module_interface: in_process`).

## Decision
- **Reactivation** (API-PTY-005, API-PTY-010) requires the DELETE permission of the page. Reactivation is the reverse of deactivation, and the SRS names the Owner as the actor of both.
- **Address endpoints** (API-PTY-011 … 015, 029, 012):
  - Writes require UPDATE on PTY_CUSTOMER when the Party holds a Customer, or UPDATE on PTY_SUPPLIER when it holds a Supplier (SRS B4: "Address create, update and deactivation require UPDATE on this page"). The controller checks that the caller has either permission. The service then checks the permission that matches the roles the Party holds.
  - Reads require VIEW on either page.
- **Clear credit** (API-PTY-018) requires PERM_PTY_CUSTOMER_CREDIT_UPDATE. It clears two fields, and the page grants no DELETE.
- **Operations for consuming modules:**
  - Walk-in Customer (API-PTY-033) and credit check (API-PTY-035) require PERM_PTY_CUSTOMER_VIEW, which the seller performing a sale holds.
  - Guest resolution (API-PTY-034) requires PERM_PTY_CUSTOMER_CREATE for a REST caller.
  - Use of an approval (API-PTY-036) requires PERM_PTY_CREDIT_OVERRIDE_CREATE, the requester's permission.
  - The switch-off answer (API-PTY-037) requires PERM_PTY_CREDIT_SETTINGS_VIEW, which is held by the Owner, who switches flags.
- **Global search** (API-PTY-027) checks VIEW in the service. A caller without VIEW receives an empty list, not a 403 (REQ-PTY-043).
- **Reading a single request** (API-PTY-020) follows REQ-PTY-102. A caller without PERM_PTY_CREDIT_OVERRIDE_UPDATE reads only requests it created. Any other request answers not found, so its existence is not revealed.
- **In-process callers.** A consuming module that calls `PtyPartyApi` authorises its own caller against its own permissions before it calls PTY — for example, the storefront for its anonymous shopper. PTY applies tenancy but does not re-check a PTY permission on the in-process path. The REST path keeps the permission above.
- **Invitation acceptance.** `CustomerInvitationAcceptanceHandler` returns an outcome to the given's callback: LINKED, or REFUSED together with the RULE-PTY-013 / RULE-PTY-020 message in ar + en. These outcomes have no HTTP status and no Error Catalog row. The database backstops them with CHK_PTY_CUSTOMER_WALK_IN_SEC_USER and UQ_PTY_CUSTOMER_SEC_USER_ID.

## Consequences
- The permission matrix grants only the actions B4 grants. CREATE and DELETE on PTY_CUSTOMER_CREDIT stay ungranted, and so do CREATE and DELETE on PTY_CREDIT_SETTINGS.
- If the given erp-core later provides a caller authority for in-process reads, the in-process path can adopt it without changing any signature.
- A custom role that needs the credit check without Customer VIEW is not supported in v1.

## Traces
API-PTY-005, 010, 011 … 015, 018, 020, 027, 029, 033 … 037 · REQ-PTY-015, REQ-PTY-021, REQ-PTY-043, REQ-PTY-052, REQ-PTY-054, REQ-PTY-102 · RULE-PTY-013, RULE-PTY-020 · SCR-REQ-PTY-001 … 005 (B4)
