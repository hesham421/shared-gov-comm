# ADR-PTY-036 — Number series and the approver notification template are seeded by the PTY migration

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise (G21, G6) · Module : PTY v1 · Date : 2026-10-29
Revises : ADR-PTY-029 (bootstrap — number series and notification template)

## Context
ADR-PTY-029 registered the series PTY_PARTY and PTY_CREDIT_OVERRIDE through `NumberSeriesApi` from PTY's `TenantProvisioningContributor`. The db-script (D-P2-07) seeded no `CORE_NUMBER_SERIES` row. The approver notification of API-PTY-019 was sent as inline text.

The erp-core checklist does not allow either:
- C9: every document number is `NumberSeriesApi.next(code)` from a `CORE_NUMBER_SERIES` row seeded in `V1000+`, and it is stored in a column unique with `TENANT_ID`;
- C8: every message is `NotificationDispatchApi.dispatch(DispatchCommand)` with a `NOTIF_TEMPLATE` code seeded in `V1000+`.

## Decision
1. db-script BLOCK 8c seeds two `CORE_NUMBER_SERIES` rows for `TENANT_ID = 1`. Both use pattern `{PREFIX}-{SEQ:6}` and reset policy `NEVER`:
   - `PTY_PARTY`, prefix `P`, gives P-000120 (DBF-PTY-007);
   - `PTY_CREDIT_OVERRIDE`, prefix `CO`, gives CO-000031 (DBF-PTY-061).
   New tenants receive copies with the counter at 1 through the given provisioning.
2. UQ_PTY_PARTY_CODE is `(TENANT_ID, CODE)` and UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO is `(TENANT_ID, DOC_NO)` (ADR-PTY-035).
3. db-script BLOCK 8d seeds the `NOTIF_TEMPLATE` row `PTY_CREDIT_OVERRIDE_REQUESTED` for `TENANT_ID = 1`, channel IN_APP and category UTILITY. Its variables are `{docNo}` and `{name}`. Its body is:
   - ar «طلب تجاوز حد الائتمان {docNo} للعميل {name} بانتظار موافقتك.»
   - en «Credit override request {docNo} for {name} is waiting for your approval.»
   The given copies it to new tenants.
4. API-PTY-019 dispatches one `DispatchCommand(recipientId = the approver's sec user id, templateCode = "PTY_CREDIT_OVERRIDE_REQUESTED", channelHint = IN_APP, moduleCode = "PTY", referenceId = creditOverrideRequestPk, referenceType = "PTY_CREDIT_OVERRIDE_REQUEST", variables = {docNo, name})` per approver. PTY formats no subject or body.
5. PTY's `TenantProvisioningContributor` now creates only the walk-in Customer (REQ-PTY-044). It no longer registers a series.

## Consequences
- The `bootstrap` block names the migration as the producer of both series and of the template.
- When dispatch runs is decided in the backend plan's CORE (after commit, on the given event executor — G9).

## Traces
DBF-PTY-007, DBF-PTY-061 · RULE-PTY-007, RULE-PTY-031 · REQ-PTY-006, REQ-PTY-092, REQ-PTY-093 · API-PTY-001, 006, 019, 033, 034
