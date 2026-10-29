# ADR-PTY-034 — Frontend binding decisions for PTY v1

Status  : ACCEPTED (non-breaking) · Stage : P3.2 · Module : PTY v1 · Date : 2026-10-29
Amended by : ADR-PTY-048 — the five lookup keys are read through PTY's own operation API-PTY-038 (`OwnedLookups`), not the shell's mdl lookup client; every other decision of this ADR holds

## Context
The P3.2 frontend plan is bound to `api-spec-pty.yaml` (37 operations). The SRS, the PRD and the document leave several frontend choices open. Each is decided below so that the implementer does not have to. None adds a field, rule or permission to the SRS.

## Decisions

1. **Reads outside the API document.** The screens need four things that are not operations of api-spec-pty.yaml: lookup values (given `erp-core` `mdl`: CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT, CREDIT_OVERRIDE_STATUS — SRS A6, A9), the `b2bCredit` flag (given application configuration, `GET /api/public/app-config`), the session's permission names (given `sec`), and the name of the user who decided a Credit override request (given `sec` user read; the document's `CreditOverrideRequest.decidedBy` is an identifier). They are hooks of kind `lookup` or `init`, never `read`; the last one is UXD-PTY-001. No PTY operation is invented for them.

2. **Operations not bound to a PTY screen.** API-PTY-012 (read Address), API-PTY-033 (walk-in Customer), API-PTY-034 (guest Customer resolution), API-PTY-035 (credit check), API-PTY-036 (use Credit override request) and API-PTY-037 (`b2bCredit` switch-off dependency) are called by Sales & POS, Purchasing, Storefront or the capability-flag owner (SRS A8.1). No PTY screen calls them; they stay in the API binding with their REQs and appear in the operations coverage as consumer-only. REQ-PTY-052 and REQ-PTY-054 have no operation (in-process, ADR-PTY-025) and no frontend surface.

3. **Registration in place (REQ-PTY-008).** The Customer and Supplier screens export a Quick register sub-view (`CustomerQuickRegister`, `SupplierQuickRegister`) that a host document mounts in its own container. It is the Entry's primary field group (names, mobile, email) with one mandatory mark on the name group (AC-PTY-007, AC-PTY-021), one save (API-PTY-001 / API-PTY-006), and returns `{id, code}` to the host so the host screen stays open (AC-PTY-010, AC-PTY-011). It shows no customer group (the server sets RETAIL, REQ-PTY-007) and no address pane. The back-office Entry keeps the SRS B3 count (2 marks for Customers, 1 for Suppliers).

4. **One save with ordered calls.** Addresses are a child collection of a `page` container, so they sit inline; one line is edited in a one-line drawer that returns the line to the page's form. The screen's one Save sends the record call first (create or update) and, only after it succeeded, the address changes (API-PTY-014, API-PTY-013, API-PTY-011, then API-PTY-015) one after another; the address calls are not sent when no line changed. A refused record call sends nothing else.

5. **Commands are not saves.** Deactivate, Reactivate, Make supplier, Make customer, Clear credit, Approve, Reject and Cancel request are single confirmed calls. They are disabled while the screen's form holds unsaved changes, so no user leaves believing an unsent edit was saved with them.

6. **Refusals stay reachable.** Commands whose refusal the SRS tests are offered, not hidden: Deactivate and Make supplier on the walk-in Customer (AC-PTY-055, AC-PTY-057), Cancel request on a request the user did not create when the user holds DELETE (AC-PTY-109). The server's refusal message is shown.

7. **Cache policy deviations.** Lookups: `staleTime` 30 min, `gcTime` 60 min. Application configuration: refetched on every route mount (`staleTime` 0) so a `b2bCredit` change shows from the next screen load (REQ-PTY-073). Everything else uses the tanstack-query defaults.

8. **Credit history placement.** The credit history of SCR-PTY-003 is an inline read-only pane beneath the credit fields, paged with the body, with no save and no scroll region of its own.

9. **Removed (gate-analysis revise, G1 / G20).** The former PENDING interim for the Party named by RULE-PTY-002 is superseded by ADR-PTY-043: API-PTY-028 gains an email filter, and the action "Open that party" «فتح ذلك الطرف» finds the Party through the Customer search and then the Supplier search by email. This ADR holds no open item.

## Consequences
- The plan's API binding, F2 hooks, F4 routes and the ALIGN-FE operations coverage follow these decisions.
- No decision of this ADR is open; the RULE-PTY-002 action is decided in ADR-PTY-043.

## Traces
SCR-PTY-001, SCR-PTY-002, SCR-PTY-003, SCR-PTY-004, UXD-PTY-001 · REQ-PTY-002, REQ-PTY-007, REQ-PTY-008, REQ-PTY-013, REQ-PTY-030, REQ-PTY-031, REQ-PTY-033, REQ-PTY-035, REQ-PTY-073, REQ-PTY-103, REQ-PTY-108 · AC-PTY-007, AC-PTY-010, AC-PTY-011, AC-PTY-013, AC-PTY-021, AC-PTY-055, AC-PTY-057, AC-PTY-109 · API-PTY-001, API-PTY-006, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037 · RULE-PTY-002
