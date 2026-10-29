# ADR-PTY-028 — Credit computation and derived properties

Status  : ACCEPTED (non-breaking) · Stage : P3.1 · Module : PTY v1 · Date : 2026-10-29

## Context
Several values the SRS returns have no column: open exposure, projected exposure, available credit, credit check result, approval-required, the credit-overrun action, carries-credit, and credit history. The SRS does not say:
- whether the server trusts a caller-supplied credit check result when a Credit override request is created;
- whether a request may be created under WARN;
- how the 3-second contributor limit works on a single deployable;
- whether the walk-in Customer appears on the Customer credit list.

## Decision
- **Open exposure** is the sum of `CustomerCreditExposureContributor.openExposure(customerId)` over every registered bean, and 0 when there is none. Each contributor is an in-process call, invoked asynchronously with a 3-second time budget. A timeout or exception from any contributor makes the exposure unavailable. This is a time limit on a local call, not a network timeout, so no catalog row exists for it.
- **Credit check algorithm:**
  1. If the flag is off → NOT_APPLICABLE.
  2. If the credit limit is empty → NO_CREDIT.
  3. If the exposure is unavailable → EXPOSURE_UNAVAILABLE, with openExposure empty.
  4. If projected exposure (open exposure + sale amount) ≤ limit → WITHIN_LIMIT.
  5. Otherwise → OVER_LIMIT.

  OVER_LIMIT, NO_CREDIT and EXPOSURE_UNAVAILABLE carry the overrun action (default BLOCK_WITH_OVERRIDE), and approvalRequired = (action = BLOCK_WITH_OVERRIDE).
- **Creating a request recomputes the check on the server** and ignores any result the caller holds. The request stores the recomputed result, the limit at request and the open exposure. If the recomputed result is WITHIN_LIMIT, creation is refused with RULE-PTY-025.
- **A request may be created under WARN.** No rule forbids it and it harms nothing: approval is simply not required then.
- **Available credit** = credit limit − open exposure. It is empty when either value is empty.
- **carriesCredit** = credit limit or payment terms is non-empty. It is used by the credit list filter and the switch-off count.
- **The walk-in Customer is not listed on the Customer credit list**, because it can never carry credit (RULE-PTY-011). Inactive Customers are listed, so that their credit can be cleared before the flag is switched off.
- **Credit history** is read from the given AuditApi: the events of the Customer for the two fields, newest first. User names are resolved through SecUserDirectoryApi. PTY keeps no history table.
- **The credit-overrun action** is the tenant setting `PTY_CREDIT_OVERRUN_ACTION` in the given cu module (ADR-PTY-029).

## Consequences
- A sale can never be approved against a result that the caller computed on stale data.
- When a contributor is slow, credit sales fall back to the overrun handling and are never passed as within the limit.

## Traces
API-PTY-016, 019, 030, 031, 035, 037 · REQ-PTY-068, REQ-PTY-069, REQ-PTY-074, REQ-PTY-079 … REQ-PTY-089, REQ-PTY-091, REQ-PTY-104, REQ-PTY-108 · RULE-PTY-011, RULE-PTY-025 · ADR-PTY-017
